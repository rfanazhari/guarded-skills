# Persistence files

Both adapters implement `repository.{{Aggregate}}Repository`. Not-found, conflict, and a page past the end behave the same. The in-memory adapter stores a clone so a caller cannot mutate the map through a returned pointer.

## `infrastructure/repository/{{aggregate}}_memory_repository.go`

```go
func (r *{{aggregate}}MemoryRepository) Create(ctx context.Context, item *entity.{{Aggregate}}) error {
	r.mu.Lock()
	defer r.mu.Unlock()
	if _, ok := r.items[item.ID]; ok {
		return errs.ErrConflict
	}
	clone := *item
	r.items[item.ID] = &clone
	return nil
}
```

`FindByID` takes a read lock, returns `errs.ErrNotFound` when the key is absent, and returns a clone.

`FindAll` copies every value, sorts by `CreatedAt` descending, and returns `[]*entity.{{Aggregate}}{}` with the real total when `offset` is past the end. Never return a nil slice.

`Update` returns `errs.ErrNotFound` when the id is absent, otherwise stores a clone.

`Delete` removes the key. A missing key returns `errs.ErrNotFound`.

Guard every method with `sync.RWMutex`.

## `infrastructure/database/postgres.go`

Open GORM with `TranslateError: true` so a unique violation becomes `gorm.ErrDuplicatedKey`.

```go
db, err := gorm.Open(postgres.Open(dsn), &gorm.Config{TranslateError: true})
```

The DSN is built from the config fields `host`, `user`, `password`, `dbname`, `port`, `sslmode`, and `TimeZone=UTC`.

## `infrastructure/repository/{{aggregate}}_gorm_repository.go`

The model lives in this file, not in `domain`. Table name is `{{table}}`.

```go
type {{aggregate}}Model struct {
	ID          string    `gorm:"primaryKey;type:varchar(36)"`
	Title       string    `gorm:"type:varchar(255);not null"`
	Description string    `gorm:"type:text"`
	Status      string    `gorm:"type:varchar(32);not null"`
	CreatedAt   time.Time
	UpdatedAt   time.Time
}
```

`New{{Aggregate}}GormRepository` calls `db.AutoMigrate` for that model. In comments and docs, call this startup schema sync. Do not call it a versioned migration.

`Create` maps `gorm.ErrDuplicatedKey` to `errs.ErrConflict`.

`FindByID` maps `gorm.ErrRecordNotFound` to `errs.ErrNotFound`.

`Delete` uses a hard `Delete` on the id. `RowsAffected == 0` returns `errs.ErrNotFound`. There is no `gorm.DeletedAt` field.

`FindAll` counts, then applies offset, limit, and `created_at DESC`. Build the result with `make([]*entity.{{Aggregate}}, 0, len(models))` so an empty page is `[]`.

Map with `toDomain` and `fromDomain`. Queries use `WithContext(ctx)`.

## `infrastructure/repository/{{aggregate}}_conformance_test.go`

One unexported function runs three cases against a `repository.{{Aggregate}}Repository`:

1. `FindByID` and `Delete` of an unknown id return `errs.ErrNotFound`.
2. `Create` of the same id twice returns `errs.ErrConflict` on the second call.
3. After one insert, `FindAll` with an offset past the end returns a non-nil empty slice and total `1`.

Call that function for `New{{Aggregate}}MemoryRepository` with no extra setup.

Call it for the GORM repository only when `CONTRACT_DATABASE_DSN` is set. When the variable is empty, `t.Skip`. The skill report still says the PostgreSQL adapter was not verified. Do not describe a skip as a pass.

Use a unique id per case so a shared database does not collide. Clean up rows the PostgreSQL case inserted.

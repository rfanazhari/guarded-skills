# Application files

Package name for the implementation is `{{aggregate}}`. The test package is `{{aggregate}}_test`.

`NewUsecase` installs the production id and clock. Tests call `NewUsecaseWithClock` only.

## `application/usecase/{{aggregate}}/{{aggregate}}_usecase.go`

```go
package {{aggregate}}

import (
	"context"
	"strings"
	"time"

	"github.com/google/uuid"
	"{{module}}/domain/entity"
	"{{module}}/domain/repository"
	"{{module}}/pkg/errs"
)

type CreateInput struct {
	Title       string
	Description string
}

type Usecase interface {
	Create(ctx context.Context, input CreateInput) (*entity.{{Aggregate}}, error)
	Get(ctx context.Context, id string) (*entity.{{Aggregate}}, error)
	List(ctx context.Context, page, limit int) ([]*entity.{{Aggregate}}, int64, error)
	Activate(ctx context.Context, id string) (*entity.{{Aggregate}}, error)
	Delete(ctx context.Context, id string) error
}

type usecase struct {
	repo    repository.{{Aggregate}}Repository
	idGen   func() string
	nowFunc func() time.Time
}

func NewUsecase(repo repository.{{Aggregate}}Repository) Usecase {
	return NewUsecaseWithClock(repo,
		func() string { return uuid.New().String() },
		func() time.Time { return time.Now().UTC() },
	)
}

func NewUsecaseWithClock(repo repository.{{Aggregate}}Repository, idGen func() string, nowFunc func() time.Time) Usecase {
	return &usecase{repo: repo, idGen: idGen, nowFunc: nowFunc}
}

func (u *usecase) Create(ctx context.Context, input CreateInput) (*entity.{{Aggregate}}, error) {
	item, err := entity.New{{Aggregate}}(u.idGen(), input.Title, input.Description, u.nowFunc())
	if err != nil {
		return nil, err
	}
	if err := u.repo.Create(ctx, item); err != nil {
		return nil, err
	}
	return item, nil
}

func (u *usecase) Get(ctx context.Context, id string) (*entity.{{Aggregate}}, error) {
	id = strings.TrimSpace(id)
	if id == "" {
		return nil, errs.ErrInvalidInput
	}
	return u.repo.FindByID(ctx, id)
}

func (u *usecase) List(ctx context.Context, page, limit int) ([]*entity.{{Aggregate}}, int64, error) {
	if page < 1 {
		page = 1
	}
	if limit < 1 || limit > 100 {
		limit = 10
	}
	return u.repo.FindAll(ctx, (page-1)*limit, limit)
}

func (u *usecase) Activate(ctx context.Context, id string) (*entity.{{Aggregate}}, error) {
	item, err := u.Get(ctx, id)
	if err != nil {
		return nil, err
	}
	if err := item.Activate(u.nowFunc()); err != nil {
		return nil, err
	}
	if err := u.repo.Update(ctx, item); err != nil {
		return nil, err
	}
	return item, nil
}

func (u *usecase) Delete(ctx context.Context, id string) error {
	id = strings.TrimSpace(id)
	if id == "" {
		return errs.ErrInvalidInput
	}
	if _, err := u.repo.FindByID(ctx, id); err != nil {
		return err
	}
	return u.repo.Delete(ctx, id)
}
```

Do not call `time.Now` or `uuid.New` inside `Create`, `Get`, `List`, `Activate`, or `Delete`.

## Test

The mock is a private struct in the test file. It is not the in-memory adapter.

Each scenario row becomes a `t.Run` whose name starts with the id, for example `SC-01: valid title creates DRAFT`. Build the use case with a fixed id and `time.Date(..., time.UTC)`.

Assert `assert.ErrorIs` for `errs.ErrInvalidInput`, `errs.ErrNotFound`, and `errs.ErrInvalidStateTransition`.

`SC-06` creates an entry, activates it once, then asserts that a second activate returns `ErrInvalidStateTransition`.

Run `go test -v ./application/usecase/{{aggregate}}/` after the test file exists and before the use case methods exist, and keep the failing output. Then implement until the same command passes.

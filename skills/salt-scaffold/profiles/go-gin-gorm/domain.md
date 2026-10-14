# Domain files

Generate a status constant only for a value named in `.context/contracts/04-BEHAVIORAL-STATE-CONTRACT.md`. The sample is `DRAFT` and `ACTIVE`.

## `pkg/errs/errs.go`

```go
package errs

import "errors"

var (
	ErrNotFound               = errors.New("resource not found")
	ErrInvalidInput           = errors.New("invalid input data")
	ErrConflict               = errors.New("resource already exists")
	ErrInvalidStateTransition = errors.New("invalid state transition")
	ErrUnauthorized           = errors.New("unauthorized access")
	ErrForbidden              = errors.New("forbidden operation")
	ErrInternal               = errors.New("internal server error")
)
```

Keep this list aligned with `.context/02-SHARED-CONTRACTS/error-codes.md`. Add a sentinel only after the error table has a row.

## `domain/enum/status.go`

```go
package enum

type Status string

const (
	StatusDraft  Status = "DRAFT"
	StatusActive Status = "ACTIVE"
)

func (s Status) String() string { return string(s) }

func (s Status) IsValid() bool {
	switch s {
	case StatusDraft, StatusActive:
		return true
	default:
		return false
	}
}
```

## `domain/entity/{{aggregate}}.go`

`Activate` allows only the transition the behavioral contract lists. For the default contract that is `DRAFT` to `ACTIVE`.

```go
package entity

import (
	"strings"
	"time"

	"{{module}}/domain/enum"
	"{{module}}/pkg/errs"
)

type {{Aggregate}} struct {
	ID          string
	Title       string
	Description string
	Status      enum.Status
	CreatedAt   time.Time
	UpdatedAt   time.Time
}

func New{{Aggregate}}(id, title, description string, now time.Time) (*{{Aggregate}}, error) {
	title = strings.TrimSpace(title)
	if title == "" {
		return nil, errs.ErrInvalidInput
	}
	return &{{Aggregate}}{
		ID:          id,
		Title:       title,
		Description: description,
		Status:      enum.StatusDraft,
		CreatedAt:   now,
		UpdatedAt:   now,
	}, nil
}

func (e *{{Aggregate}}) Activate(now time.Time) error {
	if e.Status != enum.StatusDraft {
		return errs.ErrInvalidStateTransition
	}
	e.Status = enum.StatusActive
	e.UpdatedAt = now
	return nil
}
```

No `json` or `gorm` tags on the entity. No `DeletedAt` field unless the persistence contract says soft delete.

## `domain/repository/{{aggregate}}_repository.go`

```go
package repository

import (
	"context"

	"{{module}}/domain/entity"
)

type {{Aggregate}}Repository interface {
	Create(ctx context.Context, item *entity.{{Aggregate}}) error
	FindByID(ctx context.Context, id string) (*entity.{{Aggregate}}, error)
	FindAll(ctx context.Context, offset, limit int) ([]*entity.{{Aggregate}}, int64, error)
	Update(ctx context.Context, item *entity.{{Aggregate}}) error
	Delete(ctx context.Context, id string) error
}
```

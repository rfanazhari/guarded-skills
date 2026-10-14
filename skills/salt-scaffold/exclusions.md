# Do not copy these from a reference service

Tick each item before calling the scaffold done. The reference that produced this skill disagrees with itself on these points. The generated service follows the contract pack, which is stricter.

- [ ] Domain path is `domain/`. No `internal/domain` path is written into contracts or imports.
- [ ] Delete is hard delete unless the persistence contract says soft delete. No `deleted_at` column is added for a hard delete. The GORM model does not embed `gorm.DeletedAt` unless soft delete was requested and both adapters hide deleted rows.
- [ ] The entity has no column that the contract omits. In particular, do not add a `metadata` JSON column that the entity does not store.
- [ ] The state machine matches `.context/contracts/04-BEHAVIORAL-STATE-CONTRACT.md`. Do not add suspend, archive, or reactivate methods the contract does not list. Activate from a non-`DRAFT` status returns `ErrInvalidStateTransition` when that is what the contract says.
- [ ] CORS does not send `Access-Control-Allow-Origin: *` together with `Access-Control-Allow-Credentials: true`.
- [ ] No `.env` file is created. `.env.example` is created. The container image does not copy either file in as a secret.
- [ ] An empty `valueobject` package is not created.
- [ ] Schema creation on startup is described as startup schema sync. It is not described as a versioned migration.
- [ ] Duplicate id on create returns `ErrConflict` from both adapters.
- [ ] A list page past the end returns an empty slice and the true total, from both adapters.
- [ ] `GET /healthz` stays outside the success envelope.

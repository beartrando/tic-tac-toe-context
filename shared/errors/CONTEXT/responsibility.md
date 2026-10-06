# Responsibility

- Классы ошибок: `DomainError` + `AccessDeniedError`, `InvalidAuthorizationError`,
  `InvalidSessionError`, `NotFoundError`, `ValidationError`, `TokenExpiredError`,
  `UnknownError`, `ServiceUnavailableError`, `InvalidResponseError` (экспорт — `src/index.ts`).
- Маппинг на `ErrorCode` из сгенерированного `src/contracts/proto/common/error.ts`
  (код `common/error.proto`: `UNKNOWN`, `INVALID_SESSION`, `ACCESS_DENIED`, `NOT_FOUND`,
  `VALIDATION_FAILED`, `SERVICE_UNAVAILABLE`, `INVALID_RESPONSE`, `INVALID_AUTHORIZATION`,
  `TOKEN_EXPIRED`).

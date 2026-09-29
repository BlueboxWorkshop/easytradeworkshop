# Changelog — credit-card-order-service

All notable changes to this service are documented here.

---

## [Unreleased]

### Fixed

- **ArithmeticException / by zero on `GET /v1/orders/{accountId}/status/latest`**
  ([upstream issue #27](https://github.com/JasonOstroski/AWSBlueboxWorkshopEasytrade2026/issues/27))

  `OrderController.CountArythmeticSequenceTotal()` contained intentional fault-injection
  code that set `theGreatDivider = 0`. When the `credit_card_meltdown` feature flag was
  enabled, every call to the `/status/latest` endpoint hit this path and threw
  `java.lang.ArithmeticException: / by zero`, resulting in ~100% error rate on that
  endpoint.

  **Root cause:** `theGreatDivider` was hardcoded to `0` instead of the mathematically
  correct value `2` (arithmetic sequence sum formula: `(first + last) * count / 2`).
  The original code included comments acknowledging the value was wrong by design.

  **Fix:** Changed `theGreatDivider` from `0` to `2` and removed the fault-injection
  comments and `deepcode ignore` suppression annotation. The `credit_card_meltdown`
  feature flag can now be toggled without triggering an uncaught production exception.

# Repzilla–HWS Interface

## Endpoints Exposed by HWS

All requests must include `esp32_id` and `terminal_id` headers.

| Method | Endpoint | Request body | Response |
| --- | --- | --- | --- |
| POST | `/users` | `user_id`, `name` | `user_id`, `name`; biometric details to discuss |
| PUT | `/users/{user_id}` | `user_id`, `name`; biometric information if supported | `user_id`, `name`; biometric details to discuss |
| DELETE | `/users/{user_id}` | — | — |
| POST | `/users/{user_id}/add-biometric` | — | — |
| POST | `/users/{user_id}/remove-biometric` | — | — |
| GET | `/device-info` | — | — |

**To discuss:** How biometric information is handled in user creation, updates, and responses. Dashes indicate details not yet specified.

## Endpoints Exposed by Repzilla (Backend)

All requests must include ESP32 and terminal IDs in the headers.

| Method | Endpoint |
| --- | --- |
| POST | `/api/hms/mark-attendance` |
| GET | `/api/hms/user-information` |

**To confirm:** The backend header name is written as `esp_32` in the original notes, while HWS uses `esp32_id`. The terminal header is `terminal_id`.

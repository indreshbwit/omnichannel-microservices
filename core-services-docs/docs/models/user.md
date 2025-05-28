
---

#### 📄 `docs/models/user.md`

```md
# Domain Model: User

| Field      | Type     | Description               |
|------------|----------|---------------------------|
| id         | string   | UUID                      |
| tenantId   | string   | Associated tenant         |
| name       | string   | Full name                 |
| email      | string   | Unique email              |
| roleId     | string   | FK to Role                |

## Diagram

![ER Diagram](../../diagrams/previews/er-diagram.png)


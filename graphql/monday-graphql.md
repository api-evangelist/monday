# Monday.com GraphQL API

## Overview

Monday.com exposes a native GraphQL API that provides full programmatic access to boards, items, columns, users, workspaces, updates, webhooks, and other platform resources. All API requests are sent as HTTP POST to a single endpoint.

- **Endpoint:** `https://api.monday.com/v2`
- **Protocol:** GraphQL over HTTPS
- **Authentication:** Bearer token (personal API key or OAuth 2.0 access token) via the `Authorization` header
- **API version header:** `API-Version: 2024-01` (date-based versioning)
- **Developer docs:** https://developer.monday.com/api-reference/
- **GraphQL playground:** https://monday.com/developers/v2/try-it-yourself
- **GitHub org:** https://github.com/mondaydotcomorg

---

## Authentication

```http
POST https://api.monday.com/v2
Authorization: Bearer <your_api_token>
Content-Type: application/json
API-Version: 2024-01
```

Personal API tokens are generated from **Profile > Developers > My Access Tokens**. OAuth tokens are obtained through the standard OAuth 2.0 authorization code flow for installed apps.

---

## Rate Limits & Complexity

Monday.com uses a **complexity budget** instead of simple request-per-minute rate limits. Each query has a complexity cost; the budget resets every minute.

Query the current budget:

```graphql
query {
  complexity {
    before
    after
    reset_in_x_seconds
    query
  }
}
```

---

## Schema File

See [`monday-schema.graphql`](monday-schema.graphql) for the full schema definition including all types, enums, inputs, queries, mutations, and subscriptions.

---

## Named Types (63)

| Type | Kind | Description |
|------|------|-------------|
| `Account` | Object | Monday.com organisation account |
| `AccountSlug` | Object | Account slug identifier |
| `AccountProduct` | Object | Product enabled on an account |
| `Plan` | Object | Subscription plan details |
| `APIKey` | Object | Personal API key |
| `User` | Object | Platform user |
| `UserDetails` | Object | Minimal user for nested contexts |
| `UserKind` | Enum | Filters: all, guests, non_guests, non_pending |
| `UserRole` | Enum | ADMIN, MEMBER, VIEWER, GUEST |
| `Team` | Object | Group of users |
| `TeamDetails` | Object | Minimal team for nested contexts |
| `Workspace` | Object | Workspace container for boards |
| `WorkspaceDetails` | Object | Minimal workspace for nested contexts |
| `WorkspaceKind` | Enum | open or closed |
| `WorkspaceSettings` | Object | Workspace icon and branding |
| `WorkspaceIcon` | Object | Workspace icon image and color |
| `Board` | Object | A monday.com board |
| `BoardDetails` | Object | Minimal board for nested contexts |
| `BoardType` | Enum | board, sub_items_board, document, custom_object |
| `BoardKind` | Enum | public, private, share |
| `BoardDuplication` | Object | Result of a duplicate_board mutation |
| `BoardsSubscriber` | Object | A user subscribed to a board |
| `Column` | Object | Column definition on a board |
| `BoardColumn` | Object | Minimal column for nested contexts |
| `ColumnType` | Enum | All 30+ column types (text, numbers, status, date, etc.) |
| `ColumnValue` | Interface | Base interface for all column values |
| `TextColumnValue` | Object | Plain text column value |
| `NumberColumnValue` | Object | Numeric column value |
| `StatusColumnValue` | Object | Status/label column value |
| `DateColumnValue` | Object | Date (and optional time) column value |
| `PersonColumnValue` | Object | People column value |
| `CheckboxColumnValue` | Object | Checkbox column value |
| `ConnectBoardColumnValue` | Object | Board connection column value |
| `DependencyColumnValue` | Object | Item dependency column value |
| `RatingColumnValue` | Object | Rating (star) column value |
| `LinkColumnValue` | Object | URL/link column value |
| `FileColumnValue` | Object | File attachment column value |
| `EmailColumnValue` | Object | Email address column value |
| `PhoneColumnValue` | Object | Phone number column value |
| `MirrorColumnValue` | Object | Mirror (reflected) column value |
| `TimelineColumnValue` | Object | Timeline date range column value |
| `TagColumnValue` | Object | Tags column value |
| `HourColumnValue` | Object | Hour/time column value |
| `DropdownColumnValue` | Object | Dropdown selection column value |
| `CountryColumnValue` | Object | Country picker column value |
| `WorldClockColumnValue` | Object | World clock/timezone column value |
| `WeekColumnValue` | Object | Week picker column value |
| `LocationColumnValue` | Object | Geographic location column value |
| `FormulaColumnValue` | Object | Formula computed column value |
| `VoteColumnValue` | Object | Vote column value |
| `ProgressColumnValue` | Object | Progress tracking column value |
| `TimeTrackingColumnValue` | Object | Time tracking column value |
| `ColorPickerColumnValue` | Object | Color picker column value |
| `CreationLogColumnValue` | Object | Item creation log column value |
| `LastUpdatedColumnValue` | Object | Last updated log column value |
| `ItemIdColumnValue` | Object | Item ID auto-number column value |
| `Group` | Object | A group of items on a board |
| `GroupDetails` | Object | Minimal group for nested contexts |
| `Item` | Object | A row/item on a board |
| `ItemDetails` | Object | Minimal item for nested contexts |
| `ItemColumn` | Object | Column-value pair on an item |
| `ItemsPage` | Object | Paginated list of items (cursor-based) |
| `ItemsPageByColumnValues` | Object | Items page filtered by column values |
| `Cursor` | Object | Pagination cursor |
| `Update` | Object | Comment/update on an item |
| `UpdateLikes` | Object | Likes on an update |
| `UpdateBoardRelation` | Object | Board relation within an update |
| `Reply` | Object | Reply to an update |
| `Tag` | Object | A tag label |
| `TagDetails` | Object | Minimal tag |
| `Asset` | Object | Uploaded file/asset |
| `View` | Object | Generic board/workspace view |
| `BoardView` | Object | A view configured on a board |
| `ViewType` | Enum | BoardView or WorkspaceView |
| `Webhook` | Object | Webhook subscription on a board |
| `WebhookEventType` | Enum | All triggerable webhook events |
| `Notification` | Object | In-app notification |
| `NotificationTargetType` | Enum | Project or Post |
| `Event` | Object | Activity log or subscription event |
| `Complexity` | Object | Query complexity budget |
| `Folder` | Object | Folder organizing boards |
| `Document` | Object | WorkDoc document |
| `DocumentBlock` | Object | Block within a WorkDoc |
| `State` | Enum | active, all, archived, deleted |
| `SubscriberKind` | Enum | owner or subscriber |
| `Plan` | Object | Account subscription plan |
| `PlanVersion` | Enum | free, basic, standard, pro, enterprise |

---

## Example Queries

### Fetch boards with their groups and items

```graphql
query {
  boards(limit: 10, state: active) {
    id
    name
    board_kind
    workspace {
      id
      name
    }
    groups {
      id
      title
      color
    }
    items_page(limit: 50) {
      cursor
      items {
        id
        name
        column_values {
          id
          text
          type
          ... on StatusColumnValue {
            index
            label {
              text
              color
            }
          }
          ... on DateColumnValue {
            date
            time
          }
          ... on PersonColumnValue {
            persons_and_teams {
              id
              kind
            }
          }
        }
      }
    }
  }
}
```

### Paginate items with cursor

```graphql
query GetNextPage($cursor: String!) {
  boards(ids: [1234567890]) {
    items_page(limit: 50, cursor: $cursor) {
      cursor
      items {
        id
        name
        updated_at
      }
    }
  }
}
```

### Filter items by column values

```graphql
query {
  items_page_by_column_values(
    limit: 50
    board_id: 1234567890
    columns: [
      { column_id: "status", column_values: ["Done"] }
      { column_id: "person", column_values: ["12345678"] }
    ]
  ) {
    cursor
    items {
      id
      name
    }
  }
}
```

### Create an item

```graphql
mutation {
  create_item(
    board_id: 1234567890
    group_id: "topics"
    item_name: "New task"
    column_values: "{\"status\": {\"label\": \"In Progress\"}, \"date4\": {\"date\": \"2024-06-01\"}}"
    create_labels_if_missing: false
  ) {
    id
    name
  }
}
```

### Update multiple column values

```graphql
mutation {
  change_multiple_column_values(
    item_id: 9876543210
    board_id: 1234567890
    column_values: "{\"text\": \"Updated text\", \"numbers\": \"42\"}"
  ) {
    id
    name
    column_values {
      id
      text
    }
  }
}
```

### Register a webhook

```graphql
mutation {
  create_webhook(
    board_id: 1234567890
    url: "https://your-app.example.com/webhook"
    event: change_column_value
    config: "{\"columnId\": \"status\"}"
  ) {
    id
    event
  }
}
```

### Post an update (comment)

```graphql
mutation {
  create_update(
    item_id: 9876543210
    body: "This item has been reviewed and approved."
  ) {
    id
    body
    created_at
    creator {
      id
      name
    }
  }
}
```

---

## Versioning

Monday.com uses date-based API versioning. Pass the `API-Version` header to opt into a specific version:

```
API-Version: 2024-01
```

Breaking changes are introduced in new versions; the previous version remains active for a deprecation window. Consult the [changelog](https://developer.monday.com/api-reference/changelog) for migration details.

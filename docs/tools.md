# Tools

Breakcold MCP tools are generated from the same public API contract as the REST API.

## Discovery

| Tool | What it does | Required scopes | Token cost |
| --- | --- | --- | --- |
| `capabilities_list` | List Breakcold MCP capabilities, granted scopes, and the tools available in each capability group. | Authenticated session | 0 tokens |
| `tools_search` | Search available Breakcold MCP tools by name, description, capability, or required scope. | Authenticated session | 0 tokens |

## Workspaces

| Tool | What it does | Required scopes | Token cost |
| --- | --- | --- | --- |
| `workspaces_list` | List workspaces the authenticated Breakcold actor can access. | Authenticated session | 0 tokens |

## CRM

| Tool | What it does | Required scopes | Token cost |
| --- | --- | --- | --- |
| `crm_field_options_create` | Create a CRM field option. | `crm:metadata:write` | 0.4 tokens |
| `crm_field_options_delete` | Delete a CRM field option. | `crm:metadata:write` | 0.4 tokens |
| `crm_field_options_list` | List CRM field options. | `crm:metadata:read` | 0.2 tokens |
| `crm_field_options_reorder` | Reorder CRM field options. | `crm:metadata:write` | 0.4 tokens |
| `crm_field_options_update` | Update a CRM field option. | `crm:metadata:write` | 0.4 tokens |
| `crm_fields_create` | Create a CRM field definition. | `crm:metadata:write` | 0.4 tokens |
| `crm_fields_delete` | Permanently delete a custom CRM field definition, its field options and stored values or relations, and remove it from saved view configurations. | `crm:metadata:write` | 0.4 tokens |
| `crm_fields_get` | Fetch one CRM field definition. | `crm:metadata:read` | 0.2 tokens |
| `crm_fields_list` | List CRM field definitions. | `crm:metadata:read` | 0.2 tokens |
| `crm_fields_reorder` | Reorder CRM fields. | `crm:metadata:write` | 0.4 tokens |
| `crm_fields_update` | Update a CRM field definition. | `crm:metadata:write` | 0.4 tokens |
| `crm_objects_create` | Create a custom CRM object type. | `crm:metadata:write` | 0.4 tokens |
| `crm_objects_delete` | Permanently delete a custom CRM object type together with its custom field definitions, field options, and saved views. | `crm:metadata:write` | 0.4 tokens |
| `crm_objects_get` | Fetch one CRM object type. | `crm:metadata:read` | 0.2 tokens |
| `crm_objects_list` | List CRM object types in a workspace. | `crm:metadata:read` | 0.2 tokens |
| `crm_objects_reorder` | Reorder CRM object types. | `crm:metadata:write` | 0.4 tokens |
| `crm_objects_update` | Update CRM object metadata. | `crm:metadata:write` | 0.4 tokens |
| `crm_record_views_create` | Create a CRM record view. | `crm:metadata:write` | 0.4 tokens |
| `crm_record_views_delete` | Delete a CRM record view. | `crm:metadata:write` | 0.4 tokens |
| `crm_record_views_get` | Fetch one CRM record view. | `crm:metadata:read` | 0.2 tokens |
| `crm_record_views_list` | List CRM record views. | `crm:metadata:read` | 0.2 tokens |
| `crm_record_views_reorder` | Reorder CRM record views. | `crm:metadata:write` | 0.4 tokens |
| `crm_record_views_update` | Update a CRM record view and replace its saved config. | `crm:metadata:write` | 0.4 tokens |

## Records

| Tool | What it does | Required scopes | Token cost |
| --- | --- | --- | --- |
| `custom_activities_create` | Create a custom activity linked to a CRM record and notify supplied participant users in-app and, when enabled by their preferences, by email. | `records:write` | 0.4 tokens |
| `records_archive` | Archive a CRM record. | `records:write` | 0.4 tokens |
| `records_create` | Create one complete CRM record atomically. Supply every required field and a stable idempotency key. When creating a deal from a person, include that person's record id in the deal contact relation field; do not create a partial deal and patch it later. | `records:write` | 0.4 tokens |
| `records_get` | Fetch a CRM record by ID. | `records:read` | 0.2 tokens |
| `records_list` | List or search CRM records. | `records:read` | 0.2 tokens |
| `records_restore` | Restore an archived CRM record. | `records:write` | 0.4 tokens |
| `records_update` | Update CRM record field values. Multiselect fields append by default; pass multiselectMode: "replace" to overwrite them (omitted options removed, [] clears). Identity-field changes may start configured automatic enrichment, and matching active webhooks receive field-update events at their external URLs. | `records:write` | 0.4 tokens |

## Tasks

| Tool | What it does | Required scopes | Token cost |
| --- | --- | --- | --- |
| `tasks_complete` | Mark a task as done. | `tasks:write` | 0.4 tokens |
| `tasks_create` | Create a task linked to a record. | `tasks:write` | 0.4 tokens |
| `tasks_list` | List tasks linked to a record. | `tasks:read` | 0.2 tokens |
| `tasks_update` | Update task details. | `tasks:write` | 0.4 tokens |

## Notes

| Tool | What it does | Required scopes | Token cost |
| --- | --- | --- | --- |
| `notes_create` | Create a note linked to a record. Matching active webhooks receive the note-created event at their external URLs. | `notes:write` | 0.4 tokens |
| `notes_get` | Fetch a note by ID. | `notes:read` | 0.2 tokens |
| `notes_list` | List notes linked to a record. | `notes:read` | 0.2 tokens |
| `notes_update` | Update note content. Matching active webhooks receive the note-updated event at their external URLs. | `notes:write` | 0.4 tokens |

## Inbox

| Tool | What it does | Required scopes | Token cost |
| --- | --- | --- | --- |
| `inbox_attachments_upload_url_create` | Generate a temporary Convex file-storage upload URL for inbox send attachments without uploading or modifying a file. | `inbox:send` | 0.4 tokens |
| `inbox_conversations_compose` | Send a new email, LinkedIn, Telegram, or WhatsApp message immediately and return the resulting conversation and message. | `inbox:send` | 0.4 tokens |
| `inbox_conversations_list` | List conversations linked to a record. | `inbox:read` | 0.2 tokens |
| `inbox_conversations_search` | List or search inbox conversations in a workspace, optionally filtered by inbox view or record-link state. | `inbox:read` | 0.2 tokens |
| `inbox_messages_get` | Fetch one inbox message. | `inbox:read` | 0.2 tokens |
| `inbox_messages_list` | List messages for a conversation. | `inbox:read` | 0.2 tokens |
| `inbox_messages_send` | Send an immediate social message, email reply, or email forward in an existing inbox conversation. | `inbox:send` | 0.4 tokens |
| `inbox_send_accounts_list` | List inbox accounts you can send from, including the workspaceIntegrationAccountId to use with inbox_conversations_compose. | `inbox:send` | 0.2 tokens |
| `inbox_views_create` | Create an inbox view. | `inbox:views:write` | 0.4 tokens |
| `inbox_views_delete` | Delete an inbox view. | `inbox:views:write` | 0.4 tokens |
| `inbox_views_get` | Fetch one inbox view. | `inbox:views:read` | 0.2 tokens |
| `inbox_views_list` | List visible inbox views. | `inbox:views:read` | 0.2 tokens |
| `inbox_views_sidebar_get` | Fetch inbox view sidebar layout. | `inbox:views:read` | 0.2 tokens |
| `inbox_views_sidebar_reorder` | Reorder inbox view sidebar items. | `inbox:views:write` | 0.4 tokens |
| `inbox_views_update` | Update an inbox view and replace its saved config. | `inbox:views:write` | 0.4 tokens |

## Meetings

| Tool | What it does | Required scopes | Token cost |
| --- | --- | --- | --- |
| `meetings_get` | Fetch one meeting with recording and transcript metadata. | `meetings:read` | 0.2 tokens |
| `meetings_list` | List workspace meetings with recording and transcript metadata. | `meetings:read` | 0.2 tokens |
| `meetings_transcript_list` | List full meeting transcript text in ordered paginated segments. | `meetings:read` | 0.2 tokens |

## Webhooks

| Tool | What it does | Required scopes | Token cost |
| --- | --- | --- | --- |
| `integration_hooks_create` | Create and activate a Zapier or Make webhook subscription that delivers matching future Breakcold events to the supplied external HTTPS URL. | `webhooks:write` | 0.4 tokens |
| `integration_hooks_delete` | Permanently delete a Zapier or Make instant-trigger hook and its stored delivery logs. | `webhooks:write` | 0.4 tokens |
| `integration_samples_list` | List generated sample payloads for instant triggers. | `webhooks:write` | 0.2 tokens |

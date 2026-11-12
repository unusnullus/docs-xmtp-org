# GroupPermissions

Group defaults or member restrictions. The server normalizes permission implications and intersects a restriction with the current defaults; a restriction cannot grant more than the defaults.

| Field | Type | Required | Description and constraints |
| --- | --- | --- | --- |
| `can_add_web_page_previews` | boolean | No | Whether the add web page previews permission is present. Effective authority also depends on current membership, group defaults, hierarchy and product support. |
| `can_change_info` | boolean | No | Whether the change info permission is present. Effective authority also depends on current membership, group defaults, hierarchy and product support. |
| `can_edit_tag` | boolean | No | Whether the edit tag permission is present. Effective authority also depends on current membership, group defaults, hierarchy and product support. |
| `can_invite_users` | boolean | No | Whether the invite users permission is present. Effective authority also depends on current membership, group defaults, hierarchy and product support. |
| `can_manage_topics` | boolean | No | Whether the manage topics permission is present. Effective authority also depends on current membership, group defaults, hierarchy and product support. |
| `can_pin_messages` | boolean | No | Whether the pin messages permission is present. Effective authority also depends on current membership, group defaults, hierarchy and product support. |
| `can_react_to_messages` | boolean | No | Whether the react to messages permission is present. Effective authority also depends on current membership, group defaults, hierarchy and product support. |
| `can_send_audios` | boolean | No | Whether the send audios permission is present. Effective authority also depends on current membership, group defaults, hierarchy and product support. |
| `can_send_documents` | boolean | No | Whether the send documents permission is present. Effective authority also depends on current membership, group defaults, hierarchy and product support. |
| `can_send_messages` | boolean | No | Whether the send messages permission is present. Effective authority also depends on current membership, group defaults, hierarchy and product support. |
| `can_send_other_messages` | boolean | No | Whether the send other messages permission is present. Effective authority also depends on current membership, group defaults, hierarchy and product support. |
| `can_send_photos` | boolean | No | Whether the send photos permission is present. Effective authority also depends on current membership, group defaults, hierarchy and product support. |
| `can_send_polls` | boolean | No | Whether the send polls permission is present. Effective authority also depends on current membership, group defaults, hierarchy and product support. |
| `can_send_video_notes` | boolean | No | Whether the send video notes permission is present. Effective authority also depends on current membership, group defaults, hierarchy and product support. |
| `can_send_videos` | boolean | No | Whether the send videos permission is present. Effective authority also depends on current membership, group defaults, hierarchy and product support. |
| `can_send_voice_notes` | boolean | No | Whether the send voice notes permission is present. Effective authority also depends on current membership, group defaults, hierarchy and product support. |

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).

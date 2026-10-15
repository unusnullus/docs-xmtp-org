# UpdatesDropped

A notice that some updates were lost. This event is delivered regardless of the update filter.

You receive one of the following forms. Required fields apply within each form.

- `reason` is `"access_revoked"`, `"backlog_limit"`, or `"expired"`: [QueuedUpdatesDropped](queuedupdatesdropped.md).

- `reason` is `"source_gap"`: [SourceUpdatesDropped](sourceupdatesdropped.md).

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).

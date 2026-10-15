# InputRichText

Inline rich text represented as a string or an ordered sequence of text nodes. Use this node when sending rich content. Combine strings, formatting nodes, and nested arrays to build rich text. The complete rich message can use up to 32,768 UTF-16 code units and a nesting depth of 16. A button node contains its button definition in the button field.

Supply exactly one of the following forms. Required fields apply within each form.

- The value has the type string.

- One supported inline text node: [InputRichInline](inputrichinline.md).

- The value has the type Array of [InputRichText](inputrichtext.md).

Return to the [type index](../types.md) or find an operation in the [API index](../../api.md).

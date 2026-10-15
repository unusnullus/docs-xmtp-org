# API types

Find the models for your workflow below. Each entry explains what you send or receive and links to its fields.

## Bot identity, profile, and commands <a href="#bot-identity-profile-and-commands" id="bot-identity-profile-and-commands"></a>

| Type | Description |
| --- | --- |
| [AdminRights](types/adminrights.md) | The administrator permissions requested by your bot when it is added to a chat. |
| [Bot](types/bot.md) | Your bot's identity, effective settings, and feature availability. |
| [Command](types/command.md) | One command displayed in your bot's command menu. |
| [Commands](types/commands.md) | The command set stored for the requested scope and language. |
| [CommandsReceipt](types/commandsreceipt.md) | Confirmation of a command configuration change, including the resulting bot UI version. |
| [PatchProfile](types/patchprofile.md) | Change the default profile text or one localization. |
| [Profile](types/profile.md) | Your bot's default profile text and localizations. |
| [ProfileReceipt](types/profilereceipt.md) | The resulting profile text and version after an update. |
| [ProfileText](types/profiletext.md) | One set of profile text fields, used for the default profile or a localization. |
| [ResolvedCommands](types/resolvedcommands.md) | The selected command set and the scope and language that matched. |
| [SetAdminRights](types/setadminrights.md) | The default administrator rights your bot requests for groups or channels. |
| [SetCommands](types/setcommands.md) | The ordered command list to store for the requested scope and language. |
| [SetProfilePhoto](types/setprofilephoto.md) | The uploaded photo or animation to use as your bot's profile photo. |
| [UserProfilePhotos](types/userprofilephotos.md) | The user's current profile photo, returned in up to four sizes. |

## Chats and users <a href="#chats-and-users" id="chats-and-users"></a>

| Type | Description |
| --- | --- |
| [Chat](types/chat.md) | The identity and kind of a chat available to your bot. |
| [ChatFullInfo](types/chatfullinfo.md) | The current information for an existing private chat and its user. |
| [ChatMember](types/chatmember.md) | The user's membership status in a chat. |
| [PrivateChat](types/privatechat.md) | The private chat identity and its user. |
| [SharedUser](types/shareduser.md) | A user shared with your bot through a request_users button. |
| [User](types/user.md) | The user identity returned with messages and events, including the ARX app language when known. |
| [UserIdentity](types/useridentity.md) | The user ID and public identity of an account. |

## Delivery and API status <a href="#delivery-and-api-status" id="delivery-and-api-status"></a>

| Type | Description |
| --- | --- |
| [Binary](types/binary.md) | The original file bytes returned by a download. |
| [Boolean](types/boolean.md) | A boolean result confirming whether the operation succeeded. |
| [Empty](types/empty.md) | An empty JSON object. |
| [ErrorEnvelope](types/errorenvelope.md) | A failed API request. ok is always false; error contains the code, message, and request identifier. |
| [OpenApi](types/openapi.md) | The public OpenAPI document returned by openapi.get, without the usual result envelope. |
| [Platform](types/platform.md) | The capabilities, supported media and reactions, effects, and limits reported by ARX. |
| [PlatformLimits](types/platformlimits.md) | The current messaging, file, update, and configuration limits reported by ARX. |
| [SetWebhook](types/setwebhook.md) | The HTTPS endpoint, verification settings, and update filter for webhook delivery. |
| [WebhookInfo](types/webhookinfo.md) | Your bot's delivery mode, saved webhook settings, queue size, and recent delivery errors. |
| [WebhookTest](types/webhooktest.md) | The HTTP status and elapsed time from a test of your configured webhook endpoint. |

## Keyboards and callbacks <a href="#keyboards-and-callbacks" id="keyboards-and-callbacks"></a>

| Type | Description |
| --- | --- |
| [CallbackAnswer](types/callbackanswer.md) | The notification, alert, or bot start link used to answer an inline button press. |
| [CallbackQuery](types/callbackquery.md) | An inline button press received by your bot. |
| [InlineButton](types/inlinebutton.md) | An inline keyboard button. |
| [InlineButtonCallback](types/inlinebuttoncallback.md) | A button that sends a callback query to your bot. |
| [InlineButtonCopyText](types/inlinebuttoncopytext.md) | A button that copies its configured text to the user's clipboard. |
| [InlineButtonDisabled](types/inlinebuttondisabled.md) | A button displayed without an action. |
| [InlineButtonUrl](types/inlinebuttonurl.md) | A button that opens a link. |
| [InlineKeyboard](types/inlinekeyboard.md) | The inline keyboard attached to a received message, with buttons arranged in rows. |
| [InputForceReply](types/inputforcereply.md) | A request to open the reply input for the user. |
| [InputInlineButton](types/inputinlinebutton.md) | An inline button to attach to a message. |
| [InputInlineButtonCallback](types/inputinlinebuttoncallback.md) | A button that sends a callback query to your bot. |
| [InputInlineButtonCopyText](types/inputinlinebuttoncopytext.md) | A button that copies its configured text to the user's clipboard. |
| [InputInlineButtonDisabled](types/inputinlinebuttondisabled.md) | A button displayed without an action. |
| [InputInlineButtonUrl](types/inputinlinebuttonurl.md) | A button that opens a link. |
| [InputInlineKeyboard](types/inputinlinekeyboard.md) | An inline keyboard to attach to a message. |
| [InputRemoveKeyboard](types/inputremovekeyboard.md) | A request to remove the user's custom reply keyboard. |
| [InputReplyButton](types/inputreplybutton.md) | A reply keyboard button that sends text or requests information from the user. |
| [InputReplyButtonRequestContact](types/inputreplybuttonrequestcontact.md) | A reply button that asks the user to share their contact details with your bot. |
| [InputReplyButtonRequestLocation](types/inputreplybuttonrequestlocation.md) | A reply button that asks the user to share their location. |
| [InputReplyButtonRequestUsers](types/inputreplybuttonrequestusers.md) | A reply button that asks the user to select accounts to share with your bot. |
| [InputReplyButtonText](types/inputreplybuttontext.md) | A reply button that sends its label as a text message. |
| [InputReplyKeyboard](types/inputreplykeyboard.md) | A custom reply keyboard arranged in rows, with optional display settings. |
| [MenuButton](types/menubutton.md) | The menu button configuration for your bot or one private chat. |
| [ReplyMarkup](types/replymarkup.md) | A keyboard or reply input instruction to attach to a message. |

## Locations and contacts <a href="#locations-and-contacts" id="locations-and-contacts"></a>

| Type | Description |
| --- | --- |
| [Contact](types/contact.md) | An account contact card or a manual contact card returned in a message. |
| [ContactContent](types/contactcontent.md) | The contact card content returned within a message. |
| [EditLiveLocation](types/editlivelocation.md) | Changes to an active live location authored by your bot. |
| [InputContact](types/inputcontact.md) | The account ID or manual contact details to send as a contact card. |
| [InputLiveLocation](types/inputlivelocation.md) | The duration and optional movement settings for new live location sharing. |
| [InputLocation](types/inputlocation.md) | The coordinates and optional live sharing settings to send as a location. |
| [InputVenue](types/inputvenue.md) | The coordinates, name, address, and optional provider details to send as a venue card. |
| [LiveLocation](types/livelocation.md) | The duration, expiration, and movement settings of live location sharing. |
| [Location](types/location.md) | The coordinates and optional live sharing state returned in a location message. |
| [LocationContent](types/locationcontent.md) | The location content returned within a message. |
| [StopLiveLocation](types/stoplivelocation.md) | The optional inline keyboard replacement to apply when stopping live sharing. |
| [Venue](types/venue.md) | The venue name, address, coordinates, and optional provider information returned in a message. |
| [VenueContent](types/venuecontent.md) | The venue content returned within a message. |
| [VenueProvider](types/venueprovider.md) | The map provider and its place identifier and category. |

## Messages and files <a href="#messages-and-files" id="messages-and-files"></a>

| Type | Description |
| --- | --- |
| [Album](types/album.md) | The messages created by one album request, in album order. |
| [AlbumAudio](types/albumaudio.md) | The file source and caption settings for an audio file in an album. |
| [AlbumDocument](types/albumdocument.md) | The file source and caption settings for a document in an album. |
| [AlbumItem](types/albumitem.md) | One supported media item in an album request. |
| [AlbumLivePhoto](types/albumlivephoto.md) | The file source and caption settings for a live photo in an album. |
| [AlbumPhoto](types/albumphoto.md) | The file source and caption settings for a photo in an album. |
| [AlbumVideo](types/albumvideo.md) | The file source and caption settings for a video in an album. |
| [Animation](types/animation.md) | A received message containing an animation and its metadata. |
| [Audio](types/audio.md) | A received message containing an audio file and its metadata. |
| [Content](types/content.md) | The content of a received message. |
| [ContentMessage](types/contentmessage.md) | A received message containing text, media, or another supported content variant. |
| [CopyMessages](types/copymessages.md) | The source message IDs and optional replacements for a copy request. |
| [DeleteBatch](types/deletebatch.md) | The IDs deleted by a batch request and the IDs that were skipped. |
| [DeleteMessages](types/deletemessages.md) | The message IDs to remove from one chat. |
| [DeletedMessage](types/deletedmessage.md) | The identity of a message reported as deleted. |
| [Dice](types/dice.md) | An animated dice message with its emoji and generated result. |
| [DiceValue](types/dicevalue.md) | The allowed dice emoji and result range reported by the platform. |
| [Document](types/document.md) | A received message containing a document and its metadata. |
| [EditMessage](types/editmessage.md) | The content, caption, live location, or inline keyboard changes to apply to a message. |
| [File](types/file.md) | A file upload result or metadata returned by files.get. |
| [ForwardMessages](types/forwardmessages.md) | The source message IDs and delivery options for a forward request. |
| [ForwardOrigin](types/forwardorigin.md) | The original sender and source message attribution retained by a forwarded message. |
| [InaccessibleMessage](types/inaccessiblemessage.md) | The identity of a message whose content is unavailable to your bot. |
| [InputAnimation](types/inputanimation.md) | The file source and message settings for sending an animation. |
| [InputAudio](types/inputaudio.md) | The file source and message settings for sending an audio file. |
| [InputContent](types/inputcontent.md) | The message content to send. |
| [InputDice](types/inputdice.md) | The dice emoji to send. |
| [InputDocument](types/inputdocument.md) | The file source and message settings for sending a document. |
| [InputDraftFile](types/inputdraftfile.md) | An existing file available to your bot for use in a draft. |
| [InputEditContent](types/inputeditcontent.md) | Replacement message content accepted by messages.edit. |
| [InputFile](types/inputfile.md) | One file source for message media: an existing file, a named upload part, or a public URL. |
| [InputLivePhoto](types/inputlivephoto.md) | The photo and short video sources to send together as one live photo. |
| [InputPhoto](types/inputphoto.md) | The file source and message settings for sending a photo. |
| [InputReply](types/inputreply.md) | The message to reply to, with an optional quote and a choice of how to handle a missing target. |
| [InputThumbnail](types/inputthumbnail.md) | A separate JPEG upload used as a custom media thumbnail. |
| [InputVideo](types/inputvideo.md) | The file source and message settings for sending a video. |
| [InputVideoNote](types/inputvideonote.md) | The file source and message settings for sending a round video note. |
| [InputVoice](types/inputvoice.md) | The file source and message settings for sending a voice message. |
| [LiveInputFile](types/liveinputfile.md) | An existing file or named binary part for a live photo. |
| [LivePhoto](types/livephoto.md) | A photo and its associated short video returned as one live photo message. |
| [MaybeInaccessibleMessage](types/maybeinaccessiblemessage.md) | A message returned with its content when accessible, or only its identity when unavailable. |
| [MediaFile](types/mediafile.md) | The file identifier and measured metadata for media embedded in a message. |
| [Message](types/message.md) | A content message or a service message available to your bot. |
| [MessageBatch](types/messagebatch.md) | The messages created by a copy or forward request and any source IDs that were skipped. |
| [MessageEffect](types/messageeffect.md) | One visual message effect available for the listed chat kinds. |
| [MessageEffects](types/messageeffects.md) | The catalog of message effects and its revision. |
| [MessageReactions](types/messagereactions.md) | The current reaction counts and selected reaction for a message. |
| [Photo](types/photo.md) | The available sizes of a photo. |
| [PhotoContent](types/photocontent.md) | The photo content returned within a message. |
| [PinMessage](types/pinmessage.md) | The notification settings for pinning a message. |
| [Pins](types/pins.md) | The pinned message IDs returned for a chat. |
| [Reaction](types/reaction.md) | An emoji or custom emoji reaction selected by your bot or a user. |
| [ReactionState](types/reactionstate.md) | The current reactions and state version returned after setting a message reaction. |
| [Reply](types/reply.md) | The reply target returned with a message, including any quoted excerpt. |
| [SendAction](types/sendaction.md) | The temporary activity your bot displays in a chat. |
| [SendAlbum](types/sendalbum.md) | The media items and delivery settings for a new album. |
| [SendMessage](types/sendmessage.md) | The content and delivery settings for a new message. |
| [ServiceMessage](types/servicemessage.md) | A received message that reports a service event rather than ordinary content. |
| [SetDraft](types/setdraft.md) | The text or rich content and stop controls for a temporary draft. |
| [SetReactions](types/setreactions.md) | The reaction to apply to a message. |
| [UnpinMessage](types/unpinmessage.md) | The optional message ID selecting which pinned message to remove. |
| [UploadFile](types/uploadfile.md) | The file kind and optional filename sent in the JSON payload part of a file upload. |
| [Video](types/video.md) | A received message containing a video and its metadata. |
| [VideoNote](types/videonote.md) | A received message containing a round video note and its metadata. |
| [Voice](types/voice.md) | A received message containing a voice message and its metadata. |

## Polls and checklists <a href="#polls-and-checklists" id="polls-and-checklists"></a>

| Type | Description |
| --- | --- |
| [Checklist](types/checklist.md) | The checklist title, tasks, and collaboration settings returned in a message. |
| [ChecklistContent](types/checklistcontent.md) | The checklist content returned within a message. |
| [ChecklistTask](types/checklisttask.md) | A checklist task with its identifier, label, and completion state. |
| [ChecklistTaskText](types/checklisttasktext.md) | A checklist task label and its formatting entities. |
| [ChecklistTitle](types/checklisttitle.md) | The checklist heading and its formatting entities. |
| [InputChecklist](types/inputchecklist.md) | The title, tasks, and collaboration settings for a new checklist. |
| [InputChecklistContent](types/inputchecklistcontent.md) | The checklist content to send in a new message. |
| [InputChecklistTask](types/inputchecklisttask.md) | One task to create in a checklist, with an identifier unique within that checklist. |
| [InputPoll](types/inputpoll.md) | The question, options, and voting rules for a new poll or quiz. |
| [InputPollContent](types/inputpollcontent.md) | The poll content to send in a new message. |
| [InputPollLocation](types/inputpolllocation.md) | A static location to attach to a poll. |
| [InputPollMedia](types/inputpollmedia.md) | A supported media attachment for a poll question or explanation. |
| [InputPollOption](types/inputpolloption.md) | One poll option, with a label and optional media attachment. |
| [InputPollOptionMedia](types/inputpolloptionmedia.md) | A supported media attachment or link for a poll option. |
| [InputPollVenue](types/inputpollvenue.md) | A static venue to attach to a poll, including coordinates, name, address, and optional provider details. |
| [InputReplyButtonRequestPoll](types/inputreplybuttonrequestpoll.md) | A reply button that asks the user to create a poll. |
| [Poll](types/poll.md) | The current state of a poll or quiz, including options, votes, and closing settings. |
| [PollAnswer](types/pollanswer.md) | A user's current selection of poll option IDs. |
| [PollContent](types/pollcontent.md) | The poll content returned within a message. |
| [PollDescription](types/polldescription.md) | The additional poll description and its formatting entities. |
| [PollExplanation](types/pollexplanation.md) | The quiz explanation and its formatting entities. |
| [PollLink](types/polllink.md) | An HTTP or HTTPS link attached to a poll option. |
| [PollLocation](types/polllocation.md) | A static location returned as poll media. |
| [PollMedia](types/pollmedia.md) | A media attachment returned with a poll question or explanation. |
| [PollOption](types/polloption.md) | A poll option with a stable UUID, its label, and vote count. |
| [PollOptionMedia](types/polloptionmedia.md) | A media attachment or link returned with a poll option. |
| [PollOptionText](types/polloptiontext.md) | A poll option label and its formatting entities. |
| [PollQuestion](types/pollquestion.md) | The poll question text and its formatting entities. |
| [PollVenue](types/pollvenue.md) | A venue returned as poll media, including its location and address. |
| [StopPoll](types/stoppoll.md) | The optional inline keyboard replacement to apply when closing a poll. |

## Rich message blocks and text <a href="#rich-message-blocks-and-text" id="rich-message-blocks-and-text"></a>

| Type | Description |
| --- | --- |
| [InputDraftRich](types/inputdraftrich.md) | Rich content for a temporary draft, supplied as blocks, HTML, or ARX Markdown. |
| [InputDraftRichAnchor](types/inputdraftrichanchor.md) | A named position that links can target within the rich message. |
| [InputDraftRichAnimation](types/inputdraftrichanimation.md) | An animation block with optional caption text. |
| [InputDraftRichAudio](types/inputdraftrichaudio.md) | An audio block with optional caption text. |
| [InputDraftRichBlock](types/inputdraftrichblock.md) | One supported rich content block. |
| [InputDraftRichBlockquote](types/inputdraftrichblockquote.md) | A block containing quoted rich content. |
| [InputDraftRichButton](types/inputdraftrichbutton.md) | An interactive button embedded in rich content. |
| [InputDraftRichButtons](types/inputdraftrichbuttons.md) | A row of buttons embedded in rich content. |
| [InputDraftRichCaption](types/inputdraftrichcaption.md) | The text and optional credit associated with rich media. |
| [InputDraftRichCollage](types/inputdraftrichcollage.md) | A collage of media items within rich content. |
| [InputDraftRichDetails](types/inputdraftrichdetails.md) | A collapsible section with a summary and nested content. |
| [InputDraftRichDivider](types/inputdraftrichdivider.md) | A horizontal divider between rich content blocks. |
| [InputDraftRichDocument](types/inputdraftrichdocument.md) | A document block with optional caption text. |
| [InputDraftRichExpandableBlockquote](types/inputdraftrichexpandableblockquote.md) | A quoted text block that the user can expand. |
| [InputDraftRichFooter](types/inputdraftrichfooter.md) | A footer text block within rich content. |
| [InputDraftRichHeading](types/inputdraftrichheading.md) | A rich heading at the selected size. |
| [InputDraftRichInline](types/inputdraftrichinline.md) | One supported inline text node. |
| [InputDraftRichInlineAnchor](types/inputdraftrichinlineanchor.md) | A named anchor within inline text. |
| [InputDraftRichInlineAnchorLink](types/inputdraftrichinlineanchorlink.md) | An inline link to a named anchor in this message. |
| [InputDraftRichInlineBankCard](types/inputdraftrichinlinebankcard.md) | A bank card number displayed within rich text. |
| [InputDraftRichInlineBold](types/inputdraftrichinlinebold.md) | Inline text displayed in bold. |
| [InputDraftRichInlineBotCommand](types/inputdraftrichinlinebotcommand.md) | A bot command displayed within rich text. |
| [InputDraftRichInlineButton](types/inputdraftrichinlinebutton.md) | An interactive button within inline rich text. |
| [InputDraftRichInlineButtonCallback](types/inputdraftrichinlinebuttoncallback.md) | A button that sends a callback query to your bot. |
| [InputDraftRichInlineButtonCopyText](types/inputdraftrichinlinebuttoncopytext.md) | A button that copies its configured text to the user's clipboard. |
| [InputDraftRichInlineButtonDisabled](types/inputdraftrichinlinebuttondisabled.md) | A button displayed without an action. |
| [InputDraftRichInlineButtonUrl](types/inputdraftrichinlinebuttonurl.md) | A button that opens a link. |
| [InputDraftRichInlineCashtag](types/inputdraftrichinlinecashtag.md) | A cashtag displayed within rich text. |
| [InputDraftRichInlineCode](types/inputdraftrichinlinecode.md) | Literal inline code text. |
| [InputDraftRichInlineDateTime](types/inputdraftrichinlinedatetime.md) | A formatted date and time within inline rich text. |
| [InputDraftRichInlineEmail](types/inputdraftrichinlineemail.md) | An email link within rich text. |
| [InputDraftRichInlineHashtag](types/inputdraftrichinlinehashtag.md) | A hashtag displayed within rich text. |
| [InputDraftRichInlineItalic](types/inputdraftrichinlineitalic.md) | Inline text displayed in italics. |
| [InputDraftRichInlineMarked](types/inputdraftrichinlinemarked.md) | Highlighted inline text. |
| [InputDraftRichInlineMath](types/inputdraftrichinlinemath.md) | An inline mathematical expression. |
| [InputDraftRichInlineMention](types/inputdraftrichinlinemention.md) | An account handle mention within rich text. |
| [InputDraftRichInlinePhone](types/inputdraftrichinlinephone.md) | A telephone link within rich text. |
| [InputDraftRichInlineReferenceLink](types/inputdraftrichinlinereferencelink.md) | An inline link to a named reference in this message. |
| [InputDraftRichInlineSpoiler](types/inputdraftrichinlinespoiler.md) | Inline text concealed until the user reveals it. |
| [InputDraftRichInlineStrikethrough](types/inputdraftrichinlinestrikethrough.md) | Inline text displayed with a strikethrough. |
| [InputDraftRichInlineSubscript](types/inputdraftrichinlinesubscript.md) | Inline text displayed as a subscript. |
| [InputDraftRichInlineSuperscript](types/inputdraftrichinlinesuperscript.md) | Inline text displayed as a superscript. |
| [InputDraftRichInlineTextMention](types/inputdraftrichinlinetextmention.md) | An inline mention linked to a user ID. |
| [InputDraftRichInlineUnderline](types/inputdraftrichinlineunderline.md) | Underlined inline text. |
| [InputDraftRichInlineUrl](types/inputdraftrichinlineurl.md) | An inline text link to a URL. |
| [InputDraftRichList](types/inputdraftrichlist.md) | An ordered or unordered list of rich content items. |
| [InputDraftRichMap](types/inputdraftrichmap.md) | A map block showing coordinates within rich content. |
| [InputDraftRichMath](types/inputdraftrichmath.md) | A mathematical expression displayed as a block. |
| [InputDraftRichParagraph](types/inputdraftrichparagraph.md) | A paragraph of inline rich text. |
| [InputDraftRichPhoto](types/inputdraftrichphoto.md) | A photo block with optional caption text. |
| [InputDraftRichPreformatted](types/inputdraftrichpreformatted.md) | A code or preformatted text block, with optional syntax highlighting. |
| [InputDraftRichPullQuote](types/inputdraftrichpullquote.md) | A prominent quoted text block with optional credit. |
| [InputDraftRichReference](types/inputdraftrichreference.md) | A named reference that links in the rich message can target. |
| [InputDraftRichSlideshow](types/inputdraftrichslideshow.md) | A sequence of media items displayed as a slideshow. |
| [InputDraftRichTable](types/inputdraftrichtable.md) | A rich table containing rows and cells. |
| [InputDraftRichText](types/inputdraftrichtext.md) | Inline rich text represented as a string or an ordered sequence of text nodes. |
| [InputDraftRichVideo](types/inputdraftrichvideo.md) | A video block with optional caption text. |
| [InputDraftRichVoice](types/inputdraftrichvoice.md) | A voice message block with optional caption text. |
| [InputRich](types/inputrich.md) | Structured rich message content, supplied as blocks, HTML, or ARX Markdown. |
| [InputRichAnchor](types/inputrichanchor.md) | A named position that links can target within the rich message. |
| [InputRichAnimation](types/inputrichanimation.md) | An animation block with optional caption text. |
| [InputRichAudio](types/inputrichaudio.md) | An audio block with optional caption text. |
| [InputRichBlock](types/inputrichblock.md) | One supported rich content block. |
| [InputRichBlockquote](types/inputrichblockquote.md) | A block containing quoted rich content. |
| [InputRichButton](types/inputrichbutton.md) | An interactive button embedded in rich content. |
| [InputRichButtons](types/inputrichbuttons.md) | A row of buttons embedded in rich content. |
| [InputRichCaption](types/inputrichcaption.md) | The text and optional credit associated with rich media. |
| [InputRichCollage](types/inputrichcollage.md) | A collage of media items within rich content. |
| [InputRichDetails](types/inputrichdetails.md) | A collapsible section with a summary and nested content. |
| [InputRichDivider](types/inputrichdivider.md) | A horizontal divider between rich content blocks. |
| [InputRichDocument](types/inputrichdocument.md) | A document block with optional caption text. |
| [InputRichExpandableBlockquote](types/inputrichexpandableblockquote.md) | A quoted text block that the user can expand. |
| [InputRichFooter](types/inputrichfooter.md) | A footer text block within rich content. |
| [InputRichHeading](types/inputrichheading.md) | A rich heading at the selected size. |
| [InputRichInline](types/inputrichinline.md) | One supported inline text node. |
| [InputRichInlineAnchor](types/inputrichinlineanchor.md) | A named anchor within inline text. |
| [InputRichInlineAnchorLink](types/inputrichinlineanchorlink.md) | An inline link to a named anchor in this message. |
| [InputRichInlineBankCard](types/inputrichinlinebankcard.md) | A bank card number displayed within rich text. |
| [InputRichInlineBold](types/inputrichinlinebold.md) | Inline text displayed in bold. |
| [InputRichInlineBotCommand](types/inputrichinlinebotcommand.md) | A bot command displayed within rich text. |
| [InputRichInlineButton](types/inputrichinlinebutton.md) | An interactive button within inline rich text. |
| [InputRichInlineButtonCallback](types/inputrichinlinebuttoncallback.md) | A button that sends a callback query to your bot. |
| [InputRichInlineButtonCopyText](types/inputrichinlinebuttoncopytext.md) | A button that copies its configured text to the user's clipboard. |
| [InputRichInlineButtonDisabled](types/inputrichinlinebuttondisabled.md) | A button displayed without an action. |
| [InputRichInlineButtonUrl](types/inputrichinlinebuttonurl.md) | A button that opens a link. |
| [InputRichInlineCashtag](types/inputrichinlinecashtag.md) | A cashtag displayed within rich text. |
| [InputRichInlineCode](types/inputrichinlinecode.md) | Literal inline code text. |
| [InputRichInlineDateTime](types/inputrichinlinedatetime.md) | A formatted date and time within inline rich text. |
| [InputRichInlineEmail](types/inputrichinlineemail.md) | An email link within rich text. |
| [InputRichInlineHashtag](types/inputrichinlinehashtag.md) | A hashtag displayed within rich text. |
| [InputRichInlineItalic](types/inputrichinlineitalic.md) | Inline text displayed in italics. |
| [InputRichInlineMarked](types/inputrichinlinemarked.md) | Highlighted inline text. |
| [InputRichInlineMath](types/inputrichinlinemath.md) | An inline mathematical expression. |
| [InputRichInlineMention](types/inputrichinlinemention.md) | An account handle mention within rich text. |
| [InputRichInlinePhone](types/inputrichinlinephone.md) | A telephone link within rich text. |
| [InputRichInlineReferenceLink](types/inputrichinlinereferencelink.md) | An inline link to a named reference in this message. |
| [InputRichInlineSpoiler](types/inputrichinlinespoiler.md) | Inline text concealed until the user reveals it. |
| [InputRichInlineStrikethrough](types/inputrichinlinestrikethrough.md) | Inline text displayed with a strikethrough. |
| [InputRichInlineSubscript](types/inputrichinlinesubscript.md) | Inline text displayed as a subscript. |
| [InputRichInlineSuperscript](types/inputrichinlinesuperscript.md) | Inline text displayed as a superscript. |
| [InputRichInlineTextMention](types/inputrichinlinetextmention.md) | An inline mention linked to a user ID. |
| [InputRichInlineUnderline](types/inputrichinlineunderline.md) | Underlined inline text. |
| [InputRichInlineUrl](types/inputrichinlineurl.md) | An inline text link to a URL. |
| [InputRichList](types/inputrichlist.md) | An ordered or unordered list of rich content items. |
| [InputRichMap](types/inputrichmap.md) | A map block showing coordinates within rich content. |
| [InputRichMath](types/inputrichmath.md) | A mathematical expression displayed as a block. |
| [InputRichParagraph](types/inputrichparagraph.md) | A paragraph of inline rich text. |
| [InputRichPhoto](types/inputrichphoto.md) | A photo block with optional caption text. |
| [InputRichPreformatted](types/inputrichpreformatted.md) | A code or preformatted text block, with optional syntax highlighting. |
| [InputRichPullQuote](types/inputrichpullquote.md) | A prominent quoted text block with optional credit. |
| [InputRichReference](types/inputrichreference.md) | A named reference that links in the rich message can target. |
| [InputRichSlideshow](types/inputrichslideshow.md) | A sequence of media items displayed as a slideshow. |
| [InputRichTable](types/inputrichtable.md) | A rich table containing rows and cells. |
| [InputRichText](types/inputrichtext.md) | Inline rich text represented as a string or an ordered sequence of text nodes. |
| [InputRichVideo](types/inputrichvideo.md) | A video block with optional caption text. |
| [InputRichVoice](types/inputrichvoice.md) | A voice message block with optional caption text. |
| [Rich](types/rich.md) | The rich content blocks returned in a message. |
| [RichAnchor](types/richanchor.md) | A named position that links can target within the rich message. |
| [RichAnimation](types/richanimation.md) | An animation block with optional caption text. |
| [RichAudio](types/richaudio.md) | An audio block with optional caption text. |
| [RichBlock](types/richblock.md) | One supported rich content block. |
| [RichBlockquote](types/richblockquote.md) | A block containing quoted rich content. |
| [RichButton](types/richbutton.md) | An interactive button embedded in rich content. |
| [RichButtonDateTime](types/richbuttondatetime.md) | A formatted date and time within a rich button label. |
| [RichButtonLabelPart](types/richbuttonlabelpart.md) | A text or date and time part within a rich button label. |
| [RichButtons](types/richbuttons.md) | A row of buttons embedded in rich content. |
| [RichCaption](types/richcaption.md) | The text and optional credit associated with rich media. |
| [RichCollage](types/richcollage.md) | A collage of media items within rich content. |
| [RichDetails](types/richdetails.md) | A collapsible section with a summary and nested content. |
| [RichDivider](types/richdivider.md) | A horizontal divider between rich content blocks. |
| [RichDocument](types/richdocument.md) | A document block with optional caption text. |
| [RichExpandableBlockquote](types/richexpandableblockquote.md) | A quoted text block that the user can expand. |
| [RichFooter](types/richfooter.md) | A footer text block within rich content. |
| [RichHeading](types/richheading.md) | A rich heading at the selected size. |
| [RichInline](types/richinline.md) | One supported inline text node. |
| [RichInlineAnchor](types/richinlineanchor.md) | A named anchor within inline text. |
| [RichInlineAnchorLink](types/richinlineanchorlink.md) | An inline link to a named anchor in this message. |
| [RichInlineBankCard](types/richinlinebankcard.md) | A bank card number displayed within rich text. |
| [RichInlineBold](types/richinlinebold.md) | Inline text displayed in bold. |
| [RichInlineBotCommand](types/richinlinebotcommand.md) | A bot command displayed within rich text. |
| [RichInlineButton](types/richinlinebutton.md) | An interactive button within inline rich text. |
| [RichInlineButtonCallback](types/richinlinebuttoncallback.md) | A button that sends a callback query to your bot. |
| [RichInlineButtonCopyText](types/richinlinebuttoncopytext.md) | A button that copies its configured text to the user's clipboard. |
| [RichInlineButtonDisabled](types/richinlinebuttondisabled.md) | A button displayed without an action. |
| [RichInlineButtonUrl](types/richinlinebuttonurl.md) | A button that opens a link. |
| [RichInlineCashtag](types/richinlinecashtag.md) | A cashtag displayed within rich text. |
| [RichInlineCode](types/richinlinecode.md) | Literal inline code text. |
| [RichInlineDateTime](types/richinlinedatetime.md) | A formatted date and time within inline rich text. |
| [RichInlineEmail](types/richinlineemail.md) | An email link within rich text. |
| [RichInlineHashtag](types/richinlinehashtag.md) | A hashtag displayed within rich text. |
| [RichInlineItalic](types/richinlineitalic.md) | Inline text displayed in italics. |
| [RichInlineMarked](types/richinlinemarked.md) | Highlighted inline text. |
| [RichInlineMath](types/richinlinemath.md) | An inline mathematical expression. |
| [RichInlineMention](types/richinlinemention.md) | An account handle mention within rich text. |
| [RichInlinePhone](types/richinlinephone.md) | A telephone link within rich text. |
| [RichInlineReferenceLink](types/richinlinereferencelink.md) | An inline link to a named reference in this message. |
| [RichInlineSpoiler](types/richinlinespoiler.md) | Inline text concealed until the user reveals it. |
| [RichInlineStrikethrough](types/richinlinestrikethrough.md) | Inline text displayed with a strikethrough. |
| [RichInlineSubscript](types/richinlinesubscript.md) | Inline text displayed as a subscript. |
| [RichInlineSuperscript](types/richinlinesuperscript.md) | Inline text displayed as a superscript. |
| [RichInlineTextMention](types/richinlinetextmention.md) | An inline mention linked to a user ID. |
| [RichInlineUnderline](types/richinlineunderline.md) | Underlined inline text. |
| [RichInlineUrl](types/richinlineurl.md) | An inline text link to a URL. |
| [RichList](types/richlist.md) | An ordered or unordered list of rich content items. |
| [RichMap](types/richmap.md) | A map block showing coordinates within rich content. |
| [RichMath](types/richmath.md) | A mathematical expression displayed as a block. |
| [RichParagraph](types/richparagraph.md) | A paragraph of inline rich text. |
| [RichPhoto](types/richphoto.md) | A photo block with optional caption text. |
| [RichPreformatted](types/richpreformatted.md) | A code or preformatted text block, with optional syntax highlighting. |
| [RichPullQuote](types/richpullquote.md) | A prominent quoted text block with optional credit. |
| [RichReference](types/richreference.md) | A named reference that links in the rich message can target. |
| [RichSlideshow](types/richslideshow.md) | A sequence of media items displayed as a slideshow. |
| [RichTable](types/richtable.md) | A rich table containing rows and cells. |
| [RichText](types/richtext.md) | Inline rich text represented as a string or an ordered sequence of text nodes. |
| [RichVideo](types/richvideo.md) | A video block with optional caption text. |
| [RichVoice](types/richvoice.md) | A voice message block with optional caption text. |

## Text, entities, and previews <a href="#text-entities-and-previews" id="text-entities-and-previews"></a>

| Type | Description |
| --- | --- |
| [ChecklistEntity](types/checklistentity.md) | A supported formatting entity within checklist text. |
| [Entity](types/entity.md) | A formatting entity in received text. |
| [EntityBlockquote](types/entityblockquote.md) | A UTF-16 text span representing quoted text. |
| [EntityBold](types/entitybold.md) | A UTF-16 text span representing bold text. |
| [EntityBotCommand](types/entitybotcommand.md) | A UTF-16 text span representing a bot command. |
| [EntityCashtag](types/entitycashtag.md) | A UTF-16 text span representing a cashtag. |
| [EntityCode](types/entitycode.md) | A UTF-16 text span representing inline code. |
| [EntityDateTime](types/entitydatetime.md) | A UTF-16 text span representing a formatted date and time. |
| [EntityEmail](types/entityemail.md) | A UTF-16 text span representing an email address. |
| [EntityExpandableBlockquote](types/entityexpandableblockquote.md) | A UTF-16 text span representing an expandable quotation. |
| [EntityHashtag](types/entityhashtag.md) | A UTF-16 text span representing a hashtag. |
| [EntityItalic](types/entityitalic.md) | A UTF-16 text span representing italic text. |
| [EntityMention](types/entitymention.md) | A UTF-16 text span representing an account handle mention. |
| [EntityPhoneNumber](types/entityphonenumber.md) | A UTF-16 text span representing a telephone number. |
| [EntityPre](types/entitypre.md) | A UTF-16 text span representing preformatted code. |
| [EntitySpoiler](types/entityspoiler.md) | A UTF-16 text span representing concealed text. |
| [EntityStrikethrough](types/entitystrikethrough.md) | A UTF-16 text span representing strikethrough text. |
| [EntityTextLink](types/entitytextlink.md) | A UTF-16 text span representing a text link. |
| [EntityTextMention](types/entitytextmention.md) | A UTF-16 text span representing a user mention. |
| [EntityUnderline](types/entityunderline.md) | A UTF-16 text span representing underlined text. |
| [EntityUrl](types/entityurl.md) | A UTF-16 text span representing a URL. |
| [InputChecklistEntity](types/inputchecklistentity.md) | A supported formatting entity for checklist input. |
| [InputEntity](types/inputentity.md) | An explicit formatting span in text input. |
| [InputEntityBlockquote](types/inputentityblockquote.md) | A UTF-16 text span representing quoted text. |
| [InputEntityBold](types/inputentitybold.md) | A UTF-16 text span representing bold text. |
| [InputEntityBotCommand](types/inputentitybotcommand.md) | A UTF-16 text span representing a bot command. |
| [InputEntityCashtag](types/inputentitycashtag.md) | A UTF-16 text span representing a cashtag. |
| [InputEntityCode](types/inputentitycode.md) | A UTF-16 text span representing inline code. |
| [InputEntityDateTime](types/inputentitydatetime.md) | A UTF-16 text span representing a formatted date and time. |
| [InputEntityEmail](types/inputentityemail.md) | A UTF-16 text span representing an email address. |
| [InputEntityExpandableBlockquote](types/inputentityexpandableblockquote.md) | A UTF-16 text span representing an expandable quotation. |
| [InputEntityHashtag](types/inputentityhashtag.md) | A UTF-16 text span representing a hashtag. |
| [InputEntityItalic](types/inputentityitalic.md) | A UTF-16 text span representing italic text. |
| [InputEntityMention](types/inputentitymention.md) | A UTF-16 text span representing an account handle mention. |
| [InputEntityPhoneNumber](types/inputentityphonenumber.md) | A UTF-16 text span representing a telephone number. |
| [InputEntityPre](types/inputentitypre.md) | A UTF-16 text span representing preformatted code. |
| [InputEntitySpoiler](types/inputentityspoiler.md) | A UTF-16 text span representing concealed text. |
| [InputEntityStrikethrough](types/inputentitystrikethrough.md) | A UTF-16 text span representing strikethrough text. |
| [InputEntityTextLink](types/inputentitytextlink.md) | A UTF-16 text span representing a text link. |
| [InputEntityTextMention](types/inputentitytextmention.md) | A UTF-16 text span representing a user mention. |
| [InputEntityUnderline](types/inputentityunderline.md) | A UTF-16 text span representing underlined text. |
| [InputEntityUrl](types/inputentityurl.md) | A UTF-16 text span representing a URL. |
| [InputLinkPreview](types/inputlinkpreview.md) | The link preview options to apply to a text message. |
| [InputQuote](types/inputquote.md) | The text excerpt and position to quote from the message being replied to. |
| [InputText](types/inputtext.md) | The text message body and either a markup format or explicit formatting entities. |
| [LinkPreview](types/linkpreview.md) | The link preview metadata and display settings returned with a message. |
| [Quote](types/quote.md) | The quoted excerpt and its position in the original message. |
| [SendText](types/sendtext.md) | The text and delivery settings for a new text message. |
| [Text](types/text.md) | The message body after markup has been parsed, with its formatting entities. |

## Updates and service events <a href="#updates-and-service-events" id="updates-and-service-events"></a>

| Type | Description |
| --- | --- |
| [BotOwnerChangedService](types/botownerchangedservice.md) | A service event reporting that the bot owner changed. |
| [ChecklistTasksAddedService](types/checklisttasksaddedservice.md) | A service event identifying tasks added to a checklist. |
| [ChecklistTasksDoneService](types/checklisttasksdoneservice.md) | A service event identifying checklist tasks marked complete or incomplete. |
| [MemberUpdated](types/memberupdated.md) | A change to a user's membership status in a chat. |
| [MessagePinnedService](types/messagepinnedservice.md) | A service event identifying a message that was pinned. |
| [MessagesDeleted](types/messagesdeleted.md) | The messages reported as deleted from one chat. |
| [PollOptionAddedService](types/polloptionaddedservice.md) | A service event identifying an option added to a poll. |
| [PollOptionDeletedService](types/polloptiondeletedservice.md) | A service event identifying an option removed from a poll. |
| [ProximityAlertService](types/proximityalertservice.md) | A service event reporting that a live location entered another user's proximity radius. |
| [QueuedUpdatesDropped](types/queuedupdatesdropped.md) | A notice identifying updates lost from the delivery queue. |
| [ReactionUpdate](types/reactionupdate.md) | The previous and current reactions selected by a user on a message. |
| [Service](types/service.md) | A service event carried by a message. |
| [SourceUpdatesDropped](types/sourceupdatesdropped.md) | A notice identifying a gap in the source event sequence for a chat. |
| [StoppedMessageGeneration](types/stoppedmessagegeneration.md) | The draft generation stopped by the user, identified by chat and draft ID. |
| [Update](types/update.md) | An event delivered to your bot. |
| [UpdateBatch](types/updatebatch.md) | The ordered updates returned by a polling request. |
| [UpdateCallbackQuery](types/updatecallbackquery.md) | An update carrying an inline button press. |
| [UpdateEditedMessage](types/updateeditedmessage.md) | An update carrying an edited message. |
| [UpdateMessage](types/updatemessage.md) | An update carrying a new message. |
| [UpdateMessageReaction](types/updatemessagereaction.md) | An update carrying a change to a message reaction. |
| [UpdateMessagesDeleted](types/updatemessagesdeleted.md) | An update carrying deleted messages. |
| [UpdateMyChatMember](types/updatemychatmember.md) | An update carrying a change to your bot's chat membership. |
| [UpdatePoll](types/updatepoll.md) | An update carrying a changed poll. |
| [UpdatePollAnswer](types/updatepollanswer.md) | An update carrying a user's poll answer. |
| [UpdateStoppedMessageGeneration](types/updatestoppedmessagegeneration.md) | An update carrying a user stopping draft generation. |
| [UpdateUpdatesDropped](types/updateupdatesdropped.md) | An update carrying lost updates. |
| [UpdatesDropped](types/updatesdropped.md) | A notice that some updates were lost. |
| [UsersSharedService](types/userssharedservice.md) | A service event containing users shared through a request_users button. |

Return to the [API index](../api.md) to find the operation that uses a type.

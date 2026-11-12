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
| [GroupAdminRights](types/groupadminrights.md) | Granular group administrator rights. |
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
| [ChatMetadata](types/chatmetadata.md) | Current chat details visible to your bot, for a private chat, group or public channel. |
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
| [GroupCallbackQuery](types/groupcallbackquery.md) | A callback from a current group member, resolved from the canonical message/button. |
| [GroupUpdateCallbackQuery](types/groupupdatecallbackquery.md) | GroupUpdateCallbackQuery is a group contract model. |
| [InlineButton](types/inlinebutton.md) | An inline keyboard button. |
| [InlineButtonCallback](types/inlinebuttoncallback.md) | A button that sends a callback query to your bot. |
| [InlineButtonCopyText](types/inlinebuttoncopytext.md) | A button that copies its configured text to the user's clipboard. |
| [InlineButtonDisabled](types/inlinebuttondisabled.md) | A button displayed without an action. |
| [InlineButtonMiniApp](types/inlinebuttonminiapp.md) | An inline Mini App button returned by the Bot API. |
| [InlineButtonUrl](types/inlinebuttonurl.md) | A button that opens a link. |
| [InlineKeyboard](types/inlinekeyboard.md) | The inline keyboard attached to a received message, with buttons arranged in rows. |
| [InputForceReply](types/inputforcereply.md) | A request to open the reply input for the user. |
| [InputInlineButton](types/inputinlinebutton.md) | An inline button to attach to a message. |
| [InputInlineButtonCallback](types/inputinlinebuttoncallback.md) | A button that sends a callback query to your bot. |
| [InputInlineButtonCopyText](types/inputinlinebuttoncopytext.md) | A button that copies its configured text to the user's clipboard. |
| [InputInlineButtonDisabled](types/inputinlinebuttondisabled.md) | A button displayed without an action. |
| [InputInlineButtonMiniApp](types/inputinlinebuttonminiapp.md) | An inline button that opens a Mini App on a registered origin when Mini Apps are enabled. |
| [InputInlineButtonUrl](types/inputinlinebuttonurl.md) | A button that opens a link. |
| [InputInlineKeyboard](types/inputinlinekeyboard.md) | An inline keyboard to attach to a message. |
| [InputRemoveKeyboard](types/inputremovekeyboard.md) | A request to remove the user's custom reply keyboard. |
| [InputReplyButton](types/inputreplybutton.md) | A reply keyboard button that sends text or requests information from the user. |
| [InputReplyButtonMiniApp](types/inputreplybuttonminiapp.md) | A reply-keyboard button that opens a Mini App in a private chat with your bot. |
| [InputReplyButtonRequestContact](types/inputreplybuttonrequestcontact.md) | A reply button that asks the user to share their contact details with your bot. |
| [InputReplyButtonRequestLocation](types/inputreplybuttonrequestlocation.md) | A reply button that asks the user to share their location. |
| [InputReplyButtonRequestManagedBot](types/inputreplybuttonrequestmanagedbot.md) | A private manager reply button that opens the human creation sheet. |
| [InputReplyButtonRequestUsers](types/inputreplybuttonrequestusers.md) | A reply button that asks the user to select accounts to share with your bot. |
| [InputReplyButtonText](types/inputreplybuttontext.md) | A reply button that sends its label as a text message. |
| [InputReplyKeyboard](types/inputreplykeyboard.md) | A custom reply keyboard arranged in rows, with optional display settings. |
| [MenuButton](types/menubutton.md) | The menu button configuration for your bot or one private chat. |
| [PreparedKeyboardButton](types/preparedkeyboardbutton.md) | A reusable prepared-button identifier and its absolute expiry, 24 hours after saving. |
| [PreparedManagedButton](types/preparedmanagedbutton.md) | A saved request for native managed-bot creation. |
| [PreparedUsersButton](types/preparedusersbutton.md) | A saved native user-selection request. |
| [ReplyMarkup](types/replymarkup.md) | A keyboard or reply input instruction to attach to a message. |
| [SavePreparedKeyboardButton](types/savepreparedkeyboardbutton.md) | A button bound to a known human account and this bot. |
| [SavedInlineAppButton](types/savedinlineappbutton.md) | A Mini App header supplied by the authenticated bot. |

## Locations and contacts <a href="#locations-and-contacts" id="locations-and-contacts"></a>

| Type | Description |
| --- | --- |
| [Contact](types/contact.md) | An account contact card or a manual contact card returned in a message. |
| [ContactContent](types/contactcontent.md) | The contact card content returned within a message. |
| [EditLiveLocation](types/editlivelocation.md) | Changes to an active live location authored by your bot. |
| [InlineResultContact](types/inlineresultcontact.md) | An inline contact result with an ID chosen by your bot. |
| [InlineResultLocation](types/inlineresultlocation.md) | An inline location result with an ID chosen by your bot. |
| [InlineResultVenue](types/inlineresultvenue.md) | An inline venue result with an ID chosen by your bot. |
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
| [AnswerGuestQuery](types/answerguestquery.md) | The single inline result to answer a query issued to your bot. |
| [AnswerJoinRequestQuery](types/answerjoinrequestquery.md) | The decision for the assigned join request: approve, decline or keep the request pending. |
| [AnswerMiniAppQuery](types/answerminiappquery.md) | One result to send in response to the Mini App query. |
| [AnswerSavedInlineQuery](types/answersavedinlinequery.md) | One personal uncached answer within the saved query's original deadline. |
| [Audio](types/audio.md) | A received message containing an audio file and its metadata. |
| [BotAccessSettings](types/botaccesssettings.md) | The current access settings of a bot you manage. |
| [BotSubscriptionUpdated](types/botsubscriptionupdated.md) | A subscription state change. |
| [BusinessAccountBioAccepted](types/businessaccountbioaccepted.md) | True after the connected account biography change is accepted, including a change to the existing biography. |
| [BusinessAccountPhotoAccepted](types/businessaccountphotoaccepted.md) | True after the connected account photo change is accepted, including removal of the current photo. |
| [BusinessAccountUsernameAccepted](types/businessaccountusernameaccepted.md) | True after the connected account username change is accepted, including a change to the existing username. |
| [BusinessConnection](types/businessconnection.md) | The connected human account, its permissions, and its connection state. |
| [BusinessDeviceBundles](types/businessdevicebundles.md) | Authorized human device bundles. |
| [BusinessEndpoint](types/businessendpoint.md) | Public Business endpoint data for BusinessEndpoint. |
| [BusinessEndpointCertificate](types/businessendpointcertificate.md) | Public Business endpoint data for BusinessEndpointCertificate. |
| [BusinessEndpointPrekey](types/businessendpointprekey.md) | Public Business endpoint data for BusinessEndpointPrekey. |
| [BusinessEndpointReportReadback](types/businessendpointreportreadback.md) | The endpoint report currently visible for a business connection. |
| [BusinessEndpointReportRecorded](types/businessendpointreportrecorded.md) | Confirmation that the endpoint report was recorded, with its new revision. |
| [BusinessEndpointSignedPrekey](types/businessendpointsignedprekey.md) | Public Business endpoint data for BusinessEndpointSignedPrekey. |
| [BusinessEnvelope](types/businessenvelope.md) | An encrypted Business event. |
| [BusinessEnvelopeAcceptance](types/businessenvelopeacceptance.md) | Confirmation that the complete operation was recorded for delivery. |
| [ChannelChat](types/channelchat.md) | The identity of a public channel. |
| [ChannelChatFullInfo](types/channelchatfullinfo.md) | Current public channel FullInfo for a bot member or administrator. |
| [ChannelMember](types/channelmember.md) | A channel membership with its status and administrator rights. |
| [ChannelMemberUpdated](types/channelmemberupdated.md) | A change to your bot's membership in a channel. |
| [Content](types/content.md) | The content of a received message. |
| [ContentMessage](types/contentmessage.md) | A received message containing text, media, or another supported content variant. |
| [CopyMessages](types/copymessages.md) | The source message IDs and optional replacements for a copy request. |
| [CreateInvoiceLink](types/createinvoicelink.md) | Create a payment link using a configured asset and an exact base-unit amount. |
| [DeleteBatch](types/deletebatch.md) | The IDs deleted by a batch request and the IDs that were skipped. |
| [DeleteMessages](types/deletemessages.md) | The message IDs to remove from one chat. |
| [DeletedMessage](types/deletedmessage.md) | The identity of a message reported as deleted. |
| [Dice](types/dice.md) | An animated dice message with its emoji and generated result. |
| [DiceValue](types/dicevalue.md) | The allowed dice emoji and result range reported by the platform. |
| [Document](types/document.md) | A received message containing a document and its metadata. |
| [E2eGuestMessage](types/e2eguestmessage.md) | An E2E Guest invocation. |
| [EditMessage](types/editmessage.md) | The content, caption, live location, or inline keyboard changes to apply to a message. |
| [Entitlement](types/entitlement.md) | The platform's current access decision for a buyer. |
| [File](types/file.md) | A file upload result or metadata returned by files.get. |
| [ForumTopic](types/forumtopic.md) | Metadata and current capabilities of a group forum topic. |
| [ForumTopicCapabilities](types/forumtopiccapabilities.md) | Operations currently available to your bot for this forum topic. |
| [ForwardMessages](types/forwardmessages.md) | The source message IDs and delivery options for a forward request. |
| [ForwardOrigin](types/forwardorigin.md) | The original sender and source message attribution retained by a forwarded message. |
| [GroupAdministrators](types/groupadministrators.md) | GroupAdministrators is a group contract model. |
| [GroupBanMember](types/groupbanmember.md) | Arguments for members.ban in Super Groups with group moderation enabled. |
| [GroupChat](types/groupchat.md) | A server-managed Super Group visible to a bot. |
| [GroupChatFullInfo](types/groupchatfullinfo.md) | Group metadata available to a member bot. invite_link is the bot's own primary link. has_visible_history and has_aggressive_anti_spam_enabled are administrator-only. |
| [GroupContentMessage](types/groupcontentmessage.md) | GroupContentMessage is a group contract model. |
| [GroupCreateInviteLink](types/groupcreateinvitelink.md) | Arguments for invite_links.create in Super Groups with group moderation enabled. |
| [GroupEditInviteLink](types/groupeditinvitelink.md) | Arguments for invite_links.edit in Super Groups with group moderation enabled. |
| [GroupExternalOriginChannel](types/groupexternaloriginchannel.md) | GroupExternalOriginChannel is a group contract model. |
| [GroupExternalOriginChat](types/groupexternaloriginchat.md) | GroupExternalOriginChat is a group contract model. |
| [GroupExternalOriginHidden](types/groupexternaloriginhidden.md) | The original time without a private source or author identifier. |
| [GroupExternalOriginUser](types/groupexternaloriginuser.md) | GroupExternalOriginUser is a group contract model. |
| [GroupExternalOriginVisible](types/groupexternaloriginvisible.md) | GroupExternalOriginVisible is a group contract model. |
| [GroupExternalReplyHidden](types/groupexternalreplyhidden.md) | A private or otherwise hidden source summary. |
| [GroupExternalReplyInfo](types/groupexternalreplyinfo.md) | An external reply summary. |
| [GroupExternalReplyVisible](types/groupexternalreplyvisible.md) | An admitted public group or channel source, with both canonical source IDs. |
| [GroupForumClose](types/groupforumclose.md) | Close an existing group forum topic. |
| [GroupForumCreate](types/groupforumcreate.md) | Create a named topic in a group with forums enabled. |
| [GroupForumDelete](types/groupforumdelete.md) | Delete a group forum topic with DeleteMessages and a recoverable Idempotency-Key. |
| [GroupForumDeletion](types/groupforumdeletion.md) | Progress of an accepted group topic purge. |
| [GroupForumEdit](types/groupforumedit.md) | Change a group forum topic's name or icon. |
| [GroupForumReopen](types/groupforumreopen.md) | Reopen an existing group forum topic. |
| [GroupGetMessages](types/groupgetmessages.md) | The message IDs to read, for example the message_id of a reply reference. |
| [GroupInaccessibleMessage](types/groupinaccessiblemessage.md) | GroupInaccessibleMessage is a group contract model. |
| [GroupInviteLink](types/groupinvitelink.md) | A bot's own invite link. invite_code is the hash segment used by Bot API paths; the human API's internal link_id is not exposed. |
| [GroupInviteUrl](types/groupinviteurl.md) | GroupInviteUrl is a group contract model. |
| [GroupJoinRequest](types/groupjoinrequest.md) | GroupJoinRequest is a group contract model. |
| [GroupListAdministrators](types/grouplistadministrators.md) | Arguments for members.list_admins in Super Groups with group moderation enabled. |
| [GroupMember](types/groupmember.md) | An authoritative membership variant. |
| [GroupMemberAdministrator](types/groupmemberadministrator.md) | GroupMemberAdministrator is a group contract model. |
| [GroupMemberBanned](types/groupmemberbanned.md) | GroupMemberBanned is a group contract model. |
| [GroupMemberCount](types/groupmembercount.md) | GroupMemberCount is a group contract model. |
| [GroupMemberLeft](types/groupmemberleft.md) | GroupMemberLeft is a group contract model. |
| [GroupMemberMember](types/groupmembermember.md) | GroupMemberMember is a group contract model. |
| [GroupMemberOwner](types/groupmemberowner.md) | GroupMemberOwner is a group contract model. |
| [GroupMemberRestricted](types/groupmemberrestricted.md) | GroupMemberRestricted is a group contract model. |
| [GroupMemberUpdated](types/groupmemberupdated.md) | A membership transition. user is the actor, while old.user and new.user identify the subject. |
| [GroupMessage](types/groupmessage.md) | A group message already routed to this bot. |
| [GroupMessageHistory](types/groupmessagehistory.md) | One page of group history visible to your bot. |
| [GroupMessageReaction](types/groupmessagereaction.md) | GroupMessageReaction is a group contract model. |
| [GroupMessageReactionCount](types/groupmessagereactioncount.md) | Anonymous reaction totals, at most once per message per minute and with at most two minutes delay. |
| [GroupMessages](types/groupmessages.md) | The requested messages your bot can currently read, in ascending message_id order. |
| [GroupMessagesDeleted](types/groupmessagesdeleted.md) | GroupMessagesDeleted is a group contract model. |
| [GroupPermissions](types/grouppermissions.md) | Group defaults or member restrictions. |
| [GroupPromoteMember](types/grouppromotemember.md) | Arguments for members.promote in Super Groups with group moderation enabled. |
| [GroupQuotedContentMessage](types/groupquotedcontentmessage.md) | GroupQuotedContentMessage is a group contract model. |
| [GroupQuotedMessage](types/groupquotedmessage.md) | GroupQuotedMessage is a group contract model. |
| [GroupQuotedServiceMessage](types/groupquotedservicemessage.md) | GroupQuotedServiceMessage is a group contract model. |
| [GroupReply](types/groupreply.md) | One level of an admitted local quote, a content-free message_id reference for an edit, or an admitted external source summary. |
| [GroupRequestChat](types/grouprequestchat.md) | The request_chat action fields. |
| [GroupRestrictMember](types/grouprestrictmember.md) | Arguments for members.restrict in Super Groups with group moderation enabled. |
| [GroupSenderChat](types/groupsenderchat.md) | The real group or channel identity of an anonymous sender or automatic forward. |
| [GroupServiceMessage](types/groupservicemessage.md) | GroupServiceMessage is a group contract model. |
| [GroupSetChatPhoto](types/groupsetchatphoto.md) | Arguments for chats.set_photo in Super Groups with group moderation enabled. |
| [GroupSetCustomTitle](types/groupsetcustomtitle.md) | Arguments for members.set_custom_title in Super Groups with group moderation enabled. |
| [GroupSetMemberTag](types/groupsetmembertag.md) | Arguments reserved for member tags; no Bot API route is registered for this model. |
| [GroupSetPermissions](types/groupsetpermissions.md) | Arguments for chats.set_permissions in Super Groups with group moderation enabled. |
| [GroupUnbanMember](types/groupunbanmember.md) | Arguments for members.unban in Super Groups with group moderation enabled. |
| [GroupUpdate](types/groupupdate.md) | Each group update contains exactly one payload matching type. |
| [GroupUpdateChat](types/groupupdatechat.md) | Arguments for chats.update in Super Groups with group moderation enabled. |
| [GroupUpdateChatJoinRequest](types/groupupdatechatjoinrequest.md) | GroupUpdateChatJoinRequest is a group contract model. |
| [GroupUpdateChatMember](types/groupupdatechatmember.md) | GroupUpdateChatMember is a group contract model. |
| [GroupUpdateEditedMessage](types/groupupdateeditedmessage.md) | GroupUpdateEditedMessage is a group contract model. |
| [GroupUpdateMessage](types/groupupdatemessage.md) | GroupUpdateMessage is a group contract model. |
| [GroupUpdateMessageReaction](types/groupupdatemessagereaction.md) | GroupUpdateMessageReaction is a group contract model. |
| [GroupUpdateMessageReactionCount](types/groupupdatemessagereactioncount.md) | GroupUpdateMessageReactionCount is a group contract model. |
| [GroupUpdateMessagesDeleted](types/groupupdatemessagesdeleted.md) | GroupUpdateMessagesDeleted is a group contract model. |
| [GroupUpdateMyChatMember](types/groupupdatemychatmember.md) | GroupUpdateMyChatMember is a group contract model. |
| [GuardQueryAcknowledged](types/guardqueryacknowledged.md) | True when the join request decision or registered Mini App URL was accepted. |
| [GuestDisclosedContent](types/guestdisclosedcontent.md) | Caller-disclosed text and entities. |
| [GuestDisclosedQuote](types/guestdisclosedquote.md) | An optional client-asserted quote. |
| [GuestMessage](types/guestmessage.md) | An accepted Nexo group original or a client-asserted E2E disclosure. |
| [InaccessibleMessage](types/inaccessiblemessage.md) | The identity of a message whose content is unavailable to your bot. |
| [IncomingMemberUpdated](types/incomingmemberupdated.md) | A membership transition in a private chat, group or channel. |
| [IncomingMessage](types/incomingmessage.md) | A private payload or a group payload selected by chat.kind. |
| [IncomingMessagesDeleted](types/incomingmessagesdeleted.md) | A private payload or a group payload selected by chat.kind. |
| [InlineCachedFile](types/inlinecachedfile.md) | An existing file available to your bot. |
| [InlineInputFile](types/inlineinputfile.md) | An existing bot file or an HTTPS source to import when selected. |
| [InlineMessageContent](types/inlinemessagecontent.md) | Content sent as the human who selected the result. |
| [InlineQuery](types/inlinequery.md) | An inline query with its original 30-second answer deadline. |
| [InlineResult](types/inlineresult.md) | One result supplied by your bot. |
| [InlineResultAnimation](types/inlineresultanimation.md) | An inline animation result with an ID chosen by your bot. |
| [InlineResultArticle](types/inlineresultarticle.md) | An inline article result with an ID chosen by your bot. |
| [InlineResultAudio](types/inlineresultaudio.md) | An inline audio result with an ID chosen by your bot. |
| [InlineResultDocument](types/inlineresultdocument.md) | An inline document result with an ID chosen by your bot. |
| [InlineResultGame](types/inlineresultgame.md) | An inline game result with an ID chosen by your bot. |
| [InlineResultPhoto](types/inlineresultphoto.md) | An inline photo result with an ID chosen by your bot. |
| [InlineResultSticker](types/inlineresultsticker.md) | An inline sticker result with an ID chosen by your bot. |
| [InlineResultVideo](types/inlineresultvideo.md) | An inline video result with an ID chosen by your bot. |
| [InlineResultVoice](types/inlineresultvoice.md) | An inline voice result with an ID chosen by your bot. |
| [InlineText](types/inlinetext.md) | Text content with optional formatting, entities, and link preview choices. |
| [InputAnimation](types/inputanimation.md) | The file source and message settings for sending an animation. |
| [InputAudio](types/inputaudio.md) | The file source and message settings for sending an audio file. |
| [InputContent](types/inputcontent.md) | The message content to send. |
| [InputDice](types/inputdice.md) | The dice emoji to send. |
| [InputDocument](types/inputdocument.md) | The file source and message settings for sending a document. |
| [InputDraftFile](types/inputdraftfile.md) | An existing file available to your bot for use in a draft. |
| [InputEditContent](types/inputeditcontent.md) | Replacement message content accepted by messages.edit. |
| [InputFile](types/inputfile.md) | One file source for message media: an existing file, a named upload part, or a public URL. |
| [InputInvoice](types/inputinvoice.md) | A private-chat invoice request using a configured asset and an exact base-unit amount. |
| [InputLivePhoto](types/inputlivephoto.md) | The photo and short video sources to send together as one live photo. |
| [InputPhoto](types/inputphoto.md) | The file source and message settings for sending a photo. |
| [InputReply](types/inputreply.md) | The message to reply to, with an optional quote and a choice of how to handle a missing target. |
| [InputStickerContent](types/inputstickercontent.md) | Send a sticker through messages.send. |
| [InputThumbnail](types/inputthumbnail.md) | A separate JPEG upload used as a custom media thumbnail. |
| [InputVideo](types/inputvideo.md) | The file source and message settings for sending a video. |
| [InputVideoNote](types/inputvideonote.md) | The file source and message settings for sending a round video note. |
| [InputVoice](types/inputvoice.md) | The file source and message settings for sending a voice message. |
| [InvoiceContent](types/invoicecontent.md) | The merchant invoice displayed in a private bot chat. |
| [InvoiceLink](types/invoicelink.md) | The original reusable invoice link returned after acceptance. |
| [LiveInputFile](types/liveinputfile.md) | An existing file or named binary part for a live photo. |
| [LivePhoto](types/livephoto.md) | A photo and its associated short video returned as one live photo message. |
| [ManagedBot](types/managedbot.md) | A Managed bot and the human owner represented by this update. |
| [ManagedBotToken](types/managedbottoken.md) | The current secret token of a bot managed by the authenticated caller. |
| [MaybeInaccessibleMessage](types/maybeinaccessiblemessage.md) | A message returned with its content when accessible, or only its identity when unavailable. |
| [MediaFile](types/mediafile.md) | The file identifier and measured metadata for media embedded in a message. |
| [MembershipUpdate](types/membershipupdate.md) | A change to your bot's membership in a private chat or a channel. |
| [MerchantAssetBalance](types/merchantassetbalance.md) | An on-chain balance in one asset held at the merchant's payout wallet. |
| [MerchantBalance](types/merchantbalance.md) | Current holdings at the merchant's payout wallet, observed on chain within the last 30 seconds. |
| [Message](types/message.md) | A content message or a service message available to your bot. |
| [MessageBatch](types/messagebatch.md) | The messages created by a copy or forward request and any source IDs that were skipped. |
| [MessageEffect](types/messageeffect.md) | One visual message effect available for the listed chat kinds. |
| [MessageEffects](types/messageeffects.md) | The catalog of message effects and its revision. |
| [MessageReactions](types/messagereactions.md) | The current reaction counts and selected reaction for a message. |
| [NexoGuestMessage](types/nexoguestmessage.md) | The canonical accepted Nexo group Message and at most its authorized reply. guest_caller_user or guest_caller_chat repeats its public author; anonymous authors never expose the internal caller. |
| [OpenJoinRequestMiniApp](types/openjoinrequestminiapp.md) | The registered HTTPS Mini App URL for the pending join request. |
| [PaymentAnalytics](types/paymentanalytics.md) | Daily payment aggregates for the requested bot and date range. |
| [PaymentAnalyticsBucket](types/paymentanalyticsbucket.md) | One day of payment and manual-subscription aggregates. |
| [PaymentAnalyticsRevenue](types/paymentanalyticsrevenue.md) | Exact payment revenue in one configured asset. |
| [PaymentOneTimePlan](types/paymentonetimeplan.md) | A payment plan purchased once. |
| [PaymentPayoutReadiness](types/paymentpayoutreadiness.md) | The current payout wallet's setup status on one network. |
| [PaymentPlan](types/paymentplan.md) | A one-time or manually renewed recurring payment plan. |
| [PaymentPlanPage](types/paymentplanpage.md) | One page of this bot's payment plans. |
| [PaymentReadiness](types/paymentreadiness.md) | Current payment setup checks for this bot. |
| [PaymentRecurringPlan](types/paymentrecurringplan.md) | A recurring plan that requires a new wallet approval for each renewal. |
| [PaymentResult](types/paymentresult.md) | The merchant-scoped result of a payment operation. |
| [PaymentSupportedToken](types/paymentsupportedtoken.md) | A configured asset and its exact payment limits. |
| [PaymentTokenAmount](types/paymenttokenamount.md) | An exact transfer quantity encoded in base units with its configured asset and decimals. |
| [PaymentTokenCatalog](types/paymenttokencatalog.md) | The configured assets available for bot payments. |
| [PaymentUpdateReadiness](types/paymentupdatereadiness.md) | Payment update types enabled for the bot's current delivery configuration. |
| [PersonalChannelChat](types/personalchannelchat.md) | Metadata for a user's currently visible personal public channel. |
| [PersonalChannelPosts](types/personalchannelposts.md) | A bounded page of visible personal public channel posts with current channel metadata. |
| [Photo](types/photo.md) | The available sizes of a photo. |
| [PhotoContent](types/photocontent.md) | The photo content returned within a message. |
| [PinMessage](types/pinmessage.md) | The notification settings for pinning a message. |
| [Pins](types/pins.md) | The pinned message IDs returned for a chat. |
| [PreCheckoutAnswer](types/precheckoutanswer.md) | The approval or buyer-visible rejection of one pre-checkout query. |
| [PreCheckoutQuery](types/precheckoutquery.md) | The merchant approval query sent before a transfer, with its original ten-second deadline. |
| [PreparedMessageReceipt](types/preparedmessagereceipt.md) | The prepared message identifier and its fixed expiry time. |
| [PrivateMemberUpdated](types/privatememberupdated.md) | A private membership transition. |
| [PrivateTopic](types/privatetopic.md) | Private topic metadata. |
| [PrivateTopicBotCreate](types/privatetopicbotcreate.md) | Create a private topic with a required name and optional icon. |
| [PrivateTopicCreate](types/privatetopiccreate.md) | Create a private topic with a required name and optional icon. |
| [PrivateTopicDelete](types/privatetopicdelete.md) | Retire the topic using its current metadata version. |
| [PrivateTopicEdit](types/privatetopicedit.md) | Change the topic name or icon using its current metadata version. |
| [PrivateTopicHumanCreate](types/privatetopichumancreate.md) | Create a private topic. |
| [PrivateTopicIcons](types/privatetopicicons.md) | Versioned icon catalogue with supported colors and SVG vectors. |
| [PrivateTopicPage](types/privatetopicpage.md) | Active private topics in ascending creation message ID order. |
| [PrivateTopicRetired](types/privatetopicretired.md) | Accepted topic retirement. |
| [PrivateTopicTopic](types/privatetopictopic.md) | Private topic metadata. |
| [PublicChatResolution](types/publicchatresolution.md) | The canonical chat ID and kind of an active public group or channel. |
| [Reaction](types/reaction.md) | An emoji or custom emoji reaction selected by your bot or a user. |
| [ReactionState](types/reactionstate.md) | The current reactions and state version returned after setting a message reaction. |
| [RefundCharge](types/refundcharge.md) | A refund instruction for a confirmed charge. |
| [RefundRequest](types/refundrequest.md) | The original refund acceptance receipt, returned with HTTP 202. |
| [RefundRequestUpdated](types/refundrequestupdated.md) | A refund request state. |
| [RegisterBusinessEndpoint](types/registerbusinessendpoint.md) | Public Business endpoint data for RegisterBusinessEndpoint. |
| [RenewalDue](types/renewaldue.md) | An ordered reminder emitted before the paid period ends. |
| [Reply](types/reply.md) | The reply target returned with a message, including any quoted excerpt. |
| [SavePreparedMessage](types/savepreparedmessage.md) | A reusable result for one known human recipient and the allowed destination types. |
| [SendAction](types/sendaction.md) | The temporary activity your bot displays in a chat. |
| [SendAlbum](types/sendalbum.md) | The media items and delivery settings for a new album. |
| [SendBusinessEnvelopes](types/sendbusinessenvelopes.md) | One encrypted Business operation with its complete set of device copies. |
| [SendMessage](types/sendmessage.md) | The content and delivery settings for a new message. |
| [SentGuestMessage](types/sentguestmessage.md) | The inline message reference returned after accepting one Guest answer. |
| [SentMessage](types/sentmessage.md) | A private or channel message, or an accepted group content message, selected by chat.kind. |
| [SentMiniAppMessage](types/sentminiappmessage.md) | The accepted delivery mode and optional reference for an interactive message. |
| [ServiceMessage](types/servicemessage.md) | A received message that reports a service event rather than ordinary content. |
| [SetBusinessAccountBio](types/setbusinessaccountbio.md) | The connected account and its new biography. |
| [SetBusinessAccountPhoto](types/setbusinessaccountphoto.md) | The new photo for the connected account. |
| [SetBusinessAccountUsername](types/setbusinessaccountusername.md) | The connected account and its required new username. |
| [SetBusinessEndpointReport](types/setbusinessendpointreport.md) | The endpoint version and capabilities to record for a business connection, conditional on the previous report revision. |
| [SetDraft](types/setdraft.md) | The text or rich content and stop controls for a temporary draft. |
| [SetManagedBotAccessSettings](types/setmanagedbotaccesssettings.md) | The desired access settings. |
| [SetReactions](types/setreactions.md) | The reaction to apply to a message. |
| [StickerContent](types/stickercontent.md) | A message containing a sticker file. |
| [StickerFile](types/stickerfile.md) | Metadata for a sticker file, including its animation and video format indicators. |
| [Subscription](types/subscription.md) | A manually renewed subscription and its confirmed payment history. |
| [Subscriptions](types/subscriptions.md) | One cursor page of subscriptions visible to this bot. |
| [SubscriptionsListQuery](types/subscriptionslistquery.md) | Optional filters applied before cursor pagination of this bot's subscriptions. |
| [TopicDeletionResult](types/topicdeletionresult.md) | Deletion progress under the contract for the selected chat kind. |
| [TopicMutationResult](types/topicmutationresult.md) | The resulting private topic or group forum topic, selected by the chat kind. |
| [TopicsCreate](types/topicscreate.md) | Create a named private or group forum topic. |
| [TopicsDelete](types/topicsdelete.md) | Delete a private topic or a group forum topic, selected by the chat kind. |
| [TopicsEdit](types/topicsedit.md) | Change a private or group forum topic. |
| [UnpinMessage](types/unpinmessage.md) | The optional message ID selecting which pinned message to remove. |
| [UploadBusinessEndpointPrekeys](types/uploadbusinessendpointprekeys.md) | Public Business endpoint data for UploadBusinessEndpointPrekeys. |
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
| [GroupUpdatePoll](types/groupupdatepoll.md) | GroupUpdatePoll is a group contract model. |
| [GroupUpdatePollAnswer](types/groupupdatepollanswer.md) | GroupUpdatePollAnswer is a group contract model. |
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
| [InputDraftRichInlineButtonMiniApp](types/inputdraftrichinlinebuttonminiapp.md) | A button that opens a registered Mini App from a temporary rich draft. |
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
| [InputRichInlineButtonMiniApp](types/inputrichinlinebuttonminiapp.md) | A rich-content Mini App button with a formatted label and a registered origin. |
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
| [RichInlineButtonMiniApp](types/richinlinebuttonminiapp.md) | A rich-content Mini App button returned by the Bot API. |
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
| [GeneralTopicHiddenService](types/generaltopichiddenservice.md) | A group forum service reporting general_topic_hidden. |
| [GeneralTopicUnhiddenService](types/generaltopicunhiddenservice.md) | A group forum service reporting general_topic_unhidden. |
| [GroupBotPrivacyChangedService](types/groupbotprivacychangedservice.md) | An immediate privacy-mode change. |
| [GroupChatSharedService](types/groupchatsharedservice.md) | A group or channel selected through request_chat. |
| [GroupLifecycleService](types/grouplifecycleservice.md) | A group lifecycle service. |
| [GroupService](types/groupservice.md) | GroupService is a group contract model. |
| [ManagedBotCreatedService](types/managedbotcreatedservice.md) | A platform service message confirming the managed bot created by the human in the private chat with its manager. |
| [MemberUpdated](types/memberupdated.md) | A change to a user's membership status in a chat. |
| [MessagePinnedService](types/messagepinnedservice.md) | A service event identifying a message that was pinned. |
| [MessagesDeleted](types/messagesdeleted.md) | The messages reported as deleted from one chat. |
| [MiniAppDataService](types/miniappdataservice.md) | A service event carrying page data and the text of its saved reply keyboard button. |
| [PaymentRevertedService](types/paymentrevertedservice.md) | A service message reporting that a previously confirmed payment was reverted. |
| [PollOptionAddedService](types/polloptionaddedservice.md) | A service event identifying an option added to a poll. |
| [PollOptionDeletedService](types/polloptiondeletedservice.md) | A service event identifying an option removed from a poll. |
| [ProximityAlertService](types/proximityalertservice.md) | A service event reporting that a live location entered another user's proximity radius. |
| [QueuedUpdatesDropped](types/queuedupdatesdropped.md) | A notice identifying updates lost from the delivery queue. |
| [ReactionUpdate](types/reactionupdate.md) | The previous and current reactions selected by a user on a message. |
| [Service](types/service.md) | A service event carried by a message. |
| [SourceUpdatesDropped](types/sourceupdatesdropped.md) | A notice identifying a gap in the source event sequence for a chat. |
| [StoppedMessageGeneration](types/stoppedmessagegeneration.md) | The draft generation stopped by the user, identified by chat and draft ID. |
| [SubscriptionNoticeService](types/subscriptionnoticeservice.md) | A buyer-visible service message about a manually renewed subscription. |
| [SuccessfulPaymentService](types/successfulpaymentservice.md) | A transfer confirmed by the payment service. |
| [TopicClosedService](types/topicclosedservice.md) | A group forum service reporting topic_closed. |
| [TopicCreatedService](types/topiccreatedservice.md) | The accepted service message that creates and identifies a topic. |
| [TopicEditedService](types/topiceditedservice.md) | The accepted service message describing a changed topic name or icon. |
| [TopicReopenedService](types/topicreopenedservice.md) | A group forum service reporting topic_reopened. |
| [Update](types/update.md) | An event delivered to your bot. |
| [UpdateBatch](types/updatebatch.md) | The ordered updates returned by a polling request. |
| [UpdateBusinessConnection](types/updatebusinessconnection.md) | An update reporting the connected account state or permissions. |
| [UpdateBusinessEnvelope](types/updatebusinessenvelope.md) | A raw encrypted Business update for polling or webhook delivery. |
| [UpdateCallbackQuery](types/updatecallbackquery.md) | An update carrying an inline button press. |
| [UpdateChannelPost](types/updatechannelpost.md) | A new post in a public channel. |
| [UpdateEditedChannelPost](types/updateeditedchannelpost.md) | An edited post in a public channel. |
| [UpdateEditedMessage](types/updateeditedmessage.md) | An update carrying an edited message. |
| [UpdateGuestMessage](types/updateguestmessage.md) | A Guest invocation for this bot only. |
| [UpdateInlineQuery](types/updateinlinequery.md) | An inline query update containing the requesting user, query text and answer deadline. |
| [UpdateManagedBot](types/updatemanagedbot.md) | An update reporting a Managed bot and its human owner. |
| [UpdateMessage](types/updatemessage.md) | An update carrying a new message. |
| [UpdateMessageReaction](types/updatemessagereaction.md) | An update carrying a change to a message reaction. |
| [UpdateMessagesDeleted](types/updatemessagesdeleted.md) | An update carrying deleted messages. |
| [UpdateMyChatMember](types/updatemychatmember.md) | An update carrying a change to your bot's chat membership. |
| [UpdatePoll](types/updatepoll.md) | An update carrying a changed poll. |
| [UpdatePollAnswer](types/updatepollanswer.md) | An update carrying a user's poll answer. |
| [UpdatePreCheckoutQuery](types/updateprecheckoutquery.md) | An urgent pre-checkout query that expires at its original deadline. |
| [UpdateRefundRequestUpdated](types/updaterefundrequestupdated.md) | A durable refund request update ordered with this payer's payment events. |
| [UpdateRenewalDue](types/updaterenewaldue.md) | An ordered reminder that a subscription needs manual renewal. |
| [UpdateStoppedMessageGeneration](types/updatestoppedmessagegeneration.md) | An update carrying a user stopping draft generation. |
| [UpdateSubscription](types/updatesubscription.md) | Cancel the specified buyer's subscription without refunding confirmed payments. |
| [UpdateSubscriptionUpdated](types/updatesubscriptionupdated.md) | A durable subscription update ordered with this payer's payment events. |
| [UpdateUpdatesDropped](types/updateupdatesdropped.md) | An update carrying lost updates. |
| [UpdatesDropped](types/updatesdropped.md) | A notice that some updates were lost. |
| [UsersSharedService](types/userssharedservice.md) | A service event containing users shared through a request_users button. |
| [WriteAccessAllowedService](types/writeaccessallowedservice.md) | A service event identifying the explicit source of permission to write to the user. |

Return to the [API index](../api.md) to find the operation that uses a type.

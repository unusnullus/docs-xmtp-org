---
description: "Read activity alerts, acknowledge a fix and control owner notifications."
---

# Alerts

You can check activity alerts for your bots in NexoCreator. Send `/alerts`, or open `/mybots`, select your bot and choose Alerts. These notices help you spot a drop in replies or activity. You can access alerts only for bots you own.

## Understand a notice <a href="#understand-a-notice" id="understand-a-notice"></a>

The default eligibility threshold is an average of at least 300 incoming private messages and callbacks per minute across the last 15 minutes. Evaluation uses completed five-minute windows. Suspended and deleting bots are excluded.

| Condition | Default trigger | What to check |
| --- | --- | --- |
| Few private messages sent | The last completed UTC hour has fewer than 30% of the usual outgoing private messages for that weekday and hour. The baseline is the median of the three preceding weeks, all three comparison hours must exist, and the baseline must be at least 18,000 messages per hour. | Check whether your worker is running and whether delivery or sending has stopped. |
| Few replies to private messages | Replies divided by incoming private messages fall below the greater of 20% or half the seven-day median ratio in at least two of the last three windows. | Check message handlers and any services they depend on. |
| Few callback answers | Answered callbacks divided by incoming callbacks fall below the same floor and baseline rule in at least two of the last three windows. | Check that your callback handler answers each query promptly. |

The reply and callback baselines use completed hourly aggregates from the previous seven days. A window with no incoming traffic for that condition does not count as a low ratio. Multiple replies can make a reply ratio exceed 100%. These are activity signals, so a notice does not by itself establish an outage.

NexoCreator combines triggered conditions into one notice per bot per hour. You can still inspect the notice while notifications are muted.

## Act on a notice <a href="#act-on-a-notice" id="act-on-a-notice"></a>

| Action | What happens |
| --- | --- |
| Fixed | Records your acknowledgement and clears that notice's hourly cooldown once. |
| Support | Opens the available support destination with the bot and alert context. |
| Mute 8 hours | Stops alert notifications for eight hours. |
| Mute 1 week | Stops alert notifications for one week. |
| Reactivate | Removes the mute when you open `/alerts`. |

Fixed does not test recovery. A later window can produce another notice, and NexoCreator does not send an automatic recovery message. Repeating a mute action keeps its original deadline rather than extending it. Alerts and their action history remain available for 30 days. Buttons check current ownership, so a former bot owner cannot act on them after a transfer.

For delivery problems, open `/delivery` as described in [NexoCreator](nexocreator.md#delivery-diagnostics-and-recovery). For rejected sends, follow [Errors](errors.md).

# Signal-Modkit: Group Moderation Tools

## What Is This?

Signal-modkit is a bot that helps community groups manage membership and share safety information. It handles three things:

1. **Onboarding** — New members agree to group rules before joining
2. **Mod Alerts** — Members can quickly reach moderators by typing `!admin`
3. **Incident Reports** — Members can report safety concerns, which get broadcast to an alerts channel

---

## Why Do We Need This?

### Signal's Limitations

Signal is excellent for private communication, but it wasn't built for managing community groups:

- **No approval process** — Anyone with a group link can join immediately
- **No moderation tools** — Admins can only remove people after problems occur
- **No bots or automation** — Signal doesn't officially support bots (this tool uses unofficial methods)
- **Admins can't delete others' messages** — Only the person who sent a message can delete it

### What the Bot Adds

| Problem | Solution |
|---------|----------|
| Strangers join without agreeing to norms | Bot requires agreement to rules before adding to group |
| Hard to reach a moderator quickly | Members type `!admin` and all mods get a private alert |
| Incident reports are scattered and informal | Structured reports go to a dedicated alerts channel |

---

## What a Signal Bot Can and Can't Do

Before suggesting features or workflows, it helps to understand what's technically possible. Signal wasn't designed for bots, so there are hard limits.

### ✅ The Bot CAN

| Capability | Notes |
|------------|-------|
| **Send messages** | To individuals or groups (with rate limits — see "Spam Detection Risk") |
| **Receive messages** | Both private DMs and group messages |
| **Send photos and files** | As attachments |
| **Add people to groups** | If the bot is a group admin |
| **Remove people from groups** | Kick or ban (if admin) |
| **React to messages** | Add emoji reactions (✅, ⚠️, etc.) |
| **Edit its own messages** | Within ~24 hours of sending |
| **Delete its own messages** | Within ~24 hours of sending |
| **See who sent a message** | Name/number of sender |
| **See group membership** | Who's currently in a group |

### ❌ The Bot CANNOT

| Limitation | Why It Matters |
|------------|----------------|
| **Delete other people's messages** | If someone posts something harmful, the bot can't remove it — only admins (manually in the app) or the sender can. This is a fundamental Signal design choice. |
| **Edit other people's messages** | Same reason. |
| **See messages from before it joined** | The bot only sees messages sent after it was added to a group. No access to history. |
| **Pin messages** | Pinning is a manual action in the Signal app; not available to bots. |
| **Know if someone read a message** | Read receipts are user-controlled and not exposed to bots. |
| **Send messages invisibly** | Every message comes from the bot's phone number. Members will see the bot as a participant. |
| **Work without a phone number** | Signal requires a phone number to register. The bot needs its own dedicated number. |
| **Make voice or video calls** | Not supported by the underlying tools. |
| **Guarantee delivery** | If someone's phone is off or they've blocked the bot, messages won't arrive. No way to confirm. |
| **Bypass rate limits** | If Signal thinks the bot is spamming, it will be throttled or banned. No way around this. |

### ⚠️ The Bot CAN, But With Caveats

| Capability | Caveat |
|------------|--------|
| **Create groups** | Technically possible, but the bot would be the only admin initially. Usually better to create groups manually. |
| **Change group settings** | Can change name, photo, and permissions if admin — but doing this automatically is rarely needed and could confuse members. |
| **Use disappearing messages** | Supported, but complicates report archives and verification workflows. Not recommended for alerts channels. |
| **Send to many people at once** | Possible, but triggers spam detection. Must be spread out over time with delays. |

### What This Means in Practice

- **The bot is a helper, not a moderator.** It can alert humans and automate simple tasks, but it can't take most moderation actions (like deleting problematic messages).
- **Speed is limited.** The bot deliberately works slowly to avoid being banned. Don't expect instant responses during high-traffic moments.
- **The bot is visible.** Members will see the bot as a group member. It's not invisible infrastructure — it's a participant with a phone number.
- **Some things need human action.** Pinning important messages, deleting others' content, and handling edge cases all require a human admin in the Signal app.

---

## How It Works

### 1. New Member Onboarding

**What new members experience:**

1. They scan a QR code or tap a link (on a flyer, website, etc.)
2. Signal opens a private chat with the bot
3. They send any message to start
4. The bot sends the group rules and asks them to reply "AGREE"
5. Once they agree, the bot adds them to the group
6. They receive a welcome message with tips

**What admins see:** Nothing, unless something goes wrong. The bot handles everything automatically.

**Why this matters:** Every new member has seen the rules and actively agreed. No one joins by accident.

---

### 2. Moderator Alerts

**What members experience:**

1. Something happens in the group that needs moderator attention
2. They type `!admin` (or `!admin someone is being disruptive`)
3. The bot reacts with ✅ so they know it worked
4. That's it — they continue in the group normally

**What admins see:**

1. Each admin gets a private message from the bot with:
   - Who asked for help
   - What they said
   - The last few messages for context
2. Admins can then check the group and take action

**Why this matters:** Members don't need to know who the admins are or message them individually. One keyword reaches everyone.

---

### 3. Incident Reporting

**The alerts channel:**

Incident reports go to a dedicated **read-only alerts channel**:

- Only the bot and admins can post — members can read but not reply
- This keeps it high-signal: just reports, no chatter
- Members can turn on notifications for this channel without getting overwhelmed
- Discussion happens in a separate group (e.g., a general chat or coordination channel)

This separation means the alerts channel stays scannable and useful during fast-moving situations.

**What members experience:**

1. They message the bot privately: *"cops at 5th and Main"*
2. The bot asks follow-up questions to get complete information:
   - Where exactly?
   - When — happening now?
   - How many? What are they wearing? Any equipment visible?
3. The bot asks if they have a photo to include (optional)
4. The bot shows a preview: *"Here's your report. Send this?"*
5. They confirm, and it's broadcast to the alerts channel

**What the alerts channel shows:**

```
🚨 #127 ⚠️ UNVERIFIED
━━━━━━━━━━━━━━━━━━━━━

📍 5th & Main, downtown
➡️ Moving north toward plaza

🕐 NOW (3:42 PM)

⚠️ Police checkpoint stopping vehicles

👥 10-15 officers
👔 Standard uniforms + riot gear
🛡️ Batons, zip ties visible

━━━━━━━━━━━━━━━━━━━━━
```

**What admins can do:**

- **Verify reports** — Send `verify 127` to the bot. The ⚠️ changes to ✅ so members know it's confirmed.
- **Merge duplicates** — If two people report the same thing, admins can combine them with `merge 128 into 127`. The combined report shows "2 reports" so readers know multiple people saw it.

**Why this matters:**
- Reports follow a consistent format — easy to scan quickly
- Unverified vs. verified is clear
- Reporters stay anonymous (their name never appears in the broadcast)
- Multiple reports of the same incident get combined, not duplicated

---

## Security Summary

### What's Protected

| Concern | How it's addressed |
|---------|-------------------|
| **Reporter privacy** | Names never appear in broadcasts. Reports are anonymous. |
| **Photo metadata** | Photos contain hidden data (GPS location, device info). The bot strips all of this before sharing. |
| **Admin phone numbers** | Admins are identified by Signal username, not phone number. |
| **Message content** | Reports are processed by Mistral AI, a log-free AI service hosted in the EU. Content is not stored or used for training. |

### What's NOT Protected

- **Signal itself** — If someone's phone is seized, their Signal messages may be accessible. This tool doesn't change that.
- **The bot's server** — Whoever runs the bot can see reports before they're broadcast. Only trusted people should operate it.
- **Screenshots** — Anyone in the alerts channel can screenshot reports.

---

## Server Security

The bot runs on a server that someone in your organization controls. This server is a potential point of vulnerability.

### What's on the server

- The bot's Signal account (can send messages as the bot)
- Database of report history and member onboarding status
- Temporarily: photos before they're broadcast
- Configuration files with admin usernames

### Recommendations

| Risk Level | Hosting Approach |
|------------|------------------|
| **Standard** | Any reputable VPS provider (DigitalOcean, Linode, etc.). Basic security practices. |
| **Elevated** | Privacy-focused hosting in a favorable jurisdiction (Iceland, Netherlands, Switzerland). Route traffic through a no-logs VPN. Pay with cryptocurrency if desired. |
| **High-risk** | Hosting with providers known to resist legal pressure (e.g., FlokiNET). Route all traffic through a no-logs VPN or Tor. Access server only through Tor. Separate operator identity entirely. |

**No-logs VPN:** A VPN that doesn't keep records of your traffic. This adds a layer between your server and the internet, making it harder to trace activity back to the server. Examples: Mullvad, ProtonVPN, IVPN. "No-logs" claims vary — research before choosing.

**Regardless of risk level:**
- Keep the server updated (security patches)
- Use full-disk encryption
- Use strong, unique passwords and SSH keys (not password login)
- Limit who has server access to as few people as possible
- Keep minimal logs; delete old data regularly

### Who should operate this?

The person running the server can see everything the bot sees. This should be someone:
- Trusted by the organization
- Technically competent (or willing to learn)
- Aware of the sensitivity of the data
- NOT a high-profile target themselves (if possible)

Consider: if this person's devices were seized, what would be exposed?

---

## Spam Detection Risk

Signal wasn't designed for bots. If the bot sends too many messages too quickly, Signal may think it's spam.

### What can happen

| Consequence | Severity | Recovery |
|-------------|----------|----------|
| **Temporary slowdown** | Low | Wait a few hours, messages resume |
| **CAPTCHA challenge** | Medium | Human must solve CAPTCHA to continue |
| **Rate limit** | High | Bot can't send messages for hours/days |
| **Account ban** | Severe | Phone number permanently blocked; need a new number |

### How the bot avoids this

The bot deliberately acts "human-like":

- **Delays between messages** — Waits 3-8 seconds minimum between any messages
- **Longer delays for new contacts** — First message to someone takes 5-15 seconds
- **Typing simulation** — Longer messages have longer delays (as if typing)
- **Jitter** — Random variation in timing so it doesn't look robotic
- **Per-recipient limits** — Won't send more than 10 messages/hour to any one person

### What operators should watch for

- Bot suddenly stops sending messages → may be rate-limited
- Users report not receiving responses → check server logs
- CAPTCHA challenge appears → solve it manually, review what triggered it

### If you get banned

- The phone number is burned; you'll need a new one
- Member onboarding state is preserved; users won't need to re-agree
- Reports in progress may be lost
- Have a backup phone number ready if this is mission-critical

### Reducing risk

- Don't add many users to groups simultaneously (spread over hours/days)
- Avoid "broadcast" patterns where bot messages many people at once
- During high-activity events, the bot may slow down noticeably — this is intentional

---

## What Admins Need to Do

### Setup (One-Time)

**Infrastructure:**
- Acquire a server to run the bot (see "Server Security" above)
- Get a dedicated phone number for the bot (prepaid SIM or online service)
- Create a Mistral AI account and API key — this is the AI service that helps structure reports. Mistral is hosted in the EU with zero data retention (your reports are not stored or logged).

**Signal configuration:**
- Register the phone number with Signal
- Create the groups:
  - **Main group** — where members chat
  - **Alerts channel** — set to "announcement-only" so only bot/admins can post
  - *(Optional)* A discussion channel if you want alerts discussion separate from general chat
- Add the bot as an admin to both groups
- Configure the rules text and admin list

### Ongoing
- Monitor the alerts channel
- Verify legitimate reports
- Merge duplicate reports
- Respond to `!admin` alerts when they come in

### Not Required
- No technical knowledge needed for day-to-day use
- Verification and merging are simple text commands sent to the bot

---

## Summary

| Feature | User Experience | Admin Experience |
|---------|----------------|------------------|
| **Onboarding** | Scan code → agree to rules → join group | Automatic, no action needed |
| **Mod Alerts** | Type `!admin` → get ✅ confirmation | Receive private message with context |
| **Incident Reports** | Message bot → answer questions → confirm | Verify with `verify #`, merge with `merge # into #` |

**Channel structure:**
- **Main group** — regular chat, where `!admin` works
- **Alerts channel** — read-only, high-signal, just incident reports (no discussion)
- **Discussion** — happens elsewhere (main group or a separate channel)

The goal: **Less friction for members, better information for admins, more safety for everyone.**
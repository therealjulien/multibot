# MMultibot

An all-in-one discord.js bot with moderation, games, a virtual economy, and community tools.

## Requirements

- Node.js 20 or newer
- A Discord application and bot, created in the [Discord Developer Portal](https://discord.com/developers/applications)

## Setup

1. Copy `.env.example` to `.env`.
2. Set `DISCORD_TOKEN` to the bot token and `CLIENT_ID` to the application ID. Set `BOT_OWNER_ID` to your Discord user ID to enable `/bot-presence`, and `GEMINI_API_KEY` to a Google AI Studio API key to enable AI. `GEMINI_MODEL` is optional. `GUILD_ID` is not needed; commands are always registered globally.
3. Install dependencies with `npm install`.
4. Register slash commands with `npm run deploy`.
5. Start the bot with `npm start`.

Invite the bot to each server with the `bot` and `applications.commands` scopes. Grant only the permissions it needs: **Kick Members**, **Ban Members**, **Moderate Members**, **Manage Messages**, **Manage Channels**, **View Channels**, **Read Message History**, **Send Messages**, **Embed Links**, and **Add Reactions**. The bot's role must be above the roles of members it moderates. Run `npm run deploy` once to register commands globally; they then become available in every server the bot has joined (subject to each bot invite having the `applications.commands` scope). Discord may take a little time to propagate global command updates.

For message-based levels and accurate leaderboards, enable the **Server Members Intent** in the Discord Developer Portal's Bot settings. For AI chat channels, also enable the privileged **Message Content Intent**. The bot uses message events to award XP and the members intent to keep departed members out of rankings.

## Commands

### Moderation

- `/kick user reason` — remove a member from the server.
- `/ban user reason` — ban a member from the server.
- `/warn user reason` — post a warning in the current channel and DM the member. Requires **Moderate Members**; if DMs are closed, the server warning is still posted and the command reports that the DM could not be delivered.
- `/hackban user_id reason` — ban by Discord user ID, including users who have not joined. If the user is in the server, normal role hierarchy checks apply.
- `/timeout user minutes reason` — timeout a member for 1 minute to 28 days. Requires **Moderate Members**.
- `/purge amount` — delete 1–100 recent channel messages. Discord skips messages older than 14 days.

Discord's default command permissions restrict these to members with the corresponding moderation permissions. The bot also checks role hierarchy before kick, ban, and timeout actions.

### Games

- `/8ball question` — ask the suspiciously confident magic 8-ball.
- `/coinflip` — free coin flip.
- `/rps choice` — play rock-paper-scissors against the bot.
- `/wyr` and `/truth-or-dare pick` — get a silly prompt.
- `/trivia answer` and `/emoji-quiz answer` — answer quick multiple-choice quizzes.
- `/number-guess guess` — try to guess the bot's number from 1 to 10.
- `/riddle` — solve a short riddle (the answer is spoiler-hidden).
- `/sillystory noun adjective verb` — generate a ridiculous mini-story.

### AI

- `/ai prompt` — ask Gemini a question; the answer is private to the requester.
- `/ai-channel action` — requires **Manage Server**. Admins can create and link a new `#ai-chat` channel (the user and bot also need **Manage Channels**), link the current text channel, unlink the AI channel without deleting the Discord channel, or enable/disable AI for the server.
- In the linked AI channel, ordinary text messages receive public Gemini replies. Prompts are limited to 1,500 characters, users have a 10-second cooldown, and up to six recent exchanges are kept in memory per channel. Conversation history is not persisted and is cleared when AI is disabled or the channel is unlinked.

AI requests and messages in linked AI channels are sent to Google's Gemini API. Do not send secrets or sensitive personal information. Configure `GEMINI_API_KEY` locally in `.env`; never commit or share the key.

### Multicoins

- `/balance` — publicly check your server-specific multicoins balance (new players start with 100).
- `/daily` — publicly collect 50 multicoins once per UTC day.
- `/slots bet` — play a public, animated four-reel slot machine with WILD cards. Bets are limited to 1–1,000 multicoins, require a sufficient balance, and have a five-second per-user cooldown.
- `/gamble game wager` — choose coin flip (wins on heads), dice (wins on 4–6), automatic blackjack, or roulette (automatically bets red).

Slot reels use independent weighted rolls: 🍒 Cherry (12), 🍋 Lemon (9), 🍉 Watermelon (6), 🔔 Bell (4), 💎 Diamond (1), and 🃏 WILD (1), out of 33 total weight. WILD substitutes for a regular symbol. Three matching symbols pay the base 7×, 11×, 26×, 64×, or 320× symbol multiplier; four matching symbols pay twice that base multiplier. Each spin randomly selects an integer RTP target from 85% to 98% and scales the paytable to that target. The target is shown in the result embed; integer coin rounding can make the realized return approximate the target. Reels stop one at a time over five seconds. The bet is deducted before the roll and any payout is credited atomically with the balance update.

Gamble results are public embeds. `/gamble` wagers are limited to 1–1,000 multicoins and cannot exceed your balance. Blackjack automatically hits below 17 and pays 3:2 for a natural blackjack (rounded down for odd wagers). Roulette uses a single-zero wheel and automatically bets on red, paying 2×.

Multicoins are fictional, have no cash value, and cannot be purchased or redeemed for real-world prizes. Balances are stored locally in `data/economy.json`; back up that file if you want to preserve the economy.

### Levels

- Members earn 15–25 XP from one message per minute and receive a channel announcement when they level up.
- `/level user` — check your level or another member's level.
- `/leaderboard` — show the top 10 current server members. Departed members are removed on leave and stale records are pruned from the leaderboard.
- `/level-reset user` — reset a member's XP and level; requires **Administrator**.

Level data is stored locally in `data/levels.json`.

### Bot presence

- `/bot-presence status text` — set the bot to online, idle, Do Not Disturb, or invisible and choose its “Playing” activity text. Only the Discord user ID in `BOT_OWNER_ID` can use it. Set `BOT_OWNER_ID` in `.env` before registering commands.

### Community tools

- `/help` — open a public interactive embed. Use its dropdown to browse economy, games, information, AI, moderation, community, and owner commands; the support and bot-invite links remain available in every category.
- `/embed title message color footer` — post a formatted announcement; requires **Manage Messages**.
- `/poll question option1 option2 option3 option4 option5` — create a reaction-vote poll with 2–5 choices. The bot needs **Send Messages**, **Embed Links**, and **Add Reactions**.
- `/suggest idea` — post a community suggestion with 👍/👎 voting.
- `/server-info` and `/user-info user` — display server and member details.
- `/youtube url` — share a YouTube video link so Discord can show its preview when available. This shares the link; it does not download the video.

# Backpacking Gear Discord Bot

Python 3.10+ bot. Each member has one loadout per server. Loadouts persist in
SQLite and can be requested by anyone in the same server.

## Commands

- `/gear create`: private, guided nine-question questionnaire. Also edits an
  existing loadout, with previous answers prefilled. Click Start, enter an
  answer, submit, then click Next question. Saving happens only after the final
  answer. An incomplete questionnaire does not overwrite a saved loadout.
- `/gear show`: posts your loadout in the channel in a code box.
- `/gear show member:@Jim`: posts Jim's saved loadout.
- `/gear delete`: immediately deletes only your own saved loadout.

Questions: Backpack, Sleeping Bag, Sleeping Pad, Tent, Cook Stove, Cook Pot,
Water filtration, Extra or Misc Items, Total Base Weight. Blank answers become
None; blank base weight becomes Unweighed. Weight is user-entered text, not
calculated. Misc items may be comma separated. Code fences in answers are
sanitized, and the bot suppresses mentions.

## Create the Discord bot

1. Open https://discord.com/developers/applications and create an application.
2. Open its Bot section, create the bot if needed, then reset/copy its token.
   Keep this token private; do not send it in Discord or commit it to git.
3. In OAuth2 / URL Generator, select `bot` and `applications.commands` scopes.
   Select View Channels and Send Messages permissions. Do not grant Administrator.
4. Open the generated URL and install the bot in your server.
5. No privileged intents are needed: leave Message Content, Server Members,
   and Presence intents disabled.

## Run on Linux Mint

Extract the ZIP and open a terminal in the backpacking-bot folder:

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
cp .env.example .env
nano .env
python bot.py
```

If creating the virtual environment fails on Mint, install `python3-venv`
with your package manager. Put the bot token in `.env`. For immediate command
availability during setup, set DISCORD_GUILD_ID to your server ID. Enable
Developer Mode in Discord settings, then right-click the server to copy its ID.
Leaving this blank registers global commands, which may take time to appear.
If switching from global to server-specific registration, old global commands
may temporarily coexist; keep the same registration mode for routine use.

Leave this process running; closing it takes the bot offline. For continuous
operation use an always-on computer or host. Keep gear.sqlite3 on persistent
storage and back it up. Run only one bot process against this database.

Questionnaires expire after ten minutes of inactivity; restarting the bot
discards incomplete questionnaires. Start /gear create again if a button
expires or a form is dismissed. Completed loadouts survive restarts. This bot
does not send DMs or read ordinary channel messages. Previously posted lists
remain in Discord after a saved loadout is deleted.

## Verification

```bash
python -m unittest -v
```

Before inviting wider use, run /gear create, complete all nine answers, run
/gear show, have a second member request your list, restart the bot and request
it again, then test /gear delete. Real Discord interaction tests require your
bot token and server; these are not included in offline tests.

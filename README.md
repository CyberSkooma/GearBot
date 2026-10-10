# Backpacking Gear Discord Bot

Python 3.10+ bot. Each member has one loadout per server. Loadouts persist in
SQLite and can be requested by anyone in the same server.

## Commands

- `/gear create`: private, guided questionnaire with shelter-specific questions. Also edits an
  existing loadout, with previous answers prefilled. Click Start, enter an
  answer, submit, then click Next question. Saving happens only after the final
  answer. An incomplete questionnaire does not overwrite a saved loadout.
- `/gear show`: posts your loadout in the channel in a code box.
- `/gear show member:@Jim`: posts Jim's saved loadout.
- `/gear delete`: immediately deletes only your own saved loadout.

## Questionnaire

The bot asks for Backpack, Sleeping Bag, and Sleeping Pad, then presents
**Tent or Hammock?** buttons.

- **Tent:** asks for your tent model, then proceeds to Cook Stove.
- **Hammock:** asks for your hammock model, then asks
  **Do you have a hammock underquilt?** with Yes and No buttons.
  - **Yes:** asks for the underquilt model and includes an Underquilt line in
    the saved and printed loadout.
  - **No:** skips the Underquilt model question and omits that line.

Both paths continue with Cook Stove, Cook Pot, Water filtration,
Extra or Misc Items, and Total Base Weight. The printed shelter label matches
your choice: Tent or Hammock. Existing tent loadouts remain compatible.
When editing, switching to Tent or answering No removes a previously saved
underquilt from the newly completed loadout. Changes are saved only when the
whole questionnaire is completed.

Blank answers become
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

## First-time setup on Linux

Use Python 3.10 or newer. Install the
prerequisites if needed:

```bash
sudo apt update
sudo apt install python3 python3-venv python3-pip
```

On other distributions, use their package manager to install Python, pip,
and virtual-environment support. The commands below assume Bash.

Extract the ZIP and open a terminal in the backpacking-bot folder:

```bash
python3 -m venv .venv
source .venv/bin/activate
pip install -r requirements.txt
cp .env.example .env
nano .env
python bot.py
```


## Restart an existing installation

Open a terminal in your bot folder and run:

```bash
source .venv/bin/activate
python bot.py
```

Stop the bot with Ctrl+C before updating. Leave this process running;
closing it takes the bot offline. For continuous
operation use an always-on computer or host. Keep gear.sqlite3 on persistent
storage and back it up. Run only one bot process against this database.

Questionnaires expire after ten minutes of inactivity; restarting the bot
discards incomplete questionnaires. Start /gear create again if a button
expires or a form is dismissed. Completed loadouts survive restarts. This bot
does not send DMs or read ordinary channel messages. Previously posted lists
remain in Discord after a saved loadout is deleted.

## Add more ordinary gear questions

Edit FIELDS near the top of gear.py. Insert ordinary questions after
Water filtration and before Extra or Misc Items. Keep Tent at position 3
(the fourth entry), because shelter selection currently depends on that
position. Miscellaneous-item and weight handling now use their field names,
so adding entries after Water filtration does not require renumbering them.
Keep Total Base Weight last. Underquilt is conditional and should not be added
to FIELDS. Restart after editing. New fields appear as None in old loadouts
until the owner completes an updated questionnaire.

Keep field labels short enough for Discord's form labels and titles, and
avoid adding so many fields that the printed list exceeds Discord's
2,000-character message limit; this version does not split long messages.

## Verification

```bash
python -m unittest -v
```

Before inviting wider use, test all three paths: Tent, Hammock with Yes,
and Hammock with No. Confirm that only the Yes path asks for and prints an
Underquilt. Also test editing a Yes loadout to No or Tent. Run
/gear show, have a second member request your list, restart the bot and request
it again, then test /gear delete. Real Discord interaction tests require your
bot token and server; these are not included in offline tests.

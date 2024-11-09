# Hosting your own instance

This bot is built on [discord.js](https://discord.js.org/#/) v14, so you'll need
Node.js 16.11.0 (or newer) installed. You will also need your own Discord bot
account. If your platform does not have Node.js 16.11.0, consider using something
like https://github.com/nvm-sh/nvm.

This guide assumes you're hosting on a Linux distro with `systemd`. The bot will
work on other platforms, but you're on your own figuring that out.

## Bot account setup

Your bot account needs the privileged "Server Members Intent" enabled.
It also needs [these permissions](../README.md#permissions).

## Running directly

Quick and easy. Also (mostly) platform-independent!

Create a file `config.json` and paste in the following
(obviously fill in the blanks with your bot's info):
```json
{
  "token": "<your token here>",
  "app": "<your bot application ID here>"
}
```

Install dependencies, register Discord slash commands, and set up the database
for your bot:
```
git clone https://github.com/Mimickal/ReactionRoleBot.git
cd ReactionRoleBot
npm ci
npm run register -- --config path/to/your/config.json
npm run knex migrate:latest -- -- --config path/to/your/config.json
```

Start the bot:
```
npm start -- --config path/to/your/config.json
```

## Running as a service

A little more effort to set up, but better for long-term use.

Follow [these same steps](#running-directly) for config and dependency setup,
except don't start the bot yet.<br/>

By default, your config is expected to live in `/etc/discord/ReactionRoleBot/config.json`.
You can change this with the `--config` argument ([see here](#additional-config)).

This bot comes with an example `systemd` service file,
[`reactionrolebot.service`](../resources/reactionrolebot.service).<br/>
You'll need to modify this file for your specific environment.
Be sure to carefully read the comments in this file!

Once you've done that, pick one of the following ways to run the bot:

#### System service

1. Copy `reactionrolebot.service` to `/etc/systemd/system/`
1. Run `systemctl restart reactionrolebot.service` to start the bot.

#### User service

1. Copy `reactionrolebot.service` to `<your home dir>/.config/systemd/user/`
1. Run `loginctl enable-linger <your user here>`
1. Run `systemctl --user enable reactionrolebot.service`
1. Run `systemctl --user start reactionrolebot.service`

## Running this bot in Pterodactyl

Until the original developers come up with a way to run this bot using github URL and a target execution file, please use this. This is **not official** instruction from the original developers directly, this is from KingColton1's instruction based on their trial and error with setting up this bot in Pterodactyl. If you prefer a server management panel like Pterodactyl over systemd, follow this.

1. Create a new server via admin panel
2. Choose NodeJS 18 (this is important, DO NOT GO WITH ANY OTHER VERSIONS OTHER THAN 18!!! If you haven't got this egg, [please grab this script](https://github.com/pelican-eggs/eggs/tree/master/generic/nodejs).)
3. Put `if [[ -d .git ]] && [[ {{AUTO_UPDATE}} == "1" ]]; then git pull; fi; if [[ ! -z ${NODE_PACKAGES} ]]; then /usr/local/bin/npm install ${NODE_PACKAGES}; fi; if [[ ! -z ${UNNODE_PACKAGES} ]]; then /usr/local/bin/npm uninstall ${UNNODE_PACKAGES}; fi; if [ -f /home/container/package.json ]; then /usr/local/bin/npm install; fi; if [[ "${MAIN_FILE}" ]]; then /usr/local/bin/npm run register -- --config "/home/container/${MAIN_FILE}"; /usr/local/bin/npm run knex migrate:latest -- -- --config "/home/container/${MAIN_FILE}"; /usr/local/bin/npm start -- --config "/home/container/${MAIN_FILE}"; fi` in Startup Command. This is basically same way as you'd install in "Running directly" section but actually work with pterodactyl.
4. Put `https://github.com/KingColton1/ReactionRoleBot.git` in Git Repo Address
5. Put `master` in Install Branch
6. Put `config.json` in Main File (this is extremely important)
7. Allocate necessary number of memory and storage space. I strongly recommend anywhere above 1 GB for memory and 500 MB for storage.
8. Create server
9. Server should be *unable* to start up - that is expected as you don't have config.json. Make a new config.json and paste following;
```json
{
  "token": "<your token here>",
  "app": "<your bot application ID here>",
  "database_file": "<your sqlite3 file here>"
}
```
9. Create a new sqlite3 file, do not create new table or anything inside that. A empty sqlite3 file is sufficient. If you do not know how to make one, a easiest option is to install [DB Browser](https://sqlitebrowser.org/) and simply create new database then exit "Create a Table" popup. That new sqlite3 file should appear in a location in your file system, e.g. Desktop if you choose where to put them when you make a new database. Finally upload a newly created sqlite3 file to the server's file system and update config.json if you haven't.
10. Finally, start your bot and it should be able to initialize everything and start bot.

## Additional config

`config.json` supports a few more optional fields:

- **database_file** - Use a different SQLite3 database file. This defaults to `/srv/discord/rolebot.sqlite3`. Users on non-Linux systems will probably want to change this.
- **enable_precache** - `true` or `false`. Makes the bot pre-cache all messages with reaction roles on startup. This can help the bot be more consistent when it first starts, but will cause larger bots to be rate limited. Use with caution!
- **guild** - A Guild ID. Limits slash command registration to this Guild. This is useful for bot development.
- **log_file** - Use a different log file. This defaults to `output.log` in the project root.

You can also run the bot with a config file other than `config.json`, but you must provide an absolute file path!

Examples:
```
# Double-double-dashes escape arg parsing for both npm and knex to pass the config to our code.
npm run knex migrate:latest -- -- --config /absolute/path/to/my_config.json

npm start -- --config /absolute/path/to/my_config.json
```

## SQLite3 errors

*Update as of November 11, 2024, this forked repo fixed this issue by adding tsx package and upgrading sqlite3 to 5.0.0 as well as changing command in package.json's scripts. This essentially solves SQLite3 error for good, but if you face SQLite3 error somehow, read here.*

The `npm ci` procedure includes compiling SQLite3 from source. Building from source is always a little harrowing because there are a lot of reasons it can fail. If you run into errors building SQLite3, try one of the following:

- Delete `package-lock.json` and run `npm install`.
- Try installing [build tools](https://stackoverflow.com/a/59719695).
- Run `npm rebuild node-gyp`.
- Run `npm install -g node-gyp`.

Try these **one at a time**.

## Overview
A Discord bot that can assign roles based on message reactions.

## Why this bot?
Several other popular role-react bots exist, but many of them have some annoying
catch. Some have uptime issues, some lock basic functionality behind premium pay
walls, and some come with way too many other features that add bloat, requiring
convoluted web APIs for configuration. In most cases the source code also isn't
available, so we can't do anything about it.

This bot attempts to address these issues. It _only_ does role reacts, and is
configured by typing to it in a Discord channel. Every feature of this bot is
completely free to use, and always will be. It's also open source, so you can
modify it to better suit your needs, or just download it and host your own
instance. Basically, there's no bullshit.

# Usage
You can interact with the bot by mentioning it (denoted here as `@bot`). The bot
will currently only respond to users with the "Administrator" permissions.

You write the post people can react to for their roles. The bot will not attempt
to write its own posts for this. Then you tell the bot which roles to map to
which emojis for the selected post.

Selecting a post. The bot tracks this on a per-user basis, so multiple users can
interact with the bot at the same time.
```
# Command:
@bot select <channel> <message_id>
@bot select <channel_id> <message_id>
@bot select <message_link>  # Not yet supported!

# Examples:
@bot select #role-assginment 123456789123456789
@bot select 123456789123456789 1234556789123456789
@bot select https://discordapp.com/channels/123456789123456789/123456789123456789/123456789123456789
```

Adding a role to the post. The bot will add its own reaction to the selected
post with the given emoji.
```
# Command:
@bot role-add <emoji> <role>
@bot role-add <emoji> <role_id>

# Examples:
@bot role-add 🦊 @test-role
@bot role-add 🦊 1234556789123456789
```

Removing a react-role from a post. The bot will remove all reactions of this
emoji from the selected post.
```
# Command
@bot role-remove <emoji>

# Examples:
@bot role-remove 🦊
```

You can also print the bot's description, version number, and link to the source
code. This command is available to all users.
```
@bot info
```

## Hosting your own instance
This bot is built on [discord.js](https://discord.js.org/#/), so you'll need
Node.js installed. Discord.js says it requires Node.js version 12.0.0 or higher,
but I've been testing with version 10.19.0 with no issues.

The `resources` directory has a service file that can be used with Linux distros
with systemd. If you're installing this on some other operating system, you're
on your own.

_I will add more detailed setup instructions later._

# discord-bot
This repository contains information on how to quickly set up a functional bot in Discord using the python API. 

## Uses
Bots in Discord can track miscellaneous junk you don't want to, handle responses to events, and just generally automate out tedious tasks. I personally set my first bot up when I wanted to track information for a tabletop game I GM for. 

## This Guide
This guide will assume the user has a Discord, but not much experience outside of using it. By the end of this you should have a functional bot that can perform a few basic commands, respond to events, and save information received from users to a local drive or the cloud. Expanding from there is up to you.

### (1) The Developer Portal
First, head on over to the [Discord Developer Portal](https://discord.com/developers/applications) and create an account. Afterwards, you should see something like the below:

![Developer_Portal](https://github.com/Cromab/discord-bot/assets/145014565/2621daa5-c712-4f36-8137-bcb4abc6c59a)

Next, lets go ahead and click on New Application. We'll name our bot "my_bot" and accept the terms of service. You should see something like the below:

![image](https://github.com/Cromab/discord-bot/assets/145014565/e37f171f-cd2e-4447-b402-9a71c9e60881)

Feel free to add a description or change the name of your new bot. We still have a few steps to go before we can really use it, though. Click on over to OAuth2 and the URL Generator.

![image](https://github.com/Cromab/discord-bot/assets/145014565/f8769878-977f-405a-9650-17b12df44dc4)

We have to select some scopes for our bot, and the obvious choice is bot. Additionally, we'll need to set up the permissions for our bot. We're going to go ahead and give ours administrator privileges. Best practice is to only give your bot what it needs to operate, but this is good enough for this guide.

![OAuth2_Generator](https://github.com/Cromab/discord-bot/assets/145014565/ab62df21-dedb-4a0b-b613-dfe246859513)

Click "Copy" by the Generated URL and go ahead and paste it into your browser. You should be redirected to something like the below:

![Bot_URL](https://github.com/Cromab/discord-bot/assets/145014565/10441341-5810-4362-8d25-7532149df267)

Go ahead and select the server you want to add this bot to and continue. You'll be redirected to that server and you should see the bot in your server. Congratulations! You have added a bot to your Discord server.

Next we're going to talk about setting up our bot, making sure it's ready, and checking if it's online.

### (2) Build-a-Bot
The first step to building our bot is to install the discord.py using pip.
```shell
pip install -U discord.py 
```

This package allows us to interact with Discord API and has many applications beyond what we'll be outlining and can be found [here](https://discordpy.readthedocs.io/en/stable/). If you're interested in building out some awesome tools, please go ahead and read those docs.

For us, we're going to be focusing on just the basics. With that, let's start by creating our bot.py script. Here we'll start with the imports we need and the all important .env file. The below chunk of code is our set-up for this.

```python
import os
import discord
from discord.ext import commands
from dotenv import load_dotenv

load_dotenv()
token = os.getenv('discord_token')

```

Reading the above snippet, you may wonder "Wait. What's a discord token and when did we get that?" You'd be right, we didn't! In the same directory where your bot.py file is, create a .env file. This will be where we store, retrieve, and optionally alter our environment variables. You could just paste your discord token in here. But it's best practice to use an environment variable rather than expose your token to the world. 

To find your bot's token, head back over the Developer Portal, click "Reset Token" and then go ahead and copy and paste that in your .env file and assign to a variable called discord_token.

![image](https://github.com/Cromab/discord-bot/assets/145014565/a8fe064a-267f-4c5b-b6cc-4bcfd470dc7c)

Now, let's see if we can create a connection to Discord for our bot.

```python
bot = commands.Bot(command_prefix='!', intents = discord.Intents.default())

@bot.event
async def on_ready():
    print(f'{bot.user.name} has connected to Discord!')

bot.run(token)
```

Here's we've done a few things. We've instantiated our bot and given it a prefix to denote commands. For example, if we have a command called hello, and end-user would type "!hello" in your Discord server and the bot would execute the command.

The second piece is the @bot.event. We can set our bot to respond to many different instances in Discord. Events are notifications to the server. We also add an on_ready() function, which when called will print the status of our bot to the console. If you haven't made the connection, the event here is literally if our bot is ready.

Try running the below from your terminal and you should receive back "my_bot has connected to Discord!"

```shell
python bot.py
```

Now, let's see about building our own custom commands and discussing intents.

### (3) Intents and Commands
You may have noticed when running your bot for the first time that you received a warning, namely, 

"WARNING  discord.ext.commands.bot Privileged message content intent is missing, commands may not work as expected."

So, what exactly does this mean? Our bot can receive commands, but if any commands need the bot to output to receive commands from a text channel or to produce an output in the text channel, our bot won't be able to. Since this is the intent behind almost any commands we'll create, that's kind of a problem. 

To fix it, let's head over to the Developer Portal again. Under "Bot" let's look at the Authorization Flow settings. There are several options, but for now let's just go ahead and give our bot Message Content Intent privileges. Then save changes.

![image](https://github.com/Cromab/discord-bot/assets/145014565/aed90825-1b62-4c29-be6b-7fc4875d915d)

But we'll have to make changes in our code, too. We set the intents of our bot to the default Discord intents, which don't include message content. So, let's factor our argument.

```python
#Declare intents
intents = discord.Intents.default()
intents.message_content = True

#Bot instantiation
bot = commands.Bot(command_prefix='!', intents=intents)
```

Now our bot can access message content. The next logical thing is to create a command. For our first command, let's create a simple "hello" command. Here's the syntax.

```python
@bot.command(name="hello")
async def hello_world(ctx):
    await ctx.send("Hello, World!")
```

Now, instead of an event, we're creating a command, so our context changes. Whenever we create a command for our bot, it will always take at least one argument in our function. This argument is called the context. The context is imperative. It's what allows a user to send a command in any text channel and have the bot respond in the same channel.

The "await" keyword may also be new. It is used within an async function to handle a promise (a JavaScript concept). The important thing is that a promise can only have one state: pending, resolved, or rejected. An async function will always return a promise, and await waits until the promise has reached its terminal state. In this case, the promise resolves in sending "Hello, World!" to the channel defined by our context, ctx.

Try running your bot.py file again and typing !hello into the chat. 

You now have a Discord bot with it's own custom commands! From here you can continue to build out your bot to perform a host of desirable tasks from creating checklists to help menus to fun little mini-games for your Server. 

## The Rest
If you have any mini-commands you'd like to add to your bot, many may already exist. I have included a few I use in the bot.py file. Have fun!

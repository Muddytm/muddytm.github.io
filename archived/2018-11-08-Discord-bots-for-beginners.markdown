
Since the dawn of time, before the heralding of the dawning sun and the birth of the moon and stars, mankind has been captivated by the lingering thought: "how do I make a bot in Discord?" Well, my friends, I have come to bequeath knowledge upon mankind in the form of this how-to guide on coding a simple Discord bot.


# First things first

Before you get started, you'll need these things:
- An internet connection! Duh.
- Python 3.5 or 3.6! Preferably.
- Discord.py (found at https://github.com/Rapptz/discord.py)

If you have these, you're 90% likely to be able to follow this guide. The Python bit is specifically important because the Python package we're using (Discord.py) works best with those versions of it. Once you go with versions later than 3.6, some dependencies start *freaking out* and it's a bad time overall. Make sure to install Discord.py with Python 3.5 and not accidentally with a different version, or you're gonna get a bucket full of errors.

Also recommended is a place to host the bot. For my bots, I use an AWS (Amazon Web Services) EC2 instance, which is either free or almost-free. One of those. I've never looked too closely. In any case, you can create a Linux box there that can easily perform whatever functions you want out of your super cool bot.

Can't think of anything else, so let's get to it!


# Discord bot creation

Actually, scratch that, we're still *technically* setting stuff up. Let's go ahead and get a bot user set up through Discord!

Go here and follow along: [https://discordapp.com/developers/applications/]

First off, go ahead and create an application and name it...whatever you want. The bot I'll be making will be called Corgi Bot, and will post things about corgis. Corgis are so hot right now.

![](https://i.imgur.com/x6XNF9n.png)

Don't mind the dog, he's just here to protect our privacy. Anyways, you'll want to copy the one labeled CLIENT ID that you've got and put it somewhere for later reference.

Over on the sidebar to the left, select "Bot", and you'll come to a screen with another button labeled "Add Bot" on it. Click that and you should see something like this:

![](https://i.imgur.com/hz3AvAa.png)

No need to change anything here, but you WILL want to copy that token. Click the copy button and paste that bad boy somewhere you'll remember to find it later on.

Now we want to add this bot to a server so it can actually do stuff! There's a couple things to consider before doing so, though. First off, what permissions do you want the bot to have? If this bot only posts pictures of corgis, we'll likely only want permissions such as **Send Messages** and **Attach Files**. If we want users to ask the bot to post pictures of corgis, we'll want the bot to be able to **Read Messages** as well. However, if you're making a bot that will essentially act as administrator or moderator, you'll probably want to give it permissions like **Administrator**.

But how do we do this? Well, to manage the permissions that your bot will have and then begin the process of adding it to a Discord server, I would recommend using this site since it's plain ol' easy peasy: [https://discordapi.com/permissions.html]

Go to that site and put in whatever permissions you want, then put the Client ID you wrote down earlier and put that in the specified blank. Should look something like this, but obviously without the dogs and with (possibly) different permissions than mine:

![](https://i.imgur.com/Cjb01og.png)

Go ahead and click that link at the bottom, and you'll be brought to this page:

![](https://i.imgur.com/Yyw6T6a.png)

Select whatever server you want the bot to join, click Authorize, and you're good to go!


# Time to actually code

So from here on out there's gonna be some coding involved. Personally I'm a sucker for Python so we'll be using that for this tutorial. If you're looking for a good text editor that supports all sorts of file formats and can be modified with gnarly plugins, I recommend Atom. I'll also be running the bot with Python 3.5 through Powershell, but you can run it through anything that can run Python 3.5/3.6, like the aforementioned Amazon EC2 Linux instance.

First things first, let's get the bot to actually come online. Right now, it's offline:

![](https://i.imgur.com/fP2jgy3.png)

Open up Atom or whatever text editor you've got, and make a new file with whatever name you want - mine will be named **run.py** (the ".py" being the file extension for a Python script). This will start us off nice and easy:

```python
import config
from discord.ext import commands

TOKEN = config.app_token
BOT_PREFIX = "!"
client = commands.Bot(command_prefix=commands.when_mentioned_or(BOT_PREFIX))

@client.event
async def on_ready():
    print ("Logged in as")
    print (client.user.name)
    print (client.user.id)
    print ("------")


@client.command(pass_context=True)
async def bark(ctx, stuff=""):
    """Bark!"""
    await client.say("Bark!")

client.run(TOKEN)
```

Woah, buckaroo - that's a lot to take in. No worries, though, let's walk through it bit by bit. First I'd like to start with these 2 lines, as a quality-of-life tip:

```python
import config

TOKEN = config.app_token
```

This is not strictly necessary - you can simply get rid of these 2 lines and add `TOKEN = "xxxxxxxxx"`, where the string of x characters is your bot user token (you wrote that down somewhere, right?). However, if you make a file named "config.py" in the same directory as your bot script and add `TOKEN = "xxxxxxxxx"` to it, then that variable can be imported by the two lines above. This is helpful so that you can use a .gitignore file to "hide" config.py when pushing your code to Git, effectively keeping your code secure by obscuring your API tokens. Neat huh? No? Whatever, I thought it was.

Onto the next:

```python
from discord.ext import commands

BOT_PREFIX = "!"
client = commands.Bot(command_prefix=commands.when_mentioned_or(BOT_PREFIX))
```

That first line is importing the relevant part of the Discord.py package to be used. If you look up other Discord.py tutorials online you'll doubtless find a few other ways people are importing the package, but this is the way I do it, so. :)

BOT_PREFIX is the variable that must be placed before a command word when attempting to use it in Discord - for instance, !quote, !test, !help, and so on.

The "client" variable is basically being initialized with this BOT_PREFIX variable so it knows how to be contacted directly - also notice the "when_mentioned_or()" function. This allows the bot to be directly mentioned, in place of using the bot prefix.

```python
@client.event
async def on_ready():
    print ("Logged in as")
    print (client.user.name)
    print (client.user.id)
    print ("------")
```

This is an *event* that occurs when the bot has effectively started. There are a lot of events that can be modified to do unique things with Discord.py. All that happens here are some print commands, as seen above - when running the bot through an interface like the Linux terminal or Powershell, this information will be printed. It will not be printed to Discord, though.

```python
@client.command(pass_context=True)
async def bark(ctx, stuff=""):
    """Bark!"""
    await client.say("Bark!")
```

This one is a bit trickier, so I'll try and explain each bit as best as possible.

The decorator "client.command" comes with the parameter "pass_context=True", which allows context about the message author, channel, server, and so on to be obtained. This information can be obtained through the passed variable "ctx".

The variable "ctx" is a mystical and powerful variable which gives information about nearly anything in the server, provide you feed it the right syntax. I'll elaborate more on this later, but for this example we can stop right there.

The variable "stuff" is whatever the author types after the initial command. The command in this case is "!bark", but if the author instead typed "!bark meow", the variable "stuff" would then be "meow". By default it is "", or simply put, an empty string. If it is instead typed as "\*stuff", then every word typed after the initial command will be given as a list of individual strings (i.e. "!bark meow moo quack" would make stuff = [meow, moo, quack]). Again, not particularly useful for *this* example, but good information to have nonetheless.

The last line, `await client.say("Bark!")`, has a lot going on. First off, the "await" bit is 100% necessary or nothing will happen. So don't forget that. The function say() is a convenient command that basically says "hey, say this thing in the same channel as the command that was sent." As a result, "Bark!" will be posted in the same channel as the prompting command. An alternative way of writing this could be `await client.send_message(ctx.message.channel, "Bark!")`.

Finally:

```python
client.run(TOKEN)
```

Try running the script without this and nothing will happen (besides a nice little bug salad). This command starts the bot with the bot user token we wrote down earlier, and immediately runs the on_ready() event and starts vigilantly checking for messages.


# Finally, let's run this freaking thing

When you're ready, go ahead and run the script however you want. Here's what mine looks like:

![](https://i.imgur.com/O7FCN81.png)

And if we were spot-on with our code review, then typing "!bark" should cause the bot to respond in the same channel with "Bark!", right? Let's see:

![](https://i.imgur.com/wjfWP1z.png)

Mission accomplished! And if you're seeing the same results on your side, then congrats! You've made your first (uh...I assume?) Discord bot!


# Other good-to-know stuff!

You've probably noticed that now you don't know how to close your bot besides killing the process entirely or closing out of the window running it. That's why I like to add a logout function accessible only to certain roles (i.e. admin), as seen below:

```python
@client.command(pass_context=True)
@commands.has_any_role("Admin")
async def logout(ctx, stuff=""):
    """Dismiss the bot."""
    await client.say("Guess I'm in the doghouse! *Ba-dum-tss*")
    await client.logout()
```

![](https://i.imgur.com/YpRqLZe.png)

Of course, "!logout" will only work if the command's author has the role "Admin".

What if you want *every* message typed in your Discord server to be subject to the bot's unearthly gaze? Well, say no more! There's an *event* for this particular thing:

```python
@client.event
async def on_message(message):
    """Check every message that comes through."""
    if "cat" in message.content:
      await client.send_message(message.channel("Cat lover detected!"))

    # This allows us to use other commands as well.
    await client.process_commands(message)
```

Basically, this allows your bot to read every message that is posted. The messages still get posted normally, of course, but this event allows your bot to have some additional finesse in what it does when certain messages are seen. In this example, when "cat" is seen in any message, the bot will pipe up:

![](https://i.imgur.com/y1KFUKx.png)

**NOTE:** that last line in the function is *very* important - `await client.process_commands(message)` allows the message to still be checked for command compatibility. If someone types "!bark" (for example), it will go through this event function first, and then be passed into process_commands() to check if it matches up with any other commands. If this doesn't happen, the message will effectively be lost to the void.


# That's it (not really)

That's all I'll go over for now, but there oodles of other functions that you can perform with Discord automation. I might write a post about some of them later, but some key ones I'm thinking of are:

- Attaching files (images, videos, audio) to a message
- Doing stuff with user roles
- Changing server properties like server image, channel names, role names
- Message moderation - easily doable with on_message()
- Interaction with other APIs
- Data storage (i.e. user quotes via !quote)

Pester me if you'd like to see more information on one or more of these topics. :)

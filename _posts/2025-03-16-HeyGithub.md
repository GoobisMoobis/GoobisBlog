---
title: 'Hey github blog.'
date: 2025-03-16
tags: "DevBlog"
---

# Jesus it's been a while...

Hello to absolutely nobody in particular (because nobody reads these). If you don't remember me, it's Fender!

I recently restarted on my roblox studio high and thought I'd make a blog post after like, 40 years.

> So, what have you been doing?

I quit Luau to do JavaScript, HTML, and CSS (web developement). I now know all of these pretty well, and randomly one day, [Phoenix (click here if you forget who he is)](https://goobismoobis.github.io/GoobisBlog/2024/08/25/FirstDevblog.html#:~:text=on%20with%20my-,IRL%20freind%20Phoenix,-%2C%20an%20online%20friend) asked me if I wanted to code a roblox game.

I already had one project I was working on before I quit studio, but had quit in the middle of making it's foundation and it was relatively basic.

It is no longer basic.

[![Untitled Road Trip Game Thumbnail](https://raw.githubusercontent.com/GoobisMoobis/GoobisBlog/refs/heads/my-pages/images/untitledroadtripthumbnail.png)](https://www.roblox.com/games/103025829078438/untitled-road-trip-game)

Welcome to Untitled Road Trip Game by Yimello Studios!

I've talked about it before, but Yimello Studios is the group me, Phoenix, and [WhiteTurtle](https://goobismoobis.github.io/GoobisBlog/2024/08/25/FirstDevblog.html#:~:text=online%20friend%20whiteturtle0923) make our games on. And this is our newest project!

> Wait, what happened to Modern RTS?

If you don't know, Modern RTS is the game I've mentioned in literally every other DevBlog.

A lot broke.

Most of it was due to our spaghetti code just... breaking out of the blue (?), but here are some other reasons

- I got bored
- Phoenix never even helped by the end, and was just a tester
- WhiteTurtle could get on less and less
- I started playing NRTS much more often after joining the [commanders league](https://discord.com/invite/cSXbDfNfUm)
- I got back into Minecraft and making MCBE addons


Eventually (most likely during the summer) me and WhiteTurtle have agreed to start back up work on it. For real this time.

However, during that time we will be working on URTG. (work will not stop on URTG when we start work on MRTS!)

> So, what is URTG?

URTG is an infinite road trip game where different events happen at random. Currently, we have these events:
- SFOTH (Sword Fight On The Heights)
  - A reference to one of the earliest popular roblox games, this makes a spinning decal appear below the truck you ride on in-game and gives every player a sword.
- Car breakdown **(Hardest event)**
  - Makes your truck break into pieces. You must rebuild it while being gangbanged by zombies
- Tiny car
  - Makes your truck (and trailer if you have it) tiny for a few seconds
- Convenience store
  - The truck pulls up to a store in which you can buy coils and if you have a trailer you can get an item for it
- Jeff the killer
  - Spawns Jeff on your truck. You must survive him.
- We have events that can happen randomly during other events too!
  - Trailer: A triler spawns in a secret location...
    - Radio: a radio to add to the trailer spawns in a random location...
   
We also have a robust admin system!

A lot of it is stolen from our other games, such as the MRTS terminal from MRTS and the textchat commands from our tower defense game (I've never mentioned that here before, but before we even STARTED MRTS we were making a tower defense game)

Current textchat commands (Prefix: !):
- The only command anyone can use is !default/!d, which puts you in the default roblox text channel
- Availible to testers
  - **!tester/!testers/!test** opens a private text channel between testers (admins/owners can see it too)
  - **!commands/!cmds** will open a gui menu with every availible command
  - **!fly/!f** will allow the player to fly
- Availbile to admins
  - **!admin/!admins/!a** opens a private text channel between admins (owners can see it too)
  - **!terminal/!t** will open the command terminal in which you can run both client-sided and server-sided code. Theres also many pre-built scripts for ready-use
  - **The F2 key** will open a file explorer menu, in which you can see EVERY SINGLE ITEM in the ENTIRE game. (MASSIVE shoutout to [tyridge77](https://devforum.roblox.com/t/live-game-explorerv33/2693615))
  - **!admingui/!admgui/!ag** will open the pre-built admin gui where you can kick, ban, and send server-wide broadcasts
- The only owner-exclusive command (exclusive to me, whiteturtle, and phoenix) is !owner/!owners/!o, for our private chat.

> Note: WhiteTurtle is **not** helping dev this game.

Here are some improvements I've made to the stuff I stole from our other projects!

- The terminal
  - Added support for easier client-side loadstring code execution
  - Instead of using [vLua](https://devforum.roblox.com/t/vlua-loadstring-reimplemented-in-lua/2495756), which only supported Lua 5.1, it is now using [vLuau](https://github.com/kosuke14/vLuau), which supports the adaptation of lua that roblox uses, [Luau](https://luau.org/)
  - Added autocomplete
  - Made the syntax module (used to change the colors of the text we type based on whatever luau code object it is) we are using better
- Admin gui
  - FINALLY made the announcement system work and look good
- Command Gui
  - Finally used richtext to format the command menu (which lists every availible command)
  - Made the search system (used to filter commands) better
 
  Anyways, that is it for now!

  I might open-source the terminal. I'm not sure.

  Feel free to Dm me on discord @justa_rando8888 if you want to be a tester for the game!

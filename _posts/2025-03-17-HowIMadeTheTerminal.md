---
title: 'How I made the terminal for MRTS and URTG'
date: 2025-03-16
tags: "DevBlog"
---

> I highly reccomend reading about [URTG](https://goobismoobis.github.io/GoobisBlog/2025/03/16/HeyGithub.html) and [MRTS](https://goobismoobis.github.io/GoobisBlog/2024/08/25/FirstDevblog.html) if you haven't already

# Welcome back to my blog!

I'll try to start posting more regular updates on here. No promises!

So, what is this blog about?

![THE TERMINAL!](https://raw.githubusercontent.com/GoobisMoobis/GoobisBlog/refs/heads/my-pages/images/theterminalwithintroductioncomments.png)

If you haven't heard me talk bout the terminal, please do what I said at the beggining of this post and read about URTG and MRTS

## Without further ado, here is how I came up with the terminal, how I made it, etc

When I originally started MRT I had no intention of making a terminal. Or even any sort of admin gui. So, what sparked my inspiration?

Well, it actually didn't start with MRTS, it started with a side project I was working on called noob factory tycoon. I was trying to run something from roblox's default command line, but it wasn't working. I kept getting an error.

Then I realized 2 important things about roblox's basic command line interface

1. It's really bad. There's no syntax highlighting, you can only write code on 1 line, it's just horrific
2. I'm really bad at using it

So, I did what every roblox dev has done at least 4 billion times: I looked up my problem on google.

![Google search: how to run code through a textbox roblox](https://raw.githubusercontent.com/GoobisMoobis/GoobisBlog/refs/heads/my-pages/images/loadstringrobloxsearch.png)

> Answer to the devforum post ↓

[![Devforum post answer: use loadstring](https://raw.githubusercontent.com/GoobisMoobis/GoobisBlog/refs/heads/my-pages/images/loadstringlookupanswerdevforum.png)](https://devforum.roblox.com/t/how-to-i-run-text-from-a-textbox-as-a-script/1258120/5#:~:text=Reply-,MrMouse2405,to%20use%20loadstring()%20function.%20Check%20this%20out%3A%20How%20to%20use%20loadstrings%3F,-399)

loadstring my beloved.

There were quite a few problems with loadstring, however.

1. It would make us vulnerable to exploits because of the remoteevents we'd need to use, meaning anyone with a script executer could literally run CODE on our game
2. It couldn't be used on the client

I'll come back to these later. However, for now, **loadstring it was!**

After learning about loadstring and doing everything I needed to do to use it, I put together a small gui and added this server-sided code:

```lua
game.ReplicatedStorage.Remotes.RunCode.OnServerEvent:Connect(function(plr, text)
  if table.find(require(game.ReplicatedStorage.Admins, plr.UserId) then
    loadstring(text)()
  end
end)
```

However, if someone typed in code that didn't work and errored, EVERY OTHER server-sided thing I wanted the terminal to do would halt. So, I wrapped it in a pcall.

```lua
local Success, Error = pcall(function()
  loadstring(text)()
end)

if Success then
  print("Code ran successfully")
else
  assert(false, Error)
end
```

What was the point of the pcall? Like, what would've been halted if the loadstring failed?

Our anticheat for the terminal.

You see, I knew when starting this project that exploiting would be a problem. So, I added an exploitation reporting system. If our system detected an exploiter, it would kick them and report them straight to us.

And now, the prototype of the terminal was done! It had no syntax highlighting, but had multi-line, and code execution!

Eventually, WhiteTurtlr caught on to what I was doing. He found (and I still don't know where) a syntax module.

I unfortunately do not know the original coding of the syntax module, and WhiteTurtle doesn't remember where he found it. However, here is it's current version:

```lua
local highlighter = {}

-- Define keywords for different categories
local keywords = {
	lua = {
		-- Lua keywords
		"and", "break", "or", "else", "elseif", "if", "then", "until", "repeat", "while", "do", "for", "in", "end",
		"local", "return", "function", "export", "type", "self", "not"
	},
	rbx = {
		-- Roblox-specific keywords and global functions
		"game", "workspace", "script", "math", "string", "table", "task", "wait", "select", "next", "Enum",
		"error", "warn", "tick", "assert", "shared", "loadstring", "tonumber", "tostring", "type",
		"typeof", "unpack", "print", "Instance", "CFrame", "Vector3", "Vector2", "Color3", "UDim", "UDim2", "Ray", "BrickColor",
		"OverlapParams", "RaycastParams", "Axes", "Random", "Region3", "Rect", "TweenInfo",
		"collectgarbage", "not", "utf8", "pcall", "xpcall", "_G", "setmetatable", "getmetatable", "os", "pairs", "ipairs", "require"
	},
	operators = {
		-- Operators and punctuation
		"#", "+", "-", "*", "%", "/", "^", "=", "~", "=", "<", ">", ",", ".", "(", ")", "{", "}", "[", "]", ";", ":"
	};
	SpecialSyntax = {
		"--Client"
	};
}

-- Define colors for different syntax elements
local colors = {
	numbers = Color3.fromRGB(255, 198 ,22),
	boolean = Color3.fromRGB(255, 198, 22),
	lua = Color3.fromRGB(248, 99, 94),
	rbx = Color3.fromRGB(117, 214, 216),
	str = Color3.fromRGB(105, 209, 105),
	comment = Color3.fromRGB(102, 102, 102),
	call = Color3.fromRGB(253 ,251, 154),
	local_property = Color3.fromRGB(97, 161, 241),
	SpecialSyntax = Color3.fromRGB(104, 241, 0)
}

-- Create sets of keywords for faster lookup
local function createKeywordSet(keywords)
	local keywordSet = {}
	for _, keyword in ipairs(keywords) do
		keywordSet[keyword] = true
	end
	return keywordSet
end

local luaSet = createKeywordSet(keywords.lua)
local rbxSet = createKeywordSet(keywords.rbx)
local operatorsSet = createKeywordSet(keywords.operators)
local SpecialSyntax = createKeywordSet(keywords.SpecialSyntax)

-- Determine the highlight color for a given token
local function getHighlight(tokens, index)
	local token = tokens[index]

	if colors[token .. "_color"] then
		return colors[token .. "_color"]
	end

	if token:sub(1, 8) == "--Client" then
		return colors.SpecialSyntax
	elseif string.sub(token, 1, 2) == "--" then
		return colors.comment
	elseif luaSet[token] then
		return colors.lua
	elseif rbxSet[token] then
		return colors.rbx
	elseif token:sub(1, 1) == "\"" or token:sub(1, 1) == "\'" or token:sub(1, 2) == "\[[" or token:sub(1, 3) == "\[=[" then
		return colors.str
	elseif token == "true" or token == "false" or token == "nil" then
		return colors.boolean
	elseif tonumber(token) then
		return colors.numbers
	end

	if tokens[index + 1] == "(" then
		if table.find(keywords.rbx, tokens[index - 2]) and tokens[index - 1] ~= ":" then
			return colors.rbx
		end
		return colors.call
	end

	if tokens[index - 1] == "." then
		if tokens[index - 2] == "Enum" then
			return colors.rbx
		end

		return colors.local_property
	end
end

-- Main function to run the syntax highlighting
function highlighter.run(source)
	local tokens = {}
	local currentToken = ""

	local inString = false
	local stringPersist = false
	local inComment = false
	local isClient = false
	local commentPersist = false
	local StringPersistType = ""

	-- Tokenize the source code
	for i = 1, #source do
		local character = source:sub(i, i)

		if inComment then
			-- Handle comments
			if character == "\n" and not commentPersist then
				table.insert(tokens, currentToken)
				table.insert(tokens, character)
				currentToken = ""

				inComment = false
			elseif source:sub(i - 1, i) == "]]" and commentPersist then
				currentToken ..= "]"

				table.insert(tokens, currentToken)
				currentToken = ""

				inComment = false
				commentPersist = false
			else
				currentToken = currentToken .. character
			end
		elseif inString then
			local function EndPersistingString(Type)
				
				currentToken ..= "]"

				table.insert(tokens, currentToken)
				currentToken = ""

				inString = false
				stringPersist = false
			end
			-- Handle strings
			if character == inString and source:sub(i-1, i-1) ~= "\\" or character == "\n" and not stringPersist then
				table.insert(tokens, currentToken)
				table.insert(tokens, character)
				currentToken = ""

				inString = false
			elseif source:sub(i - 1, i) == "]]" or source:sub(i-2, i) == "]=]" and stringPersist then
		--		if source:sub(i, i+1) == "[[" and source:sub(i-2, i) ~= "]=]" or source:sub(i, i+2) == "[=[" and source:sub(i-1, i) ~= "[[" then
				if StringPersistType == "[[" and source:sub(i - 1, i) == "]]" then
					EndPersistingString(StringPersistType)
				elseif StringPersistType == "[=[" and source:sub(i-2, i) == "]=]" then
					EndPersistingString(StringPersistType)
				else
					currentToken = currentToken .. character
				end
			else
				currentToken = currentToken .. character
			end
		elseif isClient then
			if character == "\n" then
				table.insert(tokens, currentToken)
				table.insert(tokens, character)
				currentToken = ""

				isClient = false
			else
				currentToken = currentToken .. character
			end
		else
			-- Handle other tokens
			if source:sub(i, i + 1) == "--" then
				table.insert(tokens, currentToken)
				currentToken = "-"
				inComment = true
				commentPersist = source:sub(i + 2, i + 3) == "[["
				isClient = source:sub(i + 2, i + 7) == "Client"
				if isClient then
					inComment = false
				end
			elseif source:sub(i, i+1) == "[[" or source:sub(i, i+2) == "[=[" then
				if source:sub(i, i+1) == "[[" then
					StringPersistType = "[["
				else
					StringPersistType = "[=["
				end
				currentToken = "["
				inString = true
				stringPersist = true
				
			elseif character == "\"" or character == "\'" then
				table.insert(tokens, currentToken)
				currentToken = character
				inString = character
			elseif operatorsSet[character] then
				table.insert(tokens, currentToken)
				table.insert(tokens, character)
				currentToken = ""
			elseif character:match("[%w_]") then
				currentToken = currentToken .. character
			else
				table.insert(tokens, currentToken)
				table.insert(tokens, character)
				currentToken = ""
			end
		end
	end

	table.insert(tokens, currentToken)

	local highlighted = {}

	-- Apply highlighting to tokens
	for i, token in ipairs(tokens) do
		local highlight = getHighlight(tokens, i)

		if highlight then
			local syntax = string.format("<font color = \"#%s\">%s</font>", highlight:ToHex(), token:gsub("<", "&lt;"):gsub(">", "&gt;"))
			if token == "true" or token == "false" or token == "nil" or luaSet[token] then
				syntax = `<b>{syntax}</b>`
			end

			table.insert(highlighted, syntax)
		else
			table.insert(highlighted, token)
		end
	end

	return table.concat(highlighted)
end

return highlighter
```

> Ignore special syntax. I'll talk about that more later.


All of a sudden, our terminal was looking snazzy!

After adding this script to our scrolling frame (which contains our textboxes):

```lua
local textArea = script.Parent:WaitForChild("TextBox")
local scrollFrame = script.Parent

textArea.AutomaticSize = "XY"

textArea:GetPropertyChangedSignal("TextBounds"):Connect(function()
	local requiredWidth = textArea.TextBounds.X + 16
	local requiredHeight = textArea.TextBounds.Y + 16

	scrollFrame.CanvasSize = UDim2.new(0, requiredWidth, 0, requiredHeight)

	local newXPosition = math.max(0, requiredWidth - scrollFrame.AbsoluteWindowSize.X)
	local newYPosition = math.max(0, requiredHeight - scrollFrame.AbsoluteWindowSize.Y)

	scrollFrame.CanvasPosition = Vector2.new(newXPosition, newYPosition)
end)

textArea:GetPropertyChangedSignal("Text"):Connect(function()
	local requiredWidth = textArea.TextBounds.X + 16
	local requiredHeight = textArea.TextBounds.Y + 16

	scrollFrame.CanvasSize = UDim2.new(0, requiredWidth, 0, requiredHeight)

	local newXPosition = math.max(0, requiredWidth - scrollFrame.AbsoluteWindowSize.X)
	local newYPosition = math.max(0, requiredHeight - scrollFrame.AbsoluteWindowSize.Y)

	scrollFrame.CanvasPosition = Vector2.new(newXPosition, newYPosition)
end)
```

now everything was almost perfect! Formatting code worked, long scripts looked good (with the script above), everything was chef's kiss.

Everything... except client-sided code execution.

So, again, I looked it up.

[![Devforum answer says to use vLua](https://raw.githubusercontent.com/GoobisMoobis/GoobisBlog/refs/heads/my-pages/images/devforumvluaasanswer.png)](https://devforum.roblox.com/t/use-loadstring-on-client/886112#:~:text=Nov%20%2720-,You%20can%20use%20this%20module.%20vLua%3A%20Loadstring%20reimplemented%20in%20Lua%20%2D%20Roblox,-387)

Boom! All I needed now was to use vLua in a client-sided script and add a remote (and an anticheat, again)!

Until recently, this is how the terminal was, besides me adding premade scripts after I figured out client-sided stuff.

And then I quit MRTS.

And then I forgot about the terminal.

Until recently, when i remembered I had the terminal. I imported it into URTG, but my god was it bad.

Number one, vLua only supported lua. Not luau.

Number two is that any time I wanted to run client-sided code, I had to require lua and send a remote to the client with the code I wanted to run.

So ineficient.

But I kept it that way. **[Until I found this.](https://devforum.roblox.com/t/live-game-explorerv33/2693615)**

I was looking for an in-game file explorer, and I found one! Yippie!

It had it's own console, it's own property editor,

and it's own command bar.

I already had the terminal, which was much better than a stinky command line, so I deleted the command line.

However I saw that instead of using vLua, which was what I was expecting it to use, it was using vLuau.

So, I looked it up, saw it supported not JUST lua, but ALSO LUAU, and replaced vLua (and my server-sided loadstring) with vLuau.

## And now, my terminal is better than anything i've ever seen!

After finding vLuau it gave me a burst of inspiration to make client-sided code execution better. Now, if you type `--Client (username)` as the first line in the terminal, it runs code on that person's client!

All of a sudden I was getting comments from not only Phoenix, but also all my other friends who are admins for Yimello who were saying this was a cool system. They were reccomending premade scripts, etc.

I will probably not ever open-source the terminal. There's too much to loose if someone finds a way to exploit the code.

However, I'm not evil, so feel free to use the snippets I've provided and the advice I've found online to make your own!

Just please remember to add an anticheat.

One other thing I will share with you guys because I know a lot of you hate RegEx as much as me, is how to add the detector for the `--Client` stuff.

```lua
local lines = string.split(text, "\n")
local firstLine = lines[1]

local player = nil
local splitString = string.split(firstLine, " ")


if splitString[1] == "--Client" and splitString[2] then
	local username = splitString[2]
	username = string.gsub(username, "^%s*(.-)%s*$", "%1")

	player = game.Players:FindFirstChild(username)

	if player then
		table.remove(lines, 1)
		text = table.concat(lines, "\n")
	else
		text = "assert(false, 'Client \"" .. username .. "\" does not exist')"
	end
end
```

What this does is detect if I've added a `--Client (username)` line at the beggining of my terminal code execution, and if so, it will set a variable, player, to their username. if the player I've attempted to run code on doesn't exist, it sets `text` to that, as `text` will get ran by the loadstring. The line after this checks if player is nil, and if it is, it runs the vLuau loadstring on the server. otherwise, it runs it on the client that the `player` variable is set to.

Anyways, this has been my admin terminal that I created from scratch. Thanks to WhiteTurtle for finding the syntax module, the devs of vLuau, that random guy from 2020 who made a devforum post, and everyone else who led me to make this super awesome terminal.

<hr>

goober

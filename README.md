# Welcome to the Arweave India GRID

Here is a list of all the commands you will need to participate in our GRID challenge 👇

# Basic Setup

## What is the grid?

The grid is 16x16 arena where players are spawned at the random spot. The grid coordinates the player moves, attacks, energy and health, eliminations, etc.

## How to join the grid?

You need to pay some tokens to join the grid. So obviously you need to request some tokens first. We have setup a dummy token for this practice.

First you will need to create a participant process. For this you need the aos package installed:

```bash
npm i -g https://get_ao.g8way.io
```

After the package installed, create a new process by simply running the command `aos` or as follows if you want to assign a name to your process:

```bash
aos gamer_name
```

Then set the Game ID as a variable as we will be communicating with it often and we do not want to keep typing in long IDs each time:

```lua
Game = "yw9u7AaMvLEvbRSbMd91rx-9UHLTJMq6uOX0aHxhdis"
```

Use the following command to request tokens from the Game:

```lua
Send({ Target = Game, Action = "RequestTokens" })
```

You should get a `Credit-Notice` indicating a receipt of tokens. This message is received in your `Inbox`. You can check your latest message in the inbox by running the following:

```lua
Inbox[#Inbox]
```

> Note: These tokens are only claimable once for this exercise.

You can also check your balance with the following command:

```lua
Send({ Target = Game, Action = "Balance" })
```

> This is a general command to check your balance for any AO token. Simply change the `Target` address.

Now you must pay tokens to the game to join the grid:

```lua
Send({Target = Game, Action = "Transfer", Quantity = "1000", Recipient = Game})
```

> Note: You need to send all your 1000 tokens to get full health. 

You will get a message saying `Payment-Received`. If you check its data as follows it will tell you "you are in the game":

```lua
Inbox[#Inbox].Data
```

## How to play?

The game is straightforward. There are only two options. You either move or your attack. In order to move you can send the following command:

```lua
Send({Target = Game, Action = "PlayerMove", Direction = "UpRight"})
```

> Note: There are 8 directions you can move in: `Up`, `Down`, `Left`, `Right`, `UpRight`, `UpLeft`, `DownRight`, `DownLeft`.

But how do you know you have moved? You can keep fetching the game state to know the status of your player as well as other players. This includes their co-ordinates in the grid, their health and energy:

```lua
Send({Target = Game, Action = "GetGameState"})
```

Each time you move, your energy is replenished. This energy is used for attacking. The game logic takes the energy you supply to an attack, performs some random math and calculates the damage. This damage is inflicted upon any opponent player within a 5x5 radius with your player at the center. The more energy you supply the greate the damage.

For attacking you use the following command:

```lua
Send({Target = Game, Action = "PlayerAttack", AttackEnergy = "30"})
```

You must send an attack energy between 10 to 100 for your attack to work. Further a player must be in range, else you energy is wasted. The more energy used the stronger the attack.

## How to automate this?

We have a bot that you can use to automate this! It can be tedious to keep typing new moves manually so you can automate the bot as follows:

```bash
curl -o ao-bot.lua https://zaeq3x6evc4bo7quicf5uktkjou4pjtlti4mb2rgr3l2aymjugzq.arweave.net/yAkN38SouBd-FECL2ipqS6nHpmuaOMDqJo7XoGGJobM
```

> Note: You can either open a new terminal within the same directory as the aos process you have created or exit that process with `Cmd+C` and then run this command. You can then reconnect with your aos process if you had exited it by running the command `aos` or `aos gamer_name` (depending on what you named the process).

If the `curl` command does not work on your device, simply head over to this [link](https://zaeq3x6evc4bo7quicf5uktkjou4pjtlti4mb2rgr3l2aymjugzq.arweave.net/yAkN38SouBd-FECL2ipqS6nHpmuaOMDqJo7XoGGJobM), copy the code and paste it in a new file named `ao-bot.lua` in the same directory as your aos process.

Now once you reconnect with the process of mid-game you decide to modify the bot code and want to load the new code, you can do so as follows within your aos terminal:

```lua
.load ao-bot.lua
```

And then the first time you load this logic, kick start the bot:

```lua
Send({ Target = ao.id, Action = "Tick" })
```

> Note: While modifying the code mid-game, the "Tick" command is not needed if you have already used this in your process the first time.

## Want to see your game in a UI?

```
https://grid_ao.g8way.io/?id=yw9u7AaMvLEvbRSbMd91rx-9UHLTJMq6uOX0aHxhdis
```

## How does the bot operate?

### Core Functions and Handlers:

- **Global Variables Initialization:** Sets up variables to store the game state, the game process reference, a counter for actions, and a logging system. The colors table is used for colored terminal output, enhancing readability.

- **inRange Function:** Determines if another player is within attack range. This is crucial for deciding whether to attack or move.

- **decideNextAction Function:** The bot's primary decision-making function. It checks if any player is within attack range and decides to either attack (if the bot has more than 10 energy) or move randomly. This strategy balances aggression with energy management.

- **Event Handlers:** Respond to various game events, such as announcements and game state updates, to inform the bot's next actions.

- **PrintAnnouncements:** Prints important game announcements and requests the latest game state for certain events.
    - **GetGameStateOnTick:** Requests the current game state at every tick, keeping the bot's knowledge updated.
    - **UpdateGameState:** Updates the bot's local representation of the game state upon receiving new data.
    - **decideNextAction:** Triggers the decision-making process based on the updated game state.
    - **ReturnAttack:** Responds to being hit by another player. The bot attempts to move randomly if it cannot return the attack due to low energy.

### Strategy and Behavior:

- The bot's strategy is relatively simple but effective for starting out. It focuses on energy conservation and opportunistic attacks. When not in a position to attack, it moves randomly, potentially avoiding predictable patterns that could make it an easy target.

- The decision to move randomly when unable to attack due to low energy or no players within range is a basic survival tactic, preventing the bot from being stationary and an easy target.

- The bot's reaction to being hit (ReturnAttack handler) prioritizes energy assessment before deciding its next move, showcasing a rudimentary form of self-preservation.

### Suggested Improvements:

- **Enhanced Decision Making:** Implement more sophisticated logic for movement and attacking, considering factors like the health and energy of nearby opponents, distance to the nearest opponent, and the bot's current position on the grid.

- **Energy Management:** Develop a more nuanced energy management strategy, such as saving energy for critical moments or ensuring enough energy is always available for defense.

- **Adaptive Behavior:** Introduce patterns or behaviors that change based on the game's progression or the bot's success in previous rounds, allowing it to adapt to opponents' strategies.

This bot template offers a solid starting point for players interested in participating in the game with their bots. By understanding the basic logic and structure, developers can extend and customize their bots with more advanced strategies and behaviors to compete effectively in the arena.

### Game and Sample Bot Logic

#### GRID CODE

```lua
Version = "0.3"

-- Attack info
LastPlayerAttacks = LastPlayerAttacks or {}
CurrentAttacks = CurrentAttacks or 0
TenSecondCheck = TenSecondCheck or 0

-- grid dimensions
Width = 16
Height = 16
Range = 3

-- Player energy
MaxEnergy = 100
EnergyPerSec = 1
-- Attack settings
AverageMaxStrengthHitsToKill = 3 -- Average number of hits to eliminate a player

-- Initializes default player state
-- @return Table representing player's initial state
function playerInitState()
    return {
        x = math.random(0, Width),
        y = math.random(0, Height),
        health = 100,
        energy = 0,
        lastTurn = 0
    }
end

-- Function to incrementally increase player's energy
-- Called periodically to update player energy
function onTick()
    if GameMode ~= "Playing" then return end  -- Only active during "Playing" state

    if LastTick == undefined then LastTick = Now end

    local Elapsed = Now - LastTick
    if Elapsed >= 1000 then  -- Actions performed every second
        for player, state in pairs(Players) do
            local newEnergy = math.floor(math.min(MaxEnergy, state.energy + (Elapsed * EnergyPerSec // 2000)))
            state.energy = newEnergy
        end
        LastTick = Now
    end

    if TenSecondCheck == 0 then 
        TenSecondCheck = Now 
    end

    local TenSecondElaspedCheck = Now - TenSecondCheck
    if TenSecondElaspedCheck >= 10000 then
        -- only keep the last 20
        while #LastPlayerAttacks > 20 do
            table.remove(LastPlayerAttacks, 1)
        end
        TenSecondCheck = Now
    end
    

end

local function isOccupied(x,y) 
  local result = false
  for k,v in pairs(Players) do
    if v.x == x and v.y == y then
        result = true
        return
    end
  end
end

-- Handles player movement
-- @param msg: Message request sent by player with movement direction and player info
function move(msg)
    local playerToMove = msg.From
    local direction = msg.Tags.Direction

    local directionMap = {
        Up = {x = 0, y = -1}, Down = {x = 0, y = 1},
        Left = {x = -1, y = 0}, Right = {x = 1, y = 0},
        UpRight = {x = 1, y = -1}, UpLeft = {x = -1, y = -1},
        DownRight = {x = 1, y = 1}, DownLeft = {x = -1, y = 1}
    }

    -- only 1 turn per second
    if msg.Timestamp <  Players[playerToMove].lastTurn + 1000 then
        return
    end
    -- calculate and update new coordinates
    if directionMap[direction] then
        local newX = Players[playerToMove].x + directionMap[direction].x
        local newY = Players[playerToMove].y + directionMap[direction].y

        -- Player cant move to cell already occupied.
        if isOccupied(newX, newY) then
            Send({Target = playerToMove, Action = "Move-Failed", Reason = "Cell Occupied."})
            return 
        end

        -- updates player coordinates while checking for grid boundaries
        Players[playerToMove].x = (newX - 1) % Width + 1
        Players[playerToMove].y = (newY - 1) % Height + 1

        Send({Target = playerToMove, Action="Player-Moved", Data = playerToMove .. " moved to " .. Players[playerToMove].x .. "," .. Players[playerToMove].y .. "."})
        --announce("Player-Moved", playerToMove .. " moved to " .. Players[playerToMove].x .. "," .. Players[playerToMove].y .. ".")
    else
        Send({Target = playerToMove, Action = "Move-Failed", Reason = "Invalid direction."})
    end
    Players[playerToMove].lastTurn = msg.Timestamp
    onTick()  -- Optional: Update energy each move
end

-- Handles player attacks
-- @param msg: Message request sent by player with attack info and player state
function attack(msg)
    local player = msg.From
    local attackEnergy = tonumber(msg.Tags.AttackEnergy) < 0 and 0 or tonumber(msg.Tags.AttackEnergy)

    -- get player coordinates
    local x = Players[player].x
    local y = Players[player].y

    -- only 1 turn per second
    if msg.Timestamp <  Players[player].lastTurn + 1000 then
        return
    end
    
    -- check if player has enough energy to attack
    if Players[player].energy < attackEnergy then
        ao.send({Target = player, Action = "Attack-Failed", Reason = "Not enough energy."})
        return
    end

    -- update player energy and calculate damage
    Players[player].energy = Players[player].energy - attackEnergy
    local damage = math.floor((math.random() * 2 * attackEnergy) * (1/AverageMaxStrengthHitsToKill))

    --announce("Attack", player .. " has launched a " .. damage .. " damage attack from " .. x .. "," .. y .. "!")   
    -- check if any player is within range and update their status
    for target, state in pairs(Players) do
        if target ~= player and inRange(x, y, state.x, state.y, Range) then
            local newHealth = state.health - damage
            -- Document Current Attacks
            CurrentAttacks = CurrentAttacks + 1
            LastPlayerAttacks[CurrentAttacks] = {
                Player = player,
                Target = target,
                id = CurrentAttacks
            }
            if newHealth <= 0 then
                eliminatePlayer(target, player)
            else
                Players[target].health = newHealth
                Send({Target = target, Action = "Hit", Damage = tostring(damage), Health = tostring(newHealth)})
                Send({Target = player, Action = "Successful-Hit", Recipient = target, Damage = tostring(damage), Health = tostring(newHealth)})
            end
        end
    end
    Players[player].lastTurn = msg.Timestamp
end

-- Helper function to check if a target is within range
-- @param x1, y1: Coordinates of the attacker
-- @param x2, y2: Coordinates of the potential target
-- @param range: Attack range
-- @return Boolean indicating if the target is within range
function inRange(x1, y1, x2, y2, range)
    return x2 >= (x1 - range) and x2 <= (x1 + range) and y2 >= (y1 - range) and y2 <= (y1 + range)
end

-- HANDLERS: Game state management for AO-Effect

-- Handler for player movement
Handlers.add("PlayerMove", Handlers.utils.hasMatchingTag("Action", "PlayerMove"), move)

-- Handler for player attacks
Handlers.add("PlayerAttack", Handlers.utils.hasMatchingTag("Action", "PlayerAttack"), attack)

-- ARENA GAME BLUEPRINT.

-- REQUIREMENTS: cron must be added and activated for game operation.

-- This blueprint provides the framework to operate an 'arena' style game
-- inside an ao process. Games are played in rounds, where players aim to
-- eliminate one another until only one remains, or until the game time
-- has elapsed. The game process will play rounds indefinitely as players join
-- and leave.

-- When a player eliminates another, they receive the eliminated player's deposit token
-- as a reward. Additionally, the builder can provide a bonus of these tokens
-- to be distributed per round as an additional incentive. If the intended
-- player type in the game is a bot, providing an additional 'bonus'
-- creates an opportunity for coders to 'mine' the process's
-- tokens by competing to produce the best agent.

-- The builder can also provide other handlers that allow players to perform
-- actions in the game, calling 'eliminatePlayer()' at the appropriate moment
-- in their game logic to control the framework.

-- Processes can also register in a 'Listen' mode, where they will receive
-- all announcements from the game, but are not considered for entry into the
-- rounds themselves. They are also not unregistered unless they explicitly ask
-- to be.

-- GLOBAL VARIABLES.

-- Game progression modes in a loop:
-- [Not-Started] -> Waiting -> Playing -> [Someone wins or timeout] -> Waiting...
-- The loop is broken if there are not enough players to start a game after the waiting state.
GameMode = GameMode or "Playing"
StateChangeTime = StateChangeTime or 0

-- State durations (in milliseconds)
WaitTime = WaitTime or 2 * 60 * 1000 -- 2 minutes
GameTime = GameTime or 20 * 60 * 1000 -- 20 minutes
Now = Now or 0 -- Current time, updated on every message.

-- Token information for player stakes.
PaymentToken = PaymentToken or ao.id  -- Token address
PaymentQty = PaymentQty or 1           -- Quantity of tokens for registration
BonusQty = BonusQty or 1               -- Bonus token quantity for winners

-- Players waiting to join the next game and their payment status.
Waiting = Waiting or {}
-- Active players and their game states.
Players = Players or {}
-- Number of winners in the current game.
Winners = 0
-- Processes subscribed to game announcements.
Listeners = Listeners or {}
-- Minimum number of players required to start a game.
MinimumPlayers = MinimumPlayers or 1

-- Default player state initialization.
PlayerInitState = PlayerInitState or {}


-- Sends a state change announcement to all registered listeners.
-- @param event: The event type or name.
-- @param description: Description of the event.
function announce(event, description)
    for ix, address in pairs(Listeners) do
        ao.send({
            Target = address,
            Action = "Announcement",
            Event = event,
            Data = description
        })
    end
end

-- Sends a reward to a player.
-- @param recipient: The player receiving the reward.
-- @param qty: The quantity of the reward.
-- @param reason: The reason for the reward.
function sendReward(recipient, qty, reason)
    ao.send({
        Target = PaymentToken,
        Action = "Transfer",
        Quantity = tostring(qty),
        Recipient = recipient,
        Reason = reason
    })
end

-- Removes a listener from the listeners' list.
-- @param listener: The listener to be removed.
function removeListener(listener)
    local idx = 0
    for i, v in ipairs(Listeners) do
        if v == listener then
            idx = i
            -- addLog("removeListener", "Found listener: " .. listener .. " at index: " .. idx) -- Useful for tracking listener removal
            break
        end
    end
    if idx > 0 then
        -- addLog("removeListener", "Removing listener: " .. listener .. " at index: " .. idx) -- Useful for tracking listener removal
        table.remove(Listeners, idx)
    end 
end

-- Handles the elimination of a player from the game.
-- @param eliminated: The player to be eliminated.
-- @param eliminator: The player causing the elimination.
function eliminatePlayer(eliminated, eliminator)

    sendReward(eliminator, tonumber(GameBalances[eliminated]), "Eliminated-Player")
    GameBalances[eliminated] = "0"
    Players[eliminated] = nil

    Send({
        Target = eliminated,
        Action = "Eliminated",
        Eliminator = eliminator
    })
    removeListener(eliminated)
    -- announce("Player-Eliminated", eliminated .. " was eliminated by " .. eliminator .. "!")
    
    local playerCount = 0
    for player, _ in pairs(Players) do
        playerCount = playerCount + 1
    end
end

function scaleNumber(oldValue)
    local oldMin = 10
    local oldMax = 1000
    local newMin = 1
    local newMax = 100

    local newValue = (((oldValue - oldMin) * (newMax - newMin)) / (oldMax - oldMin)) + newMin
    return newValue
end



-- HANDLERS: Game state management

-- Handler for cron messages, manages game state transitions.
Handlers.add(
    "Game-State-Timers",
    function(Msg)
        return "continue"
    end,
    function(Msg)
        Now = tonumber(Msg.Timestamp)
        onTick()
    end
)

-- Handler for player deposits to participate in the next game.
Handlers.add(
    "Transfer",
    function(Msg)
        return
            Msg.Action == "Credit-Notice" and
            -- Msg.From == CRED and
            tonumber(Msg.Quantity) >= PaymentQty and "continue"
    end,
    function(Msg)
        print("Player " .. Msg.Sender .. " has deposited " .. Msg.Quantity .. " " .. PaymentToken .. " to join the game.")
        local q = tonumber(Msg.Quantity)
        
        if not GameBalances[Msg.Sender] then
            GameBalances[Msg.Sender] = "0"
        end

        local balance = tonumber(GameBalances[Msg.Sender])
        Players[Msg.Sender] = playerInitState()
        
        balance = math.floor(balance + q)
        GameBalances[Msg.Sender] = tostring(balance)
        if balance <= 10 then
            Players[Msg.Sender].health = 1
        elseif balance >= 1000 then
            Players[Msg.Sender].health = 100
        else
            Players[Msg.Sender].health = math.floor(scaleNumber(balance))
        end
        Send({
            Target = Msg.Sender,
            Action = "Payment-Received",
            Data = "You are in the game."
        })
        
    end
)

-- Exits the game receives CRED
Handlers.add(
    "Withdraw",
    Handlers.utils.hasMatchingTag("Action", "Withdraw"),
    function(Msg)
        Players[Msg.From] = nil
        Send({Target = Game, Action = "Transfer", Quantity = GameBalances[Msg.From], Recipient = Msg.From })
        GameBalances[Msg.From] = "0"
        removeListener(Msg.From)
        Send({
            Target = Msg.From,
            Action = "Removed from the Game"
        })
    end
)


-- Retrieves the current game state.
Handlers.add(
    "GetGameState",
    Handlers.utils.hasMatchingTag("Action", "GetGameState"),
    function (Msg)
        if Players[Msg.From] and Msg.Name then
            Players[Msg.From].name = Msg.Name
        end
        local json = require("json")
        local GameState = json.encode({
            GameMode = GameMode,
            Players = Players,
        })
        Send({
            Target = Msg.From,
            Action = "GameState",
            Data = GameState
        })
    end
)

-- Retrieves the current attacks that has been made in the game.
Handlers.add(
    "GetGameAttacksInfo",
    Handlers.utils.hasMatchingTag("Action", "GetGameAttacksInfo"),
    function (Msg)
        local GameAttacksInfo = require("json").encode({
            LastPlayerAttacks = Utils.values(LastPlayerAttacks)
        })
        Send({
            Target = Msg.From,
            Action = "GameAttacksInfo",
            Data = GameAttacksInfo
        })
    end
)
```

#### BOT CODE

```lua
-- Initializing global variables to store the latest game state and game host process.
LatestGameState = LatestGameState or nil
Counter = Counter or 0

colors = {
  red = "\27[31m",
  green = "\27[32m",
  blue = "\27[34m",
  reset = "\27[0m",
  gray = "\27[90m"
}

-- Checks if two points are within a given range.
-- @param x1, y1: Coordinates of the first point.
-- @param x2, y2: Coordinates of the second point.
-- @param range: The maximum allowed distance between the points.
-- @return: Boolean indicating if the points are within the specified range.
function inRange(x1, y1, x2, y2, range)
    return math.abs(x1 - x2) <= range and math.abs(y1 - y2) <= range
end

-- Decides the next action based on player proximity and energy.
-- If any player is within range, it initiates an attack; otherwise, moves randomly.
function decideNextAction()
  local player = LatestGameState.Players[ao.id]
  local targetInRange = false

  for target, state in pairs(LatestGameState.Players) do
      if target ~= ao.id and inRange(player.x, player.y, state.x, state.y, 1) then
          targetInRange = true
          break
      end
  end

  if player.energy > 10 and targetInRange then
    print(colors.red .. "Player in range. Attacking..." .. colors.reset)
    ao.send({Target = Game, Action = "PlayerAttack", AttackEnergy = tostring(player.energy)})
  else
    -- print(colors.red .. "No player in range or insufficient energy. Moving randomly." .. colors.reset)
    local directionMap = {"Up", "Down", "Left", "Right", "UpRight", "UpLeft", "DownRight", "DownLeft"}
    local randomIndex = math.random(#directionMap)
    ao.send({Target = Game, Action = "PlayerMove", Direction = directionMap[randomIndex]})
  end
end

-- Handler to print game announcements and trigger game state updates.
Handlers.add(
  "PrintAnnouncements",
  Handlers.utils.hasMatchingTag("Action", "Announcement"),
  function (msg)
    ao.send({Target = Game, Action = "GetGameState"})
    print(colors.green .. msg.Event .. ": " .. msg.Data .. colors.reset)
    print("Location: " .. "row: " .. LatestGameState.Players[ao.id].x .. ' col: ' .. LatestGameState.Players[ao.id].y)

  end
)

-- Handler to trigger game state updates.
Handlers.add(
  "GetGameStateOnTick",
  Handlers.utils.hasMatchingTag("Action", "Tick"),
  function ()
      -- print(colors.gray .. "Getting game state..." .. colors.reset)
      ao.send({Target = Game, Action = "GetGameState"})
  end
)

-- Handler to update the game state upon receiving game state information.
Handlers.add(
  "UpdateGameState",
  Handlers.utils.hasMatchingTag("Action", "GameState"),
  function (msg)
    local json = require("json")
    LatestGameState = json.decode(msg.Data)
    ao.send({Target = ao.id, Action = "UpdatedGameState"})
    --print("Game state updated. Print \'LatestGameState\' for detailed view.")
    print("Location: " .. "row: " .. LatestGameState.Players[ao.id].x .. ' col: ' .. LatestGameState.Players[ao.id].y)
  end
)

-- Handler to decide the next best action.
Handlers.add(
  "decideNextAction",
  Handlers.utils.hasMatchingTag("Action", "UpdatedGameState"),
  function ()
    --print("Deciding next action...")
    decideNextAction()
    ao.send({Target = ao.id, Action = "Tick"})
  end
)

-- Handler to automatically attack when hit by another player.
Handlers.add(
  "ReturnAttack",
  Handlers.utils.hasMatchingTag("Action", "Hit"),
  function (msg)
    local playerEnergy = LatestGameState.Players[ao.id].energy
    if playerEnergy == undefined then
      print(colors.red .. "Unable to read energy." .. colors.reset)
      ao.send({Target = Game, Action = "Attack-Failed", Reason = "Unable to read energy."})
    elseif playerEnergy > 10 then
      print(colors.red .. "Player has insufficient energy." .. colors.reset)
      ao.send({Target = Game, Action = "Attack-Failed", Reason = "Player has no energy."})
    else
      print(colors.red .. "Returning attack..." .. colors.reset)
      ao.send({Target = Game, Action = "PlayerAttack", AttackEnergy = tostring(playerEnergy)})
    end
    ao.send({Target = ao.id, Action = "Tick"})
  end
)

Handlers.add(
  "ReSpawn",
  Handlers.utils.hasMatchingTag("Action", "Eliminated"),
  function (msg)
    Send({Target = CRED, Action = "Transfer", Quantity = "1000", Recipient = Game})
  end
)

Handlers.add(
  "StartTick",
  Handlers.utils.hasMatchingTag("Action", "Payment-Received"),
  function (msg)
    Send({Target = Game, Action = "GetGameState", Name = Name, Owner = Owner })
    print('Start Moooooving!')
  end
)

Prompt = function () return Name .. "> " end
```

## May the best bot win!
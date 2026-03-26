# sigma 🍷🗿

![version](https://img.shields.io/badge/version-1.0.0-blue)
![luau](https://img.shields.io/badge/luau-strict-purple)

A lightweight, high-performance, and type-safe signal implementation for Luau. Sigma provides a modern and familiar API for creating event-driven code, ensuring reliability and performance without the overhead of built-in instances. *Used by over 10 million clients.*

## Quickstart

### Require the module (duh)
```luau
local Sigma = require('./path-to-module')
```
### Simple example
```luau
local MySignal = Sigma.New()

MySignal:Once(function (Something)
      print(Something)
end)

MySignal:Fire(123)
```

### Example
Here's how you could set up a voting system. We first create a Voting type that holds the signals we need. By using generics like `Sigma<Player, 'Blue' | 'Red'>`, we're just telling Luau what kind of data to expect for each signal. This lets the type checker know that the `Player` and `Vote` arguments in your connected function will have the correct types.

```luau
local Players = game:GetService('Players')

export type Voting = {
      PlayerVoted : Sigma<Player, 'Blue' | 'Red'>,
      VoteEnd     : Sigma<{Blue : number, Red : number}>
}

local MapVoting : Voting = {
      PlayerVoted = Sigma.New(),
      VoteEnd     = Sigma.New()
}

MapVoting.PlayerVoted:Connect(function (Player, Vote)
      print(`{Player.Name} has voted for {Vote}`)
end)

MapVoting.VoteEnd:Connect(function (Votes)
      print(`Blue got {Votes.Blue} & Red got {Votes.Red}`)
end)

Players.PlayerAdded:Connect(function (Player)
      
      MapVoting.PlayerVoted:Fire(Player, 'Red') -- We're forcing the player to vote as soon as they arrive
end)
```

## Signal Type (Sigma 🗿)
> I'm too lazy to write all of the methods and their params, so here they are
```luau
export type Sigma<Input...> = {
      Fire    : (self : any, Input...) -> (),
      
      Connect : (self : any, Output : (Input...) -> ()) -> Connection<(Input...) -> ()>,
      Once    : (self : any, Output : (Input...) -> ()) -> Connection<(Input...) -> ()>,
      Wait    : (self : any) -> Input...,
      
      __cache   : {[string] : Connection<Input...>},
      __waiting : { thread },
      
      Destroy : (self : any) -> ()
}

export type Connection<In...> = {
      Disconnect : (self : any) -> (),
      Function   : (In...) -> (),
      _uuid      : string,
      _once      : boolean
}

```

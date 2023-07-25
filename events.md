# Event Handling / Editor Script

This file sketches how to interact with event emitters in OSPGL, using the creation of a dummy editor script as an example.

## Setting up

Create a mod as explained in [getting_started](getting_started.md), and inside this mod create a lua file. We'll name it `dummy_script.lua`:

```lua
-- dummy_script.lua
-- This line improves code completion by giving the language server context about the script type
---@module "editor_script"
```

In order for OSPGL to load this file we need to add it to the game database. In order to do this, we need to use the entry point of our mod, `package.lua`:

```lua
-- package.lua
require("game_database")

function load(db)
  db:add_editor_script("dummy_script.lua")
end
```

Now, during load, OSPGL will add the script to the list of editor scripts, and once we enter the editor it will be loaded

## Event Emitters

Event emitters may be implemented in C++ (using the `EventEmitter` class), or in lua as simple member functions. 

### TODO: Write how to implement an event emitter in lua / write a util class 

## Event Handlers

Event handlers are nothing more than functions, and an associated object returned by the event emitter that, on destruction, removes the function from the event handler. While you can 
manually use the `events` lua library and manage event handlers yourself, it's simpler to use the helper class `core:util/c_events.lua`. This class
stores handlers in a table, so you don't have to worry about them being garbage-collected and removed before you intend to.

We'll create an event handler for the editor script events. It's very important that this instance is global, as will be explained later:

```lua
-- dummy_script.lua
events = dofile("core:util/c_events.lua)
```

Now we may implement functions to be called on the different events. We'll use some dummy functions that log some text to the console.

```lua
-- dummy_script.lua
local logger = require("logger")

local function fun1()
  logger.info("Hello from fun1")
end

local function fun2()
  logger.info("Hello from fun2")
end
```

And finally we'll add them as handlers to different events. Note that `editor` is a global for editor scripts!

```lua
-- syntax: events:add(emitter, event_name, handler)
events:add(editor, "on_gui_prepare", fun1)
events:add(editor, "post_editor_update", fun1)
events:add(editor, "post_gui_draw", function () logger.info("hello from anonymous!") end)

-- We may also have named handlers, if we are interested in removing them later
-- syntax: events:add_named(emitter, event_name, id, handler)
events:add_named(editor, "post_editor_update", "to_be_removed", fun2)
events:remove("to_be_removed")
```

Now it may become clear why `events` must be global. All functions we have defined are in the local scope, and as they are referenced only by the event handlers,
if events were local, everything would be garbage collected and the script would do nothing. By making `events` global, lua won't gargabe collect it.

### Removing handlers

All handlers will be removed once `events` is destroyed. This will happen once the editor is unloaded, but you can force it to happen by simply setting `events = nil`. 
You may also remove all handlers via `events:remove_all()`, or remove named handlers by `events:remove(id)`

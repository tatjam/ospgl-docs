# OSPGL Lua

OSPGL uses LuaJIT with Lua 5.2 compatibility enabled. The standard library is limited to "safe" functions, and some changes have been applied to some of them. 
Lets go over what's changed from "stock" lua.

## Environments

The OSPGL engine will always spawn scripts in their own environment, so globals can be used. This allows, for example, many machines having the `update` method as a global, while no name conflicts arise. Despite this, all environments are actually running in the same lua state, so you can freely pass variables between machines, scenes, etc...

Once you begin including scripts, the situation can get confusing, but the behaviour is simple: `require` scripts run on a "global" environment, while `dofile` scripts run in the same environment as the caller. See below for more info.

## Require / Loadfile / Dofile

These funtions have been replaced with implementations that allow using OSPGL style asset paths. For example, to include a lua file `g_effect_manager.lua` 
from the mod `engine_effects`, you would do: `require("engine_effects:g_effect_manager.lua")`. Keep on reading to understand why the file is prefixed 
with `g_`.

Furthermore, these functions use the asset system,
so it's possible that a file will be redirected elsewhere or even modified during load by a patch module.

### Require vs Dofile

The difference between `require` and `dofile` is the fact that `require` caches files, instead of loading them every time it's called. This means
that if you `require` a file which returns a global table, and modify said table, all other scripts which `require` (or have previously) the same file 
will see the changes. 

In many lua applications, require is used pretty much exclusively, but in OSPGL we actually use both in different circumstances. Of course, you are 
free to use lua in your preferred way, but this will keep your code in the style of `core`.

#### When to use dofile

Dofile is to be used when the file being loaded represents a class or some kind of object of which there may be many instances. This reduces the 
ammount of boiler-plate code that has to be written (to instance classes), and also may improve perfomance ever so slightly as it avoids the 
use of metatable `__index` method that would otherwise have to be used.

In `core`, scripts that are to be included using `dofile` are clearly marked with the prefix `l_` or `c_`, which stands for `local` and `class` respectively. The game engine
will emit a warning if you `require` a file which begins with `c_` or `l_`, as these will behave strangely if used this way.

#### When to use require

Require is to be used when the file being loaded contains a single function, a table which contains functions, or some kind of global. As no concept of "instance" exists,
these benefit greatly from the "load once" behaviour of require. 

Note that we grealy enforce this concept of load once. In stock lua it's possible to require the same file twice, and have it load twice, by tweaking
the module name. In OSPGL, a script will only load once if loaded with `require`. This is of course not true if the file is required from two different
lua instances (for example, the planet surface generator scripts run separately from the rest of the game, so requiring a file there won't have any 
effect on requiring said file in a machine script).

In `core`, scripts that are to be included **ONLY using** `require` are clearly marked with the prefix `g_`, which stands for `global`. The game engine
will emit a warning if you `dofile / loadfile` a file which begins with `g_`, as it may not have intended behavior. 

Note that scripts without any prefix are either not meant to be included, or don't care which method you use.

#### Environment of included scripts
- If you use `require`, the script will be run in the global environment. For normal use, this means that the only global available is `osp` and you have no access to any of the globals of the script you are included from. Of course, on a planet generation script you will not have access to `osp` and so on.
- If you use `dofile`, the script is on the same environment as the calling script, and may access **global** variables defined in said environment and similar. Abusing this behaviour to pass data is discouraged as it can result in very confusing code, but if you are simply breaking down a file into components, this may be of great utility.

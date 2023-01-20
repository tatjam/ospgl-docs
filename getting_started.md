# Getting started 

You may be interested in creating content for OSPGL, but this may seem like a daunting task at first. Fear not, the process has been 
fairly streamlined as modding is one of the main ideas behind OSPGL. This guide will explain how to setup your development environment,
and explain how a simple mod can be created.

## Packages

All content in OSPGL, except some core basics included in the engine code, comes in packages. Packages can be found in the 
`res/` folder inside the game directory, and can be installed manually (by copying the folder) or using the `ospm` package 
manager utility, which automates the process of downloading, updating and installing packages.

Inside `res/` you can also place folders which won't be read as packages by prefixing them with a `.`. This allows you to store 
IDE configuration and similar data in the `res/` folder. In fact, the `.luadef/` folder contains all lua definitions.

## Setting up your text editor

Lets set up your editor so it can handle autocompletion for `.lua` files. Although there are probably many lua language servers,
we will focus on [sumneko's one](https://github.com/sumneko/lua-language-server). This language server can be installed in many editors,
such as VS Code and NeoVim (LunarVim greatly simplifies the process!). We will focus on VS Code as this is an easy to use editor that 
allows a lot of customization.

You can install this language server easily in VS Code [here](https://marketplace.visualstudio.com/items?itemName=sumneko.lua). I
also recommend you to install a TOML enhanced support extension as default support in VS Code is a bit lacking.

Now, you can simply open the `res/` folder with your editor. If you have set up the language server correctly, 
autocompletion will work, not only with the content of your mod and OSPGL libraries, but also with other mods 
that you have installed.

If you want for `dofile` to be autocompleted, **which is highly recommended**, you need to do the following (instructions for VS Code):

- Go to File->Preferences->Settings 
- Optionally, switch to "Workspace" mode so the setting isn't globally applied
- Search for `sumneko`
- In Lua > Runtime: Plugin, write the absolute path to `res/.luadef/plugin.lua`

For other editors, check [https://github.com/sumneko/lua-language-server/wiki/Plugins](https://github.com/sumneko/lua-language-server/wiki/Plugins), note that you
don't need to do the `--develop=true` step.


## Creating your first package


Create a folder inside `res/` and name it whatever you want to name your mod. Remember to open the whole `res/` folder 
as your editor project, and not only your mod, as otherwise your autocompletion will not work.

### `package.toml`: Metadata for your mod 

This file is used mostly for package management. Here's an example of such a file:

TODO: It's not clear yet how packaging management will work and `package.toml` may disappear in favor of 
storing package info directly in `package.lua`.

### `package.lua`: Entrypoint for your mod 

OSPGL by default will not read anything else than `package.lua` in your mod. (Unless, for example, some vehicle uses a machine of your mod, or similar). Use your runtime here to setup everything in your mod.
For example, you will notify the existence of parts to the `game_database`, or sign up scripts to run on `update`.


## Designing a simple part 

Lets write a simple part that on activation generates an explosion with the objective of destroying the vehicle.

### Modelling the part 

We will use Blender, but you can use any 3D editor which supports export to `gltf`. 
If you don't really know how to use blender, you can download the [file here](bomb.blend) to continue.

### Exporting the model 

### Writing the part `toml` file 

### Adding the part to the game database 

### Writing the `bomb.lua` machine 

### Adding the machine to our part 

## Document your lua code 

If you want the language server to also understand your lua code, you must give some extra information to it on, 
for example, what are the expected types that a function will take. You can find more information on this subject 
on [sumneko's wiki](https://github.com/sumneko/lua-language-server/wiki/Annotations).

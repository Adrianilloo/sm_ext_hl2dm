# sm_ext_hl2dm
SourceMod extension with corrections and enhancements for/under Half-Life 2: Deathmatch

## So, what this is about?
This extension was motivated to improve the quality of [my casual match server](https://www.gametracker.com/server_info/194.99.21.233:27017), after finally deciding to take serious care about a game limitation that started since [mid-2018](https://store.steampowered.com/oldnews/42271), and a certain [SourceMod issue](https://github.com/alliedmodders/sourcemod/issues/1566) I noticed tied to this game's servers as well (but not to other Source games like CSS or TF2!). Making it an optimized extension consisting of two miscellaneous dedicated server issues' solutions, and open for more suitable patches I might consider in the future. The description of the features follows next.

### 1. `cl_showpluginmessages 1` assistance
As many HL2:DM players and/or admins know, Valve published an update by July 2018 which switched the default value of `cl_showpluginmessages` ConVar to `0`. This setting determines whether instances of a special set of exposed plugin-like networked dialogs (e.g. SourceMod menus, but not limited to) are allowed to display on the client. So, this change caused all new HL2:DM installs not to allow these dialogs by default anymore.

This extension provides an elegant way to diminish the effects of this problem. It's able to automatically display hints to players towards enabling the setting (set to `1`). The idea was to have these hints displayed in response to two activation events (when client has setting disabled): early after connecting to the server, and as soon as new dialogs are sent to the client.

Having introduced the feature, it might be sufficient to illustrate three provided ConVars admins can configure, carefully designed for best control flexibility, as a last step to fully know how it works. The values used are the default:

`"sm_pluginmessages_help_mode" = "2"`:
- Activation mode of plugin messages setting help. If `0`, the feature will be completely disabled and clients won't get any help to enable the setting. If `1`, the help will only happen on connect. If `2`, the help will happen on connect and after sending dialogs.

`"sm_pluginmessages_join_help_delay" = "6.0" min. 0.000000 max. 10.000000`:
- When `sm_pluginmessages_help_mode` is enabled (`1` or `2`), this defines the delay in seconds after which the plugin messages setting will be checked to help clients upon connect

`"sm_pluginmessages_use_chat" = "0"`:
- Whether to display plugin messages activation hints to chat (`1`) or to console (`0`) under `sm_pluginmessages_help_mode 2`. Leaving it disabled to use console (`0`) is useful to prevent the hints from resulting in potential spam to clients, otherwise.

Finally, I hope my detailed care through this feature against related Valve break helps you all to fight the inconvenience :smiley:.

### 2. Cached SM timeleft de-sync fix

For some reason, the SourceMod core holds its own copy of the `timeleft`-supporting variable (the time at which game / current map started), rather than strictly querying it from the game only at all times.

Together with the fact that SM doesn't reset such time mark upon executing `mp_restartgame` (a command to restart current game/match without reloading map), and, more generally, when `round_start` game events happen, these factors cause SM's `timeleft` to go negative once the time that was left over (right before round restart) finishes, consequently reporting a wrong `timeleft` message when queried by anyone (`"[SM] This is the last round!!"`).

This extension simply fixes it, re-syncing SM's internal `timeleft` when `round_start` events happen, in an identical way to how it's done in other games such as CSS or TF2 (via bundled SM extension for them).

## Building

Prepare a SM extensions environment, following same pre-configuration of [Building SourceMod](https://wiki.alliedmods.net/Building_SourceMod#Requirements) tutorial. Only the HL2DM SDK is needed, so, on the `checkout-deps.sh` part, format the command like this: `sourcemod/tools/checkout-deps.sh -s hl2dm` (or `checkout-deps.ps1` on Windows).

Next, download [these](https://raw.githubusercontent.com/alliedmodders/sourcemod/master/public/sample_ext/configure.py) [files](https://raw.githubusercontent.com/alliedmodders/sourcemod/master/public/sample_ext/AMBuildScript) and place them on the project's root directory.

Then, enter the following commands inside the same directory:

```
mkdir -p build
cd build
```

Now, it's time to effectively compile the extension. If you're building for Windows, you need to switch to the "VS2015 x86 Native Tools Command Prompt" to continue with the remaining steps.

For Windows, execute:

```
python ..\configure.py --hl2sdk-root=<HL2SDKs parent directory> --mms-path=<MM:S directory> --sm-path=<SM directory> --enable-optimize -s hl2dm
```

For Linux, on a normal shell:

```
CC=clang CXX=clang++ python ../configure.py --hl2sdk-root=<HL2SDKs parent directory> --mms-path=<MM:S directory> --sm-path=<SM directory> --enable-optimize -s hl2dm
```

And finally (under any OS), just enter: `ambuild`

(Not tried on Mac OS X)

Output files should be created at `package` folder under active `build` directory, with the same hierarchy than classic `addons` server folder for SourceMod.

## Support

If you have any problem with the extension, just file an [issue](https://github.com/Adrianilloo/sm_ext_hl2dm/issues/new) writing an understandable description in your own words. If you want to propose any new patch to be implemented in the extension, please start a [new discussion](https://github.com/Adrianilloo/sm_ext_hl2dm/discussions/new) instead. Issues that would clearly be better suited to be treated within the [game's SDK](https://github.com/ValveSoftware/source-sdk-2013) will be ignored.

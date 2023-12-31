# Killing Floor 2 Linux Server

This is (was) a setup of code running on a (free-tier) EC2 AWS instance, to setup and manage a Killing Floor 2 server.

One of the biggest issues I (and some of my friends) have had with playing KF2 has been finding the *perfect* server to play on. Using the server browser to find a map that meets *all* of the criteria you wanted was a chore at best and an impossibility at worst.

The following categories are things we considered when finding a server, and would be lucky to find even just 3 matching criteria:

- Map/game mode (and to make matters more complicated: custom maps)
- Max player count
- Difficulty (normal, hard, etc.)
- Map length (short, medium, long)
- Server location, and ping, for all the people that want to play
- Password protection (for the sake of friends-only, if we didn't want to be bothered by random joiners)

The last criteria alone is pretty impossible via just the in-game server browser, so it seemed like hosting our own server would be the best overall solution.

> NOTE:
> 
> I initially ran this on an EC2 instance, but you could run this locally or in any of your preferred cloud computing solutions - whatever you want.
> For EC2, you might have to, unfortunately, either go above a free-tier to get an instance with enough drive space to download KF2 onto, or open an extra volume and attach it to your free-tiered EC2. (which will probably increase the cost as well)

## Getting Started

This assumes a couple of things:

1. You have `steamcmd` installed for Linux.
2. Your Steam installations are hooked up to `~/Steam/`.
3. You've cloned this repo into your `$HOME` directory. (`~`)
4. The `./kf2/update.steam-script.txt` file has the first line modified to point to where you want to install KF2. (you can symlink `~/Steam/` to this location later if needed)

```bash
# Pre-req
sudo apt install steamcmd

# Install/update
~/kf2-server-manager/kf2/run-update
# Start the server
~/kf2-server-manager/kf2/start-server
```

If the location for this repo is NOT `~/kf2-server-manager`, you need to run the above server scripts with the env variable `SERVER_MANAGER_HOME` set to your preferred location.
This value is `~/kf2-server-manager/kf2` by default.

## Running the Server

Simply run:

```bash
$SERVER_MANAGER_HOME/start-server
```

Which runs the KF2 Linux executable at `~/Steam/steamapps/common/kf2server/Binaries/Win64/KFGameSteamServer.bin.x86_64`.

Use `start-server --help` for usage info.

### `start-server` CLI

<!-- Long description here, shorter descriptions in the help menu. -->
<!-- Remember to update both... -->

| Flag | Description | Value(s) |
|------|-------------|----------|
| `-h,--help` | Show help menu. | - |
| `-m,--map` `<map>` | Choose the map to set on server start. Default is `kf-bioticslab`. | Look at maps in `[KFGame.KFGameInfo].GameMapCycles` in `KFGame.ini` |
| `--game-mode` `<mode>` | Choose the game mode on server start. Default is `survival`. | `survival`, `versus`, `weekly`, `endless` |

> NOTE: There is an (optional) systems check script that runs before startup of the server. This is more specific to my personal setup of the server, and can be disabled via `SYSTEM_CHECKS_ENABLED=false`.

## Configuration

In `~/Steam/steamapps/common/kf2server/KFGame/Config/` exists multiple `*.ini` config files:

- `KFGame.ini`
  - General game config for the server. (stuff like, server info, name, password, game mode, game maps, etc.)
- `KFEngine.ini`
  - Server port management, server tick rate
- `KFWeb.ini`
  - Web admin config options

> NOTE: The real names of the above config files will have a prepended system name. (ie: `Linux`/`Windows`)

### Map List

The `start-server` script depends on an existing `maps.txt` file within `$SERVER_MANAGER_HOME` to display available within the CLI help menu, and to more importantly populate the initial map rotation list for the server on startup.

The map names need to follow the `KF-<name>` convention that matches the ID of the map. You can find this manually by navigating to `<steam-installation>/kf2server/KFGame/BrewedPC/Maps/<map-name>` and looking for a correspdoning `$MAP_ID.kfm` file (will usually follow the pattern of `KF-<MAP>.kfm`, ie: `KF-BioticsLab.kfm`).

The `maps.txt` file will be automatically update via the `run-update` script in accordance to the `GameMapCycles` configuration in `KFGame.ini`. **Keep that file updated on your own!**

### Custom Maps

Add the workshop ID into `KFEngine.ini`:

```ini
; This section will exist already if there are custom maps already set up...
[OnlineSubsystemSteamworks.KFWorkshopSteamworks]
ServerSubscribedWorkshopItems=2998243647 ; <-- Add a new item with the workshop ID
```

Additionally, the map name needs to be added to the `KFGame.ini > [GameMapCycles]` config, as mentioned in the above "Map List" section.

## Updating

```bash
$SERVER_MANAGER_HOME/run-update
```

- This will install any new updates/patches for Killing Floor 2 from Steam.
- Additionally, the `maps.txt` file will be auto-updated according to the existing `GameMapCycles` config for your server.

## Resources

For the official docs, refer to the [Killing Floor 2 Wiki's Dedicated Server Guide](https://wiki.killingfloor2.com/index.php?title=Dedicated_Server_(Killing_Floor_2)).

Most of the notes here are retranscribed from there, for brevity and easier referral. There are additional notes here that are specific to my implementation.

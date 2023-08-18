# csgo_server

## Introduction

### Purpose

Hold the scripts for deploying a private CS GO server in a VM.

Supported environments:

* libvirt/kvm

### Vocabulary

* CLI - command line interface.
* csgo - Counter Strike Global Offence.
* GSLT - Game Server Login Token.
* steamcmd - steam CLI client.

### References

* [Dedicated Servers List](https://developer.valvesoftware.com/wiki/Dedicated_Servers_List)
* [How to Make a Counter-Strike: Global Offensive Server on Linux](https://www.hostinger.com/tutorials/how-to-make-a-csgo-server)
* [forum - Source Dedicated Server (Linux)](https://steamcommunity.com/discussions/forum/14/)
* [SteamCMD Error Codes](https://github.com/GameServerManagers/LinuxGSM-Docs/blob/master/steamcmd/errors.md)
* [Source Dedicated Server](https://developer.valvesoftware.com/wiki/Source_Dedicated_Server)
* [Forum - Counter-Strike: Global Offensive](https://steamcommunity.com/app/730/discussions/)
* [debian csgo docker image](https://hub.docker.com/r/cm2network/csgo/)
* [SteamCMD](https://developer.valvesoftware.com/wiki/SteamCMD)

### Overview

### Usage

#### Manually start the csgo server

* login to the VM
* sudo -i
* sudo -i steam
* ~/csgo_git_repo/csgo_scripts/run_server.sh

#### Deploy to a local libvirt VM

* `git clone https://github.com/henkoch/csgo_server.git`
* cd csgo_server/terraform
* make base
  * This will download an ubuntu cloud image and expand it to 10GB.
* ./deploy_to_libvirt.sh
* ssh -i tf-cloud-init ansible@VM_IP_ADDR

#### Deploy a local docker image

Please see the `Dockerfile`

## Installation instructions

### Install csgo server

* [Counter-Strike: Global Offensive - Dedicated Servers](https://developer.valvesoftware.com/wiki/Counter-Strike:_Global_Offensive/Dedicated_Servers)
* [CFG files](https://developer.valvesoftware.com/wiki/CFG)
* [Game Modes](https://developer.valvesoftware.com/wiki/Counter-Strike:_Global_Offensive/Game_Modes)
* [Map list](https://developer.valvesoftware.com/wiki/Counter-Strike:_Global_Offensive/Maps)
* [Access rcon on server](https://mcprohosting.com/billing/knowledgebase/325/How-to-Use-RCON-on-a-Counter-Strike-GO-Server.html)
* [Counter-Strike: Global Offensive Maps](https://liquipedia.net/counterstrike/Portal:Maps)
* [Running a Counter Strike Global Offensive Server on Ubuntu 18.04](https://www.linode.com/docs/guides/launch-a-counter-strike-global-offensive-server-on-ubuntu-18-04/)
* [Installing a CS:GO Dedicated Server in Ubuntu](https://medium.com/@oritromax/installing-a-cs-go-dedicated-server-in-ubuntu-ed37377b06d1)

* su - steam
* bash
* /usr/games/steamcmd +force_install_dir /data/steam/csgo_app +login anonymous +app_update 740 validate +quit

### configure the csgo server

* export LD_LIBRARY_PATH=/usr/lib32:/data/steam/csgo/bin
* /data/steam/csgo_app/srcds_linux --version

* /data/steam/csgo_app/srcds_linux -game csgo -usercon -net_port_try 1 -tickrate 128 -nobots +game_type 0 +game_mode 1 +mapgroup mg_active +map de_mirage

### csgo docker image

* [cm2network/steamcmd](https://hub.docker.com/r/cm2network/steamcmd)

* docker build --tag csgo:0.1.3 .
* mkdir  ~/Downloads/steam_data
* docker run -it  --volume ${HOME}/Downloads/steam_data:/data/steam/Steam --volume ${HOME}/Downloads/csgo_app:/data/steam/csgo_app --volume ${HOME}/Dropbox/Sources/Servers/csgo_server/csgo_scripts:/data/steam/csgo_scripts csgo:0.1.3 bash
  * TODO put this as a copy in the Dockerfile, once this works
* su - steam
* /usr/games/steamcmd +force_install_dir /data/steam/csgo_app/ +login anonymous +app_update 740 validate +quit
  * currently it downloads about 33GB of data.

## CSGO configuration

### Files of interest

* autoexec.cfg - [This file is executed before the first map starts.](https://developer.valvesoftware.com/wiki/Counter-Strike:_Global_Offensive/Dedicated_Servers#autoexec.cfg)
* server.cfg - [This file is executed every map change, and before the gamemode files.](https://developer.valvesoftware.com/wiki/Counter-Strike:_Global_Offensive/Dedicated_Servers#server.cfg)
* gamemodes_server.txt - [This file allows the server administrator to customize each game mode for their own server. It overrides and defaults set by Valve in gamemodes.txt.](https://developer.valvesoftware.com/wiki/Counter-Strike:_Global_Offensive/Dedicated_Servers#gamemodes_server.txt)
* gamemode_casual_server.cfg - [used for overriding the configurations that valve have put into gamemode_casual.cfg](https://developer.valvesoftware.com/wiki/Counter-Strike:_Global_Offensive/Dedicated_Servers#gamemode_casual_server.cfg)

### Maps

* Map name prefix seems to indicate the type of game it becomes
  * cs_ - Hostage/Rescue (original counter-strike mode)
  * de_ - demolition? or is it deathmatch?
  * ar_ - arms race?
  * dz_ - dangerzone

* TODO can I use this command to switch mode? `map <mapname> survival`
  * changelevel dz_blacksite survival

### Parameters of interest

* mp_maxrounds
* mp_friendlyfire
* mp_roundtime
* mp_timelimit
* mp_allowNPCs
* mp_teams_unbalance_limit
* mp_respawnwavetime
* mp_roundtime_hostage
* mp_freezetime
* mp_disable_respawn_times
* mp_disable_respawn_times
* mp_friendlyfire
* mp_limitteams
mp_teamplay
mp_autoteambalance

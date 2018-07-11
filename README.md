> **Since the original creator - Kurt Stolle (check out his GitHub profile [here](https://github.com/kurt-stolle/)) - isn't involved in the GMod community anymore, he allowed me to fork this project.**

# Jail Break 7

## Configuration

The following convars can be put in your `./cfg/server.cfg` file.
It is recommended to not put convars in your config file unless you know exactly what the effect is the convar is. Invalid values may break the gamemode.

Convar                                     | Effect
-------------------------------------------|----------------------------------------------------------------------------------
`jb_config_debug 1/0`                      | Debug mode, you might want to set this to 0 to reduce annoying console messages 
`jb_config_font Roboto`                  | The gamemode's font. I disrecommend changing this; may cause everything to go weird!
`jb_config_website example.com`            | The URL of your website. This URL will be displayed as a form of advertising your site.
`jb_config_jointime 20` [minimum: 10]      | (seconds) period when the map just loaded and people are given a chance to join.
`jb_config_setuptime 60` [minimum: 10]     | (seconds) period at the start of the round when guards may claim warden.
`jb_config_guards_allowed 30` [minimum: 1] | Percentage of players allowed to be guard
`jb_config_guards_playtime 120`            | (minutes) playtime required to be guard (admins bypass this)
`jb_config_rebel_sensitivity 0-2`          | 2 = prisoner becomes rebel on killing a guard, 1 = prisoner becomes rebel on damaging a guard, 0 = prisoner never becomes rebel.
`jb_config_prisoners_namechange 1/0`       | 1 = use fake names for prisoners (ex. Prisoner 192346), 0 = use normal nicknames for prisoners
`jb_config_warden_control_enabled 1/0`     | Toggles whether warden control should be enabled or not. (recommended: always 1);
`jb_config_prisoner_special_chance 8`        | Chance a prisoner will get a random weapon. Chance = random(1,var)==1;
`jb_config_max_warden_rounds 3`              | Maximum amount of rounds a player can be warden in a row. 
`jb_config_primaries_are_concealed 1/0`       | Conceal primaries - they won't draw on the player's back if this is set to 1.
`jb_config_secondaries_are_concealed 1/0`     | Conceal secondaries - they won't draw on the player's thigh if this is set to 1.
`jb_config_knives_are_concealed 1/0`           | Conceal knives - they won't draw on the player's thigh if this is set to 1.
`jb_config_grenades_are_concealed 1/0`       | Conceal grenades - they won't draw on the player's thigh if this is set to 1.
`jb_config_falldamage_is_disabled 1/0`   | Disables falldamage if this is set to 1.
`jb_config_rounds_per_map 10`                 | Rounds until mapvote - ONLY SET THIS IF YOU HAVE A MAPVOTE SYSTEM ON YOUR SERVER/COMMUNITY
`jb_config_notify_lastguard 1/0`           | Send the "last guard kills all" notification


Further console commands:

Command                                   | Effect
------------------------------------------|--------------------------------------------------------------------
`jb_dropweapon`                       | Drops the selected weapon.
`jb_pickup`                            | Takes the weapon in the user's crosshair.
`jb_team_select_guard`              | Switches to the guard team.
`jb_team_select_prisoner`          | Switches to the prisoner team.
`jb_team_select_spectator`         | Switches to spectator.
`jb_admin_swap STEAMID`             | Switches the player with the specified STEAMID to the opposite team.
`jb_admin_swap_specator STEAMID`  | Switches the player with the specified STEAMID to spectator.
`jb_admin_revive STEAMID`           | Revives the player with the specified STEAMID.
`jb_admin_configmode`                | Edit KOS-zones. Spawn two points with "R", move them and save by freezing both of them using right click. The corresponding file is located at \steamapps\common\GarrysMod\garrysmod\data\jailbreak\zones\.

Chat commands (add "!" or "/" infront of the command; "/" hides it from other players):

Command                                    | Effect
-------------------------------------------|--------------------------------------------------------------------
`drop`                                   | Drops the current weapon.
`pickup `                                | Takes the weapon in the user's crosshair.
`guard`                                  | Switches to the guard team.
`prisoner`                              | Switches to the prisoner team.
`spectator`                             | Switches to spectator.
`teamswap`, `swap` or `swapteam`    | Switches to the opposite team.



## Developers

### Last requests
This is how last requests are added. LR files have to put put in the lastrequests folder.
```lua

	-- Initialize a new LR class
	local LR = JB.CLASS_LR();

	-- Give it a name and description
	LR:SetName("Knife Battle");
	LR:SetDescription("The guard and the prisoner both get a knife, all other weapons are stripped, and they must fight eachother until one of the two dies");

	-- Give it an Icon for in the LR-menu
	LR:SetIcon(Material("icon16/flag_blue.png"))

	-- Setup what happens after somebody picks this Last Request
	LR:SetStartCallback(function(prisoner,guard)
		for _,ply in ipairs{prisoner,guard} do
			ply:StripWeapons();
			ply:Give("weapon_jb_knife");
			ply:Give("weapon_jb_fists");
			
			ply:SetHealth(100);
			ply:SetArmor(0);
		end
	end)

	-- Tell JailBreak that this LR is ready for use.
	LR();
```

### Hooks

These are all custom hooks called by the gamemode.
Format: `hookname ( arguments[, optional argument] )`

```lua
-- JailBreakRoundStart
-- Called when the round starts
JailBreakRoundStart ( rounds_passed )

-- JailBreakRoundEnd 
-- Called when the round ends
JailBreakRoundEnd ( rounds_passed )

-- JailBreakPlayerSwitchTeam
-- Called on team switch
JailBreakPlayerSwitchTeam ( player, team )

-- JailBreakStartMapvote
-- Called when a mapvote should be started.
-- return true: Use custom mapvote system, return false: Use default system (normally; no mapvote).
JailBreakStartMapvote ( rounds_passed, extentions_passed ) 

-- JailBreakClaimWarden
-- Called when somebody claims warden
JailBreakClaimWarden ( player, warden_rounds_in_a_row )

-- JailBreakWardenControlChanged
-- Called when a warden control is changed
JailBreakWardenControlChanged	( player, option, value )

-- JailBreakWardenSpawnProp
-- Called when the warden spawns a prop
JailBreakWardenSpawnProp ( player, type[, model] )

-- JailBreakWardenPlacePointer
-- Called when a pointer is placed
JailBreakWardenPlacePointer ( player, type, position )

```

Implement a hook using the `hook.Add` function, example:

```lua
hook.Add("JailBreakRoundStart","JB.Examples.RoundStart",function(rounds_passed) 
	if rounds_passed > 5 then
		print "We are past round 5. Let's kill everyone!";
		
		for _,ply in ipairs( player.GetAll() ) do
			ply:Kill();
		end
	end
end);
```

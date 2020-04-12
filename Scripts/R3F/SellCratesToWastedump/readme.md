This will allow you to sell a crate (or the contents of the crate) directly to the waste dump trader. How your players get the crate to the trader depends on how you have your server setup. To get crates to the trader, you can use Advanced Sling Loading, IgiLoad, R3F, etc.. It does not matter how you get the crate to the trader. What does matter, is that once it is there, it cannot be attached to anything to be detected by the Waste Dump trader. This was done to prevent players from selling someone else's crate before it is even unloaded from a truck or dropped from a helicopter. Once unloaded or dropped, anyone will be able to sell the crate to the Waste Dump trader.

You will need to modify two client-side files and one server side file. Additionally, you will need to include these three files in the CfgExileCustomCode.
 

Client-Side File: ExileClient_gui_traderDialog_updateInventoryDropdown.sqf
```
private["_dialog","_inventoryDropdown","_index","_nearVehicles","_crateTypes"];﻿
disableSerialization;
_crateTypes = [
		"CargoNet_01_box_F",
		"Exile_Container_SupplyBox",
		"I_CargoNet_01_ammo_F",
		"O_CargoNet_01_ammo_F",
		"B_CargoNet_01_ammo_F",
		"I_supplyCrate_F", 
		"O_supplyCrate_F", 
		"B_supplyCrate_F", 
		"C_supplyCrate_F",
		"IG_supplyCrate_F", 
		"Box_NATO_AmmoVeh_F", 
		"Box_East_AmmoVeh_F", 
		"Box_IND_AmmoVeh_F", 
		"I_CargoNET_01_F",
		"O_CargoNET_01_F",
		"B_CargoNET_01_F",
		"Land_CargoBox_V1_F", 
		"ASC_B_box",
		"Box_NATO_Wps_F", 
		"Box_East_Wps_F", 
		"Box_IND_Wps_F", 
		"Box_East_WpsLaunch_F", 
		"Box_NATO_WpsLaunch_F", 
		"Box_IND_WpsLaunch_F", 
		"Box_IND_WpsSpecial_F", 
		"Box_East_WpsSpecial_F", 
		"Box_NATO_WpsSpecial_F",
		"Box_NATO_Wps_F", 
		"Box_East_Wps_F", 
		"Box_IND_Wps_F", 
		"Box_East_WpsLaunch_F", 
		"Box_NATO_WpsLaunch_F", 
		"Box_IND_WpsLaunch_F", 
		"Box_IND_WpsSpecial_F", 
		"Box_East_WpsSpecial_F", 
		"Box_NATO_WpsSpecial_F",
		"Box_NATO_AmmoOrd_F", 
		"Box_East_AmmoOrd_F", 
		"Box_IND_AmmoOrd_F", 
		"Box_NATO_Grenades_F", 
		"Box_East_Grenades_F", 
		"Box_IND_Grenades_F", 
		"Box_NATO_Ammo_F", 
		"Box_East_Ammo_F", 
		"Box_IND_Ammo_F", 
		"Box_IND_Support_F", 
		"Box_East_Support_F", 
		"Box_NATO_Support_F"
	];
_dialog = uiNameSpace getVariable ["RscExileTraderDialog", displayNull];
_inventoryDropdown = _dialog displayCtrl 4004;
lbClear _inventoryDropdown;
_index = _inventoryDropdown lbAdd "Equipment";
_inventoryDropdown lbSetValue [_index, 1];
_inventoryDropdown lbSetPicture [_index, "a3\ui_f\data\gui\Rsc\RscDisplayArsenal\face_ca.paa"];
_inventoryDropdown lbSetCurSel 0;
if !((uniform player) isEqualTo "") then
{
	_index = _inventoryDropdown lbAdd "Uniform";
	_inventoryDropdown lbSetPicture [_index, "a3\ui_f\data\gui\Rsc\RscDisplayArsenal\uniform_ca.paa"];
	_inventoryDropdown lbSetValue [_index, 2];
};
if !((vest player) isEqualTo "") then
{
	_index = _inventoryDropdown lbAdd "Vest";
	_inventoryDropdown lbSetPicture [_index, "a3\ui_f\data\gui\Rsc\RscDisplayArsenal\vest_ca.paa"];
	_inventoryDropdown lbSetValue [_index, 3];
};
if !((backpack player) isEqualTo "") then
{
	_index = _inventoryDropdown lbAdd "Backpack";
	_inventoryDropdown lbSetPicture [_index, "a3\ui_f\data\gui\Rsc\RscDisplayArsenal\backpack_ca.paa"];
	_inventoryDropdown lbSetValue [_index, 4];
};
_nearVehicles = nearestObjects [player, ["LandVehicle","Air","Ship"] + _crateTypes, 80];
{
	if (local _x) then
	{
		if (alive _x) then
		{
			if (isNull attachedTo _x) then
			{
				_index = _inventoryDropdown lbAdd getText(configFile >> "CfgVehicles" >> (typeOf _x) >> "displayName");
				_inventoryDropdown lbSetData [_index, netId _x];
				_inventoryDropdown lbSetValue [_index, 5];
			};
		};
	} else {
		if (typeOf _x in _crateTypes) then
		{
			if (alive _x) then
			{
				if (isNull attachedTo _x) then
				{
					_index = _inventoryDropdown lbAdd getText(configFile >> "CfgVehicles" >> (typeOf _x) >> "displayName");
					_inventoryDropdown lbSetData [_index, netId _x];
					_inventoryDropdown lbSetValue [_index, 5];
				};
			};
		};
	};
}
forEach _nearVehicles;
true
```

Client-Side File: ExileClient_gui_wasteDumpDialog_show.sqf
```
private["_nearVehicles","_localVehicles","_display","_revenue","_sellButton","_dropdown","_vehicleObject","_vehicleName","_index","_crateTypes"];﻿
disableSerialization;
_crateTypes = [
		"CargoNet_01_box_F",﻿
		"Exile_Container_SupplyBox",
		"I_CargoNet_01_ammo_F",
		"O_CargoNet_01_ammo_F",
		"B_CargoNet_01_ammo_F",
		"I_supplyCrate_F", 
		"O_supplyCrate_F", 
		"B_supplyCrate_F", 
		"C_supplyCrate_F",
		"IG_supplyCrate_F", 
		"Box_NATO_AmmoVeh_F", 
		"Box_East_AmmoVeh_F", 
		"Box_IND_AmmoVeh_F", 
		"I_CargoNET_01_F",
		"O_CargoNET_01_F",
		"B_CargoNET_01_F",
		"Land_CargoBox_V1_F", 
		"ASC_B_box",
		"Box_NATO_Wps_F", 
		"Box_East_Wps_F", 
		"Box_IND_Wps_F", 
		"Box_East_WpsLaunch_F", 
		"Box_NATO_WpsLaunch_F", 
		"Box_IND_WpsLaunch_F", 
		"Box_IND_WpsSpecial_F", 
		"Box_East_WpsSpecial_F", 
		"Box_NATO_WpsSpecial_F",
		"Box_NATO_Wps_F", 
		"Box_East_Wps_F", 
		"Box_IND_Wps_F", 
		"Box_East_WpsLaunch_F", 
		"Box_NATO_WpsLaunch_F", 
		"Box_IND_WpsLaunch_F", 
		"Box_IND_WpsSpecial_F", 
		"Box_East_WpsSpecial_F", 
		"Box_NATO_WpsSpecial_F",
		"Box_NATO_AmmoOrd_F", 
		"Box_East_AmmoOrd_F", 
		"Box_IND_AmmoOrd_F", 
		"Box_NATO_Grenades_F", 
		"Box_East_Grenades_F", 
		"Box_IND_Grenades_F", 
		"Box_NATO_Ammo_F", 
		"Box_East_Ammo_F", 
		"Box_IND_Ammo_F", 
		"Box_IND_Support_F", 
		"Box_East_Support_F", 
		"Box_NATO_Support_F"
	];
_nearVehicles = nearestObjects [player, ["LandVehicle", "Air", "Ship"] + _crateTypes, 150];
_localVehicles = [];
{
	if (local _x) then
	{
		if (alive _x) then
		{
			if (isNull attachedTo _x) then
			{
				_localVehicles pushBack _x;
			};
		};
	} else {
		if (typeOf _x in _crateTypes) then
		{
			if (alive _x) then
			{
				if (isNull attachedTo _x) then
				{
					_localVehicles pushBack _x;
				};
			};
		};
	};
}
forEach _nearVehicles;
if (_localVehicles isEqualTo []) exitWith
{
	["ErrorTitleAndText", ["Whoops!", "Park within 50m and get in as driver first."]] call ExileClient_gui_toaster_addTemplateToast;
};
ExileClientCurrentTrader = _this;
createDialog "RscExileWasteDumpDialog";
waitUntil { !isNull findDisplay 24011 };
_display = uiNameSpace getVariable ["RscExileWasteDumpDialog", displayNull];
_revenue = _display displayCtrl 4001;
_revenue ctrlSetStructuredText (parseText "<t size='1.4'>0<img image='\exile_assets\texture\ui\poptab_notification_ca.paa' size='1' shadow='true' /></t>");
_sellButton = _display displayCtrl 4000;
_sellButton ctrlEnable false;
_dropdown = _display displayCtrl 4002;
lbClear _dropdown;
{
	_vehicleObject = _x;
	_vehicleName = getText(configFile >> "CfgVehicles" >> (typeOf _vehicleObject) >> "displayName");
	_index = _dropdown lbAdd (format ["Cargo: %1", _vehicleName]);
	_dropdown lbSetData [_index, netId _vehicleObject];
	_dropdown lbSetValue [_index, 1];
	_index = _dropdown lbAdd (format ["Vehicle + Cargo: %1", _vehicleName]);
	_dropdown lbSetData [_index, netId _vehicleObject];
	_dropdown lbSetValue [_index, 2];
}
forEach _localVehicles;
true call ExileClient_gui_postProcessing_toggleDialogBackgroundBlur;
true
```

Server-Side File: ExileServer_system_trading_network_wasteDumpRequest.sqf
```sqf
private["_sessionID","_parameters","_vehicleNetID","_mode","_vehicleObject","_vehicleDB﻿ID","_playerObject","_cargo","_revenue","_playerMoney","_respectGain","_playerRespect","_logging","_traderLog","_responseCode","_crateTypes"];
_sessionID = _this select 0;
_parameters = _this select 1;
_vehicleNetID = _parameters select 0;
_mode = _parameters select 1;
_crateTypes = [
		"CargoNet_01_box_F",
		"Exile_Container_SupplyBox",
		"I_CargoNet_01_ammo_F",
		"O_CargoNet_01_ammo_F",
		"B_CargoNet_01_ammo_F",
		"I_supplyCrate_F", 
		"O_supplyCrate_F", 
		"B_supplyCrate_F", 
		"C_supplyCrate_F",
		"IG_supplyCrate_F", 
		"Box_NATO_AmmoVeh_F", 
		"Box_East_AmmoVeh_F", 
		"Box_IND_AmmoVeh_F", 
		"I_CargoNET_01_F",
		"O_CargoNET_01_F",
		"B_CargoNET_01_F",
		"Land_CargoBox_V1_F", 
		"ASC_B_box",
		"Box_NATO_Wps_F", 
		"Box_East_Wps_F", 
		"Box_IND_Wps_F", 
		"Box_East_WpsLaunch_F", 
		"Box_NATO_WpsLaunch_F", 
		"Box_IND_WpsLaunch_F", 
		"Box_IND_WpsSpecial_F", 
		"Box_East_WpsSpecial_F", 
		"Box_NATO_WpsSpecial_F",
		"Box_NATO_Wps_F", 
		"Box_East_Wps_F", 
		"Box_IND_Wps_F", 
		"Box_East_WpsLaunch_F", 
		"Box_NATO_WpsLaunch_F", 
		"Box_IND_WpsLaunch_F", 
		"Box_IND_WpsSpecial_F", 
		"Box_East_WpsSpecial_F", 
		"Box_NATO_WpsSpecial_F",
		"Box_NATO_AmmoOrd_F", 
		"Box_East_AmmoOrd_F", 
		"Box_IND_AmmoOrd_F", 
		"Box_NATO_Grenades_F", 
		"Box_East_Grenades_F", 
		"Box_IND_Grenades_F", 
		"Box_NATO_Ammo_F", 
		"Box_East_Ammo_F", 
		"Box_IND_Ammo_F", 
		"Box_IND_Support_F", 
		"Box_East_Support_F", 
		"Box_NATO_Support_F"
	];
try 
{
	_vehicleObject = objectFromNetId _vehicleNetID;
	_vehicleDBID = _vehicleObject getVariable "ExileDatabaseID";
	if (isNull _vehicleObject) then
	{
		throw 6;
	};
	if (_vehicleObject getVariable ["ExileMutex", false]) then
	{
		throw 12;
	};
	_vehicleObject setVariable ["ExileMutex", true];
	_playerObject = _sessionID call ExileServer_system_session_getPlayerObject;
	if (isNull _playerObject) then
	{
		throw 1;
	};
	if !(alive _playerObject) then
	{
		throw 2;
	};
	if !((owner _vehicleObject) isEqualTo (owner _playerObject)) then 
	{
		if (typeOf _vehicleObject in _crateTypes) then
		{
			_vehicleObject setOwner (owner _playerObject);
		} else {
			throw 6;
		};
	};
	_cargo = _vehicleObject call ExileClient_util_containerCargo_list;
	_revenue = _cargo call ExileClient_util_gear_calculateTotalSellPrice;
	clearBackpackCargoGlobal _vehicleObject;
	clearItemCargoGlobal _vehicleObject;
	clearMagazineCargoGlobal _vehicleObject;
	clearWeaponCargoGlobal _vehicleObject;
	if (_mode isEqualTo 2) then
	{
		_revenue = _revenue + ([(typeOf _vehicleObject)] call ExileClient_util_gear_calculateTotalSellPrice);
		_vehicleObject call ExileServer_object_vehicle_remove;
		deleteVehicle _vehicleObject;
	}
	else 
	{
		_vehicleObject call ExileServer_object_vehicle_database_update;
	};
	_playerMoney = _playerObject getVariable ["ExileMoney", 0];
	_playerMoney = _playerMoney + _revenue;
	_playerObject setVariable ["ExileMoney", _playerMoney, true];
	format["setPlayerMoney:%1:%2", _playerMoney, _playerObject getVariable ["ExileDatabaseID", 0]] call ExileServer_system_database_query_fireAndForget;
	_respectGain = _revenue * getNumber (configFile >> "CfgSettings" >> "Respect" >> "tradingRespectFactor");
	_playerRespect = _playerObject getVariable ["ExileScore", 0];
	_playerRespect = floor (_playerRespect + _respectGain);
	_playerObject setVariable ["ExileScore", _playerRespect];
	format["setAccountScore:%1:%2", _playerRespect, (getPlayerUID _playerObject)] call ExileServer_system_database_query_fireAndForget;
	[_sessionID, "wasteDumpResponse", [0, _revenue, str _playerRespect]] call ExileServer_system_network_send_to;
	_logging = getNumber(configFile >> "CfgSettings" >> "Logging" >> "traderLogging");
	if (_logging isEqualTo 1) then
	{
		_traderLog = format ["PLAYER: ( %1 ) %2 SOLD ITEM: %3 (ID# %4) with Cargo %5 FOR %6 POPTABS AND %7 RESPECT | PLAYER TOTAL MONEY: %8",getPlayerUID _playerObject,_playerObject,typeOf _vehicleObject,_vehicleDBID,_cargo,_revenue,_respectGain,_playerMoney];
		"extDB2" callExtension format["1:TRADING:%1",_traderLog];
	};
}
catch
{
	_responseCode = _exception;
	[_sessionID, "wasteDumpResponse", [_responseCode, 0, ""]] call ExileServer_system_network_send_to;
};
if (!isNull _vehicleObject) then
{
	_vehicleObject setVariable ["ExileMutex", false];
};
true
CfgExileCustomCode:

  Hide contents
class CfgExileCustomCode
{
	ExileClient_g﻿ui_traderDialog_updateInventoryDropdown = "overrides\ExileClient_gui_traderDialog_updateInventoryDropdown.sqf";
	ExileClient_gui_wasteDumpDialog_show = "overrides\ExileClient_gui_wasteDumpDialog_show.sqf";
	ExileServer_system_trading_network_wasteDumpRequest = "overrides\ExileServer_system_trading_network_wasteDumpRequest.sqf";
};
```
Make sure you set the appropriate file path in your CfgExileCustomCode if it is different than mine or your server will not be able to find the files.

Note: This will NOT require any﻿ changes to Advanced Sling Load, IgiLoad, R3F or any other logistics script needed to transport crates to the Waste Dump trader.

Enjoy!﻿﻿

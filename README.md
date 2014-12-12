DayzBuildVectors
================

Install Guide
-------------

If you have not installed Rimblock's [A Plot for Life v2.34](https://github.com/RimBlock/Epoch/tree/master/A%20Plot%20for%20Life/v2.34%20%26%20Snap%20Pro%20v1.4.1), do so now (ignore the server_monitor.sqf file as I will cover it).

####Server Side

First thing you need to do is make a few edits to your dayz_server.pbo file.
Start by opening your **server_monitor.sqf**(Remove anything done from P4L installation) which is located in the system folder and find the following code block:

```
if (!_wsDone) then {
	if (count _worldspace >= 1) then { _dir = _worldspace select 0; };
	_pos = [getMarkerPos "center",0,4000,10,0,2000,0] call BIS_fnc_findSafePos;
	if (count _pos < 3) then { _pos = [_pos select 0,_pos select 1,0]; };
	diag_log ("MOVED OBJ: " + str(_idKey) + " of class " + _type + " to pos: " + str(_pos));
};
```

After that, insert this:
```
_vector = [[0,0,0],[0,0,0]];
_vecExists = false;
_ownerPUID = "0";	
if (count _worldspace >= 3) then{
	if(count _worldspace == 3) then{
			if(typename (_worldspace select 2) == "STRING")then{
				_ownerPUID = _worldspace select 2;
			}else{
				 if(typename (_worldspace select 2) == "ARRAY")then{
					_vector = _worldspace select 2;
					if(count _vector == 2)then{
						if(((count (_vector select 0)) == 3) && ((count (_vector select 1)) == 3))then{
							_vecExists = true;
						};
					};
				};					
			};
			
	}else{
		//Was not 3 elements, so check if 4 or more
		if(count _worldspace == 4) then{
			if(typename (_worldspace select 3) == "STRING")then{
				_ownerPUID = _worldspace select 3;
			}else{
				if(typename (_worldspace select 2) == "STRING")then{
					_ownerPUID = _worldspace select 2;
				};
			};
	
	
			if(typename (_worldspace select 2) == "ARRAY")then{
				_vector = _worldspace select 2;
				if(count _vector == 2)then{
					if(((count (_vector select 0)) == 3) && ((count (_vector select 1)) == 3))then{
						_vecExists = true;
					};
				};
			}else{
				if(typename (_worldspace select 3) == "ARRAY")then{
					_vector = _worldspace select 3;
					if(count _vector == 2)then{
						if(((count (_vector select 0)) == 3) && ((count (_vector select 1)) == 3))then{
							_vecExists = true;
						};
					};
				};
			};
			
		}else{
			//More than 3 or 4 elements found
			//Might add a search for the vector, ownerPUID will equal 0
		};
	};
}; 
```

Once that is complete, find this next line:
```
_object setVariable ["ObjectID", _idKey, true];
```
And place the following after it:
```
_object setVariable ["ownerPUID", _ownerPUID, true];
```
Now it's time to apply the vector to the object, so find the following line:
```
_object setdir _dir;
```
And place this after it:
```
if(_vecExists)then{
	_object setVectorDirAndUp _vector;
}; 
```
Last but not least, we need to save the objects direction to a variable. To do that, find:
```
if (DZE_GodModeBase) then {
```
And place the following above it:
```
_object setVariable["memDir",_dir,true];
```


The next file we need to edit is the **server_functions.sqf**.

Find the following function:
```
dayz_objectUID2 = {
```
And replace that code block with:
```
dayz_objectUID2 = {
	private["_position","_dir","_key","_element","_vector","_set","_vecCnt","_usedVec"];
	_dir = _this select 0;
	_key = "";
	_position = _this select 1;
	
	if((count _this) == 2) then{
		{
			_x = _x * 10;
			if ( _x < 0 ) then { _x = _x * -10 };
			_key = _key + str(round(_x));
		} count _position;
		_key = _key + str(round(_dir));
	}else{
		_vector = [];
		_usedVec = false;
		{
			_element = _x;
			if(typeName _element == "ARRAY") then{
				_vector = _element;
				if((count _vector) == 2)then{
					if(((count (_vector select 0)) == 3) && ((count (_vector select 1)) == 3))then{
							{
								_x = _x * 10;
								if ( _x < 0 ) then { _x = _x * -10 };
								_key = _key + str(round(_x));
							} count _position;
							
							_vecCnt = 0;
							{
								_set = _x;
								{
									_vecCnt = _vecCnt + (round (_x * 100))
									
								} count _set;
								
							} count _vector;
							if(_vecCnt < 0)then{
								_vecCnt = ((_vecCnt * -1) * 3);
							};
							_key = _key + str(_vecCnt);
							_usedVec = true;
					};
				};
			};
		} count _this;
		
		if!(_usedVec) then{
				{
					_x = _x * 10;
					if ( _x < 0 ) then { _x = _x * -10 };
					_key = _key + str(round(_x));
				} count _position;
				_key = _key + str(round(_dir));
		};
		
		
	};
	_key
};
```

**Optional:** Install [Precise Base Building](http://epochmod.com/forum/index.php?/topic/15813-release-v103-precise-base-building-persistent-bases-after-restart/). HIGHLY RECOMMENDED and necessary if you ran it previously.  

That completes everything server side :)

####Mission Side
As of right now, the only file pack that I have up is Rimblock's Plot For Life v2.34 infused with Raymix's Snap Build Pro.
[Download the zip file](https://github.com/strikerforce/DayzBuildVectors/archive/master.zip) and open up the folder P4L\_Snap_Replacements. That folder is equilvilent to the custom folder.
Replace the files in your mission with those (I only provided the files that needed changeing).

Once that is complete, open up your custom **compile.sqf** file and add these near **player_build =**
```
fnc_SetPitchBankYaw =       compile preprocessFileLineNumbers "Custom\BuildVectors\fnc_SetPitchBankYaw.sqf";
DZE_build_vector_file = 		"Custom\BuildVectors\build_vectors.sqf";
build_vectors = 				compile preprocessFileLineNumbers DZE_build_vector_file;
```
Then open your **init.sqf** file and add these variables near **DZE_BuildOnRoads**:
```
DZE_noRotate = []; //Objects that cannot be rotated. Ex: DZE_noRotate = ["ItemVault"] (NOTE: The objects magazine classname)
DZE_vectorDegrees = [0.01, 0.1, 1, 5, 15, 45, 90];
DZE_curDegree = 45; //Starting rotation angle. //Prefered any value in array above
DZE_dirWithDegrees = true; //When rotating objects with Q&E, use the custom degrees
```

Finally, in your **variables.sqf**, find:
```
s_player_lockUnlock_crtl = -1;
```
Under that, add:
```
s_player_toggleDegree = -1;
s_player_toggleDegrees=[];
degreeActions = -1;
s_player_toggleVector = -1;
s_player_toggleVectors=[];
vectorActions = -1;
```


That's it, your done!

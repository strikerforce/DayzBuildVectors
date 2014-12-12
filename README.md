DayzBuildVectors
================

Install Guide
-------------

###Server Side

First thing you need to do is make a few edits to your dayz_server.pbo file.
Start by opening you server_monitor.sqf which is located in the system folder and find the following code block:

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

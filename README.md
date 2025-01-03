
# Shared Libs

![Logo](https://img.icons8.com/color/96/fivem.png)

[Based on vRPEX libs](https://github.com/vRP-framework/vRP)

Tools, Proxy and Tunnel

This script can easily transfer packages of functions from a script to another.

Tunnel Script This script can easily transfer packages of functions by bypassing server-client barrier, just like an usual event.



## Example of usage: add to fxmanifest

```lua
shared_script "@shared-libs/lib/lib.lua" -- add here in your fxmanifest

client_scripts { "@shared-libs/lib/utils.lua", -- add here in your fxmanifest
'config/config-client.lua',
'client.lua' }
server_scripts { "@shared-libs/lib/utils.lua", -- add here in your fxmanifest
'@mysql-async/lib/MySQL.lua',
'config/config-server.lua',
'server.lua' }
```



## Example of usage: in your server scripts add

```lua
local Tunnel = module("shared-libs","lib/Tunnel") -- add here in your server
local Proxy = module("shared-libs","lib/Proxy") -- add here in your server
local Tools = module("shared-libs","lib/Tools") -- add here in your server

--local Tunnel = exports["shared-libs"]:myTunnel()

--local Tools = exports["shared-libs"]:myTools()
local QBCore = exports["qb-core"]:GetCoreObject()

myServerVarAndFunctions = {}
myClientVarAndFunctions = Tunnel.getInterface(GetCurrentResourceName()) -- get your shared variable in your clint
Tunnel.bindInterface(GetCurrentResourceName(),myServerVarAndFunctions) -- put the shared variable this server file

function myServerVarAndFunctions.checkJob(job)
  local source = source
  local x,y,z = myClientVarAndFunctions.getCamDirection(source) -- GET CAM INFO BY CLIENT-SIDE
  print(x,y,z)
  if job then
    if QBCore.Functions.GetPlayer(source)?.PlayerData.job.name == job then
      return true, QBCore.Functions.GetPlayer(source)?.PlayerData.job.name
    else
      return false, QBCore.Functions.GetPlayer(source)?.PlayerData.job.name
    end
  end
end
```



## Example of usage: in your Client scripts add

```lua
local Tunnel = module("shared-libs","lib/Tunnel") -- add here in your Client
local Proxy = module("shared-libs","lib/Proxy") -- add here in your Client
local Tools = module("shared-libs","lib/Tools") -- add here in your Client

--local Tunnel = exports["shared-libs"]:myTunnel()

--local Tools = exports["shared-libs"]:myTools()
local QBCore = exports["qb-core"]:GetCoreObject()

myClientVarAndFunctions = {}
Tunnel.bindInterface(GetCurrentResourceName(),myClientVarAndFunctions)  -- put the shared variable this Client file
myServerVarAndFunctions = Tunnel.bindInterface(GetCurrentResourceName()) -- get your shared variable in your server

function myClientVarAndFunctions.getCamDirection()
  local heading = GetGameplayCamRelativeHeading()+GetEntityHeading(PlayerPedId())
  local pitch = GetGameplayCamRelativePitch()
  local x = -math.sin(heading*math.pi/180.0)
  local y = math.cos(heading*math.pi/180.0)
  local z = math.sin(pitch*math.pi/180.0)
  local len = math.sqrt(x*x+y*y+z*z)
    if len ~= 0 then
      x = x/len
      y = y/len
      z = z/len
    end
  return x,y,z
end

RegisterCommand('teste',function(source,args,rawCommand)
	
	 
local jobName = args[1]
print("Current Job:" myServerVarAndFunctions.checkJob(jobName)) -- GET JOB NAME BY SERVER-SIDE

end)
```





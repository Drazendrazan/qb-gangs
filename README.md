# 🔪 **qb-gangs**

replace all QBShared.Gangs with 
```
QBShared.Gangs = json.decode(LoadResourceFile("qb-gangs", "gangs.json"))
```

add to qb-core/server/events.lua this

```
RegisterServerEvent("QBCore:Server:UpdateGangs")
AddEventHandler("QBCore:Server:UpdateGangs", function(gangs)
	QBShared.Gangs = gangs
	QBCore.Shared.Gangs = gangs
end)
```

add event to qb-core/client/events.lua
```
RegisterNetEvent("QBCore:Server:UpdateGangs")
AddEventHandler("QBCore:Server:UpdateGangs", function(gangs)
	QBShared.Gangs = gangs
	QBCore.Shared.Gangs = gangs
end)
```

**after you need to update you qb-core with gangs but no grades**

#server/commands.lua

QBCore.Commands.Add('gang', 'Check Your Gang', {}, false, function(source)
    local src = source
    local PlayerGang = QBCore.Functions.GetPlayer(source).PlayerData.gang
    TriggerClientEvent('QBCore:Notify', src, string.format('[Gang]: %s', PlayerGang.label))
end, 'user')

QBCore.Commands.Add('setgang', 'Set A Players Gang (Admin Only)', { { name = 'id', help = 'Player ID' }, { name = 'gang', help = 'Name of a gang' }}, true, function(source, args)
    local src = source
    local Player = QBCore.Functions.GetPlayer(tonumber(args[1]))
    if Player then
        Player.Functions.SetGang(tostring(args[2]))
    else
        TriggerClientEvent('QBCore:Notify', src, 'Player Not Online', 'error')
    end
end, 'admin')

#server/playerlua replace this
```
PlayerData.gang = PlayerData.gang or {}
PlayerData.gang.name = PlayerData.gang.name or 'none'
PlayerData.gang.label = PlayerData.gang.label or 'No Gang Affiliaton'
PlayerData.gang.isboss = PlayerData.gang.isboss or false
PlayerData.gang.grade = PlayerData.gang.grade or {}
PlayerData.gang.grade.name = PlayerData.gang.grade.name or 'none'
PlayerData.gang.grade.level = PlayerData.gang.grade.level or 0
```
with
```
PlayerData.gang = PlayerData.gang ~= nil and PlayerData.gang or {}
PlayerData.gang.name = PlayerData.gang.name ~= nil and PlayerData.gang.name or "geen"
PlayerData.gang.label = PlayerData.gang.label ~= nil and PlayerData.gang.label or "Geen Gang"
```

**and**

```
                self.PlayerData.job.grade = {}
                self.PlayerData.job.grade.name = 'No Grades'
                self.PlayerData.job.grade.level = 0
                self.PlayerData.job.payment = 30
                self.PlayerData.job.isboss = false
```
with
```
		self.PlayerData.gang.name = gang
		self.PlayerData.gang.label = QBCore.Shared.Gangs[gang].label
		self.Functions.UpdatePlayerData()
```

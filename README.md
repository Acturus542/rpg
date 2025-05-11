local Fluent = loadstring(game:HttpGet("https://github.com/dawid-scripts/Fluent/releases/latest/download/main.lua"))()
local SaveManager = loadstring(game:HttpGet("https://raw.githubusercontent.com/dawid-scripts/Fluent/master/Addons/SaveManager.lua"))()
local InterfaceManager = loadstring(game:HttpGet("https://raw.githubusercontent.com/dawid-scripts/Fluent/master/Addons/InterfaceManager.lua"))()

local Window = Fluent:CreateWindow({
    Title = "Acturus " .. Fluent.Version,
    SubTitle = "Acturus",
    TabWidth = 160,
    Size = UDim2.fromOffset(580, 460),
    Acrylic = true, -- The blur may be detectable, setting this to false disables blur entirely
    Theme = "Dark",
    MinimizeKey = Enum.KeyCode.LeftControl -- Used when theres no MinimizeKeybind
})

--Fluent provides Lucide Icons https://lucide.dev/icons/ for the tabs, icons are optional
local Tabs = {
    Main = Window:AddTab({ Title = "War Tycoon", Icon = "" }),
    Settings = Window:AddTab({ Title = "Settings", Icon = "settings" })
}

local Options = Fluent.Options

do
    Fluent:Notify({
        Title = "Notification",
        Content = "This is a notification",
        SubContent = "SubContent", -- Optional
        Duration = 5 -- Set to nil to make the notification not disappear
    })



    local Toggle = Tabs.Main:AddToggle("MyToggle", {Title = "RPG Spam", Default = false })

    Toggle:OnChanged(function()
        local UserInputService = game:GetService("UserInputService")
    local camera = workspace.Camera
    local players = game:GetService("Players")
    local player = players.LocalPlayer
    local isFiring = false
    local toggleEnabled = false 
    local maxDistance = 3000 
    
    local function getTargets()
        local targets = {}
        for _, targetPlayer in pairs(players:GetPlayers()) do
            if targetPlayer ~= player and targetPlayer.Character and targetPlayer.Character:FindFirstChild("HumanoidRootPart") then
                local targetPos = targetPlayer.Character.HumanoidRootPart.Position
                if (targetPos - player.Character.HumanoidRootPart.Position).Magnitude <= maxDistance then
                    table.insert(targets, targetPlayer.Character.HumanoidRootPart)
                end
            end
        end
        return targets
    end
    
    local function fireInstantRocket()
        local character = player.Character
        if not character or not character:FindFirstChild("RPG") then return end
        local rpg = character.RPG
    
        local targets = getTargets()
        if #targets == 0 then return end 
    
        for _, target in pairs(targets) do
            local fireRocketVector = Vector3.new(0, -1, 0) 
            local fireRocketPosition = target.Position 
    
            game:GetService("ReplicatedStorage").RocketSystem.Events.FireRocket:InvokeServer(fireRocketVector, rpg, rpg, fireRocketPosition)
    
            local fireRocketClientTable = {
                ["expShake"] = {["fadeInTime"] = 0.05, ["magnitude"] = 3, ["rotInfluence"] = {0.4, 0, 0.4}, ["fadeOutTime"] = 0.5, ["posInfluence"] = {1, 1, 0}, ["roughness"] = 3},
                ["gravity"] = Vector3.new(0, -20, 0), ["HelicopterDamage"] = 99999, ["FireRate"] = 99999, ["VehicleDamage"] = 99999, ["ExpName"] = "RPG",
                ["RocketAmount"] = 999, ["ExpRadius"] = 50, ["BoatDamage"] = 99999, ["TankDamage"] = 99999, ["Acceleration"] = 999, ["ShieldDamage"] = 999999,
                ["Distance"] = 999999, ["PlaneDamage"] = 99999, ["GunshipDamage"] = 99999, ["velocity"] = 0, ["ExplosionDamage"] = 99999
            }
    
            local fireRocketClientInstance1 = game:GetService("ReplicatedStorage").RocketSystem.Rockets:FindFirstChild("RPG Rocket")
            game:GetService("ReplicatedStorage").RocketSystem.Events.FireRocketClient:Fire(
                fireRocketPosition, fireRocketVector, fireRocketClientTable, fireRocketClientInstance1, rpg, rpg, player, nil, { [1] = target }
            )
        end
    end
    
    local function startFiring()
        if isFiring then return end
        isFiring = true
    
        task.spawn(function()
            while isFiring do
                fireInstantRocket()
                task.wait(1 / 9999999) 
            end
        end)
    end
    
    local function stopFiring()
        isFiring = false
    end
    
    local function updateFiring()
        if toggleEnabled then
            startFiring()
        else
            stopFiring()
        end
    end
    
    UserInputService.InputBegan:Connect(function(input, gameProcessed)
        if input.KeyCode == Enum.KeyCode.T and not gameProcessed then
            startFiring()
        end
    end)
    
    UserInputService.InputEnded:Connect(function(input, gameProcessed)
        if input.KeyCode == Enum.KeyCode.T and not gameProcessed then
            updateFiring() 
        end
    end)
        print("rpg spam changed:", Options.MyToggle.Value)
    end)

    Options.MyToggle:SetValue(false)


    
    local Slider = Tabs.Main:AddSlider("Slider", {
        Title = "Walkspeed",
        Description = "это скорость бля",
        Default = 2,
        Min = 0,
        Max = 300,
        Rounding = 1,
        Callback = function(Value)
            game.Players.LocalPlayer.Character.Humanoid.WalkSpeed = value 
            print("walkspeed was changed:", Value)
        end
    })

    Slider:OnChanged(function(Value)
        print("Slider changed:", Value)
    end)

    Slider:SetValue(3)

    
    local Toggle = Tabs.Main:AddToggle("MyToggle", {Title = "Jav spam", Default = false })

    Toggle:OnChanged(function()
    local UserInputService = game:GetService("UserInputService")
    local camera = workspace.Camera
    local players = game:GetService("Players")
    local player = players.LocalPlayer
    local isFiring = false
    local toggleEnabled = false 
    local maxDistance = 3000 
    
    local function getTargets()
        local targets = {}
        for _, targetPlayer in pairs(players:GetPlayers()) do
            if targetPlayer ~= player and targetPlayer.Character and targetPlayer.Character:FindFirstChild("HumanoidRootPart") then
                local targetPos = targetPlayer.Character.HumanoidRootPart.Position
                if (targetPos - player.Character.HumanoidRootPart.Position).Magnitude <= maxDistance then
                    table.insert(targets, targetPos) 
                end
            end
        end
        return targets
    end
    
    local function fireQuickLagRocket()
        local character = player.Character
        if not character or not character:FindFirstChild("Javelin") then return end
        local javelin = character.Javelin
    
        local targets = getTargets()
        if #targets == 0 then return end 
    
        for _, targetPos in pairs(targets) do
            local fireRocketVector = (targetPos - camera.CFrame.Position).Unit
            local fireRocketPosition = camera.CFrame.Position
    
            game:GetService("ReplicatedStorage").RocketSystem.Events.FireRocket:InvokeServer(fireRocketVector, javelin, javelin, fireRocketPosition)
    
            local fireRocketClientTable = {
                ["expShake"] = {["fadeInTime"] = 0.05, ["magnitude"] = 3, ["rotInfluence"] = {0.4, 0, 0.4}, ["fadeOutTime"] = 0.5, ["posInfluence"] = {1, 1, 0}, ["roughness"] = 3},
                ["gravity"] = Vector3.new(0, -20, 0), ["HelicopterDamage"] = 450, ["FireRate"] = 15, ["VehicleDamage"] = 350, ["ExpName"] = "Javelin",
                ["RocketAmount"] = 333, ["ExpRadius"] = 12, ["BoatDamage"] = 300, ["TankDamage"] = 300, ["Acceleration"] = 8, ["ShieldDamage"] = 11170,
                ["Distance"] = 400000, ["PlaneDamage"] = 500, ["GunshipDamage"] = 170, ["velocity"] = 99999, ["ExplosionDamage"] = 120
            }
    
            local fireRocketClientInstance1 = game:GetService("ReplicatedStorage").RocketSystem.Rockets:FindFirstChild("Javelin G-Rocket")
            game:GetService("ReplicatedStorage").RocketSystem.Events.FireRocketClient:Fire(
                fireRocketPosition, fireRocketVector, fireRocketClientTable, fireRocketClientInstance1, javelin, javelin, player, nil, { [1] = camera:FindFirstChild("Javelin") }
            )
        end
    end
    
    local function startFiring()
        if isFiring then return end
        isFiring = true
    
        task.spawn(function()
            while isFiring do
                fireQuickLagRocket()
                task.wait(1 / 9999999) 
            end
        end)
    end
    
    local function stopFiring()
        isFiring = false
    end
    
    local function updateFiring()
        if toggleEnabled then
            startFiring()
        else
            stopFiring()
        end
    end
    
    UserInputService.InputBegan:Connect(function(input, gameProcessed)
        if input.KeyCode == Enum.KeyCode.T and not gameProcessed then
            startFiring()
        end
    end)
    
    UserInputService.InputEnded:Connect(function(input, gameProcessed)
        if input.KeyCode == Enum.KeyCode.T and not gameProcessed then
            updateFiring() 
        end
    end)
        print("jav click changed:", Options.MyToggle.Value)
    end)

    Options.MyToggle:SetValue(false)

    local Toggle = Tabs.Main:AddToggle("MyToggle", {Title = "Javelin Click", Default = false })
    Toggle:OnChanged(function()
        local UserInputService = game:GetService("UserInputService")
        local camera = workspace.Camera
        local player = game:GetService("Players").LocalPlayer
        local isFiring = false
        local toggleEnabled = false 
        
        local function fireRocket()
            if not toggleEnabled then return end 
        
            local character = player.Character
            if not character or not character:FindFirstChild("Javelin") then return end
        
            local javelin = character.Javelin
            local mouse = player:GetMouse()
            if not mouse or not mouse.Hit then return end
        
            local targetPosition = mouse.Hit.Position 
            local fireRocketVector = (targetPosition - camera.CFrame.Position).Unit
        
            game:GetService("ReplicatedStorage").RocketSystem.Events.FireRocket:InvokeServer(fireRocketVector, javelin, javelin, targetPosition)
        
            local fireRocketClientTable = {
                ["expShake"] = {["fadeInTime"] = 0.05, ["magnitude"] = 3, ["rotInfluence"] = {0.4, 0, 0.4}, ["fadeOutTime"] = 0.5, ["posInfluence"] = {1, 1, 0}, ["roughness"] = 3},
                ["gravity"] = Vector3.new(0, -20, 0), ["HelicopterDamage"] = 450, ["FireRate"] = 15, ["VehicleDamage"] = 350, ["ExpName"] = "Javelin",
                ["RocketAmount"] = 333, ["ExpRadius"] = 12, ["BoatDamage"] = 300, ["TankDamage"] = 300, ["Acceleration"] = 8, ["ShieldDamage"] = 11170,
                ["Distance"] = 400000, ["PlaneDamage"] = 500, ["GunshipDamage"] = 170, ["velocity"] = 99999, ["ExplosionDamage"] = 120
            }
        
            local fireRocketClientInstance1 = game:GetService("ReplicatedStorage").RocketSystem.Rockets:FindFirstChild("Javelin G-Rocket")
            game:GetService("ReplicatedStorage").RocketSystem.Events.FireRocketClient:Fire(
                targetPosition, fireRocketVector, fireRocketClientTable, fireRocketClientInstance1, javelin, javelin, player, nil, { [1] = camera:FindFirstChild("Javelin") }
            )
        end
        
        local function startFiring()
            if isFiring or not toggleEnabled then return end
            isFiring = true
        
            task.spawn(function()
                while isFiring do
                    fireRocket()
                    task.wait(1 / 9999999) 
                end
            end)
        end
        
        local function stopFiring()
            isFiring = false
        end
        
        Toggle:OnChanged(function(value)
            toggleEnabled = value
            stopFiring()
        end)    
        
        UserInputService.InputBegan:Connect(function(input, gameProcessed)
            if input.KeyCode == Enum.KeyCode.T and not gameProcessed and toggleEnabled then
                startFiring()
            end
        end)
        
        UserInputService.InputEnded:Connect(function(input, gameProcessed)
            if input.KeyCode == Enum.KeyCode.T and not gameProcessed then
                stopFiring()
            end
        end)
        
        
        local Toggle = Tabs.Main:AddToggle("RPG Click", {Title = "RPG Click", Default = false})
        local UserInputService = game:GetService("UserInputService")
        local camera = workspace.Camera
        local player = game:GetService("Players").LocalPlayer
        local isFiring = false
        local toggleEnabled = false 
        
        local function fireRocket()
            if not toggleEnabled then return end 
        
            local character = player.Character
            if not character or not character:FindFirstChild("RPG") then return end
        
            local rpg = character.RPG 
        
            local mouse = player:GetMouse()
            if not mouse or not mouse.Hit then return end
        
            local targetPosition = mouse.Hit.Position 
            local fireRocketVector = (targetPosition - camera.CFrame.Position).Unit
        
            game:GetService("ReplicatedStorage").RocketSystem.Events.FireRocket:InvokeServer(fireRocketVector, rpg, rpg, targetPosition)
        
            local fireRocketClientTable = {
                ["expShake"] = {["fadeInTime"] = 0.05, ["magnitude"] = 3, ["rotInfluence"] = {0.4, 0, 0.4}, ["fadeOutTime"] = 0.5, ["posInfluence"] = {1, 1, 0}, ["roughness"] = 3},
                ["gravity"] = Vector3.new(0, -20, 0), ["HelicopterDamage"] = 450, ["FireRate"] = 15, ["VehicleDamage"] = 350, ["ExpName"] = "RPG",
                ["RocketAmount"] = 333, ["ExpRadius"] = 12, ["BoatDamage"] = 300, ["TankDamage"] = 300, ["Acceleration"] = 8, ["ShieldDamage"] = 11170,
                ["Distance"] = 400000, ["PlaneDamage"] = 500, ["GunshipDamage"] = 170, ["velocity"] = 99999, ["ExplosionDamage"] = 120
            }
        
            local fireRocketClientInstance1 = game:GetService("ReplicatedStorage").RocketSystem.Rockets:FindFirstChild("RPG Rocket")
            game:GetService("ReplicatedStorage").RocketSystem.Events.FireRocketClient:Fire(
                targetPosition, fireRocketVector, fireRocketClientTable, fireRocketClientInstance1, rpg, rpg, player, nil, { [1] = camera:FindFirstChild("RPG") }
            )
        end
        
        local function startFiring()
            if isFiring or not toggleEnabled then return end
            isFiring = true
        
            task.spawn(function()
                while isFiring do
                    fireRocket()
                    task.wait(1 / 9999999) 
                end
            end)
        end
        
        local function stopFiring()
            isFiring = false
        end
        
        Toggle:OnChanged(function(value)
            toggleEnabled = value
            stopFiring()
        end)
        
        UserInputService.InputBegan:Connect(function(input, gameProcessed)
            if input.KeyCode == Enum.KeyCode.T and not gameProcessed and toggleEnabled then
                startFiring()
            end
        end)
        
        UserInputService.InputEnded:Connect(function(input, gameProcessed)
            if input.KeyCode == Enum.KeyCode.T and not gameProcessed then
                stopFiring()
            end
        end)
        print("rpg click changed:", Options.MyToggle.Value)
    end)

    Options.MyToggle:SetValue(false)

    local Toggle = Tabs.Main:AddToggle("Stinger Click", {Title = "Stinger Click", Default = false})
local isFiring = false
local toggleEnabled = false
local weaponName = "Stinger"  
local FireRocketEvent = game:GetService("ReplicatedStorage").RocketSystem.Events.FireRocket
local RocketHitEvent = game:GetService("ReplicatedStorage").RocketSystem.Events.RocketHit
local Camera = workspace.CurrentCamera
local LocalPlayer = game.Players.LocalPlayer
local UserInputService = game:GetService("UserInputService")

local function fireRocket()
    if not toggleEnabled then return end  

    local Weapon = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild(weaponName) 
        or LocalPlayer.Backpack:FindFirstChild(weaponName)
    if not Weapon then return end

    local Mouse = LocalPlayer:GetMouse()
    local TargetPosition = Mouse.Hit.Position

    Weapon.Parent = LocalPlayer.Character

    local RocketID = tostring(FireRocketEvent:InvokeServer(Camera.CFrame.LookVector, Weapon, Weapon, TargetPosition))

    if Mouse.Target then
        local HitArgs = {
            [1] = TargetPosition,   
            [2] = Vector3.new(0, 0.1, 0),
            [3] = Weapon,           
            [4] = Weapon,           
            [5] = Mouse.Target,     
            [7] = LocalPlayer.Name .. "Rocket" .. RocketID 
        }

        RocketHitEvent:FireServer(unpack(HitArgs))
    end

    Weapon.Parent = LocalPlayer.Backpack
end

local function startFiring()
    if isFiring or not toggleEnabled then return end
    isFiring = true

    task.spawn(function()
        while isFiring do
            fireRocket()
            task.wait(1 / 999999)  
        end
    end)
end

local function stopFiring()
    isFiring = false
end

local function updateFiring()
    if toggleEnabled then
        stopFiring()
    end
end

Toggle:OnChanged(function(value)
    toggleEnabled = value
    updateFiring()
end)

UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if input.KeyCode == Enum.KeyCode.T and not gameProcessed and toggleEnabled then
        startFiring()
    end
end)

UserInputService.InputEnded:Connect(function(input, gameProcessed)
    if input.KeyCode == Enum.KeyCode.T and not gameProcessed then
        stopFiring()
    end
end)



-- Addons:
-- SaveManager (Allows you to have a configuration system)
-- InterfaceManager (Allows you to have a interface managment system)

-- Hand the library over to our managers
SaveManager:SetLibrary(Fluent)
InterfaceManager:SetLibrary(Fluent)

-- Ignore keys that are used by ThemeManager.
-- (we dont want configs to save themes, do we?)
SaveManager:IgnoreThemeSettings()

-- You can add indexes of elements the save manager should ignore
SaveManager:SetIgnoreIndexes({})

-- use case for doing it this way:
-- a script hub could have themes in a global folder
-- and game configs in a separate folder per game
InterfaceManager:SetFolder("FluentScriptHub")
SaveManager:SetFolder("FluentScriptHub/specific-game")

InterfaceManager:BuildInterfaceSection(Tabs.Settings)
SaveManager:BuildConfigSection(Tabs.Settings)


Window:SelectTab(1)

Fluent:Notify({
    Title = "Acturus",
    Content = "script has been loaded.",
    Duration = 8
})
end

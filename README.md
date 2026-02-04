-- AHG HUB - YOU VS HOMER [V1] - (Créditos ao Joãozinho por ter me ajudado no script)

local OrionLib = loadstring(game:HttpGet(('https://raw.githubusercontent.com/Seven7-lua/Roblox/refs/heads/main/Librarys/Orion/Orion.lua')))()

-- Bypass anti-cheat local
local ps = game:GetService("Players").LocalPlayer.PlayerScripts
local ACtable = {  
    ps:FindFirstChild("QuitsAntiCheatChecker"),  
    ps:FindFirstChild("QuitsAntiCheatLocal")  
}  
for _, v in pairs(ACtable) do  
    if v then v:Destroy() end  
end

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local Lighting = game:GetService("Lighting")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera

-- Configs
local ESPEnabled = { Bart = false, Homer = false }
local ESPSettings = {
    Bart = { Outline = false, Box = false, Name = false, Tracers = false, Color = Color3.fromRGB(41, 237, 19) },
    Homer = { Outline = false, Box = false, Name = false, Tracers = false, Color = Color3.fromRGB(227, 11, 11) }
}
local ESPObjects = {}

local WallhopEnabled = false
local WallhopSettings = { JumpHeight = 52, DetectDist = 4.8, FlickAngle = 45, RecoverySpeed = 0.15 }
local lastJump = 0

local FullbrightEnabled = false
local SprintEnabled = false
local SprintSpeed = 22.4

local AutoFarmEnabled = false
local AutoKillEnabled = false

local originalLighting = {
    Brightness = Lighting.Brightness,
    Ambient = Lighting.Ambient,
    OutdoorAmbient = Lighting.OutdoorAmbient,
    ClockTime = Lighting.ClockTime,
    FogEnd = Lighting.FogEnd,
    GlobalShadows = Lighting.GlobalShadows
}

local function GetPlayerTeam(player)
    if not player or not player.Team or not player.Team.Name then return nil end
    local tn = player.Team.Name:lower()
    if tn:find("bart") then return "Bart" end
    if tn:find("homer") then return "Homer" end
    return nil
end

local function CreateESP(player)
    if ESPObjects[player] then return end
    ESPObjects[player] = { Outline = nil, Box = {}, Name = nil, Tracer = nil }
end

local function RemoveESP(player)
    local esp = ESPObjects[player]
    if not esp then return end
    if esp.Outline then pcall(function() esp.Outline:Destroy() end) end
    for _, line in pairs(esp.Box) do pcall(function() line:Remove() end) end
    if esp.Name then pcall(function() esp.Name:Remove() end) end
    if esp.Tracer then pcall(function() esp.Tracer:Remove() end) end
    ESPObjects[player] = nil
end

local function UpdateESP()
    for _, player in ipairs(Players:GetPlayers()) do
        if player == LocalPlayer then continue end
        
        local char = player.Character
        if not char or not char:FindFirstChild("Humanoid") or char.Humanoid.Health <= 0 or
           not char:FindFirstChild("HumanoidRootPart") or not char:FindFirstChild("Head") then
            RemoveESP(player)
            continue
        end
        
        local team = GetPlayerTeam(player)
        if not team or not ESPEnabled[team] then
            RemoveESP(player)
            continue
        end
        
        CreateESP(player)
        local esp = ESPObjects[player]
        local root = char.HumanoidRootPart
        local head = char.Head
        local settings = ESPSettings[team]
        local color = settings.Color
        
        local rootPos, onScreen = Camera:WorldToViewportPoint(root.Position)
        if not onScreen then
            if esp.Outline then esp.Outline.Enabled = false end
            for _, line in pairs(esp.Box) do line.Visible = false end
            if esp.Name then esp.Name.Visible = false end
            if esp.Tracer then esp.Tracer.Visible = false end
            continue
        end
        
        if settings.Outline then
            if not esp.Outline then
                esp.Outline = Instance.new("Highlight")
                esp.Outline.Parent = char
                esp.Outline.Adornee = char
                esp.Outline.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
                esp.Outline.FillTransparency = 1
                esp.Outline.OutlineTransparency = 0.5
            end
            esp.Outline.OutlineColor = color
            esp.Outline.Enabled = true
        elseif esp.Outline then
            esp.Outline.Enabled = false
        end
        
        if settings.Box then
            if #esp.Box == 0 then
                for i = 1, 4 do
                    local line = Drawing.new("Line")
                    line.Thickness = 2
                    line.Transparency = 1
                    esp.Box[i] = line
                end
            end
            local headPos = Camera:WorldToViewportPoint(head.Position + Vector3.new(0, 0.5, 0))
            local legPos = Camera:WorldToViewportPoint(root.Position - Vector3.new(0, 3, 0))
            local height = math.abs(headPos.Y - legPos.Y)
            local width = height * 0.5
            local lines = esp.Box
            lines[1].From = Vector2.new(rootPos.X - width, headPos.Y)
            lines[1].To = Vector2.new(rootPos.X + width, headPos.Y)
            lines[2].From = Vector2.new(rootPos.X - width, legPos.Y)
            lines[2].To = Vector2.new(rootPos.X + width, legPos.Y)
            lines[3].From = Vector2.new(rootPos.X - width, headPos.Y)
            lines[3].To = Vector2.new(rootPos.X - width, legPos.Y)
            lines[4].From = Vector2.new(rootPos.X + width, headPos.Y)
            lines[4].To = Vector2.new(rootPos.X + width, legPos.Y)
            for _, line in pairs(lines) do
                line.Color = color
                line.Visible = true
            end
        elseif #esp.Box > 0 then
            for _, line in pairs(esp.Box) do line.Visible = false end
        end
        
        local distance = math.floor((LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") and 
                                     (root.Position - LocalPlayer.Character.HumanoidRootPart.Position).Magnitude) or 0)
        if settings.Name then
            if not esp.Name then
                esp.Name = Drawing.new("Text")
                esp.Name.Center = true
                esp.Name.Outline = true
                esp.Name.Size = 13
            end
            local headPos = Camera:WorldToViewportPoint(head.Position + Vector3.new(0, 1.5, 0))
            esp.Name.Text = player.Name .. " | " .. distance .. "M"
            esp.Name.Position = Vector2.new(headPos.X, headPos.Y)
            esp.Name.Color = color
            esp.Name.Visible = true
        elseif esp.Name then
            esp.Name.Visible = false
        end
        
        if settings.Tracers then
            if not esp.Tracer then
                esp.Tracer = Drawing.new("Line")
                esp.Tracer.Thickness = 1
                esp.Tracer.Transparency = 1
            end
            esp.Tracer.From = Vector2.new(Camera.ViewportSize.X / 2, Camera.ViewportSize.Y)
            esp.Tracer.To = Vector2.new(rootPos.X, rootPos.Y)
            esp.Tracer.Color = color
            esp.Tracer.Visible = true
        elseif esp.Tracer then
            esp.Tracer.Visible = false
        end
    end
end

RunService.RenderStepped:Connect(function()
    pcall(UpdateESP)
end)

Players.PlayerRemoving:Connect(RemoveESP)

-- TAGS DE RANK (OWNER e VIP)
local WhitelistTags = {
    ["PokitoJR"] = {tag = "OWNER", color = Color3.fromHex("#1b0cf0")},
    ["joaaa012333"] = {tag = "OWNER", color = Color3.fromHex("#1b0cf0")},
    -- VIPs (vazio por enquanto)
}

local function AddRankTag(player)
    if player == LocalPlayer then return end

    local tagData = WhitelistTags[player.Name] or WhitelistTags[player.UserId]
    if not tagData then return end

    local function applyTag(char)
        local head = char:WaitForChild("Head", 5)
        if not head then return end

        local billboard = head:FindFirstChild("RankTagGui") or Instance.new("BillboardGui")
        billboard.Name = "RankTagGui"
        billboard.Adornee = head
        billboard.Size = UDim2.new(0, 220, 0, 80)
        billboard.StudsOffset = Vector3.new(0, 4, 0)
        billboard.AlwaysOnTop = true
        billboard.ResetOnSpawn = false
        billboard.Parent = head

        local nameLabel = billboard:FindFirstChild("NameLabel") ") or Instance.new("TextLabel")
        nameLabel.Name = "NameLabel"
        nameLabel.Parent = billboard
        nameLabel.BackgroundTransparency = 1
        nameLabel.Size = UDim2.new(1, 0, 0.5, 0)
        nameLabel.Position = UDim2.new(0, 0, 0, 0)
        nameLabel.Text = player.Name
        nameLabel.TextColor3 = tagData.color
        nameLabel.TextStrokeTransparency = 0.6
        nameLabel.TextScaled = true
        nameLabel.Font = Enum.Font.GothamBold
        nameLabel.TextXAlignment = Enum.TextXAlignment.Center

        local tagLabel = billboard:FindFirstChild("TagLabel") or Instance.new("TextLabel")
        tagLabel.Name = "TagLabel"
        tagLabel.Parent = billboard
        tagLabel.BackgroundTransparency = 1
        tagLabel.Size = UDim2.new(1, 0, 0.5, 0)
        tagLabel.Position = UDim2.new(0, 0, 0.5, 0)
        tagLabel.Text = tagData.tag
        tagLabel.TextColor3 = tagData.color
        tagLabel.TextStrokeTransparency = 0.6
        tagLabel.TextScaled = true
        tagLabel.Font = Enum.Font.GothamSemibold
        tagLabel.TextXAlignment = Enum.TextXAlignment.Center
    end

    if player.Character then
        applyTag(player.Character)
    end
    player.CharacterAdded:Connect(applyTag)
end

for _, player in ipairs(Players:GetPlayers()) do
    AddRankTag(player)
end
Players.PlayerAdded:Connect(AddRankTag)

 local function ExecutarRitualHomer()
    local char = LocalPlayer.Character
    local root = char and char:FindFirstChild("HumanoidRootPart")
    local hum = char and char:FindFirstChild("Humanoid")
    if not (root and hum) then return end

    task.spawn(function()
        local bg = Instance.new("BodyAngularVelocity")
        bg.AngularVelocity = Vector3.new(0, 10, 0)
        bg.MaxTorque = Vector3.new(0, 9e9, 0)
        bg.Parent = root

        task.wait(1)
        bg.AngularVelocity = Vector3.new(0, 50, 0)

        local bv = Instance.new("BodyVelocity")
        bv.Velocity = Vector3.new(0, 15, 0)
        bv.MaxForce = Vector3.new(0, 9e9, 0)
        bv.Parent = root

        task.wait(2)
        bv:Destroy()
        bg:Destroy()
        hum.Health = 0
    end)
end

-- DisableFullbright
local function DisableFullbright()
    Lighting.Brightness = originalLighting.Brightness
    Lighting.Ambient = originalLighting.Ambient
    Lighting.OutdoorAmbient = originalLighting.OutdoorAmbient
    Lighting.ClockTime = originalLighting.ClockTime
    Lighting.FogEnd = originalLighting.FogEnd
    Lighting.GlobalShadows = originalLighting.GlobalShadows
end

-- Wallhop
local function getWallInfo()
    local char = LocalPlayer.Character
    local root = char and char:FindFirstChild("HumanoidRootPart")
    if not root then return nil, nil end
    
    local params = RaycastParams.new()
    params.FilterDescendantsInstances = {char}
    params.FilterType = Enum.RaycastFilterType.Exclude
    
    local directions = {
        ["F"] = root.CFrame.LookVector,
        ["L"] = -root.CFrame.RightVector,
        ["R"] = root.CFrame.RightVector,
        ["B"] = -root.CFrame.LookVector
    }
    
    for side, dir in pairs(directions) do
        local result = workspace:Raycast(root.Position, dir * WallhopSettings.DetectDist, params)
        if result and result.Instance and result.Instance.CanCollide then
            return result.Normal, side
        end
    end
    return nil, nil
end

local function applyFlick(root, side)
    local angle = math.rad(WallhopSettings.FlickAngle)
    if side == "B" then angle = -angle * 1.2 end
    if side == "R" then angle = -angle end
    
    root.CFrame = root.CFrame * CFrame.Angles(0, angle, 0)
    
    task.spawn(function()
        task.wait(0.05)
        local start = tick()
        while tick() - start < WallhopSettings.RecoverySpeed do
            local look = Camera.CFrame.LookVector
            local target = CFrame.new(root.Position, root.Position + Vector3.new(look.X, 0, look.Z))
            root.CFrame = root.CFrame:Lerp(target, 0.18)
            RunService.RenderStepped:Wait()
        end
    end)
end

UserInputService.JumpRequest:Connect(function()
    if not WallhopEnabled then return end
    local char = LocalPlayer.Character
    local root = char and char:FindFirstChild("HumanoidRootPart")
    local hum = char and char:FindFirstChild("Humanoid")
    if not (root and hum) or (tick() - lastJump < 0.18) then return end
    local normal, side = getWallInfo()
    if normal then
        lastJump = tick()
        hum:ChangeState(Enum.HumanoidStateType.Jumping)
        root.Velocity = Vector3.new(root.Velocity.X, WallhopSettings.JumpHeight, root.Velocity.Z)
        applyFlick(root, side)
    end
end)

UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if gameProcessed or not WallhopEnabled then return end
    if input.KeyCode == Enum.KeyCode.B then
        local char = LocalPlayer.Character
        local hum = char and char:FindFirstChild("Humanoid")
        if hum and hum:GetState() == Enum.HumanoidStateType.Jumping then
            local normal, side = getWallInfo()
            if normal and side == "B" then
                local root = char.HumanoidRootPart
                lastJump = tick()
                hum:ChangeState(Enum.HumanoidStateType.Jumping)
                root.Velocity = Vector3.new(root.Velocity.X * 0.8, WallhopSettings.JumpHeight * 1.1, root.Velocity.Z * 0.8)
                applyFlick(root, "B")
            end
        end
    end
end)

-- Auto Farm
local FarmPos = CFrame.new(-32, 232, 110)
local function startAutoFarm()
    task.spawn(function()
        while AutoFarmEnabled do
            local char = LocalPlayer.Character
            if char and char:FindFirstChild("HumanoidRootPart") then
                char.HumanoidRootPart.CFrame = FarmPos
                task.wait(0.3)
            end
            task.wait(5.3)
        end
    end)
end

-- Auto Kill
local ignoredPlayers = {"packj0", "kit_cynalt", "bk_faxbr"}
local function startAutoKill()
    task.spawn(function()
        while AutoKillEnabled do
            if not LocalPlayer.Team or LocalPlayer.Team.Name:lower():sub(1,1) ~= "h" then
                AutoKillEnabled = false
                OrionLib:MakeNotification({Name = "AHG HUB", Content = "Precisa estar no time Homer!", Time = 5})
                break
            end
            local char = LocalPlayer.Character
            if not char or not char:FindFirstChild("HumanoidRootPart") then task.wait(0.5) continue end
            local hrp = char.HumanoidRootPart
            local oldCFrame = hrp.CFrame
            for _, plr in ipairs(Players:GetPlayers()) do
                if plr ~= LocalPlayer and plr.Team and plr.Team.Name:lower():sub(1,1) == "b" and
                   plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") then
                    local ln = plr.Name:lower()
                    if not table.find(ignoredPlayers, ln) then
                        hrp.CFrame = CFrame.new(plr.Character.HumanoidRootPart.Position + Vector3.new(0, 1, 0))
                        task.wait(0.08)
                    end
                end
            end
            hrp.CFrame = oldCFrame
            task.wait(0.4)
        end
    end)
end

-- Loop principal
RunService.Heartbeat:Connect(function()
    local char = LocalPlayer.Character
    if not char or not char:FindFirstChild("Humanoid") then return end
    local hum = char.Humanoid
    hum.WalkSpeed = SprintEnabled and SprintSpeed or 16
    if FullbrightEnabled then
        Lighting.Brightness = 2
        Lighting.ClockTime = 14
        Lighting.FogEnd = 100000
        Lighting.GlobalShadows = false
        Lighting.OutdoorAmbient = Color3.fromRGB(128, 128, 128)
        Lighting.Ambient = Color3.fromRGB(178, 178, 178)
    end
end)

-- UI
local Window = OrionLib:MakeWindow({
    Name = "AHG HUB - YOU VS HOMER [V1]",
    HidePremium = false,
    SaveConfig = true,
    ConfigFolder = "AHGHubYouVsHomerV1",
    IntroEnabled = false
})

local TabESP = Window:MakeTab({Name = "ESP", Icon = "rbxassetid://4483345998", PremiumOnly = false})

TabESP:AddSection({Name = "ESP Homer"})
TabESP:AddToggle({Name = "Ativar ESP Homer", Default = false, Callback = function(v) ESPEnabled.Homer = v end})
TabESP:AddToggle({Name = "Outline", Default = false, Callback = function(v) ESPSettings.Homer.Outline = v end})
TabESP:AddToggle({Name = "Box", Default = false, Callback = function(v) ESPSettings.Homer.Box = v end})
TabESP:AddToggle({Name = "Name", Default = false, Callback = function(v) ESPSettings.Homer.Name = v end})
TabESP:AddToggle({Name = "Tracers", Default = false, Callback = function(v) ESPSettings.Homer.Tracers = v end})
TabESP:AddColorpicker({
    Name = "Cor Homer",
    Default = Color3.fromRGB(227, 11, 11),
    Flag = "HomerESPColor",
    Callback = function(Value)
        ESPSettings.Homer.Color = Value
    end
})

TabESP:AddSection({Name = "ESP Bart"})
TabESP:AddToggle({Name = "Ativar ESP Bart", Default = false, Callback = function(v) ESPEnabled.Bart = v end})
TabESP:AddToggle({Name = "Outline", Default = false, Callback = function(v) ESPSettings.Bart.Outline = v end})
TabESP:AddToggle({Name = "Box", Default = false, Callback = function(v) ESPSettings.Bart.Box = v end})
TabESP:AddToggle({Name = "Name", Default = false, Callback = function(v) ESPSettings.Bart.Name = v end})
TabESP:AddToggle({Name = "Tracers", Default = false, Callback = function(v) ESPSettings.Bart.Tracers = v end})
TabESP:AddColorpicker({
    Name = "Cor Bart",
    Default = Color3.fromRGB(41, 237, 19),
    Flag = "BartESPColor",
    Callback = function(Value)
        ESPSettings.Bart.Color = Value
    end
})

local TabHacks = Window:MakeTab({Name = "HACKS", Icon = "rbxassetid://4483345998", PremiumOnly = false})

TabHacks:AddToggle({Name = "Wallhop", Default = false, Callback = function(v) WallhopEnabled = v end})
TabHacks:AddToggle({Name = "Fullbright", Default = false, Callback = function(v) FullbrightEnabled = v if not v then DisableFullbright() end end})
TabHacks:AddToggle({Name = "Sprint", Default = false, Callback = function(v) SprintEnabled = v end})
TabHacks:AddToggle({Name = "Auto Farm (Obby Hard)", Default = false, Callback = function(v) AutoFarmEnabled = v if v then startAutoFarm() end end})
TabHacks:AddToggle({Name = "Auto Kill (Homer → Barts)", Default = false, Callback = function(v) AutoKillEnabled = v if v then startAutoKill() end end})

TabHacks:AddButton({
    Name = "Ritual Homer",
    Callback = function()
        ExecutarRitualHomer()
    end
})

        local function ExecutarRitualHomer()
    local char = LocalPlayer.Character
    local root = char and char:FindFirstChild("HumanoidRootPart")
    local hum = char and char:FindFirstChild("Humanoid")
    if not (root and hum) then return end

    task.spawn(function()
        local bg = Instance.new("BodyAngularVelocity")
        bg.AngularVelocity = Vector3.new(0, 10, 0)
        bg.MaxTorque = Vector3.new(0, 9e9, 0)
        bg.Parent = root

        task.wait(1)
        bg.AngularVelocity = Vector3.new(0, 50, 0)

        local bv = Instance.new("BodyVelocity")
        bv.Velocity = Vector3.new(0, 15, 0)
        bv.MaxForce = Vector3.new(0, 9e9, 0)
        bv.Parent = root

        task.wait(2)
        bv:Destroy()
        bg:Destroy()
        hum.Health = 0
    end)
end
            

-- Aba FUNÇÕES VIP (visível para VIP ou OWNER)
local VIPList = {
    ["PokitoJR"] = true,
    ["joaaa012333"] = true,
    -- Adicione VIPs aqui quando quiser
}

local isVIP = VIPList[LocalPlayer.Name] or VIPList[tostring(LocalPlayer.UserId)]

if isVIP then
    local TabVIP = Window:MakeTab({
        Name = "FUNÇÕES VIP",
        Icon = "rbxassetid://4483345998",
        PremiumOnly = false
    })

    -- X-Ray (VIP) - versão leve
    local XRayEnabled = false
    TabVIP:AddToggle({
        Name = "X-Ray",
        Default = false,
        Callback = function(v)
            XRayEnabled = v
            if v then
                for _, part in ipairs(workspace:GetDescendants()) do
                    if part:IsA("BasePart") and part.CanCollide and part.Transparency < 0.9 and part ~= LocalPlayer.Character then
                        part.LocalTransparencyModifier = 0.7
                    end
                end
            else
                for _, part in ipairs(workspace:GetDescendants()) do
                    if part:IsA("BasePart") then
                        part.LocalTransparencyModifier = 0
                    end
                end
            end
        end
    })

    -- Sprint OP 35 (VIP)
    TabVIP:AddToggle({
        Name = "Sprint OP (35)",
        Default = false,
        Callback = function(v)
            if v then
                if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
                    LocalPlayer.Character.Humanoid.WalkSpeed = 35
                end
            else
                if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
                    LocalPlayer.Character.Humanoid.WalkSpeed = 16
                end
            end
        end
    })

    -- Noclip (VIP)
    local NoclipConnection
    TabVIP:AddToggle({
        Name = "Noclip",
        Default = false,
        Callback = function(v)
            if v then
                NoclipConnection = RunService.Stepped:Connect(function()
                    if LocalPlayer.Character then
                        for _, part in ipairs(LocalPlayer.Character:GetDescendants()) do
                            if part:IsA("BasePart") then
                                part.CanCollide = false
                            end
                        end
                    end
                end)
            else
                if NoclipConnection then
                    NoclipConnection:Disconnect()
                    NoclipConnection = nil
                end
                if LocalPlayer.Character then
                    for _, part in ipairs(LocalPlayer.Character:GetDescendants()) do
                        if part:IsA("BasePart") then
                            part.CanCollide = true
                        end
                    end
                end
            end
        end
    })

    TabVIP:AddParagraph("Aviso VIP", "Essas funções são exclusivas para VIPs/OWNER.\nUse com cuidado!")
end

-- Aba SKINS (VIP) - só para VIP/OWNER
if isVIP then
    local TabSkins = Window:MakeTab({
        Name = "Skins (VIP)",
        Icon = "rbxassetid://4483345998",
        PremiumOnly = false
    })

    TabSkins:AddParagraph("Aviso Importante", "Risco de ban! Desative medalhas e oculte conquistas antes de usar!\nNão nos responsabilizamos por banimentos.")

    -- Função auxiliar buySkin
    local function buySkin(character, skinName, price)
        game:GetService('ReplicatedStorage'):WaitForChild('eventFolders')
            :WaitForChild('store'):WaitForChild('buyItem'):FireServer(character, skinName, price)
        task.wait(0.05)
        game:GetService('ReplicatedStorage'):WaitForChild('eventFolders')
            :WaitForChild('store'):WaitForChild('changeSkin'):FireServer(character, skinName)
    end

    -- Seção Bart
    TabSkins:AddSection({Name = "Bart Skins"})

    TabSkins:AddButton({
        Name = "Comprar 1M Bart (desative medalhas!)",
        Callback = function()
            local args_comprar = {"Bart", "1M", 1}
            game:GetService("ReplicatedStorage"):WaitForChild("eventFolders"):WaitForChild("store"):WaitForChild("buyItem"):FireServer(unpack(args_comprar))
            task.wait(1)
            local args_equipar = {"Bart", "1M"}
            game:GetService("ReplicatedStorage"):WaitForChild("eventFolders"):WaitForChild("store"):WaitForChild("changeSkin"):FireServer(unpack(args_equipar))
            OrionLib:MakeNotification({Name = "Sucesso!", Content = "Skin 1M Bart comprada e equipada!", Time = 3})
        end
    })

    TabSkins:AddButton({
        Name = "Comprar Zé Pilantra (risco de ban)",
        Callback = function()
            local storeRemote = game:GetService("ReplicatedStorage"):WaitForChild("eventFolders"):WaitForChild("store")
            local buyArgs = {"Bart", "CPT"}
            storeRemote:WaitForChild("buyItem"):FireServer(unpack(buyArgs))
            task.wait(1.2)
            local equipArgs = {"Bart", "CPT"}
            storeRemote:WaitForChild("changeSkin"):FireServer(unpack(equipArgs))
            OrionLib:MakeNotification({Name = "Skin CPT", Content = "Comprada e equipada com sucesso!", Time = 3})
        end
    })

    TabSkins:AddButton({
        Name = "Comprar Beauty Bart (risco de ban)",
        Callback = function()
            local storeRemote = game:GetService("ReplicatedStorage"):WaitForChild("eventFolders"):WaitForChild("store")
            local buyArgs = {"Bart", "Beauty"}
            storeRemote:WaitForChild("buyItem"):FireServer(unpack(buyArgs))
            task.wait(1.2)
            local equipArgs = {"Bart", "Beauty"}
            storeRemote:WaitForChild("changeSkin"):FireServer(unpack(equipArgs))
            OrionLib:MakeNotification({Name = "Skin Beauty", Content = "Beauty Bart foi comprada e equipada!", Time = 3})
        end
    })

    TabSkins:AddButton({
        Name = "Santa Bart [375]",
        Callback = function()
            buySkin('Bart', 'Santa Bart', 375)
            OrionLib:MakeNotification({Name = "Sucesso!", Content = "Santa Bart comprada e equipada!", Time = 3})
        end
    })

    TabSkins:AddButton({
        Name = "Ghost Bart [555]",
        Callback = function()
            buySkin('Bart', 'Ghost Bart', 555)
            OrionLib:MakeNotification({Name = "Sucesso!", Content = "Ghost Bart comprada e equipada!", Time = 3})
        end
    })

    TabSkins:AddButton({
        Name = "Jacko Bart [210]",
        Callback = function()
            buySkin('Bart', 'Jacko Bart', 210)
            OrionLib:MakeNotification({Name = "Sucesso!", Content = "Jacko Bart comprada e equipada!", Time = 3})
        end
    })

    TabSkins:AddButton({
        Name = "Frozen Bart [335]",
        Callback = function()
            buySkin('Bart', 'Frozen Bart', 335)
            OrionLib:MakeNotification({Name = "Sucesso!", Content = "Frozen Bart comprada e equipada!", Time = 3})
        end
    })

    TabSkins:AddButton({
        Name = "700k Bart [100]",
        Callback = function()
            buySkin('Bart', '700k', 100)
            OrionLib:MakeNotification({Name = "Sucesso!", Content = "700k Bart comprada e equipada!", Time = 3})
        end
    })

    TabSkins:AddButton({
        Name = "Witch Bart [350]",
        Callback = function()
            buySkin('Bart', 'Witch Bart', 350)
            OrionLib:MakeNotification({Name = "Sucesso!", Content = "Witch Bart comprada e equipada!", Time = 3})
        end
    })

    -- Seção Homer
    TabSkins:AddSection({Name = "Homer Skins"})

    TabSkins:AddButton({
        Name = "Rhonda Homer [2500]",
        Callback = function()
            buySkin('Homer', 'Rhonda', 2500)
            OrionLib:MakeNotification({Name = "Sucesso!", Content = "Rhonda Homer comprada e equipada!", Time = 3})
        end
    })

    TabSkins:AddButton({
        Name = "Zomber Homer [700]",
        Callback = function()
            buySkin('Homer', 'Zomber', 700)
            OrionLib:MakeNotification({Name = "Sucesso!", Content = "Zomber Homer comprada e equipada!", Time = 3})
        end
    })

    TabSkins:AddButton({
        Name = "Nettspend Homer [2000]",
        Callback = function()
            buySkin('Homer', 'Nettspend', 2000)
            OrionLib:MakeNotification({Name = "Sucesso!", Content = "Nettspend Homer comprada e equipada!", Time = 3})
        end
    })
end

OrionLib:Init()

task.delay(0.5, function()
    ApplyOrionRankTag()
end)

--[[
    ════════════════════════════════════════════════
    █████╗ ██╗  ██╗ ██████╗     ██╗  ██╗██╗   ██╗██████╗ 
    ██╔══██╗██║  ██║██╔════╝     ██║  ██║██║   ██║██╔══██╗
    ███████║███████║██║  ███╗    ███████║██║   ██║██████╔╝
    ██╔══██║██╔══██║██║   ██║    ██╔══██║██║   ██║██╔══██╗
    ██║  ██║██║  ██║╚██████╔╝    ██║  ██║╚██████╔╝██████╔╝
    ╚═╝  ╚═╝╚═╝  ╚═╝ ╚═════╝     ╚═╝  ╚═╝ ╚═════╝ ╚═════╝ 
    ════════════════════════════════════════════════
    🎯 YOU VS HOMER [BETA] - AHG HUB
    👤 By AHG TEAM
    📅 Versão: BETA 1.0
    ════════════════════════════════════════════════
]]--

getgenv().AHG_HOMER = getgenv().AHG_HOMER or {}

local WindUI = loadstring(game:HttpGet("https://github.com/Footagesus/WindUI/releases/latest/download/main.lua"))()

if not WindUI then return end

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera

-- Configuracoes ESP
AHG_HOMER.ESPEnabled = {
    Bart = false,
    Homer = false
}

AHG_HOMER.ESPSettings = {
    Bart = {
        Outline = false,
        Box = false,
        Name = false,
        Tracers = false,
        Color = Color3.fromHex("#29ed13")
    },
    Homer = {
        Outline = false,
        Box = false,
        Name = false,
        Tracers = false,
        Color = Color3.fromHex("#e30b0b")
    }
}

AHG_HOMER.ESPObjects = {}

-- Configuracoes Wallhop
AHG_HOMER.WallhopEnabled = false

-- Configuracoes Speed
AHG_HOMER.SpeedEnabled = false
AHG_HOMER.SpeedMult = 1.4

-- Configuracoes Fullbright
AHG_HOMER.FullbrightEnabled = false
AHG_HOMER.OriginalBrightness = game:GetService("Lighting").Brightness
AHG_HOMER.OriginalAmbient = game:GetService("Lighting").Ambient
AHG_HOMER.OriginalOutdoorAmbient = game:GetService("Lighting").OutdoorAmbient

-- TEMA AZUL E PRETO (BASEADO NO FPS FLICK)
WindUI:AddTheme({
    Name = "AHG Blue & Black Theme",

    Accent = WindUI:Gradient({
        ["0"] = { Color = Color3.fromHex("#0b25e3"), Transparency = 0 },
        ["100"] = { Color = Color3.fromHex("#000000"), Transparency = 0 },
    }, {
        Rotation = 45,
    }),

    Background = Color3.fromHex("#0a0a0a"),
    BackgroundTransparency = 0,

    Outline = WindUI:Gradient({
        ["0"] = { Color = Color3.fromHex("#0b25e3"), Transparency = 0 },
        ["50"] = { Color = Color3.fromHex("#0820b3"), Transparency = 0 },
        ["100"] = { Color = Color3.fromHex("#061a83"), Transparency = 0 },
    }, {
        Rotation = 90,
    }),

    Text = Color3.fromHex("#FFFFFF"),
    Placeholder = Color3.fromHex("#7a7a7a"),
    Button = Color3.fromHex("#1a1a1a"),
    Icon = Color3.fromHex("#0b25e3"),
    Hover = Color3.fromHex("#0b25e3"),

    WindowBackground = Color3.fromHex("#0a0a0a"),
    WindowShadow = Color3.fromHex("#000000"),

    DialogBackground = Color3.fromHex("#0a0a0a"),
    DialogBackgroundTransparency = 0,
    DialogTitle = Color3.fromHex("#FFFFFF"),
    DialogContent = Color3.fromHex("#FFFFFF"),
    DialogIcon = Color3.fromHex("#0b25e3"),

    WindowTopbarButtonIcon = Color3.fromHex("#0b25e3"),
    WindowTopbarTitle = Color3.fromHex("#FFFFFF"),
    WindowTopbarAuthor = Color3.fromHex("#0b25e3"),
    WindowTopbarIcon = Color3.fromHex("#0b25e3"),

    TabBackground = WindUI:Gradient({
        ["0"] = { Color = Color3.fromHex("#1a1a1a"), Transparency = 0 },
        ["100"] = { Color = Color3.fromHex("#0f0f0f"), Transparency = 0 },
    }, {
        Rotation = 0,
    }),
    TabTitle = Color3.fromHex("#FFFFFF"),
    TabIcon = Color3.fromHex("#0b25e3"),

    ElementBackground = Color3.fromHex("#1a1a1a"),
    ElementTitle = Color3.fromHex("#FFFFFF"),
    ElementDesc = Color3.fromHex("#AAAAAA"),
    ElementIcon = Color3.fromHex("#0b25e3"),

    PopupBackground = Color3.fromHex("#0a0a0a"),
    PopupBackgroundTransparency = 0,
    PopupTitle = Color3.fromHex("#FFFFFF"),
    PopupContent = Color3.fromHex("#FFFFFF"),
    PopupIcon = Color3.fromHex("#0b25e3"),

    Toggle = Color3.fromHex("#1a1a1a"),
    ToggleBar = WindUI:Gradient({
        ["0"] = { Color = Color3.fromHex("#0b25e3"), Transparency = 0 },
        ["100"] = { Color = Color3.fromHex("#0820b3"), Transparency = 0 },
    }, {
        Rotation = 0,
    }),

    Checkbox = Color3.fromHex("#1a1a1a"),
    CheckboxIcon = Color3.fromHex("#0b25e3"),

    Slider = Color3.fromHex("#1a1a1a"),
    SliderThumb = WindUI:Gradient({
        ["0"] = { Color = Color3.fromHex("#0b25e3"), Transparency = 0 },
        ["100"] = { Color = Color3.fromHex("#0614a3"), Transparency = 0 },
    }, {
        Rotation = 45,
    }),
})

-- CRIAR JANELA
AHG_HOMER.Window = WindUI:CreateWindow({
    Title = "AHG HUB | YOU VS HOMER",
    Author = "By AHG TEAM",
    Size = UDim2.new(0, 500, 0, 400),
    Theme = "AHG Blue & Black Theme",
    Keybind = Enum.KeyCode.RightControl,
})

AHG_HOMER.Window:Tag({
    Title = "AHG HUB V1",
    Icon = "rocket",
    Color = Color3.fromHex("#0b25e3"),
    Radius = 0,
})

AHG_HOMER.Window:EditOpenButton({
    Title = "Open AHG VS HOMER",
    Icon = "skull",
    CornerRadius = UDim.new(0, 16),
    StrokeThickness = 3,
    Color = ColorSequence.new({
        ColorSequenceKeypoint.new(0, Color3.fromHex("#0b25e3")),
        ColorSequenceKeypoint.new(0.5, Color3.fromHex("#0820b3")),
        ColorSequenceKeypoint.new(1, Color3.fromHex("#000000"))
    }),
    Enabled = true,
    Draggable = true,
})

-- CRIAR ABAS
AHG_HOMER.TabESP = AHG_HOMER.Window:Tab({Title = "ESP", Icon = "eye"})
AHG_HOMER.TabHacks = AHG_HOMER.Window:Tab({Title = "HACKS", Icon = "zap"})

-- FUNCOES ESP (OTIMIZADAS)
function AHG_HOMER.CreateESP(player)
    if AHG_HOMER.ESPObjects[player] then return end
    AHG_HOMER.ESPObjects[player] = {
        Outline = nil,
        Box = {},
        Name = nil,
        Tracer = nil
    }
end

function AHG_HOMER.RemoveESP(player)
    if not AHG_HOMER.ESPObjects[player] then return end
    
    local esp = AHG_HOMER.ESPObjects[player]
    
    if esp.Outline then 
        pcall(function() esp.Outline:Destroy() end)
    end
    
    for _, line in pairs(esp.Box) do
        pcall(function() line:Remove() end)
    end
    
    if esp.Name then 
        pcall(function() esp.Name:Remove() end)
    end
    
    if esp.Tracer then 
        pcall(function() esp.Tracer:Remove() end)
    end
    
    AHG_HOMER.ESPObjects[player] = nil
end

function AHG_HOMER.GetPlayerTeam(player)
    if not player.Team then return nil end
    
    local teamName = string.lower(player.Team.Name)
    
    if string.find(teamName, "bart") then
        return "Bart"
    elseif string.find(teamName, "homer") then
        return "Homer"
    end
    
    return nil
end

function AHG_HOMER.UpdateESP()
    for _, player in pairs(Players:GetPlayers()) do
        if player == LocalPlayer then continue end
        
        local teamType = AHG_HOMER.GetPlayerTeam(player)
        
        if not teamType or not AHG_HOMER.ESPEnabled[teamType] or not player.Character then
            AHG_HOMER.RemoveESP(player)
            continue
        end
        
        AHG_HOMER.CreateESP(player)
        
        local char = player.Character
        local rootPart = char:FindFirstChild("HumanoidRootPart")
        local head = char:FindFirstChild("Head")
        local humanoid = char:FindFirstChild("Humanoid")
        
        if not rootPart or not head or not humanoid or humanoid.Health <= 0 then
            continue
        end
        
        local settings = AHG_HOMER.ESPSettings[teamType]
        local esp = AHG_HOMER.ESPObjects[player]
        
        if settings.Outline then
            if not esp.Outline then
                esp.Outline = Instance.new("Highlight")
                esp.Outline.Parent = char
                esp.Outline.FillTransparency = 1
                esp.Outline.OutlineTransparency = 0.5
            end
            esp.Outline.OutlineColor = settings.Color
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
                    table.insert(esp.Box, line)
                end
            end
            
            local pos, onScreen = Camera:WorldToViewportPoint(rootPart.Position)
            
            if onScreen then
                local headPos = Camera:WorldToViewportPoint(head.Position + Vector3.new(0, 0.5, 0))
                local legPos = Camera:WorldToViewportPoint(rootPart.Position - Vector3.new(0, 3, 0))
                local height = math.abs(headPos.Y - legPos.Y)
                local width = height * 0.5
                
                esp.Box[1].From = Vector2.new(pos.X - width, headPos.Y)
                esp.Box[1].To = Vector2.new(pos.X + width, headPos.Y)
                esp.Box[1].Color = settings.Color
                esp.Box[1].Visible = true
                
                esp.Box[2].From = Vector2.new(pos.X - width, legPos.Y)
                esp.Box[2].To = Vector2.new(pos.X + width, legPos.Y)
                esp.Box[2].Color = settings.Color
                esp.Box[2].Visible = true
                
                esp.Box[3].From = Vector2.new(pos.X - width, headPos.Y)
                esp.Box[3].To = Vector2.new(pos.X - width, legPos.Y)
                esp.Box[3].Color = settings.Color
                esp.Box[3].Visible = true
                
                esp.Box[4].From = Vector2.new(pos.X + width, headPos.Y)
                esp.Box[4].To = Vector2.new(pos.X + width, legPos.Y)
                esp.Box[4].Color = settings.Color
                esp.Box[4].Visible = true
            else
                for _, line in pairs(esp.Box) do
                    line.Visible = false
                end
            end
        else
            for _, line in pairs(esp.Box) do
                if line then line.Visible = false end
            end
        end
        
        if settings.Name then
            if not esp.Name then
                esp.Name = Drawing.new("Text")
                esp.Name.Center = true
                esp.Name.Outline = true
                esp.Name.Size = 13
            end
            
            local pos, onScreen = Camera:WorldToViewportPoint(head.Position + Vector3.new(0, 1.5, 0))
            
            if onScreen then
                local distance = 0
                if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
                    distance = (rootPart.Position - LocalPlayer.Character.HumanoidRootPart.Position).Magnitude
                end
                
                esp.Name.Text = player.Name .. " | " .. math.floor(distance) .. "M"
                esp.Name.Position = Vector2.new(pos.X, pos.Y)
                esp.Name.Color = settings.Color
                esp.Name.Visible = true
            else
                esp.Name.Visible = false
            end
        elseif esp.Name then
            esp.Name.Visible = false
        end
        
        if settings.Tracers then
            if not esp.Tracer then
                esp.Tracer = Drawing.new("Line")
                esp.Tracer.Thickness = 1
                esp.Tracer.Transparency = 1
            end
            
            local pos, onScreen = Camera:WorldToViewportPoint(rootPart.Position)
            
            if onScreen then
                esp.Tracer.From = Vector2.new(Camera.ViewportSize.X / 2, Camera.ViewportSize.Y)
                esp.Tracer.To = Vector2.new(pos.X, pos.Y)
                esp.Tracer.Color = settings.Color
                esp.Tracer.Visible = true
            else
                esp.Tracer.Visible = false
            end
        elseif esp.Tracer then
            esp.Tracer.Visible = false
        end
    end
end

-- ============================================
-- BYPASS ANTI-CHEAT
-- ============================================
local ACtable = {
    LocalPlayer.PlayerScripts:FindFirstChild("QuitsAntiCheatChecker"),
    LocalPlayer.PlayerScripts:FindFirstChild("QuitsAntiCheatLocal")
}
for _, v in pairs(ACtable) do
    if v then v:Destroy() end
end

-- ============================================
-- WALLHOP
-- ============================================
local SETTINGS = {
    JumpHeight = 36,
    DetectDist = 3.5,
    FlickAngle = 45,
    RecoverySpeed = 0.15
}

local lastJump = 0

local function getWallInfo()
    local char = LocalPlayer.Character
    local root = char and char:FindFirstChild("HumanoidRootPart")
    if not root then return end
    
    local params = RaycastParams.new()
    params.FilterDescendantsInstances = {char}
    
    local dirs = {
        ["F"] = root.CFrame.LookVector,
        ["B"] = -root.CFrame.LookVector,
        ["L"] = -root.CFrame.RightVector,
        ["R"] = root.CFrame.RightVector
    }
    
    for side, dir in pairs(dirs) do
        local res = workspace:Raycast(root.Position, dir * SETTINGS.DetectDist, params)
        if res and res.Instance and res.Instance.CanCollide then
            return res.Normal, side
        end
    end
end

local function applyProfessionalFlick(root, side)
    local angle = math.rad(SETTINGS.FlickAngle)
    if side == "R" then angle = -angle end
    root.CFrame = root.CFrame * CFrame.Angles(0, angle, 0)
    
    task.spawn(function()
        task.wait(0.05)
        local start = tick()
        while tick() - start < SETTINGS.RecoverySpeed do
            local look = Camera.CFrame.LookVector
            local targetRotation = CFrame.new(root.Position, root.Position + Vector3.new(look.X, 0, look.Z))
            root.CFrame = root.CFrame:Lerp(targetRotation, 0.15)
            RunService.RenderStepped:Wait()
        end
    end)
end

UserInputService.JumpRequest:Connect(function()
    if not AHG_HOMER.WallhopEnabled then return end
    
    local char = LocalPlayer.Character
    local root = char and char:FindFirstChild("HumanoidRootPart")
    local hum = char and char:FindFirstChild("Humanoid")
    
    if root and hum and (tick() - lastJump > 0.15) then
        local normal, side = getWallInfo()
        if normal then
            lastJump = tick()
            hum:ChangeState(Enum.HumanoidStateType.Jumping)
            root.Velocity = Vector3.new(root.Velocity.X, SETTINGS.JumpHeight, root.Velocity.Z)
            applyProfessionalFlick(root, side)
        end
    end
end)

-- ============================================
-- RITUAL HOMER
-- ============================================
local function ExecutarRitual()
    local char = LocalPlayer.Character
    local root = char and char:FindFirstChild("HumanoidRootPart")
    if root then
        task.spawn(function()
            local bg = Instance.new("BodyAngularVelocity", root)
            bg.AngularVelocity = Vector3.new(0, 10, 0)
            bg.MaxTorque = Vector3.new(0, 9e9, 0)
            
            task.wait(1)
            bg.AngularVelocity = Vector3.new(0, 50, 0)
            
            local bv = Instance.new("BodyVelocity", root)
            bv.Velocity = Vector3.new(0, 15, 0)
            bv.MaxForce = Vector3.new(0, 9e9, 0)
            
            task.wait(2)
            bv:Destroy()
            bg:Destroy()
            char.Humanoid.Health = 0
        end)
    end
end

-- ============================================
-- FARM DE MOEDAS
-- ============================================
local FarmPos = CFrame.new(-32.1, 212.6, 111.0)

local function FarmCoin()
    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
        LocalPlayer.Character.HumanoidRootPart.CFrame = FarmPos
    end
end

-- ============================================
-- KILL ALL (VERSAO PROFISSIONAL)
-- ============================================
local function KillAll()
    local myChar = LocalPlayer.Character
    if not myChar or not myChar:FindFirstChild("Torso") then 
        AHG_HOMER.Window:Notify({
            Title = "ERRO",
            Content = "Personagem nao encontrado!",
            Duration = 3,
            Icon = "alert-circle",
        })
        return 
    end
    
    local oldPos = myChar.Torso.CFrame
    local killed = 0
    
    for _, p in ipairs(Players:GetPlayers()) do
        if p ~= LocalPlayer and p.Team and p.Team.Name:sub(1,1):lower() == "b" and p.Character and p.Character:FindFirstChild("Torso") then
            local name = string.lower(p.Name)
            if name ~= "packj0" and name ~= "kit_cynalt" and name ~= "bk_faxbr" then
                myChar.Torso.CFrame = CFrame.new(p.Character.Torso.Position + Vector3.new(0, 1, 0))
                task.wait(0.1)
                killed = killed + 1
            end
        end
    end
    
    myChar.Torso.CFrame = oldPos
    
    AHG_HOMER.Window:Notify({
        Title = "KILL ALL",
        Content = "Matou " .. killed .. " Barts!",
        Duration = 3,
        Icon = "skull",
    })
end

AHG_HOMER.BartSection = AHG_HOMER.TabESP:Section({Title = "Bart ESP"})

AHG_HOMER.BartSection:Toggle({
    Title = "ATIVAR ESP DO BART",
    Description = "Ativa/Desativa ESP para os Barts",
    Default = false,
    Callback = function(v)
        AHG_HOMER.ESPEnabled.Bart = v
    end
})

AHG_HOMER.BartSection:Toggle({
    Title = "ESP OUTLINE",
    Description = "Contorno ao redor do personagem",
    Default = false,
    Callback = function(v)
        AHG_HOMER.ESPSettings.Bart.Outline = v
    end
})

AHG_HOMER.BartSection:Toggle({
    Title = "ESP BOX",
    Description = "Caixa ao redor do personagem",
    Default = false,
    Callback = function(v)
        AHG_HOMER.ESPSettings.Bart.Box = v
    end
})

AHG_HOMER.BartSection:Toggle({
    Title = "ESP NAME",
    Description = "Nome e distancia do jogador",
    Default = false,
    Callback = function(v)
        AHG_HOMER.ESPSettings.Bart.Name = v
    end
})

AHG_HOMER.BartSection:Toggle({
    Title = "ESP TRACERS",
    Description = "Linha ate o personagem",
    Default = false,
    Callback = function(v)
        AHG_HOMER.ESPSettings.Bart.Tracers = v
    end
})

AHG_HOMER.BartSection:Colorpicker({
    Title = "COR DO ESP",
    Description = "Escolha a cor do ESP do Bart",
    Default = Color3.fromHex("#29ed13"),
    Callback = function(c)
        AHG_HOMER.ESPSettings.Bart.Color = c
    end
})

AHG_HOMER.TabESP:Divider({Text = "Homer ESP"})

AHG_HOMER.HomerSection = AHG_HOMER.TabESP:Section({Title = "Homer ESP"})

AHG_HOMER.HomerSection:Toggle({
    Title = "ATIVAR ESP DO HOMER",
    Description = "Ativa/Desativa ESP para o Homer",
    Default = false,
    Callback = function(v)
        AHG_HOMER.ESPEnabled.Homer = v
    end
})

AHG_HOMER.HomerSection:Toggle({
    Title = "ESP OUTLINE",
    Description = "Contorno ao redor do personagem",
    Default = false,
    Callback = function(v)
        AHG_HOMER.ESPSettings.Homer.Outline = v
    end
})

AHG_HOMER.HomerSection:Toggle({
    Title = "ESP BOX",
    Description = "Caixa ao redor do personagem",
    Default = false,
    Callback = function(v)
        AHG_HOMER.ESPSettings.Homer.Box = v
    end
})

AHG_HOMER.HomerSection:Toggle({
    Title = "ESP NAME",
    Description = "Nome e distancia do jogador",
    Default = false,
    Callback = function(v)
        AHG_HOMER.ESPSettings.Homer.Name = v
    end
})

AHG_HOMER.HomerSection:Toggle({
    Title = "ESP TRACERS",
    Description = "Linha ate o personagem",
    Default = false,
    Callback = function(v)
        AHG_HOMER.ESPSettings.Homer.Tracers = v
    end
})

AHG_HOMER.HomerSection:Colorpicker({
    Title = "COR DO ESP",
    Description = "Escolha a cor do ESP do Homer",
    Default = Color3.fromHex("#e30b0b"),
    Callback = function(c)
        AHG_HOMER.ESPSettings.Homer.Color = c
    end
})

AHG_HOMER.WallhopSection = AHG_HOMER.TabHacks:Section({Title = "Movement & Visuals"})

AHG_HOMER.WallhopSection:Toggle({
    Title = "WALLHOP",
    Description = "Sistema de wallhop profissional (baseado JUJU HUB)",
    Default = false,
    Callback = function(v)
        AHG_HOMER.WallhopEnabled = v
    end
})

AHG_HOMER.WallhopSection:Toggle({
    Title = "VELOCIDADE",
    Description = "Aumenta velocidade para 1.4x (22.4 studs/s)",
    Default = false,
    Callback = function(v)
        AHG_HOMER.SpeedEnabled = v
    end
})

AHG_HOMER.WallhopSection:Toggle({
    Title = "FULLBRIGHT",
    Description = "Ilumina todo o mapa (protegido contra reset)",
    Default = false,
    Callback = function(v)
        AHG_HOMER.FullbrightEnabled = v
        local Lighting = game:GetService("Lighting")
        
        if v then
            Lighting.Brightness = 2
            Lighting.ClockTime = 14
            Lighting.FogEnd = 100000
            Lighting.GlobalShadows = false
            Lighting.OutdoorAmbient = Color3.fromRGB(128, 128, 128)
            Lighting.Ambient = Color3.fromRGB(178, 178, 178)
        else
            Lighting.Brightness = AHG_HOMER.OriginalBrightness
            Lighting.GlobalShadows = true
            Lighting.OutdoorAmbient = AHG_HOMER.OriginalOutdoorAmbient
            Lighting.Ambient = AHG_HOMER.OriginalAmbient
        end
    end
})

AHG_HOMER.WallhopSection:Button({
    Title = "KILL ALL (HOMER)",
    Description = "Teleporta e mata todos os Barts (0.5s cada)",
    Callback = function()
        KillAll()
    end
})

AHG_HOMER.WallhopSection:Button({
    Title = "FARM DE MOEDAS",
    Description = "Teleporta para local de farm de moedas",
    Callback = function()
        FarmCoin()
        AHG_HOMER.Window:Notify({
            Title = "FARM",
            Content = "Teleportado para farm de moedas!",
            Duration = 3,
            Icon = "dollar-sign",
        })
    end
})

AHG_HOMER.WallhopSection:Button({
    Title = "RITUAL HOMER",
    Description = "Gira e sobe (USE COMO HOMER)",
    Callback = function()
        ExecutarRitual()
        AHG_HOMER.Window:Notify({
            Title = "RITUAL",
            Content = "Ritual Homer ativado!",
            Duration = 3,
            Icon = "skull",
        })
    end
})

Players.PlayerRemoving:Connect(function(player)
    AHG_HOMER.RemoveESP(player)
end)

-- ============================================
-- LOOPS PRINCIPAIS (OTIMIZADOS)
-- ============================================

-- Loop de ESP (60 FPS limit para otimizacao)
local lastESPUpdate = 0
RunService.RenderStepped:Connect(function()
    if tick() - lastESPUpdate >= 0.016 then -- ~60 FPS
        pcall(AHG_HOMER.UpdateESP)
        lastESPUpdate = tick()
    end
end)

-- Loop de Speed (mais leve)
RunService.Heartbeat:Connect(function()
    if AHG_HOMER.SpeedEnabled then
        local hum = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid")
        if hum then
            hum.WalkSpeed = 16 * AHG_HOMER.SpeedMult
        end
    end
end)

-- Protecao de Fullbright (impede reset)
RunService.Heartbeat:Connect(function()
    if AHG_HOMER.FullbrightEnabled then
        local Lighting = game:GetService("Lighting")
        if Lighting.Brightness ~= 2 then
            Lighting.Brightness = 2
            Lighting.ClockTime = 14
            Lighting.FogEnd = 100000
            Lighting.GlobalShadows = false
        end
    end
end)

WindUI:Notify({
    Title = "AHG HUB",
    Content = "Script carregado com sucesso! [BETA]",
    Duration = 5,
    Icon = "star",
})

print("AHG HUB | YOU VS HOMER [BETA] - Carregado!")

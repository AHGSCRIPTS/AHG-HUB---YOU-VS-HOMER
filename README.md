-- [[
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
    📅 Versão: BETA 1.5.1 - FIX NIL + UIStroke 03/02/2026
    ════════════════════════════════════════════════
]]--

getgenv().AHG_HOMER = getgenv().AHG_HOMER or {}

print("[AHG DEBUG] Iniciando...")

local success, WindUI = pcall(function()
    return loadstring(game:HttpGet("https://raw.githubusercontent.com/Footagesus/WindUI/main/main.lua"))()
end)

if not success or type(WindUI) ~= "table" then
    warn("[AHG ERRO] WindUI falhou! Motivo: " .. tostring(WindUI))
    return  -- Para evitar nil value crash
end

print("[AHG DEBUG] WindUI carregado OK!")

-- PATCH AHG: Cria UIStroke faltantes no numpad virtual da WindUI (fix erro linha ~2684)
spawn(function()
    task.wait(2)  -- Aumentado para dar mais tempo
    local pgui = game.Players.LocalPlayer:WaitForChild("PlayerGui", 10)
    if not pgui then warn("[AHG] PlayerGui não encontrado!") return end
    
    local function criarStrokeSeFaltar(botao)
        if not botao or not botao:IsA("TextButton") then return end
        if botao:FindFirstChild("UIStroke") then return end
        
        local stroke = Instance.new("UIStroke")
        stroke.Name = "UIStroke"
        stroke.Parent = botao
        stroke.Color = Color3.fromRGB(255, 255, 0)  -- Amarelo como no seu print original
        stroke.Thickness = 2
        stroke.Transparency = 0
        stroke.Enabled = true
    end
    
    -- Fix inicial nos botões existentes
    for _, nome in pairs({"One", "Two", "Three", "Four", "Five"}) do
        for _, obj in pairs(pgui:GetDescendants()) do
            if obj.Name == nome then
                criarStrokeSeFaltar(obj)
            end
        end
    end
    
    -- Monitora novos botões criados (quando abre sliders/keybinds)
    pgui.DescendantAdded:Connect(function(child)
        if child:IsA("TextButton") and table.find({"One","Two","Three","Four","Five"}, child.Name) then
            task.wait(0.2)
            criarStrokeSeFaltar(child)
        end
    end)
    
    print("[AHG HUB] Patch UIStroke aplicado - erros de WindUI corrigidos!")
end)

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

-- TEMA AZUL E PRETO
WindUI:AddTheme({
    Name = "AHG Blue & Black Theme",
    Accent = WindUI:Gradient({
        ["0"] = { Color = Color3.fromHex("#0b25e3"), Transparency = 0 },
        ["100"] = { Color = Color3.fromHex("#000000"), Transparency = 0 },
    }, { Rotation = 45, }),
    Background = Color3.fromHex("#0a0a0a"),
    BackgroundTransparency = 0,
    Outline = WindUI:Gradient({
        ["0"] = { Color = Color3.fromHex("#0b25e3"), Transparency = 0 },
        ["50"] = { Color = Color3.fromHex("#0820b3"), Transparency = 0 },
        ["100"] = { Color = Color3.fromHex("#061a83"), Transparency = 0 },
    }, { Rotation = 90, }),
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
    }, { Rotation = 0, }),
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
    }, { Rotation = 0, }),
    Checkbox = Color3.fromHex("#1a1a1a"),
    CheckboxIcon = Color3.fromHex("#0b25e3"),
    Slider = Color3.fromHex("#1a1a1a"),
    SliderThumb = WindUI:Gradient({
        ["0"] = { Color = Color3.fromHex("#0b25e3"), Transparency = 0 },
        ["100"] = { Color = Color3.fromHex("#0614a3"), Transparency = 0 },
    }, { Rotation = 45, }),
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
    Title = "BETA V1.0",
    Icon = "skull",
    Color = Color3.fromHex("#0b25e3"),
    Radius = 0,
})

AHG_HOMER.Window:EditOpenButton({
    Title = "Open AHG HUB",
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
        
        -- Outline
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
        
        -- Box
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
                for _, line in pairs(esp.Box) do line.Visible = false end
            end
        else
            for _, line in pairs(esp.Box) do
                if line then line.Visible = false end
            end
        end
        
        -- Name
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
                esp.Name.Color = settings.Color
                esp.Name.Position = Vector2.new(pos.X, pos.Y)
                esp.Name.Visible = true
            else
                esp.Name.Visible = false
            end
        elseif esp.Name then
            esp.Name.Visible = false
        end
        
        -- Tracers
        if settings.Tracers then
            if not esp.Tracer then
                esp.Tracer = Drawing.new("Line")
                esp.Tracer.Thickness = 2
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

-- LOOP DE UPDATE ESP (otimizado 60fps cap)
local lastESP = 0
RunService.RenderStepped:Connect(function()
    if tick() - lastESP >= 0.016 then  -- ~60fps
        pcall(AHG_HOMER.UpdateESP)
        lastESP = tick()
    end
end)

-- LOOP PRINCIPAL (Speed, Fullbright, Wallhop)
RunService.Heartbeat:Connect(function()
    local char = LocalPlayer.Character
    if not char then return end
    local hum = char:FindFirstChild("Humanoid")
    if not hum then return end
    
    -- Speed
    if AHG_HOMER.SpeedEnabled then
        hum.WalkSpeed = 16 * AHG_HOMER.SpeedMult
    end
    
    -- Fullbright (protege contra reset do jogo)
    if AHG_HOMER.FullbrightEnabled then
        local lighting = game.Lighting
        lighting.Brightness = 2
        lighting.ClockTime = 14
        lighting.FogEnd = 100000
        lighting.GlobalShadows = false
        lighting.OutdoorAmbient = Color3.fromRGB(128, 128, 128)
        lighting.Ambient = Color3.fromRGB(178, 178, 178)
    end
    
    -- Wallhop (raycast simples pra boost em parede)
    if AHG_HOMER.WallhopEnabled and hum:GetState() == Enum.HumanoidStateType.Jumping then
        local root = char:FindFirstChild("HumanoidRootPart")
        if root then
            local rayParams = RaycastParams.new()
            rayParams.FilterDescendantsInstances = {char}
            rayParams.FilterType = Enum.RaycastFilterType.Exclude
            local ray = workspace:Raycast(root.Position, root.CFrame.LookVector * 5, rayParams)
            if ray then
                root.Velocity = Vector3.new(root.Velocity.X * 1.1, 50, root.Velocity.Z * 1.1)
            end
        end
    end
end)

-- TOGGLES E ELEMENTOS NA TAB ESP
local BartSection = AHG_HOMER.TabESP:Section({Title = "Bart ESP"})
BartSection:Toggle({
    Title = "ATIVAR ESP DO BART",
    Default = false,
    Callback = function(v) AHG_HOMER.ESPEnabled.Bart = v end
})
BartSection:Toggle({
    Title = "ESP OUTLINE",
    Default = false,
    Callback = function(v) AHG_HOMER.ESPSettings.Bart.Outline = v end
})
BartSection:Toggle({
    Title = "ESP BOX",
    Default = false,
    Callback = function(v) AHG_HOMER.ESPSettings.Bart.Box = v end
})
BartSection:Toggle({
    Title = "ESP NAME",
    Default = false,
    Callback = function(v) AHG_HOMER.ESPSettings.Bart.Name = v end
})
BartSection:Toggle({
    Title = "ESP TRACERS",
    Default = false,
    Callback = function(v) AHG_HOMER.ESPSettings.Bart.Tracers = v end
})
BartSection:ColorPicker({
    Title = "COR DO ESP",
    Default = Color3.fromHex("#29ed13"),
    Callback = function(c) AHG_HOMER.ESPSettings.Bart.Color = c end
})

local HomerSection = AHG_HOMER.TabESP:Section({Title = "Homer ESP"})
HomerSection:Toggle({
    Title = "ATIVAR ESP DO HOMER",
    Default = false,
    Callback = function(v) AHG_HOMER.ESPEnabled.Homer = v end
})
HomerSection:Toggle({
    Title = "ESP OUTLINE",
    Default = false,
    Callback = function(v) AHG_HOMER.ESPSettings.Homer.Outline = v end
})
HomerSection:Toggle({
    Title = "ESP BOX",
    Default = false,
    Callback = function(v) AHG_HOMER.ESPSettings.Homer.Box = v end
})
HomerSection:Toggle({
    Title = "ESP NAME",
    Default = false,
    Callback = function(v) AHG_HOMER.ESPSettings.Homer.Name = v end
})
HomerSection:Toggle({
    Title = "ESP TRACERS",
    Default = false,
    Callback = function(v) AHG_HOMER.ESPSettings.Homer.Tracers = v end
})
HomerSection:ColorPicker({
    Title = "COR DO ESP",
    Default = Color3.fromHex("#e30b0b"),
    Callback = function(c) AHG_HOMER.ESPSettings.Homer.Color = c end
})

-- TAB HACKS
local HacksSection = AHG_HOMER.TabHacks:Section({Title = "Movement & Visuals"})
HacksSection:Toggle({
    Title = "WALLHOP",
    Default = false,
    Callback = function(v) AHG_HOMER.WallhopEnabled = v end
})
HacksSection:Toggle({
    Title = "VELOCIDADE",
    Default = false,
    Callback = function(v) AHG_HOMER.SpeedEnabled = v end
})
HacksSection:Slider({
    Title = "Speed Multiplier",
    Min = 1,
    Max = 5,
    Default = 1.4,
    Increment = 0.1,
    Callback = function(v) AHG_HOMER.SpeedMult = v end
})
HacksSection:Toggle({
    Title = "FULLBRIGHT",
    Default = false,
    Callback = function(v)
        AHG_HOMER.FullbrightEnabled = v
        if not v then
            local lighting = game.Lighting
            lighting.Brightness = AHG_HOMER.OriginalBrightness
            lighting.Ambient = AHG_HOMER.OriginalAmbient
            lighting.OutdoorAmbient = AHG_HOMER.OriginalOutdoorAmbient
            lighting.GlobalShadows = true
        end
    end
})

-- NOTIFY FINAL
WindUI:Notify({
    Title = "AHG HUB",
    Content = "Carregado com sucesso! ✅",
    Duration = 5
})

print("[AHG HUB] Script finalizado e pronto!")

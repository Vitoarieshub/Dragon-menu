-- Dragon menu 
-- Desenvolvido por Victor 
-- Script otimizado 

-- Carregar Fluent
local Fluent = loadstring(game:HttpGet("https://github.com/dawid-scripts/Fluent/releases/latest/download/main.lua"))()

-- Função para enviar notificações
local function notify(title, content)
    Fluent:Notify({
        Title = title,
        Content = content,
        Duration = 3 -- Define a duração da notificação para 3 segundos
    })
end

-- Aviso ao executar
notify("Executado com Sucesso!", "Melhor script universal.")

-- Criar a janela principal
local Window = Fluent:CreateWindow({
    Title = "Dragon menu " .. Fluent.Version,
    TabWidth = 90,
    Size = UDim2.fromOffset(420, 310),
    Theme = "Dark"
})

-- Tabela de abas
local Tabs = {
    Main = Window:AddTab({ Title = "Ínício" }),
    Players = Window:AddTab({ Title = "Jogador" }),
    Exploits = Window:AddTab({ Title = "Exploits" }),
    Settings = Window:AddTab({ Title = "Config" })
}

-- Funções utilitárias
local function setHumanoidProperty(property, value)
    local player = game.Players.LocalPlayer
    local character = player.Character or player.CharacterAdded:Wait()
    local humanoid = character:WaitForChild("Humanoid")
    humanoid[property] = value
    print(property .. " ajustado para:", value)
end

-- Infinite Jump
local jumpConnection
local function toggleInfiniteJump(enable)
    if enable then
        if not jumpConnection then
            jumpConnection = game:GetService("UserInputService").JumpRequest:Connect(function()
                local player = game.Players.LocalPlayer
                local character = player.Character or player.CharacterAdded:Wait()
                local humanoid = character:WaitForChild("Humanoid")
                humanoid:ChangeState(Enum.HumanoidStateType.Jumping)
            end)
        end
    elseif jumpConnection then
        jumpConnection:Disconnect()
        jumpConnection = nil
    end
end

-- Noclip
local noclipConnection
local function toggleNoclip(enable)
    if enable then
        if not noclipConnection then
            noclipConnection = game:GetService("RunService").Stepped:Connect(function()
                for _, part in pairs(game.Players.LocalPlayer.Character:GetDescendants()) do
                    if part:IsA("BasePart") then
                        part.CanCollide = false
                    end
                end
            end)
        end
        notify("Travessa Paredes", "Você pode travessar paredes agora!")
    else
        if noclipConnection then
            noclipConnection:Disconnect()
            noclipConnection = nil
        end
        for _, part in pairs(game.Players.LocalPlayer.Character:GetDescendants()) do
            if part:IsA("BasePart") then
                part.CanCollide = true
            end
        end
        notify("Travessa Paredes", "Foi Desativado.")
    end
end

-- Aba: Início
Tabs.Main:AddParagraph({ Title = "Programador Victor", Content = "Scripts personalizados" })

Tabs.Main:AddButton({
    Title = "Fly",
    Callback = function()
        loadstring(game:HttpGet("https://raw.githubusercontent.com/Vitoarieshub/Fly-universal-/refs/heads/main/README.md"))()
    end
})

Tabs.Main:AddToggle("noclip", {
    Title = "Travessa Paredes",
    Description = "Ativa/desativa a travessia de paredes",
    Default = false,
    Callback = toggleNoclip
})

Tabs.Main:AddToggle("infjump", {
    Title = "Pulo infinito",
    Description = "Ativa/desativa o pulo infinito",
    Default = false,
    Callback = function(state)
        notify(
            state and "Pulo infinito" or "Pulo infinito", 
            state and "Pulo infinito ativado com sucesso!" or "Pulo infinito desativado."
        )
        toggleInfiniteJump(state)
    end
})

Tabs.Main:AddSlider("Gravity", {
    Title = "Gravidade",
    Description = "Ajusta a gravidade do jogador",
    Default = 196.2, -- Gravidade padrão no Roblox
    Min = 0,
    Max = 500,
    Rounding = 1,
    Callback = function(value)
        game.Workspace.Gravity = value
        notify("Gravidade", "Foi ajustada para: " .. value)
    end
})

Tabs.Main:AddSlider("JumpPower", {
    Title = "Altura do pulo",
    Description = "Define a altura do pulo",
    Default = 50,
    Min = 0,
    Max = 900,
    Rounding = 1,
    Callback = function(value)
        setHumanoidProperty("JumpPower", value)
    end
})

Tabs.Main:AddSlider("WalkSpeed", {
    Title = "Velocidade",
    Description = "Define a velocidade do jogador",
    Default = 20,
    Min = 0,
    Max = 200,
    Rounding = 1,
    Callback = function(value)
        setHumanoidProperty("WalkSpeed", value)
    end
})

Tabs.Main:AddSlider("FOV", {
    Title = "Campo de visão",
    Description = "Ajusta o campo de visão da câmera",
    Default = 70,
    Min = 30,
    Max = 120, -- Máximo permitido pelo Roblox
    Rounding = 1,
    Callback = function(value)
        game.Workspace.CurrentCamera.FieldOfView = value
    end
})

Tabs.Main:AddParagraph({ Title = "Quer saber as atualizações", Content = "Kwai:Vitoroficial Insta:vitoroemanuel"})

-- Aba: Jogadores
Tabs.Players:AddButton({
    Title = "ESP Nome",
    Callback = function()
        loadstring(game:HttpGet("https://pastebin.com/raw/rSUGN1fK"))()
    end
})

Tabs.Players:AddButton({
    Title = "ESP Linhas",
    Callback = function()
        loadstring(game:HttpGet("https://pastebin.com/raw/nnHbfvGW"))()
    end
})

Tabs.Players:AddParagraph({ Title = "Teleporte", Content = "Funciona em todos os servidores" })

Tabs.Players:AddButton({
    Title = "Teleporte",
    Callback = function()
        local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer

-- Criar ScreenGui
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")
ScreenGui.ResetOnSpawn = false -- Faz com que a GUI não desapareça ao morrer

-- Criar Frame principal
local Frame = Instance.new("Frame")
Frame.Size = UDim2.new(0, 200, 0, 150)
Frame.Position = UDim2.new(0.5, -100, 0.5, -75)
Frame.BackgroundColor3 = Color3.new(0.1, 0.1, 0.1)
Frame.BorderSizePixel = 2
Frame.Active = true
Frame.Draggable = true
Frame.Visible = false
Frame.Parent = ScreenGui

-- Criar título
local Title = Instance.new("TextLabel")
Title.Text = "Teleporte"
Title.Size = UDim2.new(1, 0, 0, 20)
Title.Position = UDim2.new(0, 0, 0, 0)
Title.BackgroundColor3 = Color3.new(0.2, 0.2, 0.2)
Title.TextColor3 = Color3.new(1, 1, 1)
Title.Font = Enum.Font.SourceSansBold
Title.TextSize = 19
Title.Parent = Frame

-- Criar Dropdown (Lista de Jogadores)
local PlayerList = Instance.new("TextButton")
PlayerList.Text = "Selecionar Jogador"
PlayerList.Size = UDim2.new(1, -20, 0, 30)
PlayerList.Position = UDim2.new(0, 10, 0, 40)
PlayerList.BackgroundColor3 = Color3.new(0.3, 0.3, 0.3)
PlayerList.TextColor3 = Color3.new(1, 1, 1)
PlayerList.Font = Enum.Font.SourceSans
PlayerList.TextSize = 16
PlayerList.Parent = Frame

-- Criar ScrollingFrame para a lista de jogadores
local DropDown = Instance.new("ScrollingFrame")
DropDown.Size = UDim2.new(0, 150, 0, 100)
DropDown.Position = UDim2.new(1, 10, 0, 40) -- Posicionado ao lado direito
DropDown.BackgroundColor3 = Color3.new(0.2, 0.2, 0.2)
DropDown.BorderSizePixel = 2
DropDown.ScrollingEnabled = true
DropDown.CanvasSize = UDim2.new(0, 0, 0, 0)
DropDown.Visible = false
DropDown.Parent = Frame

local PlayerButtons = {}

-- Atualizar lista de jogadores online
local function UpdatePlayerList()
    for _, button in pairs(PlayerButtons) do
        button:Destroy()
    end
    PlayerButtons = {}

    local yOffset = 0
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer then
            local Button = Instance.new("TextButton")
            Button.Text = player.Name
            Button.Size = UDim2.new(1, 0, 0, 25)
            Button.Position = UDim2.new(0, 0, 0, yOffset)
            Button.BackgroundColor3 = Color3.new(0.4, 0.4, 0.4)
            Button.TextColor3 = Color3.new(1, 1, 1)
            Button.Font = Enum.Font.SourceSans
            Button.TextSize = 14
            Button.Parent = DropDown
            Button.MouseButton1Click:Connect(function()
                PlayerList.Text = player.Name
                DropDown.Visible = false
            end)
            table.insert(PlayerButtons, Button)
            yOffset = yOffset + 25
        end
    end
    DropDown.CanvasSize = UDim2.new(0, 0, 0, yOffset)
end

PlayerList.MouseButton1Click:Connect(function()
    DropDown.Visible = not DropDown.Visible
    UpdatePlayerList()
end)

Players.PlayerAdded:Connect(UpdatePlayerList)
Players.PlayerRemoving:Connect(UpdatePlayerList)

-- Criar botão de teleporte
local TeleportButton = Instance.new("TextButton")
TeleportButton.Text = "Teleporta"
TeleportButton.Size = UDim2.new(1, -20, 0, 30)
TeleportButton.Position = UDim2.new(0, 10, 0, 80)
TeleportButton.BackgroundColor3 = Color3.new(0, 0.5, 0)
TeleportButton.TextColor3 = Color3.new(1, 1, 1)
TeleportButton.Font = Enum.Font.SourceSansBold
TeleportButton.TextSize = 16
TeleportButton.Parent = Frame

TeleportButton.MouseButton1Click:Connect(function()
    local TargetPlayerName = PlayerList.Text
    if TargetPlayerName == "Selecionar Jogador" then return end
    
    local TargetPlayer = Players:FindFirstChild(TargetPlayerName)
    if TargetPlayer and TargetPlayer.Character and TargetPlayer.Character:FindFirstChild("HumanoidRootPart") then
        LocalPlayer.Character:SetPrimaryPartCFrame(TargetPlayer.Character.HumanoidRootPart.CFrame + Vector3.new(0, 2, 0))
    end
end)

-- Criar botão para abrir/fechar a GUI
local ToggleButton = Instance.new("TextButton")
ToggleButton.Text = "T"
ToggleButton.Size = UDim2.new(0, 40, 0, 40)
ToggleButton.Position = UDim2.new(0, 10, 1, -50)
ToggleButton.BackgroundColor3 = Color3.new(0.2, 0.2, 0.2)
ToggleButton.TextColor3 = Color3.new(1, 1, 1)
ToggleButton.Font = Enum.Font.SourceSansBold
ToggleButton.TextSize = 23
ToggleButton.Parent = ScreenGui
ToggleButton.Active = true
ToggleButton.Draggable = true

ToggleButton.MouseButton1Click:Connect(function()
    Frame.Visible = not Frame.Visible
end)

    end
})

Tabs.Players:AddParagraph({ Title = "Assistir Jogador", Content = "Funciona em todos os servidores" })

Tabs.Players:AddButton({
    Title = "Assistir jogador",
    Callback = function()
        loadstring(game:HttpGet("https://raw.githubusercontent.com/Vitoarieshub/assistir-jogador-/refs/heads/main/README.md"))()
    end
})

Tabs.Players:AddButton({
    Title = "Click TP",
    Callback = function()
        mouse = game.Players.LocalPlayer:GetMouse()
tool = Instance.new("Tool")
tool.RequiresHandle = false
tool.Name = "Equip to Click TP"
tool.Activated:connect(function()
local pos = mouse.Hit+Vector3.new(0,2.5,0)
pos = CFrame.new(pos.X,pos.Y,pos.Z)
game.Players.LocalPlayer.Character.HumanoidRootPart.CFrame = pos
end)
tool.Parent = game.Players.LocalPlayer.Backpack
    end
})

Tabs.Players:AddButton({
    Title = "Emote",
    Callback = function()
        loadstring(game:HttpGet("https://pastebin.com/raw/eCpipCTH"))()
    end
})

Tabs.Exploits:AddButton({
    Title = "Fly car",
    Callback = function()
        loadstring(game:HttpGet("https://rawscripts.net/raw/Universal-Script-Android-vfly-24974"))()
    end
})

-- Aba: Exploits
Tabs.Exploits:AddButton({
    Title = "infiniteyield",
    Callback = function()
        loadstring(game:HttpGet('https://raw.githubusercontent.com/EdgeIY/infiniteyield/master/source'))()
    end
})

Tabs.Exploits:AddButton({
    Title = "Chat Bypass",
    Callback = function()
        loadstring(game:HttpGet("https://pastebin.com/raw/qJwH9964"))();
    end
})

Tabs.Exploits:AddButton({
    Title = "AutoJJs",
    Callback = function()
        loadstring(game:HttpGet('https://raw.githubusercontent.com/Zv-yz/AutoJJs/main/Main.lua'))(Options);
    end
})

Tabs.Exploits:AddButton({
    Title = "Bring Parts",
    Callback = function()
        loadstring(game:HttpGet("https://rawscripts.net/raw/Universal-Script-Bring-Parts-27586"))()
    end
})

Tabs.Exploits:AddButton({
    Title = "Invisível",
    Callback = function()
        loadstring(game:HttpGet('https://pastebin.com/raw/3Rnd9rHf'))()
    end
})

-- Aba: Configuração
Tabs.Settings:AddButton({
    Title = "Anti Kick",
    Callback = function()
        loadstring(game:HttpGet("https://raw.githubusercontent.com/Exunys/Anti-Kick/main/Anti-Kick.lua"))()
        notify("Anti Kick", "Proteção contra kick foi ativada.")
    end
})

Tabs.Settings:AddButton({
    Title = "FPS",
    Callback = function()
        local Players = game:GetService("Players")
        local RunService = game:GetService("RunService")
        local player = Players.LocalPlayer

        local function createFPSCounter()
            local playerGui = player:FindFirstChild("PlayerGui") or player:WaitForChild("PlayerGui")

            -- Remover contador existente para evitar duplicações
            local existingGui = playerGui:FindFirstChild("FPSCounter")
            if existingGui then
                existingGui:Destroy()
            end

            -- Criar novo ScreenGui
            local screenGui = Instance.new("ScreenGui")
            screenGui.Name = "FPSCounter"
            screenGui.Parent = playerGui

            -- Criar FPS Label
            local fpsLabel = Instance.new("TextLabel")
            fpsLabel.Size = UDim2.new(0, 80, 0, 25)
            fpsLabel.Position = UDim2.new(1, -90, 0, 10)
            fpsLabel.BackgroundTransparency = 0.3
            fpsLabel.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
            fpsLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
            fpsLabel.TextScaled = false
            fpsLabel.TextSize = 14
            fpsLabel.Font = Enum.Font.Code
            fpsLabel.Text = "FPS: 0"
            fpsLabel.Parent = screenGui
            fpsLabel.Active = true
            fpsLabel.Draggable = true
            fpsLabel.BorderSizePixel = 1
            fpsLabel.BorderColor3 = Color3.new(1, 1, 1)
            fpsLabel.TextStrokeTransparency = 0.6
            fpsLabel.TextStrokeColor3 = Color3.new(0, 0, 0)

            -- Variáveis para FPS
            local lastTime = tick()
            local frameCount = 0

            -- Atualiza o FPS a cada segundo
            RunService.RenderStepped:Connect(function()
                frameCount = frameCount + 1
                local currentTime = tick()
                if currentTime - lastTime >= 1 then
                    fpsLabel.Text = "FPS: " .. frameCount
                    frameCount = 0
                    lastTime = currentTime
                end
            end)
        end

        -- Criar o contador de FPS inicialmente
        createFPSCounter()

        -- Criar novamente após respawn
        player.CharacterAdded:Connect(function()
            wait(1) -- Pequeno delay para evitar problemas de carregamento
            createFPSCounter()
        end)
    end
})

Tabs.Settings:AddButton({
    Title = "FPS Boost",
    Callback = function()
        -- Otimiza todas as partes para reduzir o impacto gráfico
        for _, v in ipairs(workspace:GetDescendants()) do
            if v:IsA("Part") or v:IsA("MeshPart") or v:IsA("UnionOperation") then
                v.Material = Enum.Material.SmoothPlastic -- Remove texturas complexas
                v.Reflectance = 0 -- Remove reflexos
                v.CastShadow = false -- Desativa sombras
            elseif v:IsA("Decal") or v:IsA("Texture") then
                v.Transparency = 1 -- Oculta texturas e decals
            elseif v:IsA("ParticleEmitter") or v:IsA("Trail") or v:IsA("Smoke") or v:IsA("Fire") or v:IsA("Explosion") then
                v:Destroy() -- Remove efeitos que consomem desempenho
            end
        end

        -- Ajusta configurações para melhorar o FPS
        pcall(function()
            settings().Rendering.QualityLevel = Enum.QualityLevel.Level01 -- Reduz a qualidade gráfica
            workspace.GlobalShadows = false -- Remove sombras globais
            
            if game:FindFirstChild("Lighting") then
                local lighting = game.Lighting
                lighting.FogEnd = 9e9 -- Remove neblina
                lighting.GlobalShadows = false -- Desativa sombras globais
                lighting.Brightness = 2 -- Ajusta o brilho para compensar a remoção de sombras
            end
        end)

        -- Notificação de sucesso (se houver sistema de notificação)
        if Fluent then
            Fluent:Notify({
                Title = "FPS Boost",
                Content = "Otimização aplicada!",
                Duration = 3
            })
        end
    end
})

local safePosition = Vector3.new(0, 50, 0) -- Posição segura no mapa
local voidLimit = -300
local maxHeight = 300
local isAntiVoidActive = false

local function checkVoid()
    local humanoidRootPart = game.Players.LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
    if humanoidRootPart then
        local pos = humanoidRootPart.Position
        if pos.Y < voidLimit or pos.Y > maxHeight then
            humanoidRootPart.CFrame = CFrame.new(safePosition)
        end
    end
end

game:GetService("RunService").Stepped:Connect(function()
    if isAntiVoidActive then
        checkVoid()
    end
end)

Tabs.Settings:AddToggle("Anti Void", {
    Title = "Anti Void",
    Description = "Ativa/desativa Anti void",
    Default = false,
    Callback = function(state)
        isAntiVoidActive = state
    end
})

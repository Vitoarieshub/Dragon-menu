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
notify("Executado com Sucesso!", "Seja bem vindo.")

-- Criar a janela principal
local Window = Fluent:CreateWindow({
    Title = "Dragon legit " .. Fluent.Version,
    TabWidth = 90,
    Size = UDim2.fromOffset(420, 310),
    Theme = "Dark"
})

-- Tabela de abas
local Tabs = {
    Main = Window:AddTab({ Title = "Ínício" }),
    Visual = Window:AddTab({ Title = "Visual" }),
    Players = Window:AddTab({ Title = "Jogadores" }),
    Settings = Window:AddTab({ Title = "Configuração" })
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

Tabs.Main:AddParagraph({ Title = "Segue nas redes sociais", Content = "Kwai:Vitoroficial Insta:vitoroemanuel"})

-- Aba: Visual
Tabs.Visual:AddButton({
    Title = "ESP Nome",
    Callback = function()
        loadstring(game:HttpGet("https://pastebin.com/raw/rSUGN1fK"))()
    end
})

Tabs.Visual:AddButton({
    Title = "ESP Linhas",
    Callback = function()
        loadstring(game:HttpGet("https://pastebin.com/raw/nnHbfvGW"))()
    end
})

Tabs.Visual:AddSlider("FOV", {
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

Tabs.Visual:AddToggle("esp_nome", {
    Title = "ESP Nome",
    Description = "Ativa/Desativa o ESP de nome",
    Default = false,
    Callback = function(state)
        local Players = game:GetService("Players")
        local RunService = game:GetService("RunService")
        local LocalPlayer = Players.LocalPlayer

        -- Função para criar ESP
        local function criarESP(player)
            if player == LocalPlayer then return end

            local char = player.Character or player.CharacterAdded:Wait()
            local head = char:WaitForChild("Head", 5)
            if head and not head:FindFirstChild("ESP") then
                local esp = Instance.new("BillboardGui")
                esp.Name = "ESP"
                esp.Adornee = head
                esp.Size = UDim2.new(0, 100, 0, 50)
                esp.StudsOffset = Vector3.new(0, 2, 0)
                esp.AlwaysOnTop = true

                local text = Instance.new("TextLabel")
                text.Size = UDim2.new(1, 0, 1, 0)
                text.BackgroundTransparency = 1
                text.Text = player.Name
                text.TextColor3 = Color3.fromRGB(255, 0, 0)
                text.TextScaled = true
                text.Font = Enum.Font.GothamBold
                text.TextStrokeTransparency = 0.4
                text.TextStrokeColor3 = Color3.new(0, 0, 0)
                text.Parent = esp

                esp.Parent = head
            end
        end

        -- Ativa ou Desativa ESP
        if state then
            for _, player in ipairs(Players:GetPlayers()) do
                criarESP(player)
            end

            Players.PlayerAdded:Connect(function(player)
                player.CharacterAdded:Connect(function()
                    wait(1)
                    criarESP(player)
                end)
            end)

            Players.PlayerRemoving:Connect(function(player)
                if player.Character then
                    local esp = player.Character:FindFirstChild("ESP", true)
                    if esp then
                        esp:Destroy()
                    end
                end
            end)
        else
            for _, player in ipairs(Players:GetPlayers()) do
                if player.Character then
                    local esp = player.Character:FindFirstChild("ESP", true)
                    if esp then
                        esp:Destroy()
                    end
                end
            end
        end
    end
})


Tabs.Players:AddButton({
    Title = "Teleporte",
    Callback = function()
        local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer

-- Criar ScreenGui
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Parent = LocalPlayer:WaitForChild("PlayerGui")
ScreenGui.ResetOnSpawn = false -- Faz com que a GUI não desapareça ao morrer

-- Função para aplicar Squircle (bordas arredondadas)
local function applySquircle(element, radius)
    local UICorner = Instance.new("UICorner")
    UICorner.CornerRadius = UDim.new(0, radius)
    UICorner.Parent = element
end

-- Criar Frame principal (com Squircle)
local Frame = Instance.new("Frame")
Frame.Size = UDim2.new(0, 200, 0, 150)
Frame.Position = UDim2.new(0.6, -20, 0.4, -65)
Frame.BackgroundColor3 = Color3.new(0, 0, 0)
Frame.BorderSizePixel = 0
Frame.Active = true
Frame.Draggable = true
Frame.Visible = false
Frame.Parent = ScreenGui
applySquircle(Frame, 15) -- Aplica Squircle

-- Criar título
local Title = Instance.new("TextLabel")
Title.Text = "Teleporte"
Title.Size = UDim2.new(1, 0, 0, 20)
Title.BackgroundColor3 = Color3.new(0, 0, 0)
Title.BackgroundTransparency = 0.2
Title.TextColor3 = Color3.new(1, 1, 1)
Title.Font = Enum.Font.SourceSansBold
Title.TextSize = 19
Title.Parent = Frame
applySquircle(Title, 10)

-- Criar Dropdown (Lista de Jogadores)
local PlayerList = Instance.new("TextButton")
PlayerList.Text = "Selecionar Jogador"
PlayerList.Size = UDim2.new(1, -20, 0, 30)
PlayerList.Position = UDim2.new(0, 10, 0, 40)
PlayerList.BackgroundColor3 = Color3.new(0.3, 0.3, 0.3)
PlayerList.BackgroundTransparency = 0.2
PlayerList.TextColor3 = Color3.new(1, 1, 1)
PlayerList.Font = Enum.Font.SourceSans
PlayerList.TextSize = 16
PlayerList.Parent = Frame
applySquircle(PlayerList, 10)

-- Criar ScrollingFrame para a lista de jogadores
local DropDown = Instance.new("ScrollingFrame")
DropDown.Size = UDim2.new(0, 150, 0, 100)
DropDown.Position = UDim2.new(1, 10, 0, 40)
DropDown.BackgroundColor3 = Color3.new(0, 0, 0)
DropDown.BackgroundTransparency = 0.3
DropDown.BorderSizePixel = 0
DropDown.ScrollingEnabled = true
DropDown.CanvasSize = UDim2.new(0, 0, 0, 0)
DropDown.Visible = false
DropDown.Parent = Frame
applySquircle(DropDown, 10)

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
            Button.BackgroundColor3 = Color3.new(0, 0, 0)
            Button.BackgroundTransparency = 0.2
            Button.TextColor3 = Color3.new(1, 1, 1)
            Button.Font = Enum.Font.SourceSans
            Button.TextSize = 14
            Button.Parent = DropDown
            applySquircle(Button, 8)
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
TeleportButton.BackgroundColor3 = Color3.new(0, 0.3, 0)
TeleportButton.BackgroundTransparency = 0.2
TeleportButton.TextColor3 = Color3.new(1, 1, 1)
TeleportButton.Font = Enum.Font.SourceSansBold
TeleportButton.TextSize = 16
TeleportButton.Parent = Frame
applySquircle(TeleportButton, 10)

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
ToggleButton.Position = UDim2.new(1, -60, 0.1, 10) -- Ajustado para acompanhar o Frame mais para cima
ToggleButton.BackgroundColor3 = Color3.new(0, 0, 0)
ToggleButton.TextColor3 = Color3.new(1, 1, 1)
ToggleButton.Font = Enum.Font.SourceSansBold
ToggleButton.TextSize = 23
ToggleButton.Parent = ScreenGui
ToggleButton.Active = true
ToggleButton.Draggable = true
applySquircle(ToggleButton, 10)

ToggleButton.MouseButton1Click:Connect(function()
    Frame.Visible = not Frame.Visible
end)
    end
})

Tabs.Players:AddButton({
    Title = "Assistir jogador",
    Callback = function()
        loadstring(game:HttpGet("https://raw.githubusercontent.com/Vitoarieshub/assistir-jogador-/refs/heads/main/README.md"))()
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
            fpsLabel.BackgroundTransparency = 1 -- Transparente
            fpsLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
            fpsLabel.TextScaled = false
            fpsLabel.TextSize = 14
            fpsLabel.Font = Enum.Font.Code
            fpsLabel.Text = "FPS: 0"
            fpsLabel.Parent = screenGui
            fpsLabel.Active = true
            fpsLabel.Draggable = true
            fpsLabel.TextStrokeTransparency = 0.6
            fpsLabel.TextStrokeColor3 = Color3.new(0, 0, 0) -- Contorno leve

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

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
    Main = Window:AddTab({ Title = "Main" }),
    Players = Window:AddTab({ Title = "Players" }),
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
        notify("Noclip Ativado", "Você pode atravessar paredes agora!")
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
        notify("Noclip", "Noclip foi Desativado.")
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
    Title = "Noclip",
    Description = "Ativa/desativa a travessia de paredes",
    Default = false,
    Callback = toggleNoclip
})

Tabs.Main:AddToggle("infjump", {
    Title = "Infinite Jump",
    Description = "Ativa/desativa o pulo infinito",
    Default = false,
    Callback = function(state)
        notify(
            state and "Infinite Jump" or "Infinite Jump", 
            state and "Pulo infinito Ativado com sucesso!" or "Pulo infinito Desativado."
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
    Title = "JumpPower",
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
    Title = "WalkSpeed",
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
    Title = "FOV",
    Description = "Ajusta o campo de visão da câmera",
    Default = 70,
    Min = 30,
    Max = 120, -- Máximo permitido pelo Roblox
    Rounding = 1,
    Callback = function(value)
        game.Workspace.CurrentCamera.FieldOfView = value
    end
})

Tabs.Main:AddParagraph({ Title = "Quer fazer seu próprio script", Content = "Kwai:Vitoroficial Insta:vitoroemanuel"})

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

Tabs.Players:AddParagraph({ Title = "Teleport", Content = "Funciona em todos os servidores" })

Tabs.Players:AddButton({
    Title = "Teleport",
    Callback = function()
        loadstring(game:HttpGet('https://raw.githubusercontent.com/Infinity2346/Tect-Menu/main/Teleport%20Gui.lua'))()
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
        loadstring(game:HttpGet("https://raw.githubusercontent.com/AlgariBot/lua/refs/heads/Lua-Script-Executor/LocalNeverPatchedBypass.txt"))()
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
        local StarterGui = game:GetService("StarterGui")

        local player = Players.LocalPlayer
        local playerGui = player:FindFirstChild("PlayerGui") or player:WaitForChild("PlayerGui")

        -- Evita duplicação removendo qualquer contador existente
        local existingGui = playerGui:FindFirstChild("FPSCounter")
        if existingGui then
            existingGui:Destroy()
        end

        -- Criar ScreenGui
        local screenGui = Instance.new("ScreenGui")
        screenGui.Name = "FPSCounter"
        screenGui.ResetOnSpawn = false -- Mantém a GUI após respawn
        screenGui.Parent = playerGui

        -- Criar FPS Counter
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

        -- Melhorando o estilo
        fpsLabel.BorderSizePixel = 1
        fpsLabel.BorderColor3 = Color3.new(1, 1, 1)
        fpsLabel.TextStrokeTransparency = 0.6
        fpsLabel.TextStrokeColor3 = Color3.new(0, 0, 0)

        -- Salva no StarterGui para reaparecer após reset
        StarterGui:SetCoreGuiEnabled(Enum.CoreGuiType.All, true)

        -- Variáveis para medir FPS
        local lastTime = tick()
        local frameCount = 0

        -- Atualiza o contador de FPS
        local function updateFPS()
            frameCount = frameCount + 1
            local currentTime = tick()

            if currentTime - lastTime >= 1 then
                fpsLabel.Text = "FPS: " .. frameCount
                frameCount = 0
                lastTime = currentTime
            end
        end

        -- Conexão persistente para atualizar FPS
        RunService.RenderStepped:Connect(updateFPS)

        -- Recria o contador após respawn
        player.CharacterAdded:Connect(function()
            task.wait(1) -- Pequeno delay para garantir que a GUI carregue
            if not playerGui:FindFirstChild("FPSCounter") then
                screenGui.Parent = playerGui
            end
        end)
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
    Description = "Ativa/desativa a Anti void",
    Default = false,
    Callback = function(state)
        isAntiVoidActive = state
    end
})

-- Dragon menu (Beta)
-- Desenvolvido por Vitor
-- Script otimizado e organizado

-- Carregar Fluent
local Fluent = loadstring(game:HttpGet("https://github.com/dawid-scripts/Fluent/releases/latest/download/main.lua"))()

-- Função para enviar notificações
local function notify(title, content)
    Fluent:Notify({ Title = title, Content = content })
end

-- Aviso ao executar
notify("Executado!", "🐉 O melhor script universal executado com sucesso 🐉.")

-- Criar a janela principal
local Window = Fluent:CreateWindow({
    Title = "Dragon menu (Beta) " .. Fluent.Version,
    TabWidth = 90,
    Size = UDim2.fromOffset(420, 310),
    Theme = "Dark"
})

-- Tabela de abas
local Tabs = {
    Main = Window:AddTab({ Title = "Main" }),
    Players = Window:AddTab({ Title = "Players" }),
    Settings = Window:AddTab({ Title = "Config", Icon = "settings" })
}

-- Funções utilitárias
local function setHumanoidProperty(property, value)
    local player = game.Players.LocalPlayer
    local character = player.Character or player.CharacterAdded:Wait()
    local humanoid = character:WaitForChild("Humanoid")
    humanoid[property] = value
    print(property .. " ajustado para:", value)
end

-- Função para Infinite Jump
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

-- Função para Noclip
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
        notify("Noclip Desativado", "Noclip foi desligado.")
    end
end

-- Funções de atualização de lista de jogadores e teleporte
local function updatePlayerList(dropdown)
    local players = game.Players:GetPlayers()
    local playerNames = {}
    for _, player in ipairs(players) do
        table.insert(playerNames, player.Name)
    end
    dropdown:Refresh(playerNames)
end

local function teleportToPlayer(playerName)
    local player = game.Players:FindFirstChild(playerName)
    if player and player.Character then
        local character = game.Players.LocalPlayer.Character
        if character then
            character:SetPrimaryPartCFrame(player.Character.PrimaryPart.CFrame)
        end
    end
end

-- Aba: Início (Main)
Tabs.Main:AddParagraph({ Title = "Programador Vitor", Content = "Scripts personalizados" })

Tabs.Main:AddButton({
    Name = "Fly",
    Callback = function()
        loadstring(game:HttpGet("https://raw.githubusercontent.com/Vitoarieshub/Fly-universal-/refs/heads/main/README.md"))()
        notify("Fly", "Fly ativado!")
    end
})

Tabs.Main:AddToggle("Travessa Paredes", {
    Title = "Travessa Paredes",
    Description = "Ativa/desativa a travessia de paredes",
    Default = false,
    Callback = toggleNoclip
})

Tabs.Main:AddToggle("Pulo infinito", {
    Title = "Pulo infinito",
    Description = "Ativa/desativa o pulo infinito",
    Default = false,
    Callback = function(state)
        notify(state and "Infinite Jump Ativado" or "Infinite Jump Desativado", 
               state and "Pulo infinito ativado com sucesso!" or "Pulo infinito desativado.")
        toggleInfiniteJump(state)
    end
})

Tabs.Main:AddSlider("JumpPower", {
    Title = "Ajustar pulo",
    Description = "Define a altura do pulo",
    Default = 50,
    Min = 0,
    Max = 200,
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
    Max = 100,
    Rounding = 1,
    Callback = function(value)
        setHumanoidProperty("WalkSpeed", value)
    end
})

-- Aba: Jogadores (Players)
Tabs.Players:AddButton({
    Title = "ESP Universal",
    Callback = function()
        loadstring(game:HttpGet("https://pastebin.com/raw/rSUGN1fK"))()
    end
})

local teleportDropdown = Tabs.Players:AddDropdown({
    Name = "Teleportar no Player",
    Options = {},
    Default = nil,
    Callback = teleportToPlayer
})

Tabs.Players:AddButton({
    Name = "Atualizar Lista de Players",
    Callback = function()
        updatePlayerList(teleportDropdown)
        notify("Atualizar Lista", "Lista de jogadores atualizada!")
    end
})

-- Inicializar a Lista de Jogadores ao Carregar o Script
updatePlayerList(teleportDropdown)

-- Eventos para Atualizar a Lista quando um Jogador Entra ou Sai
game.Players.PlayerAdded:Connect(function()
    updatePlayerList(teleportDropdown)
end)

game.Players.PlayerRemoving:Connect(function()
    updatePlayerList(teleportDropdown)
end)

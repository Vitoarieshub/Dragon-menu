local Fluent = loadstring(game:HttpGet("https://github.com/dawid-scripts/Fluent/releases/latest/download/main.lua"))()

local Window = Fluent:CreateWindow({
    Title = "Dragon menu " .. Fluent.Version,
    TabWidth = 160, 
    Size = UDim2.fromOffset(480, 440),
    Theme = "Dark"
})

local Tabs = {
    Main = Window:AddTab({ Title = "Geral" }),
    Jogador = Window:AddTab({ Title = "Jogador" }),
    Settings = Window:AddTab({ Title = "Configuração", Icon = "settings" })
}

-- Função de Notificação
local function notify(title, text)
    game.StarterGui:SetCore("SendNotification", {
        Title = title,
        Text = text,
        Duration = 5
    })
end

-- Botão Fly
Tabs.Main:AddButton({
    Title = "Fly",
    Callback = function()
        loadstring(game:HttpGet("https://raw.githubusercontent.com/Vitoarieshub/Fly-universal-/refs/heads/main/README.md"))()
        notify("Fly", "Fly ativado!")
    end
})

-- Botão Fly Car
Tabs.Main:AddButton({
    Title = "Fly Car",
    Callback = function()
        loadstring(game:HttpGet("https://rawscripts.net/raw/Universal-Script-Fly-Car-Mobile-gui-11884"))()
        notify("Fly Car", "Fly Car ativado!")
    end
})

-- Função Noclip
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
        notify("Travessa Paredes", "Você pode atravessar paredes agora!")
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
        notify("Travessa Paredes", "Travessa Paredes desativado.")
    end
end

Tabs.Main:AddToggle({
    Title = "Travessa Paredes",
    Default = false,
    Callback = toggleNoclip
})

-- Função Pulo Infinito
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
        notify("Pulo Infinito", "Pulo infinito ativado!")
    else
        if jumpConnection then
            jumpConnection:Disconnect()
            jumpConnection = nil
        end
        notify("Pulo Infinito", "Pulo infinito desativado.")
    end
end

Tabs.Main:AddToggle({
    Title = "Pulo Infinito",
    Default = false,
    Callback = toggleInfiniteJump
})

-- Função para definir a gravidade
Tabs.Main:AddTextbox({
    Title = "Gravidade",
    Default = "196.2",
    TextDisappear = true,
    Callback = function(value)
        local gravityValue = tonumber(value)
        if gravityValue then
            game.Workspace.Gravity = gravityValue
            notify("Gravidade Alterada", "A gravidade foi ajustada para " .. value .. ".")
        else
            notify("Erro", "Por favor, digite um valor numérico válido para a gravidade.")
        end
    end
})

-- Ajustar Altura do Pulo
Tabs.Main:AddTextbox({
    Title = "Altura do Pulo",
    Default = "50",
    TextDisappear = true,
    Callback = function(value)
        local humanoid = game.Players.LocalPlayer.Character:FindFirstChild("Humanoid")
        if humanoid then
            humanoid.JumpPower = tonumber(value)
            notify("Altura do Pulo", "Altura do Pulo ajustada para " .. value)
        end
    end
})

-- Ajustar Velocidade
Tabs.Main:AddTextbox({
    Title = "Velocidade",
    Default = "20",
    TextDisappear = true,
    Callback = function(value)
        local humanoid = game.Players.LocalPlayer.Character:FindFirstChild("Humanoid")
        if humanoid then
            humanoid.WalkSpeed = tonumber(value)
            notify("Velocidade", "Velocidade ajustada para " .. value)
        end
    end
})

Tabs.Main:AddButton({
    Title = "Resetar Velocidade, Pulo e Gravidade",
    Callback = function()
        local player = game.Players.LocalPlayer
        local character = player.Character or player.CharacterAdded:Wait()
        local humanoid = character:FindFirstChildOfClass("Humanoid")

        if humanoid then
            humanoid.WalkSpeed = 20
            humanoid.JumpPower = 50
            game.Workspace.Gravity = 196.2
            
            notify("Resetado", "Velocidade, Altura do Pulo e Gravidade foram resetadas!")
        else
            notify("Erro", "Humanoide não encontrado!")
        end
    end
})

local playerDropdown
local EspectarConnection

-- Função para atualizar a lista de jogadores no dropdown
local function updatePlayerList(dropdown)
    local playerNames = {}
    for _, player in ipairs(game.Players:GetPlayers()) do
        table.insert(playerNames, player.Name)
    end
    dropdown:Refresh(playerNames, true)
end

-- Função para começar a espectar o jogador
local function spectatePlayer(playerName)
    local localPlayer = game.Players.LocalPlayer
    local player = game.Players:FindFirstChild(playerName)

    if player and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
        workspace.CurrentCamera.CameraSubject = player.Character.HumanoidRootPart
        notify("Espectador", "Você está agora espectando: " .. playerName)
    else
        notify("Erro", "Jogador não encontrado.")
    end
end

-- Função para parar de espectar e voltar para o personagem local
local function stopSpectating()
    workspace.CurrentCamera.CameraSubject = game.Players.LocalPlayer.Character:WaitForChild("Humanoid")
    notify("Espectador", "Você parou de espectar o jogador.")
end

local Section = Tabs.Jogador:AddSection({
    Name = "Espectar"
})

-- Criação do Dropdown para escolher o jogador para espectar
playerDropdown = Tabs.Jogador:AddDropdown({
    Name = "Espectar Jogador",
    Options = {},
    Callback = function(selectedPlayer)
        spectatePlayer(selectedPlayer)
    end
})

-- Botão para Atualizar a Lista de Jogadores
Tabs.Jogador:AddButton({
    Name = "Atualizar Lista de Jogadores",
    Callback = function()
        updatePlayerList(playerDropdown)
        notify("Atualizar Lista", "Lista de jogadores atualizada!")
    end
})

-- Botão para Parar de Espectar
Tabs.Jogador:AddButton({
    Name = "Parar de Espectar",
    Callback = function()
        stopSpectating()
    end
})

-- Inicializar a Lista de Jogadores ao Carregar o Script
updatePlayerList(playerDropdown)

-- Eventos para Atualizar a Lista quando um Jogador Entra ou Sai
game.Players.PlayerAdded:Connect(function()
    updatePlayerList(playerDropdown)
end)

game.Players.PlayerRemoving:Connect(function()
    updatePlayerList(playerDropdown)
end)

local teleportDropdown

-- Função para teleportar para outro jogador
local function teleportToPlayer(playerName)
    local player = game.Players:FindFirstChild(playerName)
    if player and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
        local character = game.Players.LocalPlayer.Character
        if character then
            character:MoveTo(player.Character.HumanoidRootPart.Position)
            notify("Teleporte", "Teleportado para " .. playerName)
        end
    else
        notify("Erro", "Jogador não encontrado.")
    end
end

local Section = Tabs.Jogador:AddSection({
    Name = "Teleporta"
})

-- Dropdown de Seleção de Jogador para teleportar
teleportDropdown = Tabs.Jogador:AddDropdown({
    Name = "Teleportar para Jogador",
    Options = {},
    Default = nil,
    Callback = function(selectedPlayer)
        teleportToPlayer(selectedPlayer)
    end
})

-- Botão para Atualizar Lista de Jogadores
Tabs.Jogador:AddButton({
    Name = "Atualizar Lista de Jogadores",
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

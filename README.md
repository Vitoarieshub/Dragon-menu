local Fluent = loadstring(game:HttpGet("https://github.com/dawid-scripts/Fluent/releases/latest/download/main.lua"))()

local Window = Fluent:CreateWindow({
    Title = "Dragon Menu " .. Fluent.Version,
    TabWidth = 160,
    Size = UDim2.fromOffset(480, 440),
    Theme = "Dark"
})

local Tabs = {
    Geral = Window:AddTab({ Title = "Geral" }),
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
Tabs.Geral:AddButton({
    Title = "Fly",
    Callback = function()
        loadstring(game:HttpGet("https://raw.githubusercontent.com/Vitoarieshub/Fly-universal-/refs/heads/main/README.md"))()
        notify("Fly", "Fly ativado!")
    end
})

-- Botão Fly Car
Tabs.Geral:AddButton({
    Title = "Fly Car",
    Callback = function()
        loadstring(game:HttpGet("https://rawscripts.net/raw/Universal-Script-Fly-Car-Mobile-gui-11884"))()
        notify("Fly Car", "Fly Car ativado!")
    end
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

Tabs.Geral:AddToggle({
    Title = "Pulo Infinito",
    Default = false,
    Callback = toggleInfiniteJump
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

Tabs.Geral:AddToggle({
    Title = "Travessa Paredes",
    Default = false,
    Callback = toggleNoclip
})

-- Função para definir a gravidade
Tabs.Geral:AddTextbox({
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
Tabs.Geral:AddTextbox({
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
Tabs.Geral:AddTextbox({
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

Tabs.Geral:AddButton({
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

-- Função para espectar jogador
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

-- Seção de Espectar
local Section = Tabs.Jogador:AddSection({
    Title = "Espectar"
})

-- Dropdown de seleção de jogador para espectar
local playerDropdown = Tabs.Jogador:AddDropdown({
    Title = "Espectar Jogador",
    Options = {},
    Callback = function(selectedPlayer)
        spectatePlayer(selectedPlayer)
    end
})

-- Função para atualizar a lista de jogadores no dropdown
local function updatePlayerList(dropdown)
    local playerNames = {}
    for _, player in ipairs(game.Players:GetPlayers()) do
        table.insert(playerNames, player.Name)
    end
    dropdown:Refresh(playerNames, true)
end

-- Botão para atualizar a lista de jogadores
Tabs.Jogador:AddButton({
    Title = "Atualizar Lista de Jogadores",
    Callback = function()
        updatePlayerList(playerDropdown)
        notify("Atualizar Lista", "Lista de jogadores atualizada!")
    end
})

-- Botão para parar de espectar
Tabs.Jogador:AddButton({
    Title = "Parar de Espectar",
    Callback = function()
        stopSpectating()
    end
})

-- Inicializar a lista de jogadores ao carregar o script
updatePlayerList(playerDropdown)

-- Eventos para atualizar a lista quando um jogador entra ou sai
game.Players.PlayerAdded:Connect(function()
    updatePlayerList(playerDropdown)
end)

game.Players.PlayerRemoving:Connect(function()
    updatePlayerList(playerDropdown)
end)

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

-- Seção de Teleporta
local Section = Tabs.Jogador:AddSection({
    Title = "Teleporta"
})

-- Dropdown de seleção de jogador para teleportar
local teleportDropdown = Tabs.Jogador:AddDropdown({
    Title = "Teleportar para Jogador",
    Options = {},
    Callback = function(selectedPlayer)
        teleportToPlayer(selectedPlayer)
    end
})

-- Botão para atualizar lista de jogadores
Tabs.Jogador:AddButton({
    Title = "Atualizar Lista de Jogadores",
    Callback = function()
        updatePlayerList(teleportDropdown)
        notify("Atualizar Lista", "Lista de jogadores atualizada!")
    end
})

-- Inicializar a lista de jogadores ao carregar o script
updatePlayerList(teleportDropdown)

-- Eventos para atualizar a lista quando um jogador entra ou sai
game.Players.PlayerAdded:Connect(function()
    updatePlayerList(teleportDropdown)
end)

game.Players.PlayerRemoving:Connect(function()
    updatePlayerList(teleportDropdown)
end)

-- Botão Anti Kick
Tabs.Settings:AddButton({
    Title = "Anti Kick",
    Callback = function()
        loadstring(game:HttpGet("https://raw.githubusercontent.com/Exunys/Anti-Kick/main/Anti-Kick.lua"))()
        notify("Anti Kick", "Anti Kick ativado!")
    end
})

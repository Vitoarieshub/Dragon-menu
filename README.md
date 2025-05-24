loadstring(game:HttpGet("https://raw.githubusercontent.com/Snxdfer/back-ups-for-libs/refs/heads/main/RedzUI.lua"))()

-- Cria a janela principal
MakeWindow({
    Hub = {
        Title = "Dragon Menu - Universal",
        Animation = "by : Vito0296poq"
    },
    
   Key = {
        KeySystem = false, -- Ativa o sistema de Key
        Title = "Sistema de Chave",
        Description = "Digite a chave correta para continuar.",
        KeyLink = "https://seusite.com/chave", -- Link para obter a chave (opcional)
        Keys = {"1234", "chave-extra"}, -- Chaves válidas
        Notifi = {
            Notifications = true,
            CorrectKey = "Chave correta! Iniciando script...",
            Incorrectkey = "Chave incorreta, tente novamente.",
            CopyKeyLink = "Link copiado!"
        }
    }
})

-- Botão de minimizar
MinimizeButton({
    Image = "rbxassetid://1234567890",
    Size = {40, 40},
    Color = Color3.fromRGB(10, 10, 10),
    Corner = true,
    Stroke = false,
    StrokeColor = Color3.fromRGB(255, 0, 0)
})

-- Criação da aba principal
local Main = MakeTab({Name = "Principal"})
local Player = MakeTab({Name = "Jogadores"})
local Settings = MakeTab({Name = "Configuração"})

-- Notificação inicial
-- Removido se não quiser notificação:
-- MakeNotifi({
--     Title = "Dragon Menu",
--     Text = "Carregando com sucesso!",
--     Time = 5
-- })

AddButton(Main, {
    Name = "Fly GUI v4",
    Callback = function()
        print("Botão foi clicado!")
        pcall(function()
            loadstring(game:HttpGet("https://raw.githubusercontent.com/Vitoarieshub/Fly-Gui-v4/main/FlyGuiV4.lua"))()
        end)
    end
})

-- Noclip
local noclipConnection

function toggleNoclip(enable)
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
    end
end

-- Toggle para ativar/desativar colisão
AddToggle(Main, {
    Name = "Desabilitar Colisões",
    Default = false,
    Callback = function(Value)
        toggleNoclip(Value)
    end
})

-- Infinite Jump
local jumpConnection
local function toggleInfiniteJump(enable)
    if enable then
        if not jumpConnection then
            jumpConnection = game:GetService("UserInputService").JumpRequest:Connect(function()
                local player = game.Players.LocalPlayer
                local character = player.Character or player.CharacterAdded:Wait()
                local humanoid = character:FindFirstChildOfClass("Humanoid")
                if humanoid then
                    humanoid:ChangeState(Enum.HumanoidStateType.Jumping)
                end
            end)
        end
    else
        if jumpConnection then
            jumpConnection:Disconnect()
            jumpConnection = nil
        end
    end
end

-- Toggle para ativar/desativar pulo infinito
local Toggle = AddToggle(Main, {
    Name = "Pulos infinito",
    Default = false,
    Callback = function(Value)
        toggleInfiniteJump(Value)
    end
})

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer

local velocidadeAtivada = false
local velocidadeValor = 25 -- valor inicial

-- Slider de Velocidade
AddSlider(Main, {
    Name = "Velocidade",
    MinValue = 16,
    MaxValue = 250,
    Default = 25,
    Increase = 1,
    Callback = function(Value)
        velocidadeValor = Value
        if velocidadeAtivada then
            local character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
            local humanoid = character:FindFirstChildOfClass("Humanoid")
            if humanoid then
                humanoid.WalkSpeed = velocidadeValor
            end
        end
    end
})

-- Toggle para ativar/desativar a velocidade
AddToggle(Main, {
    Name = "Velocidade",
    Default = false,
    Callback = function(Value)
        velocidadeAtivada = Value
        local character = LocalPlayer.Character or LocalPlayer.CharacterAdded:Wait()
        local humanoid = character:FindFirstChildOfClass("Humanoid")
        if humanoid then
            humanoid.WalkSpeed = Value and velocidadeValor or 16
        end
    end
})

local jumpAtivado = false
local jumpPowerSelecionado = 25
local jumpPowerPadrao = 50  -- Valor padrão do JumpPower do Roblox

-- Função para aplicar ou restaurar altura do pulo
local function aplicarJumpPower()
    local player = game.Players.LocalPlayer
    local character = player.Character or player.CharacterAdded:Wait()
    local humanoid = character:FindFirstChildOfClass("Humanoid")
    if humanoid then
        humanoid.UseJumpPower = true
        if jumpAtivado then
            humanoid.JumpPower = jumpPowerSelecionado
        else
            humanoid.JumpPower = jumpPowerPadrao
        end
    end
end

-- Slider de Altura do Pulo
AddSlider(Main, {
    Name = "Altura do pulo",
    MinValue = 10,
    MaxValue = 900,
    Default = 25,
    Increase = 1,
    Callback = function(Value)
        jumpPowerSelecionado = Value
        if jumpAtivado then
            aplicarJumpPower()
        end
    end
})

-- Toggle para ativar/desativar altura do pulo
AddToggle(Main, {
    Name = "Altura do pulo",
    Default = false,
    Callback = function(Value)
        jumpAtivado = Value
        aplicarJumpPower()
    end
})

local fovAtivado = false
local fovValor = 70 -- valor inicial padrão

-- Função para aplicar o FOV
local function aplicarFov()
    local camera = workspace.CurrentCamera
    if camera and fovAtivado then
        camera.FieldOfView = fovValor
    end
end

-- Atualiza FOV quando o personagem respawnar
game.Players.LocalPlayer.CharacterAdded:Connect(function()
    wait(0.5)
    aplicarFov()
end)

-- Slider para ajustar o FOV
AddSlider(Main, {
    Name = "Campo de visão",
    MinValue = 16,
    MaxValue = 120,
    Default = 70,
    Increase = 1,
    Callback = function(Value)
        fovValor = Value
        aplicarFov()
    end
})

-- Toggle para ativar/desativar o FOV
AddToggle(Main, {
    Name = "Campo de visão",
    Default = false,
    Callback = function(Value)
        fovAtivado = Value
        aplicarFov()
    end
})

-- Variáveis de controle
local espAtivado = false
local connections = {}

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer

-- Função para criar ESP
local function criarESP(player)
    if player == LocalPlayer then return end

    task.spawn(function()
        while espAtivado do
            local char = player.Character
            local head = char and char:FindFirstChild("Head")
            local humanoid = char and char:FindFirstChild("Humanoid")

            if char and head and humanoid and humanoid.Health > 0 then
                local esp = head:FindFirstChild("ESP")
                if not esp then
                    esp = Instance.new("BillboardGui")
                    esp.Name = "ESP"
                    esp.Adornee = head
                    esp.Size = UDim2.new(0, 150, 0, 50)
                    esp.StudsOffset = Vector3.new(0, 2, 0)
                    esp.AlwaysOnTop = true

                    local text = Instance.new("TextLabel")
                    text.Name = "Texto"
                    text.Size = UDim2.new(1, 0, 1, 0)
                    text.BackgroundTransparency = 1
                    text.TextColor3 = Color3.fromRGB(255, 255, 255)
                    text.TextSize = 14
                    text.TextScaled = false
                    text.Font = Enum.Font.GothamBold
                    text.TextStrokeTransparency = 0.4
                    text.TextStrokeColor3 = Color3.new(0, 0, 0)
                    text.Parent = esp

                    esp.Parent = head

                    humanoid.Died:Connect(function()
                        if esp then esp:Destroy() end
                    end)
                end

                local texto = esp:FindFirstChild("Texto")
                if texto and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") and char:FindFirstChild("HumanoidRootPart") then
                    local distancia = (LocalPlayer.Character.HumanoidRootPart.Position - char.HumanoidRootPart.Position).Magnitude
                    texto.Text = player.Name .. " - " .. math.floor(distancia) .. "m"
                end
            end
            wait(0.3)
        end
    end)
end

-- Monitorar jogadores
local function monitorarPlayer(player)
    if connections[player] then
        connections[player]:Disconnect()
    end

    connections[player] = player.CharacterAdded:Connect(function()
        wait(1)
        if espAtivado then
            criarESP(player)
        end
    end)

    criarESP(player)
end

-- Toggle para ativar/desativar o ESP
AddToggle(Player, {
    Name = "ESP nome",
    Default = false,
    Callback = function(Value)
        espAtivado = Value

        if espAtivado then
            for _, player in ipairs(Players:GetPlayers()) do
                monitorarPlayer(player)
            end

            connections["PlayerAdded"] = Players.PlayerAdded:Connect(function(player)
                monitorarPlayer(player)
            end)
        else
            for _, player in ipairs(Players:GetPlayers()) do
                local char = player.Character
                if char then
                    local head = char:FindFirstChild("Head")
                    if head then
                        local esp = head:FindFirstChild("ESP")
                        if esp then
                            esp:Destroy()
                        end
                    end
                end
                if connections[player] then
                    connections[player]:Disconnect()
                    connections[player] = nil
                end
            end

            if connections["PlayerAdded"] then
                connections["PlayerAdded"]:Disconnect()
                connections["PlayerAdded"] = nil
            end
        end
    end
})

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer
local linhas = {}
local espConnections = {}
local espLinhaAtivado = false
local corVermelha = Color3.fromRGB(255, 0, 0)

local function criarLinha(player)
    if player == LocalPlayer then return end

    if linhas[player] then
        linhas[player]:Remove()
        linhas[player] = nil
    end
    if espConnections[player] then
        espConnections[player]:Disconnect()
        espConnections[player] = nil
    end

    local linha = Drawing.new("Line")
    linha.Thickness = 2
    linha.Transparency = 1
    linha.Visible = false
    linha.Color = corVermelha
    linhas[player] = linha

    espConnections[player] = RunService.RenderStepped:Connect(function()
        if not espLinhaAtivado then
            linha.Visible = false
            return
        end

        local char = player.Character
        local head = char and char:FindFirstChild("Head")
        if not head then
            linha.Visible = false
            return
        end

        local cam = workspace.CurrentCamera
        local screenSize = cam.ViewportSize
        local headPos, onScreen = cam:WorldToViewportPoint(head.Position)

        if onScreen then
            linha.From = Vector2.new(screenSize.X / 2, 0)
            linha.To = Vector2.new(headPos.X, headPos.Y)
            linha.Visible = true
        else
            linha.Visible = false
        end
    end)

    player.CharacterAdded:Connect(function()
        wait(1)
        if espLinhaAtivado then
            criarLinha(player)
        end
    end)
end

function ativarESP()
    for _, p in ipairs(Players:GetPlayers()) do
        criarLinha(p)
    end
    espConnections["PlayerAdded"] = Players.PlayerAdded:Connect(function(p)
        wait(1)
        criarLinha(p)
    end)
end

function desativarESP()
    for _, linha in pairs(linhas) do
        if linha then linha:Remove() end
    end
    linhas = {}
    for _, conn in pairs(espConnections) do
        if conn then conn:Disconnect() end
    end
    espConnections = {}
end

AddToggle(Player, {
    Name = "ESP Linha",
    Default = false,
    Callback = function(Value)
        espLinhaAtivado = Value
        if espLinhaAtivado then
            ativarESP()
        else
            desativarESP()
        end
    end
})

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local RunService = game:GetService("RunService")

local playerName = ""
local observando = false
local observarConnection = nil

-- Caixa de texto para digitar nome do jogador
AddTextBox(Player, {
    Name = "Digite o nome do jogador",
    Default = "",
    Placeholder = "Nome do jogador aqui...",
    Callback = function(text)
        playerName = text
    end
})

-- Função para encontrar jogador pelo nome digitado (busca começa pelo começo do nome)
local function encontrarJogador(nome)
    local lowerName = nome:lower()
    for _, player in pairs(Players:GetPlayers()) do
        if player.Name:lower():sub(1, #lowerName) == lowerName then
            return player
        end
    end
    return nil
end

-- Função para parar de observar
local function pararObservar()
    if observarConnection then
        observarConnection:Disconnect()
        observarConnection = nil
    end
    observando = false

    -- Reseta a câmera para modo padrão (seguindo o próprio personagem)
    if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
        workspace.CurrentCamera.CameraSubject = LocalPlayer.Character.Humanoid
    end

    print("Observação desativada.")
end

-- Função para iniciar observação (mudar câmera para seguir jogador)
local function iniciarObservar(jogador)
    if not jogador or jogador == LocalPlayer then
        warn("Jogador inválido para observar.")
        return
    end

    observando = true

    if not jogador.Character or not jogador.Character:FindFirstChild("Humanoid") then
        warn("Personagem do jogador não está disponível.")
        return
    end

    -- Define a câmera para seguir o humanoide do jogador alvo
    workspace.CurrentCamera.CameraSubject = jogador.Character.Humanoid
    print("Observando " .. jogador.Name)
end

AddToggle(Player, {
    Name = "Observar",
    Default = false,
    Callback = function(Value)
        if Value then
            local jogador = encontrarJogador(playerName)
            if jogador then
                iniciarObservar(jogador)
            else
                warn("Jogador não encontrado para observar.")
            end
        else
            pararObservar()
        end
    end
})

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local RunService = game:GetService("RunService")

local playerName = ""
local teleportLoopConnection = nil
local teleportando = false

-- Caixa de texto para digitar nome do jogador
AddTextBox(Player, {
    Name = "Digite o nome do jogador",
    Default = "",
    Placeholder = "Nome do jogador aqui...",
    Callback = function(text)
        playerName = text
    end
})

-- Função para encontrar jogador pelo nome digitado (busca começa pelo começo do nome)
local function encontrarJogador(nome)
    local lowerName = nome:lower()
    for _, player in pairs(Players:GetPlayers()) do
        if player.Name:lower():sub(1, #lowerName) == lowerName then
            return player
        end
    end
    return nil
end

-- Função para parar o teleporte em loop
local function pararTeleportar()
    if teleportLoopConnection then
        teleportLoopConnection:Disconnect()
        teleportLoopConnection = nil
    end
    teleportando = false
    print("Teleporte em loop desativado.")
end

-- Função para iniciar teleporte em loop
local function iniciarTeleportar(jogador)
    if not jogador or jogador == LocalPlayer then
        warn("Jogador inválido para teleportar.")
        return
    end

    teleportando = true

    if not jogador.Character or not jogador.Character:FindFirstChild("HumanoidRootPart") then
        warn("Personagem do jogador não está disponível.")
        return
    end

    teleportLoopConnection = RunService.RenderStepped:Connect(function()
        if not teleportando then return end

        local localChar = LocalPlayer.Character
        local targetChar = jogador.Character

        if localChar and targetChar and localChar:FindFirstChild("HumanoidRootPart") and targetChar:FindFirstChild("HumanoidRootPart") then
            -- Teleporta perto do jogador (distância ajustável)
            localChar.HumanoidRootPart.CFrame = targetChar.HumanoidRootPart.CFrame * CFrame.new(3, 0, 3)
        else
            warn("Personagem do jogador ou seu personagem não está disponível.")
        end
    end)

    print("Teleportando em loop para " .. jogador.Name)
end

-- Toggle para ativar/desativar teleporte em loop
AddToggle(Player, {
    Name = "Teleporte em Loop",
    Default = false,
    Callback = function(Value)
        if Value then
            local jogador = encontrarJogador(playerName)
            if jogador then
                iniciarTeleportar(jogador)
            else
                warn("Jogador não encontrado para teleportar.")
            end
        else
            pararTeleportar()
        end
    end
})

local Players = game:GetService("Players")
local StarterGui = game:GetService("StarterGui")

-- Variável para armazenar o estado das notificações
local notificacaoAtivada = false

-- Função para exibir notificações
local function notify(title, text)
    if notificacaoAtivada then
        StarterGui:SetCore("SendNotification", {
            Title = title,
            Text = text,
            Duration = 5
        })
    end
end

-- Notifica quando um jogador entra no jogo
Players.PlayerAdded:Connect(function(player)
    notify("Jogador entrou", player.Name .. " entrou no jogo.")
end)

-- Notifica quando um jogador sai do jogo
Players.PlayerRemoving:Connect(function(player)
    notify("Jogador saiu", player.Name .. " saiu do jogo.")
end)

-- Toggle para ativar/desativar as notificações
AddToggle(Settings, {
    Name = "Notificações de jogadores",
    Default = false,
    Callback = function(Value)
        notificacaoAtivada = Value
        notify("Notificações", Value and "Ativadas" or "Desativadas")
    end
})

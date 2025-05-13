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
    Title = "Dragon Menu | Universal legít  " .. Fluent.Version,
    TabWidth = 90,
    Size = UDim2.fromOffset(370, 300),
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

local velocidadeAtiva = false
local velocidadeValor = 20 -- valor padrão

-- Slider para ajustar o valor da velocidade
Tabs.Main:AddSlider("WalkSpeed", {
    Title = "Velocidade",
    Description = "Define a velocidade do jogador",
    Default = 20,
    Min = 0,
    Max = 200,
    Rounding = 1,
    Callback = function(value)
        velocidadeValor = value
        if velocidadeAtiva then
            setHumanoidProperty("WalkSpeed", velocidadeValor)
        end
    end
})

-- Toggle para ativar/desativar a velocidade
Tabs.Main:AddToggle("velocidade", {
    Title = "Velocidade",
    Description = "Ativa/desativa a velocidade",
    Default = false,
    Callback = function(state)
        velocidadeAtiva = state
        if state then
            setHumanoidProperty("WalkSpeed", velocidadeValor)
        else
            setHumanoidProperty("WalkSpeed", 20) -- valor padrão do Roblox
        end
    end
})

Tabs.Main:AddParagraph({ Title = "Segue nas redes sociais", Content = "Kwai:Vitoroficial Insta:vitoroemanuel"})

-- Variável para armazenar o estado do ESP
local espAtivado = false
local connections = {}

Tabs.Visual:AddToggle("esp_nome_distancia", {
    Title = "ESP Nome (Aualizado)",
    Description = "Ativa/Desativa ESP Nome e Distância",
    Default = false,
    Callback = function(state)
        espAtivado = state
        local Players = game:GetService("Players")
        local LocalPlayer = Players.LocalPlayer

        -- Função para criar ESP
        local function criarESP(player)
            if player == LocalPlayer then return end
            if not espAtivado then return end

            -- Atualização contínua
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
                            text.TextColor3 = Color3.fromRGB(255, 255, 255) -- Cor branca
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
                            texto.Text = player.Name .. " [" .. math.floor(distancia) .. "m]"
                        end
                    end
                    wait(0.3)
                end
            end)
        end

        -- Função para monitorar respawns e mudanças
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

        if espAtivado then
            for _, player in ipairs(Players:GetPlayers()) do
                monitorarPlayer(player)
            end

            connections["PlayerAdded"] = Players.PlayerAdded:Connect(function(player)
                monitorarPlayer(player)
            end)
        else
            -- Desativar ESP
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

-- Variável para armazenar o estado do ESP
local espAtivado = false
local linhas = {} -- Tabela para armazenar as linhas criadas
local connections = {} -- Tabela para armazenar conexões dos jogadores
local corVermelha = Color3.fromRGB(255, 0, 0)

-- Função para criar e atualizar a linha ESP
local function criarLinha(player)
    if player == game.Players.LocalPlayer then return end

    -- Remove linha e conexão antiga, se existir
    if linhas[player] then
        linhas[player]:Remove()
        linhas[player] = nil
    end
    if connections[player] then
        connections[player]:Disconnect()
        connections[player] = nil
    end

    local linha = Drawing.new("Line")
    linha.Thickness = 2
    linha.Transparency = 1
    linha.Visible = false
    linha.Color = corVermelha
    linhas[player] = linha

    -- Atualizar a linha a cada frame
    connections[player] = game:GetService("RunService").RenderStepped:Connect(function()
        local character = player.Character
        if not character then
            linha.Visible = false
            return
        end

        local head = character:FindFirstChild("Head")
        if not head or not head:IsA("BasePart") then
            linha.Visible = false
            return
        end

        if not espAtivado then
            linha.Visible = false
            return
        end

        local camera = workspace.CurrentCamera
        local screenSize = camera.ViewportSize
        local headPosition, onScreen = camera:WorldToViewportPoint(head.Position)

        if onScreen then
            linha.From = Vector2.new(screenSize.X / 2, 0)
            linha.To = Vector2.new(headPosition.X, headPosition.Y)
            linha.Visible = true
        else
            linha.Visible = false
        end
    end)

    -- Atualiza a linha sempre que o Character mudar
    player.CharacterAdded:Connect(function()
        wait(0.5)
        if espAtivado then
            criarLinha(player)
        end
    end)
end

-- Ativa o ESP para todos os jogadores
local function ativarESP()
    for _, player in pairs(game.Players:GetPlayers()) do
        criarLinha(player)
    end

    connections["PlayerAdded"] = game.Players.PlayerAdded:Connect(function(player)
        player.CharacterAdded:Connect(function()
            wait(0.5)
            if espAtivado then
                criarLinha(player)
            end
        end)
        criarLinha(player)
    end)
end

-- Desativa o ESP e limpa tudo
local function desativarESP()
    for _, linha in pairs(linhas) do
        if linha then
            linha:Remove()
        end
    end
    linhas = {}

    for _, connection in pairs(connections) do
        if connection then
            connection:Disconnect()
        end
    end
    connections = {}
end

-- Toggle para ativar/desativar o ESP
Tabs.Visual:AddToggle("esp_linha_rgb", {
    Title = "ESP Linha (Atualizado)",
    Description = "Ativa/Desativa ESP linha vermelha",
    Default = false,
    Callback = function(state)
        espAtivado = state
        if state then
            ativarESP()
        else
            desativarESP()
        end
    end
})

local Players = game:GetService("Players")
local player = Players.LocalPlayer
local camera = workspace.CurrentCamera
local savedFOV = 70 -- Valor padrão do FOV

-- Função para aplicar o FOV salvo
local function applySavedFOV()
    camera.FieldOfView = savedFOV
end

-- Aplicar o FOV salvo ao iniciar
applySavedFOV()

-- Aplicar o FOV salvo sempre que o personagem for adicionado
player.CharacterAdded:Connect(function()
    -- Esperar um pequeno intervalo para garantir que o personagem esteja totalmente carregado
    wait(0.1)
    applySavedFOV()
end)

-- Toggle para ativar/desativar o campo de visão
Tabs.Visual:AddToggle("campo", {
    Title = "Campo de visão",
    Description = "Ativa/desativa o campo de visão",
    Default = false,
    Callback = function(value)
        if value then
            savedFOV = 150 -- ou qualquer valor aumentado
        else
            savedFOV = 70 -- valor padrão
        end
        applySavedFOV()
    end
})

Tabs.Players:AddButton({
    Title = "Teleporte (anti ban)",
    Callback = function()
        loadstring(game:HttpGet("https://raw.githubusercontent.com/Vitoarieshub/Teleporte-/refs/heads/main/README.md"))()
    end
})

Tabs.Players:AddButton({
    Title = "Assistir jogador",
    Callback = function()
        loadstring(game:HttpGet("https://raw.githubusercontent.com/Vitoarieshub/assistir-jogador-/refs/heads/main/README.md"))()
    end
})

-- Variável para armazenar o estado das notificações
local notificacaoAtivada = false

-- Função para exibir notificações
local function notify(title, text)
    if notificacaoAtivada then
        game.StarterGui:SetCore("SendNotification", {
            Title = title,
            Text = text,
            Duration = 5
        })
    end
end

-- Notifica quando um jogador entra no jogo
game.Players.PlayerAdded:Connect(function(player)
    notify("Jogador", player.Name .. " entrou no jogo.")
end)

-- Notifica quando um jogador sai do jogo
game.Players.PlayerRemoving:Connect(function(player)
    notify("Jogador", player.Name .. " saiu do jogo.")
end)

-- Adiciona a opção de ativar/desativar notificações
Tabs.Settings:AddToggle("notificacao", {
    Title = "Notificações",
    Description = "de entrada/saída de jogadores",
    Default = false,
    Callback = function(state)
        notificacaoAtivada = state
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
            fpsLabel.Position = UDim2.new(1, -290, 0, 1)
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

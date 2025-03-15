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
    Title = "Dragon Menu  " .. Fluent.Version,
    TabWidth = 90,
    Size = UDim2.fromOffset(420, 310),
    Theme = "Dark"
})

-- Tabela de abas
local Tabs = {
    Main = Window:AddTab({ Title = "Ínício" }),
    Visual = Window:AddTab({ Title = "Visual" }),
    Players = Window:AddTab({ Title = "Jogadores" }),
    Exploits = Window:AddTab({ Title = "Exploits" }),
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

-- Variável para armazenar o estado do ESP
local espAtivado = false
local connections = {}

Tabs.Visual:AddToggle("esp_nome_distancia", {
    Title = "ESP Nome (Atualizado)",
    Description = "Ativa/Desativa ESP Nome e Distância",
    Default = false,
    Callback = function(state)
        espAtivado = state
        local Players = game:GetService("Players")
        local LocalPlayer = Players.LocalPlayer

        -- Função para criar ESP
        local function criarESP(player)
            if player == LocalPlayer then return end
            if not espAtivado then return end -- Impede a criação se o ESP estiver desligado

            local char = player.Character or player.CharacterAdded:Wait()
            local head = char:FindFirstChild("Head", 5)
            local humanoid = char:FindFirstChild("Humanoid")

            if head and humanoid then
                local esp = head:FindFirstChild("ESP")
                if not esp then
                    esp = Instance.new("BillboardGui")
                    esp.Name = "ESP"
                    esp.Adornee = head
                    esp.Size = UDim2.new(0, 150, 0, 50) -- Ajusta o tamanho para incluir nome e distância
                    esp.StudsOffset = Vector3.new(0, 2, 0)
                    esp.AlwaysOnTop = true

                    local text = Instance.new("TextLabel")
                    text.Size = UDim2.new(1, 0, 1, 0)
                    text.BackgroundTransparency = 1
                    text.TextColor3 = Color3.fromRGB(255, 255, 255) -- Cor branca
                    text.TextSize = 14 -- Tamanho da fonte
                    text.TextScaled = false -- Não usa escala automática
                    text.Font = Enum.Font.GothamBold
                    text.TextStrokeTransparency = 0.4
                    text.TextStrokeColor3 = Color3.new(0, 0, 0)
                    text.Parent = esp

                    esp.Parent = head
                end

                -- Atualiza o texto para incluir o nome e a distância
                local function atualizarTexto()
                    while espAtivado and char and char.Parent and head.Parent and humanoid.Health > 0 do
                        if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") and char:FindFirstChild("HumanoidRootPart") then
                            local distancia = (LocalPlayer.Character.HumanoidRootPart.Position - char.HumanoidRootPart.Position).Magnitude
                            esp.TextLabel.Text = player.Name .. " - " .. math.floor(distancia) .. "m"
                        end
                        wait(0.1) -- Atualiza a cada 0.1 segundos
                    end

                    -- Remove o ESP quando o personagem morre
                    if esp then
                        esp:Destroy()
                    end
                end

                -- Inicia a atualização do texto
                task.spawn(atualizarTexto)

                -- Adiciona um evento para remover o ESP quando o jogador morre
                humanoid.Died:Connect(function()
                    if esp then
                        esp:Destroy()
                    end
                end)
            end
        end

        -- Função para monitorar renascimentos e atualizar ESP automaticamente
        local function monitorarPersonagem(player)
            if espAtivado then
                if connections[player] then
                    connections[player]:Disconnect() -- Desconectar conexão antiga
                end

                connections[player] = player.CharacterAdded:Connect(function(char)
                    wait(1) -- Espera o personagem carregar
                    if espAtivado then
                        criarESP(player)
                    end
                end)
            end
        end

        -- Ativar ESP para todos os jogadores
        if espAtivado then
            for _, player in ipairs(Players:GetPlayers()) do
                criarESP(player)
                monitorarPersonagem(player)
            end

            -- Criar ESP para novos jogadores somente quando o ESP estiver ligado
            connections["PlayerAdded"] = Players.PlayerAdded:Connect(function(player)
                monitorarPersonagem(player)
                criarESP(player)
            end)
        else
            -- Desativar ESP completamente
            for _, player in ipairs(Players:GetPlayers()) do
                if connections[player] then
                    connections[player]:Disconnect()
                    connections[player] = nil
                end
            end

            -- Desconectar evento de novos jogadores quando o ESP estiver desligado
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

-- Função para gerar uma cor RGB animada
local function gerarCorRGB()
    local r = math.sin(tick()) * 127 + 128
    local g = math.sin(tick() + 2) * 127 + 128
    local b = math.sin(tick() + 4) * 127 + 128
    return Color3.fromRGB(r, g, b)
end

-- Função para criar uma linha ESP para um jogador
local function criarLinha(player)
    if player == game.Players.LocalPlayer then return end

    local function atualizarLinha()
        local character = player.Character
        if not character then return end

        local head = character:FindFirstChild("Head")
        if not head then return end

        -- Criar nova linha se ainda não existir
        local linha = linhas[player]
        if not linha then
            linha = Drawing.new("Line")
            linha.Thickness = 2
            linha.Transparency = 1
            linha.Visible = false
            linhas[player] = linha
        end

        -- Atualizar posição e cor da linha constantemente
        local connection
        connection = game:GetService("RunService").RenderStepped:Connect(function()
            if not espAtivado or not character.Parent or not head.Parent then
                linha.Visible = false
                return
            end

            local camera = game.Workspace.CurrentCamera
            local screenSize = camera.ViewportSize
            local headPosition, onScreen = camera:WorldToViewportPoint(head.Position)

            if onScreen then
                linha.From = Vector2.new(screenSize.X / 2, 0) -- Linha saindo do topo da tela (centro)
                linha.To = Vector2.new(headPosition.X, headPosition.Y) -- Até a cabeça do jogador
                linha.Color = gerarCorRGB() -- Atualiza a cor constantemente
                linha.Visible = true
            else
                linha.Visible = false
            end
        end)

        -- Salvar conexão para remover depois
        connections[player] = connection
    end

    -- Monitorar renascimento do jogador
    if connections[player] then
        connections[player]:Disconnect() -- Desconectar qualquer conexão antiga
    end

    connections[player] = player.CharacterAdded:Connect(function()
        wait(0.5) -- Pequeno delay para evitar erros
        if espAtivado then
            criarLinha(player) -- Recria a linha quando o jogador renasce
        end
    end)

    atualizarLinha() -- Criar linha imediatamente
end

-- Ativar ESP para todos os jogadores
local function ativarESP()
    for _, player in pairs(game.Players:GetPlayers()) do
        criarLinha(player)
    end

    -- Monitorar novos jogadores entrando no jogo
    connections["PlayerAdded"] = game.Players.PlayerAdded:Connect(function(player)
        criarLinha(player)
    end)
end

-- Desativar ESP e remover todas as linhas
local function desativarESP()
    for _, linha in pairs(linhas) do
        if linha then
            linha:Remove()
        end
    end
    linhas = {} -- Limpa a tabela de linhas

    -- Desconectar todas as conexões para evitar vazamento de memória
    for player, connection in pairs(connections) do
        if connection then
            connection:Disconnect()
        end
    end
    connections = {}
end

-- Adiciona a opção de ativar/desativar o ESP
Tabs.Visual:AddToggle("esp_linha_rgb", {
    Title = "ESP Linha (Atualizado)",
    Description = "Ativa/Desativa ESP linha",
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

Tabs.Players:AddButton({
    Title = "Teleporte",
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

Tabs.Exploits:AddButton({
    Title = "Fly car",
    Callback = function()
        loadstring(game:HttpGet("https://rawscripts.net/raw/Universal-Script-Android-vfly-24974"))()
    end
})

Tabs.Exploits:AddButton({
    Title = "Grudar portas",
    Callback = function()
        loadstring(game:HttpGet("https://rawscripts.net/raw/Universal-Script-Bring-Parts-27586"))()
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

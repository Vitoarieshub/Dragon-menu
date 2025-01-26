Tabs.Main:AddButton({
    Title = "Resetar velocidade altura do pulo e gravidade",
    Callback = function()
    end
})

Tabs.Main:AddButton({
    Title = "Resetar",
    Callback = function()
        -- Reseta a velocidade de caminhada para o padrão
        setHumanoidProperty("WalkSpeed", 20)
        
        -- Reseta a altura do pulo para o padrão
        setHumanoidProperty("JumpPower", 50)
        
        -- Reseta a gravidade para o padrão
        game.Workspace.Gravity = 196.2
        
        -- Envia uma notificação ao jogador
        notify("Resetado!", "Velocidade, altura do pulo e gravidade.")
    end
})

-- Dragon menu [Beta]
-- Desenvolvido por Victor 
-- Script otimizado 

-- Tentar carregar Fluent
local success, Fluent = pcall(function()
    return loadstring(game:HttpGet("https://github.com/dawid-scripts/Fluent/releases/latest/download/main.lua"))()
end)

if not success or not Fluent then
    warn("Erro ao carregar o Fluent. Verifique o link ou a conexão com a internet.")
    return
end

-- Função para enviar notificações
local function notify(title, content)
    pcall(function()
        Fluent:Notify({
            Title = title,
            Content = content,
            Duration = 3 -- Define a duração da notificação para 3 segundos
        })
    end)
end

-- Aviso ao executar
notify("Executado!", "Script executado com sucesso.")

-- Criar a janela principal
local Window = Fluent:CreateWindow({
    Title = "Dragon menu [Beta] " .. Fluent.Version,
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
    if not player.Character then return end
    local humanoid = player.Character:FindFirstChildOfClass("Humanoid")
    if humanoid then
        humanoid[property] = value
        print(property .. " ajustado para:", value)
    else
        warn("Humanoid não encontrado!")
    end
end

-- Infinite Jump
local jumpConnection
local function toggleInfiniteJump(enable)
    if enable then
        if not jumpConnection then
            jumpConnection = game:GetService("UserInputService").JumpRequest:Connect(function()
                local player = game.Players.LocalPlayer
                if not player.Character then return end
                local humanoid = player.Character:FindFirstChildOfClass("Humanoid")
                if humanoid then
                    humanoid:ChangeState(Enum.HumanoidStateType.Jumping)
                end
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
                local character = game.Players.LocalPlayer.Character
                if character then
                    for _, part in pairs(character:GetDescendants()) do
                        if part:IsA("BasePart") then
                            part.CanCollide = false
                        end
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
        local character = game.Players.LocalPlayer.Character
        if character then
            for _, part in pairs(character:GetDescendants()) do
                if part:IsA("BasePart") then
                    part.CanCollide = true
                end
            end
        end
        notify("Noclip Desativado", "Noclip foi desligado.")
    end
end

-- Aba: Início
Tabs.Main:AddParagraph({ Title = "Programador Victor", Content = "Scripts personalizados" })

Tabs.Main:AddButton({
    Title = "Fly",
    Callback = function()
        local success, err = pcall(function()
            loadstring(game:HttpGet("https://raw.githubusercontent.com/Vitoarieshub/Fly-universal-/refs/heads/main/README.md"))()
        end)
        if not success then
            warn("Erro ao carregar o Fly:", err)
            notify("Erro", "Não foi possível carregar o Fly.")
        end
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
            state and "Infinite Jump Ativado" or "Infinite Jump Desativado", 
            state and "Pulo infinito ativado com sucesso!" or "Pulo infinito desativado."
        )
        toggleInfiniteJump(state)
    end
})

Tabs.Main:AddSlider("Gravity", {
    Title = "Gravidade",
    Description = "Ajusta a gravidade do jogador",
    Default = 196.2,
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
    Max = 400,
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

Tabs.Main:AddButton({
    Title = "Resetar",
    Callback = function()
        setHumanoidProperty("WalkSpeed", 20)
        setHumanoidProperty("JumpPower", 50)
        game.Workspace.Gravity = 196.2
        notify("Resetado!", "Velocidade, altura do pulo e gravidade foram resetadas.")
    end
})

-- Aba: Jogadores e Exploits
-- Resto do script continua igual

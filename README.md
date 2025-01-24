-- Dragão menu [Beta]
-- Desenvolvido por Victor 
-- Script otimizado 

-- Carregar Fluent
local Fluent = loadstring(game:HttpGet("https://github.com/dawid-scripts/Fluent/releases/latest/download/main.lua"))()

-- Função para enviar notificações
local function notify(title, content)
    Fluent:Notify({ Title = title, Content = content })
end

-- Aviso ao executar
notify("Executado!", "Script executado com sucesso.")

-- Criar a janela principal
local Window = Fluent:CreateWindow({
    Title = "Dragão menu [Beta] " .. Fluent.Version,
    TabWidth = 90,
    Size = UDim2.fromOffset(420, 310),
    Theme = "Dark"
})

-- Tabela de abas
local Tabs = {
    Main = Window:AddTab({ Title = "Início" }),
    Players = Window:AddTab({ Title = "Jogadores" }),
    Settings = Window:AddTab({ Title = "Configuração", Icon = "settings" })
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
        notify("Noclip Desativado", "Noclip foi desligado.")
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
            state and "Infinite Jump Ativado" or "Infinite Jump Desativado", 
            state and "Pulo infinito ativado com sucesso!" or "Pulo infinito desativado."
        )
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
    Max = 200,
    Rounding = 1,
    Callback = function(value)
        setHumanoidProperty("WalkSpeed", value)
    end
})

-- Aba: Jogadores
Tabs.Players:AddParagraph({ Title = "ESP", Content = "funcionar alguns servidores" })

-- Aba: Jogadores
Tabs.Players:AddButton({
    Title = "ESP Nome",
    Callback = function()
        loadstring(game:HttpGet("https://pastebin.com/raw/rSUGN1fK"))()
    end
})

-- Aba: Jogadores
Tabs.Players:AddButton({
    Title = "ESP Linhas",
    Callback = function()
        loadstring(game:HttpGet("https://pastebin.com/raw/nnHbfvGW"))()
    end
})

-- Aba: Configuração
Tabs.Configuração:AddButton({
    Title = "Anti Kick",
    Callback = function()
        https://raw.githubusercontent.com/Exunys/Anti-Kick/main/Anti-Kick.lua
    end
})

-- Função para aplicar o boost de FPS
local function applyFPSBoost()
    settings().Rendering.QualityLevel = Enum.QualityLevel.Level01
    game:GetService("Lighting").GlobalShadows = false

    for _, v in pairs(game:GetService("Workspace"):GetDescendants()) do
        if v:IsA("Part") or v:IsA("UnionOperation") or v:IsA("MeshPart") then
            v.Material = Enum.Material.SmoothPlastic
            v.Reflectance = 0
        elseif v:IsA("Decal") or v:IsA("Texture") then
            v:Destroy()
        elseif v:IsA("ParticleEmitter") or v:IsA("Trail") then
            v:Destroy()
        elseif v:IsA("PointLight") or v:IsA("SpotLight") or v:IsA("SurfaceLight") then
            v:Destroy()
        end
    end

    game:GetService("Workspace").CurrentCamera.FieldOfView = 70
    game:GetService("Workspace").CurrentCamera.MaxDistance = 500
    notify("FPS Boost", "Ativado com sucesso!")
end

-- Função para reverter as mudanças e voltar à qualidade normal
local function revertFPSBoost()
    settings().Rendering.QualityLevel = Enum.QualityLevel.Automatic
    game:GetService("Lighting").GlobalShadows = true
    notify("FPS Boost", "Desativado.")
end

local isFPSBoostActive = false  -- Inicia com o boost desativado

-- Função para alternar entre aplicar e reverter o boost de FPS
local function toggleFPSBoost()
    if isFPSBoostActive then
        revertFPSBoost()
    else
        applyFPSBoost()
    end
    isFPSBoostActive = not isFPSBoostActive
end

-- Aba: Configuração
Tabs.Configuração:AddButton({
    Title = "Boost FPS",
    Callback = function()
        https://raw.githubusercontent.com/Exunys/Anti-Kick/main/Anti-Kick.lua
    end
})

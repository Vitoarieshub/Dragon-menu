local Fluent = loadstring(game:HttpGet("https://github.com/dawid-scripts/Fluent/releases/latest/download/main.lua"))()

local Window = Fluent:CreateWindow({
    Title = "Dragon menu " .. Fluent.Version,
    TabWidth = 160,
    Size = UDim2.fromOffset(460, 440),
    Theme = "Dark"
})

-- Função de Notificação
local function notify(title, text)
    game.StarterGui:SetCore("SendNotification", {
        Title = title,
        Text = text,
        Duration = 5
    })
end

local Tabs = {
     Main = Window:AddTab({ Title = "scripts" })
    Settings = Window:AddTab({ Title = "Configuração", Icon = "settings" })
}

-- Parágrafo
Tabs.Main:AddParagraph({ Title = "Desenvolvedor Vitor", Content = "Script atualizado aqui" })

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

-- Noclip
Tabs.Main:AddToggle({
    Title = "Travessa Paredes",
    Default = false,
    Callback = toggleNoclip
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

-- Pulo Infinito
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

-- Definir Gravidade
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

-- Resetar Velocidade, Pulo e Gravidade
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

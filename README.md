loadstring(game:HttpGet("https://raw.githubusercontent.com/Snxdfer/back-ups-for-libs/refs/heads/main/RedzUI.lua"))()

-- Cria a janela principal
MakeWindow({
    Hub = {
        Title = "Dragon Menu - Ilha Bela",
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
local Visuais = MakeTab({Name = "Visuais"})
local Player = MakeTab({Name = "Jogadores"})


-- Notificação inicial
-- Removido se não quiser notificação:
-- MakeNotifi({
--     Title = "Dragon Menu",
--     Text = "Carregando com sucesso!",
--     Time = 5
-- })

AddButton(Main, {
    Name = "Fly GUI Car",
    Callback = function()
        print("Botão foi clicado!")
        pcall(function()
            loadstring(game:HttpGet('https://raw.githubusercontent.com/ScpGuest666/Random-Roblox-script/refs/heads/main/Roblox%20Vehicle%20Fly%20Gui%20script'))()
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
AddToggle(Visuais, {
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
local LocalPlayer = Players.LocalPlayer
local espTags = {}
local espConnections = {}
local espAtivo = false

-- Cria o ESP acima do jogador se for STAFF
local function createESP(player)
	local character = player.Character
	if not character then return end

	local head = character:FindFirstChild("Head")
	if not head then return end

	-- Verifica se já existe ESP
	if espTags[player] then return end

	-- Verifica se existe "staff" em algum TextLabel
	local hasStaffTag = false
	for _, gui in pairs(head:GetChildren()) do
		if gui:IsA("BillboardGui") then
			for _, label in pairs(gui:GetChildren()) do
				if label:IsA("TextLabel") and string.find(label.Text:lower(), "staff") then
					hasStaffTag = true
					break
				end
			end
		end
	end

	if not hasStaffTag then return end

	-- Criar o Billboard ESP
	local billboard = Instance.new("BillboardGui")
	billboard.Name = "ESP_Staff"
	billboard.Adornee = head
	billboard.Size = UDim2.new(0, 100, 0, 20)
	billboard.StudsOffset = Vector3.new(0, 2, 0)
	billboard.AlwaysOnTop = true

	local text = Instance.new("TextLabel")
	text.Size = UDim2.new(1, 0, 1, 0)
	text.BackgroundTransparency = 1
	text.Text = "STAFF"
	text.TextColor3 = Color3.fromRGB(255, 0, 0)
	text.TextStrokeTransparency = 0
	text.Font = Enum.Font.SourceSansBold
	text.TextScaled = true
	text.Parent = billboard

	billboard.Parent = head
	espTags[player] = billboard
end

-- Remove todos os ESPs e desconecta eventos
local function removeAllESP()
	for player, gui in pairs(espTags) do
		if gui and gui.Parent then
			gui:Destroy()
		end
	end
	espTags = {}

	for _, conn in pairs(espConnections) do
		if conn then conn:Disconnect() end
	end
	espConnections = {}
end

-- Ativa ou desativa o ESP
local function toggleEsp(enabled)
	removeAllESP()
	espAtivo = enabled

	if not enabled then return end

	for _, player in pairs(Players:GetPlayers()) do
		if player ~= LocalPlayer then
			-- Cria ESP se personagem já existe
			if player.Character then
				createESP(player)
			end

			-- Conecta para novos personagens
			local conn = player.CharacterAdded:Connect(function()
				wait(1)
				if espAtivo then
					createESP(player)
				end
			end)
			table.insert(espConnections, conn)
		end
	end

	-- Detecta novos jogadores
	local conn = Players.PlayerAdded:Connect(function(player)
		local sub = player.CharacterAdded:Connect(function()
			wait(1)
			if espAtivo then
				createESP(player)
			end
		end)
		table.insert(espConnections, sub)
	end)
	table.insert(espConnections, conn)
end

-- Botão na interface
AddToggle(Visuais, {
	Name = "ESP STAFF",
	Default = false,
	Callback = function(Value)
		toggleEsp(Value)
	end
})

local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer

local jogadorSelecionado = nil
local observando = false
local observarConnection = nil
local dropdownRef = nil

-- Gera a lista atual de jogadores válidos
local function gerarListaDeJogadores()
	local nomes = {}
	for _, player in ipairs(Players:GetPlayers()) do
		if player ~= LocalPlayer then
			table.insert(nomes, player.Name)
		end
	end
	return nomes
end

-- Atualiza o dropdown manualmente
local function atualizarListaDropdown()
	local nomesAtuais = gerarListaDeJogadores()

	-- Limpa a seleção se o jogador saiu
	if jogadorSelecionado and not Players:FindFirstChild(jogadorSelecionado.Name) then
		jogadorSelecionado = nil
	end

	if dropdownRef and dropdownRef.UpdateOptions then
		dropdownRef:UpdateOptions(nomesAtuais)
	end
end

-- Observação contínua
local function observarJogador(jogador)
	if observarConnection then
		observarConnection:Disconnect()
	end

	local function setarCamera()
		if jogador.Character and jogador.Character:FindFirstChild("Humanoid") then
			workspace.CurrentCamera.CameraSubject = jogador.Character.Humanoid
			print("Observando " .. jogador.Name)
		end
	end

	setarCamera()

	observarConnection = jogador.CharacterAdded:Connect(function()
		wait(1)
		if observando then
			setarCamera()
		end
	end)
end

-- Parar observação
local function pararObservar()
	if observarConnection then
		observarConnection:Disconnect()
		observarConnection = nil
	end
	observando = false

	if LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("Humanoid") then
		workspace.CurrentCamera.CameraSubject = LocalPlayer.Character.Humanoid
	end

	print("Observação desativada.")
end

-- Criação do dropdown inicial
dropdownRef = AddDropdown(Player, {
	Name = "Selecionar jogador para observar",
	Options = gerarListaDeJogadores(),
	Callback = function(valorSelecionado)
		jogadorSelecionado = Players:FindFirstChild(valorSelecionado)
	end
})

-- Botão manual para atualizar a lista
AddButton(Player, {
	Name = "Atualizar Lista de Jogadores",
	Callback = function()
		atualizarListaDropdown()
		print("Lista de jogadores atualizada manualmente.")
	end
})

-- Toggle para observar
AddToggle(Player, {
	Name = "Observar",
	Default = false,
	Callback = function(Value)
		if Value then
			if jogadorSelecionado then
				observando = true
				observarJogador(jogadorSelecionado)
			else
				warn("Nenhum jogador selecionado.")
			end
		else
			pararObservar()
		end
	end
})

-- Remove observação se o jogador sair
Players.PlayerRemoving:Connect(function(player)
	if jogadorSelecionado == player then
		pararObservar()
		jogadorSelecionado = nil
	end
end)

- Toggle para ativar/desativar o ESP
AddToggle(Visuais, {
    Name = "ESP nome",
    Default = false,
    Callback = function(Value)
        espAtivado = Value
        -- Variável para armazenar o estado do ESP
local espAtivado = false
local connections = {}
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
                            text.TextColor3 = Color3.fromRGB(255, 255, 255)
                            text.TextSize = 18
                            text.TextScaled = true
                            text.Font = Enum.Font.GothamBold
                            text.TextStrokeTransparency = 0.4
                            text.TextStrokeColor3 = Color3.new(0, 0, 0)
                            text.TextTransparency = 0.2
                            text.Parent = esp

                            esp.Parent = head

                            humanoid.Died:Connect(function()
                                if esp then esp:Destroy() end
                            end)
                        end

                        local texto = esp:FindFirstChild("Texto")
                        if texto and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") and char:FindFirstChild("HumanoidRootPart") then
                            local distancia = (LocalPlayer.Character.HumanoidRootPart.Position - char.HumanoidRootPart.Position).Magnitude
                            texto.Text = player.Name .. " " .. math.floor(distancia)
                            texto.TextTransparency = math.clamp(distancia / 150, 0.2, 0.9)
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

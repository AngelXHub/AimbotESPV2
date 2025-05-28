-- Services
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local Camera = workspace.CurrentCamera

local LocalPlayer = Players.LocalPlayer
local Mouse = LocalPlayer:GetMouse()

-- Configuration
local ESPEnabled = false
local AimlockEnabled = false
local FlyEnabled = false
local AimlockKey = Enum.KeyCode.Q -- Aimlock toggle
local FlyKey = Enum.KeyCode.F -- Fly toggle

-- UI Setup
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "ESP_Aimbot_UI"
ScreenGui.Parent = game.CoreGui -- Coloca no CoreGui para não sumir ao morrer

local Frame = Instance.new("Frame")
Frame.Position = UDim2.new(0, 10, 0, 100)
Frame.Size = UDim2.new(0, 160, 0, 120)
Frame.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
Frame.BackgroundTransparency = 0.5
Frame.BorderSizePixel = 0
Frame.Parent = ScreenGui
Frame.Active = true
Frame.Draggable = true

local TextLabel = Instance.new("TextLabel")
TextLabel.Size = UDim2.new(1, 0, 0, 20)
TextLabel.BackgroundTransparency = 1
TextLabel.Text = "ESP / Aimbot / Fly"
TextLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
TextLabel.TextScaled = true
TextLabel.Font = Enum.Font.GothamBold
TextLabel.Parent = Frame

local function createToggle(name, position)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(0, 140, 0, 25)
    btn.Position = position
    btn.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
    btn.TextColor3 = Color3.fromRGB(255, 255, 255)
    btn.Font = Enum.Font.Gotham
    btn.TextScaled = true
    btn.Text = name .. ": OFF"
    btn.Parent = Frame
    return btn
end

local ESPButton = createToggle("ESP", UDim2.new(0, 10, 0, 30))
local AimlockButton = createToggle("Aimlock", UDim2.new(0, 10, 0, 60))
local FlyButton = createToggle("Fly", UDim2.new(0, 10, 0, 90))

ESPButton.MouseButton1Click:Connect(function()
    ESPEnabled = not ESPEnabled
    ESPButton.Text = "ESP: " .. (ESPEnabled and "ON" or "OFF")
end)

AimlockButton.MouseButton1Click:Connect(function()
    AimlockEnabled = not AimlockEnabled
    AimlockButton.Text = "Aimlock: " .. (AimlockEnabled and "ON" or "OFF")
end)

FlyButton.MouseButton1Click:Connect(function()
    FlyEnabled = not FlyEnabled
    FlyButton.Text = "Fly: " .. (FlyEnabled and "ON" or "OFF")
end)

-- Wall Check Function
local function hasLineOfSight(origin, targetPos)
    local ray = Ray.new(origin, (targetPos - origin).Unit * 1000)
    local hit, pos = workspace:FindPartOnRay(ray, LocalPlayer.Character, false, true)
    if hit then
        local distToTarget = (targetPos - origin).Magnitude
        local distToHit = (pos - origin).Magnitude
        return distToHit >= distToTarget - 2 -- tolerância pequena
    end
    return true
end

-- ESP Implementation
local espObjects = {}

local function createESPBox()
    local box = Drawing.new("Square")
    box.Visible = false
    box.Transparency = 1
    box.Thickness = 2
    box.Color = Color3.new(1, 0, 0)
    box.Filled = false
    box.ZIndex = 10
    return box
end

local function isEnemy(player)
    if not player or not player.Character or not player.Character:FindFirstChild("Humanoid") then
        return false
    end
    local lpTeam = LocalPlayer.Team
    local plTeam = player.Team
    if lpTeam and plTeam and lpTeam == plTeam then
        return false
    end
    return player ~= LocalPlayer
end

local function updateESP()
    for _, player in pairs(Players:GetPlayers()) do
        if isEnemy(player) then
            if not espObjects[player] then
                espObjects[player] = createESPBox()
            end
            local box = espObjects[player]
            local char = player.Character
            local rootPart = char and char:FindFirstChild("HumanoidRootPart")
            local head = char and char:FindFirstChild("Head")
            if rootPart and head then
                local rootPos, onScreen1 = Camera:WorldToViewportPoint(rootPart.Position)
                local headPos, onScreen2 = Camera:WorldToViewportPoint(head.Position)
                if onScreen1 and onScreen2 then
                    local height = math.abs(headPos.Y - rootPos.Y)
                    local width = height / 2
                    box.Visible = ESPEnabled
                    box.Size = Vector2.new(width, height)
                    box.Position = Vector2.new(rootPos.X - width/2, rootPos.Y - height/2)
                else
                    box.Visible =

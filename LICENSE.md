-- Roblox Script: Aimbot with head detection on shoot, ESP lines and boxes
-- Simplified version focusing on requested features
-- GUI toggle to enable/disable Aimbot, ESP Lines and Boxes
-- Press 'Q' to open/close the GUI

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera

-- Settings
local Settings = {
    Aimbot = false,
    ESP = false,
    ESPLines = true,
    ESPBoxes = true,
}

-- Check the map name "GARENA FREE FIRE MAX🏆"
local function isCorrectMap()
    return workspace.Name == "GARENA FREE FIRE MAX🏆"
end

if not isCorrectMap() then
    warn("This script runs only on map 'GARENA FREE FIRE MAX🏆'. Script disabled.")
    return
end

-- GUI Creation
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "SimpleAimbotESPGui"
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = game:GetService("CoreGui")
ScreenGui.Enabled = true

-- Helper function to create a toggle button
local function createToggle(name, position)
    local frame = Instance.new("Frame")
    frame.Size = UDim2.new(0, 200, 0, 30)
    frame.Position = position
    frame.BackgroundColor3 = Color3.fromRGB(25, 25, 30)
    frame.BorderSizePixel = 0
    frame.Parent = ScreenGui

    local label = Instance.new("TextLabel")
    label.Size = UDim2.new(0, 120, 1, 0)
    label.Position = UDim2.new(0, 5, 0, 0)
    label.BackgroundTransparency = 1
    label.TextColor3 = Color3.fromRGB(220, 220, 220)
    label.Font = Enum.Font.GothamSemibold
    label.TextSize = 16
    label.Text = name
    label.TextXAlignment = Enum.TextXAlignment.Left
    label.Parent = frame

    local button = Instance.new("TextButton")
    button.Size = UDim2.new(0, 50, 0, 22)
    button.Position = UDim2.new(1, -60, 0, 4)
    button.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
    button.BorderSizePixel = 0
    button.Font = Enum.Font.GothamBold
    button.TextSize = 18
    button.TextColor3 = Color3.fromRGB(200, 200, 200)
    button.Text = "OFF"
    button.Parent = frame

    local toggled = false
    button.MouseButton1Click:Connect(function()
        toggled = not toggled
        if toggled then
            button.Text = "ON"
            button.BackgroundColor3 = Color3.fromRGB(50, 150, 50)
        else
            button.Text = "OFF"
            button.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
        end
        Settings[name] = toggled
    end)

    return frame
end

-- Create toggles
createToggle("Aimbot", UDim2.new(0, 10, 0, 10))
createToggle("ESP", UDim2.new(0, 10, 0, 45))
createToggle("ESPLines", UDim2.new(0, 10, 0, 80))
createToggle("ESPBoxes", UDim2.new(0, 10, 0, 115))

-- Toggle GUI visibility with Q key
UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if gameProcessed then return end
    if input.KeyCode == Enum.KeyCode.Q then
        ScreenGui.Enabled = not ScreenGui.Enabled
    end
end)

-- ESP Storage
local espObjects = {}

local function createESP(plr)
    if espObjects[plr] then return end

    local box = Instance.new("BoxHandleAdornment")
    box.Adornee = nil
    box.AlwaysOnTop = true
    box.ZIndex = 10
    box.Transparency = 0.5
    box.Size = Vector3.new(4, 6, 1)
    box.Color3 = Color3.new(1,0,0)
    box.Parent = Camera

    local line = Instance.new("LineHandleAdornment")
    line.AlwaysOnTop = true
    line.ZIndex = 10
    line.Transparency = 0.7
    line.Color3 = Color3.fromRGB(255, 255, 255)
    line.Thickness = 1
    line.Visible = false
    line.Parent = Camera

    espObjects[plr] = {
        Box = box,
        Line = line
    }
end

local function removeESP(plr)
    if espObjects[plr] then
        if espObjects[plr].Box then
            espObjects[plr].Box:Destroy()
        end
        if espObjects[plr].Line then
            espObjects[plr].Line:Destroy()
        end
        espObjects[plr] = nil
    end
end

local function updateESP()
    local hrp = LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
    if not hrp then return end

    for plr, esp in pairs(espObjects) do
        if plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") and plr.Character:FindFirstChild("Head") and plr.Character:FindFirstChild("Humanoid") and plr.Character.Humanoid.Health > 0 then
            local targetHRP = plr.Character.HumanoidRootPart
            local targetHead = plr.Character.Head

            -- Box
            if Settings.ESPBoxes then
                esp.Box.Adornee = targetHRP
                esp.Box.Visible = true
            else
                esp.Box.Visible = false
            end

            -- Line from camera to head
            if Settings.ESPLines then
                esp.Line.Visible = true
                esp.Line.From = Camera.CFrame.Position
                esp.Line.To = targetHead.Position
            else
                esp.Line.Visible = false
            end
        else
            removeESP(plr)
        end
    end
end

local function espLoop()
    for _, plr in pairs(Players:GetPlayers()) do
        if plr ~= LocalPlayer and plr.Character and plr.Character:FindFirstChild("Humanoid") and plr.Character.Humanoid.Health > 0 then
            createESP(plr)
        else
            removeESP(plr)
        end
    end
    updateESP()
end

-- Aimbot function - Smoothly aim at closest enemy head when mouse is down (simulate 'atirar')
local mouse = LocalPlayer:GetMouse()
local aiming = false

mouse.Button1Down:Connect(function()
    aiming = true
end)

mouse.Button1Up:Connect(function()
    aiming = false
end)

local function getClosestEnemyHeadToCursor()
    local closestPlayer = nil
    local shortestDistance = math.huge
    local mousePos = UserInputService:GetMouseLocation()

    for _, plr in pairs(Players:GetPlayers()) do
        if plr ~= LocalPlayer and plr.Character and plr.Character:FindFirstChild("Head") and plr.Character:FindFirstChild("Humanoid") then
            if plr.Character.Humanoid.Health > 0 then
                local headPos, onScreen = Camera:WorldToScreenPoint(plr.Character.Head.Position)
                if onScreen then
                    local dx = mousePos.X - headPos.X
                    local dy = mousePos.Y - headPos.Y
                    local dist = math.sqrt(dx*dx + dy*dy)
                    if dist < shortestDistance then
                        shortestDistance = dist
                        closestPlayer = plr
                    end
                end
            end
        end
    end
    return closestPlayer
end

RunService.RenderStepped:Connect(function()
    -- ESP update
    if Settings.ESP and (Settings.ESPLines or Settings.ESPBoxes) then
        espLoop()
    else
        -- Remove all ESP if disabled
        for plr, _ in pairs(espObjects) do
            removeESP(plr)
        end
    end

    -- Aimbot Logic
    if Settings.Aimbot and aiming then
        local target = getClosestEnemyHeadToCursor()
        if target and target.Character and target.Character:FindFirstChild("Head") then
            local headPos = target.Character.Head.Position
            local cameraCFrame = Camera.CFrame
            local direction = (headPos - cameraCFrame.Position).unit
            local newCFrame = CFrame.new(cameraCFrame.Position, cameraCFrame.Position + direction)
            Camera.CFrame = cameraCFrame:Lerp(newCFrame, 0.25) -- smooth follow
        end
    end
end)

print("Simple Aimbot and ESP script loaded. Press 'Q' to toggle GUI.")

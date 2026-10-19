--// DAVI7 MENU
--// Roblox Studio - LocalScript

local Players = game:GetService("Players")
local UIS = game:GetService("UserInputService")
local player = Players.LocalPlayer

local Settings = {
    AimEnabled = true,
    AimKey = Enum.UserInputType.MouseButton2,
    FOV = 150,
    Smoothness = 1.0,
    YOffset = 1.5,
    VisCheck = false,
    TeamCheck = false,
    AutoSwitch = true,
    OverrideCam = false,
    ESPEnabled = true,
    ESPBoxes = true,
    ESPTracers = true,
    ESPInfo = true,
    ESPDrawFOV = true,
    ESPMaxDist = 5000,
    HitboxEnabled = false,
    HitboxSize = 10,
    HitboxTransparency = 0.6,
    Notifications = true,
}

local gui = Instance.new("ScreenGui")
gui.Name = "DAVI7_MENU"
gui.ResetOnSpawn = false
gui.Parent = player:WaitForChild("PlayerGui")

local main = Instance.new("Frame")
main.Size = UDim2.fromOffset(310, 440)
main.Position = UDim2.new(0.5, -155, 0.5, -220)
main.BackgroundColor3 = Color3.fromRGB(18, 18, 24)
main.BorderSizePixel = 0
main.Active = true
main.Draggable = true
main.Parent = gui

Instance.new("UICorner", main).CornerRadius = UDim.new(0, 10)

local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, 0, 0, 45)
title.BackgroundColor3 = Color3.fromRGB(190, 25, 45)
title.Text = "DAVI7 MENU"
title.TextColor3 = Color3.new(1, 1, 1)
title.Font = Enum.Font.GothamBold
title.TextSize = 20
title.Parent = main
Instance.new("UICorner", title).CornerRadius = UDim.new(0, 10)

local scroll = Instance.new("ScrollingFrame")
scroll.Position = UDim2.fromOffset(8, 53)
scroll.Size = UDim2.new(1, -16, 1, -61)
scroll.BackgroundTransparency = 1
scroll.BorderSizePixel = 0
scroll.ScrollBarThickness = 4
scroll.AutomaticCanvasSize = Enum.AutomaticSize.Y
scroll.CanvasSize = UDim2.new()
scroll.Parent = main

local layout = Instance.new("UIListLayout")
layout.Padding = UDim.new(0, 6)
layout.SortOrder = Enum.SortOrder.LayoutOrder
layout.Parent = scroll

local function section(name)
    local label = Instance.new("TextLabel")
    label.Size = UDim2.new(1, -5, 0, 28)
    label.BackgroundTransparency = 1
    label.Text = "  " .. name
    label.TextColor3 = Color3.fromRGB(255, 75, 90)
    label.TextXAlignment = Enum.TextXAlignment.Left
    label.Font = Enum.Font.GothamBold
    label.TextSize = 14
    label.Parent = scroll
end

local function toggle(name, key)
    local button = Instance.new("TextButton")
    button.Size = UDim2.new(1, -5, 0, 34)
    button.BackgroundColor3 = Color3.fromRGB(38, 38, 48)
    button.TextColor3 = Color3.new(1, 1, 1)
    button.Font = Enum.Font.Gotham
    button.TextSize = 13
    button.Parent = scroll
    Instance.new("UICorner", button).CornerRadius = UDim.new(0, 6)

    local function refresh()
        button.Text = name .. ": " ..
            (Settings[key] and "ON" or "OFF")
    end

    refresh()

    button.Activated:Connect(function()
        Settings[key] = not Settings[key]
        refresh()
    end)
end

local function value(name, key, step, min, max)
    local row = Instance.new("Frame")
    row.Size = UDim2.new(1, -5, 0, 36)
    row.BackgroundColor3 = Color3.fromRGB(38, 38, 48)
    row.Parent = scroll
    Instance.new("UICorner", row).CornerRadius = UDim.new(0, 6)

    local label = Instance.new("TextLabel")
    label.Size = UDim2.new(1, -85, 1, 0)
    label.Position = UDim2.fromOffset(8, 0)
    label.BackgroundTransparency = 1
    label.TextColor3 = Color3.new(1, 1, 1)
    label.TextXAlignment = Enum.TextXAlignment.Left
    label.Font = Enum.Font.Gotham
    label.TextSize = 12
    label.Parent = row

    local minus = Instance.new("TextButton")
    minus.Size = UDim2.fromOffset(28, 28)
    minus.Position = UDim2.new(1, -64, 0, 4)
    minus.Text = "-"
    minus.Parent = row

    local plus = Instance.new("TextButton")
    plus.Size = UDim2.fromOffset(28, 28)
    plus.Position = UDim2.new(1, -32, 0, 4)
    plus.Text = "+"
    plus.Parent = row

    for _, b in ipairs({minus, plus}) do
        b.BackgroundColor3 = Color3.fromRGB(190, 25, 45)
        b.TextColor3 = Color3.new(1, 1, 1)
        b.Font = Enum.Font.GothamBold
        b.TextSize = 16
        Instance.new("UICorner", b).CornerRadius = UDim.new(0, 5)
    end

    local function refresh()
        label.Text = name .. ": " .. tostring(Settings[key])
    end

    minus.Activated:Connect(function()
        Settings[key] = math.max(min, math.round(
            (Settings[key] - step) * 100) / 100)
        refresh()
    end)

    plus.Activated:Connect(function()
        Settings[key] = math.min(max, math.round(
            (Settings[key] + step) * 100) / 100)
        refresh()
    end)

    refresh()
end

section("AIM SETTINGS")
toggle("Aim Enabled", "AimEnabled")
value("FOV", "FOV", 10, 10, 500)
value("Smoothness", "Smoothness", 0.1, 0.1, 5)
value("Y Offset", "YOffset", 0.1, -5, 5)
toggle("Visibility Check", "VisCheck")
toggle("Team Check", "TeamCheck")
toggle("Auto Switch", "AutoSwitch")
toggle("Override Camera", "OverrideCam")

section("ESP SETTINGS")
toggle("ESP Enabled", "ESPEnabled")
toggle("ESP Boxes", "ESPBoxes")
toggle("ESP Tracers", "ESPTracers")
toggle("ESP Info", "ESPInfo")
toggle("ESP Draw FOV", "ESPDrawFOV")
value("ESP Max Distance", "ESPMaxDist", 100, 100, 5000)

section("HITBOX SETTINGS")
toggle("Hitbox Enabled", "HitboxEnabled")
value("Hitbox Size", "HitboxSize", 1, 1, 30)
value("Hitbox Transparency", "HitboxTransparency", 0.1, 0, 1)

section("GENERAL")
toggle("Notifications", "Notifications")

local hint = Instance.new("TextLabel")
hint.Size = UDim2.new(1, -5, 0, 30)
hint.BackgroundTransparency = 1
hint.Text = "RightShift = Mostrar/Ocultar"
hint.TextColor3 = Color3.fromRGB(180, 180, 190)
hint.Font = Enum.Font.Gotham
hint.TextSize = 11
hint.Parent = scroll

UIS.InputBegan:Connect(function(input, processed)
    if not processed and input.KeyCode == Enum.KeyCode.RightShift then
        main.Visible = not main.Visible
    end
end)

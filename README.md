local Rayfield = loadstring(game:HttpGet('https://sirius.menu/rayfield'))()

-- --- UI WINDOW ---
local Window = Rayfield:CreateWindow({
   Name = "🎯DAVI7 MENU",
   LoadingTitle = "DAVI7 MENUS AIM🎯",
   LoadingSubtitle = "CRIADO POR DAVI7",
   ConfigurationSaving = { Enabled = false }
})

local AimTab = Window:CreateTab("Combat (Aim)", 4483362458)
local VisTab = Window:CreateTab("Visuals (ESP)", 4483345998)


-- Variáveis de Controle
local TPWalkEnabled, TPWalkSpeed = false, 1
local SpeedBoostActive = false
local InfiniteJumpEnabled = false
local AutoRejoinKick, AutoRejoinMorte = false, false
local LineEspEnabled = false
local AntiVoidEnabled = false
local TargetLockEnabled = false

-- --- ABA PRINCIPAL (MOVIMENTAÇÃO) ---
local MainTab = Window:CreateTab("Velocidade etc...", 4483362458)

MainTab:CreateSection("Boost de Velocidade")

-- Função Speed-Boost (85 Studs)
MainTab:CreateToggle({
   Name = "Speed-Boost 100% Anti-ser-espulso (85 metros)",
   CurrentValue = false,
   Callback = function(Value)
      SpeedBoostActive = Value
      if SpeedBoostActive then
         spawn(function()
            while SpeedBoostActive do
               local char = game.Players.LocalPlayer.Character
               local hum = char and char:FindFirstChild("Humanoid")
               if hum and hum.MoveDirection.Magnitude > 0 then
                  char:TranslateBy(hum.MoveDirection * (85 * 0.012))
               end
               task.wait(0.01)
            end
         end)
      end
   end,
})

MainTab:CreateSection("Movimentação Manual")

-- Slider de Velocidade para o TPWalk
MainTab:CreateSlider({
   Name = "Ajustar TPWalk (1-4)",
   Range = {1, 4},
   Increment = 0.1,
   CurrentValue = 1,
   Callback = function(Value) TPWalkSpeed = Value end,
})

-- Botão de Ativar TPWalk
MainTab:CreateToggle({
   Name = "Ativar TPWalk Custom",
   CurrentValue = false,
   Callback = function(Value)
      TPWalkEnabled = Value
      if TPWalkEnabled then
         spawn(function()
            while TPWalkEnabled do
               local char = game.Players.LocalPlayer.Character
               local hum = char and char:FindFirstChild("Humanoid")
               if hum and hum.MoveDirection.Magnitude > 0 then
                  char:TranslateBy(hum.MoveDirection * (TPWalkSpeed * 0.8))
               end
               task.wait(0.01)
            end
         end)
      end
   end,
})

MainTab:CreateToggle({
   Name = "Pulo Infinito",
   CurrentValue = false,
   Callback = function(Value) InfiniteJumpEnabled = Value end,
})

--// SERVICES
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UIS = game:GetService("UserInputService")
local player = Players.LocalPlayer

--// VARIABLES
local flying = false
local speed = 2
local noclip = false
local godmode = false
local walkspeed = 16
local infiniteJump = false
local espEnabled = false

--// CHARACTER
local function getChar()
    return player.Character or player.CharacterAdded:Wait()
end

-- =======================
-- FLY
-- =======================
local bv, bg

local function startFly()
    local character = getChar()
    local hrp = character:WaitForChild("HumanoidRootPart")
    local humanoid = character:WaitForChild("Humanoid")

    bv = Instance.new("BodyVelocity")
    bg = Instance.new("BodyGyro")

    bv.MaxForce = Vector3.new(9e9,9e9,9e9)
    bg.MaxTorque = Vector3.new(9e9,9e9,9e9)

    bv.Parent = hrp
    bg.Parent = hrp

    humanoid.PlatformStand = true
    flying = true

    RunService.RenderStepped:Connect(function()
        if not flying then
            if bv then bv:Destroy() end
            if bg then bg:Destroy() end
            humanoid.PlatformStand = false
            return
        end

        local cam = workspace.CurrentCamera
        local move = Vector3.zero

        if UIS:IsKeyDown(Enum.KeyCode.W) then move += cam.CFrame.LookVector end
        if UIS:IsKeyDown(Enum.KeyCode.S) then move -= cam.CFrame.LookVector end
        if UIS:IsKeyDown(Enum.KeyCode.A) then move -= cam.CFrame.RightVector end
        if UIS:IsKeyDown(Enum.KeyCode.D) then move += cam.CFrame.RightVector end
        if UIS:IsKeyDown(Enum.KeyCode.Space) then move += Vector3.new(0,1,0) end
        if UIS:IsKeyDown(Enum.KeyCode.LeftControl) then move -= Vector3.new(0,1,0) end

        bv.Velocity = move * (speed * 50)
        bg.CFrame = cam.CFrame
    end)
end

local function stopFly()
    flying = false
end

-- =======================
-- NOCLIP
-- =======================
RunService.Stepped:Connect(function()
    if noclip and player.Character then
        for _,v in pairs(player.Character:GetDescendants()) do
            if v:IsA("BasePart") then
                v.CanCollide = false
            end
        end
    end
end)

-- =======================
-- GODMODE
-- =======================
RunService.Heartbeat:Connect(function()
    if godmode then
        local char = player.Character
        if char then
            local hum = char:FindFirstChildOfClass("Humanoid")
            if hum then
                hum.Health = hum.MaxHealth
            end
        end
    end
end)

-- =======================
-- WALKSPEED
-- =======================
RunService.Heartbeat:Connect(function()
    local char = player.Character
    if char then
        local hum = char:FindFirstChildOfClass("Humanoid")
        if hum then
            hum.WalkSpeed = walkspeed
        end
    end
end)

-- =======================
-- INFINITE JUMP
-- =======================
UIS.JumpRequest:Connect(function()
    if infiniteJump then
        local char = player.Character
        if char then
            local hum = char:FindFirstChildOfClass("Humanoid")
            if hum then
                hum:ChangeState(Enum.HumanoidStateType.Jumping)
            end
        end
    end
end)

-- =======================
-- ESP
-- =======================
local espObjects = {}

local function createESP(plr)
    if plr == player then return end

    local function addChar(char)
        local highlight = Instance.new("Highlight")
        highlight.FillTransparency = 1
        highlight.OutlineColor = Color3.fromRGB(255,0,0)
        highlight.OutlineTransparency = 0
        highlight.Adornee = char
        highlight.Parent = char

        local billboard = Instance.new("BillboardGui")
        billboard.Size = UDim2.new(0,100,0,40)
        billboard.AlwaysOnTop = true
        billboard.StudsOffset = Vector3.new(0,3,0)

        local name = Instance.new("TextLabel")
        name.Size = UDim2.new(1,0,1,0)
        name.BackgroundTransparency = 1
        name.TextColor3 = Color3.new(1,1,1)
        name.TextStrokeTransparency = 0
        name.Text = plr.Name
        name.Parent = billboard

        billboard.Parent = char:WaitForChild("Head")

        espObjects[plr] = {highlight, billboard}
    end

    if plr.Character then
        addChar(plr.Character)
    end

    plr.CharacterAdded:Connect(addChar)
end

local function removeESP()
    for _,objs in pairs(espObjects) do
        for _,o in pairs(objs) do
            if o then o:Destroy() end
        end
    end
    espObjects = {}
end

Players.PlayerAdded:Connect(function(plr)
    if espEnabled then
        createESP(plr)
    end
end)

-- =======================
-- UI
-- =======================
MainTab:CreateToggle({
   Name = "Fly",
   CurrentValue = false,
   Callback = function(v) if v then startFly() else stopFly() end end,
})

MainTab:CreateToggle({
   Name = "Noclip",
   CurrentValue = false,
   Callback = function(v) noclip = v end,
})

MainTab:CreateToggle({
   Name = "Godmode",
   CurrentValue = false,
   Callback = function(v) godmode = v end,
})

MainTab:CreateToggle({
   Name = "Infinite Jump",
   CurrentValue = false,
   Callback = function(v) infiniteJump = v end,
})

MainTab:CreateToggle({
   Name = "ESP",
   CurrentValue = false,
   Callback = function(v)
        espEnabled = v
        if v then
            for _,plr in pairs(Players:GetPlayers()) do
                createESP(plr)
            end
        else
            removeESP()
        end
   end,
})

MainTab:CreateSlider({
   Name = "Fly Speed",
   Range = {1, 10},
   Increment = 1,
   CurrentValue = 2,
   Callback = function(v) speed = v end,
})

MainTab:CreateSlider({
   Name = "WalkSpeed",
   Range = {16, 150},
   Increment = 2,
   CurrentValue = 16,
   Callback = function(v) walkspeed = v end,
})



-- --- ABA SEGURANÇA ---
local SafetyTab = Window:CreateTab("Segurança", 4483362458)

local SubirAntiVoidGui = nil
SafetyTab:CreateToggle({
   Name = "Anti-Void Manual",
   CurrentValue = false,
   Callback = function(Value)
      AntiVoidEnabled = Value
      if not Value and SubirAntiVoidGui then SubirAntiVoidGui:Destroy() SubirAntiVoidGui = nil end
      if Value then
         spawn(function()
            while AntiVoidEnabled do
               local root = game.Players.LocalPlayer.Character and game.Players.LocalPlayer.Character:FindFirstChild("HumanoidRootPart")
               if root and root.Position.Y < -60 then
                  root.Velocity = Vector3.new(0, 0, 0)
                  if not SubirAntiVoidGui then
                     local sg = Instance.new("ScreenGui", game.CoreGui); local btn = Instance.new("TextButton", sg); SubirAntiVoidGui = sg
                     btn.Size = UDim2.new(0, 150, 0, 60); btn.Position = UDim2.new(0.5, -75, 0.7, 0); btn.BackgroundColor3 = Color3.fromRGB(0, 200, 100); btn.Text = "SAIR DO VOID"; btn.TextColor3 = Color3.new(1,1,1); Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 12)
                     btn.MouseButton1Down:Connect(function() _G.VoidHold = true while _G.VoidHold and root and AntiVoidEnabled do root.Velocity = Vector3.new(0, 0, 0); root.CFrame = root.CFrame + Vector3.new(0, 3.5, 0); task.wait(0.02) end end)
                     btn.MouseButton1Up:Connect(function() _G.VoidHold = false end)
                  end
               end
               task.wait(0.1)
            end
         end)
      end
   end,
})

-- --- VARIABLEN & DATEN ---
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera

local Settings = {
    -- Combat (Aimbot)
    AimbotEnabled = false,
    SilentAim = false, -- Schüsse treffen eher ohne direktes Zielen
    Smoothness = 0.5,
    AimbotPart = "Head",
    AimbotFOV = 150,
    ShowFOV = true,
    FOVColor = Color3.fromRGB(255, 0, 0),
    FOVThickness = 1,
    
    -- Visuals (ESP)
    ESPEnabled = false,
    ESPBoxes = false,
    ESPNames = false,
    ESPTracer = false, -- Snaplines / Tracker
    ESPColor = Color3.fromRGB(255, 0, 0),
    TracerOrigin = "Bottom", -- "Mouse" oder "Bottom"
    
    -- Warnings
    BackWarning = false, -- "Enemy Behind You"
}

-- --- DRAWING SETUP ---
local FOVCircle = Drawing.new("Circle")
FOVCircle.Filled = false
FOVCircle.Transparency = 1
FOVCircle.Visible = false

local WarningText = Drawing.new("Text")
WarningText.Visible = false
WarningText.Center = true
WarningText.Size = 30
WarningText.Outline = true
WarningText.Font = 2
WarningText.Color = Color3.new(1, 0, 0)
WarningText.Text = "⚠️ ENEMY BEHIND YOU ⚠️"


-- --- AIMBOT SEKTION ---
AimTab:CreateSection("Aimbot Settings")

AimTab:CreateToggle({
   Name = "Aimbot",
   CurrentValue = false,
   Callback = function(v) Settings.AimbotEnabled = v end
})

AimTab:CreateSlider({
   Name = "Smoothness / Strength",
   Range = {0, 1}, Increment = 0.1, CurrentValue = 0.5,
   Callback = function(v) Settings.Smoothness = v end
})

AimTab:CreateDropdown({
   Name = "Target Bone",
   Options = {"Head", "UpperTorso", "HumanoidRootPart"},
   CurrentOption = "Head",
   Callback = function(v) Settings.AimbotPart = v end
})

AimTab:CreateSection("FOV Customization")

AimTab:CreateToggle({
   Name = "Aimbot FOV ",
   CurrentValue = true,
   Callback = function(v) Settings.ShowFOV = v end
})

AimTab:CreateSlider({
   Name = "FOV Radius",
   Range = {50, 800}, Increment = 10, CurrentValue = 150,
   Callback = function(v) Settings.AimbotFOV = v end
})

AimTab:CreateColorPicker({
    Name = "FOV Color",
    Color = Color3.fromRGB(255, 0, 0),
    Callback = function(v) Settings.FOVColor = v end
})

-- --- VISUALS SEKTION ---
VisTab:CreateSection("ESP Options")

VisTab:CreateToggle({
   Name = " ESP ",
   CurrentValue = false,
   Callback = function(v) Settings.ESPEnabled = v end
})

VisTab:CreateToggle({
   Name = " Boxes ",
   CurrentValue = false,
   Callback = function(v) Settings.ESPBoxes = v end
})

VisTab:CreateToggle({
   Name = "Snaplines / Tracker",
   CurrentValue = false,
   Callback = function(v) Settings.ESPTracer = v end
})

VisTab:CreateToggle({
   Name = "Back-Warning (!)",
   CurrentValue = false,
   Callback = function(v) Settings.BackWarning = v end
})

VisTab:CreateColorPicker({
    Name = "Visual Color",
    Color = Color3.fromRGB(255, 0, 0),
    Callback = function(v) Settings.ESPColor = v end
})

-- --- CORE LOGIK ---

local function CreatePlayerVisuals(Player)
    local Box = Drawing.new("Square")
    local Line = Drawing.new("Line")
    
    RunService.RenderStepped:Connect(function()
        if Player.Character and Player.Character:FindFirstChild("HumanoidRootPart") and Player.Character:FindFirstChild("Humanoid") and Player.Character.Humanoid.Health > 0 then
            local Root = Player.Character.HumanoidRootPart
            local Pos, OnScreen = Camera:WorldToViewportPoint(Root.Position)
            
            -- Box ESP
            if OnScreen and Settings.ESPEnabled and Settings.ESPBoxes then
                local Size = (Camera:WorldToViewportPoint(Root.Position - Vector3.new(0, 3, 0)).Y - Camera:WorldToViewportPoint(Root.Position + Vector3.new(0, 2.6, 0)).Y)
                Box.Visible = true
                Box.Size = Vector2.new(Size * 0.7, Size)
                Box.Position = Vector2.new(Pos.X - Box.Size.X/2, Pos.Y - Box.Size.Y/2)
                Box.Color = Settings.ESPColor
            else
                Box.Visible = false
            end

            -- Tracker / Snaplines
            if OnScreen and Settings.ESPEnabled and Settings.ESPTracer then
                Line.Visible = true
                Line.From = Vector2.new(Camera.ViewportSize.X / 2, Camera.ViewportSize.Y)
                Line.To = Vector2.new(Pos.X, Pos.Y)
                Line.Color = Settings.ESPColor
            else
                Line.Visible = false
            end
        else
            Box.Visible = false
            Line.Visible = false
        end
    end)
end

-- --- AIMBOT & WARNING UPDATES ---
RunService.RenderStepped:Connect(function()
    -- FOV Update
    FOVCircle.Visible = Settings.ShowFOV
    FOVCircle.Radius = Settings.AimbotFOV
    FOVCircle.Position = UserInputService:GetMouseLocation()
    FOVCircle.Color = Settings.FOVColor
    
    -- Aimbot Lock
    if Settings.AimbotEnabled and UserInputService:IsMouseButtonPressed(Enum.UserInputType.MouseButton2) then
        local Target = nil
        local ShortestDist = Settings.AimbotFOV
        
        for _, v in pairs(Players:GetPlayers()) do
            if v ~= LocalPlayer and v.Character and v.Character:FindFirstChild(Settings.AimbotPart) then
                local Pos, OnScreen = Camera:WorldToViewportPoint(v.Character[Settings.AimbotPart].Position)
                if OnScreen then
                    local Dist = (Vector2.new(Pos.X, Pos.Y) - UserInputService:GetMouseLocation()).Magnitude
                    if Dist < ShortestDist then
                        Target = v
                        ShortestDist = Dist
                    end
                end
            end
        end
        
        if Target then
            Camera.CFrame = Camera.CFrame:Lerp(CFrame.new(Camera.CFrame.Position, Target.Character[Settings.AimbotPart].Position), (1 - Settings.Smoothness))
        end
    end

    -- Warning Update
    local ShowWarning = false
    if Settings.BackWarning then
        for _, v in pairs(Players:GetPlayers()) do
            if v ~= LocalPlayer and v.Character and v.Character:FindFirstChild("HumanoidRootPart") then
                local RelPos = Camera.CFrame:PointToObjectSpace(v.Character.HumanoidRootPart.Position)
                if RelPos.Z > 0 and (Camera.CFrame.Position - v.Character.HumanoidRootPart.Position).Magnitude < 50 then
                    ShowWarning = true
                end
            end
        end
    end
    WarningText.Visible = ShowWarning
    WarningText.Position = Vector2.new(Camera.ViewportSize.X / 2, 150)
end)

-- Initialisierung für Spieler
for _, p in pairs(Players:GetPlayers()) do if p ~= LocalPlayer then CreatePlayerVisuals(p) end end
Players.PlayerAdded:Connect(CreatePlayerVisuals)

Rayfield:Notify({Title = "DAVI7 MENU", Content = "DAVI7 MENU ABERTO COM SUCESSO!", Duration = 4})

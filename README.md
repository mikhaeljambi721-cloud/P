--// BloxStrike Speed Changer (Max 100000) - Draggable GUI
local player = game.Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local humanoid = character:WaitForChild("Humanoid")

--// Bikin GUI
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "BloxStrikeSpeedGui"
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = player:WaitForChild("PlayerGui")

local Frame = Instance.new("Frame")
Frame.Size = UDim2.new(0, 240, 0, 220)
Frame.Position = UDim2.new(0.5, -120, 0.5, -110)
Frame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
Frame.Active = true
Frame.Draggable = true
Frame.Parent = ScreenGui

local UICorner = Instance.new("UICorner")
UICorner.CornerRadius = UDim.new(0, 12)
UICorner.Parent = Frame

--// Title
local Title = Instance.new("TextLabel")
Title.Size = UDim2.new(1, 0, 0, 30)
Title.BackgroundTransparency = 1
Title.Text = "⚡ Speed Changer (Max 100K)"
Title.TextColor3 = Color3.fromRGB(255, 255, 255)
Title.TextSize = 14
Title.Font = Enum.Font.GothamBold
Title.Parent = Frame

--// TextBox input speed
local TextBox = Instance.new("TextBox")
TextBox.Size = UDim2.new(0.8, 0, 0, 35)
TextBox.Position = UDim2.new(0.1, 0, 0.18, 0)
TextBox.PlaceholderText = "Masukkan Speed (1-100000)"
TextBox.Text = "16"
TextBox.TextColor3 = Color3.fromRGB(255, 255, 255)
TextBox.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
TextBox.TextSize = 13
TextBox.Font = Enum.Font.Gotham
TextBox.ClearTextOnFocus = false
TextBox.Parent = Frame

local BoxCorner = Instance.new("UICorner")
BoxCorner.CornerRadius = UDim.new(0, 6)
BoxCorner.Parent = TextBox

--// Tombol On/Off
local ToggleBtn = Instance.new("TextButton")
ToggleBtn.Size = UDim2.new(0.8, 0, 0, 35)
ToggleBtn.Position = UDim2.new(0.1, 0, 0.42, 0)
ToggleBtn.Text = "Speed: OFF"
ToggleBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
ToggleBtn.BackgroundColor3 = Color3.fromRGB(80, 80, 100)
ToggleBtn.TextSize = 13
ToggleBtn.Font = Enum.Font.GothamBold
ToggleBtn.Parent = Frame

local ToggleCorner = Instance.new("UICorner")
ToggleCorner.CornerRadius = UDim.new(0, 6)
ToggleCorner.Parent = ToggleBtn

--// Tombol Apply
local ApplyBtn = Instance.new("TextButton")
ApplyBtn.Size = UDim2.new(0.8, 0, 0, 35)
ApplyBtn.Position = UDim2.new(0.1, 0, 0.66, 0)
ApplyBtn.Text = "Apply Speed"
ApplyBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
ApplyBtn.BackgroundColor3 = Color3.fromRGB(80, 80, 200)
ApplyBtn.TextSize = 13
ApplyBtn.Font = Enum.Font.GothamBold
ApplyBtn.Parent = Frame

local ApplyCorner = Instance.new("UICorner")
ApplyCorner.CornerRadius = UDim.new(0, 6)
ApplyCorner.Parent = ApplyBtn

--// Status Text
local Status = Instance.new("TextLabel")
Status.Size = UDim2.new(1, 0, 0, 20)
Status.Position = UDim2.new(0, 0, 0.88, 0)
Status.BackgroundTransparency = 1
Status.Text = "Status: Idle"
Status.TextColor3 = Color3.fromRGB(180, 180, 180)
Status.TextSize = 11
Status.Font = Enum.Font.Gotham
Status.Parent = Frame

--// Logika
local speedEnabled = false
local currentSpeed = 16
local MAX_SPEED = 100000

local function getHum()
    local char = player.Character
    return char and char:FindFirstChildOfClass("Humanoid")
end

local function applySpeed()
    if speedEnabled then
        local hum = getHum()
        if hum then
            hum.WalkSpeed = currentSpeed
        end
    end
end

--// Apply Button
ApplyBtn.MouseButton1Click:Connect(function()
    local input = tonumber(TextBox.Text)
    
    if not input then
        Status.Text = "Status: ❌ Masukkan angka!"
        Status.TextColor3 = Color3.fromRGB(255, 100, 100)
        return
    end
    
    -- Clamp max 100000
    if input > MAX_SPEED then
        input = MAX_SPEED
        TextBox.Text = tostring(MAX_SPEED)
        Status.Text = "Status: ⚠ Max 100000!"
        Status.TextColor3 = Color3.fromRGB(255, 200, 100)
    elseif input < 1 then
        input = 1
        TextBox.Text = "1"
        Status.Text = "Status: ⚠ Min 1!"
        Status.TextColor3 = Color3.fromRGB(255, 200, 100)
    else
        Status.Text = "Status: ✅ Speed = " .. input
        Status.TextColor3 = Color3.fromRGB(100, 255, 100)
    end
    
    currentSpeed = input
    applySpeed()
end)

--// Toggle Button
ToggleBtn.MouseButton1Click:Connect(function()
    speedEnabled = not speedEnabled
    
    if speedEnabled then
        ToggleBtn.Text = "Speed: ON"
        ToggleBtn.BackgroundColor3 = Color3.fromRGB(50, 150, 80)
        applySpeed()
    else
        ToggleBtn.Text = "Speed: OFF"
        ToggleBtn.BackgroundColor3 = Color3.fromRGB(80, 80, 100)
        local hum = getHum()
        if hum then
            hum.WalkSpeed = 16
        end
    end
end)

--// Handle respawn
player.CharacterAdded:Connect(function(char)
    character = char
    humanoid = char:WaitForChild("Humanoid")
    task.wait(1)
    applySpeed()
end)

--// Loop kecil biar speed tetap kekunci walau game reset
task.spawn(function()
    while task.wait(0.5) do
        if speedEnabled then
            applySpeed()
        end
    end
end)

print("✅ Speed GUI Loaded! Max: 100000")

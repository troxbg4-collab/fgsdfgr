local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local LocalPlayer = Players.LocalPlayer
local PlayerGui = LocalPlayer:WaitForChild("PlayerGui")

pcall(function()
    if PlayerGui:FindFirstChild("CatHUB_UI") then
        PlayerGui.CatHUB_UI:Destroy()
    end
end)

local screenGui = Instance.new("ScreenGui")
screenGui.Name = "CatHUB_UI"
screenGui.ResetOnSpawn = false
screenGui.Parent = PlayerGui

local sound = Instance.new("Sound")
sound.SoundId = "rbxassetid://17582213219"
sound.Volume = 1
sound.Parent = screenGui

local mainFrame = Instance.new("Frame")
mainFrame.Size = UDim2.new(0, 200, 0, 160)
mainFrame.Position = UDim2.new(0.5, -100, 0.5, -80)
mainFrame.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
mainFrame.BackgroundTransparency = 0.2
mainFrame.BorderSizePixel = 0
mainFrame.Parent = screenGui
Instance.new("UICorner", mainFrame).CornerRadius = UDim.new(0, 12)

local mainStroke = Instance.new("UIStroke")
mainStroke.Color = Color3.new(1, 1, 1)
mainStroke.Thickness = 1.5
mainStroke.Parent = mainFrame

local strokeGradient = Instance.new("UIGradient")
strokeGradient.Color = ColorSequence.new({
    ColorSequenceKeypoint.new(0, Color3.new(1, 1, 1)),
    ColorSequenceKeypoint.new(0.5, Color3.new(0, 0, 0)),
    ColorSequenceKeypoint.new(1, Color3.new(1, 1, 1)),
})
strokeGradient.Parent = mainStroke

RunService.RenderStepped:Connect(function()
    strokeGradient.Rotation = (strokeGradient.Rotation + 1) % 360
end)

local titleBar = Instance.new("Frame")
titleBar.Size = UDim2.new(1, 0, 0, 32)
titleBar.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
titleBar.BackgroundTransparency = 0.5
titleBar.BorderSizePixel = 0
titleBar.Parent = mainFrame
Instance.new("UICorner", titleBar).CornerRadius = UDim.new(0, 12)

local titleLabel = Instance.new("TextLabel")
titleLabel.Size = UDim2.new(1, -20, 1, 0)
titleLabel.Position = UDim2.new(0, 10, 0, 0)
titleLabel.BackgroundTransparency = 1
titleLabel.Text = "Stars Half TP"
titleLabel.TextColor3 = Color3.new(1, 1, 1)
titleLabel.Font = Enum.Font.GothamBold
titleLabel.TextSize = 14
titleLabel.TextXAlignment = Enum.TextXAlignment.Left
titleLabel.Parent = titleBar

local minimized = false
local minBtn = Instance.new("TextButton")
minBtn.Size = UDim2.new(0, 24, 0, 24)
minBtn.Position = UDim2.new(1, -28, 0.5, -12)
minBtn.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
minBtn.Text = "-"
minBtn.TextColor3 = Color3.new(1, 1, 1)
minBtn.Font = Enum.Font.GothamBold
minBtn.TextSize = 14
minBtn.BorderSizePixel = 0
minBtn.Parent = titleBar
Instance.new("UICorner", minBtn).CornerRadius = UDim.new(0, 6)

local content = Instance.new("Frame")
content.Size = UDim2.new(1, -20, 1, -42)
content.Position = UDim2.new(0, 10, 0, 37)
content.BackgroundTransparency = 1
content.Parent = mainFrame

local function createButton(label, yOffset)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(1, 0, 0, 30)
    btn.Position = UDim2.new(0, 0, 0, yOffset)
    btn.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
    btn.BorderSizePixel = 0
    btn.Text = ""
    btn.AutoButtonColor = false
    btn.Parent = content
    Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 8)

    local lbl = Instance.new("TextLabel")
    lbl.Size = UDim2.new(1, -12, 1, 0)
    lbl.Position = UDim2.new(0, 12, 0, 0)
    lbl.BackgroundTransparency = 1
    lbl.Text = label
    lbl.TextColor3 = Color3.new(1, 1, 1)
    lbl.Font = Enum.Font.GothamBold
    lbl.TextSize = 12
    lbl.TextXAlignment = Enum.TextXAlignment.Left
    lbl.Parent = btn

    return btn, lbl
end

local btn1, lbl1 = createButton("Active (Reset)", 0)
local btn2, lbl2 = createButton("Teleport & Steal [F]", 36)
local btn3, lbl3 = createButton("Speed: OFF", 72)

-- // ACTIVE (RESET) //
btn1.MouseButton1Click:Connect(function()
    sound:Play()
    LocalPlayer.Character:FindFirstChildOfClass("Humanoid").Health = 0
end)

-- // SPEED TOGGLE //
local speedActive = false
btn3.MouseButton1Click:Connect(function()
    sound:Play()
    speedActive = not speedActive
    local char = LocalPlayer.Character
    if char then
        local hum = char:FindFirstChildOfClass("Humanoid")
        if hum then
            if speedActive then
                hum.WalkSpeed = 33
                lbl3.Text = "Speed: ON"
                btn3.BackgroundColor3 = Color3.fromRGB(0, 170, 0)
            else
                hum.WalkSpeed = 16
                lbl3.Text = "Speed: OFF"
                btn3.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
            end
        end
    end
end)

-- // DRAGGING //
local dragging, dragStart, startPos

titleBar.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
        dragging = true
        dragStart = input.Position
        startPos = mainFrame.Position
        input.Changed:Connect(function()
            if input.UserInputState == Enum.UserInputState.End then
                dragging = false
            end
        end)
    end
end)

UserInputService.InputChanged:Connect(function(input)
    if dragging and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
        local delta = input.Position - dragStart
        mainFrame.Position = UDim2.new(
            startPos.X.Scale, startPos.X.Offset + delta.X,
            startPos.Y.Scale, startPos.Y.Offset + delta.Y
        )
    end
end)

-- // MINIMIZE //
minBtn.MouseButton1Click:Connect(function()
    sound:Play()
    minimized = not minimized
    if minimized then
        content.Visible = false
        mainFrame.Size = UDim2.new(0, 200, 0, 32)
        minBtn.Text = "+"
    else
        content.Visible = true
        mainFrame.Size = UDim2.new(0, 200, 0, 160)
        minBtn.Text = "-"
    end
end)

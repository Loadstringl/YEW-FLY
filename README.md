local player = game.Players.LocalPlayer
local camera = workspace.CurrentCamera
local RunService = game:GetService("RunService")
local UIS = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")

local flyEnabled = false
local bv, bg, flyConn
local flySpeed = 70
local defaultSpeed = 70


local sg = Instance.new("ScreenGui")
sg.ResetOnSpawn = false
sg.IgnoreGuiInset = true
sg.Parent = player.PlayerGui


local notif = Instance.new("Frame")
notif.Size = UDim2.new(0, 240, 0, 50)
notif.Position = UDim2.new(1, -250, 0, 10)
notif.BackgroundColor3 = Color3.fromRGB(0, 130, 255)
notif.BorderSizePixel = 0
notif.Parent = sg
Instance.new("UICorner", notif).CornerRadius = UDim.new(0, 8)
local notifLabel = Instance.new("TextLabel", notif)
notifLabel.Size = UDim2.new(1, 0, 1, 0)
notifLabel.Text = "✈ YEW FLY"
notifLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
notifLabel.BackgroundTransparency = 1
notifLabel.TextScaled = true
notifLabel.Font = Enum.Font.GothamBold
task.delay(5, function() notif:Destroy() end)


local frame = Instance.new("Frame")
frame.Size = UDim2.new(0, 190, 0, 140)
frame.Position = UDim2.new(0.5, -95, 0.02, 0)
frame.BackgroundColor3 = Color3.fromRGB(15, 15, 15)
frame.Active = true
frame.Draggable = true
frame.Parent = sg
Instance.new("UICorner", frame).CornerRadius = UDim.new(0, 10)
local stroke = Instance.new("UIStroke", frame)
stroke.Thickness = 2
stroke.Color = Color3.fromRGB(0, 170, 255)

local title = Instance.new("TextLabel", frame)
title.Size = UDim2.new(0.75, 0, 0, 30)
title.Text = "✈ YEW FLY"
title.TextColor3 = Color3.fromRGB(0, 210, 255)
title.BackgroundTransparency = 1
title.TextScaled = true
title.Font = Enum.Font.GothamBold

local minBtn = Instance.new("TextButton", frame)
minBtn.Size = UDim2.new(0, 28, 0, 28)
minBtn.Position = UDim2.new(1, -32, 0, 2)
minBtn.BackgroundColor3 = Color3.fromRGB(200, 0, 0)
minBtn.Text = "−"
minBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
minBtn.TextScaled = true
minBtn.Font = Enum.Font.GothamBold
Instance.new("UICorner", minBtn).CornerRadius = UDim.new(0, 6)

local speedBox = Instance.new("TextBox", frame)
speedBox.Size = UDim2.new(0.88, 0, 0, 28)
speedBox.Position = UDim2.new(0.06, 0, 0, 33)
speedBox.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
speedBox.TextColor3 = Color3.fromRGB(255, 255, 255)
speedBox.Text = "70"
speedBox.TextScaled = true
speedBox.PlaceholderText = "Speed..."
speedBox.Font = Enum.Font.Gotham
Instance.new("UICorner", speedBox).CornerRadius = UDim.new(0, 6)


local upBtn = Instance.new("TextButton", frame)
upBtn.Size = UDim2.new(0.42, 0, 0, 30)
upBtn.Position = UDim2.new(0.06, 0, 0, 68)
upBtn.BackgroundColor3 = Color3.fromRGB(0, 100, 200)
upBtn.Text = "⬆ UP"
upBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
upBtn.TextScaled = true
upBtn.Font = Enum.Font.GothamBold
Instance.new("UICorner", upBtn).CornerRadius = UDim.new(0, 6)

local downBtn = Instance.new("TextButton", frame)
downBtn.Size = UDim2.new(0.42, 0, 0, 30)
downBtn.Position = UDim2.new(0.52, 0, 0, 68)
downBtn.BackgroundColor3 = Color3.fromRGB(0, 100, 200)
downBtn.Text = "⬇ DOWN"
downBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
downBtn.TextScaled = true
downBtn.Font = Enum.Font.GothamBold
Instance.new("UICorner", downBtn).CornerRadius = UDim.new(0, 6)

local flyBtn = Instance.new("TextButton", frame)
flyBtn.Size = UDim2.new(0.88, 0, 0, 30)
flyBtn.Position = UDim2.new(0.06, 0, 0, 103)
flyBtn.BackgroundColor3 = Color3.fromRGB(0, 130, 255)
flyBtn.Text = "START FLY"
flyBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
flyBtn.TextScaled = true
flyBtn.Font = Enum.Font.GothamBold
Instance.new("UICorner", flyBtn).CornerRadius = UDim.new(0, 8)


local statusLabel = Instance.new("TextLabel", frame)
statusLabel.Size = UDim2.new(1, 0, 0, 18)
statusLabel.Position = UDim2.new(0, 0, 1, -18)
statusLabel.Text = "Push joystick to fly!"
statusLabel.TextColor3 = Color3.fromRGB(150, 150, 150)
statusLabel.BackgroundTransparency = 1
statusLabel.TextScaled = true
statusLabel.Font = Enum.Font.Gotham

local heightOffset = 0
local targetHeight = 0
local isGoingUp = false
local isGoingDown = false

upBtn.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.Touch or input.UserInputType == Enum.UserInputType.MouseButton1 then
        isGoingUp = true
    end
end)
upBtn.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.Touch or input.UserInputType == Enum.UserInputType.MouseButton1 then
        isGoingUp = false
    end
end)

downBtn.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.Touch or input.UserInputType == Enum.UserInputType.MouseButton1 then
        isGoingDown = true
    end
end)
downBtn.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.Touch or input.UserInputType == Enum.UserInputType.MouseButton1 then
        isGoingDown = false
    end
end)


local minimized = false
minBtn.MouseButton1Click:Connect(function()
    minimized = not minimized
    if minimized then
        frame.Size = UDim2.new(0, 190, 0, 32)
        for _, child in ipairs(frame:GetChildren()) do
            if child ~= title and child ~= minBtn and child:IsA("GuiObject") then
                child.Visible = false
            end
        end
        minBtn.Text = "+"
    else
        frame.Size = UDim2.new(0, 190, 0, 140)
        for _, child in ipairs(frame:GetChildren()) do
            if child:IsA("GuiObject") then
                child.Visible = true
            end
        end
        minBtn.Text = "−"
    end
end)


local function stopFly()
    flyEnabled = false
    heightOffset = 0
    targetHeight = 0
    
    if flyConn then 
        flyConn:Disconnect() 
        flyConn = nil 
    end
    
    local char = player.Character
    if char then
        local hum = char:FindFirstChildOfClass("Humanoid")
        if hum then
            hum.PlatformStand = false
            hum:ChangeState(Enum.HumanoidStateType.GettingUp)
            hum.AutoRotate = true
        end
        
        
        local animate = char:FindFirstChild("Animate")
        if animate then
            animate.Disabled = false
        end
    end
    
    if bv then 
        bv:Destroy() 
        bv = nil 
    end
    if bg then 
        bg:Destroy() 
        bg = nil 
    end
    
    flyBtn.Text = "START FLY"
    flyBtn.BackgroundColor3 = Color3.fromRGB(0, 130, 255)
    statusLabel.Text = "Push joystick to fly!"
    statusLabel.TextColor3 = Color3.fromRGB(150, 150, 150)
end


flyBtn.MouseButton1Click:Connect(function()
    flyEnabled = not flyEnabled
    local char = player.Character
    if not char then 
        statusLabel.Text = "No Character!"
        statusLabel.TextColor3 = Color3.fromRGB(255, 50, 50)
        flyEnabled = false
        return 
    end
    
    local root = char:FindFirstChild("HumanoidRootPart")
    local hum = char:FindFirstChildOfClass("Humanoid")
    if not root or not hum then 
        statusLabel.Text = "Missing Parts!"
        statusLabel.TextColor3 = Color3.fromRGB(255, 50, 50)
        flyEnabled = false
        return 
    end

    local speed = tonumber(speedBox.Text)
    if not speed or speed <= 0 then speed = 70 end
    flySpeed = speed

    if flyEnabled then
        
        hum.PlatformStand = true
        hum:ChangeState(Enum.HumanoidStateType.Physics)
        hum.AutoRotate = false
        
        
        local animate = char:FindFirstChild("Animate")
        if animate then
            animate.Disabled = true
        end

        
        bv = Instance.new("BodyVelocity")
        bv.MaxForce = Vector3.new(9e9, 9e9, 9e9)
        bv.Velocity = Vector3.new(0, 0, 0)
        bv.Parent = root

        bg = Instance.new("BodyGyro")
        bg.MaxTorque = Vector3.new(9e9, 9e9, 9e9)
        bg.P = 100000
        bg.D = 5000
        bg.CFrame = root.CFrame
        bg.Parent = root
        
        
        targetHeight = root.Position.Y

        flyBtn.Text = "STOP FLY ✓"
        flyBtn.BackgroundColor3 = Color3.fromRGB(0, 180, 0)
        statusLabel.Text = "Use joystick to move!"
        statusLabel.TextColor3 = Color3.fromRGB(0, 255, 100)

        
        flyConn = RunService.Heartbeat:Connect(function()
            local c = player.Character
            if not c then 
                stopFly() 
                return 
            end
            
            local r = c:FindFirstChild("HumanoidRootPart")
            local h = c:FindFirstChildOfClass("Humanoid")
            if not r or not h then 
                stopFly() 
                return 
            end

            
            local moveDir = h.MoveDirection
            
            
            if isGoingUp then
                targetHeight = targetHeight + (flySpeed * 0.02)
            elseif isGoingDown then
                targetHeight = targetHeight - (flySpeed * 0.02)
            end
            
            
            local targetPos = r.Position
            if moveDir.Magnitude > 0.01 then
                
                local horizontalDir = Vector3.new(moveDir.X, 0, moveDir.Z).Unit
                bv.Velocity = horizontalDir * flySpeed
                
                
                bg.CFrame = CFrame.new(r.Position, r.Position + horizontalDir)
                statusLabel.Text = "Flying..."
            else
                
                local currentY = r.Position.Y
                local heightDiff = targetHeight - currentY
                local verticalVel = math.clamp(heightDiff * 5, -flySpeed * 0.5, flySpeed * 0.5)
                
                bv.Velocity = Vector3.new(0, verticalVel, 0)
                bg.CFrame = CFrame.new(r.Position, r.Position + camera.CFrame.LookVector)
                statusLabel.Text = "Hovering..."
            end
        end)
    else
        stopFly()
    end
end)


player.CharacterAdded:Connect(function()
    task.wait(1)
    stopFly()
    local char = player.Character
    if char then
        local animate = char:FindFirstChild("Animate")
        if animate then
            animate.Disabled = false
        end
    end
end)

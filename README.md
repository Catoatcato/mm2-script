-- LocalScript inside StarterGui

local player = game.Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local humanoid = character:WaitForChild("Humanoid")
local hrp = character:WaitForChild("HumanoidRootPart")

local screenGui = Instance.new("ScreenGui", player:WaitForChild("PlayerGui"))

-- Helper to make buttons
local function makeButton(text, posY)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(0, 200, 0, 50)
    btn.Position = UDim2.new(0.5, -100, posY, 0)
    btn.Text = text
    btn.Parent = screenGui
    return btn
end

------------------------------------------------
-- Fly Toggle
------------------------------------------------
local flying = false
local flyBtn = makeButton("Toggle Fly", 0.6)

flyBtn.MouseButton1Click:Connect(function()
    flying = not flying
    if flying then
        humanoid.PlatformStand = true
        local conn
        conn = game:GetService("RunService").Heartbeat:Connect(function()
            if not flying then conn:Disconnect() return end
            local moveDir = Vector3.new()
            local input = game:GetService("UserInputService")

            if input:IsKeyDown(Enum.KeyCode.W) then
                moveDir += workspace.CurrentCamera.CFrame.LookVector
            end
            if input:IsKeyDown(Enum.KeyCode.S) then
                moveDir -= workspace.CurrentCamera.CFrame.LookVector
            end
            if input:IsKeyDown(Enum.KeyCode.A) then
                moveDir -= workspace.CurrentCamera.CFrame.RightVector
            end
            if input:IsKeyDown(Enum.KeyCode.D) then
                moveDir += workspace.CurrentCamera.CFrame.RightVector
            end
            if input:IsKeyDown(Enum.KeyCode.Space) then
                moveDir += Vector3.new(0,1,0)
            end
            if input:IsKeyDown(Enum.KeyCode.LeftShift) then
                moveDir += Vector3.new(0,-1,0)
            end

            hrp.Velocity = moveDir * 100 -- flight speed
        end)
    else
        humanoid.PlatformStand = false
        hrp.Velocity = Vector3.new(0,0,0)
    end
end)

------------------------------------------------
-- Infinite Jump Toggle
------------------------------------------------
local infiniteJump = false
local jumpBtn = makeButton("Infinite Jump", 0.7)

jumpBtn.MouseButton1Click:Connect(function()
    infiniteJump = not infiniteJump
end)

game:GetService("UserInputService").JumpRequest:Connect(function()
    if infiniteJump then
        humanoid:ChangeState(Enum.HumanoidStateType.Jumping)
    end
end)

------------------------------------------------
-- Invincible Toggle
------------------------------------------------
local invincible = false
local invBtn = makeButton("Invincible", 0.8)

invBtn.MouseButton1Click:Connect(function()
    invincible = not invincible
    if invincible then
        humanoid.MaxHealth = math.huge
        humanoid.Health = math.huge
    else
        humanoid.MaxHealth = 100
        humanoid.Health = 100
    end
end)

------------------------------------------------
-- Spin Power (fast spin)
------------------------------------------------
local spinBtn = makeButton("Spin Power", 0.9)

spinBtn.MouseButton1Click:Connect(function()
    local spin = Instance.new("BodyAngularVelocity")
    spin.AngularVelocity = Vector3.new(0, 200, 0) -- fast spin speed
    spin.MaxTorque = Vector3.new(0, math.huge, 0)
    spin.Parent = hrp
    game:GetService("Debris"):AddItem(spin, 2) -- lasts 2 seconds
end)

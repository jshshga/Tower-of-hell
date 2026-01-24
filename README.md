-- إنشاء الواجهة (GUI)
local player = game.Players.LocalPlayer
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "PlatformGui_Final"
screenGui.Parent = player:WaitForChild("PlayerGui")

local OLD_POS = UDim2.new(0.5, -75, 0.8, 0)
local SHIFTED_POS = UDim2.new(0.5, -110, 0.8, 0)

local mainButton = Instance.new("TextButton")
mainButton.Size = UDim2.new(0, 150, 0, 50)
mainButton.Position = OLD_POS
mainButton.Text = "تفعيل المنصة: OFF"
mainButton.BackgroundColor3 = Color3.fromRGB(200, 0, 0)
mainButton.TextColor3 = Color3.fromRGB(255, 255, 255)
mainButton.Font = Enum.Font.SourceSansBold
mainButton.TextSize = 18
mainButton.Parent = screenGui

local addButton = Instance.new("TextButton")
addButton.Size = UDim2.new(0, 50, 0, 50)
addButton.Position = UDim2.new(0.5, 50, 0.8, 0)
addButton.Text = "+"
addButton.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
addButton.TextColor3 = Color3.fromRGB(255, 255, 255)
addButton.Font = Enum.Font.SourceSansBold
addButton.TextSize = 30
addButton.Visible = false
addButton.Parent = screenGui

local corner1 = Instance.new("UICorner")
corner1.CornerRadius = UDim.new(0, 10)
corner1.Parent = mainButton
local corner2 = Instance.new("UICorner")
corner2.CornerRadius = UDim.new(0, 10)
corner2.Parent = addButton

-- البرمجة المعدلة لمنع الرفع التلقائي
local runService = game:GetService("RunService")
local active = false
local currentPlatform = nil
local Y_OFFSET = -3.5 
local lastFloorY = 0 -- لتخزين آخر ارتفاع ثابت

local function createPlatform()
    local p = Instance.new("Part")
    p.Size = Vector3.new(15, 1, 15)
    p.Anchored = true
    p.CanCollide = true
    p.Material = Enum.Material.Neon
    p.Color = Color3.fromRGB(255, 255, 255)
    p.Transparency = 0.3
    p.Parent = workspace
    return p
end

mainButton.MouseButton1Click:Connect(function()
    active = not active
    if active then
        mainButton.Text = "تفعيل المنصة: ON"
        mainButton.BackgroundColor3 = Color3.fromRGB(0, 180, 0)
        mainButton.Position = SHIFTED_POS
        addButton.Visible = true
        currentPlatform = createPlatform()
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            lastFloorY = player.Character.HumanoidRootPart.Position.Y + Y_OFFSET
        end
    else
        mainButton.Text = "تفعيل المنصة: OFF"
        mainButton.BackgroundColor3 = Color3.fromRGB(200, 0, 0)
        mainButton.Position = OLD_POS
        addButton.Visible = false
        if currentPlatform then currentPlatform:Destroy() currentPlatform = nil end
    end
end)

addButton.MouseButton1Click:Connect(function()
    if active and currentPlatform then
        currentPlatform.Transparency = 0.1
        currentPlatform.Color = Color3.fromRGB(150, 150, 150)
        currentPlatform = createPlatform()
    end
end)

runService.RenderStepped:Connect(function()
    if active and currentPlatform then
        local character = player.Character
        local hrp = character and character:FindFirstChild("HumanoidRootPart")
        local humanoid = character and character:FindFirstChild("Humanoid")
        
        if hrp and humanoid then
            local currentHeight = hrp.Position.Y + Y_OFFSET
            
            -- إذا كان اللاعب يقفز أو يطير للأعلى، نحدث الارتفاع
            if currentHeight > lastFloorY + 0.5 then 
                lastFloorY = currentHeight
            -- إذا سقط اللاعب أسفل المنصة، نعيد ضبط الارتفاع لتحته مباشرة
            elseif currentHeight < lastFloorY - 1 then
                lastFloorY = currentHeight
            end
            
            -- تحديث الموقع مع تثبيت الارتفاع (Y) ومنع الاهتزاز
            currentPlatform.Position = Vector3.new(hrp.Position.X, lastFloorY, hrp.Position.Z)
        end
    end
end)

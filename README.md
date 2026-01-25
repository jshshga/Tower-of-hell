local player = game.Players.LocalPlayer
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "PlatformGui_Medium_Style"
screenGui.Parent = player:WaitForChild("PlayerGui")
screenGui.ResetOnSpawn = false 

-- المتغيرات
local active = false
local currentPlatform = nil
local history = {} 
local deletedHistory = {} 
local Y_OFFSET = -3.5 
local lastFloorY = 0

-- الواجهة الرئيسية (قابلة للتحريك)
local mainFrame = Instance.new("Frame")
mainFrame.Size = UDim2.new(0, 160, 0, 50) -- حجم وسط
mainFrame.Position = UDim2.new(0.5, -80, 0.8, 0)
mainFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
mainFrame.BackgroundTransparency = 0.2
mainFrame.Parent = screenGui

-- زر الفتح (أعلى اليسار)
local openButton = Instance.new("TextButton")
openButton.Size = UDim2.new(0, 90, 0, 40)
openButton.Position = UDim2.new(0, 15, 0, 15)
openButton.Text = "فتح القائمة"
openButton.BackgroundColor3 = Color3.fromRGB(0, 120, 215)
openButton.TextColor3 = Color3.fromRGB(255, 255, 255)
openButton.Font = Enum.Font.SourceSansBold
openButton.TextSize = 18 -- خط وسط
openButton.Visible = false
openButton.Parent = screenGui

-- وظيفة التحريك
local function makeDraggable(obj)
    local UserInputService = game:GetService("UserInputService")
    local dragging, dragInput, dragStart, startPos
    obj.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
            dragging = true
            dragStart = input.Position
            startPos = obj.Position
            input.Changed:Connect(function()
                if input.UserInputState == Enum.UserInputState.End then dragging = false end
            end)
        end
    end)
    obj.InputChanged:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch then
            dragInput = input
        end
    end)
    UserInputService.InputChanged:Connect(function(input)
        if input == dragInput and dragging then
            local delta = input.Position - dragStart
            obj.Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + delta.X, startPos.Y.Scale, startPos.Y.Offset + delta.Y)
        end
    end)
end
makeDraggable(mainFrame)
makeDraggable(openButton)

-- الزر الرئيسي
local mainButton = Instance.new("TextButton")
mainButton.Size = UDim2.new(1, 0, 1, 0)
mainButton.Text = "تفعيل المنصة: OFF"
mainButton.BackgroundColor3 = Color3.fromRGB(200, 0, 0)
mainButton.TextColor3 = Color3.fromRGB(255, 255, 255)
mainButton.Font = Enum.Font.SourceSansBold
mainButton.TextSize = 18 -- خط وسط
mainButton.Parent = mainFrame

-- أزرار (+) و (-)
local addButton = Instance.new("TextButton")
addButton.Size = UDim2.new(0, 45, 0, 45)
addButton.Position = UDim2.new(1, 10, 0, 2)
addButton.Text = "+"
addButton.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
addButton.TextColor3 = Color3.fromRGB(255, 255, 255)
addButton.Font = Enum.Font.SourceSansBold
addButton.TextSize = 25 -- حجم وسط
addButton.Visible = false
addButton.Parent = mainFrame

local removeButton = Instance.new("TextButton")
removeButton.Size = UDim2.new(0, 45, 0, 45)
removeButton.Position = UDim2.new(0, -55, 0, 2)
removeButton.Text = "-"
removeButton.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
removeButton.TextColor3 = Color3.fromRGB(255, 255, 255)
removeButton.Font = Enum.Font.SourceSansBold
removeButton.TextSize = 30 -- حجم وسط
removeButton.Visible = false
removeButton.Parent = mainFrame

-- أزرار التحكم العلوية
local closeButton = Instance.new("TextButton")
closeButton.Size = UDim2.new(0, 30, 0, 30)
closeButton.Position = UDim2.new(1, -30, 0, -35) 
closeButton.Text = "X"
closeButton.BackgroundColor3 = Color3.fromRGB(150, 0, 0)
closeButton.TextColor3 = Color3.fromRGB(255, 255, 255)
closeButton.Font = Enum.Font.SourceSansBold
closeButton.TextSize = 16
closeButton.Parent = mainFrame

local clearAllButton = Instance.new("TextButton")
clearAllButton.Size = UDim2.new(0, 30, 0, 30)
clearAllButton.Position = UDim2.new(1, -65, 0, -35) 
clearAllButton.Text = "🗑"
clearAllButton.BackgroundColor3 = Color3.fromRGB(200, 100, 0)
clearAllButton.TextColor3 = Color3.fromRGB(255, 255, 255)
clearAllButton.TextSize = 16
clearAllButton.Parent = mainFrame

local restoreButton = Instance.new("TextButton")
restoreButton.Size = UDim2.new(0, 30, 0, 30)
restoreButton.Position = UDim2.new(1, -100, 0, -35) 
restoreButton.Text = "↩️"
restoreButton.BackgroundColor3 = Color3.fromRGB(0, 150, 100)
restoreButton.TextColor3 = Color3.fromRGB(255, 255, 255)
restoreButton.TextSize = 16
restoreButton.Parent = mainFrame

-- حواف دائرية
local function addCorner(parent)
    local c = Instance.new("UICorner")
    c.CornerRadius = UDim.new(0, 8)
    c.Parent = parent
end
addCorner(mainFrame); addCorner(mainButton); addCorner(addButton); 
addCorner(removeButton); addCorner(closeButton); addCorner(clearAllButton); addCorner(openButton); addCorner(restoreButton)

-- المنطق البرمجي للمنصة
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
        addButton.Visible = true; removeButton.Visible = true
        currentPlatform = createPlatform()
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            lastFloorY = player.Character.HumanoidRootPart.Position.Y + Y_OFFSET
        end
    else
        mainButton.Text = "تفعيل المنصة: OFF"
        mainButton.BackgroundColor3 = Color3.fromRGB(200, 0, 0)
        addButton.Visible = false; removeButton.Visible = false
        if currentPlatform then currentPlatform:Destroy(); currentPlatform = nil end
    end
end)

addButton.MouseButton1Click:Connect(function()
    if active and currentPlatform then
        currentPlatform.Color = Color3.fromRGB(150, 150, 150); currentPlatform.Transparency = 0.5
        table.insert(history, currentPlatform) 
        currentPlatform = createPlatform()
    end
end)

removeButton.MouseButton1Click:Connect(function()
    if #history > 0 then
        local lastPart = table.remove(history, #history)
        if lastPart then
            lastPart.Parent = nil
            table.insert(deletedHistory, lastPart)
        end
    end
end)

clearAllButton.MouseButton1Click:Connect(function()
    if #history > 0 then
        for _, p in pairs(history) do
            p.Parent = nil
            table.insert(deletedHistory, p)
        end
        history = {}
    end
end)

restoreButton.MouseButton1Click:Connect(function()
    if #deletedHistory > 0 then
        for i = #deletedHistory, 1, -1 do
            local p = table.remove(deletedHistory, i)
            p.Parent = workspace
            table.insert(history, p)
        end
    end
end)

closeButton.MouseButton1Click:Connect(function()
    mainFrame.Visible = false; openButton.Visible = true
end)

openButton.MouseButton1Click:Connect(function()
    mainFrame.Visible = true; openButton.Visible = false
end)

game:GetService("RunService").RenderStepped:Connect(function()
    if active and currentPlatform then
        local character = player.Character
        if character and character:FindFirstChild("HumanoidRootPart") then
            local hrp = character.HumanoidRootPart
            local currentHeight = hrp.Position.Y + Y_OFFSET
            if math.abs(currentHeight - lastFloorY) > 1.5 then
                lastFloorY = currentHeight
            end
            currentPlatform.Position = Vector3.new(hrp.Position.X, lastFloorY, hrp.Position.Z)
        end
    end
end)

-- إنشاء الواجهة (GUI)
local player = game.Players.LocalPlayer
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "PlatformGui_Pro"
screenGui.Parent = player:WaitForChild("PlayerGui")
screenGui.ResetOnSpawn = false 

local OLD_POS = UDim2.new(0.5, -75, 0.8, 0)
local SHIFTED_POS = UDim2.new(0.5, -75, 0.8, 0) -- سيبقى في المنتصف والأزرار حوله

-- الزر الرئيسي (ON/OFF)
local mainButton = Instance.new("TextButton")
mainButton.Size = UDim2.new(0, 150, 0, 50)
mainButton.Position = OLD_POS
mainButton.Text = "تفعيل المنصة: OFF"
mainButton.BackgroundColor3 = Color3.fromRGB(200, 0, 0)
mainButton.TextColor3 = Color3.fromRGB(255, 255, 255)
mainButton.Font = Enum.Font.SourceSansBold
mainButton.TextSize = 18
mainButton.Parent = screenGui

-- زر (+) للإضافة (يمين)
local addButton = Instance.new("TextButton")
addButton.Size = UDim2.new(0, 45, 0, 45)
addButton.Position = UDim2.new(0.5, 85, 0.8, 2)
addButton.Text = "+"
addButton.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
addButton.TextColor3 = Color3.fromRGB(255, 255, 255)
addButton.Font = Enum.Font.SourceSansBold
addButton.TextSize = 25
addButton.Visible = false
addButton.Parent = screenGui

-- زر (-) للحذف (يسار)
local removeButton = Instance.new("TextButton")
removeButton.Size = UDim2.new(0, 45, 0, 45)
removeButton.Position = UDim2.new(0.5, -130, 0.8, 2)
removeButton.Text = "-"
removeButton.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
removeButton.TextColor3 = Color3.fromRGB(255, 255, 255)
removeButton.Font = Enum.Font.SourceSansBold
removeButton.TextSize = 25
removeButton.Visible = false
removeButton.Parent = screenGui

-- زر (X) لحذف الواجهة
local closeButton = Instance.new("TextButton")
closeButton.Size = UDim2.new(0, 25, 0, 25)
closeButton.Position = UDim2.new(0.5, 110, 0.74, 0) 
closeButton.Text = "X"
closeButton.BackgroundColor3 = Color3.fromRGB(150, 0, 0)
closeButton.TextColor3 = Color3.fromRGB(255, 255, 255)
closeButton.Font = Enum.Font.SourceSansBold
closeButton.TextSize = 14
closeButton.Parent = screenGui

-- حواف دائرية
local function addCorner(parent)
    local c = Instance.new("UICorner")
    c.CornerRadius = UDim.new(0, 8)
    c.Parent = parent
end
addCorner(mainButton)
addCorner(addButton)
addCorner(removeButton)
addCorner(closeButton)

-- البرمجة
local runService = game:GetService("RunService")
local active = false
local currentPlatform = nil
local history = {} -- جدول لحفظ المنصات الثابتة فقط
local Y_OFFSET = -3.5 
local lastFloorY = 0

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
        addButton.Visible = true
        removeButton.Visible = true
        currentPlatform = createPlatform()
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            lastFloorY = player.Character.HumanoidRootPart.Position.Y + Y_OFFSET
        end
    else
        mainButton.Text = "تفعيل المنصة: OFF"
        mainButton.BackgroundColor3 = Color3.fromRGB(200, 0, 0)
        addButton.Visible = false
        removeButton.Visible = false
        if currentPlatform then currentPlatform:Destroy() end
        for _, p in pairs(history) do p:Destroy() end
        history = {}
        active = false
    end
end)

addButton.MouseButton1Click:Connect(function()
    if active and currentPlatform then
        currentPlatform.Color = Color3.fromRGB(150, 150, 150) -- تمييز المنصة القديمة
        table.insert(history, currentPlatform) -- إضافة المنصة القديمة للتاريخ
        currentPlatform = createPlatform() -- إنشاء منصة جديدة تتبعك
    end
end)

removeButton.MouseButton1Click:Connect(function()
    if #history > 0 then
        local lastPart = table.remove(history, #history) -- سحب آخر بارت من الجدول
        if lastPart then lastPart:Destroy() end -- حذفه
    end
end)

closeButton.MouseButton1Click:Connect(function()
    if currentPlatform then currentPlatform:Destroy() end
    for _, p in pairs(history) do p:Destroy() end
    screenGui:Destroy()
end)

runService.RenderStepped:Connect(function()
    if active and currentPlatform then
        local character = player.Character
        if character and character:FindFirstChild("HumanoidRootPart") then
            local hrp = character.HumanoidRootPart
            local currentHeight = hrp.Position.Y + Y_OFFSET
            if currentHeight > lastFloorY + 0.5 then 
                lastFloorY = currentHeight
            elseif currentHeight < lastFloorY - 1.5 then
                lastFloorY = currentHeight
            end
            currentPlatform.Position = Vector3.new(hrp.Position.X, lastFloorY, hrp.Position.Z)
        end
    end
end)

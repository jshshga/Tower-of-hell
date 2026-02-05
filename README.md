local player = game.Players.LocalPlayer
local playerGui = player:WaitForChild("PlayerGui")
local TweenService = game:GetService("TweenService")
local RunService = game:GetService("RunService")

-----------------------------------------------------------
-- [1] واجهة النسخ (بيضوية + نيون + خط كبير)
-----------------------------------------------------------
local introGui = Instance.new("ScreenGui")
introGui.Name = "IntroWelcome"
introGui.Parent = playerGui

local welcomeFrame = Instance.new("Frame")
welcomeFrame.Size = UDim2.new(0, 0, 0, 0)
welcomeFrame.Position = UDim2.new(0.5, 0, 0.5, 0)
welcomeFrame.BackgroundColor3 = Color3.fromRGB(20, 20, 20)
welcomeFrame.ClipsDescendants = true
welcomeFrame.Parent = introGui
Instance.new("UICorner", welcomeFrame).CornerRadius = UDim.new(0, 20)

local uiStroke = Instance.new("UIStroke", welcomeFrame)
uiStroke.Color = Color3.fromRGB(0, 170, 255)
uiStroke.Thickness = 3

local welcomeLabel = Instance.new("TextLabel")
welcomeLabel.Size = UDim2.new(1, 0, 0, 100)
welcomeLabel.Text = "اتمنى يعجبك سكربت و هاذا اسم حسابي و لا تنسى تحط فولو"
welcomeLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
welcomeLabel.BackgroundTransparency = 1
welcomeLabel.Font = Enum.Font.GothamBold
welcomeLabel.TextSize = 19
welcomeLabel.TextWrapped = true
welcomeLabel.Parent = welcomeFrame

local copyBtn = Instance.new("TextButton")
copyBtn.Size = UDim2.new(0, 180, 0, 50)
copyBtn.Position = UDim2.new(0.5, -90, 0, 120)
copyBtn.Text = "نسخ الحساب"
copyBtn.BackgroundColor3 = Color3.fromRGB(0, 120, 215)
copyBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
copyBtn.Font = Enum.Font.GothamBold
copyBtn.TextSize = 20
copyBtn.Parent = welcomeFrame
Instance.new("UICorner", copyBtn).CornerRadius = UDim.new(1, 0)

welcomeFrame:TweenSizeAndPosition(UDim2.new(0, 380, 0, 200), UDim2.new(0.5, -190, 0.5, -100), "Out", "Back", 0.5)
spawn(function()
    while welcomeFrame.Parent do
        TweenService:Create(uiStroke, TweenInfo.new(1), {Color = Color3.fromRGB(255, 0, 255)}):Play()
        wait(1)
        TweenService:Create(uiStroke, TweenInfo.new(1), {Color = Color3.fromRGB(0, 170, 255)}):Play()
        wait(1)
    end
end)

copyBtn.MouseButton1Click:Connect(function()
    if setclipboard then setclipboard("auigegif") end
    copyBtn.Text = "تم النسخ! ✅"
    wait(1)
    copyBtn.Text = "نسخ الحساب"
end)
task.delay(5, function() if introGui then introGui:Destroy() end end)

-----------------------------------------------------------
-- [2] سكربت المنصة (مع استرجاع ألوان الأزرار الأصلية)
-----------------------------------------------------------
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "Custom_Platform_System"
screenGui.Parent = playerGui
screenGui.ResetOnSpawn = false

local active, noclipActive, checkpointActive = false, false, false
local currentPlatform = nil
local history, deletedHistory = {}, {}
local Y_OFFSET = -3.5
local checkPointPos, cpMarker = nil, nil 

local mainFrame = Instance.new("Frame")
mainFrame.Size = UDim2.new(0, 180, 0, 55) 
mainFrame.Position = UDim2.new(0.5, -90, 0.8, 0)
mainFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
mainFrame.BackgroundTransparency = 0.2
mainFrame.Draggable = true 
mainFrame.Active = true
mainFrame.Parent = screenGui
Instance.new("UICorner", mainFrame).CornerRadius = UDim.new(0, 8)

local openButton = Instance.new("TextButton")
openButton.Size = UDim2.new(0, 120, 0, 50)
openButton.Text = "فتح القائمة"
openButton.BackgroundColor3 = Color3.fromRGB(0, 120, 215)
openButton.TextColor3 = Color3.fromRGB(255, 255, 255)
openButton.TextSize = 20
openButton.Visible = false
openButton.Draggable = true
openButton.Parent = screenGui
Instance.new("UICorner", openButton).CornerRadius = UDim.new(0, 8)

local mainButton = Instance.new("TextButton")
mainButton.Size = UDim2.new(1, 0, 1, 0)
mainButton.Text = "المنصة: OFF"
mainButton.BackgroundColor3 = Color3.fromRGB(200, 0, 0)
mainButton.TextColor3 = Color3.fromRGB(255, 255, 255)
mainButton.Font = Enum.Font.SourceSansBold
mainButton.TextSize = 20
mainButton.Parent = mainFrame
Instance.new("UICorner", mainButton).CornerRadius = UDim.new(0, 8)

local function createTopBtn(text, pos, color)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(0, 36, 0, 36)
    btn.Position = pos
    btn.Text = text
    btn.BackgroundColor3 = color
    btn.TextColor3 = Color3.fromRGB(255, 255, 255)
    btn.TextSize = 20
    btn.Parent = mainFrame
    Instance.new("UICorner", btn).CornerRadius = UDim.new(0, 8)
    return btn
end

local closeBtn = createTopBtn("X", UDim2.new(1, -36, 0, -42), Color3.fromRGB(150, 0, 0))
local clearBtn = createTopBtn("🗑", UDim2.new(1, -76, 0, -42), Color3.fromRGB(200, 100, 0))
local restBtn = createTopBtn("↩️", UDim2.new(1, -116, 0, -42), Color3.fromRGB(0, 150, 100))
local noclBtn = createTopBtn("👻", UDim2.new(1, -156, 0, -42), Color3.fromRGB(200, 0, 0))
local cpBtn = createTopBtn("📍", UDim2.new(1, -196, 0, -42), Color3.fromRGB(200, 0, 0))

-- أزرار + و - (رجعت اللون 40, 40, 40)
local addBtn = Instance.new("TextButton")
addBtn.Size = UDim2.new(0, 50, 0, 50)
addBtn.Position = UDim2.new(1, 10, 0, 2)
addBtn.Text = "+"; addBtn.TextSize = 30; addBtn.Visible = false; 
addBtn.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
addBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
addBtn.Parent = mainFrame
Instance.new("UICorner", addBtn).CornerRadius = UDim.new(0, 8)

local remBtn = Instance.new("TextButton")
remBtn.Size = UDim2.new(0, 50, 0, 50)
remBtn.Position = UDim2.new(0, -60, 0, 2)
remBtn.Text = "-"; remBtn.TextSize = 35; remBtn.Visible = false; 
remBtn.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
remBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
remBtn.Parent = mainFrame
Instance.new("UICorner", remBtn).CornerRadius = UDim.new(0, 8)

-- منطق النوكلب (استرجاع التصادم)
noclBtn.MouseButton1Click:Connect(function()
    noclipActive = not noclipActive
    noclBtn.BackgroundColor3 = noclipActive and Color3.fromRGB(0, 180, 0) or Color3.fromRGB(200, 0, 0)
    if not noclipActive then
        local char = player.Character
        if char then
            if char:FindFirstChild("HumanoidRootPart") then char.HumanoidRootPart.CanCollide = true end
            if char:FindFirstChild("UpperTorso") then char.UpperTorso.CanCollide = true
            elseif char:FindFirstChild("Torso") then char.Torso.CanCollide = true end
        end
    end
end)

cpBtn.MouseButton1Click:Connect(function()
    checkpointActive = not checkpointActive
    cpBtn.BackgroundColor3 = checkpointActive and Color3.fromRGB(0, 180, 0) or Color3.fromRGB(200, 0, 0)
    if checkpointActive and player.Character then
        checkPointPos = player.Character.HumanoidRootPart.CFrame
        if cpMarker then cpMarker:Destroy() end
        cpMarker = Instance.new("Part", workspace)
        cpMarker.Size = Vector3.new(4, 0.4, 4); cpMarker.CFrame = checkPointPos * CFrame.new(0, -3.0, 0)
        cpMarker.Anchored = true; cpMarker.CanCollide = false; cpMarker.Material = "Neon"; cpMarker.Color = Color3.fromRGB(0, 255, 255); cpMarker.Transparency = 0.4
    else
        checkPointPos = nil
        if cpMarker then cpMarker:Destroy(); cpMarker = nil end
    end
end)

player.CharacterAdded:Connect(function(char)
    if checkpointActive and checkPointPos then
        local hrp = char:WaitForChild("HumanoidRootPart", 5)
        if hrp then task.wait(0.2) hrp.CFrame = checkPointPos end
    end
end)

mainButton.MouseButton1Click:Connect(function()
    active = not active
    mainButton.Text = active and "المنصة: ON" or "المنصة: OFF"
    mainButton.BackgroundColor3 = active and Color3.fromRGB(0, 180, 0) or Color3.fromRGB(200, 0, 0)
    addBtn.Visible, remBtn.Visible = active, active
    if active then
        currentPlatform = Instance.new("Part", workspace)
        currentPlatform.Size = Vector3.new(15, 1, 15); currentPlatform.Anchored = true; currentPlatform.Material = "Neon"; currentPlatform.Transparency = 0.3
    elseif currentPlatform then currentPlatform:Destroy(); currentPlatform = nil end
end)

addBtn.MouseButton1Click:Connect(function()
    if active and currentPlatform then
        local p = currentPlatform:Clone(); p.Parent = workspace; p.Color = Color3.fromRGB(180, 180, 180); p.Transparency = 0.5
        table.insert(history, p)
    end
end)

remBtn.MouseButton1Click:Connect(function()
    if #history > 0 then local p = table.remove(history, #history); p.Parent = nil; table.insert(deletedHistory, p) end
end)

clearBtn.MouseButton1Click:Connect(function()
    for _, p in pairs(history) do p.Parent = nil; table.insert(deletedHistory, p) end
    history = {}
end)

restBtn.MouseButton1Click:Connect(function()
    for i = #deletedHistory, 1, -1 do local p = table.remove(deletedHistory, i); p.Parent = workspace; table.insert(history, p) end
end)

closeBtn.MouseButton1Click:Connect(function() mainFrame.Visible = false; openButton.Visible = true end)
openButton.MouseButton1Click:Connect(function() mainFrame.Visible = true; openButton.Visible = false end)

RunService.Stepped:Connect(function()
    if player.Character then
        if active and currentPlatform then currentPlatform.Position = player.Character.HumanoidRootPart.Position + Vector3.new(0, Y_OFFSET, 0) end
        if noclipActive then
            for _, p in pairs(player.Character:GetDescendants()) do if p:IsA("BasePart") then p.CanCollide = false end end
        end
    end
end)

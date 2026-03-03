--=========================================================
-- [0] BLACKLIST (NEON RAINBOW WARNING) - مدموج داخل السكربت
--=========================================================
local Players = game:GetService("Players")
local plr = Players.LocalPlayer
local TweenService = game:GetService("TweenService")
local RunService = game:GetService("RunService")

local BLACKLIST = {  
    "Waleedking70", "JJFH23456771", "omarbkp", 
    "fastma_storage", "No_Louay", "mlk123456771", "fling_2009", "fahad_moen3", "ONIRYTC",
    ""
}

local function IsPlayerBlacklisted(playerName)
    local nameLower = playerName:lower()
    for _, blacklistedName in ipairs(BLACKLIST) do
        if nameLower == blacklistedName:lower() then
            return true
        end
    end
    return false
end

local function ShowBlacklistNeonAndKick()
    local playerGui = plr:WaitForChild("PlayerGui")

    local warningGui = Instance.new("ScreenGui")
    warningGui.Name = "BLACKLIST_GUI"
    warningGui.Parent = playerGui
    warningGui.ResetOnSpawn = false

    local frame = Instance.new("Frame")
    frame.Parent = warningGui
    frame.Size = UDim2.new(0, 0, 0, 0)
    frame.Position = UDim2.new(0.5, 0, 0.5, 0)
    frame.AnchorPoint = Vector2.new(0.5, 0.5)
    frame.BackgroundColor3 = Color3.fromRGB(10, 10, 14)
    frame.ClipsDescendants = true
    Instance.new("UICorner", frame).CornerRadius = UDim.new(0, 26)

    local stroke = Instance.new("UIStroke")
    stroke.Parent = frame
    stroke.Thickness = 6
    stroke.Color = Color3.fromRGB(0, 255, 255)

    local grad = Instance.new("UIGradient")
    grad.Parent = stroke
    grad.Color = ColorSequence.new{
        ColorSequenceKeypoint.new(0.00, Color3.fromRGB(255, 0, 0)),
        ColorSequenceKeypoint.new(0.16, Color3.fromRGB(255, 160, 0)),
        ColorSequenceKeypoint.new(0.33, Color3.fromRGB(255, 255, 0)),
        ColorSequenceKeypoint.new(0.50, Color3.fromRGB(0, 255, 90)),
        ColorSequenceKeypoint.new(0.66, Color3.fromRGB(0, 255, 255)),
        ColorSequenceKeypoint.new(0.83, Color3.fromRGB(60, 80, 255)),
        ColorSequenceKeypoint.new(1.00, Color3.fromRGB(255, 0, 255)),
    }

    -- Glow fake (طبقتين Frame شفافة)
    local glow1 = Instance.new("Frame", frame)
    glow1.BackgroundColor3 = Color3.fromRGB(0, 255, 255)
    glow1.BackgroundTransparency = 0.85
    glow1.Size = UDim2.new(1, 40, 1, 40)
    glow1.Position = UDim2.new(0.5, 0, 0.5, 0)
    glow1.AnchorPoint = Vector2.new(0.5, 0.5)
    Instance.new("UICorner", glow1).CornerRadius = UDim.new(0, 34)

    local glow2 = Instance.new("Frame", frame)
    glow2.BackgroundColor3 = Color3.fromRGB(255, 0, 255)
    glow2.BackgroundTransparency = 0.9
    glow2.Size = UDim2.new(1, 80, 1, 80)
    glow2.Position = UDim2.new(0.5, 0, 0.5, 0)
    glow2.AnchorPoint = Vector2.new(0.5, 0.5)
    Instance.new("UICorner", glow2).CornerRadius = UDim.new(0, 46)

    local title = Instance.new("TextLabel")
    title.Parent = frame
    title.BackgroundTransparency = 1
    title.Size = UDim2.new(1, -24, 0, 70)
    title.Position = UDim2.new(0, 12, 0, 16)
    title.Font = Enum.Font.GothamBlack
    title.TextScaled = true
    title.TextColor3 = Color3.fromRGB(255, 255, 255)
    title.Text = "⚠️ BLACKLIST DETECTED ⚠️"

    local sub = Instance.new("TextLabel")
    sub.Parent = frame
    sub.BackgroundTransparency = 1
    sub.Size = UDim2.new(1, -24, 0, 55)
    sub.Position = UDim2.new(0, 12, 0, 92)
    sub.Font = Enum.Font.GothamBold
    sub.TextScaled = true
    sub.TextColor3 = Color3.fromRGB(220, 220, 220)
    sub.Text = "Access Denied"

    local nameLbl = Instance.new("TextLabel")
    nameLbl.Parent = frame
    nameLbl.BackgroundTransparency = 1
    nameLbl.Size = UDim2.new(1, -24, 0, 35)
    nameLbl.Position = UDim2.new(0, 12, 0, 150)
    nameLbl.Font = Enum.Font.Gotham
    nameLbl.TextScaled = true
    nameLbl.TextColor3 = Color3.fromRGB(180, 180, 180)
    nameLbl.Text = "User: " .. plr.Name

    -- دخول Bounce
    frame:TweenSize(UDim2.new(0, 520, 0, 220), "Out", "Back", 0.55, true)

    -- رينبو + نبض + لمعان
    task.spawn(function()
        local t0 = tick()
        while frame.Parent do
            local t = tick() - t0
            grad.Rotation = (t * 120) % 360
            stroke.Thickness = 6 + math.sin(t * 6) * 2

            local pulse = (math.sin(t * 4) + 1) / 2
            glow1.BackgroundTransparency = 0.90 - (pulse * 0.12)
            glow2.BackgroundTransparency = 0.94 - (pulse * 0.08)

            task.wait()
        end
    end)

    task.wait(4)
    plr:Kick("🚫 دز")
end

if IsPlayerBlacklisted(plr.Name) then
    ShowBlacklistNeonAndKick()
    return
end

--=========================================================
-- [1] واجهة النسخ (الأصلية)
--=========================================================
local player = plr
local playerGui = player:WaitForChild("PlayerGui")

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
task.spawn(function()
    while welcomeFrame.Parent do
        TweenService:Create(uiStroke, TweenInfo.new(1), {Color = Color3.fromRGB(255, 0, 255)}):Play()
        task.wait(1)
        TweenService:Create(uiStroke, TweenInfo.new(1), {Color = Color3.fromRGB(0, 170, 255)}):Play()
        task.wait(1)
    end
end)

copyBtn.MouseButton1Click:Connect(function()
    if setclipboard then setclipboard("auigegif") end
    copyBtn.Text = "تم النسخ! ✅"
    task.wait(1)
    copyBtn.Text = "نسخ الحساب"
end)
task.delay(5, function() if introGui then introGui:Destroy() end end)

--=========================================================
-- [2] سكربت المنصة (تتبع القفز الحقيقي فقط)
--=========================================================
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "Custom_Platform_System"
screenGui.Parent = playerGui
screenGui.ResetOnSpawn = false

local active, noclipActive, checkpointActive = false, false, false
local noclipStates = {}
local currentPlatform = nil
local fixedY = 0 
local history, deletedHistory = {}, {}
local Y_OFFSET = -3.45 
local checkPointPos, cpMarker = nil, nil 
local clearAllMode = false

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

local addBtn = Instance.new("TextButton")
addBtn.Size = UDim2.new(0, 50, 0, 50); addBtn.Position = UDim2.new(1, 10, 0, 2)
addBtn.Text = "+"; addBtn.TextSize = 30; addBtn.Visible = false; 
addBtn.BackgroundColor3 = Color3.fromRGB(40, 40, 40); addBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
addBtn.Parent = mainFrame; Instance.new("UICorner", addBtn).CornerRadius = UDim.new(0, 8)

local remBtn = Instance.new("TextButton")
remBtn.Size = UDim2.new(0, 50, 0, 50); remBtn.Position = UDim2.new(0, -60, 0, 2)
remBtn.Text = "-"; remBtn.TextSize = 35; remBtn.Visible = false; 
remBtn.BackgroundColor3 = Color3.fromRGB(40, 40, 40); remBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
remBtn.Parent = mainFrame; Instance.new("UICorner", remBtn).CornerRadius = UDim.new(0, 8)

-- noclip toggle
noclBtn.MouseButton1Click:Connect(function()
    noclipActive = not noclipActive
    noclBtn.BackgroundColor3 = noclipActive and Color3.fromRGB(0, 180, 0) or Color3.fromRGB(200, 0, 0)

    local char = player.Character
    if noclipActive then
        noclipStates = {}
        if char then
            for _, p in pairs(char:GetDescendants()) do
                if p:IsA("BasePart") then
                    noclipStates[p] = p.CanCollide
                    p.CanCollide = false
                end
            end
        end
    else
        if char then
            for part, prevCanCollide in pairs(noclipStates) do
                if part and part.Parent then
                    part.CanCollide = (prevCanCollide == nil) and true or prevCanCollide
                end
            end
        end
        noclipStates = {}
    end
end)

-- checkpoint toggle
cpBtn.MouseButton1Click:Connect(function()
    checkpointActive = not checkpointActive
    
    if checkpointActive then
        if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            local success, err = pcall(function()
                checkPointPos = player.Character.HumanoidRootPart.CFrame
            end)
            
            if success then
                cpBtn.BackgroundColor3 = Color3.fromRGB(0, 180, 0)
                
                if cpMarker then 
                    cpMarker:Destroy() 
                    cpMarker = nil
                end
                
                cpMarker = Instance.new("Part")
                cpMarker.Size = Vector3.new(4, 0.4, 4)
                cpMarker.CFrame = checkPointPos * CFrame.new(0, -3.0, 0)
                cpMarker.Anchored = true
                cpMarker.CanCollide = false
                cpMarker.Material = Enum.Material.Neon
                cpMarker.Color = Color3.fromRGB(0, 255, 255)
                cpMarker.Transparency = 0.4
                cpMarker.Name = "CheckpointMarker"
                cpMarker.Parent = workspace
                
                print("✅ تم حفظ الشيك بوينت بنجاح!")
            else
                checkpointActive = false
                cpBtn.BackgroundColor3 = Color3.fromRGB(200, 0, 0)
                warn("⚠️ حدث خطأ في حفظ الشيك بوينت:", err)
            end
        else
            checkpointActive = false
            cpBtn.BackgroundColor3 = Color3.fromRGB(200, 0, 0)
            warn("⚠️ لا يوجد شخصية أو جزء HumanoidRootPart")
        end
    else
        cpBtn.BackgroundColor3 = Color3.fromRGB(200, 0, 0)
        checkPointPos = nil
        if cpMarker then 
            cpMarker:Destroy() 
            cpMarker = nil
        end
    end
end)

mainButton.MouseButton1Click:Connect(function()
    active = not active
    mainButton.Text = active and "المنصة: ON" or "المنصة: OFF"
    mainButton.BackgroundColor3 = active and Color3.fromRGB(0, 180, 0) or Color3.fromRGB(200, 0, 0)
    addBtn.Visible, remBtn.Visible = active, active
    if active and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
        fixedY = player.Character.HumanoidRootPart.Position.Y + Y_OFFSET
        currentPlatform = Instance.new("Part", workspace)
        currentPlatform.Size = Vector3.new(15, 0.8, 15)
        currentPlatform.Anchored = true
        currentPlatform.Material = Enum.Material.Neon
        currentPlatform.Transparency = 0.3
        currentPlatform.Color = Color3.fromRGB(0, 255, 255)
    elseif currentPlatform then
        currentPlatform:Destroy()
        currentPlatform = nil
    end
end)

-- follow platform
RunService.RenderStepped:Connect(function()
    if player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
        local hrp = player.Character.HumanoidRootPart
        
        if active and currentPlatform then
            local currentY = hrp.Position.Y + Y_OFFSET
            if hrp.Velocity.Y > 10 then 
                fixedY = currentY
            end
            currentPlatform.Position = Vector3.new(hrp.Position.X, fixedY, hrp.Position.Z)
        end
        
        if noclipActive then
            for _, p in pairs(player.Character:GetDescendants()) do
                if p:IsA("BasePart") then p.CanCollide = false end
            end
        end
    end
end)

-- add platform
addBtn.MouseButton1Click:Connect(function()
    if active and currentPlatform then
        local p = currentPlatform:Clone()
        p.Parent = workspace
        p.Color = Color3.fromRGB(180, 180, 180)
        p.Transparency = 0.5
        table.insert(history, p)
        print("تمت إضافة منصة جديدة. العدد الكلي: " .. #history)
    end
end)

-- remove last platform (send to trash)
remBtn.MouseButton1Click:Connect(function()
    if #history > 0 then
        local p = table.remove(history, #history)
        if p then
            p.Parent = nil
            table.insert(deletedHistory, p)
            clearAllMode = false
            print("تم حذف منصة. المتبقي: " .. #history .. " | في سلة المهملات: " .. #deletedHistory)
        end
    else
        print("⚠️ لا توجد منصات لحذفها")
    end
end)

-- clear all platforms (send all to trash)
clearBtn.MouseButton1Click:Connect(function()
    if #history > 0 then
        print("🗑️ إرسال جميع المنصات إلى سلة المهملات...")
        for i = #history, 1, -1 do
            local p = table.remove(history, i)
            if p then
                p.Parent = nil
                table.insert(deletedHistory, p)
            end
        end
        clearAllMode = true
        print("✅ تم إرسال " .. #deletedHistory .. " منصة إلى سلة المهملات")
    else
        print("⚠️ لا توجد منصات لتنظيفها")
    end
end)

-- restore platforms
restBtn.MouseButton1Click:Connect(function()
    if #deletedHistory > 0 then
        if clearAllMode then
            print("↩️ استرجاع جميع المنصات من الزبالة...")
            for i = #deletedHistory, 1, -1 do
                local p = table.remove(deletedHistory, i)
                if p then
                    p.Parent = workspace
                    table.insert(history, p)
                end
            end
            clearAllMode = false
            print("✅ تم استرجاع جميع المنصات. العدد الكلي: " .. #history)
        else
            local p = table.remove(deletedHistory, #deletedHistory)
            if p then
                p.Parent = workspace
                table.insert(history, p)
                print("↩️ تم استرجاع منصة واحدة. المتبقي في السلة: " .. #deletedHistory .. " | العدد الكلي: " .. #history)
            end
        end
    else
        print("⚠️ سلة المهملات فارغة")
    end
end)

closeBtn.MouseButton1Click:Connect(function() mainFrame.Visible = false; openButton.Visible = true end)
openButton.MouseButton1Click:Connect(function() mainFrame.Visible = true; openButton.Visible = false end)

--=========================================================
-- [3] نظام إعادة الظهور الآمن عند الموت
--=========================================================
local function setupRespawnHandler()
    player.CharacterAdded:Connect(function(character)
        task.wait(0.5)
        if checkpointActive and checkPointPos then
            local humanoidRootPart = character:WaitForChild("HumanoidRootPart", 3)
            if humanoidRootPart then
                local success, err = pcall(function()
                    humanoidRootPart.CFrame = checkPointPos
                    print("✅ تم التحويل إلى الشيك بوينت!")
                end)
                if not success then
                    warn("⚠️ لم يتم التحويل إلى الشيك بوينت:", err)
                end
            end
        end
    end)
end

setupRespawnHandler()
print("✅ تم تحميل سكربت المنصة بنجاح!")
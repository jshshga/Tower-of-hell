-- إنشاء الواجهة (GUI) برمجياً لضمان ظهورها
local player = game.Players.LocalPlayer
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "PlatformGui"
screenGui.Parent = player:WaitForChild("PlayerGui")

local button = Instance.new("TextButton")
button.Size = UDim2.new(0, 150, 0, 50)
button.Position = UDim2.new(0.5, -75, 0.8, 0) -- يظهر في أسفل منتصف الشاشة
button.Text = "تفعيل المنصة: OFF"
button.BackgroundColor3 = Color3.fromRGB(200, 0, 0)
button.TextColor3 = Color3.fromRGB(255, 255, 255)
button.Font = Enum.Font.SourceSansBold
button.TextSize = 20
button.Parent = screenGui

-- إضافة حواف دائرية للزر
local corner = Instance.new("UICorner")
corner.CornerRadius = UDim.new(0, 10)
corner.Parent = button

-- برمجة المنصة
local character = player.Character or player.CharacterAdded:Wait()
local humanoid = nil
local hrp = nil
local runService = game:GetService("RunService")

local active = false
local platform = nil

-- إعدادات المنصة
local PLATFORM_SIZE = Vector3.new(15, 1, 15)
local Y_OFFSET = -3.5

button.MouseButton1Click:Connect(function()
	active = not active
	character = player.Character
	if character then
		humanoid = character:FindFirstChild("Humanoid")
		hrp = character:FindFirstChild("HumanoidRootPart")
	end

	if active then
		button.Text = "تفعيل المنصة: ON"
		button.BackgroundColor3 = Color3.fromRGB(0, 180, 0)
		
		-- إنشاء الـ Part
		platform = Instance.new("Part")
		platform.Size = PLATFORM_SIZE
		platform.Anchored = true
		platform.CanCollide = true
		platform.Material = Enum.Material.Neon
		platform.Color = Color3.fromRGB(255, 255, 255)
		platform.Transparency = 0.2
		platform.Parent = workspace
	else
		button.Text = "تفعيل المنصة: OFF"
		button.BackgroundColor3 = Color3.fromRGB(200, 0, 0)
		if platform then
			platform:Destroy()
			platform = nil
		end
	end
end)

runService.RenderStepped:Connect(function()
	if active and platform and character and character:FindFirstChild("HumanoidRootPart") then
		local currentHRP = character.HumanoidRootPart
		local currentHumanoid = character:FindFirstChild("Humanoid")
		
		local targetPos = currentHRP.Position + Vector3.new(0, Y_OFFSET, 0)
		
		-- سرعة الرفع عند القفز
		if currentHumanoid and (currentHumanoid:GetState() == Enum.HumanoidStateType.Jumping or currentHumanoid:GetState() == Enum.HumanoidStateType.Freefall) then
			platform.CFrame = platform.CFrame:Lerp(CFrame.new(currentHRP.Position + Vector3.new(0, Y_OFFSET + 0.6, 0)), 0.3)
		else
			platform.CFrame = CFrame.new(targetPos)
		end
	end
end)

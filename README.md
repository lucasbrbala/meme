-- LocalScript (StarterPlayerScripts)
local Players = game:GetService("Players")
local UIS = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer
local Mouse = LocalPlayer:GetMouse()

-- Settings
local fovRadius = 100
local teamCheck = true
local discordLink = "https://discord.gg/seuservidor" -- Altere para seu link real
local aimbotEnabled = true

-- Interface
local gui = Instance.new("ScreenGui", LocalPlayer:WaitForChild("PlayerGui"))
gui.Name = "AimbotArsenal"
gui.Enabled = false

local frame = Instance.new("Frame", gui)
frame.Size = UDim2.new(0, 250, 0, 300)
frame.Position = UDim2.new(0.5, -125, 0.3, 0)
frame.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
frame.BorderSizePixel = 0

local title = Instance.new("TextLabel", frame)
title.Size = UDim2.new(1, 0, 0, 40)
title.Text = "Aimbot Arsenal"
title.BackgroundTransparency = 1
title.TextColor3 = Color3.fromRGB(255, 255, 255)
title.Font = Enum.Font.SourceSansBold
title.TextSize = 22

-- FOV Size buttons
local fovDown = Instance.new("TextButton", frame)
fovDown.Text = "- FOV"
fovDown.Size = UDim2.new(0.45, 0, 0, 30)
fovDown.Position = UDim2.new(0.05, 0, 0.2, 0)
fovDown.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
fovDown.TextColor3 = Color3.new(1,1,1)

local fovUp = Instance.new("TextButton", frame)
fovUp.Text = "+ FOV"
fovUp.Size = UDim2.new(0.45, 0, 0, 30)
fovUp.Position = UDim2.new(0.5, 0, 0.2, 0)
fovUp.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
fovUp.TextColor3 = Color3.new(1,1,1)

-- Team check toggle
local teamCheckBtn = Instance.new("TextButton", frame)
teamCheckBtn.Text = "Team Check: ON"
teamCheckBtn.Size = UDim2.new(0.9, 0, 0, 30)
teamCheckBtn.Position = UDim2.new(0.05, 0, 0.4, 0)
teamCheckBtn.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
teamCheckBtn.TextColor3 = Color3.new(1,1,1)

-- Discord Button
local discordBtn = Instance.new("TextButton", frame)
discordBtn.Text = "Discord"
discordBtn.Size = UDim2.new(0.9, 0, 0, 30)
discordBtn.Position = UDim2.new(0.05, 0, 0.55, 0)
discordBtn.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
discordBtn.TextColor3 = Color3.new(1,1,1)

-- Créditos
local credits = Instance.new("TextLabel", frame)
credits.Text = "Created by ttk"
credits.Size = UDim2.new(1, 0, 0, 30)
credits.Position = UDim2.new(0, 0, 1, -30)
credits.BackgroundTransparency = 1
credits.TextColor3 = Color3.fromRGB(255, 255, 255)
credits.Font = Enum.Font.SourceSansItalic
credits.TextSize = 14

-- Botão funcionalidade
fovDown.MouseButton1Click:Connect(function()
	fovRadius = math.max(10, fovRadius - 10)
end)

fovUp.MouseButton1Click:Connect(function()
	fovRadius = math.min(500, fovRadius + 10)
end)

teamCheckBtn.MouseButton1Click:Connect(function()
	teamCheck = not teamCheck
	teamCheckBtn.Text = "Team Check: " .. (teamCheck and "ON" or "OFF")
end)

discordBtn.MouseButton1Click:Connect(function()
	setclipboard(discordLink)
end)

-- Toggle menu com tecla P
UIS.InputBegan:Connect(function(input, gpe)
	if gpe then return end
	if input.KeyCode == Enum.KeyCode.P then
		gui.Enabled = not gui.Enabled
	end
end)

-- Função de Aimbot
local function getClosestPlayer()
	local closest = nil
	local shortestDist = fovRadius

	for _, player in pairs(Players:GetPlayers()) do
		if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("Head") then
			if teamCheck and player.Team == LocalPlayer.Team then
				continue
			end
			local headPos = workspace.CurrentCamera:WorldToViewportPoint(player.Character.Head.Position)
			local dist = (Vector2.new(headPos.X, headPos.Y) - Vector2.new(Mouse.X, Mouse.Y)).Magnitude
			if headPos.Z > 0 and dist < shortestDist then
				shortestDist = dist
				closest = player.Character.Head
			end
		end
	end
	return closest
end

-- Aimbot loop
RunService.RenderStepped:Connect(function()
	if UIS:IsMouseButtonPressed(Enum.UserInputType.MouseButton2) and aimbotEnabled then
		local target = getClosestPlayer()
		if target then
			local cam = workspace.CurrentCamera
			cam.CFrame = CFrame.new(cam.CFrame.Position, target.Position)
		end
	end
end)

-- FOV círculo (desenho visual opcional)
local fovCircle = Drawing.new("Circle")
fovCircle.Color = Color3.fromRGB(255, 255, 255)
fovCircle.Thickness = 1
fovCircle.Radius = fovRadius
fovCircle.Visible = true
fovCircle.Filled = false

RunService.RenderStepped:Connect(function()
	fovCircle.Position = Vector2.new(Mouse.X, Mouse.Y)
	fovCircle.Radius = fovRadius
end)

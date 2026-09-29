-- DHEN AIM LOCK + ESP
-- Galaxy GUI + Bold Font
-- LocalScript
-- StarterPlayer > StarterPlayerScripts

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")

local player = Players.LocalPlayer
local camera = workspace.CurrentCamera

--==================================================
-- AIM SETTINGS
--==================================================

local AIM_SMOOTH = 0.20
local FOV_RADIUS = 180

local aiming = false
local teamCheck = true
local wallCheck = true
local fovCheck = true
local target = nil

--==================================================
-- ESP SETTINGS
--==================================================

local espEnabled = false
local espTeamCheck = true
local nameESP = true
local boxESP = false
local distanceESP = false

local espObjects = {}

--==================================================
-- GUI
--==================================================

local gui = Instance.new("ScreenGui")
gui.Name = "Dh3nAimbotESP"
gui.ResetOnSpawn = false
gui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
gui.Parent = player:WaitForChild("PlayerGui")

--==================================================
-- GALAXY COLORS
--==================================================

local GALAXY_DARK = Color3.fromRGB(8, 6, 22)
local GALAXY_PURPLE = Color3.fromRGB(115, 65, 210)
local GALAXY_BLUE = Color3.fromRGB(55, 100, 220)
local GALAXY_BUTTON = Color3.fromRGB(28, 24, 55)
local GALAXY_OFF = Color3.fromRGB(20, 18, 38)
local WHITE = Color3.fromRGB(245, 245, 255)

--==================================================
-- MAIN PANEL
--==================================================

local panel = Instance.new("Frame")
panel.Name = "MainPanel"
panel.Size = UDim2.fromOffset(250, 380)
panel.Position = UDim2.new(0.5, -125, 0.5, -190)
panel.BackgroundColor3 = GALAXY_DARK
panel.BorderSizePixel = 0
panel.ClipsDescendants = true
panel.Parent = gui

local panelCorner = Instance.new("UICorner")
panelCorner.CornerRadius = UDim.new(0, 14)
panelCorner.Parent = panel

-- Galaxy gradient

local galaxyGradient = Instance.new("UIGradient")
galaxyGradient.Color = ColorSequence.new({
	ColorSequenceKeypoint.new(0, Color3.fromRGB(8, 5, 25)),
	ColorSequenceKeypoint.new(0.35, Color3.fromRGB(35, 12, 65)),
	ColorSequenceKeypoint.new(0.65, Color3.fromRGB(15, 25, 70)),
	ColorSequenceKeypoint.new(1, Color3.fromRGB(5, 7, 25))
})
galaxyGradient.Rotation = 35
galaxyGradient.Parent = panel

-- Glow border

local panelStroke = Instance.new("UIStroke")
panelStroke.Thickness = 2
panelStroke.Color = GALAXY_PURPLE
panelStroke.Transparency = 0.15
panelStroke.Parent = panel

--==================================================
-- GALAXY STARS
--==================================================

math.randomseed(tick())

for i = 1, 55 do
	local star = Instance.new("Frame")

	local size = math.random(1, 3)

	star.Size = UDim2.fromOffset(size, size)

	star.Position = UDim2.new(
		math.random(),
		0,
		math.random(),
		0
	)

	star.BackgroundColor3 = Color3.fromRGB(
		math.random(170, 255),
		math.random(170, 255),
		255
	)

	star.BackgroundTransparency = math.random(20, 75) / 100
	star.BorderSizePixel = 0
	star.ZIndex = 1
	star.Parent = panel

	local starCorner = Instance.new("UICorner")
	starCorner.CornerRadius = UDim.new(1, 0)
	starCorner.Parent = star

	task.spawn(function()
		while star.Parent do

			local fadeOut = TweenService:Create(
				star,
				TweenInfo.new(
					math.random(7, 15) / 10,
					Enum.EasingStyle.Sine,
					Enum.EasingDirection.InOut
				),
				{
					BackgroundTransparency = math.random(65, 95) / 100
				}
			)

			fadeOut:Play()
			fadeOut.Completed:Wait()

			local fadeIn = TweenService:Create(
				star,
				TweenInfo.new(
					math.random(7, 15) / 10,
					Enum.EasingStyle.Sine,
					Enum.EasingDirection.InOut
				),
				{
					BackgroundTransparency = math.random(10, 45) / 100
				}
			)

			fadeIn:Play()
			fadeIn.Completed:Wait()
		end
	end)
end

--==================================================
-- TITLE
--==================================================

local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, 0, 0, 35)
title.Position = UDim2.fromOffset(0, 5)
title.BackgroundTransparency = 1
title.Text = "DHEN"
title.TextColor3 = WHITE
title.TextSize = 19
title.Font = Enum.Font.GothamBold
title.ZIndex = 5
title.Parent = panel

--==================================================
-- LOGO
--==================================================

local logo = Instance.new("TextButton")
logo.Name = "DhenLogo"
logo.Size = UDim2.fromOffset(60, 60)
logo.Position = UDim2.new(0.5, -195, 0.5, -30)
logo.BackgroundColor3 = GALAXY_DARK
logo.Text = "D"
logo.TextColor3 = WHITE
logo.TextSize = 30
logo.Font = Enum.Font.GothamBold
logo.BorderSizePixel = 0
logo.AutoButtonColor = false
logo.ZIndex = 10
logo.Parent = gui

local logoGradient = Instance.new("UIGradient")
logoGradient.Color = Colored:Connect(function(input)
		if input.UserInputType == Enum.UserInputType.MouseButton1 then
			if moveConnection then
				moveConnection:Disconnect()
			end
		end
	end)
end)

--==================================================
-- ESP TAB
--==================================================

local espContent = Instance.new("Frame")
espContent.Size = UDim2.new(1, 0, 1, 0)
espContent.BackgroundTransparency = 1
espContent.Visible = false
espContent.Parent = content

local espButton = Instance.new("TextButton")
espButton.Size = UDim2.new(1, 0, 0, 32)
espButton.Position = UDim2.fromOffset(0, 0)
espButton.Text = "ESP: OFF"
espButton.TextColor3 = Color3.new(1, 1, 1)
espButton.TextSize = 13
espButton.Font = Enum.Font.Gotham
espButton.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
espButton.BorderSizePixel = 0
espButton.Parent = espContent

Instance.new("UICorner", espButton).CornerRadius = UDim.new(0, 6)

espButton.MouseButton1Click:Connect(function()
	espEnabled = not espEnabled
	espButton.Text = "ESP: " .. (espEnabled and "ON" or "OFF")
end)

local espTeamButton = espButton:Clone()
espTeamButton.Position = UDim2.fromOffset(0, 38)
espTeamButton.Text = "Team Check: ON"
espTeamButton.Parent = espContent

espTeamButton.MouseButton1Click:Connect(function()
	espTeamCheck = not espTeamCheck
	espTeamButton.Text = "Team Check: " .. (espTeamCheck and "ON" or "OFF")
end)

local nameButton = espButton:Clone()
nameButton.Position = UDim2.fromOffset(0, 76)
nameButton.Text = "Name ESP: ON"
nameButton.Parent = espContent

nameButton.MouseButton1Click:Connect(function()
	nameESP = not nameESP
	nameButton.Text = "Name ESP: " .. (nameESP and "ON" or "OFF")
end)

local boxButton = espButton:Clone()
boxButton.Position = UDim2.fromOffset(0, 114)
boxButton.Text = "Box ESP: OFF"
boxButton.Parent = espContent

boxButton.MouseButton1Click:Connect(function()
	boxESP = not boxESP
	boxButton.Text = "Box ESP: " .. (boxESP and "ON" or "OFF")
end)

local distanceButton = espButton:Clone()
distanceButton.Position = UDim2.fromOffset(0, 152)
distanceButton.Text = "Distance ESP: OFF"
distanceButton.Parent = espContent

distanceButton.MouseButton1Click:Connect(function()
	distanceESP = not distanceESP
	distanceButton.Text = "Distance ESP: " .. (distanceESP and "ON" or "OFF")
end)

--==================================================
-- TAB SWITCH
--==================================================

aimTab.MouseButton1Click:Connect(function()
	aimContent.Visible = true
	espContent.Visible = false

	aimTab.BackgroundColor3 = Color3.fromRGB(40, 150, 80)
	espTab.BackgroundColor3 = Color3.fromRGB(70, 70, 70)
end)

espTab.MouseButton1Click:Connect(function()
	aimContent.Visible = false
	espContent.Visible = true

	espTab.BackgroundColor3 = Color3.fromRGB(40, 150, 80)
	aimTab.BackgroundColor3 = Color3.fromRGB(70, 70, 70)
end)

--==================================================
-- AIM FUNCTIONS
--==================================================

local function getCharacter(playerObject)
	return playerObject.Character
end

local function getRoot(character)
	return character and character:FindFirstChild("HumanoidRootPart")
end

local function getHumanoid(character)
	return character and character:FindFirstChildOfClass("Humanoid")
end

local function isAlive(character)
	local humanoid = getHumanoid(character)
	return humanoid and humanoid.Health > 0
end

local function isVisible(part)
	if not wallCheck then
		return true
	end

	local origin = camera.CFrame.Position
	local direction = part.Position - origin

	local params = RaycastParams.new()
	params.FilterType = Enum.RaycastFilterType.Exclude
	params.FilterDescendantsInstances = {
		player.Character
	}

	local result = workspace:Raycast(origin, direction, params)

	if not result then
		return true
	end

	return result.Instance:IsDescendantOf(part.Parent)
end

local function getClosestTarget()
	local closest = nil
	local closestDistance = math.huge

	local mousePosition = UserInputService:GetMouseLocation()
	local center = Vector2.new(mousePosition.X, mousePosition.Y)

	for _, otherPlayer in ipairs(Players:GetPlayers()) do
		if otherPlayer ~= player then

			local character = getCharacter(otherPlayer)
			local humanoid = getHumanoid(character)
			local root = getRoot(character)

			if character and humanoid and root and humanoid.Health > 0 then

				if teamCheck and otherPlayer.Team == player.Team then
					continue
				end

				local screenPosition, onScreen =
					camera:WorldToViewportPoint(root.Position)

				if onScreen then

					local screenPoint =
						Vector2.new(screenPosition.X, screenPosition.Y)

					local distance =
						(screenPoint - center).Magnitude

					if (not fovCheck or distance <= FOV_RADIUS)
						and distance < closestDistance
						and isVisible(root) then

						closestDistance = distance
						closest = otherPlayer
					end
				end
			end
		end
	end

	return closest
end

--==================================================
-- AIM INPUT
--==================================================

UserInputService.InputBegan:Connect(function(input, processed)
	if processed then
		return
	end

	if input.UserInputType == Enum.UserInputType.MouseButton2 then
		aiming = true
		target = getClosestTarget()
	end
end)

UserInputService.InputEnded:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton2 then
		aiming = false
		target = nil
	end
end)

--==================================================
-- AIM LOOP
--==================================================

RunService.RenderStepped:Connect(function()

	if aiming and target then

		local character = target.Character
		local humanoid = getHumanoid(character)
		local root = getRoot(character)

		if character and humanoid and root and humanoid.Health > 0 then

			local targetPosition = root.Position

			local desiredCFrame =
				CFrame.lookAt(
					camera.CFrame.Position,
					targetPosition
				)

			camera.CFrame =
				camera.CFrame:Lerp(
					desiredCFrame,
					AIM_SMOOTH
				)

		else
			target = getClosestTarget()
		end
	end
end)

--==================================================
-- ESP
--==================================================

local function removeESP(otherPlayer)

	local objects = espObjects[otherPlayer]

	if objects then
		for _, object in pairs(objects) do
			if object and object.Parent then
				object:Destroy()
			end
		end
	end

	espObjects[otherPlayer] = nil
end

local function createESP(otherPlayer)

	if otherPlayer == player then
		return
	end

	removeESP(otherPlayer)

	local character = otherPlayer.Character

	if not character then
		return
	end

	local humanoid = character:FindFirstChildOfClass("Humanoid")
	local root = character:FindFirstChild("HumanoidRootPart")

	if not humanoid or not root then
		return
	end

	if espTeamCheck and otherPlayer.Team == player.Team then
		return
	end

	local objects = {}

	-- Highlight

	local highlight = Instance.new("Highlight")
	highlight.Name = "DhenESP"
	highlight.FillTransparency = 0.75
	highlight.OutlineTransparency = 0
	highlight.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
	highlight.Parent = character

	table.insert(objects, highlight)

	-- Billboard

	local billboard = Instance.new("BillboardGui")
	billboard.Name = "DhenESPInfo"
	billboard.Size = UDim2.fromOffset(180, 50)
	billboard.StudsOffset = Vector3.new(0, 3, 0)
	billboard.AlwaysOnTop = true
	billboard.Parent = root

	local label = Instance.new("TextLabel")
	label.Size = UDim2.fromScale(1, 1)
	label.BackgroundTransparency = 1
	label.TextColor3 = Color3.new(1, 1, 1)
	label.TextStrokeTransparency = 0
	label.TextSize = 13
	label.Font = Enum.Font.GothamBold
	label.Parent = billboard

	table.insert(objects, billboard)

	espObjects[otherPlayer] = objects

	RunService.RenderStepped:Connect(function()
		if not espEnabled then
			highlight.Enabled = false
			billboard.Enabled = false
			return
		end

		if not character.Parent or humanoid.Health <= 0 then
			removeESP(otherPlayer)
			return
		end

		if espTeamCheck and otherPlayer.Team == player.Team then
			highlight.Enabled = false
			billboard.Enabled = false
			return
		end

		highlight.Enabled = boxESP

		billboard.Enabled =
			nameESP or distanceESP

		local text = ""

		if nameESP then
			text = otherPlayer.DisplayName
		end

		if distanceESP then
			local myCharacter = player.Character
			local myRoot =
				myCharacter and myCharacter:FindFirstChild("HumanoidRootPart")

			if myRoot then
				local distance =
					math.floor(
						(root.Position - myRoot.Position).Magnitude
					)

				if text ~= "" then
					text = text .. " | "
				end

				text = text .. distance .. "m"
			end
		end

		label.Text = text
	end)
end

--==================================================
-- ESP UPDATE
--==================================================

Players.PlayerAdded:Connect(function(otherPlayer)

	otherPlayer.CharacterAdded:Connect(function()
		task.wait(1)

		if espEnabled then
			createESP(otherPlayer)
		end
	end)

end)

Players.PlayerRemoving:Connect(function(otherPlayer)
	removeESP(otherPlayer)
end)

RunService.RenderStepped:Connect(function()

	if not espEnabled then
		for _, objects in pairs(espObjects) do
			for _, object in pairs(objects) do
				if object then
					object.Enabled = false
				end
			end
		end

		return
	end

	for _, otherPlayer in ipairs(Players:GetPlayers()) do

		if otherPlayer ~= player then

			if not espObjects[otherPlayer] then
				createESP(otherPlayer)
			end

		end
	end
end)

--==================================================
-- INITIAL ESP
--==================================================

for _, otherPlayer in ipairs(Players:GetPlayers()) do

	if otherPlayer ~= player then

		if otherPlayer.Character then
			createESP(otherPlayer)
		end

		otherPlayer.CharacterAdded:Connect(function()
			task.wait(1)

			if espEnabled then
				createESP(otherPlayer)
			end
		end)
	end
end

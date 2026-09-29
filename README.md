-- DHEN AIM LOCK + ESP
-- LocalScript
-- StarterPlayer > StarterPlayerScripts

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")

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
-- MAIN PANEL
--==================================================

local panel = Instance.new("Frame")
panel.Name = "MainPanel"
panel.Size = UDim2.fromOffset(230, 360)
panel.Position = UDim2.new(0.5, -115, 0.5, -180)
panel.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
panel.BorderSizePixel = 0
panel.Parent = gui

local panelCorner = Instance.new("UICorner")
panelCorner.CornerRadius = UDim.new(0, 10)
panelCorner.Parent = panel

--==================================================
-- LOGO
--==================================================

local logo = Instance.new("TextButton")
logo.Name = "DhenLogo"
logo.Size = UDim2.fromOffset(60, 60)
logo.Position = UDim2.new(0.5, -185, 0.5, -30)
logo.BackgroundColor3 = Color3.fromRGB(30, 30, 30)
logo.Text = "D"
logo.TextColor3 = Color3.new(1, 1, 1)
logo.TextSize = 30
logo.Font = Enum.Font.GothamBold
logo.BorderSizePixel = 0
logo.AutoButtonColor = false
logo.Parent = gui

local logoCorner = Instance.new("UICorner")
logoCorner.CornerRadius = UDim.new(1, 0)
logoCorner.Parent = logo

logo.MouseButton1Click:Connect(function()
	panel.Visible = not panel.Visible
end)

--==================================================
-- DRAG
--==================================================

local dragging = false
local dragStart
local startPos

panel.InputBegan:Connect(function(input)
	if input.UserInputType == Enum.UserInputType.MouseButton1 then
		dragging = true
		dragStart = input.Position
		startPos = panel.Position

		input.Changed:Connect(function()
			if input.UserInputState == Enum.UserInputState.End then
				dragging = false
			end
		end)
	end
end)

UserInputService.InputChanged:Connect(function(input)
	if dragging and input.UserInputType == Enum.UserInputType.MouseMovement then
		local delta = input.Position - dragStart

		panel.Position = UDim2.new(
			startPos.X.Scale,
			startPos.X.Offset + delta.X,
			startPos.Y.Scale,
			startPos.Y.Offset + delta.Y
		)

		logo.Position = UDim2.new(
			panel.Position.X.Scale,
			panel.Position.X.Offset - 70,
			panel.Position.Y.Scale,
			panel.Position.Y.Offset + 150
		)
	end
end)

--==================================================
-- TITLE
--==================================================

local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, 0, 0, 35)
title.BackgroundTransparency = 1
title.Text = "DHEN"
title.TextColor3 = Color3.new(1, 1, 1)
title.TextSize = 17
title.Font = Enum.Font.GothamBold
title.Parent = panel

--==================================================
-- TABS
--==================================================

local aimTab = Instance.new("TextButton")
aimTab.Size = UDim2.fromOffset(105, 32)
aimTab.Position = UDim2.fromOffset(10, 35)
aimTab.Text = "AIMBOT"
aimTab.TextSize = 13
aimTab.Font = Enum.Font.GothamBold
aimTab.TextColor3 = Color3.new(1, 1, 1)
aimTab.BackgroundColor3 = Color3.fromRGB(40, 150, 80)
aimTab.BorderSizePixel = 0
aimTab.Parent = panel

local aimTabCorner = Instance.new("UICorner")
aimTabCorner.CornerRadius = UDim.new(0, 6)
aimTabCorner.Parent = aimTab

local espTab = Instance.new("TextButton")
espTab.Size = UDim2.fromOffset(105, 32)
espTab.Position = UDim2.fromOffset(120, 35)
espTab.Text = "ESP"
espTab.TextSize = 13
espTab.Font = Enum.Font.GothamBold
espTab.TextColor3 = Color3.new(1, 1, 1)
espTab.BackgroundColor3 = Color3.fromRGB(70, 70, 70)
espTab.BorderSizePixel = 0
espTab.Parent = panel

local espTabCorner = Instance.new("UICorner")
espTabCorner.CornerRadius = UDim.new(0, 6)
espTabCorner.Parent = espTab

--==================================================
-- CONTENT
--==================================================

local content = Instance.new("Frame")
content.Size = UDim2.new(1, -20, 1, -80)
content.Position = UDim2.fromOffset(10, 75)
content.BackgroundTransparency = 1
content.Parent = panel

--==================================================
-- AIM TAB
--==================================================

local aimContent = Instance.new("Frame")
aimContent.Size = UDim2.new(1, 0, 1, 0)
aimContent.BackgroundTransparency = 1
aimContent.Parent = content

-- Team Check

local teamButton = Instance.new("TextButton")
teamButton.Size = UDim2.new(1, 0, 0, 32)
teamButton.Position = UDim2.fromOffset(0, 0)
teamButton.Text = "Team Check: ON"
teamButton.TextColor3 = Color3.new(1, 1, 1)
teamButton.TextSize = 13
teamButton.Font = Enum.Font.Gotham
teamButton.BackgroundColor3 = Color3.fromRGB(45, 45, 45)
teamButton.BorderSizePixel = 0
teamButton.Parent = aimContent

Instance.new("UICorner", teamButton).CornerRadius = UDim.new(0, 6)

teamButton.MouseButton1Click:Connect(function()
	teamCheck = not teamCheck
	teamButton.Text = "Team Check: " .. (teamCheck and "ON" or "OFF")
end)

-- Wall Check

local wallButton = teamButton:Clone()
wallButton.Position = UDim2.fromOffset(0, 38)
wallButton.Text = "Wall Check: ON"
wallButton.Parent = aimContent

wallButton.MouseButton1Click:Connect(function()
	wallCheck = not wallCheck
	wallButton.Text = "Wall Check: " .. (wallCheck and "ON" or "OFF")
end)

-- FOV Check

local fovButton = teamButton:Clone()
fovButton.Position = UDim2.fromOffset(0, 76)
fovButton.Text = "FOV Check: ON"
fovButton.Parent = aimContent

fovButton.MouseButton1Click:Connect(function()
	fovCheck = not fovCheck
	fovButton.Text = "FOV Check: " .. (fovCheck and "ON" or "OFF")
end)

-- FOV Label

local fovLabel = Instance.new("TextLabel")
fovLabel.Size = UDim2.new(1, 0, 0, 25)
fovLabel.Position = UDim2.fromOffset(0, 120)
fovLabel.BackgroundTransparency = 1
fovLabel.Text = "FOV: 180"
fovLabel.TextColor3 = Color3.new(1, 1, 1)
fovLabel.TextSize = 13
fovLabel.Font = Enum.Font.Gotham
fovLabel.Parent = aimContent

-- FOV Slider

local fovSlider = Instance.new("TextButton")
fovSlider.Size = UDim2.new(1, 0, 0, 8)
fovSlider.Position = UDim2.fromOffset(0, 148)
fovSlider.Text = ""
fovSlider.AutoButtonColor = false
fovSlider.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
fovSlider.BorderSizePixel = 0
fovSlider.Parent = aimContent

Instance.new("UICorner", fovSlider).CornerRadius = UDim.new(1, 0)

local fovFill = Instance.new("Frame")
fovFill.Size = UDim2.new((FOV_RADIUS - 50) / 450, 0, 1, 0)
fovFill.BackgroundColor3 = Color3.fromRGB(40, 150, 80)
fovFill.BorderSizePixel = 0
fovFill.Parent = fovSlider

Instance.new("UICorner", fovFill).CornerRadius = UDim.new(1, 0)

local function updateFOV(input)
	local x = math.clamp(
		input.Position.X - fovSlider.AbsolutePosition.X,
		0,
		fovSlider.AbsoluteSize.X
	)

	local percent = x / fovSlider.AbsoluteSize.X

	FOV_RADIUS = math.floor(50 + percent * 450)

	fovLabel.Text = "FOV: " .. FOV_RADIUS
	fovFill.Size = UDim2.new(percent, 0, 1, 0)
end

fovSlider.MouseButton1Down:Connect(function()
	local moveConnection

	moveConnection = UserInputService.InputChanged:Connect(function(input)
		if input.UserInputType == Enum.UserInputType.MouseMovement then
			updateFOV(input)
		end
	end)

	UserInputService.InputEnded:Connect(function(input)
		if input.UserInputType == Enum.UserInputType.MouseButton1 then
			if moveConnection then
				moveConnection:Disconnect()
			end
		end
	end)
end)

-- Smoothness Label

local smoothLabel = Instance.new("TextLabel")
smoothLabel.Size = UDim2.new(1, 0, 0, 25)
smoothLabel.Position = UDim2.fromOffset(0, 180)
smoothLabel.BackgroundTransparency = 1
smoothLabel.Text = "Smoothness: 0.20"
smoothLabel.TextColor3 = Color3.new(1, 1, 1)
smoothLabel.TextSize = 13
smoothLabel.Font = Enum.Font.Gotham
smoothLabel.Parent = aimContent

-- Smoothness Slider

local smoothSlider = Instance.new("TextButton")
smoothSlider.Size = UDim2.new(1, 0, 0, 8)
smoothSlider.Position = UDim2.fromOffset(0, 208)
smoothSlider.Text = ""
smoothSlider.AutoButtonColor = false
smoothSlider.BackgroundColor3 = Color3.fromRGB(60, 60, 60)
smoothSlider.BorderSizePixel = 0
smoothSlider.Parent = aimContent

Instance.new("UICorner", smoothSlider).CornerRadius = UDim.new(1, 0)

local smoothFill = Instance.new("Frame")
smoothFill.Size = UDim2.new((AIM_SMOOTH - 0.05) / 0.95, 0, 1, 0)
smoothFill.BackgroundColor3 = Color3.fromRGB(40, 150, 80)
smoothFill.BorderSizePixel = 0
smoothFill.Parent = smoothSlider

Instance.new("UICorner", smoothFill).CornerRadius = UDim.new(1, 0)

local function updateSmooth(input)
	local x = math.clamp(
		input.Position.X - smoothSlider.AbsolutePosition.X,
		0,
		smoothSlider.AbsoluteSize.X
	)

	local percent = x / smoothSlider.AbsoluteSize.X

	AIM_SMOOTH = math.floor((0.05 + percent * 0.95) * 100) / 100

	smoothLabel.Text = string.format("Smoothness: %.2f", AIM_SMOOTH)
	smoothFill.Size = UDim2.new(percent, 0, 1, 0)
end

smoothSlider.MouseButton1Down:Connect(function()
	local moveConnection

	moveConnection = UserInputService.InputChanged:Connect(function(input)
		if input.UserInputType == Enum.UserInputType.MouseMovement then
			updateSmooth(input)
		end
	end)

	UserInputService.InputEnded:Connect(function(input)
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

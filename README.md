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
panel.Size = UDim2.fromOffset(230, 320)

-- CENTERED ON EXECUTE
panel.Position = UDim2.new(
	0.5, -115,
	0.5, -160
)

panel.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
panel.BorderSizePixel = 0
panel.Parent = gui

local panelCorner = Instance.new("UICorner")
panelCorner.CornerRadius = UDim.new(0, 10)
panelCorner.Parent = panel

--==================================================
-- D LOGO
--==================================================

local logo = Instance.new("TextButton")
logo.Name = "DhenLogo"
logo.Size = UDim2.fromOffset(60, 60)

-- CENTERED BESIDE PANEL
logo.Position = UDim2.new(
	0.5, -185,
	0.5, -30
)

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
-- TAB BUTTONS
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

local aimContent = Instance.new("Frame")
aimContent.Size = UDim2.new(1, -20, 1, -75)
aimContent.Position = UDim2.fromOffset(10, 72)
aimContent.BackgroundTransparency = 1
aimContent.Parent = panel

local espContent = Instance.new("Frame")
espContent.Size = UDim2.new(1, -20, 1, -75)
espContent.Position = UDim2.fromOffset(10, 72)
espContent.BackgroundTransparency = 1
espContent.Visible = false
espContent.Parent = panel

--==================================================
-- TOGGLE FUNCTION
--==================================================

local function makeToggle(parent, name, y, default, callback)

	local button = Instance.new("TextButton")

	button.Size = UDim2.new(1, 0, 0, 32)
	button.Position = UDim2.fromOffset(0, y)
	button.BorderSizePixel = 0
	button.Font = Enum.Font.GothamBold
	button.TextSize = 13
	button.TextColor3 = Color3.new(1, 1, 1)
	button.Parent = parent

	local enabled = default

	local function update()

		button.Text =
			name .. ": " ..
			(enabled and "ON" or "OFF")

		button.BackgroundColor3 =
			enabled
			and Color3.fromRGB(40, 150, 80)
			or Color3.fromRGB(70, 70, 70)

	end

	button.Activated:Connect(function()

		enabled = not enabled

		callback(enabled)

		update()

	end)

	update()

	return button
end

--==================================================
-- AIMBOT SETTINGS
--==================================================

makeToggle(
	aimContent,
	"TEAM CHECK",
	0,
	true,
	function(value)
		teamCheck = value
	end
)

makeToggle(
	aimContent,
	"WALL CHECK",
	38,
	true,
	function(value)
		wallCheck = value
	end
)

makeToggle(
	aimContent,
	"FOV CHECK",
	76,
	true,
	function(value)
		fovCheck = value
	end
)

--==================================================
-- FOV LABEL
--==================================================

local fovLabel = Instance.new("TextLabel")
fovLabel.Size = UDim2.new(1, 0, 0, 25)
fovLabel.Position = UDim2.fromOffset(0, 115)
fovLabel.BackgroundTransparency = 1
fovLabel.Text = "FOV SIZE: " .. FOV_RADIUS
fovLabel.TextColor3 = Color3.new(1, 1, 1)
fovLabel.TextSize = 13
fovLabel.Font = Enum.Font.GothamBold
fovLabel.TextXAlignment = Enum.TextXAlignment.Left
fovLabel.Parent = aimContent

--==================================================
-- FOV SLIDER
--==================================================

local fovSlider = Instance.new("TextButton")
fovSlider.Size = UDim2.new(1, 0, 0, 28)
fovSlider.Position = UDim2.fromOffset(0, 143)
fovSlider.BackgroundColor3 = Color3.fromRGB(70, 70, 

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer

-- Circle radius and rotation speed
local radius = 10
local speed = 2

local connection
connection = RunService.RenderStepped:Connect(function()
	local character = LocalPlayer.Character
	if not character or not character:FindFirstChild("HumanoidRootPart") then return end
	
	local rootPart = character.HumanoidRootPart
	local playersList = Players:GetPlayers()
	local totalPlayers = #playersList - 1 -- Exclude local player
	
	if totalPlayers <= 0 then return end
	
	local index = 0
	for _, player in ipairs(playersList) do
		if player ~= LocalPlayer then
			local targetChar = player.Character
			if targetChar and targetChar:FindFirstChild("HumanoidRootPart") then
				index = index + 1
				local angle = (index / totalPlayers) * (math.pi * 2) + (tick() * speed)
				
				-- Calculate the circular position around you
				local offsetX = math.cos(angle) * radius
				local offsetZ = math.sin(angle) * radius
				local targetPos = rootPart.Position + Vector3.new(offsetX, 3, offsetZ)
				
				-- Move other players to the rotation position
				targetChar.HumanoidRootPart.CFrame = CFrame.new(targetPos, rootPart.Position)
			end
		end
	end
end)

-- Disconnect connection when character removes/dies
LocalPlayer.CharacterRemoving:Connect(function()
	if connection then
		connection:Disconnect()
	end
end)

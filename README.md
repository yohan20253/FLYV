-- Script GUI para voo (Fly) no Roblox
-- Deve estar dentro de um ScreenGui no StarterGui

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")

local player = Players.LocalPlayer
local character = player.Character or player.CharacterAdded:Wait()
local humanoidRootPart = character:WaitForChild("HumanoidRootPart")

local flyButton = script.Parent:WaitForChild("FlyButton")
local flying = false
local speed = 50

local bodyGyro
local bodyVelocity

-- Função para iniciar o voo
local function startFlying()
	bodyGyro = Instance.new("BodyGyro")
	bodyGyro.P = 9e4
	bodyGyro.maxTorque = Vector3.new(9e9, 9e9, 9e9)
	bodyGyro.CFrame = humanoidRootPart.CFrame
	bodyGyro.Parent = humanoidRootPart

	bodyVelocity = Instance.new("BodyVelocity")
	bodyVelocity.Velocity = Vector3.new(0, 0, 0)
	bodyVelocity.MaxForce = Vector3.new(9e9, 9e9, 9e9)
	bodyVelocity.Parent = humanoidRootPart

	flying = true
	flyButton.Text = "Desativar Voo"
end

-- Função para parar o voo
local function stopFlying()
	if bodyGyro then bodyGyro:Destroy() end
	if bodyVelocity then bodyVelocity:Destroy() end
	flying = false
	flyButton.Text = "Ativar Voo"
end

-- Alternar voo quando botão for clicado
flyButton.MouseButton1Click:Connect(function()
	if flying then
		stopFlying()
	else
		startFlying()
	end
end)

-- Movimento durante o voo
RunService.RenderStepped:Connect(function()
	if flying and character and humanoidRootPart then
		local cam = workspace.CurrentCamera
		local moveDirection = Vector3.new()

		local keys = {
			W = Enum.KeyCode.W,
			S = Enum.KeyCode.S,
			A = Enum.KeyCode.A,
			D = Enum.KeyCode.D,
			Space = Enum.KeyCode.Space,
			Shift = Enum.KeyCode.LeftShift
		}

		local UIS = game:GetService("UserInputService")

		if UIS:IsKeyDown(keys.W) then
			moveDirection += cam.CFrame.LookVector
		end
		if UIS:IsKeyDown(keys.S) then
			moveDirection -= cam.CFrame.LookVector
		end
		if UIS:IsKeyDown(keys.A) then
			moveDirection -= cam.CFrame.RightVector
		end
		if UIS:IsKeyDown(keys.D) then
			moveDirection += cam.CFrame.RightVector
		end
		if UIS:IsKeyDown(keys.Space) then
			moveDirection += Vector3.new(0, 1, 0)
		end
		if UIS:IsKeyDown(keys.Shift) then
			moveDirection -= Vector3.new(0, 1, 0)
		end

		if moveDirection.Magnitude > 0 then
			bodyVelocity.Velocity = moveDirection.Unit * speed
		else
			bodyVelocity.Velocity = Vector3.zero
		end

		bodyGyro.CFrame = cam.CFrame
	end
end)

local cam = {}

--- Services ---

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local UserGameSettings = UserSettings():GetService("UserGameSettings")
local TweenService = game:GetService("TweenService")

--- Constants ---

local Player = Players.LocalPlayer
local CurrentCamera = workspace.CurrentCamera
local DefaultFieldOfView = 70
local CameraTweenInfo = TweenInfo.new(0.1, Enum.EasingStyle.Quad, Enum.EasingDirection.InOut) --Feel free to edit your desired tween style or adding more styles as you like
local MouseSensitivity = UserInputService.MouseDeltaSensitivity
local rad = math.rad
local Rot = CFrame.new()

local function GetRollAngle()

	return -game.Workspace.Camera.CFrame.RightVector:Dot(Player.Character.Humanoid.MoveDirection)
end

--- States ---

local isAligned = false --To define the state when the character facing forward as the camera angle
local isSteppedIn = false --To define the state when moveing camera without right-clicking
local isTilt = false --To define the state when camera will tilt in movement direction

--- Functions ---

function cam.MouseIcon(Status) --To toggle mouse icon

	UserInputService.MouseIconEnabled = Status

end

function cam.MouseLock(Status) --To toggle moving-camera-without-right-clicking mode as well as locking mouse at center of screen

	isSteppedIn = Status

end

function cam.Alignment(Status) --To toggle character alignment to the camera

	local Character = Player.Character
	local Humanoid = Character:WaitForChild("Humanoid")

	Humanoid.AutoRotate = not Status
	isAligned = Status

end

function cam.Tilt(Status)
	
	if Status == true then
		
		RunService:BindToRenderStep("RotateCameraInDirectionPlayerIsGoing", Enum.RenderPriority.Camera.Value + 1, function()
			local Roll = GetRollAngle()
			Rot = Rot:Lerp(CFrame.Angles(0, 0, rad(Roll * 1.35)),0.075)

			game.Workspace.Camera.CFrame *= Rot
		end)
		
	elseif Status == false then
		RunService:UnbindFromRenderStep("RotateCameraInDirectionPlayerIsGoing")
	end
	
end

function cam.RunStart()
	
	local camera = game.Workspace.Camera
	local character = Player.Character
	
	
	local stop = false
	RunService:BindToRenderStep("runBind", 201, function(deltaTime)
		local deltaRate = 60 * deltaTime
		camera.FieldOfView = camera.FieldOfView + deltaRate
		if camera.FieldOfView > 79 then
			stop = true
		end
		if stop == true then
			RunService:UnbindFromRenderStep("runBind")
			if camera.FieldOfView > 80 then
				camera.FieldOfView = 81
			end
		end

	end)
end

function cam.RunEnd()

	local camera = game.Workspace.Camera
	local character = Player.Character

	
	local stop = false
	RunService:BindToRenderStep("runBindend", 201, function(deltaTime)
		local deltaRate = deltaTime * 60
		camera.FieldOfView = camera.FieldOfView - deltaRate
		if camera.FieldOfView < 71 then
			stop = true
		end
		if stop == true then
			RunService:UnbindFromRenderStep("runBindend")
			if camera.FieldOfView < 70 then
				camera.FieldOfView = 70
			end
		end
		
	end)
		
end


--- RaycastParams ---

local Params = RaycastParams.new()
Params.FilterType = Enum.RaycastFilterType.Exclude

--- RenderStep ---

RunService.RenderStepped:Connect(function()

	if isSteppedIn == true then --To achieve moving-camera-without-right-clicking mode 			
		UserInputService.MouseBehavior = Enum.MouseBehavior.LockCenter			
	end

	if isAligned == true then --To achieve character alignment to the camera & camera offset obstruction detection

		local Character = Player.Character
		local HumanoidRootPart = Character:WaitForChild("HumanoidRootPart")
		local Head = Character:WaitForChild("Head")

		Params.FilterDescendantsInstances = {Character} --Add the instances that you would like to ignore detection

		local Result = workspace:Raycast(Head.Position, CurrentCamera.CFrame.Position - Head.Position, Params)

		if Result ~= nil then

			---Credit to Arbeiters for below collision calculation method
			local ObstructionDisplacement = (Result.Position - Head.Position)
			local ObstructionPosition = Head.Position + (ObstructionDisplacement.Unit * (ObstructionDisplacement.Magnitude - 1))
			local x,y,z,r00,r01,r02,r10,r11,r12,r20,r21,r22 = CurrentCamera.CFrame:components()

			CurrentCamera.CFrame = CFrame.new(ObstructionPosition.x, ObstructionPosition.y, ObstructionPosition.z , r00, r01, r02, r10, r11, r12, r20, r21, r22)

		end

		local rx, ry, rz = CurrentCamera.CFrame:ToOrientation()
		HumanoidRootPart.CFrame = CFrame.new(HumanoidRootPart.CFrame.Position) * CFrame.fromOrientation(0, ry, 0)

	end

end)

return cam

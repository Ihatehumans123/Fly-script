local uis = game:GetService("UserInputService") --- will need animations to work(idk)?

local plr = game.Players.LocalPlayer
local isflying = false
local isjumping = false
local char = plr.Character
local flying
local idling
local yaxis = 1
local holdingspace = false
local holdingalt = false
local bodyposition

char.Humanoid.StateChanged:Connect(function(old,new)
	if new == Enum.HumanoidStateType.Jumping or new == Enum.HumanoidStateType.Freefall then
		isjumping = true
		
	elseif new == Enum.HumanoidStateType.Landed then
		isjumping = false
		isflying = false
		yaxis = 0
		char.Humanoid.WalkSpeed = 16
		if bodyposition then
			bodyposition:Destroy()
			bodyposition = nil
		end
	if flying then
			flying:Stop()
			flying = nil
	end
	if idling then
		idling:Stop()
		idling = nil
	end
	
	end
end)
uis.InputBegan:Connect(function(i,g)
	if g then return end
	if isjumping then
	if i.KeyCode == Enum.KeyCode.Space then
		
			if not bodyposition then
				bodyposition = Instance.new("BodyPosition",char.HumanoidRootPart)
				bodyposition.MaxForce = Vector3.new(0,9999,0)
				bodyposition.P = 5000
			end
			
			isflying = true
			if not idling then
				idling = char.Humanoid:LoadAnimation(script.Hover)
				idling:Play()
			end
			holdingspace = true
			repeat 
				task.wait()
				yaxis += 1
				bodyposition.Position = Vector3.new(0,yaxis,0)
				
			until holdingspace == false or isflying == false
		
	end
	end
end)


uis.InputEnded:Connect(function(i,g)
	if g then return end
	if i.KeyCode == Enum.KeyCode.Space then
		if holdingspace then
		holdingspace = false
		end
	end
	
end)

uis.InputBegan:Connect(function(i,g)
	if g then return end
	if isflying then
	if i.KeyCode == Enum.KeyCode.LeftAlt then
		holdingalt = true
		repeat 
			task.wait()
				yaxis -= 1
				bodyposition.Position = Vector3.new(0,yaxis,0)
		until holdingalt == false or yaxis <= 0
		if yaxis <= 0 then
			bodyposition:Destroy()
			bodyposition = nil
			yaxis = 1
		end
		
	end
	end
end)

uis.InputEnded:Connect(function(i,g)
	if g then return end
	if i.KeyCode == Enum.KeyCode.LeftAlt then
		holdingalt = false
		
	end
end)


char.Humanoid:GetPropertyChangedSignal("MoveDirection"):Connect(function()
	if isflying then
	local movedirection = char.Humanoid.MoveDirection
	if movedirection.Magnitude == 0 then
		
		if flying then
			flying:Stop()
			flying = nil
		end
		if not idling then
			idling = char.Humanoid:LoadAnimation(script.Hover)
			idling:Play()
		end
	else
		if idling then
			idling:Stop()
			idling = nil
			
		end
		if not flying then
			flying = char.Humanoid:LoadAnimation(script.fly)
			flying:Play()
		end
		char.Humanoid.WalkSpeed = 40
	end
	end
end)

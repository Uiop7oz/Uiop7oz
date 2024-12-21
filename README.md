local OrionLib = loadstring(game:HttpGet(('https://raw.githubusercontent.com/shlexware/Orion/main/source')))()

local Window = OrionLib:MakeWindow({Name = "V Hub - NM7", HidePremium = false, SaveConfig = true, IntroText = "V  hub- NM7 loading :D", ConfigFolder = "V HUB - NM7 "})

local Tab = Window:MakeTab({
	Name = "الصفحة الرئيسية ",
	Icon = "rbxassetid://4483345998",
	PremiumOnly = false
})

Tab:AddButton({
	Name = "قتل الكل",
	Callback = function()
		local player = game.Players.LocalPlayer
		local replicatedStorage = game:GetService("ReplicatedStorage")
		
		local attackRange = 10
		local killedPlayers = {}
		
		while wait(0.000000000000000000000001) do
			for _, target in pairs(game.Players:GetPlayers()) do
				if target ~= player and (target.Team and (target.Team.Name == "Inmates" or target.Team.Name == "Criminals" or target.Team.Name == "Guards")) then
					if target.Character and target.Character:FindFirstChild("HumanoidRootPart") then
						local distance = (player.Character.HumanoidRootPart.Position - target.Character.HumanoidRootPart.Position).Magnitude
						if distance <= attackRange then
							replicatedStorage.meleeEvent:FireServer(target)
							killedPlayers[target.Name] = true
						end
					end
				end
			end
		end		
  	end    
})

Tab:AddSlider({
	Name = "حدد سرعتك",
	Min = 16,
	Max = 100,
	Default = 5,
	Color = Color3.fromRGB(255,255,255),
	Increment = 1,
	ValueName = "سرعه",
	Callback = function(Value)
		game.Players.LocalPlayer.Character.Humanoid.WalkSpeed = Value
	end    
})

Tab:AddSlider({
	Name = "قفز عالي",
	Min = 50,
	Max = 100,
	Default = 5,
	Color = Color3.fromRGB(255,255,255),
	Increment = 1,
	ValueName = "JP",
	Callback = function(Value)
		game.Players.LocalPlayer.Character.Humanoid.JumpPower = Value
	end    
})

Tab:AddButton({
	Name = "كشف ",
	Callback = function()
		_G.FriendColor = Color3.fromRGB(0, 0, 255)
		_G.EnemyColor = Color3.fromRGB(255, 0, 0)
		_G.UseTeamColor = true
		
		--------------------------------------------------------------------
		local Holder = Instance.new("Folder", game.CoreGui)
		Holder.Name = "كشف"
		
		local Box = Instance.new("BoxHandleAdornment")
		Box.Name = "nilBox"
		Box.Size = Vector3.new(1, 2, 1)
		Box.Color3 = Color3.new(100 / 255, 100 / 255, 100 / 255)
		Box.Transparency = 0.7
		Box.ZIndex = 0
		Box.AlwaysOnTop = false
		Box.Visible = false
		
		local NameTag = Instance.new("BillboardGui")
		NameTag.Name = "nilNameTag"
		NameTag.Enabled = false
		NameTag.Size = UDim2.new(0, 200, 0, 50)
		NameTag.AlwaysOnTop = true
		NameTag.StudsOffset = Vector3.new(0, 1.8, 0)
		local Tag = Instance.new("TextLabel", NameTag)
		Tag.Name = "Tag"
		Tag.BackgroundTransparency = 1
		Tag.Position = UDim2.new(0, -50, 0, 0)
		Tag.Size = UDim2.new(0, 300, 0, 20)
		Tag.TextSize = 15
		Tag.TextColor3 = Color3.new(100 / 255, 100 / 255, 100 / 255)
		Tag.TextStrokeColor3 = Color3.new(0 / 255, 0 / 255, 0 / 255)
		Tag.TextStrokeTransparency = 0.4
		Tag.Text = "nil"
		Tag.Font = Enum.Font.SourceSansBold
		Tag.TextScaled = false
		
		local LoadCharacter = function(v)
			repeat wait() until v.Character ~= nil
			v.Character:WaitForChild("Humanoid")
			local vHolder = Holder:FindFirstChild(v.Name)
			vHolder:ClearAllChildren()
			local b = Box:Clone()
			b.Name = v.Name .. "Box"
			b.Adornee = v.Character
			b.Parent = vHolder
			local t = NameTag:Clone()
			t.Name = v.Name .. "NameTag"
			t.Enabled = true
			t.Parent = vHolder
			t.Adornee = v.Character:WaitForChild("Head", 5)
			if not t.Adornee then
				return UnloadCharacter(v)
			end
			t.Tag.Text = v.Name
			b.Color3 = Color3.new(v.TeamColor.r, v.TeamColor.g, v.TeamColor.b)
			t.Tag.TextColor3 = Color3.new(v.TeamColor.r, v.TeamColor.g, v.TeamColor.b)
			local Update
			local UpdateNameTag = function()
				if not pcall(function()
					v.Character.Humanoid.DisplayDistanceType = Enum.HumanoidDisplayDistanceType.None
					local maxh = math.floor(v.Character.Humanoid.MaxHealth)
					local h = math.floor(v.Character.Humanoid.Health)
				end) then
					Update:Disconnect()
				end
			end
			UpdateNameTag()
			Update = v.Character.Humanoid.Changed:Connect(UpdateNameTag)
		end
		
		local UnloadCharacter = function(v)
			local vHolder = Holder:FindFirstChild(v.Name)
			if vHolder and (vHolder:FindFirstChild(v.Name .. "Box") ~= nil or vHolder:FindFirstChild(v.Name .. "NameTag") ~= nil) then
				vHolder:ClearAllChildren()
			end
		end
		
		local LoadPlayer = function(v)
			local vHolder = Instance.new("Folder", Holder)
			vHolder.Name = v.Name
			v.CharacterAdded:Connect(function()
				pcall(LoadCharacter, v)
			end)
			v.CharacterRemoving:Connect(function()
				pcall(UnloadCharacter, v)
			end)
			v.Changed:Connect(function(prop)
				if prop == "TeamColor" then
					UnloadCharacter(v)
					wait()
					LoadCharacter(v)
				end
			end)
			LoadCharacter(v)
		end
		
		local UnloadPlayer = function(v)
			UnloadCharacter(v)
			local vHolder = Holder:FindFirstChild(v.Name)
			if vHolder then
				vHolder:Destroy()
			end
		end
		
		for i,v in pairs(game:GetService("Players"):GetPlayers()) do
			spawn(function() pcall(LoadPlayer, v) end)
		end
		
		game:GetService("Players").PlayerAdded:Connect(function(v)
			pcall(LoadPlayer, v)
		end)
		
		game:GetService("Players").PlayerRemoving:Connect(function(v)
			pcall(UnloadPlayer, v)
		end)
		
		game:GetService("Players").LocalPlayer.NameDisplayDistance = 0
		
		if _G.Reantheajfdfjdgs then
			return
		end
		
		_G.Reantheajfdfjdgs = ":suifayhgvsdghfsfkajewfrhk321rk213kjrgkhj432rj34f67df"
		
		local players = game:GetService("Players")
		local plr = players.LocalPlayer
		
		function esp(target, color)
			if target.Character then
				if not target.Character:FindFirstChild("GetReal") then
					local highlight = Instance.new("Highlight")
					highlight.RobloxLocked = true
					highlight.Name = "GetReal"
					highlight.Adornee = target.Character
					highlight.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
					highlight.FillColor = color
					highlight.Parent = target.Character
				else
					target.Character.GetReal.FillColor = color
				end
			end
		end
		
		while task.wait() do
			for i, v in pairs(players:GetPlayers()) do
				if v ~= plr then
					esp(v, _G.UseTeamColor and v.TeamColor.Color or ((plr.TeamColor == v.TeamColor) and _G.FriendColor or _G.EnemyColor))
				end
			end
		end
  	end    
})

Tab:AddButton({
	Name = "عائد لانهائي",
	Callback = function()
		loadstring(game:HttpGet("https://raw.githubusercontent.com/EdgeIY/infiniteyield/master/source"))()
  	end    
})

Tab:AddButton({
	Name = "طيران gui",
	Callback = function()
		loadstring(game:HttpGet("https://raw.githubusercontent.com/XNEOFF/FlyGuiV3/main/FlyGuiV3.txt"))()
  	end    
})

local TpTab = Window:MakeTab({
	Name = "التنقل",
	Icon = "rbxassetid://4483345998",
	PremiumOnly = false
})

TpTab:AddButton({
	Name = "الانتقال الفوري إلى السجن",
	Callback = function()
local player = game.Players.LocalPlayer
local targetPosition = Vector3.new(826, 99, 2358) 

player.Character:MoveTo(targetPosition)
  	end    
})

TpTab:AddButton({
	Name = "الانتقال الفوري إلى الفناء",
	Callback = function()
local player = game.Players.LocalPlayer
local targetPosition = Vector3.new(785, 98, 2461) 

player.Character:MoveTo(targetPosition)
  	end    
})

TpTab:AddButton({
	Name = "الانتقال الفوري إلى قاعدة الجريمة",
	Callback = function()
local player = game.Players.LocalPlayer
local targetPosition = Vector3.new(-856, 94, 2059) 

player.Character:MoveTo(targetPosition)
  	end    
})

TpTab:AddButton({
	Name = "الانتقال الفوري إلى غرفة الحراس",
	Callback = function()
local player = game.Players.LocalPlayer
local targetPosition = Vector3.new(836, 99, 2266) 

player.Character:MoveTo(targetPosition)
  	end    
})

local DelTab = Window:MakeTab({
	Name = "Delete",
	Icon = "rbxassetid://4483345998",
	PremiumOnly = false
})

DelTab:AddButton({
	Name = "حذف الأبواب",
	Callback = function()
		local doorsFolder = game:GetService("Workspace"):FindFirstChild("Doors")

		if doorsFolder then
			doorsFolder:Destroy()
		end
  	end    
})

DelTab:AddButton({
	Name = "Delete fences",
	Callback = function()
		local Prison_Fences = game:GetService("Workspace"):FindFirstChild("Prison_Fences")

		if Prison_Fences then
			Prison_Fences:Destroy()
		end
  	end    
})

local MiscTab = Window:MakeTab({
Name = "أعدادات ",
Icon = "rbxassetid://4483345998",
PremiumOnly = false
})

MiscTab:AddButton({
	Name = "تدمير واجهة المستخدم الرسومية",
	Callback = function()
		OrionLib:Destroy()
  	end    
})

<!---
Uiop7oz/Uiop7oz is a ✨ special ✨ repository because its `README.md` (this file) appears on your GitHub profile.
You can click the Preview link to take a look at your changes.
--->

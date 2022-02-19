--[[
WWW.RBLXSCRIPTS.NET
]]
local Library = require(game.ReplicatedStorage:WaitForChild("Framework"):WaitForChild("Library"))
local tt = os.clock()
local p = require(game:GetService("ReplicatedStorage").Framework.Modules.Client["5 | PetUI"])

local stars = {
	Rare = 1,
	Epic = 2,
	Legendary = 2,
	Mythical = 1,
	Exclusive = 1
}
function getidx(s)
	local idx = 0
	local inv = game:GetService("Players").LocalPlayer.PlayerGui.Inventory
	local pets = inv.Frame.Main.Pets
	for i, v in pairs(pets:GetChildren()) do
		if v:IsA("TextButton") and v.Name ~= "Pet" and v.Name ~= "Empty" then
			local petstuff, player = Library.PetCmds.Get(v.Name)
			if v.Equipped.Visible == true or petstuff.s > s then
				idx = idx + 1
			end
		end
	end
	return idx
end
function geteidx(s)
	local inv = game:GetService("Players").LocalPlayer.PlayerGui.Inventory
	local pets = inv.Frame.Main.Pets
	local idx = 0
	for i, v in pairs(pets:GetChildren()) do
		if v:IsA("TextButton") and v.Name ~= "Pet" and v.Name ~= "Empty" then
			local petstuff, player = Library.PetCmds.Get(v.Name)
			if v.Equipped.Visible == true then
				if petstuff.s >= s then
					idx = idx + 1    
				end
			end
		end
	end
	return idx
end
function getIdbyPet(pet)
	local s = nil
	for i,v in pairs(Library.Directory.Pets) do

		if v.name == pet then
			s = i    
		end
	end
	return s
end
function getequipped()
    local o = 0
    for i,v in pairs(Library.Save.Get().Pets) do
        if v.e then o = o + 1 end    
    end
    return o
end
function add(pet, t,cs)
	local inv = game:GetService("Players").LocalPlayer.PlayerGui.Inventory
	local col = Color3.fromRGB(0, 0, 0)

	if inv.Frame.Main.Pets:FindFirstChild("Empty") then

		local rand = Random.new()
		local p = Library.Directory.Pets[pet] or Library.Directory.Pets["1"]
		local e = Library.Assets.UI.Inventory.Pet:Clone()
		e.Parent = inv.Frame.Main.Pets
		if p.rarity == "Legendary" or p.rarity == "Mythical" or p.rarity == "Exclusive" then
			local ee = Library.Assets.UI.Raritys:FindFirstChild(p.rarity):Clone()
			ee.Parent = e.RarityGradient
			e.RarityGradient.Visible = true
			e.BackgroundTransparency = 1
			local eee = e.Level:FindFirstChildOfClass("UIStroke")
			eee.Color = Color3.new(1, 1, 1)
			local n = ee:Clone()
			n.Parent = eee
		else
			e.Level:FindFirstChildOfClass("UIStroke").Thickness = 4
		end
		if stars[p.rarity] then
			local sg = Library.Assets.UI.Inventory.StarGradient
			for i = 1, stars[p.rarity] do
				local s = Library.Assets.UI.Inventory.Star:Clone()
				if p.rarity == "Mythical" or p.rarity == "Exclusive" then
					s.Image = "rbxassetid://7112773308"
				end
				sg:Clone().Parent = s
				s.Parent = e.Stars
			end
			e.Stars.Visible = true
		end
		if p.rarity == "Mythical" or p.rarity == "Exclusive" then
			local function eeeee()
				local sparkle = Library.Assets.UI.Inventory.Sparkle:Clone()
				sparkle.Position = UDim2.new(rand:NextNumber(), 0, rand:NextNumber(), 0)
				sparkle.Size = UDim2.new(0, 0, 0, 0)
				sparkle.Parent = e
				local nextnum = rand:NextNumber(0.2, 0.35)
				Library.Functions.FastTween(
					sparkle,
					{
						Size = UDim2.new(nextnum, 0, nextnum, 0)
					},
					{rand:NextNumber(1.5, 2.75), "Quad", "Out"}
				).Completed:Connect(
					function()
						Library.Functions.FastTween(
							sparkle,
							{
								Size = UDim2.new(0, 0, 0, 0)
							},
							{rand:NextNumber(0.75, 1.25), "Sine", "InOut"}
						).Completed:Wait()
						sparkle:Destroy()
					end
				)
			end
			coroutine.wrap(
				function()
					while e and e.Parent do
						eeeee()
						wait(rand:NextNumber(1, 2.5))
					end
				end
			)()
		end

		local s = math.random(p.strengthMin, p.strengthMax)
		s = tostring(s):gsub("-", "")
		s = tonumber(s)
		if p.rarity == "Exclusive" and Library.Directory.Products[p.name] and pet ~= "157" then
			s = Library.Directory.Products[p.name].PetData.extraData.s
		end

		if t == "Golden" then
			s = s * 3    
		end
		if t == "Rainbow" then
			s = s * 7    
		end
		if t == "Dark Matter" then
			s = s * 20    
		end
		if pet ~= "157" and pet ~= "225" then
			e.Level.Text = Library.Functions.NumberShorten(s)
		else
			e.Level.Text = "???"
		end
		local index = getidx(s)
		if cs then s = cs end
		local im = p.thumbnail
		if t == "Normal" then
			im = p.thumbnail
		elseif t == "Golden" then
			im = p.goldenThumbnail
		elseif t == "Dark Matter" then
			im = p.darkMatterThumbnail
		end
		e.PetIcon.Image = im
		e.MouseEnter:Connect(
			function()
				Library.Audio.Play("rbxassetid://6907626084", script, 1, 0.2)
				e.Size = UDim2.new(0,110,0,110)
				e.BackgroundColor3 = Color3.fromRGB(69, 239, 69)
				e.RarityGradient.Size = UDim2.new(0,110,0,110)
				e.PetIcon.Size = UDim2.new(0,110,0,110)
			end
		)
		e.MouseLeave:Connect(function()
			e.Size = UDim2.new(0,100,0,100)
			e.BackgroundColor3 = Color3.fromRGB(59, 177, 252)
			e.RarityGradient.Size = UDim2.new(0,100,0,100)
			e.PetIcon.Size = UDim2.new(0,100,0,100)
		end)
		e.SelectionGained:Connect(
			function()
				Library.Audio.Play("rbxassetid://6907626084", script, 1, 0.2)
			end
		)
		e.MouseButton1Down:Connect(function()
			if e.Equipped.Visible == true then e.Equipped.Visible = false; e.LayoutOrder = getidx(s) elseif e.Equipped.Visible == false then e.Equipped.Visible = true; e.LayoutOrder = geteidx(s) end            
			e.Size = UDim2.new(0,90,0,90)
			e.RarityGradient.Size = UDim2.new(0,90,0,90)
			e.PetIcon.Size = UDim2.new(0,90,0,90)
		end)
		e.MouseButton1Up:Connect(function()
			e.Size = UDim2.new(0,105,0,105)
			e.RarityGradient.Size = UDim2.new(0,105,0,105)
			e.PetIcon.Size = UDim2.new(0,105,0,105)
			if getequipped() < 20 then
			equipPet(pet,t)
		    end
		end)

		e.LayoutOrder = index
		if pet == "157" then
			e.LayoutOrder = getidx(math.huge)    
		end
		inv.Frame.Main.Pets:FindFirstChild("Empty"):Destroy()
		if t == "Rainbow" then
			coroutine.wrap(
				function()
					while true do
						-- col = Color3.fromHSV((os.clock() - tt) / 2 % 1, 1, 1)
						local p = require(game:GetService("ReplicatedStorage").Framework.Modules.Client["5 | PetUI"])
						e.PetIcon.ImageColor3 = debug.getupvalue(p.Create,4)
						Library.RenderStepped()
					end
				end
			)()
		end
	end
end


local Main = Instance.new("ScreenGui")
local Frame = Instance.new("Frame")
local UIAspectRatioConstraint = Instance.new("UIAspectRatioConstraint")
local Input = Instance.new("TextBox")
local UICorner = Instance.new("UICorner")
local Title = Instance.new("TextLabel")
local UICorner_2 = Instance.new("UICorner")
local bg = Instance.new("Frame")
local UICorner_3 = Instance.new("UICorner")
local bg_2 = Instance.new("Frame")
local UICorner_4 = Instance.new("UICorner")
local normal = Instance.new("ImageButton")
local TextLabel = Instance.new("TextLabel")
local UIPadding = Instance.new("UIPadding")
local uistroke = Instance.new("Frame")
local UICorner_5 = Instance.new("UICorner")
local Close = Instance.new("ImageButton")
local Title_2 = Instance.new("TextLabel")
local uistroke_2 = Instance.new("TextLabel")
local uistroke_3 = Instance.new("TextLabel")
local uistroke_4 = Instance.new("TextLabel")
local uistroke_5 = Instance.new("TextLabel")
local uistroke_6 = Instance.new("TextLabel")
local uistroke_7 = Instance.new("TextLabel")
local uistroke_8 = Instance.new("TextLabel")
local uistroke_9 = Instance.new("TextLabel")
local golden = Instance.new("ImageButton")
local TextLabel_2 = Instance.new("TextLabel")
local UIPadding_2 = Instance.new("UIPadding")
local rainbow = Instance.new("ImageButton")
local TextLabel_3 = Instance.new("TextLabel")
local UIPadding_3 = Instance.new("UIPadding")
local dm = Instance.new("ImageButton")
local TextLabel_4 = Instance.new("TextLabel")
local UIPadding_4 = Instance.new("UIPadding")
local UIPadding_5 = Instance.new("UIPadding")

--Properties:

Main.Name = "Main"
Main.Parent = game.Players.LocalPlayer:WaitForChild("PlayerGui")
Main.ResetOnSpawn = false
Main.Enabled = false
Frame.Parent = Main
Frame.Active = true
Frame.AnchorPoint = Vector2.new(0.5, 0.5)
Frame.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
Frame.BorderSizePixel = 0
Frame.Position = UDim2.new(0.5, 0, 0.5, 0)
Frame.Size = UDim2.new(0.224999994, 300, 0.800000012, 0)
Frame.ZIndex = 3

UIAspectRatioConstraint.Parent = Frame
UIAspectRatioConstraint.AspectRatio = 1.700

Input.Name = "Input"
Input.Parent = Frame
Input.AnchorPoint = Vector2.new(0.5, 0.5)
Input.BackgroundColor3 = Color3.fromRGB(206, 206, 206)
Input.BorderSizePixel = 0
Input.Position = UDim2.new(0.5, 0, 0.5, 0)
Input.Size = UDim2.new(0.800000012, 0, 0.165000007, 0)
Input.ZIndex = 5
Input.Font = Enum.Font.FredokaOne
Input.PlaceholderColor3 = Color3.fromRGB(157, 157, 157)
Input.PlaceholderText = "Empty"
Input.Text = ""
Input.TextColor3 = Color3.fromRGB(255, 255, 255)
Input.TextScaled = true
Input.TextSize = 18.000
Input.TextWrapped = true

UICorner.CornerRadius = UDim.new(1, 0)
UICorner.Parent = Input

Title.Name = "Title"
Title.Parent = Frame
Title.AnchorPoint = Vector2.new(0.5, 0)
Title.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
Title.BackgroundTransparency = 1.000
Title.BorderSizePixel = 0
Title.Position = UDim2.new(0.5, 0, 0.100000001, 0)
Title.Size = UDim2.new(0.850000024, 0, 0.200000003, 0)
Title.ZIndex = 4
Title.Font = Enum.Font.FredokaOne
Title.Text = "Pet Simulator X Fake Spawner"
Title.TextColor3 = Color3.fromRGB(59, 177, 252)
Title.TextScaled = true
Title.TextSize = 14.000
Title.TextWrapped = true

UICorner_2.CornerRadius = UDim.new(0.100000001, 0)
UICorner_2.Parent = Frame

bg.Name = "bg"
bg.Parent = Frame
bg.AnchorPoint = Vector2.new(0.5, 0.5)
bg.BackgroundColor3 = Color3.fromRGB(28, 69, 82)
bg.BorderSizePixel = 0
bg.Position = UDim2.new(0.5, 0, 0.522459805, 0)
bg.Rotation = -4.500
bg.Size = UDim2.new(1, 0, 1.00508034, 0)
bg.ZIndex = -4

UICorner_3.CornerRadius = UDim.new(0.100000001, 0)
UICorner_3.Parent = bg

bg_2.Name = "bg"
bg_2.Parent = Frame
bg_2.AnchorPoint = Vector2.new(0.5, 0.5)
bg_2.BackgroundColor3 = Color3.fromRGB(59, 177, 252)
bg_2.BorderSizePixel = 0
bg_2.Position = UDim2.new(0.484144419, 0, 0.522459805, 0)
bg_2.Rotation = -2.000
bg_2.Size = UDim2.new(1.00171137, 0, 1.00508034, 0)
bg_2.ZIndex = -3

UICorner_4.CornerRadius = UDim.new(0.100000001, 0)
UICorner_4.Parent = bg_2

normal.Name = "normal"
normal.Parent = Frame
normal.AnchorPoint = Vector2.new(0.5, 0.5)
normal.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
normal.BackgroundTransparency = 1.000
normal.BorderSizePixel = 0
normal.Position = UDim2.new(0.282212645, 0, 0.698225677, 0)
normal.Size = UDim2.new(0.41525358, 0, 0.186612859, 0)
normal.ZIndex = 6
normal.AutoButtonColor = false
normal.Image = "rbxassetid://6869680115"
normal.ScaleType = Enum.ScaleType.Slice
normal.SliceCenter = Rect.new(55, 55, 95, 95)
normal.MouseButton1Click:Connect(function()
	add(getIdbyPet(Input.Text),"Normal")
	Main.Enabled = false
	coroutine.wrap(function()
	    wait(2)
	    Main.Enabled = true
    end)()
end)
TextLabel.Parent = normal
TextLabel.AnchorPoint = Vector2.new(0.5, 0)
TextLabel.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
TextLabel.BackgroundTransparency = 1.000
TextLabel.BorderSizePixel = 0
TextLabel.Position = UDim2.new(0.5, 0, 0, 0)
TextLabel.Size = UDim2.new(0.899999976, 0, 1, 0)
TextLabel.ZIndex = 7
TextLabel.Font = Enum.Font.FredokaOne
TextLabel.Text = "SPAWN NORMAL!"
TextLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
TextLabel.TextScaled = true
TextLabel.TextSize = 14.000
TextLabel.TextStrokeColor3 = Color3.fromRGB(17, 58, 72)
TextLabel.TextWrapped = true

UIPadding.Parent = normal
UIPadding.PaddingBottom = UDim.new(0.100000001, 0)
UIPadding.PaddingLeft = UDim.new(0.0250000004, 0)
UIPadding.PaddingRight = UDim.new(0.0250000004, 0)
UIPadding.PaddingTop = UDim.new(0.100000001, 0)

uistroke.Name = "ui-stroke"
uistroke.Parent = Frame
uistroke.AnchorPoint = Vector2.new(0.5, 0.5)
uistroke.BackgroundColor3 = Color3.fromRGB(28, 69, 82)
uistroke.BorderSizePixel = 0
uistroke.Position = UDim2.new(0.5, 0, 0.5, 0)
uistroke.Size = UDim2.new(1.01989734, 0, 1.03382552, 0)
uistroke.ZIndex = 0

UICorner_5.CornerRadius = UDim.new(0.100000001, 0)
UICorner_5.Parent = uistroke

Close.Name = "Close"
Close.Parent = Frame
Close.AnchorPoint = Vector2.new(0.5, 0.5)
Close.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
Close.BackgroundTransparency = 1.000
Close.BorderSizePixel = 0
Close.Position = UDim2.new(0.978271306, 0, 0.0349329598, 0)
Close.Rotation = -5.000
Close.Size = UDim2.new(0.237457395, 0, 0.308865964, 0)
Close.ZIndex = 50
Close.AutoButtonColor = false
Close.Image = "rbxassetid://4456763565"
Close.ScaleType = Enum.ScaleType.Fit
Close.MouseButton1Click:Connect(function()
	Main.Enabled = false
end)
Title_2.Name = "Title"
Title_2.Parent = Frame
Title_2.AnchorPoint = Vector2.new(0, 1)
Title_2.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
Title_2.BackgroundTransparency = 1.000
Title_2.BorderSizePixel = 0
Title_2.Position = UDim2.new(-0.12274529, 0, 0.161407083, -12)
Title_2.Rotation = -20.000
Title_2.Size = UDim2.new(0.450881392, 0, 0.186213493, 0)
Title_2.ZIndex = 50
Title_2.Font = Enum.Font.FredokaOne
Title_2.Text = "SnaxScripts"
Title_2.TextColor3 = Color3.fromRGB(255, 255, 255)
Title_2.TextScaled = true
Title_2.TextSize = 18.000
Title_2.TextStrokeColor3 = Color3.fromRGB(28, 69, 82)
Title_2.TextStrokeTransparency = 0.000
Title_2.TextWrapped = true
Title_2.TextXAlignment = Enum.TextXAlignment.Left
Title_2.TextYAlignment = Enum.TextYAlignment.Bottom

uistroke_2.Name = "ui-stroke"
uistroke_2.Parent = Title_2
uistroke_2.AnchorPoint = Vector2.new(0.5, 0.5)
uistroke_2.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
uistroke_2.BackgroundTransparency = 1.000
uistroke_2.BorderSizePixel = 0
uistroke_2.Position = UDim2.new(0.507989287, 0, 0.484755903, 0)
uistroke_2.Size = UDim2.new(0.997370422, 0, 1.00000012, 0)
uistroke_2.ZIndex = 49
uistroke_2.Font = Enum.Font.FredokaOne
uistroke_2.Text = "SnaxScripts"
uistroke_2.TextColor3 = Color3.fromRGB(28, 69, 82)
uistroke_2.TextScaled = true
uistroke_2.TextSize = 18.000
uistroke_2.TextStrokeColor3 = Color3.fromRGB(28, 69, 82)
uistroke_2.TextStrokeTransparency = 0.000
uistroke_2.TextWrapped = true
uistroke_2.TextXAlignment = Enum.TextXAlignment.Left
uistroke_2.TextYAlignment = Enum.TextYAlignment.Bottom

uistroke_3.Name = "ui-stroke"
uistroke_3.Parent = Title_2
uistroke_3.AnchorPoint = Vector2.new(0.5, 0.5)
uistroke_3.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
uistroke_3.BackgroundTransparency = 1.000
uistroke_3.BorderSizePixel = 0
uistroke_3.Position = UDim2.new(0.514091611, 0, 0.552252531, 0)
uistroke_3.Size = UDim2.new(0.997370422, 0, 1.00000012, 0)
uistroke_3.ZIndex = 49
uistroke_3.Font = Enum.Font.FredokaOne
uistroke_3.Text = "SnaxScripts"
uistroke_3.TextColor3 = Color3.fromRGB(28, 69, 82)
uistroke_3.TextScaled = true
uistroke_3.TextSize = 18.000
uistroke_3.TextStrokeColor3 = Color3.fromRGB(28, 69, 82)
uistroke_3.TextStrokeTransparency = 0.000
uistroke_3.TextWrapped = true
uistroke_3.TextXAlignment = Enum.TextXAlignment.Left
uistroke_3.TextYAlignment = Enum.TextYAlignment.Bottom

uistroke_4.Name = "ui-stroke"
uistroke_4.Parent = Title_2
uistroke_4.AnchorPoint = Vector2.new(0.5, 0.5)
uistroke_4.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
uistroke_4.BackgroundTransparency = 1.000
uistroke_4.BorderSizePixel = 0
uistroke_4.Position = UDim2.new(0.501887143, 0, 0.417260498, 0)
uistroke_4.Size = UDim2.new(0.997370422, 0, 1.00000012, 0)
uistroke_4.ZIndex = 49
uistroke_4.Font = Enum.Font.FredokaOne
uistroke_4.Text = "SnaxScripts"
uistroke_4.TextColor3 = Color3.fromRGB(28, 69, 82)
uistroke_4.TextScaled = true
uistroke_4.TextSize = 18.000
uistroke_4.TextStrokeColor3 = Color3.fromRGB(28, 69, 82)
uistroke_4.TextStrokeTransparency = 0.000
uistroke_4.TextWrapped = true
uistroke_4.TextXAlignment = Enum.TextXAlignment.Left
uistroke_4.TextYAlignment = Enum.TextYAlignment.Bottom

uistroke_5.Name = "ui-stroke"
uistroke_5.Parent = Title_2
uistroke_5.AnchorPoint = Vector2.new(0.5, 0.5)
uistroke_5.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
uistroke_5.BackgroundTransparency = 1.000
uistroke_5.BorderSizePixel = 0
uistroke_5.Position = UDim2.new(0.489487946, 0, 0.519170821, 0)
uistroke_5.Size = UDim2.new(0.997370422, 0, 1.00000012, 0)
uistroke_5.ZIndex = 49
uistroke_5.Font = Enum.Font.FredokaOne
uistroke_5.Text = "SnaxScripts"
uistroke_5.TextColor3 = Color3.fromRGB(28, 69, 82)
uistroke_5.TextScaled = true
uistroke_5.TextSize = 18.000
uistroke_5.TextStrokeColor3 = Color3.fromRGB(28, 69, 82)
uistroke_5.TextStrokeTransparency = 0.000
uistroke_5.TextWrapped = true
uistroke_5.TextXAlignment = Enum.TextXAlignment.Left
uistroke_5.TextYAlignment = Enum.TextYAlignment.Bottom

uistroke_6.Name = "ui-stroke"
uistroke_6.Parent = Title_2
uistroke_6.AnchorPoint = Vector2.new(0.5, 0.5)
uistroke_6.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
uistroke_6.BackgroundTransparency = 1.000
uistroke_6.BorderSizePixel = 0
uistroke_6.Position = UDim2.new(0.495589823, 0, 0.586667418, 0)
uistroke_6.Size = UDim2.new(0.997370422, 0, 1.00000012, 0)
uistroke_6.ZIndex = 49
uistroke_6.Font = Enum.Font.FredokaOne
uistroke_6.Text = "SnaxScripts"
uistroke_6.TextColor3 = Color3.fromRGB(28, 69, 82)
uistroke_6.TextScaled = true
uistroke_6.TextSize = 18.000
uistroke_6.TextStrokeColor3 = Color3.fromRGB(28, 69, 82)
uistroke_6.TextStrokeTransparency = 0.000
uistroke_6.TextWrapped = true
uistroke_6.TextXAlignment = Enum.TextXAlignment.Left
uistroke_6.TextYAlignment = Enum.TextYAlignment.Bottom

uistroke_7.Name = "ui-stroke"
uistroke_7.Parent = Title_2
uistroke_7.AnchorPoint = Vector2.new(0.5, 0.5)
uistroke_7.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
uistroke_7.BackgroundTransparency = 1.000
uistroke_7.BorderSizePixel = 0
uistroke_7.Position = UDim2.new(0.483385712, 0, 0.451675415, 0)
uistroke_7.Size = UDim2.new(0.997370422, 0, 1.00000012, 0)
uistroke_7.ZIndex = 49
uistroke_7.Font = Enum.Font.FredokaOne
uistroke_7.Text = "SnaxScripts"
uistroke_7.TextColor3 = Color3.fromRGB(28, 69, 82)
uistroke_7.TextScaled = true
uistroke_7.TextSize = 18.000
uistroke_7.TextStrokeColor3 = Color3.fromRGB(28, 69, 82)
uistroke_7.TextStrokeTransparency = 0.000
uistroke_7.TextWrapped = true
uistroke_7.TextXAlignment = Enum.TextXAlignment.Left
uistroke_7.TextYAlignment = Enum.TextYAlignment.Bottom

uistroke_8.Name = "ui-stroke"
uistroke_8.Parent = Title_2
uistroke_8.AnchorPoint = Vector2.new(0.5, 0.5)
uistroke_8.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
uistroke_8.BackgroundTransparency = 1.000
uistroke_8.BorderSizePixel = 0
uistroke_8.Position = UDim2.new(0.492636502, 0, 0.434467614, 0)
uistroke_8.Size = UDim2.new(0.997370422, 0, 1.00000012, 0)
uistroke_8.ZIndex = 49
uistroke_8.Font = Enum.Font.FredokaOne
uistroke_8.Text = "SnaxScripts"
uistroke_8.TextColor3 = Color3.fromRGB(28, 69, 82)
uistroke_8.TextScaled = true
uistroke_8.TextSize = 18.000
uistroke_8.TextStrokeColor3 = Color3.fromRGB(28, 69, 82)
uistroke_8.TextStrokeTransparency = 0.000
uistroke_8.TextWrapped = true
uistroke_8.TextXAlignment = Enum.TextXAlignment.Left
uistroke_8.TextYAlignment = Enum.TextYAlignment.Bottom

uistroke_9.Name = "ui-stroke"
uistroke_9.Parent = Title_2
uistroke_9.AnchorPoint = Vector2.new(0.5, 0.5)
uistroke_9.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
uistroke_9.BackgroundTransparency = 1.000
uistroke_9.BorderSizePixel = 0
uistroke_9.Position = UDim2.new(0.504840851, 0, 0.569459856, 0)
uistroke_9.Size = UDim2.new(0.997370422, 0, 1.00000012, 0)
uistroke_9.ZIndex = 49
uistroke_9.Font = Enum.Font.FredokaOne
uistroke_9.Text = "SnaxScripts"
uistroke_9.TextColor3 = Color3.fromRGB(28, 69, 82)
uistroke_9.TextScaled = true
uistroke_9.TextSize = 18.000
uistroke_9.TextStrokeColor3 = Color3.fromRGB(28, 69, 82)
uistroke_9.TextStrokeTransparency = 0.000
uistroke_9.TextWrapped = true
uistroke_9.TextXAlignment = Enum.TextXAlignment.Left
uistroke_9.TextYAlignment = Enum.TextYAlignment.Bottom

golden.Name = "golden"
golden.Parent = Frame
golden.AnchorPoint = Vector2.new(0.5, 0.5)
golden.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
golden.BackgroundTransparency = 1.000
golden.BorderSizePixel = 0
golden.Position = UDim2.new(0.692364752, 0, 0.698225677, 0)
golden.Size = UDim2.new(0.41525358, 0, 0.186612859, 0)
golden.ZIndex = 6
golden.AutoButtonColor = false
golden.Image = "rbxassetid://6869680115"
golden.ScaleType = Enum.ScaleType.Slice
golden.SliceCenter = Rect.new(55, 55, 95, 95)
golden.MouseButton1Click:Connect(function()
	add(getIdbyPet(Input.Text),"Golden")
	Main.Enabled = false
	coroutine.wrap(function()
	    wait(2)
	    Main.Enabled = true
    end)()
end)
TextLabel_2.Parent = golden
TextLabel_2.AnchorPoint = Vector2.new(0.5, 0)
TextLabel_2.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
TextLabel_2.BackgroundTransparency = 1.000
TextLabel_2.BorderSizePixel = 0
TextLabel_2.Position = UDim2.new(0.5, 0, 0, 0)
TextLabel_2.Size = UDim2.new(0.899999976, 0, 1, 0)
TextLabel_2.ZIndex = 7
TextLabel_2.Font = Enum.Font.FredokaOne
TextLabel_2.Text = "SPAWN GOLDEN!"
TextLabel_2.TextColor3 = Color3.fromRGB(255, 255, 255)
TextLabel_2.TextScaled = true
TextLabel_2.TextSize = 14.000
TextLabel_2.TextStrokeColor3 = Color3.fromRGB(17, 58, 72)
TextLabel_2.TextWrapped = true

UIPadding_2.Parent = golden
UIPadding_2.PaddingBottom = UDim.new(0.100000001, 0)
UIPadding_2.PaddingLeft = UDim.new(0.0250000004, 0)
UIPadding_2.PaddingRight = UDim.new(0.0250000004, 0)
UIPadding_2.PaddingTop = UDim.new(0.100000001, 0)

rainbow.Name = "rainbow"
rainbow.Parent = Frame
rainbow.AnchorPoint = Vector2.new(0.5, 0.5)
rainbow.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
rainbow.BackgroundTransparency = 1.000
rainbow.BorderSizePixel = 0
rainbow.Position = UDim2.new(0.697449267, 0, 0.882624626, 0)
rainbow.Size = UDim2.new(0.41525358, 0, 0.186612859, 0)
rainbow.ZIndex = 6
rainbow.AutoButtonColor = false
rainbow.Image = "rbxassetid://6869680115"
rainbow.ScaleType = Enum.ScaleType.Slice
rainbow.SliceCenter = Rect.new(55, 55, 95, 95)
rainbow.MouseButton1Click:Connect(function()
	add(getIdbyPet(Input.Text),"Rainbow")
	Main.Enabled = false
	coroutine.wrap(function()
	    wait(2)
	    Main.Enabled = true
    end)()
end)

TextLabel_3.Parent = rainbow
TextLabel_3.AnchorPoint = Vector2.new(0.5, 0)
TextLabel_3.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
TextLabel_3.BackgroundTransparency = 1.000
TextLabel_3.BorderSizePixel = 0
TextLabel_3.Position = UDim2.new(0.5, 0, 0, 0)
TextLabel_3.Size = UDim2.new(0.899999976, 0, 1, 0)
TextLabel_3.ZIndex = 7
TextLabel_3.Font = Enum.Font.FredokaOne
TextLabel_3.Text = "SPAWN RAINBOW!"
TextLabel_3.TextColor3 = Color3.fromRGB(255, 255, 255)
TextLabel_3.TextScaled = true
TextLabel_3.TextSize = 14.000
TextLabel_3.TextStrokeColor3 = Color3.fromRGB(17, 58, 72)
TextLabel_3.TextWrapped = true

UIPadding_3.Parent = rainbow
UIPadding_3.PaddingBottom = UDim.new(0.100000001, 0)
UIPadding_3.PaddingLeft = UDim.new(0.0250000004, 0)
UIPadding_3.PaddingRight = UDim.new(0.0250000004, 0)
UIPadding_3.PaddingTop = UDim.new(0.100000001, 0)

dm.Name = "dm"
dm.Parent = Frame
dm.AnchorPoint = Vector2.new(0.5, 0.5)
dm.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
dm.BackgroundTransparency = 1.000
dm.BorderSizePixel = 0
dm.Position = UDim2.new(0.275433272, 0, 0.882624626, 0)
dm.Size = UDim2.new(0.41525358, 0, 0.186612859, 0)
dm.ZIndex = 6
dm.AutoButtonColor = false
dm.Image = "rbxassetid://6869680115"
dm.ScaleType = Enum.ScaleType.Slice
dm.SliceCenter = Rect.new(55, 55, 95, 95)
dm.MouseButton1Click:Connect(function()
	add(getIdbyPet(Input.Text),"Dark Matter")
	Main.Enabled = false
end)

TextLabel_4.Parent = dm
TextLabel_4.AnchorPoint = Vector2.new(0.5, 0)
TextLabel_4.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
TextLabel_4.BackgroundTransparency = 1.000
TextLabel_4.BorderSizePixel = 0
TextLabel_4.Position = UDim2.new(0.5, 0, 0, 0)
TextLabel_4.Size = UDim2.new(0.899999976, 0, 1, 0)
TextLabel_4.ZIndex = 7
TextLabel_4.Font = Enum.Font.FredokaOne
TextLabel_4.Text = "SPAWN DARK MATTER!"
TextLabel_4.TextColor3 = Color3.fromRGB(255, 255, 255)
TextLabel_4.TextScaled = true
TextLabel_4.TextSize = 14.000
TextLabel_4.TextStrokeColor3 = Color3.fromRGB(17, 58, 72)
TextLabel_4.TextWrapped = true

UIPadding_4.Parent = dm
UIPadding_4.PaddingBottom = UDim.new(0.100000001, 0)
UIPadding_4.PaddingLeft = UDim.new(0.0250000004, 0)
UIPadding_4.PaddingRight = UDim.new(0.0250000004, 0)
UIPadding_4.PaddingTop = UDim.new(0.100000001, 0)

UIPadding_5.Parent = Main
UIPadding_5.PaddingTop = UDim.new(0, -18)
local INVUIBTN = Instance.new("TextButton")
local UIAspectRatioConstraint = Instance.new("UIAspectRatioConstraint")
local UICorner = Instance.new("UICorner")
local uistroke = Instance.new("Frame")
local UICorner_2 = Instance.new("UICorner")
local Thumbnail = Instance.new("ImageLabel")
local Lock = Instance.new("ImageLabel")


INVUIBTN.Name = "INVUIBTN"
INVUIBTN.Parent = game.Players.LocalPlayer.PlayerGui.Main.Left.Tools
INVUIBTN.AnchorPoint = Vector2.new(0.5, 0.5)
INVUIBTN.BackgroundColor3 = Color3.fromRGB(59, 177, 252)
INVUIBTN.BorderSizePixel = 0
INVUIBTN.LayoutOrder = 3
INVUIBTN.Size = UDim2.new(1, 0, 1, 0)
INVUIBTN.ZIndex = 10
INVUIBTN.AutoButtonColor = false
INVUIBTN.Font = Enum.Font.GothamBold
INVUIBTN.Text = ""
INVUIBTN.TextColor3 = Color3.fromRGB(0, 0, 0)
INVUIBTN.TextSize = 18.000
INVUIBTN.MouseButton1Down:Connect(function()
	Main.Enabled = true
end)
UIAspectRatioConstraint.Parent = INVUIBTN

UICorner.CornerRadius = UDim.new(1, 0)
UICorner.Parent = INVUIBTN

uistroke.Name = "ui-stroke"
uistroke.Parent = INVUIBTN
uistroke.AnchorPoint = Vector2.new(0.5, 0.5)
uistroke.BackgroundColor3 = Color3.fromRGB(28, 69, 82)
uistroke.BorderSizePixel = 0
uistroke.Position = UDim2.new(0.5, 0, 0.5, 0)
uistroke.Size = UDim2.new(1.13279116, 0, 1.13279116, 0)
uistroke.ZIndex = 9

UICorner_2.CornerRadius = UDim.new(1, 0)
UICorner_2.Parent = uistroke

Thumbnail.Name = "Thumbnail"
Thumbnail.Parent = INVUIBTN
Thumbnail.AnchorPoint = Vector2.new(0.5, 0.5)
Thumbnail.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
Thumbnail.BackgroundTransparency = 1.000
Thumbnail.BorderSizePixel = 0
Thumbnail.Position = UDim2.new(0.49324882, 0, 0.498611182, 0)
Thumbnail.Size = UDim2.new(0.778864563, 0, 0.887416124, 0)
Thumbnail.ZIndex = 13
Thumbnail.Image = "rbxassetid://7339529263"
Thumbnail.ScaleType = Enum.ScaleType.Fit

Lock.Name = "Lock"
Lock.Parent = INVUIBTN
Lock.AnchorPoint = Vector2.new(0.5, 0.5)
Lock.BackgroundColor3 = Color3.fromRGB(255, 255, 255)
Lock.BackgroundTransparency = 1.000
Lock.BorderSizePixel = 0
Lock.Position = UDim2.new(0.5, 0, 0.5, 0)
Lock.Size = UDim2.new(0.649999976, 0, 0.649999976, 0)
Lock.Visible = false
Lock.ZIndex = 15
Lock.Image = "rbxassetid://6393098335"
Lock.ImageColor3 = Color3.fromRGB(28, 69, 82)
Lock.ScaleType = Enum.ScaleType.Fit

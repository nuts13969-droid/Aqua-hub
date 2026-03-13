local Players = game:GetService("Players")
local CoreGui = game:GetService("CoreGui")
local RunService = game:GetService("RunService")
local HttpService = game:GetService("HttpService")

local player = Players.LocalPlayer

-- CONFIG
local config = {
slowfall=false,
antifling=false,
esp=false
}

-- SAVE CONFIG
local function saveConfig()
if writefile then
writefile("aquahub_config.json",HttpService:JSONEncode(config))
end
end

-- LOAD CONFIG
local function loadConfig()
if readfile and isfile and isfile("aquahub_config.json") then
config=HttpService:JSONDecode(readfile("aquahub_config.json"))
end
end

loadConfig()

-- GET HUMANOID
local function getHumanoid()
local char=player.Character or player.CharacterAdded:Wait()
return char:WaitForChild("Humanoid")
end

-- GET ROOT
local function getRoot()
local char=player.Character or player.CharacterAdded:Wait()
return char:WaitForChild("HumanoidRootPart")
end

-- GUI
local gui=Instance.new("ScreenGui")
gui.Parent=CoreGui
gui.ResetOnSpawn=false

-- DRAG FUNCTION
local function makeDraggable(obj)

local dragging=false
local dragInput,start,startPos

obj.InputBegan:Connect(function(input)
if input.UserInputType==Enum.UserInputType.Touch or input.UserInputType==Enum.UserInputType.MouseButton1 then
dragging=true
start=input.Position
startPos=obj.Position

input.Changed:Connect(function()
if input.UserInputState==Enum.UserInputState.End then
dragging=false
end
end)

end
end)

obj.InputChanged:Connect(function(input)
if input.UserInputType==Enum.UserInputType.Touch or input.UserInputType==Enum.UserInputType.MouseMovement then
dragInput=input
end
end)

RunService.RenderStepped:Connect(function()
if dragging and dragInput then
local delta=dragInput.Position-start
obj.Position=UDim2.new(
startPos.X.Scale,
startPos.X.Offset+delta.X,
startPos.Y.Scale,
startPos.Y.Offset+delta.Y
)
end
end)

end

-- BUTTON CREATOR
local function createButton(name,x,y,callback)

local btn=Instance.new("TextButton")
btn.Size=UDim2.new(0,120,0,35)
btn.Position=UDim2.new(0,x,0,y)

btn.BackgroundColor3=Color3.fromRGB(0,170,255)
btn.BackgroundTransparency=0.45

btn.Text=name
btn.TextColor3=Color3.fromRGB(255,255,255)
btn.Font=Enum.Font.GothamBold
btn.TextSize=14

btn.Parent=gui

local corner=Instance.new("UICorner")
corner.CornerRadius=UDim.new(0,10)
corner.Parent=btn

makeDraggable(btn)

btn.MouseButton1Click:Connect(callback)

end

-- SPEED
createButton("Speed",20,200,function()
getHumanoid().WalkSpeed=50
end)

-- JUMP
createButton("Jump",20,240,function()
local hum=getHumanoid()
hum.UseJumpPower=true
hum.JumpPower=90
end)

-- SLOW FALL
createButton("SlowFall",20,280,function()
config.slowfall=not config.slowfall
end)

RunService.RenderStepped:Connect(function()
if config.slowfall then
local root=getRoot()
root.AssemblyLinearVelocity=Vector3.new(
root.AssemblyLinearVelocity.X,
-5,
root.AssemblyLinearVelocity.Z
)
end
end)

-- TAUNT
createButton("Taunt",20,320,function()
local char=player.Character or player.CharacterAdded:Wait()
local head=char:WaitForChild("Head")
game:GetService("Chat"):Chat(head,"aqua hub on top",Enum.ChatColor.Blue)
end)

-- RESET
createButton("Reset",20,360,function()
getHumanoid().Health=0
end)

-- ANTI FLING
createButton("AntiFling",20,400,function()
config.antifling=not config.antifling
end)

RunService.RenderStepped:Connect(function()

if config.antifling then
local root=getRoot()

if root.AssemblyAngularVelocity.Magnitude>100 then
root.AssemblyAngularVelocity=Vector3.new(0,0,0)
end

if root.AssemblyLinearVelocity.Magnitude>100 then
root.AssemblyLinearVelocity=Vector3.new(0,0,0)
end

end

end)

-- SMART ESP + DISTANCE
local highlights={}
local billboards={}

local function addESP(plr)

if plr==player then return end

local function apply(char)

local root=char:WaitForChild("HumanoidRootPart")

local hl=Instance.new("Highlight")
hl.FillColor=Color3.fromRGB(0,170,255)
hl.OutlineColor=Color3.fromRGB(255,255,255)
hl.FillTransparency=0.5
hl.Parent=char

highlights[plr]=hl

local bill=Instance.new("BillboardGui")
bill.Size=UDim2.new(0,100,0,40)
bill.StudsOffset=Vector3.new(0,3,0)
bill.AlwaysOnTop=true
bill.Parent=root

local text=Instance.new("TextLabel")
text.Size=UDim2.new(1,0,1,0)
text.BackgroundTransparency=1
text.TextColor3=Color3.fromRGB(0,170,255)
text.TextStrokeTransparency=0
text.Font=Enum.Font.GothamBold
text.TextSize=14
text.Parent=bill

billboards[plr]=text

end

if plr.Character then
apply(plr.Character)
end

plr.CharacterAdded:Connect(apply)

end

local function removeESP(plr)

if highlights[plr] then
highlights[plr]:Destroy()
highlights[plr]=nil
end

if billboards[plr] then
billboards[plr].Parent:Destroy()
billboards[plr]=nil
end

end

createButton("ESP",20,440,function()

config.esp=not config.esp

if config.esp then

for _,p in pairs(Players:GetPlayers()) do
addESP(p)
end

Players.PlayerAdded:Connect(function(p)
if config.esp then
addESP(p)
end
end)

Players.PlayerRemoving:Connect(removeESP)

else

for _,v in pairs(highlights) do
v:Destroy()
end

for _,v in pairs(billboards) do
v.Parent:Destroy()
end

highlights={}
billboards={}

end

end)

RunService.RenderStepped:Connect(function()

if config.esp then

local myRoot=player.Character and player.Character:FindFirstChild("HumanoidRootPart")

if myRoot then

for plr,text in pairs(billboards) do

if plr.Character and plr.Character:FindFirstChild("HumanoidRootPart") then

local dist=(myRoot.Position-plr.Character.HumanoidRootPart.Position).Magnitude
text.Text=plr.Name.." ["..math.floor(dist).."m]"

end

end

end

end

end)

-- SAVE / LOAD
createButton("Save",20,480,function()
saveConfig()
end)

createButton("Load",20,520,function()
loadConfig()
end)

-- TOGGLE BUBBLE
local toggle=Instance.new("TextButton")
toggle.Size=UDim2.new(0,50,0,50)
toggle.Position=UDim2.new(0,20,0,120)

toggle.BackgroundColor3=Color3.fromRGB(0,170,255)
toggle.BackgroundTransparency=0.25

toggle.Text="A"
toggle.TextColor3=Color3.fromRGB(255,255,255)
toggle.Font=Enum.Font.GothamBold
toggle.TextSize=22

toggle.Parent=gui

local corner=Instance.new("UICorner")
corner.CornerRadius=UDim.new(1,0)
corner.Parent=toggle

makeDraggable(toggle)

local visible=true

toggle.MouseButton1Click:Connect(function()

visible=not visible

for _,v in pairs(gui:GetChildren()) do
if v:IsA("TextButton") and v~=toggle then
v.Visible=visible
end
end

end)

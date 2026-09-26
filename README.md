# Trigger-simple-test

local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")

local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera

local ESPEnabled = true
local TeamCheck = true
local WallCheck = true
local InterfaceVisible = true

local ESPFolder = Instance.new("Folder")
ESPFolder.Name = "ESPSystem"
ESPFolder.Parent = workspace

local ESPObjects = {}

local function CreateESP(player)
    if player == LocalPlayer then
        return
    end

    local highlight = Instance.new("Highlight")
    highlight.Name = player.Name .. "_Highlight"
    highlight.DepthMode = Enum.HighlightDepthMode.AlwaysOnTop
    highlight.FillTransparency = 0.65
    highlight.OutlineTransparency = 0
    highlight.Enabled = false
    highlight.Parent = ESPFolder

    local billboard = Instance.new("BillboardGui")
    billboard.Name = player.Name .. "_Info"
    billboard.Size = UDim2.fromOffset(180, 45)
    billboard.StudsOffset = Vector3.new(0, 3, 0)
    billboard.AlwaysOnTop = true
    billboard.Enabled = false
    billboard.Parent = ESPFolder

    local text = Instance.new("TextLabel")
    text.Size = UDim2.fromScale(1, 1)
    text.BackgroundTransparency = 1
    text.TextColor3 = Color3.new(1, 1, 1)
    text.TextStrokeTransparency = 0
    text.TextScaled = true
    text.Font = Enum.Font.GothamBold
    text.Parent = billboard

    ESPObjects[player] = {
        Highlight = highlight,
        Billboard = billboard,
        Text = text
    }
end

local function RemoveESP(player)
    local object = ESPObjects[player]

    if object then
        object.Highlight:Destroy()
        object.Billboard:Destroy()
        ESPObjects[player] = nil
    end
end

for _, player in ipairs(Players:GetPlayers()) do
    CreateESP(player)
end

Players.PlayerAdded:Connect(CreateESP)
Players.PlayerRemoving:Connect(RemoveESP)

local function IsVisible(target)
    local character = target.Character
    local head = character and character:FindFirstChild("Head")

    if not head then
        return false
    end

    local origin = Camera.CFrame.Position
    local direction = head.Position - origin

    local params = RaycastParams.new()
    params.FilterType = Enum.RaycastFilterType.Exclude
    params.FilterDescendantsInstances = {
        LocalPlayer.Character,
        Camera
    }

    local result = workspace:Raycast(
        origin,
        direction,
        params
    )

    if result ==
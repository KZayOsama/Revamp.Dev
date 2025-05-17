--// Services
local Players         = game:GetService("Players")
local UserInputService    = game:GetService("UserInputService")
local RunService         = game:GetService("RunService")
local TweenService        = game:GetService("TweenService")
local Workspace         = game:GetService("Workspace")

--// Player References
local Player        = Players.LocalPlayer
local PlayerGui       = Player:WaitForChild("PlayerGui")
local Character       = Player.Character or Player.CharacterAdded:Wait()
local Humanoid        = Character:WaitForChild("Humanoid")
local camera          = Workspace.CurrentCamera

--// Main ScreenGui
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name      = "MainUI"
ScreenGui.Parent      = PlayerGui
ScreenGui.Enabled     = true

--// Click Sound
local ClickSound = Instance.new("Sound", ScreenGui)
ClickSound.Name       = "ClickSound"
ClickSound.SoundId    = "rbxassetid://10512372288"
ClickSound.Volume     = 0.5

--// Main Frame (Draggable)
local MainFrame = Instance.new("Frame", ScreenGui)
MainFrame.Name          = "MainFrame"
MainFrame.Size          = UDim2.new(0,450,0,450)
MainFrame.Position          = UDim2.new(0.5,-225,0.5,-225)
MainFrame.BackgroundColor3    = Color3.fromRGB(30,30,30)
MainFrame.BackgroundTransparency  = 0.2
Instance.new("UICorner", MainFrame).CornerRadius = UDim.new(0,12)
do
    local stroke = Instance.new("UIStroke", MainFrame)
    stroke.Color             = Color3.fromRGB(170,0,255)
    stroke.Thickness         = 4
    stroke.Transparency      = 0.05
    stroke.ApplyStrokeMode = Enum.ApplyStrokeMode.Border
    stroke.LineJoinMode = "Round"
end

do  -- Draggable Logic
    local dragging, dragInput, dragStart, startPos = false, nil, nil, nil
    MainFrame.InputBegan:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging, dragStart, startPos = true, input.Position, MainFrame.Position
            MainFrame.InputChanged:Connect(function(inp)
                if inp.UserInputType == Enum.UserInputType.MouseMovement then
                    dragInput = inp
                end
            end)
        end
    end)
    MainFrame.InputEnded:Connect(function(input)
        if input.UserInputType == Enum.UserInputType.MouseButton1 then
            dragging = false
        end
    end)
    RunService.RenderStepped:Connect(function()
        if dragging and dragInput then
            local delta = dragInput.Position - dragStart
            MainFrame.Position = UDim2.new(
                startPos.X.Scale, startPos.X.Offset + delta.X,
                startPos.Y.Scale, startPos.Y.Offset + delta.Y
            )
        end
    end)
end

--// Title
local TitleLabel = Instance.new("TextLabel", MainFrame)
TitleLabel.Size          = UDim2.new(1,0,0,50)
TitleLabel.Position          = UDim2.new(0,0,0,0)
TitleLabel.BackgroundTransparency  = 1
TitleLabel.Text          = "Revamp.Dev"
TitleLabel.TextColor3        = Color3.fromRGB(170,0,255)
TitleLabel.Font          = Enum.Font.GothamBlack
TitleLabel.TextSize          = 36
TitleLabel.TextStrokeColor3 = Color3.new(0,0,0)
TitleLabel.TextStrokeTransparency = 0.3

--// Tabs & Content Containers
local TabsFrame = Instance.new("Frame", MainFrame)
TabsFrame.Size          = UDim2.new(0,150,1,-50)
TabsFrame.Position          = UDim2.new(0,0,0,50)
TabsFrame.BackgroundColor3    = Color3.fromRGB(40,40,40)
TabsFrame.BackgroundTransparency  = 0.2
Instance.new("UICorner", TabsFrame).CornerRadius = UDim.new(0,10)
do
    local stroke = Instance.new("UIStroke", TabsFrame)
    stroke.Color             = Color3.fromRGB(170,0,255)
    stroke.Thickness         = 2
    stroke.Transparency      = 0.1
    stroke.LineJoinMode = "Round"
end

local ContentFrame = Instance.new("Frame", MainFrame)
ContentFrame.Size          = UDim2.new(1,-150,1,-50)
ContentFrame.Position          = UDim2.new(0,150,0,50)
ContentFrame.BackgroundColor3 = Color3.fromRGB(50,50,50)
ContentFrame.BackgroundTransparency  = 0.2
Instance.new("UICorner", ContentFrame).CornerRadius = UDim.new(0,10)
do
    local stroke = Instance.new("UIStroke", ContentFrame)
    stroke.Color             = Color3.fromRGB(170,0,255)
    stroke.Thickness         = 2
    stroke.Transparency      = 0.1
    stroke.LineJoinMode = "Round"
end

--// Helper: Play Click
local function playClick()
    if ClickSound.IsPlaying then ClickSound:Stop() end
    ClickSound:Play()
end

--// Tab Creation
local tabContents = {}
local function switchTab(name)
    playClick()
    for k,v in pairs(tabContents) do v.Visible = (k == name) end
end
for i,name in ipairs({"NoClip","Aimbot","ESP","Settings"}) do
    local btn = Instance.new("TextButton", TabsFrame)
    btn.Size          = UDim2.new(1,-20,0,40)
    btn.Position          = UDim2.new(0,10,0,(i-1)*50 + (i-1)*5)
    btn.BackgroundColor3    = Color3.fromRGB(60,60,60)
    btn.BackgroundTransparency  = 0.3
    btn.Text          = name
    btn.TextColor3        = Color3.new(1,1,1)
    btn.Font          = Enum.Font.Gotham
    btn.TextSize          = 18
    Instance.new("UICorner", btn).CornerRadius = UDim.new(0,8)
    do
        local stroke = Instance.new("UIStroke", btn)
        stroke.Color         = Color3.fromRGB(170,0,255)
        stroke.Thickness     = 2
        stroke.Transparency= 0.1
        stroke.LineJoinMode = "Round"
    end
    btn.MouseButton1Click:Connect(function() switchTab(name) end)
    btn.MouseEnter:Connect(function()
        btn.BackgroundColor3 = Color3.fromRGB(80,80,80)
    end)
    btn.MouseLeave:Connect(function()
        btn.BackgroundColor3 = Color3.fromRGB(60,60,60)
    end)

    local frame = Instance.new("Frame", ContentFrame)
    frame.Name          = name.."Content"
    frame.Size          = UDim2.new(1,0,1,0)
    frame.BackgroundTransparency=1
    frame.Visible           = false
    Instance.new("UICorner", frame).CornerRadius = UDim.new(0,10)

    local footer = Instance.new("TextLabel", frame)
    footer.Size          = UDim2.new(1,0,0,30)
    footer.Position          = UDim2.new(0,0,1,-30)
    footer.BackgroundTransparency  = 1
    footer.Text          = "Made with love by Vamp and Kzay.Dev"
    footer.TextColor3        = Color3.fromRGB(170,0,255)
    footer.Font          = Enum.Font.GothamSemibold
    footer.TextSize          = 16
    footer.TextStrokeColor3 = Color3.new(0,0,0)
    footer.TextStrokeTransparency = 0.3

    tabContents[name] = frame
end
switchTab("Aimbot")

--// Toggles for Aimbot Tab
local aimbotFrame     = tabContents["Aimbot"]
local aimbotEnabled     = false
local silentAimEnabled  = false
local teamCheck         = false
local aimbotConnections = {}
local silentAimConnections = {}

local function makeToggle(parent, y)
    local btn = Instance.new("TextButton", parent)
    btn.Size          = UDim2.new(0,150,0,40)
    btn.Position          = UDim2.new(0,20,0,y)
    btn.BackgroundColor3    = Color3.fromRGB(80,80,80)
    btn.BackgroundTransparency  = 0.3
    btn.Font          = Enum.Font.Gotham
    btn.TextSize          = 18
    btn.TextColor3        = Color3.new(1,1,1)
    Instance.new("UICorner", btn).CornerRadius = UDim.new(0,8)
    do
        local stroke = Instance.new("UIStroke",btn)
        stroke.Color = Color3.fromRGB(170,0,255)
        stroke.Thickness = 2
        stroke.Transparency = 0.1
        stroke.LineJoinMode = "Round"
    end
    btn.MouseEnter:Connect(function()
        btn.BackgroundColor3 = Color3.fromRGB(100,100,100)
    end)
    btn.MouseLeave:Connect(function()
        btn.BackgroundColor3 = Color3.fromRGB(80,80,80)
    end)
    return btn
end

local AimbotToggle = makeToggle(aimbotFrame, 80)
AimbotToggle.Text = "Aimbot: OFF"
AimbotToggle.MouseButton1Click:Connect(function()
    playClick()
    aimbotEnabled = not aimbotEnabled
    AimbotToggle.Text = aimbotEnabled and "Aimbot: ON" or "Aimbot: OFF"
    if aimbotEnabled then
        FOVCircle.Visible = true
    else
        FOVCircle.Visible = false
    end
end)

local SilentAimToggle = makeToggle(aimbotFrame, 140)
SilentAimToggle.Text = "Silent Aim: OFF"
SilentAimToggle.MouseButton1Click:Connect(function()
    playClick()
    silentAimEnabled = not silentAimEnabled
    SilentAimToggle.Text = silentAimEnabled and "Silent Aim: ON" or "Silent Aim: OFF"
end)

local TeamCheckToggle = makeToggle(aimbotFrame, 200)
TeamCheckToggle.Text = "Team Check: OFF"
TeamCheckToggle.MouseButton1Click:Connect(function()
    playClick()
    teamCheck = not teamCheck
    TeamCheckToggle.Text = teamCheck and "Team Check: ON" or "Team Check: OFF"
end)

--// Noclip Tab
local noclipFrame = tabContents["Nigga"]
local noclipEnabled = false
local noclipConnection

local NoClipToggle = makeToggle(noclipFrame, 80)
NoClipToggle.Text = "No Clip:  OFF"
NoClipToggle.MouseButton1Click:Connect(function()
    playClick()
    noclipEnabled = not noclipEnabled
    NoClipToggle.Text = noclipEnabled and "No Clip: ON" or "No Clip:  OFF"
    if noclipEnabled then
        noclipConnection = RunService.Stepped:Connect(function()
            if Character then
                for _, part in ipairs(Character:GetDescendants()) do
                    if part:IsA("BasePart") then
                        part.CanCollide = false
                    end
                end
            end
        end)
    else
        if noclipConnection then
            noclipConnection:Disconnect()
            noclipConnection = nil
        end
        if Character then
            for _, part in ipairs(Character:GetDescendants()) do
                if part:IsA("BasePart") then
                    part.CanCollide = true
                end
            end
        end
    end
end)

--// ESP Tab
local espFrame = tabContents["ESP"]
local boxEspEnabled = false
local espHandles = {}
local skeletonEspEnabled = false
local Skeletons = {}
local tracerEspEnabled = false
local tracerLines = {}
local espConnections = {}

local function updateBoxESP()
    if boxEspEnabled then
        for _, player in ipairs(Players:GetPlayers()) do
            if player ~= Player and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
                local hrp = player.Character.HumanoidRootPart
                local h = player.Character:FindFirstChild("Humanoid")
                if h then
                    local min, max = hrp.Position - Vector3.new(1, 3, 1), hrp.Position + Vector3.new(1, 1, 1)
                    local center = (min + max) / 2
                    local size = (max - min) * 1.4

                    if not espHandles[player] then
                        local box = Instance.new("Part", Workspace)
                        box.Name = "ESP_Box"
                        box.Size = size
                        box.Anchored = true
                        box.CanCollide = false
                        box.Position = center
                        box.Color = Color3.fromRGB(0, 255, 0)
                        box.Transparency = 0.5
                        espHandles[player] = box
                    else
                        local box = espHandles[player]
                        box.Size = size
                        box.Position = center
                    end
                end
            end
        end
    else
        for _, box in pairs(espHandles) do
            if box and box.Parent then
                box:Destroy()
            end
        end
        espHandles = {}
    end
end

local function createLine()
    local line = Drawing.new("Line")
    line.Thickness = 2
    line.Color = Color3.fromRGB(0, 255, 0)
    line.Visible = true
    return line
end

local R15Connections = {
    {"Head", "UpperTorso"},
    {"UpperTorso", "LowerTorso"},
    {"UpperTorso", "LeftUpperArm"},
    {"LeftUpperArm", "LeftLowerArm"},
    {"LeftLowerArm", "LeftHand"},
    {"UpperTorso", "RightUpperArm"},
    {"RightUpperArm", "RightLowerArm"},
    {"RightLowerArm", "RightHand"},
    {"LowerTorso", "LeftUpperLeg"},
    {"LeftUpperLeg", "LeftLowerLeg"},
    {"LeftLowerLeg", "LeftFoot"},
    {"LowerTorso", "RightUpperLeg"},
    {"RightUpperLeg", "RightLowerLeg"},
    {"RightLowerLeg", "RightFoot"},
}

local R6Connections = {
    {"Head", "Torso"},
    {"Torso", "Left Arm"},
    {"Torso", "Right Arm"},
    {"Torso", "Left Leg"},
    {"Torso", "Right Leg"},
}

local function getRigType(character)
    local humanoid = character:FindFirstChildOfClass("Humanoid")
    if humanoid then
        return humanoid.RigType
    end
    return Enum.HumanoidRigType.R15
end

local function clearSkeleton(player)
    if Skeletons[player] then
        for _, line in pairs(Skeletons[player].Lines) do
            line:Remove()
        end
        Skeletons[player] = nil
    end
end

local function setupSkeleton(player)
    if Skeletons[player] then
        clearSkeleton(player)
    end

    Skeletons[player] = {
        Lines = {},
        RigType = Enum.HumanoidRigType.R15,
        Character = nil
    }

    local function onCharacterAdded(character)
        clearSkeleton(player)

        Skeletons[player] = {
            Lines = {},
            RigType = getRigType(character),
            Character = character
        }

        local connections = (Skeletons[player].RigType == Enum.HumanoidRigType.R6) and R6Connections or R15Connections
        for _ = 1, #connections do
            table.insert(Skeletons[player].Lines, createLine())
        end
    end

    player.CharacterAdded:Connect(onCharacterAdded)

    if player.Character then
        onCharacterAdded(player.Character)
    end
end

for _, player in ipairs(Players:GetPlayers()) do
    if player ~= Player then
        setupSkeleton(player)
    end
end

local playerAddedConnection = Players.PlayerAdded:Connect(function(player)
    if player ~= Player then
        setupSkeleton(player)
    end
end)

local playerRemovingConnection = Players.PlayerRemoving:Connect(function(player)
    clearSkeleton(player)
end)

local renderSteppedConnection = RunService.RenderStepped:Connect(function()
    if skeletonEspEnabled then
        for player, data in pairs(Skeletons) do
            local character = data.Character
            if character and character:FindFirstChild("HumanoidRootPart") then
                local connections = (data.RigType == Enum.HumanoidRigType.R6) and R6Connections or R15Connections

                for i, parts in ipairs(connections) do
                    local part0 = character:FindFirstChild(parts[1])
                    local part1 = character:FindFirstChild(parts[2])

                    local line = data.Lines[i]
                    if part0 and part1 then
                        local pos0, onScreen0 = camera:WorldToViewportPoint(part0.Position)
                        local pos1, onScreen1 = camera:WorldToViewportPoint(part1.Position)

                        if onScreen0 and onScreen1 then
                            line.From = Vector2.new(pos0.X, pos0.Y)
                            line.To = Vector2.new(pos1.X, pos1.Y)
                            line.Visible = true
                        else
                            line.Visible = false
                        end
                    else
                        line.Visible = false
                    end
                end
            else
                for _, line in pairs(data.Lines) do
                    line.Visible = false
                end
            end
        end
    end
    updateBoxESP()
    if tracerEspEnabled then
        for _, player in ipairs(Players:GetPlayers()) do
            if player ~= Player and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
                local targetHRP = player.Character.HumanoidRootPart
                local myHRP = Player.Character and Player.Character:FindFirstChild("HumanoidRootPart")

                if myHRP then
                    if not tracerLines[player] then
                        local line = Drawing.new("Line")
                        line.Color = Color3.fromRGB(255, 255, 255)
                        line.Thickness = 2
                        line.Visible = true
                        tracerLines[player] = line
                    end

                    local line = tracerLines[player]
                    local myScreenPos, myOnScreen = camera:WorldToViewportPoint(myHRP.Position)
                    local targetScreenPos, targetOnScreen = camera:WorldToViewportPoint(targetHRP.Position)

                    if myOnScreen and targetOnScreen then
                        line.From = Vector2.new(myScreenPos.X, myScreenPos.Y)
                        line.To = Vector2.new(targetScreenPos.X, targetScreenPos.Y)
                        line.Visible = true
                    else
                        line.Visible = false
                    end
                else
                    if tracerLines[player] then
                        tracerLines[player]:Remove()
                        tracerLines[player] = nil
                    end
                end
            end
        end
    else
        for _, line in pairs(tracerLines) do
            line.Visible = false
        end
    end
end)

local playerAddedESPConnection = Players.PlayerAdded:Connect(function(newPlayer)
    newPlayer.CharacterAdded:Connect(function(character)
        task.delay(0.5, updateBoxESP)
    end)
end)

local playerRemovingESPConnection = Players.PlayerRemoving:Connect(function(player)
    if espHandles[player] then
        local box = espHandles[player]
        if box and box.Parent then
            box:Destroy()
        end
        espHandles[player] = nil
    end
    if tracerLines[player] then
        tracerLines[player]:Remove()
        tracerLines[player] = nil
    end
end)

local BoxESPToggle = makeToggle(espFrame, 80)
BoxESPToggle.Text = "Box ESP: OFF"
BoxESPToggle.MouseButton1Click:Connect(function()
    playClick()
    boxEspEnabled = not boxEspEnabled
    BoxESPToggle.Text = boxEspEnabled and "Box ESP: ON" or "Box ESP: OFF"
    updateBoxESP()
end)

local SkeletonESPToggle = makeToggle(espFrame, 140)
SkeletonESPToggle.Text = "Skeleton ESP: OFF"
SkeletonESPToggle.MouseButton1Click:Connect(function()
    playClick()
    skeletonEspEnabled = not skeletonEspEnabled
    SkeletonESPToggle.Text = skeletonEspEnabled and "Skeleton ESP: ON" or "Skeleton ESP: OFF"
    if not skeletonEspEnabled then
        for _, playerSkeleton in pairs(Skeletons) do
            if playerSkeleton then
                for _, line in pairs(playerSkeleton.Lines) do
                    line.Visible = false
                end
            end
        end
    end
end)

local TracerESPToggle = makeToggle(espFrame, 200)
TracerESPToggle.Text = "Tracer ESP: OFF"
TracerESPToggle.MouseButton1Click:Connect(function()
    playClick()
    tracerEspEnabled = not tracerEspEnabled
    TracerESPToggle.Text = tracerEspEnabled and "Tracer ESP: ON" or "Tracer ESP: OFF"
end)

--// Settings Tab
do
    local settingsFrame = tabContents["Settings"]
    local toggleKey = Enum.KeyCode.RightShift
    local waiting = false
    local uisConnections = {}

    local ChangeBind = makeToggle(settingsFrame, 80)
    ChangeBind.Size = UDim2.new(0,220,0,40)
    ChangeBind.Text = "Open/Close: [RightShift]"
    ChangeBind.MouseButton1Click:Connect(function()
        playClick()
        waiting = true
        ChangeBind.Text = "Press any key..."
    end)

    local inputBeganConnection = UserInputService.InputBegan:Connect(function(inp, processed)
        if processed then return end
        if waiting and inp.UserInputType==Enum.UserInputType.Keyboard then
            toggleKey = inp.KeyCode
            ChangeBind.Text = "Open/Close: ["..inp.KeyCode.Name.."]"
            waiting = false
            return
        end
        if inp.KeyCode == toggleKey then
            playClick()
            ScreenGui.Enabled = not ScreenGui.Enabled
        end
    end)
    table.insert(uisConnections, inputBeganConnection)

    local ResetBtn = makeToggle(settingsFrame, 140)
    ResetBtn.Name = "ResetGUI"
    ResetBtn.Text = "Reset GUI"
    ResetBtn.MouseButton1Click:Connect(function()
        playClick()
        ScreenGui.Enabled = false
        task.delay(0.5, function() ScreenGui.Enabled = true end)
    end)

    local DestroyBtn = makeToggle(settingsFrame, 200)
    DestroyBtn.Name = "DestroyGUI"
    DestroyBtn.Text = "Destroy GUI"
    DestroyBtn.TextSize = 22
    DestroyBtn.MouseButton1Click:Connect(function()
        playClick()
        aimbotEnabled = false
        silentAimEnabled = false
        teamCheck = false
        boxEspEnabled = false
        skeletonEspEnabled = false
        tracerEspEnabled = false
        noclipEnabled = false
        AimbotToggle.Text = "Aimbot: OFF"
        SilentAimToggle.Text = "Silent Aim: OFF"
        TeamCheckToggle.Text = "Team Check: OFF"
        BoxESPToggle.Text = "Box ESP: OFF"
        SkeletonESPToggle.Text = "Skeleton ESP: OFF"
        TracerESPToggle.Text = "Tracer ESP: OFF"
        NoClipToggle.Text = "No Clip:  OFF"

        if playerAddedConnection then
            playerAddedConnection:Disconnect()
        end
        if playerRemovingConnection then
            playerRemovingConnection:Disconnect()
        end
        if renderSteppedConnection then
            renderSteppedConnection:Disconnect()
        end
        if playerAddedESPConnection then
            playerAddedESPConnection:Disconnect()
        end
        if playerRemovingESPConnection then
            playerRemovingESPConnection:Disconnect()
        end
        if noclipConnection then
            noclipConnection:Disconnect()
        end
        for _, connection in ipairs(uisConnections) do
            connection:Disconnect()
        end
        for _, connection in ipairs(aimbotConnections) do
            connection:Disconnect()
        end
        for _, connection in ipairs(silentAimConnections) do
            connection:Disconnect()
        end

        if FOVCircle then
            FOVCircle:Remove()
        end
        for _, box in pairs(espHandles) do
            if box and box.Parent then
                box:Destroy()
            end
        end
        for _, playerSkeleton in pairs(Skeletons) do
            if playerSkeleton then
                for _, line in pairs(playerSkeleton.Lines) do
                    line:Remove()
                end
            end
        end
        for _, line in pairs(tracerLines) do
            line:Remove()
        end
        ScreenGui:Destroy()
    end)
end

--// Falling Stars Effect
do
    local StarsFolder = Instance.new("Folder", ScreenGui)
    StarsFolder.Name = "StarsFolder"
    local starsConnection = spawn(function()
        while ScreenGui and ScreenGui.Parent do
            task.wait(math.random(0.05,0.15))
            local star = Instance.new("Frame")
            local ms, mp = MainFrame.AbsoluteSize, MainFrame.AbsolutePosition
            star.Size          = UDim2.new(0,math.random(2,4),0,math.random(2,4))
            star.Position          = UDim2.new(0,math.random(mp.X,mp.X+ms.X),0,mp.Y-10)
            star.BackgroundColor3 = Color3.fromRGB(170,0,255)
            star.BorderSizePixel  = 0
            star.Parent          = StarsFolder
            local goalPos = UDim2.new(0,star.Position.X.Offset,0,mp.Y+ms.Y+10)
            local tween = TweenService:Create(star, TweenInfo.new(3,Enum.EasingStyle.Quad,Enum.EasingDirection.Out), {
                Position = goalPos,
                BackgroundTransparency = 1,
            })
            tween:Play()
            tween.Completed:Connect(function() star:Destroy() end)
        end
    end)
end

--// AIMBOT & SILENT AIM LOGIC
local aimRadius = 150
local FOVCircle = Drawing.new("Circle")
FOVCircle.Color       = Color3.fromRGB(0,170,255)
FOVCircle.Thickness   = 2
FOVCircle.Filled      = false
FOVCircle.Transparency= 0.7
FOVCircle.Radius      = aimRadius
FOVCircle.Visible     = false

local aiming = false
local lockedTarget = nil
local mouseBeganConnection = UserInputService.InputBegan:Connect(function(input, processed)
    if processed then return end
    if input.UserInputType == Enum.UserInputType.MouseButton2 then
        aiming = true
        lockedTarget = getClosestTarget()
    end
end)
table.insert(aimbotConnections, mouseBeganConnection)

local mouseEndedConnection = UserInputService.InputEnded:Connect(function(input, processed)
    if processed then return end
    if input.UserInputType == Enum.UserInputType.MouseButton2 then
        aiming = false
        lockedTarget = nil
    end
end)
table.insert(aimbotConnections, mouseEndedConnection)

local function getClosestTarget()
    local bestDist, bestPl = math.huge, nil
    local mx, my = UserInputService:GetMouseLocation().X, UserInputService:GetMouseLocation().Y
    for _, pl in ipairs(Players:GetPlayers()) do
        if pl ~= Player and pl.Character and pl.Character:FindFirstChild("HumanoidRootPart") then
            if not teamCheck or (teamCheck and pl.Team ~= Player.Team) then
                local root = pl.Character.HumanoidRootPart
                local screenPos, onScreen = camera:WorldToScreenPoint(root.Position)
                if onScreen then
                    local dist = (Vector2.new(screenPos.X, screenPos.Y) - Vector2.new(mx, my)).Magnitude
                    if dist < bestDist and dist <= aimRadius then
                        bestDist, bestPl = dist, pl
                    end
                end
            end
        end
    end
    return bestPl
end

local aimRenderSteppedConnection = RunService.RenderStepped:Connect(function()
    local mx, my = UserInputService:GetMouseLocation().X, UserInputService:GetMouseLocation().Y
    FOVCircle.Position = Vector2.new(mx, my)
    FOVCircle.Visible   = aimbotEnabled

    if aiming and aimbotEnabled then
        if lockedTarget and lockedTarget.Character then
            local hrp = lockedTarget.Character:FindFirstChild("HumanoidRootPart")
            if hrp then
                camera.CFrame = CFrame.new(camera.CFrame.Position, hrp.Position)
            end
        elseif lockedTarget == nil then
            local target = getClosestTarget()
            if target and target.Character then
                local hrp = target.Character:FindFirstChild("HumanoidRootPart")
                if hrp then
                    camera.CFrame = CFrame.new(camera.CFrame.Position, hrp.Position)
                end
            end
        end
    end
end)
table.insert(aimbotConnections, aimRenderSteppedConnection)

local __namecall
local nameCallConnection = hookmetamethod(game, "__namecall", function(self, ...)
    local method = getnamecallmethod()
    if not checkcaller() and silentAimEnabled and method == "FireServer" then
        local args = {...}
        if tostring(self):lower():find("shoot") then
            local tgt = getClosestTarget()
            if tgt and tgt.Character then
                if not teamCheck or (teamCheck and tgt.Team ~= Player.Team) then
                    local hrp = tgt.Character:FindFirstChild("HumanoidRootPart")
                    if hrp and typeof(args[2]) == "Vector3" then
                        args[2] = hrp.Position
                    end
                end
            end
            return self.FireServer(self, unpack(args))
        end
    end
    return __namecall(self, ...)
end)
table.insert(silentAimConnections, nameCallConnection)

--// Character Respawn Handling
Player.CharacterAdded:Connect(function(newCharacter)
    Character = newCharacter
    Humanoid = newCharacter:WaitForChild("Humanoid")
    if noclipEnabled and noclipConnection == nil then
        noclipConnection = RunService.Stepped:Connect(function()
            if Character then
                for _, part in ipairs(Character:GetDescendants()) do
                    if part:IsA("BasePart") then
                        part.CanCollide = false
                    end
                end
            end
        end)
    end
end)

-- Run as a LocalScript (for example, in StarterPlayerScripts).
-- LeftAngle and RightAngle are exposed as ScreenGui attributes and CODE.lua globals.
-- The optional hook requires hookmetamethod/newcclosure/getnamecallmethod/setnamecallmethod.
_G.Kuy = true
local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local TweenService = game:GetService("TweenService")
local player = Players.LocalPlayer
assert(player, "Never.lua must run on the client as a LocalScript")
local playerGui = player:WaitForChild("PlayerGui")
local old = playerGui:FindFirstChild("NeverAngleUI")
if old then old:Destroy() end

local accent = Color3.fromRGB(230, 230, 230)
local white = Color3.fromRGB(243, 243, 243)
local muted = Color3.fromRGB(135, 135, 135)
local connections = {}
local function connect(signal, callback)
    local connection = signal:Connect(callback)
    table.insert(connections, connection)
    return connection
end
local function create(class, props, parent)
    local instance = Instance.new(class)
    for key, value in pairs(props) do instance[key] = value end
    instance.Parent = parent
    return instance
end
local function round(frame, radius)
    create("UICorner", {CornerRadius = UDim.new(0, radius or 999)}, frame)
end
local function stroke(frame, color, width, transparency)
    return create("UIStroke", {Color = color, Thickness = width or 1, Transparency = transparency or 0, ApplyStrokeMode = Enum.ApplyStrokeMode.Border}, frame)
end
local function text(parent, content, position, size, fontSize, color, z)
    return create("TextLabel", {
        BackgroundTransparency = 1, Text = content, Position = position, Size = size,
        Font = Enum.Font.Roboto, TextSize = fontSize, TextColor3 = color or white,
        ZIndex = z or 5,
    }, parent)
end
local gui = create("ScreenGui", {
    Name = "NeverAngleUI", ResetOnSpawn = false, IgnoreGuiInset = true,
    ZIndexBehavior = Enum.ZIndexBehavior.Sibling, DisplayOrder = 50,
}, playerGui)
local panel = create("Frame", {
    Name = "Panel", AnchorPoint = Vector2.new(0.5, 0.5), Position = UDim2.fromScale(0.5, 0.5),
    Size = UDim2.fromOffset(440, 582), BackgroundColor3 = Color3.fromRGB(15, 15, 15),
    BorderSizePixel = 0, ClipsDescendants = true,
}, gui)
round(panel, 18)
stroke(panel, Color3.fromRGB(58, 58, 58))
create("UIGradient", {
    Color = ColorSequence.new({
        ColorSequenceKeypoint.new(0, Color3.fromRGB(255, 255, 255)),
        ColorSequenceKeypoint.new(0.35, Color3.fromRGB(200, 200, 200)),
        ColorSequenceKeypoint.new(1, Color3.fromRGB(150, 150, 150)),
    }), Rotation = 65,
}, panel)
local uiScale = create("UIScale", {Scale = 1}, panel)
local header = create("Frame", {
    ZIndex = 2, Size = UDim2.new(1, 0, 0, 52), BackgroundColor3 = Color3.fromRGB(17, 17, 17),
    BackgroundTransparency = 0.2, BorderSizePixel = 0,
}, panel)
text(header, "✦", UDim2.fromOffset(17, 10), UDim2.fromOffset(28, 30), 25, accent)
local title = text(header, "NEVER / ANGLE", UDim2.fromOffset(52, 10), UDim2.fromOffset(225, 30), 16)
title.TextXAlignment = Enum.TextXAlignment.Left
title.Font = Enum.Font.SciFi
local function button(caption, position, size)
    local b = create("TextButton", {
        Text = caption, Position = position, Size = size, Font = Enum.Font.SciFi,
        TextSize = 11, TextColor3 = accent, BackgroundColor3 = Color3.fromRGB(30, 30, 30),
        BorderSizePixel = 0, AutoButtonColor = false, ZIndex = 10,
    }, header)
    round(b, 6)
    stroke(b, Color3.fromRGB(63, 63, 63))
    return b
end
local reset = button("RESET", UDim2.fromOffset(286, 14), UDim2.fromOffset(65, 25))
local close = button("HIDE", UDim2.fromOffset(360, 14), UDim2.fromOffset(62, 25))

local canvas = create("Frame", {
    Name = "AngleCircle", BackgroundTransparency = 1, ZIndex = 3,
    AnchorPoint = Vector2.new(0.5, 0.5), Position = UDim2.fromOffset(220, 190), Size = UDim2.fromOffset(240, 240),
}, panel)
local ring = create("Frame", {
    AnchorPoint = Vector2.new(0.5, 0.5), Position = UDim2.fromScale(0.5, 0.5),
    Size = UDim2.fromOffset(208, 208), BackgroundColor3 = Color3.fromRGB(12, 12, 12),
    BackgroundTransparency = 0.35, BorderSizePixel = 0, ZIndex = 2,
}, canvas)
round(ring)
stroke(ring, Color3.fromRGB(37, 37, 37), 2)
text(canvas, "0°", UDim2.fromOffset(100, -5), UDim2.fromOffset(40, 20), 11, muted)
text(canvas, "180°", UDim2.fromOffset(92, 225), UDim2.fromOffset(56, 20), 11, muted)
text(canvas, "90°", UDim2.fromOffset(-29, 109), UDim2.fromOffset(42, 20), 11, muted)
text(canvas, "90°", UDim2.fromOffset(227, 109), UDim2.fromOffset(42, 20), 11, muted)
text(canvas, "FRONT", UDim2.fromOffset(80, 28), UDim2.fromOffset(80, 20), 10, muted)
text(canvas, "▲", UDim2.fromOffset(108, 45), UDim2.fromOffset(24, 16), 12, Color3.fromRGB(174, 174, 174))
text(canvas, "L", UDim2.fromOffset(29, 113), UDim2.fromOffset(20, 20), 12, Color3.fromRGB(177, 177, 177))
text(canvas, "R", UDim2.fromOffset(191, 113), UDim2.fromOffset(20, 20), 12, Color3.fromRGB(163, 163, 163))

-- A layered top-down human head and shoulders, drawn without external assets.
local function ellipse(name, x, y, w, h, color, z)
    local shape = create("Frame", {
        Name = name, AnchorPoint = Vector2.new(0.5, 0.5), Position = UDim2.fromOffset(x, y),
        Size = UDim2.fromOffset(w, h), BackgroundColor3 = color, BorderSizePixel = 0, ZIndex = z,
    }, canvas)
    round(shape)
    return shape
end
ellipse("BodyShadow", 120, 142, 120, 47, Color3.fromRGB(23, 23, 23), 3)
ellipse("Shoulders", 120, 132, 116, 30, Color3.fromRGB(54, 54, 54), 3)
ellipse("Torso", 120, 144, 86, 39, Color3.fromRGB(39, 39, 39), 3)
ellipse("LeftArm", 65, 127, 25, 19, Color3.fromRGB(60, 60, 60), 3)
ellipse("RightArm", 175, 127, 25, 19, Color3.fromRGB(60, 60, 60), 3)
ellipse("Neck", 120, 128, 30, 25, Color3.fromRGB(74, 74, 74), 4)
ellipse("LeftEar", 96, 109, 10, 19, Color3.fromRGB(86, 86, 86), 4)
ellipse("RightEar", 144, 109, 10, 19, Color3.fromRGB(86, 86, 86), 4)
ellipse("HeadOutline", 120, 107, 49, 60, Color3.fromRGB(55, 55, 55), 5)
ellipse("Head", 120, 104, 40, 50, Color3.fromRGB(85, 85, 85), 5)
ellipse("HeadHighlight", 117, 98, 28, 34, Color3.fromRGB(102, 102, 102), 6)
ellipse("Crown", 115, 93, 18, 22, Color3.fromRGB(114, 114, 114), 6)

local radius = 104
local function point(angle, side)
    local radians = math.rad(angle)
    return Vector2.new(120 + side * math.sin(radians) * radius, 120 - math.cos(radians) * radius)
end
local segments = {Left = {}, Right = {}}
local segmentStep = 5
local function segmentGeometry(startAngle, endAngle, side)
    local a, b = point(startAngle, side), point(endAngle, side)
    local midpoint, delta = (a + b) / 2, b - a
    return UDim2.fromOffset(midpoint.X, midpoint.Y), math.deg(math.atan2(delta.Y, delta.X)),
        UDim2.fromOffset(delta.Magnitude + 0.6, 3)
end
for _, sideName in ipairs({"Left", "Right"}) do
    local side = sideName == "Left" and -1 or 1
    for index = 1, 180 / segmentStep do
        local startAngle, endAngle = (index - 1) * segmentStep, index * segmentStep
        local position, rotation, size = segmentGeometry(startAngle, endAngle, side)
        local line = create("Frame", {
            AnchorPoint = Vector2.new(0.5, 0.5), BorderSizePixel = 0,
            BackgroundColor3 = accent, ZIndex = 5, Visible = false,
            Position = position, Rotation = rotation, Size = size,
        }, canvas)
        segments[sideName][index] = {line = line, startAngle = startAngle, endAngle = endAngle,
            position = position, rotation = rotation, size = size, visible = false, partial = false}
    end
end
local handles = {}
for _, sideName in ipairs({"Left", "Right"}) do
    local handle = create("TextButton", {
        Name = sideName .. "Handle", Text = "", AnchorPoint = Vector2.new(0.5, 0.5),
        Size = UDim2.fromOffset(20, 20), BackgroundColor3 = white,
        BorderSizePixel = 0, ZIndex = 9, AutoButtonColor = false,
    }, canvas)
    round(handle)
    stroke(handle, Color3.fromRGB(77, 77, 77), 3)
    handles[sideName] = handle
end
local summary = text(panel, "", UDim2.fromOffset(20, 320), UDim2.fromOffset(400, 28), 20)
summary.Font = Enum.Font.SciFi
text(panel, "TARGET FRONT · LEFT / RIGHT LINKED", UDim2.fromOffset(20, 349), UDim2.fromOffset(400, 22), 10, muted)

local sliders = {}
for index, sideName in ipairs({"Left", "Right"}) do
    local row = create("Frame", {
        Name = sideName .. "Card", ZIndex = 4, BackgroundColor3 = Color3.fromRGB(19, 19, 19), BackgroundTransparency = 0.12, Position = UDim2.fromOffset(30, 382 + (index - 1) * 53),
        Size = UDim2.fromOffset(380, 48),
    }, panel)
    round(row, 8)
    local label = text(row, string.upper(sideName), UDim2.fromOffset(0, 0), UDim2.fromOffset(80, 18), 11, muted)
    label.TextXAlignment = Enum.TextXAlignment.Left
    local value = text(row, "", UDim2.fromOffset(295, 0), UDim2.fromOffset(85, 18), 12, accent)
    value.TextXAlignment = Enum.TextXAlignment.Right
    local hit = create("TextButton", {
        Name = sideName .. "Slider", Text = "", BackgroundTransparency = 1,
        Position = UDim2.fromOffset(0, 19), Size = UDim2.fromOffset(380, 26), ZIndex = 8,
    }, row)
    local track = create("Frame", {
        Position = UDim2.fromOffset(0, 10), Size = UDim2.new(1, 0, 0, 5),
        BackgroundColor3 = Color3.fromRGB(47, 47, 47), BorderSizePixel = 0, ZIndex = 8,
    }, hit)
    round(track)
    local fill = create("Frame", {Size = UDim2.fromScale(0, 1), BackgroundColor3 = accent, BorderSizePixel = 0, ZIndex = 8}, track)
    round(fill)
    local knob = create("Frame", {
        AnchorPoint = Vector2.new(0.5, 0.5), Position = UDim2.fromScale(0, 0.5),
        Size = UDim2.fromOffset(13, 13), BackgroundColor3 = white, BorderSizePixel = 0, ZIndex = 9,
    }, track)
    round(knob)
    text(row, "0°", UDim2.fromOffset(0, 39), UDim2.fromOffset(24, 12), 9, muted)
    text(row, "180°", UDim2.fromOffset(346, 39), UDim2.fromOffset(34, 12), 9, muted)
    sliders[sideName] = {row = row, hit = hit, track = track, fill = fill, knob = knob, value = value}
end

local angles = {Left = 59, Right = 59}
-- Preserve an explicit external disable; enable the merged behavior by default.
if _G.Kuy == nil then _G.Kuy = true end
local hookState
local targetNames
local function targetRoot(value)
    if typeof(value) ~= "Instance" then return nil end
    if value:IsA("Player") then value = value.Character end
    if not value then return nil end
    local current = value
    while current and current ~= workspace do
        if current:IsA("Model") then
            local root = current:FindFirstChild("HumanoidRootPart") or current.PrimaryPart
            if root and root:IsA("BasePart") then return root end
        end
        current = current.Parent
    end
    if value:IsA("BasePart") then return value end
    return nil
end
-- Build the name index once, on demand; never scan the entire workspace per hit.
local function indexTargets()
    targetNames = {}
    local function add(instance)
        if not instance:IsA("BasePart") and not instance:IsA("Model") then return end
        local name = instance.Name
        local bucket = targetNames[name]
        if not bucket then bucket = {}; targetNames[name] = bucket end
        bucket[instance] = true
    end
    connect(workspace.DescendantAdded, add)
    connect(workspace.DescendantRemoving, function(instance)
        local bucket = targetNames[instance.Name]
        if bucket then
            bucket[instance] = nil
            if next(bucket) == nil then targetNames[instance.Name] = nil end
        end
    end)
    for _, instance in ipairs(workspace:GetDescendants()) do add(instance) end
end
local function resolveTarget(value)
    if typeof(value) == "Instance" then return targetRoot(value) end
    if type(value) ~= "string" then return nil end
    local targetPlayer = Players:FindFirstChild(value)
    if targetPlayer and targetPlayer:IsA("Player") and targetPlayer ~= player then
        return targetRoot(targetPlayer)
    end
    if not targetNames then indexTargets() end
    local bucket = targetNames[value]
    if not bucket then return nil end
    local found
    for instance in pairs(bucket) do
        if instance.Name == value and instance:IsDescendantOf(workspace)
            and not (player.Character and instance:IsDescendantOf(player.Character)) then
            local root = targetRoot(instance)
            if root then
                -- A plain part name must identify one target, not an arbitrary first match.
                if found and found ~= root then return nil end
                found = root
            end
        end
    end
    return found
end
local hitParts = {
    Head = true, HumanoidRootPart = true, Torso = true, UpperTorso = true, LowerTorso = true,
    ["Left Arm"] = true, ["Right Arm"] = true, ["Left Leg"] = true, ["Right Leg"] = true,
    LeftUpperArm = true, LeftLowerArm = true, LeftHand = true,
    RightUpperArm = true, RightLowerArm = true, RightHand = true,
    LeftUpperLeg = true, LeftLowerLeg = true, LeftFoot = true,
    RightUpperLeg = true, RightLowerLeg = true, RightFoot = true,
}
local function shouldUseHead(target, hitPart)
    if angles.Left + angles.Right <= 10 then return false end
    if type(hitPart) ~= "string" or not hitParts[hitPart] then return false end
    local root = resolveTarget(target)
    local ownRoot = player.Character and player.Character:FindFirstChild("HumanoidRootPart")
    if not root or not ownRoot or root == ownRoot or not root:IsDescendantOf(workspace) then return false end
    local offset = ownRoot.Position - root.Position
    local direction = Vector3.new(offset.X, 0, offset.Z)
    local look = root.CFrame.LookVector
    local forward = Vector3.new(look.X, 0, look.Z)
    if direction.Magnitude < 0.001 or forward.Magnitude < 0.001 then return false end
    direction, forward = direction.Unit, forward.Unit
    local right = Vector3.new(-forward.Z, 0, forward.X)
    -- Scalar dot products avoid nested :Dot namecalls inside the RemoteEvent hook.
    local lateral = direction.X * right.X + direction.Z * right.Z
    local frontal = direction.X * forward.X + direction.Z * forward.Z
    local signedAngle = math.deg(math.atan2(lateral, frontal))
    return signedAngle >= -angles.Left - 0.0001 and signedAngle <= angles.Right + 0.0001
end
local function bindCodeHook()
    if type(hookmetamethod) ~= "function" or type(newcclosure) ~= "function"
        or type(getnamecallmethod) ~= "function" or type(setnamecallmethod) ~= "function" then
        gui:SetAttribute("CodeHookAvailable", false)
        warn("Never: required hook functions (including setnamecallmethod) are unavailable; the angle UI remains usable.")
        return
    end
    -- Reuse one hook across reruns; its state follows the newest UI.
    hookState = _G.NeverAngleCodeHook
    if type(hookState) == "table" and hookState.version ~= 5 then
        -- Disable older hooks so their method handling cannot affect this revision.
        hookState.gui = nil
        hookState = nil
    end
    if type(hookState) ~= "table" then
        hookState = {version = 5}
        _G.NeverAngleCodeHook = hookState
    end
    hookState.gui = gui
    hookState.angles = angles
    hookState.shouldUseHead = shouldUseHead
    if not hookState.installed then
        local state = hookState
        local old
        old = hookmetamethod(game, "__namecall", newcclosure(function(self, ...)
            local method = getnamecallmethod()
            -- Include explicit Event:FireServer calls from user scripts as well as game calls.
            -- Helpers never call FireServer, so nested lookups cannot re-enter this branch.
            if state.gui and _G.Kuy and method == "FireServer" then
                local target, hitPart = ...
                local useHead = false
                if target ~= "Use" then
                    local ok, result = pcall(state.shouldUseHead, target, hitPart)
                    useHead = ok and result == true
                end
                -- Target lookup calls Instance methods. Restore FireServer after those
                -- nested namecalls, including when lookup fails or no rewrite is needed.
                if useHead then
                    local Args = table.pack(...)
                    Args[2] = "Head"
                    print("Arg 1:", Args[1])
                    print("Arg 2:", Args[2])
                    setnamecallmethod(method)
                    -- Keep nil arguments and the original argument count intact.
                    return old(self, unpack(Args, 1, Args.n))
                end
                setnamecallmethod(method)
            end
            return old(self, ...)
        end))
        hookState.installed = true
    end
    gui:SetAttribute("CodeHookAvailable", true)
end
local renderDirty = false
local function publishAngles()
    _G.Left = angles.Left
    _G.right = angles.Right
    local total = angles.Left + angles.Right
    gui:SetAttribute("LeftAngle", angles.Left)
    gui:SetAttribute("RightAngle", angles.Right)
    gui:SetAttribute("TotalAngle", total)
    gui:SetAttribute("HeadSectorEnabled", total > 10)
end
local function render()
    for _, sideName in ipairs({"Left", "Right"}) do
        local side = sideName == "Left" and -1 or 1
        local angle = angles[sideName]
        for _, segment in ipairs(segments[sideName]) do
            local visible = angle > segment.startAngle
            if visible ~= segment.visible then
                segment.line.Visible = visible
                segment.visible = visible
            end
            if visible and angle < segment.endAngle then
                local position, rotation, size = segmentGeometry(segment.startAngle, angle, side)
                segment.line.Position = position
                segment.line.Rotation = rotation
                segment.line.Size = size
                segment.partial = true
            elseif segment.partial then
                segment.line.Position = segment.position
                segment.line.Rotation = segment.rotation
                segment.line.Size = segment.size
                segment.partial = false
            end
        end
        local endpoint = point(angle, side)
        -- Keep both controls accessible when both endpoints meet at 0 or 180.
        if angles.Left == angles.Right and (angle == 0 or angle == 180) then
            endpoint = endpoint + Vector2.new(side * 10, 0)
        end
        handles[sideName].Position = UDim2.fromOffset(endpoint.X, endpoint.Y)
        local slider = sliders[sideName]
        slider.fill.Size = UDim2.fromScale(angle / 180, 1)
        slider.knob.Position = UDim2.fromScale(angle / 180, 0.5)
        slider.value.Text = string.format("%d°", angles[sideName])
    end
    local total = angles.Left + angles.Right
    summary.Text = string.format("%d°  ·  %d%%", total, math.floor(total / 360 * 100 + 0.5))
end
-- Both controls update the same angle, keeping the UI and hook sides synchronized.
local function setAngle(value)
    local angle = math.clamp(math.floor(value + 0.5), 0, 180)
    if angles.Left == angle and angles.Right == angle then return end
    angles.Left = angle
    angles.Right = angle
    publishAngles()
    renderDirty = true
end
local drag = nil
local function updateDrag(position)
    if not drag then return end
    if drag.kind == "slider" then
        local track = sliders[drag.side].track
        if track.AbsoluteSize.X > 0 then
            setAngle((position.X - track.AbsolutePosition.X) / track.AbsoluteSize.X * 180)
        end
    else
        local center = canvas.AbsolutePosition + canvas.AbsoluteSize / 2
        local delta = Vector2.new(position.X, position.Y) - center
        if delta.Magnitude < 3 then return end
        local side = drag.side == "Left" and -1 or 1
        -- Clamp at the front/back axis when dragging across the other half.
        local lateral = math.max(0, delta.X * side)
        setAngle(math.deg(math.atan2(lateral, -delta.Y)))
    end
end
local function beginDrag(input, sideName, kind)
    if not panel.Visible then return end
    if input.UserInputType ~= Enum.UserInputType.MouseButton1 and input.UserInputType ~= Enum.UserInputType.Touch then return end
    drag = {side = sideName, kind = kind, input = input, touch = input.UserInputType == Enum.UserInputType.Touch}
    updateDrag(input.Position)
end
for _, sideName in ipairs({"Left", "Right"}) do
    local selectedSide = sideName
    connect(handles[selectedSide].InputBegan, function(input) beginDrag(input, selectedSide, "circle") end)
    connect(sliders[selectedSide].hit.InputBegan, function(input) beginDrag(input, selectedSide, "slider") end)
end
connect(UserInputService.InputChanged, function(input)
    if not drag then return end
    if (drag.touch and input == drag.input) or (not drag.touch and input.UserInputType == Enum.UserInputType.MouseMovement) then
        updateDrag(input.Position)
    end
end)
connect(UserInputService.InputEnded, function(input)
    if drag and ((drag.touch and input == drag.input) or (not drag.touch and input.UserInputType == Enum.UserInputType.MouseButton1)) then
        drag = nil
    end
end)
connect(UserInputService.WindowFocusReleased, function() drag = nil end)
connect(reset.Activated, function()
    drag = nil
    setAngle(59)
end)
-- HIDE uses the same reversible toggle as the keyboard shortcut.
local cameraConnection
local baseScale = 1
local isShown = true
local function resize()
    local camera = workspace.CurrentCamera
    if camera then
        local viewport = camera.ViewportSize
        baseScale = math.max(0.1, math.min(1, (viewport.X - 24) / 440, (viewport.Y - 24) / 582))
        uiScale.Scale = baseScale
    end
end
local function bindCamera()
    if cameraConnection then cameraConnection:Disconnect() end
    if workspace.CurrentCamera then
        cameraConnection = workspace.CurrentCamera:GetPropertyChangedSignal("ViewportSize"):Connect(resize)
    end
    resize()
end
connect(workspace:GetPropertyChangedSignal("CurrentCamera"), bindCamera)

-- Shared easing: short transitions with cancellation when the pointer changes direction.
local activeTweens = {}
local function animate(object, props, duration)
    local previous = activeTweens[object]
    if previous then previous:Cancel() end
    local tween = TweenService:Create(object, TweenInfo.new(duration or 0.28, Enum.EasingStyle.Quart, Enum.EasingDirection.Out), props)
    activeTweens[object] = tween
    tween:Play()
    return tween
end
-- Hover feedback runs only on enter/leave; no frame loop or cursor tracking.
local function interactive(frame)
    local border = frame:FindFirstChildOfClass("UIStroke") or stroke(frame, Color3.fromRGB(112, 112, 112), 1, 0.72)
    local restingTransparency = border.Transparency
    local restingColor = frame.BackgroundColor3
    connect(frame.MouseEnter, function()
        animate(border, {Transparency = 0.15}, 0.12)
        if frame:IsA("TextButton") then animate(frame, {BackgroundColor3 = Color3.fromRGB(61, 61, 61)}, 0.12) end
    end)
    connect(frame.MouseLeave, function()
        animate(border, {Transparency = restingTransparency}, 0.12)
        if frame:IsA("TextButton") then animate(frame, {BackgroundColor3 = restingColor}, 0.12) end
    end)
end
interactive(panel)
round(canvas, 12)
interactive(canvas)
interactive(reset)
interactive(close)
for _, sideName in ipairs({"Left", "Right"}) do
    interactive(sliders[sideName].row)
    interactive(handles[sideName])
    sliders[sideName].value.Font = Enum.Font.SciFi
end

-- Static background keeps the UI idle until the user interacts.
create("Frame", {Position = UDim2.fromOffset(24, 510), Size = UDim2.fromOffset(392, 1),
    BackgroundColor3 = Color3.fromRGB(67, 67, 67), BackgroundTransparency = 0.4, BorderSizePixel = 0, ZIndex = 3}, panel)
local keyLabel = text(panel, "คีย์ลัดซ่อน / เปิด", UDim2.fromOffset(28, 520), UDim2.fromOffset(205, 25), 13, muted)
keyLabel.TextXAlignment = Enum.TextXAlignment.Left
local keyButton = create("TextButton", {
    Name = "KeybindButton", Position = UDim2.fromOffset(257, 518), Size = UDim2.fromOffset(155, 29),
    BackgroundColor3 = Color3.fromRGB(31, 31, 31), TextColor3 = white,
    TextSize = 11, Font = Enum.Font.SciFi, AutoButtonColor = false, BorderSizePixel = 0, ZIndex = 10,
}, panel)
round(keyButton, 6)
interactive(keyButton)
local footerHint = "ลากแถบบนเพื่อย้าย • มุมซ้าย / ขวาขยับพร้อมกัน"
local footer = text(panel, footerHint, UDim2.fromOffset(20, 552), UDim2.fromOffset(400, 18), 11, muted)
local reopen = create("TextButton", {
    Name = "Reopen", AnchorPoint = Vector2.new(0, 1), Position = UDim2.new(0, 18, 1, -18),
    Size = UDim2.fromOffset(148, 38), Text = "NEVER / OPEN", Visible = false,
    BackgroundColor3 = Color3.fromRGB(20, 20, 20), TextColor3 = white,
    TextSize = 12, Font = Enum.Font.SciFi, BorderSizePixel = 0, AutoButtonColor = false, ZIndex = 20,
}, gui)
round(reopen, 8)
interactive(reopen)
local toggleKey = Enum.KeyCode.RightControl
local capturingKey = false
local windowDrag = nil
local function refreshKey()
    keyButton.Text = "KEY / " .. toggleKey.Name
    gui:SetAttribute("ToggleKey", toggleKey.Name)
end
local function setVisible(visible)
    isShown = visible
    drag, windowDrag = nil, nil
    capturingKey = false
    refreshKey()
    footer.Text = footerHint
    gui:SetAttribute("UIVisible", visible)
    reopen.Visible = not visible
    panel.Visible = visible
    uiScale.Scale = baseScale
end
connect(close.Activated, function() setVisible(false) end)
connect(reopen.Activated, function() setVisible(true) end)
connect(keyButton.Activated, function()
    capturingKey = not capturingKey
    if capturingKey then
        keyButton.Text = "PRESS KEY..."
        footer.Text = "กดคีย์ที่ต้องการ • กด Esc เพื่อยกเลิก"
    else
        refreshKey()
        footer.Text = footerHint
    end
end)
connect(UserInputService.InputBegan, function(input, processed)
    if input.UserInputType ~= Enum.UserInputType.Keyboard or UserInputService:GetFocusedTextBox() then return end
    if capturingKey then
        if input.KeyCode ~= Enum.KeyCode.Escape and input.KeyCode ~= Enum.KeyCode.Unknown then toggleKey = input.KeyCode end
        capturingKey = false
        refreshKey()
        footer.Text = footerHint
        return
    end
    if not processed and input.KeyCode == toggleKey then setVisible(not isShown) end
end)

-- Only the title region starts a window drag; buttons and angle controls remain independent.
local titleDrag = create("Frame", {Name = "DragArea", Size = UDim2.fromOffset(276, 52),
    BackgroundTransparency = 1, Active = true, ZIndex = 12}, header)
local function clampWindow(center)
    local camera = workspace.CurrentCamera
    if not camera then return center end
    local viewport = camera.ViewportSize
    local half = Vector2.new(440, 582) * baseScale / 2
    return Vector2.new(math.clamp(center.X, half.X + 6, math.max(half.X + 6, viewport.X - half.X - 6)),
        math.clamp(center.Y, half.Y + 6, math.max(half.Y + 6, viewport.Y - half.Y - 6)))
end
connect(titleDrag.InputBegan, function(input)
    if not isShown or (input.UserInputType ~= Enum.UserInputType.MouseButton1 and input.UserInputType ~= Enum.UserInputType.Touch) then return end
    local center = panel.AbsolutePosition + panel.AbsoluteSize / 2
    windowDrag = {input = input, start = Vector2.new(input.Position.X, input.Position.Y), center = center,
        touch = input.UserInputType == Enum.UserInputType.Touch}
end)
connect(UserInputService.InputChanged, function(input)
    if not windowDrag then return end
    if (windowDrag.touch and input == windowDrag.input) or (not windowDrag.touch and input.UserInputType == Enum.UserInputType.MouseMovement) then
        local center = clampWindow(windowDrag.center + Vector2.new(input.Position.X, input.Position.Y) - windowDrag.start)
        panel.Position = UDim2.fromOffset(center.X, center.Y)
    end
end)
connect(UserInputService.InputEnded, function(input)
    if windowDrag and ((windowDrag.touch and input == windowDrag.input) or (not windowDrag.touch and input.UserInputType == Enum.UserInputType.MouseButton1)) then windowDrag = nil end
end)
connect(UserInputService.WindowFocusReleased, function() windowDrag = nil end)
connect(panel:GetPropertyChangedSignal("AbsoluteSize"), function()
    if windowDrag then return end
    local center = panel.AbsolutePosition + panel.AbsoluteSize / 2
    local bounded = clampWindow(center)
    if (center - bounded).Magnitude > 1 then panel.Position = UDim2.fromOffset(bounded.X, bounded.Y) end
end)
refreshKey()
gui:SetAttribute("UIVisible", true)

local uiWorker
local uiAlive = true
connect(gui.Destroying, function()
    uiAlive = false
    if uiWorker then task.cancel(uiWorker); uiWorker = nil end
    drag = nil
    if hookState and hookState.gui == gui then
        hookState.gui = nil
        hookState.angles = nil
        hookState.shouldUseHead = nil
    end
    if cameraConnection then cameraConnection:Disconnect() end
    for _, tween in pairs(activeTweens) do tween:Cancel() end
    for _, connection in ipairs(connections) do connection:Disconnect() end
end)
publishAngles()
render()
bindCamera()
bindCodeHook()

-- Batch input bursts into at most 30 redraws per second. No redraw while idle/hidden.
uiWorker = task.spawn(function()
    local updateInterval = 1 / 30
    local warned = false
    local function reportError(message)
        if not warned then
            warned = true
            warn("Never UI update: " .. tostring(message))
        end
    end
    while task.wait(updateInterval) do
        if not uiAlive then break end
        if renderDirty and panel.Visible then
            local ok = xpcall(function()
                render()
                renderDirty = false
            end, reportError)
            if ok then
                warned = false
                updateInterval = 1 / 30
            else
                -- Avoid a fast error/retry loop if a UI update cannot complete.
                updateInterval = 0.5
            end
        end
    end
end)

--[[
    ⬡ BIANN SUNSET SHADER v3.2 ⬡
    Smooth UI · Fly · Speed · Jump · Teleport · Noclip · Infinite Jump
    + Rain · Wet Ground · Storm Mode · Lightning
    Discord: https://discord.gg/N38HzXn2E
    User: BiannXz KalxX · N349
]]

-- === SERVICES ===
local Lighting = game:GetService("Lighting")
local Players = game:GetService("Players")
local CoreGui = game:GetService("CoreGui")
local TweenService = game:GetService("TweenService")
local UserInputService = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local StarterGui = game:GetService("StarterGui")
local TeleportService = game:GetService("TeleportService")
local GuiService = game:GetService("GuiService")
local VirtualUser = game:GetService("VirtualUser")

local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera

-- === CLEANUP ===
pcall(function()
    for _, v in pairs(Lighting:GetChildren()) do
        if v.Name:find("BiannSunset") or v.Name:find("BiannRain") or v.Name:find("BiannStorm") then v:Destroy() end
    end
    if CoreGui:FindFirstChild("BiannSunsetGUI") then
        CoreGui:FindFirstChild("BiannSunsetGUI"):Destroy()
    end
end)

-- === CONFIG ===
local Config = {
    Multiplier = 1,
    AntiAFK = false,
    SunsetOn = false,
    RainOn = false,
    StormOn = false,
    FlySpeed = 100,
    WalkSpeed = 16,
    JumpPower = 50,
    FlyEnabled = false,
    Noclip = false,
    InfiniteJump = false,
}

local DiscordLink = "https://discord.gg/N38HzXn2E"

-- === SAVE ORIGINAL ===
local Orig = {
    Ambient = Lighting.Ambient,
    OutdoorAmbient = Lighting.OutdoorAmbient,
    Brightness = Lighting.Brightness,
    ClockTime = Lighting.ClockTime,
    FogEnd = Lighting.FogEnd,
    FogStart = Lighting.FogStart,
    GlobalShadows = Lighting.GlobalShadows,
    EnvironmentDiffuseScale = Lighting.EnvironmentDiffuseScale,
    EnvironmentSpecularScale = Lighting.EnvironmentSpecularScale,
    ExposureCompensation = Lighting.ExposureCompensation,
}

-- === HELPER ===
local function GetHRP()
    local char = LocalPlayer.Character
    if char then return char:FindFirstChild("HumanoidRootPart") end
end

local function GetHumanoid()
    local char = LocalPlayer.Character
    if char then return char:FindFirstChildOfClass("Humanoid") end
end

-- === GRAPHICS: SUNSET ===
local function ApplySunset(mult)
    Config.SunsetOn = true
    Config.Multiplier = mult

    for _, v in pairs(Lighting:GetChildren()) do
        if v.Name:find("BiannSunset") then v:Destroy() end
    end

    Lighting.ClockTime = 17
    Lighting.Ambient = Color3.fromRGB(120 + mult * 5, 100 + mult * 5, 90 + mult * 5)
    Lighting.OutdoorAmbient = Color3.fromRGB(180 + mult * 8, 140 + mult * 8, 110 + mult * 8)
    Lighting.Brightness = 1.5 + mult * 0.3
    Lighting.GlobalShadows = true
    Lighting.EnvironmentDiffuseScale = 0.6 + mult * 0.1
    Lighting.EnvironmentSpecularScale = 0.8 + mult * 0.1
    Lighting.ExposureCompensation = 0.1 + mult * 0.05
    Lighting.FogEnd = 80000
    Lighting.FogStart = 0

    local atmos = Instance.new("Atmosphere")
    atmos.Name = "BiannSunset_Atmos"
    atmos.Density = 0.2 + mult * 0.03
    atmos.Offset = 0.1 + mult * 0.02
    atmos.Color = Color3.fromRGB(255, 200, 160)
    atmos.Decay = Color3.fromRGB(200, 130, 90)
    atmos.Glare = 0.8 + mult * 0.2
    atmos.Haze = 1 + mult * 0.3
    atmos.Parent = Lighting

    local bloom = Instance.new("BloomEffect")
    bloom.Name = "BiannSunset_Bloom"
    bloom.Intensity = 0.8 + mult * 0.3
    bloom.Size = 20 + mult * 4
    bloom.Threshold = 1.2 - mult * 0.05
    bloom.Parent = Lighting

    local cc = Instance.new("ColorCorrectionEffect")
    cc.Name = "BiannSunset_CC"
    cc.Brightness = 0.02 + mult * 0.02
    cc.Contrast = 0.1 + mult * 0.05
    cc.Saturation = 0.2 + mult * 0.08
    cc.TintColor = Color3.fromRGB(255, 220, 190)
    cc.Parent = Lighting

    local sun = Instance.new("SunRaysEffect")
    sun.Name = "BiannSunset_Sun"
    sun.Intensity = 0.1 + mult * 0.05
    sun.Spread = 0.8 + mult * 0.1
    sun.Parent = Lighting

    local dof = Instance.new("DepthOfFieldEffect")
    dof.Name = "BiannSunset_DOF"
    dof.FarIntensity = 0.1 + mult * 0.05
    dof.FocusDistance = 80
    dof.InFocusRadius = 30
    dof.NearIntensity = 0.2 + mult * 0.05
    dof.Parent = Lighting

    local blur = Instance.new("BlurEffect")
    blur.Name = "BiannSunset_Blur"
    blur.Size = 0.5 + mult * 0.2
    blur.Parent = Lighting
end

local function RemoveSunset()
    Config.SunsetOn = false
    for _, v in pairs(Lighting:GetChildren()) do
        if v.Name:find("BiannSunset") then v:Destroy() end
    end
    Lighting.Ambient = Orig.Ambient
    Lighting.OutdoorAmbient = Orig.OutdoorAmbient
    Lighting.Brightness = Orig.Brightness
    Lighting.ClockTime = Orig.ClockTime
    Lighting.FogEnd = Orig.FogEnd
    Lighting.FogStart = Orig.FogStart
    Lighting.GlobalShadows = Orig.GlobalShadows
    Lighting.EnvironmentDiffuseScale = Orig.EnvironmentDiffuseScale
    Lighting.EnvironmentSpecularScale = Orig.EnvironmentSpecularScale
    Lighting.ExposureCompensation = Orig.ExposureCompensation
end

-- === GRAPHICS: RAIN (UPGRADED) ===
local rainConn = nil
local rainParts = {}
local function ApplyRain()
    Config.RainOn = true

    local atmos = Lighting:FindFirstChild("BiannRain_Atmos")
    if not atmos then
        atmos = Instance.new("Atmosphere")
        atmos.Name = "BiannRain_Atmos"
        atmos.Density = 0.45
        atmos.Offset = 0.25
        atmos.Color = Color3.fromRGB(170, 190, 220)
        atmos.Decay = Color3.fromRGB(80, 110, 150)
        atmos.Glare = 0.4
        atmos.Haze = 2.5
        atmos.Parent = Lighting
    end

    local cc = Lighting:FindFirstChild("BiannRain_CC")
    if not cc then
        cc = Instance.new("ColorCorrectionEffect")
        cc.Name = "BiannRain_CC"
        cc.Brightness = -0.1
        cc.Contrast = 0.25
        cc.Saturation = -0.2
        cc.TintColor = Color3.fromRGB(180, 210, 240)
        cc.Parent = Lighting
    end

    local wetGround = Lighting:FindFirstChild("BiannRain_WetGround")
    if not wetGround then
        wetGround = Instance.new("ColorCorrectionEffect")
        wetGround.Name = "BiannRain_WetGround"
        wetGround.Brightness = 0.15
        wetGround.Contrast = 0.35
        wetGround.Saturation = 0.1
        wetGround.TintColor = Color3.fromRGB(240, 250, 255)
        wetGround.Parent = Lighting
    end

    local wetSky = Lighting:FindFirstChild("BiannRain_WetSky")
    if not wetSky then
        wetSky = Instance.new("Sky")
        wetSky.Name = "BiannRain_WetSky"
        wetSky.SkyboxBk = "rbxassetid://159454299"
        wetSky.SkyboxDn = "rbxassetid://159454296"
        wetSky.SkyboxFt = "rbxassetid://159454293"
        wetSky.SkyboxLf = "rbxassetid://159454286"
        wetSky.SkyboxRt = "rbxassetid://159454300"
        wetSky.SkyboxUp = "rbxassetid://159454288"
        wetSky.SunAngularSize = 8
        wetSky.MoonAngularSize = 8
        wetSky.StarCount = 0
        wetSky.Parent = Lighting
    end

    local blur = Lighting:FindFirstChild("BiannRain_Blur")
    if not blur then
        blur = Instance.new("BlurEffect")
        blur.Name = "BiannRain_Blur"
        blur.Size = 2.5
        blur.Parent = Lighting
    end

    rainConn = RunService.RenderStepped:Connect(function()
        if not Config.RainOn then return end
        for i = 1, 3 do
            local drop = Instance.new("Part")
            drop.Name = "BiannRain_Drop"
            drop.Size = Vector3.new(0.08, 0.6, 0.08)
            drop.Material = Enum.Material.Neon
            drop.Color = Color3.fromRGB(200, 230, 255)
            drop.Transparency = 0.3
            drop.Anchored = true
            drop.CanCollide = false
            drop.CanQuery = false
            drop.CanTouch = false

            local camPos = Camera.CFrame.Position
            local randX = math.random(-70, 70)
            local randZ = math.random(-70, 70)
            drop.Position = camPos + Vector3.new(randX, 40, randZ)
            drop.Parent = workspace

            task.spawn(function()
                for i = 1, 30 do
                    if drop and drop.Parent then
                        drop.Position = drop.Position - Vector3.new(0, 3, 0)
                        task.wait(0.025)
                    end
                end
                -- Splash
                local splash = Instance.new("Part")
                splash.Size = Vector3.new(1.2, 0.1, 1.2)
                splash.Material = Enum.Material.Neon
                splash.Color = Color3.fromRGB(200, 230, 255)
                splash.Transparency = 0.5
                splash.Anchored = true
                splash.CanCollide = false
                splash.CanQuery = false
                splash.CanTouch = false
                if drop then splash.Position = drop.Position end
                splash.Parent = workspace

                task.spawn(function()
                    for i = 1, 10 do
                        if splash and splash.Parent then
                            splash.Size = splash.Size + Vector3.new(0.3, 0, 0.3)
                            splash.Transparency = splash.Transparency + 0.05
                            task.wait(0.03)
                        end
                    end
                    if splash then splash:Destroy() end
                end)

                if drop then drop:Destroy() end
            end)

            table.insert(rainParts, drop)
            if #rainParts > 200 then
                local old = table.remove(rainParts, 1)
                if old and old.Parent then old:Destroy() end
            end
        end
    end)
end

local function RemoveRain()
    Config.RainOn = false
    if rainConn then rainConn:Disconnect() rainConn = nil end
    for _, v in pairs(rainParts) do
        if v and v.Parent then v:Destroy() end
    end
    rainParts = {}

    for _, v in pairs(Lighting:GetChildren()) do
        if v.Name:find("BiannRain") then v:Destroy() end
    end
end

-- === GRAPHICS: STORM ===
local lightningConn = nil
local function ApplyStorm()
    Config.StormOn = true

    Lighting.Ambient = Color3.fromRGB(50, 55, 65)
    Lighting.OutdoorAmbient = Color3.fromRGB(70, 75, 85)
    Lighting.Brightness = 1
    Lighting.ClockTime = 15
    Lighting.GlobalShadows = true
    Lighting.FogEnd = 30000
    Lighting.FogStart = 0

    local atmos = Lighting:FindFirstChild("BiannStorm_Atmos")
    if not atmos then
        atmos = Instance.new("Atmosphere")
        atmos.Name = "BiannStorm_Atmos"
        atmos.Density = 0.5
        atmos.Offset = 0.3
        atmos.Color = Color3.fromRGB(100, 110, 120)
        atmos.Decay = Color3.fromRGB(50, 60, 70)
        atmos.Glare = 0
        atmos.Haze = 3
        atmos.Parent = Lighting
    end

    local cc = Lighting:FindFirstChild("BiannStorm_CC")
    if not cc then
        cc = Instance.new("ColorCorrectionEffect")
        cc.Name = "BiannStorm_CC"
        cc.Brightness = -0.15
        cc.Contrast = 0.3
        cc.Saturation = -0.3
        cc.TintColor = Color3.fromRGB(120, 130, 150)
        cc.Parent = Lighting
    end

    lightningConn = RunService.Heartbeat:Connect(function()
        if not Config.StormOn then return end
        if math.random(1, 150) == 1 then
            local flash = Lighting:FindFirstChild("BiannStorm_Flash")
            if not flash then
                flash = Instance.new("ColorCorrectionEffect")
                flash.Name = "BiannStorm_Flash"
                flash.Brightness = 0.5
                flash.Contrast = 0.2
                flash.TintColor = Color3.fromRGB(255, 255, 255)
                flash.Parent = Lighting
            end
            flash.Brightness = 0.7
            task.wait(0.05)
            flash.Brightness = 0.1
            task.wait(0.08)
            flash.Brightness = 0.5
            task.wait(0.06)
            flash.Brightness = -0.1
        end
    end)
end

local function RemoveStorm()
    Config.StormOn = false
    if lightningConn then lightningConn:Disconnect() lightningConn = nil end

    for _, v in pairs(Lighting:GetChildren()) do
        if v.Name:find("BiannStorm") then v:Destroy() end
    end

    Lighting.Ambient = Orig.Ambient
    Lighting.OutdoorAmbient = Orig.OutdoorAmbient
    Lighting.Brightness = Orig.Brightness
    Lighting.ClockTime = Orig.ClockTime
    Lighting.FogEnd = Orig.FogEnd
    Lighting.FogStart = Orig.FogStart
    Lighting.GlobalShadows = Orig.GlobalShadows
end

-- === ANTI AFK ===
local antiAfkConn = nil
local function ToggleAntiAFK(state)
    Config.AntiAFK = state
    if state then
        if antiAfkConn then antiAfkConn:Disconnect() end
        antiAfkConn = LocalPlayer.Idled:Connect(function()
            pcall(function()
                VirtualUser:CaptureController()
                VirtualUser:ClickButton2(Vector2.new())
            end)
        end)
    else
        if antiAfkConn then
            antiAfkConn:Disconnect()
            antiAfkConn = nil
        end
    end
end

-- === REJOIN (FIXED) ===
local function Rejoin()
    pcall(function()
        TeleportService:Teleport(game.PlaceId, LocalPlayer)
    end)
end

-- === FLY ===
local flyConn, flyBV, flyBG
local function ToggleFly(state)
    Config.FlyEnabled = state
    if state then
        local hrp = GetHRP()
        if not hrp then return end

        flyBV = Instance.new("BodyVelocity")
        flyBV.MaxForce = Vector3.new(1e5, 1e5, 1e5)
        flyBV.Velocity = Vector3.zero
        flyBV.Parent = hrp

        flyBG = Instance.new("BodyGyro")
        flyBG.MaxTorque = Vector3.new(1e5, 1e5, 1e5)
        flyBG.P = 1000
        flyBG.CFrame = hrp.CFrame
        flyBG.Parent = hrp

        flyConn = RunService.RenderStepped:Connect(function()
            local moveDir = Vector3.zero
            local camCF = Camera.CFrame
            if UserInputService:IsKeyDown(Enum.KeyCode.W) then moveDir += camCF.LookVector end
            if UserInputService:IsKeyDown(Enum.KeyCode.S) then moveDir -= camCF.LookVector end
            if UserInputService:IsKeyDown(Enum.KeyCode.A) then moveDir -= camCF.RightVector end
            if UserInputService:IsKeyDown(Enum.KeyCode.D) then moveDir += camCF.RightVector end
            if UserInputService:IsKeyDown(Enum.KeyCode.Space) then moveDir += Vector3.new(0,1,0) end
            if UserInputService:IsKeyDown(Enum.KeyCode.LeftShift) then moveDir -= Vector3.new(0,1,0) end

            if moveDir.Magnitude > 0 then
                flyBV.Velocity = moveDir.Unit * Config.FlySpeed
            else
                flyBV.Velocity = Vector3.zero
            end
            flyBG.CFrame = camCF
        end)
    else
        if flyConn then flyConn:Disconnect() end
        if flyBV then flyBV:Destroy() end
        if flyBG then flyBG:Destroy() end
    end
end

-- === SPEED / JUMP ===
local function SetSpeed(v)
    Config.WalkSpeed = v
    local h = GetHumanoid()
    if h then h.WalkSpeed = v end
end

local function SetJump(v)
    Config.JumpPower = v
    local h = GetHumanoid()
    if h then
        h.JumpPower = v
        h.UseJumpPower = true
    end
end

-- === NOCLIP ===
local noclipConn
local function ToggleNoclip(state)
    Config.Noclip = state
    if state then
        noclipConn = RunService.Stepped:Connect(function()
            local char = LocalPlayer.Character
            if char then
                for _, part in pairs(char:GetDescendants()) do
                    if part:IsA("BasePart") then
                        part.CanCollide = false
                    end
                end
            end
        end)
    else
        if noclipConn then
            noclipConn:Disconnect()
            noclipConn = nil
        end
        local char = LocalPlayer.Character
        if char then
            for _, part in pairs(char:GetDescendants()) do
                if part:IsA("BasePart") then
                    part.CanCollide = true
                end
            end
        end
    end
end

-- === INFINITE JUMP ===
local infJumpConn
local function ToggleInfJump(state)
    Config.InfiniteJump = state
    if state then
        infJumpConn = UserInputService.JumpRequest:Connect(function()
            local h = GetHumanoid()
            if h then
                h:ChangeState(Enum.HumanoidStateType.Jumping)
            end
        end)
    else
        if infJumpConn then
            infJumpConn:Disconnect()
            infJumpConn = nil
        end
    end
end

-- === FULLBRIGHT ===
local function SetFullbright(state)
    if state then
        Lighting.Ambient = Color3.fromRGB(255, 255, 255)
        Lighting.OutdoorAmbient = Color3.fromRGB(255, 255, 255)
        Lighting.Brightness = 3
        Lighting.ClockTime = 14
        Lighting.FogEnd = 100000
        Lighting.GlobalShadows = false
    else
        Lighting.Ambient = Orig.Ambient
        Lighting.OutdoorAmbient = Orig.OutdoorAmbient
        Lighting.Brightness = Orig.Brightness
        Lighting.ClockTime = Orig.ClockTime
        Lighting.FogEnd = Orig.FogEnd
        Lighting.GlobalShadows = Orig.GlobalShadows
    end
end

-- === TELEPORT TO PLAYER ===
local function TeleportToPlayer(targetName)
    local target = Players:FindFirstChild(targetName)
    if target and target.Character then
        local hrp = GetHRP()
        local tHrp = target.Character:FindFirstChild("HumanoidRootPart")
        if hrp and tHrp then
            hrp.CFrame = tHrp.CFrame * CFrame.new(0, 0, 3)
        end
    end
end

-- === NOTIFICATION ===
local function Notify(text)
    pcall(function()
        StarterGui:SetCore("SendNotification", {
            Title = "BIANN SUNSET",
            Text = text,
            Duration = 2,
        })
    end)
end

-- === GUI ===
local gui = Instance.new("ScreenGui")
gui.Name = "BiannSunsetGUI"
gui.ResetOnSpawn = false
gui.IgnoreGuiInset = true
gui.ZIndexBehavior = Enum.ZIndexBehavior.Sibling
pcall(function() gui.Parent = CoreGui end)
if not gui.Parent then gui.Parent = LocalPlayer:WaitForChild("PlayerGui") end

-- === LOGO BUTTON ===
local LogoBtn = Instance.new("TextButton")
LogoBtn.Size = UDim2.new(0, 50, 0, 50)
LogoBtn.Position = UDim2.new(0, 20, 0.5, -25)
LogoBtn.BackgroundColor3 = Color3.fromRGB(30, 15, 5)
LogoBtn.BackgroundTransparency = 0.1
LogoBtn.Text = "🗾"
LogoBtn.TextSize = 26
LogoBtn.Font = Enum.Font.GothamBold
LogoBtn.BorderSizePixel = 0
LogoBtn.AutoButtonColor = false
LogoBtn.Active = true
LogoBtn.ZIndex = 10
LogoBtn.Parent = gui

local lc = Instance.new("UICorner")
lc.CornerRadius = UDim.new(1, 0)
lc.Parent = LogoBtn

local ls = Instance.new("UIStroke")
ls.Color = Color3.fromRGB(255, 170, 100)
ls.Thickness = 2
ls.Transparency = 0.2
ls.Parent = LogoBtn

local lg = Instance.new("ImageLabel")
lg.Size = UDim2.new(1, 20, 1, 20)
lg.Position = UDim2.new(0, -10, 0, -10)
lg.BackgroundTransparency = 1
lg.Image = "rbxassetid://5028857084"
lg.ImageColor3 = Color3.fromRGB(255, 170, 100)
lg.ImageTransparency = 0.5
lg.ZIndex = -1
lg.Parent = LogoBtn

task.spawn(function()
    while lg.Parent do
        TweenService:Create(lg, TweenInfo.new(1.8, Enum.EasingStyle.Sine, Enum.EasingDirection.InOut), {ImageTransparency = 0.3}):Play()
        task.wait(1.8)
        TweenService:Create(lg, TweenInfo.new(1.8, Enum.EasingStyle.Sine, Enum.EasingDirection.InOut), {ImageTransparency = 0.7}):Play()
        task.wait(1.8)
    end
end)

-- === MENU FRAME ===
local MenuFrame = Instance.new("Frame")
MenuFrame.Size = UDim2.new(0, 480, 0, 340)
MenuFrame.Position = UDim2.new(0, 85, 0.5, -170)
MenuFrame.BackgroundColor3 = Color3.fromRGB(15, 10, 8)
MenuFrame.BackgroundTransparency = 0.05
MenuFrame.BorderSizePixel = 0
MenuFrame.Visible = false
MenuFrame.Active = true
MenuFrame.ZIndex = 20
MenuFrame.Parent = gui

local mc = Instance.new("UICorner")
mc.CornerRadius = UDim.new(0, 16)
mc.Parent = MenuFrame

local ms = Instance.new("UIStroke")
ms.Color = Color3.fromRGB(255, 170, 100)
ms.Thickness = 1.5
ms.Transparency = 0.3
ms.Parent = MenuFrame

-- === SIDEBAR ===
local Sidebar = Instance.new("Frame")
Sidebar.Size = UDim2.new(0, 130, 1, 0)
Sidebar.BackgroundColor3 = Color3.fromRGB(25, 15, 8)
Sidebar.BackgroundTransparency = 0.2
Sidebar.BorderSizePixel = 0
Sidebar.ZIndex = 21
Sidebar.Parent = MenuFrame

local sc = Instance.new("UICorner")
sc.CornerRadius = UDim.new(0, 16)
sc.Parent = Sidebar

local scFix = Instance.new("Frame")
scFix.Size = UDim2.new(0, 16, 1, 0)
scFix.Position = UDim2.new(1, -16, 0, 0)
scFix.BackgroundColor3 = Color3.fromRGB(25, 15, 8)
scFix.BackgroundTransparency = 0.2
scFix.BorderSizePixel = 0
scFix.ZIndex = 21
scFix.Parent = Sidebar

local SideTitle = Instance.new("TextLabel")
SideTitle.Size = UDim2.new(1, 0, 0, 40)
SideTitle.Position = UDim2.new(0, 0, 0, 10)
SideTitle.BackgroundTransparency = 1
SideTitle.Text = "BIANN"
SideTitle.TextColor3 = Color3.fromRGB(255, 170, 100)
SideTitle.TextSize = 18
SideTitle.Font = Enum.Font.GothamBold
SideTitle.ZIndex = 22
SideTitle.Parent = Sidebar

local SideSub = Instance.new("TextLabel")
SideSub.Size = UDim2.new(1, 0, 0, 16)
SideSub.Position = UDim2.new(0, 0, 0, 30)
SideSub.BackgroundTransparency = 1
SideSub.Text = "SUNSET v3.2"
SideSub.TextColor3 = Color3.fromRGB(200, 130, 90)
SideSub.TextSize = 9
SideSub.Font = Enum.Font.Gotham
SideSub.ZIndex = 22
SideSub.Parent = Sidebar

local sidebarBtns = {}
local function CreateSideBtn(name, icon, order)
    local b = Instance.new("TextButton")
    b.Size = UDim2.new(1, -12, 0, 36)
    b.Position = UDim2.new(0, 6, 0, 52 + order * 42)
    b.BackgroundColor3 = Color3.fromRGB(35, 20, 10)
    b.BackgroundTransparency = 0.5
    b.Text = "  " .. icon .. "  " .. name
    b.TextColor3 = Color3.fromRGB(255, 200, 160)
    b.TextSize = 12
    b.Font = Enum.Font.GothamBold
    b.TextXAlignment = Enum.TextXAlignment.Left
    b.BorderSizePixel = 0
    b.AutoButtonColor = false
    b.ZIndex = 22
    b.Active = true
    b.Parent = Sidebar

    local c = Instance.new("UICorner")
    c.CornerRadius = UDim.new(0, 10)
    c.Parent = b

    b.MouseEnter:Connect(function()
        if ActiveSideBtn ~= b then
            TweenService:Create(b, TweenInfo.new(0.25, Enum.EasingStyle.Quart, Enum.EasingDirection.Out), {
                BackgroundTransparency = 0.3,
                TextColor3 = Color3.fromRGB(255, 255, 255)
            }):Play()
        end
    end)
    b.MouseLeave:Connect(function()
        if ActiveSideBtn ~= b then
            TweenService:Create(b, TweenInfo.new(0.25, Enum.EasingStyle.Quart, Enum.EasingDirection.Out), {
                BackgroundTransparency = 0.5,
                TextColor3 = Color3.fromRGB(255, 200, 160)
            }):Play()
        end
    end)

    sidebarBtns[name] = b
    return b
end

CreateSideBtn("User", "👤", 0)
CreateSideBtn("Main", "🏠", 1)
CreateSideBtn("Grafik", "🎨", 2)
CreateSideBtn("Seting", "⚙️", 3)

-- === CONTENT AREA ===
local ContentArea = Instance.new("Frame")
ContentArea.Size = UDim2.new(1, -140, 1, -20)
ContentArea.Position = UDim2.new(0, 135, 0, 10)
ContentArea.BackgroundTransparency = 1
ContentArea.ZIndex = 22
ContentArea.Parent = MenuFrame

local Pages = {}
local ActivePage = nil
local ActiveSideBtn = nil

local function CreatePage(name)
    local p = Instance.new("ScrollingFrame")
    p.Name = name
    p.Size = UDim2.new(1, 0, 1, 0)
    p.BackgroundTransparency = 1
    p.BorderSizePixel = 0
    p.ScrollBarThickness = 3
    p.ScrollBarImageColor3 = Color3.fromRGB(255, 170, 100)
    p.CanvasSize = UDim2.new(0, 0, 0, 0)
    p.AutomaticCanvasSize = Enum.AutomaticSize.Y
    p.Visible = false
    p.ZIndex = 23
    p.ScrollBarImageTransparency = 0.3
    p.Parent = ContentArea

    local l = Instance.new("UIListLayout")
    l.Padding = UDim.new(0, 7)
    l.SortOrder = Enum.SortOrder.LayoutOrder
    l.Parent = p

    local pad = Instance.new("UIPadding")
    pad.PaddingTop = UDim.new(0, 4)
    pad.PaddingBottom = UDim.new(0, 10)
    pad.PaddingRight = UDim.new(0, 6)
    pad.Parent = p

    Pages[name] = p
    return p
end

-- === COMPONENTS ===
local function SectionTitle(parent, text)
    local l = Instance.new("TextLabel")
    l.Size = UDim2.new(1, 0, 0, 18)
    l.BackgroundTransparency = 1
    l.Text = "── " .. text .. " ──"
    l.TextColor3 = Color3.fromRGB(255, 170, 100)
    l.TextSize = 10
    l.Font = Enum.Font.GothamBold
    l.ZIndex = 24
    l.Parent = parent
    return l
end

local function ToggleRow(parent, text, defaultState, callback)
    local row = Instance.new("TextButton")
    row.Size = UDim2.new(1, 0, 0, 38)
    row.BackgroundColor3 = Color3.fromRGB(30, 18, 10)
    row.BackgroundTransparency = 0.3
    row.Text = ""
    row.BorderSizePixel = 0
    row.AutoButtonColor = false
    row.ZIndex = 24
    row.Parent = parent

    local c = Instance.new("UICorner")
    c.CornerRadius = UDim.new(0, 10)
    c.Parent = row

    local s = Instance.new("UIStroke")
    s.Color = Color3.fromRGB(255, 170, 100)
    s.Thickness = 1
    s.Transparency = 0.7
    s.Parent = row

    local label = Instance.new("TextLabel")
    label.Size = UDim2.new(1, -60, 1, 0)
    label.Position = UDim2.new(0, 14, 0, 0)
    label.BackgroundTransparency = 1
    label.Text = text
    label.TextColor3 = Color3.fromRGB(255, 220, 190)
    label.TextSize = 12
    label.Font = Enum.Font.Gotham
    label.TextXAlignment = Enum.TextXAlignment.Left
    label.ZIndex = 25
    label.Parent = row

    local indicator = Instance.new("TextLabel")
    indicator.Size = UDim2.new(0, 40, 1, 0)
    indicator.Position = UDim2.new(1, -50, 0, 0)
    indicator.BackgroundTransparency = 1
    indicator.Text = defaultState and "ON" or "OFF"
    indicator.TextColor3 = defaultState and Color3.fromRGB(0, 255, 100) or Color3.fromRGB(255, 80, 80)
    indicator.TextSize = 11
    indicator.Font = Enum.Font.GothamBold
    indicator.ZIndex = 25
    indicator.Parent = row

    local state = defaultState

    row.MouseEnter:Connect(function()
        TweenService:Create(row, TweenInfo.new(0.25, Enum.EasingStyle.Quart, Enum.EasingDirection.Out), {BackgroundTransparency = 0.15}):Play()
        TweenService:Create(s, TweenInfo.new(0.25, Enum.EasingStyle.Quart, Enum.EasingDirection.Out), {Transparency = 0.4}):Play()
    end)
    row.MouseLeave:Connect(function()
        TweenService:Create(row, TweenInfo.new(0.25, Enum.EasingStyle.Quart, Enum.EasingDirection.Out), {BackgroundTransparency = 0.3}):Play()
        TweenService:Create(s, TweenInfo.new(0.25, Enum.EasingStyle.Quart, Enum.EasingDirection.Out), {Transparency = 0.7}):Play()
    end)

    row.MouseButton1Click:Connect(function()
        state = not state
        indicator.Text = state and "ON" or "OFF"
        TweenService:Create(indicator, TweenInfo.new(0.2), {
            TextColor3 = state and Color3.fromRGB(0, 255, 100) or Color3.fromRGB(255, 80, 80)
        }):Play()
        TweenService:Create(row, TweenInfo.new(0.15), {BackgroundTransparency = 0.5}):Play()
        task.wait(0.1)
        TweenService:Create(row, TweenInfo.new(0.2, Enum.EasingStyle.Quart, Enum.EasingDirection.Out), {BackgroundTransparency = 0.15}):Play()
        if callback then callback(state) end
    end)

    return row
end

local function ActionBtn(parent, text, icon, callback)
    local b = Instance.new("TextButton")
    b.Size = UDim2.new(1, 0, 0, 36)
    b.BackgroundColor3 = Color3.fromRGB(30, 18, 10)
    b.BackgroundTransparency = 0.3
    b.Text = "  " .. (icon or "▶") .. "  " .. text
    b.TextColor3 = Color3.fromRGB(255, 220, 190)
    b.TextSize = 12
    b.Font = Enum.Font.GothamBold
    b.TextXAlignment = Enum.TextXAlignment.Left
    b.BorderSizePixel = 0
    b.AutoButtonColor = false
    b.ZIndex = 24
    b.Parent = parent

    local c = Instance.new("UICorner")
    c.CornerRadius = UDim.new(0, 10)
    c.Parent = b

    local s = Instance.new("UIStroke")
    s.Color = Color3.fromRGB(255, 170, 100)
    s.Thickness = 1
    s.Transparency = 0.7
    s.Parent = b

    b.MouseEnter:Connect(function()
        TweenService:Create(b, TweenInfo.new(0.25, Enum.EasingStyle.Quart, Enum.EasingDirection.Out), {BackgroundTransparency = 0.1}):Play()
        TweenService:Create(s, TweenInfo.new(0.25, Enum.EasingStyle.Quart, Enum.EasingDirection.Out), {Transparency = 0.4}):Play()
    end)
    b.MouseLeave:Connect(function()
        TweenService:Create(b, TweenInfo.new(0.25, Enum.EasingStyle.Quart, Enum.EasingDirection.Out), {BackgroundTransparency = 0.3}):Play()
        TweenService:Create(s, TweenInfo.new(0.25, Enum.EasingStyle.Quart, Enum.EasingDirection.Out), {Transparency = 0.7}):Play()
    end)
    b.MouseButton1Click:Connect(function()
        TweenService:Create(b, TweenInfo.new(0.15), {BackgroundTransparency = 0.5}):Play()
        task.wait(0.1)
        TweenService:Create(b, TweenInfo.new(0.2, Enum.EasingStyle.Quart, Enum.EasingDirection.Out), {BackgroundTransparency = 0.1}):Play()
        if callback then callback() end
    end)

    return b
end

-- === USER PAGE ===
local pUser = CreatePage("User")

local ProfileCard = Instance.new("Frame")
ProfileCard.Size = UDim2.new(1, 0, 0, 90)
ProfileCard.BackgroundColor3 = Color3.fromRGB(30, 18, 10)
ProfileCard.BackgroundTransparency = 0.3
ProfileCard.BorderSizePixel = 0
ProfileCard.ZIndex = 24
ProfileCard.Parent = pUser

local pc = Instance.new("UICorner")
pc.CornerRadius = UDim.new(0, 12)
pc.Parent = ProfileCard

local pcs = Instance.new("UIStroke")
pcs.Color = Color3.fromRGB(255, 170, 100)
pcs.Thickness = 1
pcs.Transparency = 0.5
pcs.Parent = ProfileCard

local AvatarImg = Instance.new("ImageLabel")
AvatarImg.Size = UDim2.new(0, 60, 0, 60)
AvatarImg.Position = UDim2.new(0, 14, 0, 15)
AvatarImg.BackgroundColor3 = Color3.fromRGB(50, 30, 15)
AvatarImg.BorderSizePixel = 0
AvatarImg.Image = "rbxthumb://type=AvatarHeadShot&id=" .. LocalPlayer.UserId .. "&w=150&h=150"
AvatarImg.ZIndex = 25
AvatarImg.Parent = ProfileCard

local ac = Instance.new("UICorner")
ac.CornerRadius = UDim.new(1, 0)
ac.Parent = AvatarImg

local aS = Instance.new("UIStroke")
aS.Color = Color3.fromRGB(255, 170, 100)
aS.Thickness = 2
aS.Parent = AvatarImg

local NameLabel = Instance.new("TextLabel")
NameLabel.Size = UDim2.new(1, -100, 0, 22)
NameLabel.Position = UDim2.new(0, 90, 0, 20)
NameLabel.BackgroundTransparency = 1
NameLabel.Text = LocalPlayer.DisplayName
NameLabel.TextColor3 = Color3.fromRGB(255, 220, 190)
NameLabel.TextSize = 15
NameLabel.Font = Enum.Font.GothamBold
NameLabel.TextXAlignment = Enum.TextXAlignment.Left
NameLabel.ZIndex = 25
NameLabel.Parent = ProfileCard

local UserLabel = Instance.new("TextLabel")
UserLabel.Size = UDim2.new(1, -100, 0, 16)
UserLabel.Position = UDim2.new(0, 90, 0, 42)
UserLabel.BackgroundTransparency = 1
UserLabel.Text = "@" .. LocalPlayer.Name
UserLabel.TextColor3 = Color3.fromRGB(200, 130, 90)
UserLabel.TextSize = 11
UserLabel.Font = Enum.Font.Gotham
UserLabel.TextXAlignment = Enum.TextXAlignment.Left
UserLabel.ZIndex = 25
UserLabel.Parent = ProfileCard

local UserIdLabel = Instance.new("TextLabel")
UserIdLabel.Size = UDim2.new(1, -100, 0, 14)
UserIdLabel.Position = UDim2.new(0, 90, 0, 60)
UserIdLabel.BackgroundTransparency = 1
UserIdLabel.Text = "ID: " .. LocalPlayer.UserId
UserIdLabel.TextColor3 = Color3.fromRGB(200, 130, 90)
UserIdLabel.TextSize = 10
UserIdLabel.Font = Enum.Font.Gotham
UserIdLabel.TextXAlignment = Enum.TextXAlignment.Left
UserIdLabel.ZIndex = 25
UserIdLabel.Parent = ProfileCard

task.spawn(function()
    while gui.Parent do
        pcall(function()
            AvatarImg.Image = "rbxthumb://type=AvatarHeadShot&id=" .. LocalPlayer.UserId .. "&w=150&h=150"
        end)
        task.wait(5)
    end
end)

SectionTitle(pUser, "SOCIAL")
ActionBtn(pUser, "Join Discord Server", "💬", function()
    Notify("Discord: " .. DiscordLink)
    pcall(function()
        GuiService:OpenBrowserWindow(DiscordLink)
    end)
end)

ActionBtn(pUser, "Copy Discord Link", "📋", function()
    pcall(function()
        if setclipboard then
            setclipboard(DiscordLink)
            Notify("Discord link copied!")
        else
            Notify("Clipboard not supported")
        end
    end)
end)

-- === MAIN PAGE ===
local pMain = CreatePage("Main")

SectionTitle(pMain, "PLAYER")
ToggleRow(pMain, "Fly (WASD + Space/Shift)", false, function(state)
    ToggleFly(state)
    Notify("Fly: " .. (state and "ON" or "OFF"))
end)

ToggleRow(pMain, "Noclip", false, function(state)
    ToggleNoclip(state)
    Notify("Noclip: " .. (state and "ON" or "OFF"))
end)

ToggleRow(pMain, "Infinite Jump", false, function(state)
    ToggleInfJump(state)
    Notify("Infinite Jump: " .. (state and "ON" or "OFF"))
end)

SectionTitle(pMain, "MOVEMENT")
ActionBtn(pMain, "Speed: 50", "⚡", function() SetSpeed(50) Notify("Speed: 50") end)
ActionBtn(pMain, "Speed: 100", "⚡", function() SetSpeed(100) Notify("Speed: 100") end)
ActionBtn(pMain, "Speed: 200", "⚡", function() SetSpeed(200) Notify("Speed: 200") end)
ActionBtn(pMain, "Jump: 120", "🦘", function() SetJump(120) Notify("Jump: 120") end)
ActionBtn(pMain, "Jump: 250", "🦘", function() SetJump(250) Notify("Jump: 250") end)
ActionBtn(pMain, "Reset Speed & Jump", "↺", function()
    SetSpeed(16)
    SetJump(50)
    Notify("Speed & Jump reset")
end)

SectionTitle(pMain, "SERVER")
ActionBtn(pMain, "Rejoin Server", "🔄", function()
    Notify("Rejoining...")
    task.wait(1)
    Rejoin()
end)

-- === GRAFIK PAGE ===
local pGrafik = CreatePage("Grafik")

SectionTitle(pGrafik, "SUNSET SHADER")

ToggleRow(pGrafik, "Sunset Mode", false, function(state)
    if state then
        ApplySunset(Config.Multiplier)
        Notify("Sunset: ON")
    else
        RemoveSunset()
        Notify("Sunset: OFF")
    end
end)

ToggleRow(pGrafik, "Fullbright", false, function(state)
    SetFullbright(state)
    Notify("Fullbright: " .. (state and "ON" or "OFF"))
end)

SectionTitle(pGrafik, "WEATHER")

ToggleRow(pGrafik, "Rain Mode", false, function(state)
    if state then
        ApplyRain()
        Notify("Rain: ON")
    else
        RemoveRain()
        Notify("Rain: OFF")
    end
end)

ToggleRow(pGrafik, "Storm Mode (Dark + Lightning)", false, function(state)
    if state then
        ApplyStorm()
        Notify("Storm: ON")
    else
        RemoveStorm()
        Notify("Storm: OFF")
    end
end)

SectionTitle(pGrafik, "GRAPHICS MULTIPLIER")

local multContainer = Instance.new("Frame")
multContainer.Size = UDim2.new(1, 0, 0, 38)
multContainer.BackgroundTransparency = 1
multContainer.ZIndex = 24
multContainer.Parent = pGrafik

local multBtns = {}
local function CreateMultBtn(text, val, order)
    local b = Instance.new("TextButton")
    b.Size = UDim2.new(0, 62, 0, 34)
    b.Position = UDim2.new(0, order * 68, 0, 0)
    b.BackgroundColor3 = Color3.fromRGB(30, 18, 10)
    b.BackgroundTransparency = 0.3
    b.Text = text
    b.TextColor3 = Color3.fromRGB(255, 220, 190)
    b.TextSize = 13
    b.Font = Enum.Font.GothamBold
    b.BorderSizePixel = 0
    b.AutoButtonColor = false
    b.ZIndex = 24
    b.Parent = multContainer

    local c = Instance.new("UICorner")
    c.CornerRadius = UDim.new(0, 10)
    c.Parent = b

    local s = Instance.new("UIStroke")
    s.Color = Color3.fromRGB(255, 170, 100)
    s.Thickness = 1
    s.Transparency = 0.7
    s.Parent = b

    b.MouseEnter:Connect(function()
        TweenService:Create(b, TweenInfo.new(0.25, Enum.EasingStyle.Quart, Enum.EasingDirection.Out), {BackgroundTransparency = 0.1}):Play()
    end)
    b.MouseLeave:Connect(function()
        TweenService:Create(b, TweenInfo.new(0.25, Enum.EasingStyle.Quart, Enum.EasingDirection.Out), {BackgroundTransparency = 0.3}):Play()
    end)
    b.MouseButton1Click:Connect(function()
        Config.Multiplier = val
        if Config.SunsetOn then
            ApplySunset(val)
        end
        Notify("Graphics: " .. text)

        for _, bb in pairs(multBtns) do
            TweenService:Create(bb, TweenInfo.new(0.25), {
                BackgroundColor3 = Color3.fromRGB(30, 18, 10)
            }):Play()
        end
        TweenService:Create(b, TweenInfo.new(0.25), {
            BackgroundColor3 = Color3.fromRGB(80, 40, 15)
        }):Play()
    end)

    multBtns[text] = b
    return b
end

CreateMultBtn("1×", 1, 0)
CreateMultBtn("2×", 2, 1)
CreateMultBtn("3×", 3, 2)
CreateMultBtn("4×", 4, 3)
CreateMultBtn("5×", 5, 4)

-- === SETING PAGE ===
local pSeting = CreatePage("Seting")

SectionTitle(pSeting, "SETTINGS")

ToggleRow(pSeting, "Anti AFK", Config.AntiAFK, function(state)
    ToggleAntiAFK(state)
end)

ActionBtn(pSeting, "Rejoin Server", "🔄", function()
    Notify("Rejoining...")
    task.wait(1)
    Rejoin()
end)

ActionBtn(pSeting, "Reset Graphics", "↺", function()
    RemoveSunset()
    RemoveRain()
    RemoveStorm()
    Config.Multiplier = 1
    Notify("Graphics reset")
end)

SectionTitle(pSeting, "TELEPORT TO PLAYER")

local tpContainer = Instance.new("Frame")
tpContainer.Size = UDim2.new(1, 0, 0, 200)
tpContainer.BackgroundTransparency = 1
tpContainer.ZIndex = 24
tpContainer.Parent = pSeting

local tpLayout = Instance.new("UIListLayout")
tpLayout.Padding = UDim.new(0, 6)
tpLayout.SortOrder = Enum.SortOrder.LayoutOrder
tpLayout.Parent = tpContainer

local function RefreshPlayerList()
    for _, c in pairs(tpContainer:GetChildren()) do
        if c:IsA("TextButton") then c:Destroy() end
    end

    for _, plr in pairs(Players:GetPlayers()) do
        if plr ~= LocalPlayer then
            local b = Instance.new("TextButton")
            b.Size = UDim2.new(1, 0, 0, 32)
            b.BackgroundColor3 = Color3.fromRGB(30, 18, 10)
            b.BackgroundTransparency = 0.3
            b.Text = "  🎯  " .. plr.Name
            b.TextColor3 = Color3.fromRGB(255, 220, 190)
            b.TextSize = 11
            b.Font = Enum.Font.GothamBold
            b.TextXAlignment = Enum.TextXAlignment.Left
            b.BorderSizePixel = 0
            b.AutoButtonColor = false
            b.ZIndex = 24
            b.Parent = tpContainer

            local c = Instance.new("UICorner")
            c.CornerRadius = UDim.new(0, 10)
            c.Parent = b

            local s = Instance.new("UIStroke")
            s.Color = Color3.fromRGB(255, 170, 100)
            s.Thickness = 1
            s.Transparency = 0.7
            s.Parent = b

            b.MouseEnter:Connect(function()
                TweenService:Create(b, TweenInfo.new(0.2), {BackgroundTransparency = 0.1}):Play()
            end)
            b.MouseLeave:Connect(function()
                TweenService:Create(b, TweenInfo.new(0.2), {BackgroundTransparency = 0.3}):Play()
            end)
            b.MouseButton1Click:Connect(function()
                TeleportToPlayer(plr.Name)
                Notify("Teleported to: " .. plr.Name)
            end)
        end
    end
end

RefreshPlayerList()
Players.PlayerAdded:Connect(RefreshPlayerList)
Players.PlayerRemoving:Connect(function()
    task.wait(0.5)
    RefreshPlayerList()
end)

-- === SWITCH PAGE ===
local function SwitchPage(name)
    if ActivePage then ActivePage.Visible = false end
    if ActiveSideBtn then
        TweenService:Create(ActiveSideBtn, TweenInfo.new(0.3, Enum.EasingStyle.Quart, Enum.EasingDirection.Out), {
            BackgroundTransparency = 0.5,
            TextColor3 = Color3.fromRGB(255, 200, 160)
        }):Play()
    end

    ActivePage = Pages[name]
    ActiveSideBtn = sidebarBtns[name]
    if ActivePage then ActivePage.Visible = true end

    if ActiveSideBtn then
        TweenService:Create(ActiveSideBtn, TweenInfo.new(0.3, Enum.EasingStyle.Quart, Enum.EasingDirection.Out), {
            BackgroundTransparency = 0.1,
            TextColor3 = Color3.fromRGB(255, 255, 255)
        }):Play()
    end
end

for name, btn in pairs(sidebarBtns) do
    btn.MouseButton1Click:Connect(function()
        SwitchPage(name)
    end)
end

-- === TOGGLE MENU ===
local menuOpen = false

LogoBtn.MouseButton1Click:Connect(function()
    menuOpen = not menuOpen
    if menuOpen then
        MenuFrame.Visible = true
        MenuFrame.BackgroundTransparency = 1
        MenuFrame.Size = UDim2.new(0, 440, 0, 310)
        TweenService:Create(MenuFrame, TweenInfo.new(0.4, Enum.EasingStyle.Quart, Enum.EasingDirection.Out), {
            BackgroundTransparency = 0.05,
            Size = UDim2.new(0, 480, 0, 340)
        }):Play()
    else
        local tw = TweenService:Create(MenuFrame, TweenInfo.new(0.3, Enum.EasingStyle.Quart, Enum.EasingDirection.In), {
            BackgroundTransparency = 1,
            Size = UDim2.new(0, 440, 0, 310)
        })
        tw:Play()
        tw.Completed:Connect(function()
            MenuFrame.Visible = false
        end)
    end
end)

-- === DRAG LOGO ===
local dragging = false
local dragStart, startPos

LogoBtn.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
        dragging = true
        dragStart = input.Position
        startPos = LogoBtn.Position
    end
end)

LogoBtn.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1 or input.UserInputType == Enum.UserInputType.Touch then
        dragging = false
    end
end)

UserInputService.InputChanged:Connect(function(input)
    if dragging and (input.UserInputType == Enum.UserInputType.MouseMovement or input.UserInputType == Enum.UserInputType.Touch) then
        local d = input.Position - dragStart
        TweenService:Create(LogoBtn, TweenInfo.new(0.08, Enum.EasingStyle.Linear), {
            Position = UDim2.new(startPos.X.Scale, startPos.X.Offset + d.X, startPos.Y.Scale, startPos.Y.Offset + d.Y)
        }):Play()
    end
end)

-- === AUTO REAPPLY ON RESPAWN ===
LocalPlayer.CharacterAdded:Connect(function(char)
    task.wait(1)
    local h = char:FindFirstChildOfClass("Humanoid")
    if h then
        h.WalkSpeed = Config.WalkSpeed
        h.JumpPower = Config.JumpPower
        h.UseJumpPower = true
    end
    if Config.FlyEnabled then
        Config.FlyEnabled = false
        ToggleFly(true)
    end
end)

-- === INIT ===
SwitchPage("User")

print("[BIANN SUNSET v3.2] Loaded · User: BiannXz KalxX")
print("[BIANN SUNSET] Discord: " .. DiscordLink)
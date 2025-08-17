-- Galaxy Hub - by whitte & kota
-- Discord: https://discord.gg/qbaMNrxVJ5
-- Recomendado: Delta Mobile

-- Serviços
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local RunService = game:GetService("RunService")
local UIS = game:GetService("UserInputService")
local Lighting = game:GetService("Lighting")
local CoreGui = game:GetService("CoreGui")

-- GUI raiz
local ScreenGui = Instance.new("ScreenGui")
ScreenGui.Name = "GalaxyHub (beta)"
ScreenGui.ResetOnSpawn = false
ScreenGui.Parent = CoreGui

-- Janela principal
local MainFrame = Instance.new("Frame")
MainFrame.Size = UDim2.new(0, 520, 0, 420)
MainFrame.Position = UDim2.new(0.5, -260, 0.5, -210)
MainFrame.BackgroundColor3 = Color3.fromRGB(35, 35, 35)
MainFrame.Active = true
MainFrame.Draggable = true
MainFrame.Parent = ScreenGui

-- Título
local Title = Instance.new("TextLabel")
Title.Size = UDim2.new(1, 0, 0, 42)
Title.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
Title.Text = "GALAXY - by White"
Title.Font = Enum.Font.GothamBold
Title.TextSize = 18
Title.TextColor3 = Color3.fromRGB(255, 255, 255)
Title.Parent = MainFrame

-- Rodapé (Discord)
local Footer = Instance.new("TextButton")
Footer.Size = UDim2.new(1, -10, 0, 22)
Footer.Position = UDim2.new(0, 5, 1, -26)
Footer.BackgroundTransparency = 1
Footer.Text = "Discord: https://discord.gg/qbaMNrxVJ5  (clique para copiar)"
Footer.Font = Enum.Font.Gotham
Footer.TextSize = 12
Footer.TextColor3 = Color3.fromRGB(150, 180, 255)
Footer.Parent = MainFrame
Footer.MouseButton1Click:Connect(function()
    if setclipboard then
        setclipboard("https://discord.gg/qbaMNrxVJ5")
        Footer.Text = "Link copiado!"
        task.delay(1.5, function()
            Footer.Text = "Discord: https://discord.gg/qbaMNrxVJ5  (clique para copiar)"
        end)
    end
end)

-- Abas
local TabBar = Instance.new("Frame")
TabBar.Size = UDim2.new(1, -20, 0, 36)
TabBar.Position = UDim2.new(0, 10, 0, 50)
TabBar.BackgroundTransparency = 1
TabBar.Parent = MainFrame

local Content = Instance.new("Frame")
Content.Size = UDim2.new(1, -20, 1, -120)
Content.Position = UDim2.new(0, 10, 0, 90)
Content.BackgroundTransparency = 1
Content.Parent = MainFrame

local tabs = {"Player","Others","Tools"}
local tabButtons = {}
local pages = {}

local function switchTab(name)
    for t, frame in pairs(pages) do
        frame.Visible = (t == name)
    end
    for t, btn in pairs(tabButtons) do
        btn.BackgroundColor3 = (t == name) and Color3.fromRGB(90,90,90) or Color3.fromRGB(60,60,60)
    end
end

for i, name in ipairs(tabs) do
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(0, 140, 0, 30)
    btn.Position = UDim2.new(0, (i-1)*150, 0, 0)
    btn.Text = name
    btn.Font = Enum.Font.GothamBold
    btn.TextSize = 14
    btn.TextColor3 = Color3.fromRGB(255,255,255)
    btn.BackgroundColor3 = Color3.fromRGB(60,60,60)
    btn.Parent = TabBar
    tabButtons[name] = btn

    local page = Instance.new("Frame")
    page.Size = UDim2.new(1, 0, 1, 0)
    page.BackgroundTransparency = 1
    page.Visible = false
    page.Parent = Content
    pages[name] = page

    btn.MouseButton1Click:Connect(function()
        switchTab(name)
    end)
end
switchTab("Player")

----------------------------------------------------------------
-- UTILS
local function safeHumanoid()
    if LocalPlayer.Character then
        return LocalPlayer.Character:FindFirstChildOfClass("Humanoid")
    end
end

local function makeBtn(parent, text, x, y, w, h, cb)
    local b = Instance.new("TextButton")
    b.Size = UDim2.new(0, w or 140, 0, h or 35)
    b.Position = UDim2.new(0, x, 0, y)
    b.Text = text
    b.Font = Enum.Font.GothamBold
    b.TextSize = 14
    b.TextColor3 = Color3.fromRGB(255,255,255)
    b.BackgroundColor3 = Color3.fromRGB(70,70,70)
    b.Parent = parent
    if cb then b.MouseButton1Click:Connect(cb) end
    return b
end

local function makeBox(parent, placeholder, x, y, w, h)
    local t = Instance.new("TextBox")
    t.Size = UDim2.new(0, w or 120, 0, h or 35)
    t.Position = UDim2.new(0, x, 0, y)
    t.PlaceholderText = placeholder
    t.ClearTextOnFocus = false
    t.Font = Enum.Font.Gotham
    t.TextSize = 14
    t.TextColor3 = Color3.fromRGB(0,0,0)
    t.BackgroundColor3 = Color3.fromRGB(200,200,200)
    t.Parent = parent
    return t
end

----------------------------------------------------------------
-- PLAYER
local playerPage = pages["Player"]

-- Speed
local speedBox = makeBox(playerPage, "Speed", 160, 0, 100, 35)
makeBtn(playerPage, "Set", 270, 0, 45, 35, function()
    local hum = safeHumanoid()
    local v = tonumber(speedBox.Text)
    if hum and v then hum.WalkSpeed = v end
end)

-- Jump
local jumpBox = makeBox(playerPage, "Jump", 160, 50, 100, 35)
makeBtn(playerPage, "Set", 270, 50, 45, 35, function()
    local hum = safeHumanoid()
    local v = tonumber(jumpBox.Text)
    if hum and v then hum.JumpPower = v end
end)

-- Fly GUI
makeBtn(playerPage, "Fly GUI", 0, 0, 140, 35, function()
    loadstring(game:HttpGet("https://raw.githubusercontent.com/XNEOFF/FlyGuiV3/main/FlyGuiV3.txt"))()
end)

-- ESP (Highlight)
local espEnabled = false
local highlights = {}
local function clearESP()
    for plr, h in pairs(highlights) do
        if h and h.Destroy then pcall(function() h:Destroy() end) end
        highlights[plr] = nil
    end
end
makeBtn(playerPage, "ESP", 0, 50, 140, 35, function()
    espEnabled = not espEnabled
    clearESP()
    if espEnabled then
        for _, p in ipairs(Players:GetPlayers()) do
            if p ~= LocalPlayer and p.Character then
                local h = Instance.new("Highlight")
                h.FillTransparency = 1
                h.OutlineColor = Color3.fromRGB(255, 0, 0)
                h.Adornee = p.Character
                h.Parent = CoreGui
                highlights[p] = h
            end
        end
    end
end)
Players.PlayerAdded:Connect(function(p)
    if espEnabled then
        p.CharacterAdded:Connect(function(char)
            task.wait(0.2)
            local h = Instance.new("Highlight")
            h.FillTransparency = 1
            h.OutlineColor = Color3.fromRGB(255, 0, 0)
            h.Adornee = char
            h.Parent = CoreGui
            highlights[p] = h
        end)
    end
end)
Players.PlayerRemoving:Connect(function(p)
    if highlights[p] then pcall(function() highlights[p]:Destroy() end) highlights[p] = nil end
end)

-- Noclip
local noclip = false
local noclipConn
makeBtn(playerPage, "Noclip", 0, 100, 140, 35, function()
    noclip = not noclip
    if noclipConn then noclipConn:Disconnect() noclipConn = nil end
    if noclip then
        noclipConn = RunService.Stepped:Connect(function()
            local char = LocalPlayer.Character
            if char then
                for _, part in ipairs(char:GetDescendants()) do
                    if part:IsA("BasePart") then part.CanCollide = false end
                end
            end
        end)
    end
end)

-- Invisibilidade (tornar transparente as partes do personagem)
makeBtn(playerPage, "Invisibilidade", 160, 100, 155, 35, function()
    local char = LocalPlayer.Character
    if not char then return end
    for _, p in ipairs(char:GetDescendants()) do
        if p:IsA("BasePart") or p:IsA("MeshPart") then
            p.Transparency = 1
            if p:FindFirstChildOfClass("Decal") then
                for _, d in ipairs(p:GetChildren()) do
                    if d:IsA("Decal") then d.Transparency = 1 end
                end
            end
        end
    end
end)

----------------------------------------------------------------
-- OTHERS
local othersPage = pages["Others"]

-- Otimização
makeBtn(othersPage, "Otimização", 0, 0, 140, 35, function()
    Lighting.GlobalShadows = false
    Lighting.FogEnd = 9e9
    Lighting.Brightness = 2
    for _, v in ipairs(workspace:GetDescendants()) do
        if v:IsA("BasePart") then
            v.Material = Enum.Material.Plastic
            v.Reflectance = 0
        elseif v:IsA("Decal") or v:IsA("Texture") then
            v.Transparency = 1
        elseif v:IsA("ParticleEmitter") or v:IsA("Trail") then
            v.Enabled = false
        end
    end
end)

-- Marcador de FPS
local fpsActive = false
local fpsLabel -- colocado no ScreenGui (HUD)
makeBtn(othersPage, "Marcador de FPS", 0, 50, 140, 35, function()
    fpsActive = not fpsActive
    if fpsActive then
        if not fpsLabel then
            fpsLabel = Instance.new("TextLabel")
            fpsLabel.Name = "FPSLabel"
            fpsLabel.Size = UDim2.new(0, 110, 0, 30)
            fpsLabel.Position = UDim2.new(1, -120, 0, 10)
            fpsLabel.BackgroundColor3 = Color3.fromRGB(0, 0, 0)
            fpsLabel.BackgroundTransparency = 0.35
            fpsLabel.TextColor3 = Color3.fromRGB(0, 255, 0)
            fpsLabel.Text = "FPS: 0"
            fpsLabel.TextScaled = true
            fpsLabel.Parent = ScreenGui
        end
        local last = tick()
        local frames = 0
        -- cria uma conexão por toggle; para simplificar, recriamos
        if fpsLabel:GetAttribute("Conn") then
            local old = fpsLabel:GetAttribute("Conn")
            if typeof(old) == "RBXScriptConnection" then pcall(function() old:Disconnect() end) end
        end
        local conn = RunService.RenderStepped:Connect(function()
            if not fpsActive then return end
            frames += 1
            local now = tick()
            if now - last >= 1 then
                fpsLabel.Text = "FPS: " .. frames
                frames = 0
                last = now
            end
        end)
        fpsLabel:SetAttribute("Conn", conn)
    else
        if fpsLabel then
            local old = fpsLabel:GetAttribute("Conn")
            if typeof(old) == "RBXScriptConnection" then pcall(function() old:Disconnect() end) end
            fpsLabel:Destroy()
            fpsLabel = nil
        end
    end
end)

-- Tocador de música (ID)
local musicBox = makeBox(othersPage, "ID da música", 0, 110, 140, 35)
local sound = Instance.new("Sound")
sound.Name = "GalaxyMusic"
sound.Parent = workspace
sound.Volume = 5
makeBtn(othersPage, "Play", 150, 110, 70, 35, function()
    local id = tonumber(musicBox.Text)
    if id then
        sound.SoundId = "rbxassetid://"..id
        sound.Looped = true
        sound:Play()
    end
end)
makeBtn(othersPage, "Stop", 230, 110, 70, 35, function()
    sound:Stop()
end)

-- Infinite Yield
makeBtn(othersPage, "Infinite Yield", 0, 160, 140, 35, function()
    loadstring(game:HttpGet('https://raw.githubusercontent.com/EdgeIY/infiniteyield/master/source'))()
end)

----------------------------------------------------------------
-- TOOLS
local toolsPage = pages["Tools"]

-- TP Tool
makeBtn(toolsPage, "TP Tool", 0, 0, 140, 35, function()
    local tool = Instance.new("Tool")
    tool.Name = "TP Tool"
    tool.RequiresHandle = false
    tool.Parent = LocalPlayer.Backpack
    tool.Activated:Connect(function()
        local mouse = LocalPlayer:GetMouse()
        if mouse and mouse.Hit and LocalPlayer.Character and LocalPlayer.Character:FindFirstChild("HumanoidRootPart") then
            LocalPlayer.Character.HumanoidRootPart.CFrame = CFrame.new(mouse.Hit.p + Vector3.new(0, 3, 0))
        end
    end)
end)

-- Fling Tool (com mini GUI Stop)
makeBtn(toolsPage, "Fling Tool", 0, 50, 140, 35, function()
    local tool = Instance.new("Tool")
    tool.Name = "Fling Tool"
    tool.RequiresHandle = false
    tool.Parent = LocalPlayer.Backpack

    local flingActive = false
    local loopConn

    local function stopFling(mini)
        flingActive = false
        if loopConn then loopConn:Disconnect() loopConn = nil end
        if mini then pcall(function() mini:Destroy() end) end
    end

    tool.Activated:Connect(function()
        local mouse = LocalPlayer:GetMouse()
        local targetChar = mouse.Target and mouse.Target.Parent
        local targetHum = targetChar and targetChar:FindFirstChildOfClass("Humanoid")
        local targetRoot = targetChar and targetChar:FindFirstChild("HumanoidRootPart")

        if not (targetHum and targetRoot) then return end
        local myChar = LocalPlayer.Character
        local myRoot = myChar and myChar:FindFirstChild("HumanoidRootPart")
        if not myRoot then return end

        flingActive = true

        -- Mini GUI
        local mini = Instance.new("ScreenGui")
        mini.Name = "FlingMini"
        mini.Parent = ScreenGui
        local stopBtn = Instance.new("TextButton")
        stopBtn.Size = UDim2.new(0, 110, 0, 36)
        stopBtn.Position = UDim2.new(0.5, -55, 0, 60)
        stopBtn.Text = "Stop Fling"
        stopBtn.Font = Enum.Font.GothamBold
        stopBtn.TextSize = 16
        stopBtn.TextColor3 = Color3.fromRGB(255,255,255)
        stopBtn.BackgroundColor3 = Color3.fromRGB(200, 0, 0)
        stopBtn.Parent = mini
        stopBtn.MouseButton1Click:Connect(function() stopFling(mini) end)

        -- Loop: orbitar girando e "descendo"
        local angle = 0
        loopConn = RunService.Heartbeat:Connect(function(dt)
            if not flingActive then return end
            if not (myRoot and targetRoot and targetChar.Parent) then
                stopFling(mini)
                return
            end
            angle += dt * 6 * math.pi -- gira rápido
            local radius = 4
            local height = math.sin(tick()*2) * 1.5 - 3 -- fica um pouco abaixo
            local offset = Vector3.new(math.cos(angle)*radius, height, math.sin(angle)*radius)
            myRoot.CFrame = CFrame.new(targetRoot.Position + offset)
            myRoot.Velocity = (targetRoot.Position - myRoot.Position).Unit * 180 + Vector3.new(0, -120, 0)
        end)
    end)
end)

----------------------------------------------------------------
-- Botão de minimizar (☰)
local MinBtn = Instance.new("TextButton")
MinBtn.Size = UDim2.new(0, 40, 0, 40)
MinBtn.Position = UDim2.new(0, 10, 0, 10)
MinBtn.Text = "☰"
MinBtn.Font = Enum.Font.GothamBold
MinBtn.TextSize = 24
MinBtn.TextColor3 = Color3.fromRGB(255, 255, 255)
MinBtn.BackgroundColor3 = Color3.fromRGB(50, 50, 50)
MinBtn.Parent = ScreenGui
MinBtn.ZIndex = 999

local minimized = false
MinBtn.MouseButton1Click:Connect(function()
    minimized = not minimized
    MainFrame.Visible = not minimized
    Footer.Visible = not minimized
end)
--------------------------------------------------------
-- ABA GAMES (nova aba no topo)
table.insert(tabs, "Games")

local btnGames = Instance.new("TextButton")
btnGames.Size = UDim2.new(0, 140, 0, 30)
btnGames.Position = UDim2.new(0, 450, 0, 0)
btnGames.Text = "Games"
btnGames.Font = Enum.Font.GothamBold
btnGames.TextSize = 14
btnGames.TextColor3 = Color3.fromRGB(255,255,255)
btnGames.BackgroundColor3 = Color3.fromRGB(60,60,60)
btnGames.Parent = TabBar
tabButtons["Games"] = btnGames

local gamesPage = Instance.new("Frame")
gamesPage.Size = UDim2.new(1, 0, 1, 0)
gamesPage.BackgroundTransparency = 1
gamesPage.Visible = false
gamesPage.Parent = Content
pages["Games"] = gamesPage

btnGames.MouseButton1Click:Connect(function()
    switchTab("Games")
end)

y = 0
makeBtn(gamesPage, "Blox Fruits", 0, y, 200, 35, function()
    loadstring(game:HttpGet("https://raw.githubusercontent.com/tlredz/Scripts/refs/heads/main/main.luau"))()
end)
y = y + 45
makeBtn(gamesPage, "Grow a Garden", 0, y, 200, 35, function()
    loadstring(game:HttpGet("https://rawscripts.net/raw/Grow-a-Garden-eF-Hub-45212"))()
end)
y = y + 45
makeBtn(gamesPage, "Car Dealership Tycoon", 0, y, 200, 35, function()
    loadstring(game:HttpGet("https://rawscripts.net/raw/HYPERCARS!-Car-Dealership-Tycoon-acceleration-changer-46631"))()
end)
y = y + 45
makeBtn(gamesPage, "Blade Ball", 0, y, 200, 35, function()
    loadstring(game:HttpGet("https://rawscripts.net/raw/Universal-Script-CludeHub-9824"))()
end)
y = y + 45
makeBtn(gamesPage, "Steal a Brainrot", 0, y, 200, 35, function()
    loadstring(game:HttpGet("https://pastefy.app/mTbfVy0H/raw", true))()
end)
y = y + 45
makeBtn(gamesPage, "Brookhaven", 0, y, 200, 35, function()
    loadstring(game:HttpGet("https://raw.githubusercontent.com/Daivd977/Deivd999/refs/heads/main/pessal"))()
end)
y = y + 45
makeBtn(gamesPage, "99 Noites", 0, y, 200, 35, function()
    loadstring(game:HttpGet("https://raw.githubusercontent.com/AhmadV99/Speed-Hub-X/main/Speed%20Hub%20X.lua", true))()
end)

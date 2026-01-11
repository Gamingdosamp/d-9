-- Painel Hub Universal Roblox

local Players = game:GetService("Players")
local UIS = game:GetService("UserInputService")
local RunService = game:GetService("RunService")

local player = Players.LocalPlayer
local playerGui = player:WaitForChild("PlayerGui")

-- Função para criar cor a partir de hex
local function HexToColor3(hex)
    hex = hex:gsub("#","")
    if #hex ~= 6 then return Color3.new(1,1,0) end
    local r = tonumber(hex:sub(1,2),16)/255
    local g = tonumber(hex:sub(3,4),16)/255
    local b = tonumber(hex:sub(5,6),16)/255
    return Color3.new(r,g,b)
end

-- Criar ScreenGui
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "D9Hub"
screenGui.ResetOnSpawn = false
screenGui.Parent = playerGui

-- Botão inicial para abrir/fechar o painel
local initButton = Instance.new("TextButton")
initButton.Size = UDim2.new(0, 60, 0, 60)
initButton.Position = UDim2.new(0, 10, 1, -70)
initButton.BackgroundColor3 = Color3.fromRGB(40, 40, 45)
initButton.TextColor3 = Color3.new(1,1,1)
initButton.Font = Enum.Font.SourceSansBold
initButton.TextSize = 28
initButton.Text = "D'9"
initButton.AutoButtonColor = true
initButton.Name = "InitButton"
initButton.Parent = screenGui

local initCorner = Instance.new("UICorner", initButton)
initCorner.CornerRadius = UDim.new(0, 10)

-- Painel principal
local mainFrame = Instance.new("Frame")
mainFrame.Size = UDim2.new(0, 380, 0, 460)
mainFrame.Position = UDim2.new(0.5, -190, 0.5, -230)
mainFrame.BackgroundColor3 = Color3.fromRGB(30, 30, 35)
mainFrame.BorderSizePixel = 0
mainFrame.AnchorPoint = Vector2.new(0.5, 0.5)
mainFrame.Visible = false
mainFrame.Parent = screenGui

local uiCorner = Instance.new("UICorner", mainFrame)
uiCorner.CornerRadius = UDim.new(0, 14)

-- Título
local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, 0, 0, 40)
title.Position = UDim2.new(0, 0, 0, 0)
title.BackgroundColor3 = Color3.fromRGB(20, 20, 25)
title.Text = "D'9 Universal Hub"
title.TextColor3 = Color3.new(1,1,1)
title.Font = Enum.Font.SourceSansBold
title.TextSize = 26
title.Parent = mainFrame

local titleCorner = Instance.new("UICorner", title)
titleCorner.CornerRadius = UDim.new(0, 14)

-- Container para os botões
local buttonsFrame = Instance.new("Frame")
buttonsFrame.Size = UDim2.new(1, -20, 1, -60)
buttonsFrame.Position = UDim2.new(0, 10, 0, 50)
buttonsFrame.BackgroundTransparency = 1
buttonsFrame.Parent = mainFrame

local uiList = Instance.new("UIListLayout")
uiList.Padding = UDim.new(0, 10)
uiList.FillDirection = Enum.FillDirection.Vertical
uiList.SortOrder = Enum.SortOrder.LayoutOrder
uiList.Parent = buttonsFrame

-- Função para criar botões
local function createButton(text, parent, callback)
    local btn = Instance.new("TextButton")
    btn.Size = UDim2.new(1, 0, 0, 40)
    btn.BackgroundColor3 = Color3.fromRGB(60, 60, 70)
    btn.TextColor3 = Color3.new(1,1,1)
    btn.Font = Enum.Font.SourceSansBold
    btn.TextSize = 20
    btn.Text = text
    btn.Parent = parent
    btn.AutoButtonColor = true
    btn.MouseButton1Click:Connect(callback)

    local corner = Instance.new("UICorner", btn)
    corner.CornerRadius = UDim.new(0, 8)

    return btn
end

-- Variáveis para comandos
local flying = false
local flySpeed = 50
local flyForce = nil

-- Função Fly simples
local function toggleFly()
    local character = player.Character
    if not character or not character:FindFirstChild("HumanoidRootPart") or not character:FindFirstChild("Humanoid") then return end

    local hrp = character.HumanoidRootPart
    local humanoid = character.Humanoid

    if flying then
        flying = false
        if flyForce then
            flyForce:Destroy()
            flyForce = nil
        end
        humanoid.PlatformStand = false
    else
        flying = true
        humanoid.PlatformStand = true

        flyForce = Instance.new("BodyVelocity")
        flyForce.Velocity = Vector3.new(0,0,0)
        flyForce.MaxForce = Vector3.new(1e5,1e5,1e5)
        flyForce.Parent = hrp

        -- Atualizar o movimento de voo com teclado
        RunService:BindToRenderStep("FlyMovement", Enum.RenderPriority.Camera.Value, function()
            if not flying then
                RunService:UnbindFromRenderStep("FlyMovement")
                return
            end
            local moveDirection = Vector3.new()
            if UIS:IsKeyDown(Enum.KeyCode.W) then moveDirection = moveDirection + workspace.CurrentCamera.CFrame.LookVector end
            if UIS:IsKeyDown(Enum.KeyCode.S) then moveDirection = moveDirection - workspace.CurrentCamera.CFrame.LookVector end
            if UIS:IsKeyDown(Enum.KeyCode.A) then moveDirection = moveDirection - workspace.CurrentCamera.CFrame.RightVector end
            if UIS:IsKeyDown(Enum.KeyCode.D) then moveDirection = moveDirection + workspace.CurrentCamera.CFrame.RightVector end
            if UIS:IsKeyDown(Enum.KeyCode.Space) then moveDirection = moveDirection + Vector3.new(0,1,0) end
            if UIS:IsKeyDown(Enum.KeyCode.LeftControl) then moveDirection = moveDirection - Vector3.new(0,1,0) end

            flyForce.Velocity = moveDirection.Unit * flySpeed
        end)
    end
end

-- Velocidade de caminhada e pulo padrão (para reset)
local defaultWalkSpeed = 16
local defaultJumpPower = 50

-- Botão Fly
createButton("Toggle Fly", buttonsFrame, function()
    toggleFly()
end)

-- Botão WalkSpeed
createButton("Set WalkSpeed to 50", buttonsFrame, function()
    local humanoid = player.Character and player.Character:FindFirstChildOfClass("Humanoid")
    if humanoid then
        humanoid.WalkSpeed = 50
    end
end)

-- Botão Reset WalkSpeed
createButton("Reset WalkSpeed", buttonsFrame, function()
    local humanoid = player.Character and player.Character:FindFirstChildOfClass("Humanoid")
    if humanoid then
        humanoid.WalkSpeed = defaultWalkSpeed
    end
end)

-- Botão JumpPower
createButton("Set JumpPower to 100", buttonsFrame, function()
    local humanoid = player.Character and player.Character:FindFirstChildOfClass("Humanoid")
    if humanoid then
        humanoid.JumpPower = 100
    end
end)

-- Botão Reset JumpPower
createButton("Reset JumpPower", buttonsFrame, function()
    local humanoid = player.Character and player.Character:FindFirstChildOfClass("Humanoid")
    if humanoid then
        humanoid.JumpPower = defaultJumpPower
    end
end)

-- Função Teleport para jogador
local function tpToPlayer(targetName)
    local target = nil
    for _, p in pairs(Players:GetPlayers()) do
        if p.Name:lower():find(targetName:lower()) then
            target = p
            break
        end
    end
    if target and target.Character and target.Character:FindFirstChild("HumanoidRootPart") and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
        player.Character.HumanoidRootPart.CFrame = target.Character.HumanoidRootPart.CFrame + Vector3.new(0, 3, 0)
    else
        warn("Jogador não encontrado ou personagem inválido")
    end
end

-- Criar campo e botão para teleport
local tpBox = Instance.new("TextBox")
tpBox.PlaceholderText = "Digite nome para teleportar"
tpBox.Size = UDim2.new(1, 0, 0, 40)
tpBox.Position = UDim2.new(0, 0, 0, 320)
tpBox.BackgroundColor3 = Color3.fromRGB(50, 50, 55)
tpBox.TextColor3 = Color3.new(1,1,1)
tpBox.Font = Enum.Font.SourceSans
tpBox.TextSize = 18
tpBox.Parent = buttonsFrame

local tpBtn = createButton("Teleport to Player", buttonsFrame, function()
    local targetName = tpBox.Text
    if targetName ~= "" then
        tpToPlayer(targetName)
    end
end)

-- ESP simples: colocar nome em cima dos jogadores
local espEnabled = false
local espTags = {}

local function createTag(plr)
    if espTags[plr] then return end
    local tag = Instance.new("BillboardGui")
    tag.Adornee = plr.Character and plr.Character:FindFirstChild("Head")
    if not tag.Adornee then return end
    tag.Size = UDim2.new(0, 100, 0, 30)
    tag.AlwaysOnTop = true
    tag.Parent = plr.Character.Head

    local label = Instance.new("TextLabel")
    label.Size = UDim2.new(1,0,1,0)
    label.BackgroundTransparency = 1
    label.TextColor3 = Color3.new(1, 0.5, 0)
    label.Font = Enum.Font.SourceSansBold
    label.TextStrokeTransparency = 0.5
    label.Text = plr.Name
    label.Parent = tag

    espTags[plr] = tag
end

local function removeTag(plr)
    if espTags[plr] then
        espTags[plr]:Destroy()
        espTags[plr] = nil
    end
end

local function toggleESP()
    espEnabled = not espEnabled
    if espEnabled then
        for _, plr in pairs(Players:GetPlayers()) do
            if plr.Character and plr.Character:FindFirstChild("Head") then
                createTag(plr)
            end
        end
        -- Atualizar tags para jogadores que entrarem
        Players.PlayerAdded:Connect(function(plr)
            if espEnabled and plr.Character and plr.Character:FindFirstChild("Head") then
                createTag(plr)
            end
            plr.CharacterAdded:Connect(function(char)
                if espEnabled and char:FindFirstChild("Head") then
                    createTag(plr)
                end
            end)
        end)
    else
        for plr,_ in pairs(espTags) do
            removeTag(plr)
        end
    end
end

createButton("Toggle ESP", buttonsFrame, toggleESP)

-- Toggle painel via botão inicial
local function togglePanel()
    mainFrame.Visible = not mainFrame.Visible
end

initButton.MouseButton1Click:Connect(togglePanel)

-- Toggle painel via tecla B
UIS.InputBegan:Connect(function(input, gameProcessed)
    if not gameProcessed then
        if input.UserInputType == Enum.UserInputType.Keyboard then
            if input.KeyCode == Enum.KeyCode.B then
                togglePanel()
            end
        end
    end
end)

print("D'9 Universal Hub carregado! Use o botão 'D'9' ou tecla B para abrir o painel.")

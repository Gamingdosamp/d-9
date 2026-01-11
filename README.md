-- Painel Universal Roblox - GUI LocalScript

local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UIS = game:GetService("UserInputService")

local player = Players.LocalPlayer
local playerGui = player:WaitForChild("PlayerGui")

-- Função para converter HEX em Color3
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
screenGui.Name = "D9UltimatePanel"
screenGui.ResetOnSpawn = false
screenGui.Parent = playerGui

-- Botão fixo (D'9)
local initButton = Instance.new("TextButton")
initButton.Size = UDim2.new(0, 50, 0, 50)
initButton.Position = UDim2.new(0, 10, 1, -60)
initButton.BackgroundColor3 = Color3.fromRGB(40,40,45)
initButton.TextColor3 = Color3.new(1,1,1)
initButton.Font = Enum.Font.SourceSansBold
initButton.TextSize = 24
initButton.Text = "D'9"
initButton.AutoButtonColor = true
initButton.Name = "InitButton"
initButton.Parent = screenGui

local initCorner = Instance.new("UICorner", initButton)
initCorner.CornerRadius = UDim.new(0, 10)

-- Painel Principal
local mainFrame = Instance.new("Frame")
mainFrame.Size = UDim2.new(0, 400, 0, 500)
mainFrame.Position = UDim2.new(0.5, -200, 0.5, -250)
mainFrame.BackgroundColor3 = Color3.fromRGB(35, 35, 40)
mainFrame.BorderSizePixel = 0
mainFrame.AnchorPoint = Vector2.new(0.5, 0.5)
mainFrame.Visible = false
mainFrame.Parent = screenGui

local uiCorner = Instance.new("UICorner", mainFrame)
uiCorner.CornerRadius = UDim.new(0, 10)

-- Título
local title = Instance.new("TextLabel")
title.Size = UDim2.new(1, 0, 0, 40)
title.Position = UDim2.new(0, 0, 0, 0)
title.BackgroundColor3 = Color3.fromRGB(25, 25, 30)
title.Text = "D'9 Ultimate Panel"
title.TextColor3 = Color3.new(1, 1, 1)
title.Font = Enum.Font.SourceSansBold
title.TextSize = 24
title.Parent = mainFrame

local titleCorner = Instance.new("UICorner", title)
titleCorner.CornerRadius = UDim.new(0, 10)

-- Exemplo botão interno
local exampleButton = Instance.new("TextButton")
exampleButton.Size = UDim2.new(0.9, 0, 0, 40)
exampleButton.Position = UDim2.new(0.05, 0, 0, 60)
exampleButton.BackgroundColor3 = Color3.fromRGB(70, 70, 85)
exampleButton.TextColor3 = Color3.new(1, 1, 1)
exampleButton.Text = "Exemplo: Olá Mundo!"
exampleButton.Font = Enum.Font.SourceSansBold
exampleButton.TextScaled = true
exampleButton.Parent = mainFrame

exampleButton.MouseButton1Click:Connect(function()
    print("Botão do painel clicado!")
end)

-- Função toggle painel
local function togglePanel()
    mainFrame.Visible = not mainFrame.Visible
end

-- Conectar botão D'9
initButton.MouseButton1Click:Connect(togglePanel)

-- Conectar tecla B para abrir/fechar painel
UIS.InputBegan:Connect(function(input, gameProcessed)
    if not gameProcessed then
        if input.UserInputType == Enum.UserInputType.Keyboard then
            if input.KeyCode == Enum.KeyCode.B then
                togglePanel()
            end
        end
    end
end)

print("D'9 Ultimate Panel carregado! Aperte B ou clique em D'9 para abrir/fechar.")

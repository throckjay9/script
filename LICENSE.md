
local Players = game:GetService("Players")
local UserInputService = game:GetService("UserInputService")
local player = Players.LocalPlayer

-- Criar a GUI
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "FF_GUI"
screenGui.ResetOnSpawn = false
screenGui.Parent = player:WaitForChild("PlayerGui")

-- Criar o Frame da GUI
local mainFrame = Instance.new("Frame")
mainFrame.Name = "MainFrame"
mainFrame.Size = UDim2.new(0.3, 0, 0.35, 0)
mainFrame.Position = UDim2.new(0.35, 0, 0.15, 0)
mainFrame.BackgroundColor3 = Color3.fromRGB(25, 25, 25)
mainFrame.BackgroundTransparency = 0.1
mainFrame.BorderSizePixel = 0
mainFrame.Visible = false
mainFrame.Parent = screenGui

-- UICorner para cantos arredondados no Frame
local frameCorner = Instance.new("UICorner")
frameCorner.CornerRadius = UDim.new(0, 12)
frameCorner.Parent = mainFrame

-- Título da GUI
local titleLabel = Instance.new("TextLabel")
titleLabel.Name = "TitleLabel"
titleLabel.Size = UDim2.new(1, 0, 0.2, 0)
titleLabel.Position = UDim2.new(0, 0, 0, 0)
titleLabel.BackgroundTransparency = 1
titleLabel.Text = "🧠 FF MAX MENU"
titleLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
titleLabel.TextScaled = true
titleLabel.Font = Enum.Font.GothamBold
titleLabel.Parent = mainFrame

-- Função para criar botões estilizados
local function createButton(text, positionY)
	local button = Instance.new("TextButton")
	button.Size = UDim2.new(0.8, 0, 0.18, 0)
	button.Position = UDim2.new(0.1, 0, positionY, 0)
	button.Text = text
	button.BackgroundColor3 = Color3.fromRGB(40, 40, 40)
	button.TextColor3 = Color3.fromRGB(255, 255, 255)
	button.Font = Enum.Font.Gotham
	button.TextScaled = true
	button.AutoButtonColor = true
	button.Parent = mainFrame

	local corner = Instance.new("UICorner")
	corner.CornerRadius = UDim.new(0, 8)
	corner.Parent = button

	return button
end

-- Criar os botões
local aimbotOn = false
local aimbotButton = createButton("Ativar Aimbot", 0.25)
aimbotButton.MouseButton1Click:Connect(function()
	aimbotOn = not aimbotOn
	aimbotButton.Text = aimbotOn and "Desativar Aimbot" or "Ativar Aimbot"
	print("Aimbot:", aimbotOn)
end)

local espBoxOn = false
local espBoxButton = createButton("Ativar ESP Caixa", 0.47)
espBoxButton.MouseButton1Click:Connect(function()
	espBoxOn = not espBoxOn
	espBoxButton.Text = espBoxOn and "Desativar ESP Caixa" or "Ativar ESP Caixa"
	print("ESP Caixa:", espBoxOn)
end)

local espLineOn = false
local espLineButton = createButton("Ativar ESP Linha", 0.69)
espLineButton.MouseButton1Click:Connect(function()
	espLineOn = not espLineOn
	espLineButton.Text = espLineOn and "Desativar ESP Linha" or "Ativar ESP Linha"
	print("ESP Linha:", espLineOn)
end)

-- Toggle da GUI com tecla T (ou mude pra outra se quiser)
local guiVisible = false
UserInputService.InputBegan:Connect(function(input, gameProcessed)
	if not gameProcessed and input.KeyCode == Enum.KeyCode.T then
		guiVisible = not guiVisible
		mainFrame.Visible = guiVisible
	end
end)

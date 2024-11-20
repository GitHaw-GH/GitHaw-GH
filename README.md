-- LocalScript em StarterPlayerScripts

local player = game.Players.LocalPlayer
local playerGui = player:WaitForChild("PlayerGui")
local character = player.Character or player.CharacterAdded:Wait()
local humanoid = character:WaitForChild("Humanoid")

-- Configurações gerais
local sprintSpeed = 22  -- Velocidade do sprint
local walkSpeed = humanoid.WalkSpeed
local sprintDuration = 7 -- Duração do sprint (segundos)
local sprintCooldown = 7 -- Tempo para regenerar a barra de sprint (segundos)
local sprintValue = 1 -- Proporção inicial da barra (entre 0 e 1)

-- Criar GUI principal
local screenGui = Instance.new("ScreenGui")
screenGui.Name = "SprintUI"
screenGui.Parent = playerGui

-- Exibir mensagens no mesmo local
local messageLabel = Instance.new("TextLabel")
messageLabel.Size = UDim2.new(0.8, 0, 0.1, 0)
messageLabel.Position = UDim2.new(0.1, 0, 0.85, 0) -- Próximo ao fundo
messageLabel.BackgroundTransparency = 1
messageLabel.TextColor3 = Color3.new(1, 1, 1)
messageLabel.Font = Enum.Font.Oswald
messageLabel.TextScaled = true
messageLabel.Text = ""
messageLabel.Parent = screenGui

local function displayMessages(messages, delayTime)
    for _, text in ipairs(messages) do
        messageLabel.Text = text
        wait(delayTime)
    end
    messageLabel.Text = ""
end

-- Mostrar mensagens
local messages = {
    "Hyperextreme Started",
    "Script made by Ellenarte, Sprout & Kaeny",
    "(Images sounds ids) Ellenarte & Kaeny",
    "Script made by Sprout",
    "Inspired in hardcore mode",
    "Good luck"
}

task.spawn(function()
    displayMessages(messages, 2)  -- As mensagens aparecem com 2 segundos de intervalo
end)

-- Criar barra de sprint (inicialmente invisível)
local sprintFrame = Instance.new("Frame")
sprintFrame.Size = UDim2.new(0.3, 0, 0.05, 0)
sprintFrame.Position = UDim2.new(0.35, 0, 0.9, 0)
sprintFrame.BackgroundColor3 = Color3.fromRGB(240, 230, 140) -- Cor areia
sprintFrame.BorderSizePixel = 0
sprintFrame.Visible = false -- Começa invisível
sprintFrame.Parent = screenGui

-- Arredondar a barra
local uiCorner = Instance.new("UICorner")
uiCorner.CornerRadius = UDim.new(0, 12)
uiCorner.Parent = sprintFrame

-- Criar a barra de progresso
local sprintBar = Instance.new("Frame")
sprintBar.Size = UDim2.new(sprintValue, 0, 1, 0)
sprintBar.BackgroundColor3 = Color3.new(0, 0.8, 0.4)
sprintBar.Parent = sprintFrame

local uiCornerBar = Instance.new("UICorner")
uiCornerBar.CornerRadius = UDim.new(0, 12)
uiCornerBar.Parent = sprintBar

-- Botão Sprint (inicialmente invisível)
local sprintButton = Instance.new("TextButton")
sprintButton.Size = UDim2.new(0.15, 0, 0.05, 0)
sprintButton.Position = UDim2.new(0.67, 0, 0.9, 0)
sprintButton.Text = "Sprint"
sprintButton.TextScaled = true
sprintButton.Font = Enum.Font.Oswald
sprintButton.BackgroundColor3 = Color3.new(0.1, 0.1, 0.1)
sprintButton.TextColor3 = Color3.new(1, 1, 1)
sprintButton.Visible = false -- Começa invisível
sprintButton.Parent = screenGui

-- Arredondar o botão
local buttonCorner = Instance.new("UICorner")
buttonCorner.CornerRadius = UDim.new(0, 12)
buttonCorner.Parent = sprintButton

-- Função de recuperação da barra de sprint
local function regenerateSprintBar()
    while sprintValue < 1 do
        sprintValue = sprintValue + (1 / sprintCooldown) * wait()
        sprintBar.Size = UDim2.new(sprintValue, 0, 1, 0)
    end
    sprintValue = 1
end

-- Mostrar barra de sprint e botão após as mensagens
task.spawn(function()
    wait(#messages * 2)  -- Espera o tempo total das mensagens
    sprintFrame.Visible = true -- Exibe a barra de sprint após as mensagens
    sprintButton.Visible = true -- Exibe o botão após as mensagens
end)

-- Lógica do botão de sprint
local sprinting = false
sprintButton.MouseButton1Click:Connect(function()
    if sprintValue > 0 and not sprinting then
        sprinting = true
        humanoid.WalkSpeed = sprintSpeed
        -- Consumir a barra de sprint durante o sprint
        local decrementRate = 1 / sprintDuration
        while sprintValue > 0 and sprinting do
            sprintValue = sprintValue - decrementRate * wait()
            sprintBar.Size = UDim2.new(sprintValue, 0, 1, 0)
        end
        humanoid.WalkSpeed = walkSpeed
        sprinting = false
    end
end)

-- Regeneração contínua da barra de sprint
game:GetService("RunService").RenderStepped:Connect(function()
    if sprintValue < 1 and not sprinting then
        regenerateSprintBar()
    end
end)

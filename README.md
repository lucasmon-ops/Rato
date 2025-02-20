local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local UserInputService = game:GetService("UserInputService")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera

local ESP_ENABLED = true
local AIMBOT_ENABLED = true
local AIMBOT_KEY = Enum.UserInputType.MouseButton2 -- Botão direito do mouse

-- Função de ESP (Destaca inimigos)
local function addESP(player)
    if player ~= LocalPlayer and player.Character then
        local highlight = Instance.new("Highlight")
        highlight.Parent = player.Character
        highlight.FillColor = Color3.fromRGB(255, 0, 0) -- Vermelho para inimigos
        highlight.OutlineColor = Color3.fromRGB(255, 255, 255)
        highlight.FillTransparency = 0.5
    end
end

-- Aplicar ESP a todos os jogadores existentes
for _, player in pairs(Players:GetPlayers()) do
    if ESP_ENABLED then
        addESP(player)
    end
end

-- Atualizar ESP para novos jogadores
Players.PlayerAdded:Connect(function(player)
    if ESP_ENABLED then
        player.CharacterAdded:Connect(function()
            addESP(player)
        end)
    end
end)

-- Função para encontrar o inimigo mais próximo
local function getClosestEnemy()
    local closestEnemy = nil
    local shortestDistance = math.huge
    
    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Team ~= LocalPlayer.Team and player.Character and player.Character:FindFirstChild("Head") then
            local enemyPosition = Camera:WorldToViewportPoint(player.Character.Head.Position)
            local mousePosition = UserInputService:GetMouseLocation()
            local distance = (Vector2.new(enemyPosition.X, enemyPosition.Y) - mousePosition).Magnitude
            
            if distance < shortestDistance then
                shortestDistance = distance
                closestEnemy = player.Character.Head
            end
        end
    end
    return closestEnemy
end

-- Aimbot ativado ao pressionar o botão direito do mouse
UserInputService.InputBegan:Connect(function(input, gameProcessed)
    if not gameProcessed and input.UserInputType == AIMBOT_KEY and AIMBOT_ENABLED then
        local target = getClosestEnemy()
        if target then
            Camera.CFrame = CFrame.new(Camera.CFrame.Position, target.Position)
        end
    end
end)

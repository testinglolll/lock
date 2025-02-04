local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local Camera = game:GetService("Workspace").CurrentCamera
local LocalPlayer = Players.LocalPlayer
local Mouse = LocalPlayer:GetMouse()

local aimLockEnabled = false

-- Função para encontrar o inimigo mais próximo
local function getClosestEnemy()
    local closestEnemy = nil
    local shortestDistance = math.huge -- Valor muito alto para começar

    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("Head") then
            local character = player.Character
            local head = character.Head
            local distance = (head.Position - Camera.CFrame.Position).Magnitude

            if distance < shortestDistance then
                closestEnemy = player
                shortestDistance = distance
            end
        end
    end

    return closestEnemy
end

-- Função para ativar ou desativar o AimLock
local function toggleAimLock()
    aimLockEnabled = not aimLockEnabled
    if aimLockEnabled then
        print("AimLock Ativado")
        -- Conectar a função de atualização de mira
        RunService.RenderStepped:Connect(function()
            local closestEnemy = getClosestEnemy()
            if closestEnemy then
                local enemyHead = closestEnemy.Character and closestEnemy.Character:FindFirstChild("Head")
                if enemyHead then
                    -- Ajustando a mira para a cabeça do inimigo
                    Camera.CFrame = CFrame.new(Camera.CFrame.Position, enemyHead.Position)
                end
            end
        end)
    else
        print("AimLock Desativado")
    end
end

-- Conectar o botão de AimLock na UI
SectionAim:Button({
    text = "AimLock",
    callback = function()
        toggleAimLock()
    end,
})

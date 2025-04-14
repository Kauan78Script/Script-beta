-- Definindo a interface
local player = game.Players.LocalPlayer
local playerGui = player:WaitForChild("PlayerGui")
local screenGui = Instance.new("ScreenGui")
local rebirthButton = Instance.new("TextButton")
local timeLabel = Instance.new("TextLabel")
local eventEndTime = os.time() + (7 * 60 * 60)  -- Evento dura 7 horas a partir de agora (7 horas em segundos)

screenGui.Parent = playerGui
screenGui.Name = "RebirthGui"

rebirthButton.Parent = screenGui
rebirthButton.Size = UDim2.new(0, 200, 0, 50)
rebirthButton.Position = UDim2.new(0.5, -100, 0.5, -50)
rebirthButton.Text = "Ativar Rebirth Automático"
rebirthButton.BackgroundColor3 = Color3.fromRGB(0, 255, 0)

timeLabel.Parent = screenGui
timeLabel.Size = UDim2.new(0, 200, 0, 50)
timeLabel.Position = UDim2.new(0.5, -100, 0.5, 50)
timeLabel.Text = "Tempo Restante: 7h"
timeLabel.BackgroundColor3 = Color3.fromRGB(0, 0, 0)

-- Variáveis de controle
local rebirthsAllowed = 100  -- 100 rebirths por hora
local rebirthCooldown = 3600  -- 1 hora em segundos (3600 segundos = 1h)
local lastRebirthTime = 0  -- Último tempo de Rebirth
local rebirthsMade = 0  -- Contador de rebirths feitos
local rebirthActive = false

-- Função para atualizar o tempo restante
local function updateTimeLabel()
    local currentTime = os.time()
    local eventRemaining = eventEndTime - currentTime
    local timeRemaining = rebirthCooldown - (currentTime - lastRebirthTime)

    -- Atualiza a contagem do evento
    if eventRemaining <= 0 then
        timeLabel.Text = "Evento Finalizado!"
        rebirthButton.Text = "Evento Finalizado"
        rebirthButton.Active = false
    else
        -- Exibe o tempo restante do evento
        timeLabel.Text = "Tempo Restante Evento: " .. math.floor(eventRemaining / 60) .. "m " .. math.floor(eventRemaining % 60) .. "s"
    end
end

-- Função para realizar o Rebirth
local function performRebirth()
    if rebirthsMade < rebirthsAllowed then
        local powerStat = player.leaderstats:FindFirstChild("Power")
        local rebirthStat = player.leaderstats:FindFirstChild("Rebirths")
        
        if powerStat and rebirthStat then
            local rebirthCost = 1000 * (rebirthStat.Value + 1)  -- Cálculo do custo do Rebirth (ajustável)

            if powerStat.Value >= rebirthCost then
                -- Deduz o poder e realiza o rebirth
                powerStat.Value = powerStat.Value - rebirthCost
                rebirthStat.Value = rebirthStat.Value + 1
                player:ResetCharacter()
                rebirthsMade = rebirthsMade + 1
                print("Rebirth realizado! Total de rebirths: " .. rebirthsMade)
            else
                print("Poder insuficiente para Rebirth.")
            end
        end
    else
        print("Você alcançou o limite de 100 rebirths por hora!")
    end
end

-- Função para gerenciar a ativação/desativação do Rebirth
rebirthButton.MouseButton1Click:Connect(function()
    rebirthActive = not rebirthActive
    
    if rebirthActive then
        rebirthButton.Text = "Desativar Rebirth Automático"
        lastRebirthTime = os.time()  -- Marca o momento em que o Rebirth é ativado
        rebirthsMade = 0  -- Reseta o contador de rebirths

        -- Começa o loop de verificação do tempo e realiza o Rebirth quando o tempo permitir
        while rebirthActive do
            updateTimeLabel()
            
            if os.time() - lastRebirthTime >= rebirthCooldown then
                performRebirth()
                lastRebirthTime = os.time()  -- Reseta o tempo após realizar o Rebirth
            end

            wait(1)  -- Verifica a cada segundo
        end
    else
        rebirthButton.Text = "Ativar Rebirth Automático"
    end
end)# Script-beta

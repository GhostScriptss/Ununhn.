local player = game.Players.LocalPlayer
local screenGui = Instance.new("ScreenGui", player.PlayerGui)
local frame = Instance.new("Frame", screenGui)

-- Configurando o Frame
frame.Position = UDim2.new(0.1, 0, 0.1, 0)
frame.Size = UDim2.new(0.2, 0, 0.3, 0)
frame.BackgroundColor3 = Color3.new(0, 0, 0)
frame.BorderSizePixel = 0
frame.BackgroundTransparency = 0.5

-- Títulos e Botões
local title = Instance.new("TextLabel", frame)
title.Text = "GhostScripts"
title.Size = UDim2.new(1, 0, 0.3, 0)
title.BackgroundTransparency = 1
title.TextColor3 = Color3.new(1, 1, 1)
title.Font = Enum.Font.GothamBold
title.TextScaled = true

-- Funções (ESP, Hitbox e Aimbot)
local functions = {"ESP", "Hitbox", "Aimbot"}
local states = {ESP = false, Hitbox = false, Aimbot = false}

for i, funcName in ipairs(functions) do
    local button = Instance.new("TextButton", frame)
    button.Text = funcName .. ": OFF"
    button.Size = UDim2.new(1, -20, 0.2, 0)
    button.Position = UDim2.new(0, 10, 0.3 + (i - 1) * 0.25, 0)
    button.BackgroundColor3 = Color3.new(0.2, 0.2, 0.2)
    button.TextColor3 = Color3.new(1, 1, 1)
    button.Font = Enum.Font.Gotham
    button.TextScaled = true

    -- Alternar estado ao clicar
    button.MouseButton1Click:Connect(function()
        states[funcName] = not states[funcName]
        button.Text = funcName .. ": " .. (states[funcName] and "ON" or "OFF")

        -- Adicione aqui a lógica da funcionalidade
        if funcName == "ESP" then
            if states.ESP then
                -- Ativar ESP
                print("ESP ativado")
                for _, p in pairs(game.Players:GetPlayers()) do
                    if p ~= player then
                        local highlight = Instance.new("Highlight", p.Character or p.CharacterAdded:Wait())
                        highlight.FillColor = Color3.new(1, 0, 0)
                        highlight.OutlineTransparency = 0.5
                    end
                end
            else
                -- Desativar ESP
                print("ESP desativado")
                for _, p in pairs(game.Players:GetPlayers()) do
                    if p ~= player then
                        if p.Character then
                            for _, obj in pairs(p.Character:GetChildren()) do
                                if obj:IsA("Highlight") then
                                    obj:Destroy()
                                end
                            end
                        end
                    end
                end
            end
        elseif funcName == "Hitbox" then
            if states.Hitbox then
                print("Hitbox ativado")
                -- Aumentar hitbox para 30
                for _, p in pairs(game.Players:GetPlayers()) do
                    if p ~= player then
                        if p.Character and p.Character:FindFirstChild("HumanoidRootPart") then
                            p.Character.HumanoidRootPart.Size = Vector3.new(30, 30, 30)
                            p.Character.HumanoidRootPart.Transparency = 0.5
                            p.Character.HumanoidRootPart.CanCollide = false
                        end
                    end
                end
            else
                print("Hitbox desativado")
                -- Restaurar hitbox ao tamanho padrão
                for _, p in pairs(game.Players:GetPlayers()) do
                    if p ~= player then
                        if p.Character and p.Character:FindFirstChild("HumanoidRootPart") then
                            p.Character.HumanoidRootPart.Size = Vector3.new(2, 2, 1)
                            p.Character.HumanoidRootPart.Transparency = 0
                            p.Character.HumanoidRootPart.CanCollide = true
                        end
                    end
                end
            end
        elseif funcName == "Aimbot" then
            if states.Aimbot then
                print("Aimbot ativado")
                -- Aimbot: Mira no jogador mais próximo
                local function getClosestEnemy()
                    local closestPlayer = nil
                    local shortestDistance = math.huge
                    for _, p in pairs(game.Players:GetPlayers()) do
                        if p ~= player and p.Team ~= player.Team then
                            if p.Character and p.Character:FindFirstChild("Head") then
                                local distance = (player.Character.Head.Position - p.Character.Head.Position).Magnitude
                                if distance < shortestDistance then
                                    closestPlayer = p
                                    shortestDistance = distance
                                end
                            end
                        end
                    end
                    return closestPlayer
                end

                game:GetService("RunService").RenderStepped:Connect(function()
                    if states.Aimbot then
                        local target = getClosestEnemy()
                        if target and target.Character and target.Character:FindFirstChild("Head") then
                            -- Ajustar a câmera para mirar no "Head" do inimigo
                            workspace.CurrentCamera.CFrame = CFrame.new(
                                workspace.CurrentCamera.CFrame.Position,
                                target.Character.Head.Position
                            )
                        end
                    end
                end)
            else
                print("Aimbot desativado")
            end
        end
    end)
end

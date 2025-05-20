-- LocalScript em StarterPlayerScripts

-- Variáveis principais
local UIS = game:GetService("UserInputService")
local Players = game:GetService("Players")
local RunService = game:GetService("RunService")
local LocalPlayer = Players.LocalPlayer
local Camera = workspace.CurrentCamera

-- GUI Toggle
local guiEnabled = false

-- Criando a GUI
local screenGui = Instance.new("ScreenGui", LocalPlayer:WaitForChild("PlayerGui"))
screenGui.Name = "FF_GUI"
screenGui.Enabled = false

local frame = Instance.new("Frame", screenGui)
frame.Size = UDim2.new(0.2, 0, 0.1, 0)
frame.Position = UDim2.new(0.4, 0, 0, 0)
frame.BackgroundColor3 = Color3.new(0.1, 0.1, 0.1)
frame.BackgroundTransparency = 0.3

local label = Instance.new("TextLabel", frame)
label.Size = UDim2.new(1, 0, 1, 0)
label.Text = "GARENA FREE FIRE MAX🏆 GUI"
label.TextColor3 = Color3.new(1, 1, 1)
label.BackgroundTransparency = 1

-- Função de ESP
function drawESP(player)
    local box = Drawing.new("Square")
    box.Color = Color3.fromRGB(255, 0, 0)
    box.Thickness = 1
    box.Transparency = 1
    box.Filled = false

    local line = Drawing.new("Line")
    line.Color = Color3.fromRGB(0, 255, 0)
    line.Thickness = 1

    RunService.RenderStepped:Connect(function()
        if player and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
            local pos, onScreen = Camera:WorldToViewportPoint(player.Character.HumanoidRootPart.Position)
            if onScreen then
                local headPos, _ = Camera:WorldToViewportPoint(player.Character:FindFirstChild("Head").Position)
                box.Size = Vector2.new(50, 100)
                box.Position = Vector2.new(pos.X - 25, pos.Y - 50)
                box.Visible = true

                line.From = Vector2.new(Camera.ViewportSize.X / 2, Camera.ViewportSize.Y)
                line.To = Vector2.new(pos.X, pos.Y)
                line.Visible = true
            else
                box.Visible = false
                line.Visible = false
            end
        end
    end)
end

-- Aimbot básico (seguindo a cabeça mais próxima)
function getClosestPlayer()
    local closestDistance = math.huge
    local closestPlayer = nil

    for _, player in pairs(Players:GetPlayers()) do
        if player ~= LocalPlayer and player.Character and player.Character:FindFirstChild("Head") then
            local head = player.Character.Head
            local pos, onScreen = Camera:WorldToViewportPoint(head.Position)
            if onScreen then
                local dist = (Vector2.new(pos.X, pos.Y) - Vector2.new(Camera.ViewportSize.X / 2, Camera.ViewportSize.Y / 2)).Magnitude
                if dist < closestDistance then
                    closestDistance = dist
                    closestPlayer = player
                end
            end
        end
    end

    return closestPlayer
end

-- Aimbot render loop
RunService.RenderStepped:Connect(function()
    if guiEnabled then
        local target = getClosestPlayer()
        if target and target.Character and target.Character:FindFirstChild("Head") then
            Camera.CFrame = CFrame.new(Camera.CFrame.Position, target.Character.Head.Position)
        end
    end
end)

-- Ativando ESP para todos os jogadores
Players.PlayerAdded:Connect(function(player)
    player.CharacterAdded:Connect(function()
        wait(1)
        drawESP(player)
    end)
end)

-- Toggle da GUI e Aimbot
UIS.InputBegan:Connect(function(input, processed)
    if not processed and input.KeyCode == Enum.KeyCode.Q then
        guiEnabled = not guiEnabled
        screenGui.Enabled = guiEnabled
    end
end)

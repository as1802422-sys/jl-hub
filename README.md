--[[
Script MultiFunções (Auto Farm, Aimbot, ESP) para KRNL / Delta / Fluxus
Feito por Copilot
AVISO: Uso apenas educativo em jogo próprio ou privado!
]]

-- Serviços
local Players = game:GetService("Players")
local LocalPlayer = Players.LocalPlayer
local RunService = game:GetService("RunService")
local Workspace = game:GetService("Workspace")

-- UI
local ScreenGui = Instance.new("ScreenGui", game.CoreGui)
local MainFrame = Instance.new("Frame", ScreenGui)
MainFrame.BackgroundColor3 = Color3.fromRGB(40,40,40)
MainFrame.Position = UDim2.new(0.02,0,0.3,0)
MainFrame.Size = UDim2.new(0,190,0,250)
MainFrame.Active = true
MainFrame.Draggable = true

local Title = Instance.new("TextLabel", MainFrame)
Title.Text = "MultiScript Hub"
Title.BackgroundTransparency = 1
Title.Size = UDim2.new(1,0,0,30)
Title.TextColor3 = Color3.new(1,0,0)
Title.Font = Enum.Font.SourceSansBold
Title.TextSize = 18

function createButton(text, y, callback)
    local button = Instance.new("TextButton", MainFrame)
    button.Text = text
    button.Position = UDim2.new(0.05,0,0,(40 * y)+35)
    button.Size = UDim2.new(0.9, 0, 0, 35)
    button.BackgroundColor3 = Color3.fromRGB(70,0,0)
    button.TextColor3 = Color3.fromRGB(255, 255, 255)
    button.Font = Enum.Font.SourceSans
    button.TextSize = 16
    button.MouseButton1Click:Connect(callback)
    return button
end

-- Estado dos hacks
local autofarmOn = false
local aimbotOn = false
local bossfarmOn = false
local espOn     = false

-- Função: AutoFarm NPCs
function killNPCs()
    for _, npc in ipairs(Workspace:GetChildren()) do
        if npc:IsA("Model") and npc:FindFirstChild("Humanoid") and npc:FindFirstChild("HumanoidRootPart") then
            local missionTag = npc:FindFirstChild("MissionTag") or npc:FindFirstChild("QuestTag") -- adapte conforme o jogo
            if missionTag and (npc.Humanoid.Health > 0) then
                local mag = (npc.HumanoidRootPart.Position - LocalPlayer.Character.HumanoidRootPart.Position).magnitude
                if mag < 60 then
                    -- Simular dano como auto-click
                    pcall(function()
                        for i = 1, 5 do -- 5 hits simulados
                            npc.Humanoid.Health = 0
                        end
                    end)
                end
            end
        end
    end
end

-- Função: AutoFarm Boss
function killBosses()
    for _, boss in ipairs(Workspace:GetChildren()) do
        if boss:IsA("Model") and boss:FindFirstChild("Humanoid") and boss:FindFirstChild("HumanoidRootPart") then
            if boss.Name:lower():find("boss") and boss.Humanoid.Health > 0 then
                pcall(function()
                    for i = 1,8 do boss.Humanoid.Health = 0 end
                end)
            end
        end
    end
end

-- Função: Aimbot
function aimbot()
    local closest = nil
    local closestDist = math.huge
    for _,p in ipairs(Players:GetPlayers()) do
        if p ~= LocalPlayer and p.Character and p.Character:FindFirstChild("HumanoidRootPart") and p.Character:FindFirstChild("Humanoid") then
            if p.Character.Humanoid.Health > 0 then
                local mag = (LocalPlayer.Character.HumanoidRootPart.Position - p.Character.HumanoidRootPart.Position).magnitude
                if mag < 70 and mag < closestDist then
                    closestDist = mag
                    closest = p
                end
            end
        end
    end
    if closest and closest.Character and closest.Character:FindFirstChild("HumanoidRootPart") then
        workspace.CurrentCamera.CFrame = CFrame.new(workspace.CurrentCamera.CFrame.Position, closest.Character.HumanoidRootPart.Position)
    end
end

-- ESP (Vermelho)
function enableESP()
    espOn = true
    for _, obj in ipairs(Workspace:GetChildren()) do
        if obj:IsA("Model") and obj:FindFirstChild("HumanoidRootPart") and not obj:FindFirstChild("EspBox") then
            local box = Instance.new("BoxHandleAdornment")
            box.Name = "EspBox"
            box.Adornee = obj.HumanoidRootPart
            box.AlwaysOnTop = true
            box.ZIndex = 10
            box.Size = obj.HumanoidRootPart.Size + Vector3.new(1,1,1)
            box.Color3 = Color3.new(1,0,0)
            box.Transparency = 0.6
            box.Parent = obj
        end
    end
end

function disableESP()
    espOn = false
    for _, obj in ipairs(Workspace:GetChildren()) do
        if obj:IsA("Model") and obj:FindFirstChild("EspBox") then
            obj.EspBox:Destroy()
        end
    end
end

-- Botões
local farmBtn = createButton("Auto Farm Missão [OFF]",1,function()
    autofarmOn = not autofarmOn
    farmBtn.Text = autofarmOn and "Auto Farm Missão [ON]" or "Auto Farm Missão [OFF]"
end)

local bossBtn = createButton("Auto Farm Boss [OFF]",2,function()
    bossfarmOn = not bossfarmOn
    bossBtn.Text = bossfarmOn and "Auto Farm Boss [ON]" or "Auto Farm Boss [OFF]"
end)

local aimBtn = createButton("Aimbot [OFF]",3,function()
    aimbotOn = not aimbotOn
    aimBtn.Text = aimbotOn and "Aimbot [ON]" or "Aimbot [OFF]"
end)

local espBtn = createButton("ESP Vermelho [OFF]",4,function()
    if espOn then disableESP() else enableESP() end
    espBtn.Text = espOn and "ESP Vermelho [ON]" or "ESP Vermelho [OFF]"
end)

-- Loops dos cheats
spawn(function()
    while true do
        if autofarmOn then killNPCs() end
        if bossfarmOn then killBosses() end
        if aimbotOn then aimbot() end
        if espOn then enableESP() end
        wait(0.3)
    end
end)

-- BRIGs (Breakable/desbreakable)
local brigBtn = createButton("Bring NPC [OFF]",5,function()
    local char = LocalPlayer.Character
    if char and char:FindFirstChild("HumanoidRootPart") then
        for _, npc in ipairs(Workspace:GetChildren()) do
            if npc:IsA("Model") and npc:FindFirstChild("HumanoidRootPart") and npc:FindFirstChild("Humanoid") then
                if (npc.HumanoidRootPart.Position - char.HumanoidRootPart.Position).magnitude < 50 then
                    npc.HumanoidRootPart.CFrame = char.HumanoidRootPart.CFrame * CFrame.new(3,0,3)
                end
            end
        end
    end
end)

-- Finalização
print("Script MultiFuncional carregado! Feito por Copilot")

-- Arraste sua tela se não enxergar todos os botões

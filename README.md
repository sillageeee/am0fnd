local Rayfield = loadstring(game:HttpGet('https://sirius.menu/rayfield'))()

local Window = Rayfield:CreateWindow({
   Name = "Xenz Hub", 
   Icon = 0, -- Icon in Topbar. Can use Lucide Icons (string) or Roblox Image (number). 0 to use no icon (default).
   LoadingTitle = "Xenz Cheats", 
   LoadingSubtitle = "by Xenz Cheats", 
   Theme = "Default", -- Check https://docs.sirius.menu/rayfield/configuration/themes

   DisableRayfieldPrompts = false,
   DisableBuildWarnings = false, -- Prevents Rayfield from warning when the script has a version mismatch with the interface

   ConfigurationSaving = {
      Enabled = true,
      FolderName = nil, -- Create a custom folder for your hub/game
      FileName = "Big Hub"
   },

   Discord = {
      Enabled = false, -- Prompt the user to join your Discord server if their executor supports it
      Invite = "noinvitelink", -- The Discord invite code, do not include discord.gg/. E.g. discord.gg/ ABCD would be ABCD
      RememberJoins = true -- Set this to false to make them join the discord every time they load it up
   },

   KeySystem = false, -- Set this to true to use our key system
   KeySettings = {
      Title = "Untitled",
      Subtitle = "Key System",
      Note = "No method of obtaining the key is provided", -- Use this to tell the user how to get a key
      FileName = "Key", -- It is recommended to use something unique as other scripts using Rayfield may overwrite your key file
      SaveKey = true, -- The user's key will be saved, but if you change the key, they will be unable to use your script
      GrabKeyFromSite = false, -- If this is true, set Key below to the RAW site you would like Rayfield to get the key from
      Key = {"Hello"} -- List of keys that will be accepted by the system, can be RAW file links (pastebin, github etc) or simple strings ("hello","key22")
   }
})

-- Criando a aba "Aimbot"
local AimbotTab = Window:NewTab("Aimbot")
local AimbotSection = AimbotTab:NewSection("Configurações do Aimbot")

-- Variáveis de Aimbot
local aimbotEnabled = false
local aimFOVEnabled = false
local aimFOV = 100
local smoothness = 0.5
local teamCheckEnabled = false
local wallCheckEnabled = false
local aimbotTarget = "Head" -- Pode ser "Head", "Torso", etc.

-- Toggle para Aimbot
AimbotSection:NewToggle("Aimbot Ativado", "Ativa ou desativa o aimbot", function(val)
    aimbotEnabled = val
    print("Aimbot: " .. tostring(aimbotEnabled))
end)

-- Toggle para AimFOV
AimbotSection:NewToggle("Ativar AimFOV", "Ativa ou desativa o FOV do aimbot", function(val)
    aimFOVEnabled = val
    print("AimFOV Ativado: " .. tostring(aimFOVEnabled))
end)

-- Slider para AimFOV
AimbotSection:NewSlider("Tamanho do AimFOV", "Ajusta o raio do FOV do aimbot", 500, 2000, aimFOV, function(val)
    aimFOV = val
    print("AimFOV: " .. aimFOV)
end)

-- Slider para Suavidade (Smoothness)
AimbotSection:NewSlider("Suavidade do Aimbot", "Ajusta a suavidade do aimbot", 0, 1, smoothness, function(val)
    smoothness = val
    print("Suavidade: " .. smoothness)
end)

-- Toggle para Team Check
AimbotSection:NewToggle("Ativar Team Check", "Desativa a mira nos membros da sua equipe", function(val)
    teamCheckEnabled = val
    print("Team Check Ativado: " .. tostring(teamCheckEnabled))
end)

-- Toggle para Wall Check
AimbotSection:NewToggle("Ativar Wall Check", "Desativa a mira através das paredes", function(val)
    wallCheckEnabled = val
    print("Wall Check Ativado: " .. tostring(wallCheckEnabled))
end)

-- Seletor de onde o aimbot vai grudar
AimbotSection:NewDropdown("Selecionar Ponto de Mira", "Onde o aimbot vai grudar", {"Head", "Torso", "HumanoidRootPart"}, function(val)
    aimbotTarget = val
    print("Aimbot irá grudar no: " .. aimbotTarget)
end)

-- Função de Aimbot (simples)
local function aimbot()
    if aimbotEnabled then
        local closestTarget = nil
        local shortestDistance = aimFOV
        local camera = game.Workspace.CurrentCamera
        local localPlayer = game.Players.LocalPlayer
        local mouse = localPlayer:GetMouse()

        -- Iterar sobre os jogadores e encontrar o alvo mais próximo
        for _, player in ipairs(game.Players:GetPlayers()) do
            if player ~= localPlayer and player.Character and player.Character:FindFirstChild("HumanoidRootPart") then
                local target = player.Character[aimbotTarget]
                if target then
                    local screenPos = camera:WorldToViewportPoint(target.Position)
                    local distance = (Vector2.new(mouse.X, mouse.Y) - Vector2.new(screenPos.X, screenPos.Y)).Magnitude
                    if distance < shortestDistance then
                        closestTarget = player
                        shortestDistance = distance
                    end
                end
            end
        end

        -- Realizar a mira no alvo mais próximo
        if closestTarget then
            local target = closestTarget.Character[aimbotTarget]
            local camera = game.Workspace.CurrentCamera
            local humanoid = closestTarget.Character:FindFirstChildOfClass("Humanoid")
            if humanoid and humanoid.Health > 0 then
                camera.CFrame = CFrame.new(camera.CFrame.Position, target.Position)
            end
        end
    end
end

-- Loop principal para aimbot
game:GetService("RunService").Heartbeat:Connect(function()
    if aimbotEnabled then
        aimbot()
    end
end)

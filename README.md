local Rayfield = loadstring(game:HttpGet("https://sirius.menu/gen2"))()

local window = Rayfield:CreateWindow({
    name = "Yuyu Hub",
    subtitle = "Slap Royale Exploit",
})

local Maintab = window:CreateTab({ name = "Main", icon = 93364949241311 })

local toggle = Maintab:CreateToggle({ name = "Damage all on bus" })

local hasBeenUsed = false

toggle:OnChanged(function(Value)
    -- Interrompe a execução caso o toggle seja desligado manualmente
    if not Value then return end

    -- Se já foi executado nesta partida, impede a reativação, desativa o toggle e exibe a notificação
    if hasBeenUsed then
        toggle:Set(false)
        window:Notify({
            title = "Try again in another match.",
            content = "This toggle has already been activated and can no longer be used.",
            duration = 7,
        })
        return
    end

    local Players = game:GetService("Players")
    local ReplicatedStorage = game:GetService("ReplicatedStorage")
    local Workspace = game:GetService("Workspace")
    local VirtualInputManager = game:GetService("VirtualInputManager")
    local StarterGui = game:GetService("StarterGui")
    local Player = Players.LocalPlayer

    local function GetItem(n)
        local f1 = Workspace:FindFirstChild("Item")
        local f2 = Workspace:FindFirstChild("Items")
        if f1 and f1:FindFirstChild(n) then return f1[n] end
        if f2 and f2:FindFirstChild(n) then return f2[n] end
        return Workspace:FindFirstChild(n, true)
    end

    local function PressF()
        VirtualInputManager:SendKeyEvent(true, Enum.KeyCode.F, false, game)
        task.wait(0.05)
        VirtualInputManager:SendKeyEvent(false, Enum.KeyCode.F, false, game)
    end

    local function Click()
        local vp = Workspace.CurrentCamera.ViewportSize
        VirtualInputManager:SendMouseButtonEvent(vp.X/2, vp.Y/2, 0, true, game, 0)
        task.wait(0.05)
        VirtualInputManager:SendMouseButtonEvent(vp.X/2, vp.Y/2, 0, false, game, 0)
    end

    local function Equip(n)
        local c = Player.Character
        if c then
            local t = Player.Backpack:FindFirstChild(n) or c:FindFirstChild(n)
            if t then c:FindFirstChildOfClass("Humanoid"):EquipTool(t) end
        end
    end

    local tp = GetItem("True Power")
    if not tp then
        -- Desativa o toggle e exibe a notificação personalizada se não houver o item
        toggle:Set(false)
        window:Notify({
            title = "Stopped",
            content = "This match doesn't have enough rare items to deal all damage.",
            duration = 7,
        })
        return
    end

    -- Marca a habilidade como já executada nesta partida
    hasBeenUsed = true

    local r = Player.Character:FindFirstChild("HumanoidRootPart")
    r.CFrame = tp:GetPivot()
    task.wait(0.25)
    PressF()
    task.wait(0.25)

    local ff = GetItem("Forcefield Crystal")
    if ff then
        r.CFrame = ff:GetPivot()
        task.wait(0.25)
        PressF()
        task.wait(0.25)
    end

    local b1 = GetItem("Bomb")
    if b1 then
        r.CFrame = b1:GetPivot()
        task.wait(0.25)
        PressF()
        task.wait(0.25)
    end

    local b2 = GetItem("Bomb")
    if b2 then
        r.CFrame = b2:GetPivot()
        task.wait(0.25)
        PressF()
        task.wait(0.25)
    end

    -- Busca e coleta da 3ª bomba conforme solicitado
    local b3 = GetItem("Bomb")
    if b3 then
        r.CFrame = b3:GetPivot()
        task.wait(0.25)
        PressF()
        task.wait(0.25)
    end

    local bus = Workspace:WaitForChild("BusModel", 60)
    if bus then
        task.wait(2)
        Equip("Forcefield Crystal")
        task.wait(0.25)
        Click()
        task.wait(0.25)
        Equip("Bomb")
        task.wait(0.25)
        Click()
        task.wait(0.25)
        Equip("Bomb")
        task.wait(0.25)
        Click()
        task.wait(0.25)
        -- Utilização da 3ª bomba no ônibus
        Equip("Bomb")
        task.wait(0.25)
        Click()
        task.wait(0.25)
        
        local je = ReplicatedStorage.Remotes:FindFirstChild("BusJumping")
        if je then je:FireServer() end
        
        -- Ajuste do fluxo pós-pulo conforme os novos tempos solicitados
        task.wait(5)
        Equip("True Power")
        task.wait(0.25)
        Click()
        
        task.wait(1.75)
        Equip("Pow")
        
        task.wait(0.15)
        
        local t = nil
        local dist = 999
        for _, p in pairs(Players:GetPlayers()) do
            if p ~= Player and p.Character and p.Character:FindFirstChild("HumanoidRootPart") then
                local d = (p.Character.HumanoidRootPart.Position - bus:GetPivot().Position).Magnitude
                if d < dist then dist = d; t = p end
            end
        end
        
        if t and t.Character:FindFirstChild("HumanoidRootPart") then
            r.CFrame = t.Character.HumanoidRootPart.CFrame
            -- Trava o HumanoidRootPart antes de disparar a ação
            r.Anchored = true
            task.wait(0.30)
            
            local se = ReplicatedStorage.Remotes:FindFirstChild("Slap")
            if se then
                -- Dispara o RemoteEvent Slap apenas uma única vez no jogador escolhido
                se:FireServer(t.Character.HumanoidRootPart)
            end
            -- Destrava o HumanoidRootPart ao finalizar o disparo
            r.Anchored = false
        end
    end

    -- Desativa a chave na interface ao finalizar a sequência por completo
    toggle:Set(false)
end,
})

local Label = MainTab:CreateLabel("Make sure you are using the Pow Glove and have low ping.", "rewind")

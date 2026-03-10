--[[
    CYBERNODRY - ULTIMATE MONEY INJECTOR
    - Alvos: DecrementMoney, Transfer, Economy, Money, Coins
    - Sistema: Inversão de Valor por Nickname
    - By: CyberNoDry
]]

local Player = game:GetService("Players").LocalPlayer
local ReplicatedStorage = game:GetService("ReplicatedStorage")
local CoreGui = game:GetService("CoreGui")

-- Função para encontrar qualquer remote de dinheiro no jogo
local function GetMoneyRemote()
    local names = {"DecrementMoney", "TransferMoney", "AddCoins", "UpdateBalance", "EconomyEvent"}
    for _, name in pairs(names) do
        local found = ReplicatedStorage:FindFirstChild(name, true)
        if found and found:IsA("RemoteEvent") then return found end
    end
    -- Fallback para o que vimos na sua print
    return ReplicatedStorage:WaitForChild("Remotes"):WaitForChild("Events"):WaitForChild("Economy"):WaitForChild("DecrementMoney")
end

local MoneyRemote = GetMoneyRemote()

-- Interface Profissional
local function Create(cls, props)
    local inst = Instance.new(cls)
    for i, v in pairs(props) do inst[i] = v end
    return inst
end

if CoreGui:FindFirstChild("CyberCash_V2") then CoreGui.CyberCash_V2:Destroy() end

local ScreenGui = Create("ScreenGui", { Name = "CyberCash_V2", Parent = CoreGui })
local Main = Create("Frame", {
    Parent = ScreenGui, BackgroundColor3 = Color3.fromRGB(12, 12, 12),
    Position = UDim2.new(0.5, -110, 0.25, 0), Size = UDim2.new(0, 220, 0, 220),
    Active = true, Draggable = true
})
Create("UICorner", { Parent = Main })
Create("UIStroke", { Parent = Main, Thickness = 2, Color = Color3.fromRGB(255, 215, 0) })

local Title = Create("TextLabel", { Parent = Main, Text = "CYBER CASH INJECTOR", Size = UDim2.new(1, 0, 0, 35), BackgroundTransparency = 1, TextColor3 = Color3.fromRGB(255, 215, 0), Font = Enum.Font.GothamBold, TextSize = 13 })

-- Campo do NICKNAME
local NickInput = Create("TextBox", {
    Parent = Main, PlaceholderText = "Seu Nickname...",
    Text = Player.Name, Position = UDim2.new(0.1, 0, 0.2, 0), Size = UDim2.new(0.8, 0, 0, 35),
    BackgroundColor3 = Color3.fromRGB(25, 25, 25), TextColor3 = Color3.new(1,1,1),
    Font = Enum.Font.Gotham, TextSize = 11
})
Create("UICorner", { Parent = NickInput })

-- Campo do VALOR
local AmountInput = Create("TextBox", {
    Parent = Main, PlaceholderText = "Valor do Dinheiro...",
    Text = "", Position = UDim2.new(0.1, 0, 0.42, 0), Size = UDim2.new(0.8, 0, 0, 35),
    BackgroundColor3 = Color3.fromRGB(25, 25, 25), TextColor3 = Color3.new(1,1,1),
    Font = Enum.Font.Gotham, TextSize = 11
})
Create("UICorner", { Parent = AmountInput })

local SetBtn = Create("TextButton", {
    Parent = Main, Text = "INJETAR NO NICK ⚡",
    Position = UDim2.new(0.1, 0, 0.7, 0), Size = UDim2.new(0.8, 0, 0, 40),
    BackgroundColor3 = Color3.fromRGB(0, 100, 0), TextColor3 = Color3.new(1,1,1),
    Font = Enum.Font.GothamBold, TextSize = 12
})
Create("UICorner", { Parent = SetBtn })

-- Lógica de Injeção
SetBtn.MouseButton1Click:Connect(function()
    local targetNick = NickInput.Text
    local val = tonumber(AmountInput.Text)
    
    if val and targetNick ~= "" then
        SetBtn.Text = "ENVIANDO..."
        
        -- Tenta disparar o remote de economia com o valor negativo (Bypass)
        -- Alguns sistemas de transferência aceitam (Nick, Valor)
        pcall(function()
            MoneyRemote:FireServer(-val, targetNick)
            -- Caso o remote aceite apenas o valor, ele altera o líder de quem disparou
            MoneyRemote:FireServer(-val) 
        end)
        
        task.wait(1)
        SetBtn.Text = "INJETADO! ✅"
        task.wait(1)
        SetBtn.Text = "INJETAR NO NICK ⚡"
    else
        SetBtn.Text = "ERRO NOS DADOS! ❌"
        task.wait(1)
        SetBtn.Text = "INJETAR NO NICK ⚡"
    end
end)

local Close = Create("TextButton", { Parent = Main, Text = "X", Position = UDim2.new(1, -25, 0, 5), Size = UDim2.new(0, 20, 0, 20), BackgroundTransparency = 1, TextColor3 = Color3.new(1,0,0), Font = Enum.Font.GothamBold })
Close.MouseButton1Click:Connect(function() ScreenGui:Destroy() end)

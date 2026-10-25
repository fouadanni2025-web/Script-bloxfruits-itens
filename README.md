-- ============================================
-- DRAXZS BLOX FRUITS - ITEM HUNTER (V6)
-- by DRAXZS | Sea Check por PlaceId + Guias
-- ============================================

local Rayfield = loadstring(game:HttpGet('https://sirius.menu/rayfield'))()

local Window = Rayfield:CreateWindow({
   Name = "DRAXZS | ITEM HUNTER",
   LoadingTitle = "Carregando Caçador...",
   LoadingSubtitle = "by DRAXZS | Guia Completo",
   ConfigurationSaving = { Enabled = true, FolderName = "DRAXZS_Hunter", FileName = "Config" },
   KeySystem = false
})

local player = game.Players.LocalPlayer
local UIS = game:GetService("UserInputService")
local RunService = game:GetService("RunService")
local camera = workspace.CurrentCamera

-- ============================================
-- DETECÇÃO DE SEA POR PLACE ID
-- ============================================
-- IDs oficiais do Blox Fruits no Roblox
local Sea1_ID = 2753915549
local Sea2_ID = 4442272183
local Sea3_ID = 7449423635

local function getCurrentSea()
    local placeId = game.PlaceId
    if placeId == Sea1_ID then return "Sea 1"
    elseif placeId == Sea2_ID then return "Sea 2"
    elseif placeId == Sea3_ID then return "Sea 3"
    else return "Desconhecido" end
end

-- ============================================
-- ABA WELCOME
-- ============================================
local WelcomeTab = Window:CreateTab("Welcome", 4483362458)
WelcomeTab:CreateSection("Bem-vindo ao DRAXZS Item Hunter")
WelcomeTab:CreateParagraph({ Title = "O que é este menu?", Content = "Guia interativo para achar qualquer item no Blox Fruits. Mostra onde ir, preço, chance de drop e como fazer puzzle." })
WelcomeTab:CreateParagraph({ Title = "Como Usar", Content = "1. Ligue o Fly na aba Config.\n2. Escolha o item.\n3. Se você estiver no Sea correto, uma Box vermelha aparece no céu.\n4. Voe até a Box e siga as instruções." })
WelcomeTab:CreateParagraph({ Title = "Dica", Content = "O menu detecta automaticamente em qual Sea você está. Se tentar clicar em um item de outro Sea, ele vai te passar a ilha errada." })

-- ============================================
-- SISTEMA DE VOO MANUAL
-- ============================================
local flyConfig = { enabled = false, speed = 120 }

RunService.Stepped:Connect(function()
    if not flyConfig.enabled then return end
    local char = player.Character
    if char then
        for _, part in ipairs(char:GetDescendants()) do
            if part:IsA("BasePart") then part.CanCollide = false end
        end
    end
end)

RunService.RenderStepped:Connect(function(dt)
    if not flyConfig.enabled then return end
    local char = player.Character
    if not char then return end
    local hrp = char:FindFirstChild("HumanoidRootPart")
    if not hrp then return end

    hrp.Velocity = Vector3.new(0, 0, 0)
    local dir = Vector3.new(0, 0, 0)
    if UIS:IsKeyDown(Enum.KeyCode.W) then dir = dir + camera.CFrame.LookVector end
    if UIS:IsKeyDown(Enum.KeyCode.S) then dir = dir - camera.CFrame.LookVector end
    if UIS:IsKeyDown(Enum.KeyCode.A) then dir = dir - camera.CFrame.RightVector end
    if UIS:IsKeyDown(Enum.KeyCode.D) then dir = dir + camera.CFrame.RightVector end
    if UIS:IsKeyDown(Enum.KeyCode.Space) then dir = dir + Vector3.new(0, 1, 0) end
    if UIS:IsKeyDown(Enum.KeyCode.LeftShift) then dir = dir - Vector3.new(0, 1, 0) end
    if dir.Magnitude > 0 then hrp.CFrame = hrp.CFrame + dir.Unit * flyConfig.speed * dt end
end)

local function toggleFly(state)
    flyConfig.enabled = state
    if not state then
        local char = player.Character
        if char then
            for _, part in ipairs(char:GetDescendants()) do
                if part:IsA("BasePart") then part.CanCollide = true end
            end
        end
    end
end

-- ============================================
-- SISTEMA DE WAYPOINT
-- ============================================
local waypointBox, waypointLabel = nil, nil

local function createWaypoint(x, y, z, seaText, islandName, itemSea)
    -- VERIFICAÇÃO DO SEA
    local currentSea = getCurrentSea()
    if currentSea ~= "Desconhecido" and currentSea ~= itemSea and itemSea ~= "Gamepass" then
        Rayfield:Notify({ Title = "Sea Errado!", Content = "Você está no " .. currentSea .. " e esse item é do " .. itemSea .. ". Vá para o Sea correto!", Duration = 6 })
        return
    end

    if waypointBox then waypointBox:Destroy() end
    if waypointLabel then waypointLabel:Destroy() end

    waypointBox = Instance.new("Part")
    waypointBox.Size = Vector3.new(20, 20, 20)
    waypointBox.Position = Vector3.new(x, y + 150, z)
    waypointBox.Anchored = true
    waypointBox.CanCollide = false
    waypointBox.Transparency = 0.5
    waypointBox.Color = Color3.fromRGB(255, 0, 0)
    waypointBox.Material = Enum.Material.Neon
    waypointBox.Parent = workspace

    local billboard = Instance.new("BillboardGui")
    billboard.Size = UDim2.new(0, 250, 0, 50)
    billboard.StudsOffset = Vector3.new(0, 15, 0)
    billboard.AlwaysOnTop = true
    billboard.Parent = waypointBox

    waypointLabel = Instance.new("TextLabel")
    waypointLabel.Size = UDim2.new(1, 0, 1, 0)
    waypointLabel.BackgroundTransparency = 1
    waypointLabel.Text = seaText .. "\n" .. islandName
    waypointLabel.TextColor3 = Color3.fromRGB(255, 255, 255)
    waypointLabel.TextStrokeTransparency = 0
    waypointLabel.Font = Enum.Font.GothamBold
    waypointLabel.TextSize = 22
    waypointLabel.Parent = billboard
    
    Rayfield:Notify({ Title = "Waypoint Criado", Content = "Voe até a Box vermelha no céu!", Duration = 5 })
end

local function removeWaypoint()
    if waypointBox then waypointBox:Destroy(); waypointBox = nil end
    if waypointLabel then waypointLabel:Destroy(); waypointLabel = nil end
end

-- ============================================
-- DADOS DAS ESPADAS
-- ============================================
local swords = {
    ["Katana"] = { x = 1093, y = 16, z = 1310, sea = "Sea 1", island = "Starter Island", desc = "Preço: $1,000\nOnde ir: Sword Dealer na Starter Island.\nO que fazer: Pague $1,000." },
    ["Cutlass"] = { x = 1093, y = 16, z = 1310, sea = "Sea 1", island = "Starter Island", desc = "Preço: $1,000\nOnde ir: Sword Dealer na Starter Island.\nO que fazer: Pague $1,000." },
    ["Dual Katana"] = { x = -1152, y = 5, z = 3826, sea = "Sea 1", island = "Pirate Village", desc = "Preço: $12,000\nOnde ir: Sword Dealer of the West na Pirate Village.\nO que fazer: Pague $12,000." },
    ["Iron Mace"] = { x = -1152, y = 5, z = 3826, sea = "Sea 1", island = "Pirate Village", desc = "Preço: $25,000\nOnde ir: Sword Dealer na Pirate Village.\nO que fazer: Pague $25,000." },
    ["Saber"] = { x = -1613, y = 37, z = 152, sea = "Sea 1", island = "Jungle", desc = "Drop: Garantido (Puzzle)\nOnde ir: Ruínas na Jungle.\nO que fazer: Pise nas 5 pedras, mate o Saber Expert." },
    ["Rengoku"] = { x = 6170, y = 290, z = -6734, sea = "Sea 2", island = "Ice Castle", desc = "Drop: 5% (Hidden Key)\nOnde ir: Ice Castle (Sea 2).\nO que fazer: Pegue Hidden Key (Awakened Ice Admiral 5%). Use na porta secreta." },
    ["Bisento"] = { x = 5259, y = 40, z = 4711, sea = "Sea 1", island = "Fountain City", desc = "Preço: $1,000,000\nOnde ir: Master Sword Dealer.\nO que fazer: Pague $1,000,000." },
    ["Soul Cane"] = { x = 5259, y = 40, z = 4711, sea = "Sea 1", island = "Fountain City", desc = "Preço: $750,000\nOnde ir: NPC Living Skeleton.\nO que fazer: Pague $750,000." },
    ["Trident"] = { x = 3856, y = -2, z = 1174, sea = "Sea 1", island = "Underwater City", desc = "Drop: Garantido\nOnde ir: Underwater City.\nO que fazer: Derrote Fishman Lord." },
    ["Gravity Blade"] = { x = -2448, y = 8, z = -3208, sea = "Sea 2", island = "Green Zone", desc = "Drop: Baixo (Fajita)\nOnde ir: Green Zone.\nO que fazer: Derrote boss Fajita." },
    ["Dark Blade"] = { x = 0, y = 0, z = 0, sea = "Gamepass", island = "Loja de Robux", desc = "Preço: 1,200 Robux\nOnde ir: Loja de Gamepass.\nO que fazer: Compre o Gamepass." },
    ["Dragon Trident"] = { x = -2448, y = 8, z = -3208, sea = "Sea 2", island = "Green Zone", desc = "Drop: Garantido\nOnde ir: Green Zone.\nO que fazer: Derrote boss Tide Keeper." },
    ["Buddy Sword"] = { x = -9499, y = 150, z = 5765, sea = "Sea 3", island = "Haunted Castle", desc = "Drop: Garantido (Cake Queen)\nOnde ir: Ice Cream Land.\nO que fazer: Derrote Cake Queen." },
    ["Twin Hooks"] = { x = -12681, y = 380, z = -7504, sea = "Sea 3", island = "Floating Turtle", desc = "Drop: Garantido\nOnde ir: Floating Turtle.\nO que fazer: Derrote Captain Elephant." },
    ["Wando"] = { x = -6224, y = 16, z = -4902, sea = "Sea 2", island = "Hot and Cold", desc = "Quest: Citizen\nOnde ir: Hot and Cold.\nO que fazer: Complete a quest do Citizen." },
    ["Shisui"] = { x = -6224, y = 16, z = -4902, sea = "Sea 2", island = "Hot and Cold", desc = "Preço: $2,000,000\nOnde ir: Legendary Sword Dealer.\nO que fazer: Pague $2,000,000." },
    ["Sadis"] = { x = -6224, y = 16, z = -4902, sea = "Sea 2", island = "Hot and Cold", desc = "Preço: $2,000,000\nOnde ir: Legendary Sword Dealer.\nO que fazer: Pague $2,000,000." },
    ["Koko"] = { x = 6170, y = 290, z = -6734, sea = "Sea 2", island = "Ice Castle", desc = "Drop: Garantido\nOnde ir: Ice Castle.\nO que fazer: Derrote boss Order." },
    ["Midnight Blade"] = { x = -5312, y = 12, z = 8531, sea = "Sea 1", island = "Magma Village", desc = "Drop: Garantido\nOnde ir: Magma Village.\nO que fazer: Derrote boss El Admin." },
    ["Pole (1st Form)"] = { x = -3053, y = 240, z = -10112, sea = "Sea 2", island = "Forgotten Island", desc = "Drop: 5% (Thunder God)\nOnde ir: Forgotten Island.\nO que fazer: Junte 20 Mystery Fragments, invoque e derrote Thunder God." },
    ["Pipe"] = { x = 3856, y = -2, z = 1174, sea = "Sea 1", island = "Underwater City", desc = "Preço: $100,000\nOnde ir: Sword Dealer of the East.\nO que fazer: Pague $100,000." },
    ["Tushita"] = { x = -12681, y = 380, z = -7504, sea = "Sea 3", island = "Floating Turtle", desc = "Drop: Garantido (Puzzle)\nOnde ir: Floating Turtle.\nO que fazer: Acenda as 5 tochas em 5 min e derrote Longma." },
    ["Yama"] = { x = -4459, y = 200, z = -5726, sea = "Sea 3", island = "Hydra Island", desc = "Drop: Garantido (Puzzle)\nOnde ir: Hydra Island.\nO que fazer: Complete 30 quests, quebre a parede da cachoeira e pegue." },
    ["Hallow Scythe"] = { x = -9499, y = 150, z = 5765, sea = "Sea 3", island = "Haunted Castle", desc = "Drop: 5% (Soul Reaper)\nOnde ir: Haunted Castle.\nO que fazer: Junte Hallow Essence, use no altar e derrote Soul Reaper." },
    ["Dark Dagger"] = { x = -5097, y = 295, z = -3177, sea = "Sea 3", island = "Castle on the Sea", desc = "Drop: 100% (rip_indra)\nOnde ir: Castle on the Sea.\nO que fazer: Invoque e derrote rip_indra True Form." },
    ["Spikey Trident"] = { x = -9499, y = 150, z = 5765, sea = "Sea 3", island = "Haunted Castle", desc = "Drop: 5% (Cake Prince) / 15% (Dough King)\nOnde ir: Cake Land.\nO que fazer: Derrote Cake Prince ou Dough King." },
    ["Serpent Bow"] = { x = -9499, y = 150, z = 5765, sea = "Sea 3", island = "Haunted Castle", desc = "Drop: 1% (Serpent)\nOnde ir: Haunted Castle.\nO que fazer: Derrote boss Serpent." },
    ["Cursed Dual Katana"] = { x = -12681, y = 380, z = -7504, sea = "Sea 3", island = "Floating Turtle", desc = "Drop: Garantido (Puzzle)\nOnde ir: Floating Turtle.\nO que fazer: Tenha Yama e Tushita (350 Maestria). Fale com o Crypt Master." },
}

-- ============================================
-- DADOS DOS ACESSÓRIOS
-- ============================================
local accessories = {
    ["Grey Top Hat"] = { x = 1093, y = 16, z = 1310, sea = "Sea 1", island = "Starter Island", desc = "Preço: $100\nOnde ir: Starter Island.\nO que fazer: Pague $100." },
    ["Tiger Fur"] = { x = -1613, y = 37, z = 152, sea = "Sea 1", island = "Jungle", desc = "Preço: $1,000\nOnde ir: Jungle.\nO que fazer: Pague $1,000." },
    ["Gold Chain"] = { x = -4851, y = 25, z = 4332, sea = "Sea 1", island = "Marine Fortress", desc = "Preço: $5,000\nOnde ir: Marine Fortress.\nO que fazer: Pague $5,000." },
    ["Cool Shades"] = { x = 5259, y = 40, z = 4711, sea = "Sea 1", island = "Fountain City", desc = "Preço: $5,000\nOnde ir: Kingdom.\nO que fazer: Pague $5,000." },
    ["Bandana"] = { x = 5259, y = 40, z = 4711, sea = "Sea 1", island = "Fountain City", desc = "Preço: $5,000\nOnde ir: Kingdom.\nO que fazer: Pague $5,000." },
    ["Straw Hat"] = { x = 5259, y = 40, z = 4711, sea = "Sea 1", island = "Fountain City", desc = "Preço: $15,000\nOnde ir: Kingdom.\nO que fazer: Pague $15,000." },
    ["Brim Hat"] = { x = 5259, y = 40, z = 4711, sea = "Sea 1", island = "Fountain City", desc = "Preço: $20,000\nOnde ir: Kingdom.\nO que fazer: Pague $20,000." },
    ["Swordsman Hat"] = { x = 5259, y = 40, z = 4711, sea = "Sea 1", island = "Fountain City", desc = "Preço: $20,000\nOnde ir: Kingdom.\nO que fazer: Pague $20,000." },
    ["Dark Coat"] = { x = 5259, y = 40, z = 4711, sea = "Sea 1", island = "Fountain City", desc = "Preço: $20,000\nOnde ir: Kingdom.\nO que fazer: Pague $20,000." },
    ["Winged Hat"] = { x = -4851, y = 800, z = -2561, sea = "Sea 1", island = "Skylands", desc = "Preço: $50,000\nOnde ir: Skypiea.\nO que fazer: Pague $50,000." },
    ["Black Cape"] = { x = -4851, y = 800, z = -2561, sea = "Sea 1", island = "Skylands", desc = "Preço: $50,000\nOnde ir: Skypiea.\nO que fazer: Pague $50,000." },
    ["Swan Glasses"] = { x = -2247, y = 73, z = -1671, sea = "Sea 2", island = "Kingdom of Rose", desc = "Drop: Garantido (Don Swan)\nOnde ir: Mansion.\nO que fazer: Dê fruta de 1M pro Trevor e derrote Don Swan." },
    ["Tomoe Ring"] = { x = -2247, y = 73, z = -1671, sea = "Sea 2", island = "Kingdom of Rose", desc = "Drop: Garantido (Swan Pirate)\nOnde ir: Kingdom of Rose.\nO que fazer: Derrote Swan Pirate." },
    ["Cool Shades S2"] = { x = -6224, y = 16, z = -4902, sea = "Sea 2", island = "Hot and Cold", desc = "Drop: Garantido (Yeti)\nOnde ir: Hot and Cold.\nO que fazer: Derrote Yeti." },
    ["Choppa"] = { x = -5312, y = 12, z = 8531, sea = "Sea 1", island = "Magma Village", desc = "Drop: Garantido (Magma Admiral)\nOnde ir: Magma Village.\nO que fazer: Derrote Magma Admiral." },
    ["Warrior Helmet"] = { x = -2247, y = 73, z = -1671, sea = "Sea 2", island = "Kingdom of Rose", desc = "Quest: Colosseum\nOnde ir: Colosseum.\nO que fazer: Complete a quest." },
    ["Water Kung Fu Gi"] = { x = 3856, y = -2, z = 1174, sea = "Sea 1", island = "Underwater City", desc = "Preço: $750,000\nOnde ir: Underwater City.\nO que fazer: Pague $750,000." },
    ["Superhuman Gi"] = { x = -5434, y = 12, z = -793, sea = "Sea 2", island = "Graveyard", desc = "Preço: $3,000,000\nOnde ir: Graveyard.\nO que fazer: Pague $3,000,000." },
    ["Black Fedora"] = { x = -2247, y = 73, z = -1671, sea = "Sea 2", island = "Kingdom of Rose", desc = "Preço: $20,000\nOnde ir: Kingdom.\nO que fazer: Pague $20,000." },
    ["Sunglasses"] = { x = -2247, y = 73, z = -1671, sea = "Sea 2", island = "Kingdom of Rose", desc = "Preço: $5,000\nOnde ir: Kingdom.\nO que fazer: Pague $5,000." },
    ["Horns"] = { x = -2247, y = 73, z = -1671, sea = "Sea 2", island = "Kingdom of Rose", desc = "Preço: $5,000\nOnde ir: Kingdom.\nO que fazer: Pague $5,000." },
    ["Pink Coat"] = { x = -2247, y = 73, z = -1671, sea = "Sea 2", island = "Kingdom of Rose", desc = "Preço: $20,000\nOnde ir: Kingdom.\nO que fazer: Pague $20,000." },
    ["Ghoul Mask"] = { x = -9499, y = 150, z = 5765, sea = "Sea 3", island = "Haunted Castle", desc = "Preço: $5,000,000 + Essence\nOnde ir: Haunted Castle.\nO que fazer: Pague $5M e entregue a Essence pro Fear." },
    ["Pale Scarf"] = { x = -9499, y = 150, z = 5765, sea = "Sea 3", island = "Haunted Castle", desc = "Drop: Garantido (Cake Prince)\nOnde ir: Cake Land.\nO que fazer: Derrote Cake Prince." },
    ["Cursed Captain's Hat"] = { x = -9499, y = 150, z = 5765, sea = "Sea 3", island = "Haunted Castle", desc = "Quest: Cursed Captain\nOnde ir: Haunted Castle.\nO que fazer: Complete a quest." },
    ["Dino Hood"] = { x = -12149, y = 6, z = -8480, sea = "Sea 3", island = "Tiki Outpost", desc = "Craft: 25 Bones + 10 Tusk\nOnde ir: Prehistoric Island.\nO que fazer: Entregue os itens pro Fossil Expert." },
    ["Hunter Cape"] = { x = -12149, y = 6, z = -8480, sea = "Sea 3", island = "Tiki Outpost", desc = "Drop: Garantido (Terrorshark)\nOnde ir: Tiki Outpost.\nO que fazer: Use Monster Magnet e derrote Terrorshark." },
    ["Holy Crown"] = { x = -9499, y = 150, z = 5765, sea = "Sea 3", island = "Haunted Castle", desc = "Drop: Garantido (Soul Reaper)\nOnde ir: Haunted Castle.\nO que fazer: Invoque e derrote Soul Reaper." },
}

-- ============================================
-- DADOS DAS ARMAS
-- ============================================
local weapons = {
    ["Slingshot"] = { x = 1093, y = 16, z = 1310, sea = "Sea 1", island = "Starter Island", desc = "Preço: $3,000\nOnde ir: Starter Island.\nO que fazer: Pague $3,000." },
    ["Flintlock"] = { x = -1152, y = 5, z = 3826, sea = "Sea 1", island = "Pirate Village", desc = "Preço: $10,000\nOnde ir: Pirate Village.\nO que fazer: Pague $10,000." },
    ["Musket"] = { x = -4851, y = 25, z = 4332, sea = "Sea 1", island = "Marine Fortress", desc = "Preço: $20,000\nOnde ir: Marine Fortress.\nO que fazer: Pague $20,000." },
    ["Cannon"] = { x = -4851, y = 25, z = 4332, sea = "Sea 1", island = "Marine Fortress", desc = "Preço: $50,000\nOnde ir: Marine Fortress.\nO que fazer: Pague $50,000." },
    ["Dual Flintlock"] = { x = -4851, y = 25, z = 4332, sea = "Sea 1", island = "Marine Fortress", desc = "Preço: $65,000\nOnde ir: Marine Fortress.\nO que fazer: Pague $65,000." },
    ["Flintlock S2"] = { x = -2247, y = 73, z = -1671, sea = "Sea 2", island = "Kingdom of Rose", desc = "Preço: $12,000\nOnde ir: Kingdom.\nO que fazer: Pague $12,000." },
    ["Bazooka"] = { x = -2448, y = 8, z = -3208, sea = "Sea 2", island = "Green Zone", desc = "Preço: $50,000\nOnde ir: Green Zone.\nO que fazer: Pague $50,000." },
    ["Superhuman"] = { x = -5434, y = 12, z = -793, sea = "Sea 2", island = "Graveyard", desc = "Preço: $3,000,000\nOnde ir: Graveyard.\nO que fazer: Pague $3,000,000." },
    ["Kabucha"] = { x = -2247, y = 73, z = -1671, sea = "Sea 2", island = "Kingdom of Rose", desc = "Drop: Garantido (Strongest God)\nOnde ir: Remote Island.\nO que fazer: Derrote Strongest God." },
    ["Shark Anchor"] = { x = -12149, y = 6, z = -8480, sea = "Sea 3", island = "Tiki Outpost", desc = "Drop: Garantido (Terrorshark)\nOnde ir: Tiki Outpost.\nO que fazer: Use Monster Magnet e derrote Terrorshark." },
    ["Skull Guitar"] = { x = -9499, y = 150, z = 5765, sea = "Sea 3", island = "Haunted Castle", desc = "Drop: Garantido (Puzzle)\nOnde ir: Haunted Castle.\nO que fazer: Complete o puzzle e use a Weird Machine." },
    ["Serpent Bow"] = { x = -9499, y = 150, z = 5765, sea = "Sea 3", island = "Haunted Castle", desc = "Drop: 1% (Serpent)\nOnde ir: Sea of Treats.\nO que fazer: Derrote Serpent." },
    ["Acidum Rifle"] = { x = -9499, y = 150, z = 5765, sea = "Sea 3", island = "Haunted Castle", desc = "Drop: Garantido (Drip)\nOnde ir: Sea of Treats.\nO que fazer: Derrote Drip." },
}

-- ============================================
-- CRIAÇÃO DAS ABAS
-- ============================================

local function createItemMenu(tab, items)
    for name, data in pairs(items) do
        tab:CreateButton({
            Name = "[" .. data.sea .. "] " .. name .. " - " .. data.island,
            Callback = function()
                createWaypoint(data.x, data.y, data.z, data.sea, data.island, data.sea)
                Rayfield:Notify({ Title = name, Content = data.desc, Duration = 12 })
            end,
        })
    end
end

local SwordTab = Window:CreateTab("Espadas", 4483362458)
SwordTab:CreateSection("Clique para criar o Waypoint no céu")
createItemMenu(SwordTab, swords)

local AccTab = Window:CreateTab("Acessórios", 4483362458)
AccTab:CreateSection("Clique para criar o Waypoint no céu")
createItemMenu(AccTab, accessories)

local GunTab = Window:CreateTab("Armas", 4483362458)
GunTab:CreateSection("Clique para criar o Waypoint no céu")
createItemMenu(GunTab, weapons)

-- ============================================
-- ABA CONFIG
-- ============================================
local ConfigTab = Window:CreateTab("Config", 4483362458)
ConfigTab:CreateSection("Controle de Voo")

ConfigTab:CreateToggle({
   Name = "Ativar Fly (Noclip)",
   CurrentValue = false,
   Callback = function(Value)
       toggleFly(Value)
       if Value then Rayfield:Notify({ Title = "Fly Ativado", Content = "Use W/A/S/D + Space/Shift.", Duration = 5 }) end
   end,
})

ConfigTab:CreateSlider({
   Name = "Velocidade do Voo",
   Range = {50, 300},
   Increment = 10,
   Suffix = "speed",
   CurrentValue = 120,
   Callback = function(Value) flyConfig.speed = Value end,
})

ConfigTab:CreateButton({
   Name = "Remover Waypoint do Céu",
   Callback = function() removeWaypoint(); Rayfield:Notify({ Title = "Waypoint", Content = "Box removida.", Duration = 2 }) end,
})

-- ============================================
-- INICIALIZAÇÃO
-- ============================================
Rayfield:LoadConfiguration()
Rayfield:Notify({ Title = "DRAXZS HUNTER", Content = "Menu carregado! Detecção de Sea ativada.", Duration = 5 })

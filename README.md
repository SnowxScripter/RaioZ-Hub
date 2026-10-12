
-- RAIOZ HUB | UI para ambiente Lua do Roblox
-- Interface visual; recursos do jogo não conectados.

local Players = game:GetService("Players")
local UIS = game:GetService("UserInputService")
local player = Players.LocalPlayer

local gui = Instance.new("ScreenGui")
gui.Name = "RaioZHub"
gui.ResetOnSpawn = false
gui.Parent = game:GetService("CoreGui")

local yellow = Color3.fromRGB(255, 211, 75)
local white = Color3.fromRGB(255, 255, 255)
local bg = Color3.fromRGB(247, 248, 250)
local dark = Color3.fromRGB(35, 39, 47)
local muted = Color3.fromRGB(125, 130, 140)

local function new(class, props, parent)
    local obj = Instance.new(class)
    for k, v in pairs(props) do obj[k] = v end
    obj.Parent = parent
    return obj
end

local function round(obj, n)
    new("UICorner", {
        CornerRadius = UDim.new(0, n or 10)
    }, obj)
end

local window = new("Frame", {
    Size = UDim2.fromOffset(650, 440),
    Position = UDim2.new(0.5, -325, 0.5, -220),
    BackgroundColor3 = bg,
    BorderSizePixel = 0,
    Active = true
}, gui)
round(window, 16)

new("UIStroke", {
    Color = Color3.fromRGB(230, 232, 236),
    Thickness = 1
}, window)

local header = new("Frame", {
    Size = UDim2.new(1, 0, 0, 58),
    BackgroundColor3 = white,
    BorderSizePixel = 0
}, window)
round(header, 16)

new("TextLabel", {
    Position = UDim2.fromOffset(18, 6),
    Size = UDim2.new(1, -75, 0, 30),
    BackgroundTransparency = 1,
    Text = "RaioZ Hub",
    Font = Enum.Font.GothamBold,
    TextSize = 23,
    TextXAlignment = Enum.TextXAlignment.Left,
    TextColor3 = dark
}, header)

new("TextLabel", {
    Position = UDim2.fromOffset(20, 34),
    Size = UDim2.new(1, -80, 0, 15),
    BackgroundTransparency = 1,
    Text = "EGG CONTROL CENTER",
    Font = Enum.Font.Gotham,
    TextSize = 9,
    TextXAlignment = Enum.TextXAlignment.Left,
    TextColor3 = muted
}, header)

local close = new("TextButton", {
    Position = UDim2.new(1, -44, 0, 12),
    Size = UDim2.fromOffset(32, 32),
    Text = "×",
    Font = Enum.Font.Gotham,
    TextSize = 23,
    TextColor3 = dark,
    BackgroundColor3 = bg,
    BorderSizePixel = 0
}, header)
round(close, 8)
close.Activated:Connect(function()
    window.Visible = false
end)

local sidebar = new("Frame", {
    Position = UDim2.fromOffset(12, 70),
    Size = UDim2.new(0, 150, 1, -82),
    BackgroundColor3 = white,
    BorderSizePixel = 0
}, window)
round(sidebar, 12)

local content = new("ScrollingFrame", {
    Position = UDim2.fromOffset(174, 70),
    Size = UDim2.new(1, -186, 1, -82),
    BackgroundTransparency = 1,
    BorderSizePixel = 0,
    ScrollBarThickness = 4,
    CanvasSize = UDim2.new()
}, window)

new("UIListLayout", {
    Padding = UDim.new(0, 9),
    SortOrder = Enum.SortOrder.LayoutOrder
}, content)

local function clear()
    for _, v in ipairs(content:GetChildren()) do
        if v:IsA("GuiObject")
            and not v:IsA("UIListLayout") then
            v:Destroy()
        end
    end
end

local function label(parent, text, y, height, size, color)
    return new("TextLabel", {
        Position = UDim2.fromOffset(13, y),
        Size = UDim2.new(1, -26, 0, height or 25),
        BackgroundTransparency = 1,
        Text = text,
        Font = Enum.Font.Gotham,
        TextSize = size or 13,
        TextColor3 = color or dark,
        TextXAlignment = Enum.TextXAlignment.Left,
        TextWrapped = true
    }, parent)
end

local function card(title, height)
    local f = new("Frame", {
        Size = UDim2.new(1, -4, 0, height),
        BackgroundColor3 = white,
        BorderSizePixel = 0
    }, content)
    round(f, 12)
    new("UIStroke", {
        Color = Color3.fromRGB(230, 232, 236),
        Thickness = 1
    }, f)
    label(f, title, 10, 24, 15, dark).Font =
        Enum.Font.GothamBold
    return f
end

local function button(parent, text, y, callback)
    local b = new("TextButton", {
        Position = UDim2.fromOffset(12, y),
        Size = UDim2.new(1, -24, 0, 34),
        BackgroundColor3 = bg,
        BorderSizePixel = 0,
        Text = text,
        Font = Enum.Font.GothamSemibold,
        TextSize = 12,
        TextColor3 = dark
    }, parent)
    round(b, 8)
    b.Activated:Connect(callback)
    return b
end

local function cycle(parent, title, options, y, callback)
    local index = 1
    local b = button(parent, title .. ": " .. options[1], y, function()
        index = index % #options + 1
        b.Text = title .. ": " .. options[index]
        callback(options[index])
    end)
    return b
end

local areas = {
    "Forest", "Lake", "Desert", "Jungle", "Snow",
    "Volcano", "Abyss Ocean", "Prehistoric", "Cosmic",
    "Cherry Blossom", "Titan Temple",
    "Angels & Demons", "Enchanted Forest"
}

local tabs = {"Ovos", "Pets", "Movimento",
    "Proteção", "Configurações"}
local tabButtons = {}

local function render(tab)
    clear()

    for name, b in pairs(tabButtons) do
        b.BackgroundColor3 = name == tab and yellow or white
    end

    if tab == "Ovos" then
        local f = card("Busca de ovos", 150)
        cycle(f, "Raridade", {
            "Todas", "Comum", "Incomum", "Raro",
            "Épico", "Lendário", "Mítico", "Divino", "Secreto"
        }, 43, function(v)
            f:SetAttribute("Raridade", v)
        end)
        button(f, "Auto Ovo: visualização", 87, function()
            f:SetAttribute("AutoOvo", true)
        end)
        local s = card("Status dos ovos no mapa", 95)
        label(s, "Ovos encontrados: não conectado", 42, 20, 12, muted)
        label(s, "Disponibilidade: não conectado", 64, 20, 12, muted)

    elseif tab == "Pets" then
        local f = card("Renda dos pets", 100)
        label(f, "Dinheiro por segundo: —", 43, 25, 15, dark)
        label(f, "Aguardando dados do jogo", 69, 20, 11, muted)
        local s = card("Ovos na sua base", 90)
        label(s, "Lista de ovos não conectada", 43, 24, 12, muted)

    elseif tab == "Movimento" then
        local f = card("Velocidade", 110)
        local speed = 16
        local value = label(f, "Velocidade selecionada: 16", 42, 22, 13)
        button(f, "Aumentar velocidade", 70, function()
            speed = speed >= 200 and 16 or speed + 16
            value.Text = "Velocidade selecionada: " .. speed
            f:SetAttribute("Speed", speed)
        end)
        local a = card("Áreas do mapa", 190)
        cycle(a, "Destino", areas, 43, function(v)
            a:SetAttribute("Destino", v)
        end)
        label(a, "Seletor visual; destinos não conectados.", 85, 40, 11, muted)

    elseif tab == "Proteção" then
        local f = card("Proteção e combate", 140)
        label(f, "Configurações de demonstração.", 42, 22, 12, muted)
        button(f, "Ataque por proximidade: visual", 70, function()
            f:SetAttribute("AttackPreview", true)
        end)
        button(f, "Anti-Stun: visual", 108, function()
            f:SetAttribute("StunPreview", true)
        end)

    elseif tab == "Configurações" then
        local f = card("Preferências", 120)
        button(f, "Ocultar painel", 43, function()
            window.Visible = false
        end)
        button(f, "Tema escuro: visual", 82, function()
            window.BackgroundColor3 = Color3.fromRGB(38, 40, 46)
        end)
    end

    content.CanvasSize = UDim2.new(0, 0, 0,
        content.UIListLayout.AbsoluteContentSize.Y + 10)
end

for i, name in ipairs(tabs) do
    local b = new("TextButton", {
        Position = UDim2.fromOffset(7, 8 + (i - 1) * 49),
        Size = UDim2.new(1, -14, 0, 42),
        BackgroundColor3 = white,
        BorderSizePixel = 0,
        Text = name,
        Font = Enum.Font.GothamSemibold,
        TextSize = 12,
        TextColor3 = dark
    }, sidebar)
    round(b, 9)
    tabButtons[name] = b
    b.Activated:Connect(function()
        render(name)
    end)
end

-- Arrastar a janela pelo cabeçalho
local dragging, start, origin = false

header.InputBegan:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1
        or input.UserInputType == Enum.UserInputType.Touch then
        dragging = true
        start = input.Position
        origin = window.Position
    end
end)

UIS.InputChanged:Connect(function(input)
    if dragging and (
        input.UserInputType == Enum.UserInputType.MouseMovement
        or input.UserInputType == Enum.UserInputType.Touch
    ) then
        local d = input.Position - start
        window.Position = UDim2.new(
            origin.X.Scale, origin.X.Offset + d.X,
            origin.Y.Scale, origin.Y.Offset + d.Y
        )
    end
end)

UIS.InputEnded:Connect(function(input)
    if input.UserInputType == Enum.UserInputType.MouseButton1
        or input.UserInputType == Enum.UserInputType.Touch then
        dragging = false
    end
end)

UIS.InputBegan:Connect(function(input, processed)
    if not processed and input.KeyCode == Enum.KeyCode.RightShift then
        window.Visible = not window.Visible
    end
end)

render("Ovos")

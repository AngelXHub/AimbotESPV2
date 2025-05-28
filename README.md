local RauzitoHub = {
    ESP = false,
    Aimlock = false,
    WallCheck = false,
    TeamCheck = true,
    AimFOV = 50, -- Grau de campo de visão do aimlock
}

-- Função que simula o desenho do círculo do Aim FOV no console
function DrawAimFOVCircle()
    local radius = math.floor(RauzitoHub.AimFOV / 10)  -- escala para console
    local diameter = radius * 2 + 1

    for y = -radius, radius do
        local line = ""
        for x = -radius, radius do
            local dist = math.sqrt(x*x + y*y)
            if math.abs(dist - radius) < 0.7 then
                line = line .. "*"
            else
                line = line .. " "
            end
        end
        print(line)
    end
    print("Aim FOV Circle (approx radius: " .. RauzitoHub.AimFOV .. " degrees)")
end

-- Função de renderização da interface
function DrawRauzitoHub()
    -- Título
    print("====== Rauzito Hub ======")

    -- ESP Toggle
    print("1. ESP: " .. (RauzitoHub.ESP and "ON" or "OFF"))

    -- Aimlock Toggle
    print("2. Aimlock: " .. (RauzitoHub.Aimlock and "ON" or "OFF"))

    -- WallCheck Toggle
    print("3. WallCheck: " .. (RauzitoHub.WallCheck and "ON" or "OFF"))

    -- TeamCheck Toggle
    print("4. Team Check: " .. (RauzitoHub.TeamCheck and "ON" or "OFF"))

    -- Aim FOV Slider
    print("5. Aim FOV: " .. RauzitoHub.AimFOV)

    -- Desenha o círculo do Aim FOV
    DrawAimFOVCircle()

    print("6. Sair")
    print("==========================")
end

-- Função para alternar os estados
function ToggleOption(option)
    if option == 1 then
        RauzitoHub.ESP = not RauzitoHub.ESP
    elseif option == 2 then
        RauzitoHub.Aimlock = not RauzitoHub.Aimlock
    elseif option == 3 then
        RauzitoHub.WallCheck = not RauzitoHub.WallCheck
    elseif option == 4 then
        RauzitoHub.TeamCheck = not RauzitoHub.TeamCheck
    elseif option == 5 then
        print("Defina o novo Aim FOV (10-180): ")
        local input = tonumber(io.read())
        if input and input >= 10 and input <= 180 then
            RauzitoHub.AimFOV = input
        else
            print("Valor inválido!")
        end
    end
end

-- Loop principal da interface
while true do
    DrawRauzitoHub()

    print("Escolha uma opção (1-6): ")
    local choice = tonumber(io.read())

    if choice == 6 then
        print("Fechando Rauzito Hub...")
        break
    else
        ToggleOption(choice)
    end
end

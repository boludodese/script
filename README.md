function love.load()
    player = {
        x = 100, y = 300,
        w = 40, h = 60,
        vy = 0,
        speed = 200,
        jumpPower = -350,
        onGround = false
    }
    gravity = 900

    turboJumpActive = false

    -- UI box
    ui = {
        x = 20, y = 20,
        w = 170, h = 45,
        dragging = false,
        dragOffsetX = 0, dragOffsetY = 0
    }
end

function love.update(dt)
    -- Movimento lateral
    if love.keyboard.isDown("left") or love.keyboard.isDown("a") then
        player.x = player.x - player.speed * dt
    end
    if love.keyboard.isDown("right") or love.keyboard.isDown("d") then
        player.x = player.x + player.speed * dt
    end

    -- Gravidade
    player.vy = player.vy + gravity * dt
    player.y = player.y + player.vy * dt

    -- Chão
    if player.y + player.h > 400 then
        player.y = 400 - player.h
        player.vy = 0
        player.onGround = true
    else
        player.onGround = false
    end
end

function love.keypressed(key)
    if key == "space" and player.onGround then
        player.vy = player.jumpPower
    end

    if key == "f" then
        turboJumpActive = not turboJumpActive
    end

    if turboJumpActive and key == "f" and player.onGround then
        player.vy = player.jumpPower * 3
        -- 3x mais rápido
        player.x = player.x + (player.speed * 3) * (player.x < 800/2 and 1 or -1) * 0.2
    end
end

function love.draw()
    -- Chão
    love.graphics.setColor(0.6,0.6,0.6)
    love.graphics.rectangle("fill", 0, 400, 800, 200)

    -- Jogador
    love.graphics.setColor(0.1,0.4,1)
    love.graphics.rectangle("fill", player.x, player.y, player.w, player.h)

    -- UI
    love.graphics.setColor(0,0,0,0.7)
    love.graphics.rectangle("fill", ui.x, ui.y, ui.w, ui.h, 8, 8)
    love.graphics.setColor(1,1,1)
    local txt = turboJumpActive and "ATIVADO" or "DESATIVADO"
    love.graphics.printf(txt, ui.x, ui.y + 10, ui.w, "center")
end

function love.mousepressed(x, y, button)
    if button == 1 then
        if x > ui.x and x < ui.x + ui.w and y > ui.y and y < ui.y + ui.h then
            ui.dragging = true
            ui.dragOffsetX = x - ui.x
            ui.dragOffsetY = y - ui.y
        end
    end
end

function love.mousereleased(x, y, button)
    if button == 1 then
        ui.dragging = false
    end
end

function love.mousemoved(x, y, dx, dy)
    if ui.dragging then
        ui.x = x - ui.dragOffsetX
        ui.y = y - ui.dragOffsetY
    end
end

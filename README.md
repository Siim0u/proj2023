# proj2023

import pygame
from random import randint
import time
pygame.init()
ekraan = pygame.display.set_mode([800, 600])
ekraan.fill([0, 100, 0])

pygame.display.set_caption("MÃ¤ng")
player = pygame.image.load("ship_big.png")
pilt2 = pygame.image.load("asteroid.png")
pilt3 = pygame.image.load("bg.png")
menuicon = pygame.image.load("menu.png")
seticon = pygame.image.load("set.png")
enemy = pygame.image.load("vastane.png").convert_alpha()
laser_img = pygame.image.load("laser.png").convert_alpha()
player_laser_img = pygame.image.load("laser2.png").convert_alpha()  # Use the uploaded player laser image
player = pygame.transform.scale(player, (300, 150))
enemy = pygame.transform.scale(enemy, (200, 150))
player = pygame.transform.flip(player, True, False)
pilt2 = pygame.transform.scale(pilt2, (100, 100))
pilt3 = pygame.transform.scale(pilt3, (800, 600))
menuicon = pygame.transform.scale(menuicon, (35, 35))
seticon = pygame.transform.scale(seticon, (60, 60))
laser_img = pygame.transform.scale(laser_img, (50, 20))
player_laser_img = pygame.transform.scale(player_laser_img, (70, 40))


punktid = 0

teksti_font = pygame.font.Font(None, 50)
suurfont = pygame.font.Font(None, 100)
rist = teksti_font.render("X", 2, [0, 0, 0])
easy = teksti_font.render("EASY", 2, [0, 0, 0])
medium = teksti_font.render("MEDIUM", 2, [0, 0, 0])
hard = teksti_font.render("HARD", 2, [0, 0, 0])
back = teksti_font.render("BACK", 2, [0, 0, 0])
menutext = teksti_font.render("MENU", 2, [255, 255, 255])
tekst_pildina = teksti_font.render("SCORE: " + str(punktid), 1, [255, 255, 255])
loss = suurfont.render("YOU LOSE!", 1, [255, 255, 255])
pygame.display.flip()
menu = 1

def difficulty(diff):
    if diff == 3:
        text = teksti_font.render("DIFFICULTY: HARD", 1, [255, 255, 255])
    elif diff == 2:
        text = teksti_font.render("DIFFICULTY: MEDIUM", 1, [255, 255, 255])
    else:
        text = teksti_font.render("DIFFICULTY: EASY", 1, [255, 255, 255])
    return text
    
player_x = 200
player_y = 200
pilt2_x = 800
pilt2_y = randint(0, 550)
raske_x = 800
raske_y = 800
suur_x = 800
suur_y = randint(0, 450)
enemy_x = 500
enemy_y = randint(0, 550)
samm = 4
elud = 3
punktid = 0
raskus = 1
settings = 0
tulemused = 0

enemy_direction = 1  # Enemy will move down initially (1 for down, -1 for up)
enemy_move_speed = 0.5
enemy_move_range = 85  # Scale player laser image

# Player laser variables
player_laser_active = False
player_laser_x = player_x
player_laser_y = player_y
player_laser_speed = 3

# Enemy laser variables
laser_active = False
laser_x = enemy_x
laser_y = enemy_y
laser_speed = 0.5
last_shot_time = 0
min_shot_interval = 3  # Minimum seconds between shots
max_shot_interval = 5 # Maximum seconds between shots
shot_interval = randint(min_shot_interval, max_shot_interval)

# Function to shoot player laser
def shoot_player_laser():
    global player_laser_active, player_laser_x, player_laser_y
    if not player_laser_active:
        player_laser_active = True
        player_laser_x = player_x + player.get_width() // 2  # Start the laser at the center of the player
        player_laser_y = player_y + player.get_height() // 2  # Center laser on player
        print("Laser shot!")  # Center laser on player


running = True
pygame.key.set_repeat(1,10)
while running:
    #ekraan.fill([255, 255, 255])
    ekraan.blit(pilt3, (0,0))
    pygame.draw.rect(ekraan, [0, 225, 0], [330, 300, 150, 80], 0)
    pygame.draw.rect(ekraan, [225, 0, 0], [755, 5, 40, 40], 0)
    ekraan.blit(rist, [764, 10])
    pygame.draw.polygon(ekraan, color=(0, 0, 0),points=[(390, 320), (390, 360), (430, 340)])
    pygame.draw.rect(ekraan, [120, 120, 170], [330, 400, 150, 80], 0)
    ekraan.blit(seticon, [375, 410])
    if settings == 1:
        pygame.draw.rect(ekraan, [0, 225, 0], [330, 200, 150, 80], 0)
        pygame.draw.rect(ekraan, [225, 225, 0], [330, 300, 150, 80], 0)
        pygame.draw.rect(ekraan, [225, 0, 0], [330, 400, 150, 80], 0)
        pygame.draw.rect(ekraan, [120, 120, 170], [330, 530, 150, 50], 0)
        ekraan.blit(easy, [358, 225])
        ekraan.blit(medium, [337, 325])
        ekraan.blit(hard, [355, 425])
        ekraan.blit(back, [355, 540])
    
    if tulemused == 1:
        pygame.draw.rect(ekraan, [255, 255, 255], [170, 60, 460, 170], 0)
        pygame.draw.rect(ekraan, [0, 0, 0], [180, 70, 440, 150], 0)
        pygame.draw.rect(ekraan, [255, 255, 255], [200, 220, 400, 165], 0)
        pygame.draw.rect(ekraan, [0, 0, 0], [210, 180, 380, 195], 0)
        pygame.draw.rect(ekraan, [255, 255, 255], [320, 390, 170, 100], 0)
        pygame.draw.rect(ekraan, [0, 0, 0], [330, 400, 150, 80], 0)
        ekraan.blit(loss, [210, 100])
        ekraan.blit(tekst_pildina, [230, 230])
        ekraan.blit(difficulty(raskus), [230, 280])
        ekraan.blit(menutext, [355, 425])
    
    if menu == 0 and settings == 0 and tulemused == 0:
        ekraan.blit(pilt3, (0,0))
        ekraan.blit(player, (player_x,player_y))
        ekraan.blit(pilt2, (pilt2_x,pilt2_y))
        ekraan.blit(pilt2, (suur_x,suur_y))
        ekraan.blit(enemy, (enemy_x,enemy_y))
        pygame.draw.rect(ekraan, [225, 0, 0], [755, 5, 40, 40], 0)
        ekraan.blit(rist, [764, 10])
        pygame.draw.rect(ekraan, [200, 0, 200], [705, 5, 40, 40], 0)
        pygame.draw.rect(ekraan, [0, 0, 0], [5, 5, 205, 45], 0)
        ekraan.blit(tekst_pildina, [8, 10])
        ekraan.blit(menuicon, [707, 7])
        pygame.draw.rect(ekraan, [0, 0, 0], [0, 550, 205, 50], 0)
        
        if enemy_x > 600:
            enemy_x -= 0.15 + punktid * 0.02 + raskus * 0.05 
        if enemy_y < player_y:
            enemy_y += 0.15 + punktid * 0.02 + raskus * 0.05
        elif enemy_y > player_y:
            enemy_y -= 0.15 + punktid * 0.02 + raskus * 0.05
            

            
        if raskus >= 3:
            ekraan.blit(pilt2, (raske_x,raske_y))
            raske_x -= 0.15 + punktid * 0.02 + raskus * 0.05
            if raske_x < 0:
                punktid = punktid + 1
                raske_x = 800
                raske_y = randint(0, 550)
            if player_x + 280 > raske_x and player_x < raske_x + 100 and player_y + 100 > raske_y and player_y < raske_y + 100:
                punktid = punktid - 1
                raske_x = 800
                elud -= 1
                raske_y = randint(0, 550)
        pilt2_x -= 0.15 + punktid * 0.02 + raskus * 0.05
        if pilt2_x < 0:
            punktid = punktid + 1
            pilt2_x = 800
            pilt2_y = randint(0, 550)
        if player_x + 280 > pilt2_x and player_x < pilt2_x + 100 and player_y + 100 > pilt2_y and player_y < pilt2_y + 100:
            punktid = punktid - 1
            pilt2_x = 800
            elud -= 1
            pilt2_y = randint(0, 550)
            
        suur_x -= 0.1 + punktid * 0.02 + raskus * 0.05
        
        if suur_x < 0:
            punktid = punktid + 1
            suur_x = 800
            suur_y = randint(0, 550)
        if player_x + 280 > suur_x and player_x < suur_x + 100 and player_y + 100 > suur_y and player_y < suur_y + 100:
            punktid = punktid - 1
            suur_x = 800
            elud -= 1
            suur_y = randint(0, 550)
            
        # Enemy laser mechanics
        current_time = time.time()
        if not laser_active and current_time - last_shot_time > shot_interval:
            laser_active = True
            laser_x = enemy_x
            laser_y = enemy_y + enemy.get_height() // 2
            last_shot_time = current_time
           

        if laser_active:
            laser_x -= laser_speed
            ekraan.blit(laser_img, (laser_x, laser_y))
            if player_x < laser_x < player_x + player.get_width() and player_y < laser_y < player_y + player.get_height():
                elud -= 1
                laser_active = False

        if laser_x < 0:
            laser_active = False

        if player_laser_active:
            player_laser_x += player_laser_speed
            ekraan.blit(player_laser_img, (player_laser_x, player_laser_y))
            if player_laser_x > ekraan.get_width():
                player_laser_active = False  # Reset the laser when it goes off-screen
                
        # Check for collision with asteroid
        if player_laser_active and pilt2_x < player_laser_x < pilt2_x + pilt2.get_width() and pilt2_y < player_laser_y < pilt2_y + pilt2.get_height():
            punktid += 1  # Increase score
            pilt2_x = 800  # Reset asteroid position
            pilt2_y = randint(0, 550)
            player_laser_active = False  # Deactivate the player's laser
            
        if player_laser_active and suur_x < player_laser_x < suur_x + pilt2.get_width() and suur_y < player_laser_y < suur_y + pilt2.get_height():
            punktid += 1  # Increase score
            suur_x = 800  # Reset asteroid position
            suur_y = randint(0, 550)
            player_laser_active = False  # Deactivate the player's laser

        if player_x > 900 or player_x < -100:
            player_x = 20
        if player_y > 500:
            player_y = 400
        if player_y < -100:
            player_y = 20
        tekst_pildina = teksti_font.render("SCORE: " + str(punktid), 1, [255, 255, 255])
        if elud <= 0:
            tulemused = 1
        elif elud >= 3:
            pygame.draw.rect(ekraan, [0, 225, 0], [136, 550, 69, 50], 0)
            pygame.draw.rect(ekraan, [0, 225, 0], [68, 550, 66, 50], 0)
            pygame.draw.rect(ekraan, [0, 225, 0], [0, 550, 66, 50], 0)
        elif elud >= 2:
            pygame.draw.rect(ekraan, [0, 225, 0], [68, 550, 66, 50], 0)
            pygame.draw.rect(ekraan, [0, 225, 0], [0, 550, 66, 50], 0)
        elif elud >= 1:
            pygame.draw.rect(ekraan, [0, 225, 0], [0, 550, 66, 50], 0)
            
            
    pygame.display.flip()
    
    for i in pygame.event.get():
        if i.type == pygame.MOUSEBUTTONDOWN:
            hiir_x,hiir_y = i.pos
            if hiir_x > 755 and hiir_x < 795 and hiir_y > 5 and hiir_y < 45:
                running = False
            if hiir_x > 705 and hiir_x < 745 and hiir_y > 5 and hiir_y < 45:
                menu = 1
        if i.type == pygame.QUIT:
            running = False
        if menu == 1:
            player_x = 200
            player_y = 200
            raske_x = 800
            raske_y = 800
            pilt2_x = 800
            pilt2_y = randint(0, 550)
            suur_x = 800
            suur_y = randint(0, 450)
            vastane_x = 750
            vastane_y = 200
            samm = 7
            elud = 3
            punktid = 0
            if i.type == pygame.MOUSEBUTTONDOWN:
                hiir_x,hiir_y = i.pos
                if hiir_x > 330 and hiir_x < 480 and hiir_y > 300 and hiir_y < 380:
                    menu = 0
                elif hiir_x > 755 and hiir_x < 795 and hiir_y > 5 and hiir_y < 45:
                    running = False
                elif hiir_x > 330 and hiir_x < 480 and hiir_y > 400 and hiir_y < 480:
                    settings = 1
                    menu = 0
        if settings == 1:
            if i.type == pygame.MOUSEBUTTONDOWN:
                hiir_x,hiir_y = i.pos
                if hiir_x > 330 and hiir_x < 480 and hiir_y > 200 and hiir_y < 280:
                    raskus = 1
                elif hiir_x > 330 and hiir_x < 480 and hiir_y > 300 and hiir_y < 380:
                    raskus = 2
                elif hiir_x > 330 and hiir_x < 480 and hiir_y > 400 and hiir_y < 480:
                    raskus = 3
                elif hiir_x > 330 and hiir_x < 480 and hiir_y > 530 and hiir_y < 580:
                    menu = 1
                    settings = 0
        if tulemused == 1:
            if i.type == pygame.MOUSEBUTTONDOWN:
                hiir_x,hiir_y = i.pos
                if hiir_x > 330 and hiir_x < 480 and hiir_y > 400 and hiir_y < 480:
                    menu = 1
                    tulemused = 0
        elif i.type == pygame.KEYDOWN:
            if i.key == pygame.K_UP:
                player_y = player_y - samm
            elif i.key == pygame.K_DOWN:
                player_y = player_y + samm
            elif i.key == pygame.K_LEFT:
                player_x = player_x - samm
            elif i.key == pygame.K_RIGHT:
                player_x = player_x + samm
            if i.key == pygame.K_SPACE:
                shoot_player_laser()
        
pygame.quit()

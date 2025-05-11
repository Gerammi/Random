import pygame
import sys
import random


pygame.init()

WIDTH, HEIGHT = 800, 600

screen = pygame.display.set_mode((WIDTH, HEIGHT))
pygame.display.set_caption("The star fight")

rocket_image =  pygame.image.load("rocket.png")
rocket_rect = rocket_image.get_rect()

rocket_speed = 5

bullet_image =  pygame.image.load("bullet.png")
bullet_rect = bullet_image.get_rect()

bullet_speed = 8

enemy_image =  pygame.image.load("enemy.png.")
enemy_rect = bullet_image.get_rect()

enemy_speed = 2

meteor_image =  pygame.image.load("meteor.png.")
meteor_rect = bullet_image.get_rect()

meteor_speed = 1

rocket_rect.x  = WIDTH // 2 - rocket_rect.width // 2
rocket_rect.y = HEIGHT - rocket_rect.width - 10

bullets = []
enemies = []

font_path ="score_lives_font (1).ttf"
font = pygame.font.Font(font_path, 36)

font_path2 ="game_rules_font.ttf"
font2 = pygame.font.Font(font_path2, 25)


score = 2
lives = 5

pygame.mixer.music.load("gamesound.mp3")
pygame.mixer.music.set_volume(0.2)
pygame.mixer.music.play(-1)

hit_sound = pygame.mixer.Sound("hit_enemy.mp3")


meteorites = []

while True:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            pygame.quit()
            sys.exit()
        if event.type  == pygame.KEYDOWN and event.key == pygame.K_SPACE:
            bullet_rect = bullet_image.get_rect()
            bullet_rect.x  = rocket_rect.x + rocket_rect.width // 2 - bullet_rect.width // 2
            bullet_rect.y = rocket_rect.y
            bullets.append(bullet_rect)

    screen.fill((0,0,255))
    screen.blit(rocket_image, rocket_rect)

    keys = pygame.key.get_pressed()

    if keys[pygame.K_RIGHT] and rocket_rect.x < WIDTH - rocket_rect.width:
        rocket_rect.x += rocket_speed

    if keys[pygame.K_LEFT] and rocket_rect.y > 0 :
        rocket_rect.x -= rocket_speed

    bullets = [bullet for bullet in bullets if bullet.y > 0]

    for bullet in bullets:
        bullet.y -= bullet_speed

    for bullet in bullets:
        screen.blit(bullet_image, bullet)

    if random.randint(1, 100) <=3:
        enemy_rect = enemy_image.get_rect()
        enemy_rect.x = random.randint(0,WIDTH - enemy_rect.width)
        enemy_rect.y = 0
        enemies.append(enemy_rect)

    for enemy in enemies:
        enemy.y += enemy_speed
        if enemy.y + enemy.height >  HEIGHT:
            enemies.remove(enemy)

    for enemy in enemies:
        screen.blit(enemy_image, enemy)

    for enemy in enemies:
        if enemy.colliderect(rocket_rect):
            enemies.remove(enemy)
            lives -= 1

    score_text = font.render("очки : {}".format(score), True, (255, 255, 255))
    screen.blit(score_text, (10, 550))

    lives_text = font.render("жизни : {}".format(lives), True, (250, 253, 253))
    screen.blit(lives_text, (10, 510))

    pravila_text = font2.render("сбей 20 инопланитян для победы :)", True, (168, 216, 255))
    screen.blit(pravila_text, (220, 20))



    if random.randint(1, 100) <= 6:
        meteor_rect = meteor_image.get_rect()
        meteor_rect.x = random.randint(0, WIDTH - meteor_rect.width)
        meteor_rect.y = 5
        meteorites.append(meteor_rect)

    for meteor in meteorites:
        meteor.y += meteor_speed
        if meteor.y - meteor.height > HEIGHT:
            meteorites.remove(meteor)

    for meteor in meteorites:
        screen.blit(meteor_image, meteor)


    for bullet in bullets:
        for enemy in enemies:
            if bullet.colliderect(enemy):
                # +1 балл
                score += 1
                bullets.remove(bullet)
                enemies.remove(enemy)
                hit_sound.play()

    for bullet in bullets:
        for meteor in meteorites:
            if bullet.colliderect(meteor):
                bullets.remove(bullet)
                meteorites.remove(meteor)

    pygame.display.flip()
    pygame.time.Clock().tick(60)

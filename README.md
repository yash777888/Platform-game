# Platform-game
import pygame
from sys import exit
from random import randint

FPS = 60

def display_score():
    current_time =int( int(pygame.time.get_ticks() - start_time ) / 100)

    score_surface = test_font.render(f'Score : {current_time}' , False , (64,64,64))
    score_rectangle = score_surface.get_rect(center = (400 , 50))

    
    screen.blit(score_surface,score_rectangle)
    
    return current_time

def obstacle_movement(obstacle_list):

    if obstacle_list:
        for obstacle_rectangle in obstacle_list:
            obstacle_rectangle.x -= 5

            if obstacle_rectangle.bottom == 300:
                screen.blit(snail_surface , obstacle_rectangle)

            else:
                screen.blit(fly_surface , obstacle_rectangle)

        obstacle_list = [obstacle for obstacle in obstacle_list if obstacle.x > -100]

        return obstacle_list
    
    else:
        return []

def collisions(player , obstacles):
    if obstacles:
        for obstacle_rect in obstacles:
            if player.colliderect(obstacle_rect):
                return False
            
    return True

def player_animation():
    global player_index , player_surface

    if player_rectangle.bottom < 300:
        player_surface = player_jump 

    else:
        player_index += 0.1
        if player_index >= len(player_walk_list):
            player_index = 0
        
        player_surface = player_walk_list[int(player_index)]


pygame.init()          #simply makes the systems ready for the tasks up ahead 

screen = pygame.display.set_mode((800,400))
clock = pygame.time.Clock()

test_font = pygame.font.Font("C:\\Users\\YASH\\UltimatePygameIntro-main\\font\\Pixeltype.ttf", 50)

game_name = test_font.render("Pixel Runner" , False , (111 , 196 , 169))
game_name_rectangle = game_name.get_rect(center = (400 , 80))

game_message = test_font.render("Press space to run" , False , (111 , 196 , 169))
game_message_rectangle = game_message.get_rect(center = (400 , 330))

pygame.display.set_caption("Runner")

sky_surface = pygame.image.load("C:\\Users\\YASH\\UltimatePygameIntro-main\\graphics\\Sky.png").convert()
ground_surface = pygame.image.load('C:\\Users\\YASH\\UltimatePygameIntro-main\\graphics\\ground.png').convert()

#Obstacles
snail_frame_1 = pygame.image.load("C:\\Users\\YASH\\UltimatePygameIntro-main\\graphics\\snail\\snail1.png").convert_alpha()
snail_frame_2 =  pygame.image.load("C:\\Users\\YASH\\UltimatePygameIntro-main\\graphics\\snail\\snail2.png").convert_alpha()
snail_frame_list = [snail_frame_1 , snail_frame_2]
snail_frame_index = 0
snail_surface = snail_frame_list[snail_frame_index]

fly_frame_1= pygame.image.load("C:\\Users\\YASH\\UltimatePygameIntro-main\\graphics\\fly\\fly1.png").convert_alpha()
fly_frame_2= pygame.image.load("C:\\Users\\YASH\\UltimatePygameIntro-main\\graphics\\fly\\fly2.png").convert_alpha()
fly_frame_list = [fly_frame_1 , fly_frame_2]
fly_frame_index = 0
fly_surface = fly_frame_list[fly_frame_index]

obstacle_rectangle_list = []

player_walk_1 = pygame.image.load("C:\\Users\\YASH\\UltimatePygameIntro-main\\graphics\\player\\player_walk_1.png").convert_alpha()
player_walk_2 = pygame.image.load("C:\\Users\\YASH\\UltimatePygameIntro-main\\graphics\\player\\player_walk_2.png").convert_alpha()
player_walk_list = [player_walk_1 , player_walk_2]
player_index = 0
player_jump = pygame.image.load("C:\\Users\\YASH\\UltimatePygameIntro-main\\graphics\\player\\jump.png").convert_alpha()
player_surface = player_walk_list[player_index]
player_rectangle = player_surface.get_rect(midbottom = (80,300))

player_stand = pygame.image.load("C:\\Users\\YASH\\UltimatePygameIntro-main\\graphics\\player\\player_stand.png").convert_alpha()
player_stand_scaled = pygame.transform.rotozoom(player_stand , 0 , 2)
player_stand_rectangle = player_stand_scaled.get_rect(center = (400 , 200))

#score_surface = test_font.render("My Game" , False , (64,64,64))
#score_rectangle = score_surface.get_rect(center = (400,50))

player_gravity = 0 
start_time = 0

game_active = True

#Timer
obstacle_timer = pygame.USEREVENT + 1
pygame.time.set_timer(obstacle_timer , 1500)

snail_animation_timer = pygame.USEREVENT + 2
pygame.time.set_timer(snail_animation_timer , 500)

fly_animation_timer = pygame.USEREVENT + 3
pygame.time.set_timer(fly_animation_timer , 200)

while True:
    
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            pygame.quit()
            exit()

        if game_active:

            if event.type == pygame.MOUSEBUTTONDOWN:
                if player_rectangle.collidepoint(event.pos) and player_rectangle.bottom >= 300:
                    player_gravity = -20

            if event.type == pygame.KEYDOWN:
                if event.key == pygame.K_SPACE and player_rectangle.bottom >= 300:
                    player_gravity = -20

        else:
            if event.type == pygame.KEYDOWN and event.key == pygame.K_SPACE:
                game_active = True
                start_time = pygame.time.get_ticks()
            
        if game_active:
            if event.type == obstacle_timer:
                if randint(0,2):
                    obstacle_rectangle_list.append(snail_surface.get_rect(bottomright = (randint(900 , 1100),300)))
                else:
                    obstacle_rectangle_list.append(fly_surface.get_rect(bottomright = (randint(900 , 1100),210)))
            
            if event.type == snail_animation_timer:
                if snail_frame_index == 0: 
                    snail_frame_index = 1

                else:
                    snail_frame_index = 0
                    snail_surface = snail_frame_list[snail_frame_index]


            if event.type == fly_animation_timer:
                if fly_frame_index == 0: 
                    fly_frame_index = 1

                else:
                    fly_frame_index = 0
                    fly_surface = fly_frame_list[fly_frame_index]
                
    if game_active == True:

        player_animation()

        screen.blit(sky_surface , (0,0))        #blit means to put the "Test Surface" on the "Main Surface"
        screen.blit(ground_surface , (0,300))

        #pygame.draw.rect(screen , "#c0e8ec" , score_rectangle)
        #pygame.draw.rect(screen , "#c0e8ec" , score_rectangle.inflate(10,10))


        #screen.blit(score_surface, score_rectangle)

        #if snail_rectangle.right <= 0:
        #   snail_rectangle.left = 800

        #if player_rectangle.colliderect(snail_rectangle):
            # print("Collision")
    
        #snail_rectangle.x -= 5
        #player_rectangle.x += 2

        #Obstacle Movement
        obstacle_rectangle_list = obstacle_movement(obstacle_rectangle_list )

        #screen.blit(snail_surface , snail_rectangle)
        screen.blit(player_surface , player_rectangle)

        keys = pygame.key.get_pressed()

        player_gravity += 1 
        player_rectangle.y += player_gravity

        if player_rectangle.bottom >= 300:
            player_rectangle.bottom = 300
        
        score = display_score()

        if not collisions(player_rectangle, obstacle_rectangle_list):
            game_active = False

    else:
        screen.fill((94 , 129 , 162))
        screen.blit(player_stand_scaled, player_stand_rectangle)
        screen.blit(game_name , game_name_rectangle)
        obstacle_rectangle_list.clear()
        player_rectangle.midbottom = (80 , 300)

        score_message =test_font.render(f"Your score: {score}" , False , (111,196,169))
        score_message_rectangle = score_message.get_rect(center = (400 , 330))

        if score == 0:
            screen.blit(game_message, game_message_rectangle)

        else:
            screen.blit(score_message , score_message_rectangle)

    pygame.display.update()
    clock.tick(FPS)

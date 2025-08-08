# Pixel-Runner
My first pygame! Watched a tutorial to implement most features!

Sprites, bg images, all you can find online and even make your own custom surfaces for this game!

CODE:
```python
# MODULES/LIBRARIES AND FUNCTIONS                                                                                                                                                                                                                                                                                                 
import os
os.chdir(r"C:\Users\pc\Desktop\PYTHON\Pygames\IMAGES_AUDIOS_ETC_FOR_PYGAME")
import pygame
from sys import exit as BREAK
import random
import time

# Classes for sprites
# player class
class Player(pygame.sprite.Sprite):
    def __init__(self):
        super().__init__()
        player_walk1_image = "player_walk_1.png"
        player_walk2_image = "player_walk_2.png"
        player_jump_image = "jump.png"

        player_walk1 = pygame.image.load(player_walk1_image).convert_alpha()
        player_walk2 = pygame.image.load(player_walk2_image).convert_alpha()
        self.player_walking = [player_walk1, player_walk2]
        self.player_index = 0
        self.player_jump = pygame.image.load(player_jump_image).convert_alpha()

        self.image = self.player_walking[self.player_index]
        self.rect = self.image.get_rect(center = (400, 300))
        self.gravity = 0

        self.jump_sound = pygame.mixer.Sound("jump.mp3")
        self.jump_sound.set_volume(0.4)

    def player_input(self):
        keys = pygame.key.get_pressed()
        if keys[pygame.K_SPACE] and self.rect.bottom >= 300:
            self.jump_sound.play()    
            self.gravity = -20
        elif keys[pygame.K_UP] and self.rect.bottom >= 300:
            self.jump_sound.play()
            self.gravity = -20    

    def apply_gravity(self):
        self.gravity += 1
        self.rect.y += self.gravity
        if self.rect.bottom >= 300:
            self.rect.bottom = 300

    def animation_handler(self):
        if self.rect.bottom < 300:
            self.image = self.player_jump
        else:
            self.player_index += 0.1
            if self.player_index >= len(self.player_walking): self.player_index = 0
            self.image = self.player_walking[int(self.player_index)]

    def update(self):
        self.player_input()
        self.apply_gravity()
        self.animation_handler()

# obstacle class
class Obstacle(pygame.sprite.Sprite):
    def __init__(self, type):
        super().__init__()

        if type == "snail":
            snail_image_1 = "snail1.png"
            snail_image_2 = "snail2.png"
            snail_surface_1 = pygame.image.load(snail_image_1).convert_alpha()
            snail_surface_2 = pygame.image.load(snail_image_2).convert_alpha()
            self.frames = [snail_surface_1, snail_surface_2]
            y_pos = 300
        elif type == "fly":
            fly_image_1 = "Fly1.png"
            fly_image_2 = "Fly2.png"
            fly_surface_1 = pygame.image.load(fly_image_1).convert_alpha()
            fly_surface_2 = pygame.image.load(fly_image_2).convert_alpha()
            self.frames = [fly_surface_1, fly_surface_2]
            y_pos = 210

        self.index = 0
        self.image = self.frames[self.index]
        self.rect = self.image.get_rect(midbottom = (random.randint(900, 1100), y_pos))

    def animation_handler(self):
        self.index += 0.08

        if self.index >= len(self.frames):
            self.index = 0
        self.image = self.frames[int(self.index)]
    
    def move_rect(self):
        self.rect.x -= 5

    def kill_image(self):
        if self.rect.x <= -100:
            self.kill()

    def update(self):
        self.animation_handler()
        self.move_rect()
        self.kill_image()


# Displays score
def display_score():
    current_time = int(pygame.time.get_ticks() / 1000) - start_time
    score_surface = font.render(f"Score: {current_time}", False, (68 ,68 ,68))
    score_rectangle = score_surface.get_rect(center = (400, 45))
    SCREEN.blit(score_surface, score_rectangle)
    return current_time

# displays intro and etc
def game_over_intro_surface():
    SCREEN.fill((100, 100, 162))
    score_message = font.render(f"Your score was: {score}. Press E to play again!", False, (0, 0, 0))
    score_message_rect = score_message.get_rect(center = (400, 320))
    if score == 0: SCREEN.blit(game_message_surface, game_message_rectangle)
    else: SCREEN.blit(score_message, score_message_rect)
    SCREEN.blit(player_stand, player_stand_rectangle)
    SCREEN.blit(game_name_text_surface, game_name_rectangle)

# collison function
def collision_sprite():
    if pygame.sprite.spritecollide(player_group.sprite, obstacle_group, False):
        obstacle_group.empty()
        return False
    else:
        return True


# INITIALIZING AND OTHER MAIN HANDLING
pygame.init()
SCREEN = pygame.display.set_mode((800, 400))
TITLE = pygame.display.set_caption("Zaroon the sigmas first pygame!")
clock_object = pygame.time.Clock()
font = pygame.font.Font("Pixeltype.ttf", 50)
start_time = 0
game_music = pygame.mixer.Sound("music.wav")
game_music.set_volume(0.33)
game_music.play(loops=-1)


# class groups
# player group
player_group = pygame.sprite.GroupSingle()
player_group.add(Player())

# obstacle group
obstacle_group = pygame.sprite.Group()


# IMAGES/SURFACES AND POSITION VARIABLES ETC ON SCREEN
# background images
image_sky = "Sky.png"
sky_surface = pygame.image.load(image_sky).convert_alpha()

image_ground = "Ground.png"
ground_surface = pygame.image.load(image_ground).convert_alpha()

# intro screen with player standing image
player_stand_image = "player_stand.png"
player_stand = pygame.image.load(player_stand_image)
player_stand = pygame.transform.rotozoom(player_stand, 0, 1.5)
player_stand_rectangle = player_stand.get_rect(center = (400, 200))

# game name intro variables etc
game_name_text_surface = font.render("Pixel Runner", False, ("Pink"))
game_name_rectangle = game_name_text_surface.get_rect(center = (405, 90))
game_message_surface = font.render("Press E to play!", False, (0, 0, 0))
game_message_rectangle = game_message_surface.get_rect(center = (405, 320))

# Timer for obstacles to load
obstacle_timer = pygame.USEREVENT + 1
pygame.time.set_timer(obstacle_timer, 1100)

# snail anim timer
# snail_anim_timer = pygame.USEREVENT + 2
# pygame.time.set_timer(snail_anim_timer, 480)

# # fly anim timer
# fly_anim_timer = pygame.USEREVENT + 3
# pygame.time.set_timer(fly_anim_timer, 200)

# LOAD EVRYTHING INSIDE HERE
scroll = 0
score = 0
game_active = False
running = True
while running:
    for each_event in pygame.event.get():
        # QUIT THE GAME WITH CROSS BUTTON ON TOP RIGHT
        if each_event.type == pygame.QUIT:
            print("Exiting window...")
            time.sleep(0.5)
            print("Window exited")     
            pygame.quit()
            BREAK() # or change running to False (running = False)

        if game_active:
            pass
            # MOVING GRAV ON KEYBOARD
                    # movement but not needed for this game
                    # elif each_event.key == pygame.K_RIGHT:
                    #     player_x += 40
                    # elif each_event.key == pygame.K_LEFT:
                    #     player_x -= 40
        else:
            if each_event.type == pygame.KEYDOWN and each_event.key == pygame.K_e:
                game_active = True
                start_time = int(pygame.time.get_ticks() / 1000)

        # obstacle anims and loading etc
        if game_active:
            if each_event.type == obstacle_timer and game_active:
                obstacle_group.add(Obstacle(random.choice(["fly", "snail", "snail"])))
    

    if game_active:
        # IMPLEMENT SURFACES INTO MAIN SCREEN
        scroll -= 2.6
        SCREEN.blit(sky_surface, (0 + scroll, 0))
        SCREEN.blit(sky_surface, (scroll + sky_surface.get_width(), 0))
        SCREEN.blit(ground_surface, (0 + scroll, 300))
        SCREEN.blit(ground_surface, (scroll + ground_surface.get_width(), 300))

        if abs(scroll) > sky_surface.get_width():
            scroll = 0

        if abs(scroll) > ground_surface.get_width():
            scroll = 0

        score = display_score()
        # draw classes
        # draw player sprites
        player_group.draw(SCREEN)
        player_group.update()

        # draw obstacle sprites
        obstacle_group.draw(SCREEN)
        obstacle_group.update()
        
        # CHECK FOR COLLISIONS
        game_active = collision_sprite()
    else:
        # SCREEN.fill((100, 100, 162))
        game_over_intro_surface()
        
    # NEEDED TO UPDATE EVERY FRAME 
    pygame.display.update()
    clock_object.tick(60)
```

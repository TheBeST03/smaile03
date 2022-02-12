# smaile03
from pygame import *

from random  import randint
class GameSprite(sprite.Sprite):
    def __init__(self, player_image, player_x, player_y ,sixe_x, size_y, player_speed):
        super().__init__()
        self.image = transform.scale(image.load(player_image), (sixe_x, size_y))
        self.speed  = player_speed
        self.rect = self.image.get_rect()
        self.rect.x = player_x
        self.rect.y = player_y
    def reset(self):
        window.blit(self.image, (self.rect.x, self.rect.y))

class Player(GameSprite):
    def update(self): 
        keys_pressed = key.get_pressed()
        if keys_pressed[K_a] and self.rect.x > 5:
            self.rect.x -=25
        if keys_pressed[K_d] and self.rect.x < 595:
            self.rect.x +=25
    def fire(self):
        bullet = Bullet('bullet.png', self.rect.centerx, self.rect.top,15,20, -15)
        bullets.add(bullet)
lost = 0
score = 0
class Enemy(GameSprite):
    def update(self):
        self.rect.y += 5 
        global lost
        if self.rect.y > 500:
            self.rect.x = randint(80,700- 80)
            self.rect.y = 0
            lost = lost + 1

class Bullet(GameSprite):
    def update(self):
        self.rect.y +=self.speed
        if self.rect.y < 0:
            self.kill()


window = display.set_mode((700, 500))
display.set_caption("Шутер")
background = transform.scale(image.load('galaxy.jpg'), (700, 500))

monsters = sprite.Group()
for i in range(1, 8):
    monster = Enemy('ufo.png', randint(80,500-80), -40, 80 , 50, randint(1, 5))
    monsters.add(monster)


finish = False
mixer.init()
fire_sound = mixer.Sound('fire.ogg')

mixer.music.load('space.ogg')
mixer.music.play()
game = True

ROBOT = Player('rocket.png', 10, 400, 80, 100, 2)

bullets = sprite.Group()

font.init()
font1 = font.Font(None,80)
win = font1.render('YOU WIN!!!', True, (255, 255, 255))
lose = font1.render('YOU LOSE!!!!!!!!!', True, (180,0 ,0))


monster = Enemy("ufo.png",  (620), -40, 80, 50, randint(1,5))
monsters.add(monster)








while game:
    for e in event.get():
        if  e.type == QUIT:
            game = False
        elif e.type == KEYDOWN:
            if e.key == K_SPACE:
                ROBOT.fire()
    if finish != True:
        window.blit(background,(0,0))
        ROBOT.update()
        ROBOT.reset()
        monsters.update()
        bullets.update()
        monsters.draw(window)
        
        bullets.draw(window)
        collides =sprite.groupcollide(monsters, bullets, True, True)
        for c in collides:

            score = score + 1
            monster = Enemy("ufo.png",  randint(1,620), -40, 80, 50, randint(1,5))
            monsters.add(monster)
        if score >= 1000:
            finish = True
            window.blit(win, (200,200))
        if lost >= 10:
            finish = True
            window.blit(lose, (200, 200))

        display.update()





    time.delay(50)




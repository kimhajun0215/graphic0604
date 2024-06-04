import pygame
import random

# 초기화
pygame.init()

# 화면 설정
screen_width = 800
screen_height = 600
screen = pygame.display.set_mode((screen_width, screen_height))
pygame.display.set_caption("공 게임")

# 색상 설정
WHITE = (255, 255, 255)
RED = (255, 0, 0)
BLUE = (0, 0, 255)
GREEN = (0, 255, 0)

# 공 클래스
class Ball:
    def __init__(self, x, y, dx, dy, color):
        self.x = x
        self.y = y
        self.dx = dx
        self.dy = dy
        self.color = color
        self.radius = 10

    def move(self):
        self.x += self.dx
        self.y += self.dy
        if self.x - self.radius < 0 or self.x + self.radius > screen_width:
            self.dx = -self.dx
        if self.y - self.radius < 0 or self.y + self.radius > screen_height:
            self.dy = -self.dy

    def draw(self, screen):
        pygame.draw.circle(screen, self.color, (self.x, self.y), self.radius)

# 아이템 클래스
class Item:
    def __init__(self, x, y, effect):
        self.x = x
        self.y = y
        self.effect = effect
        self.width = 20
        self.height = 20

    def draw(self, screen):
        if self.effect == 'increase_balls':
            color = RED
        elif self.effect == 'speed_up':
            color = BLUE
        elif self.effect == 'slow_down':
            color = GREEN
        pygame.draw.rect(screen, color, (self.x, self.y, self.width, self.height))

# 게임 설정
balls = [Ball(random.randint(100, 700), random.randint(100, 500), random.choice([-3, 3]), random.choice([-3, 3]), WHITE)]
items = [Item(random.randint(100, 700), random.randint(100, 500), random.choice(['increase_balls', 'speed_up', 'slow_down'])) for _ in range(5)]

# 게임 루프
running = True
clock = pygame.time.Clock()
while running:
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            running = False

    screen.fill((0, 0, 0))
    
    for ball in balls:
        ball.move()
        ball.draw(screen)

    for item in items:
        item.draw(screen)

    # 공과 아이템 충돌 검사
    for ball in balls:
        for item in items:
            if ball.x - ball.radius < item.x + item.width and ball.x + ball.radius > item.x and ball.y - ball.radius < item.y + item.height and ball.y + ball.radius > item.y:
                if item.effect == 'increase_balls':
                    balls.append(Ball(random.randint(100, 700), random.randint(100, 500), random.choice([-3, 3]), random.choice([-3, 3]), WHITE))
                elif item.effect == 'speed_up':
                    ball.dx *= 1.5
                    ball.dy *= 1.5
                elif item.effect == 'slow_down':
                    ball.dx *= 0.5
                    ball.dy *= 0.5
                items.remove(item)
                break

    pygame.display.flip()
    clock.tick(60)

pygame.quit()

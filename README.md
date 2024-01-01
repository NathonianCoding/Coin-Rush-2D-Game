import sys,pygame,random,math
import pygame.event as GAME_EVENTS
import pygame.locals as GAME_GLOBALS
import pygame.time as GAME_TIME


class Box:
    
  
  x=250
  y=250
  width=50
  height=10

  @classmethod
  def moveLeft(cls):
    if cls.x-10>=0: # manages bounds
      cls.x-=10
  @classmethod
  def moveRight(cls):
    if cls.x+10<=500: # manages bounds
      cls.x+=10


  
   
class Coin:
    colours=[(255,255,0),(255,0,0)]
    

    def __init__(self,x,y):
      self.x=x
      self.y=y

      self.colour=random.choice(self.colours)
    
      self.radius=random.randint(5,20)
      self.speed=5
      
    def descend(self):
        self.y+=self.speed

    
    def draw(self):
        pygame.draw.circle(surface, self.colour,(self.x,self.y),self.radius,0)
       

    
    def isCollected(cls,box):
        if cls.y+cls.radius>=box.y and cls.y-cls.radius<=box.y:
            if box.x<=cls.x<=box.x+box.width:
                return True
    def point(self):
      return 1 if self.colour==self.colours[0] else -1
    
        
        
        

# Main code body


    
pygame.init()
surface=pygame.display.set_mode((500,500))
box=Box()
leftDown=False
rightDown=False
dragging=False
coins=[]
lastCreated=0
interval=0.5
score='0'

pygame.font.init()
font=pygame.font.SysFont('Comic Sans MS', 30)
font2=pygame.font.SysFont('Comic Sans MS', 50)
                    
coin_sfx=pygame.mixer.Sound("mixkit-winning-a-coin-video-game-2069.wav")



# Computes descending coins 
def handleCoins():
  
    global score,coin_sfx
    coinsToRemove=[]
    for idx in range(len(coins)):
        coins[idx].draw()
        coins[idx].descend()
        if coins[idx].isCollected(box):
            coinsToRemove.append(idx)
            score=str(int(score) + coins[idx].point())
            if coins[idx].point()==1:
              coin_sfx.play()
        elif coins[idx].y>500:
          coinsToRemove.append(idx)
          
    for i in coinsToRemove:
        coins.pop(i)
            
def moveBox():
    global leftDown,rightDown

    if leftDown:
      box.moveLeft()

    if rightDown:
      box.moveRight()




  
pi=math.pi
  
while True:

  
  Endscreen=font.render("Your score is:", False, (0,0,0))
  scoreDisplay=font.render(score, False, (255,0,0))
  
  timeLeft=30-GAME_TIME.get_ticks()//1000
  if timeLeft>10:
    time_colour=(0,255,50)
  else:
    time_colour=(255,0,0)
  timeDisplay=font.render(str(timeLeft),False, time_colour)
    

  
  
  surface.fill((0,0,0))
  surface.blit(scoreDisplay, (5,0))
  surface.blit(timeDisplay, (305,50))
  handleCoins()

  moveBox()


      
  
    
    
  for event in GAME_EVENTS.get():
    if event.type==pygame.KEYDOWN:
        if event.key==pygame.K_LEFT:
            leftDown=True
        if event.key==pygame.K_RIGHT:
            rightDown=True
            
    if event.type==pygame.KEYUP:
        if event.key==pygame.K_LEFT:
            leftDown=False
        if event.key==pygame.K_RIGHT:
            rightDown=False
        
    if event.type==GAME_GLOBALS.QUIT:
      sys.exit()

  if (GAME_TIME.get_ticks()-lastCreated)/1000>=interval:
      coins.append(Coin(random.randint(0,500),0))
      lastCreated=GAME_TIME.get_ticks()


  if GAME_TIME.get_ticks()/1000>31:
    surface.fill((65,105,225))
    surface.blit(Endscreen, (80,250))
    surface.blit(scoreDisplay, (280,250))
    pygame.display.update()
    sys.exit()

    
  pygame.draw.rect(surface, (255,79,80), (box.x,box.y,box.width,box.height))
  pygame.time.Clock().tick(60)
  
  
  pygame.draw.arc(surface, (255,255,255), (300,50,50,50),(2*pi)*GAME_TIME.get_ticks()/1000/30,0,5)

  pygame.display.update()


import pygame
import random
import time
from tkinter import *
from functools import partial
#Initialise all values
height=800
width=600
blck=(0,0,0)
yellow=(240,240,27)
red=(255,0,0)
white=(255,255,255)
grey=(160,160,160)
green=(0,255,0)
pygame.init()
pygame.mixer.init()
game=pygame.display.set_mode((height,width))
pygame.display.set_caption("Speedy Cars")   
car=pygame.image.load("carImg.png")
ptime=pygame.time.Clock()
count=0
f=pygame.font.SysFont("calibri", 25, bold=True, italic=False)
myfont=pygame.font.SysFont("calibri", 70, bold=True, italic=False)
#PRESSED NO WHEN ASKED WANA PLAY AGAIN   
def gExit(r):
    r.destroy()
    pygame.quit()
    quit()
#PRESSED YES WHEN ASKED WANA PLAY AGAIN
def presYes(r):
    r.destroy()
    start_game()

#carDISplayed    
def carDisplay(x,y):
   game.blit(car,(x,y))
   
   
#CRASH TEXT and POP_UP WANA PLAY AGAIN WINDOW   
def crashed(count):
    pygame.font.init()
    
    text=myfont.render('YOU CRASHED',True,yellow)
    textsurface = myfont.render('GAME OVER!!!',False, yellow)
    game.blit(text,(200,120))
    game.blit(textsurface,(200,200))
    pygame.display.update()
    time.sleep(1)
    
    r=Tk()
    r.title("Confirm")
    r.configure(background="yellow")
    hL=Label(r,text="Wana play again?",font="Arial 40 bold",bg="yellow")
    ScoreL=Label(r,text="Final Score:%s"%(count),font="Arial 30 bold", bg="yellow")
    ScoreL.pack()
    hL.pack()
    yes=Button(r,text="yes", width='30', command=partial(presYes,r))
    yes.pack(side=LEFT)
    no=Button(r,text="no" ,width='30',command=partial(gExit,r))
    no.pack()
    r.mainloop()
    
        

def obstacle(x,y,Obwidth,ObHeight):
    pygame.draw.rect(game,red,(x,y,Obwidth,ObHeight))
    pygame.display.update()
   
#count object dodged    
def obCount(count):
    pygame.draw.rect(game,green,(0,0,95,800))
    coText=f.render("Score:%s"%count,True,blck)
    game.blit(coText,(0,20))
    pygame.draw.rect(game,green,(706,0,95,800))
    

    
#main game logic    
def start_game():
    pygame.mixer.music.load("racing1.mp3")
    pygame.mixer.music.play(12)
    height=800
    width=600
    x=width*0.60
    y=height*0.55
    x_change=0
    xObstacle=random.randrange(120,width)
    yObstacle=-600
    speed=7
    Obwidth=100
    ObHeight=100
    car_width=66
    car_height=157
    count=0
    closing=False
    while not closing:
        crashMusic=pygame.mixer.Sound("Crash.WAV")
        for ev in pygame.event.get():
            if ev.type==pygame.QUIT:
                pygame.quit()
                quit()
            if ev.type==pygame.KEYDOWN:
                if ev.key==pygame.K_LEFT:
                    x_change=-5
            
                if ev.key==pygame.K_RIGHT:
                    x_change=5
                
            if ev.type==pygame.KEYUP:
                 if ev.key==pygame.K_RIGHT or ev.key==pygame.K_LEFT:
                     x_change=0
            
        
        x+=x_change
        game.fill(grey)
        pygame.draw.line(game,yellow,(100,0),(100,800),10)
        pygame.draw.line(game,yellow,(700,0),(700,800),10)
        carDisplay(x,y)
        obCount(count)
        obstacle(xObstacle,yObstacle,Obwidth,ObHeight)
        yObstacle+=speed
        speed+=0.001
        obCount(count)
        if x<113 or x>627:
            pygame.mixer.music.stop()
            crashMusic.play()
            crashed(count)    
       
        if yObstacle>height:
             yObstacle=0-ObHeight
             xObstacle=random.randrange(120,width)
             count=count+1
        if yObstacle+ObHeight>=y:
            if x>=xObstacle and x<xObstacle+Obwidth or x+car_width>xObstacle and x+car_width<xObstacle+Obwidth:
                pygame.mixer.music.stop()
                crashMusic.play()
                crashed(count)     
             
        pygame.display.update()
        ptime.tick(60)
        
# Intial window calls this function when PLAY button is pressed       
def play_game(root):
    root.destroy()
    start_game()

#Intial window    
root=Tk()
root.title("Need For Speed")
root.configure(background="red")
hL=Label(root,text="SPEEDY CARS \n Lets !!! PLAY THIS GAME",font="Arial 40 bold",bg="red")
hL.pack()
play=Button(root,text="PLAY", width='30',activebackground="green", command=partial(play_game,root))
play.pack()
ex=Button(root,text="EXIT" ,width='30',activebackground="yellow",command= root.destroy)
ex.pack()
root.mainloop()
pygame.quit()
quit()


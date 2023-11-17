# Drawing-app

#import

import sys
import pygame
import ctypes

#increasing DPI to make screen sharper
ctypes.windll.shcore.SetProcessDpiAwareness(True)

#pygame config
pygame.init()
fps = 300
fpsClock = pygame.time.Clock()
width, height = 1280, 720
screen = pygame.display.set_mode((width, height), pygame.RESIZABLE)

font = pygame.font.SysFont('Arial', 20)


#############################################################
#                         VARIABLES
#############################################################

#Dur buttons will append themselves to this list
objects = []


# initial colour

#            R   G  B

drawColor = [0, 0, 0]

# Inital brush size
brushSize = 10
brushSizeSteps = 3


#drawing area size
canvasSize = [800, 800]


#Button Class

class Button():
    def __init__(self, x, y, width, height, buttonText='Button', onclickFunction=None, onePress=False):
        self.x = x
        self.y = y
        self.width = width
        self.height = height
        self.onclickFunction = onclickFunction
        self.onePress = onePress

        self.fillColours = {
            'normal': '#ffffff',
            'hover': '#e37e0e',
            'pressed': '#333333',

        }

        self.buttonSurface = pygame.Surface((self.width, self.height))
        self.buttonRect = pygame.Rect(self.x, self.y, self.width, self.height)

        self.buttonSurf = font.render(buttonText, True, (20, 20, 20))

        self.alreadyPressed = False

        objects.append(self)

    def process(self):

        mousePos = pygame.mouse.get_pos()

        self.buttonSurface.fill(self.fillColors['normal'])
        if self.buttonRect.collidepoint(mousePos):
            self.buttonSurface.fill(self.fillColors['hover'])

            if pygame.mouse.get_pressed(num_buttons=3)[0]:
                self.buttonSurface.fill(self.fillColors['pressed'])

            elif not self.alreadyPressed:
                self.onclickFunction()
                self.alreadyPressed = True

            else:
                self.alreadyPressed = False

        self.buttonSurface.blit(self.buttonSurf, [
            self.buttonRect.width/2 - self.buttonSurf.get_rect().width/2,
            self.buttonRect.height/2 - self.buttonSurf.get_rect().height/2
        ])
        screen.blit(self.buttonSurface, self.buttonRect)

####################################################################
# ------------------------------------------------------------------
#                           Handler Functions                      #
####################################################################

#changing colur
def changeColor(color):
    global brushSize
    drawColor = color

# change brush size

def changebrushSize(dir):
    global brushSize
    if dir == 'greater':
        brushSize += brushSizeSteps
    else:
        brushSize -= brushSizeSteps

# save the surface on disk

def save(canvas=None):
    pygame.image.save(canvas, "canvas.png")

 #button Variables

buttonWidth = 120
buttonHeight = 35

#buttons

# lambda is used to store multiple parameters

button = [
    ['Black', lambda: changeColor([0, 0, 0])],
    ['White', lambda: changeColor([255, 255, 255,])],
    ['Red', lambda: changeColor([255, 0, 0])],
    ['Green', lambda: changeColor([0, 255, 0])],
    ['Blue', lambda: changeColor([0, 0, 255])],
    ['Brush Large', lambda: changebrushSize('greater')],
    ['Brush Smaller', lambda: changebrushSize('smaller')],
    ['Save', save],

]

for index, buttonName in enumerate(button):
    Button(index * (buttonWidth + 10) + 10, 10, buttonWidth, buttonHeight, buttonName[0], buttonName[1])

#canvas

canvas = pygame.surface
canvas.fill(255, 255, 255)

#game loop
while True:
    screen.fill((30, 30, 30,))
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            pygame.quit()
            sys.exit()


    # draw loop
    for object in objects:
        object.process()

        #draw canvas to middle of the screen

        x, y = screen.get_size()
        screen.blit(canvas, [x/2 - canvasSize[0]/2, y/2 - canvasSize[1]/2])

        #drawing with the mouse

        if pygame.mouse.get_pressed()[0]:
            mx, my = pygame.get_pos()

            #calc position of mouse

            dx = mx - x/2 + canvasSize[0]/2
            dy = my - y/2 + canvasSize[1]/2

            pygame.draw.circle(
                canvas,
                drawColor,
                [dx, dy],
                brushSize,
            )

            #refernece dot
            pygame.draw.circle(
                screen,
                drawColor,
                [100, 100],
                brushSize,
            )

            pygame.display.flip()
            fpsClock.tick(fps)

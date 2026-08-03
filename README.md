# Simple-Multiplayer-Game-code
This is a two-player game. There will be a square and a triangle on the screen. One player can change the position of the triangle with a small delay by simply left-clicking anywhere on the screen. The other player can move the square by using keyboard. The goal of the game is to catch the triangle.


#include<stdio.h>
#include<GL/glut.h>
#include<math.h>
#include<string.h>

float xpos1 = 260, ypos1 = 260;
float xpos2 = 260, ypos2 = 220;
float xpos3 = 220, ypos3 = 220;
float xpos4 = 220, ypos4 = 260;

float deltaX = 20.0;
float deltaY = 20.0;

int anti_clockwise = -15;
int clockwise = 15;

float triX = 100, triY = 100;
float triSize = 15.0f;

int lastClickTime = 0;
int delayTime = 1000;
int gameOver = 0;

float degreesToRadians(float angle){
    return angle*(M_PI/180.0f);
}

void drawText(float x, float y, char *string) {
    glRasterPos2f(x, y);
    int len = strlen(string);
    for (int i = 0; i < len; i++) {
        glutBitmapCharacter(GLUT_BITMAP_HELVETICA_18, string[i]);
    }
}

void checkCollision() {
    float centerX = (xpos1 + xpos3) / 2.0f;
    float centerY = (ypos1 + ypos3) / 2.0f;

    float dist = sqrt((centerX - triX)*(centerX - triX) + (centerY - triY)*(centerY - triY));

    if (dist < 25.0f) {
        gameOver = 1;
    }
}

void display(){
    glClear(GL_COLOR_BUFFER_BIT);
    glLoadIdentity();

    glColor3f(0.0f, 0.8f, 1.0f);
    glBegin(GL_POLYGON);
        glVertex2f(xpos1, ypos1);
        glVertex2f(xpos2, ypos2);
        glVertex2f(xpos3, ypos3);
        glVertex2f(xpos4, ypos4);
    glEnd();

    glColor3f(1.0f, 0.84f, 0.0f);
    glBegin(GL_TRIANGLES);
        glVertex2f(triX, triY + triSize);
        glVertex2f(triX - triSize, triY - triSize);
        glVertex2f(triX + triSize, triY - triSize);
    glEnd();

    if (gameOver) {
        glColor3f(1.0f, 0.2f, 0.2f);
        drawText(150, 260, "GAME OVER - SQUARE WINS!");
    }

    glutSwapBuffers();
}

void customization(){
    glClearColor(0.0, 0.0, 0.0, 1.0);
}

void reshape(int w, int h){
    glMatrixMode(GL_PROJECTION);
    glLoadIdentity();
    gluOrtho2D(0, 500, 0, 500);

    glMatrixMode(GL_MODELVIEW);
}

void keyboardfunc(unsigned char key, int x, int y){
    if (gameOver) return;

    switch (key){
    case 'w':
        ypos1 += deltaY; ypos2 += deltaY;
        ypos3 += deltaY; ypos4 += deltaY;
        break;

    case 's':
        ypos1 -= deltaY; ypos2 -= deltaY;
        ypos3 -= deltaY; ypos4 -= deltaY;
        break;

    case 'a':
        xpos1 -= deltaX; xpos2 -= deltaX;
        xpos3 -= deltaX; xpos4 -= deltaX;
        break;

    case 'd':
        xpos1 += deltaX; xpos2 += deltaX;
        xpos3 += deltaX; xpos4 += deltaX;
        break;

    case 'f':{
        float centerX =( xpos1+xpos3) / 2.0f;
        float centerY =( ypos1+ypos3) / 2.0f;

        float angle = degreesToRadians(anti_clockwise);
        float temp;

        temp = xpos1 - centerX;
        xpos1= centerX + (temp *cos(angle) - (ypos1- centerY)*sin(angle));
        ypos1= centerY + (temp *sin(angle) + (ypos1- centerY)*cos(angle));

        temp = xpos2 - centerX;
        xpos2= centerX + (temp *cos(angle) - (ypos2- centerY)*sin(angle));
        ypos2= centerY + (temp *sin(angle) + (ypos2- centerY)*cos(angle));

        temp = xpos3 - centerX;
        xpos3= centerX + (temp *cos(angle) - (ypos3- centerY)*sin(angle));
        ypos3= centerY + (temp *sin(angle) + (ypos3- centerY)*cos(angle));

        temp = xpos4- centerX;
        xpos4= centerX + (temp *cos(angle) - (ypos4- centerY)*sin(angle));
        ypos4= centerY + (temp *sin(angle) + (ypos4- centerY)*cos(angle));

        break;
    }

    case 'g' : {
        float centerX =( xpos1+xpos3) / 2.0f;
        float centerY =( ypos1+ypos3) / 2.0f;

        float angle = degreesToRadians(clockwise);
        float temp;

        temp = xpos1 - centerX;
        xpos1= centerX + (temp *cos(angle) - (ypos1- centerY)*sin(angle));
        ypos1= centerY + (temp *sin(angle) + (ypos1- centerY)*cos(angle));

        temp = xpos2 - centerX;
        xpos2= centerX + (temp *cos(angle) - (ypos2- centerY)*sin(angle));
        ypos2= centerY + (temp *sin(angle) + (ypos2- centerY)*cos(angle));

        temp = xpos3 - centerX;
        xpos3= centerX + (temp *cos(angle) - (ypos3- centerY)*sin(angle));
        ypos3= centerY + (temp *sin(angle) + (ypos3- centerY)*cos(angle));

        temp = xpos4- centerX;
        xpos4= centerX + (temp *cos(angle) - (ypos4- centerY)*sin(angle));
        ypos4= centerY + (temp *sin(angle) + (ypos4- centerY)*cos(angle));

        break;
    }
    }

    checkCollision();
    glutPostRedisplay();
}

void onMouseClick(int button, int state_click, int x, int y) {
    if (gameOver) return;

    if(button == GLUT_LEFT_BUTTON && state_click == GLUT_DOWN){
        int currentTime = glutGet(GLUT_ELAPSED_TIME);

        // Check if 2 seconds have passed since last click
        if (currentTime - lastClickTime >= delayTime) {
            triX = x;
            triY = 500 - y; // Fixed GLUT Y-axis coordinate inversion

            lastClickTime = currentTime;

            checkCollision();
            glutPostRedisplay();
        }
    }
}

int main (int argc,char **argv)
{
    glutInit(&argc,argv);
    glutInitDisplayMode(GLUT_RGB | GLUT_DOUBLE);

    glutInitWindowPosition(100,50);
    glutInitWindowSize(500,500);
    glutCreateWindow("Square Catch Triangle Game");
    glutDisplayFunc(display);
    glutReshapeFunc(reshape);
    glutKeyboardFunc(keyboardfunc);
    glutMouseFunc(onMouseClick);

    customization();
    glutMainLoop();
    return 0;
}

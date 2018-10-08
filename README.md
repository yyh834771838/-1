#include <stdio.h>
#include <stdlib.h>
#include <conio.h>
#include<windows.h>
#include<time.h>
 

#define UP 0
#define DOWN 1
#define LEFT 2
#define RIGHT 3


#define Height 30 
#define Width 80


int a[Height][Width];        
int s[Height*Width][2];      
int Length;       
int dir;      
int eated=0;        
int score=0; 
     
void init()       
{
    srand((unsigned)time(NULL));            
    CONSOLE_CURSOR_INFO cursor_info = {1, 0};
    SetConsoleCursorInfo(GetStdHandle(STD_OUTPUT_HANDLE), &cursor_info);  
    int i,j;
    for(i=0; i<Height; i++)
    {
        a[i][0]=1;         
        a[i][Width-1]=1;       
    }
    for(j=0; j<Width; j++)
    {
        a[0][j]=1;     
        a[Height-1][j]=1;    
    }
    
    Length=4;        
    s[0][0]=Height/2;
    s[0][1]=Width/2;     
    for(i=1; i<4; i++)
    {
        s[i][0]=s[0][0]+i;
        s[i][1]=s[0][1]; 
    }
    dir=UP;
}

void gotoxy(int i,int j)        
{
    COORD position= {j,i};
    SetConsoleCursorPosition(GetStdHandle(STD_OUTPUT_HANDLE),position);
}



int check(int X,int Y)       
{
    if(a[X][Y]==1)        
        return 0;
    int i;
    for(i=0; i<Length; i++)
    {
        if(X==s[i][0]&&Y==s[i][1])      
            return 0;
    }
    if(X==0||X==Height-1||Y==0||Y==Width-1)      
        return 0;
    return 1;                       
}

void obstacle()
    {

     int i,j;
    do
    {
        i=rand()%Height;         
        j=rand()%Width;
    }  while(check(i,j)==0); 
   a[i][j]=-3;                    
    gotoxy(i,j);
    printf("*");
}
	
	
void food()
{
    int i,j;
    do
    {
        i=rand()%Height;         
        j=rand()%Width;
    }
    while(check(i,j)==0);           
    a[i][j]=-1;                    
    gotoxy(i,j);
    printf("§");                   
}


void poison()
{
    int i,j;
    do
    {
        i=rand()%Height;                
        j=rand()%Width;
    }
    while(check(i,j)==0);          
    a[i][j]=-2;                     
    gotoxy(i,j);
    printf("&");                  
}



void Map()                  
{
    gotoxy(0,0);
    int i,j;
    for(i=0; i<Height; i++)
    {
        for(j=0; j<Width; j++)         
        {
            if(a[i][j]==0)        
                printf(" ");
            else                 
                printf("*");
        }
        printf("\n");             
    }
}

void move()
{
    int i;
    gotoxy(s[Length-1][0],s[Length-1][1]);
    printf(" ");                           
    if(eated==-1)          
    {
        Length++;
        eated=0;        
    }
     if(eated==-2)               
    {
        Length--;
        gotoxy(s[Length-1][0],s[Length-1][1]);
        printf(" "); 
        eated=0;        
    }
    for(i=Length-1; i>0; i--)  
    {
        s[i][0]=s[i-1][0];
        s[i][1]=s[i-1][1];
    }
    switch(dir)
    {
    case UP:
        s[0][0]--;
        break;
    case DOWN:
        s[0][0]++;
        break;
    case LEFT:
        s[0][1]--;
        break;
    case RIGHT:
        s[0][1]++;
        break;
    }

}

void Snake()               
{
    int i;
     gotoxy(s[0][0],s[0][1]);  
     printf("@");
    for(i=1; i<Length; i++)
    {
        gotoxy(s[i][0],s[i][1]);        
        printf("O");                    
    }
}

void key()
{
    if(kbhit()!=0)          
    {
        char in;
        while(!kbhit()==0)  
            in=getch();
        switch(in)
        {
        case 'w':
        case 'W':
            if(dir!=DOWN)         
                dir=UP;
            break;
        case 's':
        case 'S':
            if(dir!=UP)
            dir=DOWN;
            break;
        case 'a':
        case 'A':
            if(dir!=RIGHT)
                dir=LEFT;
            break;
        case 'd':
        case 'D':
            if(dir!=LEFT)
                dir=RIGHT;
            break;
        case 'p':
        case 'P':
            gotoxy(Height,0);       
            system("pause");
            gotoxy(Height,0);
            printf("                   "); 
            break;
        }
    }
}

bool gameOver()
{
    bool isGameOver=false;
    int sX=s[0][0],sY=s[0][1];      
    if(sX==0||sX==Height-1||sY==0||sY==Width-1)
        isGameOver=true;
    for(int i=1; i<Length; i++)    
    {
        if(s[i][0]==sX&&s[i][1]==sY)
            isGameOver=true;
    }
     if(a[s[0][0]][s[0][1]]==-3)
        isGameOver=true;
    return isGameOver;
}

void printScore()
{
    gotoxy(0,Width+2);
    printf("  贪吃蛇小游戏");
    gotoxy(1,Width+2);
    printf("食物：§;毒药：&;障碍物：*");
    gotoxy(2,Width+2);
    printf("请在游戏开始时切换成英文输入或开启大写");
    gotoxy(4,Width+2);
    printf("  得分:%d",score);
}


int difficult()
{    int Time;
    if(kbhit()!=0)          
    {
        char in;
        while(!kbhit()==0)      in=getch();
        switch(in)
        {
          case 'A':   
		  Time=100;
          break;
          case 'B':   
		  Time=200; 
          break;
          case 'C':   
		  Time=300;
          break;	
          case 'D':   
		  Time=400;
          break;
          case 'E':  
		  Time=500;
          break;
        }	
   
	}
	return Time;
}

  

int main()
{
    init(); 	         
    Map();                
    food();
    poison(); 
    while(1)
    {
    
        Snake();               
        printScore();
        Sleep(250);         
        key();
        move();                
        if(gameOver())
        {
            system("cls");         
            printf("Game Over\n");
            printf("你的最终得分:%d\n",score);
            system("pause");
            break;
        }
        if(a[s[0][0]][s[0][1]]==-1) 
        {
            eated=-1;             
            score+=10;
            food();
            poison();
            obstacle();
            a[s[0][0]][s[0][1]]=0;  
        }
        if(a[s[0][0]][s[0][1]]==-2) 
        {
            eated=-2;             
            score-=10;
            food(); 
            poison();
            obstacle();
            a[s[0][0]][s[0][1]]=0;  
        }
        
    }
    return 0;
}

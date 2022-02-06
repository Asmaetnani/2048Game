# Final project:2048 Game
## Introduction
The goal of this project is to create the 2048 Game using what we learned in all the C++ GUI Programming with QT lectures .
As the last chapter we have seen in this semester was QPainter we have choosen to realise our final project using the QPainter functionalities.QPainter provides highly optimized functions to do most of the drawing GUI programs require. It can draw everything from simple lines to complex shapes like pies and chords and in our case the overview of the game which is the table of bricks with the dimension 4 * 4.
## How is the game played
A game of 2048 is played on a 4×4 board.When we start, the board will have a tile "2" in random location, and after making a move (up, down, left, or right) any tiles with the same value that are adjacent to each other and are moving together  will merge and end up with a new tile equal to the sum of the earlier two.
## Programming part
First of all our project is a QT widget application that inherits from the class QWidget.   

We are going to start by programming the main.cpp to set the size of the mainwindow ,its title and the colour of the background.
### main.cpp
```cpp
#include "game2048.h"
#include <QApplication>

int main(int argc, char *argv[])
{
    QApplication a(argc, argv);

    Game2048 w;
    w.setFixedSize(550,750);
    w.setWindowTitle("2048");
    w.setStyleSheet("* { background-color: black; }");
    w.show();
    return a.exec();
}

```
Now in the header we are going to specify the variables we are going to use ,the functions and the slots:  
*  Void Up(): to control the up move.
*  Void Down():to control the down move.
*  Void Left():to control the left move.
        
*  Void Right():to control the right move.
      
* Void Judge(): to find the empty bricks and fill them and also the judge if the game ends. 
      
* Void keyPressEvent(QKeyEvent *event):to control the keyboard so that when we press a specific letter we can perform an action.
* Void start(): the slot to start the game.
### the code for game2048.h
```cpp
#ifndef GAME2048_H
#define GAME2048_H

#include <QWidget>
#include <QKeyEvent>
#include <QPushButton>
#include <QPainter>
#include <QTime>

class Game2048 : public QWidget
{
    Q_OBJECT

public:
    explicit Game2048(QWidget *parent = nullptr);
    ~Game2048();
    void paintEvent(QPaintEvent *);


    void Up();
    void Down();
    void Left();
    void Right();
    void Judge();

    QPushButton *button;
    int table[4][4];
    int score;
    bool status;

    struct V{
        int i;
        int j;
    };

protected:
      void keyPressEvent(QKeyEvent *event);
signals:

public slots:
    void start();

};
#endif // GAME2048_H
```
## Now it's time to move to the game2048.cpp file for the implementation.
### Main constructor
As a starter in the constructor we will initialize the score with 0 so that each time the game begins the score will start with the value 0. Then we are going to use a loop to generate the table of bricks , and we are going to add a QPushButton that will be responsible for starting the game,we will set different parameters for this button to style it such as the font,the dimensions and the background color.Finally we are going to connect this button with the start slot.
```cpp
Game2048::Game2048(QWidget *parent)
    : QWidget(parent),score(0),status(false)
{  for(int i=0;i<4;i++)
        for(int j=0;j<4;j++)
            table[i][j]=0;

    button = new QPushButton("New game",this);
    button->setGeometry(180,650,200,70);
    button->setStyleSheet("QPushButton{ background-color: Darkcyan;}");
    button->setFont(QFont("Helvetica [Cronyx]",30,700,false));
    connect(button,SIGNAL(clicked()),this,SLOT(start()));
}
```
### Implementation of the start slot
This slot is responsible for launching the game .First we are going to show a QMessageBox telling the user the game rules and then we are going to choose a random position  for the starting brick using qrand function,the value written in this brick will be 2 . After the game starts the text in the QPushbutton will become "Replay" allowing the player to start the game again.  
```cpp
void Game2048::start()
{   QMessageBox msgbox2;
    msgbox2.setWindowTitle("GAME RULES");
    msgbox2.setText("Use U to move up,D for down,L for left and R for right.");
    msgbox2.setStyleSheet("* { background-color: white; }");
    msgbox2.setDefaultButton(QMessageBox::Ok);
    msgbox2.exec();
    score = 0;
    for(int i=0;i<4;i++)
        for(int j=0;j<4;j++)
            table[i][j]=0;
    button->setText("Replay");

    int randi = qrand() % 4;
    int randj = qrand() % 4;
    table[randi][randj] = 2;

    status = true;
    update();

}
```
### Implementation of the moves
The player can move in four directions left,right,up and down . To perform this actions we first need to make sure that there's enough space in front to move to, once this condition have been satisfied the action can be done. We are going to use different loops to check the border of our table and execute the moves.
```cpp
void Game2048::Up()
{
    
    for(int i=0;i<4;i++)
         for(int j=1;j<4;j++){
             if(table[i][j] == 0){
                 continue;
             }
            for(int r=0;r<j;r++){
                if(table[i][r] == 0){
                    table[i][r] = table[i][j];
                   table[i][j] = 0;
                    break;
                }
            }
         }
    
    for(int i=0;i<4;i++)
         for(int j=0;j<3;j++){
             if(table[i][j] == table[i][j+1]){
                 table[i][j] = table[i][j]*2;
                 table[i][j+1] = 0;
                 score += table[i][j];
                 for(int r=j+2;r<4;p++)
                    table[i][r-1] = table[i][r];
             }

         }
}
```
```cpp
void Game2048::Down()
{

    for(int i=0;i<4;i++)
         for(int j=2;j>=0;j--){
             if(table[i][j] == 0){
                 continue;
             }
            for(int r=3;r>j;r--){
                if(table[i][r] == 0){
                    table[i][r] = table[i][j];
                    table[i][j] = 0;
                    break;
                }
            }
         }

    for(int i=0;i<4;i++)
         for(int j=3;j>0;j--){
             if(table[i][j] == table[i][j-1]){
                 table[i][j] = table[i][j]*2;
                 table[i][j-1] = 0;
                 score += table[i][j];
                 for(int r=j-2;r>=0;r--)
                     table[i][r+1] =table[i][r];
             }

         }
}
```
```cpp
void Game2048::Left()
{

    for(int j=0;j<4;j++)
         for(int i=1;i<4;i++){
             if(table[i][j] == 0){
                 continue;
             }
            for(int r=0;r<i;r++){
                if(table[r][j] == 0){
                   table[r][j] = table[i][j];
                    table[i][j] = 0;
                    break;
                }
            }
         }

    for(int j=0;j<4;j++)
         for(int i=0;i<3;i++){
             if(table[i][j] == table[i+1][j]){
                 table[i][j] = table[i][j]*2;
                 table[i+1][j] = 0;
                 score += table[i][j];
                 for(int r=i+2;r<4;r++)
                     table[r-1][j] = table[r][j];
             }

         }

}
```
```cpp
void Game2048::Right()
{
    
    for(int j=0;j<4;j++)
         for(int i=2;i>=0;i--){
             if(table[i][j] == 0){
                 continue;
             }
            for(int r=3;r>i;r--){
                if(table[r][j] == 0){
                    table[r][j] = table[i][j];
                    table[i][j] = 0;
                    break;
                }
            }
         }
    
    for(int j=0;j<4;j++)
         for(int i=3;i>0;i--){
             if(table[i][j] == table[i-1][j]){
                 table[i][j] = table[i][j]*2;
                table[i-1][j] = 0;
                 score += table[i][j];
                 for(int r=i-2;r>=0;r--)
                     table[r+1][j] = table[r][j];
             }

         }

}
```
### Implementation of the keyPressEvent
Now we are going to link this moves to the keyborad as following : the key U with Up function, the key D with Down function, the key L with Left function and the key R with Right function . We are also going to ignore any other different press.
```cpp
void Game2048::keyPressEvent(QKeyEvent *event)
{
    if(!status)
        return;

   switch(event->key())
   {
    case Qt::Key_U:
       Up();
         break;
    case Qt::Key_D:
       Down();
         break;
    case Qt::Key_L:
       Left();
         break;
    case Qt::Key_R:
       Right();
         break;

    default:return;
   }

   Judge();
   update();

}
```
### Implementation of the paintEvent
In this part we will draw the form of the table of bricks and specify the color for each number as well as the font of the writing. To draw each brick we will use the function drawRect(int x, int y, int width, int height),to choose the background color we will use  the function setBrush(Qt::BrushStyle style) and to write inside that rectangle we will us the drawText(const QRectF &rectangle, const QString &text, const QTextOption &option = QTextOption()) function .

```cpp
void Game2048::paintEvent(QPaintEvent *)
{
    QPainter p(this);
    QPen pen(Qt::darkCyan);
    p.setPen(pen);
    p.setFont(QFont("Helvetica [Cronyx]",40,700,false));
    p.drawText(QPoint(170,140),"SCORE:"+QString::number(score));

    for(int i=0;i<4;i++)
        for(int j=0;j<4;j++){
            p.setPen(Qt::transparent);
            if(table[i][j] == 0){
                p.setBrush(Qt::gray);
                p.drawRect(i*100+80,j*100+200,95,95);
            }
            else if(table[i][j] == 2){
                p.setBrush(Qt::darkCyan);
                p.drawRect(i*100+80,j*100+200,95,95);
                p.setPen(Qt::black);
                p.setFont(QFont("Helvetica [Cronyx]",50,700,false));
                p.drawText(QRectF(i*100+80,j*100+200,95,95),QString::number(2),QTextOption(Qt::AlignCenter));
            }
            else if(table[i][j] == 4){
                p.setBrush(Qt::darkYellow);
                p.drawRect(i*100+80,j*100+200,95,95);
                p.setPen(Qt::black);
                p.setFont(QFont("Helvetica [Cronyx]",50,700,false));
                p.drawText(QRectF(i*100+80,j*100+200,95,95),QString::number(4),QTextOption(Qt::AlignCenter));            }
            else if(table[i][j] == 8){
                p.setBrush(Qt::blue);
                p.drawRect(i*100+80,j*100+200,95,95);
                p.setPen(Qt::black);
                p.setFont(QFont("Helvetica [Cronyx]",50,700,false));
                p.drawText(QRectF(i*100+80,j*100+200,95,95),QString::number(8),QTextOption(Qt::AlignCenter));            }
            else if(table[i][j] == 16){
                p.setBrush(Qt::darkRed);
                p.drawRect(i*100+80,j*100+200,95,95);
                p.setPen(Qt::black);
                p.setFont(QFont("Helvetica [Cronyx]",50,700,false));
                p.drawText(QRectF(i*100+80,j*100+200,95,95),QString::number(16),QTextOption(Qt::AlignCenter));            }
            else if(table[i][j] == 32){
                p.setBrush(Qt::red);
                p.drawRect(i*100+80,j*100+200,95,95);
                p.setPen(Qt::black);
                p.setFont(QFont("Helvetica [Cronyx]",50,700,false));
                p.drawText(QRectF(i*100+80,j*100+200,95,95),QString::number(32),QTextOption(Qt::AlignCenter));            }
            else if(table[i][j] == 64){
                p.setBrush(Qt::darkMagenta);
                p.drawRect(i*100+80,j*100+200,95,95);
                p.setPen(Qt::black);
                p.setFont(QFont("Helvetica [Cronyx]",50,700,false));
                p.drawText(QRectF(i*100+80,j*100+200,95,95),QString::number(64),QTextOption(Qt::AlignCenter));            }
            else if(table[i][j] == 128){
                p.setBrush(Qt::magenta);
                p.drawRect(i*100+80,j*100+200,95,95);
                p.setPen(Qt::black);
                p.setFont(QFont("Helvetica [Cronyx]",50,700,false));
                p.drawText(QRectF(i*100+80,j*100+200,95,95),QString::number(128),QTextOption(Qt::AlignCenter));            }
            else if(table[i][j] == 256){
                p.setBrush(Qt::cyan);
                p.drawRect(i*100+80,j*100+200,95,95);
                p.setPen(Qt::black);
                p.setFont(QFont("Helvetica [Cronyx]",50,700,false));
                p.drawText(QRectF(i*100+80,j*100+200,95,95),QString::number(256),QTextOption(Qt::AlignCenter));            }
            else if(table[i][j] == 512){
                p.setBrush(Qt::yellow);
                p.drawRect(i*100+80,j*100+200,95,95);
                p.setPen(Qt::black);
                p.setFont(QFont("Helvetica [Cronyx]",50,700,false));
                p.drawText(QRectF(i*100+80,j*100+200,95,95),QString::number(512),QTextOption(Qt::AlignCenter));            }
            else if(table[i][j] == 1024){
                p.setBrush(Qt::black);
                p.drawRect(i*100+80,j*100+200,100,100);
                p.setPen(Qt::white);
                p.setFont(QFont("Helvetica [Cronyx]",50,700,false));
                p.drawText(QRectF(i*100+80,j*100+200,95,95),QString::number(1024),QTextOption(Qt::AlignCenter));            }
            else if(table[i][j] == 2048){
                p.setBrush(Qt::green);
                p.drawRect(i*100+80,j*100+200,95,95);
                p.setPen(Qt::black);
                p.setFont(QFont("Helvetica [Cronyx]",50,700,false));
                p.drawText(QRectF(i*100+80,j*100+200,95,95),QString::number(2048),QTextOption(Qt::AlignCenter));            }
            else{
                p.setBrush(Qt::darkBlue);
                p.drawRect(i*100+80,j*100+200,95,95);
                p.setPen(Qt::black);
                p.setFont(QFont("Helvetica [Cronyx]",50,700,false));
                p.drawText(QRectF(i*100+80,j*100+200,95,95),QString::number(table[i][j]),QTextOption(Qt::AlignCenter));
            }

        }
}

```
### Implementation of the judge function
We will need a function to judge whether the game should end or no and that's by checking if there is enough space which means if there is still empty bricks if no it will check if there is still a possible move if no a QMessageBox will apear announcing that the game is over and that the player have lost .
```cpp
void Game2048::Judge()
{
    int i,j;

   struct V brick[15];
   int a=0;
   for(i=0;i<4;i++)
       for(j=0;j<4;j++){
           if(table[i][j] == 0)
           {
               brick[a].i=i;
               brick[a].j=j;
               ++a;
           }
       }

   if(a == 0){
       for(i=0;i<4;i++)
           for(j=0;j<3;j++)
               if(table[i][j] == table[i][j+1])
                  return;
       for(j=0;j<4;j++)
            for(i=0;i<3;i++)
                if(table[i][j] ==table[i+1][j])
                   return;
      QMessageBox msgbox;
      msgbox.setWindowTitle("GAME OVER !");
      msgbox.setText("YOU LOST ! TRY YOUR CHANCE AGAIN.");
      msgbox.setStyleSheet("* { background-color: white; }");
      msgbox.setDefaultButton(QMessageBox::Ok);
      msgbox.exec();

      return;
   }

   int rand = qrand() % (a);
   table[brick[rand].i][brick[rand].j] = 2;

}

```
## Results
When we first execute the program this is what our application looks like :




Then after pressing the New game button this is what it shows:



Once you press the Ok button the game starts and its overview is the following:





When you lose this is the message that you get : 


        
 
## Conclusion
  As a summary we had the opportunity to code the game "2048 Game",which is a hugely interesting game to attempt and to solve, it was challenging but with the background accumulated during this semester and some help from the internet we have been able to reach those results .    




#include <iostream>
#include <windows.h>
#include <ctime>
#include <conio.h>

using namespace std;


bool gameOver;
const int width = 20;
const int height = 20;
int x, y, fruitX, fruitY;
int score, max_score;
int speed = 200;
int tailX[100], tailY[100];
int nTail = 0;

enum eDirection { STOP = 0, LEFT, RIGHT, UP, DOWN };
eDirection dir;

void Setup();
void Draw();
void Input();
void Logic();

int main() {

    setlocale(LC_ALL, "RUS");

    char menu;

    cout << "Начать игру? (Y/N):";
    cin >> menu;

    if (menu == 'Y' || menu == 'y') {


        Setup();

        while (!gameOver) {
            Draw();
            Input();
            Logic();
        }

        main();
    }
    else if (menu == 'N' || menu == 'n') {
        system("cls");
    }
    return 0;
}

void Setup() {

    srand(time(NULL));

    gameOver = false;
    dir = STOP;
    x = width / 2 - 1;
    y = height / 2 - 1;

    fruitX = rand() % width;
    fruitY = rand() % height;

    score = 0;
    nTail = 0;

    for (int i = 0; i < 100; i++) {
        tailX[i] = 0;
        tailY[i] = 0;
    }

}

void Draw() {

    Sleep(speed);
    system("cls");

    for (int i = 0; i < width + 1; i++)
        cout << "_";
    cout << endl;

    for (int i = 0; i < height; i++) {
        for (int j = 0; j < width; j++) {

            if (j == 0 || j == width - 1) {
                cout << "|";
            }

            if (i == y && j == x) {
                cout << "O";
            }
            else if (i == fruitY && j == fruitX) {
                cout << "*";
            }
            else {
                bool print = false;
                for (int k = 0; k < nTail; k++) {
                    if (tailX[k] == j && tailY[k] == i) {
                        print = true;
                        cout << "o";
                    }
                }
                if (!print)
                    cout << " ";
            }

        }
        cout << endl;
    }

    for (int i = 0; i < width + 1; i++)
        cout << "_";
    cout << endl;

    cout << " Счёт: " << score << endl;
    cout << " Рекорд： " << max_score << endl;
}

void Input() {

    if (_kbhit()) {
        switch (_getch()) {

        case 'a':
            if (dir != RIGHT) dir = LEFT;
            break;

        case 'd':
            if (dir != LEFT) dir = RIGHT;
            break;

        case 'w':
            if (dir != DOWN) dir = UP;
            break;

        case 's':
            if (dir != UP) dir = DOWN;
            break;

        case 'x':
            gameOver = true;
            break;
        }
    }

}

void Logic() {

    int prevX = tailX[0];
    int prevY = tailY[0];
    int prev2x, prev2y;

    tailX[0] = x;
    tailY[0] = y;

    for (int i = 1; i < nTail; i++) {
        prev2x = tailX[i];
        prev2y = tailY[i];
        tailX[i] = prevX;
        tailY[i] = prevY;
        prevX = prev2x;
        prevY = prev2y;
    }

    switch (dir) {

    case LEFT:
        x--;
        break;

    case RIGHT:
        x++;
        break;

    case UP:
        y--;
        break;

    case DOWN:
        y++;
        break;
    }

    if (x >= width - 1)
        x = 0;
    else if (x < 0)
        x = width - 2;

    if (y >= height)
        y = 0;
    else if (y < 0)
        y = height - 1;


    for (int i = 0; i < nTail; i++) {
        if (tailX[i] == x && tailY[i] == y) {
            gameOver = true;
        }
    }

    if (x == fruitX && y == fruitY) {
        nTail++;
        srand(time(NULL));
        score += 10 + (rand() % 5);
        if (score >= max_score) {
            max_score = score;
        }
        fruitX = rand() % width - 1;
        fruitY = rand() % height;
        cout << "\a";
    }
}

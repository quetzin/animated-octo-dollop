# animated-octo-dollop
//Author: Quetzin Pimentel
//Date: 26 October 2022
//Collaborators: Gentry Atkinson

#include <iostream>
#include <ctime>
#include <cmath>
#include <ctype.h>
#include <iomanip>

using namespace std;

class Robot {
public:
    int X;
    int Y;
    int distance;
    int speed;
    int xValue;
    int yValue;
    string name;
    char lastCommand;
    int currentSpeed;
    Robot() {
        X = 0;
        Y = 0;
        currentSpeed = 1;
        distance = 0;
        lastCommand = 'a';
    }
    void setxValue(int xValue) { this->xValue = xValue; }
    void setyValue(int yValue) { this->yValue = yValue; }
    void setDistance(int distance) { this->distance = distance; }
    void setSpeed(int speed) { this->speed = speed; }
    void setName(string name) { this->name = name; }

    int getDistance() { return distance; }
    int getSpeed() { return speed; }
    int getxValue() { return xValue; }
    int getyValue() { return yValue; }
    string getName() { return name; }
};

//Name: Find Robot
//Parameters:
//  roboList: a list of pointers to robots
//  identifier: name of robot to search for
//  SIZE: size of robotList
//Return: either index of roboList or -1
//Side Effects: None
int findRobot(Robot** rl, string identifier, int SIZE) {
    for (int i = 0; i < SIZE; i++) {
        if (rl[i]->name == identifier) {
            return i;
        }
    }
    return -1;
}

//Name: Move Robot
//Parameters:
//  r: a pointer to a robot
//  d: the direction to move a robot
//Return: none (void)
//Side Effects: the members of r will be updated
void moveRobot(Robot* r, char d) {
    if (toupper(d) == toupper(r->lastCommand)) {
        if (r->currentSpeed < 4)
            r->currentSpeed += 1;
    }
    else {
        r->currentSpeed = 1;
    }
    switch (d) {
    case 'u':
    case 'U':
        r->Y += r->currentSpeed;
        break;

    case 'd':
    case 'D':
        r->Y -= r->currentSpeed;
        break;

    case 'r':
    case 'R':
        r->X += r->currentSpeed;
        break;

    case 'l':
    case 'L':
        r->X -= r->currentSpeed;
        break;

    case 's':
    case 'S':
        if (r->lastCommand == 's' && 'S') {
            cout << "Robot is now started" << endl;
            break;
        }
        else {
            cout << "Robot is now stopped" << endl;
            break;
        }
    default:
        cout << "Invalid Direction" << endl;

        return;
    }
    r->distance += r->currentSpeed;
    r->lastCommand = d;
}

//Name: Make Robo List
//Parameters:
//  numRobos: the size of list to make
//Return: a pointer to a list of robot pointers
// Side Effects: might alter values of robots
Robot** makeRoboList(int numRobos) {
    Robot** rl = new Robot * [numRobos];
    for (int i = 0; i < numRobos; i++) {
        rl[i] = new Robot;
    }
    return rl;
}

//Name: Sort Robo List
//Parameters:
//  rl: an array of robot pointers
//  SIZE: the size of rl
//Returns: None
//Side Effects: rl will list decending by distance
void sortRoboList(Robot** rl, const int SIZE) {
    //Bubble sort
    //Iterate of upperBounds from SIZE -1 to 1
    for (int upperBound = SIZE - 1; upperBound > 0; upperBound--) {
        //Iterate all indexes from 0 to upperBound
        for (int i = 0; i < upperBound; i++) {
            //compare every pair, swap if out of order
            if (rl[i]->distance < rl[i + 1]->distance) {
                //swap
                Robot* temp;
                temp = rl[i];
                rl[i] = rl[i + 1];
                rl[i + 1] = temp;
            }
        }
    }
    return;
}


int main() {
    //Create a list of Robot pointers
    int listSize, roboIndex;
    char userChoice, roboDirection;
    string roboName, newName;
    cout << "How many Robots will you like to track?" << endl;
    cin >> listSize;
    Robot** roboList = makeRoboList(listSize);
    cout << "Please name each robot." << endl;
    for (int i = 0; i < listSize; i++) cin >> roboList[i]->name;
    //Until the user quits
    do {
        //Print menu
        cout << "M- move one robot" << endl
            << "D- print the distance of each robot" << endl
            << "R- rename one robot" << endl
            << "Q- quit the program" << endl;
        //Let user pick menu item
        cin >> userChoice;
        switch (userChoice) {
        case 'm':
        case 'M':

            //if user pick M:
            //  let user type a name
            cin >> roboName;
            //  search a list of robots
            roboIndex = findRobot(roboList, roboName, listSize);
            if (roboIndex != -1) {
                //  let user type a direction, if robot exists
                cout << "U- up or positive y" << endl
                    << "D- down or negative y" << endl
                    << "R- right or positive x" << endl
                    << "L- left or negative x" << endl
                    << "S- stop or start moving" << endl;
                cin >> roboDirection;
                //  move a robot(if it exists)
                moveRobot(roboList[roboIndex], roboDirection);
                cout << roboList[roboIndex]->name << ": ("
                    << roboList[roboIndex]->X << ", "
                    << roboList[roboIndex]->Y << ")" << endl;
            }
            else {
                cout << "Robot does not exist!" << endl;
            }
            break;
            //if user pick D:
        case 'd':
        case 'D':
            sortRoboList(roboList, listSize);
            for (int i = 0; i < listSize; i++) {
                cout << roboList[i]->name << '\t' << roboList[i]->distance
                    << endl;
            }
            //  print all distances(sorted)
            //if user pick R:
        case 'r':
        case 'R':
            cin >> roboName;
            cout << "Please rename Robot" << endl;
            cin >> newName;
            cout << roboName << "'s name is now " << newName << endl;
            roboList[roboIndex]->name = newName;
            break;

            //rename robot
            //if user pick Q:
            //  quit
        case 'q':
        case 'Q':
            break;
        default:
            cout << "Not a valid input for menu." << endl;
        }
    } while (userChoice != 'q' && userChoice != 'Q');
    // say goodbye
    cout << "Thank you for your time!" << endl;
    for (int i = 0; i < listSize; i++) delete[] roboList[i];
    delete[] roboList;

    return 0;
}

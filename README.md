// assignment-2
#include <iostream>
#include <string>
#include <iomanip>
#include <fstream>

using namespace std;

//global constants
const int MAX_ARRAY_SIZE = 18;
const int MIN_ARRAY_SIZE = 8;
const int MAX_PIECES = 72; 
const int NOPLAYER = 0;
const int WHITEWINS = 1;
const int REDWINS = 2;
const int NOONEWINS = 0;
const int WHITESOLDIER = 1;
const int WHITEMULE = 2;
const int WHITEKING = 3;
const int REDSOLDIER = 4;
const int REDMULE = 5;
const int REDKING = 6;
const int WHITEPLAYER = 1;
const int REDPLAYER = 2;

//functions to be used in main
void InitializeBoard(int CMCheckersBoard[MAX_ARRAY_SIZE][MAX_ARRAY_SIZE],
					 int numRowsInBoard );

void DisplayBoard( int CMCheckersBoard[MAX_ARRAY_SIZE][MAX_ARRAY_SIZE], 
				  int numRowsInBoard);

int CountJumps( int CMCheckersBoard[MAX_ARRAY_SIZE][MAX_ARRAY_SIZE], 
			   int numRowsInBoard, int player, int xLocArray[], int yLocArray[] );

bool CheckList( int inArray1[], int inArray2[], int xIndex, int yindex);

int CountMove1Squares( int CMCheckersBoard[MAX_ARRAY_SIZE][MAX_ARRAY_SIZE],
					  int numRowsInBoard, int player, int xLocArray[],  int yLocArray[] );

bool IsMove1Square( int CMCheckersBoard[MAX_ARRAY_SIZE][MAX_ARRAY_SIZE], 
				   int numRowsInBoard, int player, int xLoc, int yLoc);

bool IsJump( int CMCheckersBoard[MAX_ARRAY_SIZE][MAX_ARRAY_SIZE], 
			int numRowsInBoard, int player, int xLoc, int yLoc);

bool MakeMove( int CMCheckersBoard[MAX_ARRAY_SIZE][MAX_ARRAY_SIZE], 
			  int numRowsInBoard, int player, int fromSquareNum, 
			  int toSquareNum, bool &jumped );

bool CheckWin(int CMCheckersBoard[MAX_ARRAY_SIZE][MAX_ARRAY_SIZE], int numRowsInBoard);

int main()
{
	//arrays
	int myCMCheckersBoard[MAX_ARRAY_SIZE][MAX_ARRAY_SIZE] = {0};
	int xIndicesMove[MAX_PIECES] = {0};
	int yIndicesMove[MAX_PIECES] = {0};
	int xIndicesJump[MAX_PIECES] = {0};
	int yIndicesJump[MAX_PIECES] = {0};

	//variables
	int numRowsInBoard = 0;
	int counter = 0;

	cout << "Enter the number of squares along each edge of the board: ";


	while (counter <= 2)
	{
		if (!(cin >> numRowsInBoard))
		{
			cin.clear();
			cin.ignore(1000, '\n');
			cout << "ERROR:  Board size is not an integer" << endl << "8 <= number of squares <= 18";
			cout << endl << "Enter the number of squares along each edge of the board: ";
			counter++;
			if (counter > 2) 
			{
				cout << endl << "ERROR:  Too many errors entering the size of the board ";
				return 1;
			}
			//cin >> numRowsInBoard;
			continue;


		}
		if (numRowsInBoard > 18)
		{
			counter++;
			cout << "ERROR:  Board size too large" << endl << "8 <= number of squares <= 18";
			cout << endl << "Enter the number of squares along each edge of the board: ";
			cin >> numRowsInBoard;
		}
		else if (numRowsInBoard < 8)
		{
			counter++;
			cout << "ERROR:  Board size too small." << endl << "8 <= number of squares <= 18";
			cout << endl << "Enter the number of squares along each edge of the board: ";
			cin >> numRowsInBoard;
		}
		else if (numRowsInBoard%2 != 0)
		{
			counter++;
			cout << "ERROR:  Board size odd" << endl << "8 <= number of squares <= 18";
			cout << endl << "Enter the number of squares along each edge of the board: ";
			cin >> numRowsInBoard;
		}
		else break;
		if (counter > 2) 
		{
			counter++;
			cout << endl << "ERROR:  Too many errors entering the size of the board ";
			return 1;
		}

	}

	InitializeBoard(myCMCheckersBoard, numRowsInBoard);

	DisplayBoard(myCMCheckersBoard, numRowsInBoard);






	return 0;
}

void InitializeBoard(int CMCheckersBoard[MAX_ARRAY_SIZE][MAX_ARRAY_SIZE], int numRowsInBoard)
{
	int i =0;
	int j = 0;
	//sets all to zero
	for (i=0; i<numRowsInBoard; i++) 
	{
		for (j=0; j<numRowsInBoard; j++)
		{
			CMCheckersBoard[i][j] = 0; 
		}
	}

	//places all the white mules, 2's
	for (i=0; i<1; i++) 
	{
		for (j=1; j<numRowsInBoard; j+=2)
		{
			CMCheckersBoard[i][j] = 2; 
		}
	}
	//places all the white soldiers, 1's
	for (i=1; i<(numRowsInBoard/2-2)+1; i+=2) 
	{
		for (j=0; j<numRowsInBoard; j+=2)
		{
			CMCheckersBoard[i][j] = 1;
		}
	}
	for (i=2; i<(numRowsInBoard/2-2)+1; i+=2) 
	{
		for (j=1; j<numRowsInBoard; j+=2)
		{
			CMCheckersBoard[i][j] = 1;
		}	
	}
	for (i=2; i<(numRowsInBoard/2-2)+1; i+=2) 
	{
		for (j=1; j<numRowsInBoard; j+=2)
		{
			CMCheckersBoard[i][j] = 1;
		}
	}
	//places all the red mules, 5's
	i= numRowsInBoard-1;
	while (i==numRowsInBoard-1)
	{
		for (j=0; j<numRowsInBoard; j+=2)
		{
			CMCheckersBoard[i][j] = 5;
		}
		i--;
	}
	//places all the red soldiers, 4's
	for (i=numRowsInBoard-2; i>(numRowsInBoard/2-2)+2; i-=1)
	{
		for (j=1; j<numRowsInBoard; j+=2)
		{
			CMCheckersBoard[i][j] = 4;
		}
		i--;
	}
	for (i=numRowsInBoard-3; i>(numRowsInBoard/2-2)+2; i-=1)
	{
		for (j=0; j<numRowsInBoard; j+=2)
		{
			CMCheckersBoard[i][j] = 4;
		}
		i--;
	}
}

void DisplayBoard( int CMCheckersBoard[MAX_ARRAY_SIZE][MAX_ARRAY_SIZE],
				  int numRowsInBoard)
{
	int counter = 0;

	for (int i=0; i<numRowsInBoard; i++) 
	{
		for (int j=0; j<numRowsInBoard; j++)
		{
			if (CMCheckersBoard[i][j] == 1)
			{
				cout << setw(4) << "WS";
			}
			else if (CMCheckersBoard[i][j] == 2)
			{
				cout << setw(4) << "WM";
			}
			else if (CMCheckersBoard[i][j] == 3)
			{
				cout << setw(4) << "WK";
			}
			else if (CMCheckersBoard[i][j] == 4)
			{
				cout << setw(4) << "RS";
			}
			else if (CMCheckersBoard[i][j] == 5)
			{
				cout << setw(4) << "RM";
			}
			else if (CMCheckersBoard[i][j] == 6)
			{
				cout << setw(4) << "RK";
			}
			else 
			{
				cout << setw(4) << counter;
			}
			counter ++;
		}

		cout << endl;
	}

}

int CountJumps( int CMCheckersBoard[MAX_ARRAY_SIZE][MAX_ARRAY_SIZE], int numRowsInBoard, 
			   int player, int xLocArray[], int yLocArray[] )
{
	int whitecounter = 0;
	int redcounter = 0;
	int counter = 0;

	for (int i=0; i<numRowsInBoard; i++)
	{
		xLocArray[i] = -1;
		yLocArray[i] = -1;
	}

	for (int i=0; i<numRowsInBoard; i++)
	{
		for (int j=0; j<numRowsInBoard; j++)
		{
			if (IsJump() == true)
			{
				counter++;
				xLocArray[j] = j;
			}
		}
		yLocArray[i] = i;
	}

	if (player == 1)
	{
		whitecounter = counter;
	}
	if (player == 2)
	{
		redcounter = counter;
	}

}

bool IsJump( int CMCheckersBoard[MAX_ARRAY_SIZE][MAX_ARRAY_SIZE],
			int numRowsInBoard, int player, int xLoc, int yLoc)
{
	int tracker = 0;
	int newXloc = 0;
	int newYloc = 0;
	if (CMCheckersBoard[yLoc][xloc] != 0)
	{
		if (player == 1) 
		{
			newXloc = numRowsInBoard-1;
			//checking the right side
			if (xLoc == 0)
			{
				if (CMCheckersBoard[yLoc+1][newXloc] == 4 || CMCheckersBoard[yLoc+1][newXloc] == 5 
					||CMCheckersBoard[yLoc+1][newXloc] == 6)
				{
					tracker++;
				}	 

				if (CMCheckersBoard[yLoc+2][newXloc-1] == 4 || CMCheckersBoard[yLoc+2][newXloc-1] == 5 
					||CMCheckersBoard[yLoc+2][newXloc-1] == 6)
				{
					tracker++;
				}
			}


			else if (xLoc == 1)
			{
				if (CMCheckersBoard[yLoc+1][xLoc] == 4 || CMCheckersBoard[yLoc+1][xLoc] == 5 
					||CMCheckersBoard[yLoc+1][xLoc] == 6)
				{
					tracker++;
				}
				if (CMCheckersBoard[yLoc+2][newXloc-1] == 4 || CMCheckersBoard[yLoc+2][newXloc-1] == 5 
					||CMCheckersBoard[yLoc+2][newXloc-1] == 6)
				{
					tracker++;
				}
			}
			else 
			{
				if (CMCheckersBoard[yLoc+1][xLoc-1] == 4 || CMCheckersBoard[yLoc+1][xLoc-1] == 5 
					||CMCheckersBoard[yLoc+1][xLoc-1] == 6)
				{
					tracker++;
				}
				if (CMCheckersBoard[yLoc+2][xLoc-2] == 4 || CMCheckersBoard[yLoc+2][xLoc-2] == 5 
					||CMCheckersBoard[yLoc+2][xLoc-2] == 6)
				{
					tracker++;
				}
			}

		}
		else if (player == 2)
		{
			newXloc = 0;
			//checking the right side
			if (xLoc == numRowsInBoard-1)
			{ 
				if (CMCheckersBoard[yLoc-1][newXloc] == 1 || CMCheckersBoard[yLoc-1][newXloc] == 2
					||CMCheckersBoard[yLoc-1][newXloc] == 3)
				{
					tracker++;
				}
				if (CMCheckersBoard[yLoc-2][newXloc+1] == 1 || CMCheckersBoard[yLoc-2][newXloc1] == 2
					||CMCheckersBoard[yLoc-2][newXloc+1] == 3)
				{
					tracker++;
				}
				else if (xLoc == numRowsInBoard-2)
				{ 

					if (CMCheckersBoard[yLoc-1][xLoc+1] == 1 || CMCheckersBoard[yLoc+1][xLoc-1] == 2
						||CMCheckersBoard[yLoc-1][xLoc+1] == 3)
					{
						tracker++;
					}
					if (CMCheckersBoard[yLoc-2][newXloc] == 1 || CMCheckersBoard[yLoc+2][newXloc] == 2
						||CMCheckersBoard[yLoc-2][newXloc] == 3)
					{
						tracker++;
					}
				}
				else 
				{
					if (CMCheckersBoard[yLoc-1][xLoc+1] == 1 || CMCheckersBoard[yLoc+1][xLoc-1] == 2
						||CMCheckersBoard[yLoc-1][xLoc+1] == 3)
					{
						tracker++;
					}
					if (CMCheckersBoard[yLoc-2][xLoc+2] == 1 || CMCheckersBoard[yLoc+2][xLoc-2] == 2
						||CMCheckersBoard[yLoc-2][xLoc+2] == 3)
					{
						tracker++;
					}
				}
			}
		}
		if (tracker == 2)
		{
			return true;
		}

	}
}

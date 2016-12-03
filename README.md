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
	int numfrom = 0;
	int numto = 0;
	int numMove = 0;
	int numJump = 0;

	int xfrom = 0;
	int xto = 0;
	int yfrom = 0;
	int yto = 0;

	int tries = 1;
	int player = 0;

	bool checkJump;
	bool checkMove;
	bool jump;
	bool move;

	char end = '0';

	cout << "Enter the number of squares along each edge of the board: "<<endl;


	while (counter <= 2)
	{
		if (!(cin >> numRowsInBoard))
		{
			cin.clear();
			cin.ignore(1000, '\n');
			cout << "ERROR:  Board size is not an integer" << endl << "8 <= number of squares <= 18";
			cout << endl << "Enter the number of squares along each edge of the board: "<<endl;
			counter++;
			if (counter > 2) 
			{
				cout << endl << "ERROR:  Too many errors entering the size of the board ";
				return 1;
			}
			//cin >> numRowsInBoard;
			continue;


		}
		if (numRowsInBoard%2 != 0)
		{
			counter++;
			cout << "ERROR:  Board size odd" << endl << "8 <= number of squares <= 18";
			cout << endl << "Enter the number of squares along each edge of the board: "<<endl;;
			cin >> numRowsInBoard;
		}
		else if (numRowsInBoard > 18)
		{
			counter++;
			cout << "ERROR:  Board size too large" << endl << "8 <= number of squares <= 18";
			cout << endl << "Enter the number of squares along each edge of the board: "<<endl;;
			cin >> numRowsInBoard;
		}
		else if (numRowsInBoard < 8)
		{
			counter++;
			cout << "ERROR:  Board size too small." << endl << "8 <= number of squares <= 18";
			cout << endl << "Enter the number of squares along each edge of the board: "<<endl;;
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
	numJump = CountJumps( myCMCheckersBoard, numRowsInBoard, player, xIndicesJump, yIndicesJump );
	numMove = CountMove1Squares(myCMCheckersBoard, numRowsInBoard,player,xIndicesMove,yIndicesMove);


	counter = 0;

	while(CheckWin(myCMCheckersBoard, numRowsInBoard) == false)
	{
		if (counter%2==0) 
		{
			player = 1;
		}
		else 
		{
			player = 2;
		}

		tries = 1;

		while (player == 1)
		{
			numJump = CountJumps( myCMCheckersBoard, numRowsInBoard, player, xIndicesJump, yIndicesJump );
			numMove = CountMove1Squares(myCMCheckersBoard, numRowsInBoard,player,xIndicesMove,yIndicesMove);

			if(numMove == 0 && numJump ==0)
			{
				cout << "White is unable to move" <<endl;
				cout << "GAME OVER, Red has won" <<endl;
				cout << "Enter any character to close the game" << endl;
				cin >> end;
				return 0;
			}

			if (tries == 1)
			{
				cout << "White takes a turn" << endl;
			}

			tries++;

			cout << "Enter the square number of the checker you want to move" << endl;

			if (!(cin >> numfrom))
			{
				cerr << "ERROR:  you did not enter an integer" << endl;
				cerr << "Try again" << endl;
				continue;
			}

			if (numfrom <0 || numfrom >= numRowsInBoard *numRowsInBoard)
			{
				cerr << "ERROR:  that square is not on the board." << endl;
				cerr << "Try again"<< endl;
				continue;
			}

			xfrom = numfrom % numRowsInBoard;
			yfrom = numfrom / numRowsInBoard;

			checkJump = CheckList(xIndicesJump, yIndicesJump, xfrom, yfrom);
			checkMove = CheckList(xIndicesMove, yIndicesMove, xfrom, yfrom);

			if (myCMCheckersBoard[yfrom][xfrom] == 4 || myCMCheckersBoard[yfrom][xfrom] == 5 || myCMCheckersBoard[yfrom][xfrom] == 6)
			{
				cerr << "ERROR:  that square contains an opponent’s checker" << endl;
				cerr << "Try again" << endl;
				continue;
			}

			if (myCMCheckersBoard[yfrom][xfrom] == 0)
			{
				cerr << "ERROR:  that square is empty" << endl;
				cerr << "Try again" << endl;
				continue;
			}



			if (numJump > 0 && checkJump == false)
			{
				cerr << "ERROR: You can jump with another checker, you may not move your chosen checker" << endl;
				cerr << "You can jump using checkers on the following squares: ";

				for (int i = 0; i < MAX_PIECES; i++)
				{
					cout << myCMCheckersBoard[yIndicesJump[i]][xIndicesJump[i]] << " ";

				}
				cout << endl;

				cerr << "Try again" << endl;
				continue;
			}

			if (checkJump == false && checkMove == false)
			{
				cerr << "ERROR: There is no legal move for this checker" << endl;
				cerr << "Try again" << endl;
				continue;
			}
			
				break;
		}

		while (player == 1)
		{
			numJump = CountJumps( myCMCheckersBoard, numRowsInBoard, player, xIndicesJump, yIndicesJump );
			numMove = CountMove1Squares(myCMCheckersBoard, numRowsInBoard,player,xIndicesMove,yIndicesMove);

			cout << "Enter the square number of the square you want to move your checker to" << endl;

			if (!(cin >> numto))
			{
				cerr << "ERROR:  you did not enter an integer" << endl;
				cerr << "Try again" << endl;
				continue;
			}

			if (numto <0 || numto >= numRowsInBoard *numRowsInBoard)
			{
				cerr << "ERROR:  It is not possible to move to a square that is not on the board." << endl;
				cerr << "Try again"<< endl;
				continue;
			}

			xto = numto % numRowsInBoard;
			yto = numto / numRowsInBoard;

			if (myCMCheckersBoard[yto][xto] != 0)
			{
				cerr << "ERROR:  It is not possible to move to a square that is already occupied" << endl;
				cerr << "Try again" << endl;
				continue;
			}

			if (abs(xto-xfrom) == 1 && abs(yto-yfrom) == 1)
			{
				if (checkJump == true)
				{
					cerr << "ERROR:  You can jump with this checker, you must jump not move 1 space" << endl;
					cerr << "Try again" << endl;
					continue;
				}
			}
			MakeMove( myCMCheckersBoard, numRowsInBoard, player, numfrom, numto, jump );
			move = MakeMove( myCMCheckersBoard, numRowsInBoard, player, numfrom, numto, jump );
			DisplayBoard(myCMCheckersBoard, numRowsInBoard);

			if(move == false)
		{
			cerr << "ERROR: moving to that square is not legal, Try again" << endl;
			continue;
		}
			else if (move == true)
			{
				if (jump == true)
				{ 
					if (checkJump == true)
					{
						numfrom = numto;
						cout << "You can jump again,  Please enter the next square you wish to move your checker to" << endl;
						cin >> numto;

					}
				else
					break;
				}
				else
					break;
			}
		}



	

	tries = 1;

	while (player == 2)
	{
		numJump = CountJumps( myCMCheckersBoard, numRowsInBoard, player, xIndicesJump, yIndicesJump );
		numMove = CountMove1Squares(myCMCheckersBoard, numRowsInBoard,player,xIndicesMove,yIndicesMove);

		if(numMove == 0 && numJump ==0)
		{
			cout << "Red is unable to move" << endl;
			cout << "GAME OVER, White has won" <<endl;
			cout << "Enter any character to close the game" << endl;
			cin >> end;
			return 0;
		}

		if (tries == 1)
		{
			cout << "Red takes a turn" << endl;
		}

		tries++;

		cout << "Enter the square number of the checker you want to move" << endl;

		if (!(cin >> numfrom))
		{
			cerr << "ERROR:  you did not enter an integer" << endl;
			cerr << "Try again" << endl;
			continue;
		}

		if (numfrom <0 || numfrom >= numRowsInBoard *numRowsInBoard)
		{
			cerr << "ERROR:  that square is not on the board." << endl;
			cerr << "Try again"<< endl;
			continue;
		}

		xfrom = numfrom % numRowsInBoard;
		yfrom = numfrom / numRowsInBoard;

		checkJump = CheckList(xIndicesJump, yIndicesJump, xfrom, yfrom);
		checkMove = CheckList(xIndicesMove, yIndicesMove, xfrom, yfrom);


		if (myCMCheckersBoard[yfrom][xfrom] == 1 || myCMCheckersBoard[yfrom][xfrom] == 2 || myCMCheckersBoard[yfrom][xfrom] == 3)
		{
			cerr << "ERROR:  that square contains an opponent’s checker" << endl;
			cerr << "Try again" << endl;
			continue;
		}

		if (myCMCheckersBoard[yfrom][xfrom] == 0)
		{
			cerr << "ERROR:  that square is empty" << endl;
			cerr << "Try again" << endl;
			continue;
		}

		if (numJump > 0 && checkJump == false)
		{
			cerr << "ERROR: You can jump with another checker, you may not move your chosen checker" << endl;
			cerr << "You can jump using checkers on the following squares: ";

			for (int i = 0; i < MAX_PIECES; i++)
			{
				cout << myCMCheckersBoard[yIndicesJump[i]][xIndicesJump[i]] << " ";

			}
			cout << endl;

			cerr << "Try again" << endl;
			continue;
		}

		if (checkJump == false && checkMove == false)
		{
			cerr << "ERROR: There is no legal move for this checker" << endl;
			cerr << "Try again" << endl;
			continue;
		}

		break;
	}
	while (player == 2)
	{
		numJump = CountJumps( myCMCheckersBoard, numRowsInBoard, player, xIndicesJump, yIndicesJump );
		numMove = CountMove1Squares(myCMCheckersBoard, numRowsInBoard,player,xIndicesMove,yIndicesMove);

		cout << "Enter the square number of the square you want to move your checker to" << endl;

		if (!(cin >> numto))
		{
			cerr << "ERROR:  you did not enter an integer" << endl;
			cerr << "Try again" << endl;
			continue;
		}

		if (numto <0 || numto >= numRowsInBoard *numRowsInBoard)
		{
			cerr << "ERROR:  It is not possible to move to a square that is not on the board." << endl;
			cerr << "Try again"<< endl;
			continue;
		}

		xto = numto % numRowsInBoard;
		yto = numto / numRowsInBoard;

		if (myCMCheckersBoard[yto][xto] != 0)
		{
			cerr << "ERROR:  It is not possible to move to a square that is already occupied" << endl;
			cerr << "Try again" << endl;
			continue;
		}

		if (abs(xto-xfrom) == 1 && abs(yto-yfrom) == 1)
		{
			if (checkJump == true)
			{
				cerr << "ERROR:  You can jump with this checker, you must jump not move 1 space" << endl;
				cerr << "Try again" << endl;
				continue;
			}
		}
		MakeMove(myCMCheckersBoard, numRowsInBoard, player, numfrom, numto, jump);
		move = MakeMove(myCMCheckersBoard, numRowsInBoard, player, numfrom, numto, jump);
		DisplayBoard(myCMCheckersBoard, numRowsInBoard);

		if(move == false)
		{
			cerr << "ERROR: moving to that square is not legal, Try again" << endl;
			continue;
		}
		else if (move == true)
			{
				if (jump == true)
				{ 
					if (checkJump == true)
					{
						numfrom = numto;
						cout << "You can jump again,  Please enter the next square you wish to move your checker to" << endl;
						cin >> numto;
					}
				else
					break;
				}
				else
					break;
			}
	}
counter++;
}



	








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

void DisplayBoard( int CMCheckersBoard[MAX_ARRAY_SIZE][MAX_ARRAY_SIZE],int numRowsInBoard)
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

int CountJumps( int CMCheckersBoard[MAX_ARRAY_SIZE][MAX_ARRAY_SIZE], int numRowsInBoard,  int player, int xLocArray[], int yLocArray[] )
{

	int counter = 0;
	int i = 0;

	for (i=0; i<numRowsInBoard; i++)
	{
		xLocArray[i] = -1;
		yLocArray[i] = -1;
	}

	i = 0;
	for (int yindex=0; yindex<numRowsInBoard; yindex++)
	{

		for (int xindex=0; xindex<numRowsInBoard; xindex++)
		{
			if (IsJump(CMCheckersBoard, numRowsInBoard, player,xindex,yindex) == true)
			{
				counter++;
				xLocArray[i] = xindex;
				yLocArray[i] = yindex;
				i++;
			}
		}

	}
	return counter;

}

bool IsJump( int CMCheckersBoard[MAX_ARRAY_SIZE][MAX_ARRAY_SIZE],
			int numRowsInBoard, int player, int xLoc, int yLoc)
{
	int Rtracker = 0;
	int Ltracker = 0;
	int RKtracker = 0;
	int LKtracker = 0;
	int newXloc = 0;
	int newYloc = 0;


	if (CMCheckersBoard[yLoc][xLoc] != 0)
	{
		///////////////////////////////////////////////////////////////////////////////////
		// WHITE PLAYER ///////////////////////////////////////////////////////////////////
		if (player == 1)  //white player coming from the top
		{
			if (xLoc == 0)  ////////////////////////////////////////////////////////////
			{
				newXloc = numRowsInBoard-1;
				//checking the right side for oponents piece
				if (CMCheckersBoard[yLoc+1][newXloc] == 4 || CMCheckersBoard[yLoc+1][newXloc] == 5 
					||CMCheckersBoard[yLoc+1][newXloc] == 6)
				{
					Rtracker++;
				}	 
				//checking the right side for empty space
				if (CMCheckersBoard[yLoc+2][newXloc-1] == 0)
				{
					Rtracker++;
				}

				//checking the left side for oponents piece
				if (CMCheckersBoard[yLoc+1][xLoc+1] == 4 || CMCheckersBoard[yLoc+1][xLoc+1] == 5 
					||CMCheckersBoard[yLoc+1][xLoc+1] == 6)
				{
					Ltracker++;
				}	 
				//checking the left side for empty space
				if (CMCheckersBoard[yLoc+2][xLoc+2] == 0)
				{
					Ltracker++;
				}
				//IF THE CHECKER IS A WHITE KING
				if (CMCheckersBoard[yLoc][xLoc] == 3) 
				{
					newXloc = numRowsInBoard-1;
					//checking the right side for oponents piece
					if (CMCheckersBoard[yLoc-1][newXloc] == 1 || CMCheckersBoard[yLoc-1][newXloc] == 2
						||CMCheckersBoard[yLoc-1][newXloc] == 3)
					{
						RKtracker++;
					}
					//checking the right side for empty space
					if (CMCheckersBoard[yLoc-2][newXloc+1] == 0)
					{
						RKtracker++;
					}
					//checking the left side for oponents piece
					if (CMCheckersBoard[yLoc-1][xLoc-1] == 1 || CMCheckersBoard[yLoc-1][xLoc-1] == 2
						||CMCheckersBoard[yLoc-1][xLoc-1] == 3)
					{
						LKtracker++;
					}
					//checking the left side for empty space
					if (CMCheckersBoard[yLoc-2][xLoc-2] == 0)
					{
						LKtracker++;
					}
				}
			}


			else if (xLoc == 1) ////////////////////////////////////////////////////////////////////////
			{
				newXloc = numRowsInBoard-1;
				//checking the right side for oponents piece
				if (CMCheckersBoard[yLoc+1][xLoc-1] == 4 || CMCheckersBoard[yLoc+1][xLoc-1] == 5 
					||CMCheckersBoard[yLoc+1][xLoc-1] == 6)
				{
					Rtracker++;
				}
				//checking the right side for empty space
				if (CMCheckersBoard[yLoc+2][newXloc] == 0)
				{
					Rtracker++;
				}

				//checking the left side for oponents piece
				if (CMCheckersBoard[yLoc+1][xLoc+1] == 4 || CMCheckersBoard[yLoc+1][xLoc+1] == 5 
					||CMCheckersBoard[yLoc+1][xLoc+1] == 6)
				{
					Ltracker++;
				}	 
				//checking the left side for empty space
				if (CMCheckersBoard[yLoc+2][xLoc+2] == 0)
				{
					Ltracker++;
				}
				//IF THE CHECKER IS A WHITE KING
				if (CMCheckersBoard[yLoc][xLoc] == 3) 
					//checking the right side for oponents piece
				{
					newXloc = numRowsInBoard-1;
					if (CMCheckersBoard[yLoc-1][xLoc+1] == 1 || CMCheckersBoard[yLoc-1][xLoc+1] == 2
						||CMCheckersBoard[yLoc-1][xLoc+1] == 3)
					{
						RKtracker++;
					}
					//checking the right side for empty space
					if (CMCheckersBoard[yLoc-2][newXloc] == 0)
					{
						RKtracker++;
					}
					//checking the left side for oponents piece
					if (CMCheckersBoard[yLoc-1][xLoc-1] == 1 || CMCheckersBoard[yLoc-1][xLoc-1] == 2
						||CMCheckersBoard[yLoc-1][xLoc-1] == 3)
					{
						LKtracker++;
					}
					//checking the left side for empty space
					if (CMCheckersBoard[yLoc-2][xLoc-2] == 0)
					{
						LKtracker++;
					}
				}


			}
			else if (xLoc == numRowsInBoard-1) /////////////////////////////////////////////////
			{
				//checking the right side for oponents piece
				if (CMCheckersBoard[yLoc+1][xLoc-1] == 4 || CMCheckersBoard[yLoc+1][xLoc-1] == 5 
					||CMCheckersBoard[yLoc+1][xLoc-1] == 6)
				{
					Rtracker++;
				}
				//checking the right side for empty piece
				if (CMCheckersBoard[yLoc+2][xLoc-2] == 0)
				{
					Rtracker++;
				}
				//checking the left side for oponents piece
				if (CMCheckersBoard[yLoc+1][newXloc] == 4 || CMCheckersBoard[yLoc+1][newXloc] == 5 
					||CMCheckersBoard[yLoc+1][newXloc] == 6)
				{
					Ltracker++;
				}	 
				//checking the left side for empty space
				if (CMCheckersBoard[yLoc+2][newXloc+1] == 0)
				{
					Ltracker++;
				}
				//IF THE CHECKER IS A WHITE KING
				if (CMCheckersBoard[yLoc][xLoc] == 3) 
				{
					//checking the right side for oponents piece
					if (CMCheckersBoard[yLoc-1][xLoc-1] == 1 || CMCheckersBoard[yLoc-1][xLoc-1] == 2
						||CMCheckersBoard[yLoc-1][xLoc-1] == 3)
					{
						RKtracker++;
					}
					//checking the right side for empty space
					if (CMCheckersBoard[yLoc-2][xLoc-2] == 0)
					{
						RKtracker++;
					}
					newXloc = numRowsInBoard-1;
					//checking the left side for oponents piece
					if (CMCheckersBoard[yLoc-1][newXloc] == 1 || CMCheckersBoard[yLoc-1][newXloc] == 2
						||CMCheckersBoard[yLoc-1][newXloc] == 3)
					{
						LKtracker++;
					}
					//checking the left side for empty space
					if (CMCheckersBoard[yLoc-2][newXloc-1] == 0)
					{
						LKtracker++;
					}
				}

			}
			else if (xLoc == numRowsInBoard-2) //////////////////////////////////////////
			{
				//checking the right side for oponents piece
				if (CMCheckersBoard[yLoc+1][xLoc-1] == 4 || CMCheckersBoard[yLoc+1][xLoc-1] == 5 
					||CMCheckersBoard[yLoc+1][xLoc-1] == 6)
				{
					Rtracker++;
				}
				//checking the right side for empty space
				if (CMCheckersBoard[yLoc+2][xLoc-2] == 0)
				{
					Rtracker++;
				}
				//checking the left side for oponents piece
				if (CMCheckersBoard[yLoc+1][xLoc+1] == 4 || CMCheckersBoard[yLoc+1][xLoc+1] == 5 
					||CMCheckersBoard[yLoc+1][xLoc+1] == 6)
				{
					Ltracker++;
				}	 
				//checking the left side for empty space
				if (CMCheckersBoard[yLoc+2][newXloc] == 0)
				{
					Ltracker++;
				}
				//IF THE CHECKER IS A WHITE KING
				if (CMCheckersBoard[yLoc][xLoc] == 3) 
				{
					//checking the right side for oponents piece
					if (CMCheckersBoard[yLoc-1][xLoc+1] == 1 || CMCheckersBoard[yLoc-1][xLoc+1] == 2
						||CMCheckersBoard[yLoc-1][xLoc+1] == 3)
					{
						RKtracker++;
					}
					//checking the right side for empty space
					if (CMCheckersBoard[yLoc-2][xLoc+2] == 0)
					{
						RKtracker++;
					}
					newXloc = numRowsInBoard-1;
					//checking the left side for oponents piece
					if (CMCheckersBoard[yLoc-1][xLoc-1] == 1 || CMCheckersBoard[yLoc-1][xLoc-1] == 2
						||CMCheckersBoard[yLoc-1][xLoc-1] == 3)
					{
						LKtracker++;
					}
					//checking the left side for empty space
					if (CMCheckersBoard[yLoc-2][newXloc] == 0)
					{
						LKtracker++;
					}
				}
			}

			else  ////////////////////////////////////////////////////////////////////
			{
				//checking the right side for oponents piece
				if (CMCheckersBoard[yLoc+1][xLoc-1] == 4 || CMCheckersBoard[yLoc+1][xLoc-1] == 5 
					||CMCheckersBoard[yLoc+1][xLoc-1] == 6)
				{
					Rtracker++;
				}
				//checking the right side for empty space
				if (CMCheckersBoard[yLoc+2][xLoc-2] == 0)
				{
					Rtracker++;
				}
				//checking the left side for oponents piece
				if (CMCheckersBoard[yLoc+1][xLoc+1] == 4 || CMCheckersBoard[yLoc+1][xLoc+1] == 5 
					||CMCheckersBoard[yLoc+1][xLoc+1] == 6)
				{
					Ltracker++;
				}	 
				//checking the left side for empty space
				if (CMCheckersBoard[yLoc+2][xLoc+2] == 0)
				{
					Ltracker++;
				}
				//IF THE CHECKER IS A WHITE KING
				if (CMCheckersBoard[yLoc][xLoc] == 3) 
				{
					//checking the right side for oponents piece
					if (CMCheckersBoard[yLoc-1][xLoc+1] == 1 || CMCheckersBoard[yLoc+1][xLoc-1] == 2
						||CMCheckersBoard[yLoc-1][xLoc+1] == 3)
					{
						RKtracker++;
					}
					//checking the right side for empty space
					if (CMCheckersBoard[yLoc-2][xLoc+2] == 0)
					{
						RKtracker++;
					}
					//checking the left side for oponents piece
					if (CMCheckersBoard[yLoc-1][xLoc-1] == 1 || CMCheckersBoard[yLoc-1][xLoc-1] == 2
						||CMCheckersBoard[yLoc-1][xLoc-1] == 3)
					{
						LKtracker++;
					}
					//checking the left side for empty space
					if (CMCheckersBoard[yLoc-2][xLoc-2] == 0)
					{
						LKtracker++;
					}
				}

			}

		}
		///////////////////////////////////////////////////////////////////////////
		// RED PLAYER //////////////////////////////////////////////////////////////
		else if (player == 2)//red player, going up from the bottom
		{

			if (xLoc == numRowsInBoard-1) ///////////////////////////////////////////////////////
			{ 
				//checking the right side for oponents piece
				if (CMCheckersBoard[yLoc-1][newXloc] == 1 || CMCheckersBoard[yLoc-1][newXloc] == 2
					||CMCheckersBoard[yLoc-1][newXloc] == 3)
				{
					Rtracker++;
				}
				//checking the right side for empty space
				if (CMCheckersBoard[yLoc-2][newXloc+1] == 0)
				{
					Rtracker++;
				}
				//checking the left side for oponents piece
				if (CMCheckersBoard[yLoc-1][xLoc-1] == 1 || CMCheckersBoard[yLoc-1][xLoc-1] == 2
					||CMCheckersBoard[yLoc-1][xLoc-1] == 3)
				{
					Ltracker++;
				}
				//checking the left side for empty space
				if (CMCheckersBoard[yLoc-2][xLoc-2] == 0)
				{
					Ltracker++;
				}
				//IF THE CHECKER IS A RED KING
				if (CMCheckersBoard[yLoc][xLoc] == 6) 
				{
					//checking the right side for oponents piece
					if (CMCheckersBoard[yLoc+1][newXloc] == 1 || CMCheckersBoard[yLoc+1][newXloc] == 2 
						||CMCheckersBoard[yLoc+1][newXloc] == 3)
					{
						RKtracker++;
					}	 
					//checking the right side for empty space
					if (CMCheckersBoard[yLoc+2][newXloc+1] == 0)
					{
						RKtracker++;
					}

					//checking the left side for oponents piece
					if (CMCheckersBoard[yLoc+1][xLoc+1] == 1 || CMCheckersBoard[yLoc+1][xLoc+1] == 2 
						||CMCheckersBoard[yLoc+1][xLoc+1] == 3)
					{
						LKtracker++;
					}	 
					//checking the left side for empty space
					if (CMCheckersBoard[yLoc+2][xLoc+2] == 0)
					{
						LKtracker++;
					}
				}

				else if (xLoc == numRowsInBoard-2) ////////////////////////////////////////////////
				{ 
					//checking the right side for oponents piece
					if (CMCheckersBoard[yLoc-1][xLoc+1] == 1 || CMCheckersBoard[yLoc-1][xLoc+1] == 2
						||CMCheckersBoard[yLoc-1][xLoc+1] == 3)
					{
						Rtracker++;
					}
					//checking the right side for empty space
					if (CMCheckersBoard[yLoc-2][newXloc] == 0)
					{
						Rtracker++;
					}
					//checking the left side for oponents piece
					if (CMCheckersBoard[yLoc-1][xLoc-1] == 1 || CMCheckersBoard[yLoc-1][xLoc-1] == 2
						||CMCheckersBoard[yLoc-1][xLoc-1] == 3)
					{
						Ltracker++;
					}
					//checking the left side for empty space
					if (CMCheckersBoard[yLoc-2][xLoc-2] == 0)
					{
						Ltracker++;
					}
					//IF THE CHECKER IS A RED KING
					if (CMCheckersBoard[yLoc][xLoc] == 6) 
					{
						//checking the right side for oponents piece
						if (CMCheckersBoard[yLoc+1][xLoc+1] == 1 || CMCheckersBoard[yLoc+1][xLoc+1] == 2
							||CMCheckersBoard[yLoc+1][xLoc+1] == 3)
						{
							RKtracker++;
						}
						//checking the right side for empty space
						if (CMCheckersBoard[yLoc+2][newXloc] == 0)
						{
							RKtracker++;
						}

						//checking the left side for oponents piece
						if (CMCheckersBoard[yLoc+1][xLoc-1] == 1 || CMCheckersBoard[yLoc+1][xLoc-1] == 2 
							||CMCheckersBoard[yLoc+1][xLoc-1] == 3)
						{
							LKtracker++;
						}	 
						//checking the left side for empty space
						if (CMCheckersBoard[yLoc+2][xLoc-2] == 0)
						{
							LKtracker++;
						}
					}
				}
				else if (xLoc == 0) ///////////////////////////////////////////////////////////////
				{
					//checking the right side for oponents piece
					if (CMCheckersBoard[yLoc-1][xLoc+1] == 1 || CMCheckersBoard[yLoc-1][xLoc+1] == 2
						||CMCheckersBoard[yLoc-1][xLoc+1] == 3)
					{
						Rtracker++;
					}
					//checking the right side for empty space
					if (CMCheckersBoard[yLoc-2][xLoc+2] == 0)
					{
						Rtracker++;
					}
					newXloc = numRowsInBoard-1;
					//checking the left side for oponents piece
					if (CMCheckersBoard[yLoc-1][newXloc] == 1 || CMCheckersBoard[yLoc-1][newXloc] == 2
						||CMCheckersBoard[yLoc-1][newXloc] == 3)
					{
						Ltracker++;
					}
					//checking the left side for empty space
					if (CMCheckersBoard[yLoc-2][newXloc-1] == 0)
					{
						Ltracker++;
					}
					//IF THE CHECKER IS A RED KING
					if (CMCheckersBoard[yLoc][xLoc] == 6) 
					{
						newXloc = numRowsInBoard-1;
						//checking the right side for oponents piece
						if (CMCheckersBoard[yLoc+1][xLoc+1] == 1 || CMCheckersBoard[yLoc+1][xLoc+1] == 2 
							||CMCheckersBoard[yLoc+1][xLoc+1] == 3)
						{
							RKtracker++;
						}
						//checking the right side for empty piece
						if (CMCheckersBoard[yLoc+2][xLoc+2] == 0)
						{
							RKtracker++;
						}
						//checking the left side for oponents piece
						if (CMCheckersBoard[yLoc+1][newXloc] == 1 || CMCheckersBoard[yLoc+1][newXloc] == 2 
							||CMCheckersBoard[yLoc+1][newXloc] == 3)
						{
							LKtracker++;
						}	 
						//checking the left side for empty space
						if (CMCheckersBoard[yLoc+2][newXloc-1] == 0)
						{
							LKtracker++;
						}

						else if (xLoc == 1) /////////////////////////////////////////////////////////////////
						{
							//checking the right side for oponents piece
							if (CMCheckersBoard[yLoc-1][xLoc+1] == 1 || CMCheckersBoard[yLoc-1][xLoc+1] == 2
								||CMCheckersBoard[yLoc-1][xLoc+1] == 3)
							{
								Rtracker++;
							}
							//checking the right side for empty space
							if (CMCheckersBoard[yLoc-2][xLoc+2] == 0)
							{
								Rtracker++;
							}
							newXloc = numRowsInBoard-1;
							//checking the left side for oponents piece
							if (CMCheckersBoard[yLoc-1][xLoc-1] == 1 || CMCheckersBoard[yLoc-1][xLoc-1] == 2
								||CMCheckersBoard[yLoc-1][xLoc-1] == 3)
							{
								Ltracker++;
							}
							//checking the left side for empty space
							if (CMCheckersBoard[yLoc-2][newXloc] == 0)
							{
								Ltracker++;
							}
							//IF THE CHECKER IS A RED KING
							if (CMCheckersBoard[yLoc][xLoc] == 6) 
							{
								newXloc = numRowsInBoard;
								//checking the right side for oponents piece
								if (CMCheckersBoard[yLoc+1][xLoc+1] == 1 || CMCheckersBoard[yLoc+1][xLoc+1] == 2 
									||CMCheckersBoard[yLoc+1][xLoc+1] == 3)
								{
									RKtracker++;
								}
								//checking the right side for empty space
								if (CMCheckersBoard[yLoc+2][xLoc+2] == 0)
								{
									RKtracker++;
								}
								//checking the left side for oponents piece
								if (CMCheckersBoard[yLoc+1][xLoc-1] == 1 || CMCheckersBoard[yLoc+1][xLoc-1] == 2 
									||CMCheckersBoard[yLoc+1][xLoc-1] == 3)
								{
									LKtracker++;
								}	 
								//checking the left side for empty space
								if (CMCheckersBoard[yLoc+2][newXloc] == 0)
								{
									LKtracker++;
								}
							}
						}
						else  ////////////////////////////////////////////////////////////////////////
						{
							//checking the right side for oponents piece
							if (CMCheckersBoard[yLoc-1][xLoc+1] == 1 || CMCheckersBoard[yLoc+1][xLoc-1] == 2
								||CMCheckersBoard[yLoc-1][xLoc+1] == 3)
							{
								Rtracker++;
							}
							//checking the right side for empty space
							if (CMCheckersBoard[yLoc-2][xLoc+2] == 0)
							{
								Rtracker++;
							}
							//checking the left side for oponents piece
							if (CMCheckersBoard[yLoc-1][xLoc-1] == 1 || CMCheckersBoard[yLoc-1][xLoc-1] == 2
								||CMCheckersBoard[yLoc-1][xLoc-1] == 3)
							{
								Ltracker++;
							}
							//checking the left side for empty space
							if (CMCheckersBoard[yLoc-2][xLoc-2] == 0)
							{
								Ltracker++;
							}
							//IF THE CHECKER IS A RED KING
							if (CMCheckersBoard[yLoc][xLoc] == 6) 
							{
								//checking the right side for oponents piece
								if (CMCheckersBoard[yLoc+1][xLoc+1] == 1 || CMCheckersBoard[yLoc+1][xLoc+1] == 2
									||CMCheckersBoard[yLoc+1][xLoc+1] == 3)
								{
									RKtracker++;
								}
								//checking the right side for empty space
								if (CMCheckersBoard[yLoc+2][xLoc+2] == 0)
								{
									RKtracker++;
								}
								//checking the left side for oponents piece
								if (CMCheckersBoard[yLoc+1][xLoc-1] == 1 || CMCheckersBoard[yLoc+1][xLoc-1] == 2 
									||CMCheckersBoard[yLoc+1][xLoc-1] == 3)
								{
									LKtracker++;
								}	 
								//checking the left side for empty space
								if (CMCheckersBoard[yLoc+2][xLoc-2] == 0)
								{
									LKtracker++;
								}
							}

						}
					}
				}

				// returning true if there is an available jump

				if (Rtracker == 2)
				{
					return true;
				}
				if  (Ltracker == 2)
				{
					return true;
				}
				//figure out what to do with the king trackers


			}
		}
	}
}



int CountMove1Squares( int CMCheckersBoard[MAX_ARRAY_SIZE][MAX_ARRAY_SIZE], int numRowsInBoard, int player, int xLocArray[],  int yLocArray[] )
{	
	int counter = 0;
	int i = 0;

	for (i=0; i<numRowsInBoard; i++)
	{
		xLocArray[i] = -1;
		yLocArray[i] = -1;
	}

	i = 0;
	for (int yindex=0; yindex<numRowsInBoard; yindex++)
	{


		for (int xindex=0; xindex<numRowsInBoard; xindex++)
		{

			if (IsMove1Square(CMCheckersBoard, numRowsInBoard, player,xindex,yindex) == true)
			{
				counter++;
				xLocArray[i] = xindex;
				yLocArray[i] = yindex;
				i++;
			}
		}

	}
	return counter;
}



bool IsMove1Square( int CMCheckersBoard[MAX_ARRAY_SIZE][MAX_ARRAY_SIZE], int numRowsInBoard, int player, int xLoc, int yLoc)
{
	bool isMove = false;
	if(player ==1)
	{
		if(CMCheckersBoard[yLoc][xLoc] ==1 || CMCheckersBoard[yLoc][xLoc] ==2 || CMCheckersBoard[yLoc][xLoc] ==3)
		{
			if(xLoc = numRowsInBoard - 1)
			{
				if(CMCheckersBoard[yLoc + 1][0] ==0)
				{
					isMove = true;
				}
				if(CMCheckersBoard[yLoc + 1][xLoc - 1] ==0)
				{
					isMove = true;
				}
			}
			else if(xLoc = 0)
			{
				if(CMCheckersBoard[yLoc + 1][xLoc +1] ==0)
				{
					isMove = true;
				}
				if(CMCheckersBoard[yLoc + 1][numRowsInBoard - 1] ==0)
				{
					isMove = true;
				}
			}
			else
			{
				if(CMCheckersBoard[yLoc + 1][xLoc +1] ==0)
				{
					isMove = true;
				}
				if(CMCheckersBoard[yLoc + 1][xLoc - 1] ==0)
				{
					isMove = true;
				}
			}
		}
		if(CMCheckersBoard[yLoc][xLoc] ==3 )
		{
			if(CMCheckersBoard[yLoc - 1][xLoc +1] ==0)
			{
				isMove = true;
			}
			if(CMCheckersBoard[yLoc - 1][xLoc - 1] ==0)
			{
				isMove = true;
			}
		}		
	}



	if(player ==2)
	{
		if(CMCheckersBoard[yLoc][xLoc] ==4 || CMCheckersBoard[yLoc][xLoc] ==5 || CMCheckersBoard[yLoc][xLoc] ==6)
		{
			if(xLoc = numRowsInBoard - 1)
			{
				if(CMCheckersBoard[yLoc - 1][0] ==0)
				{
					isMove = true;
				}
				if(CMCheckersBoard[yLoc - 1][xLoc - 1] ==0)
				{
					isMove = true;
				}
			}
			else if(xLoc = 0)
			{
				if(CMCheckersBoard[yLoc - 1][xLoc +1] ==0)
				{
					isMove = true;
				}
				if(CMCheckersBoard[yLoc - 1][numRowsInBoard - 1] ==0)
				{
					isMove = true;
				}
			}
			else
			{
				if(CMCheckersBoard[yLoc - 1][xLoc +1] ==0)
				{
					isMove = true;
				}
				if(CMCheckersBoard[yLoc - 1][xLoc - 1] ==0)
				{
					isMove = true;
				}
			}
		}
		if(CMCheckersBoard[yLoc][xLoc] ==6 )
		{
			if(CMCheckersBoard[yLoc + 1][xLoc +1] ==0)
			{
				isMove = true;
			}
			if(CMCheckersBoard[yLoc + 1][xLoc - 1] ==0)
			{
				isMove = true;
			}
		}
	}
	return isMove;
}

bool CheckList( int inArray1[], int inArray2[], int xIndex, int yindex)
{
	bool check = false;

	for(int i = 0; i <MAX_PIECES; i++)
	{
		if(inArray1[i]== xIndex && inArray2[i] == yindex)
		{
			check = true;
		}
	}
	return check;
}

bool MakeMove( int CMCheckersBoard[MAX_ARRAY_SIZE][MAX_ARRAY_SIZE], int numRowsInBoard, int player, int fromSquareNum, int toSquareNum, bool &jumped )
{
	int xdistance = 0;
	int ydistance = 0;
	int xfrom = 0;
	int xto = 0;
	int yto = 0;
	int yfrom = 0;
	int i = 0;
	int j = 0;
	int end = 0;

	xfrom = fromSquareNum % numRowsInBoard;
	xto = toSquareNum % numRowsInBoard;
	yfrom = fromSquareNum / numRowsInBoard;
	yto = toSquareNum / numRowsInBoard;

	xdistance = abs(toSquareNum % numRowsInBoard - fromSquareNum % numRowsInBoard);
	ydistance = abs(toSquareNum / numRowsInBoard - fromSquareNum / numRowsInBoard);

	if (xdistance == numRowsInBoard || xdistance == -numRowsInBoard)
	{
		xdistance = 1;
	}

	if (xdistance > 2 && ydistance > 2)
	{
		cerr << "Error; illegal move" << endl;
		return false;
	}

	if(player == 1)
	{
		if (xdistance == 2 && ydistance == 2)
		{
			if(xto - xfrom > 0)
			{
				if(CMCheckersBoard[yfrom + 1][xfrom + 1] != 4 || CMCheckersBoard[yfrom + 1][xfrom + 1] != 5)
				{
					cerr << "Error;  illegal move" << endl;
					return false;
				}
			}

			if(xto - xfrom < 0)
			{
				if (CMCheckersBoard[yfrom - 1][xfrom + 1] != 4 || CMCheckersBoard[yfrom - 1][xfrom + 1] != 5)
				{
					cerr << "Error;  illegal move" << endl;
					return false;
				}
			}
		}

		if (CMCheckersBoard[yfrom][xfrom] == 1 || CMCheckersBoard[yfrom][xfrom] == 2)
		{
			if (xdistance >= 1 && ydistance == 0)
			{
				cerr << "Error; illegal move" << endl;
				return false;
			}

			if (xdistance == 0 && ydistance >= 1)
			{
				cerr << "Error; illegal move" << endl;
				return false;
			}

			if (yto - yfrom < 0)
			{
				cerr << "Error; illegal move" << endl;
				return false;
			}
		}
	}


	if(player == 2)
	{
		if (xdistance == 2 && ydistance == 2)
		{
			if(xto - xfrom > 0)
			{
				if(CMCheckersBoard[yfrom + 1][xfrom - 1] != 1 || CMCheckersBoard[yfrom + 1][xfrom - 1] != 2)
				{
					cerr << "Error;  illegal move" << endl;
					return false;
				}
			}

			if(xto - xfrom < 0)
			{
				if (CMCheckersBoard[yfrom - 1][xfrom - 1] != 1 || CMCheckersBoard[yfrom - 1][xfrom - 1] != 2)
				{
					cerr << "Error;  illegal move" << endl;
					return false;
				}
			}
		}

		if (CMCheckersBoard[yfrom][xfrom] == 4 || CMCheckersBoard[yfrom][xfrom] == 5)
		{

			if (xdistance >= 1 && ydistance == 0)
			{
				cerr << "Error; illegal move" << endl;
				return false;
			}

			if (xdistance == 0 && ydistance >= 1)
			{
				cerr << "Error; illegal move" << endl;
				return false;
			}

			if (yto - yfrom > 0)
			{
				cerr << "Error; illegal move" << endl;
				return false;
			}
		}
	}

	if(IsMove1Square( CMCheckersBoard, numRowsInBoard, player, xfrom, yfrom) == true)
	{
		CMCheckersBoard[yto][xto] = CMCheckersBoard[yfrom][xfrom];
		CMCheckersBoard[yfrom][xfrom] = 0;
	}

	if (IsJump( CMCheckersBoard, numRowsInBoard, player, xfrom, yfrom) == true)
	{
		if(xto - xfrom > 0)
		{
			CMCheckersBoard[yfrom + 1][xfrom - 1] == 0;
		}

		if(xto - xfrom < 0)
		{
			CMCheckersBoard[yfrom - 1][xfrom - 1] == 0;
		}
		jumped = true;
	}

	if (player ==1)
	{
		if (yto == numRowsInBoard)
		{
			CMCheckersBoard[yto][xto] == 3;
		}

		if (CMCheckersBoard[yto][xto] != 2)
		{
			return true;
		}

		if (CMCheckersBoard[yto][xto] == 2)
		{
			cerr << "White has created a Mule King, Red wins the game" << endl;
			cerr << "Enter any character to terminate the game then press the enter key ";
			cin >> end;
			return 0;
		}
	}
}

bool CheckWin(int CMCheckersBoard[MAX_ARRAY_SIZE][MAX_ARRAY_SIZE], int numRowsInBoard)
{
	int whitemule = 0;
	int whitesol = 0;
	int whiteking = 0;

	int redmule = 0;
	int redsol = 0;
	int redking = 0;

	int i = 0;
	int j = 0;

	for (i = 0; i < numRowsInBoard; i++)
	{
		for (j = 0; j < numRowsInBoard; j++)
		{


			if (CMCheckersBoard[i][j] == 5)
			{
				redmule++;
			}

			if (CMCheckersBoard[i][j] == 2)
			{
				whitemule++;
			}

			if (CMCheckersBoard[i][j] == 1)
			{
				whitesol++;
			}

			if (CMCheckersBoard[i][j] == 3)
			{
				whiteking++;
			}

			if (CMCheckersBoard[i][j] == 4)
			{
				redsol++;
			}

			if (CMCheckersBoard[i][j] == 6)
			{
				redking++;
			}
		}
	}

	if (redmule == 0)
	{
		cout << "The Red Player has won the game by losing all of the Red Mules" << endl;
		return true;
	}

	else if (whitemule == 0)
	{
		cout << "The White Player has won the game by losing all of the White Mules" << endl;
		return true;
	}

	else if (redsol == 0 && redking == 0)
	{
		cout << "The White Player has won by capturing all of the red players soldiers and kings" << endl;
		return true;
	}

	else if (whitesol == 0 && whiteking == 0)
	{
		cout << "The Red Player has won by capturing all of the white players soldiers and kings" << endl;
		return true;
	}

	else 
	{
		return false;
	}
}

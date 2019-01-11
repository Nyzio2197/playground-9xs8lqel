// this is a 9x9 connect 4, where you can place the tokens anywhere on the board
// there is a single use, choice move "gravity" that shifts all the pieces on the board in a general direction
// have fun

import java.util.Scanner;

public class Connect4_MKIII extends Thread {
    public static Scanner in = new Scanner(System.in);
    public static String[][] board = new String[9][9];
    public static boolean player = true;
    public static boolean usedGravityPlayer1 = false;
    public static boolean usedGravityPlayer2 = false;
    public static boolean justUsedGravity = false;
    public static String p1 = "";
    public static String p2 = "";

    public static void main(String[] args) {
    	// print rules
    	printRules();
        // checks for custom play
        setPlay();
        // sets full board to " "
        for (int row = 0; row < board.length; row++)
            for (int column = 0; column < board[row].length; column++)
                board[row][column] = " ";

        // prints an empty board for reference
        printBoard();
        game: while (true) {
            // 2 players
            for (int n = 0; n < 2; n++) {
                // checks if the user wants to use their gravity move
                if ((player && !usedGravityPlayer1) || (!player && !usedGravityPlayer2)) {
                    System.out.print("  Would you like to use gravity? ");
                    if (in.next().substring(0, 1).equalsIgnoreCase("y")) {
                        useGravity();
                    }
                }
                // makes move if player did not use gravity
                if (!justUsedGravity) {
                    System.out.print("  Please input row and column ");
                    updateBoard(in.nextInt(), in.nextInt());
                }
                // if gravity was used, it clears it for the next player
                if (justUsedGravity) {
                	justUsedGravity = false;
                	if(player) {
                		usedGravityPlayer1 = true;
                	} else {
                		usedGravityPlayer2 = true;
                	}
                }
                // prints the new board updated with the newest play
                printBoard();
                // checks for a draw
                if (checkDraw()) {
                    System.out.println("  You're both bad at this");
                    break game;
                }
                // checks if there is a winner
                if (player) {
                    if (checkForWinner(p1)) {
                        System.out.println("  Player 1 won!");
                        break;
                    }
                } else {
                    if (checkForWinner(p2)) {
                        System.out.println("  Player 2 won!");
                        break;
                    }
                }
                // switches the players
                player = !player;
            }
        }
    }
    
    public static void printRules() {
    	System.out.println("\n\n\tHere are the rules for this game\n"
    					 + "\tYou need to get 4 in a row to win\n"
    					 + "\tYou can use gravity once per game\n"
    					 + "\tBEGIN!\n\n");
    }

    public static void setPlay() {
        if (p1 == "")
            p1 = "O";
        if (p2 == "")
            p2 = "X";
    }

    public static void printBoard() {
        System.out.println("    -4    -3    -2    -1     0     1     2     3     4");
        System.out.println("  +-----+-----+-----+-----+-----+-----+-----+-----+-----+");
        for (int n = board.length - 1; n >= 0; n--) {
            for (int x = 0; x < board[n].length; x++) {
                System.out.print("  |  " + board[n][x]);
            }
            System.out.println("  |  " + (n - 4));
            System.out.println("  +-----+-----+-----+-----+-----+-----+-----+-----+-----+");
        }
    }

    public static void useGravity() {
        while (true) {
            justUsedGravity = true;
            System.out.print("  What direction would you like to shift everything? ");
            String choiceOfDirection = in.next();
            if (choiceOfDirection.substring(0, 1).equalsIgnoreCase("U")) {
                gravityUp();
                break;
            } else if (choiceOfDirection.substring(0, 1).equalsIgnoreCase("D")) {
                gravityDown();
                break;
            } else if (choiceOfDirection.substring(0, 1).equalsIgnoreCase("L")) {
                gravityLeft();
                break;
            } else if (choiceOfDirection.substring(0, 1).equalsIgnoreCase("R")) {
                gravityRight();
                break;
            } else {
                System.out.println("  Please try again.");
            }
        }

    }
    
    public static void gravityUp() {
    	for(int column = 0; column < board[0].length; column++) {
    		column:
    		while(true) {
    			// finds topmost row
    			int topmostRow = board.length - 1;
    			boolean fullColumn = true;
    			for(int row = topmostRow; row >= 0; row--) {
    				if(board[row][column] == " ") {
    					topmostRow = row;
    					fullColumn = false;
    					break;
    				}
    			}
    			if(fullColumn)
    				break column;
    			// shifts everything up
    			for(int row = topmostRow; row >= 0; row--) {
    				if(board[row][column] != " ") {
    					// shifts to topmost row
    					board[topmostRow--][column] = board[row][column];
    					board[row][column] = " ";
    				}
    				// if hit wall, then move on to the next column
    				if(row == 0) {
    					break column;
    				}
    			}
    		}
    	}
    }

    public static void gravityDown() {
    	for(int column = 0; column < board[0].length; column++) {
    		column:
    		while(true) {
    			// finds bottommost row
    			int bottommostRow = 0;
    			boolean fullColumn = true;
    			for(int row = bottommostRow; row < board.length; row++) {
    				if(board[row][column] == " ") {
    					bottommostRow = row;
    					fullColumn = false;
    					break;
    				}
    			}
    			if(fullColumn)
    				break column;
    			// shifts everything down
    			for(int row = bottommostRow; row < board.length; row++) {
    				if(board[row][column] != " ") {
    					// shifts to topmost row
    					board[bottommostRow++][column] = board[row][column];
    					board[row][column] = " ";
    					bottommostRow %= 8;
    				}
    				// if hit wall, then move on to the next column
    				if(row == board.length - 1) {
    					break column;
    				}
    			}
    		}
    	}
    }

    public static void gravityLeft() {
        for(int row = board.length - 1; row >= 0; row--) {
            row: 
            while(true) {
                // finds the leftmost index
                int leftmostIndex = 0;
                boolean fullRow = true;
                for(int index = leftmostIndex; index < board[row].length; index++) {
                    if(board[row][index] == " ") {
                        leftmostIndex = index;
                        fullRow = false;
                        break;
                    }
                }
                if(fullRow)
                    break row;
                // shifts everything left
                for (int index = leftmostIndex; index < board[row].length; index++) {
                    if(board[row][index] != " ") {
                        // shifts to leftmost possible index
                        board[row][leftmostIndex++] = board[row][index];
                        board[row][index] = " ";
                        leftmostIndex %= 8;
                    }
                    // if hit wall, then move on to the next row;
                    if(index == board[row].length - 1) {
                        break row;
                    }
                }
            }
        }
    }

    public static void gravityRight() {
    	for(int row = board.length - 1; row >= 0; row--) {
            row: 
            while(true) {
                // finds the rightmost index
                int rightmostIndex = board[row].length - 1;
                boolean fullRow = true;
                for(int index = board[row].length - 1; index >= 0; index--) {
                    if(board[row][index] == " ") {
                        rightmostIndex = index;
                        fullRow = false;
                        break;
                    }
                }
                if(fullRow)
                    break row;
                // shifts everything right
                for(int index = board[row].length - 1; index >= 0; index--) {
                    if(board[row][index] != " ") {
                        // shifts to leftmost possible index
                        board[row][rightmostIndex--] = board[row][index];
                        board[row][index] = " ";
                    }
                    // if hit wall, then move on to the next row;
                    if(index == 0) {
                        break row;
                    }
                }
            }
        }
    }

    public static void updateBoard(int row, int column) {
        row += 4; column += 4;
        boolean notFinished = true;
        if (board[row][column] == " ") {
            if (player) {
                board[row][column] = p1;
            } else {
                board[row][column] = p2;
            }
            notFinished = false;
        }
        if (notFinished) {
            System.out.println("  INVALID! Please try again.");
            System.out.print("  Please input row and column");
            updateBoard(in.nextInt(), in.nextInt());
        }
    }

    public static boolean checkForWinner(String play) {
        boolean isWin = false;
        outerHorizontal: for (int a = 0; a < board.length; a++) {
            for (int b = 0; b < board[a].length - 3; b++) {
                if (board[a][b] == play && board[a][b + 1] == play && board[a][b + 2] == play
                        && board[a][b + 3] == play) {
                    isWin = true;
                    break outerHorizontal;
                }

            }
        }

        outerVertical: for (int x = 0; x < board.length - 3; x++) {
            for (int y = 0; y < board[x].length; y++) {
                if (board[x][y] == play && board[x + 1][y] == play && board[x + 2][y] == play
                        && board[x + 3][y] == play) {
                    isWin = true;
                    break outerVertical;
                }
            }
        }

        outerLeftDiagonal: for (int p = 0; p < board.length - 3; p++) {
            for (int o = 0; o < board[p].length - 3; o++) {
                if (board[p][o] == play && board[p + 1][o + 1] == play && board[p + 2][o + 2] == play
                        && board[p + 3][o + 3] == play) {
                    isWin = true;
                    break outerLeftDiagonal;
                }
            }
        }

        outerRightDiagonal: for (int u = 0; u < board.length - 3; u++) {
            for (int i = 3; i < board[u].length; i++) {
                if (board[u][i] == play && board[u + 1][i - 1] == play && board[u + 2][i - 2] == play
                        && board[u + 3][i - 3] == play) {
                    isWin = true;
                    break outerRightDiagonal;
                }
            }
        }

        return isWin;
    }

    public static boolean checkDraw() {
        if (checkForWinner(p1) && checkForWinner(p2)) {
            return true;
        }
        for (int n = 0; n < board.length; n++) {
            for (int a = 0; a < board[n].length; a++) {
                if (board[n][a] == " ")
                    return false;
            }
        }
        return true;
    }
}


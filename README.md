package lab_03;

import java.util.Scanner;
import java.util.Random;

public class Lab_03 {

    // 1. Variáveis "Globais"
    private static Scanner input = new Scanner(System.in);
    private static final char EMPTY = ' ';
    private static char[][] board;

    // 2. Método Principal (Main)
    public static void main(String[] args) {
        boolean end;
        boolean humanTurn = chooseOption(new String[]{"Primeiro humano", "Primeiro computador"}) == 1;
        boolean humanX = chooseOption(new String[]{"Humano X", "Humano 0"}) == 1;
        
        initializeBoard(readBoardSize());
        printlnBoard(false);
        
        do {
            System.out.println();
            humanTurn = play(humanTurn, humanX);
            printlnBoard(false);
            end = checkVictory();
            if (end) {
                System.out.println("Ganhou o " + (humanTurn ? "computador" : "humano") + "!");
            } else {
                end = noMoreMoves();
                if (end) {
                    System.out.println("Empataram!");
                }
            }
        } while (!end);
    }

    // --- NÍVEL 1: MENUS E TAMANHO ---
    public static int chooseOption(String[] options) {
        int option;
        boolean invalid;
        do {
            for (int i = 0; i < options.length; i++) {
                System.out.println((i + 1) + " - " + options[i]);
            }
            System.out.print("Escolha uma opção entre [1," + options.length + "]: ");
            option = input.nextInt();
            invalid = (option < 1 || option > options.length);
            if (invalid) System.out.println("A opção tem de ser [1," + options.length + "]!");
        } while (invalid);
        return option;
    }

    public static int readBoardSize() {
        int size;
        do {
            System.out.print("Indique um tamanho para o tabuleiro: ");
            size = input.nextInt();
        } while (size < 3);
        return size;
    }

    // --- NÍVEL 2: INICIALIZAÇÃO E POSIÇÕES ---
    public static void initializeBoard(int size) {
        board = new char[size][size];
        for (int i = 0; i < size; i++) {
            for (int j = 0; j < size; j++) board[i][j] = EMPTY;
        }
    }

    public static int getPosition(int row, int col) {
        return row * board.length + col + 1;
    }

    public static int[] getRowColumn(int pos) {
        return new int[]{(pos - 1) / board.length, (pos - 1) % board.length};
    }

    public static boolean isEmpty(int row, int col) {
        return board[row][col] == EMPTY;
    }

    public static boolean isEmpty(int pos) {
        int[] coords = getRowColumn(pos);
        return isEmpty(coords[0], coords[1]);
    }

    // --- NÍVEL 3: APRESENTAÇÃO (VISUAL) ---
    public static int getPositionMaxLength() {
        return String.valueOf(board.length * board.length).length();
    }

    public static String getPositionFormatter() {
        return "%0" + getPositionMaxLength() + "d";
    }

    public static void printlnBoard(boolean printPositions) {
        for (int i = 0; i < board.length; i++) {
            if (i > 0) printlnRowSeparator();
            for (int j = 0; j < board.length; j++) {
                if (j > 0) System.out.print("|");
                if (printPositions && isEmpty(i, j)) {
                    System.out.printf(getPositionFormatter(), getPosition(i, j));
                } else {
                    String fmt = "%" + getPositionMaxLength() + "c";
                    System.out.printf(fmt, board[i][j]);
                }
            }
            System.out.println();
        }
    }

    public static void printlnRowSeparator() {
        int len = getPositionMaxLength();
        for (int j = 0; j < board.length; j++) {
            if (j > 0) System.out.print("+");
            for (int k = 0; k < len; k++) System.out.print("-");
        }
        System.out.println();
    }

    // --- NÍVEL 4: JOGADAS ---
    public static boolean play(boolean humanTurn, boolean humanX) {
        char symbol = (humanTurn == humanX) ? 'X' : '0';
        int pos;
        if (humanTurn) {
            System.out.println("Escolha uma posição:");
            printlnBoard(true);
            do {
                System.out.print("> ");
                pos = input.nextInt();
            } while (pos < 1 || pos > board.length * board.length || !isEmpty(pos));
        } else {
            pos = getEmptyPositions()[new Random().nextInt(getEmptyPositionsCounter())];
        }
        int[] c = getRowColumn(pos);
        board[c[0]][c[1]] = symbol;
        return !humanTurn;
    }

    public static int[] getEmptyPositions() {
        int[] res = new int[getEmptyPositionsCounter()];
        int idx = 0;
        for (int i = 0; i < board.length; i++) {
            for (int j = 0; j < board.length; j++) {
                if (isEmpty(i, j)) res[idx++] = getPosition(i, j);
            }
        }
        return res;
    }

    public static int getEmptyPositionsCounter() {
        int count = 0;
        for (int i = 0; i < board.length; i++) {
            for (int j = 0; j < board.length; j++) if (isEmpty(i, j)) count++;
        }
        return count;
    }

    // --- NÍVEL 5: VITÓRIA E EMPATE ---
    public static boolean noMoreMoves() {
        return getEmptyPositionsCounter() == 0;
    }

    public static boolean checkVictory() {
        int n = board.length;
        // Linhas e Colunas
        for (int i = 0; i < n; i++) {
            if (checkLine(i, true) || checkLine(i, false)) return true;
        }
        // Diagonais
        return checkDiagonals();
    }

    private static boolean checkLine(int idx, boolean isRow) {
        char first = isRow ? board[idx][0] : board[0][idx];
        if (first == EMPTY) return false;
        for (int i = 1; i < board.length; i++) {
            if ((isRow ? board[idx][i] : board[i][idx]) != first) return false;
        }
        return true;
    }

    private static boolean checkDiagonals() {
        char d1 = board[0][0], d2 = board[0][board.length - 1];
        boolean v1 = d1 != EMPTY, v2 = d2 != EMPTY;
        for (int i = 1; i < board.length; i++) {
            if (board[i][i] != d1) v1 = false;
            if (board[i][board.length - 1 - i] != d2) v2 = false;
        }
        return v1 || v2;
    }
}

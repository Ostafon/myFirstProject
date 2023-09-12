# myFirstProject
package four;

import javax.swing.*;
import java.awt.*;
import java.awt.event.*;

public class ConnectFour extends JFrame {
    private final JButton[][] cells;
    private JButton resetButton;
    private char currentPlayer = 'X';
    private static final int ROWS_SIZE = 6;
    private static final int COLS_SIZE = 7;
    private boolean gameWon = false;
    private Color baselineColor = new Color(144, 238, 144); // Light green
    private Color winningColor = Color.RED; // Winning color

    public ConnectFour() {
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        setTitle("Connect Four");
        setSize(700, 800);


        JPanel panel = new JPanel(new GridLayout(ROWS_SIZE, COLS_SIZE));
        cells = new JButton[ROWS_SIZE][COLS_SIZE];

        for (int row = 0; row < ROWS_SIZE; row++) {
            for (int col = 0; col < COLS_SIZE; col++) {
                String label = String.format("%s%d", (char) ('A' + col), ROWS_SIZE - row);
                JButton button = new JButton(label);
                button.setName("Button" + label);
                button.setFocusPainted(false);
                button.setPreferredSize(new Dimension(100, 100));
                button.setFont(new Font("Arial", Font.PLAIN, 48));
                button.setText(" ");
                button.setBackground(baselineColor);
                button.addActionListener(new CellClickListener(col));
                cells[row][col] = button;
                panel.add(button);
            }
        }

        resetButton = new JButton("Reset");
        resetButton.setName("ButtonReset");
        resetButton.setEnabled(true);
        resetButton.addActionListener(new ResetClickListener());

        JPanel resetPanel = new JPanel(new FlowLayout(FlowLayout.CENTER));
        resetPanel.add(resetButton);

        setLayout(new BorderLayout());
        add(panel, BorderLayout.NORTH);
        add(resetPanel, BorderLayout.SOUTH);

        setLocationRelativeTo(null);
        setVisible(true);
    }

    private class CellClickListener implements ActionListener {
        private int column;

        public CellClickListener(int col) {
            this.column = col;
        }

        @Override
        public void actionPerformed(ActionEvent e) {
            if (gameWon) {
                return; // If the game is already won, no further moves are allowed
            }

            int emptyRow = -1;
            for (int row = ROWS_SIZE - 1; row >= 0; row--) {
                if (cells[row][column].getText().equals(" ")) {
                    emptyRow = row;
                    break;
                }
            }

            if (emptyRow != -1) {
                cells[emptyRow][column].setText(Character.toString(currentPlayer));

                int[] winPositions = checkWin(emptyRow, column);
                if (winPositions != null) {
                    gameWon = true;
                    highlightWinningCells(winPositions);
                } else {
                    currentPlayer = (currentPlayer == 'X') ? 'O' : 'X';
                }
            }
        }
    }


    private class ResetClickListener implements ActionListener {
        @Override
        public void actionPerformed(ActionEvent e) {
            for (int row = 0; row < ROWS_SIZE; row++) {
                for (int col = 0; col < COLS_SIZE; col++) {
                    JButton button = cells[row][col];
                    button.setText(" ");
                    button.setBackground(baselineColor);
                }
            }
            gameWon = false;
            currentPlayer = 'X';
        }
    }

    private int[] checkWin(int row, int col) {
        char symbol = cells[row][col].getText().charAt(0);
        int[] winPositions = null;

        // Check horizontally
        int[] winPositionsHorizontal = checkDirection(row, col, 0, 1, symbol);
        if (winPositionsHorizontal != null) {
            return winPositionsHorizontal;
        }

        // Check vertically
        int[] winPositionsVertical = checkDirection(row, col, 1, 0, symbol);
        if (winPositionsVertical != null) {
            return winPositionsVertical;
        }

        // Check diagonally (bottom-right to top-left)
        int[] winPositionsDiagonal1 = checkDirection(row, col, 1, 1, symbol);
        if (winPositionsDiagonal1 != null) {
            return winPositionsDiagonal1;
        }

        // Check diagonally (bottom-left to top-right)
        int[] winPositionsDiagonal2 = checkDirection(row, col, 1, -1, symbol);
        if (winPositionsDiagonal2 != null) {
            return winPositionsDiagonal2;
        }

        return null; // No win in any direction
    }

    private int[] checkDirection(int row, int col, int rowIncrement, int colIncrement, char symbol) {
        int[] winPositions = new int[8];
        int count = 0;

        while (row >= 0 && row < ROWS_SIZE && col >= 0 && col < COLS_SIZE) {
            if (cells[row][col].getText().charAt(0) == symbol) {
                winPositions[count * 2] = row;
                winPositions[count * 2 + 1] = col;
                count++;

                if (count == 4) {
                    return winPositions;
                }
            } else {
                // Reset the count and positions array
                count = 0;
                winPositions = new int[8];
            }

            row += rowIncrement;
            col += colIncrement;
        }

        return null; // No win in this direction
    }

    private void highlightWinningCells(int[] winPositions) {
        char symbol = cells[winPositions[0]][winPositions[1]].getText().charAt(0);

        // Highlight the winning cells with the winning color
        for (int i = 0; i < 4; i++) {
            int row = winPositions[i * 2];
            int col = winPositions[i * 2 + 1];
            cells[row][col].setBackground(winningColor);
        }
    }
}

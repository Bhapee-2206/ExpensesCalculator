# ExpensesCalculator
import javax.swing.*;
import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.util.HashMap;
import java.util.Map;

public class ExpensesCalculator extends JFrame implements ActionListener {
    private JTextField amountField, categoryField;
    private JTextArea summaryArea;
    private JButton addButton, clearButton;
    private Map<String, Double> expenses;

    public ExpensesCalculator() {
        setTitle("Expenses Calculator");
        setSize(500, 400);
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        setLocationRelativeTo(null);
        setLayout(new BorderLayout(10, 10));

        // Initialize expenses map
        expenses = new HashMap<>();

        // Create a gradient background panel
        JPanel mainPanel = new JPanel() {
            @Override
            protected void paintComponent(Graphics g) {
                super.paintComponent(g);
                Graphics2D g2d = (Graphics2D) g;
                g2d.setRenderingHint(RenderingHints.KEY_RENDERING, RenderingHints.VALUE_RENDER_QUALITY);
                GradientPaint gradient = new GradientPaint(0, 0, new Color(30, 30, 30), getWidth(), getHeight(), new Color(10, 10, 10));
                g2d.setPaint(gradient);
                g2d.fillRect(0, 0, getWidth(), getHeight());
            }
        };
        mainPanel.setLayout(new BorderLayout(10, 10));

        // Input Panel
        JPanel inputPanel = new JPanel(new GridLayout(3, 2, 10, 10));
        inputPanel.setOpaque(false);

        inputPanel.add(new JLabel("Amount:"));
        amountField = new JTextField();
        amountField.setFont(new Font("Arial", Font.PLAIN, 16));
        inputPanel.add(amountField);

        inputPanel.add(new JLabel("Category:"));
        categoryField = new JTextField();
        categoryField.setFont(new Font("Arial", Font.PLAIN, 16));
        inputPanel.add(categoryField);

        addButton = createGradientButton("Add Expense", new Color(0, 255, 136), new Color(0, 200, 100));
        addButton.addActionListener(this);
        inputPanel.add(addButton);

        clearButton = createGradientButton("Clear All", new Color(255, 59, 48), new Color(200, 40, 30));
        clearButton.addActionListener(this);
        inputPanel.add(clearButton);

        mainPanel.add(inputPanel, BorderLayout.NORTH);

        // Summary Panel
        JPanel summaryPanel = new JPanel(new BorderLayout());
        summaryPanel.setOpaque(false);

        summaryArea = new JTextArea();
        summaryArea.setFont(new Font("Arial", Font.PLAIN, 16));
        summaryArea.setEditable(false);
        summaryArea.setBackground(new Color(20, 20, 20));
        summaryArea.setForeground(Color.WHITE);
        summaryArea.setBorder(BorderFactory.createEmptyBorder(10, 10, 10, 10));

        JScrollPane scrollPane = new JScrollPane(summaryArea);
        scrollPane.setOpaque(false);
        scrollPane.getViewport().setOpaque(false);
        summaryPanel.add(scrollPane, BorderLayout.CENTER);

        mainPanel.add(summaryPanel, BorderLayout.CENTER);

        add(mainPanel);
    }

    private JButton createGradientButton(String text, Color startColor, Color endColor) {
        JButton button = new JButton(text) {
            @Override
            protected void paintComponent(Graphics g) {
                Graphics2D g2d = (Graphics2D) g;
                g2d.setRenderingHint(RenderingHints.KEY_ANTIALIASING, RenderingHints.VALUE_ANTIALIAS_ON);
                GradientPaint gradient = new GradientPaint(0, 0, startColor, getWidth(), getHeight(), endColor);
                g2d.setPaint(gradient);
                g2d.fillRoundRect(0, 0, getWidth(), getHeight(), 20, 20);
                super.paintComponent(g);
            }
        };
        button.setFont(new Font("Arial", Font.BOLD, 16));
        button.setForeground(Color.WHITE);
        button.setBorderPainted(false);
        button.setFocusPainted(false);
        button.setContentAreaFilled(false);
        return button;
    }

    @Override
    public void actionPerformed(ActionEvent e) {
        if (e.getSource() == addButton) {
            addExpense();
        } else if (e.getSource() == clearButton) {
            clearExpenses();
        }
    }

    private void addExpense() {
        try {
            double amount = Double.parseDouble(amountField.getText());
            String category = categoryField.getText().trim();

            if (category.isEmpty()) {
                JOptionPane.showMessageDialog(this, "Please enter a category!", "Error", JOptionPane.ERROR_MESSAGE);
                return;
            }

            expenses.put(category, expenses.getOrDefault(category, 0.0) + amount);
            updateSummary();
            amountField.setText("");
            categoryField.setText("");
        } catch (NumberFormatException ex) {
            JOptionPane.showMessageDialog(this, "Invalid amount!", "Error", JOptionPane.ERROR_MESSAGE);
        }
    }

    private void clearExpenses() {
        expenses.clear();
        updateSummary();
    }

    private void updateSummary() {
        StringBuilder summary = new StringBuilder("Expenses Summary:\n\n");
        double total = 0;

        for (Map.Entry<String, Double> entry : expenses.entrySet()) {
            summary.append(entry.getKey()).append(": $").append(String.format("%.2f", entry.getValue())).append("\n");
            total += entry.getValue();
        }

        summary.append("\nTotal: $").append(String.format("%.2f", total));
        summaryArea.setText(summary.toString());
    }

    public static void main(String[] args) {
        SwingUtilities.invokeLater(() -> {
            ExpensesCalculator calculator = new ExpensesCalculator();
            calculator.setVisible(true);
        });
    }
}

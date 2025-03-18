# ExpensesTrackerGUI
import javax.swing.*;
import java.awt.*;
import java.awt.event.ActionEvent;
import java.awt.event.ActionListener;
import java.util.ArrayList;

class Expense {
    private String description;
    private double amount;

    public Expense(String description, double amount) {
        this.description = description;
        this.amount = amount;
    }

    public String getDescription() {
        return description;
    }

    public double getAmount() {
        return amount;
    }

    @Override
    public String toString() {
        return "Description: " + description + ", Amount: $" + amount;
    }
}

public class ExpensesTrackerGUI extends JFrame {
    private ArrayList<Expense> expenses;
    private DefaultListModel<String> expenseListModel;
    private JList<String> expenseList;
    private JTextField descriptionField;
    private JTextField amountField;

    public ExpensesTrackerGUI() {
        expenses = new ArrayList<>();
        expenseListModel = new DefaultListModel<>();
        expenseList = new JList<>(expenseListModel);

        setTitle("Expenses Tracker");
        setSize(400, 300);
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        setLayout(new BorderLayout());

        // Input Panel
        JPanel inputPanel = new JPanel();
        inputPanel.setLayout(new GridLayout(3, 2));

        inputPanel.add(new JLabel("Description:"));
        descriptionField = new JTextField();
        inputPanel.add(descriptionField);

        inputPanel.add(new JLabel("Amount:"));
        amountField = new JTextField();
        inputPanel.add(amountField);

        JButton addButton = new JButton("Add Expense");
        addButton.addActionListener(new AddExpenseAction());
        inputPanel.add(addButton);

        JButton deleteButton = new JButton("Delete Expense");
        deleteButton.addActionListener(new DeleteExpenseAction());
        inputPanel.add(deleteButton);

        add(inputPanel, BorderLayout.NORTH);
        add(new JScrollPane(expenseList), BorderLayout.CENTER);
    }

    private class AddExpenseAction implements ActionListener {
        @Override
        public void actionPerformed(ActionEvent e) {
            String description = descriptionField.getText();
            String amountText = amountField.getText();

            if (description.isEmpty() || amountText.isEmpty()) {
                JOptionPane.showMessageDialog(ExpensesTrackerGUI.this, "Please enter both description and amount.", "Error", JOptionPane.ERROR_MESSAGE);
                return;
            }

            try {
                double amount = Double.parseDouble(amountText);
                Expense expense = new Expense(description, amount);
                expenses.add(expense);
                expenseListModel.addElement(expense.toString());
                descriptionField.setText("");
                amountField.setText("");
            } catch (NumberFormatException ex) {
                JOptionPane.showMessageDialog(ExpensesTrackerGUI.this, "Please enter a valid amount.", "Error", JOptionPane.ERROR_MESSAGE);
            }
        }
    }

    private class DeleteExpenseAction implements ActionListener {
        @Override
        public void actionPerformed(ActionEvent e) {
            int selectedIndex = expenseList.getSelectedIndex();
            if (selectedIndex != -1) {
                expenses.remove(selectedIndex);
                expenseListModel.remove(selectedIndex);
            } else {
                JOptionPane.showMessageDialog(ExpensesTrackerGUI.this, "Please select an expense to delete.", "Error", JOptionPane.ERROR_MESSAGE);
            }
        }
    }

    public static void main(String[] args) {
        SwingUtilities.invokeLater(() -> {
            ExpensesTrackerGUI tracker = new ExpensesTrackerGUI();
            tracker.setVisible(true);
        });
    }
}

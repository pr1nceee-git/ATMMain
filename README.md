import javax.swing.JOptionPane;

public class FinalProject {
    public static void main(String[] args) {

        ATMSystem display = new ATMSystem();

        String[] accountNames = {"<Empty>", "<Empty>"};
        double[] balances = {0.0, 0.0};
        int accountsCreated = 0;

        String choice = "";

        while (!choice.equalsIgnoreCase("X")) {

            choice = JOptionPane.showInputDialog(
                "My ATotM Machine\n" +
                "==== MAIN MENU ====\n" +
                "[0] Open Account\n" +
                "[1] Deposit\n" +
                "[2] Withdraw\n" +
                "[3] Display One Account\n" +
                "[4] Display ALL Accounts\n" +
                "[5] Transfer Money\n" +
                "[X] Exit\n\n" +
                "Please select an option:"
            );

            if (choice == null) {
                display.exitMessage();
                break;
            }

            // OPEN ACCOUNT
            if (choice.equals("1")) {

                if (accountsCreated >= 2) {
                    JOptionPane.showMessageDialog(null,
                        "Maximum of 2 accounts reached!\nDelete an account to open a new one.");
                    continue;
                }

                String nameInput = JOptionPane.showInputDialog("Enter Full Name:");

                if (nameInput == null || nameInput.trim().isEmpty()) {
                    JOptionPane.showMessageDialog(null, "Name cannot be empty!");
                    continue;
                }

                accountNames[accountsCreated] = nameInput.trim();
                balances[accountsCreated] = 5000.00; // initial deposit
                accountsCreated++;

                JOptionPane.showMessageDialog(null,
                    "Account successfully created!\n" +
                    "Account Holder: " + nameInput + "\n" +
                    "Initial Balance: ₱5000.00");

                continue;
            }

            // REQUIRE ACCOUNT BEFORE OPERATIONS
            if (accountsCreated == 0 && !choice.equalsIgnoreCase("X")) {
                JOptionPane.showMessageDialog(null,
                    "No accounts created yet!\nPlease open at least one account.");
                continue;
            }

            // DEPOSIT
            if (choice.equals("2")) {

                int accIndex = display.chooseAccount(accountsCreated);
                if (accIndex == -1) continue;

                String inputAmount = JOptionPane.showInputDialog("Enter amount to deposit:");

                if (inputAmount == null) continue;

                try {
                    double amount = Double.parseDouble(inputAmount);
                    if (amount > 0) {
                        balances[accIndex] += amount;
                        display.depositSuccess();
                    } else {
                        display.invalidAmount();
                    }
                } catch (Exception e) {
                    display.invalidAmount();
                }
            }

            // WITHDRAW
            else if (choice.equals("3")) {

                int accIndex = display.chooseAccount(accountsCreated);
                if (accIndex == -1) continue;

                String inputAmount = JOptionPane.showInputDialog("Enter amount to withdraw:");

                if (inputAmount == null) continue;

                try {
                    double amount = Double.parseDouble(inputAmount);
                    if (amount > 0 && amount <= balances[accIndex]) {
                        balances[accIndex] -= amount;
                        display.withdrawSuccess();
                    } else {
                        display.insufficientFunds();
                    }
                } catch (Exception e) {
                    display.invalidAmount();
                }
            }

            // DISPLAY ONE ACCOUNT
            else if (choice.equals("4")) {

                int accIndex = display.chooseAccount(accountsCreated);
                if (accIndex == -1) continue;

                display.showDetails(accountNames[accIndex], balances[accIndex]);
            }

            // DISPLAY ALL ACCOUNTS
            else if (choice.equals("5")) {
                display.showAllAccounts(accountNames, balances, accountsCreated);
            }

            // TRANSFER MONEY
            else if (choice.equals("6")) {

                if (accountsCreated < 2) {
                    JOptionPane.showMessageDialog(null,
                        "You need 2 accounts to transfer money!");
                    continue;
                }

                int from = display.chooseSpecificAccount("Transfer FROM which account?");
                if (from == -1) continue;

                int to = (from == 0) ? 1 : 0;

                String inputAmount = JOptionPane.showInputDialog(
                        "Enter amount to transfer from " +
                        accountNames[from] + " → " + accountNames[to] + ":");

                if (inputAmount == null) continue;

                try {
                    double amount = Double.parseDouble(inputAmount);

                    if (amount > 0 && amount <= balances[from]) {
                        balances[from] -= amount;
                        balances[to] += amount;

                        display.transferSuccess(accountNames[from], accountNames[to], amount);
                    } else {
                        display.insufficientFunds();
                    }

                } catch (Exception e) {
                    display.invalidAmount();
                }
            }

           
            // EXIT
            else if (choice.equalsIgnoreCase("X")) {
                display.exitMessage();
            }

            else {
                display.invalidOption();
            }
        }
    }
}

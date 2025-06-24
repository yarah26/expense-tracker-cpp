#include <iostream>
#include <fstream>
#include <string>
#include <stdexcept>

using namespace std;

class Expense {
private:
    string category;
    string date;
    double amount;

public:
    Expense() : category(""), date(""), amount(0.0) {}
    Expense(const string& category, const string& date, double amount)
        : category(category), date(date), amount(amount) {
    }

    string getCategory() const { return category; }
    string getDate() const { return date; }
    double getAmount() const { return amount; }

    void display() const {
        cout << "Date: " << date << " | Category: " << category << " | Amount: " << amount << " Ft" << endl;
    }

    string toFileString() const {
        return date + "," + category + "," + to_string(amount);
    }

    static Expense fromFileString(const string& line) {
        size_t pos1 = line.find(',');
        size_t pos2 = line.find(',', pos1 + 1);

        string date = line.substr(0, pos1);
        string category = line.substr(pos1 + 1, pos2 - pos1 - 1);
        double amount = stod(line.substr(pos2 + 1));

        return Expense(category, date, amount);
    }
};

class ExpenseTracker {
private:
    Expense** expenses;
    int count;
    int capacity;

    void resize() {
        int newCapacity = capacity * 2;
        Expense** newArray = new Expense * [newCapacity];
        for (int i = 0; i < count; ++i) {
            newArray[i] = expenses[i];
        }
        delete[] expenses;
        expenses = newArray;
        capacity = newCapacity;
    }

public:
    ExpenseTracker() {
        count = 0;
        capacity = 10;
        expenses = new Expense * [capacity];
    }

    ~ExpenseTracker() {
        for (int i = 0; i < count; ++i) {
            delete expenses[i];
        }
        delete[] expenses;
    }

    void addExpense(const string& category, const string& date, double amount) {
        if (amount < 0) throw invalid_argument("Amount cannot be negative.");
        if (count >= capacity) resize();
        expenses[count++] = new Expense(category, date, amount);
    }

    void viewExpenses() const {
        if (count == 0) {
            cout << "No expenses recorded.\n";
            return;
        }
        for (int i = 0; i < count; ++i) {
            expenses[i]->display();
        }
    }

    void viewTotalSpentByMonth(const string& yearMonth) const {
        double total = 0;
        for (int i = 0; i < count; ++i) {
            if (expenses[i]->getDate().substr(0, 7) == yearMonth &&
                expenses[i]->getCategory() != "Income") {
                total += expenses[i]->getAmount();
            }
        }
        cout << "Total spent in " << yearMonth << ": " << total << " Ft" << endl;
    }

    void saveToFile(const string& filename) const {
        ofstream outFile(filename);
        if (!outFile) throw ios_base::failure("Cannot open file for writing.");
        for (int i = 0; i < count; ++i) {
            outFile << expenses[i]->toFileString() << endl;
        }
        outFile.close();
    }

    void loadFromFile(const string& filename) {
        ifstream inFile(filename);
        if (!inFile) return;

        string line;
        while (getline(inFile, line)) {
            Expense e = Expense::fromFileString(line);
            addExpense(e.getCategory(), e.getDate(), e.getAmount());
        }
        inFile.close();
    }
};

string chooseCategory() {
    int choice;
    cout << "Choose a category:\n";
    cout << "1. Bills\n";
    cout << "2. Entertainment\n";
    cout << "3. Food\n";
    cout << "4. Transportation\n";
    cout << "Enter your choice (1-4): ";
    cin >> choice;

    switch (choice) {
    case 1: return "Bills";
    case 2: return "Entertainment";
    case 3: return "Food";
    case 4: return "Transportation";
    default: cout << "Invalid choice. Defaulting to 'Other'\n"; return "Other";
    }
}

int main() {
    ExpenseTracker tracker;
    int choice;
    string category, date, filename = "expenses.txt";
    double amount;

    try {
        tracker.loadFromFile(filename);
    }
    catch (...) {
        cout << "Could not load previous data. Starting fresh.\n";
    }

    do {
        cout << "\n1. Add Expense\n2. Add Income\n3. View All Records\n4. View Total Spent by Month\n5. Save & Exit\nChoose an option: ";
        cin >> choice;

        switch (choice) {
        case 1:
            category = chooseCategory();
            cout << "Enter date (YYYY-MM-DD): ";
            cin >> date;
            cout << "Enter amount: ";
            cin >> amount;
            try {
                tracker.addExpense(category, date, amount);
            }
            catch (const exception& e) {
                cout << "Error: " << e.what() << endl;
            }
            break;

        case 2:
            cout << "Enter date (YYYY-MM-DD): ";
            cin >> date;
            cout << "Enter income amount: ";
            cin >> amount;
            try {
                tracker.addExpense("Income", date, amount);
            }
            catch (const exception& e) {
                cout << "Error: " << e.what() << endl;
            }
            break;

        case 3:
            tracker.viewExpenses();
            break;

        case 4: {
            string yearMonth;
            cout << "Enter year and month (YYYY-MM): ";
            cin >> yearMonth;
            tracker.viewTotalSpentByMonth(yearMonth);
            break;
        }

        case 5:
            try {
                tracker.saveToFile(filename);
                cout << "Data saved. Exiting.\n";
            }
            catch (const exception& e) {
                cout << "Error saving file: " << e.what() << endl;
            }
            break;

        default:
            cout << "Invalid option.\n";
        }

    } while (choice != 5);

    return 0;
}

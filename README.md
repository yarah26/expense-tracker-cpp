C++ Console Expense Tracker
A simple yet effective command-line expense tracker application written in C++. This tool allows users to add, view, and manage their daily expenses and income, save the data to a file, and load it back.

Features
Add Expenses: Record new expenses with a specific date, amount, and category.

Add Income: Record incoming funds.

Categorize Spending: Assign expenses to predefined categories (Bills, Entertainment, Food, Transportation).

View All Records: Display a complete list of all recorded expenses and income.

Monthly Summary: Calculate and display the total amount spent in a specific month and year.

Data Persistence: Automatically save all records to a expenses.txt file and load them upon startup.

Dynamic Memory: The application dynamically adjusts memory allocation for the list of expenses.

Getting Started
Prerequisites
To compile and run this project, you will need a C++ compiler (like g++).

Compilation
Clone the repository or save the main.cpp file to your local machine.

Open a terminal or command prompt.

Navigate to the directory where you saved the file.

Compile the code using g++:

g++ main.cpp -o ExpenseTracker -std=c++11

Running the Application
After successful compilation, an executable file named ExpenseTracker (or ExpenseTracker.exe on Windows) will be created. Run it from the terminal:

./ExpenseTracker

How to Use
Once the application is running, you will be greeted with a menu of options:

1. Add Expense
2. Add Income
3. View All Records
4. View Total Spent by Month
5. Save & Exit
Choose an option:

Add Expense: Prompts you to choose a category, and then enter the date (in YYYY-MM-DD format) and the amount.

Add Income: Prompts you to enter the date and the income amount.

View All Records: Lists all transactions, showing the date, category, and amount for each.

View Total Spent by Month: Asks for a year and month (in YYYY-MM format) and calculates the total expenses for that period (excluding income).

Save & Exit: Saves all current data to expenses.txt and closes the application.

File Structure
The application will automatically create and manage a file named expenses.txt in the same directory where the executable is located. This file stores your expense and income data in a comma-separated format (date,category,amount).

Example expenses.txt content:

2024-07-21,Food,15.75
2024-07-22,Income,1200.00
2024-07-23,Bills,85.50

Code Structure
The program is built around two main classes:

Expense: Represents a single financial transaction, holding its date, category, and amount.

ExpenseTracker: Manages a collection of Expense objects. It handles adding new expenses, displaying records, calculating totals, and managing file I/O.

The main() function contains the user interface logic, presenting the menu and handling user input.

# Expense-Tracker
- A user can plug expense tracker - This tool fetches data from the DB and tracks the expenses - An intelligent model gives analytics based on the expenses
import pandas as pd
import matplotlib.pyplot as plt

# Load CSV
df = pd.read_csv("expense_data_1.csv")

# Make sure date column is in datetime format
df['date'] = pd.to_datetime(df['date'], errors='coerce')

# Separate Income and Expenses
expenses = df[df['income/expenses'].str.lower() == 'expense']
income = df[df['income/expenses'].str.lower() == 'income']

# -----------------------------
# 1. Summary Stats
# -----------------------------
print("=== Summary ===")
print(f"Total Income: ₹{income['inr'].sum():,.2f}")
print(f"Total Expenses: ₹{expenses['inr'].sum():,.2f}")
print(f"Net Savings: ₹{income['inr'].sum() - expenses['inr'].sum():,.2f}")

# -----------------------------
# 2. Expenses by Category
# -----------------------------
category_expenses = expenses.groupby("category")["inr"].sum().sort_values(ascending=False)
print("\n=== Expenses by Category ===")
print(category_expenses)

plt.figure(figsize=(6,6))
category_expenses.plot(kind='pie', autopct='%1.1f%%')
plt.title("Expenses by Category")
plt.ylabel("")  # Hide y-label
plt.show()

# -----------------------------
# 3. Income vs Expenses Over Time
# -----------------------------
daily_summary = df.groupby(["date", "income/expenses"])["inr"].sum().unstack(fill_value=0)
daily_summary['Net'] = daily_summary.get('Income', 0) - daily_summary.get('Expense', 0)

print("\n=== Daily Summary (sample) ===")
print(daily_summary.head())

plt.figure(figsize=(10,6))
plt.plot(daily_summary.index, daily_summary.get('Income', 0), label="Income", marker='o')
plt.plot(daily_summary.index, daily_summary.get('Expense', 0), label="Expenses", marker='o')
plt.plot(daily_summary.index, daily_summary['Net'], label="Net Savings", linestyle='--')
plt.title("Income vs Expenses Over Time")
plt.xlabel("Date")
plt.ylabel("INR")
plt.legend()
plt.grid(True)
plt.show()

# -----------------------------
# 4. Expenses by Account
# -----------------------------
account_expenses = expenses.groupby("account")["inr"].sum().sort_values(ascending=False)
print("\n=== Expenses by Account ===")
print(account_expenses)

plt.figure(figsize=(8,5))
account_expenses.plot(kind='bar', color="tomato")
plt.title("Expenses by Account")
plt.ylabel("INR")
plt.show()

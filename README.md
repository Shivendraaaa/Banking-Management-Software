import threading
import time       
import sys #  these 3 modules are for importing  loading animation
import uuid # module for generating random user-id

class Loan:
    def __init__(self, loan_type, interest_rate, max_duration):
        self.loan_type = loan_type
        self.interest_rate = interest_rate
        self.max_duration = max_duration

class Customer:
    def __init__(self, name, account_type, initial_balance, address, contact_number, email, account_number, user_id=None):
        self.account_number = account_number
        self.user_id = user_id if user_id else str(uuid.uuid4())  # Generate a unique ID if not provided
        self.name = name
        self.account_type = account_type
        self.balance = initial_balance
        self.address = address
        self.contact_number = contact_number
        self.email = email
        self.loans = []

    def show_balance(self):
        return f"Account Number: {self.account_number}, User ID: {self.user_id}, Name: {self.name}, Balance: RM {self.balance}"

    def deposit(self, amount):
        self.balance += amount
        print(f"Amount {amount} deposited successfully. Current balance: RM {self.balance}")

    def withdraw(self, amount):
        if self.account_type == "Current" and self.balance - amount < -50000:
            surcharge = abs(self.balance - amount) * 0.06
            print(f"Withdrawal amount exceeds limit. Surcharge applied: RM {surcharge}")
            self.balance -= surcharge
        elif self.account_type == "Saving" and self.balance - amount < 50:
            surcharge = abs(self.balance - amount) * 0.06
            print(f"Withdrawal amount leads to a fine. Surcharge applied: RM {surcharge}")
            self.balance -= surcharge
        else:
            self.balance -= amount
            print(f"Amount {amount} withdrawn successfully. Current balance: RM {self.balance}")

    def calculate_interest(self, rate):
        return self.balance * rate / 100

    def add_interest(self, rate):
        interest = self.calculate_interest(rate)
        self.balance += interest
        print(f"Interest added: RM {interest}. Current balance: RM {self.balance}")

    def monthly_balance_sheet(self):
        print(f"\nMonthly Balance Sheet for {self.name}")
        print(f"Account Type: {self.account_type}")
        print(f"Current Balance: RM {self.balance}")
        print(f"Address: {self.address}")
        print(f"Contact Number: {self.contact_number}")
        print(f"Email: {self.email}")
        print("-------------------------------")

class LoanManagementSystem:
    def __init__(self):
        self.customers = []
        self.loans = {
            1: Loan("Car Loan", 0.06, 60),
            2: Loan("Two Wheeler Loan", 0.04, 60),
            3: Loan("Home Loan", 0.08, 300),
            4: Loan("Maintenance Loan", 0.05, 60),
            5: Loan("Personal Loan", 0.1, 60)
        }

    def add_customer(self, customer):
        self.customers.append(customer)

    def show_all_balances(self):
        for customer in self.customers:
            print(customer.show_balance())

    def apply_loan(self, customer, loan_type):
        if loan_type in self.loans:
            loan = self.loans[loan_type]
            customer.loans.append(loan)
            print(f"Loan approved for {customer.name}. Details:")
            print(f"  Loan Type: {loan.loan_type}")
            print(f"  Interest Rate: {loan.interest_rate * 100}%")
            print(f"  Maximum Duration: {loan.max_duration} months")
        else:
            print("Invalid loan type.")

def delete_account(bank, customer):
    bank.customers.remove(customer)
    print(f"Customer {customer.name}'s account deleted successfully.")

class Bank:
    def __init__(self):
        self.customers = []

    def add_customer(self, customer):
        self.customers.append(customer)

    def delete_customer(self, customer):
        self.customers.remove(customer)
        print(f"Customer {customer.name}'s account deleted successfully.")

    def issue_debit_card(self, customer):
        print("Debit card issued. Charge: RM 10 per Quarter.")
        customer.balance -= 10

    def opt_text_message_service(self, customer):
        print("Text message service opted. Charge: RM 5 per Quarter.")
        customer.balance -= 5

    def conclude_monthly_balance_sheets(self):
        for customer in self.customers:
            customer.monthly_balance_sheet()

    def display_extra_details(self, customer):
        print("\nExtra Customer Details:")
        print(f"Account Type: {customer.account_type}")
        print(f"Current Balance: RM {customer.balance}")
        print("-------------------------------")

def display_menu():
    print("******************************************")
    print("*               Main Menu                 *")
    print("******************************************")
    print("* 1. Add Customer                         *")
    print("* 2. Perform Transactions                 *")
    print("* 3. Calculate Interest                   *")
    print("* 4. Monthly Balance Sheets               *")
    print("* 5. Debit Cards & Text Service           *")
    print("* 6. Extra Customer Details               *")
    print("* 7. Apply for a Loan                     *")
    print("* 8. Show All Balances                    *")
    print("* 9. Delete Customer Account              *")
    print("* 10. Display All Information             *")
    print("*     (Administrator Only)                *")
    print("* 11. Exit                                *")
    print("*******************************************")
    return input("Enter your choice (1-11): ")

def loading_animation():
    print("Initializing...")
    for _ in range(6):  # Adjust the range based on the desired loading duration
        sys.stdout.write('\rLoading Banking... |')
        time.sleep(0.2)
        sys.stdout.write('\rLoading Banking System... /')
        time.sleep(0.2)
        sys.stdout.write('\rLoading Banking System... -')
        time.sleep(0.2)
        sys.stdout.write('\rLoading Banking System... \\')
        time.sleep(0.2)
    sys.stdout.write('\rLoading... Done!\n')

def automatic_loading():
    global loading_thread
    loading_thread = threading.Thread(target=loading_animation)
    loading_thread.start()

if __name__ == "__main__":
    automatic_loading()

    # Simulate some time-consuming initialization
    time.sleep(2)

    loading_thread.join()

    # Continue with the rest of your code...
    bank = Bank()
    loan_system = LoanManagementSystem()


while True:
    try:
        choice = display_menu()

        if choice == '1':
            name = input("Enter customer name: ")
            account_type = input("Enter account type (Current/Saving): ").capitalize()
            initial_balance = float(input("Enter initial balance (RM): "))
            address = input("Enter customer address: ")
            contact_number = input("Enter customer contact number: ")
            email = input("Enter customer email: ")
            generate_id = input("Do you want to generate a unique ID? (yes/no): ").lower()
            
            if generate_id == 'yes':
                user_id = str(uuid.uuid4())  # Generate a unique ID
            else:
                user_id = input("Enter user ID: ")

            account_number = input("Enter customer account number: ")

            new_customer = Customer(name, account_type, initial_balance, address, contact_number, email, account_number, user_id)
            bank.add_customer(new_customer)
            loan_system.add_customer(new_customer)
            print(f"Customer {name} added successfully. Account Number: {account_number}, User ID: {user_id}")

        elif choice == '2':
            if not bank.customers:
                print("No customers available. Please add a customer first.")
            else:
                print("\nChoose a customer:")
                for i, customer in enumerate(bank.customers, 1):
                    print(f"{i}. {customer.name}")

                customer_choice = int(input("Enter the number of the customer: "))
                if 1 <= customer_choice <= len(bank.customers):
                    current_customer = bank.customers[customer_choice - 1]
                    amount = float(input(f"Enter transaction amount for {current_customer.name} (RM): "))
                    transaction_type = input("Enter transaction type (deposit/withdraw): ").lower()
                    if transaction_type == 'deposit':
                        current_customer.deposit(amount)
                    elif transaction_type == 'withdraw':
                        current_customer.withdraw(amount)
                else:
                    print("Invalid customer choice.")

        elif choice == '3':
            if not bank.customers:
                print("No customers available. Please add a customer first.")
            else:
                print("\nChoose a customer:")
                for i, customer in enumerate(bank.customers, 1):
                    print(f"{i}. {customer.name}")

                customer_choice = int(input("Enter the number of the customer: "))
                if 1 <= customer_choice <= len(bank.customers):
                    current_customer = bank.customers[customer_choice - 1]
                    rate = float(input(f"Enter interest rate for {current_customer.name} (%): "))
                    current_customer.add_interest(rate)
                else:
                    print("Invalid customer choice.")

        elif choice == '4':
            if not bank.customers:
                print("No customers available. Please add a customer first.")
            else:
                bank.conclude_monthly_balance_sheets()

        elif choice == '5':
            if not bank.customers:
                print("No customers available. Please add a customer first.")
            else:
                print("\nChoose a customer:")
                for i, customer in enumerate(bank.customers, 1):
                    print(f"{i}. {customer.name}")

                customer_choice = int(input("Enter the number of the customer: "))
                if 1 <= customer_choice <= len(bank.customers):
                    current_customer = bank.customers[customer_choice - 1]
                    if input(f"Issue debit card for {current_customer.name}? (yes/no): ").lower() == 'yes':
                        bank.issue_debit_card(current_customer)
                    if input(f"Opt for text message service for {current_customer.name}? (yes/no): ").lower() == 'yes':
                        bank.opt_text_message_service(current_customer)
                else:
                    print("Invalid customer choice.")

        elif choice == '6':
            if not bank.customers:
                print("No customers available. Please add a customer first.")
            else:
                print("\nChoose a customer:")
                for i, customer in enumerate(bank.customers, 1):
                    print(f"{i}. {customer.name}")

                customer_choice = int(input("Enter the number of the customer: "))
                if 1 <= customer_choice <= len(bank.customers):
                    current_customer = bank.customers[customer_choice - 1]
                    bank.display_extra_details(current_customer)
                else:
                    print("Invalid customer choice.")

        elif choice == '7':
            if not bank.customers or not loan_system.loans:
                print("No customers or loans available. Please add a customer and loans first.")
            else:
                print("\nCustomers:")
                for i, customer in enumerate(bank.customers, 1):
                    print(f"{i}. {customer.name}")

                customer_choice = int(input("Enter the number of the customer: "))
                if 1 <= customer_choice <= len(bank.customers):
                    current_customer = bank.customers[customer_choice - 1]
                    
                    print("\nLoan Types:")
                    for loan_type, loan_details in loan_system.loans.items():
                        print(f"{loan_type}. {loan_details.loan_type}")

                    loan_type = int(input("Enter the type of loan (1-5): "))
                    loan_system.apply_loan(current_customer, loan_type)
                else:
                    print("Invalid customer choice.")

        elif choice == '8':
            if not bank.customers:
                print("No customers available. Please add a customer first.")
            else:
                account_number = input("Enter your account number: ")
                found_customer = None

                for customer in bank.customers:
                    if customer.account_number == account_number:
                        found_customer = customer
                        break

                if found_customer:
                    print(found_customer.show_balance())
                else:
                    print(f"No customer found with account number {account_number}.")


        elif choice == '9':
            if not bank.customers:
                print("No customers available. Please add a customer first.")
            else:
                print("\nChoose a customer to delete:")
                for i, customer in enumerate(bank.customers, 1):
                    print(f"{i}. {customer.name}")

                customer_choice = int(input("Enter the number of the customer to delete: "))
                if 1 <= customer_choice <= len(bank.customers):
                    current_customer = bank.customers[customer_choice - 1]
                    bank.delete_customer(current_customer)
                else:
                    print("Invalid customer choice.")
                    
        elif choice == '10':
            admin_password = input("Enter the administrator password: ")
            if admin_password == "admin123":
                print("\nAll Information (Administrator Only):")
                for customer in bank.customers:
                    print(f"\nCustomer: {customer.name}")
                    print(f"Account Type: {customer.account_type}")
                    print(f"Current Balance: RM {customer.balance}")
                    print(f"Address: {customer.address}")
                    print(f"Contact Number: {customer.contact_number}")
                    print(f"Email: {customer.email}")
                    print(f"Loans: {', '.join([loan.loan_type for loan in customer.loans])}")
                    print("-------------------------------")
            else:
                print("Incorrect administrator password. Access denied.")
            

        elif choice == '11':
            print("Exiting the program. Thank you!")
            break

        else:
            print("Invalid choice. Please enter a number between 1 and 10.")

    except ValueError as e:
        print(f"Error: {e}")

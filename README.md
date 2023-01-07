# OOPCapstone
This Python program will read data from a text file called inventory.txt and will perform different operations to allow users to display a detailed inventory of the company products and to edit, add, delete, and search the contents of this inventory.
## Installation
You will need PyCharm to run this project.\
Copy the files inventory.py and inventory.yxy to the root file of your new project in PyCharm
## Usage
For the development of this software, one class, 7 functions, and a menu were created. \
The components will be described below highlighting their purpose.
### Shoes class
```Python
class Shoes:
    """
        Class constructor to initialise the following attributes:
            ● country = str
            ● code = str
            ● product = str
            ● cost = str
            ● quantity = str
    """
    def __init__(self, country, code, product, cost, quantity):
        self.country = country
        self.code = code
        self.product = product
        self.cost = cost
        self.quantity = quantity

    def get_cost(self):
        """
           Function to return the calculated value of the shoes.
        """
        value = int(self.cost) * int(self.quantity)
        return value

    def get_quantity(self):
        """
            Function to return the quantity of the shoes.
        """
        return self.quantity

    def __str__(self):
        """
            function to return a string representation of the class.
        """
        return f"{self.country:<16}{self.code:<12}{self.product:<20}{'£{:,.2f}'.format(int(self.cost)):<16}" \
               f"{self.quantity:<17}"

    def to_file(self):
        """
            function to return class attributes to be saved in file.
        """
        return f"{self.country},{self.code},{self.product},{self.cost},{self.quantity}"
```
### Read data function
```Python
def read_shoes_data():
    """
        Function to open the file inventory.txt
        and read the data from this file, then create a shoes object with this data
        and append this object into the shoes list. One line in this file represents
        data to create one object of shoes. Try-except in this function
        for error handling.
        for loop skip the first line on the test file.
    """
    product_list = []
    try:
        with open('./inventory.txt', 'r', encoding='utf-8-sig') as file:

            # for loop to iterate the lines in text file and strips any white spaces
            # and append the result into a new list
            for lines in file:

                inventory = lines.strip()
                product_list.append(inventory)

            # for lop to iterate the new list and split line where a comma is
            # the split line will be store in five variables to crate a Shoes object per each line
            # then will be store in the shoe list as a list of objects
            for products in product_list:

                country, code, product, cost, quantity = products.split(",")
                shoes_object = Shoes(country, code, product, cost, quantity)
                shoe_list.append(shoes_object)

    except FileNotFoundError as error:

        print("\nThe file that you are trying to open does not exist")
        print(error)
        print()
```
### Add product to inventory
```Python
def capture_shoes():
    """
        Function to allow a user to enter/capture data
        about a shoe and use this data to create a shoe object
        and append this object inside the shoe list.
        Try-except in this function for error handling.
    """
    print("Register a new shoe product: ")

    # declaring five variables to take in user input
    country = input("Enter product country of origin: ").title()
    code = input("Enter product code: ").upper()
    product = input("Enter product model: ").title()
    cost = input("Enter product cost: ")
    quantity = input("Enter product quantity: ")
    file = None
    try:

        # write a new string on file in separate lines
        file = open('./inventory.txt', 'a', encoding='utf-8-sig')
        file.write("\n".join([country + "," + code + "," + product + "," + cost + "," + quantity]) + "\n")

    except FileNotFoundError as error:

        print("\nThe file that you are trying to open does not exist")
        print(error)
        print()

    finally:
        if file is not None:
            # prints user-friendly message and close the file
            print("\nData successfully saved!\n")
            file.close()
```
### View all products
```Python
def view_all():
    """
        Function to iterate over the shoes list and
        print the details of the shoes returned from the __str__
        function skipping index 0 in a user-friendly format.
    """
    print("\nShoes Inventory:")
    print("="*90)
    print(f"Number\t\t\tCountry\t\t\tCode\t\tProduct\t\t\t\tCost\t\t\tQuantity")
    print("=" * 90)

    # for loop to iterate the items in shoe_list and return the result skipping index 0.
    for num, shoes in enumerate(shoe_list[1:], start=1):
        print(f"{num:<16}{shoes}")
    print("=" * 90)
    print()
```
### Re-stock a product
```Python
def re_stock():
    """
        Function to find the shoe object with the lowest quantity,
        which is the shoe to be re-stocked. takes user input to add a new
        quantity of shoes and then update it.
        File to be saved with new data.
        Try-except in this function for error handling.
    """
    position = 0
    product_quantity = []

    # for loop to iterate the list of objects skipping index 0 and enumerating the objects in list
    # append the results into a new list as integers
    for num, shoe in enumerate(shoe_list[1:]):
        product_quantity.append(int(shoe.get_quantity()))

        # conditional statement to check if the quantity of the object is equals to the minimum quantity
        # stored in new list and add 1 to the position
        if int(shoe.get_quantity()) == min(product_quantity):
            position = num + 1

    # prints in a user-friendly format the product description with the lowest quantity
    print("\nProduct description:")
    print("="*90)
    print(f"Number\t\t\tCountry\t\t\tCode\t\tProduct\t\t\t\tCost\t\t\tQuantity")
    print(f"{position:<16}{shoe_list[position]}")
    print("=" * 90)

    print(f"Stock number {position}, is nearly out of stock. Only {min(product_quantity)} available.")

    # while loop that will run until a condition is met
    while True:
        try:

            # variable to store user input. it will then replace the quantity of the product at a
            # specific position in shoe list. declaring a new list to store the result of a for loop that iterates
            # the shoe list and appends results in a variable that calls the function to_file() to return
            # the updated class objects to be saved in file. Opens the file and writes the updated data
            new_stock = int(input("Enter the quantity of shoes to be updated: "))
            shoe_list[position].quantity = new_stock
            new_stock_data = [nsd.to_file() for nsd in shoe_list]

            with open('./inventory.txt', 'w', encoding='utf-8-sig') as updated_stock_file:
                updated_stock_file.write("\n".join(new_stock_data) + "\n")

            print()
            print("Changes successfully saved!\n")
            break
        except ValueError:

            print("Oops!. Invalid data entered, only numbers are accepted.")
```
### Search product
```Python
def search_shoe():
    """
         Function to search for a shoe in the shoe list
         using the shoe code entered by the user and return the object to be printed in a user-friendly format.
    """
    # declaring variables and initialize them to an integer and a list
    position = 0
    product_code = []

    # declaring variable that takes user input and store it
    shoe_code = input("Search shoe by its code: ").upper()

    # for loop to iterate the list of objects skipping index 0 and enumerating the objects in list
    # append the results into a new list
    for num, shoe in enumerate(shoe_list[1:]):
        product_code.append(shoe.code)

        # conditional statement to check if user input is equal to a shoe code, then add 1 to the enumerated position
        if shoe_code == shoe.code:
            position = num + 1

    # prints in a user-friendly format the product description of the shoe being searched
    print("\nProduct description:")
    print("=" * 90)
    print(f"Number\t\t\tCountry\t\t\tCode\t\tProduct\t\t\t\tCost\t\t\tQuantity")
    print(f"{position:<16}{shoe_list[position]}")
    print("=" * 90)
```
### Total value of products
```Python
def value_per_item():
    """
        Function to print in a user-friendly format the calculated
        total value for each item.
    """
    print("\nProduct description:")
    print("=" * 110)
    print(f"Number\t\t\tCountry\t\t\tCode\t\tProduct\t\t\t\tCost\t\t\tQuantity\t\t\tValue")
    print("=" * 110)

    # for loop to iterate and enumerate the objects in the shoe list skipping index 0 and prints
    # the results using a string formatted
    for num, shoes in enumerate(shoe_list[1:], start=1):
        print(f"{num:<16}{shoes}£{'{:,.2f}'.format(shoes.get_cost())}")

    print("=" * 110)
    print()
```
### Product with high stock
```Python
def highest_qty():
    """
        Function to find the shoe object with the highest quantity,
        which is the shoe to be put on sale, and print a message in a user-friendly format.
    """
    position = 0
    product_highest_qty = []

    # for loop to iterate the list of objects skipping index 0 and enumerating the objects in list
    # append the results into a new list as integers
    for num, shoe in enumerate(shoe_list[1:]):
        product_highest_qty.append(int(shoe.get_quantity()))

        # conditional statement to check if the quantity of the object is equals to the maximum quantity
        # stored in new list and add 1 to the position
        if int(shoe.get_quantity()) == max(product_highest_qty):
            position = num + 1

    # prints in a user-friendly format the product description with the highest quantity
    print("\nProduct description:")
    print("=" * 90)
    print(f"Number\t\t\tCountry\t\t\tCode\t\tProduct\t\t\t\tCost\t\t\tQuantity")
    print(f"{position:<16}{shoe_list[position]}")
    print("=" * 90)
    print(f"Product {shoe_list[position].code}, to be put on 'SALE!!!'.\n{max(product_highest_qty)} "
          f"currently in stock.\n")
```
### Main menu of the program
```Python
# declaring a list variable that will be used to store a list of objects of Shoes class.
shoe_list = []
"""
Creating a menu that executes each function above according to the user input
a while loop will run until the user decides to exit the program 
This menu should be inside the while loop. Be creative!
"""
# calls the function to read data from the file as soon as the program is executed, if such file does not exist or
# is corrupted a message will be printed to the user with information about the error
read_shoes_data()

# declaring a variable to take store user input
# declaring variable to store a pattern of number (1-7) using regex library
# conditional statement to check if user input full-matches the pattern
# if there is a match with the pattern (1-7) the program
# will continue or will exit
menu = ""
pattern = re.compile("[1-7]")

while menu != "7":

    # prints in a user-friendly format a menu for the user to be able to interact with the program
    print("=" * 60)
    print("\t\t\t\t\tInventory Management\t\t\t\t\t")
    print("=" * 60)
    print("\tSelect a number from the Main Menu below:\t\t\t\t")
    print("""
    \t(1) View Product Inventory
    \t(2) Add a New Product
    \t(3) Restock a Product
    \t(4) Search For Product
    \t(5) View Total Value Per Product
    \t(6) View Product With The Highest Quantity
    \t(7) Exit Program\n
    """)
    print("=" * 60)
    menu = input("\tEnter your selection: ")
    print("=" * 60)

    if menu == "1":

        # will call the view_all function
        view_all()

    elif menu == "2":

        # will call the capture_shoes function
        capture_shoes()

    elif menu == "3":

        # will call the re_stock function
        re_stock()

    elif menu == "4":

        # will call the search_shoe function
        search_shoe()

    elif menu == "5":

        # will call the value_per_item function
        value_per_item()

    elif menu == "6":

        # will call the highest_qty function
        highest_qty()

    elif menu == "7":

        print("Goodbye!")

    else:

        print("\nOops!, Invalid input!.\nEnter a number from 1 to 7\n")
```
## Contributing
Pull requests are welcome. For any changes, please open an issue first to discuss what you would like to change.
Please make sure to update tests as appropriate.
## Credits
© Angel De Sousa

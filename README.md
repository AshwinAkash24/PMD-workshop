# PMD-workshop
# Aim:
To develop an Android application using SQLite Database that stores Country names and their respective Currencies, and performs CRUD (Create, Read, Update, Delete) operations through a ListView interface.
# Algorithm
**Step 1: Create Database**
Create a SQLite database named CountryDB.
Create a table countries with fields:
ID (Primary Key)
Country Name
Currency
**Step 2: Add Record**
Open Add Country screen.
Enter country name and currency.
Save the data into SQLite database.
Display a success message.
**Step 3: View Records**
Retrieve all records from SQLite database.
Store records in an ArrayList.
Display records using ListView.
**Step 4: Update Record**
Select a record from ListView.
Open Modify Country screen.
Edit country name or currency.
Update the record in SQLite database.
Refresh ListView.
**Step 5: Delete Record**
Select a record from ListView.
Click Delete button.
Remove the selected record from database.
Refresh ListView.
**Step 6: Exit Application**
Close the application after performing required operations.

# Program Description
**DatabaseHelper.java**
Creates SQLite database and countries table.
Handles database creation and upgrades.
**DBManager.java**
Provides methods for:
Insert Record
Fetch Records
Update Record
Delete Record
**CountryListActivity.java**
Displays all country records in ListView.
Handles selection of records.
**AddCountryActivity.java**
Allows user to enter country name and currency.
Inserts data into SQLite database.
**ModifyCountryActivity.java**
Allows editing and deleting selected records.

# Output



# Result
The Android application was successfully developed using SQLite Database to store Country names and their corresponding Currencies. CRUD operations such as Insert, View, Update, and Delete were implemented successfully, and the records were displayed using a ListView interface. The application functioned correctly and met all the specified requirements.

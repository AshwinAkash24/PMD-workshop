# PMD-workshop
# Aim:
To develop an Android application using SQLite Database that stores Country names and their respective Currencies, and performs CRUD (Create, Read, Update, Delete) operations through a ListView interface.
# Algorithm
**Step 1: Create Database**<br>
Create a SQLite database named CountryDB.
Create a table countries with fields:
ID (Primary Key)
Country Name
Currency<br>
**Step 2: Add Record**<br>
Open Add Country screen.
Enter country name and currency.
Save the data into SQLite database.
Display a success message.<br>
**Step 3: View Records**<br>
Retrieve all records from SQLite database.
Store records in an ArrayList.
Display records using ListView.<br>
**Step 4: Update Record**<br>
Select a record from ListView.
Open Modify Country screen.
Edit country name or currency.
Update the record in SQLite database.
Refresh ListView.<br>
**Step 5: Delete Record**<br>
Select a record from ListView.
Click Delete button.
Remove the selected record from database.
Refresh ListView.<br>
**Step 6: Exit Application**<br>
Close the application after performing required operations.

# Program Description
**DatabaseHelper.java**<br>
Creates SQLite database and countries table.
Handles database creation and upgrades.<br>
**DBManager.java**<br>
Provides methods for:
Insert Record
Fetch Records
Update Record
Delete Record<br>
**CountryListActivity.java**<br>
Displays all country records in ListView.
Handles selection of records.<br>
**AddCountryActivity.java**<br>
Allows user to enter country name and currency.
Inserts data into SQLite database.<br>
**ModifyCountryActivity.java**<br>
Allows editing and deleting selected records.

# Output
<img width="1912" height="1075" alt="image" src="https://github.com/user-attachments/assets/6ee722cf-170b-4a91-9ecb-ee56d66e04d7" />
<img width="1916" height="1076" alt="image" src="https://github.com/user-attachments/assets/6b199adb-1014-4ac7-8f8e-de5f3f8855ae" />
<img width="1917" height="1074" alt="image" src="https://github.com/user-attachments/assets/916b4970-2dbc-4802-857c-1691520cc943" />





# Result
The Android application was successfully developed using SQLite Database to store Country names and their corresponding Currencies. CRUD operations such as Insert, View, Update, and Delete were implemented successfully, and the records were displayed using a ListView interface. The application functioned correctly and met all the specified requirements.

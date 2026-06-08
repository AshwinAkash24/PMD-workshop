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

# Program
```
Developed By:Ashwin Akash M
Registered Number: 212223230024
```
## AddCountry.java
```
package com.example.pmd_workshop;

import android.content.Intent;
import android.os.Bundle;
import android.view.View;
import android.view.View.OnClickListener;
import android.widget.Button;
import android.widget.EditText;

import androidx.appcompat.app.AppCompatActivity;

public class AddCountryActivity extends AppCompatActivity implements OnClickListener {

    private Button addRecordBtn;
    private EditText countryEditText;
    private EditText currencyEditText;

    private DBManager dbManager;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        setTitle("Add Record");

        setContentView(R.layout.activity_add_country);

        countryEditText = (EditText) findViewById(R.id.subject_edittext);
        currencyEditText = (EditText) findViewById(R.id.description_edittext);

        addRecordBtn = (Button) findViewById(R.id.add_record);

        dbManager = new DBManager(this);
        dbManager.open();
        addRecordBtn.setOnClickListener(this);
    }

    @Override
    public void onClick(View v) {
        if (v.getId() == R.id.add_record) {
            final String country = countryEditText.getText().toString();
            final String currency = currencyEditText.getText().toString();

            dbManager.insert(country, currency);

            Intent main = new Intent(AddCountryActivity.this, CountryListActivity.class)
                    .setFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP);

            startActivity(main);
        }
    }
}

```
## CountryList.java
```
package com.example.pmd_workshop;

import android.content.Intent;
import android.database.Cursor;
import android.os.Bundle;
import android.view.Menu;
import android.view.MenuItem;
import android.view.View;
import android.widget.AdapterView;
import android.widget.ListView;
import android.widget.SimpleCursorAdapter;
import android.widget.TextView;

import androidx.appcompat.app.AppCompatActivity;

public class CountryListActivity extends AppCompatActivity {

    private DBManager dbManager;
    private ListView listView;
    private SimpleCursorAdapter adapter;

    final String[] from = new String[] { DatabaseHelper._ID,
            DatabaseHelper.COUNTRY, DatabaseHelper.CURRENCY };

    final int[] to = new int[] { R.id.id, R.id.title, R.id.desc };

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.fragment_emp_list);

        dbManager = new DBManager(this);
        dbManager.open();
        Cursor cursor = dbManager.fetch();

        listView = (ListView) findViewById(R.id.list_view);
        listView.setEmptyView(findViewById(R.id.empty));

        adapter = new SimpleCursorAdapter(this, R.layout.view_record, cursor, from, to, 0);
        adapter.notifyDataSetChanged();

        listView.setAdapter(adapter);

        // OnCLickListiner For List Items
        listView.setOnItemClickListener(new AdapterView.OnItemClickListener() {
            @Override
            public void onItemClick(AdapterView<?> parent, View view, int position, long viewId) {
                TextView idTextView = (TextView) view.findViewById(R.id.id);
                TextView titleTextView = (TextView) view.findViewById(R.id.title);
                TextView descTextView = (TextView) view.findViewById(R.id.desc);

                String id = idTextView.getText().toString();
                String title = titleTextView.getText().toString();
                String desc = descTextView.getText().toString();

                Intent modify_intent = new Intent(getApplicationContext(), ModifyCountryActivity.class);
                modify_intent.putExtra("title", title);
                modify_intent.putExtra("desc", desc);
                modify_intent.putExtra("id", id);

                startActivity(modify_intent);
            }
        });
    }

    @Override
    public boolean onCreateOptionsMenu(Menu menu) {
        getMenuInflater().inflate(R.menu.main, menu);
        return true;
    }

    @Override
    public boolean onOptionsItemSelected(MenuItem item) {
        int id = item.getItemId();
        if (id == R.id.add_record) {
            Intent add_mem = new Intent(this, AddCountryActivity.class);
            startActivity(add_mem);
        }
        return super.onOptionsItemSelected(item);
    }
}

```

## Database.java
```
package com.example.pmd_workshop;

import android.content.Context;
import android.database.sqlite.SQLiteDatabase;
import android.database.sqlite.SQLiteOpenHelper;

public class DatabaseHelper extends SQLiteOpenHelper {

    // Table Name
    public static final String TABLE_NAME = "COUNTRIES";

    // Table columns
    public static final String _ID = "_id";
    public static final String COUNTRY = "country";
    public static final String CURRENCY = "currency";

    // Database Information
    static final String DB_NAME = "PMD_WORKSHOP.DB";

    // database version
    static final int DB_VERSION = 1;

    // Creating table query
    private static final String CREATE_TABLE = "create table " + TABLE_NAME + "(" + _ID
            + " INTEGER PRIMARY KEY AUTOINCREMENT, " + COUNTRY + " TEXT NOT NULL, " + CURRENCY + " TEXT);";

    public DatabaseHelper(Context context) {
        super(context, DB_NAME, null, DB_VERSION);
    }

    @Override
    public void onCreate(SQLiteDatabase db) {
        db.execSQL(CREATE_TABLE);
    }

    @Override
    public void onUpgrade(SQLiteDatabase db, int oldVersion, int newVersion) {
        db.execSQL("DROP TABLE IF EXISTS " + TABLE_NAME);
        onCreate(db);
    }
}

```
## DBManager.java
```
package com.example.pmd_workshop;

import android.content.ContentValues;
import android.content.Context;
import android.database.Cursor;
import android.database.SQLException;
import android.database.sqlite.SQLiteDatabase;

public class DBManager {

    private DatabaseHelper dbHelper;
    private Context context;
    private SQLiteDatabase database;

    public DBManager(Context c) {
        context = c;
    }

    public DBManager open() throws SQLException {
        dbHelper = new DatabaseHelper(context);
        database = dbHelper.getWritableDatabase();
        return this;
    }

    public void close() {
        dbHelper.close();
    }

    public void insert(String name, String currency) {
        ContentValues contentValue = new ContentValues();
        contentValue.put(DatabaseHelper.COUNTRY, name);
        contentValue.put(DatabaseHelper.CURRENCY, currency);
        database.insert(DatabaseHelper.TABLE_NAME, null, contentValue);
    }

    public Cursor fetch() {
        String[] columns = new String[] { DatabaseHelper._ID, DatabaseHelper.COUNTRY, DatabaseHelper.CURRENCY };
        Cursor cursor = database.query(DatabaseHelper.TABLE_NAME, columns, null, null, null, null, null);
        if (cursor != null) {
            cursor.moveToFirst();
        }
        return cursor;
    }

    public int update(long _id, String name, String currency) {
        ContentValues contentValues = new ContentValues();
        contentValues.put(DatabaseHelper.COUNTRY, name);
        contentValues.put(DatabaseHelper.CURRENCY, currency);
        int i = database.update(DatabaseHelper.TABLE_NAME, contentValues, DatabaseHelper._ID + " = " + _id, null);
        return i;
    }

    public void delete(long _id) {
        database.delete(DatabaseHelper.TABLE_NAME, DatabaseHelper._ID + "=" + _id, null);
    }

}

```
## ModifyCountyActivity.java
```
package com.example.pmd_workshop;

import android.content.Intent;
import android.os.Bundle;
import android.view.View;
import android.view.View.OnClickListener;
import android.widget.Button;
import android.widget.EditText;

import androidx.appcompat.app.AppCompatActivity;

public class ModifyCountryActivity extends AppCompatActivity implements OnClickListener {

    private EditText countryText;
    private Button updateBtn, deleteBtn;
    private EditText currencyText;

    private long _id;

    private DBManager dbManager;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        setTitle("Modify Record");

        setContentView(R.layout.activity_modify_country);

        dbManager = new DBManager(this);
        dbManager.open();

        countryText = (EditText) findViewById(R.id.subject_edittext);
        currencyText = (EditText) findViewById(R.id.description_edittext);

        updateBtn = (Button) findViewById(R.id.btn_update);
        deleteBtn = (Button) findViewById(R.id.btn_delete);

        Intent intent = getIntent();
        String id = intent.getStringExtra("id");
        String name = intent.getStringExtra("title");
        String desc = intent.getStringExtra("desc");

        _id = Long.parseLong(id);

        countryText.setText(name);
        currencyText.setText(desc);

        updateBtn.setOnClickListener(this);
        deleteBtn.setOnClickListener(this);
    }

    @Override
    public void onClick(View v) {
        int id = v.getId();
        if (id == R.id.btn_update) {
            String country = countryText.getText().toString();
            String currency = currencyText.getText().toString();

            dbManager.update(_id, country, currency);
            this.returnHome();
        } else if (id == R.id.btn_delete) {
            dbManager.delete(_id);
            this.returnHome();
        }
    }

    public void returnHome() {
        Intent home_intent = new Intent(getApplicationContext(), CountryListActivity.class)
                .setFlags(Intent.FLAG_ACTIVITY_CLEAR_TOP);
        startActivity(home_intent);
    }
}

```

## activity_add_country.xml:
```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="16dp">

    <EditText
        android:id="@+id/subject_edittext"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="@string/enter_country_name"
        android:inputType="text"
        android:autofillHints="countryName" />

    <EditText
        android:id="@+id/description_edittext"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="@string/enter_currency"
        android:inputType="text"
        android:autofillHints="currency" />

    <Button
        android:id="@+id/add_record"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:text="@string/add_record" />
</LinearLayout>

```
## activity_modify_country.xml
```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    android:padding="16dp">

    <EditText
        android:id="@+id/id_edittext"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:enabled="false"
        android:inputType="none"
        android:visibility="gone" />

    <EditText
        android:id="@+id/subject_edittext"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="@string/country_name"
        android:inputType="text"
        android:autofillHints="countryName" />

    <EditText
        android:id="@+id/description_edittext"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:hint="@string/currency"
        android:inputType="text"
        android:autofillHints="currency" />

    <LinearLayout
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:orientation="horizontal"
        style="?android:attr/buttonBarStyle"
        android:weightSum="2">

        <Button
            android:id="@+id/btn_update"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:text="@string/update"
            style="?android:attr/buttonBarButtonStyle" />

        <Button
            android:id="@+id/btn_delete"
            android:layout_width="0dp"
            android:layout_height="wrap_content"
            android:layout_weight="1"
            android:text="@string/delete"
            style="?android:attr/buttonBarButtonStyle" />
    </LinearLayout>
</LinearLayout>

```
## fragmnent_emp_list.xml
```
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <ListView
        android:id="@+id/list_view"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:dividerHeight="1dp" />

    <TextView
        android:id="@+id/empty"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_centerInParent="true"
        android:text="@string/no_records_found"
        android:textSize="20sp" />
</RelativeLayout>

```
## view_record.xml
```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:orientation="vertical"
    android:padding="10dp">

    <TextView
        android:id="@+id/id"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:visibility="gone" />

    <TextView
        android:id="@+id/title"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:paddingBottom="2dp"
        android:paddingTop="2dp"
        android:textColor="@color/black"
        android:textSize="18sp"
        android:textStyle="bold" />

    <TextView
        android:id="@+id/desc"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:paddingBottom="2dp"
        android:paddingTop="2dp"
        android:textColor="@color/black"
        android:textSize="15sp" />
</LinearLayout>

```
# Output
<img width="1912" height="1075" alt="image" src="https://github.com/user-attachments/assets/6ee722cf-170b-4a91-9ecb-ee56d66e04d7" />
<img width="1916" height="1076" alt="image" src="https://github.com/user-attachments/assets/6b199adb-1014-4ac7-8f8e-de5f3f8855ae" />
<img width="1917" height="1074" alt="image" src="https://github.com/user-attachments/assets/916b4970-2dbc-4802-857c-1691520cc943" />





# Result
The Android application was successfully developed using SQLite Database to store Country names and their corresponding Currencies. CRUD operations such as Insert, View, Update, and Delete were implemented successfully, and the records were displayed using a ListView interface. The application functioned correctly and met all the specified requirements.

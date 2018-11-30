# Using Python in Conjunction with SQLite to Automate Database Management Processes

![Python Logo](https://upload.wikimedia.org/wikipedia/commons/c/c3/Python-logo-notext.svg)    ![SQLite Logo](https://upload.wikimedia.org/wikipedia/commons/3/38/SQLite370.svg)

## Tutorial Objectives

The objectives of this tutorial are outlined as follows:

* Provide the user with a structured basis of understanding on how programming languages can interact with each other to manage a database system.
* Demonstrate how Python can use SQL commands to automate database queries. This is when the tutorial is most helpful.
* Explain the functions and capabilities of what Python and SQLite can accomplish when used in combination.
* Allow the user to practice what they’ve learned with applicable problem sets and answers.
* Provide further resources for more in depth study of the topic.

## Recommended Prerequisite Knowledge

Since we will be demonstrating SQL use in the Python programming language, a topic not typical for beginners, it is recommended that you have at least a small amount of programming knowledge in order to more quickly grasp the concepts presented in this tutorial.

## Tutorial Dependencies

#### SQLite3
Since we are using sqlite as our database management language, you can download the latest version here: [SQLite Download](https://www.sqlite.org/download.html "SQLite Download"). Or, depending on your operating system, you can enter a command such as the following into your terminal:

`sudo apt-get install sqlite3`

#### Python3
Furthermore, this tutorial uses Python as the programming language to automate the database processes, hence, you will need to have Python installed. You can download it online with https://www.python.org/downloads/ or enter a command such as the following into your terminal.

`sudo apt-get install python3`

Noticeably, programs do not save any data in memory between program executions, this is where databases will come in very handy.
The following information is available at https://docs.python.org/2/library/sqlite3.html however, sometimes it can be hard to understand programming language documentation.

##Example Program and Explanation 1

In this example we will build a simple database of students called “student”, and then print all of the values within our student database in a python program.


```python
    import sqlite
    import sys
    connection = sqlite3.connect('example.db')
    c = connection.cursor()
```

The import sqlite3 line allows access to the sqlite3 API. This is what essentially lets you access your database. Import sys is just used for user input in this example.
“connection” is the actual connection to the ‘example.db’ database.
‘c’ , which is a database cursor, is what allows you to execute SQL statements.

The lines of code above excluding ‘import sys’ are all that you should need to access your database in python. Next we should set up a loop that allows the user to enter as many students into the database as they wish.


```python
    def mainMenu():
        decision = 0
        #while block allows program to run until exited
        while(decision != 4):
            print("What would you like to do?")
            decision = input("1) Add a student 2) Print Table Contents 3) Delete a student 4) Exit Program\n")
            decision = int(decision)    #decision is the choice made by user
            #call respective function based on decision
            if(decision == 1):
                print("You chose to add a student:")
                addStudent()
            elif(decision == 2):
                print("You chose to print out the table's contents...")
                printContents()
            elif(decision == 3):
                print("You chose to delete from the database")
                removeStudent()
            else:
                print("You chose to Exit the Program. Goodbye.")
                c.close() #close the cursor to the database
                connection.close() #close connection to the database
```
The “mainMenu()” method is just a simple way for us to display some information to the user to allow them to choose between three decisions. 

1. To add a student to the database.
2. To print the contents of the “student” table.
3. exit the program.

If the user chooses 1, the program will set the decision variable equal to 1. And move to the addStudent() method.
If the user chooses 2, the program will set the decision variable equal to 2. And move to the printContents() method.
If the user chooses 3, the program will set the decision variable equal to 3. Notice that this will break the while loop, causing the program to terminate.

Lets walk through option 1, to add a student to the database. This is where the meat and potatoes of this program is.


```python
    #addStudent() creates and adds a new student into the 'student' table
    def addStudent():
        #create table 'student' if the table does not exist
        c.execute('CREATE TABLE IF NOT EXISTS student(id INTEGER PRIMARY KEY AUTOINCREMENT, name TEXT, grade INTEGER)')
        #c performs a SELECT * statement on the 'student' table
        c.execute('SELECT * FROM student')
        print()
    
        #if table is empty
        if(c.fetchone() is None):
            print("The Table is empty... Lets add a student.")
        else:
            print(c.fetchall())
    
        #get user input for information on new student
        fName = input("Enter a name:\n")
        grade = input("Enter a grad:(0-12)\n")
        grade = int(grade)
        studentInfo = [None,fName,grade]
        #insert student into the 'student' table
        c.execute('INSERT INTO student VALUES (?,?,?)', studentInfo)
        connection.commit()
```     
Here, the first thing that we do is use the “execute” functionality of “c”, to create a table called “student” if one doesn’t already exist. The columns of the table will be: 

1. An auto incrementing integer variable called “id” which is also the primary key. 
2. A text variable called “name”.
3. An integer variable called “grade”.

Where “c” uses the fetchone() method it gets one row from the “student” table, if it is None (empty), then we know there is no entries in our database. Otherwise, we print the entire list of students in the “student” table. 
We need to create a tuple to enter the new information into the “student” table.

Notice that the first value is None. That is because our first column in the “student” table is an auto incrementing variable, and is configured automatically by the database. 

The series of questions marks in the INSERT statement are placeholders that are filled by the student info tuple.

Finally, once we have inserted our new data into the table, we need to commit those changes. What this line does, is actually saves the new information to the database so that it will still be there for the next run of the program.

Now let's quickly look at what happens if the user had selected option 2:


```python
    def printContents():
        c.execute('CREATE TABLE IF NOT EXISTS student(id INTEGER PRIMARY KEY AUTOINCREMENT, name TEXT, grade INTEGER)')
        c.execute('SELECT * FROM student')
        if(c.fetchone() is None):
            print("The Table is empty... Lets add a student.")
        else:
            print(c.fetchall())
```

This method is fairly simple, and I only included it to help you see that in fact your database is growing with each entry and run of the program. Notice that it is the same code used in addStudent().

Finally, if the user chooses option 3.


```python
    def mainMenu():
        decision = 0
        while(decision != 4):
            print("What would you like to do?")
            decision = input("1) Add a student 2) Print Table Contents 3) Delete a student 4) Exit Program\n")
            decision = int(decision)
            if(decision == 1):
                print("You chose to add a student:")
                addStudent()
            elif(decision == 2):
                print("You chose to print out the table's contents...")
                printContents()
            elif(decision == 3):
                print("You chose to delete from the database")
                removeStudent()
            else:
                print("You chose to Exit the Program. Goodbye.")
                c.close()
                connection.close()
```

This option is fully encapsulated by the conditional statement where decision equals three. Here it is important to remember to close “c” the cursor, and “connection” the connection to the database when you are finished using the database.


## Example Program and Explanation 2
For this example we will extend from code given in the first example and add another function to demonstrate and explain how to remove certain fields from the database.


```python
    def removeStudent():
        student_name = input("Enter a name:\n")
        c.execute('DELETE FROM student WHERE name = ?', (student_name,))
```

In this method, we retrieve the student name from standard input. Then, we need to still use “c”, the same cursor object that we used in the previous examples, to execute a delete statement in the student table. In this example we increased the complexity slightly by specifying where to delete from in the table with the “WHERE” specification. Then we include the name we retrieved from standard input as a tuple, to remove the student from the student table.
After performing this operation, use the printContent() method that you created earlier to ensure that the removeStudent() method worked as expected.

##Practice Problems

**Problem 1:** A manager of a business wants your help creating a database, the manager wants you to organize all his employees in a table where each employee has an ID, name, department, date employed. In this table, you will insert a few entries to be used for later problems. You should imply the data types for your table.

Entry 1: 001, Robin Hood, Theft Protection, 1/1/1990

Entry 2: 007, James Bond, Accounting, 5/5/1995

Entry 3: 114, Erica Samson, Accounting, 7/3/1993

Entry 4: 125, Jimmy Johns, Sales, 12/25/1998

Entry 5: 174, Zelis Zompzon, Marketing, 9/17/1997 

**Problem 2:** Now that we have our table up and operational, the manager wants us to demo parts of our database to see that it suits their needs. The manager wants to be able to see the entire employee table, retrieve employee name and employed date from the employee ID, using employee ID to determine department, employees that have been employed since January 1st 1996, and able to retrieve all employees in a certain department.

**Problem 3:** Turns out the business has taken some hits recently and will be going through some downsizing. Now that certain employees will no longer be working at the company the manager would like us to remove certain employees from the database. The manager would like us to remove Employee with ID 001 from the table. Also, unfortunately, it would seem the entire accounting department will need to go, and so the manager in an effort to save text, would like you to delete all members of accounting from the table via one line of sql code. You will then show him the table with the remaining employees. 

## Further Resources

Link to full program from example 1: [Problem1 Github](https://github.com/Matt3697/CSCI-440/blob/master/sql_tutorial.py "Our Github Repo for Problem1")

### References

[DB-API 2.0 interface for SQLite databases](https://docs.python.org/2/library/sqlite3.html "DB-API 2.0 interface for SQLite databases")

[Geeks for Geeks: SQL using Python](https://www.geeksforgeeks.org/sql-using-python/)

[SQLite Python: Querying Data](http://www.sqlitetutorial.net/sqlite-python/sqlite-python-select/ "SQLite Python: Querying Data")

### Tutorial Authors

Michael Pollard

Hugh Jackovich

Cory Petersen

Matthew Sagen

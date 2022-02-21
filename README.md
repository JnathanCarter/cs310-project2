# cs310-project2
This assignment is intended to give you more practice with database programming in Java, using the MySQL DBMS and the JDBC API.  It involves implementing a set of database methods which are designed to be used by a simplified version of JSU's Interactive Class Schedule (Links to an external site.) page.  These methods should allow the user of the application to: search the Spring 2022 course schedule, register for classes, or cancel their registration(s).

Before proceeding with this assignment, be sure that you have completed the installation of MySQL on your workstation, following the procedure documented on the course home page on Canvas.  You should also run the "DatabaseTest" program included with the installation instructions, and study the program code carefully; this code, and the example code given in the "Java Database Programming" lecture notes, will be useful for reference when completing this assignment.

Begin by unpacking the attached archive.  You will find a database backup file containing JSU's entire Spring 2022 schedule, along with a diagram of the database to help you to understand its structure.  You do not need to open the database backup file directly; instead, log in to the MySQL Client at the command line, and import the backup file into your local MySQL server with the following command:

source C:\Users\JSU\Desktop\jsu_sp22_v1.sql

(You should see a succession of "Query OK" messages as MySQL executes the commands given in the backup file to reconstruct the original database.  Of course, if your database backup file is in a different location on your file system, you should make the corresponding changes to the path.)

(As an alternative, if you have the MySQL GUI Tools installed, you can import this file using the MySQL Administrator.  Launch the Administrator, enter your root password, and in the "Server" field, make sure that you enter "localhost" if it is not entered already.  After you log in, choose "Restore" from the menu of options on the left, click "Open Backup File" on the right, browse to the SQL backup file from Canvas, and click "Open" in the dialog box.  After selecting it, the "Start Restore" button should be enabled; click this button, and the import should take only a few seconds.)

Next, create a new MySQL user account with access to the "jsu_sp22_v1" database you have just imported.  Using the MySQL command-line client, enter the following commands, one at a time, to create a new MySQL user account for your application with full access to the database:

create user 'cs310_p2_user'@'localhost' identified by 'P2!user';
grant all on jsu_sp22_v1.* to 'cs310_p2_user'@'localhost';
flush privileges;

Test the new "cs310_p2_user" account by using it to log in to a MySQL client, such as the MySQL Workbench, the MySQL Administrator or Query Browser, or the command-line MySQL Client.  (As shown above, the user name is "cs310_p2_user" and the password is "P2!user".  Your application should use this account name and password when connecting to the database, not the MySQL root account!)

Once you have successfully tested this new account, the next step is to fork and clone the following GitHub repository:

https://github.com/jsnellen/cs310-project2 (Links to an external site.)

This repository contains a partial implementation of the application for this assignment, which you are to use as a starting point.  It includes a few "helper methods" which you might find useful, including a "getResultSetAsJSON()" method which takes a ResultSet returned by a database query and encodes the data within it as an array of JSON objects, using the original field names (if any) as the keys.  You will also find a unit test file for checking your completed work; this is described in more detail below.

### NOTE: As with Individual Project #1, you should be sure to clone your fork of this repository, not the original!  Also, when you create the project in NetBeans, be sure to use the "Java Project with Existing Sources" option, according to the procedure shown in the "Version Control, Part Four" lecture notes, to keep the source files (which should be placed under version control) separate from the NetBeans project files and metadata (which should not be placed under version control).

## Part One: Preparing the Database

After importing the "jsu_sp22_v1" database to your local MySQL server, you will need to add a new record to the "login" table of the database, and corresponding records in the "userrole" and "student" tables, to create a "student account" in the database for yourself.  Use the following commands, entered one at a time:

use jsu_sp22_v1;
INSERT INTO `login` (username, password) VALUES ('jsmith123', SHA2('yourpassword', 512));
INSERT INTO userrole (username, rolename) VALUES ('jsmith123', 'user');
INSERT INTO student (username) VALUES ('jsmith123');

The new record in the "student" table should use the same username that you added to the "login" table.  A numeric ID will be automatically assigned to the new record in this table; this is the ID that you will use when registering for classes, dropping classes, etc.

(Feel free to change the username and password according to your own preference.  You will not actually be using these to log in to the system; this is just a "dummy account" for you to use to register for classes.  A more complete system would also implement authentication, and differentiated roles for students, faculty, and staff, but that is outside the scope of this assignment.)

## Part Two: Adding the Database Methods

Now that you have added a student account to the database, you are ready to begin implementing and testing the database methods.  If you have not already done so, import the partial code provided in the GitHub repository into a new NetBeans project.  Using this code as a starting point, complete a collection of methods in the provided Database class which perform the following actions:

Search the course schedule by subject ID, course number, and term ID.  Your method should accept these criteria as arguments; the term ID should be an integer and the subject ID and course number should be strings.  Your database query should match these values to the "subjectid" and "num" fields in the "section" table; the query results should include all available information for the matching sections and should be returned by the method in JSON format, using key names corresponding to the original field names in the database.
Register for a course.  This will involve inserting a new record into the "registration" table containing: the numeric student ID assigned to the student's user account (this is the same ID that was created earlier in the "student" table), the term ID (from the "term" table), and the CRN for the specified section.  All three values should be given as arguments to the method; the method should return an integer value representing the number of records affected by the query.
Drop a previous registration for a single course.  This will involve a query which deletes the corresponding record from the "registration" table.  The method should accept the student ID, term ID, and CRN number as arguments, and should return an integer value representing the number of records affected by the query.
Withdraw from all registered courses.  This will again involve deleting records from the "registration" table, this time for all registrations which match the given term ID and student ID.  The method should accept both values as arguments, and should return an integer value representing the number of records affected by the query.
List all registered courses for a given student ID and term ID.  The query results should return all information about the registered sections, as given in the "section" table; this will involve using a query which joins the CRN numbers listed in the "registration" table with the corresponding entries in the "section" table.  The method should return the query results in JSON format, using key names corresponding to the field names in the database.
See the "Database Programming" lecture notes for more information, particularly the SQL Quick Reference and the JDBC example code given at the end of the notes.  After you have completed these methods, test your work by running the included "CourseRegistrationDatabaseTest" unit test file; six unit tests are given in this file, and for your work to be considered complete, all six tests must pass without errors.  All database-related logic must be organized into the provided Database class; you should use the main class to open a connection to the database (already implemented for you) and to test your methods one by one during development.

This assignment is worth 100 points and is due by the end of the day on Sunday, February 20th.  As with our earlier assignments, push your changes to your fork of the original repository, and submit your GitHub URL to Canvas when you are finished.

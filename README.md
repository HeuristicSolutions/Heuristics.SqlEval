# Heuristics.SqlEval

Hello, and thank you for your interest in Heuristic Solutions!

Our interview process includes a take-home technical assessment. We believe that this approach is a more realistic evaluation than having you write code on a whiteboard or asking you obscure technical "gotcha" questions, and it allows you to work at your own pace and on your own time schedule.

We know your time is valuable, so we're only asking that you spend about **two hours** on this. You're welcome to spend more if you like, either to put your best foot forward or because you're enjoying the challenge, but it's not required. 


## Assessment overview
The assessment simulates a real-world project that you might deliver in this role, though dramatically simplified. As part of this assessment you will:

* Create a stored procedure to report on some data;
* Perform a number of data import and manipulation tasks using T-SQL;
* Prepare an outcome report listing any errors or issues you identified;
* Participate in a solution review with our technical team;

The solution review is a collaborative review exercise, similar to a code review, in which we will discuss your approach, any trade-offs you made, and alternative techniques that might have been considered. 

During this assessment we'll learn about your abilities to work with data, and you'll learn a bit about how we approach collaboration and technical problem solving. It's a win-win!

## Setting up your environment

**We will provide you with a fully-provisioned virtual machine containing the necessary tools if desired.** 

You are also welcome to use your own development environment if you prefer, as long as it has SQL Server 2016 or newer.

If you are using your own environment, restore the *SqlAssessment.bak* file to a database named "SqlAssessment". The VM already has the database restored.

## Task 1 - Removing duplicates
The *SqlAssessment* database contains some information about people and their addresses:

Table                | Purpose
-----                | -----
**`MEMBER`**         | Contains information about people
**`MEMBER_ADDRESS`** | Contains address information, linked back to `MEMBER`

The `MEMBER_ADDRESS` table contains a "LABEL" column so that we can track multiple different addresses for each person, such as a "Home" and "Work" address. 

By business rule, a Member should only have ONE address per label. However, some of the existing data violates this rule (some Members have multiple addresses with the same label).

Your task is to:

1. Delete duplicate data that violates the business rule. For each label with multiple addresses, keep the record with the most recent MODIFIED_ON date.

2. Create a unique index preventing duplicates from being created in the future.

| :exclamation:        | Create a report showing the scripts you wrote (or documenting your process) and listing the addresses that were deleted. This will be submitted at the end of the assessment.       |
|---------------|:------------------------|

## Task 2 - Reporting on Address data
Create a stored procedure called `GET_ADDRESS_STATS` that takes a single optional parameter `@Label`.

This procedure should return two different result sets.

### Result set 1
The first result set should contain one row per unique State + Label combination, and the number of addresses with that label in that State. 

* If `@Label` is not null and not empty, only addresses matching that Label are counted. Otherwise all data are counted.
* Results should be ordered by State then Label.

Example:

| STATE | LABEL | Count |
| ----- | ----- | ----- |
AK | Home | 2
AK | Work | 50
AZ | Work | 39

### Result set 2
The second result set should contain one row only, like this:

| NEVER_MODIFIED | RECENTLY_MODIFIED | MODIFIED_NOT_RECENT | 
| -------------- | ----------------- | ------------------- |
| 72 | 3 | 12 |

| Column | Purpose |
| ------ | ------- |
`.NEVER_MODIFIED` | Number of Addresses where `CREATED_ON` and `MODIFIED_ON` are the same
`.RECENTLY_MODIFIED` | Number of Addresses where `CREATED_ON` and `MODIFIED_ON` are different, but within 72 hours of each other
`.MODIFIED_NOT_RECENT` | Number of Addresses where `CREATED_ON` and `MODIFIED_ON` are different and differ by more than 72 hours

	

| :zap:        | Filter this by `@Label` the same as result set 1.<br><br>If possible, please use a CTE in your procedure.       |
|---------------|:------------------------|

| :exclamation:        | The SQL scripts used to create your procedure will be submitted at the end of the assessment. |
|---------------|:------------------------|

## Task 3 - Data import
Now things are getting interesting!

The file *ImportData.csv* contains some new data that needs to be inserted into the database.

The business rules for the import are:

* If a MEMBER already exists with a matching Email Address then update it; otherwise insert a new row

* The "Address" columns should map to a `MEMBER_ADDRESS` record with label "Home". 
	* If all of the address fields are empty, and a "Home" address exists, delete it
	* If address fields are not empty, do an "upsert"; if an address exists with that label then update it, otherwise create a new one
	
* If a row cannot be processed for any reason (errors, insufficient data, etc), skip it and continue processing the other records. Keep track of the skipped rows and the reason they were skipped.

| :exclamation:        | Create a file containing the SQL scripts used to import the data (or an explanation of the tool you used) and the rows that couldn't be processed. These will be submitted at the end of the assessment. |
|---------------|:------------------------|

## Extra credit (optional)
Here are some additional, **optional** things you can do if you really want to impress us.

1. Row 2003 isn't importing correctly; the last name is getting messed up. Why? What could we do about this?

2. A stakeholder reports that the import is processing too slowly. What could you do to speed it up?

## Wrapping it up
Thanks again for spending your time on this!

Now that you're done, please submit to us the following things:

| File | Description | 
| ---- | ----------- |
SqlAssessment_YOUR_NAME.bak | Create a backup of your database 
Task1_deletes.csv | List of the `MEMBER_ADDRESS` rows that were deleted as dupes in Task 1
GET_ADDRESS_STATS.sql | SQL script for creating your stored procedure in Task 2
Task3_solution.txt | Text file explaining how you approached the import task. Did you write scripts? Use a tool?
Task3_errors.csv | List of rows from the import file that could not be fully processed for whatever reason. Should include an indication of the problem or error that was encountered

We'll then set up a time to go over the results with you.

## Final notes
This is meant to approximate a real-world project, so if you have questions or problems please feel free to ask us. Sometimes how you handle problems is just as important as getting the right answer!
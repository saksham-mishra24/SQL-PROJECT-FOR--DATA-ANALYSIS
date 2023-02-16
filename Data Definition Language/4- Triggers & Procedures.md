# Create procedure and statement trigger to allow dmls during business hours:

```sql
   
CREATE OR ALTER PROCEDURE secure_dml
AS
BEGIN
  IF CONVERT(TIME, GETDATE()) NOT BETWEEN '08:00' AND '18:00'
  OR DATENAME(WEEKDAY, GETDATE()) IN ('Saturday', 'Sunday')
  BEGIN
    RAISERROR ('You may only make changes during normal office hours', 16, 1);
    RETURN;
  END
END;
GO

CREATE OR ALTER TRIGGER secure_employees
ON employees
AFTER INSERT, UPDATE, DELETE
AS
BEGIN
  EXEC secure_dml;
END;
GO

DISABLE TRIGGER secure_employees ON employees;

```


```
The code provided consists of a stored procedure named "secure_dml" and a trigger named "secure_employees" that executes the "secure_dml" stored procedure on insert, update, and delete operations on the "employees" table.

The purpose of the stored procedure and trigger is to restrict changes to the "employees" table to normal office hours on weekdays.

The last line of the code, "DISABLE TRIGGER secure_employees ON employees;", disables the "secure_employees" trigger on the "employees" table. 

This may be useful if, for example, you need to perform bulk updates to the table outside of normal office hours and do not want the trigger to raise errors.

However, it is important to remember to re-enable the trigger after performing the bulk updates to ensure that future changes to the table are subject to the restrictions imposed by the trigger. 

The syntax for re-enabling a disabled trigger is "ENABLE TRIGGER trigger_name ON table_name;".
```


# Create procedure to add a row to the JOB_HISTORY table and row trigger 

 * To call the procedure when data is updated in the job_id or department_id columns in the EMPLOYEES table:

```sql 

CREATE PROCEDURE add_job_history
   @p_emp_id INT,
   @p_start_date DATE,
   @p_end_date DATE,
   @p_job_id VARCHAR(10),
   @p_department_id INT
AS
BEGIN
   INSERT INTO job_history (employee_id, start_date, end_date, job_id, department_id)
   VALUES(@p_emp_id, @p_start_date, @p_end_date, @p_job_id, @p_department_id);
END;
GO

```

```
* This SQL code creates a stored procedure named "add_job_history" that inserts a new row into the "job_history" table. The procedure takes in five parameters:

@p_emp_id: an integer representing the employee ID of the person whose job history is being added.
@p_start_date: a date representing the start date of the job history.
@p_end_date: a date representing the end date of the job history.
@p_job_id: a string representing the job ID of the person's job history.
@p_department_id: an integer representing the department ID of the person's job history.

* The INSERT statement within the procedure inserts the values of the parameters into the corresponding columns of the "job_history" table.

* Note that the procedure assumes that the "job_history" table already exists with the appropriate columns. 

* If the table does not exist or the columns do not match the parameters of the procedure, the procedure will fail.

```

```sql
CREATE TRIGGER update_job_history
   ON employees
   AFTER UPDATE OF job_id, department_id
AS
BEGIN
   DECLARE @employee_id INT;
   DECLARE @hire_date DATE;
   DECLARE @job_id VARCHAR(10);
   DECLARE @department_id INT;
   SET @employee_id = (SELECT employee_id FROM inserted);
   SET @hire_date = (SELECT hire_date FROM inserted);
   SET @job_id = (SELECT job_id FROM deleted);
   SET @department_id = (SELECT department_id FROM deleted);
   EXEC add_job_history @employee_id, @hire_date, GETDATE(), @job_id, @department_id;
END;
GO

```

```

* This is a SQL Server trigger that is designed to be executed on the "employees" table. 

* The trigger is defined to execute after an update occurs on the "job_id" and "department_id" columns of the "employees" table.

When the trigger is fired, it first declares four variables @employee_id, @hire_date, @job_id, and @department_id. 

The trigger then sets the values of these variables based on the data contained in the "inserted" and "deleted" tables.

The "inserted" table contains the new values for the updated row, while the "deleted" table contains the old values for the updated row. 

Therefore, the trigger is designed to extract the employee ID, hire date, old job ID, and old department ID from the "inserted" and "deleted" tables.

Finally, the trigger calls the "add_job_history" stored procedure with the extracted employee ID, hire date, current date, old job ID, and old department ID as parameters.

The purpose of this trigger is to log changes to an employee's job and department history whenever an update is made to these fields in the "employees" table. 

This can be useful for tracking changes in an organization's job and department structure over time.

```

# Create Indexes for Tables.


```sql

CREATE INDEX emp_department_ix ON employees (department_id);
CREATE INDEX emp_job_ix ON employees (job_id);
CREATE INDEX emp_manager_ix ON employees (manager_id);
CREATE INDEX emp_name_ix ON employees (last_name, first_name);
CREATE INDEX dept_location_ix ON departments (location_id);
CREATE INDEX jhist_job_ix ON job_history (job_id);
CREATE INDEX jhist_employee_ix ON job_history (employee_id);
CREATE INDEX jhist_department_ix ON job_history (department_id);
CREATE INDEX loc_city_ix  ON locations (city);
CREATE INDEX loc_state_province_ix  ON locations (state_province);
CREATE INDEX loc_country_ix ON locations (country_id);

```
## Let's Talk a little bit about Indexes -

* An index is a database object that can speed up data retrieval operations on tables or views by providing fast access to specific data. 
* An index works like a book's index, providing a list of keywords with pointers to the pages where the information can be found.
* An index consists of one or more columns from a table or view, along with a pointer to the physical location of the data on disk. 
* When a query is executed that involves a table or view with an index, the SQL Server query optimizer can use the index to quickly find the rows that match the query criteria, rather than scanning the entire table or view.

There are two main types of indexes in SQL Server:

# * Clustered Index: 

* This type of index determines the physical order of data in a table. 
* A table can have only one clustered index, and it is created on the primary key by default, if one exists. 
* If no primary key exists, a clustered index can be created on a non-primary key column.

# * Nonclustered Index: 

* This type of index does not determine the physical order of data in a table. Instead, it creates a separate data structure that points to the physical location of the data. 
* A table can have multiple nonclustered indexes, and they can be created on any column or combination of columns in a table.


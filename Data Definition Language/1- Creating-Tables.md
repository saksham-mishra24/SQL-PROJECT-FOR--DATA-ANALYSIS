# Create the REGIONS table to hold region information for locations.
 * HR.LOCATIONS table has a foreign key to this table.

```sql
CREATE TABLE regions (
      region_id INT NOT NULL, 
      region_name VARCHAR(25)
      );
      
CREATE UNIQUE INDEX reg_id_ui ON regions (region_id);

ALTER TABLE regions 
ADD CONSTRAINT reg_id_pk PRIMARY KEY (region_id);
```
* NOTE - DROP INDEX command is used to delete an index in a table.

`
 DROP INDEX table_name.index_name;
`

# Create Countries table for country information for customers and company locations.
 * OE.CUSTOMERS table and HR.LOCATIONS table table has a foreign key to this table.

```sql

CREATE TABLE countries (
    country_id CHAR(2) NOT NULL,
    country_name VARCHAR(40),
    region_id INT,
    CONSTRAINT country_c_id_pk PRIMARY KEY (country_id)
) 
WITH (DATA_COMPRESSION = ROW);

ALTER TABLE countries 
ADD constraint countr_reg_fk FOREIGN KEY (region_id) REFERENCES regions(region_id)
ALTER TABLE countries
ADD CONSTRAINT country_id_nn CHECK (country_id IS NOT NULL);
;
```
* OR

```sql
CREATE TABLE countries (
    country_id CHAR(2) CONSTRAINT country_id_nn NOT NULL,
    country_name VARCHAR(40),
    region_id INT,
    CONSTRAINT country_c_id_pk PRIMARY KEY (country_id)
) 
WITH (DATA_COMPRESSION = ROW);

ALTER TABLE countries 
ADD constraint countr_reg_fk FOREIGN KEY (region_id) REFERENCES regions(region_id)
```

</details>
<br>
<details>
  <summary>Click here to check the explanation of the code !</summary>
<br>
  
* The code you provided creates a table called "countries" with three columns: "country_id", "country_name", and "region_id". The "country_id" column is of type CHAR(2), which means it can store two characters, and is defined as NOT NULL, which means it must have a value for every row. The "country_name" column is of type VARCHAR(40), which means it can store up to 40 characters, and is not defined as NOT NULL, which means it can have a NULL value. The "region_id" column is of type INT and is also not defined as NOT NULL, which means it can have a NULL value.

* The table also has a primary key constraint on the "country_id" column, which means that each row in the table must have a unique value in that column. Finally, the table is defined with the option "WITH (DATA_COMPRESSION = ROW)", which means that the data in the table will be compressed at the row level to save disk space.

* The first ALTER TABLE statement adds a foreign key constraint to the "countries" table. This constraint, called "countr_reg_fk", references the "region_id" column of the "regions" table. This means that each value in the "region_id" column of the "countries" table must correspond to a value in the "region_id" column of the "regions" table, or be NULL.

* The second ALTER TABLE statement adds a check constraint to the "countries" table. This constraint, called "country_id_nn", ensures that the "country_id" column is not NULL for each row in the table. If a row is inserted or updated in the table with a NULL value for "country_id", the check constraint will prevent the transaction from completing.

Overall, this SQL code creates a table of countries, with a primary key, a foreign key, and a check constraint to ensure data integrity.
</details>
<br>

* HEY ! EXTRA INFO - 
  By default, SQL Server creates a clustered index on the primary key column if one is not explicitly defined.
    
  
# Create the LOCATIONS table to hold address information for company departments.
 * HR.DEPARTMENTS has a foreign key to this table.

```sql
CREATE TABLE locations( 
     location_id int not null, 
     street_address VARCHAR(40),
     postal_code VARCHAR(12), 
     city VARCHAR(30) NOT NULL, 
     state_province VARCHAR(25), 
     country_id CHAR(2)
     )
CREATE UNIQUE INDEX loc_id_pk ON locations (location_id) ;

ALTER TABLE locations 
ADD PRIMARY KEY (location_id) ,FOREIGN KEY (country_id) REFERENCES countries(country_id) ;

CREATE SEQUENCE locations_seq START WITH 3300 INCREMENT BY 100 MAXVALUE 9900;
```

`` CREATE SEQUENCE -  Useful for any subsequent addition of rows to locations table Rem Starts with 3300.``


# Create the DEPARTMENTS table to hold company department information.
 * HR.EMPLOYEES and HR.JOB_HISTORY have a foreign key to this table.

```sql
CREATE TABLE departments ( 
      department_id int NOT NULL,
      department_name VARCHAR(30)  NOT NULL , 
      manager_id int , 
      location_id int
      );

CREATE UNIQUE INDEX dept_id_pk ON departments (department_id)

ALTER TABLE departments 
ADD PRIMARY KEY (department_id), FOREIGN KEY (location_id)REFERENCES locations (location_id) ;

CREATE SEQUENCE departments_seq START WITH 280 INCREMENT BY 10 MAXVALUE 9990;
```
# Create the JOBS table to hold the different names of job roles within the company.
* HR.EMPLOYEES has a foreign key to this table.
```sql

CREATE TABLE jobs ( 
     job_id VARCHAR(10),
     job_title VARCHAR(35) CONSTRAINT job_title_nn NOT NULL,
     min_salary INT, 
     max_salary INT
     ) ;
CREATE UNIQUE INDEX job_id_pk  ON jobs (job_id) ;
ALTER TABLE jobs
ADD ( CONSTRAINT job_id_pk  PRIMARY KEY(job_id)) ;
```


# Create the EMPLOYEES table to hold the employee personnel information for the company.
* HR.EMPLOYEES has a self referencing foreign key to this table.

```sql       
CREATE TABLE employees ( 
     employee_id int NOT NULL, 
     first_name VARCHAR(20), 
     last_name VARCHAR(25) CONSTRAINT emp_last_name_nn NOT NULL, 
     email VARCHAR(25) CONSTRAINT emp_email_nn NOT NULL, 
     phone_number VARCHAR(20), 
     hire_date DATE CONSTRAINT emp_hire_date_nn NOT NULL, 
     job_id VARCHAR(10) CONSTRAINT emp_job_nn NOT NULL, 
     salary INT, 
     commission_pct INT, 
     manager_id INT, 
     department_id INT, 
     CONSTRAINT emp_salary_minCHECK (salary > 0), 
     CONSTRAINT emp_email_uk UNIQUE (email)
   ) ;
   
CREATE UNIQUE INDEX emp_emp_id_pk ON employees (employee_id) ;
 
ALTER TABLE employees
ADD PRIMARY KEY (employee_id), FOREIGN KEY (department_id)  REFERENCES departments ,FOREIGN KEY (job_id) REFERENCES jobs (job_id) ,FOREIGN KEY (manager_id)
  REFERENCES employees;
 
ALTER TABLE departments
ADD FOREIGN KEY (manager_id) REFERENCES employees (employee_id) ;
 
CREATE SEQUENCE employees_seq START WITH 207 INCREMENT BY 1
``` 

* HEY ! EXTRA INFO - 
 1. To enable a foreign key in SQL Server:

``ALTER TABLE table_name
CHECK CONSTRAINT fk_name;``

2. To temporarily disable the checking of referential integrity constraints for the specified foreign key constraints.

``ALTER TABLE table_name
NO CHECK CONSTRAINT fk_name;``

3. The SQL command "sp_rename" is used to rename an existing object in the database, such as a table, column, index, or constraint.
``sp_rename  'fk_employee' , 'fk_employe_manager_id'``

# Create JOB_HISTORY table to hold history of jobs that employees have held in  past. 
* HR.JOBS, HR_DEPARTMENTS, and HR.EMPLOYEES have a foreign key to this table.

```sql
CREATE TABLE job_history ( 
      employee_id int NOT NULL, 
      start_date DATE NOT NULL, 
      end_date DATE NOT NULL, 
      job_id VARCHAR(10)  NOT NULL, 
      department_id int,
      CHECK (end_date > start_date)
      ) ;

CREATE UNIQUE INDEX jhist_emp_id_st_date_pk  ON job_history (employee_id, start_date) ;

ALTER TABLE job_history 
ADD PRIMARY KEY (employee_id, start_date), FOREIGN KEY (job_id) REFERENCES jobs, FOREIGN KEY (employee_id) REFERENCES employees, 
FOREIGN KEY (department_id) REFERENCES departments
```

# Create EMP_DETAILS_VIEW that joins employees, jobs, departments, jobs, countries, and locations table to provide details about employees. 

```sql
CREATE VIEW emp_details_view
   (employee_id,
   job_id,
   manager_id,
   department_id,
   location_id,
   country_id,
   first_name,
   last_name,
   salary,
   commission_pct,
   department_name,
   job_title,
   city,
   state_province,
   country_name,
   region_name)
   AS SELECT
   e.employee_id, 
   e.job_id, 
   e.manager_id, 
   e.department_id,
   d.location_id,
   l.country_id,
   e.first_name,
   e.last_name,
   e.salary,
   e.commission_pct,
   d.department_name,
   j.job_title,
   l.city,
   l.state_province,
   c.country_name,
   r.region_name
   FROM
   employees e,
   departments d,
   jobs j,
   locations l,
   countries c,
   regions r
   WHERE e.department_id = d.department_id
   AND d.location_id = l.location_id
   AND l.country_id = c.country_id
   AND c.region_id = r.region_id
   AND j.job_id = e.job_id
```




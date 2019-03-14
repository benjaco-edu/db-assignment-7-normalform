# Database assignment normal form

_Assignment:_ 
https://github.com/datsoftlyngby/soft2019spring-databases/blob/master/assignments/Assignment7.md#exercise-3

![](https://raw.githubusercontent.com/benjaco-edu/db-assignment-7-normalform/master/view.png)



### Excercise 1

Mention which violation there are to:

_First normal form (if any)_

In some cases depending on the businesslogic the first NF. might be violated by the customername column and the address column due to the fact that they contain more than one value (e.i. customername consists of both a first and a last name)

_Second normal form (if any)_

(the table is not in NF1)

The second NF is violated in the following columns:
custCity and custZip, basically all the columns containing information about the rep 

_Third normal form (if any)_

(the table is not in NF2)

This is the same problem as above, there are several columns that are not solely dependent on the primary key, eg. all the rep* fields are dependent on the employe and its office.


### Excercise 2

Assume we did not include the customerNumber in the table. What could be a key, and do we get the same violations of the normal forms?

We considered to create a composite key of out the name and phonenumber to uniquely identify the customers, but in a (very)rare case you could end up with a different person(customer), with the same name acquiring the phonenumber after it have been given up(for some reason). This same argument can be used for the address as well.
 
This ultimately leads to the conclusion that none of the column - composite or otherwise can functions as a valid primary key. 
Without a primary key the table would therefore be violating the NF.1 and thereby all the rest of the NFs.

### Excercise 3

Write a safe update statement that change the repPhone column from oldNumber (say 12345678) to newNumber (say 87654321).

```sql
update CustomerOverview set repPhone = 87654321 where repPhone = 12345678
```
The ‘safe’ part of this update is the fact that the ‘where’-clause is used

Write an update of repEmail which do not update properly (do not update it everywhere it should)

```sql
update CustomerOverview set repEmail = “new@email.address” where customerName = "Mini Wheels Co." and repName = "Leslie Jennings"
```

The ‘unsafe’ part of this update is the fact that both the repName and the customerName can change, this would lead to undesired updates.


### Excercise 4

Draw a b-tree of following with fanout of 4

```sql
with my_cust as
   (select customerNumber,
       customerName,
       customers.country as custCountry,
       offices.city      as repCity
    from employees
       inner join customers on employees.employeeNumber = customers.salesRepEmployeeNumber
       inner join offices on employees.officeCode = offices.officeCode)
select *
from my_cust
where repCity = 'Sydney'
```

![](https://raw.githubusercontent.com/benjaco-edu/db-assignment-7-normalform/master/board.jpg)

We also tried to see how the data would have looked like on a read harddrive with 4k pages.

By running the following sql-statement, the actual byte-size of the customerName is calculated. This calculations reveals that all 10 customernames would in fact fit on a single page. This would make the drawing uninteressting, so in the follow drawing it has been decided that the page split will be be 3 -(4-3-3)-(data). 

```sql
with my_cust as
   (select customerNumber,
       customerName,
       customers.country as custCountry,
       offices.city      as repCity
    from employees
       inner join customers on employees.employeeNumber = customers.salesRepEmployeeNumber
       inner join offices on employees.officeCode = offices.officeCode)
       
select *,  sum(le) over(order by customerName) from (select *, length(customerName) as le
from my_cust
where repCity = 'Sydney' order by customerName) as sub;
```

![](https://raw.githubusercontent.com/benjaco-edu/db-assignment-7-normalform/master/pages.png)

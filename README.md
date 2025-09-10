## 1. Lets begin with an example of a  SQL query(~prompt):

```sql
SELECT $Name, $Parent, $PartyGSTIN, $ClosingBalance FROM ledger
```
Expected response(table):

| $Name               | $Parent          | $PartyGSTIN       | $ClosingBalance |
|---------------------|------------------|-------------------|-----------------|
| Nimbus Retail Pvt Ltd | Sundry Debtors  | 27AACCN4356P1Z4   | 2,14,750.00     |
| Kaveri Agro Mart    | Sundry Debtors    | 33AABFK7685R1Z1   | 18,960.00       |
| BlueLeaf Stationery | Sundry Debtors    | 07ABCPD1234L1Z7   | 920.00          |
| Shree Metal Works   | Sundry Creditors  | 29AAGFS8821B1Z9   | 31,487.20       |

It show the  $Name, $Parent, $PartyGSTIN, $ClosingBalance (Columns) from the table Ledger. There are 250+ tables in tally.

## 2. Basic Filter  -   ```WHERE```

For filtering a specific value/datapoint in a column, we can use WHERE

```sql
SELECT $Name, $Parent, $PartyGSTIN, $ClosingBalance FROM ledger WHERE $Parent = 'Sundry Debtors'
```

| $Name                 | $Parent        | $PartyGSTIN     | $ClosingBalance |
| --------------------- | -------------- | --------------- | ---------------- |
| Nimbus Retail Pvt Ltd | Sundry Debtors | 27AACCN4356P1Z4 | 2,14,750.00      |
| Kaveri Agro Mart      | Sundry Debtors | 33AABFK7685R1Z1 | 18,960.00        |
| BlueLeaf Stationery   | Sundry Debtors |                 | 920.00           |

## 3. Wildcard filter  -  ```LIKE```

```sql
SELECT $Name, $Parent, $PartyGSTIN, $ClosingBalance FROM ledger WHERE $Parent LIKE '%Sundry%'
```

| $Name                 | $Parent          | $PartyGSTIN     | $ClosingBalance |
| --------------------- | ---------------- | --------------- | ---------------- |
| Nimbus Retail Pvt Ltd | Sundry Debtors   | 27AACCN4356P1Z4 | 2,14,750.00      |
| Kaveri Agro Mart      | Sundry Debtors   | 33AABFK7685R1Z1 | 18,960.00        |
| BlueLeaf Stationery   | Sundry Debtors   |                 | 920.00           |
| Shree Metal Works     | Sundry Creditors | 29AAGFS8821B1Z9 | 31,487.20        |

## 4. Filtering blanks   -  ```Null``` and  ```Not Null```

```sql
SELECT $Name, $Parent, $PartyGSTIN, $ClosingBalance FROM ledger WHERE $PartyGSTIN IS NULL
```

| $Name               | $Parent        | $PartyGSTIN  | $ClosingBalance |
| ------------------- | -------------- | ------------ | ---------------- |
| BlueLeaf Stationery | Sundry Debtors |              | 920.00           |

```sql
SELECT $Name, $Parent, $PartyGSTIN, $ClosingBalance FROM ledger WHERE $PartyGSTIN IS NOT NULL
```

| $Name                 | $Parent          | $PartyGSTIN     | $ClosingBalance |
| --------------------- | ---------------- | --------------- | ---------------- |
| Nimbus Retail Pvt Ltd | Sundry Debtors   | 27AACCN4356P1Z4 | 2,14,750.00      |
| Kaveri Agro Mart      | Sundry Debtors   | 33AABFK7685R1Z1 | 18,960.00        |
| Shree Metal Works     | Sundry Creditors | 29AAGFS8821B1Z9 | 31,487.20        |


## 5. Filter - to Select only with Debit Values prefix   -  ```$$IsDr```  

```sql
SELECT $Name, $Parent, $PartyGSTIN, $ClosingBalance FROM ledger WHERE $$IsDr:$ClosingBalance
```

| $Name                 | $Parent        | $PartyGSTIN     | $ClosingBalance  |
| --------------------- | -------------- | --------------- | ---------------- |
| Nimbus Retail Pvt Ltd | Sundry Debtors | 27AACCN4356P1Z4 | 2,14,750.00      |
| Kaveri Agro Mart      | Sundry Debtors | 33AABFK7685R1Z1 | 18,960.00        |
| BlueLeaf Stationery   | Sundry Debtors |                 | 920.00           |

*(for credit balances just use NOT $$IsCr)*


## 6. Sort   -  ```ORDER BY```

```sql
SELECT $Name, $Parent, $PartyGSTIN, $ClosingBalance FROM ledger ORDER BY $ClosingBalance
```

| $Name                 |  $Parent         | $PartyGSTIN     |  $ClosingBalance |
| --------------------- | ---------------- | --------------- | ---------------- |
| Shree Metal Works     | Sundry Creditors | 29AAGFS8821B1Z9 | 31,487.20        |
| BlueLeaf Stationery   | Sundry Debtors   |                 | 920.00           |
| Kaveri Agro Mart      | Sundry Debtors   | 33AABFK7685R1Z1 | 18,960.00        |
| Nimbus Retail Pvt Ltd | Sundry Debtors   | 27AACCN4356P1Z4 | 2,14,750.00      |

**By an ingenious design, tally will consider Credit as Positive values and Debit as negative values
The above answer response is in descending order of closing balance, and the creditor at the first is positive value, debitors have negative value.** 

To reverse the order, you can add  ```DESC```  to the end:

```sql
SELECT $Name, $Parent, $PartyGSTIN, $ClosingBalance FROM ledger ORDER BY $ClosingBalance DESC
```

| \$Name                | \$Parent         | \$PartyGSTIN    | \$ClosingBalance |
| --------------------- | ---------------- | --------------- | ---------------- |
| Nimbus Retail Pvt Ltd | Sundry Debtors   | 27AACCN4356P1Z4 | 2,14,750.00      |
| Shree Metal Works     | Sundry Creditors | 29AAGFS8821B1Z9 | 31,487.20        |
| Kaveri Agro Mart      | Sundry Debtors   | 33AABFK7685R1Z1 | 18,960.00        |
| BlueLeaf Stationery   | Sundry Debtors   |                 | 920.00           |


## 7. Show (-)ve symbols debit values  -  ```$$AscrAmt:``` 

For more clarity, you can add Prefix ```$$AscrAmt:``` to show the value with correct symbols as stored in Tally.

```sql
SELECT $Name, $Parent, $PartyGSTIN, $$AscrAmt:$ClosingBalance FROM ledger ORDER BY $ClosingBalance DESC
```

| \$Name                | \$Parent         | \$PartyGSTIN    | \$\$AscrAmt:\$ClosingBalance |
| --------------------- | ---------------- | --------------- | ---------------------------- |
| BlueLeaf Stationery   | Sundry Debtors   |                 | (-)920.00                    |
| Kaveri Agro Mart      | Sundry Debtors   | 33AABFK7685R1Z1 | (-)18,960.00                 |
| Nimbus Retail Pvt Ltd | Sundry Debtors   | 27AACCN4356P1Z4 | (-)2,14,750.00               |
| Shree Metal Works     | Sundry Creditors | 29AAGFS8821B1Z9 | 31,487.20                    |


*($$AsDrAmt: is possible but dont bother, and dont use NOT $$AscrAmt:)*



## 8. Grouping (Like pivot)  -  ```GROUP BY```

```sql

SELECT $Parent, $ClosingBalance FROM ledger GROUP BY $Parent
```

| \$Parent         | $ClosingBalance |
| ---------------- |--------------   |
| Sundry Debtors   |     2,34,630.00 |
| Sundry Creditors |       31,487.20 |


## 9. Filtering by Range  -  ```BETWEEN``` and ```AND```

```sql
SELECT $Name, $Parent, $ClosingBalance FROM Ledger WHERE $ClosingBalance BETWEEN 10000 AND 20000
```

| \$Name           | \$Parent       | \$ClosingBalance |
| ---------------- | -------------- | ---------------- |
| Kaveri Agro Mart | Sundry Debtors | 18,960.00        |

---

## 10. Boolean Operators in   ```WHERE```

You can also use comparison operators inside the `WHERE` clause.

| Operator | Description              |
| -------- | ------------------------ |
| =        | Equal to                 |
| !=       | Not equal to (also <>    |
| >        | Greater than             |
| <        | Less than                |
| >=       | Greater than or equal to |
| <=       | Less than or equal to    |

---

### Example: Greater Than   ```>```

```sql
SELECT $Name, $ClosingBalance FROM Ledger WHERE $ClosingBalance > 20000
```

| \$Name                | \$ClosingBalance |
| --------------------- | ---------------- |
| Nimbus Retail Pvt Ltd | 2,14,750.00      |
| Shree Metal Works     | 31,487.20        |

---

### Example: Not Equal To   ```!=```

```sql
SELECT $Name, $ClosingBalance FROM Ledger WHERE $ClosingBalance != 920
```

| \$Name                | \$ClosingBalance |
| --------------------- | ---------------- |
| Nimbus Retail Pvt Ltd | 2,14,750.00      |
| Kaveri Agro Mart      | 18,960.00        |
| Shree Metal Works     | 31,487.20        |

---
### Example: Multiple boolean operators

```sql
SELECT $Name, $Parent, $ClosingBalance FROM Ledger  WHERE $ClosingBalance > 10000  AND $ClosingBalance < 20000  AND $Parent = "Sundry Debtors"
```

| \$Name           | \$Parent       | \$ClosingBalance |
| ---------------- | -------------- | ---------------- |
| Kaveri Agro Mart | Sundry Debtors | 18,960.00        |


---

## 11. Top 'n' items by value
* this is very very experimental. inconsistent results. not mission ready.
  
```sql
Select Top 3 $ClosingBalance from ledger
```

| \$Name                | \$ClosingBalance |
| --------------------- | ---------------- |
| Nimbus Retail Pvt Ltd | 2,14,750.00      |
| Shree Metal Works     | 31,487.20        |
| Kaveri Agro Mart      | 18,960.00        |


#FAQs, etc 

1. The SQL specific keywords (SELECT, FROM) used in clauses are not CaSe SenSitIVE.
i.e. 
```sql
SELECT $Name, $Parent, $PartyGSTIN, $ClosingBalance FROM ledger
```
and 
```sql
select $Name, $Parent, $PartyGSTIN, $ClosingBalance from ledger
```
will give the same response. 

2. Ymmv. There were many instances same query works on one data and not on others. 

### Notes for machines (pandas, etc):
1. The negative symbol will be in parenthesis.
2. The numerical values will return with commas.
3. Date columns will return as YYYYMMdd.

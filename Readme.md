README — Tally SQL / ODBC Query Examples

A quick reference of common Tally SQL/ODBC query examples and behaviours for extracting ledger data (columns, filters, sorting, grouping). Use `Ctrl+N` to open the calc panel — there you can run or test queries interactively.

## 1. Lets begin with an example:

						   

	  
															   
   

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

It show the  $Name, $Parent, $PartyGSTIN, $ClosingBalance (Columns) from the table - "Ledger". There are 250+ tables in Tally.
																				 

   

							  

## 2. Basic Filter  -   `WHERE`

For filtering a specific value/datapoint in a column, we can use WHERE

```sql
SELECT $Name, $Parent, $PartyGSTIN, $ClosingBalance FROM ledger WHERE $Parent = 'Sundry Debtors'
```

| $Name                 | $Parent        | $PartyGSTIN     | $ClosingBalance |
| --------------------- | -------------- | --------------- | ---------------- |
| Nimbus Retail Pvt Ltd | Sundry Debtors | 27AACCN4356P1Z4 | 2,14,750.00      |
| Kaveri Agro Mart      | Sundry Debtors | 33AABFK7685R1Z1 | 18,960.00        |
| BlueLeaf Stationery   | Sundry Debtors |                 | 920.00           |

---   
																															
## 3. Wildcard filter  -  `LIKE`

```sql
SELECT $Name, $Parent, $PartyGSTIN, $ClosingBalance FROM ledger WHERE $Parent LIKE '%Sundry%'
```

| $Name                 | $Parent          | $PartyGSTIN     | $ClosingBalance |
| --------------------- | ---------------- | --------------- | ---------------- |
| Nimbus Retail Pvt Ltd | Sundry Debtors   | 27AACCN4356P1Z4 | 2,14,750.00      |
| Kaveri Agro Mart      | Sundry Debtors   | 33AABFK7685R1Z1 | 18,960.00        |
| BlueLeaf Stationery   | Sundry Debtors   |                 | 920.00           |
| Shree Metal Works     | Sundry Creditors | 29AAGFS8821B1Z9 | 31,487.20        |

---
																													  
## 4. Filtering blanks   -  `IS NULL` and  `IS NOT NULL`

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

	  

## 5. Filter - to Select only with Debit Values prefix   -  `$$IsDr`  

```sql
SELECT $Name, $Parent, $PartyGSTIN, $ClosingBalance FROM ledger WHERE $$IsDr:$ClosingBalance
```

| $Name                 | $Parent        | $PartyGSTIN     | $ClosingBalance  |
| --------------------- | -------------- | --------------- | ---------------- |
| Nimbus Retail Pvt Ltd | Sundry Debtors | 27AACCN4356P1Z4 | 2,14,750.00      |
| Kaveri Agro Mart      | Sundry Debtors | 33AABFK7685R1Z1 | 18,960.00        |
| BlueLeaf Stationery   | Sundry Debtors |                 | 920.00           |

*(for credit balances just use NOT $$IsCr)*

					

	  

## 6. Sort   -  `ORDER BY`

```sql
SELECT $Name, $Parent, $PartyGSTIN, $ClosingBalance FROM ledger ORDER BY $ClosingBalance
```

| $Name                 |  $Parent         | $PartyGSTIN     |  $ClosingBalance |
| --------------------- | ---------------- | --------------- | ---------------- |
| Shree Metal Works     | Sundry Creditors | 29AAGFS8821B1Z9 | 31,487.20        |
| BlueLeaf Stationery   | Sundry Debtors   |                 | 920.00           |
| Kaveri Agro Mart      | Sundry Debtors   | 33AABFK7685R1Z1 | 18,960.00        |
| Nimbus Retail Pvt Ltd | Sundry Debtors   | 27AACCN4356P1Z4 | 2,14,750.00      |

** By an ingenious design, tally will consider Credit as Positive values and Debit as negative values
The above answer response is in ascending order - Credit (+ve) values first, then debit(-ve) values 

To reverse the order, you can add  `DESC`  to the end:

```sql
SELECT $Name, $Parent, $PartyGSTIN, $ClosingBalance FROM ledger ORDER BY $ClosingBalance DESC
```

| $Name                 | $Parent          | $PartyGSTIN     | $ClosingBalance  |
| --------------------- | ---------------- | --------------- | ---------------- |
| Nimbus Retail Pvt Ltd | Sundry Debtors   | 27AACCN4356P1Z4 | 2,14,750.00      |
| Shree Metal Works     | Sundry Creditors | 29AAGFS8821B1Z9 | 31,487.20        |
| Kaveri Agro Mart      | Sundry Debtors   | 33AABFK7685R1Z1 | 18,960.00        |
| BlueLeaf Stationery   | Sundry Debtors   |                 | 920.00           |

																						 

## 7. To Explicitly show (-)ve symbols for debit values  -  `$$AscrAmt:` 

For more clarity, you can add Prefix `$$AscrAmt:` to show the value with correct symbols as stored in Tally.

```sql
SELECT $Name, $Parent, $PartyGSTIN, $$AscrAmt:$ClosingBalance FROM ledger ORDER BY $ClosingBalance DESC
```

| $Name                 | $Parent          | $PartyGSTIN     | $$AscrAmt:$ClosingBalance   |
| --------------------- | ---------------- | --------------- | ---------------------------- |
| BlueLeaf Stationery   | Sundry Debtors   |                 | (-)920.00                    |
| Kaveri Agro Mart      | Sundry Debtors   | 33AABFK7685R1Z1 | (-)18,960.00                 |
| Nimbus Retail Pvt Ltd | Sundry Debtors   | 27AACCN4356P1Z4 | (-)2,14,750.00               |
| Shree Metal Works     | Sundry Creditors | 29AAGFS8821B1Z9 | 31,487.20                    |


*(`$$AsDrAmt:` is possible but dont bother, and dont use NOT $$AscrAmt:)*

										  

	  

## 8. Grouping (pivot-like)  -  `GROUP BY`

```sql

SELECT $Parent, $ClosingBalance FROM ledger GROUP BY $Parent
```

| \$Parent         | $ClosingBalance |
| ---------------- |--------------   |
| Sundry Debtors   |     2,34,630.00 |
| Sundry Creditors |       31,487.20 |

						 

	  
																																
## 9. Filtering by range  -  `BETWEEN ... AND ...`

```sql
SELECT $Name, $Parent, $ClosingBalance FROM Ledger WHERE $ClosingBalance BETWEEN 10000 AND 20000
```

| \$Name           | \$Parent       | \$ClosingBalance |
| ---------------- | -------------- | ---------------- |
| Kaveri Agro Mart | Sundry Debtors | 18,960.00        |

---

## 10. Boolean Operators in   `WHERE`

You can also use comparison operators inside the `WHERE` clause.

| Operator | Description              |
| -------- | ------------------------ |
| =        | Equal to                 |
| !=       | Not equal to (also <>)   |
| >        | Greater than             |
| <        | Less than                |
| >=       | Greater than or equal to |
| <=       | Less than or equal to    |

---

### Example: Greater Than   `>`

```sql
SELECT $Name, $ClosingBalance FROM Ledger WHERE $ClosingBalance > 20000
```

   

					
| \$Name                | \$ClosingBalance |
| --------------------- | ---------------- |
| Nimbus Retail Pvt Ltd | 2,14,750.00      |
| Shree Metal Works     | 31,487.20        |

   

										  

---

### Example: Not Equal To   `!=`

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
**this is very very experimental. inconsistent results. not mission ready.
  
```sql
Select Top 3 $ClosingBalance from ledger
```

| \$Name                | \$ClosingBalance |
| --------------------- | ---------------- |
| Nimbus Retail Pvt Ltd | 2,14,750.00      |
| Shree Metal Works     | 31,487.20        |
| Kaveri Agro Mart      | 18,960.00        |



## FAQs, etc 

1. **Ymmv.** Behaviour can vary across Tally versions, datasets and ODBC drivers. Keep example queries as a guide and test on your dataset.  
2. The SQL specific keywords (SELECT, FROM) used in clauses are not CaSe SenSitIVE.


## Notes for machines (pandas, scripts, etc):

1. **Number formatting (assumptions)**
   * The numerical values in examples use **Indian-style grouping** (e.g., `2,14,750.00` - lakh grouping).
   * Decimal separator is `.` and thousands grouping uses commas.
   
   # This is not fully tested:
 
2.  ** Debits may appear as parentheses (e.g., `(-)920.00`). Normalise by a helper function that interprets Indian grouping when parsing numeric strings.But beware of Negative balances 
   
3. Date columns will return as YYYYMMdd.

4. In Tally datasets, Credit values appear as positive and Debit as negative (Tally's stored sign/representation). So the numeric order might look reversed if you interpret sign differently — use `$$AscrAmt:` if you want the stored/annotated representation (see section 7)
																	 
   
6. **Tally specifc semantics**
Tally prefixes and functions like `$$IsDr:`, `$$IsCr`, `$$AscrAmt:`(not standard SQL). Handle them in post-processing (e.g., interpret `$$IsDr:$ClosingBalance` as a boolean indicator to filter debits, credits).

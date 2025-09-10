## 1. Lets begin with an example:

An SQL query(prompt), followed by the response(table).

```sql
SELECT $Name, $Parent, $PartyGSTIN, $ClosingBalance FROM ledger
```


| $Name               | $Parent          | $PartyGSTIN       | $ClosingBalance |
|---------------------|------------------|-------------------|-----------------|
| Nimbus Retail Pvt Ltd | Sundry Debtors   | 27AACCN4356P1Z4   | 2,14,750.00     |
| Kaveri Agro Mart    | Sundry Debtors    | 33AABFK7685R1Z1   | 18,960.00       |
| BlueLeaf Stationery | Sundry Debtors    | 07ABCPD1234L1Z7   | 920.00          |
| Shree Metal Works   | Sundry Creditors  | 29AAGFS8821B1Z9   | 31,487.20       |

It show the  $Name, $Parent, $PartyGSTIN, $ClosingBalance (Columns) from the table Ledger. There are 250+ tables in tally.

## 3. Simple Filters  -   ```WHERE```

For filtering a specific value/datapoint in a column, we can use WHERE

```sql
SELECT $Name, $Parent, $PartyGSTIN, $ClosingBalance FROM ledger WHERE $Parent = 'Sundry Debtors'
```

| $Name                 | $Parent        | $PartyGSTIN     | $ClosingBalance |
| --------------------- | -------------- | --------------- | ---------------- |
| Nimbus Retail Pvt Ltd | Sundry Debtors | 27AACCN4356P1Z4 | 2,14,750.00      |
| Kaveri Agro Mart      | Sundry Debtors | 33AABFK7685R1Z1 | 18,960.00        |
| BlueLeaf Stationery   | Sundry Debtors |                 | 920.00           |

### Wildcard filter  -  ```LIKE```

```sql
SELECT $Name, $Parent, $PartyGSTIN, $ClosingBalance FROM ledger WHERE $Parent LIKE '%Sundry%'
```

| $Name                 | $Parent          | $PartyGSTIN     | $ClosingBalance |
| --------------------- | ---------------- | --------------- | ---------------- |
| Nimbus Retail Pvt Ltd | Sundry Debtors   | 27AACCN4356P1Z4 | 2,14,750.00      |
| Kaveri Agro Mart      | Sundry Debtors   | 33AABFK7685R1Z1 | 18,960.00        |
| BlueLeaf Stationery   | Sundry Debtors   |                 | 920.00           |
| Shree Metal Works     | Sundry Creditors | 29AAGFS8821B1Z9 | 31,487.20        |

## 4. Null and Not Null

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






## 2. Filters
### To only Ledgers with Debit Balances ($$IsDr)

```sql
SELECT $Name, $Parent, $PartyGSTIN, $ClosingBalance FROM ledger WHERE $$IsDr:$ClosingBalance
```

| \$Name                | \$Parent       | \$PartyGSTIN    | \$ClosingBalance |
| --------------------- | -------------- | --------------- | ---------------- |
| Nimbus Retail Pvt Ltd | Sundry Debtors | 27AACCN4356P1Z4 | 2,14,750.00      |
| Kaveri Agro Mart      | Sundry Debtors | 33AABFK7685R1Z1 | 18,960.00        |
| BlueLeaf Stationery   | Sundry Debtors |                 | 920.00           |

*(for credit balances just use NOT $$IsCr)*



WIP
## 3. Simple sort (ORDER BY): 

```sql
SELECT $Name, $Parent, $PartyGSTIN, $ClosingBalance FROM ledger ORDER BY $ClosingBalance
```

| $Name                 |  $Parent         | $PartyGSTIN     |  $ClosingBalance |
| --------------------- | ---------------- | --------------- | ---------------- |
| Shree Metal Works     | Sundry Creditors | 29AAGFS8821B1Z9 | 31,487.20        |
| BlueLeaf Stationery   | Sundry Debtors   |                 | 920.00           |
| Kaveri Agro Mart      | Sundry Debtors   | 33AABFK7685R1Z1 | 18,960.00        |
| Nimbus Retail Pvt Ltd | Sundry Debtors   | 27AACCN4356P1Z4 | 2,14,750.00      |

By an ingenius design, tally will consider Credit as Positive values and Debit as negative values
The above answer response is in descending order of closing balance, and the creditor at the first is positive value, debitors have negative value. 

To reverse the order, you can add DESC to the end

```sql
SELECT $Name, $Parent, $PartyGSTIN, $ClosingBalance FROM ledger ORDER BY $ClosingBalance DESC
```

For more clarity, you can add Prefix ```$$AscrAmt:``` to show the values with correct symbols as stored in Tally.

```sql
SELECT $Name, $Parent, $PartyGSTIN, $$AscrAmt:$ClosingBalance FROM ledger ORDER BY $ClosingBalance DESC
```


### Top-N by balance

```sql
SELECT TOP 3 $Name, $ClosingBalance FROM ledger ORDER BY $ClosingBalance DESC
```

| \$Name                | \$ClosingBalance |
| --------------------- | ---------------- |
| Nimbus Retail Pvt Ltd | 2,14,750.00      |
| Shree Metal Works     | 31,487.20        |
| Kaveri Agro Mart      | 18,960.00        |

### Grouping by parent (distinct parents)

```sql
SELECT $Parent FROM ledger GROUP BY $Parent
```

| \$Parent         |
| ---------------- |
| Sundry Debtors   |
| Sundry Creditors |


#FAQs, etc 

1. The SQL specific keywords (SeLEcT, fRoM) used in clauses are not CaSe SenSitIVE.
i.e. 
```sql
SELECT $Name, $Parent, $PartyGSTIN, $ClosingBalance FROM ledger
```
and 
```sql
select $Name, $Parent, $PartyGSTIN, $ClosingBalance from ledger
```
will give the same response. 

2. The numerical values will return with commas, if you are using the data directly to any other pipeline line polars, keep that in mind.

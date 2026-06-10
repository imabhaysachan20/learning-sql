<!-- How Order by or the sequence the data is stored affects join? -->

## How `ORDER BY` or Data Storage Order Affects Joins

The physical order in which rows are stored, or the order produced by an `ORDER BY` clause, can impact how efficiently a database performs a join.

- **Sorted data** can enable faster algorithms like **Merge Join**, where both tables are scanned once in order.
- **Unsorted data** often requires **Hash Join** or **Nested Loop Join**, which may involve extra memory or repeated lookups.
- If an index already keeps the join columns ordered, the database can avoid an expensive sort operation.

> The result of a SQL query has **no guaranteed order unless you explicitly use `ORDER BY`**. The internal storage order is an optimization detail and should not be relied upon.


<!-- -- Multiple null rows and orderby on that column then which rows will come first at the last ocuple of rows -->

## Multiple `NULL` Values with `ORDER BY`

I learned that when I sort a column containing multiple `NULL` values, SQL does **not guarantee the order among those `NULL` rows**. Even if they appear in a particular order today, they might come back differently the next time.

For example:

```sql
SELECT *
FROM employees
ORDER BY salary;

If several employees have `salary = NULL`, their relative order is undefined.

>  If you need a consistent order, add a secondary sort key:
>
> ```sql
> ORDER BY salary, employee_id;
> ```
>
> This ensures rows with the same value (including `NULL`) are always returned in a predictable order.



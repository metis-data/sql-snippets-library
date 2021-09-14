## table-size
### General
- command-name: table-size
- command-type: SELECT
- last-update: 2021-09-03 16:45
- description: Shows the size, in KB, and the number of rows for each user table. The size does not include the index size. 
- tags: #user-tables

### Parameters
None

### SQL Command
``` sql 
SELECT
    s.name AS SchemaName,
    t.name AS TableName,
    t.object_id AS Object_ID,
    SUM(p.rows) AS Rows,
    SUM(a.total_pages) * 8 AS TotalSpaceKB,
    SUM(a.used_pages) * 8 AS UsedSpaceKB,
    (SUM(a.total_pages) - SUM(a.used_pages)) * 8 AS UnusedSpaceKB
FROM
    sys.tables t
INNER JOIN
    sys.indexes i ON t.OBJECT_ID = i.object_id
INNER JOIN
    sys.partitions p ON i.object_id = p.OBJECT_ID AND i.index_id = p.index_id
INNER JOIN
    sys.allocation_units a ON p.partition_id = a.container_id
LEFT OUTER JOIN
    sys.schemas s ON t.schema_id = s.schema_id
WHERE
    t.name NOT LIKE 'dt%'
    AND t.is_ms_shipped = 0
    AND i.OBJECT_ID > 255
GROUP BY
    s.Name, t.name, t.object_id
ORDER BY
   s.name, t.name
```

### Related Commands
- related-command-name: tableAnalysis

### Links
- [Get size of all tables in database](https://stackoverflow.com/questions/7892334/get-size-of-all-tables-in-database/7892349)
- [sys.allocation_units](https://docs.microsoft.com/en-us/sql/relational-databases/system-catalog-views/sys-allocation-units-transact-sql?view=sql-server-ver15) 

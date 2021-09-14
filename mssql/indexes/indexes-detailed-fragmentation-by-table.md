## indexes-detailed-fragmentation-by-table
### General
- command-name: indexes-detailed-fragmentation-by-table
- command-type: SELECT
- last-update: 2021-09-03 16:45
- description: Shows fragmentation of all indexes in a specific table, by its object-id
- tags: #user-tables

### Parameters
| parameter-name        | data-type          	| default  	|
| :------------- 	|:-------------		| :--------	|
| object-id	        | int 			|  		|


### SQL Command
``` sql 
SELECT
	IDX.Object_ID,
	OBJECT_NAME(IDX.OBJECT_ID) AS Table_Name,
	IDX.index_id as Index_ID,
	IDX.name AS Index_Name,
	IDXPS.index_type_desc AS Index_Type,
	STATS_DATE(IDX.object_id, IDX.index_id) AS 'lastupdated',
	IDXPS.index_depth,
	IDXPS.index_level,
	IDXPS.avg_fragmentation_in_percent  AS Fragmentation_Percentage,
	IDXPS.record_count,
	IDXPS.avg_record_size_in_bytes

FROM
	sys.dm_db_index_physical_stats(DB_ID(), $(object-id), NULL, NULL, 'DETAILED') AS IDXPS
INNER JOIN sys.indexes IDX
	ON IDX.object_id = IDXPS.object_id
	AND IDX.index_id = IDXPS.index_id
WHERE IDX.Object_ID = $(object-id)
ORDER BY Table_Name, Index_ID
```

### Related Commands
- related-command-name: indexAnalysis

### Links
- [A quick look at: dm_db_index_physical_stats](https://www.red-gate.com/simple-talk/blogs/quick-look-at-dm_db_index_physical_stats/)
- [sys.dm_db_index_physical_stats](https://docs.microsoft.com/en-us/sql/relational-databases/system-dynamic-management-views/sys-dm-db-index-physical-stats-transact-sql?view=sql-server-ver15) 

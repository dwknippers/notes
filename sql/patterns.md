# SQL Patterns
## Description
Short collection of commonly used SQL patterns.

### Loop over items in comma seperated string
```sql
declare @S varchar(20)
set @S = '1,2,3,4,5'

while len(@S) > 0
begin
  --print left(@S, charindex(',', @S+',')-1)
  exec YourSP left(@S, charindex(',', @S+',')-1)
  set @S = stuff(@S, 1, charindex(',', @S+','), '')
end
```
>_source: https://stackoverflow.com/questions/10031658/comma-separated-list-in-sql_  
_answered: Mikael Eriksson, edited: Cœur_
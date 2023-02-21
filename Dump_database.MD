
## Dump Data from Server

To create a dump file , run the following command

```python
  sudo su - postgres
```

```python
  pg_dump --no-owner --format==custom Database_Name > db_0001.dump
```

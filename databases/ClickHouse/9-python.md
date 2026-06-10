# ClickHouse - Python

## Setup

```shell
python3 -m venv venv
source venv/bin/activate
pip install -U pip wheel
pip install clickhouse-connect
# pip install clickhouse-connect[async]
```

## Sample code

```python
import clickhouse_connect

client = clickhouse_connect.get_client(host='localhost', username='user', password='password')

## run a ClickHouse SQL command
client.command('CREATE TABLE new_table (key UInt32, value String, metric Float64) ENGINE MergeTree ORDER BY key')

## insert batch data
row1 = [1000, 'String Value 1000', 5.233]
row2 = [2000, 'String Value 2000', -107.04]
data = [row1, row2]
client.insert('new_table', data, column_names=['key', 'value', 'metric'])

## retrieve data using ClickHouse SQ
result = client.query('SELECT max(key), avg(metric) FROM new_table')
print(result.result_rows)
```

## AsyncClient wrapper

```python
import asyncio
import clickhouse_connect

async def main():
    client = await clickhouse_connect.get_async_client(host='localhost', username='user', password='password')
    result = await client.query("SELECT name FROM system.databases LIMIT 5")
    print(result.result_rows)
    await client.close()

asyncio.run(main())
```

## References

- <https://clickhouse.com/docs/integrations/python>
- <https://clickhouse.com/docs/integrations/language-clients/python/driver-api>
- <https://clickhouse.com/docs/integrations/language-clients/python/sqlalchemy>

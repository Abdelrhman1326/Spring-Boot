downloading Redis on docker:
``` powershell
docker run --name my-redis -p 6379:6379 -d redis
```
running Redis in cli:
``` powershell
docker exec -it my-redis redis-cli
```
### Setting, getting, and keys values:
``` bash
SET name AbdElrhman
GET name
DEL name
# check if a specefic key does exist:
EXISTS name
# get all keys:
KEYS *
# clean the db:
flushall
```

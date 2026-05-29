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

# expiration:
ttl name # time to live -> if returned value is -1, then it is infinite.
# setting expiration:
expire name 10 # now it will expire in 10 seconds

# setting expiration date while setting the key:
set age 10 20 # now it will expire in 10 seconds
```

### Lists:
22114422
---
icon: stethoscope
---

# View Server Logs

ssh into the server - using passcord (see discord) - vcm is the admin account\
dev server

```
ssh vcm@dev-mishmash.colab.duke.edu
```

test server

```
ssh vcm@test-mishmash.colab.duke.edu
```

production server

```
ssh vcm@mishmash.colab.duke.edu
```

cd into the git directory&#x20;

```
cd /home/vcm/studyabroad
```

run docker compose logs to check what containers are running

```
docker ps
```

run command to see all docker compose logs and follow the logs live

```
docker compose logs -f
```

run command to logs for one container - get name from docker ps

```
docker compose logs -f <container_name> 
```

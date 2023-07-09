# Mongo Replica Set
To create basic mongoDB replica set we need to alocate volume for containers:

```yaml
volumes:
  mongodb_master_data:
    driver: local
```

## Primary node
Then we can create first container that will be primary node in replica set:
```yaml
services:
  mongodb-primary:
    image: docker.io/bitnami/mongodb:5.0
    environment:
      - MONGODB_ADVERTISED_HOSTNAME=mongodb-primary
      - MONGODB_REPLICA_SET_MODE=primary
      - MONGODB_ROOT_PASSWORD=${ROOT_PASSWORD}
      - MONGODB_REPLICA_SET_KEY=${REPLICA_SET_KEY}
    volumes:
      - 'mongodb_master_data:/bitnami/mongodb'
    ports:
      - '27017:27017'
```
Here we use bitnami image for mongoDB. We set environment variables to configure replica set. We also expose port `27017` to host machine. From environment variables we get `ROOT_PASSWORD` and `REPLICA_SET_KEY` that are described in `.env` file:

```js	
ROOT_PASSWORD=secret
REPLICA_SET_KEY=secret
```

## Secondary node
To add secondary node we may use same image with different environment variables:

```yaml
services:
  # ... adding primary node
  mongodb-secondary-<some name>:
    image: docker.io/bitnami/mongodb:5.0
    depends_on:
      - mongodb-primary
    environment:
      - MONGODB_ADVERTISED_HOSTNAME=mongodb-secondary-1
      - MONGODB_REPLICA_SET_MODE=secondary
      - MONGODB_INITIAL_PRIMARY_HOST=mongodb-primary
      - MONGODB_INITIAL_PRIMARY_ROOT_PASSWORD=${ROOT_PASSWORD}
      - MONGODB_REPLICA_SET_KEY=${REPLICA_SET_KEY}
```
As we can see, id depends on primary node. Alike primary node, we set environment variables to configure replica set. 

## Arbiter node
Arbiter node is responsible for voting in replica set. It does not store any data. To create arbiter node we can use same image with different environment variables:
```yaml
services:
  # ... adding primary and secondary nodes
  mongodb-arbiter:
    image: docker.io/bitnami/mongodb:5.0
    depends_on:
      - mongodb-primary
    environment:
      - MONGODB_ADVERTISED_HOSTNAME=mongodb-arbiter
      - MONGODB_REPLICA_SET_MODE=arbiter
      - MONGODB_INITIAL_PRIMARY_HOST=mongodb-primary
      - MONGODB_INITIAL_PRIMARY_ROOT_PASSWORD=${ROOT_PASSWORD}
      - MONGODB_REPLICA_SET_KEY=${REPLICA_SET_KEY}
```

## Running replica set
Now we can run this replica set using `docker-compose up -d` command. To check if replica set is working we can use `docker-compose logs -f` command.
# Compose database #

Until now we have succesfully made an .NET application run on docker protected behind an Nginx load balancer.
We have also made the application persist its data in an PostgreSQL server.

We now need to bring things together and include the database in the docker-compose environment.

As the connection between the mvc app and the database should be internal, the host part of the connection string should point to the name of the PostgreSQL container (db) resolved by internal DNS.

Put this line in the src\appsettings.json:

```json
  "MyMoviesConnStr": "Host=db;Port=5432;Database=MyMoviesDb;Username=postgres;Password=moviedbsecretpassword"
```

Add this section in *docker-compose.yaml*:

```yaml
  db:
    image: postgres:12.1-alpine
    environment:
      POSTGRES_PASSWORD: "moviedbsecretpassword"
```

..and in the same file add db as a dependency to the mvc container:

```yaml
  mvc:
    depends_on:
      - reverseproxy
      - db
```

Create the environment settings file in the root of your working directory, call it *docker.env*:

```conf
MyMoviesConnStr=Host=db;Port=5432;Database=MyMoviesDb;Username=postgres;Password=moviedbsecretpassword
```

You can now build the docker images and launch them again:

```powershell
docker-compose build
docker-compose up -d
```

Inspect the running containers:

```powershell
docker ps

CONTAINER ID        IMAGE                   COMMAND                  CREATED             STATUS              PORTS
  NAMES
11e132a65a85        mymovies_mvc            "dotnet MyMoviesMvc.…"   5 minutes ago       Up 5 minutes        5000/tcp
  mymovies_mvc_1
49f5e8eec4b8        mymovies_reverseproxy   "nginx -g 'daemon of…"   5 minutes ago       Up 5 minutes        0.0.0.0:80->80/tcp, 0.0.0.0:443->443/tcp
  mymovies_reverseproxy_1
95821712dfd4        postgres:12.1-alpine    "docker-entrypoint.s…"   5 minutes ago       Up 5 minutes        5432/tcp
  mymovies_db_1
```

We have an ecosystem of three containers that provide our movies application in combination. Only Nginx has ports exposed, the inter-container communication is going on in an isolated network, which means the attack surface for the evil minded is very small.

Go and inspect our movies app again: <http://dev.mymovies.local>

Let us stop the the Docker-Compose stack:

```powershell
docker-compose down
```

Docker-Compose is a neet way to describe a multi-container setup that can easily be deployed to different environments, if you are so fortunate to sell the same solution to more costumers. It also makes it quite simple to move your service from one hosting provider to another.

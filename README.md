# Membuat simple bank app API
https://www.youtube.com/playlist?list=PLy_6D98if3ULEtXtNSY_2qN21VCKgoQAE

https://gist.github.com/rmzluish/f24fcc8c0b9ad8ba4e3f1f84a7c3bfa2

# GIT
```
git init
git add README.md
git commit -m "first commit"
git branch -M main
git remote add origin git@github.com:absormu/simplebank.git
git push -u origin main
```
## Bikin skema db postgresql
https://dbdiagram.io/
```

Bisa export :
    postgresql
    mysql
    png
    pdf

Table accounts as A {
    id bigserial [pk]
    owner varchar [not null]
    balance bigint [not null]
    currency varchar [not null]
    create_at timestamptz [not null, default: `now()`]

    Indexes {
        owner
    }
}

Table entries {
    id bigserial [pk]
    account_id bigint [ref: > A.id,not null]
    amount bigint [not null, note:'can be negative or positive']
    create_at timestamptz [not null, default: `now()`]

    Indexes {
        account_id
    }
}

Table transfer {
    id bigserial [pk]
    from_account_id bigint [ref: > A.id,not null]
    to_account_id bigint [ref: > A.id,not null]
    amount bigint [not null, note:'must be positive']
    create_at timestamptz [not null, default: `now()`]

    Indexes {
        from_account_id
        to_account_id
        (from_account_id,to_account_id)
    }
}
```
## Bikin docker container Docker + Postgres to create DB schema

```
* Instalasi
        https://en.opensuse.org/Docker
        1. zypper install docker python3-docker-compose
        2. sudo systemctl enable docker
        3. sudo usermod -G docker -a absor
        4. sudo systemctl restart docker
        5. sudo systemctl status docker
        6. docker version
        7. sudo systemctl start docker

* Create db postgresql 
    remove & create agar registri docker clean
    --rm remove container
    PGDATA menyimpan data pada direktori sehingga jika container mati data tetap aman
    
    container name : simplebank-db
    database name  : simplebankdb
    database user  : simplebank

    - remove & create
    docker run --rm \
            --name simplebank-db \
            -e POSTGRES_DB=simplebankdb \
            -e POSTGRES_USER=simplebank \
            -e POSTGRES_PASSWORD=z7BOE4aIKq2JP3VCLz7o \
            -e PGDATA=/var/lib/postgresql/data/pgdata \
            -v "$PWD/simplebank-data:/var/lib/postgresql/data" \
            -p 5432:5432 \
            postgres:13
            
   - buka tab terminal dan masuk ke host docker container
    docker exec -it simplebank-db psql -d simplebankdb -U simplebank
    \q -> untuk keluar
    docker logs simplebank-db -> untuk liat log container
    psql -h 127.0.0.1 -U simplebank simplebankdb
    pg_dump -h 127.0.0.1 -U simplebank simplebankdb
    docker exec -it simplebank-db pg_dump -h 127.0.0.1 -U simplebank simplebankdb
    - 

* Container
    docker ps
    
```
## database migration in Golang


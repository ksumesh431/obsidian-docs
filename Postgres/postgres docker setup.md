
### Create volume manually to use external volume flag in docker compose
`docker volume create postgres_setup_postgres_data`


`sudo docker compose up -d `

### Change password
`sudo docker exec -it my_postgres psql -U postgres -c "ALTER USER postgres WITH PASSWORD 'newpass';"`


### Importing steps

- create db if doesnt exists
` sudo docker exec -it my_postgres psql -U postgres -c "CREATE DATABASE smartdog;"`

- copy backup file in container
`  sudo docker cp 'TEST_SERVER_DB_BACKUP (1).sql' my_postgres:/tmp/backup.sql`

- restore backup
`sudo docker exec -it my_postgres pg_restore -U postgres -d smartdog /tmp/backup.sql`

- Verify restore by listing tables
`sudo docker exec -it my_postgres psql -U postgres -d smartdog -c "\dt"`

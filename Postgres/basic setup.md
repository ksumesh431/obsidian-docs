install postgres. do systemctl start and status to chk
`sudo systemctl status postgresql`


switch to postgres user 


write `psql`  to enter the psql client

\q to exit

`createdb db_name` to cerate new db

to switch to that db, `psql -d db_name`

to get info bout the db, in the psql clinet, enter \conninfo

`\password db_name` to change password
postgresql設定コマンド
docker run --name mrs -d -p 5432:5432 postgres
docker exec -it mrs /bin/bash

psql -U postgres

CREATE ROLE mrs LOGIN UNENCRYPTED PASSWORD 'mrs' NOSUPERUSER INHERIT NOCREATEDB NOCREATEROLE NOREPLICATION;

CREATE DATABASE mrs WITH OWNER = mrs ENCODING = 'UTF8' TABLESPACE = pg_default LC_COLLATE = 'C' LC_CTYPE = 'C' TEMPLATE = 'template0' CONNECTION LIMIT = -1;

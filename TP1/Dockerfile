FROM postgres:17.2-alpine

COPY initdb/CreateScheme.sql /docker-entrypoint-initdb.d/
COPY initdb/InsertData.sql /docker-entrypoint-initdb.d/

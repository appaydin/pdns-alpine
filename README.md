# PowerDNS-SQLite Alpine

Alpine based SQLite backend PowerDNS docker container

The powerdns settings are stored in /etc/pdns/pdns.conf. You can override settings by creating volume. The database is at /pdns/pdns_db.sqlite. By default, the Restfull api is enabled.

Change Folder Ownership:
```
chown -R 100:101 pdns/
```

### Example Application:
docker-compose:
```yaml
version: '3'

services:
    powerdns:
        build: .
        ports:
            - 53:53/udp
            - 53:53/tcp
            - 8081:8081
        environment:
            - PDNS_LOG_LEVEL=0
            - PDNS_API_KEY=34H5G34J5H43H34
            - PDNS_WEBSERVER_PASSWORD=123456
            - PDNS_WEBSERVER_ALLOWED_FROM=127.0.0.1,::1,172.0.0.0/8
        volumes: 
            - ./pdns/:/pdns/
    dnsadmin:
        image: ngoduykhanh/powerdns-admin:latest
        environment: 
            - SQLALCHEMY_DATABASE_URI=sqlite:////pdns/pdns_db.sqlite
            - GUINCORN_TIMEOUT=60
            - GUNICORN_WORKERS=2
            - GUNICORN_LOGLEVEL=DEBUG
        volumes: 
            - ./pdns/:/pdns/
        ports: 
            - 8082:80
        logging:
            driver: json-file
            options:
              max-size: 10m
```
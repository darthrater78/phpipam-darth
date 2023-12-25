# phpipam-darth

#Just a simple docker compose file for phpipam. Edited for a different subnet than docker standard.

# WARNING: Replace the example passwords with secure secrets.
# WARNING: 'my_secret_phpipam_pass' and 'my_secret_mysql_root_pass'

version: '3'

services:
  phpipam-web:
    image: phpipam/phpipam-www:latest
    ports:
      - "80:80"
    environment:
      - TZ=America/New_York
      - IPAM_DATABASE_HOST=phpipam-mariadb
      - IPAM_DATABASE_PASS=[YOURPASS]
      - IPAM_DATABASE_WEBHOST=%
    restart: unless-stopped
    volumes:
      - phpipam-logo:/phpipam/css/images/logo
      - phpipam-ca:/usr/local/share/ca-certificates:ro
    depends_on:
      - phpipam-mariadb
    networks:
      - phpipam-network

  phpipam-cron:
    image: phpipam/phpipam-cron:latest
    environment:
      - TZ=America/New_York
      - IPAM_DATABASE_HOST=phpipam-mariadb
      - IPAM_DATABASE_PASS=[YOURPASS]
      - SCAN_INTERVAL=1h
    restart: unless-stopped
    volumes:
      - phpipam-ca:/usr/local/share/ca-certificates:ro
    depends_on:
      - phpipam-mariadb
    networks:
      - phpipam-network

  phpipam-mariadb:
    image: mariadb:latest
    environment:
      - MYSQL_ROOT_PASSWORD=[YOURPASS]
    restart: unless-stopped
    volumes:
      - phpipam-db-data:/var/lib/mysql
    networks:
      - phpipam-network

volumes:
  phpipam-db-data:
  phpipam-logo:
  phpipam-ca:

networks:
  phpipam-network:
    ipam:
      config:
        - subnet: 172.25.0.0/16

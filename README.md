Joomla Docker Compose Setup
This docker-compose.yml file defines a Joomla and MySQL container setup with a custom network and persistent storage.
Services

This section defines the application services (joomla and mysql).
1. Joomla Service

  joomla:
    image: joomla

    Uses the official Joomla Docker image from Docker Hub.

    ports:
      - "8022:80"

    Maps port 8022 on the host to port 80 in the container.

    Joomla will be accessible at http://localhost:8022 or http://server-ip:8022.

    depends_on:
      - mysql

    Ensures that the mysql service starts before Joomla.

    Does not guarantee MySQL is ready before Joomla starts (use health checks if needed).

    environment:
      JOOMLA_DB_HOST: mysql
      JOOMLA_DB_USER: root
      JOOMLA_DB_PASSWORD: "password"
      JOOMLA_DB_NAME: joomla

    Defines database connection settings:

        JOOMLA_DB_HOST → Connects to mysql service.

        JOOMLA_DB_USER → Database username (root).

        JOOMLA_DB_PASSWORD → Password for the MySQL root user.

        JOOMLA_DB_NAME → Joomla will use this database.

    restart: always

    Ensures the container restarts automatically if it stops.

    volumes:
      - ./host:/var/www/html

    Mounts the ./host directory from the host machine to /var/www/html inside the container.

    This ensures that Joomla files persist even if the container is removed.

    You can edit files on the host without accessing the container.

    networks:
      - joomlanet

          Connects Joomla to the custom network joomlanet for communication with MySQL.

2. MySQL Service

  mysql:
    image: mysql

    Uses the official MySQL Docker image.

    environment:
      MYSQL_DATABASE: joomla
      MYSQL_ROOT_PASSWORD: "password"

    MYSQL_DATABASE → Creates a database named joomla.

    MYSQL_ROOT_PASSWORD → Sets the root password to "password".

    restart: always

    Ensures MySQL restarts automatically if it crashes.

    volumes:
      - ./mysqldata:/var/lib/mysql

    Mounts ./mysqldata from the host to /var/lib/mysql in the container.

    This ensures MySQL data persists even if the container is stopped or removed.

    networks:
      - joomlanet

    Connects MySQL to the custom network joomlanet for Joomla to access it.

Networks

networks:
  joomlanet:
    ipam:
      driver: default
      config:
        - subnet: "10.5.20.0/24"

    Defines a custom network called joomlanet.

    Uses IPAM (IP Address Management) to assign a specific subnet.

    The subnet 10.5.20.0/24 means:

        IP Range: 10.5.20.1 to 10.5.20.254

        Subnet Mask: 255.255.255.0

        Each container gets an IP in this range.


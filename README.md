# Configuracion del backend

### Ejecucion de spring boot
Ingresar a la carpeta del proyecto

```bash
mvn spring-boot:run -Dspring-boot.run.arguments="--server.port=8085 -Dspring.application.name=backdev04 -Djava.process.name=backdev04"
mvn spring-boot:run -Dspring-boot.run.arguments="--server.port=8086"
mvn spring-boot:run -Dspring-boot.run.arguments="--server.port=8087"
```

### Creacion del artefacto
Ingresar a la carpeta del proyecto

```bash
mvn clean
mvn package
```

### Ejecucion del artefacto
Ingresar a la carpeta target
```bash
java -jar spring-boot-jpa-postgresql-0.0.1-SNAPSHOT.jar -Dspring-boot.run.arguments="--server.port=8085 -Dspring.application.name=backdev04 -Djava.process.name=backdev04" &
java -jar spring-boot-jpa-postgresql-0.0.1-SNAPSHOT.jar -Dspring-boot.run.arguments="--server.port=8086 -Dspring.application.name=backtest04 -Djava.process.name=backtest04" &
java -jar spring-boot-jpa-postgresql-0.0.1-SNAPSHOT.jar -Dspring-boot.run.arguments="--server.port=8087 -Dspring.application.name=backstaging04 -Djava.process.name=backstaging04" &

java -jar -Dspring.application.name=backdev04 -Djava.process.name=backdev04 spring-boot-jpa-postgresql-0.0.1-SNAPSHOT.jar --server.port=8085 &
java -jar -Dspring.application.name=backtest04 -Djava.process.name=backtest04 spring-boot-jpa-postgresql-0.0.1-SNAPSHOT.jar --server.port=8086 &
java -jar -Dspring.application.name=backstaging04 -Djava.process.name=backstaging04 spring-boot-jpa-postgresql-0.0.1-SNAPSHOT.jar --server.port=8087 &
```

### Detener los procesos
Ejecutar en el powershell

```bash
wmic process where "commandline like '%backdev04%'" call terminate
wmic process where "commandline like '%backtest04%'" call terminate
wmic process where "commandline like '%backstaging04%'" call terminate
```

### Configuracion del server nginx
```bash
server {
        listen 80;
		server_name _;
        location /backdev04/ {
            rewrite ^/backdev04/(.*)$ /$1 break;
            proxy_pass http://localhost:8085;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }
        
        location /backtest04/ {
            rewrite ^/backtest04/(.*)$ /$1 break;
            proxy_pass http://localhost:8086;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }
        
        location /backstaging04/ {
            rewrite ^/backstaging04/(.*)$ /$1 break;
            proxy_pass http://localhost:8087;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Forwarded-Proto $scheme;
        }
    }
```

### Servicios expuestos
* http://127.0.0.1/backdev04/api/tutorials
* http://127.0.0.1/backtest04/api/tutorials
* http://127.0.0.1/backstaging04/api/tutorials
> Los servicios expuestos se pueden verificar desde un navegador web

## El front debe apuntar a esos dominios
* http://127.0.0.1/backdev04/
* http://127.0.0.1/backtest04/
* http://127.0.0.1/backstaging04/
> Modificar el **archivo angular-17-client/src/environments/environment.prod.ts** la `apiUrl`, en cada `branch`

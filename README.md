# Configuracion del backend

### Ejecucion de spring boot
Ingresar a la carpeta del proyecto

```bash
mvn spring-boot:run -Dspring-boot.run.arguments="--server.port=8085" &
mvn spring-boot:run -Dspring-boot.run.arguments="--server.port=8086" &
mvn spring-boot:run -Dspring-boot.run.arguments="--server.port=8087" &
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

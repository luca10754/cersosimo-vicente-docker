# TP Docker — MySQL + Java App Server
## Datos del alumno
- Nombre: Cersosimo Vicente
- Nombre de los alumnos: Alvite Rodrigo y Bagnato Luca
## 1. ¿Qué es Docker?

- Docker es una plataforma de contenedores que permite empaquetar una aplicación junto con todas sus dependencias en una unidad estandarizada llamada contenedor.
...
## 2. Volúmenes en Docker

- Un volumen es un mecanismo de persistencia gestionado por Docker. Permite que los datos sobrevivan al ciclo de vida del contenedor. Existen tres tipos principales:
  
  - Named Volumes: gestionados por Docker, recomendados para bases de datos.

  - Bind Mounts: mapeo directo de un directorio del host.

  - tmpfs Mounts: almacenamiento en memoria RAM (no persistente).

...
## 3. Redes en Docker

- Docker gestiona la comunicación entre contenedores mediante redes virtuales. Los contenedores en la misma red se pueden comunicar usando sus nombres como hostname gracias a la resolución DNS interna automática de Docker.

- Los tipos de redes doker son:

  - bridge
  - host
  - none
  - overlay
...
## 4. ¿Por qué Payara Server?

- Para un entorno Docker Java empresarial con GUI, Payara Server es la opción más recomendable por las siguientes razones:

  - Es una distribución de GlassFish mantenida activamente con soporte de producción.

  - Soporta Jakarta EE completo: JPA, EJB, JAX-RS, CDI, JMS, y más.

  - Incluye Payara Admin Console, una consola de administración web (GUI) accesible en el puerto 4848.

  - Tiene imágenes Docker oficiales optimizadas y actualizadas.

  - Escala bien de desarrollo a producción sin cambiar el stack tecnológico.

...
## 5. Explicación del docker-compose.yml

- permite definir y gestionar entornos multi-contenedor. En lugar de ejecutar múltiples comandos docker run con decenas de flags, definís toda la infraestructura en un archivo versionable que va junto al código fuente del proyecto.
...
## 6. Explicación del init.sql

-  Este script se ejecuta automáticamente la primera vez que el contenedor MySQL inicializa la base de datos:
...
## 7. Dificultades y soluciones

- Problema: MySQL no acepta conexiones

  - 1. Verificar que el contenedor está corriendo
    
    ```docker ps | grep mysql```
 
  - 2. Ver logs detallados de error

    ```docker logs mysql-container 2>&1 | tail -50```
 
  - 3. Verificar estado del healthcheck
     
    ```docker inspect mysql-container | grep -A 8 '"Health"'```

- Problema: Payara Admin Console no carga en el navegador

  - Ver logs de arranque de Payara
    
    ```docker logs payara-container | tail -80```
 

  - Verificar que el puerto 4848 está publicado

    ```docker ps | grep payara```
 

  - Entrar al contenedor para diagnosticar

    ```docker exec -it payara-container bash```

    ```asadmin list-domains```

- Problema: Payara no puede conectarse a MySQL (Ping falla)

  - Verificar que ambos contenedores están en la misma red

    ```docker network inspect java-net```
 

  - Probar conectividad de red desde Payara a MySQL

    ```docker exec payara-container ping mysql-container -c 3```
 

  - Verificar que el driver JDBC está instalado

    ```docker exec payara-container ls /opt/payara/appserver/glassfish/domains/domain1/lib/```
 

  - Si no está, copiarlo y reiniciar

    ```docker cp mysql-connector-j.jar```

    ```payara-container:/opt/payara/appserver/glassfish/domains/domain1/lib/```

    ```docker exec payara-container asadmin restart-domain domain1```




# Capturas de Pantalla Obligatorias

## **Parte 1 — Infraestructura Docker (5 capturas)**

## 1. Salida de docker --version y docker info en la terminal
![Versión de Docker](capturas/01-docker-version.png)
## 2. Salida de docker network ls mostrando la red java-net
![Red creada](capturas/02-red-creada.png)
## 3. Salida de docker volume inspect mysql-data
![Volumen MySQL](capturas/03-volumen-creado.png)
## 4. Salida de docker ps con ambos contenedores activos
![Contenedores activos](capturas/04-contenedores-corriendo.png)
## 5. docker network inspect java-net con ambos contenedores en la red
![Inspección de red](capturas/05-network-inspect.png)

## **Parte 2 — MySQL (3 capturas)**
## 6. Logs de MySQL mostrando: ready for connections
![Logs de MySQL](capturas/06-mysql-logs.png)
## 7. Salida de SHOW DATABASES; mostrando la base appdb
![Bases de datos](capturas/07-mysql-databases.png)
## 8. Salida de SELECT * FROM usuarios; con los datos del init.sql
![Datos de usuarios](08-mysql-tabla.png)

## **Parte 3 — Payara Admin Console / GUI (5 capturas)**
## 9. Pantalla de login de Admin Console en http://localhost:4848
![Login de Admin Console](capturas/09-payara-login.png)
## 10. Dashboard principal de Payara tras iniciar sesión
![Dashboard de Payara](capturas/10-payara-dashboard.png)
## 11. Pantalla del Connection Pool MySQLPool creado
![Connection Pool MySQLPool](capturas/11-connection-pool.png)
## 12. Resultado del botón Ping mostrando conexión exitosa a MySQL
![Ping a MySQL](capturas/12-ping-exitoso.png)
## 13. JDBC Resource jdbc/MySQLDS visible en la consola
![JDBC Resource](capturas/13-jdbc-resource.png)

## Parte 4 — Conectividad entre contenedores (1 captura)
## 14. Salida del ping de Payara hacia mysql-container desde la terminal
![Ping de Payara a MySQL](capturas/14-ping-contenedores.png)



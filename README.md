# API Gateway

Enrutador principal y punto único de entrada (Single Point of Contact) hacia los microservicios del sistema mediante Spring Cloud Gateway.

## 1. Variables de Entorno

| Variable | Valor Local | Valor Docker | Descripción |
| :--- | :--- | :--- | :--- |
| `SERVER_PORT` | `8080` | `8080` | Puerto público unificado |
| `AUTH_SERVICE_URL` | `http://localhost:8081` | `http://login-service:8081` | Destino del servicio de login |
| `PRODUCTS_SERVICE_URL` | `http://localhost:8082` | `http://productos-service:8082` | Destino del servicio de productos |
| `WISHLIST_SERVICE_URL` | `http://localhost:8083` | `http://wishlist-service:8083` | Destino del servicio de wishlist |

## 2. Configuración de Enrutamiento (`application.yml`)

```yaml
server:
  port: 8080

spring:
  application:
    name: api-gateway
  cloud:
    gateway:
      # Configuración de CORS si conectas un frontend (Angular, React, etc.)
      globalcors:
        cors-configurations:
          '[/**]':
            allowedOrigins: "*"
            allowedMethods:
              - GET
              - POST
              - PUT
              - DELETE
              - OPTIONS
            allowedHeaders: "*"

      # Definición de rutas hacia los microservicios
      routes:
        # 1. Microservicio de Autenticación / Usuarios
        - id: auth-service
          uri: http://localhost:8081   # Ajusta al puerto de tu servicio de auth
          predicates:
            - Path=/api/v1/auth/**, /api/v1/roles/**

        # 2. Microservicio de Productos
        - id: products-service
          uri: http://localhost:8082   # Ajusta al puerto de tu servicio de productos
          predicates:
            - Path=/products/**

        # 3. Microservicio de Wishlist y su Historial
        - id: wishlist-service
          uri: http://localhost:8083   # Puerto de wishlist que vimos en tus pruebas
          predicates:
            - Path=/api/v1/wishlist/**

## 3. Manual de Despliegue
Despliegue Local (Maven)

Bash
mvn clean install -DskipTests
mvn spring-boot:run

Despliegue con Docker

Bash
docker build -t api-gateway .
docker run -d -p 8080:8080 --name api-gateway --network carvajal-network api-gateway

## 4. Comentarios Adicionales para el Despliegue

Al usar Spring Cloud Gateway (arquitectura reactiva sobre Netty), no se debe incluir la dependencia spring-boot-starter-web (Tomcat) en el pom.xml.

Todas las llamadas desde Postman o el Frontend deben hacerse al puerto 8080.
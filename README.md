# Proyecto de Microservicios con Spring Boot y Spring Cloud

Este es un proyecto de ejemplo para estudiar y poner en práctica una arquitectura de **microservicios** utilizando el ecosistema de **Spring Boot**, **Spring Cloud Netflix (Eureka)** y **Spring Cloud Gateway**. El proyecto está compuesto por varios módulos (microservicios) que se relacionan entre sí a través de un **Discovery Server** y un **API Gateway**.  

                      +-------------------+
                      |   Discovery       |  (Eureka Server)
                      |      Server       |
                      +---------+---------+
                                ^
                                | (Registro & Descubrimiento)
         +----------------------+-----------------------+
         |                                              |
  +------v-------+                                 +-----v-----+
  | Inventory    | (Eureka Client)                 | Products  | (Eureka Client)
  |  Service     |-------------------------------->| Service  |
  +--------------+                                 +-----------+

  +----------------------------------------------------+
  | Orders Service (Eureka Client)                     |
  | (usa MySQL, llama a otros servicios según sea      |
  |  necesario a través de Eureka)                     |
  +----------------------------------------------------+

                             +----------------------+
                             |     API Gateway      |
                             | (Spring Cloud Gateway|
                             +----------+-----------+
                                        |
                                   (Enrutamiento)
                                        |
                                   (Cliente final)


## Estructura de Módulos

El proyecto está organizado como un **multi-módulo** de Maven. A continuación, se listan los módulos principales y una breve descripción de cada uno:

1. **microservices (parent)**  
   - Contiene la configuración común (dependencias, versiones de Spring Boot y Spring Cloud) y la gestión de módulos.
   - Versión de Java: 17  
   - Versión de Spring Boot: 3.0.6  
   - Versión de Spring Cloud: 2022.0.2  

2. **discovery-server**  
   - Implementa **Eureka Server** para el registro y la localización de microservicios (Service Discovery).
   - Permite que cada microservicio se registre automáticamente y se descubra dinámicamente sin necesidad de configurar manualmente las direcciones.

3. **api-gateway**  
   - Implementa **Spring Cloud Gateway**, que expone un punto de entrada único (Edge Service) para enrutamiento a los diferentes microservicios.
   - Se encarga de la gestión centralizada de rutas y peticiones.

4. **inventory-service**  
   - Microservicio encargado de la gestión de inventarios.
   - Utiliza **Spring Boot Starter Data JPA** y se conecta a **PostgreSQL** (en contenedor Docker).
   - Se registra como cliente de Eureka (Eureka Client).

5. **orders-service**  
   - Microservicio que gestiona órdenes o pedidos.
   - Combina **Spring Boot Starter Data JPA** y **Spring Boot Starter WebFlux**.
   - Se conecta a **MySQL** (en contenedor Docker) y se registra como cliente de Eureka (Eureka Client).

6. **products-service**  
   - Microservicio para la gestión de productos.
   - Utiliza **Spring Boot Starter Data JPA** y se conecta a **PostgreSQL** (en contenedor Docker).
   - Se registra también como cliente de Eureka (Eureka Client).

Cada uno de estos servicios cuenta con sus propias dependencias y configuración, pero todos comparten la definición de la versión de Spring Cloud y otras dependencias comunes a través del `pom.xml` del módulo **microservices** (el parent).

## Tecnologías y Herramientas

- **Java 17**  
  Es la versión de Java establecida en los `pom.xml`.

- **Spring Boot 3.0.6**  
  Framework principal para la creación de microservicios en Java.

- **Spring Cloud Netflix Eureka**  
  Facilita la implementación de Eureka Server (service discovery) y Eureka Client (registro de microservicios).

- **Spring Cloud Gateway**  
  Implementa el patrón de API Gateway para enrutar las peticiones a los microservicios registrados.

- **Bases de Datos en Docker**  
  - **PostgreSQL** para los servicios de *Inventory* y *Products*.  
  - **MySQL** para el servicio de *Orders*.  
  - Se manejan a través de un archivo `docker-compose.yml` (ver detalles más abajo).

- **Maven**  
  Herramienta de construcción y gestión de dependencias.

- **Lombok**  
  Librería para simplificar el código Java mediante anotaciones (getter/setter, constructor, builder, etc.).

- **Spring Boot DevTools**  
  Ofrece un flujo de desarrollo más rápido con auto-reinicio y recarga de clases. (Está configurado como `optional` en los servicios)

## Configuración de las Bases de Datos en Docker

En el archivo `docker-compose.yml` (versión 3), se definen tres contenedores:

### Detalles de las Bases de Datos Dockerizadas

Para facilitar el desarrollo y las pruebas, cada microservicio se conecta a una base de datos que se levanta a través de **contenedores Docker**. En el archivo `docker-compose.yml` se definen tres servicios de bases de datos:

- **PostgreSQL** para el microservicio de Inventario (Inventory).
- **MySQL** para el microservicio de Órdenes (Orders).
- **PostgreSQL** para el microservicio de Productos (Products).

## Guía de Implementación de Exposición de Métricas en Spring Boot para Prometheus y Grafana

### 1. Añadir Dependencias en `pom.xml`

Para habilitar la exposición de métricas, necesitamos añadir dos dependencias en el archivo `pom.xml`:

- **`spring-boot-starter-actuator`**: Proporciona endpoints de monitoreo y métricas de la aplicación.
- **`micrometer-registry-prometheus`**: Permite que las métricas de Micrometer sean recogidas por Prometheus.

**Instrucciones**:
1. Abre el archivo `pom.xml`.
2. Añade las siguientes dependencias dentro de la sección `<dependencies>`:

   ```xml
   <dependency>
       <groupId>org.springframework.boot</groupId>
       <artifactId>spring-boot-starter-actuator</artifactId>
   </dependency>
   <dependency>
       <groupId>io.micrometer</groupId>
       <artifactId>micrometer-registry-prometheus</artifactId>
   </dependency>
   ```

### 2. Configurar la Exposición de Métricas en `application.properties`

Spring Boot permite configurar qué endpoints estarán expuestos y accesibles a través de Actuator. Para exponer las métricas en el endpoint `/actuator/prometheus`, ajustaremos el archivo `application.properties`.

**Instrucciones**:
1. Abre el archivo `src/main/resources/application.properties`.
2. Añade las siguientes configuraciones:

   ```properties
   # Configuración de Actuator y Prometheus
   management.endpoints.web.exposure.include=*
   management.endpoint.prometheus.enabled=true
   management.metrics.export.prometheus.enabled=true
   ```

   - **`management.endpoints.web.exposure.include=*`**: Expone todos los endpoints de Actuator.
   - **`management.endpoint.prometheus.enabled=true`**: Habilita el endpoint específico para Prometheus.
   - **`management.metrics.export.prometheus.enabled=true`**: Activa la exportación de métricas para Prometheus.

### 3. Configurar Seguridad en el Endpoint de Prometheus

Si la aplicación utiliza Spring Security, debemos permitir el acceso sin autenticación al endpoint `/actuator/prometheus` para que Prometheus pueda acceder a las métricas sin restricciones.

Para lograr esto, crearemos una clase de configuración de seguridad en el proyecto.

**Instrucciones**:
1. Crea un subpaquete llamado `config` en `src/main/java/com/kibernumacademy/devops` (o el paquete base de tu aplicación) para organizar las configuraciones.
2. En el paquete `config`, crea una nueva clase llamada `SecurityConfig.java`.
3. Agrega el siguiente código en `SecurityConfig.java`:

   ```java
   package com.kibernumacademy.devops.config;

   import org.springframework.context.annotation.Configuration;
   import org.springframework.security.config.annotation.web.builders.HttpSecurity;
   import org.springframework.security.config.annotation.web.configuration.EnableWebSecurity;
   import org.springframework.security.config.annotation.web.configuration.WebSecurityConfigurerAdapter;

   @Configuration
   @EnableWebSecurity
   public class SecurityConfig extends WebSecurityConfigurerAdapter {
       @Override
       protected void configure(HttpSecurity http) throws Exception {
           http
               .authorizeRequests()
               .antMatchers("/actuator/prometheus").permitAll()  // Permitir acceso a Prometheus sin autenticación
               .anyRequest().authenticated()
               .and()
               .httpBasic();  // Mantiene autenticación básica para otros endpoints
       }
   }
   ```

   Este código hace lo siguiente:
   - **Permitir acceso sin autenticación** al endpoint `/actuator/prometheus`.
   - **Requerir autenticación para otros endpoints**, manteniendo la seguridad básica en la aplicación.

### 4. Verificar la Configuración y Reiniciar la Aplicación

Después de completar los pasos anteriores, verifica que:

- Las dependencias en el `pom.xml` están añadidas y guardadas.
- Las configuraciones en `application.properties` están correctas.
- La clase `SecurityConfig.java` está en el lugar adecuado y configurada.

**Instrucciones**:
1. Compila y ejecuta la aplicación para asegurarte de que los cambios se han aplicado correctamente.
2. Accede al endpoint de métricas en `http://localhost:9090/actuator/prometheus` (ajusta el puerto si es necesario).

### 5. Prueba de Acceso al Endpoint de Prometheus

1. Abre un navegador o utiliza `curl` para acceder al endpoint:
   ```bash
   curl http://localhost:9090/actuator/prometheus
   ```
   
   - Deberías ver un listado de métricas en formato de texto que Prometheus puede interpretar.
   - Si la seguridad está configurada correctamente, no se te pedirá autenticación para este endpoint.

### 6. Configuración en Prometheus (Opcional para la Integración Completa)

Para que Prometheus recoja estas métricas automáticamente, agrega la configuración de `scrape` en el archivo `prometheus.yml` de tu servidor de Prometheus.

**Ejemplo de configuración en `prometheus.yml`**:
```yaml
scrape_configs:
  - job_name: 'spring-boot-app'
    metrics_path: '/actuator/prometheus'
    static_configs:
      - targets: ['192.168.19:8070']
```

Con esto, Prometheus recogerá las métricas desde el endpoint `/actuator/prometheus` de tu aplicación en `localhost:8070`. 
No olvides reiniciar el servidor de Prometheus después de modificar el archivo `prometheus.yml`.


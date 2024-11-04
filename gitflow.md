### Guía de Implementación de Exposición de Métricas en Spring Boot usando Git Flow

En este escenario, tenemos un proyecto existente que necesita integrar la exposición de métricas para Prometheus y Grafana siguiendo la guía proporcionada. Aplicaremos la metodología **Git Flow** para organizar el trabajo entre **Usuario A (Owner)** y **Usuario B (Developer)**, utilizando issues para gestionar las tareas.

#### **Contexto Inicial**

- **Proyecto Existente**: Una aplicación Spring Boot sin la implementación de exposición de métricas.
- **Objetivo**: Integrar la funcionalidad de exposición de métricas siguiendo la guía.
- **Roles**:
  - **Usuario A (Owner)**: Responsable del repositorio y la gestión general.
  - **Usuario B (Developer)**: Desarrollador asignado para implementar las nuevas funcionalidades.

---

### **1. Preparación del Repositorio y Configuración de Ramas**

#### **1.1. Configuración Inicial en GitLab (Usuario A - Owner)**

1. **Crear el Proyecto en GitLab**:
   - Usuario A crea un nuevo proyecto en GitLab llamado `spring-boot-metrics`.
   - Establece `develop` como la rama por defecto en **Settings > Repository** para seguir la metodología Git Flow.

2. **Clonar el Repositorio (Usuario A y Usuario B)**:
   - Ambos usuarios clonan el repositorio y estarán en la rama `develop` por defecto.
   ```bash
   git clone <URL_del_proyecto_GitLab>
   cd spring-boot-metrics
   ```

#### **1.2. Verificar el Estado Actual del Código**

- El código existente está en la rama `main` o `develop`, pero **no incluye** la implementación de exposición de métricas.

---

### **2. Creación de Issues en GitLab (Usuario A - Owner)**

Usuario A crea los siguientes issues para dividir el trabajo según la guía:

1. **Issue 1: Añadir Dependencias en `pom.xml`**
   - *Descripción*: Agregar `spring-boot-starter-actuator` y `micrometer-registry-prometheus` al `pom.xml`.
   - *Asignado a*: Usuario B

2. **Issue 2: Configurar Exposición de Métricas en `application.properties`**
   - *Descripción*: Configurar los endpoints de Actuator y Prometheus en `application.properties`.
   - *Asignado a*: Usuario B

3. **Issue 3: Configurar Seguridad en el Endpoint de Prometheus**
   - *Descripción*: Crear `SecurityConfig.java` para permitir acceso sin autenticación al endpoint `/actuator/prometheus`.
   - *Asignado a*: Usuario B


---

### **3. Desarrollo de Funcionalidades con Git Flow (Usuario B - Developer)**

#### **3.1. Trabajar en Cada Issue Usando Ramas `feature`**

Por cada issue asignado, Usuario B creará una rama `feature` desde `develop` y, al finalizar, realizará un Merge Request hacia `develop`.

##### **Issue 1: Añadir Dependencias en `pom.xml`**

1. **Crear la Rama `feature`**:
   ```bash
   git checkout develop
   git pull origin develop
   git checkout -b feature/añadir-dependencias
   ```

2. **Implementar los Cambios**:
   - Abrir `pom.xml` y añadir las dependencias según la guía.

3. **Commit y Push**:
   ```bash
   git add pom.xml
   git commit -m "Añadir dependencias de Actuator y Prometheus"
   git push origin feature/añadir-dependencias
   ```

4. **Crear Merge Request (MR)**:
   - En GitLab, abrir una MR desde `feature/añadir-dependencias` hacia `develop`.
   - Asignar la MR a Usuario A para revisión.

    Ejemplo de descripción en la MR:
     ```
     Completado el requerimiento del #1 porfavor revisar para fusionar con develop closes #1
     ```

     Al mencionar `Closes #1`, GitLab automáticamente enlaza el issue con la MR y cerrará el issue cuando la MR sea aprobada y fusionada en `develop`.

   - **Diferencia entre `Closes` y `Fixes`**: 
     - **`Closes #1`**: Se usa para indicar que el issue está completo y puede cerrarse al finalizar la MR.
     - **`Fixes #1`**: Generalmente se usa para indicar que la MR corrige un problema o bug específico en el issue, no solo completando el trabajo, sino también solucionando algo roto.
     - Ambos comandos tienen el mismo efecto de cerrar el issue al fusionarse la MR, pero `Closes` suele ser más apropiado para el trabajo de desarrollo de características.


##### **Issue 2: Configurar Exposición de Métricas en `application.properties`**

1. **Crear la Rama `feature`**:
   ```bash
   git checkout develop
   git pull origin develop
   git checkout -b feature/configurar-metrics
   ```

2. **Implementar los Cambios**:
   - Modificar `src/main/resources/application.properties` según la guía.

3. **Commit y Push**:
   ```bash
   git add src/main/resources/application.properties
   git commit -m "Configurar exposición de métricas en application.properties"
   git push origin feature/configurar-metrics
   ```

4. **Crear Merge Request (MR)**:
   - Abrir una MR desde `feature/configurar-metrics` hacia `develop`.
   - Asignar la MR a Usuario A para revisión.

##### **Issue 3: Configurar Seguridad en el Endpoint de Prometheus**

1. **Crear la Rama `feature`**:
   ```bash
   git checkout develop
   git pull origin develop
   git checkout -b feature/configurar-seguridad
   ```

2. **Implementar los Cambios**:
   - Crear el paquete `com.kibernumacademy.devops.config`.
   - Añadir `SecurityConfig.java` con el código proporcionado.

3. **Commit y Push**:
   ```bash
   git add src/main/java/com/kibernumacademy/devops/config/SecurityConfig.java
   git commit -m "Configurar seguridad para el endpoint de Prometheus"
   git push origin feature/configurar-seguridad
   ```

4. **Crear Merge Request (MR)**:
   - Abrir una MR desde `feature/configurar-seguridad` hacia `develop`.
   - Asignar la MR a Usuario A para revisión.
---

### **4. Revisión y Aprobación de Merge Requests (Usuario A - Owner)**

Por cada MR creada por Usuario B:

1. **Revisar el Código**:
   - Verificar que los cambios cumplen con los requisitos del issue.
   - Comprobar la calidad del código y seguir las prácticas recomendadas.

2. **Probar los Cambios**:
   - Usuario A puede sincronizar la rama `feature` localmente para pruebas.
     ```bash
     git fetch origin feature/nombre-de-la-rama
     git checkout feature/nombre-de-la-rama
     ```

3. **Aprobar y Fusionar la MR**:
   - Si todo está correcto, aprobar la MR en GitLab.
   - Fusionar la MR en la rama `develop`.

4. **Eliminar la Rama `feature` Remota** (opcional):
   - Después de fusionar, eliminar la rama `feature` en GitLab para mantener limpio el repositorio.

---

### **5. Preparación para el Lanzamiento**

#### **5.1. Crear una Rama `release` (Usuario A - Owner)**

1. **Crear la Rama `release` desde `develop`**:
   ```bash
   git checkout develop
   git pull origin develop
   git checkout -b release/v1.0
   ```

2. **Realizar Pruebas Finales**:
   - Usuario A realiza pruebas integrales en la rama `release`.
   - Si se encuentran errores menores, se corrigen directamente en `release`.

3. **Commit y Push**:
   ```bash
   git add .
   git commit -m "Preparar versión v1.0 para lanzamiento"
   git push origin release/v1.0
   ```

#### **5.2. Fusionar `release` en `main` y Etiquetar la Versión**

1. **Fusionar en `main` y Etiquetar**:
   ```bash
   git checkout main
   git pull origin main
   git merge release/v1.0
   git tag -a v1.0 -m "Lanzamiento de la versión 1.0 con métricas"
   git push origin main --tags
   ```

2. **Fusionar Cambios de `release` en `develop`**:
   - Para mantener sincronizadas las ramas.
   ```bash
   git checkout develop
   git merge release/v1.0
   git push origin develop
   ```

#### **5.3. Eliminar la Rama `release` (opcional)**

- Una vez fusionada, se puede eliminar la rama `release` tanto local como remotamente.
  ```bash
  git branch -d release/v1.0
  git push origin --delete release/v1.0
  ```

---

### **6. Manejo de Hotfixes (si es necesario)**

Si se detecta un error crítico en producción después del lanzamiento:

#### **6.1. Crear una Rama `hotfix` (Usuario A - Owner)**

1. **Crear la Rama desde `main`**:
   ```bash
   git checkout main
   git pull origin main
   git checkout -b hotfix/correccion-critica
   ```

2. **Implementar la Corrección**:
   - Realizar los cambios necesarios para corregir el error.

3. **Commit y Push**:
   ```bash
   git add .
   git commit -m "Corregir error crítico en producción"
   git push origin hotfix/correccion-critica
   ```

#### **6.2. Fusionar el Hotfix en `main` y `develop`**

1. **Fusionar en `main` y Etiquetar**:
   ```bash
   git checkout main
   git merge hotfix/correccion-critica
   git tag -a v1.0.1 -m "Lanzamiento de hotfix v1.0.1"
   git push origin main --tags
   ```

2. **Fusionar en `develop`**:
   ```bash
   git checkout develop
   git merge hotfix/correccion-critica
   git push origin develop
   ```

3. **Eliminar la Rama `hotfix`**:
   ```bash
   git branch -d hotfix/correccion-critica
   git push origin --delete hotfix/correccion-critica
   ```

---

### **7. Resumen del Proceso**

- **Usuario A**:
  - Crea el repositorio y establece `develop` como rama por defecto.
  - Crea issues y asigna tareas a Usuario B.
  - Revisa y aprueba los Merge Requests.
  - Maneja las ramas `release` y `hotfix`.

- **Usuario B**:
  - Trabaja en ramas `feature` para cada issue asignado.
  - Realiza commits atómicos y descriptivos.
  - Crea Merge Requests para revisión.

- **Flujo Git Flow Aplicado**:
  - Uso de ramas `feature` para desarrollar nuevas funcionalidades.
  - Uso de ramas `release` para preparar lanzamientos.
  - Uso de ramas `hotfix` para corregir errores críticos en producción.
  - Fusiones estratégicas en `develop` y `main` para mantener la integridad del código.

---

### **Consideraciones Finales**

- **Comunicación**: Mantener una comunicación fluida entre Usuario A y Usuario B para resolver dudas y asegurar que los estándares de codificación y prácticas recomendadas se sigan correctamente.

- **Buenas Prácticas**:
  - Escribir mensajes de commit claros y significativos.
  - Revisar el código (code review) para mejorar la calidad y detectar posibles errores.
  - Actualizar constantemente la rama `develop` local antes de crear nuevas ramas `feature` para evitar conflictos.

- **Integración Continua** (Opcional):
  - Configurar pipelines de CI/CD en GitLab para automatizar pruebas y despliegues.

---

### **Aplicación de la Metodología Git Flow a la Guía Original**

Al aplicar Git Flow a la implementación de la exposición de métricas en Spring Boot:

- **Se asegura una integración ordenada y controlada** de las nuevas funcionalidades.
- **Facilita el seguimiento** de qué cambios se realizaron para cada funcionalidad específica.
- **Permite manejar errores** de manera eficiente a través de ramas `hotfix`.
- **Mejora la colaboración** entre los desarrolladores al tener roles y responsabilidades claras.

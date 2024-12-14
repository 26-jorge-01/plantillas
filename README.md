# Plantillas - API (Spark - MySql automatizada con Airflow)

## **Índice**

1. [Descripción General](#descripción-general)
2. [Estructura del Proyecto](#estructura-del-proyecto)
3. [Prerequisitos](#prerequisitos)
4. [Instalación](#instalación)
5. [Ejecución del Proyecto](#ejecución-del-proyecto)
6. [Documentación de Componentes](#documentación-de-componentes)
7. [Pruebas](#pruebas)
8. [Estado Actual del Proyecto](#estado-actual-del-proyecto)
9. [Pasos Futuros](#pasos-futuros)
10. [Enlaces de Interés](#enlaces-de-interés)
11. [Contribuciones](#contribuciones)
12. [Licencia](#licencia)

---

## **Descripción General**

Este proyecto combina Apache Airflow y FastAPI para orquestar flujos de trabajo y exponer datos a través de una API REST. Fue diseñado para ser modular, escalable y fácilmente adaptable a entornos locales y de producción.

**Objetivos Principales:**
- Automatizar flujos de trabajo con Apache Airflow.
- Exponer información procesada mediante una API REST con FastAPI.
- Garantizar una transición escalonada a producción.

**Componentes Principales:**
- **MySQL:** Almacén de datos.
- **Redis:** Backend para tareas de Airflow y rate-limiting.
- **FastAPI:** API REST.
- **Airflow:** Orquestación de flujos de trabajo.

---

## **Estructura del Proyecto**

```
.
├── airflow_config/
│   ├── dags/
|   |   ├── libs/
│   │   └── api_to_db_pipeline.py
│   ├── airflow.cfg
│   ├── Dockerfile
│   ├── requirements.txt
│   └── wait-for-it.sh
├── api/
│   ├── app/
│   │   └── main.py
│   ├── Dockerfile
│   └── requirements.txt
├── config/
│   └── .env
├── database/
│   └── schema.sql
├── docker-compose.yml
└── README.md
```

### **Descripción de Archivos Clave**

- **`airflow_config/`:** Configuración de Airflow.
  - **`dags/`:** Flujos de trabajo (DAGs) definidos para Airflow.
     - **`libs/`:** Contiene los módulos dedicados especificamente para el uso de los DAGs
  - **`airflow.cfg`:** Configuraciones adicionales de Airflow.
  - **`Dockerfile`:** Instrucciones para construir la imagen de Docker de Airflow.
  - **`requirements.txt`:** Dependencias de la API.
  - **`wait-for-it.sh`:** Script para sincronizar el inicio de servicios.
- **`api/`:** Contiene el código fuente de la API desarrollada con FastAPI.
  - **`Dockerfile`:** Instrucciones para construir la imagen de Docker de la API.
  - **`requirements.txt`:** Dependencias de la API.
- **`config/.env`:** Archivo de variables de entorno.
- **`database/`:** Scripts de inicialización para la base de datos MySQL.
- **`docker-compose.yml`:** Orquestación de los contenedores Docker.

---

## **Prerequisitos**

1. **Instalaciones Necesarias:**
   - [Docker](https://www.docker.com/get-started)
   - [Docker Compose](https://docs.docker.com/compose/install/)

2. **Configuración del Entorno Local:**
   - Asegúrate de tener un archivo `config/.env` con las siguientes variables:
     ```env
     MYSQL_ROOT_PASSWORD=mysql_root_password
     MYSQL_DATABASE=mysql_database
     MYSQL_USER=mysql_user
     MYSQL_PASSWORD=mysql_password
     ```

---

## **Instalación**

1. **Clona el Repositorio:**
   ```bash
   git clone nombre_repositorio
   cd nombre_repositorio
   ```

2. **Configura las Variables de Entorno:**
   - Crea el archivo `config/.env` si no existe y define las credenciales.

3. **Construye y Levanta los Contenedores:**
   ```bash
   docker-compose --env-file ./config/.env build
   docker-compose --env-file ./config/.env up -d
   ```

---

## **Ejecución del Proyecto**

### **Servicios Disponibles**

- **API:** Disponible en `http://localhost:8000`
- **Airflow Webserver:** Disponible en `http://localhost:8080`
- **MySQL:** Disponible localmente en el puerto `3307`.

### **Crear Usuario Administrador en Airflow**

1. Ingresa al contenedor de Airflow:
   ```bash
   docker exec -it airflow bash
   ```
2. Crea un usuario administrador:
   ```bash
   airflow users create \
       --username admin \
       --firstname Admin \
       --lastname User \
       --role Admin \
       --email admin@example.com \
       --password admin
   ```

---

## **Documentación de Componentes**

### **1. API**
- Framework: **FastAPI**.
- Comando principal:
  ```bash
  uvicorn app.main:app --host 0.0.0.0 --port 8000
  ```
- Archivo de dependencias: `api/requirements.txt`.

### **2. Airflow**
- DAGs disponibles en `airflow_config/dags/`.
- Configuraciones adicionales en `airflow_config/airflow.cfg`.

### **3. MySQL**
- Base de datos inicializada con `database/schema.sql`.
- Acceso:
  ```bash
  mysql -uuser -ppassword -h127.0.0.1 -P3307 public_contracts
  ```

### **4. Redis**
- Utilizado para rate-limiting y como backend de Celery en Airflow.

---

## **Pruebas**

1. **Pruebas Unitarias para la API:**
   - Usa **pytest**:
     ```bash
     pytest tests/
     ```

2. **Pruebas para los DAGs de Airflow:**
   - Usa `pytest-airflow`:
     ```python
     from airflow.models import DagBag

     def test_dag_import():
         dag_bag = DagBag()
         assert len(dag_bag.import_errors) == 0, "DAG import errors found!"
     ```

---

## **Estado Actual del Proyecto**

### **Funcionalidades Actuales:**
- Configuración inicial de FastAPI y Airflow.
- Integración con MySQL y Redis.
- Docker Compose funcional.

### **Limitaciones:**
- Uso local de Redis y MySQL (sin redundancia).
- Sin configuración HTTPS (solo HTTP).

---

## **Pasos Futuros**

1. **Seguridad:**
   - Implementar HTTPS con NGINX o Traefik.
   - Migrar credenciales a un gestor de secretos (AWS Secrets Manager o HashiCorp Vault).

2. **Escalabilidad:**
   - Configurar balanceo de carga para la API.
   - Usar un servicio gestionado para la base de datos (AWS RDS o Google Cloud SQL).

3. **Automatización:**
   - Implementar un pipeline CI/CD con GitHub Actions o GitLab CI.

4. **Monitoreo:**
   - Configurar Prometheus y Grafana para monitoreo de contenedores y servicios.

---

## **Enlaces de Interés**

- [Documentación de Docker](https://docs.docker.com/)
- [FastAPI](https://fastapi.tiangolo.com/)
- [Apache Airflow](https://airflow.apache.org/)
- [Redis](https://redis.io/)
- [MySQL](https://dev.mysql.com/doc/)

---

## **Contribuciones**

1. Haz un fork del repositorio.
2. Crea una rama para tu característica:
   ```bash
   git checkout -b feature/nueva-caracteristica
   ```
3. Realiza cambios y haz commit:
   ```bash
   git commit -am "Agrega nueva característica"
   ```
4. Abre un Pull Request.

---

## **Licencia**

Este proyecto está bajo la Licencia MIT. Consulta el archivo [LICENSE](LICENSE) para más detalles.
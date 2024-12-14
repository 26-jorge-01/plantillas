# Usa la imagen base oficial de Apache Airflow
FROM apache/airflow:2.7.1

# Establece el directorio de trabajo
WORKDIR /opt/airflow

# Copia DAGs al contenedor
COPY ./dags /opt/airflow/dags

# Copia la configuración de Airflow si tienes un archivo airflow.cfg
COPY ./airflow.cfg /opt/airflow/airflow.cfg

# Instala dependencias adicionales si es necesario
COPY ./requirements.txt /opt/airflow/requirements.txt
RUN pip install --no-cache-dir -r /opt/airflow/requirements.txt

# Da permisos de ejecución al script wait-for-it.sh (si lo usas)
COPY ./wait-for-it.sh /wait-for-it.sh
USER root
RUN chmod +x /wait-for-it.sh
USER airflow

# Expone el puerto para el webserver
EXPOSE 8080

# Comando por defecto para iniciar el servidor web
CMD ["airflow", "webserver"]

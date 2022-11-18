# Quick Start
[Running Airflow in Docker](https://airflow.apache.org/docs/apache-airflow/stable/howto/docker-compose/index.html)

# Before you begin
docker must be installed!

# Initializing Environment

## Fetching `docker-compose.yaml`

To deploy Airflow on Docker Compose, you should fetch docker-compose.yaml.
```
curl -LfO 'https://airflow.apache.org/docs/apache-airflow/2.4.3/docker-compose.yaml'
```

## Setting the right Airflow user

On Linux, the quick-start needs to know your host user id and needs to have group id set to 0. Otherwise the files created in dags, logs and plugins will be created with root user ownership. You have to make sure to configure them for the docker-compose:
```
cd airflow-docker
mkdir -p ./dags ./logs ./plugins ./data
echo -e "AIRFLOW_UID=$(id -u)" > .env
```
For other operating systems, you may get a warning that AIRFLOW_UID is not set, but you can safely ignore it. You can also manually create an .env file in the same folder as docker-compose.yaml with this content

## Initialize the database
On all operating systems, you need to run database migrations and create the first user account. To do this, run.
```
docker compose up airflow-init
```

## Cleaning-up the environment
The docker-compose environment we have prepared is a “quick-start” one. It was not designed to be used in production and it has a number of caveats - one of them being that the best way to recover from any problem is to clean it up and restart from scratch.

The best way to do this is to:
Run command in the directory you downloaded the docker-compose.yaml file
```
docker-compose down --volumes --remove-orphans 
```


# Running Airflow
Now you can start all services:
```
docker-compose up -d
```

# Accessing the environment
After starting Airflow, you can interact with it in 3 ways:

 - by running CLI commands.

 - via a browser using the web interface.

 - using the REST API.


## Running the CLI commands
You can also run CLI commands, but you have to do it in one of the defined airflow-* services. For example, to run airflow info, run the following command:

```
docker-compose run airflow-worker airflow info
```

## Accessing the web interface
Once the cluster has started up, you can log in to the web interface and begin experimenting with DAGs.

The webserver is available at: `<http://localhost:8080>`. The default account has the login `airflow` and the password `airflow`.


## Sending requests to the REST API
Basic username password authentication is currently supported for the REST API, which means you can use common tools to send requests to the API.

The webserver is available at: `<http://localhost:8080>`. The default account has the login airflow and the password airflow.

Here is a sample curl command, which sends a request to retrieve a pool list:
```
ENDPOINT_URL="http://localhost:8080/"
curl -X GET  \
    --user "airflow:airflow" \
    "${ENDPOINT_URL}/api/v1/pools"
```

## Cleaning up
To stop and delete containers, delete volumes with database data and download images, run:

```
docker-compose down --volumes --rmi all
```

# FYI
This file contains several service definitions:

 - airflow-scheduler - The scheduler monitors all tasks and DAGs, then triggers the task instances once their dependencies are complete.

 - airflow-webserver - The webserver is available at <http://localhost:8080>.

 - airflow-worker - The worker that executes the tasks given by the scheduler.

 - airflow-init - The initialization service.

 - postgres - The database.

 - redis - The redis - broker that forwards messages from scheduler to worker.

Optionally, you can enable flower by adding --profile flower option, e.g. docker-compose --profile flower up, or by explicitly specifying it on the command line e.g. docker-compose up flower.

 - flower - The flower app for monitoring the environment. It is available at <http://localhost:5555>.

All these services allow you to run Airflow with CeleryExecutor. For more information, see Architecture Overview.

Some directories in the container are mounted, which means that their contents are synchronized between your computer and the container.

 - ./dags - you can put your DAG files here.

 - ./logs - contains logs from task execution and scheduler.

 - ./plugins - you can put your custom plugins here.

This file uses the latest Airflow image (apache/airflow). If you need to install a new Python library or system library, you can build your image.

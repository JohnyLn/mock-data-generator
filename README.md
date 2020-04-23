# mock-data-generator

## Preamble

At [Senzing](http://senzing.com),
we strive to create GitHub documentation in a
"[don't make me think](https://github.com/Senzing/knowledge-base/blob/master/WHATIS/dont-make-me-think.md)" style.
For the most part, instructions are copy and paste.
Whenever thinking is needed, it's marked with a "thinking" icon :thinking:.
Whenever customization is needed, it's marked with a "pencil" icon :pencil2:.
If the instructions are not clear, please let us know by opening a new
[Documentation issue](https://github.com/Senzing/mock-data-generator/issues/new?template=documentation_request.md)
describing where we can improve.   Now on with the show...

## Overview

The [mock-data-generator.py](mock-data-generator.py) python script produces mock data for Senzing.
The `senzing/mock-data-generator` docker image produces mock data for Senzing for use in
docker formations (e.g. docker-compose, kubernetes).

[mock-data-generator.py](mock-data-generator.py) has a number of subcommands
for performing different types of Senzing mock data creation.

To see all of the subcommands, run:

```console
$ ./mock-data-generator.py --help
usage: mock-data-generator.py [-h]
                              {version,random-to-stdout,random-to-kafka,url-to-stdout,url-to-kafka}
                              ...

Generate mock data from a URL-addressable file or templated random data. For
more information, see https://github.com/Senzing/mock-data-generator

positional arguments:
  {version,random-to-stdout,random-to-kafka,url-to-stdout,url-to-kafka}
                        Subcommands (SENZING_SUBCOMMAND):
    version             Print version of mock-data-generator.py.
    random-to-stdout    Send random data to STDOUT
    random-to-kafka     Send random data to Kafka
    random-to-rabbitmq  Send random data to RabbitMQ
    url-to-stdout       Send HTTP or file data to STDOUT
    url-to-kafka        Send HTTP or file data to Kafka
    url-to-rabbitmq     Send HTTP or file data to RabbitMQ

optional arguments:
  -h, --help            show this help message and exit
```

To see the options for a subcommand, run commands like:

```console
./mock-data-generator.py random-to-stdout --help
```

### Contents

1. [Related artifacts](#related-artifacts)
1. [Expectations](#expectations)
1. [Demonstrate using Command Line Interface](#demonstrate-using-command-line-interface)
    1. [Prerequisites for CLI](#prerequisites-for-cli)
    1. [Download](#download)
    1. [Run command](#run-command)
1. [Demonstrate using Docker](#demonstrate-using-docker)
    1. [Prerequisites for Docker](#prerequisites-for-docker)
    1. [Docker network](#docker-network)
    1. [Docker user](#docker-user)
    1. [Run Docker container](#run-docker-container)
1. [Develop](#develop)
    1. [Prerequisites for development](#prerequisites-for-development)
    1. [Clone repository](#clone-repository)
    1. [Build Docker image](#build-docker-image)
1. [Examples](#examples)
    1. [Examples of CLI](#examples-of-cli)
    1. [Examples of Docker](#examples-of-docker)
1. [Advanced](#advanced)
    1. [Configuration](#configuration)
1. [Errors](#errors)
1. [References](#references)

#### Legend

1. :thinking: - A "thinker" icon means that a little extra thinking may be required.
   Perhaps there are some choices to be made.
   Perhaps it's an optional step.
1. :pencil2: - A "pencil" icon means that the instructions may need modification before performing.
1. :warning: - A "warning" icon means that something tricky is happening, so pay attention.

## Related artifacts

1. [DockerHub](https://hub.docker.com/r/senzing/mock-data-generator)
1. [Helm Chart](https://github.com/Senzing/charts/tree/master/charts/senzing-mock-data-generator)

## Expectations

- **Space:** This repository and demonstration require 6 GB free disk space.
- **Time:** Budget 40 minutes to get the demonstration up-and-running, depending on CPU and network speeds.
- **Background knowledge:** This repository assumes a working knowledge of:
  - [Docker](https://github.com/Senzing/knowledge-base/blob/master/WHATIS/docker.md)

## Demonstrate using Command Line Interface

### Prerequisites for CLI

:thinking: The following tasks need to be complete before proceeding.
These are "one-time tasks" which may already have been completed.

1. Install system dependencies:
    1. Use `apt` based installation for Debian, Ubuntu and
       [others](https://en.wikipedia.org/wiki/List_of_Linux_distributions#Debian-based)
        1. See [apt-packages.txt](src/apt-packages.txt) for list
    1. Use `yum` based installation for Red Hat, CentOS, openSuse and
       [others](https://en.wikipedia.org/wiki/List_of_Linux_distributions#RPM-based).
        1. See [yum-packages.txt](src/yum-packages.txt) for list
1. Install Python dependencies:
    1. See [requirements.txt](requirements.txt) for list
        1. [Installation hints](https://github.com/Senzing/knowledge-base/blob/master/HOWTO/install-python-dependencies.md)

### Download

1. Get a local copy of
   [mock-data-generator.py](mock-data-generator.py).
   Example:

    1. :pencil2: Specify where to download file.
       Example:

        ```console
        export SENZING_DOWNLOAD_FILE=~/mock-data-generator.py
        ```

    1. Download file.
       Example:

        ```console
        curl -X GET \
          --output ${SENZING_DOWNLOAD_FILE} \
          https://raw.githubusercontent.com/Senzing/mock-data-generator/master/mock-data-generator.py
        ```

    1. Make file executable.
       Example:

        ```console
        chmod +x ${SENZING_DOWNLOAD_FILE}
        ```

1. :thinking: **Alternative:** The entire git repository can be downloaded by following instructions at
   [Clone repository](#clone-repository)

### Run command

1. Run the command.
   Example:

   ```console
   ${SENZING_DOWNLOAD_FILE} --help
   ```

1. For more examples of use, see [Examples of CLI](#examples-of-cli).

## Demonstrate using Docker

### Prerequisites for Docker

:thinking: The following tasks need to be complete before proceeding.
These are "one-time tasks" which may already have been completed.

1. The following software programs need to be installed:
    1. [docker](https://github.com/Senzing/knowledge-base/blob/master/HOWTO/install-docker.md)

### Docker network

:thinking: **Optional:**  Use if Docker container is part of a Docker network.

1. List Docker networks.
   Example:

    ```console
    sudo docker network ls
    ```

1. :pencil2: Specify Docker network.
   Choose value from NAME column of `docker network ls`.
   Example:

    ```console
    export SENZING_NETWORK=*nameofthe_network*
    ```

1. Construct parameter for `docker run`.
   Example:

    ```console
    export SENZING_NETWORK_PARAMETER="--net ${SENZING_NETWORK}"
    ```

### Docker user

:thinking: **Optional:**  The Docker container runs as "USER 1001".
Use if a different userid (UID) is required.

1. :pencil2: Identify user.
    1. **Example #1:** Use specific UID. User "0" is `root`.

        ```console
        export SENZING_RUNAS_USER="0"
        ```

    1. **Example #2:** Use current user.

        ```console
        export SENZING_RUNAS_USER=$(id -u)
        ```

1. Construct parameter for `docker run`.
   Example:

    ```console
    export SENZING_RUNAS_USER_PARAMETER="--user ${SENZING_RUNAS_USER}"
    ```

### Run Docker container

Although the `Docker run` command looks complex,
it accounts for all of the optional variations described above.
Unset environment variables have no effect on the
`docker run` command and may be removed or remain.

1. :pencil2: Set environment variables.
   Example:

    ```console
    export SENZING_SUBCOMMAND=url-to-stdout

    export SENZING_INPUT_URL=https://s3.amazonaws.com/public-read-access/TestDataSets/loadtest-dataset-1M.json
    export SENZING_RECORD_MAX=250
    export SENZING_RECORD_MIN=240
    export SENZING_RECORDS_PER_SECOND=0
    ```

1. Run Docker container.
   Example:

    ```console
    sudo docker run \
      --env SENZING_SUBCOMMAND="${SENZING_SUBCOMMAND}" \
      --env SENZING_INPUT_URL=${SENZING_INPUT_URL} \
      --env SENZING_RECORD_MAX="${SENZING_RECORD_MAX}" \
      --env SENZING_RECORD_MIN="${SENZING_RECORD_MIN}" \
      --env SENZING_RECORDS_PER_SECOND="${SENZING_RECORDS_PER_SECOND}" \
      --interactive \
      --rm \
      --tty \
      ${SENZING_NETWORK_PARAMETER} \
      ${SENZING_RUNAS_USER_PARAMETER} \
      senzing/mock-data-generator
    ```

1. For more examples of use, see [Examples of Docker](#examples-of-docker).

## Develop

The following instructions are used when modifying and building the Docker image.

### Prerequisites for development

:thinking: The following tasks need to be complete before proceeding.
These are "one-time tasks" which may already have been completed.

1. The following software programs need to be installed:
    1. [git](https://github.com/Senzing/knowledge-base/blob/master/HOWTO/install-git.md)
    1. [make](https://github.com/Senzing/knowledge-base/blob/master/HOWTO/install-make.md)
    1. [docker](https://github.com/Senzing/knowledge-base/blob/master/HOWTO/install-docker.md)

### Clone repository

For more information on environment variables,
see [Environment Variables](https://github.com/Senzing/knowledge-base/blob/master/lists/environment-variables.md).

1. Set these environment variable values:

    ```console
    export GIT_ACCOUNT=senzing
    export GIT_REPOSITORY=mock-data-generator
    export GIT_ACCOUNT_DIR=~/${GIT_ACCOUNT}.git
    export GIT_REPOSITORY_DIR="${GIT_ACCOUNT_DIR}/${GIT_REPOSITORY}"
    ```

1. Using the environment variables values just set, follow steps in [clone-repository](https://github.com/Senzing/knowledge-base/blob/master/HOWTO/clone-repository.md) to install the Git repository.

### Build Docker image

1. **Option #1:** Using `docker` command and GitHub.

    ```console
    sudo docker build \
      --tag senzing/mock-data-generator \
      https://github.com/senzing/mock-data-generator.git
    ```

1. **Option #2:** Using `docker` command and local repository.

    ```console
    cd ${GIT_REPOSITORY_DIR}
    sudo docker build --tag senzing/mock-data-generator .
    ```

1. **Option #3:** Using `make` command.

    ```console
    cd ${GIT_REPOSITORY_DIR}
    sudo make docker-build
    ```

    Note: `sudo make docker-build-development-cache` can be used to create cached Docker layers.

## Examples

### Examples of CLI

The following examples require initialization described in
[Demonstrate using Command Line Interface](#demonstrate-using-command-line-interface).

#### CLI Help

1. Show help for command and sub-commands.
   Example:

    ```console
    mock-data-generator.py --help
    mock-data-generator.py random-to-stdout --help
    ```

#### CLI random to STDOUT

1. Show random file output.
   Example:

    ```console
    mock-data-generator.py random-to-stdout
    ```

1. Show random file output with 1 record per second.
   Example:

    ```console
    mock-data-generator.py random-to-stdout \
      --records-per-second 1
    ```

1. Show repeatable "random" output using random seed.
   Example:

    ```console
    mock-data-generator.py random-to-stdout \
      --random-seed 1
    ```

1. Show generating 10 (repeatable) random records at the rate of 2 per second.
   Example:

    ```console
    mock-data-generator.py random-to-stdout \
      --random-seed 22 \
      --record-min 1 \
      --record-max 10 \
      --records-per-second 2
    ```

1. Show sending output to a file of JSON-lines.
   Example:

    ```console
    mock-data-generator.py random-to-stdout \
      --random-seed 22 \
      --record-min 1 \
      --record-max 10 \
      --records-per-second 2 \
      > output-file.jsonlines
    ```

#### CLI URL to STDOUT

1. Show reading 5 records from URL-based file at the rate of 3 per second.
   Example:

    ```console
    mock-data-generator.py url-to-stdout \
      --input-url https://s3.amazonaws.com/public-read-access/TestDataSets/loadtest-dataset-1M.json \
      --record-min 1 \
      --record-max 5 \
      --records-per-second 3
    ```

### Examples of Docker

The following examples require initialization described in
[Demonstrate using Docker](#demonstrate-using-docker).

#### Docker random to STDOUT

1. :pencil2: Set environment variables.
   Example:

    ```console
    export SENZING_SUBCOMMAND=random-to-stdout
    export SENZING_RANDOM_SEED=0
    export SENZING_RECORD_MAX=10
    export SENZING_RECORD_MIN=1
    export SENZING_RECORDS_PER_SECOND=0
    ```

1. Run the docker container.
   Example:

    ```console
    sudo docker run \
      --env SENZING_SUBCOMMAND="${SENZING_SUBCOMMAND}" \
      --env SENZING_RANDOM_SEED="${SENZING_RANDOM_SEED}" \
      --env SENZING_RECORD_MAX="${SENZING_RECORD_MAX}" \
      --env SENZING_RECORD_MIN="${SENZING_RECORD_MIN}" \
      --env SENZING_RECORDS_PER_SECOND="${SENZING_RECORDS_PER_SECOND}" \
      --interactive \
      --rm \
      --tty \
      ${SENZING_NETWORK_PARAMETER} \
      ${SENZING_RUNAS_USER_PARAMETER} \
      senzing/mock-data-generator
    ```

#### Docker random to Kafka

1. :pencil2: Set environment variables.
   Example:

    ```console
    export SENZING_SUBCOMMAND=random-to-kafka

    export SENZING_KAFKA_BOOTSTRAP_SERVER=senzing-kafka:9092
    export SENZING_KAFKA_TOPIC="senzing-kafka-topic"
    export SENZING_NETWORK=senzingdockercomposestreamloaderdemo_backend
    export SENZING_RANDOM_SEED=1
    export SENZING_RECORD_MAX=220
    export SENZING_RECORD_MIN=210
    export SENZING_RECORDS_PER_SECOND=1
    ```

1. Run the docker container.
   Example:

    ```console
    sudo docker run \
      --env SENZING_SUBCOMMAND="${SENZING_SUBCOMMAND}" \
      --env SENZING_KAFKA_BOOTSTRAP_SERVER=${SENZING_KAFKA_BOOTSTRAP_SERVER} \
      --env SENZING_KAFKA_TOPIC=${SENZING_KAFKA_TOPIC} \
      --env SENZING_RANDOM_SEED="${SENZING_RANDOM_SEED}" \
      --env SENZING_RECORD_MAX="${SENZING_RECORD_MAX}" \
      --env SENZING_RECORD_MIN="${SENZING_RECORD_MIN}" \
      --env SENZING_RECORDS_PER_SECOND="${SENZING_RECORDS_PER_SECOND}" \
      --interactive \
      --rm \
      --tty \
      ${SENZING_NETWORK_PARAMETER} \
      ${SENZING_RUNAS_USER_PARAMETER} \
      senzing/mock-data-generator
    ```

#### Docker URL to STDOUT

1. :pencil2: Set environment variables.
   Example:

    ```console
    export SENZING_SUBCOMMAND=url-to-stdout

    export SENZING_INPUT_URL=https://s3.amazonaws.com/public-read-access/TestDataSets/loadtest-dataset-1M.json
    export SENZING_RECORD_MAX=250
    export SENZING_RECORD_MIN=240
    export SENZING_RECORDS_PER_SECOND=0
    ```

1. Run the docker container.
   Example:

    ```console
    sudo docker run \
      --env SENZING_SUBCOMMAND="${SENZING_SUBCOMMAND}" \
      --env SENZING_INPUT_URL=${SENZING_INPUT_URL} \
      --env SENZING_RECORD_MAX="${SENZING_RECORD_MAX}" \
      --env SENZING_RECORD_MIN="${SENZING_RECORD_MIN}" \
      --env SENZING_RECORDS_PER_SECOND="${SENZING_RECORDS_PER_SECOND}" \
      --interactive \
      --rm \
      --tty \
      ${SENZING_NETWORK_PARAMETER} \
      ${SENZING_RUNAS_USER_PARAMETER} \
      senzing/mock-data-generator
    ```

#### Docker URL to Kafka

1. :pencil2: Set environment variables.
   Example:

    ```console
    export SENZING_SUBCOMMAND=url-to-kafka

    export SENZING_INPUT_URL=https://s3.amazonaws.com/public-read-access/TestDataSets/loadtest-dataset-1M.json
    export SENZING_KAFKA_BOOTSTRAP_SERVER=senzing-kafka:9092
    export SENZING_KAFKA_TOPIC="senzing-kafka-topic"
    export SENZING_NETWORK=senzingdockercomposestreamloaderdemo_backend
    export SENZING_RECORD_MAX=300
    export SENZING_RECORD_MIN=260
    export SENZING_RECORD_MONITOR=10
    export SENZING_RECORDS_PER_SECOND=10
    ```

1. Run docker container.
   Example:

    ```console
    sudo docker run \
      --env SENZING_SUBCOMMAND="${SENZING_SUBCOMMAND}" \
      --env SENZING_INPUT_URL=${SENZING_INPUT_URL} \
      --env SENZING_KAFKA_BOOTSTRAP_SERVER=${SENZING_KAFKA_BOOTSTRAP_SERVER} \
      --env SENZING_KAFKA_TOPIC=${SENZING_KAFKA_TOPIC} \
      --env SENZING_RECORD_MAX="${SENZING_RECORD_MAX}" \
      --env SENZING_RECORD_MIN="${SENZING_RECORD_MIN}" \
      --env SENZING_RECORD_MONITOR="${SENZING_RECORD_MONITOR}" \
      --env SENZING_RECORDS_PER_SECOND="${SENZING_RECORDS_PER_SECOND}" \
      --interactive \
      --rm \
      --tty \
      ${SENZING_NETWORK_PARAMETER} \
      ${SENZING_RUNAS_USER_PARAMETER} \
      senzing/mock-data-generator
    ```

## Advanced

### Configuration

Configuration values specified by environment variable or command line parameter.

- **[SENZING_DATA_SOURCE](https://github.com/Senzing/knowledge-base/blob/master/lists/environment-variables.md#senzing_data_source)**
- **[SENZING_DEBUG](https://github.com/Senzing/knowledge-base/blob/master/lists/environment-variables.md#senzing_debug)**
- **[SENZING_ENTITY_TYPE](https://github.com/Senzing/knowledge-base/blob/master/lists/environment-variables.md#senzing_entity_type)**
- **[SENZING_INPUT_URL](https://github.com/Senzing/knowledge-base/blob/master/lists/environment-variables.md#senzing_input_url)**
  - Default: [https://s3.amazonaws.com/public-read-access/TestDataSets/loadtest-dataset-1M.json](https://s3.amazonaws.com/public-read-access/TestDataSets/loadtest-dataset-1M.json)
- **[SENZING_KAFKA_BOOTSTRAP_SERVER](https://github.com/Senzing/knowledge-base/blob/master/lists/environment-variables.md#senzing_kafka_bootstrap_server)**
- **[SENZING_KAFKA_TOPIC](https://github.com/Senzing/knowledge-base/blob/master/lists/environment-variables.md#senzing_kafka_topic)**
- **[SENZING_RABBITMQ_HOST](https://github.com/Senzing/knowledge-base/blob/master/lists/environment-variables.md#senzing_rabbitmq_host)**
- **[SENZING_RABBITMQ_PASSWORD](https://github.com/Senzing/knowledge-base/blob/master/lists/environment-variables.md#senzing_rabbitmq_password)**
- **[SENZING_RABBITMQ_QUEUE](https://github.com/Senzing/knowledge-base/blob/master/lists/environment-variables.md#senzing_rabbitmq_queue)**
- **[SENZING_RABBITMQ_USERNAME](https://github.com/Senzing/knowledge-base/blob/master/lists/environment-variables.md#senzing_rabbitmq_username)**
- **[SENZING_RANDOM_SEED](https://github.com/Senzing/knowledge-base/blob/master/lists/environment-variables.md#senzing_random_seed)**
- **[SENZING_RECORD_MAX](https://github.com/Senzing/knowledge-base/blob/master/lists/environment-variables.md#senzing_record_max)**
- **[SENZING_RECORD_MIN](https://github.com/Senzing/knowledge-base/blob/master/lists/environment-variables.md#senzing_record_min)**
- **[SENZING_RECORD_MONITOR](https://github.com/Senzing/knowledge-base/blob/master/lists/environment-variables.md#senzing_record_monitor)**
- **[SENZING_RECORDS_PER_SECOND](https://github.com/Senzing/knowledge-base/blob/master/lists/environment-variables.md#senzing_records_per_second)**
- **[SENZING_SUBCOMMAND](https://github.com/Senzing/knowledge-base/blob/master/lists/environment-variables.md#senzing_subcommand)**

## Errors

1. See [docs/errors.md](docs/errors.md).

## References

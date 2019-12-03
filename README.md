# mock-data-generator

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

### Related artifacts

1. [DockerHub](https://hub.docker.com/r/senzing/mock-data-generator)
1. [Helm Chart](https://github.com/Senzing/charts/tree/master/charts/senzing-mock-data-generator)

### Contents

1. [Using Command Line](#using-command-line)
    1. [Install](#install)
    1. [Install dependencies](#install-dependencies)
    1. [Demonstrate](#demonstrate)
1. [Using Docker](#using-docker)
    1. [Expectations](#expectations)
    1. [Configuration](#configuration)
    1. [Run docker container](#run-docker-container)
        1. [Demonstrate random to STDOUT](#demonstrate-random-to-stdout)
        1. [Demonstrate random to Kafka](#demonstrate-random-to-kafka)
        1. [Demonstrate URL to STDOUT](#demonstrate-url-to-stdout)
        1. [Demonstrate URL to Kafka](#demonstrate-url-to-kafka)
1. [Develop](#develop)
    1. [Prerequisite software](#prerequisite-software)
    1. [Clone repository](#clone-repository)
    1. [Build docker image for development](#build-docker-image-for-development)
1. [Examples](#examples)
1. [Errors](#errors)

### Legend

1. :thinking: - A "thinker" icon means that a little extra thinking may be required.
   Perhaps you'll need to make some choices.
   Perhaps it's an optional step.
1. :pencil2: - A "pencil" icon means that the instructions may need modification before performing.
1. :warning: - A "warning" icon means that something tricky is happening, so pay attention.

## Using Command Line

### Install

See [Clone repository](#clone-repository).

### Install dependencies

1. YUM installs - For Red Hat, CentOS, openSuse, and [others](https://en.wikipedia.org/wiki/List_of_Linux_distributions#RPM-based).

    ```console
    sudo xargs yum -y install < ${GIT_REPOSITORY_DIR}/src/yum-packages.txt
    ```

1. APT installs - For Debian, Ubuntu, and [others](https://en.wikipedia.org/wiki/List_of_Linux_distributions#Debian-based)

    ```console
    sudo xargs apt -y install < ${GIT_REPOSITORY_DIR}/src/apt-packages.txt
    ```

1. PIP installs

    ```console
    sudo pip install -r ${GIT_REPOSITORY_DIR}/requirements.txt
    ```

### Demonstrate

1. Show help.
   Example:

    ```console
    cd ${GIT_REPOSITORY_DIR}
    ./mock-data-generator.py --help
    ./mock-data-generator.py random-to-stdout --help
    ```

1. Show random file output.
   Example:

    ```console
    cd ${GIT_REPOSITORY_DIR}
    ./mock-data-generator.py random-to-stdout
    ```

1. Show random file output with 1 record per second.
   Example:

    ```console
    cd ${GIT_REPOSITORY_DIR}
    ./mock-data-generator.py random-to-stdout \
      --records-per-second 1
    ```

1. Show repeatable "random" output using random seed.
   Example:

    ```console
    cd ${GIT_REPOSITORY_DIR}
    ./mock-data-generator.py random-to-stdout \
      --random-seed 1
    ```

1. Show generating 10 (repeatable) random records at the rate of 2 per second.
   Example:

    ```console
    cd ${GIT_REPOSITORY_DIR}
    ./mock-data-generator.py random-to-stdout \
      --random-seed 22 \
      --record-min 1 \
      --record-max 10 \
      --records-per-second 2
    ```

1. Show sending output to a file of JSON-lines.
   Example:

    ```console
    cd ${GIT_REPOSITORY_DIR}
    ./mock-data-generator.py random-to-stdout \
      --random-seed 22 \
      --record-min 1 \
      --record-max 10 \
      --records-per-second 2 \
      > output-file.jsonlines
    ```

1. Show reading 5 records from URL-based file at the rate of 3 per second.
   Example:

    ```console
    cd ${GIT_REPOSITORY_DIR}
    ./mock-data-generator.py url-to-stdout \
      --input-url https://s3.amazonaws.com/public-read-access/TestDataSets/loadtest-dataset-1M.json \
      --record-min 1 \
      --record-max 5 \
      --records-per-second 3
    ```

## Using Docker

### Expectations

#### Space

This repository and demonstration require 6 GB free disk space.

#### Time

Budget 40 minutes to get the demonstration up-and-running, depending on CPU and network speeds.

#### Background knowledge

This repository assumes a working knowledge of:

1. [Docker](https://github.com/Senzing/knowledge-base/blob/master/WHATIS/docker.md)

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

1. To determine which configuration parameters are use for each `<subcommand>`, run:

    ```console
    ./mock-data-generator.py <subcommand> --help
    ```

### Docker network

:thinking: **Optional:**  Use if docker container is part of a docker network.

1. List docker networks.
   Example:

    ```console
    sudo docker network ls
    ```

1. :pencil2: Specify docker network.
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

:thinking: **Optional:**  The docker container runs as "USER 1001".
Use if a different userid (UID) is required.

1. :pencil2: Manually identify user.
   User "0" is root.
   Example:

    ```console
    export SENZING_RUNAS_USER="0"
    ```

   Another option, use current user.
   Example:

    ```console
    export SENZING_RUNAS_USER=$(id -u)
    ```

1. Construct parameter for `docker run`.
   Example:

    ```console
    export SENZING_RUNAS_USER_PARAMETER="--user ${SENZING_RUNAS_USER}"
    ```

### Run docker container

#### Demonstrate random to STDOUT

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

#### Demonstrate random to Kafka

1. :pencil2: Determine docker network.
   Example:

    ```console
    sudo docker network ls

    # Choose value from NAME column of docker network ls
    export SENZING_NETWORK=nameofthe_network
    ```

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

#### Demonstrate URL to STDOUT

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

#### Demonstrate URL to Kafka

1. :pencil2: Determine docker network.
   Example:

    ```console
    sudo docker network ls

    # Choose value from NAME column of docker network ls
    export SENZING_NETWORK=nameofthe_network
    ```

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

## Develop

### Prerequisite software

The following software programs need to be installed:

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

1. Follow steps in [clone-repository](https://github.com/Senzing/knowledge-base/blob/master/HOWTO/clone-repository.md) to install the Git repository.

### Build docker image for development

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

    Note: `sudo make docker-build-development-cache` can be used to create cached docker layers.

## Examples

1. Examples of use:
    1. [docker-compose-stream-loader-kafka-demo](https://github.com/Senzing/docker-compose-stream-loader-kafka-demo)
    1. [kubernetes-demo](https://github.com/Senzing/kubernetes-demo)
    1. [rancher-demo](https://github.com/Senzing/rancher-demo/tree/master/docs/db2-cluster-demo.md)

## Errors

1. See [docs/errors.md](docs/errors.md).

## References

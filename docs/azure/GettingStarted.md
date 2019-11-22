# Getting started

## System requirements

You need a development environment with the
[next LTS release candidate of Node.js](https://nodejs.org/en/about/releases/)
(right now version 12).

If you are using Windows, we recommend the
[Windows Subsystem for Linux](https://docs.microsoft.com/en-us/windows/wsl/install-win10)
with
[Ubuntu 18.04 LTS](https://www.microsoft.com/nb-no/p/ubuntu-1804-lts/9n9tngvndl3q?rtc=1).

## Install the Azure CLI

Follow the instructions from the
[Azure CLI documentation](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest)
in order to install the CLI.

Afterwards you should be able to execute the `az` command:

    az

### Dockerizing the `az` command

In case you encounter the issue where the azure CLI
[requires an older Python version](https://github.com/Azure/azure-cli/issues/11239),
you can _dockerize_ it like this:

    #!/usr/bin/env bash
    set -eu

    command="$@"

    docker run --rm --volume `pwd`:/root --volume $HOME/.azure:/root/.azure -w=/root mcr.microsoft.com/azure-cli az $command

Put above into an executable file in your path.

## Deploy the solution to your account

Authenticate the CLI:

    az login

Deploy the solution in your preferred location (you can list them using
`az account list-locations`):

    LOCATION=northeurope
    az group create -l $LOCATION -n bifravst
    az group deployment create --resource-group bifravst --mode Complete --name bifravst --template-file azuredeploy.json \
        --parameters iotHubName='bifravst' location="$LOCATION"

## Generate IoT enrollment group

Azure supports offline generation of device certificates through
[Enrollment Groups](https://docs.microsoft.com/bs-latn-ba/azure/iot-dps/quick-enroll-device-x509-node).
For these a CA certificate needs to be registered:

    node cli register-ca
    # this is a separate step because immediate verification may fail
    node cli proof-ca-posession
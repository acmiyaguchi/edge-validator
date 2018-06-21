# edge-validator

A service-endpoint for validating pings against `mozilla-pipeline-schemas`.

[![CircleCI](https://circleci.com/gh/mozilla-services/edge-validator.svg?style=svg)](https://circleci.com/gh/mozilla-services/edge-validator)

See [bug 1452166](https://bugzilla.mozilla.org/show_bug.cgi?id=1452166) for motivating background.

### Quickstart

Fetch and run the image from DockerHub:

```bash
docker run -it mozilla/edge-validator:latest
```

Start posting documents:
```
$ OK_DATA="$(echo '{"payload": {"foo": true, "bar": 1, "baz": "hello world"}}')"
$ curl -X POST -d "${OK_DATA}" localhost:8000/submit/testing/test/1
> OK
```

### Installation
#### Building from source
```bash
# clone and set the working directory
$ git clone --recursive https://github.com/mozilla-services/edge-validator.git
$ cd edge-validator

# if the `--recursive` option was omitted, then update and initialize the submodule
$ git submodule update --init

# make sure that the system pip is up to date
$ pip install --user --upgrade pip

# install pipenv for managing the application environment
$ pip install --user pipenv

# bootstrap for test/report/serve
$ make sync
```

The docker environment is suitable for running a local service or for running any of the testing suites.

```bash
$ make shell

# Alternatively
$ docker run -it edge-validator:latest pipenv shell
```

If you don't require permanent changes to the engine itself, you may pull down a prebuilt docker image through
DockerHub using the `mozilla/edge-validator:latest` image.

### Serving
#### serving via docker host (recommended)

```bash
$ docker --version          # ensure that docker is installed
$ make build                # build the container
$ make serve                # start the service on localhost:8000
```

#### serving via local host
The docker host automates the following bootstrap process.
`pipenv` should be installed on the host system.

```bash
$ pipenv shell              # enter the application environment
$ pipenv sync               # update the environment
$ flask run --port 8000     # run the application
```

### Running Tests

Unit tests do not require any dependencies and can be run out of the box.
The sync command will copy the test resources into the application resource folder.
```bash
$ make sync
$ make test
```

You may also run the tests in docker in the same way as CI.
A `junit.xml` file is generated in a `test-reports` folder.

```bash
IMAGE=edge-validator:latest ./docker_env.sh test
```

# Running Integration Tests

An integration report gives a performance report based on sampled data.

Ensure that the AWS cli is correctly configured.

```bash
$ aws s3 ls s3://telemetry-test-bucket/
```

Then run the report.

```bash
# Run using the local app context
$ make report

# Run using the docker host
$ EXTERNAL=1 PORT=8000 make report
```

The report can also be run in Docker when given the correct permissions.
```bash
$ docker run \
    -e AWS_ACCESS_KEY_ID \
    -e AWS_SECRET_ACCESS_KEY \
    -it edge-validator:latest \
    make report
```

You may also be interested in a machine consumable integration report across multiple branches.
```bash
$ pipenv run ./integration.py report --report-path test-reports/dev.report.json
```

### Contributing
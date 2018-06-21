
Quickstart
==========

You can fetch and run the image from DockerHub::

    docker run -it mozilla/edge-validator:latest

Validating Pings
-----------------

Simply POST to the endpoint to check if a document is valid.
The `testing` namespace has an example schema for validation.::

    $ OK_DATA="$(echo '{"payload": {"foo": true, "bar": 1, "baz": "hello world"}}')"
    $ curl -X POST -d "${OK_DATA}" localhost:8000/submit/testing/test/1
    > OK

The endpoint will return `200 OK` on a successful POST.
The response will be `400 BAD` if the posted documented does not pass validation.
If the URI is malformed, the validator may return with a `404 NOT FOUND`.
The status will also include the exception that caused the error.::

    $ BAD_DATA="$(echo '{"payload": {"foo": null, "bar": "3", "baz": 55}}')"
    $ curl -X POST -d "${BAD_DATA}" localhost:8000/submit/testing/test/1
    > BAD: ('type', '#/properties/payload/properties/foo', '#/payload/foo')

In this example, `payload.foo` should be a boolean and `payload.baz` should be a string.
Currently, only the first validation exception will be propagated to the user.

Mounting schemas
----------------

It is possible to mount a set of local json-schemas by mounting a folder structure mirroring `mozilla-services/mozilla-pipeline-schemas` to the container's `/app/resources/schemas` directory. ::

    $ cd mozilla-pipeline-schemas
    $ docker run -v "$(pwd)"/schemas:/app/resources/schemas -it edge-validator
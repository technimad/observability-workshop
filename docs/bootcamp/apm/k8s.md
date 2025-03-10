TODO Note on .env being overwritten

TODO change name of environment from YOURENV to something else.

The development team has started using Kubernetes for container orchestration. Switch to the provided milestone `12microservices-k8s` with the instructions from "Getting Started".

The Kubernetes manifests are located in the `k8s` folder. Add auto-instrumentation to the `public_api` microservice `deployment` by configuring the [Splunk distribution of OpenTelemetry Python][splunk-otel-python]. The `Dockerfile` has already been prepared.

Install the OpenTelemetry Collector to the environment using [Splunk's helm chart][splunk-otel-helm] and use the provided `values.yaml`:

=== "Shell Command"

    ```bash
    helm repo add splunk-otel-collector-chart https://signalfx.github.io/splunk-otel-collector-chart

    helm install my-splunk-otel-collector --set="splunkObservability.realm=${SPLUNK_REALM},splunkObservability.accessToken=${SPLUNK_ACCESS_TOKEN},clusterName=${CLUSTER_NAME}" splunk-otel-collector-chart/splunk-otel-collector -f values.yaml
    ```

Rebuild the container images for the private registry:

=== "Shell Command"

    ```bash
    docker-compose build
    ```

Push the images to the private registry:

=== "Shell Command"

    ```bash
    docker-compose push
    ```

Deploy to the cluster with

=== "Shell Command"

    ```bash
    kubectl apply -f k8s
    ```

Test the service with

=== "Shell Command"

    ```bash
    ENDPOINT=$(kubectl get service/public-api -o jsonpath='{.spec.clusterIP}')
    curl http://$ENDPOINT:8000/api -F text=@hamlet.txt
    ```

The milestone for this task is `12microservices-k8s-autoi`. It has auto-instrumentation applied for *all* microservices.

[splunk-otel-helm]: https://github.com/signalfx/splunk-otel-collector-chart


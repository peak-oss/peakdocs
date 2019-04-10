# Peak

Peak is open source software to help you manage the performance of your web applications and APIs.

## How it works

Peak uses the Kubernetes Batch API to load-test your web apps and APIs. When you create a test suite, you're asked to specify the number of nodes that will be used for the test. Peak will create each node as an Alpine Linux container running in a Kubernetes pod, and each measures the response time of requests and posts the results back to an InfluxDB instance. You can then use Grafana with the Peak dashboard to visualise your results.

## Deployment

### Deploying Peak with the Operator Framework

Peak provides an Ansible Operator that you can use to easily deploy and consume the application on OpenShift. There's a step-by-step guide to deployment provided in the [Peak Operator docs](https://github.com/peak-oss/peakoperator/blob/master/README.md#deploying-the-operator-and-crd-on-openshift).

### Deploying Peak with the OpenShift template

If you don't have access to the Operator Framework, Peak also provides an OpenShift template to help you get an up-and-running rapidly.

Firstly, create a new OpenShift project:
```
oc new-project peak-project
```
Deploy a PostgreSQL database for the peak API service:
```
oc new-app postgresql-persistent -p POSTGRESQL_DATABASE=peakdb -p VOLUME_CAPACITY=1Gi -p POSTGRESQL_VERSION=9.6
```
Create app components from the template:
```
oc new-app -f <(curl https://raw.githubusercontent.com/peak-oss/peakdocs/master/templates/peak.yaml)
```

### Peak Grafana Configuration

Peak uses a custom Grafana dashboard to display metrics, and the following steps show you how to configure Grafana.

Access the Grafana URL, and enter the default credentials `admin/admin` when prompted. Create a new, secure password when prompted
![grafana_login](https://i.imgur.com/iu3hXqO.png)

Select `Add datasource` from the home screen, and enter the following:

Key | Value
--- | ---
Name | influxdb
Type | InfluxDB
URL | http://influxdb:8086
Database | peakdb

![db_details](https://i.imgur.com/rrpQPBg.png)

Import a dashboard, and specify `8463` as the Grafana ID. When prompted in the following screen, select the InfluxDB datasource.

![dashboard_import](https://i.imgur.com/c6NTCer.png)

![dashboard_config](https://i.imgur.com/BKqba25.png)

That's it! Your peak instance should be able to run tests against your APIs, report the metrics into InfluxDB, and visualise in the Grafana dashboard.

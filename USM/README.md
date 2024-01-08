# USM

## Why ?

A single service may be deployed accross multiple hosts/instances, but we need to look at the performance of that service as an aggregation of all those instances, presenting the service as a single entity.

USM works as a combination of the **Datadog agent** and **Unified Service Tagging**. Application changes aren't needed, only angent configuration changes are. 

The pre-reqs are:
- Running on a container (if Linux)
- Running on a VM (if IIS on Windows)
- The agent needs to be installed along side the service
- the **env** tag must be applied to the deployment

If these pre-reqs are all met, you can configure the agent (via docker-compose.yaml for Docker or system-probe.yaml for Windows).

## Unified Service Tagging

Using common container tags such as ***app***, ***short_image*** and ***container_name*** the corresponding entries in the Service Catalog are created.

Once done metrics for "access requests", "errors" and "duration" will be available for inbound and outbound traffic on that service. These metrics can be used to set up SLOs and alerts on your services.

Clicking on APM -> Universal Service Monitoring will bring up the Service Catalog with the query `telemetry:universal_service_monitoring` pre-populated, in order to exclude other service lists (like RUM and APM).

The three most common tags used to identify a service are: "env" (prod, dev, etc.), "service" (name of the service) and version (version number). In the case of a container, these tags are set via the container label.

Container label example:

```bash
   com.datadoghq.ad.logs: '[{"source": "nodejs", "service": "store-frontend"}]'
   com.datadoghq.tags.env: '${DD_ENV}'
   com.datadoghq.tags.service: 'store-frontend'
   com.datadoghq.tags.version: '1.0.10'
   my.custom.label.team: 'frontend'
```

Universal Service Monitoring automatically detects services running in your infrastructure. If it does not find unified service tags, it assigns them a name based on one of the tags: app, short_image, kube_container_name, container_name, kube_deployment, kube_service.s


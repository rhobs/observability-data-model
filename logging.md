# Logging data model

Current logging data model:
- [ViaQ Data Model](https://viaq.github.io/documentation/data_model/public/data_model.html)

Relevant OTEL models:
- [Logs Data Model](https://github.com/open-telemetry/opentelemetry-specification/blob/main/specification/logs/data-model.md)
- [Kubernetes Resource Semantic Conventions](https://github.com/open-telemetry/semantic-conventions/blob/main/docs/resource/k8s.md)

Current OTEL support is Tech Preview, JSON over HTTP only.

[Transformation from ViaQ to OTEL](https://github.com/openshift/cluster-logging-operator/blob/master/internal/generator/vector/normalize/schema/otel/transform.go#L16)

``` vrl
	# Convert @timestamp to nano and delete @timestamp
	.timeUnixNano = to_unix_timestamp!(del(.@timestamp), unit:"nanoseconds")

	.severityText = del(.level)

	# Convert syslog severity keyword to number, default to 9 (unknown)
	.severityNumber = to_syslog_severity(.severityText) ?? 9

	# resources
	.resources.logs.file.path = del(.file)
	.resources.host.name= del(.hostname)
	.resources.container.name = del(.kubernetes.container_name)
	.resources.container.id = del(.kubernetes.container_id)

	# split image name and tag into separate fields
	container_image_slice = split!(.kubernetes.container_image, ":", limit: 2)
	if null != container_image_slice[0] { .resources.container.image.name = container_image_slice[0] }
	if null != container_image_slice[1] { .resources.container.image.tag = container_image_slice[1] }
	del(.kubernetes.container_image)

	# kuberenetes
	.resources.k8s.pod.name = del(.kubernetes.pod_name)
	.resources.k8s.pod.uid = del(.kubernetes.pod_id)
	.resources.k8s.pod.ip = del(.kubernetes.pod_ip)
	.resources.k8s.pod.owner = .kubernetes.pod_owner
	.resources.k8s.pod.annotations = del(.kubernetes.annotations)
	.resources.k8s.pod.labels = del(.kubernetes.labels)
	.resources.k8s.namespace.id = del(.kubernetes.namespace_id)
	.resources.k8s.namespace.name = .kubernetes.namespace_labels."kubernetes.io/metadata.name"
	.resources.k8s.namespace.labels = del(.kubernetes.namespace_labels)
	.resources.attributes.log_type = del(.log_type)
```

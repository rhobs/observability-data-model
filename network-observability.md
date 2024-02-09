# Network Observability

This is a draft document, not implemented.

## Current format

[Network Observability](https://github.com/netobserv/) (or NetObserv) currently uses a custom format documented here: https://github.com/netobserv/network-observability-operator/blob/main/docs/flows-format.adoc.

In the current NetObserv implementation, this format, or subsets of this format, can be retrieved as various data sources:
- As logs, such as stored in Grafana Loki, using a JSON format
- As metrics labels
- As consumable JSON from Kafka

## Relevant OTEL semantic conventions

There is no perfect match between OTEL semantics and NetObserv format, however several conventions can be extrapolated to create a new model in OTEL style:

- Traffic by source and destination: https://github.com/open-telemetry/semantic-conventions/blob/main/docs/general/attributes.md#source-and-destination-attributes
- Kubernetes resources: https://github.com/open-telemetry/semantic-conventions/blob/main/docs/resource/k8s.md

These two specifications are combined by prefixing with "source." or "destination." any resource such as a Kubernetes Pod.

A tradeoff is also done to avoid too much redundancy and its associated cost (increased message size, increased storage): the model being entirely related to networking, the `network.` prefix is omitted and considered as implicit. As a result, a field such as the network protocol is referred to as `protocol` instead of `network.protocol`.

## Format proposal

This new format is currently a proposal and is subject to change before being implemented in NetObserv.

The conversions are as follow:

```
SrcAddr                -> source.address
SrcMac                 -> source.mac
SrcHostIP              -> source.host.address
SrcK8S_HostName        -> source.k8s.node.name
SrcPort                -> source.port
SrcK8S_Name            -> source.k8s.name
SrcK8S_Type            -> source.k8s.kind
SrcK8S_OwnerName       -> source.k8s.owner.name
SrcK8S_OwnerType       -> source.k8s.owner.kind
SrcK8S_Namespace       -> source.k8s.namespace.name
SrcK8S_HostIP          -> source.k8s.host.address
SrcK8S_HostName        -> source.k8s.host.name
SrcK8S_Zone            -> source.zone
DstAddr                -> destination.address
DstMac                 -> destination.mac
DstHostIP              -> destination.host.address
DstK8S_HostName        -> destination.k8s.node.name
DstPort                -> destination.port
DstK8S_Name            -> destination.k8s.name
DstK8S_Type            -> destination.k8s.kind
DstK8S_OwnerName       -> destination.k8s.owner.name
DstK8S_OwnerType       -> destination.k8s.owner.kind
DstK8S_HostIP          -> destination.k8s.host.address
DstK8S_HostName        -> destination.k8s.host.name
DstK8S_Namespace       -> destination.k8s.namespace.name
DstK8S_Zone            -> destination.zone
Bytes                  -> bytes
Packets                -> packets
Proto                  -> protocol
Flags                  -> tcp.flags
TimeFlowRttNs          -> tcp.rtt
Interface              -> interface.name
IfDirection            -> interface.direction
FlowDirection          -> host.direction
DnsErrno               -> dns.errno
DnsFlags               -> dns.flags
DnsFlagsResponseCode   -> dns.responsecode
DnsId                  -> dns.id
DnsLatencyMs           -> dns.latency
Dscp                   -> dscp
IcmpCode               -> icmp.code
IcmpType               -> icmp.type
K8S_ClusterName        -> k8s.cluster.name
K8S_FlowLayer          -> k8s.layer
PktDropBytes           -> drops.bytes
PktDropPackets         -> drops.packets
PktDropLatestDropCause -> drops.latestcause
PktDropLatestFlags     -> drops.latestflags
PktDropLatestState     -> drops.lateststate
TimeFlowEndMs          -> timeflowend
TimeFlowStartMs        -> timeflowstart
TimeReceived           -> timereceived
```                   

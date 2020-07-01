---
reviewers:
  - TBD
title: Eviction
content_type: concept
weight: 70
---

<!-- overview -->

_Eviciton_ refers to the ways a running {{< glossary_tooltip text="Pod" term_id="pod" >}} can be
[terminated](docs/concepts/workloads/pods/pod/#termination-of-pods) and removed from a 
{{< glossary_tooltip text="Node" term_id="node" >}}.

<!-- body -->

## Eviction

A pod is evicted when it is forced to terminate due a voluntary or involuntary
[disruption](docs/concepts/workloads/pods/disruptions/) in the cluster.
Under normal circumstances, a pod is expected to run until all of its containers complete.
For many applications (like a website), the containers inside a pod can run indefinitely.

When a pod is evicted, the kubelet marks the pod for deletion and signals all running containers
in the pod to
[terminate](docs/concepts/workloads/pods/pod/#termination-of-pods).
Containers which do not gracefully terminate are forced to stop by the kubelet.

## User Initiated Eviction

Developers and application owners can knowingly or unknowlingly evict running pods. Deleting a pod
is perhaps the simplest means of evicting a pod. Other ways a pod can be evicted by users include:

1. Scaling a `Deployment` or other scalable workload.
2. Changing the pod template of a `Deployment` or other workload.
3. Deploying a
   [Horizontal Pod Autoscaler](docs/tasks/run-application/horizontal-pod-autoscale/)
   to automatically add or remove replicas from a scalable resource.
4. Requesting that a pod be evicted via the
   [Eviction API](docs/tasks/administer-cluster/safely-drain-node/#the-eviction-api).

## Cluster Administration Initiated Eviction

Cluster administrators can also take actions which lead to pod eviction.
Pods can be evicted if a cluster administrator places a
[taint](docs/concepts/scheduling-eviction/taint-and-toleration/#taint-based-evictions)
on a node. Taints with the `NoExecute` effect force pods on the node to be evicted if the pod does
not tolerate the taint.

[Draining a node](tasks/administer-cluster/safely-drain-node/) also
causes pods to be evicted. When a cluster administrator drains a node, every pod running on the node
is methodically evicted. Unlike taints, node drains respect
[PodDisruptionBudgets](docs/concepts/workloads/pods/disruptions/#how-disruption-budgets-work) 
that are configured for an application.

## Resource-based Evictions

Pods can also be evicted due to resource availability in the cluster.
If there are not enough resources to assign a pod to a node, the scheduler will use
[Pod Priorities](docs/concepts/configuration/pod-priority-preemption/) to
determine which pods to evict. Pods with higher priority value and an appropriate `PriorityClass`
can evict lower priority pods.

If a node is exhausted of a compute resource, the `kubelet` may also evict pods to reclaim these
resources. The
[Quality of Service](docs/tasks/configure-pod-container/quality-service-pod/) 
class assigned to the pod is used to determine which pods should be evicted first, in addition to
the pod's priority. The `kubelet`'s
[out of resource handler](docs/tasks/administer-cluster/out-of-resource/)
evicts pods with `BestEffort` or `Burstable` class first if the pod's resource consumption exceeds
its request.

## Best Practices to Handle Evictions

- Configure
  [graceful termination](docs/concepts/workloads/pods/pod/#termination-of-pods)
  for your containers.
- Set appropriate pod [resources](docs/concepts/configuration/manage-resources-containers/).
- Use `PriorityClasses` to prioritize your workloads
- Configure `PodDisruptionBudgets` for your applications.

## {{% heading "whatsnext" %}}

- Read more about [Pod Disruption Budgets](docs/concepts/workloads/pods/disruptions/) and how to configure them for your application
- Understand how [taints and tolerations](docs/concepts/scheduling-eviction/taint-and-toleration/) impact where your pods are scheduled.
- Learn how to [safely drain a node](docs/tasks/administer-cluster/safely-drain-node/).
- Read more about the `kubelet`'s [out of resource handler](docs/tasks/administer-cluster/out-of-resource/).

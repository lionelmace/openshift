---

copyright:
  years: 2022, 2022
lastupdated: "2022-11-21"

keywords: openshift, ingress, troubleshoot ingress, ingress operator, ingress cluster operator, ingress operator degraded, erriodeg

subcollection: openshift
content-type: troubleshoot

---

{{site.data.keyword.attribute-definition-list}}


# Why is the Ingress Operator in a degraded state?
{: #ts-ingress-erriodeg}
{: troubleshoot}
{: support}


Supported infrastructure providers
:   Classic
:   VPC
:   {{site.data.keyword.satelliteshort}}

You can use the the `ibmcloud oc ingress status-report ignored-errors add` command to add an error to the ignored-errors list. Ignored errors still appear in the output of the `ibmcloud oc ingress status-report get` command, but are ignored when calculating the overall Ingress Status.
{: tip}

When you check the status of your cluster's Ingress components by running the `ibmcloud oc ingress status-report get` command, you see an error similar to the following.
{: tsSymptoms}

```sh
The Ingress Operator is in a degraded state (ERRIODEG).
```
{: screen}

The Ingress Operator checks the health of the Ingress Controllers and enters a degraded state when the checks fail.
{: tsCauses}


Get the details of the `ingress` ClusterOperator and complete the steps based on the error message.
{: tsResolve}


Check the status of the `ingress` ClusterOperator. If you see `False` in the `DEGRADED` column, wait 10 to 15 minutes to see if the Ingress Status warning disappears. If not, proceed with the troubleshooting steps based on the message in the `MESSAGE` column.
```sh
oc get clusteroperator ingress
```
{: pre}


## One or more status conditions indicate unavailable: `DeploymentAvailable=False`
{: #ts-ingress-erriodeg-da-false}

1. Ensure that you cluster has at least two workers. For more information, see [Adding worker nodes and zones to clusters](/docs/openshift?topic=openshift-add_workers).
1. Ensure that your cluster workers are healthy, otherwise Ingress Controller pods cannot be scheduled. For more information, see [Worker node states](/docs/openshift?topic=openshift-worker-node-state-reference).

## One or more status conditions indicate unavailable: `LoadBalancerReady=False`
{: #ts-ingress-erriodeg-lbr-false}

1. **VPC only**: Ensure that you did not reach your LBaaS instance quota. For more information, see [Quotas and service limits](/docs/vpc?topic=vpc-quotas#alb-quotas) and the `ibmcloud is load-balancers` [command](/docs/vpc?topic=vpc-infrastructure-cli-plugin-vpc-reference#load-balancers).
1. Ensure that your cluster masters are healthy. For more information, see [Reviewing master health](/docs/openshift?topic=openshift-debug_master#review-master-health).
1. Refresh your cluster masters by running the `ibmcloud oc cluster master refresh` [command](/docs/openshift?topic=openshift-kubernetes-service-cli#cs_apiserver_refresh).

## One or more other status conditions indicate a degraded state: `CanaryChecksSucceeding=False`
{: #ts-ingress-erriodeg-ccs-false}

1. Ensure that the correct LoadBalancer service address is registered for your Ingress subdomain.
    1. Run the `ibmcloud oc cluster get` [command](/docs/openshift?topic=openshift-kubernetes-service-cli#cs_cluster_get) to see your Ingress subdomain.
    1. Run the `ibmcloud oc nlb-dns get` [command](/docs/openshift?topic=openshift-kubernetes-service-cli#cs_nlb-dns-get) to see the the registered addresses.
    1. Run the `oc get services -n openshift-ingress` command to get the actual load balancer addresses.
    1. Compare the registered and actual addresses and update the subdomain if it differs.
        **VPC**: Run the `ibmcloud oc nlb-dns replace` [command](/docs/openshift?topic=openshift-kubernetes-service-cli#cs_nlb-dns-replace) to replace the current address.
        **Classic**: Remove the currently registered addresses by running the `ibmcloud oc nlb-dns rm classic` [command](/docs/openshift?topic=openshift-kubernetes-service-cli#cs_nlb-dns-rm), then add the new addresses with the `ibmcloud oc nlb-dns add` [command](/docs/openshift?topic=openshift-kubernetes-service-cli#cs_nlb-dns-add).
        
1. Ensure that no firewall rules block the canary traffic.
    **VPC**: canary traffic originates from one of the worker nodes, flows through a VPC Public Gateway and arrives to the public side of the VPC Load Balancer instance. Configure your VPC Security Groups to allow this communication. For more information, see [Controlling traffic with VPC security groups](/docs/openshift?topic=openshift-vpc-security-group).
    **Classic**: canary traffic originates from the public IP address of one of the worker nodes and arrives to the public IP address of your classic load balancers. Configure your network policies to allow this communication. For more information, see [Controlling traffic with network policies on classic clusters](/docs/openshift?topic=openshift-network_policies).

## Next steps
{: #ts-ingress-erriodeg-next}

1. Wait 30 minutes, then run the `oc get clusteroperator ingress` command and check the `MESSAGE` column again.
1. If you see a different error message repeat the troubleshooting steps.
1. If the issue persists, contact support. Open a [support case](/docs/get-support?topic=get-support-using-avatar). In the case details, be sure to include any relevant log files, error messages, or command outputs.




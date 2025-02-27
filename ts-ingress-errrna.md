---

copyright:
  years: 2022, 2022
lastupdated: "2022-11-21"

keywords: openshift, ingress, troubleshoot ingress, ingress operator, ingress cluster operator, missing ip addresses, errrna

subcollection: openshift
content-type: troubleshoot

---

{{site.data.keyword.attribute-definition-list}}


# Why does the Ingress status show an ERRRNA error?
{: #ts-ingress-errrna}
{: troubleshoot}
{: support}

Supported infrastructure providers
:   Classic
:   VPC
:   {{site.data.keyword.satelliteshort}}

You can use the the `ibmcloud oc ingress status-report ignored-errors add` command to add an error to the ignored-errors list. Ignored errors still appear in the output of the `ibmcloud oc ingress status-report get` command, but are ignored when calculating the overall Ingress Status.
{: tip}

When you check the status of your cluster's Ingress components by running the `ibmcloud oc ingress status-report get` command, you see an error similar to the following example.
{: tsSymptoms}

```sh
One or more route not admitted (ERRRNA).
```
{: screen}


The Ingress Controller watches Routes that are created on the cluster and marks them as `admitted` when the Ingress Controller configuration is adjusted. If a Route resource is not marked as `admitted`, it means that no Ingress Controllers has processed it, therefore the configuration is ineffective.
{: tsCauses}

Review the configuration of Routes resources that have not been marked as `admitted`.
{: tsResolve}

1. Ensure that your cluster masters and workers are healthy.
    - [Review master health](/docs/openshift?topic=openshift-debug_master#review-master-health).
    - [Review worker node states](/docs/openshift?topic=openshift-worker-node-state-reference).
    
1. Fetch your Route resources and review their `Status`.
    ```sh
    oc get routes -A
    ```
    {: pre}
    
1. Identify `Routes` that do not look similar to the following contents in their `Status` field:
    ```yaml
      status:
        ingress:
        - conditions:
          - status: "True"
            type: Admitted
    ```
    {: codeblock}
    
1. Review the configuration of Route resources that are not admitted:
    - Ensure the Route configuration is correct. See [Route configuration](https://docs.openshift.com/container-platform/4.11/networking/routes/route-configuration.html){: external}.
    - If you configured [Ingress Controller sharding](https://docs.openshift.com/container-platform/4.11/networking/ingress-operator.html#nw-ingress-sharding_configuring-ingress){: external}, ensure that the labels on the Route resource or on the namespace containing the Route resource are correct.
1. Wait 10 to 15 minutes, then check your Routes again by running the `oc get routes -A` command.
1. If you see a different error, repeat the troubleshooting steps. If the issue persists, contact support. Open a [support case](/docs/get-support?topic=get-support-using-avatar). In the case details, be sure to include any relevant log files, error messages, or command outputs.



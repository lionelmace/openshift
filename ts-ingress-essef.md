---

copyright: 
  years: 2022, 2022
lastupdated: "2022-11-29"

keywords: openshift, essef

subcollection: openshift

content-type: troubleshoot

---

{{site.data.keyword.attribute-definition-list}}


# Why does the Ingress status show an ESSEF error?
{: #ts-ingress-essef}
{: troubleshoot}
{: support}

Supported infrastructure providers
:   Classic
:   VPC
:   {{site.data.keyword.satelliteshort}}

When you check the status of your cluster's Ingress components by running the `ibmcloud oc ingress status-report get` command, you see an error similar to the following example.
{: tsSymptoms}

```sh
Field for opaque secret expired or will expire soon (ESSEF).
```
{: screen}

You have secrets in your cluster that have fields which are either expired or are about to expire in the next 5 days.
{: tsCauses}


Review the secrets in your cluster and update or remove them.
{: tsResolve}

1. List your secrets.
    ```sh
    ibmcloud oc ingress secret ls
    ```
    {: pre}

1. Complete the steps depending on whether the secret fields are still needed or not. If the expiring secret field in the secret is still needed, ensure the corresponding secret in the {{site.data.keyword.secrets-manager_full_notm}} instance has been updated and has a new expiry date. Then, update the secret field with the new values from {{site.data.keyword.secrets-manager_full_notm}} by running the following command.

    To view all the secrets in your {{site.data.keyword.secrets-manager_full_notm}} instance, see [Accessing Secrets](/docs/secrets-manager?topic=secrets-manager-access-secrets&interface=ui#get-secret-value-ui).
    {: tip}

    ```sh
    ibmcloud oc ingress secret update
    ```
    {: pre}

1. If the secret in the secret field is expiring because it is no longer needed, run the **`ibmcloud oc ingress secret field rm`** command to remove it.

1. If the issue persists, contact support. Open a [support case](/docs/get-support?topic=get-support-using-avatar). In the case details, be sure to include any relevant log files, error messages, or command outputs.




# Deploy TRAIN and Anchor rTAO in DNS

## Overview

To enable DNS-based verification of root authorities in your trust ecosystem, TRAIN relies on a component called the **TDZM (Trust-DNS Zone Manager)**. This component manages DNS zones where **Root Trusted Accreditation Organisations (rTAOs)** can anchor their DIDs using TXT or TLSA records.

This page explains how to **deploy TDZM**, anchor your **rTAO**, and configure the environment to support trust chain resolution using DNS.

***

### Why Anchor Your rTAO in DNS?

Anchoring your rTAO in DNS enhances trustworthiness and auditability by:

* Providing **cryptographic proof** of domain control
* Allowing trust validators (like TRAIN) to verify the root of the trust chain against **public DNS records**
* Increasing assurance in ecosystems where DNS-based trust is required (e.g., public sector, federated networks)

***

### Deployment Options

You can deploy TDZM in two ways:

1. **Locally** using Docker Compose
2. **In a Kubernetes Cluster** using Helm charts

***

### Option 1: Run Locally with Docker Compose

This is the easiest way to run TDZM for development and testing.

#### Steps:

1. Navigate to the `deploy/local` directory in the TDZM repository.
2. Run the deployment script:

```bash
bashCopyEdit./deploy.sh build
```

> The `build` flag is optional but recommended to ensure you’re running the latest version.

This will launch:

* A **Keycloak** instance for authentication
* The **TDZM backend**
* The **TDZM UI**

#### Local Access

* TDZM Backend: [http://localhost:16001](http://localhost:16001)
* TDZM UI: [http://localhost:8001/ui](http://localhost:8001/ui)

***

### Option 2: Deploy in Kubernetes (Production)

TDZM can be deployed in a Kubernetes cluster using **Helm charts** for both the backend and UI.

#### Prerequisites

* Kubernetes cluster with **Nginx Ingress Controller**
* TLS certificates available as Kubernetes secrets\
  &#xNAN;_&#x4F;R_\
  A **Let’s Encrypt cert issuer** setup via Ingress

***

#### Configure DNS Delegation

Before TDZM can manage your trust zone, you must configure your DNS provider:

1.  **NS Record** in the parent zone pointing to your TDZM instance\
    Example:

    ```
    CopyEdittrust.federation1.com. NS ns1.trust.federation1.com.
    ```
2.  **A Record** resolving the nameserver to your TDZM server’s IP\
    Example:

    ```
    cssCopyEditns1.trust.federation1.com. A 192.0.2.4
    ```

These records must be added in the **parent zone** (e.g., `federation1.com`) for your trust zone to be valid (`trust.federation1.com`).

***

#### Helm Configuration: TDZM Backend

When deploying the backend, set the following values in your Helm `values.yaml` file:

| **Property**                             | **Description**                       | **Example**                    |
| ---------------------------------------- | ------------------------------------- | ------------------------------ |
| `zoneConfig.TF_DOMAIN_NAME`              | DNS zone managed by TDZM              | `trust.federation1.com`        |
| `zoneConfig.TF_DOMAIN_IP`                | IP address for the NS server          | `192.0.2.4`                    |
| `zoneConfig.PRIMARY_SERVER_NSD`          | Domain of the primary nameserver      | `ns1.trust.federation1.com`    |
| `zoneConfig.PRIMARY_SERVER_IP`           | IP of the primary nameserver          | `192.0.2.4`                    |
| `authConfigFileContent.ISSUER_URL`       | OIDC issuer for JWT validation        | `https://auth.federation1.com` |
| `authConfigFileContent.CLIENT_ID`        | Allowed client ID                     | `tzdm-client`                  |
| `authConfigFileContent.ALLOW_UNSAFE_SSL` | Skip SSL validation (not recommended) | `false`                        |

_Note: TDZM requires a shared volume (`ReadWriteMany`) for pod scaling. Ensure your cluster supports this (e.g., via NFS)._

***

#### Helm Configuration: TDZM UI

UI configuration options:

| **Property**         | **Description**  | **Example**                                       |
| -------------------- | ---------------- | ------------------------------------------------- |
| `zonemgr_url`        | Backend URL      | `http://tdzm-backend.namespace.svc.cluster.local` |
| `oidc_issuer_url`    | OIDC server URL  | `https://auth.federation1.com`                    |
| `oidc_client_id`     | OIDC client ID   | `tzdm-ui-client`                                  |
| `oidc_client_secret` | Client secret    | `super-secret-value`                              |
| `ui_host`            | Public UI domain | `ui.trust.federation1.com`                        |
| `app_base_url_path`  | Must be `/ui`    | `/ui`                                             |

***

### Next Step: Anchor Your rTAO DID

Once TDZM is running and DNS is delegated:

1. Add a **TXT record** under your trust zone with the content linking your rTAO DID to the domain.
2. Example DNS entry:

```
arduinoCopyEdit_did.trust.federation1.com. TXT "did:cheqd:mainnet:rtao123"
```

This DNS record will allow TRAIN to resolve and validate the rTAO’s DID during trust chain verification.

***

### Summary

| Component        | Description                                               |
| ---------------- | --------------------------------------------------------- |
| **TDZM Backend** | Manages DNS records and trust zones                       |
| **TDZM UI**      | Provides web interface to manage delegations              |
| **TRAIN**        | Uses TDZM-managed DNS records to validate rTAO identities |
| **DNS Provider** | Must delegate NS and A records to the TDZM zone manager   |

***

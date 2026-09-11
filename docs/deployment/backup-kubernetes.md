# Back up and restore Kubernetes

PowerProtect Data Manager enables you to protect the Kubernetes environment by adding a Kubernetes cluster as an asset source and discovering namespaces as assets for data protection operations.

This tutorial describes how to add a Kubernetes cluster as an asset source and how to back up and restore the Kubernetes cluster on PowerProtect Data Manager.

- [Log in](#log-in)
- [Add a PowerProtect Data Domain](#add-a-powerprotect-data-domain)
- [Add a Kubernetes cluster](#add-a-kubernetes-cluster)
- [Discover the Kubernetes cluster](#discover-the-kubernetes-cluster)
- [List assets of the Kubernetes cluster](#list-assets-of-the-kubernetes-cluster)
- [Create a protection policy](#create-a-protection-policy)
- [Assign assets](#assign-assets)
- [Trigger protection policy backup](#trigger-protection-policy-backup)
- [Restore Kubernetes cluster assets](#restore-kubernetes-cluster-assets)


## Log in
Use the login API call to retrieve the access token. See **Authentication and Authorization** for more information.

## Add a PowerProtect Data Domain
Add a PowerProtect Data Domain as described in **Add a PowerProtect Data Domain**.

## Add a Kubernetes cluster

### Step 1 - Enable Kubernetes cluster

Use the following API to enable Kubernetes cluster as an asset source.

```json
curl --request PUT 'https://<your-server>:8443/api/v2/common-settings/ASSET_SETTING' \
    --header 'Authorization: Bearer <access_token>' \
    --header 'Content-Type: application/json' \
    --data-raw '{
        "id": "ASSET_SETTING",
        "properties": [
            {
                "name": "enabledAssetTypes",
                "type": "LIST",
                "value": "KUBERNETES"
            }
        ]
    }'
```

The response is similar to this:

```json
{
    "id": "ASSET_SETTING",
    "properties": [
        {
            "name": "enabledAssetTypes",
            "value": " KUBERNETES",
            "type": "LIST"
        }
    ]
}
```

### Step 2 - Get the admin token from the Kubernetes cluster

Log in to the Kubernetes cluster, and run this command: 

```
kubectl -n kube-system describe secret $(kubectl -n kube-system get secret | grep admin-user | awk '{print $1}')
```

The response is similar to this:

```
Name:         admin-user-token-vchdd
Namespace:    kube-system
Labels:       <none>
Annotations:  kubernetes.io/service-account.name: admin-user
              kubernetes.io/service-account.uid: 8077808b-bd4c-4ac0-b136-4731a2e65cb1

Type:  kubernetes.io/service-account-token

Data
====
ca.crt:     1025 bytes
namespace:  11 bytes
token:      <token>
```

* The value of ```token``` is used for ```password``` and ```token``` in the request body of the following API call.

### Step 3 - Get the Kubernetes cluster certificate

Use the following API to retrieve the Kubernetes cluster certificate.

```json
curl --request GET 'https://<your-server>:8443/api/v2/certificates?type=Root&host=10.198.17.213&port=6443' \
    --header 'Content-Type: application/json' \
    --header 'Accept: application/json' \
    --header 'Authorization: Bearer <access_token>'
```

The response is similar to this:

```json
[
    {
        "id": "MTAuMTk4LjE3LjIxMzo2NDQzOmhvc3Q=",
        "host": "10.198.17.213",
        "port": "6443",
        "notValidBefore": "Tue Oct 26 23:59:33 PDT 2021",
        "notValidAfter": "Wed Oct 26 23:59:33 PDT 2022",
        "fingerprint": "0BC7D2EC4D72F44AF4F5A822EE2925A27B9B56AB",
        "subjectName": "CN=kube-apiserver",
        "issuerName": "CN=kubernetes",
        "state": "UNKNOWN",
        "type": "HOST"
    }
]
```

### Step 4 - Trust the Kubernetes cluster certificate

Use the following API to trust the Kubernetes certificate.

```json
curl --request PUT 'https://<your-server>:8443/api/v2/certificates/MTAuMTk4LjE3LjIxMzo2NDQzOmhvc3Q=' \
    --header 'Content-Type: application/json' \
    --header 'Accept: application/json' \
    --header 'Authorization: Bearer <access_token>' \
    --data-raw '{
        "id": "MTAuMTk4LjE3LjIxMzo2NDQzOmhvc3Q=",
        "host": "10.198.17.213",
        "port": "6443",
        "notValidBefore": "Tue Oct 26 23:59:33 PDT 2021",
        "notValidAfter": "Wed Oct 26 23:59:33 PDT 2022",
        "fingerprint": "0BC7D2EC4D72F44AF4F5A822EE2925A27B9B56AB",
        "subjectName": "CN=kube-apiserver",
        "issuerName": "CN=kubernetes",
        "state": "ACCEPTED",
        "type": "HOST"
    }'
```

* The value of the ```state``` in the request body is set to ```ACCEPTED```.

The response is similar to this:

```json
{
    "id": "MTAuMTk4LjE3LjIxMzo2NDQzOmhvc3Q=",
    "host": "10.198.17.213",
    "port": "6443",
    "notValidBefore": "Tue Oct 26 23:59:33 PDT 2021",
    "notValidAfter": "Wed Oct 26 23:59:33 PDT 2022",
    "fingerprint": "0BC7D2EC4D72F44AF4F5A822EE2925A27B9B56AB",
    "subjectName": "CN=kube-apiserver",
    "issuerName": "CN=kubernetes",
    "state": "ACCEPTED",
    "type": "HOST"
}
```

### Step 5 - Create credentials for the Kubernetes cluster

Use the following API to create the credentials for the Kubernetes cluster.

```json
curl --request POST 'https://<your-server>/api/v2/credentials' \
    --header 'Authorization: Bearer <access_token>' \
    --header 'Content-Type: application/json' \
    --data-raw '{
        "name": "k8s-api",
        "username": "",
        "password": "<token>",
        "method": "TOKEN",
        "type": "KUBERNETES",
        "token": "<token>"
    }'
```

The response is similar to this:

```json
{
    "id": "496a0561-6377-449c-8a55-e7942c0b12d3",
    "name": "k8s-api",
    "username": "",
    "password": null,
    "type": "KUBERNETES",
    "method": "TOKEN",
    "secretId": "1ed936ca-7175-4aee-acde-e57ea16fdb98",
    "internal": false,
    "consumersCount": 0
}
```

### Step 6 - Add Kubernetes cluster as an asset source

Use the following API to add the Kubernetes cluster as an asset source.

```json
curl --request POST 'https://<your-server>/api/v2/inventory-sources' \
    --header 'Authorization: Bearer <access_token>' \
    --header 'Content-Type: application/json' \
    --data-raw '{
        "name": "k8s",
        "type": "KUBERNETES",
        "address": "10.198.17.213",
        "port": 6443,
        "credentials": {
            "id": "496a0561-6377-449c-8a55-e7942c0b12d3"
        }
    }'
```

The response is similar to this:

```json
{
    "page": {
        "size": 1,
        "number": 1,
        "totalPages": 1,
        "totalElements": 1
    },
    "content": [
        {
            "id": "81214161-9eaa-4e71-a12c-dba2d5d728b8",
            "name": "k8s",
            "version": "1.18",
            "type": "KUBERNETES",
            ...
        }
    ]
}
```

The following subsections describe special use cases for adding a Kubernetes cluster.

#### Enabling protection when the CSI driver is installed as a process

In environments where the vSphere CSI driver is installed as a process, the CSI secret, which is required for vCenter connection, is not present. The following example shows how to enable protection in this scenario. For more information about this use case, see the *PowerProtect Data Manager Kubernetes User Guide*.  

In the following example, set the `distributionType` in the POST request to VANILLA_ON_VSPHERE, as shown. 

Set the `vCenterId` to the ID of a Kubernetes vCenter Server that is already created (see the tutorial **How to add a vCenter**) and discovered (see **Discover new assets**). Use a GET request (`GET /api/v2/inventory-sources`) to retrieve the Kubernetes vCenter ID and paste it into `vCenterId` in the POST request below. Then submit the request. Similarly, you can also update an existing Kubernetes cluster with a PUT request.

```json
curl --request POST 'https://<your-server>/api/v2/inventory-sources' \
    --header 'Authorization: Bearer <access_token>' \
    --header 'Content-Type: application/json' \
    --data-raw '{
        "name": "k8s",
        "type": "KUBERNETES",
        "address": "10.198.17.213",
        "port": 6443,
        "credentials": {
            "id": "496a0561-6377-449c-8a55-e7942c0b12d3"
        },
        "details": {
            "k8s": {
                "distributionType": "VANILLA_ON_VSPHERE",
                "vCenterId": "b8bf8c3b-0d9d-4088-bcd8-b1b9357eadb2"
            }      
        }
    }'
```

During discovery of the Kubernetes cluster, CNDM creates a configmap in the PowerProtect namespace along with the `distributionType` and the vCenter credentials information. The Powerprotect Controller detects the `distributionType` and CSI secret from the configmap, enabling the controller to access the vCenter. The Kubernetes Controller creates a configmap in the Velero namespace for the Velero plugin to access the vCenter.

#### Updating PowerProtect Data Manager pod configurations

This example describes how to update the pods that PowerProtect Data Manager deploys by applying changed or additional attributes using a YAML file. For more information about this use case, see the _PowerProtect Data Manager Kubernetes User Guide_.  

The three PowerProtect Data Manager pods are for the PowerProtect Controller, Velero, and cProxy configurations. You specify information about the pods in a `configurations` attribute within the API request. The three parameters to configure are `type`, `key`, and `value`, as shown:

```json
curl --request POST 'https://<your-server>/api/v2/inventory-sources' \
    --header 'Authorization: Bearer <access_token>' \
    --header 'Content-Type: application/json' \
    --data-raw '{
        "name": "k8s",
        "type": "KUBERNETES",
        "address": "10.198.17.213",
        "port": 6443,
        "credentials": {
            "id": "496a0561-6377-449c-8a55-e7942c0b12d3"
        },
        "details": {
            "k8s": {
                "configurations": [
                    {
                        "type": "POD_CONFIG",
                        "key": "POWERPROTECT_CONTROLLER",
                        "value": "cHJvdGVjdG1vbjoKICByZXNvdXJjZXM6CiAgICBwb2RDb25maWc6CiAgICAgIC0KICAgICBzcGVjOg=="
                    }, {
                        "type": "POD_CONFIG",
                        "key": "VELERO",
                        "value": "cHJvdGVjdG1vbjoKICByZXNvdXJjZXM6CiAgICBwb2RDb25maWc6CiAgICAgIC0KICAgICBzcGVjOg=="
                    }, {
                        "type": "POD_CONFIG",
                        "key": "CPROXY",
                        "value": "cHJvdGVjdG1vbjoKICByZXNvdXJjZXM6CiAgICBwb2RDb25maWc6CiAgICAgIC0KICAgICBzcGVjOg=="
                    }
                ]      
            }
        }      
    }'
```

To obtain the `value` parameter, create a YAML file containing the configuration changes for the PowerProtect Data Manager pods. For example:

```
---
metadata:
  annotations:
    k8s.v1.cni.cncf.io/networks: macvlan-conf
spec:
  template:
    spec:
      dnsConfig:
        nameservers:
          - "10.235.95.21"
        searches:
          - vmw.asl.scm.com
          - vmw.asl.scm.com.cluster.local
          - cluster.local
```

The YAML file provides the updated configuration data to the pods through the `dnsConfig`, `nameservers`, and `searches` attributes.

To encode this data into Base64 format, paste the YAML file contents into any Base64 encoder. Then paste the encoded string into the `value` parameter for each pod in the `configurations` attribute above, and submit the POST request. Similarly, you can update `inventory-sources` with a PUT request.

#### Configuring internal registry per asset source

This example describes how to configure a Docker registry for each Kubernetes cluster. For more information about this use case, see the _PowerProtect Data Manager Kubernetes User Guide_.

To configure the registry, add a configuration to the Kubernetes `configurations` attribute with `type` set to CONTROLLER_CONFIG and `key` set to k8s.docker.registry. Provide an appropriate `value`, and then submit the request. Similarly, you can update an existing registry with a PUT request.

See the POST example below:

```json
curl --request POST 'https://<your-server>/api/v2/inventory-sources' \
    --header 'Authorization: Bearer <access_token>' \
    --header 'Content-Type: application/json' \
    --data-raw '{
        "name": "k8s",
        "type": "KUBERNETES",
        "address": "10.198.17.213",
        "port": 6443,
        "credentials": {
            "id": "496a0561-6377-449c-8a55-e7942c0b12d3"
        },
        "details": {
            "k8s": {
                "configurations": [{
                    "type": "CONTROLLER_CONFIG",
                    "key": "k8s.docker.registry",
                    "value": "ap-sc.drm.ops.scm.com:8446"
                }]      
            }
        }      
    }'
```

#### Disabling the CBT autoenable setting

This example describes how to disable the autoenable setting for Changed Block Tracking (CBT). This task is done by sending a POST request using the same `configurations` attribute shown in previous examples.

In this case, the `key` must be set to k8s.ppdm.autoenable.cbt. To disable CBT, set the `value` to false, as shown. Then submit the POST request. Similarly, you can update this configuration with a PUT request, for example, to re-enable the setting (with `value' set to true).

```json
curl --request POST 'https://<your-server>/api/v2/inventory-sources' \
    --header 'Authorization: Bearer <access_token>' \
    --header 'Content-Type: application/json' \
    --data-raw '{
        "name": "k8s",
        "type": "KUBERNETES",
        "address": "10.198.17.213",
        "port": 6443,
        "credentials": {
            "id": "496a0561-6377-449c-8a55-e7942c0b12d3"
        },
        "details": {
            "k8s": {
                "configurations": [{
                    "type": "CONTROLLER_CONFIG",
                    "key": "k8s.ppdm.autoenable.cbt",
                    "value": "false"
                }]      
            }
        }      
    }'
```

## Discover the Kubernetes cluster
After you add the Kubernetes cluster with PowerProtect Data Manager, the Kubernetes cluster appears in the Asset Sources window. Then you can select the Kubernetes cluster, perform discovery, and modify the Kubernetes cluster credentials.

Use the following API to discover the Kubernetes cluster.

```json
curl --request POST 'https://<your-server>:8443/api/v2/discoveries' \
    --header 'Content-Type: application/json' \
    --header 'Accept: application/json' \
    --header 'Authorization: Bearer <access-token>' \
    --data '{
        "start": "/inventory-sources/81214161-9eaa-4e71-a12c-dba2d5d728b8",
        "level": "DataCopies"
    }'
```

The response is similar to:

```json
{
    "id": "d5e06f5c-570e-4b5f-bce8-e61b25fd9528",
    "start": "/inventory-sources/81214161-9eaa-4e71-a12c-dba2d5d728b8",
    "level": "DataCopies",
    "taskId": "70a5dff2-36a5-4cd7-b944-85dc8f6b4d0d"
}
```

## List assets of the Kubernetes cluster

Use API call ```GET /api/v2/assets``` to retrieves the assets of the Kubernetes cluster. 

```json
curl --request GET 'https://<your-server>:8443/api/v2/assets?filter=type eq "KUBERNETES"' \
    --header 'Content-Type: application/json' \
    --header 'Authorization: Bearer <access-token>'
```

The response is similar to:

```json
{
    "page": {
        "size": 7,
        "number": 1,
        "totalPages": 1,
        "totalElements": 7
    },
    "content": [        
        {
            "id": "27151acf-6476-5a43-91ea-3ab30ce30b69",
            "name": "default",
            "status": "AVAILABLE",
            "type": "KUBERNETES",
            "size": 1073741824,
            "details": {
                "k8s": {
                    "uid": "1328b750-5cd5-42a0-957d-7327a36e37ac",
                    "inventorySourceId": "81214161-9eaa-4e71-a12c-dba2d5d728b8",
                    "subType": "K8S_NAMESPACE",
                    "size": 1073741824,
                    "externalCreatedAt": "2021-10-27T06:59:56Z",
                    "inventorySourceName": "k8s"
                }
            },
            ...
        },
        ...
    ]
}
```

## Create a protection policy
Create a protection policy to back up the Kubernetes cluster assets to the PowerProtect Data Domain. The API call is similar to this:

```json
curl --request POST 'https://<your-server>:8443/api/v3/protection-policies' \
  --header 'Content-Type: application/json' \
  --header 'Authorization: Bearer <access-token>' \
  --data-raw '{
    "name": "protect-k8s",
    "description": "test k8s protection",
    "purpose": "CENTRALIZED",
    "assetType": "KUBERNETES",
    "disabled": false,
    "objectives": [
        {
            "id": "{{$guid}}",
            "type": "BACKUP",
            "operations": [
                {
                    "id": "{{$guid}}",
                    "backupLevel": "SYNTHETIC_FULL",
                    "schedule": {
                        "recurrence": {
                            "pattern": {
                                "type": "HOURLY",
                                "interval": 12
                            }
                        },
                        "window": {
                            "startTime": "2024-03-06T12:00:00.000Z",
                            "duration": "PT10H"
                        }
                    }
                }
            ],
            "target": {
                "storageContainerId": "{{your-storagesystem-id}}"
            },
            "retentions": [
                {
                    "id": "{{$guid}}",
                    "time": [
                        {
                            "unitValue": 5,
                            "unitType": "DAY",
                            "type": "RETENTION"
                        }
                    ]
                }
            ],
            "config": {
                "dataConsistency": "CRASH_CONSISTENT"
            }
        }
    ]
}'
```

The response is similar to:

```json
{
    "id": "fa201708-3644-4177-9a14-d2a44cd02249",
    "name": "protect-k8s",
    "description": "test k8s protection",
    "assetType": "KUBERNETES",
    ...
}
```

## Assign assets
With the protection policy created, assign assets to it by using this call:

```
POST /api/v2/protection-policies/{id}/asset-assignments
```

In this POST example, the ```{id}``` is the protection policy ID that you obtained from the create protection policy call. The POST body looks similar to this example:

```json
["27151acf-6476-5a43-91ea-3ab30ce30b69"]
```

* The ```ID``` in request body is the ```id``` that you retrieved from the previous section, *List assets of the Kubernetes cluster*.

The response is similar to:

```json
204 No Content
```

## Trigger protection policy backup

### Step 1 - Manually trigger protection policy backup

Use the following API to manually trigger the protection policy backup.

```
POST /api/v3/protections 
```

The request body is similar to:

```json
{
    "source": {
        "assetIds": ["27151acf-6476-5a43-91ea-3ab30ce30b69"]
    },
    "policy": {
        "id": "fa201708-3644-4177-9a14-d2a44cd02249",
        "objectives": [
            {
                "id": "3f61f1f4-82b9-4751-8437-de7bc73ff542",
                "operation": {
                    "backupLevel": "SYNTHETIC_FULL"
                },
                "retentions": [
                    {
                        "time": [
                            {
                                "type": "RETENTION",
                                "unitValue": 5,
                                "unitType": "DAY"
                            }
                        ]
                    }
                ]
            }
        ]
    }
}
```

* Get the protection policy backup objective and assigned assets from the API calls ```GET /api/v3/protection-policies/{id}``` and ```GET /api/v2/protection-policies/{id}/asset-assignments```. 

The response is similar to:

```json
{
    "results": [
        {
            "status": "202",
            "objectiveId": "3f61f1f4-82b9-4751-8437-de7bc73ff542",
            "activityId": "5ae39752-73bd-4d34-9474-426200df2348",
            "reason": null,
            "remediation": null
        }
    ]
}
```

### Step 2 - Monitor backup activity

Use the following API to monitor the activities of the protection policy backup.

```json
curl --request GET 'https://<your-server>:8443/api/v2/activities/{activityId} ' \
    --header 'Content-Type: application/json' \
    --header 'Authorization: Bearer <access-token>'
```

The response is similar to:

```json
{
    "id": "5d56c85e-cf54-4f32-97c1-6ca701402e26",
    "name": "Manually Protecting Kubernetes - protect-k8s - PROTECTION - Synthetic Full",
    "category": "PROTECT",
    "state": "COMPLETED",
    "result": {
        "status": "OK",
        "summaries": [],
        "bytesTransferred": 39042
    },
    ...
}
```

## Restore Kubernetes cluster assets

### Step 1 - View asset copies

Use the following API to verify the asset copies.

```json
curl --request POST 'https://<your-server>:8443/api/v3/copies-search' \
  --header 'Content-Type: application/json' \
  --header 'Authorization: Bearer <access-token>' \
  --data-raw '{
    "filter": "assetRef.id eq \"27151acf-6476-5a43-91ea-3ab30ce30b69\""
  }'
```

The response is similar to:

```json
{
  "page": {
    "number": 1,
    "size": 100,
    "totalElements": 1,
    "totalPages": 1,
    "maxPageableElements": 1
  },
  "content": [
    {
      "id": "b75f80d1-505f-5d30-bdeb-bab26dfda1c5",
      "assetType": "KUBERNETES",
      "sizeInBytes": 53739520,
      "backupTime": "2025-07-09T08:59:11Z",
      "assetRef": {
        "id": "27151acf-6476-5a43-91ea-3ab30ce30b69",
        "name": "default"
      },
      ...
    }
  ]
}
```

* The ```id``` is the ```copyIds``` that is used in the request body of API call ```/api/v2/restored-copies```.

### Step 2 - Restore copies

Use the following API to restore asset copies to the Kubernetes cluster.

```json
curl --request POST 'https://<your-server>:8443/api/v2/restored-copies' \
    --header 'Content-Type: application/json' \
    --header 'Authorization: Bearer <access-token>' \
    --data-raw '{
        "description": "Restore kubernetes copies",
        "restoreType": "TO_EXISTING",
        "copyIds": [
            "b75f80d1-505f-5d30-bdeb-bab26dfda1c5"
        ],
        "restoredCopiesDetails": {
            "targetK8sInfo": {
                "namespace": "kube-public",
                "skipNamespaceResources": false,
                "targetInventorySourceId": "77589377-3588-4df4-8535-a788babdb446",
                "persistentVolumeClaims": [
                    {
                        "name": "csi-pvc"
                    }
                ],
                "overwritePersistentVolumeClaim": true
            }
        }
    }'
```

The response is similar to:

```json
{
    "id": "fee5f780-eb9b-4a35-812e-1e2895796336",
    "description": "Restore kubernetes copies",
    "state": "WAITING",
    "status": "UNKNOWN",
    "restoreType": "TO_EXISTING",
    "copyId": "b75f80d1-505f-5d30-bdeb-bab26dfda1c5",
    "copyIds": [
        "b75f80d1-505f-5d30-bdeb-bab26dfda1c5"
    ],
    "startTime": "2021-10-28T02:48:49.509Z",    
    "activityId": "75255a9f-7991-47c0-92ef-d630504de7ee",
    ...
}
```

* The ```id``` is the restore activity ID that is used to query the activity detail information.

### Step 3 - Verify restore activity

Use the following API to verify the restore activity.

```json
curl --request GET 'https://<your-server>:8443/api/v2/activities/{activities_id}' \
    --header 'Content-Type: application/json' \
    --header 'Authorization: Bearer <access-token>'
```

The response is similar to:

```json
{
    "id": "fee5f780-eb9b-4a35-812e-1e2895796336",
    "name": "Restoring protection copies for Kubernetes namespaces",
    "category": "RESTORE",
    "subcategory": "TO_EXISTING",
    "parentId": "a6ac0cfa-a97c-42ee-b814-3762aebb9cd1",
    "classType": "JOB",
    "state": "COMPLETED",
    "result": {
        "status": "OK",
        "summaries": [],
        "bytesTransferred": 0
    },
    ...
}
```

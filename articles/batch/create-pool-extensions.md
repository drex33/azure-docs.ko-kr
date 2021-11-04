---
title: Batch 풀에서 확장 사용
description: 확장은 Batch 컴퓨팅 노드에서 프로비저닝 후 구성 및 설치를 용이하게 하는 소규모 애플리케이션입니다.
ms.topic: how-to
ms.date: 11/03/2021
ms.openlocfilehash: bac3c6c53552ac99aef6210cbe184da754580ca0
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131508687"
---
# <a name="use-extensions-with-batch-pools"></a>Batch 풀에서 확장 사용

확장은 Batch 컴퓨팅 노드에서 프로비저닝 후 구성 및 설치를 용이하게 하는 소규모 애플리케이션입니다. Azure Batch에서 허용하는 확장을 선택하고 프로비저닝되는 컴퓨팅 노드에 설치할 수 있습니다. 그런 다음 확장이 의도된 작업을 수행할 수 있습니다.

사용하는 확장의 라이브 상태를 확인하고 검색, 수정 또는 진단 기능을 실행하기 위해 확장이 반환하는 정보를 검색할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

- 확장이 포함된 풀은 [가상 머신 구성](nodes-and-pools.md#virtual-machine-configuration)을 사용해야 합니다.
- CustomScript 확장 유형은 Azure Batch 서비스에 예약되어 있으므로 재정의할 수 없습니다.
- 일부 확장은 제대로 기능하기 위해 컴퓨팅 노드의 컨텍스트에서 액세스할 수 있는 풀 수준 관리 ID가 필요할 수 있습니다. 확장에 해당하는 경우 [Batch 풀에서 관리 ID 구성](managed-identity-pools.md)을 참조하세요.

## <a name="supported-extensions"></a>지원되는 확장

Batch 풀을 만들 때 현재 다음 확장을 설치할 수 있습니다.

- [Linux 용 Azure Key Vault 확장](../virtual-machines/extensions/key-vault-linux.md)
- [Windows Azure Key Vault 확장](../virtual-machines/extensions/key-vault-windows.md)
- [Linux 용 Azure Monitor Logs analytics 및 모니터링 확장](../virtual-machines/extensions/oms-linux.md)
- [Windows에 대 한 Azure Monitor 로그 분석 및 모니터링 확장](../virtual-machines/extensions/oms-windows.md)
- [Azure Desired State Configuration (DSC) 확장](../virtual-machines/extensions/dsc-overview.md)
- [Windows vm에 대 한 Azure 진단 확장](../virtual-machines/windows/extensions-diagnostics.md)
- [AMD의 Windows에 대 한 HPC GPU 드라이버 확장](../virtual-machines/extensions/hpccompute-amd-gpu-windows.md)
- [NVIDIA의 Windows에 대 한 HPC GPU 드라이버 확장](../virtual-machines/extensions/hpccompute-gpu-windows.md)
- [Windows Microsoft Antimalware 확장](../virtual-machines/extensions/iaas-antimalware-windows.md)

지원 요청을 개설하여 추가 게시자 및/또는 확장 유형에 대한 지원을 요청할 수 있습니다.

## <a name="create-a-pool-with-extensions"></a>확장을 사용하여 풀 만들기

아래 예제에서는 Azure Key Vault 확장을 사용하는 Linux 노드의 Batch 풀을 만듭니다.

REST API URI

```http
 PUT https://management.azure.com/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.Batch/batchAccounts/<batchaccountName>/pools/<batchpoolName>?api-version=2021-01-01
```

요청 본문

```json
{
    "name": "test1",
    "type": "Microsoft.Batch/batchAccounts/pools",
    "properties": {
        "vmSize": "STANDARD_DS2_V2",
        "taskSchedulingPolicy": {
            "nodeFillType": "Pack"
        },
        "deploymentConfiguration": {
            "virtualMachineConfiguration": {
                "imageReference": {
                    "publisher": "canonical",
                    "offer": "ubuntuserver",
                    "sku": "18.04-lts",
                    "version": "latest"
                },
                "nodeAgentSkuId": "batch.node.ubuntu 18.04",
                "extensions": [
                    {
                        "name": "secretext",
                        "type": "KeyVaultForLinux",
                        "publisher": "Microsoft.Azure.KeyVault",
                        "typeHandlerVersion": "1.0",
                        "autoUpgradeMinorVersion": true,
                        "settings": {
                            "secretsManagementSettings": {
                                "pollingIntervalInS": "300",
                                "certificateStoreLocation": "/var/lib/waagent/Microsoft.Azure.KeyVault",
                                "requireInitialSync": true,
                                "observedCertificates": [
                                    "https://testkvwestus2.vault.azure.net/secrets/authsecreat"
                                ]
                            },
                            "authenticationSettings": {
                                "msiEndpoint": "http://169.254.169.254/metadata/identity",
                                "msiClientId": "885b1a3d-f13c-4030-afcf-9f05044d78dc"
                            }
                        },
                        "protectedSettings":{}
                    }
                ]
            }
        },
        "scaleSettings": {
            "fixedScale": {
                "targetDedicatedNodes": 1,
                "targetLowPriorityNodes": 0,
                "resizeTimeout": "PT15M"
            }
        }
```

## <a name="get-extension-data-from-a-pool"></a>풀에서 확장 데이터 가져오기

아래 예제에서는 Azure Key Vault 확장에서 데이터를 검색합니다.

REST API URI

```http
 GET https://<accountname>.<region>.batch.azure.com/pools/test3/nodes/tvmps_a3ce79db285d6c124399c5bd3f3cf308d652c89675d9f1f14bfc184476525278_d/extensions/secretext?api-version=2010-01-01
```

응답 본문

```json
{
  "odata.metadata":"https://testwestus2batch.westus2.batch.azure.com/$metadata#extensions/@Element","instanceView":{
    "name":"secretext","statuses":[
      {
        "code":"ProvisioningState/succeeded","level":0,"displayStatus":"Provisioning succeeded","message":"Successfully started Key Vault extension service. 2021-02-08T19:49:39Z"
      }
    ]
  },"vmExtension":{
    "name":"KVExtensions","publisher":"Microsoft.Azure.KeyVault","type":"KeyVaultForLinux","typeHandlerVersion":"1.0","autoUpgradeMinorVersion":true,"settings":"{\r\n  \"secretsManagementSettings\": {\r\n    \"pollingIntervalInS\": \"300\",\r\n    \"certificateStoreLocation\": \"/var/lib/waagent/Microsoft.Azure.KeyVault\",\r\n    \"requireInitialSync\": true,\r\n    \"observedCertificates\": [\r\n      \"https://testkvwestus2.vault.azure.net/secrets/testumi\"\r\n    ]\r\n  },\r\n  \"authenticationSettings\": {\r\n    \"msiEndpoint\": \"http://169.254.169.254/metadata/identity\",\r\n    \"msiClientId\": \"885b1a3d-f13c-4030-afcf-922f05044d78dc\"\r\n  }\r\n}"
  }
}

```

## <a name="next-steps"></a>다음 단계

- [풀 노드에 애플리케이션 및 데이터를 복사](batch-applications-to-pool-nodes.md)하는 다양한 방법을 알아봅니다.
- [노드 및 풀](nodes-and-pools.md) 작업에 대해 알아봅니다.

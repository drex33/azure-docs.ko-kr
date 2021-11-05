---
title: ACI(Azure Container Instances 영역 컨테이너 그룹 배포)
description: 가용성 영역에서 컨테이너 그룹을 배포하는 방법을 알아봅니다.
ms.topic: article
ms.date: 10/13/2021
ms.custom: devx-track-js, devx-track-azurecli
ms.openlocfilehash: a2cce289dc95ecc876ba308ed9406fffb37b9e18
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131859297"
---
# <a name="deploy-an-azure-container-instances-aci-container-group-in-an-availability-zone-preview"></a>가용성 영역에 ACI(Azure Container Instances) 컨테이너 그룹 배포(미리 보기)

[가용성 영역][availability-zone-overview]은 Azure 지역에서 물리적으로 별도 영역입니다. 가용성 영역을 사용하여 전체 데이터 센터의 오류 또는 손실로부터 컨테이너화된 애플리케이션을 보호할 수 있습니다. ACI(Azure Container Instances)는 영역 컨테이너 그룹 배포를 지원합니다. 즉, 인스턴스가 자체 선택된 특정 가용성 영역에 고정됩니다. 가용성 영역은 컨테이너 그룹 수준에서 지정됩니다. 컨테이너 그룹 내의 컨테이너는 고유한 가용성 영역을 가질 수 없습니다. 컨테이너 그룹의 가용성 영역을 변경하려면 컨테이너 그룹을 삭제하고 새 가용성 영역을 사용하여 다른 컨테이너 그룹을 만들어야 합니다.

> [!IMPORTANT]
> 이 기능은 현재 미리 보기로 제공됩니다. 부속 사용 약관에 동의하면 미리 보기를 사용할 수 있습니다.

> [!IMPORTANT]
> 영역 컨테이너 그룹 배포는 Linux 및 Windows Sever 2019 컨테이너 그룹에 ACI를 사용할 수 있는 대부분의 지역에서 지원됩니다. 자세한 내용은 [지역 및 리소스 가용성][container-regions]을 참조하세요.

> [!NOTE]
> 이 문서의 예제는 Bash 셸용으로 서식이 지정되어 있습니다. 다른 셸을 선호하는 경우 줄 연속 문자를 그에 따라 조정합니다.

## <a name="limitations"></a>제한 사항

> [!IMPORTANT]
> 이 기능은 현재 Azure Portal 사용할 수 없습니다.

* GPU 리소스가 있는 컨테이너 그룹은 현재 가용성 영역을 지원하지 않습니다.
* Virtual Network 삽입된 컨테이너 그룹은 현재 가용성 영역을 지원하지 않습니다.
* Windows 2016년 컨테이너 그룹은 현재 가용성 영역을 지원하지 않습니다.

### <a name="version-requirements"></a>버전 요구 사항

* Azure CLI 사용하는 경우 버전 이상 설치되어 있는지 `2.30.0` 확인합니다.
* PowerShell을 사용하는 경우 버전 이상 설치되어 있는지 `2.1.1-preview` 확인합니다.
* Java SDK를 사용하는 경우 버전 이상 설치되어 있는지 `2.9.0` 확인합니다.
* 가용성 영역 지원은 ACI API 버전 이상에서만 사용할 수 `09-01-2021` 있습니다.

## <a name="deploy-a-container-group-using-an-azure-resource-manager-arm-template"></a>ARM(Azure Resource Manager) 템플릿을 사용하여 컨테이너 그룹 배포

### <a name="create-the-arm-template"></a>ARM 템플릿 만들기

먼저 다음 JSON을 `azuredeploy.json`이라는 새 파일에 복사합니다. 이 예제 템플릿은 단일 컨테이너가 있는 컨테이너 그룹을 미국 동부의 가용성 영역 1에 배포합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "metadata": {
        "_generator": {
            "name": "bicep",
            "version": "0.4.1.14562",
            "templateHash": "12367894147709986470"
        }
    },
    "parameters": {
        "name": {
            "type": "string",
            "defaultValue": "acilinuxpublicipcontainergroup",
            "metadata": {
                "description": "Name for the container group"
            }
        },
        "image": {
            "type": "string",
            "defaultValue": "mcr.microsoft.com/azuredocs/aci-helloworld",
            "metadata": {
                "description": "Container image to deploy. Should be of the form repoName/imagename:tag for images stored in public Docker Hub, or a fully qualified URI for other registries. Images from private registries require additional registry credentials."
            }
        },
        "port": {
            "type": "int",
            "defaultValue": 80,
            "metadata": {
                "description": "Port to open on the container and the public IP address."
            }
        },
        "cpuCores": {
            "type": "int",
            "defaultValue": 1,
            "metadata": {
                "description": "The number of CPU cores to allocate to the container."
            }
        },
        "memoryInGb": {
            "type": "int",
            "defaultValue": 2,
            "metadata": {
                "description": "The amount of memory to allocate to the container in gigabytes."
            }
        },
        "restartPolicy": {
            "type": "string",
            "defaultValue": "Always",
            "allowedValues": [
                "Always",
                "Never",
                "OnFailure"
            ],
            "metadata": {
                "description": "The behavior of Azure runtime if container has stopped."
            }
        },
        "location": {
            "type": "string",
            "defaultValue": "eastus",
            "metadata": {
                "description": "Location for all resources."
            }
        }
    },
    "functions": [],
    "resources": [
        {
            "type": "Microsoft.ContainerInstance/containerGroups",
            "apiVersion": "2021-09-01",
            "zones": [
                "1"
            ],
            "name": "[parameters('name')]",
            "location": "[parameters('location')]",
            "properties": {
                "containers": [
                    {
                        "name": "[parameters('name')]",
                        "properties": {
                            "image": "[parameters('image')]",
                            "ports": [
                                {
                                    "port": "[parameters('port')]",
                                    "protocol": "TCP"
                                }
                            ],
                            "resources": {
                                "requests": {
                                    "cpu": "[parameters('cpuCores')]",
                                    "memoryInGB": "[parameters('memoryInGb')]"
                                }
                            }
                        }
                    }
                ],
                "osType": "Linux",
                "restartPolicy": "[parameters('restartPolicy')]",
                "ipAddress": {
                    "type": "Public",
                    "ports": [
                        {
                            "port": "[parameters('port')]",
                            "protocol": "TCP"
                        }
                    ]
                }
            }
        }
    ],
    "outputs": {
        "containerIPv4Address": {
            "type": "string",
            "value": "[reference(resourceId('Microsoft.ContainerInstance/containerGroups', parameters('name'))).ipAddress.ip]"
        }
    }
}
```

### <a name="deploy-the-arm-template"></a>ARM 템플릿 배포

[az group create][az-group-create] 명령을 사용하여 리소스 그룹을 만듭니다.

```azurecli
az group create --name myResourceGroup --location eastus
```

[az deployment group create][az-deployment-group-create] 명령을 사용하여 템플릿을 배포합니다.

```azurecli
az deployment group create \
  --resource-group myResourceGroup \
  --template-file azuredeploy.json
```

## <a name="get-container-group-details"></a>컨테이너 그룹 세부 정보 얻기

컨테이너 그룹이 가용성 영역에 성공적으로 배포되었는지 확인하려면 [az container show][az-container-show] 명령을 사용하여 컨테이너 그룹 세부 정보를 확인합니다.

```azurecli
az containershow --name acilinuxcontainergroup --resource-group myResourceGroup
```

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az_container_create
[container-regions]: container-instances-region-availability.md
[az-container-show]: /cli/azure/container#az_container_show
[az-group-create]: /cli/azure/group#az_group_create
[az-deployment-group-create]: /cli/azure/deployment#az_deployment_group_create
[availability-zone-overview]: /availability-zones/az-overview.md
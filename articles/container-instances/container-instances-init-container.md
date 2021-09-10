---
title: init 컨테이너 실행
description: Azure Container Instances에서 init 컨테이너를 실행하여 애플리케이션 컨테이너가 실행되기 전에 컨테이너 그룹에서 설정 작업을 수행합니다.
ms.topic: article
ms.date: 06/01/2020
ms.openlocfilehash: a108e76f76fb773d0f982a38b6415f9cd9937001
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/14/2021
ms.locfileid: "122529994"
---
# <a name="run-an-init-container-for-setup-tasks-in-a-container-group"></a>컨테이너 그룹에서 설정 작업을 수행하는 데 필요한 init 컨테이너 실행

Azure Container Instances는 컨테이너 그룹에서 ‘init 컨테이너’를 지원합니다. 애플리케이션 컨테이너 또는 컨테이너 시작 전에 init 컨테이너를 완료될 때까지 실행합니다. [Kubernetes init 컨테이너](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/)와 마찬가지로 하나 이상의 init 컨테이너를 사용하여 계정 설정, 설치 스크립트 실행 또는 데이터베이스 구성과 같은 앱 컨테이너에 대한 초기화 논리를 수행합니다.

이 문서에서는 Azure Resource Manager 템플릿을 사용하여 init 컨테이너가 포함된 컨테이너 그룹을 구성하는 방법을 보여 줍니다.

## <a name="things-to-know"></a>알아야 할 사항

* **API 버전** - init 컨테이너를 배포하려면 Azure Container Instances API 버전 2019-12-01 이상이 필요합니다. [YAML 파일](container-instances-multi-container-yaml.md) 또는 [Resource Manager 템플릿](container-instances-multi-container-group.md)의 `initContainers` 속성을 사용하여 배포합니다.
* **실행 순서** - init 컨테이너는 템플릿에 지정된 순서대로, 다른 컨테이너보다 먼저 실행됩니다. 기본적으로 컨테이너 그룹당 최대 59개의 init 컨테이너를 지정할 수 있습니다. init 컨테이너 이외의 컨테이너가 하나 이상 그룹에 있어야 합니다.
* **호스트 환경** - init 컨테이너는 그룹의 나머지 컨테이너와 동일한 하드웨어에서 실행됩니다.
* **리소스** - init 컨테이너용 리소스는 지정하지 않습니다. 컨테이너 그룹에 사용할 수 있는 CPU 및 메모리 같은 전체 리소스에 대한 권한이 부여됩니다. init 컨테이너가 실행되는 동안에는 그룹의 다른 컨테이너가 실행되지 않습니다.
* **지원되는 속성** - init 컨테이너는 볼륨, 비밀, 관리 ID와 같은 그룹 속성을 사용할 수 있습니다. 하지만 컨테이너 그룹에 대해 구성된 경우 포트 또는 IP 주소를 사용할 수 없습니다. 
* **다시 시작 정책** - 각 init 컨테이너는 그룹의 다음 컨테이너를 시작하기 전에 성공적으로 종료되어야 합니다. init 컨테이너가 성공적으로 종료되지 않은 경우 해당 컨테이너의 다시 시작 작업은 그룹에 대해 구성된 [다시 시작 정책](container-instances-restart-policy.md)에 따라 달라집니다.

    |그룹 정책  |init 정책  |
    |---------|---------|
    |항상     |OnFailure         |
    |OnFailure     |OnFailure         |
    |안 함     |안 함         |
* **요금** - 컨테이너 그룹은 init 컨테이너의 첫 번째 배포부터 요금이 부과됩니다.

## <a name="resource-manager-template-example"></a>Resource Manager 템플릿 예제

먼저 다음 JSON을 `azuredeploy.json`이라는 새 파일에 복사합니다. 템플릿은 하나의 init 컨테이너와 두 개의 애플리케이션 컨테이너로 컨테이너 그룹을 설정합니다.

* *init1* 컨테이너는 Docker Hub의 [busybox](https://hub.docker.com/_/busybox) 이미지를 실행합니다. 60초 동안 일시 중지한 다음 [emptyDir 볼륨](container-instances-volume-emptydir.md)의 파일에 명령줄 문자열을 씁니다.
* 애플리케이션 컨테이너는 둘 다 다음과 같이 Microsoft `aci-wordcount` 컨테이너 이미지를 실행합니다.
    * *hamlet* 컨테이너는 단어 개수 앱을 기본 구성으로 실행하여 셰익스피어 희곡 *Hamlet* 의 단어 빈도를 계산합니다.
    * *juliet* 앱 컨테이너는 emptDir 볼륨의 명령줄 문자열을 읽어 셰익스피어의 *Romeo and Juliet* 에 대해 단어 개수 앱을 실행합니다.

`aci-wordcount` 이미지 사용에 대한 자세한 내용 및 예제는 [컨테이너 인스턴스에서 환경 변수 설정](container-instances-environment-variables.md)을 참조하세요.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "containerGroupName": {
        "type": "string",
        "defaultValue": "myContainerGroup",
        "metadata": {
          "description": "Container Group name."
        }
      }
    },
    "resources": [
        {
            "name": "[parameters('containerGroupName')]",
            "type": "Microsoft.ContainerInstance/containerGroups",
            "apiVersion": "2019-12-01",
            "location": "[resourceGroup().location]",
            "properties": {
                "sku": "Standard",
                "initContainers": [
                {
                    "name": "init1",
                    "properties": {
                        "image": "mcr.microsoft.com/aks/e2e/library-busybox:master.210714.1",
                        "environmentVariables": [],
                        "volumeMounts": [
                            {
                                "name": "emptydir1",
                                "mountPath": "/mnt/emptydir"
                            }
                        ],
                         "command": [
                            "/bin/sh",
                            "-c",
                            "sleep 60; echo python wordcount.py http://shakespeare.mit.edu/romeo_juliet/full.html > /mnt/emptydir/command_line.txt"
                        ]
                    }
                }
            ], 
                "containers": [
                    {
                        "name": "hamlet",
                        "properties": {
                            "image": "mcr.microsoft.com/azuredocs/aci-wordcount",
                            "volumeMounts": [
                            {
                                "name": "emptydir1",
                                "mountPath": "/mnt/emptydir"
                            }
                        ],
                            "environmentVariables": [
                             {
                                "name": "NumWords",
                                "value": "3"
                             },
                             {  "name": "MinLength",
                                "value": "5"
                             }
                            ],
                            "resources": {
                                "requests": {
                                    "memoryInGB": 1.0,
                                    "cpu": 1
                                }
                            }
                        }
                    },
                    {
                        "name": "juliet",
                        "properties": {
                            "image": "mcr.microsoft.com/azuredocs/aci-wordcount",
                            "volumeMounts": [
                            {
                                "name": "emptydir1",
                                "mountPath": "/mnt/emptydir"
                            }
                            ],
                            "command": [
                                "/bin/sh",
                                "-c",
                                "$(cat /mnt/emptydir/command_line.txt)"
                            ],
                            "environmentVariables": [
                             {
                                "name": "NumWords",
                                "value": "3"
                             },
                             {  "name": "MinLength",
                                "value": "5"
                             }
                            ],
                            "resources": {
                                "requests": {
                                    "memoryInGB": 1.0,
                                    "cpu": 1
                                }
                            }
                        }
                    }
                ],
                "restartPolicy": "OnFailure",
                "osType": "Linux",
                "volumes": [
                    {
                        "name": "emptydir1",
                        "emptyDir": {}
                    }
                ]           
            },
            "tags": {}
        }
    ]
}
```

## <a name="deploy-the-template"></a>템플릿 배포

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

init 컨테이너가 있는 그룹에서는 해당 init 컨테이너 또는 다른 컨테이너를 완료하는 데 걸리는 시간 때문에 배포 시간이 길어집니다.


## <a name="view-container-logs"></a>컨테이너 로그 보기

init 컨테이너가 성공적으로 실행되었는지 확인하려면 [az container logs][az-container-logs] 명령을 사용하여 앱 컨테이너의 로그 출력을 봅니다. `--container-name` 인수는 로그를 가져올 컨테이너를 지정합니다. 이 예제에서는 다양한 명령 출력을 보여 주는 *hamlet* 및 *juliet* 컨테이너의 로그를 끌어옵니다.

```azurecli
az container logs \
  --resource-group myResourceGroup \
  --name myContainerGroup \
  --container-name hamlet
```

출력:

```console
[('HAMLET', 386), ('HORATIO', 127), ('CLAUDIUS', 120)]
```

```azurecli
az container logs \
  --resource-group myResourceGroup \
  --name myContainerGroup \
  --container-name juliet
```

출력:

```console
[('ROMEO', 177), ('JULIET', 134), ('CAPULET', 119)]
```

## <a name="next-steps"></a>다음 단계

init 컨테이너는 애플리케이션 컨테이너에 대한 설정 및 초기화 작업을 수행하는 데 도움이 됩니다. 작업 기반 컨테이너 실행에 대한 자세한 내용은 [다시 시작 정책으로 컨테이너화된 작업 실행](container-instances-restart-policy.md)을 참조하세요.

Azure Container Instances는 애플리케이션 컨테이너의 동작을 수정하는 다른 옵션을 제공합니다. 다음은 이러한 템플릿의 예입니다.

* [컨테이너 인스턴스에서 환경 변수 설정](container-instances-environment-variables.md)
* [컨테이너 인스턴스에서 명령줄을 설정하여 기본 명령줄 작업 재정의](container-instances-start-command.md)


[az-group-create]: /cli/azure/group#az_group_create
[az-deployment-group-create]: /cli/azure/deployment/group#az_deployment_group_create
[az-container-logs]: /cli/azure/container#az_container_logs

---
title: Azure에서 OKD 배포
description: Azure에서 OKD를 배포합니다.
author: haroldwongms
manager: joraio
ms.service: virtual-machines
ms.subservice: openshift
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/15/2019
ms.author: haroldw
ms.openlocfilehash: e011b48323246d9cff2b6cc6116cb6746fb81299
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122692668"
---
# <a name="deploy-okd-in-azure"></a>Azure에서 OKD 배포

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: 유연한 확장 집합 

Azure에서 OKD(이전의 OpenShift Origin)를 배포하는 두 가지 방법 중 하나를 사용할 수 있습니다.

- 필요한 모든 Azure 인프라 구성 요소를 수동으로 배포한 다음, [OKD 설명서](https://docs.okd.io)를 따릅니다.
- 또한 기존 [Resource Manager 템플릿](https://github.com/Microsoft/openshift-origin)을 사용하여 OKD 클러스터의 배포를 간소화할 수도 있습니다.

## <a name="deploy-using-the-okd-template"></a>OKD 템플릿을 사용하여 배포

Resource Manager 템플릿을 사용하여 배포하려면 매개 변수 파일을 사용하여 입력 매개 변수를 제공합니다. 배포를 추가로 사용자 지정하려면 GitHub 리포지토리를 포크하고 적절한 항목을 변경합니다.

일반적인 사용자 지정 옵션에는 다음이 포함됩니다(이에 국한되지 않음).

- 요새 VM 크기(azuredeploy.json의 변수)
- 명명 규칙(azuredeploy.json의 변수)
- OpenShift 클러스터 세부 정보, 호스트 파일을 통해 수정 됨(deployOpenShift.sh)

[OKD 템플릿](https://github.com/Microsoft/openshift-origin)에는 각기 다른 OKD 버전에 사용할 수 있는 여러 분기가 있습니다.  필요에 따라, 리포지토리에서 직접 배포하거나, 배포하기 전에 리포지토리를 포크하고 사용자 지정할 수 있습니다.

`aadClientId` 매개 변수에 대해 이전에 만든 서비스 주체에서 `appId` 값을 사용합니다.

다음은 모든 필수 입력이 포함된 azuredeploy.parameters.json이라는 매개 변수 파일의 예입니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "masterVmSize": {
            "value": "Standard_E2s_v3"
        },
        "infraVmSize": {
            "value": "Standard_E2s_v3"
        },
        "nodeVmSize": {
            "value": "Standard_E2s_v3"
        },
        "storageKind": {
            "value": "managed"
        },
        "openshiftClusterPrefix": {
            "value": "mycluster"
        },
        "masterInstanceCount": {
            "value": 3
        },
        "infraInstanceCount": {
            "value": 2
        },
        "nodeInstanceCount": {
            "value": 2
        },
        "dataDiskSize": {
            "value": 128
        },
        "adminUsername": {
            "value": "clusteradmin"
        },
        "openshiftPassword": {
            "value": "{Strong Password}"
        },
        "sshPublicKey": {
            "value": "{SSH Public Key}"
        },
        "enableMetrics": {
            "value": "true"
        },
        "enableLogging": {
            "value": "false"
        },
        "keyVaultResourceGroup": {
            "value": "keyvaultrg"
        },
        "keyVaultName": {
            "value": "keyvault"
        },
        "keyVaultSecret": {
            "value": "keysecret"
        },
        "enableAzure": {
            "value": "true"
        },
        "aadClientId": {
            "value": "11111111-abcd-1234-efgh-111111111111"
        },
        "aadClientSecret": {
            "value": "{Strong Password}"
        },
        "defaultSubDomainType": {
            "value": "nipio"
        }
    }
}
```

매개 변수를 자신의 특정 정보로 바꿉니다.

릴리스마다 다른 매개 변수를 사용할 수 있으므로, 사용하는 분기에 필요한 매개 변수를 확인합니다.

### <a name="deploy-using-azure-cli"></a>Azure CLI를 사용하여 배포


> [!NOTE] 
> 다음 명령은 Azure CLI 2.0.8 이상이 필요합니다. CLI 버전은 `az --version` 명령으로 확인할 수 있습니다. CLI 버전을 업데이트하려면 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

다음 예제에서는 openshiftrg라는 리소스 그룹에 OKD 클러스터 및 모든 관련 리소스를 배포하고 배포 이름을 myOpenShiftCluster로 지정합니다. 템플릿은 azuredeploy.parameters.json이라는 로컬 매개 변수 파일을 사용하는 동안 GitHub 리포지토리에서 직접 참조됩니다.

```azurecli 
az deployment group create -g openshiftrg --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-origin/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

배포되는 전체 노드 수에 따라 배포를 완료하는 데 30분 이상 걸립니다. 배포를 마치면 OpenShift 콘솔의 URL과 OpenShift 마스터의 DNS 이름이 터미널에 출력됩니다. 또는 Azure Portal에서 배포의 출력 섹션을 볼 수 있습니다.

```json
{
  "OpenShift Console Url": "http://openshiftlb.cloudapp.azure.com/console",
  "OpenShift Master SSH": "ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com"
}
```

배포가 완료될 때까지 명령줄을 대기하지 않으려면 그룹 배포에 대한 옵션 중 하나로 `--no-wait`를 추가합니다. 배포의 출력은 Azure Portal의 리소스 그룹에 대한 배포 섹션에서 검색할 수 있습니다.

## <a name="connect-to-the-okd-cluster"></a>OKD 클러스터에 연결

배포가 완료되면 브라우저에서 `OpenShift Console Url`을 사용하여 OpenShift 콘솔에 연결합니다. 또는 OKD 마스터에 대해 SSH를 수행할 수 있습니다. 다음은 배포의 출력을 사용하는 예제입니다.

```bash
$ ssh -p 2200 clusteradmin@myopenshiftmaster.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>리소스 정리

리소스 그룹, OpenShift 클러스터 및 모든 관련된 리소스가 더 이상 필요하지 않은 경우 [az group delete](/cli/azure/group) 명령을 사용하여 제거합니다.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>다음 단계

- [배포 후 작업](./openshift-container-platform-3x-post-deployment.md)
- [OpenShift 배포 문제 해결](./openshift-container-platform-3x-troubleshooting.md)
- [OKD 시작](https://docs.okd.io)

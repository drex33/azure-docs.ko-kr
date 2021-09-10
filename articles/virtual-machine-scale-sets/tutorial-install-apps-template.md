---
title: 자습서 - Azure 템플릿을 사용하여 확장 집합에 앱 설치
description: Azure Resource Manager 템플릿을 사용하여 사용자 지정 스크립트 확장이 있는 가상 머신 확장 집합에 애플리케이션을 설치하는 방법을 알아봅니다.
author: ju-shim
ms.author: jushiman
ms.topic: tutorial
ms.service: virtual-machine-scale-sets
ms.subservice: extensions
ms.date: 03/27/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: eb6e624e17bd0362c5a56671d40acfe5750e9378
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122693010"
---
# <a name="tutorial-install-applications-in-virtual-machine-scale-sets-with-an-azure-template"></a>자습서: Azure 템플릿을 사용하여 가상 머신 확장 집합에 애플리케이션 설치

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 균일한 확장 집합

확장 집합의 VM(가상 머신) 인스턴스에서 애플리케이션을 실행하려면 먼저 애플리케이션 구성 요소 및 필요한 파일을 설치해야 합니다. 이전 자습서에서는 사용자 지정 VM 이미지를 만들고 사용하여 VM 인스턴스를 배포하는 방법을 알아보았습니다. 이 사용자 지정 이미지에는 수동 애플리케이션 설치 및 구성이 포함되어 있습니다. 또한 각 VM 인스턴스가 배포된 후에 확장 집합에 애플리케이션 설치를 자동화하거나 이미 확장 집합에서 실행되는 애플리케이션을 업데이트할 수 있습니다. 이 자습서에서는 다음 방법에 대해 알아봅니다.

> [!div class="checklist"]
> * 확장 집합에 애플리케이션 자동 설치
> * Azure 사용자 지정 스크립트 확장 사용
> * 확장 집합에서 실행되는 애플리케이션 업데이트

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- 이 문서에는 Azure CLI 버전 2.0.29 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다.


## <a name="what-is-the-azure-custom-script-extension"></a>Azure 사용자 지정 스크립트 확장이란?
사용자 지정 스크립트 확장은 Azure VM에서 스크립트를 다운로드하고 실행합니다. 이 확장은 배포 후 구성, 소프트웨어 설치 또는 기타 구성/관리 작업에 유용합니다. 스크립트는 Azure Storage 또는 GitHub에서 다운로드하거나 확장 런타임에서 Azure Portal에 제공할 수 있습니다.

사용자 지정 스크립트 확장은 Azure Resource Manager 템플릿과 통합되며, Azure CLI, Azure PowerShell, Azure Portal 또는 REST API에서 사용할 수도 있습니다. 자세한 내용은 [사용자 지정 스크립트 확장 개요](../virtual-machines/extensions/custom-script-linux.md)를 참조하세요.

작동 중인 사용자 지정 스크립트 확장을 확인하려면 NGINX 웹 서버를 설치하고 확장 집합 VM 인스턴스의 호스트 이름을 출력하는 확장 집합을 만듭니다. 다음 사용자 지정 스크립트 확장 정의는 GitHub에서 샘플 스크립트를 다운로드하고, 필요한 패키지를 설치한 다음, VM 인스턴스 호스트 이름을 기본 HTML 페이지에 작성합니다.


## <a name="create-custom-script-extension-definition"></a>사용자 지정 스크립트 확장 정의 만들기
Azure 템플릿을 사용하여 가상 머신 확장 집합을 정의하는 경우 *Microsoft.Compute/virtualMachineScaleSets* 리소스 공급자에 확장에 대한 섹션이 포함될 수 있습니다. *extensionsProfile* 은 확장 집합의 VM 인스턴스에 적용되는 내용을 자세히 설명합니다. 사용자 지정 스크립트 확장을 사용하려면 *Microsoft.Azure.Extensions* 의 게시자와 *CustomScript* 형식을 지정합니다.

*fileUris* 속성은 원본 설치 스크립트 또는 패키지를 정의하는 데 사용됩니다. 설치 프로세스를 시작하기 위해 필요한 스크립트가 *commandToExecute* 에 정의되어 있습니다. 다음 예제에서는 NGINX 웹 서버를 설치하고 구성하는 GitHub의 샘플 스크립트를 정의합니다.

```json
"extensionProfile": {
  "extensions": [
    {
      "name": "AppInstall",
      "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "fileUris": [
            "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate_nginx.sh"
          ],
          "commandToExecute": "bash automate_nginx.sh"
        }
      }
    }
  ]
}
```

확장 집합과 사용자 지정 스크립트 확장을 배포하는 Azure 템플릿의 전체 예제는 [https://github.com/Azure-Samples/compute-automation-configurations/blob/master/scale_sets/azuredeploy.json](https://github.com/Azure-Samples/compute-automation-configurations/blob/master/scale_sets/azuredeploy.json)을 참조하세요. 이 샘플 템플릿은 다음 섹션에서 사용됩니다.


## <a name="create-a-scale-set"></a>확장 집합 만들기
샘플 템플릿을 사용하여 확장 집합을 만들고 사용자 지정 스크립트 확장을 적용해 보겠습니다. 먼저 [az group create](/cli/azure/group)를 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 *eastus* 위치에 *myResourceGroup* 이라는 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

이제 [az deployment group create](/cli/azure/deployment/group)를 사용하여 가상 머신 확장 집합을 만듭니다. 메시지가 표시되면 각 VM 인스턴스에 대한 자격 증명으로 사용되는 고유한 사용자 이름과 암호를 제공합니다.

```azurecli-interactive
az deployment group create \
  --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/scale_sets/azuredeploy.json
```

확장 집합 리소스와 VM을 모두 만들고 구성하는 데 몇 분 정도 걸립니다.

확장 집합의 각 VM 인스턴스는 GitHub에서 스크립트를 다운로드하고 실행합니다. 더 복잡한 예제에서는 여러 애플리케이션 구성 요소와 파일을 설치할 수 있습니다. 확장 집합의 크기를 강화하면 새 VM 인스턴스에서 자동으로 동일한 사용자 지정 스크립트 확장 정의를 적용하고 필요한 애플리케이션을 설치합니다.


## <a name="test-your-scale-set"></a>확장 집합 테스트
작업 중인 웹 서버를 보려면 [az network public-ip show](/cli/azure/network/public-ip)를 사용하여 부하 분산 장치의 공용 IP 주소를 가져옵니다. 다음 예제에서는 확장 집합의 일부로 만든 *myScaleSetPublicIP* 의 IP 주소를 가져옵니다.

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetPublicIP \
  --query [ipAddress] \
  --output tsv
```

웹 브라우저에 부하 분산 장치의 공용 IP 주소를 입력합니다. 부하 분산 장치는 다음 예제와 같이 VM 인스턴스 중 하나에 트래픽을 분산합니다.

![Nginx의 기본 웹 페이지](media/tutorial-install-apps-template/running-nginx.png)

다음 단계에서 업데이트된 버전이 표시되도록 웹 브라우저를 열어 둡니다.


## <a name="update-app-deployment"></a>앱 배포 업데이트
확장 집합의 수명 주기 전체에서 애플리케이션의 업데이트된 버전을 배포해야 할 수 있습니다. 사용자 지정 스크립트 확장을 사용하면 업데이트된 배포 스크립트를 참조한 다음, 해당 확장을 확장 집합에 다시 적용할 수 있습니다. 이전 단계에서 확장 집합을 만든 경우 *upgradePolicy* 가 *Automatic* 으로 설정되어 있습니다. 이 설정을 사용하면 확장 집합의 VM 인스턴스에서 자동으로 업데이트하여 애플리케이션의 최신 버전을 적용할 수 있습니다.

사용자 지정 스크립트 확장 정의를 업데이트하려면 새 설치 스크립트를 참조하도록 템플릿을 편집합니다. 변경 내용을 인식하려면 사용자 지정 스크립트 확장에 새 파일 이름을 사용해야 합니다. 사용자 지정 스크립트 확장은 변경 내용을 확인하기 위해 스크립트의 내용을 검사하지 않습니다. 다음 정의에서는 이름에 *_v2* 가 추가된 업데이트된 설치 스크립트를 사용합니다.

```json
"extensionProfile": {
  "extensions": [
    {
      "name": "AppInstall",
      "properties": {
        "publisher": "Microsoft.Azure.Extensions",
        "type": "CustomScript",
        "typeHandlerVersion": "2.0",
        "autoUpgradeMinorVersion": true,
        "settings": {
          "fileUris": [
            "https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate_nginx_v2.sh"
          ],
          "commandToExecute": "bash automate_nginx_v2.sh"
        }
      }
    }
  ]
}
```

[az deployment group create](/cli/azure/deployment/group)를 사용하여 사용자 지정 스크립트 확장 구성을 확장 집합의 VM 인스턴스에 다시 적용합니다. 이 *azuredeployv2.json* 템플릿은 업데이트된 애플리케이션 버전을 적용하는 데 사용됩니다. 실제로 이전 섹션에서 설명한 대로 업데이트된 설치 스크립트를 참조하도록 기존 *azuredeploy.json* 템플릿을 편집합니다. 메시지가 표시되면 확장 집합을 처음 만들 때 사용한 것과 동일한 사용자 이름 및 암호 자격 증명을 입력합니다.

```azurecli-interactive
az deployment group create \
  --resource-group myResourceGroup \
  --template-uri https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/scale_sets/azuredeploy_v2.json
```

확장 집합의 모든 VM 인스턴스가 자동으로 샘플 웹 페이지의 최신 버전으로 업데이트됩니다. 업데이트된 버전을 확인하려면 브라우저에서 웹 사이트를 새로 고칩니다.

![Nginx에서 업데이트된 웹 페이지](media/tutorial-install-apps-template/running-nginx-updated.png)


## <a name="clean-up-resources"></a>리소스 정리
확장 집합 및 추가 리소스를 제거하려면 [az group delete](/cli/azure/group)를 사용하여 리소스 그룹 및 모든 해당 리소스를 삭제합니다. `--no-wait` 매개 변수는 작업이 완료될 때까지 대기하지 않고 프롬프트로 제어를 반환합니다. `--yes` 매개 변수는 작업을 수행하는 추가 프롬프트 없이 리소스를 삭제할 것인지 확인합니다.

```azurecli-interactive
az group delete --name myResourceGroup --no-wait --yes
```


## <a name="next-steps"></a>다음 단계
이 자습서에서는 Azure 템플릿을 사용하여 확장 집합에 애플리케이션을 자동으로 설치하고 업데이트하는 방법을 알아보았습니다.

> [!div class="checklist"]
> * 확장 집합에 애플리케이션 자동 설치
> * Azure 사용자 지정 스크립트 확장 사용
> * 확장 집합에서 실행되는 애플리케이션 업데이트

확장 집합을 자동으로 크기 조정하는 방법을 알아보려면 다음 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [확장 집합 자동 크기 조정](tutorial-autoscale-template.md)

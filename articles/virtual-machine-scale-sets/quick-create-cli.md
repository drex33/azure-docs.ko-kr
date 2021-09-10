---
title: 빠른 시작 - Azure CLI를 사용하여 가상 머신 확장 집합 만들기
description: Azure CLI를 사용하여 가상 머신 확장 집합을 신속하게 만드는 방법을 학습하여 배포를 시작합니다.
author: ju-shim
ms.author: jushiman
ms.topic: quickstart
ms.service: virtual-machine-scale-sets
ms.date: 03/27/2018
ms.reviewer: mimckitt
ms.custom: mimckitt, devx-track-azurecli
ms.openlocfilehash: 9f89d1003cb4ec45ec55d4df0499d2efc1910000
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122693316"
---
# <a name="quickstart-create-a-virtual-machine-scale-set-with-the-azure-cli"></a>빠른 시작: Azure CLI를 사용하여 가상 머신 확장 집합 만들기

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 균일한 확장 집합

가상 머신 확장 집합을 사용하면 자동 크기 조정 가상 머신 세트를 배포하고 관리할 수 있습니다. 확장 집합의 VM 수를 수동으로 조정하거나 리소스 사용량(예: CPU, 메모리 요구량 또는 네트워크 트래픽)에 따라 자동으로 크기를 조정하는 규칙을 정의할 수 있습니다. 그러면 Azure 부하 분산 장치에서 확장 집합의 VM 인스턴스에 트래픽을 분산합니다. 이 빠른 시작에서는 Azure CLI로 가상 머신 확장 집합을 만들고, 샘플 애플리케이션을 배포합니다.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- 이 문서에는 Azure CLI 버전 2.0.29 이상이 필요합니다. Azure Cloud Shell을 사용하는 경우 최신 버전이 이미 설치되어 있습니다. 


## <a name="create-a-scale-set"></a>확장 집합 만들기
확장 집합을 만들려면 먼저 [az group create](/cli/azure/group)를 사용하여 리소스 그룹을 만듭니다. 다음 예제에서는 *eastus* 위치에 *myResourceGroup* 이라는 리소스 그룹을 만듭니다.

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

이제 [az vmss create](/cli/azure/vmss)를 사용하여 가상 머신 확장 집합을 만듭니다. 다음 예제에서는 변경 내용이 적용될 때 자동으로 업데이트하도록 설정된 *myScaleSet* 이라는 확장 집합을 만들고, *~/.ssh/id_rsa* 에 없는 경우 SSH 키를 생성합니다. 이러한 SSH 키는 VM 인스턴스에 로그인해야 하는 경우에 사용됩니다. 기존 SSH 키 집합을 사용하려면 `--ssh-key-value` 매개 변수를 대신 사용하고 키의 위치를 지정합니다.

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --generate-ssh-keys
```

확장 집합 리소스와 VM을 모두 만들고 구성하는 데 몇 분 정도 걸립니다.


## <a name="deploy-sample-application"></a>샘플 애플리케이션 배포
확장 집합을 테스트하려면 기본 웹 애플리케이션을 설치합니다. Azure 사용자 지정 스크립트 확장을 사용하여 VM 인스턴스에 애플리케이션을 설치하는 스크립트를 다운로드하고 실행합니다. 이 확장은 배포 후 구성, 소프트웨어 설치 또는 기타 구성/관리 작업에 유용합니다. 자세한 내용은 [사용자 지정 스크립트 확장 개요](../virtual-machines/extensions/custom-script-linux.md)를 참조하세요.

사용자 지정 스크립트 확장을 사용하여 기본 NGINX 웹 서버 설치 다음과 같이 [az vmss extension set](/cli/azure/vmss/extension)를 사용하여 NGINX를 설치하는 사용자 지정 스크립트 확장을 적용합니다.

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroup \
  --vmss-name myScaleSet \
  --settings '{"fileUris":["https://raw.githubusercontent.com/Azure-Samples/compute-automation-configurations/master/automate_nginx.sh"],"commandToExecute":"./automate_nginx.sh"}'
```


## <a name="allow-traffic-to-application"></a>애플리케이션에 트래픽 허용
확장 집합이 만들어지면 Azure 부하 분산 장치가 자동으로 배포됩니다. Azure 부하 분산 장치에서 확장 집합의 VM 인스턴스에 트래픽을 분산합니다. 트래픽이 샘플 웹 애플리케이션에 도달하도록 허용하려면 [az 네트워크 LB 규칙 생성](/cli/azure/network/lb/rule)을 사용하여 부하 분산 장치 규칙을 만듭니다. 다음 예제는 *myLoadBalancerRuleWeb* 이라는 규칙을 만듭니다.

```azurecli-interactive
az network lb rule create \
  --resource-group myResourceGroup \
  --name myLoadBalancerRuleWeb \
  --lb-name myScaleSetLB \
  --backend-pool-name myScaleSetLBBEPool \
  --backend-port 80 \
  --frontend-ip-name loadBalancerFrontEnd \
  --frontend-port 80 \
  --protocol tcp
```


## <a name="test-your-scale-set"></a>확장 집합 테스트
작동 중인 확장 집합을 확인하려면 웹 브라우저에서 샘플 웹 애플리케이션에 액세스합니다. [az network public-ip show](/cli/azure/network/public-ip)를 사용하여 부하 분산 장치의 공용 IP 주소를 가져옵니다. 다음 예제에서는 확장 집합의 일부로 만든 *myScaleSetLBPublicIP* 의 IP 주소를 가져옵니다.

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroup \
  --name myScaleSetLBPublicIP \
  --query '[ipAddress]' \
  --output tsv
```

웹 브라우저에 부하 분산 장치의 공용 IP 주소를 입력합니다. 부하 분산 장치는 다음 예제와 같이 VM 인스턴스 중 하나에 트래픽을 분산합니다.

![NGINX의 기본 웹 페이지](media/virtual-machine-scale-sets-create-cli/running-nginx-site.png)


## <a name="clean-up-resources"></a>리소스 정리
더 이상 필요하지 않은 경우 다음과 같이 [az group delete](/cli/azure/group)를 사용하여 리소스 그룹, 확장 집합 및 모든 관련 리소스를 제거할 수 있습니다. `--no-wait` 매개 변수는 작업이 완료될 때까지 대기하지 않고 프롬프트로 제어를 반환합니다. `--yes` 매개 변수는 작업을 수행하는 추가 프롬프트 없이 리소스를 삭제할 것인지 확인합니다.

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```


## <a name="next-steps"></a>다음 단계
이 빠른 시작에서는 기본 확장 집합을 만들고, 사용자 지정 스크립트 확장을 사용하여 VM 인스턴스에 기본 NGINX 웹 서버를 설치했습니다. 더 자세히 알아보려면 Azure 가상 머신 확장 집합을 만들고 관리하는 방법에 대한 자습서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [Azure 가상 머신 확장 집합 만들기 및 관리](tutorial-create-and-manage-cli.md)

---
title: Load Balancer TCP 초기화와 유휴 시간 제한 구성
titleSuffix: Azure Load Balancer
description: 이 문서에서는 Azure Load Balancer TCP 유휴 시간 제한과 초기화를 구성하는 방법에 대해 알아봅니다.
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18, devx-track-azurepowershell
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/26/2020
ms.author: allensu
ms.openlocfilehash: 2a360f5972e4a0b35be20af18d365a88db779c85
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/29/2021
ms.locfileid: "110695561"
---
# <a name="configure-tcp-reset-and-idle-timeout-for-azure-load-balancer"></a>Azure Load Balancer에 대한 TCP 초기화와 유휴 시간 제한 설정 구성

Azure Load Balancer의 유휴 시간 제한 범위는 다음과 같습니다.

* 아웃바운드 규칙의 경우 4분~100분
* Load Balancer 규칙 및 인바운드 NAT 규칙의 경우 4분~30분

기본적으로 4분으로 설정되어 있습니다. 비활성 기간이 시간 제한 값보다 긴 경우 클라이언트와 서비스 간의 TCP 또는 HTTP 세션이 유지되지 않을 수 있습니다. 

다음 섹션에서는 Load Balancer 리소스의 유휴 시간 제한 설정과 TCP 초기화 설정을 변경하는 방법을 설명합니다.

## <a name="set-tcp-reset-and-idle-timeout"></a>TCP 초기화 및 유휴 시간 제한 설정
---
# <a name="portal"></a>[**포털**](#tab/tcp-reset-idle-portal)

부하 분산 장치에 대한 유휴 시간 제한과 TCP 초기화를 설정하려면 부하 분산된 규칙을 편집합니다. 

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

2. 왼쪽 메뉴에서 **리소스 그룹** 을 선택합니다.

3. 부하 분산 장치를 위한 리소스 그룹을 선택합니다. 이 예제에서는 리소스 그룹의 이름을 **myResourceGroup** 으로 지정합니다.

4. 부하 분산 장치를 선택합니다. 이 예제에서는 부하 분산 장치 이름이 **myLoadBalancer** 로 지정됩니다.

5. **설정** 에서 **부하 분산 규칙** 을 선택합니다.

     :::image type="content" source="./media/load-balancer-tcp-idle-timeout/portal-lb-rules.png" alt-text="부하 분산 장치 규칙을 편집합니다." border="true":::

6. 부하 분산 규칙을 선택합니다. 이 예제에서는 부하 분산 규칙의 이름이 **myLBrule** 입니다.

7. 부하 분산 규칙에서 **유휴 시간 제한(분)** 의 슬라이더를 시간 제한 값으로 이동합니다.  

8. **TCP 초기화** 에서 **사용** 을 선택합니다.

   :::image type="content" source="./media/load-balancer-tcp-idle-timeout/portal-lb-rules-tcp-reset.png" alt-text="유휴 시간 제한과 TCP 초기화를 설정합니다." border="true":::

9. **저장** 을 선택합니다.

# <a name="powershell"></a>[**PowerShell**](#tab/tcp-reset-idle-powershell)

유휴 시간 제한과 TCP 초기화를 설정하려면 [Set-AzLoadBalancer](/powershell/module/az.network/set-azloadbalancer)를 사용하여 다음과 같은 부하 분산 규칙 매개 변수의 값을 설정합니다.

* **IdleTimeoutInMinutes**
* **EnableTcpReset**

PowerShell을 로컬로 설치하고 사용하도록 선택하는 경우 이 문서에는 Azure PowerShell 모듈 버전 5.4.1 이상이 필요합니다. 설치되어 있는 버전을 확인하려면 `Get-Module -ListAvailable Az`을 실행합니다. 업그레이드해야 하는 경우 [Azure PowerShell 모듈 설치](/powershell/azure/install-Az-ps)를 참조하세요. 또한 PowerShell을 로컬로 실행하는 경우 `Connect-AzAccount`를 실행하여 Azure와 연결해야 합니다.

다음 예제를 리소스의 값으로 바꿉니다.

* **myResourceGroup**
* **myLoadBalancer**

```azurepowershell
$lb = Get-AzLoadBalancer -Name "myLoadBalancer" -ResourceGroup "myResourceGroup"
$lb.LoadBalancingRules[0].IdleTimeoutInMinutes = '15'
$lb.LoadBalancingRules[0].EnableTcpReset = 'true'
Set-AzLoadBalancer -LoadBalancer $lb
```

# <a name="azure-cli"></a>[**Azure CLI**](#tab/tcp-reset-idle-cli)

유휴 시간 제한과 TCP 초기화를 설정하려면 [az network lb rule update](/cli/azure/network/lb/rule?az_network_lb_rule_update)에 다음 매개 변수를 사용합니다.

* **--idle-timeout**
* **--enable-tcp-reset**

시작하기 전에 현재 환경의 유효성을 검사합니다.

* Azure Portal에 로그인하고 `az login`을 실행하여 구독이 활성 상태인지 확인합니다.
* 터미널 또는 명령 창에서 `az --version`을 실행하여 Azure CLI 버전을 확인합니다. 최신 버전은 [최신 릴리스 정보](/cli/azure/release-notes-azure-cli?tabs=azure-cli)를 참조하세요.
  * 최신 버전이 없는 경우 [사용 중인 운영 체제 또는 플랫폼의 설치 가이드](/cli/azure/install-azure-cli)에 따라 설치를 업데이트합니다.

다음 예제를 리소스의 값으로 바꿉니다.

* **myResourceGroup**
* **myLoadBalancer**
* **myLBrule**


```azurecli
az network lb rule update \
    --resource-group myResourceGroup \
    --name myLBrule \
    --lb-name myLoadBalancer \
    --idle-timeout 15 \
    --enable-tcp-reset true
```
---
## <a name="next-steps"></a>다음 단계

TCP 유휴 시간 제한과 초기화에 대한 자세한 내용은 [Load Balancer TCP 초기화와 유휴 시간 제한](load-balancer-tcp-reset.md)을 참조하세요.

Load Balancer 배포 모드를 구성하는 방법에 대한 자세한 내용은 [부하 분산 장치 배포 모드 구성](load-balancer-distribution-mode.md)을 참조하세요.

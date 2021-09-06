---
title: Azure Cloud Services(추가 지원)에서 배포 교환 또는 전환
description: Azure Cloud Services(추가 지원)에서 배포 사이를 교환하거나 전환하는 방법을 알아봅니다.
ms.topic: how-to
ms.service: cloud-services-extended-support
author: surbhijain
ms.author: surbhijain
ms.reviewer: gachandw
ms.date: 04/01/2021
ms.custom: ''
ms.openlocfilehash: d9e30d77708ad5ae8c5249a15d28685a56fd0216
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114462987"
---
# <a name="swap-or-switch-deployments-in-azure-cloud-services-extended-support"></a>Azure Cloud Services(추가 지원)에서 배포 교환 또는 전환

Azure Cloud Services(추가 지원)에서는 2개의 독립 클라우드 서비스 배포 간에 교환이 가능합니다. Azure Cloud Services(클래식)와 달리 Azure Cloud Services(추가 지원)에서 Azure Resource Manager 모델은 배포 슬롯을 사용하지 않습니다. Azure Cloud Services(추가 지원)에서 클라우드 서비스의 새 릴리스를 배포할 때 Azure Cloud Services(확장 지원)의 기존 클라우드 서비스와 “교환 가능”하도록 설정할 수 있습니다.

배포를 교환한 후 새 클라우드 서비스 배포를 사용하여 새 릴리스를 스테이징하고 테스트할 수 있습니다. 실제로 교환 시 스테이징된 새 클라우드 서비스가 프로덕션 릴리스로 승격됩니다.

> [!NOTE]
> Azure Cloud Services(클래식) 배포와 Azure Cloud Services(추가 지원) 배포 간에는 교환할 수 없습니다.

처음으로 클라우드 서비스 쌍 중 두 번째를 배포할 때 클라우드 서비스를 다른 클라우드 서비스와 교환 가능하도록 설정해야 합니다. 두 번째 클라우드 서비스 쌍이 배포되면 후속 업데이트에서 기존 클라우드 서비스와 교환할 수 없습니다.

Azure Resource Manager 템플릿(ARM 템플릿), Azure Portal 또는 REST API를 사용하여 배포를 교환할 수 있습니다.

두 번째 클라우드 서비스를 배포할 때 두 클라우드 서비스는 서로 가리키도록 해당 SwappableCloudService 속성을 설정합니다. 이러한 클라우드 서비스에 대한 모든 후속 업데이트는 이 속성이 실패하도록 지정해야 하며, SwappableCloudService 속성을 삭제하거나 업데이트할 수 없음을 나타내는 오류가 반환됩니다.

설정되면 SwappableCloudService 속성이 읽기 전용으로 처리됩니다. 삭제하거나 다른 값으로 변경할 수 없습니다. 클라우드 서비스 중 하나(교환 가능한 쌍)를 삭제하면 나머지 클라우드 서비스의 SwappableCloudService 속성이 지워지게 됩니다.


## <a name="arm-template"></a>ARM 템플릿

ARM 템플릿 배포 방법을 사용하는 경우, 클라우드 서비스 교환을 가능하게 하려면 `cloudServices` 개체의 `networkProfile`에 있는 `SwappableCloudService` 속성을 페어링된 클라우드 서비스의 ID로 설정합니다.

```json
"networkProfile": {
 "SwappableCloudService": {
              "id": "[concat(variables('swappableResourcePrefix'), 'Microsoft.Compute/cloudServices/', parameters('cloudServicesToBeSwappedWith'))]"
            },
        }
```

## <a name="azure-portal"></a>Azure Portal

Azure Portal에서 배포를 교환하려면 다음을 수행합니다.

1. Portal 메뉴에서 **Cloud Services(추가 지원)** 또는 **대시보드** 를 선택합니다.
1. 업데이트하려는 클라우드 서비스를 선택합니다.
1. 클라우드 서비스에 대한 **개요** 에서 **교환** 을 선택합니다.

   :::image type="content" source="media/swap-cloud-service-portal-swap.png" alt-text="클라우드 서비스에 대한 교환 탭을 보여주는 스크린샷.":::

1. 교환 확인 창에서 배포 정보를 확인한 다음 **확인** 을 선택하여 배포를 교환합니다.

   :::image type="content" source="media/swap-cloud-service-portal-confirm.png" alt-text="배포 교환 정보 확인을 보여주는 스크린샷.":::

배포되는 클라우드 서비스에 대한 가상 IP 주소만 변경되므로 배포 교환은 빠르게 이루어집니다.

컴퓨팅 비용을 절감하려면 교환된 클라우드 서비스가 예상대로 작동하는지 확인한 후 애플리케이션 배포에 대한 스테이징 환경으로 지정된 클라우드 서비스를 삭제할 수 있습니다.

## <a name="rest-api"></a>REST API

REST API를 사용하여 Azure Cloud Services(추가 지원)에서 새로운 클라우드 서비스 배포로 교환하려면 다음 명령과 JSON 구성을 사용합니다.

```http
POST https://management.azure.com/subscriptions/subId/providers/Microsoft.Network/locations/region/setLoadBalancerFrontendPublicIpAddresses?api-version=2020-11-01
```

```json
{
  "frontendIPConfigurations": [
    {
    "id": "#LBFE1#",
    "properties": {
    "publicIPAddress": {
    "id": "#PIP2#"
    }
      }
    },
   {
    "id": "#LBFE2#",
    "properties": {
    "publicIPAddress": {
    "id": "#PIP1#"
     }
       }
    }
  ]
 }
}
```

## <a name="common-questions-about-swapping-deployments"></a>배포 교환에 대한 일반적인 질문

Azure Cloud Services(확장 지원)의 배포 교환에 대한 일반적인 질문과 답변을 검토하세요.

### <a name="what-are-the-prerequisites-for-swapping-to-a-new-cloud-services-deployment"></a>새 클라우드 서비스 배포로 교환하는 데 필수 조건은 무엇인가요?

Azure Cloud Services(추가 지원)에서 배포 교환에 성공하려면 두 가지 주요 필수 조건을 충족해야 합니다.

* 교환할 수 있는 클라우드 서비스 중 하나에 고정 IP 주소 또는 예약된 IP 주소를 사용하려는 경우 다른 클라우드 서비스도 예약된 IP 주소를 사용해야 합니다. 그러지 않으면 교체가 실패합니다.
* 교환에 성공하려면 역할의 모든 인스턴스가 실행 중이어야 합니다. 인스턴스의 상태를 확인하려면 Azure Portal에서 새로 배포된 클라우드 서비스의 **개요** 로 이동하거나 Windows PowerShell에서 `Get-AzRole` 명령을 사용합니다.

게스트 OS 업데이트 및 서비스 복구 작업으로 인해 배포 교환이 실패할 수도 있습니다. 자세한 내용은 [클라우드 서비스 배포 문제 해결](../cloud-services/cloud-services-troubleshoot-deployment-problems.md)을 참조하세요.

### <a name="can-i-make-a-vip-swap-in-parallel-with-another-mutating-operation"></a>다른 변경 작업과 동시에 VIP 교환을 진행할 수 있나요?

아니요. VIP 교환은 클라우드 서비스에서 다른 컴퓨팅 작업을 시작하기 전에 완료해야 하는 네트워킹 전용 변경 작업입니다. VIP 교환 진행 중에 클라우드 서비스에 대한 업데이트, 삭제 또는 자동 크기 조정 작업을 시작하거나, 다른 컴퓨팅 작업 진행 중에 VIP 교환을 트리거하면 클라우드 서비스가 복구할 수 없는 오류 상태에 빠질 수 있습니다.

### <a name="does-a-swap-incur-downtime-for-my-application-and-how-should-i-handle-it"></a>교환으로 인해 애플리케이션 가동 중지 시간이 발생하나요? 이를 어떻게 처리해야 하나요?

클라우드 서비스 교환은 일반적으로 Azure 부하 분산 장치에서의 구성 변경이기 때문에 빠르게 진행됩니다. 경우에 따라 10초 이상 걸리며 일시적인 연결 오류가 발생할 수 있습니다. 교환이 사용자에게 미치는 영향을 제한하려면 클라이언트 다시 시도 논리를 구현하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계 

* Azure Cloud Services(추가 지원)에 대한 [배포 필수 조건](deploy-prerequisite.md)을 검토합니다.
* [Azure Cloud Services(추가 지원)에 대한 질문과 대답](faq.yml)을 검토합니다.
* 다음 옵션 중 하나를 사용하여 Azure Cloud Services(추가 지원) 클라우드 서비스를 배포합니다.
  * [Azure Portal](deploy-portal.md)
  * [PowerShell](deploy-powershell.md)
  * [ARM 템플릿](deploy-template.md)
  * [Visual Studio](deploy-visual-studio.md)

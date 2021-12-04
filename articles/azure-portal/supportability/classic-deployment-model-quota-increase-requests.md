---
title: 클래식 배포 모델에 대 한 VM 패밀리 vCPU 할당량 늘리기
description: 이제 Resource Manager 모델로 교체된 클래식 배포 모델은 VM 및 가상 머신 확장 집합에 대해 전역 vCPU 할당량 제한을 적용합니다.
ms.date: 12/02/2021
ms.topic: how-to
ms.openlocfilehash: d9f1c9a1f69248b3c5d2842ed25fbdabe29d0852
ms.sourcegitcommit: 1e9139680ca51f55ac965c4dd6dd82bf2fd43675
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2021
ms.locfileid: "133542252"
---
# <a name="increase-a-vm-family-vcpu-quota-for-the-classic-deployment-model"></a>클래식 배포 모델에 대 한 VM 패밀리 vCPU 할당량 늘리기

클래식 배포 모델은 이전 세대 Azure 배포 모델입니다. 가상 머신 및 가상 머신 확장 집합에 대한 전역 vCPU 할당량 제한을 적용합니다. 클래식 배포 모델은 더 이상 권장되지 않으며 이제 Resource Manager 모델로 대체되었습니다.

이러한 두 배포 모델에 대한 자세한 내용 및 Resource Manager 사용의 이점에 대한 자세한 내용은 [Resource Manager 및 클래식 배포](../../azure-resource-manager/management/deployment-models.md)를 참조하세요.

새 구독을 만들 때 vCPU의 기본 할당량이 할당됩니다. 클래식 배포 모델을 사용 하 여 새 가상 컴퓨터를 배포할 때마다 모든 지역에서 신규 및 기존 vCPU 사용량의 합계는 클래식 배포 모델에 대해 승인 된 vCPU 할당량을 초과 하지 않아야 합니다.

**도움말 + 지원** 또는 **사용량 + 할당량** 을 사용 하 여 Azure Portal에서 클래식 배포 모델에 대 한 vcpu 할당량 향상을 요청할 수 있습니다.

## <a name="request-quota-increase-for-the-classic-deployment-model-using-help--support"></a>도움말 + 지원 기능을 사용 하 여 클래식 배포 모델에 대 한 요청 할당량 증가

다음 지침에 따라 Azure Portal에서 **도움말 + 지원** 을 사용 하 여 클래식 배포 모델에 대 한 vcpu 할당량 증가 요청을 만듭니다.

1. [Azure Portal](https://portal.azure.com)에 로그인 하 고 [새 지원 요청을 엽니다](how-to-create-azure-support-request.md).

1. **문제 유형** 에 대해 **서비스 및 구독 제한(할당량)** 을 선택합니다.

1. 할당량을 늘려야 하는 구독을 선택합니다.

1. **할당량 유형** 의 경우 **Compute VM(cores-vCPUs) 구독 제한이 증가** 를 선택합니다. 그런 후 **다음** 을 선택합니다.

   :::image type="content" source="media/resource-manager-core-quotas-request/new-per-vm-quota-request.png" alt-text="Azure Portal에서 VM 패밀리 vCPU 할당량을 늘리는 지원 요청을 보여 주는 스크린샷":::

1. **문제 세부 정보** 섹션에서 **세부 정보 입력** 을 선택 합니다. 배포 모델에 대해 **클래식** 을 선택한 다음 위치를 선택 합니다.

1. **SKU 제품군** 에 대해 늘릴 SKU 제품군을 하나 이상 선택합니다.

1. 구독에 대한 새 한도를 입력합니다. 완료 되 면 저장을 선택 하 **고 계속** 을 선택 하 여 지원 요청을 계속 만듭니다.

1. **추가 정보** 화면의 나머지를 완료 하 고 **다음** 을 선택 합니다.

1. **검토 + 만들기** 화면에서 지원 하도록 보낼 세부 정보를 검토 한 다음 **만들기** 를 선택 합니다.

## <a name="request-quota-increase-for-the-classic-deployment-model-from-usage--quotas"></a>사용량 + 할당량에서 클래식 배포 모델에 대 한 할당량 증가 요청

아래 지침에 따라 Azure Portal의 **사용량 + 할당량** 에서 클래식 배포 모델에 대 한 vcpu 할당량 증가 요청을 만듭니다.

1. https://portal.azure.com 에서 **구독** 을 검색하여 선택합니다.

1. 할당량을 늘려야 하는 구독을 선택합니다.

1. **사용량 + 할당량** 을 선택합니다.

1. 오른쪽 위 모서리에서 **증가 요청** 을 선택합니다.

1. 위의 단계 (4 단계부터 시작)에 따라 요청을 완료 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](../../azure-resource-manager/management/azure-subscription-service-limits.md)에 대해 알아봅니다.
- [리소스 관리자 배포 모델](../../azure-resource-manager/management/deployment-models.md)사용의 이점에 대해 알아봅니다.

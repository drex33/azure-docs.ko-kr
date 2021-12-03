---
title: 네트워킹 할당량 늘리기
description: Azure Portal에서 네트워킹 할당량 증가를 요청 하는 방법을 알아봅니다.
ms.date: 12/02/2021
ms.topic: how-to
ms.openlocfilehash: f8f3b265767e8dff4e913f41de17e870f18eddc4
ms.sourcegitcommit: 5b25f76d0fd0ffb6784a2afab808fa55b3eac07b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2021
ms.locfileid: "133519168"
---
# <a name="increase-networking-quotas"></a>네트워킹 할당량 늘리기

이 문서에서는 [Azure Portal](https://portal.azure.com)에서 VM 패밀리 vcpu 할당량에 대 한 향상을 요청 하는 방법을 보여 줍니다.

Azure Portal에서 현재 네트워킹 사용 및 할당량을 보려면 구독을 연 다음 **사용량 + 할당량** 을 선택 합니다. 다음 옵션을 사용하여 네트워크 사용량 및 제한을 확인할 수도 있습니다.

* [사용량 CLI](/cli/azure/network#az_network_list_usages)
* [PowerShell](/powershell/module/azurerm.network/get-azurermnetworkusage)
* [네트워크 사용량 API](/rest/api/virtualnetwork/virtualnetworks/listusage)

**도움말 + 지원** 또는 구독에 대 한 **사용량 + 할당량** 을 사용 하 여 Azure Portal 증가를 요청할 수 있습니다.

> [!Note]
> **공개 IP 접두사** 의 기본 크기를 변경하려면 드롭다운 목록에서 **최소 공용 IP 네트워크 간 접두사 길이** 를 선택합니다.

## <a name="request-networking-quota-increase-at-subscription-level-using-help--support"></a>도움말 + 지원을 사용하여 구독 수준에서 네트워킹 할당량 증가 요청

아래 지침에 따라 Azure Portal에서 **도움말 + 지원** 을 사용하여 지원 요청을 만듭니다.

1. [Azure Portal](https://portal.azure.com)에 로그인 하 고 [새 지원 요청을 엽니다](how-to-create-azure-support-request.md).

1. **문제 유형** 에 대해 **서비스 및 구독 제한(할당량)** 을 선택합니다.

1. 할당량을 늘려야 하는 구독을 선택합니다.

1. **할당량 유형** 에서 **네트워킹** 을 선택합니다. 그런 후 **다음** 을 선택합니다.

   :::image type="content" source="media/networking-quota-request/new-networking-quota-request.png" alt-text="Azure Portal의 새 네트워킹 할당량 증가 요청의 스크린샷":::

1. **문제 세부 정보** 섹션에서 **세부 정보 입력** 을 선택 합니다. 프롬프트에 따라 배포 모델, 위치, 요청에 포함할 리소스 및 해당 리소스에 대 한 구독에서 원하는 새 제한을 선택 합니다. 완료 되 면 저장을 선택 하 **고 계속** 을 선택 하 여 지원 요청을 만듭니다.

    :::image type="content" source="media/networking-quota-request/network-new-limits.png" alt-text="Azure Portal 네트워킹 할당량 증가 요청에 대 한 할당량 정보 화면의 스크린샷":::

1. **추가 정보** 화면의 나머지를 완료 하 고 **다음** 을 선택 합니다.

1. **검토 + 만들기** 화면에서 지원 하도록 보낼 세부 정보를 검토 한 다음 **만들기** 를 선택 합니다.

## <a name="request-networking-quota-increase-at-subscription-level-using-usages--quotas"></a>사용량 + 할당량을 사용하여 구독 수준에서 네트워킹 할당량 증가 요청

다음 지침에 따라 Azure Portal에서 **사용량 + 할당량** 을 사용하여 지원 요청을 만듭니다.

1. https://portal.azure.com 에서 **구독** 을 검색하여 선택합니다.

1. 할당량을 늘려야 하는 구독을 선택합니다.

1. **사용량 + 할당량** 을 선택합니다.

1. 오른쪽 위 모서리에서 **증가 요청** 을 선택합니다.

1. 위의 단계 (4 단계부터 시작)에 따라 요청을 완료 합니다.

## <a name="next-steps"></a>다음 단계

- [네트워킹 제한](../../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits)에 대 한 세부 정보를 검토 합니다.
- [Azure 구독 및 서비스 제한, 할당량 및 제약 조건](/azure/azure-resource-manager/management/azure-subscription-service-limits)에 대해 알아봅니다.

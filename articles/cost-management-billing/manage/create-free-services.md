---
title: Azure 체험 계정으로 평가판 서비스 만들기
description: Azure 무료 계정에 포함된 서비스를 만드는 방법을 알아봅니다. 이러한 서비스를 사용할 수 있는 모든 지역에서 만들 수 있습니다.
author: bandersmsft
ms.reviewer: amberrb
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 10/28/2021
ms.author: banders
ms.openlocfilehash: 34a176c4365f124029149a9663ea1a13409064a1
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131457965"
---
# <a name="create-services-included-with-azure-free-account"></a>Azure 무료 계정에 포함된 서비스 만들기

Azure 체험 계정을 만든 후 첫 30일 동안 타사 Marketplace 구매를 제외한 모든 서비스에서 사용할 수 있도록 청구 통화로 200달러 크레딧이 제공됩니다. 체험 크레딧을 사용하여 다양한 계층 및 유형의 Azure 서비스를 실험하여 Azure를 사용해 볼 수 있습니다. 해당 기간 동안 무료가 아닌 서비스 또는 Azure 리소스를 사용하는 경우 요금은 크레딧에서 공제됩니다.

처음 30일이 지나도록 크레딧을 모두 사용하지 않으면 손실됩니다. 처음 30일 후 및 등록 후 최대 12개월 후에는 *일부 서비스* 의 제한된 수량만 사용할 수 있습니다. 모든 Azure 서비스가 무료는 아닙니다. 30일 이전에 업그레이드하고 크레딧이 남아 있는 경우 남은 기간 동안 종량제 구독으로 나머지 크레딧을 사용할 수 있습니다. 예를 들어 11월 1일에 체험 계정에 가입하고 11월 5일에 업그레이드하는 경우 11월 30일까지 새 종량제 구독에서 크레딧을 사용할 수 있습니다. 

Azure 체험 계정에는 12개월 동안 *지정된 수량* 의 무료 서비스와 항상 무료로 제공되는 서비스 세트가 포함됩니다. 일부 서비스 계층만 특정 수량 내에서 무료로 제공됩니다. 예를 들어 Azure에는 다양한 요구 사항을 충족하는 여러 가상 머신이 있습니다. 체험 계정에는 한 가지 유형의 VM(월별 최대 750시간 동안 사용할 수 있는 B1S Burstable B 시리즈)에 대한 액세스만 무료로 포함됩니다. 체험 계정 제한을 유지하면 다양한 구성에서 무료 서비스를 사용할 수 있습니다. Azure 체험 계정 및 무료로 제공되는 제품에 대한 자세한 내용은 [Azure 체험 계정 FAQ](https://azure.microsoft.com/free/free-account-faq/)를 참조하세요.

## <a name="create-free-services-in-the-azure-portal"></a>Azure Portal에서 무료 서비스 만들기

Azure Portal의 [무료 서비스 페이지](https://go.microsoft.com/fwlink/?linkid=859151)를 사용하여 무료 서비스를 만드는 것이 좋습니다. 또는 [Azure Portal](https://portal.azure.com)에 로그인하여 **평가판 서비스** 를 검색할 수 있습니다. 무료 서비스 페이지 외부에서 리소스를 만드는 경우 체험 계층 또는 체험 리소스 구성 옵션이 항상 기본적으로 선택되는 것은 아닙니다. 요금이 청구되지 않도록 하려면 무료 서비스 페이지에서 리소스를 만들어야 합니다. 그런 다음, 리소스를 만들 때 체험 계층을 선택해야 합니다.

![평가판 서비스 페이지를 보여주는 스크린샷](./media/create-free-services/billing-freeservices-grid.png)

## <a name="services-can-be-created-in-any-region"></a>모든 지역에서 서비스를 만들 수 있음

제한을 벗어나지만 않으면 서비스를 사용할 수 있는 모든 지역에서 무료로 서비스를 만들 수 있습니다. 예를 들어 Azure 체험 계정을 사용하여 매월 750시간 동안 B1S Windows 가상 머신을 무료로 사용할 수 있습니다. B-시리즈 가상 머신을 사용할 수 있는 모든 지역에서 가상 머신을 만들 수 있습니다. 또한 750시간을 초과하지 않는 한, 요금은 청구되지 않습니다. 예를 들어 미국의 고객은 서유럽에서 B1S Windows 가상 머신을 프로비전할 수 있고 750시간 동안 무료로 사용할 수 있습니다.

지역별 Azure 서비스 가용성에 대한 자세한 내용은 [지역별 사용 가능한 제품](https://azure.microsoft.com/regions/services/)을 참조하세요.

## <a name="create-multiple-service-instances-in-allowed-limits"></a>허용된 한도에서 여러 서비스 인스턴스 만들기

총 사용량이 사용 한도 내에 있으면 무료로 여러 서비스 인스턴스를 만들 수 있습니다. 예를 들어 Azure 무료 계정을 사용하여 매월 750시간 동안 B1S Windows 가상 머신을 무료로 사용할 수 있습니다. 원하는 모든 조합으로 750시간을 사용합니다. B1S Windows 가상 머신을 5개 만들고 각각 150시간씩 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure 체험 계정에 포함된 평가판 서비스의 사용량을 확인](check-free-service-usage.md)하는 방법에 대해 알아봅니다.
- [Azure 체험 계정에 대한 요금 청구를 방지](avoid-charges-free-account.md)하는 방법을 알아봅니다.

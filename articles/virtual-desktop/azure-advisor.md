---
title: Azure Advisor와 Azure Virtual Desktop 통합 - Azure
description: Azure Virtual Desktop 배포에서 Azure Advisor를 사용하는 방법입니다.
author: Heidilohr
ms.topic: how-to
ms.date: 03/31/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 25bdeb3db8872550a4f1f03e7165cd24653272c1
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111745352"
---
# <a name="use-azure-advisor-with-azure-virtual-desktop"></a>Azure Virtual Desktop에서 Azure Advisor 사용

Azure Advisor는 지원 사례를 제출하지 않고도 사용자가 일반적인 문제를 스스로 해결하는 데 도움이 될 수 있습니다. 권장 사항은 지원 요청을 제출할 필요성을 줄여 시간과 비용을 절약합니다.

이 문서에서는 사용자를 돕기 위해 Azure Virtual Desktop 배포에서 Azure Advisor를 설정하는 방법을 알려줍니다.

## <a name="what-is-azure-advisor"></a>Azure Advisor란?

Azure Advisor는 구성 및 원격 분석을 분석하여 일반적인 문제를 해결하기 위한 맞춤형 권장 사항을 제공합니다. 이 권장 사항을 사용하면 안정성, 보안, 운영 효율성, 성능, 비용에 맞게 Azure 리소스를 최적화할 수 있습니다. [Azure Advisor 웹 사이트](https://azure.microsoft.com/services/advisor/)에서 자세히 알아보세요.

## <a name="how-to-start-using-azure-advisor"></a>Azure Advisor 사용을 시작하는 방법

시작하는 데는 Azure Portal에서 Azure 계정만 있으면 됩니다. 먼저 <https://portal.azure.com/#home>에서 Azure Portal을 연 후 다음 이미지와 같이 **Azure 서비스** 에서 **Advisor** 를 선택합니다. Azure Portal의 검색 창에 “Azure Advisor”를 입력할 수도 있습니다.

> [!div class="mx-imgBorder"]
> ![Azure Portal 스크린샷 사용자가 마우스 커서로 Azure Advisor 링크를 가리키면 드롭다운 메뉴가 표시됨](media/azure-advisor.png)

Azure Advisor를 열면 다음 5개 범주가 표시됩니다.

- Cost
- 보안
- 안정성
- 운영 효율성
- 성능

> [!div class="mx-imgBorder"]
> ![각 범주의 5개 메뉴를 보여 주는 Azure Advisor의 스크린샷. 비용, 보안, 안정성, 운영 효율성, 성능 등 5개 항목이 각 박스에 표시됨](media/advisor-categories.png)

범주를 선택하면 활성 권장 사항 페이지로 이동합니다. 이 페이지에서는 다음 이미지와 같이 Azure Advisor가 제공하는 권장 사항을 볼 수 있습니다.

> [!div class="mx-imgBorder"]
> ![운영 효율성에 대한 활성 권장 사항 목록의 스크린샷. 목록에는 다양한 우선 순위 수준의 7개 권장 사항이 표시됨](media/active-suggestions.png)

## <a name="additional-tips-for-azure-advisor"></a>Azure Advisor에 관한 추가 팁

- 권장 사항은 1주일에 두 번 이상 자주 확인해야 합니다. Azure Advisor는 활성 권장 사항을 하루에 여러 번 업데이트합니다. 새 권장 사항을 확인하면 더 작은 문제를 파악하고 해결하는 데 도움이 되므로 더 큰 문제를 방지할 수 있습니다.

- 항상 Azure Advisor에서 가장 높은 우선 순위 수준으로 문제를 해결해봅니다. 높은 우선 순위 문제는 빨간색으로 표시됩니다. 높은 우선 순위 권장 사항을 확인되지 않은 상태로 두면 완전히 문제가 발생할 수 있습니다.

- 권장 사항이 별로 중요하지 않은 것으로 보이면 이를 해제하거나 연기할 수 있습니다. 권장 사항을 해제하거나 연기하려면 **작업** 열로 이동하고 항목의 상태를 변경합니다.

- 권장 사항이 표시되는 이유를 확인하고 자신이나 사용자에게 부정적인 영향을 주지 않을 때까지 권장 사항을 해제하지 마세요. 항상 **자세한 정보** 를 선택하여 문제가 무엇인지 확인합니다. Azure Advisor의 지침에 따라 문제를 해결하면 문제가 목록에서 자동으로 사라집니다. 반복해서 연기하는 것보다 문제를 해결하는 것이 더 좋습니다.

- Azure Virtual Desktop에서 문제가 발생할 때마다 항상 먼저 Azure Advisor를 확인합니다. Azure Advisor는 문제를 해결하는 방법에 관한 지침을 제공하거나 최소한 도움이 될 수 있는 리소스를 제공합니다.

## <a name="next-steps"></a>다음 단계

권장 사항을 해결하는 방법을 알아보려면 [Azure Advisor 권장 사항 해결 방법](azure-advisor-recommendations.md)을 참조하세요.

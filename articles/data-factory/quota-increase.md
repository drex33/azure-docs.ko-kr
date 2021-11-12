---
title: 지원에서 할당량 증가 요청
description: Azure Data Factory 대한 Azure Portal 지원 요청을 만들어 할당량 증가를 요청하거나 문제 해결 지원을 받는 방법입니다.
ms.service: data-factory
ms.topic: conceptual
ms.subservice: troubleshooting
ms.date: 03/10/2020
author: jonburchel
ms.author: jburchel
ms.openlocfilehash: a6cffaf7b1f49e747b2d160318673dd1a07b9885
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132352773"
---
# <a name="request-quota-increases-and-get-support-for-azure-data-factory"></a>할당량 증가 요청 및 Azure Data Factory 대한 지원 받기

이 문서에서는 Azure Data Factory Azure Portal 지원 티켓을 제출하는 방법을 설명합니다. 이 프로세스를 통해 사용자는 할당량 증가를 요청하거나 엔지니어링 지원 팀에 대한 기술 지원 요청을 제출할 수 있습니다.

## <a name="create-a-support-ticket"></a>지원 티켓 만들기

다음 단계를 사용하여 Azure Data Factory 대한 Azure Portal 새 지원 요청을 만듭니다.

1. [Azure Portal](https://portal.azure.com) 메뉴에서 **도움말 + 지원** 을 선택합니다.

   ![도움말 + 지원 링크](./media/quota-increase/help-plus-support.png)


1. **도움말 + 지원** 에서 **새 지원 요청** 을 선택합니다.

    :::image type="content" source="./media/quota-increase/new-support-request.png" alt-text="새 지원 요청 만들기":::

1. [Azure 지원 플랜](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/)을 검토합니다.

   * **청구, 할당량 및 구독 관리** 지원은 모든 지원 수준에서 제공됩니다.
   * **고장 수리** 지원은 [개발자](https://azure.microsoft.com/support/plans/developer/), [표준](https://azure.microsoft.com/support/plans/standard/), [전문가 지원](https://azure.microsoft.com/support/plans/prodirect/) 또는 [프리미어](https://azure.microsoft.com/support/plans/premier/) 지원을 통해 제공됩니다. 고장 수리 문제는 고객이 Azure를 사용하는 동안 고객에게 발생한 문제 중 Microsoft로 인해 문제가 발생했다는 합리적인 이유가 존재하는 문제를 의미합니다.
   * **개발자 멘토링** 및 **자문 서비스** 는 [전문가 지원](https://azure.microsoft.com/support/plans/prodirect/) 및 [프리미어](https://azure.microsoft.com/support/plans/premier/) 지원 수준에서 제공됩니다.

   프리미어 지원 플랜이 있는 경우 Microsoft 프리미어 온라인 포털 에서 Azure Data Factory 문제를 보고할 수도 [있습니다.](https://premier.microsoft.com/) 범위, 응답 시간, 가격 책정 등을 포함한 다양한 지원 계획에 대한 자세한 정보는 [Azure 지원 플랜](https://azure.microsoft.com/support/plans/?WT.mc_id=Support_Plan_510979/)을 참조하세요.  Azure 지원에 대한 질문과 대답은 [Azure 지원 FAQ](https://azure.microsoft.com/support/faq/)를 참조하세요.

1. **문제 유형** 에서 적절한 문제 유형을 선택합니다. 고장 수리 문제의 경우 **기술** 을 선택합니다. 할당량 증가 요청의 경우 **서비스 및 구독 제한(할당량)** 을 선택합니다.

   :::image type="content" source="./media/quota-increase/select-quota-issue-type.png" alt-text="문제 유형 선택":::  

   > [!NOTE]
   > 이 문서의 나머지 부분에서는 할당량 증가 요청을 집중적으로 살펴봅니다. 하지만 여기에서 문제 해결 지원 요청에 대해 **기술** 을 선택할 수도 있습니다. **기술** 을 선택하는 경우 요약을 입력한 다음, **문제 유형 선택** 을 선택하여 문제 유형을 식별하라는 메시지가 표시됩니다. 문제를 해결하는 데 도움이 되는 솔루션을 볼 수 있습니다. 제공된 해결 방법으로 문제가 해결되지 않으면 **다음: 세부 정보** 를 선택하고 양식을 완료하여 지원 티켓을 제출합니다.

1. 할당량 증가 요청의 경우 **할당량 유형에** **대해 Data Factory** 선택합니다. 그런 후 **다음** 을 선택합니다.

   :::image type="content" source="./media/quota-increase/select-quota-type.png" alt-text="할당량 유형 선택":::

1. 추가 **세부 정보** 창에서 증가해야 하는 특정 할당량 한도에 대한 추가 정보를 입력합니다.  특정 [제한은 Azure Data Factory 제한](../azure-resource-manager/management/azure-subscription-service-limits.md#data-factory-limits) 문서를 참조하세요.  원하는 연락 방법을 선택하고 **다음을** 클릭합니다.

   :::image type="content" source="./media/quota-increase/provide-details.png" alt-text="&quot;세부 정보 제공 &quot; 섹션":::

## <a name="submit-your-request"></a>요청 제출

최종 페이지에서 요청 세부 정보를 검토한 후 **만들기를** 선택하여 요청을 제출합니다.

## <a name="monitor-a-support-ticket"></a>지원 티켓 모니터링

지원 요청을 제출하면 Azure 지원팀이 연락합니다. 요청 상태 및 세부 정보를 확인하려면 대시보드에서 **모든 지원 요청** 을 선택합니다.

:::image type="content" source="./media/quota-increase/monitor-ticket.png" alt-text="상태 확인":::

## <a name="other-resources"></a>기타 리소스

Stack Overflow 또는 Azure Data Factory 대한 Microsoft Q [&](https://stackoverflow.com/questions/tagged/azure-data-factory) [A 질문 페이지를](/answers/topics/azure-data-factory.html)통해 Azure Data Factory 커뮤니티에 연결할 수도 있습니다.
---
title: Microsoft Defender for Cloud에서 보안 경고 관리 | Microsoft Docs
description: 이 문서는 클라우드 기능을 통해 Microsoft Defender를 사용 하 여 보안 경고를 관리 하 고 대응 하는 데 도움이 됩니다.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 02/17/2021
ms.author: memildin
ms.custom: ignite-fall-2021
ms.openlocfilehash: fcf92ce079d4e51d448ba199a505706f9e0d5b1d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131103106"
---
# <a name="manage-and-respond-to-security-alerts-in-microsoft-defender-for-cloud"></a>Microsoft Defender for Cloud에서 보안 경고 관리 및 대응

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

이 항목에서는 클라우드 경고에 대 한 Defender를 보고 처리 하 고 리소스를 보호 하는 방법을 보여 줍니다.

보안 경고를 트리거하는 고급 검색은 클라우드의 향상 된 보안 기능을 사용 하도록 설정 된 Microsoft Defender 에서만 사용할 수 있습니다. 평가판을 사용할 수 있습니다. 업그레이드 하려면 향상 된 [보호 사용](enable-enhanced-security.md)을 참조 하세요.

## <a name="what-are-security-alerts"></a>보안 경고란?
Defender for Cloud는 방화벽 및 끝점 보호 솔루션과 같은 Azure 리소스, 네트워크 및 연결 된 파트너 솔루션의 로그 데이터를 자동으로 수집, 분석 및 통합 하 여 실제 위협을 검색 하 고 가양성을 줄입니다. 우선 순위가 지정 된 보안 경고의 목록은 문제를 신속 하 게 조사 하 고 공격을 해결 하기 위해 수행 해야 하는 단계와 함께 클라우드에 대해 Defender에 표시 됩니다.

다양한 유형의 경고에 대해 자세히 알아보려면 [보안 경고 - 참조 가이드](alerts-reference.md)를 참조하세요.

Defender for Cloud가 경고를 생성 하는 방법에 대 한 개요는 [클라우드 용 Microsoft Defender가 위협 검색 및 대응 하는 방법](alerts-overview.md)을 참조 하세요.


## <a name="manage-your-security-alerts"></a>보안 경고 관리

1. Defender for Cloud의 개요 페이지에서 페이지 맨 위에 있는 **보안 경고** 타일 또는 사이드바의 링크를 선택 합니다.

    :::image type="content" source="media/managing-and-responding-alerts/overview-page-alerts-links.png" alt-text="클라우드의 개요 페이지에 대 한 Microsoft Defender의 보안 경고 페이지로 가져오기":::

    보안 경고 페이지가 열립니다.

    :::image type="content" source="media/managing-and-responding-alerts/alerts-page.png" alt-text="클라우드의 Microsoft Defender 보안 경고 목록":::

1. 경고 목록을 필터링하려면 관련 필터를 선택합니다. 필요에 따라 필터 추가 옵션을 통해 필터를 더 **추가** 할 수 있습니다.

    :::image type="content" source="./media/managing-and-responding-alerts/alerts-adding-filters-small.png" alt-text="경고 보기에 필터 추가." lightbox="./media/managing-and-responding-alerts/alerts-adding-filters-large.png":::

    선택한 필터링 옵션에 따라 목록이 업데이트됩니다. 필터링은 매우 유용할 수 있습니다. 예를 들어 시스템에서 잠재적 위반을 조사하고 있기 때문에 최근 24시간 동안 발생한 보안 경고를 해결하려고 할 수 있습니다.


## <a name="respond-to-security-alerts"></a>보안 경고에 대응

1. **보안 경고** 목록에서 경고를 선택합니다. 사이드 창이 열리고 영향을 받는 모든 리소스 및 경고에 대한 설명이 표시됩니다. 

    :::image type="content" source="./media/managing-and-responding-alerts/alerts-details-pane.png" alt-text="보안 경고의 미니 세부 정보 보기.":::

    > [!TIP]
    > 이 측면 창이 열리면 키보드의 위쪽 및 아래쪽 화살표를 사용하여 경고 목록을 빠르게 검토할 수 있습니다.

1. 자세한 내용을 보려면 **전체 세부 정보 보기** 를 선택합니다.

    보안 경고 페이지의 왼쪽 창에는 보안 경고의 제목, 심각도, 상태, 작업 시간, 의심스러운 활동 설명, 영향을 받는 리소스에 대한 높은 수준의 정보가 표시됩니다. 영향을 받는 리소스 옆에는 리소스와 관련된 Azure 태그가 있습니다. 이를 사용하여 경고를 조사할 때 리소스의 조직 컨텍스트를 유추합니다.

    오른쪽 창에는 문제를 조사하는 데 도움을 주도록 경고에 대한 자세한 정보를 포함하는 **경고 세부 정보** 탭(IP 주소, 파일, 프로세스 등)이 포함되어 있습니다.
     
    ![보안 경고와 관련해 수행할 작업의 제안 사항.](./media/managing-and-responding-alerts/security-center-alert-remediate.png)

    또한 오른쪽 창에는 **작업 수행** 탭이 있습니다. 이 탭을 이용해 보안 경고와 관련된 추가 작업을 하세요. 작업은 다음과 같습니다.
    - *위협 완화* - 보안 경고에 대하여 수동으로 수정할 수 있는 단계를 제공합니다.
    - *향후 공격 방지* - 공격 노출 영역을 줄이고 보안 상태를 강화하여 향후 공격을 방지하는 데 도움이 되는 보안 권장 사항을 제공합니다.
    - *자동 응답 트리거* - 보안 경고에 대한 대응으로 논리 앱을 트리거하는 옵션을 제공합니다.
    - 유사한 경고 표시 안 함 - 경고가 조직과 관련이 없는 경우 유사한 특성을 가진 경고는 표시하지 않는 옵션을 제공합니다.

    ![작업 수행 탭.](./media/managing-and-responding-alerts/alert-take-action.png)




## <a name="see-also"></a>참조

이 문서에서는 보안 경고를 보는 방법에 대해 알아보았습니다. 관련 자료는 다음 페이지를 참조하세요.

- [경고 제거 규칙 구성](alerts-suppression-rules.md)
- [클라우드 트리거에 대 한 Defender에 대 한 응답 자동화](workflow-automation.md)
- [보안 경고 - 참조 가이드](alerts-reference.md)

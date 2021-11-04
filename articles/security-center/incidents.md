---
title: Microsoft Defender for Cloud | 보안 인시던트 관리 Microsoft Docs
description: 이 문서는 Microsoft Defender for Cloud를 사용하여 보안 인시던트 관리를 도와줍니다.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 02/17/2021
ms.author: memildin
ms.openlocfilehash: 739c7264a3a7199ad5dfd36a20267d86fd582a23
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131452968"
---
# <a name="manage-security-incidents-in-microsoft-defender-for-cloud"></a>Microsoft Defender for Cloud에서 보안 인시던트 관리

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

보안 경고를 심사 및 조사하는 작업은 가장 숙련된 보안 분석가라도 많은 시간이 걸릴 수 있습니다. 어디서부터 시작해야 할지 알지 못하는 경우도 많습니다. 

Defender for [Cloud는 분석을](./alerts-overview.md) 사용하여 고유한 보안 경고 간에 정보를 [연결합니다.](managing-and-responding-alerts.md) 이러한 연결을 사용하여 Defender for Cloud는 공격자의 작업 및 영향을 받는 리소스를 이해하는 데 도움이 되는 공격 캠페인 및 관련 경고에 대한 단일 보기를 제공할 수 있습니다.

이 페이지에서는 Defender for Cloud의 인시던트 개요를 제공합니다.

## <a name="what-is-a-security-incident"></a>보안 인시던트란?

Defender for Cloud에서 보안 인시던트 는 [킬 체인](alerts-reference.md#intentions) 패턴에 부합하는 리소스에 대한 모든 경고의 집계입니다. 인시던트는 [보안 경고](managing-and-responding-alerts.md) 페이지에 표시됩니다. 인시던트를 선택하여 관련 경고를 확인하고 자세한 정보를 확인하세요.

## <a name="managing-security-incidents"></a>보안 인시던트 관리

1. Defender for Cloud의 경고 페이지에서 **필터 추가** 단추를 사용하여 경고 이름을 **기준으로 여러 리소스에서 검색된 보안 인시던트** 경고 이름으로 필터링합니다. 

    :::image type="content" source="media/incidents/locating-incidents.png" alt-text="Microsoft Defender for Cloud의 경고 페이지에서 인시던트 찾기":::

    이제 인시던트만 표시되도록 목록이 필터링됩니다. 보안 인시던트에는 보안 경고와 다른 아이콘이 있습니다.

    :::image type="content" source="media/incidents/incidents-list.png" alt-text="Microsoft Defender for Cloud의 경고 페이지에 있는 인시던트 목록입니다.":::

1. 인시던트의 세부 정보를 보려면 목록에서 인시던트를 선택합니다. 인시던트의 자세한 세부 정보가 있는 측면 창이 표시됩니다.

    :::image type="content" source="media/incidents/incident-quick-peek.png" alt-text="인시던트의 세부 정보를 보여 주는 측면 창.":::

1. 자세한 내용을 보려면 **전체 세부 정보 보기** 를 선택합니다.

    [![Microsoft Defender for Cloud의 보안 인시던트 대응](media/incidents/incident-details.png)](media/incidents/incident-details.png#lightbox)

    보안 인시던트 페이지의 왼쪽 창에는 보안 인시던트에 대한 높은 수준의 정보(제목, 심각도, 상태, 작업 시간, 설명, 영향을 받는 리소스)가 표시됩니다. 영향을 받는 리소스 옆에서 관련 Azure 태그를 볼 수 있습니다. 해당 태그를 사용하여 경고를 조사할 때 리소스의 조직 컨텍스트를 유추합니다.

    오른쪽 창에는 상관 관계가 있는 보안 경고가 이 인시던트의 일부로 포함된 **경고** 탭이 있습니다. 

    >[!TIP]
    > 특정 경고에 대한 자세한 내용을 보려면 해당 경고를 선택합니다. 

    [![인시던트의 작업 수행 탭.](media/incidents/incident-take-action-tab.png)](media/incidents/incident-take-action-tab.png#lightbox)

    **작업 수행** 탭으로 전환하려면 탭이나 오른쪽 창 아래의 단추를 선택합니다. 이 탭을 사용하여 다음과 같은 추가 작업을 수행할 수 있습니다.
    - 위협 완화 - 이 보안 인시던트에 대한 수동 수정 단계를 제공합니다.
    - 향후 공격 방지 - 공격 노출 영역을 줄이고 보안 태세를 높이며 향후 공격을 방지하는 데 도움이 되는 보안 권장 사항을 제공합니다.
    - 자동 응답 트리거 - 이 보안 인시던트에 대한 대응으로 논리 앱을 트리거하는 옵션을 제공합니다.
    - 유사한 경고 표시 안 함 - 경고가 조직과 관련이 없는 경우 유사한 특성을 가진 경고는 표시하지 않는 옵션을 제공합니다. 

   > [!NOTE]
   > 동일한 경고가 인시던트의 일부로 존재할 뿐만 아니라 독립 실행형 경고로 표시될 수 있습니다.

1. 인시던트의 위협을 수정하려면 각 경고와 함께 제공되는 수정 단계를 따릅니다.


## <a name="next-steps"></a>다음 단계

이 페이지에서는 Defender for Cloud의 보안 인시던트 기능을 설명했습니다. 관련 정보는 다음 페이지를 참조하세요.

- [Defender for Cloud의 보안 경고](alerts-overview.md)
- [보안 경고 관리 및 응답](managing-and-responding-alerts.md)
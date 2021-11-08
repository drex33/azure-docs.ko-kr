---
title: Microsoft Defender for Cloud의 기본 대시보드 또는 '개요' 페이지
description: Defender for Cloud 개요 페이지의 기능에 대해 알아보기
author: memildin
ms.author: memildin
ms.date: 11/02/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 640b6fedd532174ff86e222a7479007b9e72b5bc
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131437831"
---
# <a name="microsoft-defender-for-clouds-overview-page"></a>Microsoft Defender for Cloud의 개요 페이지

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Defender for Cloud를 열 때 첫 번째로 나타나는 페이지가 개요 페이지입니다. 

이 대화형 대시보드는 하이브리드 클라우드 워크로드의 보안 상태에 대한 통합된 보기를 제공합니다. 또한 보안 경고, 적용 범위 정보 등을 보여 줍니다.

페이지에서 원하는 요소를 선택하여 자세한 정보를 가져올 수 있습니다.

:::image type="content" source="./media/overview-page/overview.png" alt-text="Defender for Cloud 개요 페이지의 스크린샷" lightbox="./media/overview-page/overview.png":::
## <a name="features-of-the-overview-page"></a>개요 페이지의 기능

:::image type="content" source="./media/overview-page/top-bar-of-overview.png" alt-text="Defender for Cloud 개요 페이지의 위쪽 표시줄 스크린샷":::

### <a name="metrics"></a>메트릭
**상단 메뉴 모음** 은 다음을 제공합니다.
- **구독** - 이 단추를 선택하여 구독 목록을 보고 필터링할 수 있습니다. Defender for Cloud는 선택한 구독의 보안 상태를 반영하도록 디스플레이를 조정합니다.
- **새로운 기능** - 새로운 기능, 버그 수정 및 더 이상 사용되지 않는 기능에 대한 최신 정보를 습득할 수 있도록 [릴리스 정보](release-notes.md)를 엽니다.
- 연결된 클라우드 계정의 **대략적인 수치** - 아래의 기본 타일에 정보의 컨텍스트를 보여 줍니다. 평가된 리소스 수, 활성 권장 사항 및 보안 경고. [자산 인벤토리에](asset-inventory.md)액세스할 평가된 리소스 번호를 선택합니다. [AWS 계정](quickstart-onboard-aws.md) 및 [GCP 프로젝트](quickstart-onboard-gcp.md)에 연결하는 방법에 대해 자세히 알아보세요.

### <a name="feature-tiles"></a>기능 타일
페이지의 가운데에는 **기능 타일** 이 있으며, 각 타일은 간략한 프로필 기능 또는 전용 대시보드로 연결됩니다.
- **보안 점수** - Defender for Cloud는 리소스, 구독 및 조직의 보안 문제를 지속적으로 평가합니다. 그런 다음, 현재 보안 상황을 한눈에 파악할 수 있도록 모든 결과를 단일 점수에 집계합니다. 즉, 점수가 높을수록 식별된 위험 수준은 낮습니다. [자세히 알아봅니다](secure-score-security-controls.md).
- **워크로드 보호** - Azure, 온-프레미스 머신 또는 기타 클라우드 공급자에서 실행되는 워크로드를 지능적으로 보호하기 위해 Defender for Cloud 내에 통합된 CWPP(클라우드 워크로드 보호 플랫폼)입니다. 각 리소스 종류에 해당하는 Microsoft Defender 플랜이 있습니다. 이 타일은 현재 선택된 구독의 연결된 리소스 적용 범위와 최근 경고를 심각도에 따라 색으로 구분하여 보여 줍니다. [향상된 보안 기능](enhanced-security-features-overview.md)에 대해 자세히 알아보세요.
- **규정 준수** - Defender for Cloud는 Azure 환경에 대한 지속적인 평가를 기반으로 규정 준수 상태에 대한 인사이트를 제공합니다. Defender for Cloud는 보안 모범 사례에 따라 환경의 위험 요소를 분석합니다. 이러한 평가는 지원되는 표준 세트에서 준수 제어에 매핑됩니다. [자세히 알아봅니다](regulatory-compliance-dashboard.md).
- **Firewall Manager** - 이 타일은 [Azure Firewall Manager](../firewall-manager/overview.md)의 허브 및 네트워크 상태를 보여줍니다.
- **인벤토리** - Microsoft Defender for Cloud의 자산 인벤토리 페이지는 Microsoft Defender for Cloud에 연결한 리소스의 보안 태세를 볼 수 있는 단일 페이지를 제공합니다. 해결되지 않은 보안 권장 사항이 있는 모든 리소스가 인벤토리에 표시됩니다. 엔드포인트용 Microsoft Defender와 통합되도록 설정했고 서버용 Microsoft Defender를 사용하도록 설정한 경우 소프트웨어 인벤토리에도 액세스할 수 있습니다. 개요 페이지의 타일을 보면 현재 선택한 구독의 정상 및 비정상 리소스를 한눈에 파악할 수 있습니다. [자세히 알아보기](asset-inventory.md).
- **정보 보호** - 이 타일의 그래프는 [Azure Purview](../purview/overview.md)에서 검사했더니 중요한 데이터를 포함하고 있으며 미해결 권장 사항 및 경고가 있는 것으로 확인된 리소스 종류를 보여줍니다. **검사** 링크를 따라 Azure Purview 계정에 액세스하고 새 검사를 구성하거나, 타일의 다른 부분을 선택하여 [자산 인벤토리](asset-inventory.md)를 열고 Purview 데이터 민감도 분류에 따라 리소스를 볼 수 있습니다. [자세히 알아보기](information-protection.md).

### <a name="insights"></a>자세한 정보
**Insights** 창에서는 사용자 환경에 맞게 사용자 지정된 다음과 같은 항목을 제공합니다.
- 가장 많이 공격받은 리소스
- 보안 점수를 늘릴 가능성이 가장 높은 [보안 제어](secure-score-security-controls.md)
- 영향을 받는 대부분의 리소스와 관련한 활성 권장 사항
- 클라우드 전문가용 Microsoft Defender의 최근 블로그 게시물

## <a name="next-steps"></a>다음 단계

이 페이지에서는 Defender for Cloud 개요 페이지를 소개했습니다. 관련 정보는 다음을 참조하세요.

- [자산 인벤토리 및 관리 도구를 사용하여 리소스 검색 및 관리](asset-inventory.md)
- [Microsoft Defender for Cloud의 보안 점수](secure-score-security-controls.md)

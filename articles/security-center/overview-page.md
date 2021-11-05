---
title: Microsoft Defender for Cloud의 기본 대시보드 또는 '개요' 페이지
description: Defender for Cloud 개요 페이지의 기능에 대해 알아보기
author: memildin
ms.author: memildin
ms.date: 03/04/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.custom: ignite-fall-2021
ms.openlocfilehash: d0ab115e1e963283a2b8f7e7abd963653b9a39dd
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131084095"
---
# <a name="microsoft-defender-for-clouds-overview-page"></a>Microsoft Defender for Cloud의 개요 페이지

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Defender for Cloud를 열 때 첫 번째로 나타나는 페이지가 개요 페이지입니다. 

이 대화형 대시보드는 하이브리드 클라우드 워크로드의 보안 상태에 대한 통합된 보기를 제공합니다. 또한 보안 경고, 적용 범위 정보 등을 보여 줍니다.

페이지에서 원하는 요소를 선택하여 자세한 정보를 가져올 수 있습니다.

:::image type="content" source="media/overview-page/overview.png" alt-text="Defender for Cloud의 개요 페이지":::

## <a name="features-of-the-overview-page"></a>개요 페이지의 기능

:::image type="content" source="media/overview-page/top-bar-of-overview.png" alt-text="Defender for Cloud의 개요 페이지 위쪽 표시줄":::

**상단 메뉴 모음** 은 다음을 제공합니다.
- **구독** - 이 단추를 선택하여 구독 목록을 보고 필터링할 수 있습니다. Defender for Cloud는 선택한 구독의 보안 상태를 반영하도록 디스플레이를 조정합니다.
- **새로운 기능** - 새로운 기능, 버그 수정 및 더 이상 사용되지 않는 기능에 대한 최신 정보를 습득할 수 있도록 [릴리스 정보](release-notes.md)를 엽니다.
- 연결된 클라우드 계정의 **대략적인 수치** - 아래의 기본 타일에 정보의 컨텍스트를 보여 줍니다. 평가된 리소스 수, 활성 권장 사항 및 보안 경고. [자산 인벤토리에](asset-inventory.md)액세스할 평가된 리소스 번호를 선택합니다. [AWS 계정](quickstart-onboard-aws.md) 및 [GCP 프로젝트](quickstart-onboard-gcp.md)에 연결하는 방법에 대해 자세히 알아보세요.


페이지의 가운데에는 **4개의 중앙 타일** 이 있으며, 각 타일은 각 항목에 대한 자세한 내용을 볼 수 있는 전용 대시보드로 연결됩니다.
- **보안 점수** - Defender for Cloud는 리소스, 구독 및 조직의 보안 문제를 지속적으로 평가합니다. 그런 다음, 현재 보안 상황을 한눈에 파악할 수 있도록 모든 결과를 단일 점수에 집계합니다. 즉, 점수가 높을수록 식별된 위험 수준은 낮습니다. [자세히 알아봅니다](secure-score-security-controls.md).
- **규정 준수** - Defender for Cloud는 Azure 환경에 대한 지속적인 평가를 기반으로 규정 준수 상태에 대한 인사이트를 제공합니다. Defender for Cloud는 보안 모범 사례에 따라 하이브리드 클라우드 환경에서 위험 요소를 분석합니다. 이러한 평가는 지원되는 표준 세트에서 준수 제어에 매핑됩니다. [자세한 정보를 알아보세요](regulatory-compliance-dashboard.md).
- **워크로드 보호** - Azure, 온-프레미스 머신 또는 기타 클라우드 공급자에서 실행되는 워크로드를 지능적으로 보호하기 위해 Defender for Cloud 내에 통합된 CWPP(클라우드 워크로드 보호 플랫폼)입니다. 각 리소스 종류에 해당하는 Microsoft Defender 플랜이 있습니다. 이 타일은 현재 선택된 구독의 연결된 리소스 적용 범위와 최근 경고를 심각도에 따라 색으로 구분하여 보여 줍니다. [Microsoft Defender의 고급 보호 계획](defender-for-cloud-introduction.md)에 대해 자세히 알아봅니다.
- **Firewall Manager** - 이 타일은 [Azure Firewall Manager](../firewall-manager/overview.md)의 허브 및 네트워크 상태를 보여줍니다. 


**Insights** 창에서는 사용자 환경에 맞게 사용자 지정된 다음과 같은 항목을 제공합니다.
- 가장 많이 공격받은 리소스
- 보안 점수를 늘릴 가능성이 가장 높은 보안 제어
- 영향을 받는 대부분의 리소스와 관련한 활성 권장 사항
- 클라우드 전문가용 Microsoft Defender의 최근 블로그 게시물

## <a name="next-steps"></a>다음 단계

이 페이지에서는 Defender for Cloud 개요 페이지를 소개했습니다. 관련 정보는 다음을 참조하세요.

- [자산 인벤토리 및 관리 도구를 사용하여 리소스 검색 및 관리](asset-inventory.md)
- [Microsoft Defender for Cloud의 보안 점수](secure-score-security-controls.md)

---
title: 클라우드 워크 로드 보호 대시보드 및 해당 기능에 대 한 Microsoft Defender
description: 클라우드 워크 로드 보호 대시보드의 Microsoft Defender 기능에 대해 알아봅니다.
author: memildin
ms.author: memildin
ms.date: 11/02/2021
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.custom: ignite-fall-2021
ms.openlocfilehash: db2f01d390cc9503abd0b0a26650e2861dc059c1
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131052773"
---
# <a name="the-workload-protections-dashboard"></a>작업 보호 대시보드

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

이 대시보드는 다음을 제공 합니다.

- 다양 한 리소스 종류의 Microsoft Defender 적용 범위에 대 한 가시성
- 고급 위협 방지 기능을 구성하는 링크
- 온보딩 상태 및 에이전트 설치
- 위협 검색 경고 

워크 로드 보호 대시보드에 액세스 하려면 클라우드 메뉴에 대 한 Defender의 클라우드 보안 섹션에서 **워크 로드 보호** 를 선택 합니다.

## <a name="whats-shown-on-the-dashboard"></a>대시보드에 표시되는 항목

:::image type="content" source="./media/workload-protections-dashboard/sample-defender-dashboard-numbered.png" alt-text="클라우드의 워크 로드 보호 대시보드의 예입니다." lightbox="./media/workload-protections-dashboard/sample-defender-dashboard-numbered.png":::

대시보드에는 다음 섹션이 포함되어 있습니다.

1. **Microsoft defender 검사** -여기에서 구독에 있고 Defender for Cloud에의 한 보호에 적합 한 리소스 유형을 볼 수 있습니다. 관련된 모든 위치에서 업그레이드할 수도 있습니다. 가능한 모든 적격 리소스를 업그레이드하려면 **모두 업그레이드** 를 선택합니다.

2. **보안 경고** -클라우드의 Defender가 사용자 환경의 모든 영역에서 위협을 발견 하면 경고를 생성 합니다. 이러한 경고는 영향을 받는 리소스의 세부 정보, 제안된 수정 단계 및 경우에 따라 응답으로 논리 앱을 트리거하는 옵션을 설명합니다. 이 그래프에서 아무 곳이나 선택하면 **보안 경고 페이지** 가 열립니다.

3. **고급 보호** -클라우드에 대 한 Defender에는 가상 컴퓨터, SQL 데이터베이스, 컨테이너, 웹 응용 프로그램, 네트워크 등에 대 한 다양 한 고급 위협 방지 기능이 포함 되어 있습니다. 이 고급 보호 섹션에서는 이러한 각 보호를 위해 선택한 구독의 리소스 상태를 확인할 수 있습니다. 그중 하나를 선택하면 해당 보호 유형의 구성 영역으로 바로 이동합니다.

4. **Insights** -뉴스, 제안 된 읽기 및 우선 순위가 높은 경고의 롤링 창에서는 사용자와 구독과 관련 된 보안 문제를 클라우드 통찰력에 제공 합니다. 취약점 분석 도구를 통해 vm에서 검색 된 높은 심각도 cves의 목록 인지, 아니면 클라우드 팀의 Defender 멤버가 제공 하는 새 블로그 게시물 인지에 관계 없이 Insights 패널에서 찾을 수 있습니다.




## <a name="next-steps"></a>다음 단계

이 문서에서는 워크 로드 보호 대시보드에 대해 알아보았습니다. 

> [!div class="nextstepaction"]
> [향상 된 보호 사용](enable-enhanced-security.md)

Microsoft Defender의 고급 보호에 대 한 자세한 내용은 [클라우드 용 Microsoft Defender 소개](defender-for-cloud-introduction.md) 를 참조 하세요.

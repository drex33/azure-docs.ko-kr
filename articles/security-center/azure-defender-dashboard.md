---
title: Azure Defender의 대시보드 및 기능
description: Azure Defender 대시보드의 기능에 대해 알아봅니다.
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: df7af81b51428f53beb7722c7f224dc7afdd28cb
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566430"
---
# <a name="the-azure-defender-dashboard"></a>Azure Defender 대시보드

Azure Defender 대시보드는 다음을 제공합니다.

- 다양한 리소스 종류에 대한 Azure Defender 적용 범위 표시
- 고급 위협 방지 기능을 구성하는 링크
- 온보딩 상태 및 에이전트 설치
- Azure Defender 위협 탐지 경고 

Azure Defender 대시보드에 액세스하려면 Security Center 메뉴의 클라우드 보안 섹션에서 **Azure Defender** 를 선택합니다.

## <a name="whats-shown-on-the-dashboard"></a>대시보드에 표시되는 항목

:::image type="content" source="./media/azure-defender-dashboard/sample-defender-dashboard-numbered.png" alt-text="Azure Defender 대시보드의 예입니다." lightbox="./media/azure-defender-dashboard/sample-defender-dashboard-numbered.png":::

대시보드에는 다음 섹션이 포함되어 있습니다.

1. **Azure defender 검사** - 여기에서 구독에 있고 Azure Defender에서 보호할 수 있는 리소스 종류를 확인할 수 있습니다. 관련된 모든 위치에서 업그레이드할 수도 있습니다. 가능한 모든 적격 리소스를 업그레이드하려면 **모두 업그레이드** 를 선택합니다.

2. **보안 경고** - Azure Defender는 환경의 모든 영역에서 위협을 탐지하면 보안 경고를 생성합니다. 이러한 경고는 영향을 받는 리소스의 세부 정보, 제안된 수정 단계 및 경우에 따라 응답으로 논리 앱을 트리거하는 옵션을 설명합니다. 이 그래프에서 아무 곳이나 선택하면 **보안 경고 페이지** 가 열립니다.

3. **고급 보호** - Azure Defender에는 가상 머신, SQL 데이터베이스, 컨테이너, 웹 애플리케이션, 네트워크 등에 대한 다양한 고급 위협 방지 기능이 포함되어 있습니다. 이 고급 보호 섹션에서는 이러한 각 보호를 위해 선택한 구독의 리소스 상태를 확인할 수 있습니다. 그중 하나를 선택하면 해당 보호 유형의 구성 영역으로 바로 이동합니다.

4. **인사이트** - 뉴스, 추천 읽기, 우선 순위가 높은 경고에 대한 이 롤링 창에서는 사용자 및 사용자의 구독과 관련 있는 보안 문제에 대한 Security Center의 인사이트를 제공합니다. 취약성 분석 도구를 통해 VM에서 검색된 심각도가 높은 CVE 목록이든 Security Center 팀 멤버의 새 블로그 게시물이든 **Azure Defender 대시보드** 의 인사이트 창에서 확인할 수 있습니다.




## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Defender 대시보드에 대해 알아보았습니다. 

> [!div class="nextstepaction"]
> [Azure Defender 사용](enable-azure-defender.md)

Azure Defender에 대한 자세한 내용은 [Azure Defender 소개](azure-defender.md)를 참조하세요.
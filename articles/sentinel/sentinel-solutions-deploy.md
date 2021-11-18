---
title: 중앙에서 Microsoft Sentinel 콘텐츠 및 솔루션 검색 및 배포
description: 이 문서에서는 고객이 데이터 커넥터 및 기타 콘텐츠와 함께 패키지된 데이터 분석 도구를 쉽게 찾고 배포할 수 있는 방법을 보여줍니다.
author: yelevin
ms.topic: how-to
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: b27bf67c831f5b5ae29b99838cce905199e8084f
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132759329"
---
# <a name="centrally-discover-and-deploy-microsoft-sentinel-out-of-the-box-content-and-solutions-public-preview"></a>Microsoft Sentinel의 기본적으로 사용 가능한 콘텐츠 및 솔루션을 중앙에서 검색 및 배포(공개 미리 보기)

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
>
> Microsoft Sentinel 솔루션 및 Microsoft Sentinel 콘텐츠 허브는 모두 개별 솔루션 패키지와 마찬가지로 현재 **미리 보기로** 제공됩니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Microsoft Sentinel 콘텐츠 허브는 엔드투엔드 제품, 도메인 또는 산업 요구 사항을 충족하는 콘텐츠가 포함된 Microsoft Sentinel 기본 제공(기본 제공) 콘텐츠 및 솔루션에 대한 액세스를 제공합니다.

이 문서에서는 Microsoft Sentinel 작업 영역에 솔루션을 설치하여 내부의 콘텐츠를 사용할 수 있도록 하는 방법을 설명합니다.

- 콘텐츠 허브에서 해당 상태, 포함된 콘텐츠, 지원 등에 따라 솔루션을 찾습니다.

- 조직의 요구에 맞는 솔루션을 찾으면 작업 영역에 솔루션을 설치합니다. 최신 변경 내용으로 업데이트된 상태로 유지해야 합니다.

> [!TIP]
> 고유한 솔루션을 만들려는 파트너인 경우 솔루션 작성 및 게시에 대한 [Microsoft Sentinel 솔루션 빌드 가이드를](https://aka.ms/sentinelsolutionsbuildguide) 참조하세요.
>
## <a name="find-a-solution"></a>솔루션 찾기

1. Microsoft Sentinel 탐색 메뉴의 **콘텐츠 관리** 에서 **콘텐츠 허브(미리 보기)** 를 선택합니다.

1. **콘텐츠 허브** 페이지에는 검색 가능하고 필터링 가능한 솔루션 그리드가 표시됩니다.

    필터에서 특정 값을 선택하거나 **검색** 필드에 솔루션 이름 또는 설명의 일부를 입력하여 표시되는 목록을 필터링합니다.

    자세한 내용은 [Microsoft Sentinel의 첫 번째 콘텐츠 및 솔루션 범주를 참조하세요.](sentinel-solutions.md#microsoft-sentinel-out-of-the-box-content-and-solution-categories)

    > [!TIP]
    > 배포한 이후 배포한 솔루션에 업데이트가 있는 경우 주황색 삼각형은 배포할 업데이트가 있음을 나타내며 페이지 맨 위에 있는 파란색 삼각형에 표시됩니다.
    >

표의 각 솔루션은 솔루션에 적용된 범주와 솔루션에 포함된 콘텐츠 형식을 보여 있습니다.

예를 들어 다음 이미지에서 **Cisco Umbrella** 솔루션은 **보안 - 기타** 범주를 표시하며 이 솔루션에는 10개의 분석 규칙, 11개의 헌팅 쿼리, 파서, 3개의 플레이북 등이 포함됩니다.

:::image type="content" source="./media/sentinel-solutions-deploy/solutions-list.png" alt-text="Microsoft Sentinel 콘텐츠 허브의 스크린샷." lightbox="./media/sentinel-solutions-deploy/solutions-list.png":::

## <a name="install-or-update-a-solution"></a>솔루션 설치 또는 업데이트

1. 콘텐츠 허브에서 솔루션을 선택하여 오른쪽에 있는 자세한 정보를 봅니다. 그런 **다음, 업데이트가** 필요한 경우 설치 또는 **업데이트** 를 선택합니다. 다음은 그 예입니다.

1. 솔루션 세부 정보 페이지에서 **만들기** 또는 **업데이트를** 선택하여 솔루션 마법사를 시작합니다. 마법사의 기본 **사항** 탭에서 솔루션을 배포할 구독, 리소스 그룹 및 작업 영역을 입력합니다. 다음은 그 예입니다.

    :::image type="content" source="media/sentinel-solutions-deploy/wizard-basics.png" alt-text="기본 사항 탭을 보여주는 솔루션 설치 마법사의 스크린샷.":::

1. **다음을** 선택하여 나머지 탭(솔루션에 포함된 구성 요소에 해당)을 순환합니다. 여기서 각 콘텐츠 구성 요소에 대해 알아보고 경우에 따라 구성할 수 있습니다.

    > [!NOTE]
    > 표시되는 탭은 솔루션에서 제공하는 콘텐츠와 일치합니다. 솔루션마다 콘텐츠 유형이 다를 수 있으므로 모든 솔루션에 동일한 탭이 모두 표시되지 않을 수 있습니다.
    >
    > Microsoft Sentinel이 시스템에 인증할 수 있도록 타사 서비스에 자격 증명을 입력하라는 메시지가 표시될 수도 있습니다. 예를 들어 플레이북을 사용하면 시스템에 규정된 대로 응답 작업을 수행할 수 있습니다.
    >

1. 마지막으로 **검토 + 만들기** 탭에서 메시지를 기다린 `Validation Passed` 다음, **만들기** 또는 **업데이트를** 선택하여 솔루션을 배포합니다. **자동화를 위한 템플릿 다운로드** 링크를 선택하여 솔루션을 코드로 배포할 수도 있습니다.

자세한 내용은 [Microsoft Sentinel 콘텐츠 허브 카탈로그](sentinel-solutions-catalog.md) 및 Microsoft [Sentinel 데이터 커넥터 찾기를 참조하세요.](data-connectors-reference.md)

## <a name="find-the-support-model-for-your-solution"></a>솔루션에 대한 지원 모델 찾기

각 솔루션은 **Microsoft** 또는 파트너의 이름이 나열된 지원 상자의 솔루션 세부 정보 창에 **해당 지원** 모델에 대한 세부 정보를 나열합니다. 다음은 그 예입니다.

:::image type="content" source="media/sentinel-solutions-deploy/find-support-details.png" alt-text="솔루션에 대한 지원 모델을 찾을 수 있는 위치의 스크린샷" lightbox="media/sentinel-solutions-deploy/find-support-details.png":::

지원 담당자에게 문의할 때 게시자, 공급자 및 계획 ID 값과 같은 솔루션에 대한 다른 세부 정보가 필요할 수 있습니다. 이러한 각 정보는 솔루션의 세부 정보 페이지의 **사용량 정보 & 지원** 탭에서 찾을 수 있습니다. 예를 들어:

:::image type="content" source="media/sentinel-solutions-deploy/usage-support.png" alt-text="솔루션에 대한 사용량 및 지원 세부 정보의 스크린샷.":::

## <a name="next-steps"></a>다음 단계

이 문서에서는 Microsoft Sentinel 솔루션과 기본 제공 콘텐츠를 찾아 배포하는 방법에 대해 알아보세요.

- [Microsoft Sentinel 솔루션에](sentinel-solutions.md)대해 자세히 알아보세요.
- 전체 [Microsoft Sentinel 솔루션 카탈로그 를](sentinel-solutions-catalog.md)참조하세요.

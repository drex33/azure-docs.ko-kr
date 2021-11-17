---
title: Microsoft 센티널 기본 콘텐츠 및 솔루션을 중앙에서 검색 및 배포
description: 이 문서에서는 고객이 데이터 커넥터 및 기타 콘텐츠와 함께 패키지 된 데이터 분석 도구를 쉽게 찾고 배포할 수 있는 방법을 보여 줍니다.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 4a6fe8c8867cf1619f24bccd65cf736b2a5c85e5
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132522406"
---
# <a name="centrally-discover-and-deploy-microsoft-sentinel-out-of-the-box-content-and-solutions-public-preview"></a>Microsoft 센티널의 중앙 검색 및 배포 (공개 미리 보기)

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
>
> Microsoft 센티널 솔루션과 Microsoft 센티널 콘텐츠 허브는 현재 **미리 보기** 상태 이며, 모든 개별 솔루션 패키지입니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Microsoft 센티널 콘텐츠 허브는 종단 간 제품, 도메인 또는 업계 요구 사항에 대 한 콘텐츠를 압축 하는 Microsoft 센티널 기본 제공 콘텐츠 및 솔루션에 대 한 액세스를 제공 합니다.

이 문서에서는 Microsoft 센티널 작업 영역에 솔루션을 설치 하 여 사용자가 사용할 수 있도록 콘텐츠를 제공 하는 방법을 설명 합니다.

- 상태, 포함 된 콘텐츠, 지원 등을 기준으로 콘텐츠 허브에서 솔루션을 찾습니다.

- 조직의 요구 사항에 맞는 솔루션을 찾았으면 작업 영역에 솔루션을 설치 합니다. 최신 변경 내용으로 업데이트 된 상태를 유지 해야 합니다.

> [!TIP]
> 사용자 고유의 솔루션을 만들려는 파트너인 경우 솔루션 제작 및 게시에 대 한 [Microsoft 센티널 솔루션 빌드 가이드](https://aka.ms/sentinelsolutionsbuildguide) 를 참조 하세요.
>
## <a name="find-a-solution"></a>솔루션 찾기

1. Microsoft 센티널 탐색 메뉴의 **콘텐츠 관리** 에서 **콘텐츠 허브 (미리 보기)** 를 선택 합니다.

1. **콘텐츠 허브** 페이지에는 검색 및 필터링 가능한 솔루션 표가 표시 됩니다.

    필터에서 특정 값을 선택 하거나 **검색** 필드에 솔루션 이름이 나 설명 중 일부를 입력 하 여 표시 된 목록을 필터링 합니다.

    자세한 내용은 [Microsoft 센티널 기본 콘텐츠 및 솔루션 범주](sentinel-solutions.md#microsoft-sentinel-out-of-the-box-content-and-solution-categories)를 참조 하세요.

    > [!TIP]
    > 배포한 솔루션에 업데이트가 있는 경우 주황색 삼각형은 배포에 대 한 업데이트를 포함 하 고 페이지 위쪽의 파란색 삼각형에 표시 됩니다.
    >

표의 각 솔루션은 솔루션에 적용 되는 범주와 솔루션에 포함 된 콘텐츠 형식을 보여 줍니다.

예를 들어, 다음 이미지에서 **Cisco 파라솔** 솔루션은 **보안** 범주를 표시 하 고이 솔루션에는 10 개의 분석 규칙, 11 개의 구하기 쿼리, 파서, 3 개의 플레이 북 등이 포함 됩니다.

:::image type="content" source="./media/sentinel-solutions-deploy/solutions-list.png" alt-text="Microsoft 센티널 콘텐츠 허브의 스크린샷" lightbox="./media/sentinel-solutions-deploy/solutions-list.png":::

## <a name="install-or-update-a-solution"></a>솔루션 설치 또는 업데이트

1. 콘텐츠 허브에서 솔루션을 선택 하 여 오른쪽에 추가 정보를 표시 합니다. 그런 다음 업데이트가 필요한 경우 **설치** 또는 **업데이트** 를 선택 합니다. 예:

1. 솔루션 정보 페이지에서 **만들기** 또는 **업데이트** 를 선택 하 여 솔루션 마법사를 시작 합니다. 마법사의 **기본 사항** 탭에서 솔루션을 배포 하려는 구독, 리소스 그룹 및 작업 영역을 입력 합니다. 예:

    :::image type="content" source="media/sentinel-solutions-deploy/wizard-basics.png" alt-text="기본 탭을 보여 주는 솔루션 설치 마법사의 스크린샷":::

1. **다음** 을 선택 하 여 나머지 탭 (솔루션에 포함 된 구성 요소에 해당)을 검토 하 고, 경우에 따라 각 콘텐츠 구성 요소를 구성 합니다.

    > [!NOTE]
    > 사용자에 게 표시 되는 탭은 솔루션에서 제공 하는 콘텐츠와 일치 합니다. 솔루션 마다 다른 유형의 콘텐츠가 있을 수 있으므로 모든 솔루션에 동일한 탭이 모두 표시 되지 않을 수 있습니다.
    >
    > Microsoft 센티널에서 시스템에 인증할 수 있도록 타사 서비스에 자격 증명을 입력 하 라는 메시지가 표시 될 수도 있습니다. 예를 들어 플레이 북을 사용 하는 경우 시스템에 규정 된 응답 작업을 수행 하는 것이 좋습니다.
    >

1. 마지막으로 **검토 + 만들기** 탭에서 메시지를 기다린 `Validation Passed` 다음 **만들기** 또는 **업데이트** 를 선택 하 여 솔루션을 배포 합니다. **자동화를 위한 템플릿 다운로드** 링크를 선택하여 솔루션을 코드로 배포할 수도 있습니다.

자세한 내용은 [Microsoft 센티널 content hub catalog](sentinel-solutions-catalog.md) 를 참조 하 고 [microsoft 센티널 데이터 커넥터를 찾으십시오](data-connectors-reference.md).

## <a name="find-the-support-model-for-your-solution"></a>솔루션에 대 한 지원 모델 찾기

각 솔루션은 **Microsoft** 또는 파트너 이름이 나열 된 **지원** 상자에 솔루션의 세부 정보 창에 있는 지원 모델에 대 한 세부 정보를 나열 합니다. 예:

:::image type="content" source="media/sentinel-solutions-deploy/find-support-details.png" alt-text="솔루션에 대 한 지원 모델을 찾을 수 있는의 스크린샷" lightbox="media/sentinel-solutions-deploy/find-support-details.png":::

지원 담당자에 게 연락할 때 게시자, 공급자 및 계획 ID 값과 같은 솔루션에 대 한 기타 세부 정보가 필요할 수 있습니다. 솔루션의 세부 정보 페이지에 있는 **사용 정보 & 지원** 탭에서 각 항목을 찾을 수 있습니다. 예를 들어:

:::image type="content" source="media/sentinel-solutions-deploy/usage-support.png" alt-text="솔루션에 대 한 사용 현황 및 지원 세부 정보 스크린샷":::

## <a name="next-steps"></a>다음 단계

이 문서에서는 Microsoft 센티널 솔루션 및 기본 제공 콘텐츠를 찾고 배포 하는 방법에 대해 알아보았습니다.

- [Microsoft 센티널 솔루션](sentinel-solutions.md)에 대해 자세히 알아보세요.
- 전체 [Microsoft 센티널 솔루션 카탈로그](sentinel-solutions-catalog.md)를 참조 하세요.

---
title: Azure Sentinel 솔루션 배포 | Microsoft Docs
description: 이 문서에서는 고객이 데이터 커넥터와 함께 패키지된 데이터 분석 도구를 쉽게 찾고 배포할 수 있는 방법을 보여 줍니다.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/05/2021
ms.author: yelevin
ms.openlocfilehash: 5cb84ad912e5430948d0dedc71d94e69d1dd1a86
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/31/2021
ms.locfileid: "123251901"
---
# <a name="discover-and-deploy-azure-sentinel-solutions"></a>Azure Sentinel 솔루션 검색 및 배포

> [!IMPORTANT]
>
> Azure Sentinel 솔루션 환경은 모든 개별 솔루션 패키지와 마찬가지로 현재 **미리 보기** 로 제공됩니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

Azure Sentinel 솔루션은 Azure Sentinel에서 제품 내 검색 가능성, 단일 단계 배포 및 엔드투엔드 제품, 도메인 및/또는 수직 시나리오 구현을 제공합니다. 이 환경은 솔루션 검색 가능성, 배포 및 활성화를 위한 [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace)와 솔루션 작성 및 게시를 위한 [Microsoft 파트너 센터](/partner-center/overview)에서 제공합니다.

솔루션은 다음 구성 요소로 구성될 수 있습니다.

- **데이터 커넥터**, 일부는 **파서** 와 함께 제공
- **통합 문서**
- **분석 규칙**
- **헌팅 쿼리**
- **플레이북**

## <a name="find-your-solution"></a>솔루션 찾기

1. Azure Sentinel 탐색 메뉴에서 **솔루션(미리 보기)** 을 선택합니다.

1. **솔루션** 블레이드에 검색 가능한 솔루션 목록이 표시됩니다.

    :::image type="content" source="./media/sentinel-solutions-deploy/solutions-list.png" alt-text="솔루션 목록":::

    - 목록 맨 아래로 스크롤했지만 원하는 내용을 찾지 못한 경우 맨 아래에 있는 **추가 로드** 링크를 선택하여 목록을 확장합니다.

        :::image type="content" source="./media/sentinel-solutions-deploy/load-more.png" alt-text="추가 솔루션 로드":::

1. 선택 범위를 좁히고 원하는 솔루션을 더 쉽게 찾으려면 목록 맨 위에 있는 **검색** 필드에 솔루션 이름의 일부를 입력합니다. 검색 엔진은 단어 단위로만 인식합니다.

    :::image type="content" source="./media/sentinel-solutions-deploy/solutions-search-1.png" alt-text="검색 솔루션":::

1. 목록에서 원하는 솔루션을 선택하여 배포합니다. 솔루션에 대한 필수적이고 중요한 정보를 표시하는 **개요** 탭에서 솔루션의 세부 정보 페이지가 열립니다.

    :::image type="content" source="./media/sentinel-solutions-deploy/proofpoint-tap-solution.png" alt-text="Proofpoint Tap 솔루션":::

1. **계획** 및 **사용 정보 + 지원** 탭에서 솔루션에 대한 다른 유용한 정보를 볼 수 있으며 **검토** 탭에서 다른 고객의 평가를 얻을 수 있습니다.

## <a name="deploy-your-solution"></a>솔루션 배포

1. **만들기** 단추를 선택하여 솔루션 배포 마법사를 시작하면 **기본** 탭에서 열립니다.

    :::image type="content" source="./media/sentinel-solutions-deploy/wizard-basics.png" alt-text="배포 마법사 기본 탭":::

1. 솔루션을 배포할 구독, 리소스 그룹 및 작업 영역을 입력합니다. 

1. **다음** 을 선택하여 나머지 탭(솔루션에 포함된 구성 요소에 해당)을 순환합니다. 여기에서 각 구성 요소에 대해 알아보고 경우에 따라 구성할 수 있습니다.

    > [!NOTE]
    > 아래에 나열된 탭은 함께 제공되는 스크린샷에 표시된 솔루션에서 제공하는 구성 요소에 해당합니다. 솔루션마다 구성 요소 유형이 다를 수 있으므로 모든 솔루션에 동일한 탭이 모두 표시되지 않을 수 있으며 아래와 같이 탭이 표시되지 않을 수 있습니다.

    1. **분석** 탭 :::image type="content" source="./media/sentinel-solutions-deploy/wizard-analytics.png" alt-text="배포 마법사 분석 탭":::

    1. **통합 문서** 탭 :::image type="content" source="./media/sentinel-solutions-deploy/wizard-workbooks.png" alt-text="배포 마법사 통합 문서 탭":::

    1. **플레이북** 탭 - 여기에 유효한 Proofpoint TAP 자격 증명을 입력해야 합니다. 그러면 플레이북이 Proofpoint 시스템에 인증하여 규정된 대응 작업을 수행할 수 있습니다.
        :::image type="content" source="./media/sentinel-solutions-deploy/wizard-playbooks.png" alt-text="배포 마법사 플레이북 탭":::

1. 마지막으로 **검토 + 만들기** 탭에서 "유효성 검사 통과" 메시지가 표시될 때까지 기다린 후 **만들기** 를 선택하여 솔루션을 배포합니다. **자동화를 위한 템플릿 다운로드** 링크를 선택하여 솔루션을 코드로 배포할 수도 있습니다.

    :::image type="content" source="./media/sentinel-solutions-deploy/wizard-create.png" alt-text="배포 마법사 검토 및 만들기 탭":::

## <a name="next-steps"></a>다음 단계

이 문서에서는 Azure Sentinel 솔루션과 이를 찾아 배포하는 방법에 대해 알아봅니다.

- [Azure Sentinel 솔루션](sentinel-solutions.md)에 대해 자세히 알아봅니다.
- 전체 [Sentinel 솔루션 카탈로그](sentinel-solutions-catalog.md)를 참조하세요.

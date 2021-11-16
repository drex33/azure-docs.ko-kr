---
title: Microsoft Sentinel | 경고 세부 정보 사용자 지정 Microsoft Docs
description: 경고의 콘텐츠에 따라 심각도 및 할당된 전술과 함께 경고의 이름을 지정하고 설명하는 방법을 사용자 지정합니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 6ce94549c4e58e0a19fff40e944ece22f948f953
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132521723"
---
# <a name="customize-alert-details-in-microsoft-sentinel"></a>Microsoft Sentinel에서 경고 세부 정보 사용자 지정 

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
>
> - 경고 세부 정보 기능은 **미리 보기** 로 제공됩니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="introduction"></a>소개

예약된 분석 규칙에 대한 이름 및 설명을 정의하고 심각도 및 MITRE ATT&CK 전술에 할당하면 특정 규칙에서 생성된 모든 경고와 그 결과로 생성된 모든 인시던트가 경고의 특정 인스턴스의 특정 콘텐츠에 관계없이 동일한 이름, 설명 등으로 표시됩니다.

**경고 세부 정보** 기능을 사용하면 경고의 모양을 콘텐츠에 맞게 조정할 수 있습니다. 여기서는 경고의 각 인스턴스 이름 또는 설명에 표시되거나 경고의 해당 인스턴스에 할당된 전술 및 심각도를 포함할 수 있는 매개 변수를 경고에서 선택할 수 있습니다. 선택한 매개 변수에 값이 없는 경우(또는 전술 및 심각도에서는 값이 잘못된 경우) 경고 세부 정보가 마법사의 첫 페이지에서 지정한 기본값으로 되돌아갑니다.

아래에 자세히 설명된 절차는 분석 규칙 만들기 마법사의 일부입니다. 여기서는 기존 분석 규칙에서 경고 세부 정보를 추가하거나 변경하는 시나리오를 해결하기 위해 독립적으로 처리됩니다.

## <a name="how-to-customize-alert-details"></a>경고 세부 정보 사용자 지정 방법

1. Microsoft Sentinel 탐색 메뉴에서 **분석을** 선택합니다.

1. 예약된 쿼리 규칙을 선택하고 **편집** 을 클릭합니다. 또는 화면 맨 위에서 **만들기 > 예약된 쿼리 규칙** 을 클릭하여 새 규칙을 만듭니다.

1. **규칙 논리 설정** 탭을 클릭합니다.

1. **경고 보강(미리 보기)** 섹션에서 **경고 세부 정보** 를 펼칩니다.

    :::image type="content" source="media/customize-alert-details/alert-enrichment.png" alt-text="경고 세부 정보 사용자 지정":::

1. 펼친 **경고 세부 정보** 섹션에서 경고에 표시하려는 세부 사항에 해당하는 매개 변수를 포함하는 자유 텍스트를 추가합니다.

    1. **경고 이름 형식** 필드에 경고의 이름으로 표시할 텍스트(경고 텍스트)를 입력하고 경고 텍스트에 포함할 매개 변수를 이중 중괄호로 묶어 포함합니다.

        예: `Alert from {{ProviderName}}: {{AccountName}} failed to log on to computer {{ComputerName}} with IP address {{IPAddress}}.`

    1. **경고 설명 형식** 필드를 사용하여 동일한 작업을 수행합니다.
    
    1. 쿼리 결과에 이 정보가 포함된 열이 있는 경우에만 **전술 열** 및 **심각도 열** 필드를 사용합니다. 각 항목에 대해 해당 정보가 포함된 열을 선택합니다.

    생각이 바뀌었거나 실수한 경우 **전술/심각도 열** 필드 옆에 있는 휴지통 아이콘을 클릭하여 경고 세부 정보를 제거하거나 **경고 이름/설명 형식** 필드에서 자유 텍스트를 삭제할 수 있습니다.

1. 경고 세부 정보 사용자 지정을 마쳤으면 마법사의 다음 탭으로 계속 진행합니다. 기존 규칙을 편집하는 경우 **검토 및 만들기** 탭을 클릭합니다. 규칙 유효성 검사가 성공하면 **저장** 을 클릭합니다.

## <a name="next-steps"></a>다음 단계
이 문서에서는 Microsoft Sentinel 분석 규칙에서 경고 세부 정보를 사용자 지정하는 방법을 배웠습니다. Microsoft Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.
- [예약된 쿼리 분석 규칙](detect-threats-custom.md)을 전체적으로 파악합니다.
- [Microsoft Sentinel의 엔터티에](entities-in-azure-sentinel.md)대해 자세히 알아봅니다.

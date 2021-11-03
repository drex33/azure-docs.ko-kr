---
title: Azure Sentinel 분석 규칙 가져오기 및 내보내기 | Microsoft Docs
description: 배포를 돕기 위해 ARM 템플릿에서 분석 규칙 내보내기 및 가져오기
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/30/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 905455a371bd479d83418eaf68fbc5bf327ce4e4
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131009399"
---
# <a name="export-and-import-analytics-rules-to-and-from-arm-templates"></a>ARM 템플릿에서 분석 규칙 내보내기 및 가져오기

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

> [!IMPORTANT]
>
> - 규칙 내보내기 및 가져오기는 **미리 보기** 상태입니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="introduction"></a>소개

이제 Azure Sentinel 배포를 코드로 관리 및 제어하는 과정의 일환으로, 분석 규칙을 ARM(Azure Resource Manager) 템플릿 파일로 내보내고 이러한 파일에서 규칙을 가져올 수 있습니다. 내보내기 작업은 브라우저의 다운로드 위치에 JSON 파일(*Azure_Sentinel_analytic_rule.json*)을 만듭니다. 그러면 다른 파일처럼 이름을 바꾸고 이동하고 처리할 수 있습니다.

내보낸 JSON 파일은 작업 영역과 독립적이므로 다른 작업 영역 및 다른 테넌트로도 가져올 수 있습니다. 코드로서, 관리형 CI/CD 프레임워크에서 버전을 제어하고 업데이트하고 배포할 수도 있습니다.

이 파일은 분석 규칙에 정의된 모든 매개 변수를 포함하므로, **예약됨** 규칙에 대해서는 기본 쿼리 및 함께 제공되는 일정 설정, 심각도, 인시던트 만들기, 이벤트와 경고 그룹화 설정, 할당된 MITRE ATT&CK 전술 등을 포함합니다. **예약됨** 뿐만 아니라 모든 유형의 분석 규칙을 JSON 파일로 내보낼 수 있습니다.

## <a name="export-rules"></a>규칙 내보내기

1. Azure Sentinel 탐색 메뉴에서 **Analytics** 를 선택합니다.

1. 내보낼 규칙을 선택하고 화면 맨 위에 있는 막대에서 **내보내기** 를 클릭합니다.

    :::image type="content" source="./media/import-export-analytics-rules/export-rule.png" alt-text="분석 규칙 내보내기" lightbox="./media/import-export-analytics-rules/export-rule.png":::

    > [!NOTE]
    > - 규칙 옆에 있는 확인란을 선택하고 끝에 있는 **내보내기** 를 클릭하여 내보낼 분석 규칙을 한 번에 여러 개 선택할 수 있습니다.
    >
    > - **내보내기** 를 클릭하기 전에 머리글 행(**심각도** 옆)의 확인란을 선택하여 디스플레이 그리드의 한 페이지에 있는 모든 규칙을 한 번에 내보낼 수 있습니다. 하지만 한 번에 두 페이지 이상의 규칙을 내보낼 수는 없습니다.
    >
    > - 이 시나리오에서는 단일 파일(*Azure_Sentinel_analytic_ **rules**.json*)이 만들어지고, 내보낸 모든 규칙에 대한 JSON 코드가 파일에 포함됩니다.

## <a name="import-rules"></a>가져오기 규칙

1. 분석 규칙 ARM 템플릿 JSON 파일을 준비합니다.

1. Azure Sentinel 탐색 메뉴에서 **Analytics** 를 선택합니다.

1. 화면 맨 위에 있는 막대에서 **가져오기** 를 클릭합니다. 결과 대화 상자에서 가져오려는 규칙을 나타내는 JSON 파일로 이동하여 선택하고 **열기** 를 선택합니다.

    :::image type="content" source="./media/import-export-analytics-rules/import-rule.png" alt-text="분석 규칙 가져오기" lightbox="./media/import-export-analytics-rules/import-rule.png":::

    > [!NOTE]
    > 단일 ARM 템플릿 파일에서 **최대 50개** 의 분석 규칙을 가져올 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 ARM 템플릿에서 분석 규칙을 내보내고 가져오는 방법을 배웠습니다.
- [사용자 지정 예약 규칙](detect-threats-custom.md)을 포함하여 [분석 규칙](detect-threats-built-in.md)에 대해 자세히 알아봅니다.
- [ARM 템플릿](../azure-resource-manager/templates/overview.md)에 대해 알아봅니다.

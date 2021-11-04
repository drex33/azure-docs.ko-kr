---
title: 검사 규칙 집합 만들기
description: Azure Purview에서 검사 규칙 집합을 만들어 조직의 데이터 원본을 빠르게 검사합니다.
author: linda33wj
ms.author: jingwang
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 79c2a7514677b00227d71d52906d83b3e7734e70
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131442068"
---
# <a name="create-a-scan-rule-set"></a>검사 규칙 집합 만들기

Azure Purview 카탈로그에서 검사 규칙 집합을 만들어 조직의 데이터 원본을 빠르게 검사할 수 있습니다.

검사 규칙 집합은 검사와 쉽게 연결할 수 있도록 검사 규칙 세트를 그룹화하기 위한 컨테이너입니다. 예를 들어, 각 데이터 원본 유형의 기본 검사 규칙 집합을 만든 다음, 회사 내 모든 검사에 대해 기본적으로 해당 검사 규칙 집합을 사용할 수 있습니다. 적절한 권한이 있는 사용자가 비즈니스 요구 사항에 따라 다양한 구성을 사용하여 다른 검사 규칙 집합을 만들게 할 수도 있습니다.

## <a name="steps-to-create-a-scan-rule-set"></a>검사 규칙 집합을 만드는 단계

검사 규칙 집합을 만들려면 다음을 수행합니다.

1. Azure [Purview Studio에서](https://web.purview.azure.com/resource/) **데이터 맵** 선택합니다.

1. 왼쪽 창에서 **검사 규칙 집합** 을 선택한 다음, **새로 만들기** 를 선택합니다.

1. **새 검사 규칙 집합** 페이지의 **원본 유형** 드롭다운 목록에서 카탈로그 검사기가 지원하는 데이터 원본을 선택합니다. 검사하려는 각 데이터 원본 유형에 대한 검사 규칙 집합을 만들 수 있습니다.

1. 검사 규칙 집합에 **이름** 을 지정합니다. 최대 길이는 63자이고 공백은 허용되지 않습니다. 필요에 따라 **설명** 을 입력합니다. 최대 길이는 256자입니다.

   :::image type="content" source="./media/create-a-scan-rule-set/purview-home-page.png" alt-text="검사 규칙 집합 페이지를 보여 주는 스크린샷" border="true":::

1. **계속** 을 선택합니다.

   **파일 형식 선택** 페이지가 나타납니다. 이 페이지의 파일 형식 옵션은 이전 페이지에서 선택한 데이터 원본 유형에 따라 달라집니다. 기본적으로 모든 파일 형식이 사용하도록 설정됩니다.

      :::image type="content" source="./media/create-a-scan-rule-set/select-file-types-page.png" alt-text="파일 형식 선택 페이지를 보여 주는 스크린샷":::

   이 페이지의 **문서 파일 형식** 선택을 사용하여 office 파일 형식인 .doc, .docm, .docx, .dot, .odp, .ods, .odt, .pdf, .pot, .pps, .ppsx, .ppt, .pptm, .pptx, .xlc, .xls, .xlsb, .xlsm, .xlsx, .xlt를 포함하거나 제외할 수 있습니다.

1. 해당 확인란을 선택하거나 선택 취소하여 파일 형식 타일을 사용하거나 사용하지 않도록 설정합니다. Data Lake 형식 데이터 원본(예: Azure Data Lake Storage Gen2 또는 Azure Blob)을 선택하는 경우 스키마를 추출하고 분류하려는 파일 형식을 사용하도록 설정합니다.

1. 특정 데이터 원본 형식의 경우 [사용자 지정 파일 형식을 만들](#create-a-custom-file-type) 수도 있습니다.

1. **계속** 을 선택합니다.

   **분류 규칙 선택** 페이지가 나타납니다. 이 페이지에는 선택한 **시스템 규칙** 및 **사용자 지정 규칙** 과 선택한 총 분류 규칙 수가 표시됩니다. 기본적으로 모든 **시스템 규칙** 확인란이 선택됩니다.

1. 포함하거나 제외하려는 규칙의 경우 범주별로 전체적으로 **시스템 규칙** 분류 규칙 확인란을 선택하거나 선택 취소할 수 있습니다.

   :::image type="content" source="./media/create-a-scan-rule-set/select-classification-rules.png" alt-text="분류 규칙 선택 페이지를 보여 주는 스크린샷":::

1. 범주 노드를 확장하고 개별 확인란을 선택하거나 선택 취소할 수 있습니다. 예를 들어, **Argentina.DNI Number** 에 대한 규칙에 높은 가양성이 있는 경우 해당하는 특정 확인란의 선택을 취소할 수 있습니다.

   :::image type="content" source="./media/create-a-scan-rule-set/select-system-rules.png" alt-text="시스템 규칙을 선택하는 방법을 보여 주는 스크린샷":::

1. **만들기** 를 선택하여 검사 규칙 집합 만들기를 완료합니다.

### <a name="create-a-custom-file-type"></a>사용자 지정 파일 형식 만들기

Azure Purview는 검사 규칙 집합에서 사용자 지정 확장을 추가하고 사용자 지정 열 구분 기호를 정의하는 작업을 지원합니다.

사용자 지정 파일 형식을 만들려면 다음을 수행합니다.

1. [검사 규칙 집합을 만드는 단계](#steps-to-create-a-scan-rule-set)의 1~5단계를 수행하거나 기존 검사 규칙 집합을 편집합니다.

1. **파일 형식 선택** 페이지에서 **새 파일 형식** 을 선택하여 새 사용자 지정 파일 형식을 만듭니다.

   :::image type="content" source="./media/create-a-scan-rule-set/select-new-file-type.png" alt-text="파일 형식 선택 페이지에서 새 파일 형식을 선택하는 방법을 보여 주는 스크린샷":::

1. **파일 확장명** 및 선택적 **설명** 을 입력합니다.

   :::image type="content" source="./media/create-a-scan-rule-set/new-custom-file-type-page.png" alt-text="새 사용자 지정 파일 형식 페이지를 보여 주는 스크린샷" border="true":::

1. **파일 콘텐츠 위치** 에 대해 다음 중 하나를 선택하여 파일 내 파일 콘텐츠 형식을 지정합니다.

   - **사용자 지정 구분 기호** 를 선택하고 고유한 **사용자 지정 구분 기호**(단일 문자만 해당)를 입력합니다.

   - **시스템 파일 형식** 을 선택하고 **시스템 파일 형식** 드롭다운 목록에서 시스템 파일 형식(예: XML)을 선택합니다.

1. **만들기** 를 선택하여 사용자 지정 파일을 저장합니다.

   시스템이 **파일 형식 선택** 페이지로 돌아가고 새 사용자 지정 파일 형식을 새 타일로 삽입합니다.

   :::image type="content" source="./media/create-a-scan-rule-set/new-custom-file-type-tile.png" alt-text="파일 형식 선택 페이지의 새 사용자 지정 파일 형식 타일을 보여 주는 스크린샷":::

1. 변경하거나 삭제하려는 경우 새 파일 형식 타일에서 **편집** 을 선택합니다.

1. **계속** 을 선택하여 검사 규칙 집합 구성을 완료합니다.

## <a name="system-scan-rule-sets"></a>시스템 검사 규칙 집합

시스템 검사 규칙 집합은 각 Azure Purview 카탈로그에 대해 자동으로 생성되는 Microsoft에서 정의한 검사 규칙 집합입니다. 각 시스템 검사 규칙 집합은 특정 데이터 원본 유형과 연결됩니다. 검사를 만들 때 시스템 검사 규칙 집합과 연결할 수 있습니다. Microsoft에서 해당 시스템 규칙 집합을 업데이트할 때마다 카탈로그에서 해당 규칙 집합을 업데이트하고 연결된 모든 검사에 업데이트를 적용할 수 있습니다.

1. 시스템 검사 규칙 집합의 목록을 보려면 **관리 센터** 에서 **검사 규칙 집합** 을 선택하고 **시스템** 탭을 선택합니다.

   :::image type="content" source="./media/create-a-scan-rule-set/system-scan-rule-sets.jpg" alt-text="시스템 검사 규칙 집합의 목록을 보여 주는 스크린샷" lightbox="./media/create-a-scan-rule-set/system-scan-rule-sets.jpg":::

1. 각 시스템 검사 규칙 집합에는 **이름**, **원본 유형**, **버전** 이 있습니다. **버전** 열에서 검사 규칙 집합의 버전 번호를 선택하면 현재 버전 및 이전 버전(있는 경우)과 연결된 규칙이 표시됩니다.

   :::image type="content" source="./media/create-a-scan-rule-set/system-scan-rule-set-page.jpg" alt-text="시스템 검사 규칙 집합 페이지를 보여 주는 스크린샷" lightbox="./media/create-a-scan-rule-set/system-scan-rule-set-page.jpg":::

1. 시스템 검사 규칙 집합에 대한 업데이트를 사용할 수 있는 경우 **버전** 열에서 **업데이트** 를 선택할 수 있습니다. 시스템 검사 규칙 페이지의 **업데이트할 새 버전 선택** 드롭다운 목록에서 버전을 선택합니다. 이 페이지에서는 새 버전 및 현재 버전과 연결된 시스템 분류 규칙의 목록을 제공합니다.

   :::image type="content" source="./media/create-a-scan-rule-set/system-scan-rule-set-version.jpg" alt-text="시스템 검사 규칙 집합의 버전을 변경하는 방법을 보여 주는 스크린샷" lightbox="./media/create-a-scan-rule-set/system-scan-rule-set-version.jpg":::

### <a name="associate-a-scan-with-a-system-scan-rule-set"></a>시스템 검사 규칙 집합과 검사 연결

[검사를 만들](tutorial-scan-data.md#scan-data-into-the-catalog) 때 다음과 같이 시스템 검사 규칙 집합과 연결할 수 있습니다.

1. **검사 규칙 집합 선택** 페이지에서 시스템 검사 규칙 집합을 선택합니다.

   :::image type="content" source="./media/create-a-scan-rule-set/set-system-scan-rule-set.jpg" alt-text="검사에 대한 시스템 검사 규칙 집합을 선택하는 방법을 보여 주는 스크린샷" lightbox="./media/create-a-scan-rule-set/set-system-scan-rule-set.jpg":::

1. **계속** 을 선택한 다음, **저장 및 실행** 을 선택합니다.

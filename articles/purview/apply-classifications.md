---
title: 자산에 분류 적용
description: 이 문서에서는 자산에 분류를 적용하는 방법을 설명합니다.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-map
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 8ec7d456c5bb2eb78c9e1729e38d918c6ac222ee
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129210887"
---
# <a name="apply-classifications-on-assets-in-azure-purview"></a>Azure Purview에서 자산에 대한 분류 적용

이 문서에서는 자산에 분류를 적용하는 방법을 설명합니다.

## <a name="introduction"></a>소개

분류에는 시스템 유형 또는 사용자 지정 유형이 있습니다. 시스템 분류는 Purview에 기본적으로 제공됩니다. 정규식 패턴을 기반으로 사용자 지정 분류를 만들 수 있습니다. 분류는 자동 또는 수동으로 자산에 적용될 수 있습니다.

이 문서에서는 데이터에 분류를 적용하는 방법을 설명합니다.

## <a name="prerequisites"></a>사전 요구 사항

- 필요에 따라 사용자 지정 분류를 만듭니다.
- 데이터 원본에 대한 검사를 설정합니다.

## <a name="apply-classifications"></a>분류 적용
Azure Purview에서 파일, 테이블 또는 열 자산에 시스템 또는 사용자 지정 분류를 적용할 수 있습니다. 이 문서에서는 자산에 분류를 수동으로 적용하는 단계를 설명합니다.

### <a name="apply-classification-to-a-file-asset"></a>파일 자산에 분류 적용
Azure Purview는 설명서 파일을 검사하고 자동으로 분류할 수 있습니다. 예를 들어 *multiple.docx* 라는 파일이 있고 해당 파일의 내용에 국가 ID 번호가 있는 경우 Azure Purview는 파일 자산의 세부 정보 페이지에 **EU 국가별 ID 번호** 라는 분류를 추가합니다.

일부 시나리오에서는 파일 자산에 분류를 수동으로 추가하는 것이 좋습니다. 리소스 집합으로 그룹화되는 파일이 여러 개 있는 경우 리소스 집합 수준에서 분류를 추가합니다.

파티션 리소스 집합에 사용자 지정 또는 시스템 분류를 추가하려면 다음 단계를 수행합니다.

1. 파티션을 검색하거나 찾아본 후 자산 세부 정보 페이지로 이동합니다.

    :::image type="content" source="./media/apply-classifications/asset-detail-page.png" alt-text="자산 세부 정보 페이지를 보여 주는 스크린샷":::

1. **개요** 탭에서 **분류** 섹션을 확인하여 기존 분류가 있는지 확인합니다. **편집** 을 선택합니다.

1. **분류** 드롭다운 목록에서 관심 있는 특정 분류를 선택합니다. 예를 들어 시스템 분류인 **신용 카드 번호** 와 사용자 지정 분류인 **CustomerAccountID** 를 선택합니다.

    :::image type="content" source="./media/apply-classifications/select-classifications.png" alt-text="자산에 추가할 분류를 선택하는 방법을 보여 주는 스크린샷":::

1. **저장** 을 선택합니다.

1. **개요** 탭에서 선택한 분류가 **분류** 섹션 아래에 표시되는지 확인합니다.

    :::image type="content" source="./media/apply-classifications/confirm-classifications.png" alt-text="분류가 자산에 추가되었는지 확인하는 방법을 보여 주는 스크린샷":::

### <a name="apply-classification-to-a-table-asset"></a>테이블 자산에 분류 적용

Azure Purview는 데이터 원본을 검사할 때 테이블 자산에 분류를 자동으로 할당하지 않습니다. 테이블 자산에 분류를 할당하려면 수동으로 추가해야 합니다.

테이블 자산에 분류 적용하려면 다음을 수행합니다.

1. 관심 있는 테이블 자산을 찾습니다. 예를 들어 **Customer** 테이블이라고 합시다.

1. 테이블에 할당된 분류가 없는지 확인합니다. **편집** 을 선택합니다.

    :::image type="content" source="./media/apply-classifications/select-edit-from-table-asset.png" alt-text="테이블 자산의 분류를 보고 편집하는 방법을 보여 주는 스크린샷":::

1. **분류** 드롭다운 목록에서 하나 이상의 분류를 선택합니다. 이 예제에서는 **CustomerInfo** 라는 사용자 지정 분류를 사용하지만 이 단계에서 어떠한 분류를 선택해도 됩니다.

    :::image type="content" source="./media/apply-classifications/select-classifications-in-table.png" alt-text="테이블 자산에 추가할 분류를 선택하는 방법을 보여 주는 스크린샷":::

1. **저장** 을 선택하여 분류를 저장합니다.

1. **개요** 페이지에서 Azure Purview가 새 분류를 추가했는지 확인합니다.

    :::image type="content" source="./media/apply-classifications/verify-classifications-added-to-table.png" alt-text="분류가 테이블 자산에 추가되었는지 확인하는 방법을 보여 주는 스크린샷":::

### <a name="add-classification-to-a-column-asset"></a>열 자산에 분류 추가

Azure Purview는 모든 열 자산을 자동으로 검사하고 모든 열 자산에 분류를 추가합니다. 그러나 분류를 변경하려는 경우 열 수준에서 변경할 수 있습니다.

열에 분류를 추가하려면 다음을 수행합니다.

1. 열 자산을 찾아 선택한 다음, **개요** 탭에서 **편집** 을 선택합니다.

1. **스키마** 탭을 선택합니다.

    :::image type="content" source="./media/apply-classifications/edit-column-schema.png" alt-text="열의 스키마를 편집하는 방법을 보여 주는 스크린샷":::

1. 관심이 있는 열을 찾아 **분류 추가** 를 선택합니다. 이 예에서는 **PasswordHash** 열에 **흔한 암호** 분류를 추가합니다.

    :::image type="content" source="./media/apply-classifications/add-classification-to-column.png" alt-text="열에 분류를 추가하는 방법을 보여 주는 스크린샷":::

1. **저장** 을 선택합니다.

1. **스키마** 탭을 선택하고 분류가 열에 추가되었는지 확인합니다.

    :::image type="content" source="./media/apply-classifications/confirm-classification-added.png" alt-text="분류가 열 스키마에 추가되었는지 확인하는 방법을 보여 주는 스크린샷":::

## <a name="impact-of-rescanning-on-existing-classifications"></a>기존 분류에서 다시 검사하기의 영향

분류는 처음에 데이터에 대한 샘플 집합 검사를 수행하여 이를 집합 regex 패턴과 비교한 결과를 기반으로 적용됩니다. 다시 검사할 때 새 분류가 적용되면 열에서 추가 분류를 가져옵니다. 기존 분류는 열에 유지되며 수동으로 제거해야 합니다.

## <a name="next-steps"></a>다음 단계
사용자 지정 분류를 만드는 방법에 대한 자세한 내용은 [사용자 지정 분류 만들기](create-a-custom-classification-and-classification-rule.md)를 참조하세요.
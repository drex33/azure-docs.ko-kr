---
title: 태그 분류, 용어집 용어, 연락처 수정에 대한 자산을 대량 편집하는 방법
description: Azure Purview에서 자산을 대량 편집하는 방법에 대해 알아봅니다.
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 10/15/2021
ms.openlocfilehash: 511c104080acf27e233fecfa3fae7efa814b9bba
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130133465"
---
# <a name="how-to-bulk-edit-assets-to-annotate-classifications-glossary-terms-and-modify-contacts"></a>자산을 대량 편집하여 분류에 주석을 달고 용어집 용어를 수정하고 연락처를 수정하는 방법

이 문서에서는 단일 작업으로 여러 용어집 용어, 분류, 소유자 및 전문가를 선택한 자산 목록에 태그 표시하는 방법을 설명합니다.

## <a name="add-assets-to-view-selected-list-using-search"></a>검색을 사용하여 선택한 목록 보기에 자산 추가

1. 대량 편집을 위해 목록에 추가하려는 데이터 자산을 검색합니다.

1. 검색 결과 페이지에서 대량 편집의 **선택한 항목 보기** 목록에 추가하려는 자산을 마우스로 가리켜 확인란을 표시합니다.

   :::image type="content" source="media/how-to-bulk-edit-assets/asset-checkbox.png" alt-text="확인란의 스크린샷":::

1. 확인란을 선택하여 대량 편집의 **선택한 항목 보기** 목록에 추가합니다. 추가되면 페이지 맨 아래에 선택한 항목 아이콘이 표시됩니다.

   :::image type="content" source="media/how-to-bulk-edit-assets/selected-list.png" alt-text="목록의 스크린샷":::

1. 위의 단계를 반복하여 모든 데이터 자산을 목록에 추가합니다.

## <a name="add-assets-to-view-selected-list-from-asset-detail-page"></a>자산 세부 정보 페이지에서 선택한 목록 보기에 자산 추가

자산 세부 정보 페이지의 대량 편집 목록에 자산을 추가할 수도 있습니다. 오른쪽 위 모서리에서 확인란을 선택하여 대량 편집 목록에 자산을 추가합니다.

   :::image type="content" source="media/how-to-bulk-edit-assets/asset-list.png" alt-text="자산의 스크린샷":::

## <a name="bulk-edit-assets-in-the-view-selected-list-to-add-replace-or-remove-glossary-terms"></a>선택한 항목 보기 목록에서 자산을 대량 편집하여 용어를 추가, 바꾸기 또는 제거합니다.

1. 대량 편집해야 하는 모든 데이터 자산의 식별이 완료되면 검색 결과 페이지 또는 자산 세부 정보 페이지에서 선택한 목록 **보기를** 선택합니다.

    :::image type="content" source="media/how-to-bulk-edit-assets/view-list.png" alt-text="보기의 스크린샷":::

1. 목록에서 자산을 제거하려면 목록을 검토하고 선택 **취소를** 선택합니다.

    :::image type="content" source="media/how-to-bulk-edit-assets/remove-list.png" alt-text="선택 취소 단추가 강조 표시된 스크린샷.":::

1. **대량 편집을** 선택하여 선택한 모든 자산에 대한 용어집 용어를 추가, 제거 또는 대체합니다.

    :::image type="content" source="media/how-to-bulk-edit-assets/bulk-edit.png" alt-text="대량 편집 단추가 강조 표시된 스크린샷.":::

1. 용어집 용어를 추가하려면 작업을 **추가** 로 선택합니다. 새 값에 추가하려는 용어집 용어를 모두 선택합니다. 완료되면 적용을 선택합니다.
    - 작업 추가는 이미 데이터 자산에 태그가 표시된 용어집 용어의 목록에 새 값을 추가합니다.  
   
    :::image type="content" source="media/how-to-bulk-edit-assets/add-list.png" alt-text="추가의 스크린샷":::

1. 용어집 용어를 바꾸려면 작업을 **바꾸기** 로 선택합니다. 새 값에서 바꿀 용어집 용어를 모두 선택합니다. 완료되면 적용을 선택합니다.
    - 바꾸기 작업은 선택한 데이터 자산에 대한 모든 용어집 용어를 새 값에서 선택한 용어로 바꿉니다.
   
1. 용어집 용어를 제거하려면 작업을 **제거** 로 선택합니다. 완료되면 적용을 선택합니다.
    - 작업 제거는 선택한 데이터 자산에 대한 모든 용어집 용어를 제거합니다.
   
    :::image type="content" source="media/how-to-bulk-edit-assets/replace-list.png" alt-text="용어 제거의 스크린샷":::

1. 분류, 소유자, 전문가를 위해 위의 단계를 반복합니다.

    :::image type="content" source="media/how-to-bulk-edit-assets/all-list.png" alt-text="분류 및 연락처의 스크린샷":::

1. 완료되면 **닫기** 또는 **모두 제거하고 닫기** 를 선택하여 대량 편집 블레이드를 닫습니다. 닫기는 선택한 자산을 제거하지 않지만 모두 제거하고 닫기는 선택한 자산을 모두 제거합니다.
    :::image type="content" source="media/how-to-bulk-edit-assets/close-list.png" alt-text="닫기의 스크린샷":::

   > [!Important]
   > 대량 편집에 권장되는 자산 수는 25개입니다. 25개를 초과하여 선택하면 성능 문제가 발생할 수 있습니다.
   > **선택한 항목 보기** 상자는 선택한 자산이 하나 이상 있는 경우에만 표시됩니다.

## <a name="next-steps"></a>다음 단계

[자습서: 용어집 용어 만들기 및 가져오기](how-to-create-import-export-glossary.md)를 따라 자세히 알아보세요.

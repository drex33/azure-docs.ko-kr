---
author: hophanms
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: include
ms.date: 1/20/2021
ms.openlocfilehash: 388de1966832b21d8134a671f23840841de198e5
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129209746"
---
## <a name="creating-and-running-a-scan"></a>검사 만들기 및 실행

> [!Note] 
> 아래 단계와 스크린샷은 서로 다른 데이터 원본 유형에 걸쳐 검사를 관리하는 일반적인 프로세스를 나타냅니다. 사용 중인 데이터 원본 유형에 따라 옵션이 약간 다를 수 있습니다.

새 검색을 만들고 실행하려면 다음을 수행합니다.

1. [Purview Studio](https://web.purview.azure.com/resource/)의 왼쪽 창에서 **데이터 맵** 탭을 선택합니다.

1. 등록한 데이터 원본을 선택합니다.

1. **새 검사** 를 선택합니다.

1. 데이터 원본에 연결할 자격 증명을 선택합니다. 

   :::image type="content" source="media/manage-scans/set-up-scan.png" alt-text="검사 설정":::

1. 목록에서 적절한 항목을 확인하여 폴더, 수집 또는 스키마와 같은 데이터 원본의 특정 부분에 대한 검사 범위를 지정할 수 있습니다.

   :::image type="content" source="media/manage-scans/scope-your-scan.png" alt-text="검사 범위 지정":::

1. 검사에 대한 검사 규칙 집합을 선택합니다. 시스템 기본값, 기존 사용자 지정 기본값 중 하나를 선택하거나 새 인라인을 만들 수 있습니다.

   :::image type="content" source="media/manage-scans/scan-rule-set.png" alt-text="검사 규칙 집합":::

1. 검사 트리거를 선택합니다. 일정을 설정하거나 검사를 한 번 실행할 수 있습니다.

   :::image type="content" source="media/manage-scans/trigger-scan.png" alt-text="트리거":::

1. 검사를 검토하고 **저장 및 실행** 을 선택합니다.

## <a name="viewing-your-scans-and-scan-runs"></a>검사 및 검사 실행 보기

기존 검사를 보려면 다음을 수행합니다.

1. 관리 센터로 이동합니다. **원본 및 검사** 섹션에서 **데이터 원본** 을 선택합니다. 

2. 원하는 데이터 원본을 선택합니다. 해당 데이터 원본에 기존 검사 목록이 표시됩니다.

3. 결과를 보려는 검사를 선택합니다.

4. 이 페이지는 각 스캔 실행에 대한 메트릭 및 상태와 함께 모든 이전 검사 실행을 표시합니다. 또한 검사가 예약되었는지 수동인지 여부, 분류가 적용된 자산 수, 발견된 총 자산 수, 검사 시작 및 종료 시간, 총 검사 기간이 표시됩니다.

## <a name="manage-your-scans---edit-delete-or-cancel"></a>검사 관리 - 편집, 삭제 또는 취소

검사를 관리하거나 삭제하려면 다음을 수행합니다.

1. 관리 센터로 이동합니다. **원본 및 검사** 섹션에서 **데이터 원본** 을 선택한 다음, 원하는 데이터 원본을 선택합니다.

2. 관리하려는 검사를 선택합니다. **편집** 을 선택하여 검사를 편집할 수 있습니다.

3. **삭제** 를 선택하여 검사를 삭제할 수 있습니다. 

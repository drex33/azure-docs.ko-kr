---
author: whhender
ms.author: whhender
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: include
ms.date: 6/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 0ea867a13772b4a0d10d21d3ca732320b404d9c5
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131010893"
---
### <a name="view-your-scans-and-scan-runs"></a>검사 및 검사 실행 보기

기존 검사를 보려면 다음을 수행합니다.

1. [Purview Studio](https://web.purview.azure.com/resource/)로 이동합니다. 왼쪽 창에서 **데이터 맵** 탭을 선택합니다.

1. 원하는 데이터 원본을 선택합니다. **최근 검사** 에서 해당 데이터 원본에 대한 기존 검사 목록을 확인하거나 **검사** 탭에서 모든 검사를 확인할 수 있습니다.

1. 보려는 결과가 있는 검사를 선택합니다.

1. 이 페이지는 각 검사 실행에 대한 상태와 메트릭과 함께 모든 이전 검사 실행을 표시합니다. 또한 검사가 예약되었는지 수동인지 여부, 분류가 적용된 자산 수, 발견된 총 자산 수, 검사 시작 및 종료 시간, 총 검사 기간이 표시됩니다.

### <a name="manage-your-scans---edit-delete-or-cancel"></a>검사 관리 - 편집, 삭제 또는 취소

검사를 관리하거나 삭제하려면 다음을 수행합니다.

1. [Purview Studio](https://web.purview.azure.com/resource/)로 이동합니다. 왼쪽 창에서 **데이터 맵** 탭을 선택합니다.

1. 원하는 데이터 원본을 선택합니다. **최근 검사** 에서 해당 데이터 원본에 대한 기존 검사 목록을 확인하거나 **검사** 탭에서 모든 검사를 확인할 수 있습니다.

1. 관리하려는 검사를 선택합니다. **검사 편집** 을 선택하여 검사를 편집할 수 있습니다.

1. **검사 실행 취소** 를 선택하여 진행 중인 검사를 취소할 수 있습니다.

1. **검사 삭제** 를 선택하여 검사를 삭제할 수 있습니다.

> [!NOTE]
> * 스캔을 삭제해도 이전 스캔에서 생성된 카탈로그 자산은 삭제되지 않습니다.
> * 원본 테이블을 변경하고 Purview의 스키마 탭에서 설명을 편집한 후에 원본 테이블을 다시 검사하면 해당 자산이 스키마 변경 내용으로 더 이상 업데이트되지 않습니다.

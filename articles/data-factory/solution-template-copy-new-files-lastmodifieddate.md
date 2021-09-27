---
title: LastModifiedDate로 새 파일 및 변경된 파일 복사
description: Azure Data Factory에서 솔루션 템플릿을 사용하여 LastModifiedDate로 새 파일 및 변경된 파일을 복사하는 방법 알아보기.
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
ms.service: data-factory
ms.subservice: tutorials
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 3/8/2019
ms.openlocfilehash: 601dcaa3ea402f8f6b8b8c0664b8a47cfb5f4359
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124743523"
---
# <a name="copy-new-and-changed-files-by-lastmodifieddate-with-azure-data-factory"></a>Azure Data Factory에서 LastModifiedDate를 사용하여 새 파일 및 변경된 파일 복사

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

이 문서에서는 LastModifiedDate를 사용해서 파일 기반 스토리지에서 대상 스토리지로 새 파일 및 변경된 파일만 복사할 수 있는 솔루션 템플릿에 대해 설명합니다. 

## <a name="about-this-solution-template"></a>이 솔루션 템플릿 정보

이 템플릿은 **LastModifiedDate** 특성을 기준으로 먼저 새 파일 및 변경된 파일만 선택하고, 선택한 파일을 데이터 원본 스토리지에서 데이터 대상 스토리지로 복사합니다.

해당 템플릿에는 하나의 작업을 포함합니다.
- **Copy** 는 LastModifiedDate를 통해서 파일 스토리지에서 대상 스토리지로 새 파일 및 변경된 파일만 복사합니다.

해당 템플릿은 다음 두 개의 매개 변수를 정의합니다.
-  *FolderPath_Source* 는 원본 스토리지에서 파일을 읽을 수 있는 폴더 경로입니다. 기본값을 고유한 폴더 경로로 바꾸어야 합니다.
-  *Directory_Source* 는 원본 스토리지에서 파일을 읽을 수 있는 하위 폴더 경로입니다. 기본값을 사용자의 하위 폴더 경로로 바꾸어야 합니다.
-  *FolderPath_Destination* 은 파일을 복사하려고 하는 대상 스토리지의 폴더 경로입니다. 기본값을 고유한 폴더 경로로 바꾸어야 합니다.
-  *Directory_Destination* 는 파일을 복사하려고 하는 대상 스토리지의 하위 폴더 경로입니다. 기본값을 사용자의 하위 폴더 경로로 바꾸어야 합니다.
-  *LastModified_From* 은 해당 LastModifiedDate 특성이 날짜/시간 값보다 크거나 같은 파일을 선택하는 데 사용됩니다.  과거에 복사되지 않은 새 파일만 선택하기 위해, 날짜/시간 값을 파이프라인이 마지막으로 트리거된 시간으로 설정할 수 있습니다. 기본값인 ‘2019-02-01T00:00:00Z’를 UTC 표준 시간대의 예상 LastModifiedDate로 바꿀 수 있습니다. 
-  *LastModified_To* 는 해당 날짜/시간 값 이전의 LastModifiedDate 특성을 가진 파일을 선택하는 데 사용됩니다. 과거에 복사되지 않은 새 파일만 선택하기 위해, 날짜/시간 값을 현재 시간으로 설정할 수 있습니다.  기본값인 ‘2019-02-01T00:00:00Z’를 UTC 표준 시간대의 예상 LastModifiedDate로 바꿀 수 있습니다. 

## <a name="how-to-use-this-solution-template"></a>이 솔루션 템플릿을 사용하는 방법

1. **LastModifiedDate를 통해 새 파일만 복사** 템플릿으로 이동합니다. 원본 스토리지 저장소에 대한 **새 연결** 을 만듭니다. 원본 스토리지 저장소는 복사하려는 파일이 있는 곳입니다.

    :::image type="content" source="media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate1.png" alt-text="원본에 대한 새 연결 만들기":::
    
2. 대상 스토리지에 대한 **새 연결** 을 만듭니다. 대상 스토리지는 파일을 복사할 위치입니다. 

    :::image type="content" source="media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate3.png" alt-text="대상에 대한 새 연결 만들기":::

3. **이 템플릿 사용** 을 선택합니다.

    :::image type="content" source="media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate4.png" alt-text="이 템플릿 사용":::
    
4. 다음 예제와 같이 패널에서 사용할 수 있는 파이프라인이 표시됩니다.

    :::image type="content" source="media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate5.png" alt-text="파이프라인 표시":::

5. **디버그** 를 선택하고 **매개 변수에** 대한 값을 작성한 다음 **마침** 을 선택합니다.  아래 그림에서 다음과 같이 매개 변수를 설정합니다.
   - **FolderPath_Source** = sourcefolder
   - **Directory_Source** = subfolder
   - **FolderPath_Destination** = destinationfolder
   - **Directory_Destination** = subfolder
   - **LastModified_From** =  2019-02-01T00:00:00Z
   - **LastModified_To** = 2019-03-01T00:00:00Z
    
    예제는 (**2019-02-01T00:00:00Z** 에서 **2019-03-01T00:00:00Z**)까지의 Timespan 내에서 마지막으로 수정된 파일이 원본 경로 **sourcefolder/subfolder** 에서 대상 경로 **destinationfolder/subfolder** 로 복사됨을 나타냅니다.  이를 사용자 고유의 매개 변수로 바꿀 수 있습니다.

    :::image type="content" source="media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate6.png" alt-text="파이프라인 실행":::

6. 결과를 검토합니다. 구성된 Timespan 내에서 마지막으로 수정된 파일만 대상 스토리지에 복사된 것을 볼 수 있습니다.

    :::image type="content" source="media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate7.png" alt-text="결과 검토":::
    
7. 이제 연속 창 트리거를 추가하여 해당 파이프라인을 자동화할 수 있습니다. 그러면 파이프라인이 항상 새 파일 및 변경된 파일을 LastModifiedDate을 통해서만 주기적으로 복사할 수 있습니다.  **트리거 추가** 를 선택하고 **새로 만들기/편집** 을 선택합니다.

    :::image type="content" source="media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate8.png" alt-text="트리거 추가를 선택할 때 표시되는 새로 만들기/편집 메뉴 옵션을 강조해서 표시하는 스크린샷":::
    
8. **트리거 추가** 창에서 **+ 새로 만들기** 를 선택합니다.

9. 트리거 유형에 대해 **연속 창** 을 선택하고 **15분마다** 되풀이로 설정합니다(시간 간격 변경 가능). 활성화 상자에 **예** 를 선택한 다음 **확인** 을 누릅니다.

    :::image type="content" source="media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate10.png" alt-text="트리거 만들기"::: 
    
10. **트리거 실행 매개 변수의** 값을 다음과 같이 설정하고 **마침** 을 선택합니다.
    - **FolderPath_Source** = **sourcefolder**.  사용자의 원본 데이터 스토리지 폴더로 바꿀 수 있습니다.
    - **Directory_Source** = **subfolder**.  사용자의 원본 데이터 스토리지 하위 폴더로 바꿀 수 있습니다.
    - **FolderPath_Destination** = **destinationfolder**.  사용자의 대상 데이터 스토리지 폴더로 바꿀 수 있습니다.
    - **Directory_Destination** = **subfolder**.  사용자의 대상 데이터 스토리지의 하위 폴더로 바꿀 수 있습니다.
    - **LastModified_From** =   **\@trigger().outputs.windowStartTime**.  파이프라인이 마지막으로 트리거된 시간을 확인하는 트리거의 시스템 변수입니다.
    - **LastModified_To** =  **\@trigger().outputs.windowEndTime**.  파이프라인이 트리거되는 시간을 결정하는 트리거의 시스템 변수입니다.
    
    :::image type="content" source="media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate11.png" alt-text="입력 매개 변수":::
    
11. **모두 게시** 를 선택합니다.
    
    :::image type="content" source="media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate12.png" alt-text="모두 게시":::

12. 데이터 원본 스토리지의 원본 폴더에 새 파일을 만듭니다.  이제 파이프라인이 자동으로 트리거되고 새 파일만 대상 스토리지에 복사될 때까지 기다립니다.

13. 왼쪽 탐색 패널에서 **모니터** 탭을 선택하고 트리거 되풀이가 15분마다로 설정된 경우 약 15분 동안 기다립니다. 

14. 결과를 검토합니다. 15분마다 파이프라인이 자동으로 트리거되고 각 파이프라인 실행 시 원본 스토리지의 새 파일 또는 변경된 파일만 대상 스토리지로 복사되는 것을 확인할 수 있습니다.

    :::image type="content" source="media/solution-template-copy-new-files-lastmodifieddate/copy-new-files-lastmodifieddate15.png" alt-text="파이프라인이 트리거될 때 반환되는 결과를 보여 주는 스크린샷":::
    
## <a name="next-steps"></a>다음 단계

- [Azure Data Factory 소개](introduction.md)

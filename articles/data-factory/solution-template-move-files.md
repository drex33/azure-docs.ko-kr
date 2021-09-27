---
title: 파일 기반 스토리지 간 파일 이동
description: Azure Data Factory로 솔루션 템플릿을 사용하여 파일 기반 스토리지 간 파일 이동 방법 알아보기.
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
ms.service: data-factory
ms.subservice: tutorials
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 7/12/2019
ms.openlocfilehash: d6be1c8c0abf050a95d02c764580657b88975e0f
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124743424"
---
# <a name="move-files-with-azure-data-factory"></a>Azure Data Factory를 사용하여 데이터 이동

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

ADF 복사 작업은 스토리지 저장소 간에 이진 파일을 복사할 때 ‘이동’ 시나리오를 기본 제공합니다.  해당 기능을 사용하는 방법은 복사 작업에서 ‘deleteFilesAfterCompletion’을 ‘true’로 설정하는 것입니다. 이렇게 하면 복사 작업은 작업 완료 후 데이터 원본 스토리지에서 파일을 삭제합니다. 

이 문서에서는 ADF의 유연한 제어 흐름과 복사 작업 및 삭제 작업을 활용하여 동일한 시나리오를 수행하는 방법인 솔루션 템플릿에 대해 설명합니다. 솔루션 템플릿을 사용하는 일반적인 시나리오 하나: 파일은 원본 스토어의 랜딩 폴더에 계속 저장됩니다. ADF 파이프라인은 일정 트리거를 만들어 해당 파일을 원본에서 대상 스토어로 정기적으로 이동시킬 수 있습니다.  ADF 파이프라인의 ‘파일 이동’ 방식은 랜딩 폴더에서 파일을 가져오고 각 파일을 대상 스토어의 다른 폴더로 복사한 다음 원본 스토어의 랜딩 폴더에서 동일한 파일을 삭제하는 것입니다.

> [!NOTE]
> 해당 템플릿은 폴더를 이동하지 않고 파일을 이동하도록 디자인되었습니다.  폴더 경로만 포함된 데이터 세트를 변경한 후 복사 작업과 삭제 작업을 사용하여 폴더를 나타내는 동일한 데이터 세트를 참조하는 방식으로 폴더를 이동할 때에는 매우 조심해야 합니다. 복사 작업과 삭제 작업 중간에 새 파일이 폴더에 들어오지 않는지를 확인해야 하기 때문입니다. 복사 작업이 방금 복사 작업을 완료했지만 삭제 작업이 시작되지 않은 순간에 새 파일에 폴더에 도착하면 삭제 작업은 전체 폴더를 삭제하여 대상으로 아직 복사되지 않은 이러한 새로 도착한 파일을 삭제할 수 있습니다.

## <a name="about-this-solution-template"></a>이 솔루션 템플릿 정보

솔루션 템플릿은 원본 파일 기반 스토어에서 파일을 가져옵니다. 그런 다음 각 항목을 대상 스토어로 이동시킵니다.

해당 템플릿은 다음 다섯 가지 작업으로 구성됩니다.
- **GetMetadata** 는 원본 스토어의 폴더에 있는 파일 및 하위 폴더를 포함하는 개체 목록을 가져옵니다. 개체를 반복적으로 검색하지 않습니다. 
- **Filter** 는 **GetMetadata** 작업에서 개체 목록을 필터링하여 파일을 선택합니다. 
- **ForEach** 는 **필터** 작업에서 파일 목록을 가져온 다음 목록을 반복하고 각 파일을 복사 및 삭제 작업에 전달합니다.
- **Copy** 는 원본에서 대상 스토어로 파일을 복사합니다.
- **Delete** 는 원본 스토어에서 동일한 파일을 삭제합니다.

해당 템플릿에서는 다음 네 가지의 매개 변수를 정의합니다.
- *SourceStore_Location* 은 파일을 이동하려는 원본 스토어의 폴더 경로입니다. 
- *SourceStore_Directory* 는 파일을 이동하려는 원본 스토어의 하위 폴더 경로입니다.
- *DestinationStore_Location* 은 파일을 이동하려는 대상 스토어의 폴더 경로입니다. 
- *DestinationStore_Directory* 는 파일을 이동하려는 대상 스토어의 하위 폴더 경로입니다.

## <a name="how-to-use-this-solution-template"></a>이 솔루션 템플릿을 사용하는 방법

1. **Move files** 템플릿으로 이동합니다. 기존 연결을 선택하거나 파일을 이동하려는 원본 파일 스토어에 대해 **새로운** 연결을 만듭니다. **DataSource_Folder** 와 **DataSource_File** 은 원본 파일 스토어의 동일한 연결에 대한 참조입니다.

    :::image type="content" source="media/solution-template-move-files/move-files1.png" alt-text="원본에 대한 새 연결 만들기":::

2. 기존 연결을 선택하거나 파일을 이동하려는 대상 파일 스토어에 대한 **새** 연결을 만듭니다.

    :::image type="content" source="media/solution-template-move-files/move-files2.png" alt-text="대상에 대한 새 연결 만들기":::

3. **이 템플릿 사용** 을 선택합니다.
    
4. 다음 예제와 같이 파이프라인이 표시됩니다.

    :::image type="content" source="media/solution-template-move-files/move-files4.png" alt-text="파이프라인 표시":::

5. **디버그** 를 선택하고 **매개 변수** 를 입력한 다음, **마침** 을 선택합니다.   매개 변수는 파일을 이동시키려는 원본 폴더 및 대상 폴더의 경로입니다. 

    :::image type="content" source="media/solution-template-move-files/move-files5.png" alt-text="파이프라인 실행":::

6. 결과를 검토합니다.

    :::image type="content" source="media/solution-template-move-files/move-files6.png" alt-text="결과 검토":::

## <a name="next-steps"></a>다음 단계

- [Azure Data Factory에서 LastModifiedDate를 사용하여 새 파일 및 변경된 파일 복사](solution-template-copy-new-files-lastmodifieddate.md)

- [Azure Data Factory를 사용하여 여러 컨테이너에서 파일 복사](solution-template-copy-files-multiple-containers.md)
---
title: 여러 컨테이너의 파일 복사
description: Azure Data Factory를 사용해 솔루션 템플릿을 이용하여 여러 컨테이너의 파일을 복사하는 방법을 알아봅니다.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.subservice: tutorials
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/1/2018
ms.openlocfilehash: d1d1ffaecdf57817e5ef7be48791d0cb50ca9b74
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124743548"
---
# <a name="copy-multiple-folders-with-azure-data-factory"></a>Azure Data Factory를 사용한 여러 폴더 복사

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

이 문서에서는 여러 복사 작업을 사용하여 파일 기반 저장소 간에 컨테이너 또는 폴더를 복사하는 방법에 대해 설명합니다. 각 복사 작업은 단일 컨테이너 또는 폴더를 복사해야 합니다. 

> [!NOTE]
> 단일 컨테이너에서 파일을 복사하려는 경우 [데이터 복사 도구](copy-data-tool.md)를 사용하여 단일 복사 작업으로 파이프라인을 만들면 더 효율적입니다. 이 문서에서 설명하는 템플릿은 해당 시나리오에 필요한 기능 이상을 제공합니다.

## <a name="about-this-solution-template"></a>이 솔루션 템플릿 정보

이 템플릿은 원본 스토리지 저장소의 지정된 부모 폴더에서 폴더를 열거합니다. 그런 다음 각 폴더를 대상 저장소에 복사합니다.

이 템플릿은 다음 세 가지 작업을 포함합니다.
- **GetMetadata** 는 원본 스토리지 저장소를 검색하고 지정된 부모 폴더에서 하위 폴더 목록을 가져옵니다.
- **ForEach** 는 **GetMetadata** 작업에서 하위 폴더 명단을 가져오고 명단을 반복하고 각 폴더를 복사 작업에 전달합니다.
- **복사** 는 원본 스토리지 저장소의 각 폴더를 대상 저장소로 복사합니다.

템플릿은 다음 매개 변수를 정의합니다.
- *SourceFileFolder* 는 데이터 원본 저장소의 부모 폴더 경로에 속합니다. *SourceFileFolder/SourceFileDirectory* 는 하위 폴더의 목록을 가져올 수 있습니다. 
- *SourceFileDirectory* 는 데이터 원본 저장소의 부모 폴더 경로에 속합니다. *SourceFileFolder/SourceFileDirectory* 는 하위 폴더의 목록을 가져올 수 있습니다. 
- *DestinationFileFolder* 는 파일이 대상 저장소로 복사되는 *DestinationFileFolder/DestinationFileDirectory* 의 부모 폴더 경로에 속합니다. 
- *DestinationFileDirectory* 는 파일이 대상 저장소로 복사되는 *DestinationFileFolder/DestinationFileDirectory* 의 부모 폴더 경로에 속합니다. 

스토리지 저장소의 루트 폴더에 있는 여러 컨테이너를 복사하려는 경우 네 가지 매개 변수를 모두 */* 로 입력할 수 있습니다. 이렇게 하면 스토리지 저장소 사이의 모든 것을 복사합니다.

## <a name="how-to-use-this-solution-template"></a>이 솔루션 템플릿을 사용하는 방법

1. **파일 저장 템플릿 간에 여러 파일 복사 컨테이너** 로 이동합니다. 원본 스토리지 저장소에 대한 **새 연결** 을 만듭니다. 원본 스토리지 저장소는 여러 컨테이너에서 파일을 복사하려는 위치입니다.

    :::image type="content" source="media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image1.png" alt-text="원본에 대한 새 연결 만들기":::

2. 대상 스토리지 저장소에 대한 **새 연결** 을 만듭니다.

    :::image type="content" source="media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image2.png" alt-text="대상에 대한 새 연결 만들기":::

3. **이 템플릿 사용** 을 선택합니다.

    :::image type="content" source="media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image3.png" alt-text="이 템플릿 사용":::
    
4. 다음 예제와 같이 파이프라인이 표시됩니다.

    :::image type="content" source="media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image4.png" alt-text="파이프라인 표시":::

5. **디버그** 를 선택하고 **매개 변수** 를 입력한 다음, **마침** 을 선택합니다.

    :::image type="content" source="media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image5.png" alt-text="파이프라인 실행":::

6. 결과를 검토합니다.

    :::image type="content" source="media/solution-template-copy-files-multiple-containers/copy-files-multiple-containers-image6.png" alt-text="결과 검토":::

## <a name="next-steps"></a>다음 단계

- [Azure Data Factory의 컨트롤 테이블을 사용하여 데이터베이스에서 대량 복사](solution-template-bulk-copy-with-control-table.md)

- [Azure Data Factory를 사용하여 여러 컨테이너에서 파일 복사](solution-template-copy-files-multiple-containers.md)

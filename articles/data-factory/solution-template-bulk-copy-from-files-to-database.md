---
title: 파일에서 데이터베이스로 대량 복사
description: 솔루션 템플릿을 사용하여 Azure Data Lake Storage Gen2에서 Azure Synapse Analytics/Azure SQL Database로 데이터를 대량으로 복사하는 방법을 알아봅니다.
titleSuffix: Azure Data Factory & Azure Synapse
author: jianleishen
ms.author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.date: 12/09/2020
ms.openlocfilehash: 59189f0c197294ca74e01d331663c51fa0e2bd7d
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124757898"
---
# <a name="bulk-copy-from-files-to-database"></a>파일에서 데이터베이스로 대량 복사

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

이 문서에서는 Azure Data Lake Storage Gen2에서 Azure Synapse Analytics/Azure SQL Database로 데이터를 대량으로 복사하는 데 사용할 수 있는 솔루션 템플릿을 설명합니다.

## <a name="about-this-solution-template"></a>이 솔루션 템플릿 정보

이 템플릿은 Azure Data Lake Storage Gen2 원본에서 파일을 검색합니다. 그런 다음, 원본의 각 파일을 반복하고 해당 파일을 대상 데이터 저장소에 복사합니다. 

현재 이 템플릿은 **DelimitedText** 형식으로 데이터를 복사하는 것만 지원합니다. 다른 데이터 형식의 파일을 원본 데이터 저장소에서 검색할 수도 있지만 대상 데이터 저장소로 복사할 수는 없습니다.  

이 템플릿은 다음 세 가지 작업을 포함합니다.
- **메타데이터 가져오기** 작업은 Azure Data Lake Storage Gen2에서 파일을 검색한 후 후속 *ForEach* 작업으로 전달합니다.
- **ForEach** 작업은 *메타데이터 가져오기* 작업에서 파일을 가져오고 각 파일을 *복사* 작업으로 반복합니다.
- **복사** 작업은 *ForEach* 작업에 있으며 각 파일을 원본 데이터 저장소에서 대상 데이터 저장소로 복사합니다.

템플릿은 다음 두 매개 변수를 정의합니다.
- *SourceContainer* 는 Azure Data Lake Storage Gen2에서 데이터가 복사되는 루트 컨테이너 경로입니다. 
- *SourceDirectory* 는 Azure Data Lake Storage Gen2에서 데이터가 복사되는 루트 컨테이너 아래의 디렉터리 경로입니다.

## <a name="how-to-use-this-solution-template"></a>이 솔루션 템플릿을 사용하는 방법

1. **파일에서 데이터베이스로 대량 복사** 템플릿으로 이동합니다. 원본 Gen2 저장소에 대한 **새** 연결을 만듭니다. "GetMetadataDataset" 및 "SourceDataset"은 원본 파일 저장소의 동일한 연결에 대한 참조입니다.

    :::image type="content" source="media/solution-template-bulk-copy-from-files-to-database/source-connection.png" alt-text="원본 데이터 저장소에 대한 새 연결 만들기":::

2. 데이터를 복사하는 싱크 데이터 저장소에 대한 **새** 연결을 만듭니다.

    :::image type="content" source="media/solution-template-bulk-copy-from-files-to-database/destination-connection.png" alt-text="싱크 데이터 저장소에 대한 새 연결 만들기":::
    
3. **이 템플릿 사용** 을 선택합니다.

    :::image type="content" source="media/solution-template-bulk-copy-from-files-to-database/use-template.png" alt-text="이 템플릿 사용":::
    
4. 다음 예제와 같이 생성된 파이프라인이 표시됩니다.

    :::image type="content" source="media/solution-template-bulk-copy-from-files-to-database/new-pipeline.png" alt-text="파이프라인 검토":::

    > [!NOTE]
    > 위에 언급된 **2단계** 에서 **Azure Synapse Analytics** 를 데이터 대상으로 선택한 경우, Azure Synapse Analytics Polybase에 필요한 대로, 스테이징을 위해 Azure Blob 스토리지에 대한 연결에 들어가야 합니다. 다음 스크린샷에 표시된 것처럼 템플릿은 Blob 스토리지에 대한 *스토리지 경로* 를 자동으로 생성합니다. 파이프라인이 실행된 후 컨테이너를 만들었는지 확인합니다.
        
    :::image type="content" source="media/solution-template-bulk-copy-from-files-to-database/staging-account.png" alt-text="Polybase 설정":::

5. **디버그** 를 선택하고 **매개 변수** 를 입력한 다음, **마침** 을 선택합니다.

    :::image type="content" source="media/solution-template-bulk-copy-from-files-to-database/debug-run.png" alt-text="**디버그** 클릭":::

6. 파이프라인 실행이 성공적으로 완료되면 다음 예제와 유사한 결과가 표시됩니다.

    :::image type="content" source="media/solution-template-bulk-copy-from-files-to-database/run-succeeded.png" alt-text="결과 검토":::

       
## <a name="next-steps"></a>다음 단계

- [Azure Data Factory 소개](introduction.md)
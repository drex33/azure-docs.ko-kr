---
title: 데이터 흐름을 사용하여 데이터 레이크에 파일 쓰기 모범 사례
description: 이 자습서에서는 데이터 흐름을 사용하여 데이터 레이크에 파일을 쓰는 모범 사례를 제공합니다.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: seo-lt-2021
ms.date: 06/04/2021
ms.openlocfilehash: 02e67e6521e1f5fa3c29375a15953557613f7d7d
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124763693"
---
# <a name="best-practices-for-writing-to-files-to-data-lake-with-data-flows"></a>데이터 흐름을 사용하여 데이터 레이크에 파일 쓰기 모범 사례

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory를 처음 사용하는 경우 [Azure Data Factory 소개](introduction.md)를 참조하세요.

이 자습서에서는 데이터 흐름을 사용하여 ADLS Gen2 또는 Azure Blob Storage에 파일을 쓸 때 적용할 수 있는 모범 사례를 알아봅니다. parquet 파일을 읽고 결과를 폴더에 저장하려면 Azure Blob Storage 계정 또는 Azure Data Lake Store Gen2 계정에 액세스해야 합니다.

## <a name="prerequisites"></a>필수 구성 요소
* **Azure 구독**. Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* **Azure Storage 계정**. ADLS 스토리지를 *원본* 및 *싱크* 데이터 저장소로 사용합니다. 스토리지 계정이 없는 경우 [Azure Storage 계정 만들기](../storage/common/storage-account-create.md)를 참조하세요.

이 자습서의 단계에서는 다음과 같다고 가정합니다. 

## <a name="create-a-data-factory"></a>데이터 팩터리 만들기

이 단계에서는 데이터 팩터리를 만들고, Data Factory UX를 열어서 데이터 팩터리에 파이프라인을 만듭니다.

1. **Microsoft Edge** 또는 **Google Chrome** 을 엽니다. 현재 Data Factory UI는 Microsoft Edge 및 Google Chrome 웹 브라우저에서만 지원됩니다.
1. 왼쪽 메뉴에서 **리소스 만들기** > **통합** > **Data Factory** 를 선택합니다.
1. **새 데이터 팩터리** 페이지의 **이름** 아래에서 **ADFTutorialDataFactory** 를 입력합니다.
1. 데이터 팩터리를 만들 Azure **구독** 을 선택합니다.
1. **리소스 그룹** 에 대해 다음 단계 중 하나를 사용합니다.

    a. **기존 항목 사용** 을 선택하고 드롭다운 목록에서 기존 리소스 그룹을 선택합니다.
    
    b. **새로 만들기** 를 선택하고 리소스 그룹의 이름을 입력합니다. 리소스 그룹에 대한 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](../azure-resource-manager/management/overview.md)를 참조하세요.
    
1. **버전** 에서 **V2** 를 선택합니다.
1. **위치** 에서 데이터 팩터리의 위치를 선택합니다. 지원되는 위치만 드롭다운 목록에 표시됩니다. 데이터 팩터리에 사용되는 데이터 저장소(예: Azure Storage, SQL Database) 및 컴퓨팅(예: Azure HDInsight)은 다른 지역에 있을 수 있습니다.
1. **만들기** 를 선택합니다.
1. 만들기가 완료되면 알림 센터에 알림이 표시됩니다. **리소스로 이동** 을 선택하여 Data Factory 페이지로 이동합니다.
1. **작성 및 모니터링** 을 선택하여 별도의 탭에서 Data Factory UI를 선택합니다.

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>데이터 흐름 작업으로 파이프라인 만들기

이 단계에서는 데이터 흐름 작업을 포함하는 파이프라인을 만듭니다.

1. Azure Data Factory 홈페이지에서 **오케스트레이션** 을 선택합니다.

   :::image type="content" source="./media/doc-common-process/get-started-page.png" alt-text="ADF 홈페이지를 보여 주는 스크린샷":::

1. 파이프라인의 **일반** 탭에서 파이프라인의 **이름** 으로 **DeltaLake** 를 입력합니다.
1. 팩터리의 위쪽 막대에서 **데이터 흐름 디버그** 슬라이더를 밉니다. 디버그 모드에서는 라이브 Spark 클러스터에 대한 변환 논리의 대화형 테스트를 수행할 수 있습니다. 데이터 흐름 클러스터는 준비하는 데 5~7분 정도 걸리며, 데이터 흐름 개발을 수행할 계획이라면 우선 디버그를 사용하도록 설정하는 것이 좋습니다. 자세한 내용은 [디버그 모드](concepts-data-flow-debug-mode.md)를 참조하세요.

    :::image type="content" source="media/tutorial-data-flow/dataflow1.png" alt-text="데이터 흐름 작업":::
1. **작업** 창에서 **이동 및 변환** 아코디언을 확장합니다. **데이터 흐름** 작업을 창에서 파이프라인 캔버스로 끌어다 놓습니다.

    :::image type="content" source="media/tutorial-data-flow/activity1.png" alt-text="데이터 흐름 작업을 드롭할 수 있는 파이프라인 캔버스를 보여주는 스크린샷입니다.":::
1. **데이터 흐름 추가** 팝업에서 **새 데이터 흐름 만들기** 를 선택하고 데이터 흐름의 이름을 **DeltaLake** 로 설정합니다. 완료되었으면 마침을 클릭합니다.

    :::image type="content" source="media/tutorial-data-flow/activity2.png" alt-text="새 데이터 흐름을 만들 때 데이터 흐름의 이름을 붙이는 위치를 보여주는 스크린샷":::

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>데이터 흐름 캔버스의 변환 논리 빌드

모든 원본 데이터를 사용하고(이 자습서에서는 Parquet 파일 원본 사용) 싱크 변환을 사용하여 데이터 레이크 ETL에 가장 효과적인 메커니즘을 통해 데이터를 Parquet 형식으로 가져옵니다.

:::image type="content" source="media/data-flow/parts-final.png" alt-text="최종 흐름":::

### <a name="tutorial-objectives"></a>자습서 개체

1. 새 데이터 흐름에서 원본 데이터 집합 중 하나를 선택합니다.
1. 데이터 흐름을 사용하여 싱크 데이터 세트를 효과적으로 분할합니다.
1. ADLS Gen2 레이크 폴더에 분할된 데이터를 가져옵니다.

### <a name="start-from-a-blank-data-flow-canvas"></a>빈 데이터 흐름 캔버스에서 시작합니다.

먼저 ADLS Gen2에서 데이터를 가져오기 위해 아래에 설명된 각 메커니즘에 대한 데이터 흐름 환경을 설정해 보겠습니다.

1. 원본 변환을 클릭합니다.
1. 아래쪽 패널에서 데이터 세트 옆에 있는 새로 만들기 단추를 클릭합니다.
1. 데이터 세트를 선택하거나 새로 만듭니다. 이 데모에서는 사용자 데이터라는 Parquet 데이터 세트를 사용합니다.
1. 파생 열 변환을 추가합니다. 이를 원하는 폴더 이름을 동적으로 설정하는 방법으로 사용하겠습니다.
1. 싱크 변환을 추가합니다.
   
### <a name="hierarchical-folder-output"></a>계층 구조 폴더 출력

데이터에서 고유한 값을 사용하여 폴더 계층 구조를 만들고 레이크에서 데이터를 분할하는 것은 매우 일반적입니다. 이 방법은 레이크 및 Spark(데이터 흐름 뒤의 컴퓨팅 엔진)에서 데이터를 구성하고 처리하는 가장 좋은 방법입니다. 그러나 이러한 방식으로 출력을 구성하는 데 약간의 성능 비용이 발생합니다. 싱크에서 이 메커니즘을 사용하면 전체 파이프라인 성능이 약간 감소할 것입니다.

1. 데이터 흐름 디자이너로 돌아가서 위의 데이터 흐름 만들기를 편집합니다. 싱크 변환을 클릭합니다.
1. 최적화 > 분할 설정 > 키를 클릭합니다.
1. 계층 구조 폴더 구조를 설정하는 데 사용하려는 열을 선택합니다.
1. 아래 예제에서는 연도와 월을 폴더 이름 지정에 대한 열로 사용합니다. 결과는 ```releaseyear=1990/month=8``` 형식의 폴더가 됩니다.
1. 데이터 흐름 원본의 데이터 파티션에 액세스할 때 ```releaseyear``` 위의 최상위 폴더만 가리키고 각 후속 폴더에 대해 와일드카드 패턴을 사용합니다(예: ```**/**/*.parquet```).
1. 데이터 값을 조작하거나 폴더 이름에 대한 가상 값을 생성해야 하는 경우에도 파생 열 변환을 사용하여 폴더 이름에 사용할 값을 만듭니다.

:::image type="content" source="media/data-flow/key-parts.png" alt-text="키 분할":::
   
### <a name="name-folder-as-data-values"></a>데이터 값으로 폴더 이름 지정

키/값 분할과 동일한 이점을 제공하지 않는 ADLS Gen2를 사용하는 레이크 데이터에 대해 약간 더 나은 성능을 보이는 싱크 기술은 ```Name folder as column data```입니다. 계층 구조의 키 분할 스타일을 사용하면 데이터 조각을 더 쉽게 처리할 수 있지만 이 기술은 데이터를 더 빨리 쓸 수 있게 평면화된 폴더 구조입니다.

1. 데이터 흐름 디자이너로 돌아가서 위의 데이터 흐름 만들기를 편집합니다. 싱크 변환을 클릭합니다.
1. 최적화 > 분할 설정> 현재 분할 사용을 클릭합니다.
1. 설정 > 열 데이터로 폴더 이름 지정을 클릭합니다.
1. 폴더 이름을 생성하는 데 사용할 열을 선택합니다.
1. 데이터 값을 조작하거나 폴더 이름에 대한 가상 값을 생성해야 하는 경우에도 파생 열 변환을 사용하여 폴더 이름에 사용할 값을 만듭니다.

:::image type="content" source="media/data-flow/folders.png" alt-text="폴더 옵션":::

### <a name="name-file-as-data-values"></a>데이터 값으로 파일 이름 지정

위의 자습서에 나열된 기술은 데이터 레이크에서 폴더 범주를 만드는 좋은 사용 사례입니다. 이러한 기술에 사용되는 기본 파일 명명 체계는 Spark 실행기 작업 ID를 사용하는 것입니다. 경우에 따라 데이터 흐름 문자 싱크에 출력 파일의 이름을 설정해야 할 수도 있습니다. 이 기술은 작은 파일에만 사용하도록 권장됩니다. 파티션 파일을 단일 출력 파일로 병합하는 프로세스는 장기 실행 프로세스입니다.

1. 데이터 흐름 디자이너로 돌아가서 위의 데이터 흐름 만들기를 편집합니다. 싱크 변환을 클릭합니다.
1. 최적화 > 분할 설정 > 단일 파티션을 클릭합니다. 이러한 단일 파티션의 요구 사항에 따라 파일이 병합될 때 실행 프로세스에서 병목 상태가 발생합니다. 이 옵션은 작은 파일에만 권장됩니다.
1. 설정 > 열 데이터로 파일 이름 지정을 클릭합니다.
1. 파일 이름을 생성하는 데 사용할 열을 선택합니다.
1. 데이터 값을 조작하거나 파일 이름에 대한 가상 값을 생성해야 하는 경우에도 파생 열 변환을 사용하여 파일 이름에 사용할 값을 만듭니다.

## <a name="next-steps"></a>다음 단계

[데이터 흐름 싱크](data-flow-sink.md)에 대해 자세히 알아봅니다.

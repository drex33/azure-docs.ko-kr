---
title: 데이터 흐름에서 열 이름 동적 설정
description: 이 자습서에서는 데이터 흐름에서 열 이름을 동적으로 설정하는 단계를 설명합니다
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: tutorials
ms.topic: conceptual
ms.custom: seo-lt-2021
ms.date: 06/17/2021
ms.openlocfilehash: 576b11e78c8cf928863d7db700942cbeab884e2c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128617833"
---
# <a name="dynamically-set-column-names-in-data-flows"></a>데이터 흐름에서 열 이름 동적 설정

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

ETL 작업용 데이터를 처리할 때, 결과를 작성하기 전에 열 이름을 변경해야 하는 경우가 많습니다. 열 이름을 잘 알려진 대상 스키마에 정렬해야 하는 경우도 있습니다. 진화하는 스키마를 기반으로 런타임에 열 이름을 설정해야 할 수도 있습니다. 이 자습서에서는 데이터 흐름을 사용하여 외부 구성 파일 및 매개 변수를 사용하여 동적으로 대상 파일과 데이터베이스 테이블에 대한 열 이름을 설정하는 방법을 알아봅니다.

Azure Data Factory를 처음 사용하는 경우 [Azure Data Factory 소개](introduction.md)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소
* **Azure 구독**. Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* **Azure Storage 계정**. ADLS 스토리지를 *원본* 및 *싱크* 데이터 저장소로 사용합니다. 스토리지 계정이 없는 경우 [Azure Storage 계정 만들기](../storage/common/storage-account-create.md)를 참조하세요.

## <a name="create-a-data-factory"></a>데이터 팩터리 만들기

이 단계에서는 데이터 팩터리를 만들고, Data Factory UX를 열어서 데이터 팩터리에 파이프라인을 만듭니다.

1. **Microsoft Edge** 또는 **Google Chrome** 을 엽니다. 현재 Data Factory UI는 Microsoft Edge 및 Google Chrome 웹 브라우저에서만 지원됩니다.
1. 왼쪽 메뉴에서 **리소스 만들기** > **통합** > **Data Factory** 를 선택합니다.
1. **새 데이터 팩터리** 페이지의 **이름** 아래에서 **ADFTutorialDataFactory** 를 입력합니다.
1. 데이터 팩터리를 만들 Azure **구독** 을 선택합니다.
1. **리소스 그룹** 에 대해 다음 단계 중 하나를 사용합니다.
    * **기존 항목 사용** 을 선택하고 드롭다운 목록에서 기존 리소스 그룹을 선택합니다.
    * **새로 만들기** 를 선택하고 리소스 그룹의 이름을 입력합니다. 리소스 그룹에 대한 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](../azure-resource-manager/management/overview.md)를 참조하세요.    
1. **버전** 에서 **V2** 를 선택합니다.
1. **위치** 에서 데이터 팩터리의 위치를 선택합니다. 지원되는 위치만 드롭다운 목록에 표시됩니다. 데이터 팩터리에 사용되는 데이터 저장소(예: Azure Storage, SQL Database) 및 컴퓨팅(예: Azure HDInsight)은 다른 지역에 있을 수 있습니다.
1. **만들기** 를 선택합니다.
1. 만들기가 완료되면 알림 센터에 알림이 표시됩니다. **리소스로 이동** 을 선택하여 Data Factory 페이지로 이동합니다.
1. **작성 및 모니터링** 을 선택하여 별도의 탭에서 Data Factory UI를 선택합니다.

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>데이터 흐름 작업으로 파이프라인 만들기

이 단계에서는 데이터 흐름 작업을 포함하는 파이프라인을 만듭니다.

1. ADF 홈 페이지에서 **파이프라인 생성** 을 선택합니다.
1. 파이프라인의 **일반** 탭에서 파이프라인의 **이름** 으로 **DeltaLake** 를 입력합니다.
1. 팩터리의 위쪽 막대에서 **데이터 흐름 디버그** 슬라이더를 밉니다. 디버그 모드에서는 라이브 Spark 클러스터에 대한 변환 논리의 대화형 테스트를 수행할 수 있습니다. 데이터 흐름 클러스터는 준비하는 데 5~7분 정도 걸리며, 데이터 흐름 개발을 수행할 계획이라면 우선 디버그를 사용하도록 설정하는 것이 좋습니다. 자세한 내용은 [디버그 모드](concepts-data-flow-debug-mode.md)를 참조하세요.

    :::image type="content" source="media/tutorial-data-flow/dataflow1.png" alt-text="데이터 흐름 작업":::
1. **작업** 창에서 **이동 및 변환** 아코디언을 확장합니다. **데이터 흐름** 작업을 창에서 파이프라인 캔버스로 끌어다 놓습니다.

    :::image type="content" source="media/tutorial-data-flow/activity1.png" alt-text="데이터 흐름 작업을 드롭할 수 있는 파이프라인 캔버스를 보여주는 스크린샷.":::
1. **데이터 흐름 추가** 팝업에서 **새 데이터 흐름 생성하기** 를 선택하고 데이터 흐름의 이름을 **DynaCols** 로 설정합니다. 완료되었으면 마침을 클릭합니다.    

## <a name="build-dynamic-column-mapping-in-data-flows"></a>데이터 흐름에서 동적 열 매핑 빌드

이 자습서에서는 샘플 동영상 등급 파일을 사용하고 원본에 있는 일부 필드의 이름을 시간이 지남에 따라 변경 될 수 있는 새 대상 열 집합으로 변경합니다. 아래에서 만들 데이터 집합은 Blob Storage 또는 ADLS Gen2 스토리지 계정의 동영상 CSV 파일을 지정해야 합니다. [여기에서 동영상 파일을 다운로드](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/moviesDB.csv)하고 Azure 스토리지 계정에 파일을 저장합니다.

:::image type="content" source="media/data-flow/dynacols-1.png" alt-text="최종 흐름":::

### <a name="tutorial-objectives"></a>자습서 개체

데이터 흐름을 사용하여 열 이름을 동적으로 설정하는 방법을 알아봅니다

1. 동영상 CSV 파일에 대한 원본 데이터 집합을 생성합니다.
1. 필드 매핑 JSON 구성 파일에 대한 조회 데이터 집합을 생성합니다.
1. 원본에서 대상 열 이름으로 열을 변환합니다.

### <a name="start-from-a-blank-data-flow-canvas"></a>빈 데이터 흐름 캔버스에서 시작합니다.

먼저 ADLS Gen2에서 데이터를 가져오기 위해 아래에 설명된 각 메커니즘에 대한 데이터 흐름 환경을 설정해 보겠습니다.

1. 원본 변환을 클릭하고 ```movies1```로 호출합니다.
1. 아래쪽 패널에서 데이터 세트 옆에 있는 새로 만들기 단추를 클릭합니다.
1. 위의 moviesDB.csv 파일을 저장한 위치에 따라 Blob 또는 ADLS Gen2 중 하나를 선택합니다.
1. 구성 JSON 파일의 소스를 설정하여 필드 매핑을 조회하는 데 사용할 두 번째 소스를 추가합니다.
1. 이를 ```columnmappings```로 호출합니다.
1. 데이터 집합의 경우, 열 매핑에 대한 구성을 저장할 새 JSON 파일을 지정합니다. 자습서 예제의 JSON 파일에 붙여 넣을 수 있습니다.
    ```
    [
    {"prevcolumn":"title","newcolumn":"movietitle"},
    {"prevcolumn":"year","newcolumn":"releaseyear"}
    ]
    ```

1. 이 원본 설정을 ```array of documents```로 설정합니다.
1. 세 번째 소스를 추가하고 ```movies2```로 호출합니다. 이를 ```movies1```와 정확히 동일하게 구성합니다.
   
### <a name="parameterized-column-mapping"></a>매개 변수화된 열 매핑

첫 번째 시나리오에서는 들어오는 필드와 일치하는 열을 기준으로 열 매핑을 설정하여 데이터 흐름에서 출력 열 이름을 설정하고 각 배열 인덱스와 들어오는 열 서수 위치를 일치시킵니다. 파이프라인에서 이 데이터 흐름을 실행하는 경우, 이 문자열 배열 매개 변수를 데이터 흐름 작업에 보내서 각 파이프라인 실행 시 다른 열 이름을 설정할 수 있습니다.

:::image type="content" source="media/data-flow/dynacols-3.png" alt-text="매개 변수":::

1. 데이터 흐름 디자이너로 돌아가서 위의 데이터 흐름 생성하기를 편집합니다.
1. 매개 변수 탭을 클릭합니다
1. 새 매개 변수 생성 및 문자열 배열 데이터 형식 선택
1. 기본값에 ```['a','b','c']```을 입력합니다
1. 최상위 ```movies1``` 소스를 사용하여 이러한 배열 값에 매핑할 열 이름을 수정합니다
1. 변환 선택을 추가합니다. 선택 변환은 들어오는 열을 출력을 위해 새 열 이름에 매핑하는 데 사용됩니다.
1. 처음 3개의 열 이름을 매개 변수에 정의된 새 이름으로 변경합니다
1. 이렇게 하려면, 아래쪽 창에 3개의 규칙 기반 매핑 항목을 추가합니다
1. 첫 번째 열에 대해 일치 규칙은 ```position==1```이 되고 이름은 ```$parameter1[1]```입니다
1. 열 2와 3에 대해 동일한 패턴을 수행합니다
 
    :::image type="content" source="media/data-flow/dynacols-4.png" alt-text="선택 변환":::

1. 선택 변환의 검사 및 데이터 미리 보기 탭을 클릭하여 새 열 이름 값을 확인합니다.```(a,b,c)``` 원래 동영상, 제목, 장르 열 이름을 바꿉니다
   
### <a name="create-a-cached-lookup-of-external-column-mappings"></a>외부 열 매핑의 캐시된 조회 생성하기

다음으로, 나중에 조회할 수 있도록 캐시된 싱크를 생성합니다. 캐시는 데이터 흐름의 각 파이프라인 실행 시 동적으로 열 이름을 바꾸는 데 사용할 수 있는 외부 JSON 구성 파일을 읽습니다.

1. 데이터 흐름 디자이너로 돌아가서 위의 데이터 흐름 생성하기를 편집합니다. 싱크 변환을 ```columnmappings``` 원본에 추가합니다.
1. 싱크 형식을 ```Cache```로 설정합니다.
1. 설정에서 키 열로 ```prevcolumn```을 선택합니다.

### <a name="lookup-columns-names-from-cached-sink"></a>캐시된 싱크에 있는 열 이름 조회

구성 파일 콘텐츠를 메모리에 저장했으므로 들어오는 열 이름을 나가는 새 열 이름에 동적으로 매핑할 수 있습니다.

1. 데이터 흐름 디자이너로 돌아가서 위의 데이터 흐름 만들기를 편집합니다. ```movies2``` 원본 변환을 클릭합니다.
1. 변환 선택을 추가합니다. 캐시된 싱크에 저장되는 JSON 구성 파일의 대상 이름에 따라 열 이름을 변경하는 데 선택 변환을 사용합니다.
1. 규칙 기반 매핑 추가. 일치 조건의 경우, ```!isNull(cachedSink#lookup(name).prevcolumn)``` 수식을 사용합니다.
1. 출력 열 이름에 대해 ```cachedSink#lookup($$).newcolumn``` 수식을 사용합니다.
1. 외부 JSON 구성 파일에서 ```prevcolumn``` 속성과 일치하는 모든 열 이름을 찾고 각 일치 항목의 이름을 새 ```newcolumn``` 이름으로 변경하는 작업을 수행했습니다.
1. 데이터 미리 보기를 클릭하고 선택 변환에서 탭을 검사합니다. 그러면 외부 매핑 파일에서 새 열 이름이 표시됩니다.

:::image type="content" source="media/data-flow/dynacols-2.png" alt-text="원본 2":::

## <a name="next-steps"></a>다음 단계

* 이 자습서의 완성된 파이프라인은 [여기](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/DynaColsPipe.zip)에서 다운로드할 수 있습니다
* [데이터 흐름 싱크](data-flow-sink.md)에 대해 자세히 알아봅니다.

---
title: 데이터 흐름을 사용한 Delta Lake ETL(추출, 변환 및 로드)
description: 이 자습서는 데이터 흐름을 사용하여 Delta Lake에서 데이터를 변환 및 분석하는 방법에 대한 단계별 지침을 제공합니다.
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: seo-lt-2021
ms.date: 06/04/2021
ms.openlocfilehash: 246b1988a7a632e9c3b332f7adbdf908f1084457
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124798259"
---
# <a name="transform-data-in-delta-lake-using-mapping-data-flows"></a>매핑 데이터 흐름을 사용한 Delta Lake의 데이터 변환

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory를 처음 사용하는 경우 [Azure Data Factory 소개](introduction.md)를 참조하세요.

이 자습서를 통해, 데이터 흐름 캔버스를 사용하여 ADLS(Azure Data Lake Storage) Gen2에서 데이터를 분석 및 변환하고 Delta Lake에 저장할 수 있는 데이터 흐름을 만들 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항
* **Azure 구독**. Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* **Azure Storage 계정**. ADLS 스토리지를 *원본* 및 *싱크* 데이터 저장소로 사용합니다. 스토리지 계정이 없는 경우 [Azure Storage 계정 만들기](../storage/common/storage-account-create.md)를 참조하세요.

이 지침서에서 변형하는 파일은 [여기](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/moviesDB2.csv)에서 찾을 수 있는 MoviesDB.csv입니다. GitHub에서 파일을 검색하려면 해당 콘텐츠를 원하는 텍스트 편집기에 복사하여 로컬에 .csv 파일로 저장합니다. 스토리지 계정에 파일을 업로드하려면 [Azure Portal을 사용하여 Blob 업로드](../storage/blobs/storage-quickstart-blobs-portal.md)를 참조하세요. 예제는 ‘sample-data’라는 컨테이너를 참조합니다.

## <a name="create-a-data-factory"></a>데이터 팩터리 만들기

이 단계에서는 데이터 팩터리를 만들고, Data Factory UX를 열어서 데이터 팩터리에 파이프라인을 만듭니다.

1. **Microsoft Edge** 또는 **Google Chrome** 을 엽니다. 현재 Data Factory UI는 Microsoft Edge 및 Google Chrome 웹 브라우저에서만 지원됩니다.
1. 왼쪽 메뉴에서 **리소스 만들기** > **통합** > **Data Factory** 를 선택합니다.
1. **새 데이터 팩터리** 페이지의 **이름** 아래에서 **ADFTutorialDataFactory** 를 입력합니다.
1. 데이터 팩터리를 만들 Azure **구독** 을 선택합니다.
1. **리소스 그룹** 에 대해 다음 단계 중 하나를 사용합니다.

    a. **기존 항목 사용** 을 선택하고 드롭다운 목록에서 기존 리소스 그룹을 선택합니다.

    b. **새로 만들기** 를 선택하고 리소스 그룹의 이름을 입력합니다. 
         
    리소스 그룹에 대한 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](../azure-resource-manager/management/overview.md)를 참조하세요. 
1. **버전** 에서 **V2** 를 선택합니다.
1. **위치** 에서 데이터 팩터리의 위치를 선택합니다. 지원되는 위치만 드롭다운 목록에 표시됩니다. 데이터 팩터리에 사용되는 데이터 저장소(예: Azure Storage, SQL Database) 및 컴퓨팅(예: Azure HDInsight)은 다른 지역에 있을 수 있습니다.
1. **만들기** 를 선택합니다.
1. 만들기가 완료되면 알림 센터에 알림이 표시됩니다. **리소스로 이동** 을 선택하여 Data Factory 페이지로 이동합니다.
1. **작성 및 모니터링** 을 선택하여 별도의 탭에서 Data Factory UI를 선택합니다.

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>데이터 흐름 작업으로 파이프라인 만들기

이 단계에서는 데이터 흐름 작업을 포함하는 파이프라인을 만듭니다.

1. 홈페이지에서 **오케스트레이션** 을 선택합니다.

   :::image type="content" source="./media/doc-common-process/get-started-page.png" alt-text="ADF 홈페이지를 보여 주는 스크린샷":::

1. 파이프라인의 **일반** 탭에서 파이프라인의 **이름** 으로 **DeltaLake** 를 입력합니다.
1. **작업** 창에서 **이동 및 변환** 을 확장합니다. **데이터 흐름** 작업을 창에서 파이프라인 캔버스로 끌어다 놓습니다.

    :::image type="content" source="media/tutorial-data-flow/activity1.png" alt-text="데이터 흐름 작업을 드롭할 수 있는 파이프라인 캔버스를 보여주는 스크린샷입니다.":::
1. **데이터 흐름 추가** 팝업에서 **새 데이터 흐름 만들기** 를 선택하고 데이터 흐름의 이름을 **DeltaLake** 로 설정합니다. 완료되었으면 마침을 클릭합니다.

    :::image type="content" source="media/tutorial-data-flow/activity2.png" alt-text="새 데이터 흐름을 만들 때 데이터 흐름의 이름을 붙이는 위치를 보여주는 스크린샷":::
1. 파이프라인 캔버스의 상단 막대에서 **데이터 흐름 디버그** 슬라이더를 밉니다. 디버그 모드에서는 라이브 Spark 클러스터에 대한 변환 논리의 대화형 테스트를 수행할 수 있습니다. 데이터 흐름 클러스터는 준비하는 데 5~7분 정도 걸리며, 데이터 흐름 개발을 수행할 계획이라면 우선 디버그를 사용하도록 설정하는 것이 좋습니다. 자세한 내용은 [디버그 모드](concepts-data-flow-debug-mode.md)를 참조하세요.

    :::image type="content" source="media/tutorial-data-flow/dataflow1.png" alt-text="데이터 흐름 디버그 슬라이더가 있는 위치를 보여주는 스크린샷.":::

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>데이터 흐름 캔버스의 변환 논리 빌드

이 자습서에서 두 개의 데이터 흐름을 생성합니다. 첫 번째 데이터 흐름은 위의 영화 CSV 파일에서 새 Delta Lake를 생성하는 간단한 원본에서 싱크로의 흐름입니다. 마지막으로, 아래에서 이 흐름 디자인을 만들어 Delta Lake의 데이터를 업데이트합니다.

:::image type="content" source="media/data-flow/data-flow-tutorial-6.png" alt-text="최종 흐름":::

### <a name="tutorial-objectives"></a>자습서 개체

1. 위의 MoviesCSV 데이터 세트 원본을 가져와 새 Delta Lake를 형성합니다.
1. 1988년 영화에 대하여 업데이트된 등급을 ‘1’로 하는 논리를 빌드합니다.
1. 1950년에서 모든 영화를 삭제합니다.
1. 1960년에서 영화를 복제하여 2021년에 새 영화를 삽입합니다.

### <a name="start-from-a-blank-data-flow-canvas"></a>빈 데이터 흐름 캔버스에서 시작합니다.

1. 원본 변환을 클릭합니다.
1. 아래쪽 패널 1의 데이터 세트 옆에 있는 새로 만들기를 클릭합니다. ADLS Gen2에 대한 새 연결된 서비스를 만듭니다.
1. 데이터 세트 형식에 대해 구분된 텍스트를 선택합니다.
1. 해당 데이터 세트 이름을 “MoviesCSV”로 지정합니다. 
1. 위의 스토리지에 업로드한 MoviesCSV 파일을 가리킵니다.
1. 쉼표로 구분하여 첫 번째 행에 헤더를 포함하도록 설정합니다. 
1. 원본 프로젝션 탭으로 이동하여 “데이터 형식 검색”을 클릭합니다.
1. 프로젝션 집합이 있으면 계속 진행할 수 있습니다. 
1. 싱크 변환을 추가합니다.
1. Delta는 인라인 데이터 세트 형식입니다. ADLS Gen2 스토리지 계정을 가리켜야 합니다.
   
   :::image type="content" source="media/data-flow/data-flow-tutorial-5.png" alt-text="인라인 데이터 세트":::

1. ADF가 Delta Lake를 만들려고 하는 스토리지 컨테이너의 폴더 이름을 선택합니다.
1. 파이프라인 디자이너로 돌아가서 디버그를 클릭하여, 캔버스에 이 데이터 흐름 작업으로만 파이프라인을 디버그 모드로 실행합니다. 그러면 새 Delta Lake가 ADLS Gen2에 생성됩니다.
1. Factory 리소스에서 새로 만들기 > 데이터 흐름을 클릭합니다. 
1. MoviesCSV를 다시 원본으로 사용하고 “데이터 형식 검색”을 다시 클릭합니다.
1. 그래프에서 원본 변환에 필터 변환을 추가합니다.
1. 사용하려는 3개년, 즉 1950년, 1988년, 1960년과 일치하는 영화 행만 허용합니다.
1. 이제 필터 변환에 파생 열 변환을 추가하여 각 1988년 영화에 대한 등급을 ‘1’로 업데이트합니다.
1. 동일한 파생 열에서 기존 연도를 가져오고 연도를 2021년으로 변경하여 2021년에 대한 영화를 만듭니다. 1960년을 선택하겠습니다.
1. 세 개의 파생 열은 다음과 같이 표시됩니다.

   :::image type="content" source="media/data-flow/data-flow-tutorial-2.png" alt-text="파생 열":::
   
1. 대체 행 변환에서 ```Update, insert, delete, and upsert``` 정책이 만들어집니다. 파생 열 뒤에 대체 행 변환을 추가합니다.
1. 대체 행 정책은 다음과 같이 표시됩니다.

   :::image type="content" source="media/data-flow/data-flow-tutorial-3.png" alt-text="행 변경":::
   
1. 각 대체 행 형식에 대한 적절한 정책을 설정했으므로 싱크 변환에 적절한 업데이트 규칙이 설정되었는지 확인합니다.

   :::image type="content" source="media/data-flow/data-flow-tutorial-4.png" alt-text="싱크":::
   
1. 여기서는 ADLS Gen2 데이터 레이크에 대한 Delta Lake 싱크를 사용하고 삽입, 업데이트, 삭제를 허용합니다. 
1. 키 열은 Movie 기본 키 열 및 연도 열로 구성된 복합 키입니다. 1960년 행을 복제하여 가짜 2021년 영화를 만들기 때문입니다. 이렇게 하면 고유성을 제공하여 기존 행을 조회할 때 충돌을 방지합니다.

### <a name="download-completed-sample"></a>다운로드 완료 샘플
[다음은 레이크에서 행을 업데이트/삭제하는 데이터 흐름을 사용하는 Delta 파이프라인에 대한 샘플 솔루션입니다.](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/DeltaPipeline.zip)

## <a name="next-steps"></a>다음 단계

[Data Flow 표현 언어](data-flow-expression-functions.md)에 대한 자세한 정보를 알아봅니다.

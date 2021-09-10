---
title: 데이터 흐름 매핑을 사용한 데이터 변환
description: 이 지침서에서는 Azure Data Factory를 통해 데이터 흐름을 매핑하여 데이터를 변환하기 위한 단계별 지침을 제공합니다.
author: kromerm
ms.author: makromer
ms.reviewer: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 06/04/2021
ms.openlocfilehash: b5d28b5e625bc7822469511ae83d5dabd5824fa0
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122642666"
---
# <a name="transform-data-using-mapping-data-flows"></a>데이터 흐름 매핑을 사용하여 데이터 변환

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory를 처음 사용하는 경우 [Azure Data Factory 소개](introduction.md)를 참조하세요.

이 지침서에서는 Azure Data Factory 사용자 인터페이스 (UX)를 사용하여 데이터 흐름 매핑을 사용하는 Azure Data Lake Storage (ADLS) Gen2 원본에서 ADLS Gen2 싱크로 데이터를 복사하고 변환하는 파이프라인을 만듭니다. 이 구성 패턴은 매핑 데이터 흐름을 사용하여 데이터를 변환할 때 확장될 수 있습니다.

 >[!NOTE]
   >이 지침서는 데이터 흐름을 일반적으로 매핑하기 위한 것입니다. 데이터 흐름은 Azure Data Factory 및 Synapse 파이프라인 모두에서 사용할 수 있습니다. Azure Synapse 파이프라인에서 데이터 흐름을 처음 접하는 경우 [Azure Synapse 파이프라인을 사용하여 데이터 흐름](../synapse-analytics/concepts-data-flow-overview.md)에 따라 이동하세요. 
   
이 자습서에서 수행하는 단계는 다음과 같습니다.

> [!div class="checklist"]
> * 데이터 팩터리를 만듭니다.
> * Data Flow 작업으로 파이프라인 만들기
> * 네 가지 변환으로 매핑 데이터 흐름을 빌드합니다.
> * 파이프라인 실행 테스트
> * 데이터 흐름 모니터링 작업

## <a name="prerequisites"></a>필수 구성 요소
* **Azure 구독**. Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* **Azure Storage 계정**. ADLS 스토리지를 *원본* 및 *싱크* 데이터 저장소로 사용합니다. 스토리지 계정이 없는 경우 [Azure Storage 계정 만들기](../storage/common/storage-account-create.md)를 참조하세요.

이 지침서에서 변형하는 파일은 [여기](https://raw.githubusercontent.com/djpmsft/adf-ready-demo/master/moviesDB.csv)에서 찾을 수 있는 MoviesDB.csv입니다. GitHub에서 파일을 검색하려면 해당 콘텐츠를 원하는 텍스트 편집기에 복사하여 로컬에 .csv 파일로 저장합니다. 스토리지 계정에 파일을 업로드하려면 [Azure Portal을 사용하여 Blob 업로드](../storage/blobs/storage-quickstart-blobs-portal.md)를 참조하세요. 예제는 ‘sample-data’라는 컨테이너를 참조합니다.

## <a name="create-a-data-factory"></a>데이터 팩터리 만들기

이 단계에서는 데이터 팩터리를 만들고, Data Factory UX를 열어서 데이터 팩터리에 파이프라인을 만듭니다.

1. **Microsoft Edge** 또는 **Google Chrome** 을 엽니다. 현재 Data Factory UI는 Microsoft Edge 및 Google Chrome 웹 브라우저에서만 지원됩니다.
2. 왼쪽 메뉴에서 **리소스 만들기** > **통합** > **Data Factory** 를 선택합니다.

   !["새로 만들기" 창에서 데이터 팩터리 선택](./media/doc-common-process/new-azure-data-factory-menu.png)

3. **새 데이터 팩터리** 페이지의 **이름** 아래에서 **ADFTutorialDataFactory** 를 입력합니다.

   Azure Data Factory의 이름은 *전역적으로 고유* 해야 합니다. 이름 값에 대한 오류 메시지가 표시되면 데이터 팩터리에 대한 다른 이름을 입력합니다. (예: yournameADFTutorialDataFactory). Data Factory 아티팩트에 대한 명명 규칙은 [Data Factory 명명 규칙](naming-rules.md)을 참조하세요.

    :::image type="content" source="./media/doc-common-process/name-not-available-error.png" alt-text="중복된 이름에 대한 새 데이터 팩터리 오류 메시지.":::
4. 데이터 팩터리를 만들 Azure **구독** 을 선택합니다.
5. **리소스 그룹** 에 대해 다음 단계 중 하나를 사용합니다.

    a. **기존 항목 사용** 을 선택하고 드롭다운 목록에서 기존 리소스 그룹을 선택합니다.

    b. **새로 만들기** 를 선택하고 리소스 그룹의 이름을 입력합니다. 
         
    리소스 그룹에 대한 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](../azure-resource-manager/management/overview.md)를 참조하세요. 
6. **버전** 에서 **V2** 를 선택합니다.
7. **위치** 에서 데이터 팩터리의 위치를 선택합니다. 지원되는 위치만 드롭다운 목록에 표시됩니다. 데이터 팩터리에 사용되는 데이터 저장소(예: Azure Storage, SQL Database) 및 컴퓨팅(예: Azure HDInsight)은 다른 지역에 있을 수 있습니다.
8. **만들기** 를 선택합니다.
9. 만들기가 완료되면 알림 센터에 알림이 표시됩니다. **리소스로 이동** 을 선택하여 Data Factory 페이지로 이동합니다.
10. **작성 및 모니터링** 을 선택하여 별도의 탭에서 Data Factory UI를 선택합니다.

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>데이터 흐름 작업으로 파이프라인 만들기

이 단계에서는 데이터 흐름 작업을 포함하는 파이프라인을 만듭니다.

1. Azure Data Factory 홈페이지에서 **오케스트레이션** 을 선택합니다.

   ![ADF 홈페이지를 보여 주는 스크린샷](./media/doc-common-process/get-started-page.png)

1. 파이프라인의 **일반** 탭에서 파이프라인의 **이름** 으로 **TransformMovies** 를 입력합니다.
1. **작업** 창에서 **이동 및 변환** 아코디언을 확장합니다. **데이터 흐름** 작업을 창에서 파이프라인 캔버스로 끌어다 놓습니다.

    ![데이터 흐름 작업을 드롭할 수 있는 파이프라인 캔버스를 보여주는 스크린샷.](media/tutorial-data-flow/activity1.png)
1. **데이터 흐름 추가** 팝업에서 **새 데이터 흐름 만들기** 를 선택하고 데이터 흐름의 이름을 **TransformMovies** 로 설정합니다. 완료되었으면 마침을 클릭합니다.

    ![새 데이터 흐름을 만들 때 데이터 흐름의 이름을 붙이는 위치를 보여주는 스크린샷](media/tutorial-data-flow/activity2.png)
1. 파이프라인 캔버스의 상단 막대에서 **데이터 흐름 디버그** 슬라이더를 밉니다. 디버그 모드에서는 라이브 Spark 클러스터에 대한 변환 논리의 대화형 테스트를 수행할 수 있습니다. 데이터 흐름 클러스터는 준비하는 데 5~7분 정도 걸리며, 데이터 흐름 개발을 수행할 계획이라면 우선 디버그를 사용하도록 설정하는 것이 좋습니다. 자세한 내용은 [디버그 모드](concepts-data-flow-debug-mode.md)를 참조하세요.

    ![데이터 흐름 작업](media/tutorial-data-flow/dataflow1.png)

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>데이터 흐름 캔버스에서 변환 논리 빌드

데이터 흐름을 만들면 데이터 흐름 캔버스로 자동 전송됩니다. 이 단계에서는 ADLS 스토리지의 MoviesDB.csv를 가져와 1910년부터 2000년까지 코미디 장르의 평균 등급을 집계하는 데이터 흐름을 만듭니다. 그런 다음 이 파일을 ADLS 스토리지에 다시 씁니다.

1. 데이터 흐름 캔버스에서 **원본 추가** 상자를 클릭하여 원본을 추가합니다.

    ![사용자 추가 단추를 보여 주는 스크린샷](media/tutorial-data-flow/dataflow2.png)
1. 원본 이름을 **MoviesDB** 로 지정합니다. **새로 만들기** 를 클릭하여 새로운 원본 데이터 세트를 만듭니다.

    ![원본 이름을 변경한 후 새로 만들기를 선택하는 위치를 보여주는 스크린샷](media/tutorial-data-flow/dataflow3.png)
1. **Azure Data Lake Storage Gen2** 를 선택합니다. 계속을 클릭합니다.

    ![Azure Data Lake Storage Gen2 타일의 위치를 보여 주는 스크린샷](media/tutorial-data-flow/dataset1.png)
1. **DelimitedText** 를 선택합니다. 계속을 클릭합니다.

    ![DelimitedText tile을 보여 주는 스크린샷](media/tutorial-data-flow/dataset2.png)
1. 데이터 세트를 **MoviesDB** 로 지정합니다. 연결된 서비스 드롭다운에서 **새로 만들기** 를 선택합니다.

    ![연결된 서비스 드롭다운 목록을 보여주는 스크린샷](media/tutorial-data-flow/dataset3.png)
1. 연결된 서비스 만들기 화면에서, ADLS Gen2 연결된 서비스의 이름을 **ADLSGen2** 로 지정하고 인증 방법을 정합니다. 그런 다음 연결 자격 증명을 입력합니다. 이 지침서에서는 계정 키를 사용하여 스토리지 계정에 연결합니다. **연결 테스트** 를 클릭하여 자격 증명이 올바르게 입력되었는지 확인할 수 있습니다. 작업을 마쳤으면 만들기를 클릭합니다.

    ![연결된 서비스](media/tutorial-data-flow/ls1.png)
1. 데이터 세트 만들기 화면으로 돌아오면 **파일 경로** 입력란에 파일이 있는 위치를 입력합니다. 이 지침서에서 MoviesDB.csv 파일은 sample-data 컨테이너에 있습니다. 파일에 헤더가 있으므로 **헤더로서의 첫 번째 행** 을 확인합니다. 스토리지의 파일에서 직접 헤더 스키마를 가져오려면 **연결/저장소에서** 를 선택합니다. 완료되면 확인을 클릭합니다.

    ![데이터 세트](media/tutorial-data-flow/dataset4.png)
1. 디버그 클러스터가 시작된 경우 원본 변환의 **데이터 미리 보기** 탭으로 이동하고 **새로 고침** 을 클릭하여 데이터의 스냅샷을 가져옵니다. 데이터 미리 보기를 사용하여 변환이 올바르게 구성되었는지 확인할 수 있습니다.

    ![변환이 올바르게 구성되었는지 확인하도록 데이터를 미리 볼 수 있는 위치를 표시하는 스크린샷](media/tutorial-data-flow/dataflow4.png)
1. 데이터 흐름 캔버스의 원본 노드 옆에서 더하기 아이콘을 클릭하여 새 변환을 추가합니다. 추가할 첫 번째 변환은 **필터** 입니다.

    ![데이터 흐름 캔버스](media/tutorial-data-flow/dataflow5.png)
1. 필터 변환의 이름을 **FilterYears** 로 지정합니다. **필터 설정** 옆에 있는 식 상자를 클릭하여 식 작성기를 엽니다. 여기에서 필터링 조건을 지정할 것입니다.

    ![식 상자의 필터를 보여주는 스크린샷.](media/tutorial-data-flow/filter1.png)
1. 데이터 흐름 식 작성기를 사용하면 다양한 변환에 사용할 식을 대화형으로 빌드할 수 있습니다. 식에는 기본 제공 함수, 입력 스키마의 열 및 사용자 정의 매개 변수가 포함될 수 있습니다. 식 빌드 방법에 대한 자세한 내용은 [데이터 흐름 식 작성기](concepts-data-flow-expression-builder.md)를 참조하세요.

    이 지침서에서는 1910년과 2000년 사이에 개봉된 코미디 장르 영화를 필터링하려고 합니다. 연도는 현재 문자열이므로 ```toInteger()``` 함수를 사용하여 정수로 변환해야 합니다. 크거나 같음(>=) 및 작거나 같음(<=) 연산자를 사용하여 리터럴 연도 값 1910 및 2000을 비교합니다. 이 식을 and(&&) 연산자로 결합합니다. 식은 다음과 같이 됩니다.

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000```

    코미디 영화를 찾으려면 ```rlike()``` 함수를 사용하여 장르 열에서 'Comedy' 패턴을 찾을 수 있습니다. 다음과 같은 rlike 식과 연도 비교를 결합합니다.

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000 && rlike(genres, 'Comedy')```

    디버그 클러스터가 활성화된 경우 **새로 고침** 을 클릭하여 사용된 입력과 비교한 식 출력을 확인하여 논리를 확인할 수 있습니다. 데이터 흐름 식 언어를 사용하여 이 논리를 수행하는 방법에는 하나 이상의 정답이 있습니다.

    ![Assert](media/tutorial-data-flow/filter2.png)

    식 작성을 마쳤으면 **저장 및 마침** 을 클릭합니다.

1. 필터가 제대로 작동하는지 확인하려면 **데이터 미리 보기** 를 가져옵니다.

    ![데이터 미리 보기 탭을 보여주는 스크린샷.](media/tutorial-data-flow/filter3.png)
1. 다음으로 추가할 변환은 **스키마 한정자** 아래의 **집계** 변환입니다.

    ![집계 스키마 한정자를 보여 주는 스크린샷.](media/tutorial-data-flow/agg1.png)
1. 집계 변환의 이름을 **AggregateComedyRatings** 으로 지정합니다. **그룹화 기준** 탭의 드롭다운에서 **연도** 를 선택하여 영화의 발표 연도별로 집계를 그룹화합니다.

    ![집계 설정에서 그룹화 방법 탭의 연도 옵션을 보여 주는 스크린샷.](media/tutorial-data-flow/agg2.png)
1. **집계** 탭으로 이동합니다. 왼쪽 텍스트 상자에서 집계 열의 이름을 **AverageComedyRating** 으로 지정합니다. 식 작성기를 통해 집계 식을 입력하려면 오른쪽 식 상자를 클릭합니다.

    ![집계 설정에서 집계 탭에 있는 연도 옵션을 보여주는 스크린샷.](media/tutorial-data-flow/agg3.png)
1. **Rating** 열의 평균을 구하려면 ```avg()``` 집계 함수를 사용합니다. **평점** 은 문자열이고 ```avg()```은 숫자 입력을 사용하기 때문에 ```toInteger()``` 함수를 통해 값을 숫자로 변환해야 합니다. 이 식은 다음과 같습니다.

    ```avg(toInteger(Rating))```

    완료되면 **저장 및 종료** 를 클릭합니다.

    ![저장된 식을 보여주는 스크린샷.](media/tutorial-data-flow/agg4.png)
1. 변환 출력을 보려면 **데이터 미리 보기** 탭으로 이동합니다. **year** 와 **AverageComedyRating** 이라는 두 개의 열만 있습니다.

    ![집계](media/tutorial-data-flow/agg3.png)
1. 다음으로 **대상** 아래에 **싱크** 변환을 추가하려고 합니다.

    ![대상 아래에 싱크대 변환을 추가할 위치를 보여주는 스크린샷.](media/tutorial-data-flow/sink1.png)
1. 싱크 이름을 **Sink** 로 지정합니다. **새로 만들기** 를 클릭하여 싱크 데이터 세트를 만듭니다.

    ![싱크 이름을 지정할 수 있는 위치를 보여주는 스크린샷 및 새 싱크 데이터 집합을 만들 수 있습니다.](media/tutorial-data-flow/sink2.png)
1. **Azure Data Lake Storage Gen2** 를 선택합니다. 계속을 클릭합니다.

    ![선택할 수 있는 Azure Data Lake Storage Gen2 타일을 보여주는 스크린샷.](media/tutorial-data-flow/dataset1.png)
1. **DelimitedText** 를 선택합니다. 계속을 클릭합니다.

    ![데이터 세트](media/tutorial-data-flow/dataset2.png)
1. 싱크 데이터 세트의 이름을 **MoviesSink** 로 지정합니다. 연결된 서비스의 경우 6단계에서 생성한 ADLS Gen2 연결된 서비스를 선택합니다. 데이터를 쓸 출력 폴더를 입력합니다. 이 지침서에서는 컨테이너 ‘sample-data’의 폴더 ‘output’에 쓰고 있습니다. 폴더는 미리 존재하지 않아도 되고 동적으로 만들 수 있습니다. **헤더로서 첫 번째 행** 을 true로 설정하고 **스키마 가져오기** 에 대해 **없음** 을 선택합니다. 마침을 클릭합니다.

    ![sink](media/tutorial-data-flow/sink3.png)

이제 데이터 흐름 빌드를 마쳤습니다. 파이프라인에서 실행할 준비가 되었습니다.

## <a name="running-and-monitoring-the-data-flow"></a>데이터 흐름 실행 및 모니터링

파이프라인을 게시하기 전에 디버그할 수 있습니다. 이 단계에서는 데이터 흐름 파이프라인의 디버그 실행을 트리거합니다. 데이터 미리 보기는 데이터를 쓰지 않지만, 디버그 실행은 싱크 대상에 데이터를 씁니다.

1. 파이프라인 캔버스로 이동합니다. 디버그 실행을 트리거하려면 **디버그** 를 클릭합니다.

    ![디버그가 강조 표시된 파이프라인 캔버스를 보여주는 스크린샷](media/tutorial-data-flow/pipeline1.png)
1. 데이터 흐름 활동의 파이프라인 디버그는 활성 디버그 클러스터를 사용하지만 여전히 초기화하는 데 1분 이상 걸립니다. **출력** 탭을 통해 진행 상황을 추적할 수 있습니다. 일단 실행이 성공하면 안경 아이콘을 클릭하여 모니터링 창을 엽니다.

    ![파이프라인](media/tutorial-data-flow/pipeline2.png)
1. 모니터링 창에서 각 변환 단계에 소요된 시간과 행 수를 볼 수 있습니다.

    ![각 변환 단계에서 사용된 행 수와 시간을 볼 수 있는 모니터링 창을 보여주는 스크린샷.](media/tutorial-data-flow/pipeline3.png)
1. 변환을 클릭하면 데이터의 열 및 분할에 대한 자세한 정보를 볼 수 있습니다.

    ![모니터링](media/tutorial-data-flow/pipeline4.png)

이 지침서를 올바르게 수행했다면 싱크 폴더에 83개의 행과 2개의 열이 작성되어 있을 것입니다. Blob Storage를 확인하여 데이터가 올바른지 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 지침서의 파이프라인은 1910에서 2000 사이의 평균 등급을 집계하고 ADLS에 데이터를 기록하는 데이터 흐름을 실행합니다. 구체적으로 다음 작업 방법을 알아보았습니다.

> [!div class="checklist"]
> * 데이터 팩터리를 만듭니다.
> * Data Flow 작업으로 파이프라인 만들기
> * 네 가지 변환으로 매핑 데이터 흐름을 빌드합니다.
> * 파이프라인 실행 테스트
> * 데이터 흐름 모니터링 작업

[Data Flow 표현 언어](data-flow-expression-functions.md)에 대한 자세한 정보를 알아봅니다.

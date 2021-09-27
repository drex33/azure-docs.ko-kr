---
title: '빠른 시작: 데이터 흐름 매핑을 사용하여 데이터 변환'
description: 이 자습서에서는 Azure Synapse Analytics를 통해 데이터 흐름을 매핑하여 데이터를 변환하기 위한 단계별 지침을 제공합니다.
author: djpmsft
ms.author: daperlov
ms.reviewer: makromer
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/03/2020
ms.openlocfilehash: 481d4534f7213d55c795383d6112fd93f7997c9c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128596869"
---
# <a name="quickstart-transform-data-using-mapping-data-flows"></a>빠른 시작: 데이터 흐름 매핑을 사용하여 데이터 변환

이 빠른 시작에서는 데이터 흐름 매핑을 사용하여 ADLS Gen2(Azure Data Lake Storage Gen2) 원본에서 ADLS Gen2 싱크로 데이터를 변환하는 파이프라인을 만들기 위해 Azure Synapse Analytics를 사용합니다. 이 빠른 시작의 구성 패턴은 매핑 데이터 흐름을 사용하여 데이터를 변환할 때 확장될 수 있습니다.

이 빠른 시작에서는 다음 단계를 수행합니다.

> [!div class="checklist"]
> * Azure Synapse Analytics에서 데이터 흐름 활동을 사용하여 파이프라인을 만듭니다.
> * 네 가지 변환으로 매핑 데이터 흐름을 만듭니다.
> * 파이프라인 실행 테스트
> * 데이터 흐름 모니터링 작업

## <a name="prerequisites"></a>필수 구성 요소

* **Azure 구독**: Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* **Azure Synapse 작업 영역**: [빠른 시작: Synapse 작업 영역](quickstart-create-workspace.md) 만들기의 지침에 따라 Azure Portal을 사용하여 Synapse 작업 영역을 만듭니다.
* **Azure Storage 계정**: ADLS 스토리지를 *원본* 및 *싱크* 데이터 저장소로 사용합니다. 스토리지 계정이 없는 경우 [Azure Storage 계정 만들기](../storage/common/storage-account-create.md)를 참조하세요.

    이 지침서에서 변형하는 파일은 [여기](https://raw.githubusercontent.com/djpmsft/adf-ready-demo/master/moviesDB.csv)에서 찾을 수 있는 MoviesDB.csv입니다. GitHub에서 파일을 검색하려면 해당 콘텐츠를 원하는 텍스트 편집기에 복사하여 로컬에 .csv 파일로 저장합니다. 스토리지 계정에 파일을 업로드하려면 [Azure Portal을 사용하여 Blob 업로드](../storage/blobs/storage-quickstart-blobs-portal.md)를 참조하세요. 이 예에서는 ‘sample-data’라는 컨테이너를 참조합니다.

### <a name="navigate-to-the-synapse-studio"></a>Synapse Studio로 이동

Azure Synapse 작업 영역이 만들어지면 다음 두 가지 방법으로 Synapse Studio를 열 수 있습니다.

* [Azure Portal](https://ms.portal.azure.com/#home)에서 Synapse 작업 영역을 엽니다. 시작하기 아래의 Open Synapse Studio 카드에서 **열기** 를 선택합니다.
* [Azure Synapse Analytics](https://web.azuresynapse.net/)를 열고, 작업 영역에 로그인합니다.

이 빠른 시작에서는 "adftest2020"이라는 작업 영역을 예로 사용합니다. 그러면 자동으로 Synapse Studio 홈 페이지로 이동합니다.

![Synapse Studio 홈 페이지](media/doc-common-process/synapse-studio-home.png)

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>데이터 흐름 작업으로 파이프라인 만들기

파이프라인에는 일련의 활동을 실행하기 위한 논리적 흐름이 포함됩니다. 이 섹션에서는 데이터 흐름 작업이 포함된 파이프라인을 만듭니다.

1. **통합** 탭으로 이동합니다. 파이프라인 헤더 옆에 있는 더하기 아이콘, [파이프라인]을 차례로 선택합니다.

   ![새 파이프라인 만들기](media/doc-common-process/new-pipeline.png)

1. 파이프 라인의 **속성** 설정 페이지에서 **이름** 에 **TransformMovies** 를 입력합니다.

1. *작업* 창의 *이동 및 변환* 아래에서 **데이터 흐름** 을 파이프라인 캔버스로 끕니다.

1. **데이터 흐름 추가** 페이지 팝업에서 **새 데이터 흐름 만들기** -> **데이터 흐름** 을 선택합니다. 완료되면 **확인** 을 클릭합니다.

   ![데이터 흐름 만들기](media/quickstart-data-flow/new-data-flow.png)

1. **속성** 페이지에서 데이터 흐름의 이름을 **TransformMovies** 로 지정합니다.

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>데이터 흐름 캔버스의 변환 논리 빌드

데이터 흐름을 만들면 데이터 흐름 캔버스로 자동 전송됩니다. 이 단계에서는 ADLS 스토리지의 MoviesDB.csv를 가져와 1910년부터 2000년까지 코미디 장르의 평균 평점을 집계하는 데이터 흐름을 만듭니다. 그런 다음 이 파일을 ADLS 스토리지에 다시 씁니다.

1. 데이터 흐름 캔버스 위에서 **데이터 흐름 디버그** 슬라이더를 켭니다. 디버그 모드에서는 라이브 Spark 클러스터에 대한 변환 논리의 대화형 테스트를 수행할 수 있습니다. 데이터 흐름 클러스터는 준비하는 데 5~7분 정도 걸리며, 데이터 흐름 개발을 수행할 계획이라면 우선 디버그를 사용하도록 설정하는 것이 좋습니다. 자세한 내용은 [디버그 모드](../data-factory/concepts-data-flow-debug-mode.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)를 참조하세요.

    ![디버그 슬라이드 켜기](media/quickstart-data-flow/debug-on.png)

1. 데이터 흐름 캔버스에서 **원본 추가** 상자를 클릭하여 원본을 추가합니다.

1. 원본 이름을 **MoviesDB** 로 지정합니다. **새로 만들기** 를 클릭하여 새로운 원본 데이터 세트를 만듭니다.

    ![새 원본 데이터 세트 만들기](media/quickstart-data-flow/new-source-dataset.png)

1. **Azure Data Lake Storage Gen2** 를 선택합니다. 계속을 클릭합니다.

    ![Azure Data Lake Storage Gen2 선택](media/quickstart-data-flow/select-source-dataset.png)

1. **DelimitedText** 를 선택합니다. 계속을 클릭합니다.

1. 데이터 세트의 이름을 **MoviesDB** 로 지정합니다. 연결된 서비스 드롭다운에서 **새로 만들기** 를 선택합니다.

1. 연결된 서비스 만들기 화면에서 ADLS Gen2 연결된 서비스의 이름을 **ADLSGen2** 로 지정하고 인증 방법을 지정합니다. 그런 다음 연결 자격 증명을 입력합니다. 이 빠른 시작에서는 계정 키를 사용하여 스토리지 계정에 연결합니다. **연결 테스트** 를 클릭하여 자격 증명이 올바르게 입력되었는지 확인할 수 있습니다. 작업을 마쳤으면 **만들기** 를 클릭합니다.

    ![원본 연결 서비스 만들기](media/quickstart-data-flow/adls-gen2-linked-service.png)

1. 데이터 세트 만들기 화면으로 돌아오면 **파일 경로** 입력란에 파일이 있는 위치를 입력합니다. 이 빠른 시작에서 'MoviesDB.csv' 파일은 'sample-data' 컨테이너에 있습니다. 파일에 헤더가 있으므로 **첫 번째 행을 헤더로** 를 선택합니다. 스토리지의 파일에서 직접 헤더 스키마를 가져오려면 **연결/저장소에서** 를 선택합니다. 완료되면 **확인** 을 클릭합니다.

    ![원본 데이터 세트 설정](media/quickstart-data-flow/source-dataset-properties.png)

1. 디버그 클러스터가 시작된 경우 원본 변환의 **데이터 미리 보기** 탭으로 이동하고 **새로 고침** 을 클릭하여 데이터의 스냅샷을 가져옵니다. 데이터 미리 보기를 사용하여 변환이 올바르게 구성되었는지 확인할 수 있습니다.

    ![데이터 미리 보기](media/quickstart-data-flow/data-preview.png)

1. 데이터 흐름 캔버스의 원본 노드 옆에 있는 더하기 아이콘을 클릭하여 새 변환을 추가합니다. 추가할 첫 번째 변환은 **필터** 입니다.

    ![필터 추가](media/quickstart-data-flow/add-filter.png)

1. 필터 변환의 이름을 **Filteryears** 로 지정합니다. **필터 설정** 옆에 있는 식 상자를 클릭하여 식 작성기를 엽니다. 여기에서 필터링 조건을 지정합니다.

1. 데이터 흐름 식 작성기를 사용하면 다양한 변환에 사용할 식을 대화형으로 작성할 수 있습니다. 식에는 기본 제공 함수, 입력 스키마의 열 및 사용자 정의 매개 변수가 포함될 수 있습니다. 식 작성 방법에 대한 자세한 내용은 [데이터 흐름 식 작성기](../data-factory/concepts-data-flow-expression-builder.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)를 참조하세요.

    이 빠른 시작에서는 1910년과 2000년 사이에 발표된 코미디 장르 영화를 필터링하려고 합니다. 연도는 현재 문자열이므로 ```toInteger()``` 함수를 사용하여 정수로 변환해야 합니다. 크거나 같음(>=) 및 작거나 같음(<=) 연산자를 사용하여 리터럴 연도 값 1910 및 200-을 비교합니다. 이 식을 and(&&) 연산자로 결합합니다. 식은 다음과 같이 됩니다.

    `toInteger(year) >= 1910 && toInteger(year) <= 2000`

    코미디 영화를 찾으려면 `rlike()` 함수를 사용하여 장르 열에서 'Comedy' 패턴을 찾을 수 있습니다. `rlike`식을 연도 비교와 통합하여 다음을 얻습니다.

    `toInteger(year) >= 1910 && toInteger(year) <= 2000 && rlike(genres, 'Comedy')`

    ![필터링 조건 지정](media/quickstart-data-flow/visual-expression-builder.png)

    디버그 클러스터가 활성화된 경우 **새로 고침** 을 클릭하여 사용된 입력과 비교한 다음 식 출력을 확인하여 논리를 확인할 수 있습니다. 데이터 흐름 식 언어를 사용하여 이 논리를 수행하는 방법에는 두 개 이상의 정답이 있습니다.

    식 작성을 마쳤으면 **저장 및 마침** 을 클릭합니다.

1. 필터가 제대로 작동하는지 확인하려면 **데이터 미리 보기** 를 가져옵니다.

1. 다음으로 추가할 변환은 **스키마 수정자** 아래의 **집계** 변환입니다.

    ![집계 추가](media/quickstart-data-flow/add-aggregate.png)

1. 집계 변환의 이름을 **AggregateByPaymentType** 으로 지정합니다. **그룹화 기준** 탭의 드롭다운에서 **연도** 를 선택하여 영화의 발표 연도별로 집계를 그룹화합니다.

    ![집계 설정 1](media/quickstart-data-flow/aggregate-settings.png)

1. **집계** 탭으로 이동합니다. 왼쪽 텍스트 상자에서 집계 열의 이름을 **AverageComedyRating** 으로 지정합니다. 식 작성기를 통해 집계 식을 입력하려면 오른쪽 식 상자를 클릭합니다.

    ![집계 설정 2](media/quickstart-data-flow/aggregate-settings-2.png)

1. **평점** 열의 평균을 구하려면 ```avg()``` 집계 함수를 사용합니다. **평점** 은 문자열이고 ```avg()```은 숫자 입력을 사용하기 때문에 ```toInteger()``` 함수를 통해 값을 숫자로 변환해야 합니다. 이 식은 다음과 같습니다.

    `avg(toInteger(Rating))`

    완료되면 **저장 및 마침** 을 클릭합니다.

    ![평균 코미디 평점](media/quickstart-data-flow/average-comedy-rating.png)

1. 변환 출력을 보려면 **데이터 미리 보기** 탭으로 이동합니다. **year** 및 **AverageComedyRating** 이라는 두 개의 열만 있습니다.

    ![집계 데이터 미리 보기](media/quickstart-data-flow/transformation-output.png)

1. 다음으로 **대상** 아래에 **싱크** 변환을 추가하려고 합니다.

    ![싱크 추가](media/quickstart-data-flow/add-sink.png)

1. 싱크 이름을 **Sink** 로 지정합니다. **새로 만들기** 를 클릭하여 싱크 데이터 세트를 만듭니다.

1. **Azure Data Lake Storage Gen2** 를 선택합니다. 계속을 클릭합니다.

1. **DelimitedText** 를 선택합니다. 계속을 클릭합니다.

1. 싱크 데이터 세트의 이름을 **MoviesSink** 로 지정합니다. 연결된 서비스의 경우 7단계에서 생성한 ADLS Gen2 연결된 서비스를 선택합니다. 데이터를 쓸 출력 폴더를 입력합니다. 이 빠른 시작에서는 'sample-data' 컨테이너의 'output' 폴더에 씁니다. 폴더는 미리 존재하지 않아도 되며 동적으로 만들 수 있습니다. **첫 번째 행을 헤더로** 를 true로 설정하고 **스키마 가져오기** 에 대해 **없음** 을 선택합니다. 완료되면 **확인** 을 클릭합니다.

    ![싱크 데이터 세트 속성](media/quickstart-data-flow/sink-dataset-properties.png)

이제 데이터 흐름 빌드를 마쳤습니다. 파이프라인에서 실행할 준비가 되었습니다.

## <a name="running-and-monitoring-the-data-flow"></a>데이터 흐름 실행 및 모니터링

파이프라인을 게시하기 전에 디버그할 수 있습니다. 이 단계에서는 데이터 흐름 파이프라인의 디버그 실행을 트리거합니다. 데이터 미리 보기는 데이터를 쓰지 않지만, 디버그 실행은 싱크 대상에 데이터를 씁니다.

1. 파이프라인 캔버스로 이동합니다. 디버그 실행을 트리거하려면 **디버그** 를 클릭합니다.

    ![디버그 파이프라인](media/quickstart-data-flow/debug-pipeline.png)

1. 데이터 흐름 활동의 파이프라인 디버그는 활성 디버그 클러스터를 사용하지만 여전히 초기화하는 데 1분 이상 걸립니다. **출력** 탭을 통해 진행 상황을 추적할 수 있습니다. 실행이 성공하면 안경 아이콘을 클릭하여 모니터링 창을 엽니다.

    ![출력 디버깅](media/quickstart-data-flow/debugging-output.png)

1. 모니터링 창에서 각 변환 단계에 소요된 시간과 행 수를 볼 수 있습니다.

    ![변환 모니터링](media/quickstart-data-flow/4-transformations.png)

1. 변환을 클릭하면 데이터의 열 및 분할에 대한 자세한 정보를 볼 수 있습니다.

    ![변환 세부 정보](media/quickstart-data-flow/transformation-details.png)

이 빠른 시작을 올바르게 수행했다면 싱크 폴더에 83개의 행과 2개의 열이 작성되어 있을 것입니다. Blob Storage를 확인하여 데이터를 확인할 수 있습니다.


## <a name="next-steps"></a>다음 단계

Azure Synapse Analytics 지원에 대해 알아보려면 다음 문서로 계속 진행하세요.

> [!div class="nextstepaction"]
> [파이프라인 및 활동](../data-factory/concepts-pipelines-activities.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)
> [데이터 흐름 매핑 개요](../data-factory/concepts-data-flow-overview.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)
> [데이터 흐름 식 언어](../data-factory/data-flow-expression-functions.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)

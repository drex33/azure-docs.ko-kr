---
title: Azure Data Factory 관리 가상 네트워크 매핑 데이터 흐름을 사용하여 데이터 변환
description: 이 자습서에서는 Azure Data Factory를 통해 데이터 흐름을 매핑하여 데이터를 변환하기 위한 단계별 지침을 제공합니다.
author: ssabat
ms.author: susabat
ms.reviewer: makromer
ms.service: data-factory
ms.subservice: tutorials
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 06/04/2021
ms.openlocfilehash: fb7dfc93048d888eb83088ca2a3f458219fdc393
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128617890"
---
# <a name="transform-data-securely-by-using-mapping-data-flow"></a>매핑 데이터 흐름을 사용하여 안전하게 데이터 변환

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory를 처음 사용하는 경우 [Azure Data Factory 소개](./introduction.md)를 참조하세요.

이 자습서에서는 Data Factory 사용자 인터페이스(UI)를 사용하여 [Data Factory 관리 가상 네트워크](managed-virtual-network-private-endpoint.md)의 매핑 데이터 흐름 사용을 통해 Azure Data Lake Storage Gen2 원본으로부터 Data Lake Storage Gen2 싱크(두 가지 모두 선택한 네트워크에 대해서만 액세스 허용)로 데이터를 복사하고 변환하는 파이프라인을 만듭니다. 매핑 데이터 흐름을 사용하여 데이터를 변환하는 경우 이 자습서의 구성 패턴을 확장할 수 있습니다.

이 자습서에서 수행하는 단계는 다음과 같습니다.

> [!div class="checklist"]
>
> * 데이터 팩터리를 만듭니다.
> * 데이터 흐름 작업을 사용하여 파이프라인을 만듭니다.
> * 네 가지 변환으로 매핑 데이터 흐름을 만듭니다.
> * 파이프라인 실행 테스트
> * 데이터 흐름 작업을 모니터링합니다.

## <a name="prerequisites"></a>사전 요구 사항

* **Azure 구독**. Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* **Azure Storage 계정**. 원본 및 싱크 데이터 저장소 모두로 사용하는 Data Lake Storage. 스토리지 계정이 없는 경우 [Azure Storage 계정 만들기](../storage/common/storage-account-create.md?tabs=azure-portal)를 참조하세요. *스토리지 계정이 선택한 네트워크에서만 액세스를 허용하는지 확인합니다.* 

이 자습서에서 변형할 파일은 moviesDB.csv로, [GitHub 콘텐츠 사이트](https://raw.githubusercontent.com/djpmsft/adf-ready-demo/master/moviesDB.csv)에 있습니다. GitHub에서 파일을 검색하려면 해당 콘텐츠를 원하는 텍스트 편집기에 복사하여 로컬에 .csv 파일로 저장합니다. 스토리지 계정에 파일을 업로드하려면 [Azure Portal을 사용하여 Blob 업로드](../storage/blobs/storage-quickstart-blobs-portal.md)를 참조하세요. 예제에서는 **sample-data** 라는 컨테이너를 참조합니다.

## <a name="create-a-data-factory"></a>데이터 팩터리 만들기

이 단계에서는 데이터 팩터리를 만들고, Data Factory UI를 열어서 데이터 팩터리에 파이프라인을 만듭니다.

1. Microsoft Edge 또는 Google Chrome을 엽니다. 현재 Microsoft Edge 및 Google Chrome 웹 브라우저만 Data Factory UI를 지원합니다.
1. 왼쪽 메뉴에서 **리소스 만들기** > **분석** > **Data Factory** 를 차례로 선택합니다.
1. **새 데이터 팩터리** 페이지의 **이름** 아래에서 **ADFTutorialDataFactory** 를 입력합니다.

   데이터 팩터리 이름은 *전역적으로 고유* 해야 합니다. 이름 값에 대한 오류 메시지가 표시되면 데이터 팩터리에 대한 다른 이름(예: yournameADFTutorialDataFactory)을 입력합니다. Data Factory 아티팩트에 대한 명명 규칙은 [Data Factory 명명 규칙](naming-rules.md)을 참조하세요.

1. 데이터 팩터리를 만들 Azure **구독** 을 선택합니다.
1. **리소스 그룹** 에 대해 다음 단계 중 하나를 사용합니다.

    * **기존 항목 사용** 을 선택하고 드롭다운 목록에서 기존 리소스 그룹을 선택합니다.
    * **새로 만들기** 를 선택하고 리소스 그룹의 이름을 입력합니다. 
         
    리소스 그룹에 대한 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](../azure-resource-manager/management/overview.md)를 참조하세요. 
1. **버전** 에서 **V2** 를 선택합니다.
1. **위치** 에서 데이터 팩터리의 위치를 선택합니다. 지원되는 위치만 드롭다운 목록에 나타납니다. 데이터 팩터리에 사용되는 데이터 저장소(예: Azure Storage, Azure SQL Database) 및 컴퓨팅(예: Azure HDInsight)은 다른 지역에 있을 수 있습니다.

1. **만들기** 를 선택합니다.
1. 만들기가 완료되면 알림 센터에 알림이 표시됩니다. **리소스로 이동** 을 선택하여 **Data Factory** 페이지로 이동합니다.
1. **작성 및 모니터링** 을 선택하여 별도의 탭에서 Data Factory UI를 선택합니다.

## <a name="create-an-azure-ir-in-data-factory-managed-virtual-network"></a>Data Factory 관리 가상 네트워크에서 Azure IR 만들기

이 단계에서는 Azure IR을 만들고 Data Factory 관리 가상 네트워크를 사용하도록 설정합니다.

1. Data Factory 포털에서 **관리** 로 이동하고, **새로 만들기** 를 선택하여 새 Azure IR을 만듭니다.

   :::image type="content" source="./media/tutorial-copy-data-portal-private/create-new-azure-ir.png" alt-text="새 Azure IR을 만드는 과정을 보여주는 스크린샷.":::
1. **통합 런타임 설정** 페이지에서 필요한 기능을 기반으로 만들 통합 런타임을 선택합니다. 이 자습서에서는 **Azure, 자체 호스팅** 을 선택한 다음, **계속** 을 클릭합니다. 
1. **Azure** 를 선택한 다음, **계속** 을 클릭하여 Azure 통합 런타임을 만듭니다.

   :::image type="content" source="./media/tutorial-copy-data-portal-private/azure-ir.png" alt-text="새 Azure IR을 보여주는 스크린샷.":::

1. **가상 네트워크 구성(미리 보기)** 에서 **사용** 을 선택합니다.

   :::image type="content" source="./media/tutorial-copy-data-portal-private/enable-managed-vnet.png" alt-text="새 Azure IR 사용을 보여주는 스크린샷.":::

1. **만들기** 를 선택합니다.

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>데이터 흐름 작업으로 파이프라인 만들기

이 단계에서는 데이터 흐름 작업을 포함하는 파이프라인을 만듭니다.

1. Azure Data Factory 홈페이지에서 **오케스트레이션** 을 선택합니다.

   :::image type="content" source="./media/doc-common-process/get-started-page.png" alt-text="파이프라인 만들기를 보여주는 스크린샷":::

1. 파이프라인의 속성 창에서 파이프라인 이름에 **TransformMovies** 를 입력합니다.
1. **작업** 창에서 **이동 및 변환** 을 확장합니다. **데이터 흐름** 작업을 창에서 파이프라인 캔버스로 끕니다.

1. **데이터 흐름 추가** 팝업에서 **새 데이터 흐름 만들기** 를 선택하고 **매핑 데이터 흐름** 을 선택합니다. 작업이 완료되면 **확인** 을 선택합니다.

    :::image type="content" source="media/tutorial-data-flow-private/mapping-dataflow.png" alt-text="매핑 데이터 흐름을 보여주는 스크린샷.":::

1. 속성 창에서 데이터 흐름의 이름을 **TransformMovies** 로 지정합니다.
1. 파이프라인 캔버스의 상단 막대에서 **데이터 흐름 디버그** 슬라이더를 밉니다. 디버그 모드에서는 라이브 Spark 클러스터에 대한 변환 논리의 대화형 테스트를 수행할 수 있습니다. 데이터 흐름 클러스터는 준비하는 데 5~7분 정도 걸리며, 데이터 흐름 개발을 수행할 계획이라면 우선 디버그를 사용하도록 설정하는 것이 좋습니다. 자세한 내용은 [디버그 모드](concepts-data-flow-debug-mode.md)를 참조하세요.

    :::image type="content" source="media/tutorial-data-flow-private/dataflow-debug.png" alt-text="데이터 흐름 디버그 슬라이더가 있는 위치를 보여주는 스크린샷.":::

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>데이터 흐름 캔버스의 변환 논리 빌드

데이터 흐름을 만들면 데이터 흐름 캔버스로 자동 전송됩니다. 이 단계에서는 Data Lake Storage의 moviesDB.csv를 가져와 1910년부터 2000년까지 코미디 장르의 평균 평점을 집계하는 데이터 흐름을 만듭니다. 그런 다음 이 파일을 Data Lake Storage에 다시 씁니다.

### <a name="add-the-source-transformation"></a>원본 변환 추가

이 단계에서는 Data Lake Storage Gen2를 원본으로 설정합니다.

1. 데이터 흐름 캔버스에서 **원본 추가** 상자를 선택하여 원본을 추가합니다.

1. 원본 이름을 **MoviesDB** 로 지정합니다. **새로 만들기** 를 선택하여 새 원본 데이터 세트를 만듭니다.

1. **Azure Data Lake Storage Gen2** 를 선택한 다음 **계속** 을 선택합니다.

1. **DelimitedText** 를 선택하고 **계속** 을 클릭합니다.

1. 데이터 세트의 이름을 **MoviesDB** 로 지정합니다. 연결된 서비스 드롭다운에서 **새로 만들기** 를 선택합니다.

1. 연결된 서비스 만들기 화면에서 Data Lake Storage Gen2 연결된 서비스의 이름을 **ADLSGen2** 로 지정하고 인증 방법을 지정합니다. 그런 다음 연결 자격 증명을 입력합니다. 이 자습서에서는 **계정 키** 를 사용하여 스토리지 계정에 연결합니다. 

1. **대화형 작성** 을 사용하도록 설정해야 합니다. 사용하도록 설정하는 데 1분 정도 걸릴 수 있습니다.

    :::image type="content" source="./media/tutorial-data-flow-private/interactive-authoring.png" alt-text="대화형 작성을 보여주는 스크린샷":::

1. **연결 테스트** 를 클릭합니다. 스토리지 계정에서 프라이빗 엔드포인트를 생성 및 승인하지 않고서는 액세스를 허용하지 않기 때문에 실패합니다. 오류 메시지에는 관리형 프라이빗 엔드포인트를 만들기 위해 따를 수 있는 프라이빗 엔드포인트 만들기에 대한 링크가 표시됩니다. 또 다른 방법은 **관리** 탭으로 직접 이동하여 [이 섹션](#create-a-managed-private-endpoint)의 지침에 따라 관리형 프라이빗 엔드포인트를 만드는 것입니다.

1. 대화 상자를 열어 둔 채 스토리지 계정으로 이동합니다.

1. [이 섹션](#approval-of-a-private-link-in-a-storage-account)의 지침에 따라 프라이빗 링크를 승인합니다.

1. 대화 상자로 돌아갑니다. **연결 테스트** 를 다시 선택하고 **만들기** 를 선택하여 연결된 서비스를 배포합니다.

1. 데이터 세트 만들기 화면으로 돌아오면 **파일 경로** 입력란에 파일이 있는 위치를 입력합니다. 이 자습서에서 MoviesDB.csv 파일은 **sample-data** 컨테이너에 있습니다. 파일에 헤더가 있으므로 **첫 번째 행을 헤더로** 확인란을 선택합니다. 스토리지의 파일에서 직접 헤더 스키마를 가져오려면 **연결/저장소에서** 를 선택합니다. 작업이 완료되면 **확인** 을 선택합니다.

    :::image type="content" source="media/tutorial-data-flow-private/source-file-path.png" alt-text="원본 경로를 보여주는 스크린샷.":::

1. 디버그 클러스터가 시작된 경우 원본 변환의 **데이터 미리 보기** 탭으로 이동하고 **새로 고침** 을 선택하여 데이터의 스냅샷을 가져옵니다. 데이터 미리 보기를 사용하여 변환이 올바르게 구성되었는지 확인할 수 있습니다.

    :::image type="content" source="media/tutorial-data-flow-private/data-preview.png" alt-text="데이터 미리 보기 탭을 보여주는 스크린샷.":::

#### <a name="create-a-managed-private-endpoint"></a>관리형 프라이빗 엔드포인트 만들기

이전 연결을 테스트할 때 하이퍼링크를 사용하지 않은 경우 경로를 따릅니다. 이제 만든 연결된 서비스에 연결할 관리형 프라이빗 엔드포인트를 만들어야 합니다.

1. **관리** 탭으로 이동합니다.

   > [!NOTE]
   > 일부 Data Factory 인스턴스에는 **관리** 탭을 사용할 수 없습니다. 표시되지 않으면 **작성자** > **연결** > **프라이빗 엔드포인트** 를 선택하여 프라이빗 엔드포인트에 액세스할 수 있습니다.

1. **관리형 프라이빗 엔드포인트** 섹션으로 이동합니다.
1. **관리형 프라이빗 엔드포인트** 아래에서 **+ 새로 만들기** 를 선택합니다.

    :::image type="content" source="./media/tutorial-data-flow-private/new-managed-private-endpoint.png" alt-text="관리형 프라이빗 엔드포인트 새로 만들기 단추를 보여주는 스크린샷"::: 

1. 목록에서 Azure Data Lake Storage Gen2 타일을 선택하고 **계속** 을 선택합니다.
1. 만든 스토리지 계정의 이름을 입력합니다.
1. **만들기** 를 선택합니다.
1. 몇 초 후면 만든 프라이빗 링크에 승인이 필요하다고 표시됩니다.
1. 만든 프라이빗 엔드포인트를 선택합니다. 스토리지 계정 수준에서 프라이빗 엔드포인트를 승인하도록 안내하는 하이퍼링크가 표시됩니다.

    :::image type="content" source="./media/tutorial-data-flow-private/manage-private-endpoint.png" alt-text="프라이빗 엔드포인트 관리 창을 보여주는 스크린샷."::: 

#### <a name="approval-of-a-private-link-in-a-storage-account"></a>스토리지 계정에서 프라이빗 링크 승인

1. 스토리지 계정의 **설정** 섹션 아래에서 **프라이빗 엔드포인트 연결** 로 이동합니다.

1. 만든 프라이빗 엔드포인트의 확인란을 선택하고 **승인** 을 선택합니다.

    :::image type="content" source="./media/tutorial-data-flow-private/approve-private-endpoint.png" alt-text="프라이빗 엔드포인트 승인 단추를 보여주는 스크린샷.":::

1. 설명을 추가하고 **예** 를 선택합니다.
1. Data Factory에서 **관리** 탭의 **관리형 프라이빗 엔드포인트** 섹션으로 돌아갑니다.
1. 약 1분 후에 프라이빗 엔드포인트에 대한 승인이 표시됩니다.

### <a name="add-the-filter-transformation"></a>필터 변환 추가

1. 데이터 흐름 캔버스의 원본 노드 옆에 있는 더하기 아이콘을 선택하여 새 변환을 추가합니다. 추가할 첫 번째 변환은 **필터** 입니다.

    :::image type="content" source="media/tutorial-data-flow-private/add-filter.png" alt-text="필터 추가를 보여주는 스크린샷.":::
1. 필터 변환의 이름을 **FilterYears** 로 지정합니다. **필터 설정** 옆에 있는 식 상자를 선택하여 식 작성기를 엽니다. 여기에서 필터링 조건을 지정합니다.

    :::image type="content" source="media/tutorial-data-flow-private/filter-years.png" alt-text="FilterYears를 보여주는 스크린샷.":::
1. 데이터 흐름 식 작성기를 사용하면 다양한 변환에 사용할 식을 대화형으로 작성할 수 있습니다. 식에는 기본 제공 함수, 입력 스키마의 열 및 사용자 정의 매개 변수가 포함될 수 있습니다. 식 작성 방법에 대한 자세한 내용은 [데이터 흐름 식 작성기](./concepts-data-flow-expression-builder.md)를 참조하세요.

    * 이 자습서에서는 1910년과 2000년 사이의 코미디 영화를 필터링하려고 합니다. 연도는 현재 문자열이므로 `toInteger()` 함수를 사용하여 정수로 변환해야 합니다. 크거나 같음(>=) 및 작거나 같음(<=) 연산자를 사용하여 리터럴 연도 값 1910 및 2000을 비교합니다. 이 식을 and(&&) 연산자로 결합합니다. 식은 다음과 같이 됩니다.

        `toInteger(year) >= 1910 && toInteger(year) <= 2000`

    * 코미디 영화를 찾으려면 `rlike()` 함수를 사용하여 장르 열에서 'Comedy' 패턴을 찾을 수 있습니다. `rlike`식을 연도 비교와 통합하여 다음을 얻습니다.

        `toInteger(year) >= 1910 && toInteger(year) <= 2000 && rlike(genres, 'Comedy')`

    * 디버그 클러스터가 활성화된 경우 **새로 고침** 을 선택하여 사용된 입력과 비교한 다음 식 출력을 확인하여 논리를 확인할 수 있습니다. 데이터 흐름 식 언어를 사용하여 이 논리를 수행하는 방법에는 두 개 이상의 정답이 있습니다.

        :::image type="content" source="media/tutorial-data-flow-private/filter-expression.png" alt-text="필터 식을 보여주는 스크린샷.":::

    * 식을 완료한 후 **저장 및 마침** 을 선택합니다.

1. 필터가 제대로 작동하는지 확인하려면 **데이터 미리 보기** 를 가져옵니다.

    :::image type="content" source="media/tutorial-data-flow-private/filter-data.png" alt-text="필터링된 데이터 미리 보기를 보여주는 스크린샷.":::

### <a name="add-the-aggregate-transformation"></a>집계 변환 추가

1. 다음으로 추가할 변환은 **스키마 수정자** 아래의 **집계** 변환입니다.

    :::image type="content" source="media/tutorial-data-flow-private/add-aggregate.png" alt-text="집계 추가를 보여주는 스크린샷.":::
1. 집계 변환의 이름을 **AggregateComedyRating** 으로 지정합니다. **그룹화 기준** 탭의 드롭다운 상자에서 **연도** 를 선택하여 영화의 발표 연도별로 집계를 그룹화합니다.

    :::image type="content" source="media/tutorial-data-flow-private/group-by-year.png" alt-text="집계 그룹을 보여주는 스크린샷.":::
1. **집계** 탭으로 이동합니다. 왼쪽 텍스트 상자에서 집계 열의 이름을 **AverageComedyRating** 으로 지정합니다. 식 작성기를 통해 집계 식을 입력하려면 오른쪽 식 상자를 선택합니다.

    :::image type="content" source="media/tutorial-data-flow-private/name-column.png" alt-text="집계 열 이름을 보여주는 스크린샷.":::
1. **Rating** 열의 평균을 구하려면 ```avg()``` 집계 함수를 사용합니다. **Rating** 은 문자열이고 ```avg()```은 숫자 입력을 사용하기 때문에 ```toInteger()``` 함수를 통해 값을 숫자로 변환해야 합니다. 이 식은 다음과 같습니다.

    `avg(toInteger(Rating))`

1. 완료 후 **저장 및 마침** 을 선택합니다.

    :::image type="content" source="media/tutorial-data-flow-private/save-aggregate.png" alt-text="집계 저장을 보여주는 스크린샷.":::
1. 변환 출력을 보려면 **데이터 미리 보기** 탭으로 이동합니다. **year** 및 **AverageComedyRating** 이라는 두 개의 열만 있습니다.

### <a name="add-the-sink-transformation"></a>싱크 변환 추가

1. 다음으로 **대상** 아래에 **싱크** 변환을 추가하려고 합니다.

    :::image type="content" source="media/tutorial-data-flow-private/add-sink.png" alt-text="싱크 추가를 보여주는 스크린샷.":::
1. 싱크 이름을 **Sink** 로 지정합니다. **새로 만들기** 를 선택하여 싱크 데이터 세트를 만듭니다.

    :::image type="content" source="media/tutorial-data-flow-private/create-sink.png" alt-text="싱크 만들기를 보여주는 스크린샷.":::
1. **새 데이터 세트** 페이지에서 **Azure Data Lake Storage Gen2** 를 선택한 다음, **계속** 을 선택합니다.

1. **형식 선택** 페이지에서 **DelimitedText** 를 선택하고 **계속** 을 선택합니다.

1. 싱크 데이터 세트의 이름을 **MoviesSink** 로 지정합니다. 연결된 서비스의 경우 원본 변환에 대해 만든 것과 동일한 **ADLSGen2** 연결된 서비스를 선택합니다. 데이터를 쓸 출력 폴더를 입력합니다. 이 자습서에서는 컨테이너 **sample-data** 의 폴더 **output** 에 쓰고 있습니다. 폴더는 미리 존재하지 않아도 되며 동적으로 만들 수 있습니다. **첫 번째 행을 헤더로** 확인란을 선택한 다음, **스키마 가져오기** 에 대해 **없음** 을 선택합니다. **확인** 을 선택합니다.

    :::image type="content" source="media/tutorial-data-flow-private/sink-file-path.png" alt-text="싱크 경로를 보여주는 스크린샷.":::

이제 데이터 흐름 빌드를 마쳤습니다. 파이프라인에서 실행할 준비가 되었습니다.

## <a name="run-and-monitor-the-data-flow"></a>데이터 흐름 실행 및 모니터링

파이프라인을 게시하기 전에 디버그할 수 있습니다. 이 단계에서는 데이터 흐름 파이프라인의 디버그 실행을 트리거합니다. 데이터 미리 보기는 데이터를 쓰지 않지만 디버그 실행은 싱크 대상에 데이터를 씁니다.

1. 파이프라인 캔버스로 이동합니다. 디버그 실행을 트리거하려면 **디버그** 를 선택합니다.

1. 데이터 흐름 활동의 파이프라인 디버깅은 활성 디버그 클러스터를 사용하지만 여전히 초기화하는 데 1분 이상 걸립니다. **출력** 탭을 통해 진행률을 추적할 수 있습니다. 실행이 완료되면 안경 아이콘을 선택하여 실행 세부 정보를 확인합니다.

1. 세부 정보 페이지에서 각 변환 단계에서 소요된 시간 및 행 수를 확인할 수 있습니다.

    :::image type="content" source="media/tutorial-data-flow-private/run-details.png" alt-text="모니터링 실행을 보여주는 스크린샷.":::
1. 변환을 선택하면 데이터의 열 및 분할에 대한 자세한 정보를 볼 수 있습니다.

이 자습서를 올바르게 수행했다면 싱크 폴더에 83개의 행과 2개의 열이 작성되어 있을 것입니다. Blob Storage를 확인하여 데이터가 올바른지 확인할 수 있습니다.

## <a name="summary"></a>요약

이 자습서에서는 Data Factory UI를 사용하여 [Data Factory 관리 가상 네트워크](managed-virtual-network-private-endpoint.md)의 매핑 데이터 흐름 사용을 통해 Data Lake Storage Gen2 원본으로부터 Data Lake Storage Gen2 싱크(두 가지 모두 선택한 네트워크에 대해서만 액세스 허용)로 데이터를 복사하고 변환하는 파이프라인을 만듭니다.

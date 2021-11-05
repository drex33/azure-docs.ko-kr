---
title: Azure Data Factory 및 Azure Data Share를 사용하여 데이터 통합
description: Azure Data Factory 및 Azure Data Share를 사용하여 데이터 복사, 변환 및 공유
author: dcstwh
ms.author: weetok
ms.service: data-factory
ms.subservice: tutorials
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 06/04/2021
ms.openlocfilehash: 4d99139e708f0b69b3ddacd40e380853dabf8b2b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131077955"
---
# <a name="data-integration-using-azure-data-factory-and-azure-data-share"></a>Azure Data Factory 및 Azure Data Share를 사용하여 데이터 통합

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

고객은 최신 데이터 웨어하우스 및 분석 프로젝트에 착수할 때 더 많은 데이터가 필요할 뿐 아니라 전체 데이터 자산의 데이터를 좀 더 자세히 볼 수 있어야 합니다. 이 워크샵에서는 Azure Data Factory 및 Azure Data Share의 향상된 기능을 활용하여 Azure에서 데이터 통합 및 관리를 간소화하는 방법을 자세히 살펴봅니다. 

코드 없는 ETL/ELT 사용부터 데이터에 대한 포괄적인 뷰 만들기까지, 데이터 엔지니어들은 Azure Data Factory의 향상된 기능을 사용하여 안심하고 더 많은 데이터를 가져올 수 있으며, 따라서 기업에 더 많은 가치를 제공할 수 있습니다. Azure Data Share를 사용하면 관리되는 방식으로 B2B 공유를 수행할 수 있습니다.

이 워크샵에서는 ADF(Azure Data Factory)를 사용하여 Azure SQL Database에서 ADLS Gen2(Azure Data Lake Storage Gen2)로 데이터를 수집합니다. 데이터를 레이크로 가져온 후에는 데이터 팩터리의 네이티브 변환 서비스인 매핑 데이터 흐름을 통해 데이터를 변환하고 Azure Synapse Analytics로 싱크합니다. 그런 다음, Azure Data Share를 사용하여 일부 추가 데이터와 함께 테이블을 변환된 데이터와 공유합니다. 

이 랩에 사용되는 데이터는 뉴욕시 택시 데이터입니다. 이 데이터를 SQL Database의 데이터베이스로 가져오려면 [taxi-data bacpac 파일](https://github.com/djpmsft/ADF_Labs/blob/master/sample-data/taxi-data.bacpac)을 다운로드합니다.

## <a name="prerequisites"></a>필수 구성 요소

* **Azure 구독**: Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.

* **Azure SQL Database**: SQL DB가 없는 경우 [SQL DB 계정 만들기](../azure-sql/database/single-database-create-quickstart.md?tabs=azure-portal)를 참조하세요.

* **Azure Data Lake Storage Gen2 스토리지 계정**: ADLS Gen2 스토리지 계정이 없는 경우 [ADLS Gen2 스토리지 계정 만들기](../storage/common/storage-account-create.md)를 참조하세요.

* **Azure Synapse Analytics**: Azure Synapse Analytics가 없는 경우 [Azure Synapse Analytics 인스턴스를 생성](../synapse-analytics/sql-data-warehouse/create-data-warehouse-portal.md)하는 방법을 알아봅니다.

* **Azure Data Factory**: 아직 데이터 팩터리를 만들지 않은 경우 [데이터 팩터리 만들기](./quickstart-create-data-factory-portal.md)를 참조하세요.

* **Azure Data Share**: 아직 데이터 공유를 만들지 않은 경우 [데이터 공유 만들기](../data-share/share-your-data.md#create-a-data-share-account)를 참조하세요.

## <a name="set-up-your-azure-data-factory-environment"></a>Azure Data Factory 환경 설정

이 섹션에서는 Azure Portal에서 ADF UX(Azure Data Factory 사용자 환경)에 액세스하는 방법을 알아봅니다. ADF UX에서는 우리가 사용할 각 데이터 저장소에 대해 다음과 같은 3개의 연결된 서비스를 구성합니다. Azure SQL DB, ADLS Gen2 및 Azure Synapse Analytics.

Azure Data Factory 연결된 서비스에서는 외부 리소스에 대한 연결 정보를 정의합니다. Azure Data Factory는 현재 85개 이상의 커넥터를 지원합니다.

### <a name="open-the-azure-data-factory-ux"></a>Azure Data Factory UX 열기

1. Microsoft Edge 또는 Google Chrome에서 [Azure Portal](https://portal.azure.com)을 엽니다.
1. 페이지 맨 위에 있는 검색 창을 사용하여 '데이터 팩터리'를 검색합니다.

    :::image type="content" source="media/lab-data-flow-data-share/portal1.png" alt-text="포털 1":::
1. 데이터 팩터리 리소스를 클릭하여 해당 리소스 블레이드를 엽니다.

    :::image type="content" source="media/lab-data-flow-data-share/portal2.png" alt-text="포털 2":::
1. **작성자 및 모니터** 를 클릭하여 ADF UX를 엽니다. adf.azure.com에서도 ADF UX에 액세스할 수 있습니다.

    :::image type="content" source="media/lab-data-flow-data-share/portal3.png" alt-text="포털 3":::
1. ADF UX 홈페이지로 리디렉션됩니다. 이 페이지에는 데이터 팩터리 개념을 배울 수 있는 빠른 시작, 교육용 비디오 및 자습서 링크가 포함되어 있습니다. 작성을 시작하려면 왼쪽 막대에서 연필 아이콘을 클릭합니다.

    :::image type="content" source="./media/doc-common-process/get-started-page-author-button.png" alt-text="포털 구성":::

### <a name="create-an-azure-sql-database-linked-service"></a>Azure SQL Database 연결된 서비스 만들기

1. 연결된 서비스를 만들려면 왼쪽 사이드바에서 허브 **관리** 를 선택하고 **연결** 창에서 **연결된 서비스** 를 선택한 다음, **새로 만들기** 를 선택하여 새 연결된 서비스를 추가합니다.

    :::image type="content" source="media/lab-data-flow-data-share/configure2.png" alt-text="포털 구성 2":::
1. 구성할 첫 번째 연결된 서비스는 Azure SQL DB입니다. 검색 창을 사용하여 데이터 저장소 목록을 필터링할 수 있습니다. **Azure SQL Database** 타일을 클릭하고 [계속]을 클릭합니다.

    :::image type="content" source="media/lab-data-flow-data-share/configure-4.png" alt-text="포털 구성 4":::
1. SQL DB 구성 창에서 연결된 서비스 이름으로 'SQLDB'를 입력합니다. 데이터 팩터리에서 데이터베이스에 연결할 수 있도록 자격 증명을 입력합니다. SQL 인증을 사용하는 경우 서비스 이름, 데이터베이스, 사용자 이름 및 암호를 입력합니다. **연결 테스트** 를 클릭하여 연결 정보가 올바른지 확인할 수 있습니다. 작업을 마쳤으면 **만들기** 를 클릭합니다.

    :::image type="content" source="media/lab-data-flow-data-share/configure5.png" alt-text="포털 구성 5":::

### <a name="create-an-azure-synapse-analytics-linked-service"></a>Azure Synapse Analytics 연결된 서비스 만들기

1. 동일한 프로세스를 반복하여 Azure Synapse Analytics 연결된 서비스를 추가합니다. 연결 탭에서 **새로 만들기** 를 클릭합니다. **Azure Synapse Analytics** 타일을 선택하고 [계속]을 클릭합니다.

    :::image type="content" source="media/lab-data-flow-data-share/configure-6.png" alt-text="포털 구성 6":::
1. 연결된 서비스 구성 창에서 연결된 서비스 이름으로 'SQLDW'를 입력합니다. 데이터 팩터리에서 데이터베이스에 연결할 수 있도록 자격 증명을 입력합니다. SQL 인증을 사용하는 경우 서비스 이름, 데이터베이스, 사용자 이름 및 암호를 입력합니다. **연결 테스트** 를 클릭하여 연결 정보가 올바른지 확인할 수 있습니다. 작업을 마쳤으면 **만들기** 를 클릭합니다.

    :::image type="content" source="media/lab-data-flow-data-share/configure-7.png" alt-text="포털 구성 7":::

### <a name="create-an-azure-data-lake-storage-gen2-linked-service"></a>Azure Data Lake Storage Gen2 연결된 서비스 만들기

1. 이 랩에 필요한 마지막 연결된 서비스는 Azure Data Lake Storage gen2입니다.  연결 탭에서 **새로 만들기** 를 클릭합니다. **Azure Data Lake Storage Gen2** 타일을 선택하고 [계속]을 클릭합니다.

    :::image type="content" source="media/lab-data-flow-data-share/configure8.png" alt-text="포털 구성 8":::
1. 연결된 서비스 구성 창에서 연결된 서비스 이름으로 'ADLSGen2'를 입력합니다. 계정 키 인증을 사용하는 경우 **스토리지 계정 이름** 드롭다운에서 ADLS Gen2 스토리지 계정을 선택합니다. **연결 테스트** 를 클릭하여 연결 정보가 올바른지 확인할 수 있습니다. 작업을 마쳤으면 **만들기** 를 클릭합니다.

    :::image type="content" source="media/lab-data-flow-data-share/configure9.png" alt-text="포털 구성 9":::

### <a name="turn-on-data-flow-debug-mode"></a>데이터 흐름 디버그 모드 켜기

*매핑 데이터 흐름을 사용하여 데이터 변환* 섹션에서는 매핑 데이터 흐름을 빌드합니다. 매핑 데이터 흐름을 빌드하기 전에 활성 스파크 클러스터에서 변환 논리를 초 단위로 테스트할 수 있는 디버그 모드를 켜는 것이 가장 좋습니다.

디버그를 설정하려면 **데이터 흐름** 활동이 있을 때 데이터 흐름 캔버스 또는 파이프라인 캔버스의 위쪽 막대에서 **데이터 흐름 디버그** 슬라이더를 클릭합니다. 확인 대화 상자가 나타나면 **확인** 을 클릭합니다. 클러스터가 약 5~7분 이내에 시작됩니다. 초기화하는 동안 *복사 작업을 사용하여 Azure SQL DB에서 ADLS Gen2로 데이터 수집* 을 진행합니다.

:::image type="content" source="media/lab-data-flow-data-share/configure10.png" alt-text="포털 구성 10":::

:::image type="content" source="media/lab-data-flow-data-share/configure-11.png" alt-text="데이터 흐름 디버그 슬라이더가 있는 위치를 보여주는 스크린샷.":::

## <a name="ingest-data-using-the-copy-activity"></a>복사 작업을 사용하여 데이터 수집

이 섹션에서는 Azure SQL DB의 테이블 하나를 ADLS Gen2 스토리지 계정으로 수집하는 복사 작업을 사용하여 파이프라인을 만듭니다. 파이프라인을 추가하고, 데이터 세트를 구성하고, ADF UX를 통해 파이프라인을 디버그하는 방법을 알아봅니다. 이 섹션에서 사용되는 구성 패턴은 관계형 데이터 저장소에서 파일 기반 데이터 저장소로 복사하는 데 적용할 수 있습니다.

Azure Data Factory에서 파이프라인은 함께 하나의 작업을 수행하는 활동의 논리적 그룹화입니다. 활동은 데이터에 대해 수행할 작업을 정의합니다. 데이터 세트는 연결된 서비스에서 사용하려는 데이터를 가리킵니다.

### <a name="create-a-pipeline-with-a-copy-activity"></a>복사 작업을 사용하여 파이프라인 만들기

1. 팩터리 리소스 창에서 더하기 아이콘을 클릭하여 새 리소스 메뉴를 엽니다. **파이프라인** 을 선택합니다.

    :::image type="content" source="media/lab-data-flow-data-share/copy1.png" alt-text="포털 복사 1":::
1. 파이프라인 캔버스의 **일반** 탭에서 'IngestAndTransformTaxiData'처럼 구체적인 파이프라인의 이름을 지정합니다.

    :::image type="content" source="media/lab-data-flow-data-share/copy2.png" alt-text="포털 복사 2":::
1. 파이프라인 캔버스의 작업 창에서 **이동 및 변환** 아코디언을 열고, **데이터 복사** 작업을 캔버스로 끌어다 놓습니다. 복사 작업에 'IngestIntoADLS'처럼 구체적인 이름을 지정합니다.

    :::image type="content" source="media/lab-data-flow-data-share/copy3.png" alt-text="포털 복사 3":::

### <a name="configure-azure-sql-db-source-dataset"></a>Azure SQL DB 원본 데이터 세트 구성

1. 복사 작업의 **원본** 탭을 클릭합니다. 새 데이터 세트를 만들려면 **새로 만들기** 를 클릭합니다. 원본은 앞에서 구성한 연결된 서비스 'SQLDB'에 위치한 'dbo.TripData' 테이블입니다.

    :::image type="content" source="media/lab-data-flow-data-share/copy4.png" alt-text="포털 복사 4":::
1. **Azure SQL Database** 를 검색하고 [계속]을 클릭합니다.

    :::image type="content" source="media/lab-data-flow-data-share/copy-5.png" alt-text="포털 복사 5":::
1. 'TripData' 데이터 세트를 호출합니다. 연결된 서비스로 'SQLDB'를 선택합니다. 테이블 이름 드롭다운에서 테이블 이름 'dbo.TripData'를 선택합니다. **연결/저장소에서** 스키마를 가져옵니다. 작업이 완료되면 [확인]을 클릭합니다.

    :::image type="content" source="media/lab-data-flow-data-share/copy6.png" alt-text="포털 복사 6":::

원본 데이터 세트 만들기가 완료되었습니다. 원본 설정의 [쿼리 사용] 필드에서 기본값인 **테이블** 을 선택합니다.

### <a name="configure-adls-gen2-sink-dataset"></a>ADLS Gen2 싱크 데이터 세트 구성

1. 복사 작업의 **싱크** 탭을 클릭합니다. 새 데이터 세트를 만들려면 **새로 만들기** 를 클릭합니다.

    :::image type="content" source="media/lab-data-flow-data-share/copy7.png" alt-text="포털 복사 7":::
1. **Azure Data Lake Storage Gen2** 를 검색하고 [계속]을 클릭합니다.

    :::image type="content" source="media/lab-data-flow-data-share/copy8.png" alt-text="포털 복사 8":::
1. csv 파일에 쓸 것이므로 선택 형식 창에서 **DelimitedText** 를 선택합니다. [계속]을 클릭합니다.

    :::image type="content" source="media/lab-data-flow-data-share/copy9.png" alt-text="포털 복사 9":::
1. 싱크 데이터 세트의 이름을 'TripDataCSV'로 지정합니다. 연결된 서비스로 'ADLSGen2'를 선택합니다. csv 파일을 쓰려는 위치를 입력합니다. 예를 들어 컨테이너 `staging-container`의 `trip-data.csv` 파일에 데이터를 쓸 수 있습니다. 출력 데이터에 헤더를 포함할 것이므로 **첫 번째 행을 헤더로** 를 true로 설정합니다. 대상에 파일이 아직 없기 때문에 **스키마 가져오기** 를 **없음** 으로 설정합니다. 작업이 완료되면 [확인]을 클릭합니다.

    :::image type="content" source="media/lab-data-flow-data-share/copy10.png" alt-text="포털 복사 10":::

### <a name="test-the-copy-activity-with-a-pipeline-debug-run"></a>파이프라인 디버그 실행을 사용하여 복사 작업 테스트

1. 복사 작업이 올바르게 작동하는지 확인하려면 파이프라인 캔버스 위쪽에 있는 **디버그** 를 클릭하여 디버그 실행을 수행합니다. 디버그 실행을 사용하면 파이프라인을 엔드투엔드 또는 데이터 팩터리 서비스에 게시하기 전의 중단점까지 테스트할 수 있습니다.

    :::image type="content" source="media/lab-data-flow-data-share/copy11.png" alt-text="포털 복사 11":::
1. 디버그 실행을 모니터링하려면 파이프라인 캔버스의 **출력** 탭으로 이동합니다. 모니터링 화면은 20초마다 또는 수동으로 새로 고침 단추를 클릭할 때마다 자동 새로 고침됩니다. 복사 작업에는 **작업** 열의 안경 아이콘을 클릭하여 액세스할 수 있는 특별한 모니터링 보기가 있습니다.

    :::image type="content" source="media/lab-data-flow-data-share/copy12.png" alt-text="포털 복사 12":::
1. 복사 모니터링 보기에서는 작업 실행 세부 정보와 성능 특성을 볼 수 있습니다. 읽고 쓴 데이터, 읽고 쓴 행, 읽고 쓴 파일, 처리량 등의 정보를 볼 수 있습니다. 모든 항목을 올바르게 구성하면 ADLS 싱크에 있는 파일 하나에 49999개 행이 기록됩니다.

    :::image type="content" source="media/lab-data-flow-data-share/copy13.png" alt-text="포털 복사 13":::
1. 다음 섹션으로 넘어가기 전에, 팩터리 위쪽 표시줄에서 **모두 게시** 를 클릭하여 변경 내용을 데이터 팩터리 서비스에 게시하는 것이 좋습니다. 이 랩에서는 다루지 않지만, Azure Data Factory는 전체 git 통합을 지원합니다. Git 통합을 통해 버전 제어, 리포지토리에 반복적으로 저장, 데이터 팩터리에 대한 협업을 수행할 수 있습니다. 자세한 내용은 [Azure Data Factory의 소스 제어](./source-control.md#troubleshooting-git-integration)를 참조하세요.

    :::image type="content" source="media/lab-data-flow-data-share/publish1.png" alt-text="포털 게시 1":::

## <a name="transform-data-using-mapping-data-flow"></a>데이터 흐름 매핑을 사용하여 데이터 변환

데이터를 Azure Data Lake Storage에 복사했으므로, 이제 해당 데이터를 데이터 웨어하우스에 조인하고 집계할 차례입니다. Azure Data Factory의 시각적으로 디자인된 변환 서비스인 매핑 데이터 흐름을 사용할 것입니다. 매핑 데이터 흐름을 사용하면 사용자는 코드 없이 변환 논리를 개발하고 ADF 서비스를 통해 관리되는 스파크 클러스터에서 변환 논리를 실행할 수 있습니다.

이 단계에서 만드는 데이터 흐름은 이전 섹션에서 만든 'TripDataCSV' 데이터 세트를 네 개의 키 열에 따라 'SQLDB'에 저장된 'dbo.TripFares' 테이블과 내부 조인합니다. 그러면 `payment_type` 열을 기준으로 데이터가 집계되어 특정 필드의 평균을 계산하고 Azure Synapse Analytics 테이블에 기록됩니다.

### <a name="add-a-data-flow-activity-to-your-pipeline"></a>파이프라인에 데이터 흐름 작업 추가

1. 파이프라인 캔버스의 작업 창에서 **이동 및 변환** 아코디언을 열고, **데이터 흐름** 작업을 캔버스로 끌어다 놓습니다.

    :::image type="content" source="media/lab-data-flow-data-share/dataflow1.png" alt-text="포털 데이터 흐름 1":::
1. 열리는 측면 창에서 **새 데이터 흐름 만들기** 를 선택하고 **매핑 데이터 흐름** 을 선택합니다. **확인** 을 클릭합니다.

    :::image type="content" source="media/lab-data-flow-data-share/dataflow2.png" alt-text="포털 데이터 흐름 2":::
1. 데이터 흐름 캔버스로 이동됩니다. 여기서 변환 논리를 작성할 것입니다. [일반] 탭에서 데이터 흐름의 이름을 'JoinAndAggregateData'로 지정합니다.

    :::image type="content" source="media/lab-data-flow-data-share/dataflow3.png" alt-text="포털 데이터 흐름 3":::

### <a name="configure-your-trip-data-csv-source"></a>주행 데이터 csv 원본 구성

1. 가장 먼저 할 일은 두 개의 원본 변환을 구성하는 것입니다. 첫 번째 원본은 'TripDataCSV' DelimitedText 데이터 세트를 가리킵니다. 원본 변환을 추가하려면 캔버스에서 **원본 추가** 상자를 클릭합니다.

    :::image type="content" source="media/lab-data-flow-data-share/dataflow4.png" alt-text="포털 데이터 흐름 4":::
1. 원본 이름을 'TripDataCSV'로 지정하고 원본 드롭다운에서 'TripDataCSV' 데이터 세트를 선택합니다. 앞에서 이 데이터 세트를 만들 때 처음에는 데이터가 없으므로 스키마를 가져오지 못했습니다. 현재는 `trip-data.csv`가 있으므로 **편집** 을 클릭하여 데이터 세트 설정 탭으로 이동합니다.

    :::image type="content" source="media/lab-data-flow-data-share/dataflow5.png" alt-text="포털 데이터 흐름 5":::
1. **스키마** 탭으로 이동하여 **스키마 가져오기** 를 클릭합니다. **연결/저장소에서** 를 선택하여 파일 저장소에서 직접 가져옵니다. 14개 문자열 형식이 표시됩니다.

    :::image type="content" source="media/lab-data-flow-data-share/dataflow6.png" alt-text="포털 데이터 흐름 6":::
1. 데이터 흐름 'JoinAndAggregateData'로 돌아갑니다. 디버그 클러스터가 시작된 경우(디버그 슬라이더 옆의 녹색 원으로 표시) **데이터 미리 보기** 탭에서 데이터 스냅샷을 가져올 수 있습니다. **새로 고침** 을 클릭하여 데이터 미리 보기를 가져옵니다.

    :::image type="content" source="media/lab-data-flow-data-share/dataflow7.png" alt-text="포털 데이터 흐름 7":::

> [!Note]
> 데이터 미리 보기는 데이터를 쓰지 않습니다.

### <a name="configure-your-trip-fares-sql-db-source"></a>요금 SQL DB 원본 구성

1. 두 번째로 추가할 원본은 SQL DB 테이블 'dbo.TripFares'를 가리킵니다. 'TripDataCSV' 원본 아래에 또 다른 **원본 추가** 상자가 있습니다. 이 상자를 클릭하여 새 원본 변환을 추가합니다.

    :::image type="content" source="media/lab-data-flow-data-share/dataflow8.png" alt-text="포털 데이터 흐름 8":::
1. 이 원본의 이름을 'TripFaresSQL'로 지정합니다. 원본 데이터 세트 필드 옆에 있는 **새로 만들기** 를 클릭하여 새 SQL DB 데이터 세트를 만듭니다.

    :::image type="content" source="media/lab-data-flow-data-share/dataflow9.png" alt-text="포털 데이터 흐름 9":::
1. **Azure SQL Database** 타일을 선택하고 [계속]을 클릭합니다. *참고: 데이터 팩터리의 여러 커넥터가 매핑 데이터 흐름에서 지원되지 않습니다. 이러한 원본 중 하나의 데이터를 변환하려면 복사 작업을 사용하여 지원되는 원본에 데이터를 수집하세요.*

    :::image type="content" source="media/lab-data-flow-data-share/dataflow-10.png" alt-text="포털 데이터 흐름 10":::
1. 'TripFares' 데이터 세트를 호출합니다. 연결된 서비스로 'SQLDB'를 선택합니다. 테이블 이름 드롭다운에서 테이블 이름 'dbo.TripFares'를 선택합니다. **연결/저장소에서** 스키마를 가져옵니다. 작업이 완료되면 [확인]을 클릭합니다.

    :::image type="content" source="media/lab-data-flow-data-share/dataflow11.png" alt-text="포털 데이터 흐름 11":::
1. 데이터를 확인하려면 **데이터 미리 보기** 탭에서 데이터 미리 보기를 가져옵니다.

    :::image type="content" source="media/lab-data-flow-data-share/dataflow12.png" alt-text="포털 데이터 흐름 12":::

### <a name="inner-join-tripdatacsv-and-tripfaressql"></a>TripDataCSV 및 TripFaresSQL 내부 조인

1. 새 변환을 추가하려면 'TripDataCSV'의 오른쪽 아래 모서리에 있는 더하기 아이콘을 클릭합니다. **여러 입력/출력** 에서 **조인** 을 선택합니다.

    :::image type="content" source="media/lab-data-flow-data-share/join1.png" alt-text="포털 조인 1":::
1. 조인 변환의 이름을 'InnerJoinWithTripFares'로 지정합니다. 오른쪽 스트림 드롭다운에서 'TripFaresSQL'을 선택합니다. 조인 유형으로 **내부** 를 선택합니다. 매핑 데이터 흐름의 다양한 조인 유형에 대한 자세한 내용은 [조인 유형](./data-flow-join.md#join-types)을 참조하세요.

    **조인 조건** 드롭다운을 통해 각 스트림에서 매칭할 열을 선택합니다. 조인 조건을 추가하려면 기존 조건 옆에 있는 더하기 아이콘을 클릭합니다. 기본적으로 모든 조인 조건은 AND 연산자와 결합됩니다. 즉, 일치 항목이 되려면 모든 조건을 충족해야 합니다. 이 랩에서는 `medallion`, `hack_license`, `vendor_id` 및 `pickup_datetime`열을 매칭하려고 합니다.

    :::image type="content" source="media/lab-data-flow-data-share/join2.png" alt-text="포털 조인 2":::
1. 25개 열을 데이터 미리 보기에 성공적으로 조인했는지 확인합니다.

    :::image type="content" source="media/lab-data-flow-data-share/join3.png" alt-text="포털 조인 3":::

### <a name="aggregate-by-payment_type"></a>payment_type으로 집계

1. 조인 변환을 완료한 후에는 'InnerJoinWithTripFares' 옆에 있는 더하기 아이콘을 클릭하여 집계 변환을 추가합니다. **스키마 한정자** 에서 **집계** 를 선택합니다.

    :::image type="content" source="media/lab-data-flow-data-share/agg1.png" alt-text="포털 agg 1":::
1. 집계 변환의 이름을 'AggregateByPaymentType'으로 지정합니다. 그룹화 방법 열로 `payment_type`을 선택합니다.

    :::image type="content" source="media/lab-data-flow-data-share/agg2.png" alt-text="포털 agg 2":::
1. **집계** 탭으로 이동합니다. 여기서는 다음 두 가지 집계를 지정합니다.
    * 결제 유형별로 그룹화된 평균 요금
    * 결제 유형별로 그룹화된 총 주행 거리

    먼저 평균 요금 식을 만듭니다. **열 추가 또는 선택** 이라는 텍스트 상자에 'average_fare'를 입력합니다.

    :::image type="content" source="media/lab-data-flow-data-share/agg3.png" alt-text="포털 agg 3":::
1. 집계 식을 입력하려면 **식 입력** 이라는 파란색 상자를 클릭합니다. 그러면 입력 스키마, 기본 제공 함수 및 작업, 사용자 정의 매개 변수를 사용하여 데이터 흐름 식을 시각적으로 만드는 데 사용되는 도구인 데이터 흐름 식 작성기가 열립니다. 식 작성기의 기능에 대한 자세한 내용은 [식 작성기 설명서](./concepts-data-flow-expression-builder.md)를 참조하세요.

    평균 요금을 구하려면 `avg()` 집계 함수를 사용하여 `toInteger()`를 통해 정수로 캐스팅된 `total_amount` 열을 집계합니다. 데이터 흐름 식 언어에서는 이것이 `avg(toInteger(total_amount))`로 정의됩니다. 완료되면 **저장 및 끝내기** 를 클릭합니다.

    :::image type="content" source="media/lab-data-flow-data-share/agg4.png" alt-text="포털 agg 4":::
1. 집계 식을 추가하려면 `average_fare` 옆에 있는 더하기 아이콘을 클릭합니다. **열 추가** 를 선택합니다.

    :::image type="content" source="media/lab-data-flow-data-share/agg5.png" alt-text="포털 agg 5":::
1. **열 추가 또는 선택** 이라는 텍스트 상자에 'total_trip_distance'를 입력합니다. 마지막 단계와 같이, 식 작성기를 열고 식을 입력합니다.

    총 주행 거리를 구하려면 `sum()` 집계 함수를 사용하여 `toInteger()`를 통해 정수로 캐스팅된 `trip_distance` 열을 집계합니다. 데이터 흐름 식 언어에서는 이것이 `sum(toInteger(trip_distance))`으로 정의됩니다. 완료되면 **저장 및 끝내기** 를 클릭합니다.

    :::image type="content" source="media/lab-data-flow-data-share/agg6.png" alt-text="포털 agg 6":::
1. **데이터 미리 보기** 탭에서 변환 논리를 테스트합니다. 보시는 것처럼, 이전보다 행과 열의 수가 훨씬 적습니다. 이 변환에 정의된 그룹화 방법 및 집계 열 3개만 다운스트림으로 진행됩니다. 이 샘플에는 결제 유형 그룹이 5개만 있으므로 5개 행만 출력됩니다.

    :::image type="content" source="media/lab-data-flow-data-share/agg7.png" alt-text="포털 agg 7":::

### <a name="configure-you-azure-synapse-analytics-sink"></a>Azure Synapse Analytics 싱크 구성

1. 변환 논리를 완료했으므로, Azure Synapse Analytics 테이블에 데이터를 싱크할 준비가 되었습니다. **대상** 섹션에서 싱크 변환을 추가합니다.

    :::image type="content" source="media/lab-data-flow-data-share/sink1.png" alt-text="포털 싱크 1":::
1. 싱크 이름을 'SQLDWSink'로 지정합니다. 싱크 데이터 세트 필드 옆에 있는 **새로 만들기** 를 클릭하여 새 Azure Synapse Analytics 데이터 세트를 만듭니다.

    :::image type="content" source="media/lab-data-flow-data-share/sink2.png" alt-text="포털 싱크 2":::

1. **Azure Synapse Analytics** 타일을 선택하고 [계속]을 클릭합니다.

    :::image type="content" source="media/lab-data-flow-data-share/sink-3.png" alt-text="포털 싱크 3":::
1. 'AggregatedTaxiData' 데이터 세트를 호출합니다. 연결된 서비스로 'SQLDW'를 선택합니다. **새 테이블 만들기** 를 선택하고 새 테이블의 이름을 dbo.AggregateTaxiData로 지정합니다. 작업이 완료되면 [확인]을 클릭합니다.

    :::image type="content" source="media/lab-data-flow-data-share/sink4.png" alt-text="포털 싱크 4":::
1. 싱크의 **설정** 탭으로 이동합니다. 새 테이블을 만드는 중이므로 테이블 작업 아래에서 **테이블 다시 만들기** 를 선택해야 합니다. 행 단위로 삽입할 것인지 아니면 일괄적으로 삽입할 것인지 여부를 전환하는 **준비 사용** 을 선택 취소합니다.

    :::image type="content" source="media/lab-data-flow-data-share/sink5.png" alt-text="포털 싱크 5":::

데이터 흐름 만들기가 완료되었습니다. 이제 파이프라인 작업에서 실행할 차례입니다.

### <a name="debug-your-pipeline-end-to-end"></a>파이프라인 엔드투엔드 디버그

1. **IngestAndTransformData** 파이프라인에 대한 탭으로 돌아갑니다. 'IngestIntoADLS' 복사 작업에 녹색 상자가 있을 것입니다. 이 상자를 'JoinAndAggregateData' 데이터 흐름 작업으로 끕니다. 그러면 복사가 성공한 경우에만 데이터 흐름 작업이 실행되는 '성공 시'가 생성됩니다.

    :::image type="content" source="media/lab-data-flow-data-share/pipeline1.png" alt-text="포털 파이프라인 1":::
1. 복사 작업에서 했던 것처럼, **디버그** 를 클릭하여 디버그 실행을 수행합니다. 디버그 실행의 경우 데이터 흐름 작업은 새 클러스터를 스핀업하는 대신 활성 디버그 클러스터를 사용합니다. 이 파이프라인이 실행될 때까지 1분이 조금 넘게 걸립니다.

    :::image type="content" source="media/lab-data-flow-data-share/pipeline2.png" alt-text="포털 파이프라인 2":::
1. 복사 작업과 마찬가지로, 데이터 흐름에는 작업 완료 시 안경 아이콘을 통해 액세스할 수 있는 특별한 모니터링 보기가 있습니다.

    :::image type="content" source="media/lab-data-flow-data-share/pipeline3.png" alt-text="포털 파이프라인 3":::
1. 모니터링 보기에서는 각 실행 단계의 실행 시간 및 행과 함께 단순화된 데이터 흐름 그래프를 볼 수 있습니다. 작업을 올바르게 수행했다면 이 작업의 5개 행에 49,999개 행이 집계되었을 것입니다.

    :::image type="content" source="media/lab-data-flow-data-share/pipeline4.png" alt-text="포털 파이프라인 4":::
1. 변환을 클릭하여 분할 정보 및 새/업데이트된/삭제된 열과 같은 추가적인 실행 세부 정보를 가져올 수 있습니다.

    :::image type="content" source="media/lab-data-flow-data-share/pipeline5.png" alt-text="포털 파이프라인 5":::

이 랩의 데이터 팩터리 부분을 완료했습니다. 트리거를 사용하여 리소스를 운영하려면 리소스를 게시하세요. 복사 작업을 사용하여 Azure SQL Database에서 Azure Data Lake Storage로 데이터를 수집한 후 해당 데이터를 Azure Synapse Analytics로 집계하는 파이프라인을 성공적으로 실행했습니다. SQL Server 자체를 살펴보면 데이터가 성공적으로 기록되었는지 확인할 수 있습니다.

## <a name="share-data-using-azure-data-share"></a>Azure Data Share를 사용하여 데이터 공유

이 섹션에서는 Azure Portal을 사용하여 새 데이터 공유를 설정하는 방법을 알아봅니다. 여기에는 Azure Data Lake Store Gen2 및 Azure Synapse Analytics의 데이터 세트를 포함할 새 데이터 공유를 만드는 작업이 포함됩니다. 그리고 데이터 소비자에게 공유 데이터를 자동으로 새로 고치는 옵션을 제공하는 스냅샷 일정을 구성할 것입니다. 그런 다음, 수신자를 데이터 공유에 추가할 것입니다. 

데이터 공유를 만든 후에는 *데이터 소비자* 의 입장이 되어 보겠습니다. 데이터 소비자로서 데이터 공유 초대를 수락하고, 데이터를 받을 위치를 구성하고, 데이터 세트를 다른 스토리지 위치로 매핑하는 흐름을 살펴볼 것입니다. 그런 다음, 데이터 소비자와 공유하는 데이터를 지정된 대상으로 복사하는 스냅샷을 트리거합니다. 

### <a name="sharing-data-data-provider-flow"></a>데이터 공유(Data Provider 흐름)

1. Microsoft Edge 또는 Google Chrome에서 Azure Portal을 엽니다.

1. 페이지 맨 위에 있는 검색 창을 사용하여 **데이터 공유** 를 검색합니다.

    :::image type="content" source="media/lab-data-flow-data-share/portal-ads.png" alt-text="포털 광고":::

1. 이름에 'Provider'가 있는 데이터 공유 계정을 선택합니다. 예를 들어 **DataProvider0102** 를 선택합니다. 

1. **데이터 공유 시작** 을 선택합니다.

    :::image type="content" source="media/lab-data-flow-data-share/ads-start-sharing.png" alt-text="공유 시작":::

1. **+ 만들기** 를 선택하여 새 데이터 공유 구성을 시작합니다. 

1. **공유 이름** 에서 원하는 이름을 지정합니다. 이 공유 이름은 데이터 소비자에게 표시되므로, TaxiData처럼 구체적인 이름을 입력해야 합니다.

1. **설명** 에서 데이터 공유의 내용을 설명하는 문장을 입력합니다. 데이터 공유에는 Azure Synapse Analytics 및 Azure Data Lake Store를 비롯한 여러 저장소에 저장된 전 세계 택시 주행 데이터가 포함됩니다. 

1. **사용 약관** 에서 데이터 소비자가 따라야 하는 사용 약관 세트를 지정합니다. "조직 외부에 이 데이터를 배포하지 마십시오" 또는 "법적 계약서를 참조하십시오"를 예로 들 수 있습니다. 

    :::image type="content" source="media/lab-data-flow-data-share/ads-details.png" alt-text="공유 세부 정보":::

1. **계속** 을 선택합니다. 

1. **데이터 세트 추가** 를 선택합니다. 

    :::image type="content" source="media/lab-data-flow-data-share/add-dataset.png" alt-text="데이터 세트 1 추가":::

1. **Azure Synapse Analytics** 를 선택하여 ADF 변환이 배치된 Azure Synapse Analytics에서 테이블을 선택합니다.

    :::image type="content" source="media/lab-data-flow-data-share/add-dataset-sql.png" alt-text="데이터 세트 sql 추가":::


1. 계속 진행할 수 있도록 실행할 스크립트가 제공됩니다. 제공된 스크립트는 Azure 데이터 공유 MSI가 대신 인증할 수 있도록 SQL 데이터베이스에 사용자를 만듭니다. 

> [!IMPORTANT]
> 스크립트를 실행하기 전에 자신을 SQL Server의 Active Directory 관리자로 설정해야 합니다. 

1. 새 탭을 열고 Azure Portal로 이동합니다. 제공된 스크립트를 복사하여 데이터를 공유하려는 데이터베이스에 사용자를 만듭니다. 이렇게 하려면 AAD 인증을 통해 쿼리 탐색기(미리 보기)를 사용하여 EDW 데이터베이스에 로그인합니다. 

    생성된 사용자가 대괄호 안에 포함되도록 스크립트를 수정해야 합니다. 예:
    
    create user [dataprovider-xxxx] from external login;  exec sp_addrolemember db_owner, [dataprovider-xxxx];
    
1. 데이터 공유에 데이터 세트를 추가하던 Azure Data Share로 돌아갑니다. 

1. **EDW** 를 선택한 다음, 테이블에 대해 **AggregatedTaxiData** 를 선택합니다. 

1. **데이터 세트 추가** 를 선택합니다.

    데이터 세트의 일부인 SQL 테이블이 생겼습니다. 다음으로, Azure Data Lake Store의 데이터 세트를 추가하겠습니다. 

1. **데이터 세트 추가** 를 선택하고 **Azure Data Lake Store Gen2** 를 선택합니다.

    :::image type="content" source="media/lab-data-flow-data-share/add-dataset-adls.png" alt-text="데이터 세트 adls 추가":::

1. **다음** 을 선택합니다.

1. *wwtaxidata* 를 확장합니다. *Boston Taxi Data* 를 확장합니다. 파일 수준까지 공유할 수 있습니다. 

1. *Boston Taxi Data* 폴더를 선택하여 전체 폴더를 데이터 공유에 추가합니다. 

1. **데이터 세트 추가** 를 선택합니다.

1. 추가된 데이터 세트를 검토합니다. SQL 테이블과 ADLS Gen2 폴더가 데이터 공유에 추가되어 있어야 합니다. 

1. **계속** 을 선택합니다.

1. 이 화면에서는 데이터 공유에 수신자를 추가할 수 있습니다. 추가된 수신자는 데이터 공유 초대를 받게 됩니다. 이 랩의 목적에 맞게 다음과 같은 2개의 이메일 주소를 추가해야 합니다.

    1. 소속된 Azure 구독의 이메일 주소. 

        :::image type="content" source="media/lab-data-flow-data-share/add-recipients.png" alt-text="수신자 추가":::

    1. *janedoe@fabrikam.com* 이라는 가상의 데이터 소비자를 추가합니다.

1. 이 화면에서는 데이터 소비자의 스냅샷 설정을 구성할 수 있습니다. 이렇게 하면 데이터 소비자는 자신이 정의한 간격으로 정기적인 데이터 업데이트를 받을 수 있습니다. 

1. **스냅샷 일정** 을 선택하고, *되풀이* 드롭다운을 사용하여 데이터의 시간별 새로 고침을 구성합니다.  

1. **만들기** 를 선택합니다.

    이제 활성 데이터 공유가 생겼습니다. 데이터 공유를 만들 때 데이터 공급자로서 무엇을 볼 수 있는지 살펴보겠습니다. 

1. 앞에서 만든 데이터 공유 **DataProvider** 를 선택합니다. **데이터 공유** 에서 **보낸 공유** 를 선택하여 탐색할 수 있습니다. 

1. 스냅샷 일정을 클릭합니다. 원한다면 스냅샷 일정을 사용하지 않도록 설정할 수 있습니다. 

1. 다음으로, **데이터 세트** 탭을 선택합니다. 데이터 세트를 만든 후 이 데이터 공유에 추가할 수 있습니다. 

1. **공유 구독** 탭을 선택합니다. 데이터 소비자가 아직 초대를 수락하지 않았기 때문에 지금은 공유 구독이 없습니다.

1. **초대** 탭으로 이동합니다. 이 탭에는 보류 중인 초대 목록이 표시됩니다. 

    :::image type="content" source="media/lab-data-flow-data-share/pending-invites.png" alt-text="보류 중인 초대":::

1. *janedoe@fabrikam.com* 의 초대를 선택합니다. [삭제]를 선택합니다. 아직 초대를 수락하지 않은 수신자는 더 이상 초대를 수락할 수 없습니다. 

1. **기록** 탭을 선택합니다. 데이터 소비자가 초대를 수락하지 않고 스냅샷을 트리거했기 때문에 아무것도 표시되지 않습니다. 

### <a name="receiving-data-data-consumer-flow"></a>데이터 수신(데이터 소비자 흐름)

데이터 공유를 검토했으므로, 입장을 바꿔서 데이터 소비자가 되어 보겠습니다. 

Microsoft Azure 수신함에 Azure 데이터 공유 초대가 도착했을 것입니다. Outlook Web Access(outlook.com)를 시작하고 Azure 구독에 제공된 자격 증명을 사용하여 로그인합니다.

받은 이메일에서 "초대 보기 >"를 클릭합니다. 이제 데이터 소비자가 데이터 공급자의 데이터 공유 초대를 수락할 때 겪게 될 경험을 시뮬레이션할 것입니다. 

:::image type="content" source="media/lab-data-flow-data-share/email-invite.png" alt-text="이메일 초대":::

구독을 선택하라는 메시지가 표시될 수 있습니다. 이 랩에서 작업 중인 구독을 선택해야 합니다. 

1. *DataProvider* 라는 초대를 클릭합니다. 

1. 이 초대 화면에서는 이전에 데이터 공급자로서 구성한 데이터 공유에 대한 다양한 세부 정보를 볼 수 있습니다. 세부 정보를 검토하고 사용 약관에 동의합니다(제공된 경우).

1. 랩을 위해 미리 준비된 구독과 리소스 그룹을 선택합니다. 

1. **데이터 공유 계정** 으로 **DataConsumer** 를 선택합니다. 새 데이터 공유 계정을 만들어도 됩니다. 

1. **받은 공유 이름** 옆에는 데이터 공급자가 지정한 이름이 기본 공유 이름으로 표시됩니다. 수신하려는 데이터를 설명하는 식별 이름(예: **TaxiDataShare**)을 공유 이름으로 입력합니다.

    :::image type="content" source="media/lab-data-flow-data-share/consumer-accept.png" alt-text="초대 수락":::

1. **지금 수락하고 구성** 을 선택해도 되고 **나중에 수락하고 구성** 을 선택해도 됩니다. 지금 수락하고 구성을 선택할 경우 모든 데이터를 복사할 스토리지 계정을 지정합니다. 나중에 수락하고 구성을 선택할 경우 공유의 데이터 세트가 매핑되지 않으므로 수동으로 매핑해야 합니다. 여기서는 후자를 선택하겠습니다. 

1. **나중에 수락하고 구성** 을 선택합니다. 

    이 옵션을 구성할 때 공유 구독이 만들어지지만 대상이 매핑되지 않았기 때문에 데이터를 배치할 곳이 없습니다. 

    다음으로, 데이터 공유에 대한 데이터 세트 매핑을 구성합니다. 

1. [받은 공유]\(5단계에서 지정한 이름)를 선택합니다.

    **스냅샷 트리거** 는 회색으로 표시되지만 공유가 활성 상태입니다. 

1. **데이터 세트** 탭을 선택합니다. 각 데이터 세트가 매핑되지 않습니다. 즉, 데이터를 복사할 대상이 없습니다. 

    :::image type="content" source="media/lab-data-flow-data-share/unmapped.png" alt-text="매핑되지 않은 데이터 세트":::

1. Azure Synapse Analytics 테이블을 선택한 다음, **+ 대상에 매핑** 을 선택합니다.

1. 화면 오른쪽에서 **대상 데이터 형식** 드롭다운을 선택합니다. 

    SQL 데이터를 다양한 데이터 저장소에 매핑할 수 있습니다. 여기서는 Azure SQL Database에 매핑합니다.

    :::image type="content" source="media/lab-data-flow-data-share/mapping-options.png" alt-text="mapping":::
    
    (선택 사항) 대상 데이터 형식으로 **Azure Data Lake Store Gen2** 를 선택합니다. 
    
    (선택 사항) 작업 중인 구독, 리소스 그룹 및 스토리지 계정을 선택합니다. 
    
    (선택 사항) 데이터를 데이터 레이크에 csv로 받을 것인지 아니면 parquet 형식으로 받을 것인지 선택할 수 있습니다. 

1. **대상 데이터 형식** 옆에서 Azure SQL Database를 선택합니다. 

1. 작업 중인 구독, 리소스 그룹 및 스토리지 계정을 선택합니다. 

    :::image type="content" source="media/lab-data-flow-data-share/map-to-sqldb.png" alt-text="sql에 매핑":::

1. 계속 진행하려면 제공된 스크립트를 실행하여 SQL Server에 새 사용자를 만들어야 합니다. 먼저 제공된 스크립트를 클립보드에 복사합니다. 

1. 새 Azure Portal 탭을 엽니다. 기존 탭을 닫지 마세요. 잠시 후 기존 탭으로 돌아가야 합니다. 

1. 열린 새 탭에서 **SQL 데이터베이스** 로 이동합니다.

1. SQL 데이터베이스를 선택합니다(구독에 데이터베이스가 하나 있음). 데이터 웨어하우스를 선택하지 않도록 주의하세요. 

1. **쿼리 편집기(미리 보기)** 를 선택합니다.

1. AAD 인증을 사용하여 쿼리 편집기에 로그인합니다. 

1. 데이터 공유에 제공된 쿼리를 실행합니다(14단계에서 클립보드에 복사함). 

    이 명령을 사용하면 Azure Data Share 서비스에서 Azure 서비스에 대한 관리 ID를 사용하여 SQL Server에 인증하고 데이터를 복사할 수 있습니다. 

1. 원래 탭으로 돌아가서 **대상에 매핑** 을 선택합니다.

1. 다음으로, 데이터 세트에 포함된 Azure Data Lake Gen2 폴더를 선택하고 Azure Blob Storage 계정에 매핑합니다. 

    :::image type="content" source="media/lab-data-flow-data-share/storage-map.png" alt-text="storage":::

    모든 데이터 세트가 매핑되었으므로, 이제 데이터 공급자로부터 데이터 수신을 시작할 준비가 되었습니다. 

    :::image type="content" source="media/lab-data-flow-data-share/all-mapped.png" alt-text="매핑됨":::
    
1. **세부 정보** 를 선택합니다. 

    이제 데이터 공유에 복사할 대상이 있으므로 **스냅샷 트리거** 가 더 이상 회색으로 표시되지 않습니다.

1. 스냅샷 트리거 -> 전체 복사를 선택합니다. 

    :::image type="content" source="media/lab-data-flow-data-share/trigger-full.png" alt-text="트리거":::

    그러면 새 데이터 공유 계정으로 데이터 복사가 시작됩니다. 실제 시나리오에서는 이 데이터가 타사에서 옵니다. 

    데이터를 가져오는 데 약 3-5분이 소요됩니다. **기록** 탭을 클릭하여 진행률을 모니터링할 수 있습니다. 

    기다리는 동안 원래 데이터 공유(DataProvider)로 이동하여 **공유 구독** 및 **기록** 탭의 상태를 확인합니다. 이제 활성 구독이 있으며, 데이터 공급자는 데이터 소비자가 공유 데이터 수신을 시작한 시간을 모니터링할 수 있습니다. 

1. 데이터 소비자의 데이터 공유로 돌아갑니다. 트리거 상태가 성공이면 대상 SQL 데이터베이스 및 데이터 레이크로 이동하여 데이터가 각 저장소에 배치되었는지 확인합니다. 

축하합니다. 랩을 완료하셨습니다.
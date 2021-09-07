---
title: SAP Business Warehouse에서 데이터 로드
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory를 사용하여 SAP Business Warehouse(BW)에서 데이터 복사
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/04/2021
ms.openlocfilehash: 7f6a17d6596ce07c593ee83ad54cc856b2b1b592
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122642339"
---
# <a name="copy-data-from-sap-business-warehouse-by-using-azure-data-factory"></a>Azure Data Factory를 사용하여 SAP Business Warehouse에서 데이터 복사
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory를 사용하여 Open Hub를 통해 SAP Business Warehouse(BW)에서 Azure Data Lake Storage Gen2로 데이터를 복사하는 방법을 보여 줍니다. 유사한 프로세스를 사용하여 다른 [지원되는 싱크 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)로 데이터를 복사할 수 있습니다.

> [!TIP]
> SAP BW Open Hub 통합 및 델타 추출 흐름을 포함하여 SAP BW에서 데이터를 복사하는 방법에 대한 일반적인 정보는 [Azure Data Factory를 사용하여 Open Hub를 통해 SAP Business Warehouse에서 데이터 복사](connector-sap-business-warehouse-open-hub.md)를 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

- **Azure Data Factory**: 없는 경우 [데이터 팩터리를 만드는](quickstart-create-data-factory-portal.md#create-a-data-factory) 단계를 수행합니다.

- **대상 유형이 ‘대상 테이블’인 SAP BW Open Hub Destination(OHD)** : OHD를 만들거나 OHD가 Data Factory에 올바르게 구성되었는지 확인하려면 이 문서의 [SAP BW Open Hub Destination 구성](#sap-bw-open-hub-destination-configurations) 섹션을 참조하세요.

- **SAP BW 사용자에 게 다음 권한이 필요** 합니다.

  - RFC(원격 함수 호출) 및 SAP BW에 대한 권한 부여.
  - **S_SDSAUTH** 권한 부여 개체 ‘실행’ 작업에 대한 권한.

- **SAP .NET 커넥터 3.0이 있는 [자체 호스팅 통합 런타임(IR)](concepts-integration-runtime.md#self-hosted-integration-runtime)** . 다음 설정 단계를 수행하세요.

  1. 자체 호스팅 통합 런타임 버전 3.13 이상을 설치하고 등록합니다. (이 프로세스에 대해서는 이 문서의 뒷부분에서 설명합니다.)

  2. SAP 웹 사이트에서 [Microsoft .NET용 64비트 SAP Connector 3.0](https://support.sap.com/en/product/connectors/msnet.html)을 다운로드하여 자체 호스팅 통합 런타임과 동일한 컴퓨터에 설치합니다. 설치하는 동안 다음 그림처럼 **선택적 설정 단계** 대화 상자에서 **GAC에 어셈블리 설치** 를 선택해야 합니다.

     ![SAP .NET Connector 설정 대화 상자](media/connector-sap-business-warehouse-open-hub/install-sap-dotnet-connector.png)

## <a name="do-a-full-copy-from-sap-bw-open-hub"></a>SAP BW Open Hub에서 전체 복사를 수행합니다.

Azure Portal에서 Data Factory로 이동합니다. **Azure Data Factory Studio 열기** 타일에서 **열기** 를 선택하여 별도의 탭에서 Data Factory UI를 엽니다.

1. 홈페이지에서 **수집** 을 선택하여 데이터 복사 도구를 엽니다.

2. **속성** 페이지에 있는 **작업 유형** 아래에서 **기본 제공 복사 작업** 을 선택한 후 **작업 주기 또는 작업 일정** 에서 **지금 한 번 실행** 을 선택한 후 **다음** 을 선택합니다.

3. **원본 데이터 저장소** 페이지에서 **+새 연결** 을 선택합니다. 커넥터 갤러리에서 **SAP BW Open Hub** 를 선택한 다음 **계속** 을 선택합니다. 검색 상자에 **SAP** 를 입력하면 커넥터를 필터링할 수 있습니다.

4. **새 연결(SAP BW Open Hub)** 페이지에서 다음 단계에 따라 새 연결을 만듭니다.

   1. **통합 런타임을 통해 연결** 목록에서 기존 자체 호스팅 IR을 선택합니다. 또는 아직 없는 경우 새 자체 호스팅 IR을 만듭니다.

      새 자체 호스팅 IR을 만들려면 **+새로 만들기** 를 선택한 다음 **자체 호스팅** 을 선택합니다. **이름** 을 입력한 후 **다음** 을 선택합니다. **빠른 설치** 를 선택하여 현재 컴퓨터에 설치하거나 제공된 **수동 설치** 단계를 따릅니다.

      [필수 구성 요소](#prerequisites)에 설명된 대로 Microsoft .NET용 SAP Connector 3.0은 자체 호스팅 IR이 실행되는 컴퓨터에 설치되어야 합니다.

   2. SAP BW **서버 이름**, **시스템 번호**, **클라이언트 ID**, **언어**(**EN** 이 아닌 경우), **사용자 이름**, **암호** 를 입력합니다.

   3. **연결 테스트** 를 선택하여 설정의 유효성을 검사한 다음, **만들기** 를 선택합니다.

   ![SAP BW Open Hub 연결된 서비스 만들기 페이지](media/load-sap-bw-data/create-sap-bw-open-hub-linked-service.png)

   4. **원본 데이터 저장소** 페이지의 **연결** 블록에서 새로 만든 연결을 선택합니다.

   5. Open Hub 대상 선택 섹션에서 SAP BW에서 사용할 수 있는 Open Hub 대상으로 이동합니다. 각 행의 끝에 있는 미리 보기 단추를 선택하여 각 대상의 데이터를 미리 볼 수 있습니다. 데이터를 복사할 OHD를 선택하고 **다음** 을 선택합니다.
   
   :::image type="content" source="./media/load-sap-bw-data/source-data-store-page.png" alt-text="‘원본 데이터 저장소’ 페이지를 보여 주는 스크린샷":::

5. 필요한 경우 필터를 지정합니다. OHD에 단일 요청 ID를 사용하는 단일 DTP(데이터 전송 프로세스) 실행의 데이터만 포함되거나 DTP가 완료된 것이 확실하고 데이터를 복사하려는 경우 **고급** 섹션에서 **마지막 요청 제외** 확인란의 선택을 취소합니다. **데이터 미리 보기** 단추를 선택하여 데이터를 미리 볼 수 있습니다.

   이러한 설정에 대한 자세한 내용은 이 문서의 [SAP BW Open Hub Destination 구성](#sap-bw-open-hub-destination-configurations) 섹션을 참조하세요. 그런 후 **다음** 을 선택합니다.

   ![SAP BW Open Hub 필터 구성](media/load-sap-bw-data/configure-sap-bw-open-hub-filter.png)

6. **대상 데이터 저장소** 페이지에서 **+새 연결** > **Azure Data Lake Storage Gen2** > **계속** 을 선택합니다.

7. **새 연결(Azure Data Lake Storage Gen2)** 페이지에서 다음 단계를 따라 연결을 만듭니다.
   1. **이름** 드롭다운 목록에서 Data Lake Storage Gen2 지원 계정을 선택합니다.
   2. **만들기** 를 선택하여 연결을 만듭니다.

   ![ADLS Gen2 연결된 서비스 만들기 페이지](media/load-sap-bw-data/create-adls-gen2-linked-service.png)

8. **대상 데이터 저장소** 페이지의 **연결** 섹션에서 새로 만든 연결을 선택하고 출력 폴더 이름으로 **copyfromopenhub** 를 입력합니다. 그런 후 **다음** 을 선택합니다.

   :::image type="content" source="./media/load-sap-bw-data/destination-data-store-page.png" alt-text="‘대상 데이터 저장소’ 페이지를 보여 주는 스크린샷":::

9. **파일 형식 설정** 페이지에서 **다음** 을 선택하여 기본 설정을 사용합니다.

    ![싱크 형식 지정 페이지](media/load-sap-bw-data/specify-sink-format.png)

10. **설정** 페이지에서 **작업 이름** 을 지정한 후 **고급** 을 확장합니다. SAP BW에서 동시에 로드할 **복사 병렬 처리 수준** 값(예: 5)을 입력합니다. 그런 후 **다음** 을 선택합니다.

    ![복사 설정 구성](media/load-sap-bw-data/configure-copy-settings.png)

11. **요약** 페이지에서 설정을 검토합니다. 그런 후 **다음** 을 선택합니다.

    :::image type="content" source="./media/load-sap-bw-data/summary-page.png" alt-text="요약 페이지를 보여 주는 스크린샷":::

12. **배포 페이지** 에서 **모니터** 를 선택하여 파이프라인을 모니터링합니다.

13. 페이지 왼쪽의 **모니터** 탭이 자동으로 선택됩니다. **파이프라인 실행** 페이지의 **파이프라인 이름** 열 아래의 링크를 사용하여 활동 세부 정보를 보고 파이프라인을 다시 실행할 수 있습니다.

14. 파이프라인 실행과 연결된 활동 실행을 보려면 **파이프라인 이름** 열에서 링크를 선택합니다. 파이프라인에는 하나의 작업(복사 작업)만 있으므로 하나의 항목만 표시됩니다. 파이프라인 실행 보기로 다시 전환하려면 위쪽에서 **모든 파이프라인 실행** 링크를 선택합니다. **새로 고침** 을 선택하여 목록을 새로 고칩니다.

    ![활동 모니터링 화면](media/load-sap-bw-data/activity-monitoring.png)

15. 각 복사 활동의 실행 세부 정보를 모니터링하려면 활동 모니터링 보기의 동일한 각 복사 활동 행에서 **세부 정보** 링크(안경 아이콘)를 선택합니다. 사용할 수 있는 세부 정보에는 원본에서 싱크로 복사된 데이터 볼륨, 데이터 처리량, 실행 단계 및 기간, 사용된 구성이 포함됩니다.

    ![활동 모니터링 세부 정보](media/load-sap-bw-data/activity-monitoring-details.png)

16. 각 복사 활동의 **최대 요청 ID** 를 보려면 활동 모니터링 보기로 돌아가 동일한 각 복사 활동 행에서 **출력** 을 선택합니다.

    ![활동 출력 화면](media/load-sap-bw-data/activity-output.png)

    ![활동 출력 세부 정보 보기](media/load-sap-bw-data/activity-output-details.png)

## <a name="incremental-copy-from-sap-bw-open-hub"></a>SAP BW Open Hub에서 증분 복사

> [!TIP]
> Data Factory의 SAP BW Open Hub 커넥터가 SAP BW에서 증분 데이터를 복사하는 방법에 대한 자세한 내용은 [SAP BW Open Hub 커넥터 델타 추출 흐름](connector-sap-business-warehouse-open-hub.md#delta-extraction-flow)을 참조하세요. 이 문서는 기본 커넥터 구성을 이해하는 데도 도움이 됩니다.

이제 SAP BW Open Hub에서의 증분 복사를 계속 구성하겠습니다.

증분 복사는 **요청 ID** 를 기반으로 하는 ‘상위 워터마크’ 메커니즘을 사용합니다. 이 ID는 DTP에 의해 SAP BW Open Hub Destination에서 자동으로 생성됩니다. 다음 다이어그램은 이 워크플로를 보여 줍니다.

![증분 복사 워크플로 흐름 차트](media/load-sap-bw-data/incremental-copy-workflow.png)

데이터 팩터리 홈페이지의 **자세히 검색** 섹션에서 **파이프라인 템플릿** 을 선택하여 기본 제공 템플릿을 사용합니다.

1. **SAP BW** 를 검색하여 **SAP BW에서 Azure Data Lake Storage Gen2 템플릿으로 증분 복사** 를 찾아 선택합니다. 이 템플릿은 데이터를 Azure Data Lake Storage Gen2에 복사합니다. 유사한 워크플로를 사용하여 다른 싱크 형식으로 복사할 수 있습니다.

2. 템플릿의 주 페이지에서 다음 세 개의 연결을 선택하거나 만든 다음 창의 오른쪽 아래 모서리에서 **이 템플릿 사용** 을 선택합니다.

   - **Azure Blob Storage**: 이 연습에서는 Azure Blob Storage를 사용하여 복사된 최대 요청 ID인 상위 워터마크를 저장합니다.
   - **SAP BW Open Hub**: 데이터를 복사할 원본입니다. 자세한 구성은 이전 전체 복사 연습을 참조하세요.
   - **Azure Data Lake Storage Gen2**: 데이터가 복사될 싱크입니다. 자세한 구성은 이전 전체 복사 연습을 참조하세요.

   ![SAP BW 템플릿에서 증분 복사](media/load-sap-bw-data/incremental-copy-from-sap-bw-template.png)

3. 이 템플릿은 조회, 데이터 복사, 웹이라는 세 가지 활동으로 파이프라인을 생성하고 성공 시 연결합니다.

   파이프라인 **매개 변수** 탭으로 이동 합니다. 제공해야 하는 모든 구성이 표시됩니다.

   ![SAP BW 구성에서 증분 복사](media/load-sap-bw-data/incremental-copy-from-sap-bw-pipeline-config.png)

   - **SAPOpenHubDestinationName**: 데이터를 복사할 Open Hub 테이블 이름을 지정합니다.

   - **Data_Destination_Container**: 데이터가 복사될 대상 Azure Data Lake Storage Gen2 컨테이너를 지정합니다. 컨테이너가 존재하지 않는 경우 Data Factory 복사 활동이 실행되는 동안 컨테이너가 만들어집니다.
  
   - **Data_Destination_Directory**: Azure Data Lake Storage Gen2 컨테이너 아래에 데이터가 복사될 폴더 경로를 지정합니다. 경로가 존재하지 않는 경우 Data Factory 복사 활동이 실행되는 동안 경로가 만들어집니다.
  
   - **HighWatermarkBlobContainer**: 상위 워터마크 값을 저장할 컨테이너를 지정합니다.

   - **HighWatermarkBlobDirectory**: 컨테이너 아래에 상위 워터마크 값을 저장할 폴더 경로를 지정합니다.

   - **HighWatermarkBlobName**: `requestIdCache.txt`와 같이 상위 워터마크 값을 저장할 Blob 이름을 지정합니다. Blob 스토리지에서 HighWatermarkBlobContainer+HighWatermarkBlobDirectory+HighWatermarkBlobName의 해당 경로(예: *container/path/requestIdCache.txt*)로 이동합니다. 콘텐츠가 0인 Blob을 만듭니다.

      ![BLOB 콘텐츠](media/load-sap-bw-data/blob.png)

   - **LogicAppURL**: 이 템플릿에서는 WebActivity로 Azure Logic Apps를 호출하여 Blob 스토리지에서 상위 워터마크 값을 설정합니다. 또는 Azure SQL Database를 사용하여 저장할 수 있습니다. 저장 프로시저 작업을 사용하여 값을 업데이트합니다.

      다음 그림처럼 먼저 논리 앱을 만들어야 합니다. 그런 다음 **HTTP POST URL** 에 붙여 넣습니다.

      ![논리 앱 구성](media/load-sap-bw-data/logic-app-config.png)

      1. Azure Portal로 이동합니다. 새 **Logic Apps** 서비스를 선택합니다. **+빈 논리 앱** 을 선택하여 **Logic Apps 디자이너** 로 이동합니다.

      2. **HTTP 요청을 수신한 경우**  트리거를 만듭니다. 다음과 같이 HTTP 요청 본문을 지정합니다.

         ```json
         {
            "properties": {
               "sapOpenHubMaxRequestId": {
                  "type": "string"
               }
            },
            "type": "object"
         }
         ```

      3. **Blob 만들기** 작업을 추가합니다. **폴더 경로** 및 **Blob 이름** 에 *HighWatermarkBlobContainer+HighWatermarkBlobDirectory* 및 *HighWatermarkBlobName* 에서 이전에 구성한 것과 동일한 값을 사용합니다.

      4. **저장** 을 선택합니다. 그런 다음 Data Factory 파이프라인에서 사용할 **HTTP POST URL** 의 값을 복사합니다.

4. Data Factory 파이프라인 매개 변수를 제공한 후 **디버그** > **마침** 을 선택하여 구성의 유효성을 검사하는 실행을 호출합니다. 또는 **게시** 를 선택하여 모든 변경 내용을 게시한 다음 **트리거 추가** 를 선택하여 실행을 실행합니다.

## <a name="sap-bw-open-hub-destination-configurations"></a>SAP BW Open Hub Destination 구성

이 섹션에서는 Data Factory에서 SAP BW Open Hub 커넥터를 사용하여 데이터를 복사하는 SAP BW 쪽 구성을 소개합니다.

### <a name="configure-delta-extraction-in-sap-bw"></a>SAP BW에서 델타 추출 구성

기록 복사와 증분 복사가 모두 필요하거나 증분 복사만 필요한 경우 SAP BW에서 델타 추출을 구성합니다.

1. Open Hub Destination을 만듭니다. 필요한 변환 및 데이터 전송 프로세스를 자동으로 만드는 OHD를 SAP Transaction RSA1에서 만들 수 있습니다. 다음 설정을 사용합니다.

   - **ObjectType**: 모든 개체 유형을 사용할 수 있습니다. 여기에서는 **InfoCube** 를 예제로 사용합니다.
   - **대상 유형**: **데이터베이스 테이블** 을 선택합니다.
   - **테이블의 키**: **기술 키** 를 선택합니다.
   - **추출**: **데이터 유지 및 테이블에 레코드 삽입** 을 선택합니다.

   ![SAP BW OHD 델타 추출 만들기 대화 상자](media/load-sap-bw-data/create-sap-bw-ohd-delta.png)

   ![SAP BW OHD 델타2 추출 만들기 대화 상자](media/load-sap-bw-data/create-sap-bw-ohd-delta2.png)

   DTP의 병렬 실행 SAP 작업 프로세스 수를 늘릴 수 있습니다.

   ![DTP의 병렬 프로세스 수를 선택할 수 있는 병렬 처리 설정을 보여 주는 스크린샷.](media/load-sap-bw-data/create-sap-bw-ohd-delta3.png)

2. 프로세스 체인에서 DTP를 예약합니다.

   큐브의 델타 DTP는 필요한 행이 압축되지 않은 경우에만 작동합니다. Open Hub 테이블에 대한 DTP 전에 BW 큐브 압축이 실행되고 있지 않아야 합니다. 가장 쉬운 방법은 DTP를 기존 프로세스 체인에 통합하는 것입니다. 다음 예제에서는 DTP(OHD에 대한)가 조정(집계 롤업) 단계와 축소(큐브 압축) 단계 사이의 프로세스 체인에 삽입됩니다.

   ![SAP BW 프로세스 체인 만들기 흐름 차트](media/load-sap-bw-data/create-sap-bw-process-chain.png)

### <a name="configure-full-extraction-in-sap-bw"></a>SAP BW에서 전체 추출 구성

델타 추출 외에도 동일한 SAP BW InfoProvider의 전체 추출이 필요할 수 있습니다. 이는 일반적으로 전체 복사를 하려고 하지만 증분 복사는 원치 않거나 [델타 추출을 다시 동기화](#resync-delta-extraction)하려는 경우에 적용됩니다.

동일한 OHD에는 두 개 이상의 DTP를 사용할 수 없습니다. 따라서 델타 추출 전에 추가 OHD를 만들어야 합니다.

![SAP BW OHD 전체 만들기](media/load-sap-bw-data/create-sap-bw-ohd-full.png)

전체 로드 OHD의 경우 델타 추출과는 다른 옵션을 선택합니다.

- OHD에서 **추출** 옵션을 **데이터를 삭제하고 레코드를 삽입** 으로 설정합니다. 그렇지 않으면 BW 프로세스 체인에서 DTP를 반복할 때 데이터가 여러 번 추출됩니다.

- DTP에서 **추출 모드** 를 **전체** 로 설정합니다. 다음 이미지처럼 OHD를 만든 직후 자동으로 생성된 DTP를 **델타** 에서 **전체** 로 변경해야 합니다.

   ![‘전체’ 추출에 대해 구성된 SAP BW OHD 만들기 대화 상자](media/load-sap-bw-data/create-sap-bw-ohd-full2.png)

- Data Factory의 BW Open Hub 커넥터에서 **마지막 요청 제외** 를 해제합니다. 그렇지 않으면 아무 것도 추출되지 않습니다.

일반적으로 전체 DTP를 수동으로 실행합니다. 또는 전체 DTP의 프로세스 체인을 만들 수 있습니다. 이 프로세스 체인은 일반적으로 기존 프로세스 체인과 독립적인 별도의 체인입니다. 어느 경우든 Data Factory 복사를 사용하여 추출을 시작하기 전에 DTP가 완료되었는지 확인합니다. 그렇지 않으면 부분 데이터만 복사됩니다.

### <a name="run-delta-extraction-the-first-time"></a>처음으로 델타 추출 실행

첫 번째 델타 추출은 기술적으로 전체 추출입니다. 기본적으로 SAP BW Open Hub 커넥터는 데이터를 복사할 때 마지막 요청을 제외합니다. 첫 번째 델타 추출에서는 후속 DTP가 별도의 요청 ID가 있는 테이블에 델타 데이터를 생성할 때까지 Data Factory 복사 작업에 의해 데이터가 추출되지 않습니다. 이 시나리오를 방지하는 방법에는 다음 두 가지가 있습니다.

- 첫 번째 델타 추출의 **마지막 요청 제외** 옵션을 해제합니다. 델타 추출을 처음 시작하기 전에 첫 번째 델타 DTP가 완료되었는지 확인합니다.
-  다음 섹션에 설명된 대로 델타 추출을 다시 동기화하는 절차를 사용합니다.

### <a name="resync-delta-extraction"></a>델타 추출 다시 동기화

다음 시나리오는 SAP BW 큐브의 데이터를 변경하지만 델타 DTP에서 고려하지 않습니다.

- SAP BW 선택적 삭제(필터 조건을 사용하여 행 삭제)
- SAP BW 요청 삭제(잘못된 요청 삭제)

2015년 이후의 모든 SAP BW 지원 패키지에서 SAP Open Hub Destination은 데이터 마트가 제어하는 데이터 대상이 아닙니다. 따라서 OHD의 데이터를 변경하지 않고 큐브에서 데이터를 삭제할 수 있습니다. 그런 다음 큐브의 데이터를 Data Factory와 다시 동기화해야 합니다.

1. SAP에서 전체 DTP를 사용하여 Data Factory에서 전체 추출을 실행합니다.
2. 델타 DTP의 Open Hub 테이블에서 모든 행을 삭제합니다.
3. 델타 DTP의 상태를 **가져옴** 으로 설정합니다.

그 후에는 모든 후속 델타 DTP 및 Data Factory 델타 추출이 예상대로 작동합니다.

델타 DTP의 상태를 **가져옴** 으로 설정하려면 다음 옵션을 사용하여 델타 DTP를 수동으로 실행합니다.

데이터 전송 없음, 원본에서 델타 상태: 가져옴

## <a name="next-steps"></a>다음 단계

SAP BW Open Hub 커넥터 지원에 대해 알아봅니다.

> [!div class="nextstepaction"]
>[SAP Business Warehouse Open Hub 커넥터](connector-sap-business-warehouse-open-hub.md)

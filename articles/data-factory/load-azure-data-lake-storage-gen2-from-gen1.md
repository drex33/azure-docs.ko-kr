---
title: Azure Data Lake Storage Gen1에서 Gen2로 데이터 복사
description: Azure Data Factory를 사용하여 Azure Data Lake Storage Gen1에서 Gen2로 데이터 복사
ms.author: jianleishen
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/06/2021
ms.openlocfilehash: 5f18e2a1fc64e33faecf17c95e9261e023cc6c9d
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124779714"
---
# <a name="copy-data-from-azure-data-lake-storage-gen1-to-gen2-with-azure-data-factory"></a>Azure Data Factory를 사용하여 Azure Data Lake Storage Gen1에서 Gen2로 데이터 복사

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Lake Storage Gen2는 [Azure Blob 스토리지](../storage/blobs/storage-blobs-introduction.md)를 기반으로 하는 빅 데이터 분석 전용의 기능 세트입니다. 파일 시스템 및 개체 스토리지 패러다임을 모두 사용하여 데이터를 조작하는 데 이 기능을 이용할 수 있습니다.

현재 Azure Data Lake Storage Gen1을 사용 중인 경우 Azure Data Factory를 사용하여 Data Lake Storage Gen1에서 Gen2로 데이터를 복사하여 Azure Data Lake Storage Gen2를 평가할 수 있습니다.

Azure Data Factory는 완전히 관리되는 클라우드 기반 데이터 통합 서비스입니다. 분석 솔루션을 빌드할 때 서비스를 사용하여 풍부한 온-프레미스 및 클라우드 기반 데이터 저장소의 데이터로 레이크를 채우고 시간을 절약할 수 있습니다. 지원되는 커넥터의 목록은 [지원되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats) 표를 참조하세요.

Azure Data Factory는 스케일 아웃, 관리되는 데이터 이동 솔루션을 제공합니다. Data Factory의 스케일 아웃 아키텍처로 인해 높은 처리량으로 데이터를 수집할 수 있습니다. 자세한 내용은 [복사 작업 성능](copy-activity-performance.md)을 참조하세요.

이 문서에서는 Data Factory 데이터 복사 도구를 사용하여 Azure Data Lake Storage Gen1에서 Azure Data Lake Storage Gen2로 데이터를 복사하는 방법을 보여 줍니다. 다른 데이터 저장소 유형에서 데이터를 복사할 때도 이와 유사한 단계를 따를 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* 데이터가 있는 Azure Data Lake Storage Gen1 계정
* Data Lake Storage Gen2가 사용하도록 설정된 Azure Storage 계정 Storage 계정이 없는 경우 [계정을 만듭니다](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM).

## <a name="create-a-data-factory"></a>데이터 팩터리 만들기

1. 왼쪽 메뉴에서 **리소스 만들기** > **데이터 + 분석** > **Data Factory** 를 차례로 선택합니다.
   
   :::image type="content" source="./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png" alt-text="새로 만들기 창에서 Data Factory 선택 항목을 보여 주는 스크린샷.":::

2. **새 데이터 팩터리** 페이지에서 다음 그림에 표시된 필드의 값을 제공합니다. 
      
   :::image type="content" source="./media/load-azure-data-lake-storage-gen2-from-gen1/new-azure-data-factory.png" alt-text="새 데이터 팩터리 페이지를 보여 주는 스크린샷.":::
 
    * **Name**: Azure Data Factory의 전역적으로 고유 이름을 입력합니다. "데이터 팩터리 이름 \"LoadADLSDemo\"를 사용할 수 없습니다" 오류가 발생하면 데이터 팩터리의 다른 이름을 입력합니다. 예를 들어 _**yourname**_**ADFTutorialDataFactory** 를 사용합니다. 데이터 팩터리를 다시 만듭니다. 데이터 팩터리 아티팩트에 대한 명명 규칙은 [데이터 팩터리 명명 규칙](naming-rules.md)을 참조하세요.
    * **구독**: 데이터 팩터리를 만들 Azure 구독을 선택합니다. 
    * **리소스 그룹**: 드롭다운 목록에서 기존 리소스 그룹을 선택합니다. **새로 만들기** 옵션을 선택하고 리소스 그룹의 이름을 입력할 수도 있습니다. 리소스 그룹에 대한 자세한 내용은 [리소스 그룹을 사용하여 Azure 리소스 관리](../azure-resource-manager/management/overview.md)를 참조하세요. 
    * **버전**: **V2** 를 선택합니다.
    * **위치**: 데이터 팩터리의 위치를 선택합니다. 지원되는 위치만 드롭다운 목록에 표시됩니다. 데이터 팩터리에서 사용되는 데이터 저장소가 다른 위치 및 지역에 있어도 됩니다. 

3. **만들기** 를 선택합니다.
4. 만들기가 완료된 후 데이터 팩터리로 이동합니다. 다음 그림과 같이 **데이터 팩터리** 홈페이지가 표시됩니다. 
   
   :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="Azure Data Factory Studio 열기 타일이 있는 Azure Data Factory 홈페이지":::

5. **Azure Data Factory Studio 열기** 타일에서 **열기** 를 선택하여 별도의 탭에서 데이터 통합 애플리케이션을 시작합니다.

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>Azure Data Lake Storage Gen2에 데이터 로드

1. 홈페이지에서 **수집** 타일을 선택하여 데이터 복사 도구를 시작합니다. 

   :::image type="content" source="./media/doc-common-process/get-started-page.png " alt-text="ADF 홈페이지를 보여 주는 스크린샷":::

2. **속성** 페이지에 있는 **작업 유형** 아래에서 **기본 제공 복사 작업** 을 선택한 후 **작업 주기 또는 작업 일정** 에서 **지금 한 번 실행** 을 선택한 후 **다음** 을 선택합니다.

3. **원본 데이터 저장소** 페이지에서 **+새 연결** 을 선택합니다.
    
4. 커넥터 갤러리에서 **Azure Data Lake Storage Gen1** 을 선택하고, **계속** 을 선택합니다.
    
    :::image type="content" source="./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page-adls-gen1.png" alt-text="Azure Data Lake Storage Gen1 연결을 선택하는 페이지를 보여 주는 스크린샷.":::
    
5. **새 연결(Azure Data Lake Storage Gen1)** 페이지에서 다음 단계를 따르세요.
   1. 계정 이름으로 Data Lake Storage Gen1을 선택하고 **Tenant** 를 지정하거나 유효한지 검사합니다.
   1. **연결 테스트** 를 선택하여 설정의 유효성을 검사합니다. 그런 다음 **생성** 를 선택합니다.
  
   > [!IMPORTANT]
   > 이 연습에서는 Azure 리소스용 관리 ID를 사용하여 Azure Data Lake Storage Gen1을 인증합니다. Azure Data Lake Storage Gen1에서 관리 ID에 적절한 권한을 부여하려면 [다음 지침](connector-azure-data-lake-store.md#managed-identity)을 따릅니다.
   
   :::image type="content" source="./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen1-account.png" alt-text="Azure Data Lake Storage Gen1 연결의 구성을 보여 주는 스크린샷.":::
      
6. **원본 데이터 저장소** 페이지에서 다음 단계를 완료합니다. 
    1. **연결** 섹션에서 새로 만든 연결을 선택합니다.
    1. **파일 또는 폴더** 에서 복사할 폴더와 파일을 찾습니다. 폴더 또는 파일을 선택하고, **확인** 을 선택합니다.
    1. **재귀적으로** 및 **이진 복사** 옵션을 선택하여 복사 동작을 지정합니다. **다음** 을 선택합니다.
    
    :::image type="content" source="./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page.png" alt-text="원본 데이터 저장소 페이지를 보여 주는 스크린샷.":::
    
7. **대상 데이터 저장소** 페이지에서 **+새 연결** > **Azure Data Lake Storage Gen2** > **계속** 을 선택합니다.

    :::image type="content" source="./media/load-azure-data-lake-storage-gen2-from-gen1/destination-data-store-page-adls-gen2.png" alt-text="Azure Data Lake Storage Gen2 연결을 선택하는 페이지를 보여 주는 스크린샷.":::

8. **새 연결(Azure Data Lake Storage Gen2)** 페이지에서 다음 단계를 따르세요.
   1. **Storage 계정 이름** 드롭다운 목록에서 Data Lake Storage Gen2 지원 계정을 선택합니다.
   1. **만들기** 를 선택하여 연결을 만듭니다. 

   :::image type="content" source="./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-account.png" alt-text="Azure Data Lake Storage Gen2 연결의 구성을 보여 주는 스크린샷.":::

9. **대상 데이터 저장소** 페이지에서 다음 단계를 완료합니다. 
    1. **연결** 블록에서 새로 만든 연결을 선택합니다. 
    1. **폴더 경로** 에서 출력 폴더 이름으로 **copyfromadlsgen1** 을 입력하고 **다음** 을 선택합니다. Data Factory는 복사하는 동안 해당 Azure Data Lake Storage Gen2 파일 시스템 및 하위 폴더를 만듭니다(없는 경우).

    :::image type="content" source="./media/load-azure-data-lake-storage-gen2-from-gen1/destination-data-store-page.png" alt-text="대상 데이터 저장소 페이지를 보여 주는 스크린샷.":::

10. **설정** 페이지에서 **작업 이름** 필드에 대해 **CopyFromADLSGen1ToGen2** 를 지정한 후 **다음** 을 선택하여 기본 설정을 사용합니다.


11. **요약** 페이지에서 설정을 검토하고, **다음** 을 선택합니다.

    :::image type="content" source="./media/load-azure-data-lake-storage-gen2-from-gen1/copy-summary.png" alt-text="요약 페이지를 보여 주는 스크린샷":::

12. **배포 페이지** 에서 **모니터** 를 선택하여 파이프라인을 모니터링합니다.

    :::image type="content" source="./media/load-azure-data-lake-storage-gen2-from-gen1/deployment-page.png" alt-text="배포 페이지를 보여 주는 스크린샷.":::

13. 왼쪽의 **모니터** 탭이 자동으로 선택됩니다. **파이프라인 이름** 열에는 활동 실행 세부 정보를 보고 파이프라인을 다시 실행하기 위한 링크가 있습니다.

    :::image type="content" source="./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-pipeline-runs.png" alt-text="파이프라인 실행 모니터링 페이지를 보여 주는 스크린샷.":::

14. 파이프라인 실행과 연결된 활동 실행을 보려면 **파이프라인 이름** 열에서 링크를 선택합니다. 파이프라인에는 하나의 작업(복사 작업)만 있으므로 하나의 항목만 표시됩니다. 파이프라인 실행 보기로 다시 전환하려면 맨 위에 있는 이동 경로 메뉴의 **모든 파이프라인 실행** 링크를 선택합니다. **새로 고침** 을 선택하여 목록을 새로 고칩니다. 

    :::image type="content" source="./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-runs.png" alt-text="모니터링 활동 실행 페이지를 보여 주는 스크린샷.":::

15. 각 복사 작업의 실행 세부 정보를 모니터링하려면 작업 모니터링 보기의 **작업 이름** 열 아래에서 **세부 정보** 링크(안경 이미지)를 선택합니다. 원본에서 싱크로 복사된 데이터 양, 데이터 처리량, 해당 기간의 실행 단계 및 사용된 구성과 같은 세부 정보를 모니터링할 수 있습니다.

    :::image type="content" source="./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-run-details.png" alt-text="활동 실행 세부 정보를 보여 주는 스크린샷.":::

16. 데이터가 Azure Data Lake Storage Gen2 계정에 복사되었는지 확인합니다.

## <a name="best-practices"></a>모범 사례

Azure Data Lake Storage Gen1에서 Azure Data Lake Storage Gen2로 업그레이드를 일반적으로 평가하려면 [빅 데이터 분석 솔루션을 Azure Data Lake Storage Gen1에서 Azure Data Lake Storage Gen2로 업그레이드](../storage/blobs/data-lake-storage-migrate-gen1-to-gen2.md)를 참조하세요. 다음 섹션에서는 Data Lake Storage Gen1에서 Data Lake Storage Gen2로 데이터를 업그레이드하는 데 Data Factory를 사용하는 모범 사례를 소개합니다.

### <a name="initial-snapshot-data-migration"></a>초기 스냅샷 데이터 마이그레이션

#### <a name="performance"></a>성능

ADF는 서로 다른 수준에서 병렬 처리를 허용하는 서버리스 아키텍처를 제공합니다. 이 아키텍처를 통해 개발자는 네트워크 대역폭과 스토리지 IOPS 및 대역폭을 최대한 활용하여 환경에 대한 데이터 이동 처리량을 최대화하는 파이프라인을 빌드할 수 있습니다. 

여러 고객은 2GBps 이상의 처리량을 유지하면서도 수억 개의 파일로 구성된 페타바이트 크기 데이터를 Data Lake Storage Gen1에서 Gen2로 마이그레이션했습니다.

다양한 병렬 처리 수준을 통해 뛰어난 데이터 이동 속도를 달성할 수 있습니다.

- 단일 복사 작업의 경우 확장 가능한 컴퓨팅 리소스를 활용할 수 있습니다. Azure Integration Runtime을 사용하면 서버리스 방식으로 각 복사 작업에 대해 최대 256 [DIU(데이터 통합 단위)](copy-activity-performance-features.md#data-integration-units)를 지정할 수 있습니다. 자체 호스팅 Integration Runtime을 사용하는 경우 수동으로 머신을 스케일 업하거나 여러 머신(최대 4개의 노드)으로 스케일 아웃할 수 있으며, 단일 복사 작업은 모든 노드에서 해당 파일 세트를 분할합니다.
- 단일 복사 작업은 여러 스레드를 사용하여 데이터 저장소에서 읽고 씁니다.
- ADF 제어 흐름은 여러 복사 작업을 병렬로 시작할 수 있습니다(예: For Each 루프 사용).

#### <a name="data-partitions"></a>데이터 파티션

Data Lake Storage Gen1의 총 데이터 크기가 10TB 미만이고 파일 수가 100만 개보다 작은 경우 단일 복사 활동 실행에서 모든 데이터를 복사할 수 있습니다. 복사할 데이터가 많은 경우 또는 일괄 처리로 데이터 마이그레이션을 관리하고 각 작업이 특정 시간 프레임 내에서 완료되도록 하려면 데이터를 분할합니다. 또한 분할을 통해 예기치 않은 문제가 발생하는 위험을 줄일 수 있습니다. 

[복사 작업 속성](connector-azure-data-lake-store.md#copy-activity-properties)에서 **name range- listAfter/listBefore** 를 사용하여 파일을 분할할 수 있습니다. 각 복사 작업은 한 번에 하나의 파티션을 복사하도록 구성할 수 있으므로 여러 복사 작업이 단일 Data Lake Storage Gen1 계정에서 데이터를 동시에 복사할 수 있습니다.


#### <a name="rate-limiting"></a>속도 제한 

모범 사례로, 대표적인 샘플 데이터 세트를 사용하여 성능 POC를 수행함으로써 적절한 파티션 크기를 결정할 수 있습니다.

1. 단일 파티션과 기본 DIU 설정을 사용하는 단일 복사 작업으로 시작합니다. [병렬 복사](copy-activity-performance-features.md#parallel-copy)는 항상 **비어 있음(기본값)** 으로 설정하는 것이 좋습니다. 복사 처리량이 좋지 않으면 [성능 튜닝 단계](copy-activity-performance.md#performance-tuning-steps)에 따라 성능 병목 현상을 식별하고 해결합니다. 

2. 네트워크의 대역폭 제한 또는 데이터 저장소의 IOPS/대역폭 제한에 도달하거나 단일 복사 작업에서 허용되는 최대 256 DIU에 도달할 때까지 DIU 설정을 점차적으로 늘립니다. 

3. 단일 복사 작업의 성능을 최대화했지만 아직 환경의 처리량 상한에 도달하지 않은 경우 여러 복사 작업을 병렬로 실행할 수 있습니다.  

[복사 작업 모니터링](copy-activity-monitoring.md#monitor-visually)에서 상당한 수의 제한 오류가 표시되면 스토리지 계정의 용량 제한에 도달했음을 나타냅니다. ADF는 데이터 손실이 없도록 각 제한 오류를 극복하기 위해 자동으로 재시도하지만 너무 많은 재시도는 복사 처리량에도 영향을 미칩니다. 이러한 경우 상당한 양의 제한 오류를 방지하기 위해 동시에 실행되는 복사 작업의 수를 줄이는 것이 좋습니다. 단일 복사 작업을 사용하여 데이터를 복사했다면 DIU를 줄이는 것이 좋습니다.


### <a name="delta-data-migration"></a>델타 데이터 마이그레이션

여러 가지 방법을 사용하여 Data Lake Storage Gen1에서 새로운 파일 또는 업데이트된 파일만 로드할 수 있습니다.

- 시간이 분할된 폴더 또는 파일 이름으로 새 파일 또는 업데이트된 파일을 로드합니다. 예를 들어 /2019/05/13/*입니다.
- LastModifiedDate에서 새 파일 또는 업데이트된 파일을 로드합니다. 많은 양의 파일을 복사하는 경우 새 파일을 식별하기 위해 전체 Data Lake Storage Gen1 계정을 검사하는 단일 복사 작업으로 인한 낮은 복사 처리량 결과를 방지하기 위해 먼저 파티션을 수행합니다. 
- 타사 도구나 솔루션을 통해 새 파일 또는 업데이트된 파일을 식별합니다. 그런 다음, 매개 변수 또는 테이블 또는 파일을 통해 파일 또는 폴더 이름을 Data Factory 파이프라인으로 전달합니다. 

증분 로드를 수행하는 적절한 빈도는 Azure Data Lake Storage Gen1의 총 파일 수와 매번 로드되는 새 파일 또는 업데이트된 파일의 양에 따라 달라집니다. 

### <a name="network-security"></a>네트워크 보안

기본적으로 ADF는 HTTPS 프로토콜을 통한 암호화된 연결을 사용하여 Azure Data Lake Storage Gen1에서 Gen2로 데이터를 전송합니다. HTTPS는 전송 중인 데이터의 암호화를 제공하고, 도청 및 메시지 가로채기(man-in-the-middle) 공격을 방지합니다.

또는 퍼블릭 인터넷을 통해 데이터를 전송하지 않으려면 프라이빗 네트워크를 통해 데이터를 전송하여 더 높은 수준의 보안을 달성할 수 있습니다.

### <a name="preserve-acls"></a>ACL 보존

Data Lake Storage Gen1에서 Data Lake Storage Gen2로 업그레이드할 때 데이터 파일과 함께 ACL을 복제하려면 [Data Lake Storage Gen1의 ACL 유지](connector-azure-data-lake-storage.md#preserve-acls)를 참조하세요. 

### <a name="resilience"></a>복원력

단일 복사 작업 실행 내에서 ADF에는 기본 제공 재시도 메커니즘이 있으므로 데이터 저장소나 기본 네트워크에서 특정 수준의 일시적 오류를 처리할 수 있습니다. 10TB를 초과하는 데이터를 마이그레이션하는 경우 예기치 않은 문제가 발생할 위험을 낮추도록 데이터를 분할하는 것이 좋습니다.

또한 복사 작업에서 [내결함성](copy-activity-fault-tolerance.md)을 사용하여 미리 정의된 오류를 건너뛸 수 있습니다. 복사 작업에서 [데이터 일관성 확인](copy-activity-data-consistency.md)을 사용하여 데이터가 원본에서 대상 저장소로 성공적으로 복사되었는지 확인할 뿐 아니라 원본 및 대상 저장소 간에 일관성이 유지되었는지도 확인할 수 있습니다.


### <a name="permissions"></a>사용 권한 

Data Factory에서 [Data Lake Storage Gen1 커넥터](connector-azure-data-lake-store.md)는 Azure 리소스 인증에 대한 서비스 주체 및 관리 ID를 지원합니다. [Data Lake Storage Gen2 커넥터](connector-azure-data-lake-storage.md)는 Azure 리소스 인증에 대한 계정 키, 서비스 주체 및 관리 ID를 지원합니다. Data Factory에서 필요한 모든 파일 또는 ACL(액세스 제어 목록)을 탐색하고 복사할 수 있도록 하려면 사용자가 제공하는 계정에 대한 충분한 권한을 부여하여 모든 파일을 액세스, 읽기 또는 작성하고 원하는 경우 ACL을 설정합니다. 마이그레이션 기간 중에 슈퍼 사용자 또는 소유자 역할을 부여합니다. 


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [복사 작업 개요](copy-activity-overview.md)
> [Azure Data Lake Storage Gen1 커넥터](connector-azure-data-lake-store.md)
> [Azure Data Lake Storage Gen2 커넥터](connector-azure-data-lake-storage.md)
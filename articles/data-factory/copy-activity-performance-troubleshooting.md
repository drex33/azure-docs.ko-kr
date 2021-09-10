---
title: 복사 작업 성능 문제 해결
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 및 Azure Synapse Analytics에서 복사 작업 성능 문제를 해결하는 방법을 알아봅니다.
ms.author: jianleishen
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 01/07/2021
ms.openlocfilehash: f424366a74ddb2955f1e0e339f82630da82b6953
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122642456"
---
# <a name="troubleshoot-copy-activity-performance"></a>복사 작업 성능 문제 해결

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory에서 복사 활동 성능 문제를 해결하는 방법을 설명합니다. 

복사 활동을 실행한 후 [복사 활동 모니터링](copy-activity-monitoring.md) 보기에서 실행 결과 및 성능 통계를 수집할 수 있습니다. 다음은 예제입니다.

![복사 활동 실행 세부 정보 모니터링](./media/copy-activity-overview/monitor-copy-activity-run-details.png)

## <a name="performance-tuning-tips"></a>성능 튜닝 팁

일부 시나리오에서는 복사 작업을 실행할 때 위 예제에서 보이는 것과 같이 맨 위에 **"성능 튜닝 팁"** 이 표시됩니다. 이 팁은 복사 처리량을 높이는 방법에 대한 제안과 함께 이 특정 복사 실행에 대해 서비스가 식별한 병목 상태를 알려줍니다. 권장 사항을 변경한 후 복사를 다시 실행합니다.

참고로, 현재 성능 튜닝 팁은 다음과 같은 경우에 대한 제안을 제공합니다.

| 범주              | 성능 튜닝 팁                                      |
| --------------------- | ------------------------------------------------------------ |
| 데이터 저장소 관련   | **Azure Synapse Analytics** 로 데이터 로드: 사용되지 않는 경우 PolyBase 또는 COPY 문을 사용하는 것이 좋습니다. |
| &nbsp;                | **Azure SQL Database** 에서/으로 데이터 복사: DTU 사용률이 높을 때 더 높은 계층으로 업그레이드하는 것이 좋습니다. |
| &nbsp;                | **Azure Cosmos DB** 에서/으로 데이터 복사: RU 사용률이 높을 때 더 큰 RU로 업그레이드하는 것이 좋습니다. |
|                       | **SAP 테이블** 에서 데이터 복사: 많은 양의 데이터를 복사하는 경우 SAP 커넥터의 파티션 옵션을 활용하여 병렬 로드를 사용하도록 설정하고 최대 파티션 수를 늘리는 것이 좋습니다. |
| &nbsp;                | **Amazon Redshift** 에서 데이터 수집: 사용되지 않는 경우 UNLOAD를 사용하는 것이 좋습니다. |
| 데이터 저장소 제한 | 복사하는 동안 데이터 저장소에서 다양한 읽기/쓰기 작업이 제한되는 경우 데이터 저장소에 대해 허용된 요청 속도를 확인하고 증가시키거나 동시 워크로드를 줄이는 것이 좋습니다. |
| 통합 런타임  | **자체 호스팅 IR(Integration Runtime)** 을 사용하고 IR에 실행 가능한 리소스가 있을 때까지 복사 활동이 큐에서 오래 대기하는 경우 IR을 확장/축소하는 것이 좋습니다. |
| &nbsp;                | 최적이 아닌 영역에 있는 **Azure Integration Runtime** 을 사용하여 읽기/쓰기 속도가 느려지는 경우 다른 지역에서 IR을 사용하도록 구성하는 것이 좋습니다. |
| 내결함성       | 내결함성을 구성하고 호환되지 않는 행을 건너뛰면 성능이 저하되는 경우 원본 및 싱크 데이터가 호환되는지 확인하는 것이 좋습니다. |
| 준비된 복사           | 준비된 복사본이 구성되어 있지만 원본 싱크 쌍에는 도움이 되지 않는 경우 제거하는 것이 좋습니다. |
| 다시 시작                | 복사 활동이 마지막 실패 지점에서 재개되었지만 원래 실행 후 DIU 설정을 변경하는 경우에는 새 DIU 설정이 적용되지 않습니다. |

## <a name="understand-copy-activity-execution-details"></a>복사 활동 실행 세부 정보 이해

복사 활동 모니터링 보기의 맨 아래에 있는 실행 세부 정보 및 기간은 복사 활동이 진행되는 주요 단계를 설명합니다(이 문서 시작 부분의 예 참조). 이 단계는 특히 복사 성능 문제를 해결하는 데 유용합니다. 복사 실행의 병목 상태가 가장 오랜 기간이 소요되는 작업입니다. 각 단계의 정의에 대한 다음 표를 참조하고, 이러한 정보와 함께 [Azure IR의 복사 작업 문제 해결](#troubleshoot-copy-activity-on-azure-ir) 및 [자체 호스팅 IR의 복사 활동 문제 해결](#troubleshoot-copy-activity-on-self-hosted-ir) 방법을 알아봅니다.

| 단계           | Description                                                  |
| --------------- | ------------------------------------------------------------ |
| 큐           | 복사 활동이 실제로 통합 런타임에서 시작될 때까지 경과된 시간입니다. |
| 사전 복사 스크립트 | IR에서 시작되는 복사 활동과 싱크 데이터 저장소에서 사전 복사 스크립트 실행을 완료한 복사 활동 사이에 경과된 시간입니다. 데이터베이스 싱크에 대해 사전 복사 스크립트를 구성할 때 적용합니다. 예를 들어 Azure SQL Database에 데이터를 쓸 때에는 새 데이터를 복사하기 전에 정리 작업을 수행하세요. |
| 전송        | 이전 단계의 끝과 소스에서 싱크로 모든 데이터를 전송하는 IR 사이에 경과된 시간입니다. <br/>전송 아래의 하위 단계는 병렬로 실행되며 일부 작업(예: 파일 형식 구문 분석/생성)은 표시되지 않습니다.<br><br/>- **첫 번째 바이트까지의 시간:** 이전 단계의 끝과 IR이 원본 데이터 저장소에서 첫 번째 바이트를 받는 시간 사이에 경과된 시간입니다. 파일 기반이 아닌 소스에 적용됩니다.<br>- **원본 나열:** 소스 파일 또는 데이터 파티션을 열거하는 데 소요된 시간입니다. 후자는 데이터베이스(예: Oracle/SAP HANA/Teradata/Netezza/etc)에서 데이터를 복사하는 경우와 같이 데이터베이스 원본에 대한 파티션 옵션을 구성할 때 적용됩니다.<br/>-**원본에서 읽기:** 원본 데이터 저장소에서 데이터를 검색하는 데 소요된 시간입니다.<br/>- **싱크에 쓰기:** 싱크 데이터 저장소에 데이터를 쓰는 데 소요된 시간입니다. Azure Cognitive Search, Azure Data Explorer, Azure Table Storage, Oracle, SQL Server, Common Data Service, Dynamics 365, Dynamics CRM, Salesforce/Salesforce Service Cloud를 비롯한 일부 커넥터에는 현재 이 메트릭이 없습니다. |

## <a name="troubleshoot-copy-activity-on-azure-ir"></a>Azure IR의 복사 활동 문제 해결

[성능 튜닝 단계](copy-activity-performance.md#performance-tuning-steps)를 따라 시나리오에 대한 성능 테스트를 계획하고 수행합니다. 

복사 작업 성능이 예상을 충족하지 못하는 경우 Azure Integration Runtime에서 실행되는 단일 복사 활동의 문제를 해결하려면 복사 모니터링 보기에 [성능 튜닝 팁](#performance-tuning-tips)이 표시되면 제안을 적용하고 다시 시도하세요. 그렇지 않으면 [복사 활동 실행 세부 정보를 이해하고](#understand-copy-activity-execution-details), 어느 단계의 기간이 **가장 긴** 지 확인한 다음 아래 지침을 적용하여 복사 성능을 향상시키세요.

- **‘사전 복사 스크립트’에 긴 시간 발생:** 즉, 싱크 데이터베이스에서 실행되는 사전 복사 스크립트를 완료하는 데 시간이 오래 걸린다는 것을 의미합니다. 지정된 사전 복사 스크립트 논리를 조정하여 성능을 향상시킵니다. 스크립트 개선에 도움이 필요한 경우 데이터베이스 팀에 문의하십시오.

- **‘전송 - 첫 번째 바이트까지의 시간’에 긴 작업 시간 발생**: 즉, 원본 쿼리가 데이터를 반환하는 데 시간이 오래 걸린다는 것을 의미합니다. 쿼리 또는 서버를 확인하고 최적화합니다. 추가 도움이 필요하면 데이터 저장소 팀에 문의하세요.

- **‘전송 - 원본 나열’에 긴 작업 시간 발생**: 즉 소스 파일 또는 원본 데이터베이스 데이터 파티션을 열거하는 속도가 느리다는 것을 의미합니다.
  - 파일 기반 소스에서 데이터를 복사할 때 폴더 경로 또는 파일 이름(`wildcardFolderPath` 또는 `wildcardFileName`)에 **와일드 카드 필터** 를 사용하거나 **파일의 마지막 수정 시간 필터**(`modifiedDatetimeStart` 또는 `modifiedDatetimeEnd`)를 사용하는 경우 해당 필터는 지정된 폴더 아래의 모든 파일을 클라이언트 쪽으로 복사 활동을 나열하게 한 다음 필터를 적용합니다. 이러한 파일 열거는 특히 작은 파일 집합만 필터 규칙을 충족하는 경우 병목 상태가 될 수 있습니다.

    - [날짜/시간 분할 파일 경로 또는 이름에 따라 파일을 복사](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md)할 수 있는지 여부를 확인합니다. 이러한 방식은 원본 나열 쪽에 부담을 주지 않습니다.

    - 대신 데이터 저장소의 기본 필터, 특히 Amazon S3/Azure Blob/Azure File Storage의 경우 ‘**prefix**’, ADLS Gen1의 경우 ‘**listAfter/listBefore**’를 사용할 수 있는지 확인합니다. 이러한 필터는 데이터 저장소 서버 쪽 필터이며 훨씬 더 나은 성능을 제공합니다.

    - 큰 데이터 집합 하나를 작은 데이터 집합 여러 개로 분할하고 이러한 복사 작업이 데이터의 각 부분을 동시에 실행하는 것을 고려하세요. Lookup/GetMetadata + ForEach + Copy를 사용하여 이 작업을 수행할 수 있습니다. 일반적인 예는 [여러 컨테이너에서 파일 복사](solution-template-copy-files-multiple-containers.md) 또는 [Amazon S3에서 ADLS Gen2로 데이터 마이그레이션](solution-template-migration-s3-azure.md) 솔루션 템플릿을 참조하세요.

  - 서비스가 원본에서 제한 오류를 보고하는지 또는 데이터 저장소의 사용률이 높은 상태인지 확인합니다. 그렇다면 데이터 저장소의 워크로드를 줄이거나 데이터 저장소 관리자에게 문의하여 제한 한도 또는 사용 가능한 리소스를 늘리십시오.

  - 원본 데이터 저장소 영역과 같거나 가까운 곳에서 Azure IR을 사용합니다.

- **‘전송 - 원본에서 읽기’에 긴 작업 시간 발생**: 

  - 적용되는 경우 커넥터별 데이터 로드 모범 사례를 채택합니다. 예를 들어 [Amazon Redshift](connector-amazon-redshift.md)에서 데이터를 복사할 때 Redshift UNLOAD를 사용하도록 구성합니다.

  - 서비스가 원본에서 제한 오류를 보고하는지 또는 데이터 저장소의 사용률이 높은지 확인합니다. 그렇다면 데이터 저장소의 워크로드를 줄이거나 데이터 저장소 관리자에게 문의하여 제한 한도 또는 사용 가능한 리소스를 늘리십시오.

  - 복사 원본 및 싱크 패턴을 확인합니다. 

    - 복사 패턴이 4개 이상의 DIU(데이터 통합 단위)를 지원하는 경우, 자세한 내용은 [이 섹션](copy-activity-performance-features.md#data-integration-units)을 참조하세요. 일반적으로 DIU를 늘려 성능을 향상시킬 수 있습니다. 

    - 그렇지 않으면 큰 데이터 집합 하나를 작은 데이터 집합 여러 개로 분할하고 이러한 복사 작업이 데이터의 각 부분을 동시에 실행하는 것을 고려하세요. Lookup/GetMetadata + ForEach + Copy를 사용하여 이 작업을 수행할 수 있습니다. 일반적인 예는 [여러 컨테이너에서 파일 복사](solution-template-copy-files-multiple-containers.md), [Amazon S3에서 ADLS Gen2로 데이터 마이그레이션](solution-template-migration-s3-azure.md) 또는 [컨트롤 테이블을 사용하여 대량 복사](solution-template-bulk-copy-with-control-table.md) 솔루션 템플릿을 참조하세요.

  - 원본 데이터 저장소 영역과 같거나 가까운 곳에서 Azure IR을 사용합니다.

- **‘전송 - 싱크로 쓰기’에 긴 작업 시간 발생**:

  - 적용되는 경우 커넥터별 데이터 로드 모범 사례를 채택합니다. 예를 들어 [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md)로 데이터를 복사하는 경우 PolyBase 또는 COPY 문을 사용합니다. 

  - 서비스가 싱크에서 제한 오류를 보고하는지 또는 데이터 저장소의 사용률이 높은지 확인합니다. 그렇다면 데이터 저장소의 워크로드를 줄이거나 데이터 저장소 관리자에게 문의하여 제한 한도 또는 사용 가능한 리소스를 늘리십시오.

  - 복사 원본 및 싱크 패턴을 확인합니다. 

    - 복사 패턴이 4개 이상의 DIU(데이터 통합 단위)를 지원하는 경우, 자세한 내용은 [이 섹션](copy-activity-performance-features.md#data-integration-units)을 참조하세요. 일반적으로 DIU를 늘려 성능을 향상시킬 수 있습니다. 

    - 그렇지 않으면 [병렬 복사](copy-activity-performance-features.md)를 점차적으로 조정합니다. 병렬 복사가 너무 많으면 성능이 저하될 수도 있습니다.

  - 싱크 데이터 저장소 영역과 같거나 가까운 곳에서 Azure IR을 사용합니다.

## <a name="troubleshoot-copy-activity-on-self-hosted-ir"></a>자체 호스팅 IR에서 복사 활동 문제 해결

[성능 튜닝 단계](copy-activity-performance.md#performance-tuning-steps)를 따라 시나리오에 대한 성능 테스트를 계획하고 수행합니다. 

복사 성능이 예상을 충족하지 못하는 경우 Azure Integration Runtime에서 실행되는 단일 복사 활동의 문제를 해결하려면 복사 모니터링 보기에 [성능 튜닝 팁](#performance-tuning-tips)이 표시되면 제안을 적용하고 다시 시도하세요. 그렇지 않으면 [복사 활동 실행 세부 정보를 이해하고](#understand-copy-activity-execution-details), 어느 단계의 기간이 **가장 긴** 지 확인한 다음 아래 지침을 적용하여 복사 성능을 향상시키세요.

- **‘큐’에 긴 시간 발생**: 즉, 복사 활동은 자체 호스팅 IR에서 실행할 리소스를 가질 때까지 큐에서 오래 대기한다는 것을 의미합니다. IR 용량과 사용량을 확인하고 워크로드에 따라 [스케일 업 또는 스케일 아웃](create-self-hosted-integration-runtime.md#high-availability-and-scalability)합니다.

- **‘전송 - 첫 번째 바이트까지의 시간’에 긴 작업 시간 발생**: 즉, 원본 쿼리가 데이터를 반환하는 데 시간이 오래 걸린다는 것을 의미합니다. 쿼리 또는 서버를 확인하고 최적화합니다. 추가 도움이 필요하면 데이터 저장소 팀에 문의하세요.

- **‘전송 - 원본 나열’에 긴 작업 시간 발생**: 즉 소스 파일 또는 원본 데이터베이스 데이터 파티션을 열거하는 속도가 느리다는 것을 의미합니다.

  - 자체 호스팅 IR 시스템이 원본 데이터 저장소에 연결하는 대기 시간이 짧은지 확인합니다. 소스가 Azure에 있는 경우 [이 도구](http://www.azurespeed.com/Azure/Latency)를 사용하여 자체 호스팅 IR 시스템에서 Azure 지역까지의 대기 시간을 확인할 수 있습니다. 이는 적을수록 좋습니다.

  - 파일 기반 소스에서 데이터를 복사할 때 폴더 경로 또는 파일 이름(`wildcardFolderPath` 또는 `wildcardFileName`)에 **와일드 카드 필터** 를 사용하거나 **파일의 마지막 수정 시간 필터**(`modifiedDatetimeStart` 또는 `modifiedDatetimeEnd`)를 사용하는 경우 해당 필터는 지정된 폴더 아래의 모든 파일을 클라이언트 쪽으로 복사 활동을 나열하게 한 다음 필터를 적용합니다. 이러한 파일 열거는 특히 작은 파일 집합만 필터 규칙을 충족하는 경우 병목 상태가 될 수 있습니다.

    - [날짜/시간 분할 파일 경로 또는 이름에 따라 파일을 복사](tutorial-incremental-copy-partitioned-file-name-copy-data-tool.md)할 수 있는지 여부를 확인합니다. 이러한 방식은 원본 나열 쪽에 부담을 주지 않습니다.

    - 대신 데이터 저장소의 기본 필터, 특히 Amazon S3/Azure Blob/Azure File Storage의 경우 ‘**prefix**’, ADLS Gen1의 경우 ‘**listAfter/listBefore**’를 사용할 수 있는지 확인합니다. 이러한 필터는 데이터 저장소 서버 쪽 필터이며 훨씬 더 나은 성능을 제공합니다.

    - 큰 데이터 집합 하나를 작은 데이터 집합 여러 개로 분할하고 이러한 복사 작업이 데이터의 각 부분을 동시에 실행하는 것을 고려하세요. Lookup/GetMetadata + ForEach + Copy를 사용하여 이 작업을 수행할 수 있습니다. 일반적인 예는 [여러 컨테이너에서 파일 복사](solution-template-copy-files-multiple-containers.md) 또는 [Amazon S3에서 ADLS Gen2로 데이터 마이그레이션](solution-template-migration-s3-azure.md) 솔루션 템플릿을 참조하세요.

  - 서비스가 원본에서 제한 오류를 보고하는지 또는 데이터 저장소의 사용률이 높은 상태인지 확인합니다. 그렇다면 데이터 저장소의 워크로드를 줄이거나 데이터 저장소 관리자에게 문의하여 제한 한도 또는 사용 가능한 리소스를 늘리십시오.

- **‘전송 - 원본에서 읽기’에 긴 작업 시간 발생**: 

  - 자체 호스팅 IR 시스템이 원본 데이터 저장소에 연결하는 대기 시간이 짧은지 확인합니다. 소스가 Azure에 있는 경우 [이 도구](http://www.azurespeed.com/Azure/Latency)를 사용하여 자체 호스팅 IR 시스템에서 Azure 지역까지의 대기 시간을 확인할 수 있습니다. 이는 적을수록 좋습니다.

  - 자체 호스팅 IR 시스템에 데이터를 효율적으로 읽고 전송할 수 있는 충분한 인바운드 대역폭이 있는지 확인합니다. 원본 데이터 저장소가 Azure에 있는 경우 [이 도구](https://www.azurespeed.com/Azure/Download)를 사용하여 다운로드 속도를 확인할 수 있습니다.

  - Azure Portal -> 데이터 팩터리 또는 Synapse 작업 영역 -> 개요 페이지에서 자체 호스팅 IR의 CPU 및 메모리 사용량 추세를 확인합니다. CPU 사용량이 많거나 사용 가능한 메모리가 부족한 경우 [IR 스케일 업/아웃](create-self-hosted-integration-runtime.md#high-availability-and-scalability)을 고려하세요.

  - 적용되는 경우 커넥터별 데이터 로드 모범 사례를 채택합니다. 예를 들면 다음과 같습니다.

    - [Oracle](connector-oracle.md#oracle-as-source), [Netezza](connector-netezza.md#netezza-as-source), [Teradata](connector-teradata.md#teradata-as-source), [SAP HANA](connector-sap-hana.md#sap-hana-as-source), [SAP 테이블](connector-sap-table.md#sap-table-as-source) 및 [SAP Open Hub](connector-sap-business-warehouse-open-hub.md#sap-bw-open-hub-as-source)에서 데이터를 복사하는 경우 데이터 파티션 옵션을 사용하여 데이터를 병렬로 복사합니다.

    - [HDFS](connector-hdfs.md)에서 데이터를 복사하는 경우 DistCp를 사용하도록 구성합니다.

    - [Amazon Redshift](connector-amazon-redshift.md)에서 데이터를 복사할 때 Redshift UNLOAD를 사용하도록 구성합니다.

  - 서비스가 원본에서 제한 오류를 보고하는지 또는 데이터 저장소의 사용률이 높은지 확인합니다. 그렇다면 데이터 저장소의 워크로드를 줄이거나 데이터 저장소 관리자에게 문의하여 제한 한도 또는 사용 가능한 리소스를 늘리십시오.

  - 복사 원본 및 싱크 패턴을 확인합니다. 

    - 파티션 옵션 사용 데이터 저장소에서 데이터를 복사하는 경우 [병렬 복사](copy-activity-performance-features.md)를 점차적으로 조정하는 것이 좋습니다. 병렬 복사가 너무 많으면 성능이 저하될 수 있습니다.

    - 그렇지 않으면 큰 데이터 집합 하나를 작은 데이터 집합 여러 개로 분할하고 이러한 복사 작업이 데이터의 각 부분을 동시에 실행하는 것을 고려하세요. Lookup/GetMetadata + ForEach + Copy를 사용하여 이 작업을 수행할 수 있습니다. 일반적인 예는 [여러 컨테이너에서 파일 복사](solution-template-copy-files-multiple-containers.md), [Amazon S3에서 ADLS Gen2로 데이터 마이그레이션](solution-template-migration-s3-azure.md) 또는 [컨트롤 테이블을 사용하여 대량 복사](solution-template-bulk-copy-with-control-table.md) 솔루션 템플릿을 참조하세요.

- **‘전송 - 싱크로 쓰기’에 긴 작업 시간 발생**:

  - 적용되는 경우 커넥터별 데이터 로드 모범 사례를 채택합니다. 예를 들어 [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md)로 데이터를 복사하는 경우 PolyBase 또는 COPY 문을 사용합니다. 

  - 자체 호스팅 IR 시스템이 싱크 데이터 저장소에 연결하는 대기 시간이 짧은지 확인합니다. 싱크가 Azure에 있는 경우 [이 도구](http://www.azurespeed.com/Azure/Latency)를 사용하여 자체 호스팅 IR 시스템에서 Azure 지역까지의 대기 시간을 확인할 수 있습니다. 이는 적을수록 좋습니다.

  - 자체 호스팅 IR 시스템에 데이터를 효율적으로 전송하고 쓸 수 있는 충분한 아웃바운드 대역폭이 있는지 확인합니다. 싱크 데이터 저장소가 Azure에 있는 경우 [이 도구](https://www.azurespeed.com/Azure/UploadLargeFile)를 사용하여 업로드 속도를 확인할 수 있습니다.

  - Azure Portal -> 데이터 팩터리 또는 Synapse 작업 영역 -> 개요 페이지에서 자체 호스팅 IR의 CPU 및 메모리 사용량 추세를 확인합니다. CPU 사용량이 많거나 사용 가능한 메모리가 부족한 경우 [IR 스케일 업/아웃](create-self-hosted-integration-runtime.md#high-availability-and-scalability)을 고려하세요.

  - 서비스가 싱크에서 제한 오류를 보고하는지 또는 데이터 저장소의 사용률이 높은지 확인합니다. 그렇다면 데이터 저장소의 워크로드를 줄이거나 데이터 저장소 관리자에게 문의하여 제한 한도 또는 사용 가능한 리소스를 늘리십시오.

  - [병렬 복사](copy-activity-performance-features.md)를 점차적으로 조정하는 것이 좋습니다. 병렬 복사가 너무 많으면 성능이 저하될 수도 있습니다.


## <a name="connector-and-ir-performance"></a>커넥터 및 IR 성능 

이 섹션에서는 특정 커넥터 형식 또는 통합 런타임에 대한 몇 가지 성능 문제 해결 가이드를 살펴봅니다.

### <a name="activity-execution-time-varies-using-azure-ir-vs-azure-vnet-ir"></a>Azure IR과 Azure VNet IR을 사용할 때 작업 실행 시간이 달라짐

작업 실행 시간은 데이터 집합이 다른 Integration Runtime을 기반으로 하는 경우에 달라집니다.

- **증상**: 데이터 집합에서 연결된 서비스 드롭다운을 전환하기만 하면 동일한 파이프라인 작업이 수행되지만 런타임은 크게 다릅니다. 데이터 집합이 Managed Virtual Network Integration Runtime 기반인 경우에는 Default Integration Runtime 기반일 때 실행하는 것보다 평균적으로 더 많은 시간이 걸립니다.  

- **원인**: 파이프라인 실행의 세부 정보를 확인하면 느린 파이프라인은 관리형 VNET(Virtual Network) IR에서 실행 중이고 일반 파이프라인은 Azure IR에서 실행 중임을 알 수 있습니다. 기본적으로 관리형 VNet IR은 서비스 인스턴스당 하나의 계산 노드를 예약하지 않으므로 Azure IR보다 큐 시간이 더 오래 걸립니다. 따라서 각 복사 작업을 시작할 준비가 되어 있고, Azure IR이 아닌 VNet 조인에서 주로 발생합니다. 

    
### <a name="low-performance-when-loading-data-into-azure-sql-database"></a>Azure SQL Database에 데이터를 로드할 때 성능 저하

- **증상**: Azure SQL Database에 데이터를 복사하는 속도가 느려집니다.

- **원인**: 문제의 근본 원인은 대부분 Azure SQL Database 쪽의 병목 상태로 인해 트리거됩니다. 가능한 원인은 다음과 같습니다.

    - Azure SQL Database 계층이 충분히 높지 않습니다.

    - Azure SQL Database DTU 사용량이 100%에 가깝습니다. [성능을 모니터링](../azure-sql/database/monitor-tune-overview.md)하고 Azure SQL Database 계층을 업그레이드하는 것을 고려할 수 있습니다.

    - 인덱스가 올바르게 설정되지 않았습니다. 데이터를 로드하기 전에 모든 인덱스를 제거하고 로드가 완료된 후 다시 만듭니다.

    - WriteBatchSize가 스키마 행 크기에 맞게 충분히 크지 않습니다. 문제에 대한 속성을 확대합니다.

    - 대량 삽입 대신 저장 프로시저를 사용 중이므로 성능이 저하될 것으로 예상됩니다. 


### <a name="timeout-or-slow-performance-when-parsing-large-excel-file"></a>큰 Excel 파일을 구문 분석할 때 시간 초과 또는 성능 저하

- **증상**:

    - Excel 데이터 집합을 만들고 연결/저장소에서 스키마를 가져오거나 데이터를 미리 보거나 워크시트를 나열하거나 새로 고칠 때 Excel 파일 크기가 크면 시간 초과 오류가 발생할 수 있습니다.

    - 복사 활동을 사용하여 큰 Excel 파일(>= 100MB)에서 다른 데이터 저장소로 데이터를 복사할 때 성능이 저하되거나 OOM 문제가 발생할 수 있습니다.

- **원인**: 

    - 스키마 가져오기, 데이터 미리 보기, Excel 데이터 집합에서 워크시트 나열과 같은 작업의 경우 시간 제한이 100초이며 고정적입니다. 큰 Excel 파일의 경우 이러한 작업이 시간 제한 값 이내에 완료되지 않을 수 있습니다.

    - 복사 작업은 전체 Excel 파일을 메모리로 읽은 다음, 지정된 워크시트와 셀을 찾아 데이터를 읽습니다. 이 동작은 기본 SDK 서비스 사용으로 인해 발생합니다.

- **해결 방법**: 

    - 스키마 가져오기의 경우 원본 파일의 하위 집합인 더 작은 샘플 파일을 생성하고 ‘연결/저장소에서 스키마 가져오기’ 대신 ‘샘플 파일에서 스키마 가져오기’를 선택할 수 있습니다.

    - 워크시트 나열의 경우 워크시트 드롭다운에서 ‘편집’을 클릭하고 시트 이름/인덱스를 대신 입력할 수 있습니다.

    - 큰 Excel 파일(>100MB)을 다른 저장소로 복사하려면 스포츠 스트리밍이 더 잘 읽고 성능을 발휘하는 Data Flow Excel 원본을 사용할 수 있습니다.
    
## <a name="other-references"></a>기타 참조

다음은 지원되는 데이터 저장소에 대한 몇 가지 성능 모니터링 및 튜닝 참조입니다.

* Azure Blob Storage: [Blob Storage의 확장성 및 성능 목표](../storage/blobs/scalability-targets.md) 및 [Blob Storage의 확장성 검사 목록](../storage/blobs/storage-performance-checklist.md)입니다.
* Azure 테이블 스토리지: [테이블 스토리지의 확장성 및 성능 목표](../storage/tables/scalability-targets.md) 및 [테이블 스토리지의 확장성 검사 목록](../storage/tables/storage-performance-checklist.md)입니다.
* Azure SQL Database: [성능을 모니터링](../azure-sql/database/monitor-tune-overview.md)하고 DTU(데이터베이스 트랜잭션 단위) 비율을 확인할 수 있습니다.
* Azure Synapse Analytics: 해당 기능은 DWU(데이터 웨어하우스 단위)로 측정됩니다. [Azure Synapse Analytics의 계산 능력 관리(개요)](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-manage-compute-overview.md)를 참조하세요.
* Azure Cosmos DB: [Azure Cosmos DB의 성능 수준](../cosmos-db/performance-levels.md)입니다.
* SQL Server: [성능에 대한 모니터링 및 튜닝](/sql/relational-databases/performance/monitor-and-tune-for-performance)입니다.
* 온-프레미스 파일 서버: [파일 서버에 대한 성능 튜닝](/previous-versions//dn567661(v=vs.85))입니다.

## <a name="next-steps"></a>다음 단계
다른 복사 작업 문서를 참조하세요.

- [복사 작업 개요](copy-activity-overview.md)
- [복사 작업 성능 및 확장성 가이드](copy-activity-performance.md)
- [복사 작업 성능 최적화 기능](copy-activity-performance-features.md)
- [데이터 레이크 또는 데이터 웨어하우스에서 Azure로 데이터 마이그레이션](data-migration-guidance-overview.md)
- [Amazon S3에서 Azure Storage로 데이터 마이그레이션](data-migration-guidance-s3-azure-storage.md)

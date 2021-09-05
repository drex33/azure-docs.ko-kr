---
title: Gen1에서 Gen2로 Azure Data Lake Storage 마이그레이션
description: Azure Data Lake Storage Gen1을 Azure Blob Storage를 기반으로 하며 빅 데이터 분석 전용 기능 집합을 제공하는 Gen2로 마이그레이션합니다.
author: normesta
ms.topic: how-to
ms.author: normesta
ms.date: 07/13/2021
ms.service: storage
ms.reviewer: rukmani-msft
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: d6d173188c147e04a688ff7c373a1a253393ee87
ms.sourcegitcommit: abf31d2627316575e076e5f3445ce3259de32dac
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/15/2021
ms.locfileid: "114204519"
---
# <a name="migrate-azure-data-lake-storage-from-gen1-to-gen2"></a>Gen1에서 Gen2로 Azure Data Lake Storage 마이그레이션

데이터, 워크로드 및 애플리케이션을 Data Lake Storage Gen1에서 Data Lake Storage Gen2로 마이그레이션할 수 있습니다.

**2024년 2월 29일** Azure Data Lake Storage Gen1은 사용 중지됩니다. 자세한 내용은 [공식 공지](https://azure.microsoft.com/updates/action-required-switch-to-azure-data-lake-storage-gen2-by-29-february-2024/)를 참조하세요. Azure Data Lake Storage Gen1을 사용하는 경우 해당 날짜 이전에 Azure Data Lake Storage Gen2로 마이그레이션해야 합니다. 이 문서에서 이 작업을 수행하는 방법을 보여 줍니다.

Azure Data Lake Storage Gen2는 [Azure Blob Storage](storage-blobs-introduction.md)를 기반으로 하며, 빅 데이터 분석 전용 기능 집합을 제공합니다. [Data Lake Storage Gen2](https://azure.microsoft.com/services/storage/data-lake-storage/)는 파일 시스템 의미 체계, 디렉터리 및 파일 수준 보안 및 크기 조정과 같은 [Azure Data Lake Storage Gen1](../../data-lake-store/index.yml)의 기능을 [Azure Blob Storage](storage-blobs-introduction.md)의 낮은 비용, 계층화된 스토리지, 고가용성/재해 복구 기능과 결합합니다.

> [!NOTE]
> 이 문서에서는 가독성을 높이기 위해 Azure Data Lake Storage Gen1을 *Gen1* 이라고 칭하고, Azure Data Lake Storage Gen2를 *Gen2* 라고 칭합니다.

## <a name="recommended-approach"></a>권장 접근 방식

Gen2로 마이그레이션하려면 다음 방법을 사용하는 것이 좋습니다.

:heavy_check_mark: 1단계: 준비 상태 평가

:heavy_check_mark: 2단계: 마이그레이션 준비

:heavy_check_mark: 3단계: 데이터 및 애플리케이션 워크로드 마이그레이션

:heavy_check_mark: 4단계: Gen1에서 Gen2로 전환

> [!NOTE]
> Gen1와 Gen2는 서로 다른 서비스이며, 현재 위치 업그레이드 환경, 의도적인 마이그레이션 작업이 필요하지 않습니다. 

### <a name="step-1-assess-readiness"></a>1단계: 준비 상태 평가

1. [Data Lake Storage Gen2 제품](https://azure.microsoft.com/services/storage/data-lake-storage/)의 이점, 비용 및 일반 아키텍처에 대해 알아봅니다. 

2. Gen1과 Gen2의 [기능을 비교](#gen1-gen2-feature-comparison)합니다. 

3. [알려진 문제](data-lake-storage-known-issues.md) 목록을 검토하여 기능의 모든 차이점을 평가합니다.

4. Gen2는 [진단 로깅](../common/storage-analytics-logging.md), [액세스 계층](storage-blob-storage-tiers.md), [Blob Storage 수명 주기 관리 정책](storage-lifecycle-management-concepts.md)과 같은 Blob Storage 기능을 지원합니다. 이러한 기능을 사용하는 것에 관심이 있는 경우 [현재 지원 수준](./data-lake-storage-supported-blob-storage-features.md)을 검토하세요.

5. [Azure 에코시스템 지원](./data-lake-storage-multi-protocol-access.md)의 현재 상태를 검토하여 솔루션이 의존하는 모든 서비스를 Gen2에서 지원하는지 확인합니다.

### <a name="step-2-prepare-to-migrate"></a>2단계: 마이그레이션 준비

1. 마이그레이션할 데이터 세트를 식별합니다.

   이 기회를 통해 더 이상 사용하지 않는 데이터 세트를 정리합니다. 데이터를 한 번에 모두 마이그레이션하지 않으려는 경우 이 기회를 통해 단계별로 마이그레이션할 수 있는 데이터의 논리적 그룹을 식별합니다.
   
2. 마이그레이션이 비즈니스에 미치는 영향을 확인합니다.

   예를 들어 마이그레이션이 이루어지는 동안 가동 중지 시간을 감당할 수 있는지 여부를 고려합니다. 이러한 고려 사항은 적절한 마이그레이션 패턴을 식별하고 가장 적합한 도구를 선택하는 데 도움이 될 수 있습니다.

3. 마이그레이션 계획을 만듭니다. 

   이러한 [마이그레이션 패턴](#migration-patterns)을 권장합니다. 이러한 패턴 중 하나를 선택하거나, 함께 결합하거나, 사용자 지정 패턴을 직접 설계할 수 있습니다.

### <a name="step-3-migrate-data-workloads-and-applications"></a>3단계: 데이터, 워크로드 및 애플리케이션 마이그레이션

원하는 패턴을 사용하여 데이터, 워크로드 및 애플리케이션을 마이그레이션합니다. 시나리오를 증분 방식으로 검증하는 것이 좋습니다.

1. [스토리지 계정을 생성](create-data-lake-storage-account.md)하고 계층 구조 네임스페이스 기능을 사용하도록 설정합니다. 

2. 데이터를 마이그레이션합니다. 

3. Gen2 엔드포인트를 가리키도록 [워크로드의 서비스](./data-lake-storage-supported-azure-services.md)를 구성합니다. 
   
4. Gen2 API를 사용하도록 애플리케이션을 업데이트합니다. 다음 가이드를 참조하세요.

| 환경 | 아티클 |
|--------|-----------|
|Azure Storage Explorer |[Azure Storage Explorer를 사용하여 Azure Data Lake Storage Gen2에서 디렉터리 및 파일 관리](data-lake-storage-explorer.md)|
|.NET |[Azure Data Lake Storage Gen2에서 .NET을 사용하여 디렉터리 및 파일 관리](data-lake-storage-directory-file-acl-dotnet.md)|
|Java|[Azure Data Lake Storage Gen2에서 Java를 사용하여 디렉터리 및 파일 관리](data-lake-storage-directory-file-acl-java.md)|
|Python|[Python을 사용하여 Azure Data Lake Storage Gen2에서 디렉터리 및 파일 관리](data-lake-storage-directory-file-acl-python.md)|
|JavaScript(Node.js)|[Node.js에서 JavaScript SDK를 사용하여 Azure Data Lake Storage Gen2의 디렉터리 및 파일 관리](data-lake-storage-directory-file-acl-javascript.md)|
|REST API |[Azure Data Lake Store REST API](/rest/api/storageservices/data-lake-storage-gen2)|
   
5. Data Lake Storage Gen2 [PowerShell cmdlet](data-lake-storage-directory-file-acl-powershell.md) 및 [Azure CLI 명령](data-lake-storage-directory-file-acl-cli.md)을 사용하도록 스크립트를 업데이트합니다.
   
6. 코드 파일 또는 Databricks Notebook, Apache Hive HQL 파일 또는 워크로드의 일부로 사용되는 다른 모든 파일의 `adl://` 문자열을 포함하는 URI 참조를 검색합니다. 이러한 참조를 새 스토리지 계정의 [Gen2 형식 URI](data-lake-storage-introduction-abfs-uri.md)로 바꿉니다. 예: Gen1 URI: `adl://mydatalakestore.azuredatalakestore.net/mydirectory/myfile`이 `abfss://myfilesystem@mydatalakestore.dfs.core.windows.net/mydirectory/myfile`이 될 수 있습니다. 

7. [Azure 역할](assign-azure-role-data-access.md), [파일 및 폴더 수준 보안](data-lake-storage-access-control.md), [Azure Storage 방화벽 및 가상 네트워크](../common/storage-network-security.md)를 포함하도록 계정에 대한 보안을 구성합니다.

### <a name="step-4-cutover-from-gen1-to-gen2"></a>4단계: Gen1에서 Gen2로 전환

애플리케이션 및 워크로드가 Gen2에서 안정적으로 작동한다고 확신하는 경우, Gen2 사용을 시작하여 비즈니스 시나리오를 충족할 수 있습니다. Gen1에서 실행되는 나머지 파이프라인을 끄고 Gen1 계정을 해제합니다. 

<a id="gen1-gen2-feature-comparison"></a>

## <a name="gen1-vs-gen2-capabilities"></a>Gen1과 Gen2 기능 비교

이 표에서는 Gen1과 Gen2의 기능을 비교합니다.

|영역형 |1세대   |2세대 |
|---|---|---|
|데이터 구성|[계층 구조 네임스페이스](data-lake-storage-namespace.md)<br>파일 및 폴더 지원|[계층 구조 네임스페이스](data-lake-storage-namespace.md)<br>컨테이너, 파일 및 폴더 지원 |
|지리적 중복| [LRS](../common/storage-redundancy.md#locally-redundant-storage)| [LRS](../common/storage-redundancy.md#locally-redundant-storage), [ZRS](../common/storage-redundancy.md#zone-redundant-storage), [GRS](../common/storage-redundancy.md#geo-redundant-storage), [RA-GRS](../common/storage-redundancy.md#read-access-to-data-in-the-secondary-region) |
|인증|[AAD 관리 ID](../../active-directory/managed-identities-azure-resources/overview.md)<br>[서비스 주체](../../active-directory/develop/app-objects-and-service-principals.md)|[AAD 관리 ID](../../active-directory/managed-identities-azure-resources/overview.md)<br>[서비스 주체](../../active-directory/develop/app-objects-and-service-principals.md)<br>[공유 액세스 키](/rest/api/storageservices/authorize-with-shared-key)|
|권한 부여|관리 - [Azure RBAC](../../role-based-access-control/overview.md)<br>데이터 - [ACL](data-lake-storage-access-control.md)|관리 – [Azure RBAC](../../role-based-access-control/overview.md)<br>데이터 -  [ACL](data-lake-storage-access-control.md), [Azure RBAC](../../role-based-access-control/overview.md) |
|암호화 - 미사용 데이터|서버 쪽 – [Microsoft 관리형](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) 또는 [고객 관리형](../common/customer-managed-keys-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) 키 사용|서버 쪽 – [Microsoft 관리형](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) 또는 [고객 관리형](../common/customer-managed-keys-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) 키 사용|
|VNET 지원|[VNET 통합](../../data-lake-store/data-lake-store-network-security.md)|[서비스 엔드포인트](../common/storage-network-security.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json), [프라이빗 엔드포인트](../common/storage-private-endpoints.md)|
|개발자 환경|[REST](../../data-lake-store/data-lake-store-data-operations-rest-api.md), [.NET](../../data-lake-store/data-lake-store-data-operations-net-sdk.md), [Java](../../data-lake-store/data-lake-store-get-started-java-sdk.md), [Python](../../data-lake-store/data-lake-store-data-operations-python.md), [PowerShell](../../data-lake-store/data-lake-store-get-started-powershell.md), [Azure CLI](../../data-lake-store/data-lake-store-get-started-cli-2.0.md)|일반 공급 - [REST](/rest/api/storageservices/data-lake-storage-gen2), [.NET](data-lake-storage-directory-file-acl-dotnet.md), [Java](data-lake-storage-directory-file-acl-java.md), [Python](data-lake-storage-directory-file-acl-python.md)<br>공개 미리 보기 - [JavaScript](data-lake-storage-directory-file-acl-javascript.md), [PowerShell](data-lake-storage-directory-file-acl-powershell.md), [Azure CLI](data-lake-storage-directory-file-acl-cli.md)|
|리소스 로그|클래식 로그<br>[Azure Monitor 통합](../../data-lake-store/data-lake-store-diagnostic-logs.md)|[클래식 로그](../common/storage-analytics-logging.md) - 일반 공급<br>[Azure Monitor 통합](monitor-blob-storage.md) - 미리 보기|
|에코시스템|[HDInsight(3.6)](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md), [Azure Databricks(3.1 이상)](https://docs.databricks.com/data/data-sources/azure/azure-datalake.html), [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-load-from-azure-data-lake-store.md), [ADF](../../data-factory/load-azure-data-lake-store.md)|[HDInsight(3.6, 4.0)](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md), [Azure Databricks(5.1 이상)](/azure/databricks/data/data-sources/azure/azure-datalake-gen2), [Azure Synapse Analytics](../../azure-sql/database/vnet-service-endpoint-rule-overview.md), [ADF](../../data-factory/load-azure-data-lake-storage-gen2.md)|

<a id="migration-patterns"></a>

## <a name="gen1-to-gen2-patterns"></a>Gen1에서 Gen2로의 마이그레이션 패턴

마이그레이션 패턴을 선택한 후 필요에 따라 해당 패턴을 수정합니다.

|마이그레이션 패턴 | 세부 정보 |
|---|---|
|**리프트 앤 시프트**|가장 간단한 패턴입니다. 데이터 파이프라인이 가동 중지 시간을 감당할 수 있는 경우 이상적입니다.|
|**증분 복사**|*리프트 앤 시프트* 와 비슷하지만 가동 중지 시간이 더 짧습니다. 복사하는 데 시간이 오래 걸리는 대량의 데이터에 이상적입니다.|
|**이중 파이프라인**|가동 중지 시간을 감당할 수 없는 파이프라인에 이상적입니다.|
|**양방향 동기화**|*이중 파이프라인* 과 유사하지만 더 점진적 접근법을 취하므로 좀 더 복잡한 파이프라인에 적합합니다.|

각 패턴을 더 자세히 살펴보겠습니다.
 
### <a name="lift-and-shift-pattern"></a>리프트 앤 시프트 패턴

가장 간단한 패턴입니다. 

1. Gen1에 대한 모든 쓰기를 중지합니다.

2. Gen1에서 Gen2로 데이터를 이동합니다. [Azure Data Factory](../../data-factory/connector-azure-data-lake-storage.md) 또는 [Azure Portal](data-lake-storage-migrate-gen1-to-gen2-azure-portal.md)을 사용하는 것이 좋습니다. ACL이 데이터와 함께 복사됩니다.

3. 수집 작업 및 워크로드를 Gen2로 전환합니다.

4. Gen1을 해제합니다.

[리프트 앤 시프트 마이그레이션 샘플](https://github.com/rukmani-msft/adlsgen1togen2migrationsamples/blob/master/src/Lift%20and%20Shift/README.md)에서 리프트 앤 시프트 패턴에 대한 샘플 코드를 확인하세요.

> [!div class="mx-imgBorder"]
> ![리프트 앤 시프트 패턴](./media/data-lake-storage-migrate-gen1-to-gen2/lift-and-shift.png)

#### <a name="considerations-for-using-the-lift-and-shift-pattern"></a>리프트 앤 시프트 패턴 사용 시 고려 사항

:heavy_check_mark: 모든 워크로드에서 동시에 Gen1에서 Gen2로 전환합니다.

:heavy_check_mark: 마이그레이션 및 전환 기간 동안 가동 중지 시간이 발생합니다.

:heavy_check_mark: 가동 중지 시간을 감당할 수 있는 파이프라인에 이상적이며 모든 앱을 한 번에 업그레이드할 수 있습니다.

> [!TIP]
> [Azure Portal](data-lake-storage-migrate-gen1-to-gen2-azure-portal.md)을 사용하여 가동 중지 시간을 줄이고 마이그레이션을 완료하는 데 필요한 단계 수를 줄이는 것이 좋습니다.

### <a name="incremental-copy-pattern"></a>증분 복사 패턴

1. Gen1에서 Gen2로 데이터 이동을 시작합니다. [Azure Data Factory](../../data-factory/connector-azure-data-lake-storage.md)를 권장합니다. ACL이 데이터와 함께 복사됩니다.

2. Gen1의 새 데이터를 증분 방식으로 복사합니다.

3. 모든 데이터가 복사되면 Gen1에 대한 모든 쓰기를 중지하고 워크로드를 Gen2로 전환합니다.

4. Gen1을 해제합니다.

[증분 복사 마이그레이션 샘플](https://github.com/rukmani-msft/adlsgen1togen2migrationsamples/blob/master/src/Incremental/README.md)에서 증분 복사 패턴에 대한 샘플 코드를 확인하세요.


> [!div class="mx-imgBorder"]
> ![증분 복사 패턴](./media/data-lake-storage-migrate-gen1-to-gen2/incremental-copy.png)

#### <a name="considerations-for-using-the-incremental-copy-pattern"></a>증분 복사 패턴 사용 시 고려 사항:

:heavy_check_mark: 모든 워크로드에서 동시에 Gen1에서 Gen2로 전환합니다.

:heavy_check_mark: 전환 기간 동안에만 가동 중단 시간이 발생합니다.

:heavy_check_mark: 모든 앱이 한 번에 업그레이드되지만 데이터 복사에 시간이 더 필요한 파이프라인에 이상적입니다.

### <a name="dual-pipeline-pattern"></a>이중 파이프라인 패턴

1. Gen1에서 Gen2로 데이터를 이동합니다. [Azure Data Factory](../../data-factory/connector-azure-data-lake-storage.md)를 권장합니다. ACL이 데이터와 함께 복사됩니다.

2. Gen1 및 Gen2 둘 다에 새 데이터를 수집합니다.

3. 워크로드를 Gen2로 전환합니다.

4. Gen1에 대한 모든 쓰기를 중지한 다음, Gen1을 해제합니다.

[이중 파이프라인 마이그레이션 샘플](https://github.com/rukmani-msft/adlsgen1togen2migrationsamples/blob/master/src/Dual%20pipeline/README.md)에서 이중 파이프라인 패턴에 대한 샘플 코드를 확인하세요.

> [!div class="mx-imgBorder"]
> ![이중 파이프라인 패턴](./media/data-lake-storage-migrate-gen1-to-gen2/dual-pipeline.png)

#### <a name="considerations-for-using-the-dual-pipeline-pattern"></a>이중 파이프라인 패턴 사용 시 고려 사항:

:heavy_check_mark: Gen1 및 Gen2 파이프라인이 side-by-side 방식으로 실행됩니다.

:heavy_check_mark: 제로 가동 중지 시간을 지원합니다.

:heavy_check_mark: 워크로드 및 애플리케이션에서 가동 중지 시간을 감당할 수 없고 두 스토리지 계정에 모두 수집할 수 있는 경우에 이상적입니다.

### <a name="bi-directional-sync-pattern"></a>양방향 동기화 패턴

1. Gen1과 Gen2 간에 양방향 복제를 설정합니다. [WanDisco](https://docs.wandisco.com/bigdata/wdfusion/adls/)를 권장합니다. 기존 데이터에 대한 복구 기능을 제공합니다.

3. 모든 이동이 완료되면 Gen1에 대한 모든 쓰기가 중지되고 양방향 복제가 해제됩니다.

4. Gen1을 해제합니다.

[양방향 동기화 마이그레이션 샘플](https://github.com/rukmani-msft/adlsgen1togen2migrationsamples/blob/master/src/Bi-directional/README.md)에서 양방향 동기화 패턴에 대한 샘플 코드를 확인하세요.

> [!div class="mx-imgBorder"]
> ![양방향 패턴](./media/data-lake-storage-migrate-gen1-to-gen2/bidirectional-sync.png)

#### <a name="considerations-for-using-the-bi-directional-sync-pattern"></a>양방향 동기화 패턴 사용 시 고려 사항:

:heavy_check_mark: 단계별 접근법이 더 적합할 수 있는 많은 수의 파이프라인 및 종속성을 포함하는 복잡한 시나리오에 이상적입니다.  

:heavy_check_mark: 마이그레이션에 많은 노력이 들지만 Gen1 및 Gen2에 대한 side-by-side 지원을 제공합니다.

## <a name="next-steps"></a>다음 단계

- 스토리지 계정에 대한 보안 설정의 다양한 부분에 대해 알아봅니다. [Azure Storage 보안 가이드](./security-recommendations.md)를 참조하세요.
- Data Lake Store의 성능을 최적화합니다. [Azure Data Lake Storage Gen2의 성능 최적화](data-lake-storage-performance-tuning-guidance.md) 참조
- Data Lake Store 관리에 대한 모범 사례를 검토합니다. [Azure Data Lake Storage Gen2 사용에 대한 모범 사례](data-lake-storage-best-practices.md) 참조
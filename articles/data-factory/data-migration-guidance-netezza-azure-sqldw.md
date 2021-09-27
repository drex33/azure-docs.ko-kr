---
title: 온-프레미스 Netezza 서버에서 Azure로 데이터 마이그레이션
description: Azure Data Factory를 사용하여 온-프레미스 Netezza 서버에서 Azure로 데이터를 마이그레이션합니다.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/09/2020
ms.openlocfilehash: d05c9bbf5c3ae632bef402a354690326f60cdd62
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124730837"
---
# <a name="use-azure-data-factory-to-migrate-data-from-an-on-premises-netezza-server-to-azure"></a>Azure Data Factory를 사용하여 온-프레미스 Netezza 서버에서 Azure로 데이터 마이그레이션 

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory는 온-프레미스 Netezza 서버에서 Azure Storage 계정 또는 Azure Synapse Analytics 데이터베이스로 대규모 데이터를 마이그레이션할 수 있는 강력하고 비용 효율적인 고성능 메커니즘을 제공합니다. 

이 문서에서는 데이터 엔지니어 및 개발자를 위한 다음 정보를 제공합니다.

> [!div class="checklist"]
> * 성능 
> * 복사 복원력
> * 네트워크 보안
> * 개괄적인 솔루션 아키텍처 
> * 구현 모범 사례  

## <a name="performance"></a>성능

Azure Data Factory는 다양한 수준에서 병렬 처리를 허용하는 서버리스 아키텍처를 제공합니다. 개발자의 경우 네트워크 및 데이터베이스 대역폭을 모두 사용하는 파이프라인을 빌드하여 환경에 대한 데이터 이동 처리량을 최대화할 수 있습니다.

:::image type="content" source="media/data-migration-guidance-netezza-azure-sqldw/performance.png" alt-text="성능 다이어그램":::

위의 다이어그램을 다음과 같이 해석할 수 있습니다.

- 단일 복사 작업은 확장 가능한 컴퓨팅 리소스를 활용할 수 있습니다. Azure Integration Runtime을 사용하는 경우 서버리스 방식으로 각 복사 작업에 대해 [최대 256개의 DIU](./copy-activity-performance.md#data-integration-units)를 지정할 수 있습니다. 자체 호스팅 IR(통합 런타임)을 사용하면 컴퓨터를 수동으로 스케일 업하거나 여러 컴퓨터([최대 노드 4개](./create-self-hosted-integration-runtime.md#high-availability-and-scalability))로 스케일 아웃할 수 있으며 단일 복사 작업은 모든 노드에 해당 파티션을 분산합니다. 

- 단일 복사 작업은 여러 스레드를 사용하여 데이터 저장소에서 읽고 씁니다. 

- Azure Data Factory 제어 흐름은 동시에 여러 복사 작업을 시작할 수 있습니다. 예를 들어 [For Each 루프](./control-flow-for-each-activity.md)를 사용하여 시작할 수 있습니다. 

자세한 내용은 [복사 작업 성능 및 확장성 가이드](./copy-activity-performance.md)를 참조하세요.

## <a name="resilience"></a>복원력

단일 복사 작업 실행 내에서 Azure Data Factory에는 기본 제공 재시도 메커니즘이 있으므로 데이터 저장소나 기본 네트워크에서 특정 수준의 일시적 오류를 처리할 수 있습니다.

Azure Data Factory 복사 작업을 사용하면 원본 및 싱크 데이터 저장소 간에 데이터를 복사할 때 2가지 방법으로 호환되지 않는 행을 처리할 수 있습니다. 복사 작업을 중단하고 실패하거나 호환되지 않는 데이터 행을 건너뛰어 나머지 데이터를 계속 복사할 수 있습니다. 또한 실패의 원인을 알아보기 위해 Azure Blob Storage 또는 Azure Data Lake Store에 호환되지 않는 행을 기록하고 데이터 원본의 데이터를 수정하고 복사 작업을 다시 시도할 수 있습니다.

## <a name="network-security"></a>네트워크 보안 

기본적으로 Azure Data Factory는 HTTPS(Hypertext Transfer Protocol Secure)를 통해 암호화된 연결을 사용하여 온-프레미스 Netezza 서버에서 Azure Storage 계정 또는 Azure Synapse Analytics 데이터베이스로 데이터를 전송합니다. HTTPS는 전송 중인 데이터의 암호화를 제공하고, 도청 및 메시지 가로채기(man-in-the-middle) 공격을 방지합니다.

또는 공용 인터넷을 통해 데이터를 전송하지 않으려는 경우 Azure Express 경로로 개인 피어링 링크를 통해 데이터를 전송하여 보안을 강화할 수 있습니다. 

다음 섹션에서는 보안을 강화하는 방법을 설명합니다.

## <a name="solution-architecture"></a>솔루션 아키텍처

이 섹션에서는 데이터를 마이그레이션하는 두 가지 방법을 설명합니다.

### <a name="migrate-data-over-the-public-internet"></a>공용 인터넷을 통해 데이터 마이그레이션

:::image type="content" source="media/data-migration-guidance-netezza-azure-sqldw/solution-architecture-public-network.png" alt-text="공용 인터넷을 통해 데이터 마이그레이션":::

위의 다이어그램을 다음과 같이 해석할 수 있습니다.

- 이 아키텍처에서 데이터는 공용 인터넷을 통해 HTTPS를 사용하여 안전하게 전송됩니다.

- 이 아키텍처를 구현하려면 회사 방화벽 뒤에 있는 Windows 컴퓨터에 Azure Data Factory 통합 런타임(자체 호스팅)을 설치해야 합니다. 이 통합 런타임에서 Netezza 서버에 직접 액세스할 수 있는지 확인합니다. 네트워크 및 데이터 저장소 대역폭을 완전히 사용하여 데이터를 복사하려면 수동으로 컴퓨터를 스케일 업하거나 여러 컴퓨터로 스케일 아웃할 수 있습니다.

- 이 아키텍처를 사용하여 초기 스냅샷 데이터와 델타 데이터를 모두 마이그레이션할 수 있습니다.

### <a name="migrate-data-over-a-private-network"></a>개인 네트워크를 통해 데이터 마이그레이션 

:::image type="content" source="media/data-migration-guidance-netezza-azure-sqldw/solution-architecture-private-network.png" alt-text="개인 네트워크를 통해 데이터 마이그레이션":::

위의 다이어그램을 다음과 같이 해석할 수 있습니다.

- 이 아키텍처에서는 Azure Express Route로 개인 피어링 링크를 통해 데이터를 마이그레이션하며 데이터가 공용 인터넷을 통과하지 않습니다. 

- 이 아키텍처를 구현하려면 Azure 가상 네트워크 내의 Windows VM(가상 머신)에 Azure Data Factory 통합 런타임(자체 호스팅)을 설치해야 합니다. 네트워크 및 데이터 저장소 대역폭을 완전히 사용하여 데이터를 복사하려면 수동으로 VM을 스케일 업하거나 여러 VM으로 스케일 아웃할 수 있습니다.

- 이 아키텍처를 사용하여 초기 스냅샷 데이터와 델타 데이터를 모두 마이그레이션할 수 있습니다.

## <a name="implement-best-practices"></a>모범 사례 구현 

### <a name="manage-authentication-and-credentials"></a>인증 및 자격 증명 관리 

- Netezza에 인증하기 위해 [연결 문자열을 통해 ODBC 인증](./connector-netezza.md#linked-service-properties)을 사용할 수 있습니다. 

- Azure Blob Storage에 인증하려면 다음을 수행합니다. 

   - [Azure 리소스에 대한 관리 ID](./connector-azure-blob-storage.md#managed-identity)를 사용하는 것이 좋습니다. Azure AD(Azure Active Directory)에서 자동으로 관리되는 Azure Data Factory ID를 기반으로 빌드된 관리 ID를 사용하면 연결된 서비스 정의에서 자격 증명을 제공할 필요 없이 파이프라인을 구성할 수 있습니다.  

   - 또는 [서비스 주체](./connector-azure-blob-storage.md#service-principal-authentication), [공유 액세스 서명](./connector-azure-blob-storage.md#shared-access-signature-authentication) 또는 [스토리지 계정 키](./connector-azure-blob-storage.md#account-key-authentication)를 사용하여 Azure Blob Storage에 인증할 수 있습니다. 

- Azure Data Lake Storage Gen2에 인증하려면 다음을 수행합니다. 

   - [Azure 리소스에 대한 관리 ID](./connector-azure-data-lake-storage.md#managed-identity)를 사용하는 것이 좋습니다.
   
   - [서비스 주체](./connector-azure-data-lake-storage.md#service-principal-authentication) 또는 [저장소 계정 키](./connector-azure-data-lake-storage.md#account-key-authentication)를 사용할 수도 있습니다. 

- Azure Synapse Analytics에 인증하려면 다음을 수행합니다.

   - [Azure 리소스에 대한 관리 ID](./connector-azure-sql-data-warehouse.md#managed-identity)를 사용하는 것이 좋습니다.
   
   - [서비스 주체](./connector-azure-sql-data-warehouse.md#service-principal-authentication) 또는 [SQL 인증](./connector-azure-sql-data-warehouse.md#sql-authentication)을 사용할 수도 있습니다.

- Azure 리소스에 대해 관리 ID를 사용하지 않는 경우 Azure Data Factory 연결된 서비스를 수정할 필요 없이 보다 쉽게 키를 중앙에서 관리하고 순환하기 위해 [Azure Key Vault에 자격 증명을 저장](./store-credentials-in-key-vault.md)하는 것이 좋습니다. [CI/CD에 대한 모범 사례](./continuous-integration-deployment.md#best-practices-for-cicd) 중 하나이기도 합니다. 

### <a name="migrate-initial-snapshot-data"></a>초기 스냅샷 데이터 마이그레이션 

작은 테이블(즉, 볼륨이 100GB 미만이거나 2시간 이내에 Azure로 마이그레이션할 수 있는 테이블)의 경우 각 복사 작업에서 테이블당 데이터를 로드하도록 할 수 있습니다. 처리량을 높이기 위해 여러 Azure Data Factory 복사 작업을 실행하여 별도의 테이블을 동시에 로드할 수 있습니다. 

각 복사 작업 내에서 병렬 쿼리를 실행하고 파티션별로 데이터를 복사하려면 다음 데이터 파티션 옵션 중 하나와 함께 [`parallelCopies` 속성 설정](./copy-activity-performance.md#parallel-copy)을 사용하여 일정 수준의 병렬 처리에 도달할 수도 있습니다.

- 효율성을 높이려면 데이터 조각에서 시작하는 것이 좋습니다.  `parallelCopies` 설정의 값이 Netezza 서버의 테이블에 있는 총 데이터 조각 파티션 수보다 작은지 확인합니다.  

- 각 데이터 조각 파티션의 볼륨이 여전히 큰 경우(예: 10GB 이상) 동적 범위 파티션으로 전환하는 것이 좋습니다. 이 옵션을 사용하면 파티션 열, 상한 및 하한별로 파티션 수와 각 파티션의 볼륨을 더 유연하게 정의할 수 있습니다.

큰 테이블(즉, 볼륨이 100GB 이상이거나 2시간 이내에 Azure로 마이그레이션할 수 *없는* 테이블)의 경우 사용자 지정 쿼리를 통해 데이터를 분할한 다음, 각 복사 작업에서 한 번에 하나의 파티션을 복사하도록 하는 것이 좋습니다. 처리량 향상을 위해 여러 Azure Data Factory 복사 작업을 동시에 실행할 수 있습니다. 사용자 지정 쿼리로 하나의 파티션을 로드하는 각 복사 작업 대상을 위해 데이터 조각 또는 동적 범위로 병렬 처리를 활성화하여 처리량을 늘릴 수 있습니다. 

네트워크 또는 데이터 저장소의 일시적 문제로 인해 복사 작업이 실패하는 경우 실패한 복사 작업을 다시 실행하여 테이블에서 특정 파티션을 다시 로드할 수 있습니다. 다른 파티션을 로드하는 다른 복사 작업은 영향을 받지 않습니다.

Azure Synapse Analytics 데이터베이스에 데이터를 로드할 때 Azure Blob Storage를 준비로 사용하여 복사 작업 내에서 PolyBase를 사용하도록 설정하는 것이 좋습니다.

### <a name="migrate-delta-data"></a>델타 데이터 마이그레이션 

테이블에서 새 행이나 업데이트된 행을 식별하려면 스키마 내에서 타임스탬프 열이나 증분 키를 사용합니다. 그런 다음, 최신 값을 외부 테이블에 상위 워터마크로 저장한 다음, 이를 사용하여 다음에 데이터를 로드할 때 델타 데이터를 필터링할 수 있습니다. 

테이블마다 다른 워터마크 열을 사용하여 새 행이나 업데이트된 행을 식별할 수 있습니다. 외부 컨트롤 테이블을 만드는 것이 좋습니다. 테이블에서 각 행은 특정 워터마크 열 이름과 상위 워터마크 값을 사용하여 Netezza 서버의 한 테이블을 나타냅니다. 

### <a name="configure-a-self-hosted-integration-runtime"></a>자체 호스팅 통합 런타임 구성

Netezza 서버에서 Azure로 데이터를 마이그레이션하는 경우 서버가 회사 방화벽 뒤의 온-프레미스에 있는지 가상 네트워크 환경 내에 있는지에 관계없이 데이터 이동에 사용되는 엔진인 자체 호스팅 IR을 Windows 컴퓨터 또는 VM에 설치해야 합니다. 자체 호스팅 IR을 설치하는 경우 다음과 같은 방법을 사용하는 것이 좋습니다.

- 각 Windows 컴퓨터 또는 VM에 대해 vCPU 32개 및 128GB 메모리 구성으로 시작합니다. 데이터 마이그레이션 중에 IR 컴퓨터의 CPU 및 메모리 사용량을 계속 모니터링하여 더 나은 성능을 위해 컴퓨터를 스케일 업하거나 비용을 절약하기 위해 컴퓨터를 스케일 다운해야 하는지 확인할 수 있습니다.

- 단일 자체 호스팅 IR을 통해 최대 4개의 VM 노드를 연결하여 스케일 아웃할 수도 있습니다. 자체 호스팅 IR에 대해 실행되는 단일 복사 작업은 모든 VM 노드를 자동으로 적용하여 데이터를 병렬로 복사합니다. 고가용성을 위해 2개의 VM 노드로 시작하여 데이터 마이그레이션 중 단일 실패 지점을 방지합니다.

### <a name="limit-your-partitions"></a>파티션 제한

모범 사례로, 대표적인 샘플 데이터 세트를 사용하여 성능 POC(개념 증명)를 수행함으로써 각 복사 작업에 적절한 파티션 크기를 결정할 수 있습니다. 2시간 내에 각 파티션을 Azure로 로드하는 것이 좋습니다.  

테이블을 복사하려면 단일 자체 호스팅 IR 컴퓨터를 사용하는 단일 복사 작업으로 시작합니다. 테이블의 데이터 조각 파티션 수에 따라 `parallelCopies` 설정을 점차 늘립니다. 복사 작업에서 발생하는 처리량에 따라 2시간 내에 전체 테이블을 Azure에 로드할 수 있는지 여부를 확인합니다. 

2시간 내에 Azure에 로드할 수 없고 자체 호스팅 IR 노드와 데이터 저장소의 용량이 완전히 사용되지 않는 경우 네트워크 제한 또는 데이터 저장소의 대역폭 제한에 도달할 때까지 동시 복사 작업 수를 점차 늘립니다. 

자체 호스팅 IR 시스템의 CPU 및 메모리 사용량을 계속 모니터링하고 CPU와 메모리가 완전히 사용되는 것이 확인되면 컴퓨터를 스케일 업하거나 여러 시스템으로 스케일 아웃할 준비를 합니다. 

Azure Data Factory 복사 작업에서 보고된 대로 제한 오류가 발생하면 Azure Data Factory에서 동시성 또는 `parallelCopies` 설정을 줄이거나 네트워크 및 데이터 저장소의 대역폭 또는 IOPS(초당 I/O 작업) 제한을 늘리는 것을 고려합니다. 


### <a name="estimate-your-pricing"></a>가격 책정 예측 

온-프레미스 Netezza 서버에서 Azure Synapse Analytics 데이터베이스로 데이터를 마이그레이션하기 위해 생성되는 다음 파이프라인을 고려합니다.

:::image type="content" source="media/data-migration-guidance-netezza-azure-sqldw/pricing-pipeline.png" alt-text="가격 책정 파이프라인":::

다음 내용이 참이라고 가정해 보겠습니다. 

- 총 데이터 볼륨은 50TB(테라바이트)입니다. 

- 첫 번째 솔루션 아키텍처를 사용하여 데이터를 마이그레이션하고 있습니다(Netezza 서버는 방화벽 뒤의 온-프레미스임).

- 50TB 볼륨이 500개의 파티션으로 나뉘며 각 복사 작업은 하나의 파티션을 이동합니다.

- 각 복사 작업은 4대의 컴퓨터에 대해 하나의 자체 호스팅 IR로 구성되며 20MBps(초당 메가바이트)의 처리량을 달성합니다. 복사 작업 내에서 `parallelCopies`는 4로 설정되고 테이블에서 데이터를 로드하는 각 스레드는 5MBps의 처리량을 달성합니다.

- ForEach 동시성은 3으로 설정되고 집계 처리량은 60MBps입니다.

- 마이그레이션을 완료하는 데 총 243시간이 소요됩니다.

위의 가정을 기반으로 한 예상 가격은 다음과 같습니다. 

:::image type="content" source="media/data-migration-guidance-netezza-azure-sqldw/pricing-table.png" alt-text="가격 책정 표":::

> [!NOTE]
> 위의 표에 표시된 가격은 가상입니다. 실제 가격은 사용자 환경의 실제 처리량에 따라 달라집니다. 자체 호스팅 IR이 설치된 Windows 컴퓨터의 가격은 포함되지 않습니다. 

### <a name="additional-references"></a>추가 참조

자세한 내용은 다음 문서와 가이드를 참조하세요.

- [Azure Data Factory를 사용하여 온-프레미스 관계형 데이터웨어 하우스 데이터베이스에서 Azure로 데이터 마이그레이션](https://azure.microsoft.com/resources/data-migration-from-on-premise-relational-data-warehouse-to-azure-data-lake-using-azure-data-factory/)
- [Netezza 커넥터](./connector-netezza.md)
- [ODBC 커넥터](./connector-odbc.md)
- [Azure Blob Storage 커넥터](./connector-azure-blob-storage.md)
- [Azure Data Lake Storage Gen2 커넥터](./connector-azure-data-lake-storage.md)
- [Azure Synapse Analytics 커넥터](./connector-azure-sql-data-warehouse.md)
- [복사 작업 성능 조정 가이드](./copy-activity-performance.md)
- [자체 호스팅 통합 런타임 만들기 및 구성](./create-self-hosted-integration-runtime.md)
- [자체 호스팅 Integration Runtime HA 및 확장성](./create-self-hosted-integration-runtime.md#high-availability-and-scalability)
- [데이터 이동 보안 고려 사항](./data-movement-security-considerations.md)
- [Azure Key Vault에 자격 증명 저장](./store-credentials-in-key-vault.md).
- [한 테이블에서 증분 방식으로 데이터 복사](./tutorial-incremental-copy-portal.md)
- [여러 테이블에서 증분 방식으로 데이터 복사](./tutorial-incremental-copy-multiple-tables-portal.md)
- [Azure Data Factory 가격 책정 페이지](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)

## <a name="next-steps"></a>다음 단계

- [Azure Data Factory를 사용하여 여러 컨테이너에서 파일 복사](solution-template-copy-files-multiple-containers.md)
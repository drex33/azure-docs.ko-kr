---
title: 통합 런타임
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 및 Azure Synapse Analytics의 통합 런타임에 대해 알아봅니다.
ms.author: lle
author: lrtoyou1223
ms.service: data-factory
ms.subservice: integration-runtime
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: 7e15c5e315d1bdc81307dcbd0242c1e7d00bdcf2
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133487761"
---
# <a name="integration-runtime-in-azure-data-factory"></a>Azure Data Factory의 통합 런타임 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

IR(Integration Runtime)은 서로 다른 네트워크 환경 간에 다음과 같은 데이터 통합 기능을 제공하기 위해 Azure Data Factory 및 Azure Synapse 파이프라인에서 사용하는 컴퓨팅 인프라입니다.

- **데이터 Flow**: 관리 되는 Azure 계산 환경에서 [데이터 Flow](concepts-data-flow-overview.md) 를 실행 합니다.  
- **데이터 이동**: 공용 또는 개인 네트워크 (온-프레미스 또는 가상 사설망)의 데이터 저장소 간에 데이터를 복사 합니다. 이 서비스는 기본 제공 커넥터, 형식 변환, 열 매핑, 성능과 확장성이 뛰어난 데이터 전송에 대 한 지원을 제공 합니다.
- **작업 디스패치**: Azure Databricks, Azure HDInsight, ML Studio(클래식), Azure SQL Database, SQL Server 등 다양한 컴퓨팅 서비스에서 실행되는 변환 작업을 디스패치 및 모니터링합니다.
- **SSIS 패키지 실행**: SSIS(SQL Server 통합 서비스) 패키지를 관리되는 Azure 컴퓨팅 환경에서 고유하게 실행합니다.

Data Factory 및 Synapse 파이프라인에서 작업은 수행할 작업을 정의합니다. 연결된 서비스는 대상 데이터 저장소 또는 컴퓨팅 서비스를 정의합니다. 통합 런타임은 작업과 연결 된 서비스 간의 브리지를 제공 합니다.  이 파일은 연결 된 서비스 또는 활동에서 참조 되며 활동이 직접 실행 되거나 디스패치 된 계산 환경을 제공 합니다. 이렇게 하면 보안 및 규정 준수 요구 사항을 충족 하는 유연성을 허용 하면서 성능을 최대화 하기 위해 대상 데이터 저장소 또는 계산 서비스에 가능한 가장 가까운 지역에서 작업을 수행할 수 있습니다.

Integration runtime은 [관리 허브](author-management-hub.md) 를 통해 직접 Azure Data Factory 및 AZURE Synapse UI에서 만들 수 있으며,이를 참조 하는 작업, 데이터 집합 또는 데이터 흐름에서 직접 만들 수 있습니다.

## <a name="integration-runtime-types"></a>통합 런타임 유형

Data Factory는 세 가지 유형의 IR (Integration Runtime)을 제공 하며, 데이터 통합 기능 및 네트워크 환경 요구 사항에 가장 적합 한 유형을 선택 해야 합니다.  IR의 세 가지 유형은 다음과 같습니다.

- Azure
- 자체 호스팅
- Azure-SSIS

> [!NOTE]
> Synapse 파이프라인은 현재 Azure 또는 자체 호스팅 통합 런타임만 지원합니다.

다음 테이블은 각 통합 런타임 유형에 대한 기능 및 네트워크 지원을 설명합니다.

IR 유형 | 공용 네트워크 | 프라이빗 네트워크 
------- | -------------- | ---------------
Azure | 데이터 흐름<br/>데이터 이동<br/>작업 디스패치 | 데이터 흐름<br/>데이터 이동<br/>작업 디스패치
자체 호스팅 | 데이터 이동<br/>작업 디스패치 | 데이터 이동<br/>작업 디스패치
Azure-SSIS | SSIS 패키지 실행 | SSIS 패키지 실행


## <a name="azure-integration-runtime"></a>Azure 통합 런타임

Azure 통합 런타임은 다음을 수행할 수 있습니다.

- Azure에서 데이터 흐름 실행 
- 클라우드 데이터 저장소 간에 복사 작업 실행
- 공용 네트워크에서 Databricks 노트북/Jar/Python 활동, hdinsight Hive 활동, hdinsight Pig 활동, hdinsight MapReduce 활동, hdinsight Spark 활동, hdinsight 스트리밍 활동, ML Studio (클래식) Batch 실행 활동, ML Studio (클래식) 업데이트 리소스 활동, 저장 프로시저 활동 Data Lake Analytics U-SQL를 전송 합니다.  활동, .NET 사용자 지정 활동, 웹 활동, 조회 작업 및 메타 데이터 가져오기 작업을 제공 합니다.

### <a name="azure-ir-network-environment"></a>Azure IR 네트워크 환경

Azure 통합 런타임은 공개 액세스 가능한 엔드포인트가 있는 데이터 소스에 연결 및 컴퓨팅 서비스를 지원합니다. 관리형 Virtual Network를 사용하도록 설정하면 Azure Integration Runtime에서 프라이빗 네트워크 환경에서 프라이빗 링크 서비스를 사용하여 데이터 저장소에 연결할 수 있습니다.

### <a name="azure-ir-compute-resource-and-scaling"></a>Azure IR 컴퓨팅 리소스 및 크기 조정
Azure 통합 런타임은 Azure에서 완전히 관리되고, 서버리스 컴퓨팅을 제공합니다.  인프라 프로비전, 소프트웨어 설치, 패치 또는 용량 크기 조정을 걱정할 필요가 없습니다.  또한 실제 사용 기간에 대해서만 지불합니다.

Azure 통합 런타임은 안전하고 안정적이고 고성능의 방법으로 클라우드 데이터 저장소 간에 데이터를 이동하는 고유 컴퓨팅을 제공합니다.  복사 작업에 사용할 데이터 통합 단위 수를 설정할 수 있으며, Azure IR의 계산 크기는 Azure Integration Runtime 크기를 명시적으로 조정 하지 않아도 적절 하 게 조정 됩니다. 

작업 디스패치는 작업을 대상 컴퓨팅 서비스에 경로 설정하는 간단한 작업이므로 이 시나리오를 위해 컴퓨팅 크기를 확장할 필요가 없습니다.

Azure IR 만들기 및 구성에 대한 자세한 내용은 [Azure IR 만들기 및 구성 방법](create-azure-integration-runtime.md)을 참조하세요. 

> [!NOTE] 
> Azure Integration runtime에는 데이터 흐름을 실행 하는 데 사용 되는 기본 계산 인프라를 정의 하는 데이터 Flow 런타임과 관련 된 속성이 있습니다. 

## <a name="self-hosted-integration-runtime"></a>자체 호스팅 통합 런타임

자체 호스팅 IR로 다음을 수행할 수 있습니다.

- 클라우드 데이터 저장소와 프라이빗 네트워크의 데이터 저장소 간에 복사 작업을 실행합니다.
- 온-프레미스 또는 Azure Virtual Network의 컴퓨팅 리소스에 대해 다음과 같은 변환 작업 디스패치: HDInsight Hive 작업(BYOC(Bring Your Own Cluster)), HDInsight Pig 작업(BYOC), HDInsight MapReduce 작업(BYOC), HDInsight Spark 작업(BYOC), HDInsight Streaming 작업(BYOC), ML Studio(클래식) Batch Execution 작업, ML Studio(클래식) 업데이트 리소스 작업, 저장 프로시저 작업, Data Lake Analytics U-SQL 작업, 사용자 지정 작업(Azure Batch에서 실행), 조회 작업, 메타데이터 가져오기 작업

> [!NOTE] 
> 자체 호스팅 integration runtime을 사용 하 여 사용자 고유의 드라이버 (예: SAP Hana, MySQL 등)를 필요로 하는 데이터 저장소를 지원 합니다.  자세한 내용은 [지원 되는 데이터 저장소](copy-activity-overview.md#supported-data-stores-and-formats)를 참조 하세요.

> [!NOTE] 
> JRE (Java Runtime Environment)는 자체 호스트 IR의 종속성입니다. JRE가 동일한 호스트에 설치 되어 있는지 확인 하세요.

### <a name="self-hosted-ir-network-environment"></a>자체 호스팅 IR 네트워크 환경

공용 클라우드 환경에서 직접적으로 보이지 않는 개인 네트워크 환경에서 데이터 통합을 안전 하 게 수행 하려는 경우 방화벽 뒤에 있는 온-프레미스 환경 또는 가상 개인 네트워크 내에서 자체 호스팅 IR을 설치할 수 있습니다.  자체 호스팅 통합 런타임을 사용 하는 경우에만 인터넷에 아웃 바운드 HTTP 기반 연결이 설정 됩니다.

### <a name="self-hosted-ir-compute-resource-and-scaling"></a>자체 호스팅 IR 컴퓨팅 리소스 및 크기 조정

온-프레미스 컴퓨터 또는 개인 네트워크 내의 가상 컴퓨터에 자체 호스팅 IR을 설치 합니다. 현재 자체 호스팅 IR은 Windows 운영 체제 에서만 지원 됩니다.  
고가용성과 확장성을 위해 논리 인스턴스를 활성-활성 모드의 여러 온-프레미스 컴퓨터와 연결하여 자체 호스팅 IR을 확장할 수 있습니다.  자세한 내용은 [자체 호스팅 IR을 만들고 구성 하는 방법](create-self-hosted-integration-runtime.md) 에 대 한 문서를 참조 하세요.

## <a name="azure-ssis-integration-runtime"></a>Azure SSIS 통합 런타임

> [!NOTE]
> Azure-SSIS 통합 런타임은 현재 Synapse 파이프라인에서 지원되지 않습니다.

기존 SSIS 워크로드를 올리고 이동하려면 SSIS 패키지를 고유하게 실행하는 Azure-SSIS IR을 만들 수 있습니다.  

### <a name="azure-ssis-ir-network-environment"></a>Azure-SSIS IR 네트워크 환경

Azure-SSIS IR는 공용 네트워크 또는 개인 네트워크에서 프로 비전 할 수 있습니다.  온-프레미스 데이터 액세스는 온-프레미스 네트워크에 연결 된 가상 네트워크에 Azure-SSIS IR를 조인 하 여 지원 됩니다.  

### <a name="azure-ssis-ir-compute-resource-and-scaling"></a>Azure-SSIS IR 컴퓨팅 리소스 및 크기 조정

Azure-SSIS IR은 SSIS 패키지를 실행 하기 위한 전용 Azure Vm의 완전히 관리 되는 클러스터입니다. SSIS 프로젝트/패키지(SSISDB) 카탈로그에 자신만의 Azure SQL Database 또는 SQL Managed Instance를 가져올 수 있습니다. 노드 크기를 지정하여 컴퓨팅 능력을 확장하고 클러스터의 노드 수를 지정하여 확장할 수 있습니다. 요구 사항에 따라 중지 하 고 시작 하 여 Azure-SSIS Integration Runtime를 실행 하는 비용을 관리할 수 있습니다.

자세한 내용은 [Azure-SSIS IR를 만들고 구성 하는 방법](create-azure-ssis-integration-runtime.md)을 참조 하세요.  만든 후에는 ssis 온-프레미스를 사용 하는 것 처럼 SQL Server Data Tools (SSDT) 및 SQL Server Management Studio (SSMS)와 같은 익숙한 도구를 사용 하 여 기존 ssis 패키지를 거의 변경 하지 않고 배포 하 고 관리할 수 있습니다.

Azure SSIS 런타임에 대 한 자세한 내용은 다음 문서를 참조 하세요. 

- [자습서: Azure에 SSIS 패키지 배포](./tutorial-deploy-ssis-packages-azure.md). 이 문서는 Azure-SSIS IR을 만들고 Azure SQL Database를 사용하여 SSIS 카탈로그를 호스트하는 단계별 지침을 제공합니다. 
- [방법: Azure-SSIS 통합 런타임 만들기](create-azure-ssis-integration-runtime.md). 이 문서에서는 자습서를 확장하여 SQL Managed Instance를 사용하고 IR을 가상 네트워크에 조인하는 방법에 대한 지침을 제공합니다. 
- [Azure-SSIS IR 모니터링](monitor-integration-runtime.md#azure-ssis-integration-runtime). 이 문서에서는 Azure-SSIS IR에 대 한 정보를 검색 하 고 반환 된 정보의 상태에 대 한 설명을 제공 하는 방법을 보여 줍니다. 
- [Azure-SSIS IR 관리](manage-azure-ssis-integration-runtime.md). 이 문서는 Azure-SSIS IR을 중지, 시작 또는 제거하는 방법을 설명합니다. 또한 IR에 노드를 추가하여 Azure-SSIS IR 규모를 확장하는 방법을 보여줍니다. 
- [Azure-SSIS IR을 가상 네트워크에 조인](join-azure-ssis-integration-runtime-virtual-network.md). 이 문서에서는 Azure-SSIS IR을 Azure 가상 네트워크에 조인하는 방법에 대한 개념 정보를 제공합니다. 또한 Azure Portal를 사용 하 여 가상 네트워크를 구성 하 고 Azure-SSIS IR에 조인 하는 단계를 제공 합니다. 

## <a name="integration-runtime-location"></a>통합 런타임 위치

### <a name="relationship-between-factory-location-and-ir-location"></a>팩터리 위치와 IR 위치 사이의 관계

Data Factory 또는 Synapse 작업 영역의 인스턴스를 만들 때 해당 위치를 지정 해야 합니다. 인스턴스에 대 한 메타 데이터가 여기에 저장 되 고 파이프라인의 트리거는 여기에서 시작 됩니다. 메타 데이터는 선택한 지역에만 저장 되며 다른 지역에 저장 되지 않습니다.

한편 파이프라인은 다른 Azure 지역에서 데이터 저장소 및 계산 서비스에 액세스 하 여 데이터 저장소 간에 데이터를 이동 하거나 compute services를 사용 하 여 데이터를 처리할 수 있습니다. 이 동작은 데이터 준수, 효율성 및 네트워크 송신 비용 절감을 위해 [전역적으로 사용할 수 있는 IR](https://azure.microsoft.com/global-infrastructure/services/)을 통해 실현됩니다.

IR 위치는 백 엔드 계산의 위치를 정의 하 고 데이터 이동, 작업 디스패치 및 SSIS 패키지 실행이 수행 되는 위치를 정의 합니다. IR 위치는 이 위치가 속한 Data Factory의 위치와 다를 수 있습니다. 

### <a name="azure-ir-location"></a>Azure IR 위치

Azure IR의 위치 영역을 설정할 수 있습니다 .이 경우 작업 실행 또는 디스패치는 선택한 영역에서 발생 합니다.

기본값은 공용 네트워크에서 Azure IR를 자동으로 확인 하는 것입니다.  이 옵션을 사용하여:

- 복사 활동의 경우 싱크 데이터 저장소의 위치를 자동으로 검색 한 다음 동일한 지역 (사용 가능한 경우)에 IR을 사용 하거나 동일한 지리에서 가장 가까운 것을 사용 하는 것이 가장 좋습니다. 그렇지 않으면입니다. 싱크 데이터 저장소의 지역이 검색 되지 않는 경우 인스턴스의 지역에서 IR이 대신 사용 됩니다.

  예를 들어 미국 동부에서 Data Factory 또는 Synapse 작업 영역을 만들었습니다. 
  
  - 미국 서 부에 있는 Azure Blob에 데이터를 복사 하는 경우 Blob이 미국 서 부 지역에 있는 것으로 검색 되 면 복사 작업은 미국 서 부에서 IR에 대해 실행 됩니다. 지역 검색이 실패 하는 경우 복사 작업은 미국 동부에서 IR에 실행 됩니다.
  - 데이터를 Salesforce로 복사 하는 경우 해당 지역을 검색할 수 없으면 미국 동부에서 IR에 복사 작업이 실행 됩니다.

  >[!TIP] 
  >엄격한 데이터 규정 준수 요구 사항이 있고 데이터가 특정 지리를 떠나지 않도록 해야 하는 경우 특정 지역에서 Azure IR를 명시적으로 만들고 ConnectVia 속성을 사용 하 여 연결 된 서비스를이 IR로 가리킬 수 있습니다. 예를 들어 영국 남부 blob의 데이터를 영국 남부의 Azure Synapse 작업 영역으로 복사 하 고 데이터에서 영국을 떠나지 않도록 하려면 영국 남부에서 Azure IR를 만들고 연결 된 서비스를이 IR에 연결 합니다.

- Lookup/GetMetadata/Delete 활동 실행 (파이프라인 활동), 변환 활동 디스패치 (외부 활동) 및 제작 작업 (테스트 연결, 폴더 목록 및 테이블 목록 찾아보기, 데이터 미리 보기)의 경우, Data Factory 또는 Synapse 작업 영역과 동일한 지역에 있는 IR이 사용 됩니다.

- Data Flow의 경우 Data Factory 또는 Synapse 작업 영역 지역의 IR이 사용됩니다. 

  > [!TIP] 
  > 가능 하면 데이터 흐름이 해당 데이터 저장소와 동일한 지역에서 실행 되도록 하는 것이 가장 좋습니다. Azure IR에 대 한 자동 확인 (데이터 저장소 위치가 Data Factory 또는 Synapse 작업 영역 위치와 동일 하 게 설정 된 경우) 또는 데이터 저장소와 동일한 지역에 새 Azure IR 인스턴스를 만든 다음 데이터 흐름을 실행 하 여이 작업을 수행할 수 있습니다. 

Azure IR에 대해 자동 확인을 사용 하 여 관리 되는 Virtual Network을 사용 하도록 설정 하면 Data Factory 또는 Synapse 작업 영역 영역의 IR이 사용 됩니다. 

Data Factory Studio 또는 Synapse Studio 또는 활동 모니터링 페이로드의 파이프라인 활동 모니터링 보기에서 활동을 실행 하는 동안 어떤 IR 위치가 적용 될 수 있는지 모니터링할 수 있습니다.

### <a name="self-hosted-ir-location"></a>자체 호스팅 IR 위치

자체 호스팅 IR은 논리적으로 Data Factory 또는 Synapse 작업 영역에 등록되며 해당 기능을 지원하기 위해 사용되는 컴퓨팅은 사용자가 제공합니다. 따라서 자체 호스팅 IR에 대한 명시적 위치 속성은 없습니다. 

데이터 이동을 수행하기 위해 사용하는 경우 자체 호스팅 IR은 소스에서 데이터를 추출하고 대상에 씁니다.

### <a name="azure-ssis-ir-location"></a>Azure-SSIS IR 위치

> [!NOTE]
> Azure-SSIS 통합 런타임은 현재 Synapse 파이프라인에서 지원되지 않습니다.

Azure SSIS IR에 적합한 위치 선택은 ETL(추출-변환-로드) 워크플로에서 고성능을 달성하기 위해 필수적입니다.

- Azure-SSIS IR 위치는 Data Factory의 위치와 동일할 필요는 없지만 SSISDB가 있는 Azure SQL Database 또는 SQL Managed Instance의 위치와 동일 해야 합니다 (). 이러한 방식으로 Azure-SSIS Integration Runtime는 서로 다른 위치 간에 과도 한 트래픽을 발생 시 키 지 않고 SSISDB에 쉽게 액세스할 수 있습니다.
- 기존 Azure SQL Database 또는 SQL Managed Instance는 없지만 온-프레미스 데이터 소스/대상이 있는 경우 온-프레미스 네트워크에 연결된 가상 네트워크의 같은 위치에 새 Azure SQL Database 또는 SQL Managed Instance를 만들어야 합니다.  이러한 방식으로 새 Azure SQL Database 또는 SQL Managed Instance를 사용 하 여 Azure-SSIS IR를 만들고 해당 가상 네트워크에 연결할 수 있습니다.  모든 항목은 데이터 이동 및 관련 비용을 최소화 하면서 성능을 최대화 하는 동일한 위치에 있습니다.
- 기존 Azure SQL Database 또는 SQL Managed Instance의 위치가 온-프레미스 네트워크에 연결 된 가상 네트워크의 위치와 다른 경우 먼저 기존 Azure SQL Database 또는 SQL Managed Instance를 사용 하 여 Azure-SSIS IR를 만든 다음 동일한 위치의 다른 가상 네트워크에 연결 합니다.  그런 다음 서로 다른 위치 간의 가상 네트워크 연결에 대 한 가상 네트워크를 구성 합니다.

다음 다이어그램에서는 Data Factory 및 해당 통합 런타임에 대 한 위치 설정을 보여 줍니다.

:::image type="content" source="media/concepts-integration-runtime/integration-runtime-location.png" alt-text="Data Factory integration runtime 위치를 표시 합니다.":::

## <a name="determining-which-ir-to-use"></a>사용할 IR 결정
하나의 작업이 둘 이상의 통합 런타임 유형과 연결되는 경우 그중 하나로 확인합니다. 자체 호스팅 통합 런타임은 Azure Data Factory 또는 관리 되는 가상 네트워크를 사용 하는 Synapse 작업 영역 인스턴스의 Azure 통합 런타임 보다 우선 합니다. 그리고 후자는 글로벌 Azure integration runtime 보다 우선적으로 적용 됩니다.

예를 들어 하나의 복사 작업은 원본에서 싱크로 데이터를 복사하는 데 사용됩니다. 글로벌 Azure integration runtime은 연결 된 서비스에 연결 된 서비스에 연결 되 고, 관리 되는 가상 네트워크 Azure Data Factory의 Azure integration runtime과 싱크 연결 된 서비스와 연결 됩니다. 그러면 원본 및 싱크 연결 된 서비스가 모두 Azure Data Factory 관리 가상 네트워크에서 Azure integration runtime을 사용 하 게 됩니다. 그러나 자체 호스팅 통합 런타임이 원본에 대해 연결 된 서비스를 연결 하는 경우 원본 및 싱크 연결 된 서비스 모두 자체 호스팅 통합 런타임을 사용 합니다.

### <a name="copy-activity"></a>복사 활동

복사 작업을 수행 하려면 원본 및 싱크 연결 된 서비스가 모두 데이터 흐름 방향을 정의 해야 합니다. 다음 논리를 사용하여 복사를 수행하는 데 사용하는 통합 런타임 인스턴스를 결정합니다. 

- **두 클라우드 데이터 원본 간 복사**: 소스 및 싱크 연결 된 서비스가 모두 Azure IR를 사용 하는 경우, 지역 Azure IR가 지정 된 경우 사용 되며, [통합 런타임 위치](#integration-runtime-location) 섹션에 설명 된 대로 IR 자동 확인 (기본값) 옵션이 선택 된 경우 Azure IR 위치가 자동으로 결정 됩니다.
- **클라우드 데이터 원본과 개인 네트워크의 데이터 원본 간 복사**: 원본 또는 싱크 연결 된 서비스가 자체 호스팅 ir을 가리키면 복사 작업이 자체 호스팅 ir에서 실행 됩니다.
- **개인 네트워크에 있는 두 데이터 원본 간 복사**: 소스 및 싱크 연결 된 서비스는 모두 integration runtime의 동일한 인스턴스를 가리켜야 하며, 해당 IR은 복사 작업을 실행 하는 데 사용 됩니다.

### <a name="lookup-and-getmetadata-activity"></a>조회 및 GetMetadata 작업

조회 및 GetMetadata 작업은 데이터 저장소 연결된 서비스와 연결된 통합 런타임에서 실행됩니다.

### <a name="external-transformation-activity"></a>외부 변환 작업

외부 계산 엔진을 활용 하는 각 외부 변환 작업에는 통합 런타임을 가리키는 대상 계산 연결 된 서비스가 있습니다. 이 IR 인스턴스는 외부 직접 코딩 된 변환 작업을 디스패치할 위치를 결정 합니다.

### <a name="data-flow-activity"></a>데이터 흐름 작업

데이터 Flow 활동은 연결 된 Azure integration runtime에서 실행 됩니다. 데이터 흐름에서 사용 되는 Spark 계산은 Azure IR의 데이터 흐름 속성에 의해 결정 되며 서비스에 의해 완전히 관리 됩니다.

## <a name="next-steps"></a>다음 단계

다음 문서를 참조하세요.

- [새 Azure Integration Runtime 만들기](create-azure-integration-runtime.md)
- [자체 호스팅 통합 런타임 만들기](create-self-hosted-integration-runtime.md)
- [Azure-SSIS 통합 런타임을 만듭니다](create-azure-ssis-integration-runtime.md). 이 문서에서는 자습서를 확장하여 SQL Managed Instance를 사용하고 IR을 가상 네트워크에 조인하는 방법에 대한 지침을 제공합니다.

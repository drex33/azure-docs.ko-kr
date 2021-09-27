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
ms.openlocfilehash: 6081e63a9a2fbc4598bac0d368d43c31121fa448
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124787929"
---
# <a name="integration-runtime-in-azure-data-factory"></a>Azure Data Factory의 통합 런타임 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

IR(Integration Runtime)은 서로 다른 네트워크 환경 간에 다음과 같은 데이터 통합 기능을 제공하기 위해 Azure Data Factory 및 Azure Synapse 파이프라인에서 사용하는 컴퓨팅 인프라입니다.

- **데이터 흐름**: 관리형 Azure 컴퓨팅 환경에서 [데이터 흐름](concepts-data-flow-overview.md)을 실행합니다.  
- **데이터 이동**: 공용 네트워크의 데이터 저장소 및 개인 네트워크(온-프레미스 또는 가상 사설망)의 데이터 저장소 간에 데이터를 복사합니다. 기본 제공 커넥터, 형식 변환, 열 매핑 및 성능이 뛰어나고 확장 가능한 데이터 전송에 대한 지원을 제공합니다.
- **작업 디스패치**: Azure Databricks, Azure HDInsight, ML Studio(클래식), Azure SQL Database, SQL Server 등 다양한 컴퓨팅 서비스에서 실행되는 변환 작업을 디스패치 및 모니터링합니다.
- **SSIS 패키지 실행**: SSIS(SQL Server 통합 서비스) 패키지를 관리되는 Azure 컴퓨팅 환경에서 고유하게 실행합니다.

Data Factory 및 Synapse 파이프라인에서 작업은 수행할 작업을 정의합니다. 연결된 서비스는 대상 데이터 저장소 또는 컴퓨팅 서비스를 정의합니다. 통합 런타임은 작업과 연결된 서비스 간의 브리지를 제공합니다.  연결된 서비스 또는 작업에서 참조되며 작업이 실행되거나 디스패치되는 컴퓨팅 환경을 제공합니다. 이러한 방식으로 보안 및 준수 요구를 충족하면서 가장 성능이 뛰어난 방법으로 대상 데이터 저장소 또는 컴퓨팅 서비스에 가능하면 가장 가까운 영역에서 작업을 수행할 수 있습니다.

통합 런타임은 [관리 허브](author-management-hub.md) 및 이를 참조하는 모든 활동, 데이터 세트 또는 데이터 흐름을 사용하여 Azure Data Factory 및 Azure Synapse UI에서 만들 수 있습니다.

## <a name="integration-runtime-types"></a>통합 런타임 유형

Data Factory는 세 유형의 통합 런타임(IR)을 제공하며 데이터 통합 기능 및 원하는 네트워크 환경 요구에 가장 적합한 유형을 선택해야 합니다.  세 유형은 다음과 같습니다.

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
- 공용 네트워크에서 Databricks Notebook/Jar/Python 작업, HDInsight Hive 작업, HDInsight Pig 작업, HDInsight MapReduce 작업, HDInsight Spark 작업, HDInsight Streaming 작업, ML Studio(클래식) Batch Execution 작업, ML Studio(클래식) 업데이트 리소스 작업, 저장 프로시저 작업, Data Lake Analytics U-SQL 작업, .NET 사용자 지정 작업, 웹 작업, 조회 작업, 메타데이터 가져오기 작업과 같은 변환 작업을 디스패치합니다.

### <a name="azure-ir-network-environment"></a>Azure IR 네트워크 환경

Azure 통합 런타임은 공개 액세스 가능한 엔드포인트가 있는 데이터 소스에 연결 및 컴퓨팅 서비스를 지원합니다. 관리형 Virtual Network를 사용하도록 설정하면 Azure Integration Runtime에서 프라이빗 네트워크 환경에서 프라이빗 링크 서비스를 사용하여 데이터 저장소에 연결할 수 있습니다.

### <a name="azure-ir-compute-resource-and-scaling"></a>Azure IR 컴퓨팅 리소스 및 크기 조정
Azure 통합 런타임은 Azure에서 완전히 관리되고, 서버리스 컴퓨팅을 제공합니다.  인프라 프로비전, 소프트웨어 설치, 패치 또는 용량 크기 조정을 걱정할 필요가 없습니다.  또한 실제 사용 기간에 대해서만 지불합니다.

Azure 통합 런타임은 안전하고 안정적이고 고성능의 방법으로 클라우드 데이터 저장소 간에 데이터를 이동하는 고유 컴퓨팅을 제공합니다.  Azure 통합 런타임의 크기를 명시적으로 조정할 필요 없이 복사 작업에 사용할 데이터 통합 단위 수를 설정할 수 있으며 Azure IR의 컴퓨팅 크기는 그에 따라 탄력적으로 확장됩니다. 

작업 디스패치는 작업을 대상 컴퓨팅 서비스에 경로 설정하는 간단한 작업이므로 이 시나리오를 위해 컴퓨팅 크기를 확장할 필요가 없습니다.

Azure IR 만들기 및 구성에 대한 자세한 내용은 [Azure IR 만들기 및 구성 방법](create-azure-integration-runtime.md)을 참조하세요. 

> [!NOTE] 
> Azure 통합 런타임에는 데이터 흐름을 실행하는 데 사용되는 기본 컴퓨팅 인프라를 정의하는 데이터 흐름 런타임과 관련된 속성이 있습니다. 

## <a name="self-hosted-integration-runtime"></a>자체 호스팅 통합 런타임

자체 호스팅 IR로 다음을 수행할 수 있습니다.

- 클라우드 데이터 저장소와 프라이빗 네트워크의 데이터 저장소 간에 복사 작업을 실행합니다.
- 온-프레미스 또는 Azure Virtual Network의 컴퓨팅 리소스에 대해 다음과 같은 변환 작업 디스패치: HDInsight Hive 작업(BYOC(Bring Your Own Cluster)), HDInsight Pig 작업(BYOC), HDInsight MapReduce 작업(BYOC), HDInsight Spark 작업(BYOC), HDInsight Streaming 작업(BYOC), ML Studio(클래식) Batch Execution 작업, ML Studio(클래식) 업데이트 리소스 작업, 저장 프로시저 작업, Data Lake Analytics U-SQL 작업, 사용자 지정 작업(Azure Batch에서 실행), 조회 작업, 메타데이터 가져오기 작업

> [!NOTE] 
> 자체 호스팅 통합 런타임을 사용하여 SAP Hana, MySQL 등 BYOD(bring-your-own driver)를 요구하는 데이터 저장소를 지원합니다. 자세한 내용은 [지원되는 데이터 원본](copy-activity-overview.md#supported-data-stores-and-formats)을 참조하세요.

> [!NOTE] 
> JRE(Java Runtime Environment)는 자체 호스팅 IR의 종속성입니다. JRE가 동일한 호스트에 설치되어 있는지 확인하세요.

### <a name="self-hosted-ir-network-environment"></a>자체 호스팅 IR 네트워크 환경

퍼블릭 클라우드 환경에서 직접 시야를 가지고 있지 않은 프라이빗 네트워크 환경에서 데이터 통합을 안전하게 수행하려면 회사 방화벽 뒤에 또는 가상 사설망 내에 온-프레미스 환경에 자체 호스팅 IR을 설치할 수 있습니다.  자체 호스팅 통합 런타임은 아웃바운드 HTTP 기반 연결만이 인터넷을 열게 만듭니다.

### <a name="self-hosted-ir-compute-resource-and-scaling"></a>자체 호스팅 IR 컴퓨팅 리소스 및 크기 조정

자체 호스팅 IR은 온-프레미스 머신 또는 프라이빗 네트워크 내의 가상 머신에 설치해야 합니다. 현재 Windows 운영 체제에서만 자체 호스팅 IR의 실행을 지원합니다.  

고가용성과 확장성을 위해 논리 인스턴스를 활성-활성 모드의 여러 온-프레미스 컴퓨터와 연결하여 자체 호스팅 IR을 확장할 수 있습니다.  자세한 내용은 세부 정보 방법 가이드 아래의 [자체 호스팅 IR 만들기 및 구성](create-self-hosted-integration-runtime.md) 방법 문서를 참조하세요.

## <a name="azure-ssis-integration-runtime"></a>Azure SSIS 통합 런타임

> [!NOTE]
> Azure-SSIS 통합 런타임은 현재 Synapse 파이프라인에서 지원되지 않습니다.

기존 SSIS 워크로드를 올리고 이동하려면 SSIS 패키지를 고유하게 실행하는 Azure-SSIS IR을 만들 수 있습니다.  

### <a name="azure-ssis-ir-network-environment"></a>Azure-SSIS IR 네트워크 환경

Azure SSIS IR은 공용 네트워크 또는 프라이빗 네트워크에서 프로비전할 수 있습니다.  Azure-SSIS IR을 온-프레미스 네트워크에 연결된 Virtual Network에 조인하여 온-프레미스 데이터 액세스를 지원합니다.  

### <a name="azure-ssis-ir-compute-resource-and-scaling"></a>Azure-SSIS IR 컴퓨팅 리소스 및 크기 조정

Azure-SSIS IR은 SSIS 패키지 실행을 전담하는 완전히 관리되는 Azure VM 클러스터입니다. SSIS 프로젝트/패키지(SSISDB) 카탈로그에 자신만의 Azure SQL Database 또는 SQL Managed Instance를 가져올 수 있습니다. 노드 크기를 지정하여 컴퓨팅 능력을 확장하고 클러스터의 노드 수를 지정하여 확장할 수 있습니다. Azure-SSIS 통합 런타임을 중단하고 적합해 보일 때 시작하여 그 실행 비용을 관리할 수 있습니다.

자세한 내용은 방법 가이드 아래의 Azure-SSIS IR 만들기 및 구성 방법 문서를 참조하세요.  만든 후 SSIS 온-프레미스를 사용할 때와 마찬가지로 SSDT(SQL Server 데이터 도구) 및 SSMS(SQL Server Management Studio) 등 친숙한 도구를 사용하여 기존 SSIS 패키지를 조금만 변경하고 배포 및 관리할 수 있습니다.

Azure-SSIS 런타임에 대한 자세한 내용은 다음 문서를 참조하세요. 

- [자습서: Azure에 SSIS 패키지 배포](./tutorial-deploy-ssis-packages-azure.md). 이 문서는 Azure-SSIS IR을 만들고 Azure SQL Database를 사용하여 SSIS 카탈로그를 호스트하는 단계별 지침을 제공합니다. 
- [방법: Azure-SSIS 통합 런타임 만들기](create-azure-ssis-integration-runtime.md). 이 문서에서는 자습서를 확장하여 SQL Managed Instance를 사용하고 IR을 가상 네트워크에 조인하는 방법에 대한 지침을 제공합니다. 
- [Azure-SSIS IR 모니터링](monitor-integration-runtime.md#azure-ssis-integration-runtime). 이 문서는 Azure-SSIS IR에 대한 정보와 반환된 정보의 상태 설명을 검색하는 방법을 설명합니다. 
- [Azure-SSIS IR 관리](manage-azure-ssis-integration-runtime.md). 이 문서는 Azure-SSIS IR을 중지, 시작 또는 제거하는 방법을 설명합니다. 또한 IR에 노드를 추가하여 Azure-SSIS IR 규모를 확장하는 방법을 보여줍니다. 
- [Azure-SSIS IR을 가상 네트워크에 조인](join-azure-ssis-integration-runtime-virtual-network.md). 이 문서에서는 Azure-SSIS IR을 Azure 가상 네트워크에 조인하는 방법에 대한 개념 정보를 제공합니다. 또한 Azure Portal을 사용하여 Azure-SSIS IR이 가상 네트워크에 조인할 수 있도록 가상 네트워크를 구성하는 단계도 제공합니다. 

## <a name="integration-runtime-location"></a>통합 런타임 위치

### <a name="relationship-between-factory-location-and-ir-location"></a>팩터리 위치와 IR 위치 사이의 관계

고객이 Data Factory 인스턴스를 만들 때 Data Factory 또는 Synapse 작업 영역의 위치를 지정해야 합니다. Data Factory 또는 Synapse 작업 영역에 대한 메타데이터는 여기에 저장되며 파이프라인 트리거는 여기에서 시작됩니다. 메타데이터는 고객이 선택한 지역에만 저장되고 다른 지역에는 저장되지 않습니다.

한편 데이터 Azure Data Factory 또는 Azure Synapse 파이프라인은 컴퓨팅 서비스를 사용하여 데이터 저장소 간에 데이터를 이동하고 데이터를 처리하도록 다른 Azure 지역에서 데이터 저장소 및 컴퓨팅 서비스에 액세스할 수 있습니다. 이 동작은 데이터 준수, 효율성 및 네트워크 송신 비용 절감을 위해 [전역적으로 사용할 수 있는 IR](https://azure.microsoft.com/global-infrastructure/services/)을 통해 실현됩니다.

IR 위치는 해당 백 엔드 컴퓨팅의 위치, 즉 기본적으로 데이터 이동, 작업 디스패치 및 SSIS 패키지 실행이 수행되는 위치를 정의합니다. IR 위치는 이 위치가 속한 Data Factory의 위치와 다를 수 있습니다. 

### <a name="azure-ir-location"></a>Azure IR 위치

Azure IR의 특정 위치를 설정할 수 있으며 작업 실행 또는 디스패치는 이 특정 영역에서 발생합니다.

공용 네트워크에서 기본값인 Azure IR 자동 확인을 사용하도록 선택할 경우

- 복사 작업에서는 싱크 데이터 저장소의 위치를 자동으로 검색한 다음, 동일한 지역(사용 가능한 경우) 또는 동일한 지리적 위치 내 가장 가까운 지역의 IR을 최대한 사용하려고 합니다. 싱크 데이터 저장소의 지역을 검색할 수 없는 경우 Data Factory 지역의 IR이 대신 사용됩니다.

  예를 들어 Data Factory 또는 Synapse 작업 영역이 미국 동부에 생성되었습니다. 
  
  - 미국 서부에 있는 Azure Blob에 데이터를 복사할 때 Blob이 미국 서부에서 감지되면 복사 작업은 미국 서부의 IR에서 실행됩니다. 지역 검색에 실패한 경우 복사 작업은 미국 동부의 IR에서 실행됩니다.
  - 지역이 검색되지 않는 Salesforce에 데이터를 복사하는 경우 복사 작업은 미국 동부의 IR에서 실행됩니다.

  >[!TIP] 
  >데이터 규정 준수 요구 사항이 엄격하고 데이터가 특정 지리를 벗어나면 안 되는 경우 특정 영역에 명시적으로 Azure IR을 만들고 ConnectVia 속성을 사용하여 연결된 서비스가 이 IR을 가리키게 할 수 있습니다. 예를 들어 영국 남부의 Blob 데이터를 영국 남부의 Azure Synapse Analytics로 복사하고 데이터가 영국을 벗어나지 않게 하려면 영국 남부에 Azure IR을 만들고 두 연결된 서비스를 이 IR에 연결하면 됩니다.

- 조회, 메타데이터 가져오기, 삭제 작업 실행(파이프라인 작업이라고도 함), 변환 작업 디스패치(외부 작업이라고도 함) 및 작성 작업(테스트 연결, 폴더 목록 및 테이블 목록 찾아보기, 데이터 미리 보기)의 경우 Data Factory 또는 Synapse 작업 영역과 동일한 지역의 IR을 사용합니다.

- Data Flow의 경우 Data Factory 또는 Synapse 작업 영역 지역의 IR이 사용됩니다. 

  > [!TIP] 
  > 데이터 흐름이 해당 데이터 저장소와 동일한 지역에서 실행되도록 하는 것이 좋습니다(가능한 경우). 이를 위해서는 Azure IR 자동 확인(데이터 저장소 위치가 Data Factory 또는 Synapse 작업 영역 위치와 동일한 경우)을 사용하거나 데이터 저장소와 동일한 지역에 새 Azure IR 인스턴스를 만들어 데이터 흐름을 실행하면 됩니다. 

Azure IR 자동 확인에 대해 관리형 Virtual Network를 사용 설정한 경우 Data Factory 또는 Synapse 작업 영역 지역의 IR을 사용합니다. 

UI 또는 작업 모니터링 페이로드에 대한 파이프라인 작업 모니터링 보기의 작업 실행 동안 어떤 IR 위치가 적용되는지 모니터링할 수 있습니다.

### <a name="self-hosted-ir-location"></a>자체 호스팅 IR 위치

자체 호스팅 IR은 논리적으로 Data Factory 또는 Synapse 작업 영역에 등록되며 해당 기능을 지원하기 위해 사용되는 컴퓨팅은 사용자가 제공합니다. 따라서 자체 호스팅 IR에 대한 명시적 위치 속성은 없습니다. 

데이터 이동을 수행하기 위해 사용하는 경우 자체 호스팅 IR은 소스에서 데이터를 추출하고 대상에 씁니다.

### <a name="azure-ssis-ir-location"></a>Azure-SSIS IR 위치

> [!NOTE]
> Azure-SSIS 통합 런타임은 현재 Synapse 파이프라인에서 지원되지 않습니다.

Azure SSIS IR에 적합한 위치 선택은 ETL(추출-변환-로드) 워크플로에서 고성능을 달성하기 위해 필수적입니다.

- Azure-SSIS IR의 위치가 Data Factory의 위치와 같을 필요는 없지만, SSISDB가 있는 사용자 Azure SQL Database 또는 SQL Managed Instance의 위치와 같아야 합니다. 이러한 방식으로 Azure SSIS 통합 런타임은 서로 다른 위치 간에 과도한 트래픽을 초래하지 않고 SSISDB에 쉽게 액세스할 수 있습니다.
- 기존 Azure SQL Database 또는 SQL Managed Instance는 없지만 온-프레미스 데이터 소스/대상이 있는 경우 온-프레미스 네트워크에 연결된 가상 네트워크의 같은 위치에 새 Azure SQL Database 또는 SQL Managed Instance를 만들어야 합니다.  이러한 방식으로 새 Azure SQL Database 또는 SQL Managed Instance를 사용하여 모두 같은 위치에 있는 가상 네트워크가 서로 다른 위치 간에 데이터 이동을 효과적으로 최소화하도록 조인하여 Azure SSIS IR을 만들 수 있습니다.
- 기존 Azure SQL Database 또는 SQL Managed Instance의 위치가 온-프레미스 네트워크에 연결된 가상 네트워크의 위치와 같지 않은 경우 먼저 기존 Azure SQL Database 또는 SQL Managed Instance를 사용하여 같은 위치에 다른 가상 네트워크를 조인하여 Azure-SSIS IR을 만든 다음 서로 다른 위치 사이에 가상 네트워크 간 연결을 구성합니다.

다음 다이어그램은 Data Factory 및 해당 통합 런타임의 위치를 보여 줍니다.

:::image type="content" source="media/concepts-integration-runtime/integration-runtime-location.png" alt-text="통합 런타임 위치":::

## <a name="determining-which-ir-to-use"></a>사용할 IR 결정
하나의 작업이 둘 이상의 통합 런타임 유형과 연결되는 경우 그중 하나로 확인합니다. 자체 호스팅 통합 런타임은 Azure Data Factory 또는 Synapse 작업 영역에서 관리형 가상 네트워크를 사용하여 Azure 통합 런타임보다 우선하여 적용됩니다. 그리고 후자는 글로벌 Azure 통합 런타임보다 우선하여 적용됩니다.

예를 들어 하나의 복사 작업은 원본에서 싱크로 데이터를 복사하는 데 사용됩니다. 글로벌 Azure 통합 런타임은 원본에 연결된 서비스와 연결되고 Azure Data Factory 관리형 가상 네트워크의 Azure 통합 런타임은 싱크에 연결된 서비스와 연결되며, 결과적으로 원본에 연결된 서비스와 싱크에 연결된 서비스 모두 관리형 가상 네트워크를 통해 Azure Data Factory 또는 Synapse 작업 영역에서 Azure 통합 런타임을 사용합니다. 그러나 자체 호스팅 통합 런타임이 원본에 대해 연결된 서비스를 연결하는 경우 원본 및 싱크 연결된 서비스 모두 자체 호스팅 통합 런타임을 사용합니다.

### <a name="copy-activity"></a>복사 활동

복사 작업의 경우 데이터 흐름의 방향을 정의하기 위해 소스 및 싱크 연결된 서비스가 필요합니다. 다음 논리를 사용하여 복사를 수행하는 데 사용하는 통합 런타임 인스턴스를 결정합니다. 

- **두 클라우드 데이터 원본 간에 복사**: 원본에 연결된 서비스와 싱크에 연결된 서비스 모두 Azure IR을 사용하는 경우 지역별 Azure IR(지정된 경우)을 사용합니다. 또는 [통합 런타임 위치](#integration-runtime-location) 섹션에서 설명한 것처럼 IR 자동 확인(기본값)을 선택하는 경우 Azure IR 위치를 자동으로 결정합니다.
- **클라우드 데이터 소스와 프라이빗 네트워크의 데이터 소스 간 복사**: 소스 또는 싱크 연결된 서비스 중 하나가 자체 호스팅 IR을 가리키는 경우 해당 자체 호스팅 통합 런타임에서 복사 작업을 실행합니다.
- **프라이빗 네트워크의 두 데이터 소스 간 복사**: 소스 및 싱크 연결된 서비스가 모두 통합 런타임의 같은 인스턴스를 가리켜야 하며 해당 통합 런타임을 사용하여 복사 작업을 실행합니다.

### <a name="lookup-and-getmetadata-activity"></a>조회 및 GetMetadata 작업

조회 및 GetMetadata 작업은 데이터 저장소 연결된 서비스와 연결된 통합 런타임에서 실행됩니다.

### <a name="external-transformation-activity"></a>외부 변환 작업

외부 컴퓨팅 엔진을 활용하는 각 외부 변환 작업에는 통합 런타임을 가리키는 대상 컴퓨팅 연결 서비스가 있습니다. 이 통합 런타임 인스턴스는 외부의 직접 코딩된 변환 작업이 디스패치되는 위치를 결정합니다.

### <a name="data-flow-activity"></a>데이터 흐름 작업

데이터 흐름 작업은 연결된 Azure 통합 런타임에 대해 실행됩니다. 데이터 흐름에서 사용되는 Spark 컴퓨팅은 Azure Integration Runtime의 데이터 흐름 속성에 의해 결정되며 ADF를 통해 완전히 관리됩니다.

## <a name="next-steps"></a>다음 단계

다음 문서를 참조하세요.

- [새 Azure Integration Runtime 만들기](create-azure-integration-runtime.md)
- [자체 호스팅 통합 런타임 만들기](create-self-hosted-integration-runtime.md)
- [Azure-SSIS 통합 런타임을 만듭니다](create-azure-ssis-integration-runtime.md). 이 문서에서는 자습서를 확장하여 SQL Managed Instance를 사용하고 IR을 가상 네트워크에 조인하는 방법에 대한 지침을 제공합니다.
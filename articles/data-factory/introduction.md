---
title: Azure Data Factory 소개
description: 데이터의 이동과 변환을 조율하고 자동화하는 클라우드 데이터 통합 서비스인 Azure Data Factory에 대해 알아봅니다.
author: dcstwh
ms.author: weetok
ms.service: data-factory
ms.subservice: tutorials
ms.topic: overview
ms.date: 06/08/2021
ms.openlocfilehash: e9925b8c01cbaaeaf28815a7188118ff2060d507
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129208023"
---
# <a name="what-is-azure-data-factory"></a>Azure 데이터 팩터리란 무엇인가요?

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

빅 데이터 세계에서 구성되지 않은 원시 데이터는 흔히 관계형, 비관계형 및 기타 스토리지 시스템에 저장됩니다. 그러나 원시 데이터 자체는 분석자, 데이터 과학자 또는 비즈니스 의사 결정자에게 의미 있는 통찰력을 제공하는 적절한 컨텍스트나 의미를 가지고 있지 않습니다. 

빅 데이터는 이처럼 수많은 원시 데이터 저장소를 조치 가능한 비즈니스 인사이트로 구체화하도록 프로세스를 조율하고 운영할 수 있는 서비스가 필요합니다. Azure Data Factory는 이처럼 복잡한 하이브리드 ETL(추출-변환-로드), ELT(추출-로드-변환) 및 데이터 통합 프로젝트를 위해 만들어진 관리되는 클라우드 서비스입니다.

예를 들어, 클라우드의 게임에서 생성되는 페타바이트의 게임 로그를 수집하는 게임 회사를 생각해 보겠습니다. 회사에서는 이러한 로그를 분석하여 고객 선호, 인구 통계 및 사용량 동작에 대한 인사이트를 얻으려고 합니다. 또한 상향 판매 및 교차 판매 기회를 식별하고, 매력적인 새로운 기능을 개발하고, 비즈니스 성장을 이끌고 고객에게 더 나은 환경을 제공하려고 합니다.

이러한 로그를 분석하려면 회사는 온-프레미스 데이터 저장소에 있는 고객 정보, 게임 정보 및 마케팅 캠페인 정보와 같은 참조 데이터를 사용해야 합니다. 회사는 온-프레미스 데이터 저장소에서 이 데이터를 이용하여 클라우드 데이터 저장소에 있는 추가 로그 데이터와 결합하기를 원합니다. 

인사이트를 추출하기 위해 클라우드의 Spark 클러스터를 사용하여 조인한 데이터를 처리하고(Azure HDInsight), 변환된 데이터를 Azure Synapse Analytics와 같은 클라우드 데이터 웨어하우스에 게시하여 이 데이터를 바탕으로 보고서를 쉽게 작성하려고 합니다. 이 워크플로를 자동화하고 일일 일정으로 모니터링 및 관리하려고 합니다. 또한 파일이 Blob 저장소 컨테이너에 배치될 때 실행하려고 합니다.

Azure Data Factory는 그러한 데이터 시나리오를 해결하는 플랫폼입니다. 이는 *데이터 이동을 오케스트레이션하고 데이터를 규모에 맞게 변환하는 데이터 기반 워크플로를 만들 수 있는 클라우드 기반 ETL 및 데이터 통합 서비스* 입니다. Azure Data Factory를 사용하여 서로 다른 데이터 저장소의 데이터를 수집할 수 있는 데이터 기반 워크플로(파이프라인이라고 함)를 만들고 예약할 수 있습니다. 데이터 흐름을 사용하거나 컴퓨팅 서비스(예: Azure HDInsight Hadoop, Azure Databricks 및 Azure SQL Database)를 사용하여 데이터를 시각적으로 변환하는 복잡한 ETL 프로세스를 작성할 수 있습니다. 

또한 변환된 데이터를 BI(비즈니스 인텔리전스) 애플리케이션용 Azure Synapse Analytics와 같은 데이터 저장소에 게시하여 사용할 수 있습니다. 궁극적으로, Azure Data Factory를 통해 원시 데이터를 더 나은 비즈니스 결정에 의미 있는 데이터 저장소 및 데이터 레이크로 구성할 수 있습니다.

:::image type="content" source="media/data-flow/overview.png" alt-text="데이터 팩터리의 최상위 수준 보기":::

## <a name="how-does-it-work"></a>작동 원리

Data Factory에는 데이터 엔지니어에게 완벽한 엔드투엔드 플랫폼을 제공하는 일련의 상호 연결된 시스템이 포함되어 있습니다.

이 시각적 개체 가이드에서는 Data Factory 아키텍처에 대해 간략하게 설명합니다.

:::image type="content" source="media\introduction\data-factory-visual-guide-small.png" alt-text="단일 고해상도 이미지로 제공되는 Azure Data Factory의 전체 시스템 아키텍처에 대한 자세한 시각적 가이드입니다." lightbox="media\introduction\data-factory-visual-guide.png":::

자세한 내용을 보려면 위의 이미지를 클릭하여 확대하거나 [고해상도 이미지](/azure/data-factory/media/introduction/data-factory-visual-guide.png)로 이동하세요. 

### <a name="connect-and-collect"></a>연결 및 수집

기업은 다양한 소스 온-프레미스, 클라우드(구조화, 비구조화 및 준구조화)에 모두 서로 다른 간격과 속도로 도착하는 다양한 종류의 데이터를 보유하고 있습니다. 

따라서 정보 생산 시스템을 구축하는 첫 번째 단계에서는 필요한 모든 데이터 원본과 SaaS(software-as-a-service) 서비스, 데이터베이스, 파일 공유 및 FTP 웹 서비스 등과 같은 처리에 연결합니다. 다음 단계는 후속 처리를 위해 중앙 집중화된 위치에 필요한 데이터를 이동하는 것입니다.

데이터 팩터리가 없으면 기업은 사용자 지정 데이터 이동 구성 요소를 구축하거나 사용자 지정 서비스를 작성하여 이러한 데이터 원본과 처리를 통합해야 합니다. 이러한 시스템을 통합하고 유지 관리하는 것은 비용이 많이 들고 어렵습니다. 또한 완전히 관리되는 서비스에서 제공할 수 있는 엔터프라이즈급 모니터링, 경고 및 컨트롤이 종종 부족합니다.

Data Factory가 있으면 향후 분석에 사용할 수 있도록 데이터 파이프라인에서 [복사 작업](copy-activity-overview.md)을 사용하여 온-프레미스 및 클라우드 소스 데이터 저장소에서 클라우드의 중앙 데이터 저장소로 데이터를 이동할 수 있습니다. 예를 들어, Azure Data Lake Storage에서 데이터를 수집하고 나중에 Azure Data Lake Analytics 컴퓨팅 서비스를 사용하여 데이터를 변환할 수 있습니다. 또한 Azure HDInsight Hadoop 클러스터를 사용하여 Azure Blob Storage에서 데이터를 수집하고 나중에 변환할 수 있습니다.

### <a name="transform-and-enrich"></a>변환 및 보강
데이터가 클라우드의 중앙 집중식 데이터 저장소에 있는 경우 ADF 매핑 데이터 흐름을 사용하여 수집된 데이터를 처리하거나 변환합니다. 데이터 흐름을 통해 데이터 엔지니어는 Spark 클러스터 또는 Spark 프로그래밍을 이해할 필요 없이 Spark에서 실행되는 데이터 변환 그래프를 작성하고 유지할 수 있습니다.

직접 변환을 코딩하는 것을 선호하는 경우 ADF는 컴퓨팅 서비스(예: HDInsight Hadoop, Spark, Data Lake Analytics 및 Machine Learning)에서 변환을 실행하기 위한 외부 활동을 지원합니다.

### <a name="cicd-and-publish"></a>CI/CD 및 게시
Azure DevOps 및 GitHub를 사용하여 [Data Factory가 데이터 파이프라인의 CI/CD를 완벽하게 지원](continuous-integration-delivery.md)합니다. 이를 통해 완성된 제품을 게시하기 전에 ETL 프로세스를 증분 방식으로 개발하고 제공할 수 있습니다. 원시 데이터를 비즈니스에 사용 가능한 형식으로 구체화했으므로 데이터를 Azure Data Warehouse, Azure SQL Database, Azure CosmosDB 또는 비즈니스 사용자가 자신의 비즈니스 인텔리전스 도구로/에서 가리킬 수 있는 분석 엔진에 로드합니다.
### <a name="monitor"></a>모니터
데이터 통합 파이프라인을 성공적으로 만들고 배포하여 구체화된 데이터에서 비즈니스 가치를 제공한 후에는 성공 및 실패 비율에 맞게 일정 계획된 작업과 파이프라인을 모니터링합니다. Azure Data Factory는 Azure Monitor, API, PowerShell, Azure Monitor 로그 및 Azure Portal의 상태 패널을 통한 파이프라인 모니터링을 기본 제공합니다.

## <a name="top-level-concepts"></a>최상위 개념
Azure 구독에는 하나 이상의 Azure Data Factory 인스턴스(또는 데이터 팩터리)가 있을 수 있습니다. Azure Data Factory는 아래의 핵심 구성 요소로 구성됩니다.
- 파이프라인
- 활동
- 데이터 세트
- 연결된 서비스
- 데이터 흐름
- Integration Runtimes

이러한 구성 요소는 함께 작동하여 데이터를 이동하고 변환하는 단계를 사용하여 데이터 기반 워크플로를 작성할 수 있는 플랫폼을 제공합니다.

### <a name="pipeline"></a>파이프라인
데이터 팩터리에는 하나 이상의 파이프라인이 포함될 수 있습니다. 파이프라인은 작업 단위를 수행하는 작업의 논리적 그룹입니다. 파이프라인의 활동이 모여 작업을 수행합니다. 예를 들어, Azure Blob에서 데이터를 수집한 다음 HDInsight 클러스터에서 Hive 쿼리를 실행하여 데이터를 분할하는 작업 그룹이 파이프라인에 포함될 수 있습니다. 

이 방식의 장점은 파이프라인을 통해 각각을 개별적으로 관리하는 대신 하나의 집합으로써 활동을 관리할 수 있다는 점입니다. 파이프라인의 작업을 서로 연결하여 순차적으로 작동하거나 독립적으로 병렬 작동할 수 있습니다.

### <a name="mapping-data-flows"></a>데이터 흐름 매핑
모든 크기의 데이터를 변환하는 데 사용할 수 있는 데이터 변환 논리 그래프를 만들고 관리합니다. 재사용 가능한 데이터 변환 루틴 라이브러리를 작성하고, 이러한 프로세스를 ADF 파이프라인에서 확장된 방식으로 실행할 수 있습니다. Data Factory는 필요할 때 회전 속도를 높이고 낮추는 논리를 Spark 클러스터에서 실행합니다. 클러스터를 관리하거나 유지 관리할 필요가 없습니다.

### <a name="activity"></a>작업
작업은 파이프라인의 처리 단계를 나타냅니다. 예를 들어, 하나의 데이터 저장소에서 다른 데이터 저장소로 데이터를 복사하는 데 복사 작업을 사용할 수 있습니다. 마찬가지로 데이터를 변환하거나 분석하기 위해서 Azure HDInsight 클러스터에서 Hive 쿼리를 실행하는 Hive 작업을 사용할 수 있습니다. Data Factory는 데이터 이동 작업, 데이터 변환 작업 및 제어 작업이라는 세 종류의 작업을 지원합니다.

### <a name="datasets"></a>데이터 세트
데이터 세트는 데이터 저장소 내의 데이터 구조를 나타내며, 사용자가 활동에서 입력 또는 출력으로 사용하려는 데이터를 가리키거나 참조할 뿐입니다. 

### <a name="linked-services"></a>연결된 서비스
연결된 서비스는 데이터 팩터리에서 외부 리소스에 연결하는 데 필요한 연결 정보를 정의하는 연결 문자열과 같습니다. 연결된 서비스는 데이터 원본에 연결을 정의하고 데이터 세트는 데이터 구조를 나타낸다고 생각하시면 됩니다. 예를 들어, Azure Storage 연결 서비스는 Azure Storage 계정에 연결할 연결 문자열을 지정합니다. 또한 Azure Blob 데이터 세트는 Blob 컨테이너 및 데이터가 포함된 폴더를 지정합니다.

연결된 서비스는 데이터 팩터리 내에서 두 가지 용도로 사용됩니다.

- SQL Server 데이터베이스, Oracle 데이터베이스, 파일 공유 또는 Azure Blob 스토리지 계정을 포함하지만 여기에 국한되지 않는 **데이터 저장소** 를 나타내기 위해 사용됩니다. 지원되는 데이터 저장소 목록은 [복사 작업](copy-activity-overview.md) 문서를 참조하세요.

- 활동의 실행을 호스팅할 수 있는 **컴퓨팅 리소스** 를 나타내기 위해 사용됩니다. 예를 들어, HDInsightHive 활동은 HDInsight Hadoop 클러스터에서 실행됩니다. 변환 작업 및 지원되는 컴퓨팅 환경 목록은 [데이터 변환](transform-data.md) 문서를 참조하세요.

### <a name="integration-runtime"></a>통합 런타임
Data Factory에서 작업은 수행할 작업을 정의합니다. 연결된 서비스는 대상 데이터 저장소 또는 컴퓨팅 서비스를 정의합니다. 통합 런타임은 작업과 연결된 서비스 간의 브리지를 제공합니다.  연결된 서비스 또는 작업에서 참조되며 작업이 실행되거나 디스패치되는 컴퓨팅 환경을 제공합니다. 이러한 방식으로 보안 및 준수 요구를 충족하면서 가장 성능이 뛰어난 방법으로 대상 데이터 저장소 또는 컴퓨팅 서비스에 가능하면 가장 가까운 영역에서 작업을 수행할 수 있습니다.

### <a name="triggers"></a>트리거
트리거는 파이프라인 실행을 시작해야 하는 시기를 결정하는 처리 단위를 나타냅니다. 다양한 유형의 이벤트에 대한 다른 종류의 트리거가 있습니다.

### <a name="pipeline-runs"></a>파이프라인 실행
파이프라인 실행은 파이프라인 실행의 인스턴스입니다. 파이프라인 실행은 일반적으로 파이프라인에 정의된 매개 변수에 인수를 전달하여 인스턴스화합니다. 인수는 수동으로 또는 트리거 정의 내에서 전달될 수 있습니다.

### <a name="parameters"></a>매개 변수
매개 변수는 읽기 전용 구성의 키-값 쌍입니다.  매개 변수는 파이프라인에서 정의됩니다. 정의된 매개 변수에 대한 인수는 트리거에 의해 만들어진 실행 컨텍스트 또는 수동으로 실행한 파이프라인에서 실행하는 동안 전달됩니다. 파이프라인 내의 작업은 매개 변수 값을 사용합니다.

데이터 세트는 강력한 형식 매개 변수이며 다시 사용/참조 가능한 엔터티입니다. 작업은 데이터 세트를 참조할 수 있으며 데이터 세트 정의에 정의된 속성을 사용할 수 있습니다.

또한 연결된 서비스도 데이터 저장소 또는 컴퓨팅 환경에 대한 연결 정보를 포함하는 강력한 형식 매개 변수입니다. 역시 다시 사용/참조 가능한 엔터티입니다.

### <a name="control-flow"></a>흐름 제어
흐름 제어는 파이프라인 수준에서 시퀀스, 분기 및 매개 변수 정의의 작업 연결 그리고 요청 시 또는 트리거에서 파이프라인을 호출할 때 인수 전달을 포함하는 파이프라인 작업의 조율입니다. 또한 사용자 지정 상태 전달 및 컨테이너 루핑, 즉 For-each 반복기도 포함합니다.

### <a name="variables"></a>variables
변수는 파이프라인 내부에서 임시 값을 저장하는 데 사용할 수 있으며, 파이프라인, 데이터 흐름 및 기타 활동 간에 값을 전달할 수 있도록 매개 변수와 함께 사용할 수도 있습니다.

## <a name="next-steps"></a>다음 단계
살펴볼 필요가 있는 중요한 다음 단계 문서는 아래와 같습니다.

- [데이터 세트 및 연결된 서비스](concepts-datasets-linked-services.md)
- [파이프라인 및 활동](concepts-pipelines-activities.md)
- [통합 런타임](concepts-integration-runtime.md)
- [데이터 흐름 매핑](concepts-data-flow-overview.md)
- [Azure Portal의 Data Factory UI](quickstart-create-data-factory-portal.md)
- [Azure Portal의 데이터 복사 도구](quickstart-create-data-factory-copy-data-tool.md)
- [PowerShell](quickstart-create-data-factory-powershell.md)
- [.NET](quickstart-create-data-factory-dot-net.md)
- [Python](quickstart-create-data-factory-python.md)
- [REST (영문)](quickstart-create-data-factory-rest-api.md)
- [Azure Resource Manager 템플릿](quickstart-create-data-factory-resource-manager-template.md)
 

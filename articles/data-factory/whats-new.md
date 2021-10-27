---
title: Azure Data Factory의 새로운 기능
description: 이 페이지에서는 Azure Data Factory의 새로운 기능과 최근 개선 사항을 소개합니다. Azure Data Factory는 복잡한 하이브리드 ETL(추출-변환-로드), ELT(추출-로드-변환) 및 데이터 통합 프로젝트를 위해 빌드된 관리형 클라우드 서비스입니다.
author: pennyzhou-msft
ms.author: xupzhou
ms.service: data-factory
ms.subservice: concepts
ms.topic: overview
ms.date: 07/14/2021
ms.openlocfilehash: 23c4da30e149f10971c4cdec7b99b63e02c45808
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130164718"
---
# <a name="whats-new-in-azure-data-factory"></a>Azure Data Factory의 새로운 기능

Azure Data Factory 서비스는 지속적으로 개선되고 있습니다. 최신 개발 정보를 확인할 수 있도록 이 문서에서는 다음에 대한 정보를 제공합니다.

- 최신 릴리스
- 알려진 문제
- 버그 수정
- 사용되지 않는 기능
- 변경 계획

이 페이지는 매월 업데이트되므로 정기적으로 다시 방문해 주세요. 

## <a name="september-2021"></a>2021년 9월
<br>
<table>
<tr><td><b>서비스 범주</b></td><td><b>서비스 개선 사항</b></td><td><b>세부 정보</b></td></tr>
  <tr><td><b>연속 통합 및 지속적인 업데이트(CI/CD)</b></td><td>확장된 CI/CD 기능</td><td>이제 Azure Data Factory의 다른 분기를 기반으로 새 Git 분기를 만들 수 있습니다.<br><a href="source-control.md#version-control">자세한 정보</a></td></tr>
<tr><td rowspan=3><b>데이터 이동</b></td><td>Amazon Relational Database Service (RDS) for Oracle 소스</td><td>이제 Azure Data Factory 및 Azure Synapse에서 Amazon RDS for Oracle 소스 커넥터를 사용할 수 있습니다.<br><a href="connector-amazon-rds-for-oracle.md">자세한 정보</a></td></tr>
<tr><td>Amazon RDS for SQL Server 소스</td><td>이제 Azure Data Factory 및 Azure Synapse에서 Amazon RDS for SQL Server 소스 커넥터를 사용할 수 있습니다.<br><a href="connector-amazon-rds-for-sql-server.md">자세한 정보</a></td></tr>
<tr><td>Azure Database for PostgreSQL의 병렬 복사 지원</td><td>이제 Azure Database for PostgreSQL 커넥터에서 병렬 복사 작업을 지원합니다.<br><a href="connector-azure-database-for-postgresql.md">자세한 정보</a></td></tr>
<tr><td rowspan=3><b>데이터 흐름</b></td><td>Azure Data Lake Storage (ADLS) Gen2를 사용하여 전처리 및 후처리 명령을 실행합니다.</td><td>이제 데이터 흐름의 ADLS Gen2 싱크를 사용하여 HDFS (Hadoop 분산 파일 시스템) 전처리 및 후처리 명령을 실행할 수 있습니다.<br><a href="connector-azure-data-lake-storage.md#pre-processing-and-post-processing-commands">자세한 정보</a></td></tr>
<tr><td>IR(Azure Integration Runtime)의 기존 인스턴스에 대한 데이터 흐름 속성 편집</td><td>IR(Azure Integration Runtime)이 기존 IR에 대한 데이터 흐름 속성을 편집할 수 있도록 업데이트 되었습니다. 이제 새 Azure IR을 만들 필요 없이 데이터 흐름 계산 속성을 수정할 수 있습니다.<br><a href="concepts-integration-runtime.md">자세한 정보</a></td></tr>
<tr><td>파이프라인 작업 실행 시작 시간을 향상시키기 위한 Azure Synapse TTL 설정</td><td>Azure Synapse Analytics는 데이터 흐름 파이프라인 작업이 몇 초 만에 실행을 시작할 수 있도록 Azure Integration Runtime에 TTL을 추가하여 데이터 흐름 파이프라인의 런타임을 최소화합니다.<br><a href="control-flow-execute-data-flow-activity.md#data-flow-integration-runtime">자세한 정보</a></td></tr>
<tr><td><b>통합 런타임</b></td><td>Azure Data Factory 관리형 vNet이 GA로 이동</td><td>이제 관리 Virtual Network의 일부로 Azure Integration Runtime를 프로비전하고 개인 엔드포인트를 활용하여 지원되는 데이터 저장소에 안전하게 연결할 수 있습니다. 데이터 트래픽은 데이터 원본에 대한 보안 연결을 제공하는 Azure 개인 링크를 통해 이동합니다. 또한 공용 인터넷으로 데이터가 반출되는 일을 방지합니다.<br><a href="managed-virtual-network-private-endpoint.md">자세한 정보</a></td></tr>
<tr><td rowspan=2><b>오케스트레이션</b></td><td>데이터 파이프라인용 SLA의 운영 및 제공</td><td>Data Factory 경고와 결합된 새로운 경과된 시간 파이프라인 실행 메트릭은 데이터 파이프라인 개발자들이 고객에게 SLA를 보다 잘 제공할 수 있게 해줍니다. 즉 파이프라인이 실행되어야 하는 기간을 알려주시면 파이프라인이 예상보다 오래 실행될 때 미리 알려드립니다.<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/operationalize-and-provide-sla-for-data-pipelines/ba-p/2767768">자세한 정보</a></td></tr>
<tr><td>실패 작업(공개 미리 보기)</td><td>새 실패 작업을 사용하면 어떤 이유로든 파이프라인에서 의도적으로 오류를 throw할 수 있습니다. 예를 들어 조회 작업이 일치하는 데이터를 반환하지 않거나 사용자 지정 작업이 내부 오류로 완료되는 경우 실패 작업을 사용할 수 있습니다.<br><a href="control-flow-fail-activity.md">자세한 정보</a></td></tr>
</table>

## <a name="august-2021"></a>2021년 8월
<br>
<table>
<tr><td><b>서비스 범주</b></td><td><b>서비스 개선 사항</b></td><td><b>세부 정보</b></td></tr>
  <tr><td><b>연속 통합 및 지속적인 업데이트(CI/CD)</b></td><td>Azure Government 및 Azure 중국에서 GitHub를 지원하는 CICD 개선</td><td>미국 정부 및 Azure 중국에서 Azure에 GitHub 지원이 추가되었습니다.<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/cicd-improvements-with-github-support-in-azure-government-and/ba-p/2686918">자세한 정보</a></td></tr>
<tr><td rowspan=2><b>데이터 이동</b></td><td>Azure Data Factory에서 Azure Cosmos DB의 API for MongoDB 커넥터의 버전 3.6 & 4.0 지원</td><td>Azure Data Factory Cosmos DB의 API for MongoDB 커넥터가 이제 서버 버전 3.6 및 4.0을 지원합니다.<br><a href="connector-azure-cosmos-db-mongodb-api.md">자세한 정보</a></td></tr>
<tr><td>Azure Synapse Analytics에 데이터 로드를 위한 COPY 문 사용 개선</td><td>이제 Azure Data Factory Azure Synapse Analytics 커넥터에서 COPY 문의 wildcardFilename으로 *.* 를 사용한 스테이징 복사 및 복사 소스가 지원됩니다.<br><a href="connector-azure-sql-data-warehouse.md#use-copy-statement">자세한 정보</a></td></tr>
<tr><td><b>데이터 흐름</b></td><td>데이터 흐름에서 소스 및 싱크로 사용 가능한 REST 엔드포인트</td><td>Azure Data Factory 및 Azure Synapse Analytics의 데이터 흐름에서 이제 REST 엔드포인트를 소스 및 싱크 모두로 사용할 수 있으며, JSON 및 XML 페이로드를 모두 완전히 지원합니다.<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/rest-source-and-sink-now-available-for-data-flows/ba-p/2596484">자세한 정보</a></td></tr>
<tr><td><b>통합 런타임</b></td><td>자체 호스팅 통합 런타임을 위한 진단 도구 제공</td><td>자체 호스팅 통합 런타임을 위한 진단 도구가 더 나은 사용자 환경을 제공하도록 디자인되었고 사용자가 잠재적인 문제를 찾을 수 있도록 도와줍니다. 이 도구는 자체 호스팅 통합 런타임 컴퓨터에서 일련의 테스트 시나리오를 실행하며, 모든 시나리오에는 일반 문제에 대한 일반적인 상태 확인 사례가 포함되어 있습니다.<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/diagnostic-tool-for-self-hosted-integration-runtime/ba-p/2634905">자세한 정보</a></td></tr>
<tr><td><b>오케스트레이션</b></td><td>고급 필터링 옵션을 포함한 사용자 지정 이벤트 트리거의 일반 공급 전환</td><td>이제 Event Grid에 게시된 사용자 지정 항목에 대응하는 트리거를 만들 수 있습니다. 또한 고급 필터링을 활용해서 대응할 이벤트를 미세하게 제어할 수 있습니다.<br><a href="how-to-create-custom-event-trigger.md">자세한 정보</a></td></tr>
</table>

## <a name="july-2021"></a>2021년 7월
<br>
<table>
<tr><td><b>서비스 범주</b></td><td><b>서비스 개선 사항</b></td><td><b>세부 정보</b></td></tr>
<tr><td><b>데이터 이동</b></td><td>ADF 데이터 복사 도구에서 메타데이터 기반 데이터 수집 파이프라인을 가져오는 시간이 10분 이내로 단축됨(공개 미리 보기)</td><td>데이터 복사 도구(공개 미리 보기)에 대한 메타데이터 기반 접근 방식으로 대용량 데이터 복사 파이프라인을 10분 이내에 빌드할 수 있습니다.<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/get-metadata-driven-data-ingestion-pipelines-on-adf-within-10/ba-p/2528219">자세한 정보</a></td></tr>
<tr><td><b>데이터 흐름</b></td><td>데이터 흐름 변환 함수에 추가된 새로운 맵 함수</td><td>데이터 엔지니어가 맵 데이터 유형 및 복잡한 맵 구조를 쉽게 생성, 판독 및 업데이트할 수 있도록 새로운 데이터 흐름 변환 함수 집합이 추가되었습니다.<br><a href="data-flow-expression-functions.md#map-functions">자세한 정보</a></td></tr>
<tr><td><b>통합 런타임</b></td><td>Azure Data Factory 관리형 VNET(공개 미리 보기)에서 새롭게 제공되는 5개 지역</td><td>Azure Data Factory 관리형 가상 네트워크(공개 미리 보기)에서 5개 지역(중국 동부2, 중국 북부2, US Gov 애리조나, US Gov 텍사스, US Gov 버지니아)이 새롭게 제공됩니다.<br><a href="managed-virtual-network-private-endpoint.md#azure-data-factory-managed-virtual-network-is-available-in-the-following-azure-regions">자세한 정보</a></td></tr>
<tr><td rowspan=2><b>개발자 생산성</b></td><td>몇 가지 세션이 추가되어 새롭게 디자인된 ADF 홈페이지</td><td>Data Factory 홈페이지가 새롭게 디자인되어 대비 및 재배치 기능이 향상되었습니다. 또한 데이터 통합 과정의 생산성 향상을 돕기 위해 몇 가지 섹션이 홈페이지에 도입되었습니다.<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/the-new-and-refreshing-data-factory-home-page/ba-p/2515076">자세한 정보</a></td></tr>
<tr><td>Azure Data Factory Studio의 새로운 방문 페이지</td><td>Azure Portal에서 Data Factory 블레이드의 방문 페이지입니다.<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/the-new-and-refreshing-data-factory-home-page/ba-p/2515076">자세한 정보</a></td></tr>
</table>

## <a name="june-2021"></a>2021년 6월
<br>
<table>
<tr><td><b>서비스 범주</b></td><td><b>서비스 개선 사항</b></td><td><b>세부 정보</b></td></tr>
<tr><td rowspan=4 valign="middle"><b>데이터 이동</b></td><td>Azure Data Factory 데이터 복사 도구가 포함된 새로운 사용자 환경</td><td>이제 향상된 데이터 수집 환경에서 다시 디자인된 데이터 복사 도구를 사용할 수 있습니다.<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/a-re-designed-copy-data-tool-experience/ba-p/2380634">자세한 정보</a></td></tr>
<tr><td>MongoDB와 MongoDB Atlas는 원본과 싱크 둘 다로 지원됨</td><td>이 개선 사항은 지원되는 모든 데이터 저장소와 MongoDB 또는 MongoDB Atlas 데이터베이스 간 데이터 복사를 지원합니다.<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/new-connectors-available-in-adf-mongodb-and-mongodb-atlas-are/ba-p/2441482">자세한 정보</a></td></tr>
<tr><td>Always Encrypted는 Azure SQL Database, Azure SQL Managed Instance, SQL Server 커넥터에서 원본과 싱크 둘 다로 지원됩니다.</td><td>Azure Data Factory에서 Azure SQL Database, Azure SQL Managed Instance, SQL Server 커넥터의 복사 작업을 위해 Always Encrypted를 사용할 수 있습니다.<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/azure-data-factory-copy-now-supports-always-encrypted-for-both/ba-p/2461346">자세한 정보</a></td></tr>
<tr><td>ADLS Gen2 또는 Azure Blob에 싱크할 때 복사 작업에서 사용자 지정 메타데이터를 설정할 수 있습니다.</td><td>ADLS Gen2 또는 Azure Blob에 쓸 때 복사 작업에서 사용자 지정 메타데이터를 설정하거나 원본 파일의 마지막으로 수정된 정보를 메타데이터로 저장할 수 있습니다.<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/support-setting-custom-metadata-when-writing-to-blob-adls-gen2/ba-p/2545506#M490">자세한 정보</a></td></tr>
<tr><td rowspan=4 valign="middle"><b>데이터 흐름</b></td><td>이제 SQL Server가 데이터 흐름의 원본 및 싱크로 지원됨</td><td>이제 SQL Server가 데이터 흐름의 원본 및 싱크로 지원됩니다. Azure Integration Runtime 관리형 VNET 기능을 사용하여 온-프레미스 SQL Server와 클라우드 VM 기반 인스턴스에 통신하도록 네트워킹을 구성하는 방법에 대한 지침을 보려면 링크를 따르세요.<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/new-data-flow-connector-sql-server-as-source-and-sink/ba-p/2406213">자세한 정보</a></td></tr>
<tr><td>이제 모든 새 Azure Integration Runtime에서 데이터 흐름 클러스터 빠른 시작 재사용이 기본적으로 사용하도록 설정됨</td><td>ADF에서 인기 있는 데이터 흐름 빠른 시작 재사용 기능이 일반 공급됨을 알려 드립니다. 이제 모든 새 Azure Integration Runtime에서 빠른 시작 재사용이 기본적으로 사용하도록 설정됩니다.<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/how-to-startup-your-data-flows-execution-in-less-than-5-seconds/ba-p/2267365">자세한 정보</a></td></tr>
<tr><td>파워 쿼리 작업(공개 미리 보기)</td><td>이제 Azure Data Factory 데이터 랭글링을 사용하여 파워 쿼리 싱크에 대한 복합 필드 매핑을 빌드할 수 있습니다. 이 업데이트를 수용하기 위해 이제 싱크가 파워 쿼리(공개 미리 보기) 작업의 파이프라인에서 구성됩니다.<br><a href="wrangling-tutorial.md">자세한 정보</a></td></tr>
<tr><td>Azure Data Factory의 업데이트된 데이터 흐름 모니터링 UI</td><td>Azure Data Factory의 모니터링 UI가 데이터 흐름 ETL 작업 실행을 보면서 성능 튜닝 영역을 빠르게 식별하기 쉽도록 새롭게 업데이트되었습니다.<br><a href="https://techcommunity.microsoft.com/t5/azure-data-factory/updated-data-flows-monitoring-ui-in-adf-amp-synapse/ba-p/2432199">자세한 정보</a></td></tr>
<tr><td><b>SSIS(SQL Server Integration Services)</b></td><td>Azure Data Factory에서 SSIS를 사용하여 간단한 3단계로 어디서나 SQL 실행</td><td>이 게시물에서는 Azure Data Factory에서 SSIS를 사용하여 어디서나 SQL 문/스크립트를 실행하는 간단한 3단계를 제공합니다.<ol><li>자체 호스팅 통합 런타임/SSIS Integration Runtime을 준비합니다.</li><li>Azure Data Factory 파이프라인에서 SSIS 패키지 실행 작업을 준비합니다.</li><li>자체 호스팅 통합 런타임/SSIS Integration Runtime에서 SSIS 패키지 실행 작업을 실행합니다.</li></ol><a href="https://techcommunity.microsoft.com/t5/sql-server-integration-services/run-any-sql-anywhere-in-3-easy-steps-with-ssis-in-azure-data/ba-p/2457244">자세한 정보</a></td></tr>
</table>

## <a name="more-information"></a>자세한 정보

- [블로그 - Azure Data Factory](https://techcommunity.microsoft.com/t5/azure-data-factory/bg-p/AzureDataFactoryBlog)
- [Stack Overflow 포럼](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Twitter](https://twitter.com/AzDataFactory?ref_src=twsrc%5Egoogle%7Ctwcamp%5Eserp%7Ctwgr%5Eauthor)
- [비디오](https://www.youtube.com/channel/UC2S0k7NeLcEm5_IhHUwpN0g/featured)

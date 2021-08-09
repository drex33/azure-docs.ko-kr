---
title: 질문과 대답(FAQ)
description: 이 문서에는 Azure Purview에 대해 자주 묻는 질문과 대답이 나와 있습니다.
author: SunetraVirdi
ms.author: suvirdi
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 05/08/2021
ms.openlocfilehash: 5926add3b4affffeb532c1b10cd81edc866f7f14
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/19/2021
ms.locfileid: "110059557"
---
# <a name="frequently-asked-questions-faq-about-azure-purview"></a>Azure Purview에 대한 FAQ(질문과 대답)

## <a name="overview"></a>개요

많은 조직에서 데이터를 전체적으로 이해하지 못하는 경우 존재하는 데이터, 데이터의 위치 및 관련 데이터를 찾아서 액세스하는 방법을 이해하는 것은 어렵습니다. 데이터에는 계보, 분류, 포괄적인 메타데이터 등의 컨텍스트가 없으므로 비즈니스 사용자가 적절한 데이터를 검색하여 해당 데이터를 적절하게 사용하기 어려워집니다. 따라서 수집된 데이터 중 일부만 비즈니스 의사 결정을 알리는 데 사용됩니다. 마지막으로 데이터 보안 문제를 식별하고 중요한 데이터를 보호하는 것은 일관되지 않습니다. 특히 데이터 민첩성을 유지하면서 지속적인 시간과 노력이 필요합니다.

Azure Purview는 데이터 거버넌스 솔루션입니다. 이를 통해 고객은 사용에 대한 제어를 유지하면서 모든 데이터에 대한 심층 지식을 얻을 수 있습니다. Azure Purview를 사용하여 조직은 데이터를 검색하고 큐레이팅합니다. 데이터 공간에 대한 통찰력을 얻고 중앙에서 데이터에 대한 액세스를 제어합니다.

## <a name="purpose-of-this-faq"></a>이 FAQ의 목적

이 FAQ는 고객과 현장 팀에서 자주 묻는 일반적인 질문에 답변합니다. ADC(Azure Data Catalog) Gen 2(사용되지 않음) 및 Azure Information Protection과 같은 Azure Purview 및 관련 솔루션에 대한 질문을 명확히 설명하기 위한 것입니다.

### <a name="what-are-the-source-types-available-for-metadata-scanning-and-classification"></a>메타데이터 검색 및 분류에 사용할 수 있는 원본 유형은 무엇인가요?

|Azure|비 Azure|
|---------|---------|
|Azure Blob Storage|Power BI|
|Azure Synapse Analytics(SQL DW)|SQL Server |
|Azure Cosmos DB|Teradata(2020년 말까지 사용 가능)|
|Azure SQL Managed Instance|SAP ECC(2020년 말까지 사용 가능)|
|Azure Data Explorer|SAP S/4 HANA(2020년 말까지 사용 가능)|
|Azure Data Lake Storage Gen1|Hive Metastore(2020년 말까지 사용 가능)|
|Azure Data Lake Storage Gen2|Amazon S3|
|Azure 파일|--|
|Azure SQL Database|--|

### <a name="what-data-systemsprocessors-can-we-connect-and-get-lineage"></a>연결하고 계보를 얻을 수 있는 데이터 시스템/프로세서는 무엇인가요?

|데이터 시스템/프로세서 
|---------
|Azure Data Factory: 복사 작업, Data Flow 작업 
|사용자 지정 계보   
|Azure Data Share   
|Power BI    |
|SQL Server Integration Services  

### <a name="how-are-adc-gen-2-azure-information-protection-and-azure-purview-related"></a>ADC Gen 2, Azure Information Protection 및 Azure Purview는 어떤 관계가 있습니까?

Azure Purview는 원래 ADC Gen 2로 시작했지만 범위가 넓혀졌습니다. 이제 Azure Information Protection의 데이터 분류, 레이블 지정 및 준수 정책 적용 기능과 결합된 ADC Gen 2의 고급 카탈로그 기능을 채택합니다. 오늘날 데이터 거버넌스의 광범위한 업계 정의에 더 가깝게 맞춥니다.

### <a name="what-happens-to-customers-using-adc-gen-1"></a>ADC Gen 1을 사용하는 고객은 어떻게 되나요?

Azure Purview는 Microsoft의 카탈로그 솔루션 공간에서 모든 제품 혁신의 중심입니다. ADC Gen 1은 계속 지원됩니다.

### <a name="can-customers-have-multiple-azure-purview-accounts-in-the-same-subscription"></a>고객이 동일한 구독에서 여러 Azure Purview 계정을 가질 수 있나요?

예, 구독 및 테넌트당 여러 Azure Purview 계정을 지원합니다.

### <a name="can-i-run-adc-gen-1-and-azure-purview-in-parallel"></a>ADC Gen 1 및 Azure Purview를 병렬로 실행할 수 있나요?

예. 모두 독립적인 서비스입니다.

### <a name="how-do-i-migrate-existing-adc-gen-1-data-assets-to-azure-purview"></a>기존 ADC Gen 1 데이터 자산을 Azure Purview로 어떻게 마이그레이션하나요?

Azure Purview API를 사용하여 ADC Gen 1에서 추출하고 Azure Purview로 수집합니다. 용어집의 경우 CSV 기반 대량 도구를 지원합니다.

### <a name="how-do-i-encrypt-sensitive-data-for-sql-tables-using-azure-purview"></a>Azure Purview를 사용하여 SQL 테이블의 중요한 데이터를 어떻게 암호화하나요?

데이터 암호화는 데이터 원본 수준에서 수행됩니다. Azure Purview는 메타데이터만 저장합니다. 데이터를 미리 볼 수 없습니다.

### <a name="will-all-the-capabilities-of-adc-gen-2-exist-in-azure-purview"></a>ADC Gen 2의 모든 기능이 Azure Purview에 존재하나요?

예.

<!--## Is the data lineage feature available in Azure Purview?

Yes, but it's limited to the Azure Data Factory connector.

<!-- ## How can I scan SQL Server on-premises? 

Use the self-host integration runtime capability. !-->

<!--### What is the difference between classification in Azure SQL Database and classification in Azure Purview?

|Azure SQL DB classification  |Azure Purview classification  |
|---------|---------|
|Classification is based on SQL metadata from system catalogs. |Classification is based on Azure Purview's sampling technique by using the system-defined or custom-defined regex pattern.|
|Custom classification is supported.     |Custom classification is supported.         |
|Doesn't use Microsoft 365 system classifiers out of the box.    | Uses Microsoft 365 system classifiers out of the box.        |
-->

### <a name="whats-the-difference-between-a-glossary-and-classification"></a>용어집과 분류의 차이점은 무엇인가요?

용어집에서는 데이터 소비자라고도 하는 데이터의 비기술/비즈니스 사용자가 명명 규칙을 사용합니다. 이러한 종류의 사람들은 비즈니스 분석가 또는 Azure Purview를 사용하여 비즈니스 사용량을 기반으로 특정 유형의 데이터를 검색하는 데이터 과학자입니다. 예를 들어 공급망 분석가는 *SKU 유형* 및 *배송 세부 정보* 를 검색해야 할 수 있습니다. 이들은 이러한 용어에 대한 용어집을 검색하여 관련 데이터를 찾을 수 있습니다.
분류는 자산에 존재하는 데이터를 식별하는 테이블, 열 또는 파일 수준에서 데이터 자산에 적용되는 태그입니다. 검색된 데이터의 유형에 따라 분류를 자동 또는 수동으로 적용할 수 있습니다. 일반적으로 분류 태그를 사용하여 자산에 중요한 데이터가 포함되어 있는지 여부와 중요한 데이터 형식을 확인할 수 있습니다.

### <a name="does-azure-purview-scan-and-classify-emails-pdfs-etc-in-my-sharepoint-and-onedrive"></a>Azure Purview는 내 Sharepoint 및 OneDrive에서 이메일, PDF 등을 검색하고 분류하나요?

온-프레미스 SharePoint 사이트 및 라이브러리에 대한 검색은 Azure Information Protection 스캐너를 통해 제공됩니다. 다음 SKU(AIP P1, EMS E3 및 M365 E3)를 사용하는 고객의 Microsoft 365 구독을 통해 스캐너를 사용할 수 있습니다. 이러한 SKU 중 하나를 사용하는 경우 Azure Information Protection 스캐너 사용을 시작하려면 올바른 권한이 있어야 합니다.

<!--### What is the difference between classifications and sensitivity labels in Azure Purview?

Azure Purview's data governance solution is based on the Apache Atlas framework. As defined by Atlas, classification is a way to identify the contents of an asset (table or file) or an entity (table column or structured file). This classification becomes a metadata property that allows Azure Purview to understand the data within each asset and govern and protect them.

Sensitivity labels are a Microsoft 365 concept that resembles classification at the asset level. You create a label with a collection of classifications applied at the asset or entity level.

Atlas-centric customers will see no real distinction between classifications and labels. To these customers, everything is a classification and labels aren't needed.

Security-focused customers will see a distinction between classification and labeling, but only because in Microsoft 365 the classifications aren't exposed directly to the user; only labels are visible. So, similar to Atlas, Office 365 security customers don't need to deal with both entities.
-->

### <a name="what-is-the-compute-used-for-the-scan"></a>검색에 사용되는 컴퓨팅은 무엇인가요?
Microsoft에서 관리하는 검색 인프라가 있습니다. 지원하는 대부분의 Azure/AWS 리소스의 경우 검색 인프라를 배포할 필요가 없습니다.

### <a name="is-there-a-way-to-provision-azure-purview-via-azure-resource-manager-arm-template--cli--powershell"></a>ARM(Azure Resource Manager) 템플릿/CLI/PowerShell을 통해 Azure Purview를 프로비전하는 방법이 있나요?

예, ARM 템플릿을 사용할 수 있습니다.

<!--### Does Azure Purview support guest users in AAD?-->

### <a name="im-already-using-atlas-can-i-easily-move-to-azure-purview"></a>이미 Atlas를 사용하고 있습니다. Azure Purview로 쉽게 이동할 수 있나요?

Azure Purview는 Atlas API와 호환됩니다. Atlas에서 마이그레이션하는 경우 먼저 Azure Purview를 사용하여 데이터 원본을 검색하는 것이 좋습니다. 계정에서 자산을 사용할 수 있게 되면 비슷한 Atlas API를 사용하여 자산 업데이트 또는 사용자 지정 계보 추가와 같이 통합할 수 있습니다. Azure Purview는 Azure Search 사용하도록 검색 API를 수정하므로 고급 검색을 사용할 수 있습니다.

### <a name="can-i-create-multiple-catalogs-in-my-tenant"></a>내 테넌트에서 여러 카탈로그를 만들 수 있나요?

예, 구독 및 테넌트당 여러 Azure Purview 계정을 만들 수 있습니다. 제한 페이지 [Azure Purview를 사용하여 리소스에 대한 할당량 관리 및 증가](how-to-manage-quotas.md)를 검토할 수 있습니다.

여러 계정을 만들어야 하는 경우에 대한 추가 권장 사항은 [Azure Purview 배포 모범 사례](deployment-best-practices.md)에 설명되어 있습니다.

### <a name="can-i-register-multiple-tenants-within-a-single-azure-purview-account"></a>단일 Azure Purview 계정 내에서 여러 테넌트를 등록할 수 있나요?

아니요, 현재 다른 테넌트의 데이터 원본을 검색하려면 해당 테넌트에 별도의 Azure Purview 계정을 만들어야 합니다.

### <a name="does-azure-purview-support-column-level-lineage"></a>Azure Purview는 열 수준 계보를 지원하나요?

예, Azure Purview는 열 수준 계보를 지원합니다.

### <a name="does-azure-purview-support-soft-delete"></a>Azure Purview는 일시 삭제를 지원하나요?

예, Azure Purview는 Azure 구독 상태 관리를 위해 일시 삭제를 지원합니다. Purview는 구독 상태(사용 안 함/경고 등)를 읽고 계정이 복원/삭제될 때까지 계정을 일시 삭제 상태로 둘 수 있습니다. 계정이 일시 삭제 상태이고 GET/DELETE 컨트롤 플레인 API 호출만 허용되면 모든 데이터 평면 API 호출이 차단됩니다. Azure 구독 상태 페이지 [Azure 구독 상태](../cost-management-billing/manage/subscription-states.md)에서 추가 정보를 찾을 수 있습니다.

### <a name="does-azure-purview-currently-support-data-loss-prevention-capabilities"></a>Azure Purview는 현재 데이터 손실 방지 기능을 지원하나요?

아니요, Azure Purview는 현재 데이터 손실 방지 기능을 제공하지 않습니다. 

Microsoft 365의 데이터 손실 방지 기능에 관심이 있는 경우 [Microsoft 정보 보호의 데이터 손실 방지](/microsoft-365/compliance/information-protection#prevent-data-loss)에 대해 읽어보세요.

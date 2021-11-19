---
title: Azure Purview의 레이블 지정
description: 민감도 레이블 및 분류 활용을 시작하여 Purview 자산 향상
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 79f7291380e756ad40a7944fecdc99a89451379d
ms.sourcegitcommit: 81a1d2f927cf78e82557a85c7efdf17bf07aa642
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2021
ms.locfileid: "132810349"
---
# <a name="labeling-in-azure-purview"></a>Azure Purview의 레이블 지정

> [!IMPORTANT]
> Azure Purview 민감도 레이블은 현재 미리 보기로 제공됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.
>

작업을 완료하기 위해 조직의 사람들은 조직 내부 및 외부의 다른 사람들과 공동 작업 수행합니다. 데이터가 항상 클라우드에 유지되는 것은 아니며 디바이스, 앱 및 서비스 간에 모든 곳에서 로밍되는 경우가 많습니다. 데이터가 로밍될 때 조직의 비즈니스 및 규정 준수 정책을 충족하는 방식으로 데이터를 안전하게 보호하려고 합니다.</br>

콘텐츠에 민감도 레이블을 적용하면 조직에서 특정 데이터가 얼마나 중요한지 명시하여 데이터를 안전하게 유지할 수 있습니다. 또한 데이터 자체를 추상화하므로 다른 플랫폼에 중요한 데이터를 노출하지 않고 레이블을 사용하여 데이터 형식을 추적합니다.</br>

예를 들어 사회 보장 번호와 신용 카드 번호가 포함된 문서에 민감도 레이블 '극비'를 적용하면 문서의 실제 데이터를 알지 못해도 문서의 민감도를 식별할 수 있습니다.

## <a name="benefits-of-labeling-in-azure-purview"></a>Azure Purview에서 레이블 지정의 이점

Azure Purview를 사용하면 자산에 민감도 레이블을 적용하여 데이터를 분류하고 보호할 수 있습니다.

* **레이블은 데이터와 함께 이동합니다.** Purview에서 사용되는 민감도 레이블은 현재 Microsoft 365, SharePoint, Teams, Power BI 및 SQL 인식됩니다. 레이블이 Purview의 자산에 적용되고 데이터가 Power BI 또는 Office 같은 다른 플랫폼으로 이동하는 경우 레이블도 데이터와 함께 이동합니다. 마찬가지로, Word 문서에 레이블을 적용한 다음 Purview에서 검사하면 레이블이 Purview로 흐릅니다.
* **데이터 자산 개요:** Purview는 미리 정리된 보고서를 통해 데이터에 대한 인사이트를 제공합니다. Purview에서 데이터를 스캔하는 경우 보유한 자산, 검사 기록, 데이터에 있는 분류, 적용된 레이블, 용어집 용어 등에 대한 정보로 보고서를 수화합니다.
* **자동 레이블 지정:** 레이블은 데이터의 민감도에 따라 자동으로 적용할 수 있습니다. 자산에서 중요한 데이터를 검색할 때 자동 레이블 지정 규칙을 사용하여 적용할 민감도 레이블을 결정합니다. 레이블을 구성하는 분류/중요한 정보 유형을 정의하여 각 민감도 레이블에 대한 자동 레이블 지정 규칙을 만들 수 있습니다.
* **파일 및 데이터베이스 열에 레이블 적용:** 레이블은 Azure Data Lake, Azure Files 등과 같은 스토리지의 파일 및 Azure SQL DB, Cosmos DB 등의 열과 같은 스키마화된 데이터에 적용할 수 있습니다.

민감도 레이블은 데이터를 분류하고 보호하기 위해 자산에 적용할 수 있는 태그입니다. 민감도 레이블에 대한 자세한 내용은 [여기를 참조하십시오.](/microsoft-365/compliance/create-sensitivity-labels)

## <a name="how-to-apply-labels-to-assets-in-azure-purview"></a>Azure Purview에서 자산에 레이블을 적용하는 방법

:::image type="content" source="media/create-sensitivity-label/apply-label-flow.png" alt-text="Purview 흐름의 자산에 레이블 적용 레이블을 만들고, 자산을 등록하고, 자산을 검사하고, 분류를 찾았으며, 적용된 레이블을 검색합니다.":::

Azure Purview에서 자산에 레이블을 적용하려면 다음 단계를 수행해야 합니다.

1. Microsoft 365 규정 준수 센터 [기존 민감도 레이블을 만들거나 Azure Purview](how-to-automatically-label-your-content.md)로 확장합니다. 민감도 레이블 만들기에는 데이터에 있는 분류에 따라 적용해야 하는 레이블을 알려주는 자동 레이블 지정 규칙이 포함됩니다.
1. Azure Purview에서 [자산을 등록하고 검사합니다.](how-to-automatically-label-your-content.md#scan-your-data-to-apply-sensitivity-labels-automatically)
1. Azure Purview는 분류를 적용합니다. 자산에 대한 검사를 예약하면 Azure Purview는 자산의 데이터 형식을 검사하고 데이터 카탈로그에 분류를 적용합니다. 분류 적용은 Azure Purview에서 자동으로 수행되며 아무 작업도 수행되지 않습니다.
1. Azure Purview는 레이블을 적용합니다. 자산에서 분류가 발견되면 Azure Purview는 자동 레이블 지정 규칙에 따라 자산에 레이블을 적용합니다. 레이블 적용은 Azure Purview에서 자동으로 수행되며, 1단계에서 자동 레이블 지정 규칙을 사용하여 레이블을 만든 경우 아무 작업도 수행되지 않습니다.

> [!NOTE]
> 자동 레이블 지정 규칙은 특정 레이블을 적용해야 하는 시기를 지정하는 조건입니다. 이러한 조건이 충족되면 레이블이 데이터에 자동으로 할당됩니다. 레이블을 만들 때 각 검사에서 레이블을 자동으로 적용하려면 파일 및 데이터베이스 열 모두에 대한 자동 레이블 지정 규칙을 정의해야 합니다.
>

## <a name="supported-data-sources"></a>지원되는 데이터 원본

민감도 레이블은 다음 데이터 원본에 대해 Azure Purview에서 지원됩니다.

|데이터 형식  |원본  |
|---------|---------|
|파일에 대한 자동 레이블 지정     |    - Azure Blob Storage</br>- Azure Files</br>Azure Data Lake Storage Gen 1 및 Gen 2</br>- Amazon S3|
|스키마화된 데이터 자산에 대한 자동 레이블 지정    |  - SQL Server</br>- Azure SQL Database</br>- Azure SQL Database Managed Instance</br>- 작업 영역 Azure Synapse Analytics</br>- Azure Cosmos Database(SQL API)</br> - Azure Database for MySQL</br> - PostgreSQL용 Azure 데이터베이스</br> - Azure Data Explorer</br>  |
| | |

## <a name="labeling-for-sql-databases"></a>SQL 데이터베이스에 대한 레이블 지정

스키마화된 데이터 자산에 대한 Purview 레이블 지정 외에도 Microsoft는 [SQL Server Management Studio(SSMS)에서](/sql/ssms/sql-server-management-studio-ssms)SQL 데이터 분류를 사용하여 SQL 데이터베이스 열에 대한 레이블 지정도 지원합니다. Purview는 전역 [민감도 레이블을](/microsoft-365/compliance/sensitivity-labels)사용하지만 SSMS 로컬로 정의된 레이블만 사용합니다.

Purview의 레이블 지정과 SSMS의 레이블 지정은 현재 서로 상호 작용하지 않는 별도의 프로세스입니다. 따라서 **SSMS 적용된 레이블은 Purview에 표시되지 않으며 그 반대의 경우도 마찬가지입니다.** 여러 플랫폼에 적용할 수 있는 글로벌 MIP 레이블을 사용하므로 SQL Database에 레이블을 지정하는 데 Azure Purview를 사용하는 것이 좋습니다.

자세한 내용은 [SQL 데이터 검색 및 분류 설명서](/sql/relational-databases/security/sql-data-discovery-and-classification)를 참조하세요. </br></br>

> [!div class="nextstepaction"]
> [콘텐츠에 자동으로 레이블을 지정하는 방법](./how-to-automatically-label-your-content.md)

> [!div class="nextstepaction"]
> [민감도 레이블 인사이트](sensitivity-insights.md)

> [!div class="nextstepaction"]
> [질문과 대답 레이블 지정](sensitivity-labels-frequently-asked-questions.yml)

---
title: Azure Purview의 레이블 지정
description: 민감도 레이블 및 분류를 활용 하 여 부서의 범위 자산을 개선 하기 시작
author: batamig
ms.author: bagol
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 09/27/2021
ms.openlocfilehash: 0cc474ba9566737cb0117cc3a0f2bfb079cdf3d9
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/06/2021
ms.locfileid: "129616515"
---
# <a name="labeling-in-azure-purview"></a>Azure Purview의 레이블 지정

> [!IMPORTANT]
> Azure 부서의 범위 민감도 레이블은 현재 미리 보기 상태입니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.
>

작업을 완료하기 위해 조직의 사람들은 조직 내부 및 외부의 다른 사람들과 공동 작업 수행합니다. 데이터가 항상 클라우드에 유지되는 것은 아니며 디바이스, 앱 및 서비스 간에 모든 곳에서 로밍되는 경우가 많습니다. 데이터가 로밍 될 때 조직의 비즈니스 및 규정 준수 정책에 부합 하는 방식으로 보안을 유지 하려고 합니다.</br>

콘텐츠에 민감도 레이블을 적용 하면 조직에 중요 한 특정 데이터가 있는지를 설명 하 여 데이터를 안전 하 게 유지할 수 있습니다. 또한 데이터 자체를 추상화 하므로 레이블을 사용 하 여 다른 플랫폼에서 중요 한 데이터를 노출 하지 않고 데이터 형식을 추적할 수 있습니다.</br>

예를 들어 주민 등록 번호와 신용 카드 번호를 포함 하는 문서에 민감도 레이블 ' 매우 기밀 '을 적용 하면 문서의 실제 데이터를 몰라도 문서의 민감도를 식별할 수 있습니다.

## <a name="benefits-of-labeling-in-azure-purview"></a>Azure 부서의 범위에서 레이블 지정의 이점

Azure 부서의 범위를 사용 하면 자산에 민감도 레이블을 적용 하 여 데이터를 분류 하 고 보호할 수 있습니다.

* **레이블은 데이터를 사용 하 여 이동 합니다.** 부서의 범위에 사용 된 민감도 레이블은 현재 Microsoft 365, SharePoint, Teams, Power BI 및 SQL에서 인식 됩니다. 레이블이 부서의 범위의 자산에 적용 되 고 데이터가 Power BI 또는 Office와 같은 다른 플랫폼으로 이동 하는 경우에도 레이블은 데이터와 함께 이동 합니다. 마찬가지로 레이블이 Word 문서에 적용 된 다음 부서의 범위에 의해 검색 되는 경우 레이블은 부서의 범위로 전달 됩니다.
* **데이터 공간 개요:** 부서의 범위는 미리 준비 된 보고서를 통해 데이터에 대 한 통찰력을 제공 합니다. 부서의 범위에서 데이터를 검색 하는 경우 보고서에는 자산, 검색 기록, 데이터에 있는 분류, 레이블 적용 됨, 용어 설명 등의 정보가 포함 되어 있습니다.
* **자동 레이블 지정:** 데이터의 민감도에 따라 레이블을 자동으로 적용할 수 있습니다. 자산이 중요 한 데이터를 검색 하는 경우 autolabeling 규칙을 사용 하 여 적용할 민감도 레이블을 결정 합니다. 각 민감도 레이블에 대해 autolabeling 규칙을 만들어 레이블을 구성 하는 분류/중요 한 정보 유형을 정의할 수 있습니다.
* **파일 및 데이터베이스 열에 레이블 적용:** 레이블은 Azure Data Lake, Azure Files 등과 같은 저장소의 파일에 적용 될 수 있으며, Azure SQL DB, Cosmos DB 등의 열과 같은 데이터를 스키마 화 된 하는 데 사용할 수 있습니다.

민감도 레이블은 데이터를 분류 하 고 보호 하기 위해 자산에 적용할 수 있는 태그입니다. [민감도 레이블에](/microsoft-365/compliance/create-sensitivity-labels.md)대 한 자세한 내용은 여기를 참조 하세요.

## <a name="how-to-apply-labels-to-assets-in-azure-purview"></a>Azure 부서의 범위에서 자산에 레이블을 적용 하는 방법

:::image type="content" source="media/create-sensitivity-label/apply-label-flow.png" alt-text="부서의 범위 flow의 자산에 레이블을 적용 합니다. 레이블 만들기, 자산 등록, 스캔 자산, 분류 찾음, 레이블 적용":::

Azure 부서의 범위에서 자산에 레이블을 적용할 수 있도록 하려면 다음 단계를 수행 해야 합니다.

1. Microsoft 365 규정 준수 센터에서 [Azure 부서의 범위로 기존 민감도 레이블을 만들거나 확장](how-to-automatically-label-your-content.md)합니다. 민감도 레이블 만들기에는 데이터에 있는 분류에 따라 적용 해야 하는 레이블을 알려 주는 autolabeling 규칙이 포함 됩니다.
1. Azure 부서의 범위에서 [자산을 등록 하 고 검사](how-to-automatically-label-your-content.md#scan-your-data-to-apply-sensitivity-labels-automatically) 합니다.
1. Azure 부서의 범위에서 분류 적용: 자산에 대 한 검색을 예약할 때 Azure 부서의 범위는 자산의 데이터 유형을 검색 하 고 데이터 카탈로그에서 분류를 적용 합니다. 분류의 응용 프로그램은 Azure 부서의 범위에 의해 자동으로 수행 되며 사용자에 대 한 작업이 없습니다.
1. Azure 부서의 범위는 레이블을 적용 합니다. 자산에 분류가 있으면 Azure 부서의 범위는 autolabeling 규칙에 따라 자산에 레이블을 적용 합니다. 레이블 응용 프로그램은 Azure 부서의 범위에서 자동으로 수행 되며, 1 단계에서 autolabeling 규칙을 사용 하 여 레이블을 만든 경우에는 아무 작업도 수행 되지 않습니다.

> [!NOTE]
> Autolabeling 규칙은 특정 레이블을 적용 해야 하는 경우를 지정 하는 조건입니다. 이러한 조건이 충족 되 면 레이블은 데이터에 자동으로 할당 됩니다. 레이블을 만들 때 파일 및 데이터베이스 열 모두에 대해 autolabeling 규칙을 정의 하 여 각 검색에서 레이블을 자동으로 적용 하도록 해야 합니다.
>

## <a name="supported-data-sources"></a>지원되는 데이터 원본

민감도 레이블은 Azure 부서의 범위에서 다음 데이터 원본에 대해 지원 됩니다.

|데이터 형식  |원본  |
|---------|---------|
|파일에 대한 자동 레이블 지정     |    - Azure Blob Storage</br>- Azure Files</br>Azure Data Lake Storage Gen 1 및 Gen 2</br>-Amazon S3|
|데이터베이스 열에 대한 자동 레이블 지정     |  - SQL Server</br>- Azure SQL Database</br>- Azure SQL Database Managed Instance</br>-Azure Synapse Analytics 작업 영역</br>-Azure Cosmos Database (SQL API)</br> -MySQL 용 Azure 데이터베이스</br> -PostgreSQL 용 Azure 데이터베이스</br> -Azure 데이터 탐색기</br>  |
| | |

## <a name="labeling-for-sql-databases"></a>SQL 데이터베이스에 대 한 레이블 지정

Microsoft는 데이터베이스 열에 대한 Purview 레이블 지정 외에도 [SSMS(SQL Server Management Studio)](/sql/ssms/sql-server-management-studio-ssms)에서 SQL 데이터 분류를 사용하여 SQL Database 열에 대한 레이블 지정을 지원합니다. 부서의 범위는 글로벌 [민감도 레이블을](/microsoft-365/compliance/sensitivity-labels)사용 하지만 SSMS는 로컬로 정의 된 레이블만 사용 합니다.

Purview의 레이블 지정과 SSMS의 레이블 지정은 현재 서로 상호 작용하지 않는 별도의 프로세스입니다. 따라서 **SSMS에 적용 되는 레이블은 부서의 범위에 표시 되지 않으며 그 반대의 경우도 마찬가지** 입니다. 여러 플랫폼에 적용할 수 있는 글로벌 MIP 레이블을 사용하므로 SQL Database에 레이블을 지정하는 데 Azure Purview를 사용하는 것이 좋습니다.

자세한 내용은 [SQL 데이터 검색 및 분류 설명서](/sql/relational-databases/security/sql-data-discovery-and-classification)를 참조하세요. </br></br>

> [!div class="nextstepaction"]
> [내용에 자동으로 레이블을 만드는 방법](./how-to-automatically-label-your-content.md)

> [!div class="nextstepaction"]
> [민감도 레이블 인사이트](sensitivity-insights.md)

> [!div class="nextstepaction"]
> [질문과 대답 레이블 지정](sensitivity-labels-frequently-asked-questions.yml)

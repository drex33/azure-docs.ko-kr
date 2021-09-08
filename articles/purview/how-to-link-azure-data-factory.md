---
title: Azure Data Factory로 연결
description: 이 문서에서는 Azure Data Factory 및 Azure Purview를 연결하여 데이터 계보를 추적하는 방법을 설명합니다.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 08/25/2021
ms.openlocfilehash: 31ac845591387ec0c7061945e3324cd5249d7b23
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123037800"
---
# <a name="how-to-connect-azure-data-factory-and-azure-purview"></a>Azure Data Factory와 Azure Purview를 연결하는 방법

이 문서에서는 데이터 계보를 추적하기 위해 Azure Purview 계정에 Azure Data Factory 계정을 연결하는 데 필요한 단계를 설명합니다. 또한 이 문서에서는 검사 범위 및 지원되는 계보 패턴에 대한 세부 정보를 제공합니다.

## <a name="view-existing-data-factory-connections"></a>기존 Data Factory 연결 보기

여러 Azure Data Factory는 단일 Azure Purview에 연결하여 계보 정보를 푸시할 수 있습니다. 현재 한도를 사용하면 Purview 관리 센터에서 한 번에 10개의 Data Factory 계정을 연결할 수 있습니다. Purview 계정에 연결된 Data Factory 계정 목록을 표시하려면 다음을 수행합니다.

1. 왼쪽 탐색 창에서 **관리** 를 선택합니다.
2. **계보 연결** 에서 **Data Factory** 를 선택합니다.
3. Data Factory 연결 목록이 표시됩니다.

    :::image type="content" source="./media/how-to-link-azure-data-factory/data-factory-connection.png" alt-text="데이터 팩터리 연결 목록을 보여주는 스크린샷" lightbox="./media/how-to-link-azure-data-factory/data-factory-connection.png":::

4. 연결 **상태** 에 대한 다양한 값을 확인합니다.

    - **연결됨**: 데이터 팩터리가 Purview 계정에 연결되어 있습니다.
    - **연결 끊김**: 데이터 팩터리가 카탈로그에 액세스할 수 있지만 다른 카탈로그에 연결되어 있습니다. 따라서 데이터 계보는 카탈로그에 자동으로 보고되지 않습니다.
    - **액세스 불가**: 현재 사용자에게 데이터 팩터리에 대한 액세스 권한이 없으므로 연결 상태를 알 수 없습니다.

>[!Note]
>Data Factory 연결을 보려면 다음 역할에 할당되어야 합니다. 관리 그룹에서의 역할 상속은 지원되지 않습니다.
>- **2021년 8월 18일이나 그 이후** 에 생성된 Purview 계정의 경우: 루트 컬렉션의 **Collection admins** 역할
>- **2021년 8월 18일 이전** 에 생성된 Purview 계정의 경우: Azure 기본 제공 **Owner**, **Contributor**, **Reader** 또는 **User Access Administrator** 역할

## <a name="create-new-data-factory-connection"></a>새 Data Factory 연결을 만듭니다.

>[!Note]
>Data Factory 연결을 추가하거나 제거하려면 다음 역할에 할당되어야 합니다. 관리 그룹에서의 역할 상속은 지원되지 않습니다.
>- **2021년 8월 18일이나 그 이후** 에 생성된 Purview 계정의 경우: 루트 컬렉션의 **Collection admins** 역할
>- **2021년 8월 18일 이전** 에 생성된 Purview 계정의 경우: **Owner** 또는 **User Access Administrator** 역할 
>
> 뿐만 아니라 사용자가 데이터 팩터리의 “Owner” 또는 “Contributor”여야 합니다. 

Purview 계정에 기존 데이터 팩터리를 연결하려면 아래 단계를 수행합니다. [ADF에서 Data Factory를 Purview 계정에 연결](../data-factory/connect-data-factory-to-azure-purview.md)할 수도 있습니다.

1. 왼쪽 탐색 창에서 **관리** 를 선택합니다.
2. **계보 연결** 에서 **Data Factory** 를 선택합니다.
3. **Data Factory 연결** 페이지에서 **새로 만들기** 를 선택합니다.

4. 목록에서 Data Factory 계정을 선택하고 **확인** 을 선택합니다. 구독 이름을 기준으로 필터링하여 목록을 제한할 수도 있습니다.

    :::image type="content" source="./media/how-to-link-azure-data-factory/connect-data-factory.png" alt-text="Azure Data Factory 연결하는 방법을 보여주는 스크린샷." lightbox="./media/how-to-link-azure-data-factory/connect-data-factory.png":::

    데이터 팩터리가 현재 Purview 계정에 이미 연결되어 있거나 데이터 팩터리에 관리 ID가 없는 경우 일부 Data Factory 인스턴스를 사용하지 못할 수 있습니다.

    선택한 데이터 팩터리가 Purview 범위 계정에 이미 연결되어 있으면 경고 메시지가 표시됩니다. 확인을 선택하여 다른 부서의 범위 계정에 대한 Data Factory 연결을 끊습니다. 추가 확인은 필요하지 않습니다.

    :::image type="content" source="./media/how-to-link-azure-data-factory/warning-for-disconnect-factory.png" alt-text="Azure Data Factory 연결을 끊을 경고를 보여주는 스크린샷." lightbox="./media/how-to-link-azure-data-factory/warning-for-disconnect-factory.png":::

>[!Note]
>이제 10개 이하의 데이터 팩터리를 한 번에 추가할 수 있습니다. 10개 이상의 데이터 팩터리를 한 번에 추가하려면 지원 티켓을 제출하세요.

### <a name="how-authentication-works"></a>인증 작동 방법

데이터 팩터리의 관리 ID는 데이터 팩터리에서 Purview로의 계보 푸시 작업을 인증하는 데 사용됩니다. UI에서 데이터 팩터리를 Purview에 연결하면 역할 할당이 자동으로 추가됩니다. 

- **2021년 8월 18일이나 그 이후** 에 생성된 Purview 계정의 경우 Purview **루트 컬렉션** 에 대한 데이터 팩터리의 관리 ID **Data Curator** 역할을 부여합니다. [Azure Purview의 액세스 제어](../purview/catalog-permissions.md) 및 [컬렉션을 통해 역할 추가 및 액세스 제한](../purview/how-to-create-and-manage-collections.md#add-roles-and-restrict-access-through-collections)에 대해 자세히 알아보세요.

- **2021년 8월 18일 이전** 에 생성된 Purview 계정의 경우 Purview 계정에 대한 데이터 팩터리의 관리 ID Azure 기본 제공 [**Purview Data Curator**](../role-based-access-control/built-in-roles.md#purview-data-curator) 역할을 부여합니다. [Azure Purview의 액세스 제어 - 레거시 권한](../purview/catalog-permissions.md#legacy-permission-guide)에 대해 자세히 알아보세요.

### <a name="remove-data-factory-connections"></a>데이터 팩터리 연결 제거

데이터 팩터리 연결을 제거하려면 다음을 수행합니다.

1. **Data Factory 연결** 페이지에서 하나 이상의 데이터 팩터리 연결 옆에 있는 **제거** 단추를 선택합니다.
2. 선택한 데이터 팩터리 연결을 삭제하려면 팝업에서 **확인** 을 선택합니다.

    :::image type="content" source="./media/how-to-link-azure-data-factory/remove-data-factory-connection.png" alt-text="데이터 팩터리를 선택하여 연결을 제거하는 방법을 보여주는 스크린샷." lightbox="./media/how-to-link-azure-data-factory/remove-data-factory-connection.png":::

## <a name="supported-azure-data-factory-activities"></a>지원되는 Azure Data Factory 작업

Azure Purview는 다음과 같은 Azure Data Factory 작업에서 런타임 계보를 캡처합니다.

- [데이터 복사](../data-factory/copy-activity-overview.md)
- [데이터 흐름](../data-factory/concepts-data-flow-overview.md)
- [SSIS 패키지 실행](../data-factory/how-to-invoke-ssis-package-ssis-activity.md)

> [!IMPORTANT]
> 원본 또는 대상이 지원되지 않는 데이터 스토리지 시스템을 사용하는 경우 Azure Purview가 계보를 삭제합니다.

Data Factory와 Purview 간의 통합은 다음 섹션에 설명된 대로 Data Factory에서 지원하는 데이터 시스템의 하위 집합만 지원합니다.

[!INCLUDE[data-factory-supported-lineage-capabilities](includes/data-factory-common-supported-capabilities.md)]

### <a name="execute-ssis-package-support"></a>SSIS 패키지 실행 지원

[지원되는 데이터 저장소](how-to-lineage-sql-server-integration-services.md#supported-data-stores)를 참조하세요.

## <a name="bring-data-factory-lineage-into-purview"></a>Purview에 Data Factory 계보 가져오기

엔드투엔트 연습을 보려면 [자습서: Azure Purview에 Data Factory 계보 데이터 푸시](../data-factory/turorial-push-lineage-to-purview.md)를 수행합니다.

## <a name="supported-lineage-patterns"></a>지원되는 계보 패턴

Azure Purview에서 지원하는 몇 가지 계보 패턴이 있습니다. 생성된 계보 데이터는 Data Factory 활동에서 사용되는 원본 및 싱크 유형을 기반으로 합니다. Data Factory는 80 이상의 원본 및 싱크를 지원하지만 [지원되는 Azure Data Factory 활동](#supported-azure-data-factory-activities)에 나열된 대로 Azure Purview는 하위 집합만 지원합니다.

계보 정보를 보내도록 Data Factory를 구성하려면 [계보의 시작](catalog-lineage-user-guide.md#get-started-with-lineage)을 참조하세요.

계보 보기에서 정보를 찾는 몇 가지 다른 방법에는 다음이 포함됩니다.

- **계보** 탭에서 도형을 가리키면 도구 설명의 자산에 대한 추가 정보를 미리 볼 수 있습니다.
- 노드 또는 가장자리를 선택하여 해당하는 자산 유형을 확인하거나 자산을 전환합니다.
- 데이터 세트의 열은 **계보** 탭의 왼쪽에 표시됩니다. 열 수준 계보에 대한 자세한 내용은 [데이터 세트 열 계보](catalog-lineage-user-guide.md#dataset-column-lineage)를 참조하세요.

### <a name="data-lineage-for-11-operations"></a>1:1 작업에 대한 데이터 계보

데이터 계보를 캡처하기 위해 가장 일반적으로 사용되는 패턴은 단일 입력 데이터 집합에서 단일 출력 데이터 집합으로 두 프로세스 간에 데이터를 이동하는 것입니다.

이러한 패턴의 예는 다음과 같습니다.

- 1 원본/입력: *Customer* (SQL 테이블)
- 1 싱크/출력: *Customer1.csv* (Azure Blob)
- 1 프로세스: *CopyCustomerInfo1 \#Customer1.csv* (Data Factory 복사 작업)

:::image type="content" source="./media/how-to-link-azure-data-factory/adf-copy-lineage.png" alt-text="일대일 Data Factory 복사 작업의 계보를 보여주는 스크린샷." lightbox="./media/how-to-link-azure-data-factory/adf-copy-lineage.png":::

### <a name="data-movement-with-11-lineage-and-wildcard-support"></a>1:1 계보 및 와일드카드를 지원하는 데이터 이동

계보 캡처에 대한 또다른 일반적인 시나리오는 와일드카드를 사용하여 단일 입력 데이터 세트에서 단일 출력 데이터 세트로 파일을 복사하는 것입니다. 와일드카드를 사용하면 복사 작업에서 파일 이름의 공통 부분을 사용하여 복사하기 위해 여러 파일을 매치시킬 수 있습니다. Azure Purview는 해당 복사 작업에서 복사한 각 개별 파일에 대한 파일 수준 계보를 캡처합니다.

이러한 패턴의 예는 다음과 같습니다.

* 원본/입력: *CustomerCall \*.csv* (ADLS Gen2 경로)
* 싱크/출력: *CustomerCall \*.Csv* (Azure blob 파일)
* 1 프로세스: *CopyGen2ToBlob \#CustomerCall.csv* (Data Factory 복사 작업)  

:::image type="content" source="./media/how-to-link-azure-data-factory/adf-copy-lineage-wildcard.png" alt-text="와일드카드 지원을 사용하는 일대일 복사 작업의 계보를 보여주는 스크린샷." lightbox="./media/how-to-link-azure-data-factory/adf-copy-lineage-wildcard.png":::

### <a name="data-movement-with-n1-lineage"></a>n:1 계보를 사용한 데이터 이동

Data Flow 작업을 사용하여 병합, 조인 등과 같은 데이터 작업을 수행할 수 있습니다. 두 개 이상의 원본 데이터 세트를 사용하여 대상 데이터 세트를 생성할 수 있습니다. 이 예제에서 Azure Purview는 개별 입력 파일에 대한 파일 수준 계보를 데이터 흐름 활동의 일부인 SQL 테이블에 캡처합니다.

이러한 패턴의 예는 다음과 같습니다.

* 2 원본/입력: *Customer.csv*, *Sales.parquet* (ADLS Gen2 경로)
* 1 싱크/출력: *Company data* (Azure SQL 테이블)
* 1 프로세스: *DataFlowBlobsToSQL* (Data Factory Data Flow 작업)

:::image type="content" source="./media/how-to-link-azure-data-factory/adf-data-flow-lineage.png" alt-text="n:1의 A D F Data Flow 작업에 대한 계보를 보여주는 스크린샷." lightbox="./media/how-to-link-azure-data-factory/adf-data-flow-lineage.png":::

### <a name="lineage-for-resource-sets"></a>리소스 집합에 대한 계보

리소스 집합은 카탈로그의 논리 개체로, 기본 스토리지의 많은 파티션 파일을 나타냅니다. 자세한 내용은 [리소스 집합 이해](concept-resource-sets.md)를 참조하세요. Azure Purview는 Azure Data Factory에서 계보를 캡처하면, 개별 파티션 파일을 정규화하는 규칙을 적용하여 단일 논리적 개체를 만듭니다.

다음 예제에서는 Azure Blob에서 Azure Data Lake Gen2 리소스 집합이 생성됩니다.

* 1 원본/입력: *Employee \_management.csv* (Azure Blob)
* 1 싱크/출력: *Employee\_management.csv* (Azure Data Lake Gen 2)
* 1 프로세스: *CopyBlobToAdlsGen2\_RS* (Data Factory 복사 작업)

:::image type="content" source="./media/how-to-link-azure-data-factory/adf-resource-set-lineage.png" alt-text="리소스 집합에 대한 계보를 보여주는 스크린샷." lightbox="./media/how-to-link-azure-data-factory/adf-resource-set-lineage.png":::

## <a name="next-steps"></a>다음 단계

[자습서: Data Factory 계보 데이터를 Azure Purview에 푸시](../data-factory/turorial-push-lineage-to-purview.md)

[카탈로그 계보 사용자 가이드](catalog-lineage-user-guide.md)

[계보에 대한 Azure Data Share 링크](how-to-link-azure-data-share.md)
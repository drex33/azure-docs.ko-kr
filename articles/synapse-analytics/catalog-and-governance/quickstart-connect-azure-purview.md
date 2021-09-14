---
title: Azure Purview에 Synapse 작업 영역 연결 
description: Azure Purview 계정에 Synapse 작업 영역을 연결합니다.
author: Jejiang
ms.service: synapse-analytics
ms.subservice: purview
ms.topic: quickstart
ms.date: 09/02/2021
ms.author: jejiang
ms.reviewer: jrasnick
ms.openlocfilehash: b7d729234244302e648a2d3a0bf9c8dc94f10d5a
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123450362"
---
# <a name="quickstartconnect-a-synapse-workspace-to-an-azure-purview-account"></a>빠른 시작: Azure Purview 계정에 Synapse 작업 영역 연결

이 빠른 시작에서는 Azure Purview 계정을 Synapse 작업 영역에 등록합니다. 이렇게 연결하면 Azure Purview 자산을 검색하고, Synapse 기능을 통해 상호 작용하고, Purview에 계보 정보를 푸시할 수 있습니다.

Synapse에서는 다음과 같은 작업을 수행할 수 있습니다.
- 위쪽의 검색 상자에서 키워드를 사용하여 Purview 자산 찾기 
- 메타데이터, [계보](../../purview/catalog-lineage-user-guide.md), 주석을 기반으로 데이터 이해 
- 연결된 서비스 또는 통합 데이터 세트를 사용하여 해당 데이터를 작업 영역에 연결 
- Synapse Apache Spark, Synapse SQL 및 Data Flow를 사용하여 이러한 데이터 세트 분석 
- 파이프라인 실행 및 [Purview에 계보 정보 푸시](../../purview/how-to-lineage-azure-synapse-analytics.md)

## <a name="prerequisites"></a>사전 요구 사항 
- [Azure  Purview 계정](../../purview/create-catalog-portal.md) 
- [Synapse 작업 영역](../quickstart-create-workspace.md) 

## <a name="permissions-for-connecting-an-azure-purview-account"></a>Azure Purview 계정 연결을 위한 권한 

Azure Purview 계정을 Synapse 작업 영역에 연결하려면 Azure Portal IAM의 Synapse 작업 영역에서 **기여자** 역할이 있어야 하고 해당 Azure Purview 계정에 대한 액세스 권한이 필요합니다. 자세한 내용은 [Azure Purview 권한](../../purview/catalog-permissions.md)을 참조하세요.

## <a name="connect-an-azure-purview-account"></a>Azure Purview 계정 연결  

Azure Purview 계정을 연결하기 위한 단계를 수행합니다.

1.  [https://web.azuresynapse.net](https://web.azuresynapse.net)으로 이동하고 Synapse 작업 영역에 로그인합니다. 
2. **관리** -> **Azure Purview** 로 이동하고 **Purview 계정에 연결** 을 선택합니다.
3. **Azure 구독에서 선택** 또는 **수동으로 입력** 을 선택할 수 있습니다. **Azure 구독에서 선택** 을 선택할 경우 액세스 가능한 계정을 선택할 수 있습니다.
4. 연결되면 **Azure Purview 계정** 탭에 Purview 계정 이름이 표시됩니다. 

Purview 계정이 방화벽으로 보호되는 경우 Purview에 대한 관리형 프라이빗 엔드포인트를 만듭니다. Azure Synapse에서 [보안 Purview 계정에 액세스](how-to-access-secured-purview-account.md)하도록 허용하는 방법에 대해 자세히 알아보세요. 초기 연결 중에 이 작업을 수행하거나 나중에 기존 연결을 편집할 수 있습니다.

Purview 연결 정보는 다음과 같은 Synapse 작업 영역 리소스에 저장됩니다. 연결을 프로그래매틱 방식으로 설정하려면 Synapse 작업 영역을 업데이트하고 `purviewConfiguration` 설정을 추가하면 됩니다.

```json
{
    "name": "ContosoSynapseWorkspace",
    "type": "Microsoft.Synapse/workspaces",
    "location": "<region>",
    "properties": {
        ...
        "purviewConfiguration": {
            "purviewResourceId": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupname>/providers/Microsoft.Purview/accounts/<PurviewAccountName>"
        }
    },
    "identity": {...},
    ...
}
```

## <a name="set-up-authentication"></a>인증 설정

Synapse 작업 영역의 관리 ID는 Synapse 작업 영역에서 Purview로 계보 푸시 작업을 인증하기 위해 사용됩니다.

- **2021년 8월 18일 또는 이후** 에 생성된 Purview 계정의 경우 Synapse 작업 영역의 관리 ID에 Purview **루트 컬렉션** 에 대한 **데이터 큐레이터** 역할을 부여합니다. [Azure Purview의 액세스 제어](../../purview/catalog-permissions.md) 및 [컬렉션을 통해 역할 추가 및 액세스 제한](../../purview/how-to-create-and-manage-collections.md#add-roles-and-restrict-access-through-collections)에 대해 자세히 알아보세요.

    Synapse 작업 영역을 Synapse Studio의 Purview에 연결하면 Synapse가 해당 역할 할당을 자동으로 추가하려고 시도합니다. Purview 루트 컬렉션에 대한 **컬렉션 관리자** 역할이 있고 네트워크에서 Purview 계정에 액세스할 수 있는 경우 이 작업은 성공적으로 수행됩니다.

- **2021년 8월 18일 이전** 생성된 Purview 계정의 경우 Synapse 작업 영역의 관리 ID에 Purview 계정에 대한 Azure 기본 제공 [**Purview 데이터 큐레이터**](../../role-based-access-control/built-in-roles.md#purview-data-curator) 역할을 부여합니다. [Azure Purview의 액세스 제어 - 레거시 권한](../../purview/catalog-permissions.md#legacy-permission-guide)에 대해 자세히 알아보세요.

    Synapse 작업 영역을 Synapse Studio의 Purview에 연결하면 Synapse가 해당 역할 할당을 자동으로 추가하려고 시도합니다. Purview 계정에 대해 Azure 기본 제공 **소유자** 또는 **사용자 액세스 관리자** 역할이 있으면 이 작업이 성공적으로 수행됩니다.

Purview 역할 할당 정보 읽기 권한이 있고 필요한 역할이 부여되지 않았으면 아래 경고가 표시될 수 있습니다. 파이프라인 계보 푸시에 대해 연결이 올바르게 설정되도록 하려면 Purview 계정으로 이동하고 **Purview 데이터 큐레이터** 역할이 Synapse 작업 영역의 관리 ID에 부여되었는지 확인합니다. 그렇지 않으면 역할 할당을 수동으로 추가합니다.

:::image type="content" source="./media/register-purview-account-warning.png" alt-text="Purview 계정 등록 경고 스크린샷":::

## <a name="report-lineage-to-azure-purview"></a>Azure Purview에 계보 보고

Synapse 작업 영역을 Purview 계정에 연결한 후 파이프라인을 실행하면 Synapse가 Purview 계정에 계보 정보를 보고합니다. 지원되는 자세한 기능 및 엔드투엔드 연습을 보려면 [Azure Synapse Analytics의 계보 및 메타데이터](../../purview/how-to-lineage-azure-synapse-analytics.md)를 참조하세요.

## <a name="discover-and-explore-data-using-purview"></a>Purview를 사용하여 데이터 검색 및 살펴보기

Synapse 작업 영역을 Purview 계정에 연결한 후 Synapse 작업 영역의 위쪽 가운데에서 검색 창을 사용하여 데이터를 검색하고 작업을 수행할 수 있습니다. [Azure Purview를 사용하여 Synapse에서 데이터 검색, 연결 및 탐색](how-to-discover-connect-analyze-azure-purview.md)에 대해 자세히 알아보세요.

## <a name="nextsteps"></a>다음 단계 

[Azure Purview를 사용하여 Synapse에서 데이터 검색, 연결 및 탐색](how-to-discover-connect-analyze-azure-purview.md)

[Azure Synapse Analytics의 메타데이터 및 계보](../../purview/how-to-lineage-azure-synapse-analytics.md)

[보안 Azure Purview 계정에 액세스](how-to-access-secured-purview-account.md)

[Azure Purview에서 Azure Synapse 자산 등록 및 검사](../../purview/register-scan-azure-synapse-analytics.md)

[Power BI의 계보를 Azure Purview로 가져오기](../../purview/how-to-lineage-powerbi.md)

[Azure Data Share 및 Azure Purview 연결](../../purview/how-to-link-azure-data-share.md)
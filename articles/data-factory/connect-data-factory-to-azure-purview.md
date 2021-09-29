---
title: Azure Purview에 Data Factory 연결
description: Azure Purview에 Data Factory를 연결하는 방법 알아보기
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019, references_regions
ms.date: 09/27/2021
ms.openlocfilehash: 5d5b1ed8a20bc459370a9bb7e437e1f5c977714d
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129218001"
---
# <a name="connect-data-factory-to-azure-purview-preview"></a>Azure Purview(미리 보기)에 Data Factory 연결

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

[Azure Purview](../purview/overview.md)는 온-프레미스, 다중 클라우드 및 SaaS(Software as a Service) 데이터를 관리하고 제어하는 데 도움이 되는 통합 데이터 거버넌스 서비스입니다. 데이터 팩터리에서 Azure Purview에 연결할 수 있습니다. 이 연결을 통해 Azure Purview를 사용하여 계보 데이터를 캡처하고 Azure Purview 자산을 검색하고 살펴볼 수 있습니다.

## <a name="connect-data-factory-to-azure-purview"></a>Azure Purview에 Data Factory 연결

Azure Purview에 데이터 팩터리를 연결하는 옵션은 두 가지가 있습니다.

- [Data Factory에서 Azure Purview 계정에 연결하기](#connect-to-azure-purview-account-in-data-factory)
- [Azure Purview에서 Data Factory 등록](#register-data-factory-in-azure-purview)

### <a name="connect-to-azure-purview-account-in-data-factory"></a>Data Factory에서 Azure Purview 계정에 연결하기

Azure Purview 계정에 연결하려면 데이터 팩터리에 대한 **소유자** 또는 **기여자** 역할이 있어야 합니다.

Data Factory 작성 UI에서 연결을 설정하려면 다음을 수행합니다.

1. ADF 작성 UI에서 **관리** -> **Azure Purview** 로 이동하고 **Purview 계정에 연결** 을 선택합니다. 

    :::image type="content" source="./media/data-factory-purview/register-purview-account.png" alt-text="Purview 계정 등록 스크린샷":::

2. **Azure 구독에서** 선택하거나 **수동으로 입력** 할 수 있습니다. **Azure 구독에서 선택** 을 선택할 경우 액세스 가능한 계정을 선택할 수 있습니다.

3. 연결되면 **Azure Purview 계정** 탭에 Purview 계정 이름이 표시됩니다.

Purview 계정이 방화벽으로 보호되는 경우 Purview에 대한 관리형 프라이빗 엔드포인트를 만듭니다. [보안 부서의 범위 계정 Data Factory 액세스](how-to-access-secured-purview-account.md)를 허용 하는 방법에 대해 자세히 알아보세요. 초기 연결 중에 이 작업을 수행하거나 나중에 기존 연결을 편집할 수 있습니다.

Purview 연결 정보는 다음과 같은 데이터 팩터리 리소스에 저장됩니다. 프로그래매틱 방식으로 연결을 설정하려면 데이터 팩터리를 업데이트하고 `purviewConfiguration` 설정을 추가하면 됩니다. SSIS 작업에서 계보를 푸시 하려는 경우에도 태그를 추가 `catalogUri` 합니다.

```json
{
    "name": "ContosoDataFactory",
    "type": "Microsoft.DataFactory/factories",
    "location": "<region>",
    "properties": {
        ...
        "purviewConfiguration": {
            "purviewResourceId": "/subscriptions/<subscriptionId>/resourceGroups/<resourceGroupname>/providers/Microsoft.Purview/accounts/<PurviewAccountName>"
        }
    },
    ...
    "identity": {...},
    "tags": {
        "catalogUri": "<PurviewAccountName>.catalog.purview.azure.com //Note: used for SSIS lineage only"
    }
}
```

### <a name="register-data-factory-in-azure-purview"></a>Azure Purview에서 Data Factory 등록

Azure Purview에서 Data Factory를 등록하는 방법에 대한 자세한 내용은 [Azure Data Factory와 Azure Purview를 연결하는 방법](../purview/how-to-link-azure-data-factory.md)을 참조하세요.

## <a name="set-up-authentication"></a>인증 설정

데이터 팩터리의 관리 ID는 데이터 팩터리에서 Purview로의 계보 푸시 작업을 인증하는 데 사용됩니다. 

- **2021년 8월 18일이나 그 이후** 에 생성된 Purview 계정의 경우 Purview **루트 컬렉션** 에 대한 **데이터 큐레이터** 역할을 데이터 팩터리 관리 ID에 부여합니다. [Azure Purview의 액세스 제어](../purview/catalog-permissions.md) 및 [컬렉션을 통해 역할 추가 및 액세스 제한](../purview/how-to-create-and-manage-collections.md#add-roles-and-restrict-access-through-collections)에 대해 자세히 알아보세요.

    작성 UI에서 데이터 팩터리를 Purview에 연결하는 경우 ADF에서 해당 역할 할당을 자동으로 추가하려고 합니다. Purview 루트 컬렉션에 대한 **컬렉션 관리자** 역할이 있고 네트워크에서 Purview 계정에 액세스할 수 있는 경우 이 작업은 성공적으로 수행됩니다.

- **2021년 8월 18일 이전** 에 생성된 Purview 계정의 경우 Purview 계정에 대한 [**Purview 데이터 큐레이터**](../role-based-access-control/built-in-roles.md#purview-data-curator) 역할(Azure 기본 제공 역할)을 데이터 팩터리 관리 ID에 부여합니다. [Azure Purview의 액세스 제어 - 레거시 권한](../purview/catalog-permissions.md#legacy-permission-guide)에 대해 자세히 알아보세요.

    작성 UI에서 데이터 팩터리를 Purview에 연결하는 경우 ADF에서 해당 역할 할당을 자동으로 추가하려고 합니다. Purview 계정에 대한 **소유자** 또는 **사용자 액세스 관리자** 역할(Azure 기본 제공 역할)이 있으면 이 작업이 성공적으로 수행됩니다.

## <a name="monitor-purview-connection"></a>부서의 범위 연결 모니터링

부서의 범위 계정에 데이터 팩터리를 연결 하면 사용 하도록 설정 된 통합 기능에 대 한 세부 정보가 포함 된 다음 페이지가 표시 됩니다.

:::image type="content" source="./media/data-factory-purview/monitor-purview-connection-status.png" alt-text="Azure Data Factory와 부서의 범위 간의 통합 상태를 모니터링 하기 위한 스크린샷":::

**데이터 계보 파이프라인** 의 경우 다음 상태 중 하나가 표시 될 수 있습니다.

- **연결** 됨: 데이터 팩터리가 부서의 범위 계정에 성공적으로 연결 되었습니다. 이는 data factory가 부서의 범위 계정과 연결 되어 있으며 계보를 푸시할 수 있는 권한이 있음을 나타냅니다. 부서의 범위 계정이 방화벽으로 보호 되는 경우 작업을 실행 하 고 계보 푸시를 수행 하는 데 사용 되는 통합 런타임이 부서의 범위 계정에 도달할 수 있는지도 확인 해야 합니다. [Azure Data Factory에서 보안 Azure 부서의 범위 계정에 액세스 하는](how-to-access-secured-purview-account.md)방법에 대해 자세히 알아보세요.
- **연결 끊김**: 부서의 범위 data 큐레이터 role이 data factory의 관리 id에 부여 되지 않았기 때문에 데이터 팩터리가 계보를 부서의 범위에 푸시할 수 없습니다. 이 문제를 해결 하려면 부서의 범위 계정으로 이동 하 여 역할 할당을 확인 하 고 필요에 따라 수동으로 역할을 부여 합니다. [인증 설정](#set-up-authentication) 섹션에서 자세히 알아보세요.
- **알 수 없음**: Data Factory 상태를 확인할 수 없습니다. 가능한 원인은 다음과 같습니다.

    - 계정이 방화벽으로 보호 되기 때문에 현재 네트워크에서 부서의 범위 계정에 연결할 수 없습니다. 대신 부서의 범위 계정에 연결 된 개인 네트워크에서 ADF UI를 시작할 수 있습니다.
    - 부서의 범위 계정에 대 한 역할 할당을 확인할 수 있는 권한이 없습니다. 부서의 범위 계정 관리자에 게 문의 하 여 역할 할당을 확인할 수 있습니다. [인증 설정](#set-up-authentication) 섹션에서 필요한 부서의 범위 역할에 대해 알아봅니다.

## <a name="report-lineage-data-to-azure-purview"></a>Azure Purview에 계보 데이터 보고

데이터 팩터리를 Purview 계정에 연결한 후 파이프라인을 실행하면 Data Factory가 Purview 계정에 계보 정보를 푸시합니다. 지원되는 세부 기능은 [지원되는 Azure Data Factory 작업](../purview/how-to-link-azure-data-factory.md#supported-azure-data-factory-activities)을 참조하세요. 전체 연습을 보려면 [자습서: Azure Purview에 Data Factory 계보 데이터 푸시](tutorial-push-lineage-to-purview.md)를 참조하세요.

## <a name="discover-and-explore-data-using-purview"></a>Purview를 사용하여 데이터 검색 및 살펴보기

데이터 팩터리를 Purview 계정에 연결한 후 Data Factory 작성 UI의 위쪽 중앙에 있는 검색 창을 사용하여 데이터를 검색하고 작업을 수행할 수 있습니다. [Purview를 사용하여 ADF의 데이터 검색 및 살펴보기](how-to-discover-explore-purview-data.md)에서 자세히 알아보세요.

## <a name="next-steps"></a>다음 단계

[자습서: Data Factory 계보 데이터를 Azure Purview에 푸시](tutorial-push-lineage-to-purview.md)

[Purview를 사용하여 ADF의 데이터 검색 및 살펴보기](how-to-discover-explore-purview-data.md)

[보안 Azure Purview 계정에 액세스](how-to-access-secured-purview-account.md)

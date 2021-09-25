---
title: Azure Purview에 Data Factory 연결
description: Azure Purview에 Data Factory를 연결하는 방법 알아보기
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019, references_regions
ms.date: 09/15/2021
ms.openlocfilehash: 574d87c051cf4e56fbaf83186ca0e50f2f52bc45
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128605566"
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

Purview 계정이 방화벽으로 보호되는 경우 Purview에 대한 관리형 프라이빗 엔드포인트를 만듭니다. Data Factory [보안 Purview 계정에 액세스하도록](how-to-access-secured-purview-account.md)하는 방법에 대해 자세히 알아봅니다. 초기 연결 중에 이 작업을 수행하거나 나중에 기존 연결을 편집할 수 있습니다.

Purview 연결 정보는 다음과 같은 데이터 팩터리 리소스에 저장됩니다. 프로그래매틱 방식으로 연결을 설정하려면 데이터 팩터리를 업데이트하고 `purviewConfiguration` 설정을 추가하면 됩니다. SSIS 활동에서 계보를 푸시하려는 경우 `catalogUri` 태그도 추가합니다.

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

Purview 역할 할당 정보를 읽을 권한이 있고 필요한 역할이 부여되지 않은 경우 아래 경고가 표시될 수 있습니다. 파이프라인 계보 푸시를 위한 연결이 제대로 설정되도록 하려면 Purview 계정으로 이동한 다음, 데이터 팩터리 관리 ID에 **Purview 데이터 큐레이터** 역할이 부여되었는지 확인합니다. 역할이 부여되지 않은 경우 역할 할당을 수동으로 추가합니다.

:::image type="content" source="./media/data-factory-purview/register-purview-account-warning.png" alt-text="Purview 계정 등록 경고 스크린샷":::

## <a name="report-lineage-data-to-azure-purview"></a>Azure Purview에 계보 데이터 보고

데이터 팩터리를 Purview 계정에 연결한 후 파이프라인을 실행하면 Data Factory가 Purview 계정에 계보 정보를 푸시합니다. 지원되는 세부 기능은 [지원되는 Azure Data Factory 작업](../purview/how-to-link-azure-data-factory.md#supported-azure-data-factory-activities)을 참조하세요. 전체 연습을 보려면 [자습서: Azure Purview에 Data Factory 계보 데이터 푸시](tutorial-push-lineage-to-purview.md)를 참조하세요.

## <a name="discover-and-explore-data-using-purview"></a>Purview를 사용하여 데이터 검색 및 살펴보기

데이터 팩터리를 Purview 계정에 연결한 후 Data Factory 작성 UI의 위쪽 중앙에 있는 검색 창을 사용하여 데이터를 검색하고 작업을 수행할 수 있습니다. [Purview를 사용하여 ADF의 데이터 검색 및 살펴보기](how-to-discover-explore-purview-data.md)에서 자세히 알아보세요.

## <a name="next-steps"></a>다음 단계

[자습서: Data Factory 계보 데이터를 Azure Purview에 푸시](tutorial-push-lineage-to-purview.md)

[Purview를 사용하여 ADF의 데이터 검색 및 살펴보기](how-to-discover-explore-purview-data.md)

[보안 Azure Purview 계정에 액세스](how-to-access-secured-purview-account.md)

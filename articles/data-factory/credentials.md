---
title: 자격 증명 사용
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory에 Azure 자격 증명을 사용 하는 방법에 대해 알아봅니다.
author: nabhishek
ms.service: data-factory
ms.subservice: security
ms.topic: conceptual
ms.date: 07/19/2021
ms.author: abnarain
ms.custom: synapse
ms.openlocfilehash: a588bf8a02c6683143cff8096ee6e03c6b120234
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131103212"
---
# <a name="credentials-in-azure-data-factory-and-azure-synapse"></a>Azure Data Factory 및 Azure Synapse의 자격 증명

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="prerequisites"></a>사전 요구 사항

사용자에 게 자격 증명으로 사용자 할당 관리 id를 구성 하려면 **microsoft.managedidentity/userAssignedIdentities/*/Assign/action** rbac 작업을 사용 하는 사용자 지정 역할 또는 관리 id 연산자 (Azure rbac) 역할이 있어야 합니다. Synapse에서 자격 증명을 만들고 사용 하려면 추가 RBAC가 필요 합니다. [자세히 알아보기](../synapse-analytics/security/synapse-workspace-synapse-rbac-roles.md).

## <a name="using-credentials"></a>자격 증명 사용

사용자 할당 관리 id, 서비스 주체를 포함할 수 있는 자격 증명을 소개 하 고, Azure Active Directory (AAD) 인증을 지 원하는 연결 된 서비스에서 사용할 수 있는 시스템 할당 관리 id도 나열 합니다. 이를 통해 모든 AAD 기반 자격 증명을 통합하고 관리할 수 있습니다.  

다음은 인증을 위해 연결된 서비스에서  **관리 ID** 를 사용하는 일반적인 단계입니다. 

# <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

1. Azure에서 만든 사용자 할당 관리 id가 없는 경우 먼저 [관리 되](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.ManagedIdentity%2FuserAssignedIdentities) 는 Azure Portal id 페이지에서 id를 하나 만듭니다.

1. Azure Portal, SDK, PowerShell REST API를 사용 하 여 사용자 할당 관리 id를 data factory 인스턴스에 연결 합니다. 아래 스크린샷은 Azure Portal (data factory 블레이드)를 사용 하 여 사용자 할당 관리 id를 연결 합니다.

   :::image type="content" source="media/credentials/uami-azure-portal.png" alt-text="Azure Portal을 사용하여 사용자가 할당한 관리 ID를 연결하는 방법을 보여주는 스크린샷":::

1. 데이터 팩터리 사용자 인터페이스에서 대화형으로 **자격 증명** 을 만듭니다. 1단계에서 데이터 팩터리와 연결된 사용자가 할당한 관리 ID를 선택할 수 있습니다. 

   :::image type="content" source="media/credentials/create-new-credential.png" alt-text="새 자격 증명을 만드는 방법을 보여 주는 스크린샷":::

   :::image type="content" source="media/credentials/user-assigned-credential-configuration.png" alt-text="새 자격 증명의 구성을 보여 주는 스크린샷":::

1. 새 연결 된 서비스를 만들고 인증에서 **사용자 할당 관리 id** 를 선택 합니다.

   :::image type="content" source="media/credentials/create-new-linked-service.png" alt-text=" 관리 ID 인증을 사용하는 새 연결된 서비스를 보여주는 스크린샷":::

   :::image type="content" source="media/credentials/linked-service-credential-configuration.png" alt-text="User-Assigned 관리 Id 및 자격 증명이 선택 된 새 연결 된 서비스 구성을 보여 주는 스크린샷":::

# <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

1. Azure에서 만든 사용자 할당 관리 id가 없는 경우 먼저 [관리 되](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.ManagedIdentity%2FuserAssignedIdentities) 는 Azure Portal id 페이지에서 id를 하나 만듭니다.

1. Azure Portal, SDK, PowerShell REST API를 사용 하 여 사용자 할당 관리 id를 작업 영역에 연결 합니다. 아래 스크린샷에서는 Azure Portal (Id 블레이드)를 사용 하 여 사용자 할당 관리 id를 연결 합니다.

   :::image type="content" source="media/credentials/synapse-uami-azure-portal.png" alt-text="Azure Portal을 사용하여 사용자가 할당한 관리 ID를 연결하는 방법을 보여주는 스크린샷":::

1. Synapse Studio에서 대화형으로 **자격 증명** 을 만듭니다. 1 단계에서 작업 영역과 연결 된 사용자 할당 관리 id를 선택할 수 있습니다.

   :::image type="content" source="media/credentials/synapse-create-new-credential.png" alt-text="새 자격 증명을 만드는 방법을 보여 주는 스크린샷":::

   :::image type="content" source="media/credentials/user-assigned-credential-configuration.png" alt-text="새 자격 증명의 구성을 보여 주는 스크린샷":::

1. 새 연결 된 서비스를 만들고 인증에서 **사용자 할당 관리 id** 를 선택 합니다.

   :::image type="content" source="media/credentials/synapse-create-new-linked-service.png" alt-text=" 관리 ID 인증을 사용하는 새 연결된 서비스를 보여주는 스크린샷":::

   :::image type="content" source="media/credentials/linked-service-credential-configuration.png" alt-text="User-Assigned 관리 Id 및 자격 증명이 선택 된 새 연결 된 서비스 구성을 보여 주는 스크린샷":::

---

> [!NOTE] 
> 위의 작업에는 SDK/PowerShell/REST API를 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [관리 ID](data-factory-service-identity.md)

관리 id를 사용 하는 경우 및 방법을 소개 하는 다음 항목을 참조 하세요.

- [Azure Key Vault에 자격 증명 저장](store-credentials-in-key-vault.md)
- [Azure 리소스 인증을 위해 관리 ID를 사용하여 Azure Data Lake Store 간에 데이터 복사](connector-azure-data-lake-store.md)

데이터 팩터리 관리 ID의 기준이 되는 Azure 리소스의 관리 ID에 대한 자세한 내용은 [Azure 리소스에 대한 관리 ID 개요](../active-directory/managed-identities-azure-resources/overview.md)를 참조하세요.

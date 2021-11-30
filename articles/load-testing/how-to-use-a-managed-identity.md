---
title: Azure 부하 테스트에 관리 되는 id 사용
titleSuffix: Azure Load Testing
description: Azure 부하 테스트에 대해 관리 id를 사용 하도록 설정 하 고이를 사용 하 여 Azure Key Vault에서 비밀을 읽는 방법을 알아봅니다.
services: load-testing
ms.service: load-testing
ms.author: ninallam
author: ninallam
ms.date: 11/30/2021
ms.topic: how-to
ms.openlocfilehash: 8de93cae4f3f33778c5fb9064c7fbcc8bf2993a8
ms.sourcegitcommit: 845eb7b0ed05ef2f0dfa6f054eaf5f32c780567c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133305589"
---
# <a name="use-managed-identities-for-azure-load-testing-preview"></a>Azure 부하 테스트 미리 보기에 관리 되는 id 사용

이 항목에서는 Azure 부하 테스트 미리 보기 리소스에 대 한 관리 되는 id를 만드는 방법 및이를 사용 하 여 Azure Key Vault에서 비밀을 읽는 방법을 보여 줍니다.

Azure Active Directory (Azure ad)에서 관리 되는 id를 사용 하면 리소스에서 Azure Key Vault 같은 다른 Azure AD로 보호 되는 리소스에 쉽게 액세스할 수 있습니다. ID는 Azure 플랫폼에서 관리합니다. Azure AD의 관리 ID에 관한 자세한 내용은 [Azure 리소스에 대한 관리 ID](/azure/active-directory/managed-identities-azure-resources/overview)를 참조하세요.

현재 Azure 부하 테스트는 시스템 할당 id만 지원 합니다. 시스템이 할당 한 id는 Azure 부하 테스트 리소스에 연결 되며 리소스를 삭제 하면 삭제 됩니다. 리소스에는 시스템 할당 id가 하나만 있을 수 있습니다.

> [!IMPORTANT]
> Azure 부하 테스트는 현재 미리 보기로 제공 됩니다.
> 베타, 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소  

- 활성 구독이 있는 Azure 계정. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.  

- Azure 부하 테스트 리소스입니다. Azure 부하 테스트 리소스를 만들어야 하는 경우 빠른 시작 [부하 테스트 만들기 및 실행](./quickstart-create-and-run-load-test.md)을 참조 하세요.

## <a name="add-a-system-assigned-identity"></a>시스템 할당 ID 추가  

Azure 부하 테스트에 대 한 시스템 할당 id를 만들려면에서 리소스에 대 한 속성을 설정 해야 합니다. Azure Portal를 사용 하거나 ARM (Azure Resource Manager) 템플릿을 사용 하 여 시스템에 할당 된 id를 사용 하도록 설정할 수 있습니다.

### <a name="set-a-system-assigned-identity-by-using-the-azure-portal"></a>Azure Portal를 사용 하 여 시스템 할당 id 설정

포털에서 관리 id를 설정 하려면 먼저 Azure 부하 테스트 리소스를 만든 다음 기능을 사용 하도록 설정 합니다.

1. [Azure Portal](https://portal.azure.com)에서 Azure 부하 테스트 리소스로 이동 합니다.

1. **ID** 를 선택합니다.

1. 시스템 할당 id를 전환 하 고 **저장** 을 선택 합니다.

    :::image type="content" source="media/how-to-use-a-managed-identity/system-assigned-managed-identity.png" alt-text="Azure 부하 테스트를 위해 시스템 할당 관리 id를 설정 하는 방법을 보여 주는 스크린샷":::

### <a name="set-a-system-assigned-identity-by-using-an-arm-template"></a>ARM 템플릿을 사용 하 여 시스템 할당 id 설정

Azure Resource Manager 템플릿을 사용하여 Azure 리소스 배포를 자동화할 수 있습니다. 모든 `Microsoft.LoadTestService/loadtests` 유형의 리소스는 ID를 사용하여 리소스 정의에 다음 속성을 포함하는 방법으로 만들 수 있습니다.

```json
"identity": {
    "type": "SystemAssigned"
}
```

시스템 할당 형식을 추가 하면 Azure에서 리소스에 대 한 id를 만들고 관리 하 게 됩니다. 예를 들어 Azure 부하 테스트 리소스는 다음과 같습니다.

```json
{
    "type": "Microsoft.LoadTestService/loadtests",
    "apiVersion": "2021-09-01-preview",
    "name": "[parameters('name')]",
    "location": "[parameters('location')]",
    "tags": "[parameters('tags')]",
    "identity": {
        "type": "SystemAssigned"
    }
}
```

리소스를 만들면 다음과 같은 추가 속성을 가져옵니다.

```json
"identity": {
    "type": "SystemAssigned",
    "tenantId": "<TENANTID>",
    "principalId": "<PRINCIPALID>"
}
```

`tenantId`속성은 id가 속하는 AZURE AD 테 넌 트를 식별 합니다. 는 `principalId` 리소스의 새 id에 대 한 고유 식별자입니다. Azure AD 내에서 서비스 주체는 Azure 부하 테스트 리소스와 동일한 이름을 갖습니다.

## <a name="grant-access-to-azure-key-vault"></a>Azure Key Vault에 대 한 액세스 권한 부여

관리 Id를 사용 하면 Azure 부하 테스트 리소스가 다른 Azure 리소스에 액세스할 수 있습니다. 이 섹션에서는 Azure 부하 테스트 서비스에 Azure Key Vault에서 비밀 값을 읽을 수 있는 액세스 권한을 부여 합니다.

아직 Azure Key Vault 없는 경우 [Azure Key Vault 빠른 시작](/azure/key-vault/secrets/quick-create-cli) 을 참조 하 여 만듭니다.

1. Azure Portal에서 Azure Key Vault 리소스를 엽니다.

1. **설정** 에서 **액세스 정책** 을 선택 하 고 **액세스 정책 추가** 를 선택 합니다.

1. **비밀 사용 권한** 에서 **가져오기** 를 선택 합니다.

    :::image type="content" source="media/how-to-use-a-managed-identity/key-vault-add-policy.png" alt-text="Azure Key Vault에 액세스 정책을 추가 하는 방법을 보여 주는 스크린샷":::

1. **보안 주체 선택** 을 선택 하 고 Azure 부하 테스트 리소스 시스템 할당 주체를 선택 합니다.

    시스템에 할당 된 보안 주체의 이름은 Azure 부하 테스트 리소스의 이름과 동일 합니다.

1. **추가** 를 선택합니다.

이제 Azure Key Vault에서 비밀 값을 읽을 수 있도록 Azure 부하 테스트 리소스에 대 한 액세스 권한이 부여 되었습니다.

## <a name="next-steps"></a>다음 단계

- 비밀을 사용 하 여 부하 테스트 매개 변수화, [부하 테스트 매개 변수화](./how-to-parameterize-load-tests.md)를 참조 하세요.

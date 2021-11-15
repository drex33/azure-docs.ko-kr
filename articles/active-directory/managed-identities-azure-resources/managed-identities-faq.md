---
title: Azure 리소스의 관리 ID 자주 묻는 질문 - Azure AD
description: 관리 ID에 관한 자주 묻는 질문
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: conceptual
ms.tgt_pltfrm: ''
ms.workload: identity
ms.date: 10/20/2021
ms.author: barclayn
ms.openlocfilehash: 48d3d5e0e1b6adfb1c0763d1fb7824d604628962
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130266139"
---
# <a name="managed-identities-for-azure-resources-frequently-asked-questions---azure-ad"></a>Azure 리소스의 관리 ID 자주 묻는 질문 - Azure AD

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

> [!NOTE]
> Azure 리소스에 대한 관리 ID는 이전에 MSI(관리 서비스 ID)로 알려진 서비스에 대한 새 이름입니다.

## <a name="administration"></a>관리

### <a name="how-can-you-find-resources-that-have-a-managed-identity"></a>관리 ID가 있는 리소스는 어떻게 찾을 수 있나요?

다음 Azure CLI 명령을 사용하여 시스템이 할당한 관리 ID가 있는 리소스 목록을 찾을 수 있습니다. 

```azurecli-interactive
az resource list --query "[?identity.type=='SystemAssigned'].{Name:name,  principalId:identity.principalId}" --output table
```

### <a name="which-azure-rbac-permissions-are-required-to-use-a-managed-identity-on-a-resource"></a>리소스에서 관리 ID를 사용하기 위한 Azure RBAC 권한은 무엇인가요?

- 시스템이 할당한 관리형 ID: 리소스에 대한 쓰기 권한이 필요합니다. 예를 들어 가상 머신의 경우 `Microsoft.Compute/virtualMachines/write`가 필요합니다. 이 작업은 [Virtual Machine 기여자](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)와 같은 리소스별 기본 제공 역할에 포함됩니다.
- 리소스에 사용자 할당 관리 ID 할당: 리소스에 대한 쓰기 권한이 있어야 합니다. 예를 들어 가상 머신의 경우 `Microsoft.Compute/virtualMachines/write`가 필요합니다. 사용자 할당 ID에 대한 `Microsoft.ManagedIdentity/userAssignedIdentities/*/assign/action` 작업도 필요합니다. 이 작업은 [관리 ID 연산자](../../role-based-access-control/built-in-roles.md#managed-identity-operator) 기본 제공 역할에 포함되어 있습니다.
- 사용자 할당 관리 ID 관리: 사용자 할당 관리 ID를 만들거나 삭제하려면 [관리 ID 기여자](../../role-based-access-control/built-in-roles.md#managed-identity-contributor) 역할 할당이 필요합니다.
- 관리 ID에 대한 역할 할당 관리: 액세스 권한을 부여하는 리소스에 대한 [소유자](../../role-based-access-control/built-in-roles.md#all) 또는 [사용자 액세스 관리자](../../role-based-access-control/built-in-roles.md#all) 역할 할당이 필요합니다. 시스템 할당 ID가 있는 리소스 또는 역할 할당이 제공되는 사용자 할당 ID에 대한 [Reader](../../role-based-access-control/built-in-roles.md#all) 역할 할당이 필요합니다. 읽기 액세스 권한이 없는 경우 역할 할당을 추가하는 동안 관리 ID로 검색하는 대신 "사용자, 그룹 또는 서비스 주체"로 검색하여 ID의 지원 서비스 주체를 찾을 수 있습니다. [Azure 역할 할당에 대해 자세히 알아보세요](../../role-based-access-control/role-assignments-portal.md).

### <a name="how-do-i-prevent-the-creation-of-user-assigned-managed-identities"></a>사용자가 할당한 관리 ID 만들기를 방지하려면 어떻게 해야 하나요?

[Azure Policy](../../governance/policy/overview.md)를 이용하여 사용자 측에서 사용자가 할당한 관리 ID를 만들지 못하게 할 수 있습니다.

1. [Azure Portal](https://portal.azure.com)로 이동하고 **정책** 으로 이동합니다.
2. **정의** 를 선택합니다.
3. **+ 정책 정의** 를 선택하고 필요한 정보를 입력합니다.
4. 정책 규칙 섹션에 다음을 붙여넣습니다.
    
    ```json
    {
      "mode": "All",
      "policyRule": {
        "if": {
          "field": "type",
          "equals": "Microsoft.ManagedIdentity/userAssignedIdentities"
        },
        "then": {
          "effect": "deny"
        }
      },
      "parameters": {}
    }
    
    ```

정책을 만든 후 사용하려는 리소스 그룹에 할당합니다.

1. 리소스 그룹으로 이동합니다.
2. 테스트에 사용할 리소스 그룹을 찾습니다.
3. 왼쪽 메뉴의 **정책** 을 선택합니다.
4. **정책 할당** 을 선택합니다.
5. **기본 사항** 섹션에서 다음을 제공합니다.
    1. **범위**: 테스트에 사용할 리소스 그룹입니다.
    1. **정책 정의**: 이전에 만든 정책입니다.
6. 다른 모든 설정은 기본값으로 유지하고 **검토 + 만들기** 를 선택합니다.

이 시점에서는 리소스 그룹에 사용자가 할당한 관리 ID를 만들 수 없습니다.

  ![정책 위반](./media/known-issues/policy-violation.png)

## <a name="concepts"></a>개념

### <a name="do-managed-identities-have-a-backing-app-object"></a>관리 ID에 지원 앱 개체가 있나요?

아니요. 디렉터리에서 관리 ID와 Azure AD 앱 등록은 동일한 항목이 아닙니다.

앱 등록은 애플리케이션 개체 및 서비스 주체의 두 가지 구성 요소로 이루어집니다.
Azure 리소스의 관리 ID에는 구성 요소 중 하나인 서비스 주체 개체만 있습니다.

관리 ID는 일반적으로 MS Graph의 앱 권한을 부여하는 데 사용되는 애플리케이션 개체가 디렉터리에 없습니다. 대신 관리 ID의 MS Graph 권한을 직접 서비스 주체에 부여해야 합니다.

### <a name="what-is-the-credential-associated-with-a-managed-identity-how-long-is-it-valid-and-how-often-is-it-rotated"></a>관리 ID와 연결된 자격 증명은 무엇인가요? 유효 기간은 얼마나 되며 얼마나 자주 회전하나요?

> [!NOTE]
> 관리 ID 인증 방법은 예고 없이 변경될 수 있는 내부 구현 세부 정보입니다.

관리 ID는 인증서 기반 인증을 사용합니다. 각 관리 ID 자격 증명은 90일 후에 만료되며 45일 후에 롤업됩니다.

### <a name="what-identity-will-imds-default-to-if-dont-specify-the-identity-in-the-request"></a>요청에 ID를 지정하지 않을 경우 IMDS 기본값은 무엇인가요?

- 시스템이 할당한 관리 ID가 사용되지 않고 요청에 ID가 지정되지 않은 경우 IMDS는 기본적으로 시스템이 할당한 관리 ID로 설정됩니다.
- 시스템이 할당한 관리 ID가 사용되지 않고 사용자가 할당한 관리 ID가 하나만 있으면 IMDS는 기본적으로 사용자가 할당한 관리 ID로 설정됩니다.
- 시스템이 할당한 관리 ID가 사용되지 않고 사용자가 할당한 관리 ID가 여러 개 있으면 요청에 관리 ID를 지정해야 합니다.

## <a name="limitations"></a>제한 사항

### <a name="can-the-same-managed-identity-be-used-across-multiple-regions"></a>동일한 관리 ID를 여러 Azure 지역에서 사용할 수 있나요?

간단히 말하자면 그렇습니다. 둘 이상의 Azure 지역에서 사용자가 할당한 관리 ID를 사용할 수 있습니다. 자세히 말하면, 사용자가 할당한 관리 ID는 지역 리소스로 만들지만 Azure AD에서 만든 연결된 SP([서비스 주체](../develop/app-objects-and-service-principals.md#service-principal-object))는 전역적으로 사용할 수 있습니다. 모든 Azure 지역에서 서비스 주체를 사용할 수 있으며 해당 가용성은 Azure AD의 가용성에 따라 달라집니다. 예를 들어, 사용자가 할당한 관리 ID를 중남부 지역에서 만들었고 해당 지역을 사용할 수 없게 되면 관련 문제는 관리 ID 자체의 [컨트롤 플레인](../../azure-resource-manager/management/control-plane-and-data-plane.md) 활동에만 영향을 줍니다.  관리 ID를 사용하도록 이미 구성된 리소스에서 수행하는 활동은 영향을 받지 않습니다.

### <a name="does-managed-identities-for-azure-resources-work-with-azure-cloud-services"></a>Azure 리소스에 대한 관리 ID가 Azure Cloud Services에서 작동하나요?

아니요. Azure 리소스에 대한 관리 ID는 Azure Cloud Services에서 지원될 예정이 없습니다.


### <a name="what-is-the-security-boundary-of-managed-identities-for-azure-resources"></a>Azure 리소스에 대한 관리 ID의 보안 경계란 무엇인가요?

ID의 보안 경계는 연결되는 리소스입니다. 예를 들어, Azure 리소스에 대한 관리 ID를 갖는 가상 머신의 보안 경계는 바로 가상 머신입니다. 해당 VM에서 실행되는 모든 코드는 Azure 리소스에 대한 관리 ID 엔드포인트 및 요청 토큰을 호출할 수 있습니다. 이것은 Azure 리소스에 대한 관리 ID를 지원하는 다른 리소스와 비슷한 환경입니다.

### <a name="will-managed-identities-be-recreated-automatically-if-i-move-a-subscription-to-another-directory"></a>구독을 다른 디렉터리로 이동하면 관리 ID가 자동으로 다시 생성되나요?

아니요. 구독을 다른 디렉터리로 이동하면 관리 ID를 수동으로 다시 만들고 다시 Azure 역할 할당을 부여해야 합니다.
- 시스템에서 할당된 관리 ID: 비활성화하거나 재활성화합니다. 
- 사용자 할당 관리 ID: 삭제한 후 다시 만들어서 필요한 리소스(예: 가상 머신)에 다시 연결합니다.

### <a name="can-i-use-a-managed-identity-to-access-a-resource-in-a-different-directorytenant"></a>관리 ID를 사용하여 다른 디렉터리/테넌트의 리소스에 액세스할 수 있나요?

아니요. 관리 ID는 현재 교차 디렉터리 시나리오를 지원하지 않습니다. 

### <a name="are-there-any-rate-limits-that-apply-to-managed-identities"></a>관리 ID에 적용되는 요금 한도가 있나요?

관리 ID 한도는 Azure 서비스 한도, Azure Instance Metadata Service(IMDS) 한도 및 Azure Active Directory 서비스 한도에 따라 달라집니다.

- **Azure 서비스 한도** 는 테넌트 및 구독 수준에서 수행할 수 있는 만들기 작업 수를 정의합니다. 사용자가 할당한 관리 ID의 이름을 지정할 수 있는 방법에도 [제한 사항](../../azure-resource-manager/management/azure-subscription-service-limits.md#managed-identity-limits)이 있습니다.
- **IMDS**: 일반적으로 IMDS에 대한 요청은 초당 5개 요청으로 제한됩니다. 이 임계값을 초과하는 요청은 429 응답으로 거부됩니다. 관리 ID 범주에 대한 요청은 초당 20개 요청 및 5개 동시 요청으로 제한됩니다. [Azure Instance Metadata Service(Windows)](../../virtual-machines/windows/instance-metadata-service.md?tabs=windows#managed-identity) 문서에서 자세히 알아볼 수 있습니다.
- **Azure Active Directory 서비스** 각 관리 ID는 Azure [AD 서비스 한도 및 제한 사항](../enterprise-users/directory-service-limits-restrictions.md)에 설명된 대로 Azure AD 테넌트에서 개체 할당량 한도를 계산합니다.


### <a name="is-it-possible-to-move-a-user-assigned-managed-identity-to-a-different-resource-groupsubscription"></a>사용자가 할당한 관리 ID를 다른 리소스 그룹/구독으로 이동할 수 있나요?

사용자가 할당한 관리 ID를 다른 리소스 그룹으로 이동하는 것은 지원되지 않습니다.

## <a name="next-steps"></a>다음 단계

- [관리 ID가 가상 머신에서 작동하는 방식](how-managed-identities-work-vm.md)을 알아봅니다.

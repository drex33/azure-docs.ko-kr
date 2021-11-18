---
title: Azure 역할 할당 조건 문제 해결(미리 보기)
description: Azure 역할 할당 조건 문제 해결(미리 보기)
services: active-directory
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.subservice: conditions
ms.topic: troubleshooting
ms.workload: identity
ms.date: 11/16/2021
ms.author: rolyon
ms.openlocfilehash: 454b3262e4de5ec857368599f441159707f66cc0
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132751723"
---
# <a name="troubleshoot-azure-role-assignment-conditions-preview"></a>Azure 역할 할당 조건 문제 해결(미리 보기)

> [!IMPORTANT]
> Azure ABAC 및 Azure 역할 할당 조건은 현재 미리 보기 상태입니다.
> 이 미리 보기 버전은 서비스 수준 계약 없이 제공되며 프로덕션 워크로드에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다.
> 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="symptom---condition-is-not-enforced"></a>증상 - 조건이 적용되지 않음

**원인 1**

동일하거나 더 높은 범위에서 보안 주체에 역할 할당이 하나 이상 있습니다.

**해결 방법 1**

동일한 데이터 작업에 대한 액세스 권한을 부여하는 여러 역할 할당(조건 유무에 관계없이)이 보안 주체에 없어 조건이 적용되지 않는지 확인합니다. 평가 논리에 대한 자세한 내용은 [Azure RBAC가 리소스에 대한 액세스 권한이 사용자에게 있는지 확인하는 방법](overview.md#how-azure-rbac-determines-if-a-user-has-access-to-a-resource)을 참조하세요.

**원인 2**

역할 할당에 권한을 부여하는 작업이 여러 개 있으며 조건에서 모든 작업을 대상으로 지정하지 않습니다. 예를 들어 `/blobs/write` 또는 `/blobs/add/action` 데이터 작업이 있는 경우 BLOB을 만들 수 있습니다. 역할 할당에 데이터 작업 2개가 모두 있고 한 조건에서 데이터 작업 중 하나만 대상으로 지정하는 경우 역할 할당은 BLOB을 만들고 조건을 무시할 수 있는 권한을 부여합니다.

**해결 방법 2**

역할 할당에 권한을 부여하는 작업이 여러 개 있는 경우 모든 관련 작업을 대상으로 지정해야 합니다.

**원인 3**

역할 할당에 조건을 추가하면 조건이 적용되는 데 최대 5분이 걸릴 수 있습니다. 조건을 추가하면 리소스 공급자(예: Microsoft.Storage)에 업데이트를 알립니다. 리소스 공급자는 로컬 캐시를 즉시 업데이트하여 최신 역할 할당이 있는지 확인합니다. 이 프로세스는 1~2분 이내에 완료되지만 최대 5분이 걸릴 수 있습니다.

**해결 방법 3**

5분 동안 기다린 다음, 조건을 다시 테스트합니다.

## <a name="symptom---condition-is-not-valid-error-when-adding-a-condition"></a>증상 - 조건을 추가할 때 조건이 유효하지 않음 오류

조건을 사용하여 역할 할당을 추가하려고 할 때 다음과 유사한 오류가 발생합니다.

`The given role assignment condition is invalid.`

**원인**

조건 형식이 올바르게 지정되지 않았습니다. 

**해결 방법**

[조건 형식 또는 구문](conditions-format.md) 이슈를 해결합니다. 또는 [Azure Portal의 시각적 개체 편집기](conditions-role-assignments-portal.md)를 사용하여 조건을 추가합니다.

## <a name="symptom---principal-does-not-appear-in-attribute-source-when-adding-a-condition"></a>증상 - 조건을 추가할 때 보안 주체가 특성 원본에 나타나지 않음

조건을 사용하여 역할 할당을 추가하려고 하면 **보안 주체가** **특성 원본** 목록에 나타나지 않습니다.

![조건을 추가할 때 특성 원본 목록의 보안 주체를 보여주는 스크린샷.](./media/conditions-troubleshoot/condition-principal-attribute-source.png)

대신 다음과 같은 메시지가 표시됩니다.

보안 주체(사용자) 특성을 사용하려면 Azure AD Premium P1 또는 P2 라이선스, Azure AD 권한(예: [특성 할당 관리자](../active-directory/roles/permissions-reference.md#attribute-assignment-administrator) 역할) 및 Azure AD에 정의된 사용자 지정 보안 특성이 모두 있어야 합니다.

![조건을 추가할 때의 주 메시지 스크린샷](./media/conditions-troubleshoot/condition-principal-attribute-message.png)

**원인**

필수 구성 조건이 충족되지 않습니다. 보안 주체 특성을 사용하려면 **다음이 모두** 있어야 합니다.

- Azure AD Premium P1 또는 P2 라이선스
- [로그인한](../active-directory/roles/permissions-reference.md#attribute-assignment-administrator) 사용자에 대한 Azure AD 권한(예: 특성 할당 관리자 역할)
- Azure AD에 정의된 사용자 지정 보안 특성

> [!IMPORTANT]
> 기본적으로 [전역 관리자](../active-directory/roles/permissions-reference.md#global-administrator) 및 기타 관리자 역할에는 사용자 지정 보안 특성을 읽거나 정의하거나 할당할 수 있는 권한이 없습니다.

**해결 방법**

1. **Azure Active Directory**  >  **개요를** 열고 테넌트 라이선스를 확인합니다.

1. **Azure Active Directory**  >  **사용자**  >  *사용자 이름*  >  **할당된 역할을** 열고 특성 할당 관리자 역할이 할당되었는지 확인합니다. 그렇지 않은 경우 Azure AD 관리자에게 이 역할을 할당하도록 요청합니다. 자세한 내용은 [사용자에게 Azure AD 역할 할당을 참조하세요.](../active-directory/roles/manage-roles-portal.md)

1. 사용자 지정 보안 특성 **Azure Active Directory** 열어 사용자 지정 보안  >  **특성이** 정의되었는지, 어떤 특성에 액세스할 수 있는지 확인합니다. 사용자 지정 보안 특성이 표시되지 않으면 Azure AD 관리자에게 관리할 수 있는 특성 집합을 추가하도록 요청합니다. 자세한 내용은 [Azure AD에서 사용자 지정 보안 특성에 대한 액세스 관리 및 Azure AD에서](../active-directory/fundamentals/custom-security-attributes-manage.md) 사용자 지정 보안 특성 추가 또는 [비활성화를](../active-directory/fundamentals/custom-security-attributes-add.md)참조하세요.

## <a name="symptom---principal-does-not-appear-in-attribute-source-when-adding-a-condition-using-pim"></a>증상 - PIM을 사용하여 조건을 추가할 때 특성 원본에 보안 주체가 표시되지 않음 

[PIM(Azure AD Privileged Identity Management)을](../active-directory/privileged-identity-management/pim-configure.md)사용하여 조건에 따라 역할 할당을 추가하려고 하면 **보안** **주체가** 특성 원본 목록에 표시되지 않습니다.

![Privileged Identity Management 사용하여 조건을 추가할 때 특성 원본 목록의 보안 주체를 보여주는 스크린샷](./media/conditions-troubleshoot/condition-principal-attribute-source.png)

**원인**

PIM은 현재 역할 할당 조건에서 보안 주체 특성을 사용할 수 없습니다.

## <a name="symptom---resource-attribute-is-not-valid-error-when-adding-a-condition-using-azure-powershell"></a>증상 - Azure PowerShell을 사용하여 조건을 추가할 때 리소스 특성이 잘못됨 오류

Azure PowerShell을 사용하여 조건이 있는 역할 할당을 추가하려고 할 때 다음과 유사한 오류가 발생합니다.

```
New-AzRoleAssignment : Resource attribute
Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<$> is not valid.
```

**원인**

조건에 달러 기호($)가 포함된 경우에는 앞에 백틱(\`)을 붙여야 합니다.

**해결 방법**

각 달러 기호 앞에 백틱(\`)을 추가합니다. 예제는 다음과 같습니다. PowerShell의 따옴표 규칙에 대한 자세한 내용은 [따옴표 규칙 정보](/powershell/module/microsoft.powershell.core/about/about_quoting_rules)를 참조하세요.

```azurepowershell
$condition = "((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'} AND SubOperationMatches{'Blob.Read.WithTagConditions'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<`$key_case_sensitive`$>] StringEquals 'Cascade'))"
```

## <a name="symptom---resource-attribute-is-not-valid-error-when-adding-a-condition-using-azure-cli"></a>증상 - Azure CLI를 사용하여 조건을 추가할 때 리소스 특성이 잘못됨 오류

Azure CLI를 사용하여 조건이 있는 역할 할당을 추가하려고 할 때 다음과 유사한 오류가 발생합니다.

```
Resource attribute Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<$> is not valid.
```

**원인**

조건에 달러 기호($)가 포함된 경우에는 앞에 백슬래시(\\)를 붙여야 합니다.

**해결 방법**

각 달러 기호 앞에 백슬래시(\\)를 추가합니다. 예제는 다음과 같습니다. Bash의 따옴표 규칙에 대한 자세한 내용은 [큰따옴표](https://www.gnu.org/software/bash/manual/html_node/Double-Quotes.html)를 참조하세요.

```azurecli
condition="((!(ActionMatches{'Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read'} AND SubOperationMatches{'Blob.Read.WithTagConditions'})) OR (@Resource[Microsoft.Storage/storageAccounts/blobServices/containers/blobs/tags:Project<\$key_case_sensitive\$>] StringEquals 'Cascade'))"
```

## <a name="symptom---error-when-assigning-a-condition-string-to-a-variable-in-bash"></a>증상 - 조건 문자열을 Bash의 변수에 할당할 때 발생하는 오류

Bash의 변수에 조건 문자열을 할당하려고 하면 `bash: !: event not found` 메시지가 표시됩니다.

**원인**

Bash에서 기록 확장을 사용하는 경우 느낌표(!)로 인해 메시지 `bash: !: event not found`가 표시될 수 있습니다.

**해결 방법**

`set +H` 명령으로 기록 확장을 사용하지 마세요. 기록 확장을 다시 사용하도록 설정하려면 `set -H`를 사용합니다.

## <a name="symptom---unrecognized-arguments-error-when-adding-a-condition-using-azure-cli"></a>증상 - Azure CLI를 사용하여 조건을 추가할 때 인식할 수 없는 인수 오류

Azure CLI를 사용하여 조건이 있는 역할 할당을 추가하려고 할 때 다음과 유사한 오류가 발생합니다.

`az: error: unrecognized arguments: --description {description} --condition {condition} --condition-version 2.0`

**원인**

역할 할당 조건 매개 변수를 지원하지 않는 이전 버전의 Azure CLI를 사용하고 있을 가능성이 높습니다.

**해결 방법**

최신 버전의 Azure CLI(2.18 이상)로 업데이트합니다. 자세한 내용은 [Azure CLI 설치](/cli/azure/install-azure-cli)를 참조하세요.

## <a name="symptom---condition-not-recognized-in-visual-editor"></a>증상 - 시각적 편집기에서 조건이 인식되지 않음

코드 편집기를 사용한 후 시각적 편집기로 전환하고 다음과 유사한 메시지가 표시됩니다.

`The current expression cannot be recognized. Switch to the code editor to edit the expression or delete the expression and add a new one.`

**원인**

시각적 편집기에서 구문 분석할 수 없는 조건이 업데이트되었습니다.

**해결 방법**

[조건 형식 또는 구문](conditions-format.md) 이슈를 해결합니다. 또는 조건을 삭제하고 다시 시도해 봅니다.

## <a name="symptom---error-when-copying-and-pasting-a-condition"></a>증상 - 조건을 복사하여 붙여넣을 때 오류 발생

**원인**

문서에서 조건을 복사하는 경우 특수 문자가 포함되어 오류가 발생할 수 있습니다. 일부 편집기(예: Microsoft Word)에서 표시되지 않는 텍스트의 서식을 지정할 때 제어 문자를 추가합니다.

**해결 방법**

서식 있는 텍스트 편집기에서 조건을 복사 했 고 조건이 정확한 경우 모든 공백을 삭제 하 고를 반환 하 고 나 서 관련 공간을 다시 추가 합니다. 또는 일반 텍스트 편집기나 Visual Studio Code와 같은 코드 편집기를 사용 합니다.

## <a name="next-steps"></a>다음 단계

- [Azure 역할 할당 조건 형식 및 구문(미리 보기)](conditions-format.md)
- [Azure 역할 할당 조건에 대한 FAQ(미리 보기)](conditions-faq.md)
- [Azure AD에서 사용자 지정 보안 특성 문제 해결 (미리 보기)](../active-directory/fundamentals/custom-security-attributes-troubleshoot.md)

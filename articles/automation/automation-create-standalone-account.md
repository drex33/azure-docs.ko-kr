---
title: 독립 실행형 Azure Automation 계정 만들기
description: 이 문서에서는 독립 실행형 Azure Automation 계정을 만드는 방법을 설명합니다.
services: automation
ms.subservice: process-automation
ms.date: 10/26/2021
ms.topic: conceptual
ms.openlocfilehash: eedee6af181a0dc99635dc455dfda6a154fe5eb9
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131451063"
---
# <a name="create-a-standalone-azure-automation-account"></a>독립 실행형 Azure Automation 계정 만들기

이 문서에서는 Azure Portal에서 Azure Automation 계정을 만드는 방법을 보여줍니다. Automation 계정을 사용하여 Azure Monitor 로그에서 추가 관리 기능 또는 통합을 사용하지 않고 Automation에 대해 평가하고 자세히 알아볼 수 있습니다. 나중에 언제든지 Runbook 작업에 고급 모니터링을 사용하기 위해 해당 관리 기능을 추가하거나 Azure Monitor 로그를 통합할 수 있습니다.

Automation 계정에서는 Azure Resource Manager 또는 클래식 배포 모델에서 리소스를 관리하여 Runbook을 인증할 수 있습니다. 하나의 Automation 계정으로 해당 테넌트의 모든 영역과 구독 전반의 리소스를 관리할 수 있습니다.

이 계정을 만들면 Runbook을 신속하게 빌드하고 배포하기 시작하여 자동화 요구 사항을 지원할 수 있습니다.

## <a name="permissions-required-to-create-an-automation-account"></a>Automation 계정을 만드는 데 필요한 사용 권한

Automation 계정을 만들거나 업데이트하고 이 문서에서 설명한 작업을 완료하려면 다음과 같은 권한이 있어야 합니다.

Automation 계정을 만들려면 Azure AD 사용자 계정을 `Microsoft.Automation` 리소스에 대한 소유자 역할과 동일한 권한이 있는 역할에 추가해야 합니다. 자세한 내용은 [Azure Automation의 역할 기반 액세스 제어](automation-role-based-access-control.md)를 참조하세요.

## <a name="create-a-new-automation-account-in-the-azure-portal"></a>Azure Portal에서 새 Automation 계정 만들기

Azure Portal에서 Azure Automation 계정을 만들려면 다음 단계를 완료합니다.

1. 구독 관리자 역할의 멤버 및 구독의 공동 관리자인 계정으로 Azure Portal에 로그인합니다.
1. **+ 리소스 만들기** 를 선택합니다.
1. **Automation** 을 검색합니다. 검색 결과에서 **Automation** 을 선택합니다.

   :::image type="content" source="./media/automation-create-standalone-account/automation-account-portal.png" alt-text="포털에서 Automation 계정 찾기":::

새 Automation 계정에 대한 옵션은 Automation 계정 **만들기** 페이지의 탭으로 구성됩니다. 다음 섹션에서는 각 탭 및 해당 옵션에 대해 설명합니다.

### <a name="basics"></a>기본

기본 **사항** 탭에서 Automation 계정에 대한 필수 정보를 제공합니다. **기본 탭을** 완료한 후 다른 탭에서 옵션을 설정하여 새 Automation 계정을 추가로 사용자 지정하도록 선택하거나 **검토 + 만들기를** 선택하여 기본 옵션을 적용하고 계정의 유효성을 검사하고 만들 수 있습니다.

> [!NOTE]
> 기본적으로 시스템 할당 관리 ID는 Automation 계정에 대해 사용하도록 설정됩니다.

다음 표에서는 **기본 사항** 탭의 필드에 대해 설명합니다.

| **필드** | **필수**<br> **or**<br> **선택적** |**설명** |
|---|---|---|
|Subscription|필수 |드롭다운 목록에서 계정에 대한 Azure 구독을 선택합니다.|
|Resource group|필수 |드롭다운 목록에서 기존 리소스 그룹을 선택하거나 **새로 만들기** 를 선택합니다.|
|Automation 계정 이름|필수 |위치 및 리소스 그룹에 고유한 이름을 입력합니다. 삭제된 Automation 계정의 이름은 즉시 사용할 수 없습니다. 사용자 인터페이스에 입력된 계정 이름은 변경할 수 없습니다. |
|지역|필수 |드롭다운 목록에서 계정의 지역을 선택합니다. Automation 계정을 배포할 수 있는 위치의 업데이트된 목록은 [지역별로 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/?products=automation&regions=all)을 참조하세요.|

다음 이미지는 새 Automation 계정에 대한 표준 구성을 보여 줍니다.

:::image type="content" source="./media/automation-create-standalone-account/create-account-basics.png" alt-text="기본 탭에서 Automation 계정을 만드는 데 필요한 필드":::

### <a name="advanced"></a>고급

**고급** 탭에서 새 Automation 계정에 대한 관리 ID 옵션을 구성할 수 있습니다. Automation 계정을 만든 후에 사용자 할당 관리 ID 옵션을 구성할 수도 있습니다.

사용자 할당 관리 ID를 만드는 방법에 대한 지침은 [사용자 할당 관리 ID 만들기를 참조하세요.](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)

다음 표에서는 **고급** 탭의 필드에 대해 설명합니다.

| **필드** | **필수**<br> **or**<br> **선택적** |**설명** |
|---|---|---|
|시스템 할당 |선택 사항 |Automation 계정의 수명 주기에 연결된 Azure Active Directory ID입니다. |
|사용자 할당 |선택 사항 |관리 ID는 사용하는 리소스와 별도로 관리되는 독립 실행형 Azure 리소스로 표시됩니다.|

나중에 관리 ID를 사용하도록 선택할 수 있으며 Automation 계정은 하나도 없이 만들어집니다. 계정을 만든 후 관리 ID를 사용하도록 설정하려면 [관리 ID 사용을 참조하세요.](enable-managed-identity-for-automation.md) 두 옵션을 모두 선택하는 경우 사용자 할당 ID에 대해 **사용자 할당 ID 추가** 옵션을 선택합니다. 사용자 **할당 관리 ID 선택** 페이지에서 구독을 선택하고 해당 구독에서 만든 하나 이상의 사용자 할당 ID를 추가하여 Automation 계정에 할당합니다.

다음 이미지는 새 Automation 계정에 대한 표준 구성을 보여 줍니다.

:::image type="content" source="./media/automation-create-standalone-account/create-account-advanced.png" alt-text="고급 탭에서 Automation 계정을 만드는 데 필요한 필드":::

### <a name="tags-tab"></a>태그 탭

**태그** 탭에서 Resource Manager 태그를 지정하여 Azure 리소스를 편리하게 구성할 수 있습니다. 자세한 내용은 [논리적 조직의 리소스, 리소스 그룹 및 구독에 태그 지정](../azure-resource-manager/management/tag-resources.md)을 참조하세요.

### <a name="review--create-tab"></a>리뷰 + 만들기 탭

**검토 + 만들기** 탭으로 이동하면 Azure는 선택한 Automation 계정 설정에서 유효성 검사를 실행합니다. 유효성 검사를 통과하면 Automation 계정 만들기를 진행할 수 있습니다.

유효성 검사에 실패하면 Portal에 수정해야 하는 설정이 표시됩니다.

새 Automation 계정을 검토합니다.

:::image type="content" source="./media/automation-create-standalone-account/automation-account-overview.png" alt-text="Automation 계정 개요 페이지":::

Automation 계정이 성공적으로 만들어지면 몇 가지 리소스가 자동으로 만들어집니다. 이러한 Runbook을 만든 후 유지하지 않으려는 경우 안전하게 삭제할 수 있습니다. 관리 ID는 Runbook에서 계정에 인증하는 데 사용할 수 있으며, 다른 ID를 만들거나 필요하지 않은 한 남아 있어야 합니다. 다음 표에서는 계정에 대한 리소스를 요약합니다.

| 리소스 | 설명 | |------|| ------| | AzureAutomationTutorial Runbook | 실행 계정을 사용하여 인증하는 방법을 보여 주는 예제 그래픽 Runbook입니다. Runbook은 Resource Manager 리소스를 모두 가져옵니다. | | AzureAutomationTutorialScript | 실행 계정을 사용하여 인증하는 방법을 보여 주는 예제 PowerShell Runbook입니다. Runbook은 모든 Resource Manager resources.| | AzureAutomationTutorialPython2Runbook | 실행 계정을 사용하여 인증하는 방법을 보여 주는 예제 Python Runbook입니다. Runbook에는 subscription.| 있는 모든 리소스 그룹이 나열됩니다.

> [!NOTE]
> 관리 ID를 사용하여 인증하도록 자습서 Runbook이 업데이트되지 않았습니다. 시스템 [할당 ID 사용](enable-managed-identity-for-automation.md#give-access-to-azure-resources-by-obtaining-a-token) 또는 [사용자 할당 ID 사용을](add-user-assigned-identity.md#give-identity-access-to-azure-resources-by-obtaining-a-token) 검토하여 리소스에 대한 관리 ID 액세스 권한을 부여하고 관리 ID 유형 중 하나를 사용하여 인증하도록 Runbook을 구성하는 방법을 알아봅니다.

## <a name="next-steps"></a>다음 단계

* PowerShell Runbook을 시작하려면 [자습서: PowerShell Runbook 만들기](./learn/powershell-runbook-managed-identity.md)를 참조하세요.
* PowerShell 워크플로 Runbook을 시작하려면 [자습서: PowerShell 워크플로 Runbook 만들기](learn/automation-tutorial-runbook-textual.md)를 참조하세요.
* Python 3 Runbook을 시작하려면 [자습서: Python 3 Runbook 만들기](learn/automation-tutorial-runbook-textual-python-3.md)를 참조하세요.
* PowerShell cmdlet 참조는 [Az.Automation](/powershell/module/az.automation)을 참조하세요.
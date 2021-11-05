---
title: 빠른 시작 - 포털을 사용하여 Azure Automation 계정 만들기
description: 이 빠른 시작은 포털을 사용하여 Azure Automation 계정 만들기를 시작하는 데 도움이 됩니다.
services: automation
ms.date: 10/26/2021
ms.topic: quickstart
ms.subservice: process-automation
ms.custom: mvc
ms.openlocfilehash: 7497362ccd71e6efabc873015796855bd989d33c
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131470688"
---
# <a name="quickstart-create-an-automation-account-using-the-azure-portal"></a>빠른 시작: Azure Portal을 사용하여 Azure Automation 계정 만들기

여러 리소스에 대한 액세스를 허용하는 브라우저 기반 사용자 인터페이스인 Azure Portal을 사용하여 Azure [Automation 계정](../automation-security-overview.md)을 만들 수 있습니다. 하나의 Automation 계정으로 해당 테넌트의 모든 영역과 구독 전반의 리소스를 관리할 수 있습니다. 이 빠른 시작은 Automation 계정을 만드는 방법을 안내합니다.

## <a name="prerequisites"></a>필수 구성 요소

활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-automation-account"></a>Automation 계정 만들기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. 위쪽 메뉴에서 **+ 리소스 만들기** 를 선택합니다.

1. **범주** 에서 **IT 및 관리 도구** 를 선택한 다음, **자동화** 를 선택합니다.

   :::image type="content" source="./media/create-account-portal/automation-account-portal.png" alt-text="포털에서 Automation 계정 찾기.":::

새 Automation 계정에 대한 옵션은 **Automation 계정 만들기** 페이지에 탭으로 구성됩니다. 다음 섹션에서는 각 탭 및 해당 옵션에 대해 설명합니다.

### <a name="basics"></a>기본

**기본 사항** 탭에서 Automation 계정에 대한 필수 정보를 제공합니다. **기본 사항** 탭을 완료한 후 다른 탭에서 옵션을 설정하여 새 Automation 계정을 추가로 사용자 지정하도록 선택하거나 **검토 + 만들기** 를 선택하여 기본 옵션을 적용하고 계정이 유효한지 검사한 후 만들 수 있습니다.

> [!NOTE]
> 기본적으로 시스템이 할당한 관리 ID는 Automation 계정에 대해 사용하도록 설정됩니다.

다음 표에서는 **기본 사항** 탭의 필드에 대해 설명합니다.

| **필드** | **필수**<br> **or**<br> **선택 사항** |**설명** |
|---|---|---|
|Subscription|필수 |드롭다운 목록에서 계정에 대한 Azure 구독을 선택합니다.|
|Resource group|필수 |드롭다운 목록에서 기존 리소스 그룹을 선택하거나 **새로 만들기** 를 선택합니다.|
|Automation 계정 이름|필수 |위치 및 리소스 그룹에 고유한 이름을 입력합니다. 삭제된 Automation 계정의 이름은 즉시 사용할 수 없습니다. 사용자 인터페이스에 입력된 계정 이름은 변경할 수 없습니다. |
|지역|필수 |드롭다운 목록에서 계정의 지역을 선택합니다. Automation 계정을 배포할 수 있는 위치의 업데이트된 목록은 [지역별로 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/?products=automation&regions=all)을 참조하세요.|

다음 이미지는 새 Automation 계정에 대한 표준 구성을 보여 줍니다.

:::image type="content" source="./media/create-account-portal/create-account-basics.png" alt-text="기본 탭에서 Automation 계정을 만드는 데 필요한 필드":::

### <a name="advanced"></a>고급

**고급** 탭에서 새 Automation 계정에 대한 관리 ID 옵션을 구성할 수 있습니다. Automation 계정을 만든 후에 사용자가 할당한 관리 ID 옵션을 구성할 수도 있습니다.

사용자가 할당 관리 ID를 만드는 방법에 대한 지침은 [사용자가 할당한 관리 ID 만들기](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)를 참조하세요.

다음 표에서는 **고급** 탭의 필드에 대해 설명합니다.

| **필드** | **필수**<br> **or**<br> **선택 사항** |**설명** |
|---|---|---|
|시스템 할당 |선택 사항 |Automation 계정의 수명 주기에 연결된 Azure Active Directory ID입니다. |
|사용자 할당 |선택 사항 |관리 ID는 사용하는 리소스와 별도로 관리되는 독립 실행형 Azure 리소스로 표시됩니다.|

나중에 관리 ID를 사용하도록 선택할 수 있으며 Automation 계정은 하나도 없이 만들어집니다. 계정을 만든 후 관리 ID를 사용하도록 설정하려면 [관리 ID 사용](enable-managed-identity.md)을 참조하세요. 두 옵션을 모두 선택하는 경우 사용자 할당 ID에 대해 **사용자 할당 ID 추가** 옵션을 선택합니다. **사용자가 할당한 관리 ID 선택** 페이지에서 구독을 선택하고 해당 구독에서 만든 하나 이상의 사용자 할당 ID를 추가하여 Automation 계정에 할당합니다.

다음 이미지는 새 Automation 계정에 대한 표준 구성을 보여 줍니다.

:::image type="content" source="./media/create-account-portal/create-account-advanced.png" alt-text="고급 탭에서 Automation 계정을 만드는 데 필요한 필드":::

### <a name="tags-tab"></a>태그 탭

**태그** 탭에서 Resource Manager 태그를 지정하여 Azure 리소스를 편리하게 구성할 수 있습니다. 자세한 내용은 [논리적 조직의 리소스, 리소스 그룹 및 구독에 태그 지정](../../azure-resource-manager/management/tag-resources.md)을 참조하세요.

### <a name="review--create-tab"></a>리뷰 + 만들기 탭

**검토 + 만들기** 탭으로 이동하면 Azure는 선택한 Automation 계정 설정에 대해 유효성 검사를 실행합니다. 유효성 검사를 통과하면 Automation 계정 만들기를 진행할 수 있습니다.

유효성 검사에 실패하면 Portal에 수정해야 하는 설정이 표시됩니다.

새 Automation 계정을 검토합니다.

:::image type="content" source="./media/create-account-portal/automation-account-overview.png" alt-text="Automation 계정 개요 페이지":::

## <a name="clean-up-resources"></a>리소스 정리

Automation 계정을 계속 사용하지 않으려면 **개요** 페이지에서 **삭제** 를 선택한 다음, 메시지가 표시되면 **예** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Automation 계정을 만들었습니다. Automation 계정에서 관리 ID를 사용하려면 다음 빠른 시작을 계속 진행합니다.

> [!div class="nextstepaction"]
> [자습서 - 관리 ID를 사용하여 Automation PowerShell Runbook 만들기](../learn/powershell-runbook-managed-identity.md)
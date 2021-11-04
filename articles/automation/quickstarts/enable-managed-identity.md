---
title: 빠른 시작 - Azure Portal을 사용하여 Automation 계정에 대한 관리 ID를 사용하도록 설정합니다.
description: 이 빠른 시작은 Azure Portal을 사용하여 Automation 계정에 대한 관리 ID를 사용하도록 설정하는 데 도움이 됩니다.
services: automation
ms.date: 09/07/2021
ms.topic: quickstart
ms.subservice: process-automation
ms.openlocfilehash: 13b314e5f73a0c970e28dfdd87551d713d60175b
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131462124"
---
# <a name="quickstart-enable-managed-identities-for-your-automation-account-using-the-azure-portal"></a>빠른 시작: Azure Portal을 사용하여 Automation 계정에 대한 관리 ID를 사용하도록 설정

이 빠른 시작에서는 Azure Automation 계정에 대해 관리 ID를 사용하도록 설정하는 방법을 보여줍니다. 관리 ID가 Azure Automation에서 작동하는 방법에 대한 자세한 내용은 [관리 ID](../automation-security-overview.md#managed-identities)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- Azure Automation 계정. 자세한 내용은 [Automation 계정 만들기](create-account-portal.md)를 참조하세요.

- 사용자가 할당한 관리 ID. 자세한 내용은 [사용자 할당 관리 ID 만들기](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)을 참조하세요. Runbook에서 해당 ID를 사용하여 관리하는 사용자 할당 관리 ID와 대상 Azure 리소스가 같은 Azure 구독에 있어야 합니다.

## <a name="enable-system-assigned-managed-identity"></a>시스템이 할당한 관리 ID 사용

1. [Azure Portal](https://portal.azure.com)에 로그인하고 Azure Automation 계정으로 이동합니다.

1.  **계정 설정** 에서  **ID(미리 보기)** 를 선택합니다.

   :::image type="content" source="media/enable-managed-identity/managed-identity-portal.png" alt-text="포털에서 ID로 이동.":::

1. 시스템 할당 **상태** 옵션을 **켜기** 로 설정한 다음, **저장** 을 누릅니다. 확인하라는 메시지가 나타나면 **예** 를 선택합니다.

   이제 Automation 계정에서 시스템이 할당한 ID를 사용할 수 있습니다. ID는 Azure Active Directory(Azure AD)에 등록되고 개체 ID로 표시됩니다.

   :::image type="content" source="media/enable-managed-identity/system-assigned-object-id.png" alt-text="관리 ID 개체 ID.":::

## <a name="add-user-assigned-managed-identity"></a>사용자가 할당한 관리 ID 추가

이 섹션은 마지막 섹션이 끝난 부분부터 계속됩니다.

1. **사용자 할당** 탭을 선택한 다음, **+ 추가** 또는 **사용자 할당 관리 ID 추가** 를 선택하여 **사용자 할당 관리 ID 추가...** 페이지를 엽니다.

   :::image type="content" source="media/enable-managed-identity/user-assigned-portal.png" alt-text="포털의 사용자 할당 탭.":::

1. **구독** 드롭다운 목록에서 사용자가 할당한 관리 ID에 대한 구독을 선택합니다.

   :::image type="content" source="media/enable-managed-identity/add-user-assigned.png" alt-text="포털에 사용자 할당 페이지를 추가합니다.":::

1. **사용자 할당 관리 ID** 에서 기존 사용자 할당 관리 ID를 선택한 다음, **추가** 를 선택합니다. 그러면 **사용자 할당** 탭으로 돌아갑니다.

   :::image type="content" source="media/enable-managed-identity/added-user-identity-portal.png" alt-text="포털에서 사용자 할당을 추가했습니다.":::

## <a name="clean-up-resources"></a>리소스 정리

Automation 계정에 연결된 사용자가 할당한 관리 ID가 더 이상 필요하지 않은 경우 다음 단계를 수행합니다.

1. **사용자 할당** 탭에서 사용자가 할당한 관리 ID를 선택합니다.

1. 상단 메뉴에서 **제거** 를 선택한 후 확인 메시지가 표시되면 **예** 를 선택합니다.

Automation 계정에 대해 활성화된 시스템이 할당한 관리 ID가 더 이상 필요하지 않은 경우, 다음 단계를 수행합니다.

1. **시스템 할당** 탭의 **상태** 에서 **끄기** 를 선택합니다.

1. 상단 메뉴에서 **저장** 을 선택한 다음, 확인 메시지가 표시되면 **예** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure Automation 계정에 대한 관리 ID를 사용하도록 설정했습니다. 관리 ID와 함께 Automation 계정을 사용하여 Runbook을 실행하려면 다음을 참조하세요.

> [!div class="nextstepaction"]
> [자습서: 관리 ID를 사용하여 Automation PowerShell Runbook 만들기](../learn/powershell-runbook-managed-identity.md)
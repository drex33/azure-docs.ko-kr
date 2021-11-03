---
title: 포털을 사용하여 관리 그룹 만들기
description: 이 빠른 시작에서는 Azure Portal을 사용하여 리소스를 리소스 계층 구조로 구성하는 관리 그룹을 만듭니다.
ms.date: 08/17/2021
ms.topic: quickstart
ms.custom: mode-portal
ms.openlocfilehash: 8870bb44a055709a82ed217a25611b3879ed011c
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131081166"
---
# <a name="quickstart-create-a-management-group"></a>빠른 시작: 관리 그룹 만들기

관리 그룹은 여러 구독에서 액세스, 정책 및 규정 준수를 관리하는 데 도움이 되는 컨테이너입니다. 이러한 컨테이너를 만들어 [Azure 정책](../policy/overview.md) 및 [Azure 역할 기반 액세스 제어](../../role-based-access-control/overview.md)와 함께 사용할 수 있는 효과적이고 효율적인 계층을 구축합니다. 관리 그룹에 대한 자세한 내용은 [Azure 관리 그룹으로 리소스 구성](overview.md)을 참조하세요.

디렉터리에서 만드는 첫 번째 관리 그룹을 완료하려면 최대 15분이 소요될 수 있습니다. 디렉터리에 대해 Azure 내의 관리 그룹 서비스를 설정하기 위해 처음으로 실행되는 프로세스가 있습니다. 프로세스가 완료되면 알림이 수신됩니다. 자세한 내용은 [관리 그룹의 초기 설정](./overview.md#initial-setup-of-management-groups)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

- Azure 구독이 아직 없는 경우 시작하기 전에 [체험](https://azure.microsoft.com/free/) 계정을 만듭니다.

- 테넌트의 모든 Azure AD 사용자는 [계층 구조 보호](./how-to/protect-resource-hierarchy.md#setting---require-authorization)가 활성화되지 않은 경우 해당 사용자에게 할당된 관리 그룹 쓰기 권한 없이 관리 그룹을 만들 수 있습니다. 이 새 관리 그룹은 루트 관리 그룹 또는 [기본 관리 그룹](./how-to/protect-resource-hierarchy.md#setting---default-management-group)의 자식이 되며 작성자에게 "소유자" 역할 할당이 부여됩니다. 관리 그룹 서비스는 이 기능을 허용하므로 루트 수준에서 역할 할당이 필요하지 않습니다. 루트 관리 그룹이 생성될 때 사용자는 액세스할 수 있는 권한이 없습니다. 관리 그룹 사용을 시작하기 위해 Azure AD 전역 관리자를 찾는 장애물을 방지하기 위해 루트 수준에서 초기 관리 그룹을 만들 수 있습니다.

### <a name="create-in-portal"></a>포털에서 만들기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.

1. **모든 서비스** > **관리 그룹 + 거버넌스** 를 선택합니다.

1. **관리 그룹** 을 선택합니다.

1. **+ 관리 그룹 추가** 를 선택합니다.

   :::image type="content" source="./media/main.png" alt-text="자식 관리 그룹 및 구독을 보여주는 관리 그룹 페이지 스크린샷":::

1. **새로 만들기** 를 선택한 상태로 두고 관리 그룹 ID 필드를 입력합니다.

   - **관리 그룹 ID** 는 이 관리 그룹에 명령을 전송하는 데 사용되는 디렉터리 고유 식별자입니다. 이 식별자는 Azure 시스템 전체에서 이 그룹을 식별하는 데 사용되므로 만든 후에는 편집할 수 없습니다. [루트 관리 그룹](./overview.md#root-management-group-for-each-directory)은 Azure Active Directory ID인 ID를 사용하여 자동으로 생성됩니다. 다른 모든 관리 그룹의 경우 고유 ID를 할당합니다.
   - 표시 이름 필드는 Azure Portal 내에 표시되는 이름을 포함합니다. 별도 표시 이름은 관리 그룹을 만들 때 사용되는 선택적 필드로, 언제든지 변경할 수 있습니다.

   :::image type="content" source="./media/create_context_menu.png" alt-text="새 관리 그룹을 만들기 위한 '관리 그룹 추가' 옵션 스크린샷":::

1. **저장** 을 선택합니다.

## <a name="clean-up-resources"></a>리소스 정리

생성한 관리 그룹을 제거하려면 다음 단계를 수행합니다.

1. **모든 서비스** > **관리 그룹 + 거버넌스** 를 선택합니다.

1. **관리 그룹** 을 선택합니다.

1. 위에서 만든 관리 그룹을 찾아 선택한 다음, 이름 옆에 있는 **세부 정보** 를 선택합니다.
   그런 다음, **삭제** 를 선택하고 프롬프트를 확인합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 리소스 계층 구조를 구성하기 위한 관리 그룹을 만들었습니다. 관리 그룹은 구독 또는 기타 관리 그룹을 포함할 수 있습니다.

관리 그룹 및 리소스 계층 구조를 관리하는 방법에 대해 자세히 알아보려면 다음을 계속 진행하세요.

> [!div class="nextstepaction"]
> 관리 그룹으로 리소스 관리

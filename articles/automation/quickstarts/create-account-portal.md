---
title: 빠른 시작 - 포털을 사용하여 Azure Automation 계정 만들기
description: 이 빠른 시작은 포털을 사용하여 Azure Automation 계정 만들기를 시작하는 데 도움이 됩니다.
services: automation
ms.date: 09/07/2021
ms.topic: quickstart
ms.subservice: process-automation
ms.custom: mvc
ms.openlocfilehash: f0f93a43fd74ba38b8be17636b695e287354ed5a
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129218278"
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

1. **액세스 정책 추가** 페이지에서 다음 정보를 제공합니다.

   | 속성 | 설명 |
   |---|---|
   |Name| 위치 및 리소스 그룹에 고유한 이름을 입력합니다. 삭제된 Automation 계정의 이름은 즉시 사용할 수 없습니다. 사용자 인터페이스에 입력된 계정 이름은 변경할 수 없습니다. |
   |구독| 드롭다운 목록에서 계정에 대한 Azure 구독을 선택합니다.|
   |Resource group|드롭다운 목록에서 기존 리소스 그룹을 선택하거나 **새로 만들기** 를 선택합니다.|
   |위치| 드롭다운 목록에서 계정 위치를 선택합니다. Automation 계정을 배포할 수 있는 위치의 업데이트된 목록은 [지역별로 사용 가능한 제품](https://azure.microsoft.com/global-infrastructure/services/?products=automation&regions=all)을 참조하세요.|
   |Azure 실행 계정 만들기| **아니오** 를 선택합니다.  Automation 계정의 Azure 실행 계정은 Azure 인증에 유용하지만, 이제는 Automation의 관리 ID를 사용할 수 있습니다. [관리 ID](../../active-directory/managed-identities-azure-resources/overview.md)는 Azure AD(Azure Active Directory) 인증을 지원하는 리소스에 연결할 때 사용할 애플리케이션의 ID를 제공합니다. |

   :::image type="content" source="./media/create-account-portal/add-automation-account-portal.png" alt-text="Automation 계정을 추가하기 위한 필수 필드":::

1. **만들기** 를 선택하여 Automation 계정 배포를 시작합니다. 약 1분 안에 만들어집니다.

1. 배포가 완료되면 알림을 받게 됩니다. 알림에서 **리소스로 이동** 을 선택하여 **Automation 계정** 페이지를 엽니다.

1. 새 Automation 계정을 검토합니다.

   :::image type="content" source="./media/create-account-portal/automation-account-overview.png" alt-text="Automation 계정 개요 페이지":::

## <a name="clean-up-resources"></a>리소스 정리

Automation 계정을 계속 사용하지 않으려면 **개요** 페이지에서 **삭제** 를 선택한 다음, 메시지가 표시되면 **예** 를 선택합니다.

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Automation 계정을 만들었습니다. Automation 계정에서 관리 ID를 사용하려면 다음 빠른 시작을 계속 진행합니다.

> [!div class="nextstepaction"]
> [빠른 시작 - 관리 ID 사용](enable-managed-identity.md)


---
title: 'Azure Digital Twins 요청이 실패했습니다. 상태: 403(사용할 수 없음)'
description: "Azure Digital Twins의 '서비스 요청이 실패했습니다. 상태: 403(사용할 수 없음)’에 대한 원인과 해결 방법."
ms.service: digital-twins
author: baanders
ms.author: baanders
ms.topic: troubleshooting
ms.date: 7/20/2020
ms.openlocfilehash: 9a5191533b336d9d0cbefcf9b58f56d6648a60df
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114456157"
---
# <a name="service-request-failed-status-403-forbidden"></a>서비스 요청에 실패했습니다. 상태: 403(사용할 수 없음)

이 문서에서는 서비스 요청에서 Azure Digital Twins로 403 오류를 수신하는 원인 및 해결 단계를 설명합니다. 

## <a name="symptoms"></a>증상

이 오류는 인증을 필요로 하는 다양한 유형의 서비스 요청에서 발생할 수 있습니다. 그 결과 API 요청이 실패하고 `403 (Forbidden)` 오류 상태가 반환됩니다.

## <a name="causes"></a>원인

### <a name="cause-1"></a>원인 #1

일반적으로 이 오류는 서비스에 대한 Azure RBAC(Azure 역할 기반 액세스 제어) 권한이 올바르게 설정되지 않았음을 나타냅니다. Azure Digital Twins 인스턴스에 대해 많은 작업을 수행하려면 **관리하려는 인스턴스** 에 *Azure Digital Twins 데이터 소유자* 역할이 있어야 합니다. 

### <a name="cause-2"></a>원인 #2

[앱 등록](./how-to-create-app-registration-portal.md)을 사용하여 인증하는 Azure Digital Twins와 통신하기 위해 클라이언트 앱을 사용하는 경우 앱 등록에 Azure Digital Twins 서비스에 대해 설정된 권한이 없기 때문에 이 오류가 발생할 수 있습니다.

앱 등록에는 Azure Digital Twins API에 대해 구성된 액세스 권한이 있어야 합니다. 그런 다음, 앱 등록에 대해 클라이언트 앱이 인증되면 앱 등록이 구성된 사용 권한이 부여됩니다.

## <a name="solutions"></a>솔루션

### <a name="solution-1"></a>해결 방법 #1

첫 번째 솔루션은 Azure 사용자가 관리하려는 인스턴스에 _**Azure Digital Twins 데이터 소유자**_ 역할을 보유하고 있는지 확인하는 것입니다. 이 역할이 없다면 새로 설정합니다.

이 역할은...
* 미리 보기 중에 이 역할의 이전 이름인 *Azure Digital Twins 소유자(미리 보기)* 와는 다릅니다(역할은 동일 하지만 이름이 변경됨).
* 전체 Azure 구독에서의 *소유자* 역할과는 다릅니다. *Azure Digital Twins 데이터 소유자* 는 Azure Digital Twins 내에서의 역할이며, 이 개별 Azure Digital Twins 인스턴스로 범위가 지정됩니다.
* Azure Digital Twins의 *소유자* 역할과는 다릅니다. 이것들은 서로 다른 두 가지 Azure Digital Twins 관리 역할이며, *Azure Digital Twins 데이터 소유자* 는 관리에 사용해야 하는 역할입니다.

#### <a name="check-current-setup"></a>현재 설정 확인

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

#### <a name="fix-issues"></a>문제 해결 

이 역할 할당이 없는 경우 **Azure 구독** 에 소유자 역할이 있는 사용자는 다음 명령을 실행하여 Azure 사용자에게 **Azure Digital Twins 인스턴스** 에서 *Azure Digital Twins 데이터 소유자* 역할을 제공해야 합니다. 

구독에 대한 소유자인 경우 이 명령을 직접 실행할 수 있습니다. 그렇지 않은 경우 소유자에게 문의하여 대신 이 명령을 실행하도록 합니다.

```azurecli-interactive
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<your-Azure-AD-email>" --role "Azure Digital Twins Data Owner"
```

이 역할 요구 사항과 할당 프로세스에 대한 자세한 내용은 *방법: 인스턴스 및 인증 설정(CLI 또는 포털)* 의 [사용자 액세스 권한 설정 섹션](how-to-set-up-instance-CLI.md#set-up-user-access-permissions)을 참조하세요.

이 역할 할당이 이미 *있고* Azure AD 앱 등록을 사용하여 클라이언트 앱을 인증하는 경우, 이 솔루션에서 403 문제를 해결하지 않았다면 다음 솔루션을 계속 진행할 수 있습니다.

### <a name="solution-2"></a>해결 방법 #2

Azure AD 앱 등록을 사용하여 클라이언트 앱을 인증하는 경우 두 번째 가능한 해결 방법은 앱 등록에 Azure Digital Twins 서비스에 대해 구성된 권한이 있는지 확인하는 것입니다. 구성되지 않았다면 새로 설정합니다.

#### <a name="check-current-setup"></a>현재 설정 확인

사용 권한이 올바르게 구성되었는지 확인하려면 Azure Portal에서 [Azure AD 앱 등록 개요 페이지](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps)로 이동합니다. 포털 검색 창에서 *앱 등록* 을 검색하여 이 페이지로 직접 이동할 수 있습니다.

*모든 애플리케이션* 탭으로 전환하여 구독에서 생성된 모든 앱 등록을 확인합니다.

목록에서 방금 만든 앱 등록이 표시됩니다. 이를 선택하고 세부 정보를 엽니다.

:::image type="content" source="media/troubleshoot-error-403/app-registrations.png" alt-text="Azure Portal의 앱 등록 페이지 스크린샷.":::

먼저, 등록에 Azure Digital Twins 권한 설정이 올바르게 설정되었는지 확인합니다. 이렇게 하려면 메뉴 모음에서 *매니페스트* 를 선택하여 앱 등록의 매니페스트 코드를 확인합니다. 코드 창의 아래쪽으로 스크롤하고 `requiredResourceAccess` 아래에서 이러한 필드를 찾습니다. 값은 아래 스크린샷에 있는 값과 일치해야 합니다.

:::image type="content" source="media/troubleshoot-error-403/verify-manifest.png" alt-text="Azure Portal의 Azure AD 앱 등록에 대한 매니페스트의 스크린샷.":::

그런 다음, 메뉴 모음에서 *API 권한* 을 선택하여 이 앱 등록에 Azure Digital Twins에 대한 읽기/쓰기 권한이 포함되어 있는지 확인합니다. 다음과 유사한 항목이 표시됩니다.

:::image type="content" source="media/troubleshoot-error-403/verify-api-permissions.png" alt-text="Azure Digital Twins의 '읽기/쓰기 액세스'를 보여주는 Azure Portal의 Azure AD 앱 등록에 대한 API 권한의 스크린샷.":::

#### <a name="fix-issues"></a>문제 해결

이 중 하나라도 설명된 것과 다르게 표시되는 경우 [앱 등록 만들기](./how-to-create-app-registration-portal.md)에서 앱 등록을 설정하는 방법에 대한 지침을 따르세요.

## <a name="next-steps"></a>다음 단계

새 Azure Digital Twins 인스턴스를 만들고 인증하는 방법에 대한 설치 단계를 읽어보세요.
* [인스턴스 및 인증 설정(CLI)](how-to-set-up-instance-cli.md)

Azure Digital Twins의 보안 및 사용 권한에 대해 자세히 알아보세요.
* [Azure Digital Twins 솔루션에 대한 보안](concepts-security.md)
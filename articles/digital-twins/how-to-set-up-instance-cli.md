---
title: 인스턴스 및 인증 설정(CLI)
titleSuffix: Azure Digital Twins
description: CLI를 사용하여 Azure Digital Twins 서비스 인스턴스를 설정하는 방법 참조
author: baanders
ms.author: baanders
ms.date: 10/13/2021
ms.topic: how-to
ms.service: digital-twins
ms.custom: contperf-fy22q2
ms.openlocfilehash: 615680552bc1854c650477f314c4ab14815dbc01
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130000236"
---
# <a name="set-up-an-azure-digital-twins-instance-and-authentication-cli"></a>Azure Digital Twins 인스턴스 및 인증 설정(CLI)

[!INCLUDE [digital-twins-setup-selector.md](../../includes/digital-twins-setup-selector.md)]

이 문서에서는 인스턴스를 만들고 인증을 설정하는 등 **새 Azure Digital Twins 인스턴스를 설정** 하는 단계에 대해 설명합니다. 이 문서를 완료 한 후에는에 대 한 프로그래밍을 시작할 준비가 된 Azure Digital Twins 인스턴스를 사용할 수 있습니다.

[!INCLUDE [digital-twins-setup-steps.md](../../includes/digital-twins-setup-steps.md)]

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

### <a name="set-up-cloud-shell-session"></a>Cloud Shell 세션 설정
[!INCLUDE [Cloud Shell for Azure Digital Twins](../../includes/digital-twins-cloud-shell.md)]

## <a name="create-the-azure-digital-twins-instance"></a>Azure Digital Twins 인스턴스 만들기

이 섹션에서는 Cloud Shell 명령을 사용 하 여 **Azure Digital Twins의 새 인스턴스를 만듭니다** . 다음을 제공해야 합니다.
* 인스턴스가 배포될 리소스 그룹. 고려 중인 기존 리소스 그룹이 아직 없는 경우 다음 명령을 사용하여 지금 만들 수 있습니다.
    ```azurecli-interactive
    az group create --location <region> --name <name-for-your-resource-group>
    ```
* 배포 지역. Azure Digital Twins를 지원하는 지역을 확인하려면 [지역별 사용 가능한 Azure 제품](https://azure.microsoft.com/global-infrastructure/services/?products=digital-twins)을 참조하세요.
* 인스턴스의 이름. 이미 지정된 이름을 사용하고 있는 지역에 다른 Azure Digital Twins 인스턴스가 있는 구독의 경우 다른 이름을 선택하라는 메시지가 표시됩니다.

다음 [az dt 명령](/cli/azure/dt?view=azure-cli-latest&preserve-view=true)에서 이러한 값을 사용하여 인스턴스를 만듭니다.

```azurecli-interactive
az dt create --dt-name <name-for-your-Azure-Digital-Twins-instance> --resource-group <your-resource-group> --location <region>
```

### <a name="verify-success-and-collect-important-values"></a>성공 확인 및 중요한 값 수집

인스턴스가 성공적으로 만들어지면 Cloud Shell에 다음과 같이 결과가 표시되어 사용자가 만든 리소스에 대한 정보를 출력합니다.

:::image type="content" source="media/how-to-set-up-instance/cloud-shell/create-instance.png" alt-text="Azure Portal 리소스 그룹 및 Azure Digital Twins 인스턴스를 성공적으로 만든 Cloud Shell 창의 스크린샷.":::

출력에서 Azure Digital Twins 인스턴스의 **hostName**, **name** 및 **resourceGroup** 을 확인합니다. 이러한 값은 모두 중요 하며, Azure Digital Twins 인스턴스 작업을 계속 하 여 인증 및 관련 Azure 리소스를 설정 하는 데 사용 해야 할 수도 있습니다. 다른 사용자가 인스턴스에 대한 프로그래밍을 수행하는 경우 다른 사용자와 이 값을 공유해야 합니다.

> [!TIP]
> `az dt show --dt-name <your-Azure-Digital-Twins-instance>`를 실행하여 언제든지 인스턴스의 모든 속성과 함께 이러한 속성을 볼 수 있습니다.

이제 Azure Digital Twins 인스턴스를 사용할 준비가 되었습니다. 다음으로, 적절한 Azure 사용자에게 관리 권한을 부여합니다.

## <a name="set-up-user-access-permissions"></a>사용자 액세스 권한 설정

[!INCLUDE [digital-twins-setup-role-assignment.md](../../includes/digital-twins-setup-role-assignment.md)]

### <a name="prerequisites-permission-requirements"></a>필수 조건: 권한 요구 사항

[!INCLUDE [digital-twins-setup-permissions.md](../../includes/digital-twins-setup-permissions.md)]

### <a name="assign-the-role"></a>역할 할당

사용자에게 Azure Digital Twins 인스턴스를 관리할 수 있는 권한을 부여하려면 인스턴스 내에서 **Azure Digital Twins 데이터 소유자** 역할을 할당해야 합니다.

다음 명령을 사용하여 역할을 할당합니다(Azure 구독에 [충분한 권한](#prerequisites-permission-requirements)이 있는 사용자가 실행해야 함). 이 명령을 사용하려면 역할을 할당해야 하는 사용자에 대한 Azure AD 계정의 *사용자 주체 이름* 을 전달해야 합니다. 대부분의 경우이 값은 Azure AD 계정의 사용자 전자 메일과 일치 합니다.

```azurecli-interactive
az dt role-assignment create --dt-name <your-Azure-Digital-Twins-instance> --assignee "<Azure-AD-user-principal-name-of-user-to-assign>" --role "Azure Digital Twins Data Owner"
```

이 명령을 실행하면 만들어진 역할 할당에 대한 정보가 출력됩니다.

> [!NOTE]
> 이 명령을 실행할 때 CLI가 **그래프 데이터베이스에서 사용자 또는 서비스 주체를 찾을 수 없다** 는 오류를 반환하는 경우:
>
> 대신 사용자의 *개체 ID* 를 사용하여 역할을 할당합니다. 이는 개인 [MSA(Microsoft 계정)](https://account.microsoft.com/account)의 사용자에게 발생할 수 있습니다. 
>
> [Azure Active Directory 사용자의 Azure Portal 페이지](https://portal.azure.com/#blade/Microsoft_AAD_IAM/UsersManagementMenuBlade/AllUsers)를 사용하여 사용자 계정을 선택하고 세부 정보를 엽니다. 사용자의 *ObjectID* 를 복사합니다.
>
> :::image type="content" source="media/includes/user-id.png" alt-text="'개체 ID' 필드에서 GUID를 강조 표시하는 Azure Portal의 사용자 페이지 스크린샷." lightbox="media/includes/user-id.png":::
>
> 그런 다음. 위의 `assignee` 매개 변수에 대한 사용자의 *개체 ID* 를 사용하여 역할 할당 목록 명령을 반복합니다.

### <a name="verify-success"></a>성공 확인

[!INCLUDE [digital-twins-setup-verify-role-assignment.md](../../includes/digital-twins-setup-verify-role-assignment.md)]

이제 Azure Digital Twins 인스턴스를 사용할 준비가 되고 관리 권한이 할당되었습니다.

## <a name="next-steps"></a>다음 단계

Azure Digital Twins CLI 명령을 사용하여 인스턴스에서 개별 REST API 호출을 테스트합니다. 
* [az dt reference](/cli/azure/dt?view=azure-cli-latest&preserve-view=true)
* [Azure Digital Twins CLI 명령 집합](concepts-cli.md)

또는 인증 코드를 사용하여 인스턴스에 클라이언트 애플리케이션을 연결하는 방법을 참조하세요.
* [앱 인증 코드 쓰기](how-to-authenticate-client.md)
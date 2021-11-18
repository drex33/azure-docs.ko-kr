---
title: 관리 ID에서 Azure AD를 사용하여 Web PubSub 리소스에 대한 요청 권한 부여
description: 이 문서에서는 관리 ID에서 Azure AD를 사용하여 Web PubSub 리소스에 대한 요청 권한을 부여하는 방법을 제공합니다.
author: terencefan
ms.author: tefa
ms.date: 11/08/2021
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.openlocfilehash: bb27176e33d94db0c1bb6e0770ae45f765b0429a
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132714222"
---
# <a name="authorize-request-to-web-pubsub-resources-with-azure-ad-from-managed-identities"></a>관리 ID에서 Azure AD를 사용하여 Web PubSub 리소스에 대한 요청 권한 부여
Azure Web PubSub Service는 Azure [리소스에 대한 관리 ID의](../active-directory/managed-identities-azure-resources/overview.md)요청에 권한을 부여하는 azure AD(Azure Active Directory)를 지원합니다. 

이 문서에서는 관리 ID에서 Web PubSub 리소스에 대한 요청에 권한을 부여하도록 Web PubSub 리소스 및 코드를 구성하는 방법을 보여 줍니다.

## <a name="configure-managed-identities"></a>관리되는 ID 구성

첫 번째 단계는 관리 ID를 구성하는 것입니다.

이는 Azure Portal 사용하여 에서 를 구성하는 `System-assigned managed identity` `Virtual Machine` 예제입니다.

1. [Azure Portal](https://portal.azure.com/)열고 Virtual Machine을 검색하여 선택합니다.
1. **설정** 섹션에서 **ID를** 선택합니다.
1. 시스템 **할당** 탭에서 **상태를** **켜기로 전환합니다.**
   ![가상 머신의 스크린샷 - ID](./media/aad-authorization/identity-virtual-machine.png)
1. **저장** 단추를 클릭하여 변경 내용을 확인합니다.

### <a name="how-to-create-user-assigned-managed-identities"></a>사용자 할당 관리 ID를 만드는 방법
- [사용자 할당 관리 ID 만들기](../active-directory/managed-identities-azure-resources/how-manage-user-assigned-managed-identities.md#create-a-user-assigned-managed-identity)

### <a name="how-to-configure-managed-identities-on-other-platforms"></a>다른 플랫폼에서 관리 ID를 구성하는 방법

- [Azure Portal을 사용하여 VM에서 Azure 리소스에 대한 관리 ID 구성](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [PowerShell을 사용하여 Azure VM에서 Azure 리소스에 대한 관리 ID 구성](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI를 사용하여 Azure VM에서 Azure 리소스에 대한 관리 ID 구성](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [템플릿을 사용하여 Azure VM에서 Azure 리소스에 대한 관리 ID 구성](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure SDK를 사용하여 Azure 리소스에 대한 관리 ID로 VM 구성](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

### <a name="how-to-configure-managed-identities-for-app-service-and-azure-functions"></a>App Service 및 Azure Functions 대한 관리 ID를 구성하는 방법

- [App Service 및 Azure Functions 관리 ID를 사용하는 방법](../app-service/overview-managed-identity.md)

## <a name="add-role-assignments-on-azure-portal"></a>Azure Portal 역할 할당 추가  

이 샘플에서는 Web `Web PubSub Service Owner` PubSub 리소스를 통해 시스템 할당 ID에 역할을 할당하는 방법을 보여줍니다. 

> [!Note]
> 역할은 관리 그룹, 구독, 리소스 그룹 또는 단일 리소스를 비롯한 모든 범위에 할당할 수 있습니다. 범위에 대한 자세한 내용은 [Azure RBAC 범위 이해를 참조하세요.](../role-based-access-control/scope-overview.md)
1. [Azure Portal](https://portal.azure.com/)열고 Web PubSub 리소스로 이동합니다.

1. **Access Control(IAM)를** 클릭하여 Azure Web PubSub에 대한 액세스 제어 설정을 표시합니다.

   다음은 리소스 그룹에 대한 액세스 제어(IAM) 페이지의 예를 보여줍니다.

1. **역할 할당** 탭을 클릭하여 관련 범위의 역할 할당을 확인합니다.

   다음 스크린샷은 Web PubSub 리소스에 대한 액세스 제어(IAM) 페이지의 예를 보여줍니다.

   ![액세스 제어 스크린샷](./media/aad-authorization/access-control.png)

1. **추가 > 역할 할당 추가를** 클릭합니다.

1. **역할** 탭에서 를 `Web PubSub Service Owner` 선택합니다.

1. **다음** 을 클릭합니다.

   ![역할 할당 추가 스크린샷](./media/aad-authorization/add-role-assignment.png)

1. **멤버** 탭의 **액세스 권한 할당** 섹션에서 관리 **ID** 를 선택합니다.

1. **멤버 선택을** 클릭합니다.

1. 관리 ID 선택 창에서 **가상 머신에 > 시스템 할당 관리 ID를** **선택합니다.**

1. 역할을 할당할 가상 머신을 검색하여 선택합니다.

1. **선택** 을 클릭하여 선택 사항을 확인합니다.

2. **다음** 을 클릭합니다.

   ![관리 ID에 역할을 할당하는 스크린샷](./media/aad-authorization/assign-role-to-managed-identities.png)

3. **검토 + 할당을** 클릭하여 변경 내용을 확인합니다.

> [!IMPORTANT]
> Azure 역할 할당이 전파되는 데 최대 30분이 걸릴 수 있습니다.
Azure 역할 할당을 할당하고 관리하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.
- [Azure Portal을 사용하여 Azure 역할 할당](../role-based-access-control/role-assignments-portal.md)
- [REST API를 사용하여 Azure 역할 할당](../role-based-access-control/role-assignments-rest.md)
- [Azure PowerShell을 사용하여 Azure 역할 할당](../role-based-access-control/role-assignments-powershell.md)
- [Azure CLI를 사용하여 Azure 역할 할당](../role-based-access-control/role-assignments-cli.md)
- [Azure Resource Manager 템플릿을 사용하여 Azure 역할 할당](../role-based-access-control/role-assignments-template.md)

## <a name="sample-codes"></a>샘플 코드

공식적으로 4개의 프로그래밍 언어를 지원합니다.

- [C#](./howto-create-serviceclient-with-net-and-azure-identity.md)
- [Python](./howto-create-serviceclient-with-python-and-azure-identity.md)
- [Java](./howto-create-serviceclient-with-java-and-azure-identity.md)
- [JavaScript](./howto-create-serviceclient-with-javascript-and-azure-identity.md)

## <a name="next-steps"></a>다음 단계

다음 관련 문서를 참조하세요.
- [Web PubSub용 Azure AD 개요](concept-azure-ad-authorization.md)
- [Azure 애플리케이션에서 Azure AD를 사용하여 Web PubSub 리소스에 대한 요청 권한 부여](howto-authorize-from-application.md)
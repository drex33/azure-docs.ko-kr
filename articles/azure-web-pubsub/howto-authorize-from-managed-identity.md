---
title: 관리 id에서 Azure AD를 사용 하 여 웹 PubSub 리소스에 대 한 요청 권한 부여
description: 이 문서에서는 관리 되는 id의 Azure AD를 사용 하 여 웹 PubSub 리소스에 대 한 요청 권한 부여에 대 한 정보
author: terencefan
ms.author: tefa
ms.date: 09/06/2021
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.openlocfilehash: 8df789836bd0067868d4f9e6d90e640473a96f33
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131482922"
---
# <a name="authorize-request-to-web-pubsub-resources-with-azure-ad-from-managed-identities"></a>관리 id에서 Azure AD를 사용 하 여 웹 PubSub 리소스에 대 한 요청 권한 부여
azure 웹 pubsub 서비스 [는 azure 리소스에 대 한 관리 되는 id](../active-directory/managed-identities-azure-resources/overview.md)에서 요청을 승인 하는 Azure Active Directory (azure AD)를 지원 합니다. 

이 문서에서는 관리 되는 id에서 웹 PubSub 리소스에 대 한 요청에 권한을 부여 하기 위해 웹 PubSub 리소스 및 코드를 구성 하는 방법을 보여 줍니다.

## <a name="configure-managed-identities"></a>관리되는 ID 구성

첫 번째 단계는 관리 되는 id를 구성 하는 것입니다.

`System-assigned managed identity`Azure Portal를 사용 하 여에를 구성 하는 예제입니다 `Virtual Machine` .

1. [Azure Portal](https://portal.azure.com/)를 열고 가상 컴퓨터를 검색 하 여 선택 합니다.
1. **설정** 섹션에서 **id** 를 선택 합니다.
1. **시스템 할당 됨** 탭에서 **상태** 를 **켜기** 로 전환 합니다.
   ![가상 컴퓨터-id의 스크린샷](./media/aad-authorization/identity-virtual-machine.png)
1. **저장** 단추를 클릭 하 여 변경 내용을 확인 합니다.

### <a name="how-to-create-user-assigned-managed-identities"></a>사용자 할당 관리 id를 만드는 방법
- [사용자 할당 관리 ID 만들기](../active-directory/managed-identities-azure-resources/how-manage-user-assigned-managed-identities.md#create-a-user-assigned-managed-identity)

### <a name="how-to-configure-managed-identities-on-other-platforms"></a>다른 플랫폼에서 관리 id를 구성 하는 방법

- [Azure Portal을 사용하여 VM에서 Azure 리소스에 대한 관리 ID 구성](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [PowerShell을 사용하여 Azure VM에서 Azure 리소스에 대한 관리 ID 구성](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI를 사용하여 Azure VM에서 Azure 리소스에 대한 관리 ID 구성](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [템플릿을 사용하여 Azure VM에서 Azure 리소스에 대한 관리 ID 구성](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure SDK를 사용하여 Azure 리소스에 대한 관리 ID로 VM 구성](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

### <a name="how-to-configure-managed-identities-for-app-service-and-azure-functions"></a>App service 및 Azure Functions에 대 한 관리 되는 id를 구성 하는 방법

- [App Service 및 Azure Functions에 관리 되는 id를 사용 하는 방법](../app-service/overview-managed-identity.md)

## <a name="add-role-assignments-on-azure-portal"></a>Azure Portal에 역할 할당 추가  

이 샘플에서는 `Web PubSub Service Owner` 웹 PubSub 리소스를 통해 시스템 할당 id에 역할을 할당 하는 방법을 보여 줍니다. 

> [!Note]
> 관리 그룹, 구독, 리소스 그룹 또는 단일 리소스를 비롯 한 모든 범위에 역할을 할당할 수 있습니다. 범위에 대해 자세히 알아보려면 [AZURE RBAC의 범위 이해](../role-based-access-control/scope-overview.md) 를 참조 하세요.
1. [Azure Portal](https://portal.azure.com/)를 열고 웹 pubsub 리소스로 이동 합니다.

1. **Access Control (IAM)** 을 클릭 하 여 Azure 웹 pubsub에 대 한 액세스 제어 설정을 표시 합니다.

   다음은 리소스 그룹에 대한 액세스 제어(IAM) 페이지의 예를 보여줍니다.

1. **역할 할당** 탭을 클릭하여 관련 범위의 역할 할당을 확인합니다.

   다음 스크린샷에서는 웹 PubSub 리소스에 대 한 액세스 제어 (IAM) 페이지의 예를 보여 줍니다.

   ![액세스 제어 스크린샷](./media/aad-authorization/access-control.png)

1. **추가 > 역할 할당 추가** 를 클릭 합니다.

1. **역할** 탭에서을 선택 `Web PubSub Service Owner` 합니다.

1. **다음** 을 클릭합니다.

   ![역할 할당 추가의 스크린샷](./media/aad-authorization/add-role-assignment.png)

1. **구성원** 탭의 **액세스 할당** 섹션에서 **관리 id** 를 선택 합니다.

1. **멤버 선택** 을 클릭 합니다.

1. **관리 Id 선택** 창에서 **시스템 할당 관리 id > 가상 컴퓨터** 를 선택 합니다.

1. 역할을 할당할 가상 컴퓨터를 검색 하 고 선택 합니다.

1. **선택** 을 클릭하여 선택 사항을 확인합니다.

2. **다음** 을 클릭합니다.

   ![관리 id에 역할을 할당 하는 스크린샷](./media/aad-authorization/assign-role-to-managed-identities.png)

3. **검토 + 할당** 을 클릭 하 여 변경 내용을 확인 합니다.

> [!IMPORTANT]
> Azure 역할 할당이 전파되는 데 최대 30분이 걸릴 수 있습니다.
Azure 역할 할당을 할당 하 고 관리 하는 방법에 대해 자세히 알아보려면 다음 문서를 참조 하세요.
- [Azure Portal을 사용하여 Azure 역할 할당](../role-based-access-control/role-assignments-portal.md)
- [REST API를 사용하여 Azure 역할 할당](../role-based-access-control/role-assignments-rest.md)
- [Azure PowerShell을 사용하여 Azure 역할 할당](../role-based-access-control/role-assignments-powershell.md)
- [Azure CLI를 사용하여 Azure 역할 할당](../role-based-access-control/role-assignments-cli.md)
- [Azure Resource Manager 템플릿을 사용하여 Azure 역할 할당](../role-based-access-control/role-assignments-template.md)

## <a name="sample-codes-while-configuring-your-server"></a>서버를 구성 하는 동안 샘플 코드

### <a name="using-system-assigned-identity"></a>시스템 할당 id 사용

[DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) 또는 [ManagedIdentityCredential](/dotnet/api/azure.identity.managedidentitycredential) 중 하나를 사용 하 여 시스템 할당 id를 사용 하는 동안 웹 pubsub 끝점을 구성할 수 있습니다.

그러나를 직접 사용 하는 것이 가장 좋습니다 `ManagedIdentityCredential` .

기본적으로 시스템 할당 관리 id가 사용 되지만를 사용 하는 경우 환경 [자격 증명](/dotnet/api/azure.identity.environmentcredential) 에 의해 유지 되는 **환경 변수를 구성 하지 않았는지 확인 하세요** `DefaultAzureCredential` . 그렇지 않으면를 사용 하 여 `EnvironmentCredential` 요청을 수행 하 고 `401 Unauthorized` 대부분의 경우 응답이 반환 됩니다.

다음은 c #에 대 한 샘플 코드입니다.

```C#
var endpoint = new Uri("https://<resource1>.webpubsub.azure.com");
var client = new WebPubSubServiceClient(endpoint, "hub", new ManagedIdentityCredential());
```

다른 지원 되는 언어에 대 한 샘플도 있습니다. [Java](), [JavaScript](), [Python]()을 참조 하세요.

### <a name="using-user-assigned-identity"></a>사용자 할당 id 사용

`ClientId`개체를 만드는 동안를 제공 하기만 하면 됩니다 `ManagedIdentityCredential` .

> [!IMPORTANT]
> 유사한 경우에도 개체 (보안 주체) ID가 아니라 **클라이언트 id** 를 사용 합니다.

다음은 c #에 대 한 샘플 코드입니다.

```C#
var endpoint = new Uri("https://<resource1>.webpubsub.azure.com");
var clientId = "<your user-assigned identity client id>";
var client = new WebPubSubServiceClient(endpoint, "hub", new ManagedIdentityCredential(clientId));
```

다른 지원 되는 언어에 대 한 샘플도 있습니다. [Java](), [JavaScript](), [Python]()을 참조 하세요.

## <a name="next-steps"></a>다음 단계

다음 관련 문서를 참조하세요.
- [웹 PubSub 용 Azure AD 개요](concept-azure-ad-authorization.md)
- [Azure 응용 프로그램에서 Azure AD를 사용 하 여 웹 PubSub 리소스에 대 한 요청 권한 부여](howto-authorize-from-application.md)
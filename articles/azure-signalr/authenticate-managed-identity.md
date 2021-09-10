---
title: Azure Active Directory를 사용하여 관리 ID 인증
description: 이 문서에서는 Azure SignalR Service에 액세스하는 Azure Active Directory를 사용하는 관리 ID 인증에 대한 정보를 제공합니다.
author: terencefan
ms.author: tefa
ms.date: 08/03/2020
ms.service: signalr
ms.topic: conceptual
ms.openlocfilehash: e25fa98e2ca43b6697de6290ad122b6585b6a072
ms.sourcegitcommit: ef448159e4a9a95231b75a8203ca6734746cd861
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/30/2021
ms.locfileid: "123186032"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-azure-signalr-resources"></a>Azure Active Directory를 사용하여 Azure SignalR 리소스에 액세스하는 관리 ID 인증
Azure SignalR Service는 [Azure 리소스에 대한 관리 ID](../active-directory/managed-identities-azure-resources/overview.md)를 사용하는 Azure Active Directory(Azure AD) 인증을 지원합니다. Azure 리소스의 관리 ID를 사용하여 Azure VM(가상 머신), 함수 앱, Virtual Machine Scale Sets 및 기타 서비스에서 실행 중인 애플리케이션의 Azure AD 자격 증명을 사용하여 Azure SignalR Service 리소스에 대한 액세스 권한을 부여할 수 있습니다. Azure AD 인증과 함께 Azure 리소스의 관리 ID를 사용하면 클라우드에서 실행되는 애플리케이션에 자격 증명을 저장할 필요가 없습니다.

이 문서에서는 관리 ID를 사용하여 Azure SignalR Service에 대한 액세스 권한을 부여하는 방법을 보여 줍니다.

## <a name="enable-managed-identities"></a>관리 ID 사용
Azure 리소스의 관리 ID를 사용하여 Azure SIgnalR Service 리소스에 대한 액세스 권한을 부여하려면 먼저 Azure 리소스에 대한 관리 ID를 사용하도록 설정해야 합니다. 
### <a name="for-azure-resources-on-a-vm"></a>VM의 Azure 리소스의 경우
VM에서 Azure 리소스에 대한 관리 ID를 사용하도록 설정하는 방법을 알아보려면 다음 문서 중 하나를 참조하세요.

- [Azure Portal](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
  1. Azure Portal **설정** 으로 이동하여 **ID** 를 선택합니다. 
  2. **상태** 를 **켜짐** 으로 선택합니다. 
  3. **저장** 을 선택하여 설정을 저장합니다. 

      ![가상 머신에 대한 관리 ID](./media/authenticate/identity-virtual-machine.png)
- [Azure PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager 템플릿](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager 클라이언트 라이브러리](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

### <a name="for-azure-functions"></a>Azure Functions의 경우

[App Service 및 Azure Functions에 대한 관리 ID를 사용하는 방법](../app-service/overview-managed-identity.md)을 참조하세요.

## <a name="grant-permissions-to-a-managed-identity-in-azure-ad"></a>Azure AD에서 관리 ID에 대한 권한 부여
애플리케이션의 관리 ID에서 Azure SignalR Service에 대한 요청에 권한을 부여하려면 먼저 관리 ID에 대한 RBAC(역할 기반 액세스 제어) 설정을 구성합니다. Azure SignalR Service는 `AccessKey` 또는 `ClientToken` 획득에 대한 권한을 포함하는 RBAC 역할을 정의합니다. RBAC 역할이 관리 ID에 할당되면 관리 ID에 적절한 범위에서 Azure SignalR Service에 대한 액세스 권한이 부여됩니다.

다음 지침에 따라 역할 할당을 관리합니다.

1. [Azure Portal](https://portal.azure.com/)에서 SignalR 리소스로 이동합니다.
1. **액세스 제어(IAM)** 를 선택하여 Azure SignalR에 대한 액세스 제어 설정을 표시합니다. 
1. **역할 할당** 탭을 선택하여 역할 할당 목록을 봅니다. 도구 모음에서 **추가** 단추를 선택한 다음, **역할 할당 추가** 를 선택합니다. 

    ![도구 모음의 추가 단추](./media/authenticate/role-assignments-add-button.png)

1. **역할 할당 추가** 페이지에서 다음 단계를 수행합니다.
    1. **SignalR Service Owner** 를 역할로 선택합니다.
    1. 역할을 할당하려는 **보안 주체**(사용자, 그룹, 서비스 사용자)를 검색합니다.
    1. **저장** 을 선택하여 역할 할당을 저장합니다. 

        ![애플리케이션에 역할 할당](./media/authenticate/assign-role-to-application.png)

    1. 역할을 할당받은 ID가 해당 역할에 따라 나열되어 표시됩니다. 예를 들어 다음 이미지는 해당 애플리케이션 `signalr-dev` 및 `signalr-service`이 SignalR 앱 서버 역할에 있음을 보여줍니다. 
        
        ![역할 할당 목록](./media/authenticate/role-assignment-list.png)

비슷한 단계에 따라 리소스 그룹 또는 구독에 범위가 지정된 역할을 할당할 수 있습니다. 역할 및 해당 범위를 정의하면 [이 GitHub 위치에서](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac) 샘플을 사용하여 이 동작을 테스트할 수 있습니다.

RBAC 역할 할당에 대한 자세한 내용은 [Azure Active Directory를 사용하여 Azure SignalR Service 리소스에 대한 액세스 인증](authorize-access-azure-active-directory.md)을 참조하세요.

Azure RBAC 및 Azure Portal을 사용하여 Azure 리소스에 대한 액세스를 관리하는 방법에 대해 자세히 알려면 [이 문서](..//role-based-access-control/role-assignments-portal.md)를 참조하세요. 

## <a name="configure-your-app"></a>앱 구성
### <a name="app-server"></a>앱 서버

`AddAzureSignalR`일 때 다음 옵션을 추가합니다.

```C#
services.AddSignalR().AddAzureSignalR(option =>
{
    option.ConnectionString = "Endpoint=https://<name>.signalr.net;AuthType=aad;Version=1.0;";
});
```

### <a name="azure-functions-app"></a>Azure Functions 앱

Azure Portal에서, 이름이 `AzureSignalRConnectionString`이고 값이 `Endpoint=https://<name>.signalr.net;AuthType=aad;`인 애플리케이션 설정을 추가합니다.

로컬에서, `local.appsettings.json` 파일의 `Values` 섹션에 추가합니다.
```json
{
    "Values": {
        "AzureSignalRConnectionString": "Endpoint=https://<name>.signalr.net;AuthType=aad;Version=1.0;"
    }
}
```

## <a name="next-steps"></a>다음 단계
- RBAC에 대한 자세한 내용은 [Azure RBAC(Azure 역할 기반 액세스 제어)란?](../role-based-access-control/overview.md)을 참조하세요.
- Azure PowerShell, Azure CLI 또는 REST API를 사용하여 할당하는 방법 및 Azure 역할 할당을 관리하는 방법을 알아보려면 다음 문서를 참조하세요.
    - [Azure PowerShell을 사용하여 RBAC(역할 기반 액세스 제어) 관리](../role-based-access-control/role-assignments-powershell.md)  
    - [Azure CLI를 사용하여 RBAC(역할 기반 액세스 제어) 관리](../role-based-access-control/role-assignments-cli.md)
    - [REST API를 사용하여 RBAC(역할 기반 액세스 제어) 관리](../role-based-access-control/role-assignments-rest.md)
    - [Azure Resource Manager 템플릿으로 RBAC(역할 기반 액세스 제어) 관리](../role-based-access-control/role-assignments-template.md)

다음 관련 문서를 참조하세요.
- [Azure Active Directory를 사용하여 Azure SignalR Service에 액세스하는 애플리케이션 인증](authenticate-application.md)
- [Azure Active Directory를 사용하여 Azure SignalR Service에 대한 액세스 권한 부여](authorize-access-azure-active-directory.md)

---
title: Azure Relay 리소스의 관리 ID를 사용하여 인증(미리 보기)
description: 이 문서에서는 관리 ID를 사용하여 Azure Relay 리소스로 액세스하는 방법을 설명합니다.
ms.topic: article
ms.date: 07/19/2021
ms.openlocfilehash: fa82f41056f3a5a8617bab572b8b4f312861419f
ms.sourcegitcommit: 6f21017b63520da0c9d67ca90896b8a84217d3d3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/23/2021
ms.locfileid: "114654223"
---
# <a name="authenticate-a-managed-identity-with-azure-active-directory-to-access-azure-relay-resources-preview"></a>Azure Active Directory에 관리 ID를 인증하여 Azure Relay 리소스 액세스(미리 보기)
[Azure 리소스용 관리 ID](../active-directory/managed-identities-azure-resources/overview.md)는 애플리케이션 코드가 실행되는 배포와 관련된 보안 ID를 만들 수 있도록 하는 Azure 간 기능입니다. 그런 다음 애플리케이션에 필요한 특정 Azure 리소스에 액세스하기 위한 사용자 지정 권한을 부여하는 액세스 제어 역할에 해당 ID를 연결할 수 있습니다.

관리 ID를 사용하는 경우 Azure 플랫폼에서 이 런타임 ID를 관리합니다. ID 자체나 액세스해야 하는 리소스에 대한 액세스 키를 애플리케이션 코드나 구성에 저장하고 보호할 필요는 없습니다. Azure App Service 애플리케이션 내에서 실행되거나 Azure 리소스용 관리 ID를 지원하도록 설정된 가상 머신에서 실행되는 Relay 클라이언트 앱은 SAS 규칙과 키 또는 기타 액세스 토큰을 처리할 필요가 없습니다. 클라이언트 앱은 Relay 네임스페이스의 엔드포인트 주소만 있으면 됩니다. 앱이 연결되면 Relay는 이 문서 뒷부분의 예제에 나와 있는 작업에서 관리 ID의 컨텍스트를 클라이언트에 바인딩합니다. 관리 ID와 연결된 Relay 클라이언트는 권한이 부여된 모든 작업을 수행할 수 있습니다. 관리 ID를 Relay 역할에 연결하는 방식을 통해 권한이 부여됩니다.

[!INCLUDE [relay-roles](./includes/relay-roles.md)]

## <a name="enable-managed-identity"></a>관리 ID 사용
먼저 Azure Relay 엔터티(하이브리드 연결 또는 WCF 릴레이)에 액세스해야 하는 Azure 리소스에 대해 관리 ID를 사용하도록 설정합니다. 예를 들어 릴레이 클라이언트 애플리케이션이 Azure VM에서 실행되는 경우 [Azure VM에 대한 관리 ID 구성](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md) 문서의 지침에 따라 VM에 대한 관리 ID를 사용하도록 설정합니다. 이 설정을 사용하도록 설정하면 새 관리 서비스 ID가 Azure AD(Azure Active Directory)에서 생성됩니다.

관리 ID를 지원하는 서비스 목록은 [Azure 리소스에 대한 관리 ID를 지원하는 서비스](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)를 참조하세요.

## <a name="assign-an-azure-relay-role-to-the-managed-identity"></a>관리 ID에 Azure Relay 역할 할당
관리 ID를 사용하도록 설정한 후 Azure Relay 역할(Azure Relay 소유자, Azure Relay 수신기 또는 Azure Relay 송신기) 중 하나를 적절한 범위의 ID에 할당합니다. Azure 역할이 관리 ID에 할당되면 적절한 범위에서 Relay 엔터티에 대한 액세스 권한이 관리 ID에 부여됩니다.

다음 섹션에서는 Azure VM 인스턴스에서 관리 ID로 실행되는 간단한 애플리케이션을 사용하여 Relay 리소스에 액세스합니다.

## <a name="sample-app-on-vm-accessing-relay-entities"></a>Relay 엔터티에 액세스하는 VM의 샘플 앱

1. GitHub에서 [하이브리드 연결 샘플 콘솔 애플리케이션](https://github.com/Azure/azure-relay/tree/master/samples/hybrid-connections/dotnet/rolebasedaccesscontrol)을 컴퓨터로 다운로드합니다.
1. [Azure VM을 만듭니다](../virtual-machines/windows/quick-create-portal.md). 이 샘플에서는 Windows 10 이미지를 사용합니다. 
1. Azure VM에 대해 시스템 할당 ID 또는 사용자 할당 ID를 사용하도록 설정합니다. 자세한 내용은 [VM에 ID 사용](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)을 참조하세요. 
1. 원하는 범위(Relay 엔터티, Relay 네임스페이스, 리소스 그룹, 구독)에서 관리 서비스 ID에 Relay 역할 중 하나를 할당합니다. 세부 단계에 대해서는 [Azure Portal을 사용하여 Azure 역할 할당](../role-based-access-control/role-assignments-portal.md)을 참조하세요.
1. [추가 정보 문서](https://github.com/Azure/azure-relay/tree/master/samples/hybrid-connections/dotnet/rolebasedaccesscontrol#rolebasedaccesscontrol-hybrid-connection-sample)의 지침에 따라 로컬 컴퓨터에서 로컬로 콘솔 앱을 빌드합니다. 
1. \<your local path\>\RoleBasedAccessControl\bin\Debug 폴더 아래의 실행 파일을 VM에 복사합니다. RDP를 사용하여 Azure VM에 연결할 수 있습니다. 자세한 내용은 [Windows를 실행하는 Azure 가상 머신에 연결하고 로그인하는 방법](../virtual-machines/windows/connect-logon.md)을 참조하세요.
1. [추가 정보 문서](https://github.com/Azure/azure-relay/tree/master/samples/hybrid-connections/dotnet/rolebasedaccesscontrol#rolebasedaccesscontrol-hybrid-connection-sample)의 지침에 따라 Azure VM에서 RoleBasedAccessControl.exe를 실행합니다. 

    > [!NOTE]
    > 위와 동일한 단계를 수행하여 [WCF Relay용 콘솔 애플리케이션](https://github.com/Azure/azure-relay/tree/master/samples/wcf-relay/RoleBasedAccessControl)을 실행합니다.

#### <a name="highlighted-code-from-the-sample"></a>샘플에서 강조 표시된 코드
Azure AD 인증을 사용하여 Azure Relay 서비스에 연결하는 방법을 보여 주는 샘플 코드는 다음과 같습니다.  

1. `TokenProvider.CreateManagedIdentityTokenProvider` 메서드를 사용하여 [TokenProvider](/dotnet/api/microsoft.azure.relay.tokenprovider) 개체를 만듭니다. 
    
    - **시스템이 할당한 관리 ID** 를 사용하는 경우:
        ```csharp
        TokenProvider.CreateManagedIdentityTokenProvider();
        ```
    - **사용자가 할당한 관리 ID** 를 사용하는 경우 Azure Portal의 **관리 ID** 페이지에서 사용자 할당 ID의 **클라이언트 ID** 를 가져옵니다. 자세한 내용은 [사용자가 할당한 관리 ID 나열](../active-directory/managed-identities-azure-resources/how-manage-user-assigned-managed-identities.md?pivots=identity-mi-methods-azp#list-user-assigned-managed-identities)을 참조하세요.
        ```csharp
        var managedCredential = new ManagedIdentityCredential(clientId);
        tokenProvider = TokenProvider.CreateManagedIdentityTokenProvider(managedCredential);    
        ```
1. 이전 단계에서 만든 하이브리드 연결 URI와 토큰 공급자를 전달하여 [HybridConnectionListener](/dotnet/api/microsoft.azure.relay.hybridconnectionlistener.-ctor#Microsoft_Azure_Relay_HybridConnectionListener__ctor_System_Uri_Microsoft_Azure_Relay_TokenProvider_) 또는 [HybridConnectionClient](/dotnet/api/microsoft.azure.relay.hybridconnectionclient.-ctor#microsoft-azure-relay-hybridconnectionclient-ctor(system-uri-microsoft-azure-relay-tokenprovider)) 개체를 만듭니다.

    **수신기:**
    ```csharp
    var listener = new HybridConnectionListener(hybridConnectionUri, tokenProvider);    
    ```
    
    **송신기:**
    ```csharp
    var sender = new HybridConnectionClient(hybridConnectionUri, tokenProvider);    
    ```

## <a name="next-steps"></a>다음 단계
Azure 릴레이에 대한 자세한 내용은 다음 항목을 참조하세요.
- [릴레이란?](relay-what-is-it.md)
- [Azure Relay 하이브리드 연결 WebSockets 시작](relay-hybrid-connections-dotnet-get-started.md)
- [Azure Relay 하이브리드 연결 HTTP 요청 시작](relay-hybrid-connections-http-requests-dotnet-get-started.md)




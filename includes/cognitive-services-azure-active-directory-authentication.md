---
author: erhopf
ms.author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 05/11/2020
ms.openlocfilehash: d8bf27b704db27192dcbf3b36c5fba614e310957
ms.sourcegitcommit: bc29cf4472118c8e33e20b420d3adb17226bee3f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2021
ms.locfileid: "113503038"
---
## <a name="authenticate-with-azure-active-directory"></a>Azure Active Directory를 사용하여 인증

> [!IMPORTANT]
> AAD 인증은 항상 Azure 리소스의 사용자 지정 하위 도메인 이름과 함께 사용해야 합니다. [지역별 엔드포인트](../articles/cognitive-services/cognitive-services-custom-subdomains.md#is-there-a-list-of-regional-endpoints)는 AAD 인증을 지원하지 않습니다.

이전 섹션에서는 단일 서비스 또는 다중 서비스 구독 키를 사용하여 Azure Cognitive Services에 대해 인증하는 방법을 살펴보았습니다. 이러한 키를 이용하면 빠르고 쉽게 개발을 시작할 수 있지만 Azure RBAC(역할 기반 액세스 제어)를 필요로 하는 더 복잡한 시나리오에서는 한계가 있습니다. AAD(Azure Active Directory)를 사용하여 인증하는 데 필요한 사항을 살펴보겠습니다.

다음 섹션에서는 Azure Cloud Shell 환경 또는 Azure CLI를 사용하여 하위 도메인을 만들고, 역할을 할당하고, Azure Cognitive Services를 호출하는 전달자 토큰을 가져옵니다. 작업하면서 어려움에 처할 경우를 위해 각 세션에서는 Azure Cloud Shell/Azure CLI의 각 명령에 대해 사용 가능한 모든 옵션이 있는 링크가 제공됩니다.

### <a name="create-a-resource-with-a-custom-subdomain"></a>사용자 지정 하위 도메인을 사용하여 리소스 만들기

먼저 사용자 지정 하위 도메인을 만들어야 합니다. 사용자 지정 하위 도메인 이름이 없는 기존 Cognitive Services 리소스를 사용하려는 경우 [Cognitive Services 사용자 지정 하위 도메인](../articles/cognitive-services/cognitive-services-custom-subdomains.md#how-does-this-impact-existing-resources)의 지침에 따라 리소스에 대한 사용자 지정 하위 도메인을 사용하도록 설정합니다.

1. Azure Cloud Shell을 열어 시작합니다. 그런 다음, [구독을 선택](/powershell/module/az.accounts/set-azcontext)합니다.

   ```powershell-interactive
   Set-AzContext -SubscriptionName <SubscriptionName>
   ```

2. 다음으로 사용자 지정 하위 도메인을 사용하여 [Cognitive Services 리소스를 만듭니다](/powershell/module/az.cognitiveservices/new-azcognitiveservicesaccount). 하위 도메인 이름은 전역적으로 고유해야 하며 특수 문자(예: ".", "!", ",")를 포함할 수 없습니다.

   ```powershell-interactive
   $account = New-AzCognitiveServicesAccount -ResourceGroupName <RESOURCE_GROUP_NAME> -name <ACCOUNT_NAME> -Type <ACCOUNT_TYPE> -SkuName <SUBSCRIPTION_TYPE> -Location <REGION> -CustomSubdomainName <UNIQUE_SUBDOMAIN>
   ```

3. 성공하는 경우 **엔드포인트** 는 리소스에 고유한 하위 도메인 이름을 표시해야 합니다.


### <a name="assign-a-role-to-a-service-principal"></a>서비스 주체에 역할 할당

이제 리소스와 연결된 사용자 지정 하위 도메인이 있으므로 서비스 주체에 역할을 할당해야 합니다.

> [!NOTE]
> Azure 역할 할당을 전파하는 데 최대 5분이 소요될 수 있습니다.

1. 먼저 [AAD 애플리케이션](/powershell/module/Az.Resources/New-AzADApplication)을 등록합니다.

   ```powershell-interactive
   $SecureStringPassword = ConvertTo-SecureString -String <YOUR_PASSWORD> -AsPlainText -Force

   $app = New-AzADApplication -DisplayName <APP_DISPLAY_NAME> -IdentifierUris <APP_URIS> -Password $SecureStringPassword
   ```

   다음 단계에서 **ApplicationId** 가 필요합니다.

2. 다음으로 AAD 애플리케이션에 대한 [서비스 주체를 만들어야](/powershell/module/az.resources/new-azadserviceprincipal) 합니다.

   ```powershell-interactive
   New-AzADServicePrincipal -ApplicationId <APPLICATION_ID>
   ```

   >[!NOTE]
   > Azure Portal에 애플리케이션을 등록하면 이 단계가 완료됩니다.

3. 마지막 단계는 서비스 주체(리소스로 범위 지정)에 ["Cognitive Services 사용자" 역할을 할당](/powershell/module/az.Resources/New-azRoleAssignment)하는 것입니다. 역할을 할당함으로써 서비스 주체에게 이 리소스에 대한 액세스 권한을 부여하게 됩니다. 동일한 서비스 주체에게 구독에서 여러 리소스에 대한 액세스 권한을 부여할 수 있습니다.
   >[!NOTE]
   > 애플리케이션에 대한 ObjectId가 아닌 서비스 주체의 ObjectId가 사용됩니다.
   > ACCOUNT_ID는 사용자가 만든 Cognitive Services 계정의 Azure 리소스 ID입니다. Azure Portal에서 리소스의 "속성"에서 Azure 리소스 ID를 찾을 수 있습니다.

   ```azurecli-interactive
   New-AzRoleAssignment -ObjectId <SERVICE_PRINCIPAL_OBJECTID> -Scope <ACCOUNT_ID> -RoleDefinitionName "Cognitive Services User"
   ```

### <a name="sample-request"></a>샘플 요청

이 샘플에서는 암호를 사용하여 서비스 주체를 인증합니다. 그런 다음, 제공된 토큰을 사용하여 Computer Vision API를 호출합니다.

1. **TenantId** 가져오기:
   ```powershell-interactive
   $context=Get-AzContext
   $context.Tenant.Id
   ```

2. 토큰 가져오기:
   > [!NOTE]
   > Azure Cloud Shell을 사용하는 경우 `SecureClientSecret` 클래스를 사용할 수 없습니다. 

   #### <a name="powershell"></a>[PowerShell](#tab/powershell)
   ```powershell-interactive
   $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList "https://login.windows.net/<TENANT_ID>"
   $secureSecretObject = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.SecureClientSecret" -ArgumentList $SecureStringPassword   
   $clientCredential = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential" -ArgumentList $app.ApplicationId, $secureSecretObject
   $token=$authContext.AcquireTokenAsync("https://cognitiveservices.azure.com/", $clientCredential).Result
   $token
   ```
   
   #### <a name="azure-cloud-shell"></a>[Azure Cloud Shell](#tab/azure-cloud-shell)
   ```Azure Cloud Shell
   $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList "https://login.windows.net/<TENANT_ID>"
   $clientCredential = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential" -ArgumentList $app.ApplicationId, <YOUR_PASSWORD>
   $token=$authContext.AcquireTokenAsync("https://cognitiveservices.azure.com/", $clientCredential).Result
   $token
   ``` 

   ---

3. Computer Vision API 호출하기:
   ```powershell-interactive
   $url = $account.Endpoint+"vision/v1.0/models"
   $result = Invoke-RestMethod -Uri $url  -Method Get -Headers @{"Authorization"=$token.CreateAuthorizationHeader()} -Verbose
   $result | ConvertTo-Json
   ```

다른 방법으로는 인증서를 사용하여 서비스 주체를 인증할 수 있습니다. 서비스 주체 외에도 다른 AAD 애플리케이션을 통해 위임된 사용 권한을 사용하면 사용자 계정도 지원합니다. 이 경우 토큰을 가져올 때 암호 또는 인증서 대신 사용자에게 2단계 인증을 요구하는 메시지가 표시됩니다.

## <a name="authorize-access-to-managed-identities"></a>관리 ID에 대한 액세스 권한 부여
 
Cognitive Services는 [Azure 리소스에 대한 관리 ID](../articles/active-directory/managed-identities-azure-resources/overview.md)를 사용하는 Azure AD(Azure Active Directory) 인증을 지원합니다. Azure 리소스의 관리 ID를 사용하면 Azure VM(가상 머신), 함수 앱, 가상 머신 확장 집합 및 기타 서비스에서 실행 중인 애플리케이션의 Azure AD 자격 증명을 사용하여 Cognitive Services 리소스에 대한 액세스 권한을 부여할 수 있습니다. Azure AD 인증과 함께 Azure 리소스의 관리 ID를 사용하면 클라우드에서 실행되는 애플리케이션에 자격 증명을 저장할 필요가 없습니다.  

### <a name="enable-managed-identities-on-a-vm"></a>VM에서 관리 ID 사용

Azure 리소스의 관리 ID를 사용하여 VM에서 Cognitive Services 리소스에 대한 액세스 권한을 부여하려면 VM에서 Azure 리소스의 관리 ID를 사용하도록 설정해야 합니다. Azure 리소스의 관리 ID를 사용하도록 설정하는 방법을 알아보려면 다음을 참조하세요.

- [Azure Portal](../articles/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Azure PowerShell](../articles/active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Azure CLI](../articles/active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Azure Resource Manager 템플릿](../articles/active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Azure Resource Manager 클라이언트 라이브러리](../articles/active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

관리 ID에 대한 자세한 내용은 [Azure 리소스의 관리 ID](../articles/active-directory/managed-identities-azure-resources/overview.md)를 참조하세요.
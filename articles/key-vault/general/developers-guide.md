---
title: Azure Key Vault 개발자 가이드
description: 개발자는 Microsoft Azure 환경 내에서 Azure Key Vault를 사용하여 암호화 키를 관리할 수 있습니다.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 10/05/2020
ms.author: mbaldwin
ms.openlocfilehash: 7c7bc9f0c520529506e988f6980756a38ef7c8b3
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129997557"
---
# <a name="azure-key-vault-developers-guide"></a>Azure Key Vault 개발자 가이드

Azure Key Vault 사용하면 애플리케이션 내에서 중요한 정보에 안전하게 액세스할 수 있습니다.

- 키, 비밀 및 인증서는 코드를 직접 작성하지 않고도 보호되며 애플리케이션에서 쉽게 사용할 수 있습니다.
- 고객이 고유한 키, 비밀, 인증서를 소유하고 관리하도록 허용하므로 핵심 소프트웨어 기능을 제공하는 데 집중할 수 있습니다. 이러한 방식으로 애플리케이션은 고객의 테넌트 키, 비밀 및 인증서에 대한 책임 또는 잠재적 책임을 소유하지 않습니다.
- 애플리케이션은 서명 및 암호화에 키를 사용할 수 있지만 키 관리는 애플리케이션 외부에 유지할 수 있습니다. 자세한 내용은 [키 정보](../keys/about-keys.md)를 참조하세요.
- 암호, 액세스 키 및 SAS 토큰과 같은 자격 증명을 Key Vault 비밀로 저장하여 관리할 수 있습니다. 자세한 내용은 [비밀 정보](../secrets/about-secrets.md)를 참조하세요.
- 인증서를 관리합니다. 자세한 내용은 [인증서 정보](../certificates/about-certificates.md)를 참조하세요.

Azure Key Vault 대한 일반적인 내용은 [Azure Key Vault 정보를](overview.md)참조하세요.

## <a name="public-previews"></a>공개 미리 보기

새로운 Key Vault 기능의 공개 미리 보기가 정기적으로 릴리스됩니다. 퍼블릭 미리 보기 기능을 사용해 보고, 피드백 메일 주소인 azurekeyvault@microsoft.com을 통해 의견을 알려주세요.

## <a name="create-and-manage-key-vaults"></a>키 자격 증명 모음 만들기 및 관리

다른 Azure 서비스와 마찬가지로 Key Vault [Azure Resource Manager](../../azure-resource-manager/management/overview.md)통해 관리됩니다. Azure Resource Manager는 Azure용 배포 및 관리 서비스입니다. Azure 계정에서 리소스를 만들고, 업데이트하고, 삭제하는 데 사용할 수 있습니다. 

[Azure RBAC(역할 기반 액세스 제어)는](../../role-based-access-control/overview.md) 관리 [계층(관리 평면이라고도](security-features.md#managing-administrative-access-to-key-vault)함)에 대한 액세스를 제어합니다. Key Vault 관리 평면을 사용하여 키 자격 증명 모음 및 해당 특성(액세스 정책 포함)을 만들고 관리합니다. *데이터 평면을* 사용하여 키, 인증서 및 비밀을 관리합니다. 

미리 정의된 Key Vault 기여자 역할을 사용하여 Key Vault 대한 관리 액세스 권한을 부여할 수 있습니다.     

### <a name="apis-and-sdks-for-key-vault-management"></a>키 자격 증명 모음 관리를 위한 API 및 SDK

| Azure CLI | PowerShell | REST API | 리소스 관리자 | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[참조](/cli/azure/keyvault)<br>[빠른 시작](quick-create-cli.md)|[참조](/powershell/module/az.keyvault)<br>[빠른 시작](quick-create-powershell.md)|[참조](/rest/api/keyvault/)|[참조](/azure/templates/microsoft.keyvault/vaults)<br>[빠른 시작](./vault-create-template.md)|[참조](/dotnet/api/microsoft.azure.management.keyvault)|[참조](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault)|[참조](/java/api/com.microsoft.azure.management.keyvault)|[참조](/javascript/api/@azure/arm-keyvault)|

설치 패키지 및 소스 코드는 클라이언트 라이브러리 를 [참조하세요.](client-libraries.md)

## <a name="authenticate-to-key-vault-in-code"></a>코드에서 Key Vault에 인증

Key Vault Azure AD(Azure Active Directory) 인증을 사용하며, 액세스 권한을 부여하려면 Azure AD 보안 주체가 필요합니다. Azure AD 보안 주체는 사용자, 애플리케이션 서비스 주체, [Azure 리소스에 대한 관리 ID](../../active-directory/managed-identities-azure-resources/overview.md)또는 이러한 유형의 그룹일 수 있습니다.

### <a name="authentication-best-practices"></a>인증 모범 사례

Azure에 배포된 애플리케이션에 관리 ID를 사용하는 것이 좋습니다. 관리 ID를 지원하지 않는 Azure 서비스를 사용하거나 애플리케이션이 온-프레미스에 배포된 경우 [인증서가 있는 서비스 주체가](../../active-directory/develop/howto-create-service-principal-portal.md) 가능한 대안입니다. 이 시나리오에서는 인증서를 Key Vault 저장하고 자주 회전해야 합니다.

개발 및 테스트 환경에 대한 비밀이 있는 서비스 주체를 사용합니다. 로컬 개발 및 Azure Cloud Shell 사용자 주체를 사용합니다.

각 환경에서 이러한 보안 주체를 권장합니다.
- **프로덕션 환경:** 인증서가 있는 관리 ID 또는 서비스 주체입니다.
- **테스트 및 개발 환경:** 관리 ID, 인증서가 있는 서비스 주체 또는 비밀이 있는 서비스 주체입니다.
- **로컬 개발:** 비밀이 있는 사용자 주체 또는 서비스 주체입니다.

### <a name="azure-identity-client-libraries"></a>Azure ID 클라이언트 라이브러리

위의 인증 시나리오는 *Azure ID 클라이언트 라이브러리에서* 지원되며 Key Vault SDK와 통합됩니다. 코드를 변경하지 않고 환경 및 플랫폼에서 Azure ID 클라이언트 라이브러리를 사용할 수 있습니다. 라이브러리는 Azure CLI, Visual Studio, Visual Studio Code 및 기타 방법을 통해 Azure 사용자에 로그인한 사용자로부터 인증 토큰을 자동으로 검색합니다. 

Azure ID 클라이언트 라이브러리에 대한 자세한 내용은 다음을 참조하세요.

| .NET | Python | Java | JavaScript |
|--|--|--|--|
|[Azure ID SDK .NET](/dotnet/api/overview/azure/identity-readme)|[Azure ID SDK Python](/python/api/overview/azure/identity-readme)|[Azure ID SDK Java](/java/api/overview/azure/identity-readme)|[Azure ID SDK JavaScript](/javascript/api/overview/azure/identity-readme)|     

> [!Note]
> .NET SDK 버전 3을 Key Vault [앱 인증 라이브러리를](/dotnet/api/overview/azure/service-to-service-authentication) 권장했지만 이제는 사용되지 않습니다. Key Vault .NET SDK 버전 4로 마이그레이션하려면 [AppAuthentication to Azure.Identity 마이그레이션 지침을](/dotnet/api/overview/azure/app-auth-migration)따르세요.

애플리케이션에서 Key Vault에 인증하는 방법에 관한 자습서는 다음을 참조하세요.
- [.NET에서 가상 머신이 있는 Azure Key Vault 사용](./tutorial-net-virtual-machine.md)
- [Python에서 가상 머신이 있는 Azure Key Vault 사용](./tutorial-python-virtual-machine.md)
- [관리 ID를 사용하여 .NET에서 Key Vault를 Azure 웹앱에 연결](./tutorial-net-create-vault-azure-web-app.md)

## <a name="manage-keys-certificates-and-secrets"></a>키, 인증서, 비밀 관리

데이터 평면은 키, 인증서 및 비밀에 대한 액세스를 제어합니다. 데이터 평면을 통한 액세스 제어를 위해 로컬 자격 증명 모음 액세스 정책 또는 Azure RBAC를 사용할 수 있습니다.

### <a name="apis-and-sdks-for-keys"></a>키에 대한 API 및 SDK

| Azure CLI | PowerShell | REST API | 리소스 관리자 | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[참조](/cli/azure/keyvault/key)<br>[빠른 시작](../keys/quick-create-cli.md)|[참조](/powershell/module/az.keyvault/)<br>[빠른 시작](../keys/quick-create-powershell.md)|[참조](/rest/api/keyvault/#key-operations)|[참조](/azure/templates/microsoft.keyvault/vaults/keys)<br>[빠른 시작](../keys/quick-create-template.md)|[참조](/dotnet/api/azure.security.keyvault.keys)<br>[빠른 시작](../keys/quick-create-net.md)|[참조](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault)<br>[빠른 시작](../keys/quick-create-python.md)|[참조](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-keys/4.2.0/index.html)<br>[빠른 시작](../keys/quick-create-java.md)|[참조](/javascript/api/@azure/keyvault-keys/)<br>[빠른 시작](../keys/quick-create-node.md)|

### <a name="apis-and-sdks-for-certificates"></a>인증서에 대한 API 및 SDK

| Azure CLI | PowerShell | REST API | 리소스 관리자 | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[참조](/cli/azure/keyvault/certificate)<br>[빠른 시작](../certificates/quick-create-cli.md)|[참조](/powershell/module/az.keyvault)<br>[빠른 시작](../certificates/quick-create-powershell.md)|[참조](/rest/api/keyvault/#certificate-operations)|해당 없음|[참조](/dotnet/api/azure.security.keyvault.certificates)<br>[빠른 시작](../certificates/quick-create-net.md)|[참조](/python/api/overview/azure/keyvault-certificates-readme)<br>[빠른 시작](../certificates/quick-create-python.md)|[참조](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-certificates/4.1.0/index.html)<br>[빠른 시작](../certificates/quick-create-java.md)|[참조](/javascript/api/@azure/keyvault-certificates/)<br>[빠른 시작](../certificates/quick-create-node.md)|

### <a name="apis-and-sdks-for-secrets"></a>비밀에 대한 API 및 SDK

| Azure CLI | PowerShell | REST API | 리소스 관리자 | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[참조](/cli/azure/keyvault/secret)<br>[빠른 시작](../secrets/quick-create-cli.md)|[참조](/powershell/module/az.keyvault/)<br>[빠른 시작](../secrets/quick-create-powershell.md)|[참조](/rest/api/keyvault/#secret-operations)|[참조](/azure/templates/microsoft.keyvault/vaults/secrets)<br>[빠른 시작](../secrets/quick-create-template.md)|[참조](/dotnet/api/azure.security.keyvault.secrets)<br>[빠른 시작](../secrets/quick-create-net.md)|[참조](/python/api/overview/azure/keyvault-secrets-readme)<br>[빠른 시작](../secrets/quick-create-python.md)|[참조](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-secrets/4.2.0/index.html)<br>[빠른 시작](../secrets/quick-create-java.md)|[참조](/javascript/api/@azure/keyvault-secrets/)<br>[빠른 시작](../secrets/quick-create-node.md)|

### <a name="usage-of-secrets"></a>비밀 사용
Azure Key Vault 사용하여 애플리케이션의 비밀만 저장합니다. Key Vault 저장해야 하는 비밀의 예는 다음과 같습니다.

- 클라이언트 애플리케이션 비밀
- 연결 문자열
- 암호
- 공유 액세스 키
- SSH 키

사용자 이름 및 애플리케이션 ID와 같은 모든 비밀 관련 정보는 비밀에 태그로 저장할 수 있습니다. 다른 중요한 구성 설정의 경우 [Azure App Configuration](../../azure-app-configuration/overview.md)사용해야 합니다.
 
### <a name="references"></a>참조 

설치 패키지 및 소스 코드는 클라이언트 라이브러리 를 [참조하세요.](client-libraries.md)

Key Vault 데이터 평면 보안에 대한 자세한 내용은 [Azure Key Vault 보안 기능을 참조하세요.](security-features.md)

## <a name="use-key-vault-in-applications"></a>애플리케이션에서 Key Vault 사용

Key Vault 최신 기능을 활용하려면 애플리케이션에서 비밀, 인증서 및 키를 사용하기 위해 사용 가능한 Key Vault SDK를 사용하는 것이 좋습니다. Key Vault SDK 및 REST API 제품에 대한 새로운 기능이 릴리스되면 업데이트되며 모범 사례 및 지침을 따릅니다.

기본 시나리오의 경우 Microsoft 파트너 또는 오픈 소스 커뮤니티에서 제공하는 지원을 통해 간소화된 사용을 위한 다른 라이브러리 및 통합 솔루션이 있습니다.

인증서의 경우 다음을 사용할 수 있습니다.

- Key Vault VM(가상 머신) 확장은 Azure Key Vault에 저장된 인증서의 자동 새로 고침을 제공합니다. 자세한 내용은 다음을 참조하세요. 
  - [Windows용 Key Vault 가상 머신 확장](../../virtual-machines/extensions/key-vault-windows.md)
  - [Linux용 Key Vault 가상 머신 확장](../../virtual-machines/extensions/key-vault-linux.md)
  - [Azure Arc 지원 서버용 가상 머신 확장 Key Vault](../../azure-arc/servers/manage-vm-extensions.md#azure-key-vault-vm-extension)
- Azure App Service 통합- Key Vault 인증서를 가져오고 자동으로 새로 고칠 수 있습니다. 자세한 내용은 [Key Vault 인증서 가져오기를](../../app-service/configure-ssl-certificate.md#import-a-certificate-from-key-vault)참조하세요.

비밀의 경우 다음을 사용할 수 있습니다.

- App Service 애플리케이션 설정을 통해 비밀을 Key Vault. 자세한 내용은 [App Service 및 Azure Functions의 Key Vault 참조 사용](../../app-service/app-service-key-vault-references.md)을 참조하세요.
- Azure VM에서 호스트되는 애플리케이션에 대한 App Configuration 서비스를 통해 비밀을 Key Vault. 자세한 내용은 [App Configuration 및 Key Vault 애플리케이션 구성을](/samples/azure/azure-sdk-for-net/app-secrets-configuration/)참조하세요.

## <a name="code-examples"></a>코드 예제

애플리케이션에서 Key Vault 사용하는 전체 예제는 [Azure Key Vault 코드 샘플을 참조하세요.](https://azure.microsoft.com/resources/samples/?service=key-vault) 

## <a name="task-specific-guidance"></a>작업별 지침

다음 문서 및 시나리오에서는 Azure Key Vault 사용에 대한 작업별 지침을 제공합니다.

- 키 자격 증명 모음에 액세스하려면 클라이언트 애플리케이션이 다양한 기능을 위해 여러 엔드포인트에 액세스할 수 있어야 합니다. [방화벽 뒤의 Key Vault 액세스를](access-behind-firewall.md)참조하세요. 
- Azure VM에서 실행되는 클라우드 애플리케이션에는 인증서가 필요합니다. 이 인증서를 이 VM으로 어떻게 얻을 수 있나요? Windows Key Vault [가상 머신 확장](../../virtual-machines/extensions/key-vault-windows.md) 또는 [Linux용 Key Vault 가상 머신 확장을](../../virtual-machines/extensions/key-vault-linux.md)참조하세요.
- Azure CLI, PowerShell 또는 Azure Portal 사용하여 액세스 정책을 할당하려면 Key Vault 액세스 [정책 할당을](assign-access-policy.md)참조하세요. 
- 일시 삭제를 사용하도록 설정된 키 자격 증명 모음 및 다양한 키 자격 증명 모음 개체의 사용 및 수명 주기에 대한 지침은 [일시 삭제 및 제거 보호를 사용하여 복구 관리 Azure Key Vault](./key-vault-recovery.md)참조하세요.
- 배포하는 동안 보안 값(예: 암호)을 매개 변수로 전달해야 하는 경우 해당 값을 키 자격 증명 모음에 비밀로 저장하고 다른 Resource Manager 템플릿의 값을 참조할 수 있습니다. [배포하는 동안 Azure Key Vault 사용하여 보안 매개 변수 값 전달을](../../azure-resource-manager/templates/key-vault-parameter.md)참조하세요.

## <a name="integration-with-key-vault"></a>Key Vault 통합

다음 서비스 및 시나리오는 Key Vault 사용하거나 통합합니다.

- [미사용 데이터 암호화는](../../security/fundamentals/encryption-atrest.md) 데이터가 유지될 때 데이터의 인코딩(암호화)을 허용합니다. 데이터 암호화 키는 액세스를 제한하기 위해 Azure Key Vault의 키 암호화 키로 암호화되는 경우가 많습니다.
- [Azure Information Protection](/azure/information-protection/plan-implement-tenant-key) 사용하면 고유한 테넌트 키를 관리할 수 있습니다. 예를 들어, Microsoft가 테넌트 키를 관리하는 대신(기본값) 테넌트 키를 직접 관리하여 해당 조직에 적용되는 특정 규정을 준수할 수 있습니다. 사용자 고유의 테넌트 키 관리는 BYOK(Bring Your Own Key)라고도 *합니다.*
- [Azure Private Link](private-link-service.md) 사용하면 가상 네트워크의 프라이빗 엔드포인트를 통해 Azure 서비스(예: Azure Key Vault, Azure Storage 및 Azure Cosmos DB)와 Azure 호스팅 고객/파트너 서비스에 액세스할 수 있습니다.
- Key Vault Azure Event Grid 통합하면 [Key Vault](../../event-grid/event-schema-key-vault.md) 저장된 비밀의 상태가 변경될 때 사용자에게 알림을 받을 수 있습니다. 새 버전의 비밀을 애플리케이션에 배포하거나 만료에 가까운 비밀을 순환하여 중단을 방지할 수 있습니다.
- Key Vault 원치 않는 액세스로부터 [Azure DevOps](/azure/devops/pipelines/release/azure-key-vault) 비밀을 보호합니다.
- Key Vault 저장된 비밀을 사용하여 Azure Databricks [에서 Azure Storage 연결합니다.](./integrate-databricks-blob-storage.md)
- Kubernetes에서 비밀 저장소 [CSI 드라이버에](./key-vault-integrate-kubernetes.md) 대한 Azure Key Vault 공급자를 구성하고 실행합니다. 

## <a name="key-vault-overviews-and-concepts"></a>Key Vault 개요 및 개념

자세한 정보:

- 삭제가 실수로 또는 의도적인 것인지에 관계없이 삭제된 개체의 복구를 허용하는 기능은 [일시 삭제 개요 Azure Key Vault 참조하세요.](soft-delete-overview.md)
- 앱에 대한 제한 및 접근 방식의 기본 개념은 [Azure Key Vault 제한 지침을](overview-throttling.md)참조하세요.
- 지역과 보안 영역 간의 관계는 [Azure Key Vault 보안 세계와 지리적 경계를 참조하세요.](overview-security-worlds.md)

## <a name="social"></a>소셜

- [Key Vault 블로그](/archive/blogs/kv/)
- [Microsoft Q&A](/answers/products/)
- [Key Vault 대한 Stack Overflow](https://stackoverflow.com/questions/tagged/azure-keyvault)

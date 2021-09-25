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
ms.openlocfilehash: 275013b82866b7cb49488edfc0e63e71a04e364a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128670351"
---
# <a name="azure-key-vault-developers-guide"></a>Azure Key Vault 개발자 가이드

Key Vault를 사용하면 애플리케이션 내에서 중요한 정보를 안전하게 액세스할 수 있습니다.

- 키, 비밀, 인증서를 코드 작성 없이 보호하고 애플리케이션에서 쉽게 사용할 수 있습니다.
- 고객이 고유한 키, 비밀, 인증서를 소유하고 관리하도록 허용하므로 핵심 소프트웨어 기능을 제공하는 데 집중할 수 있습니다. 이러한 이유로 애플리케이션에는 고객의 테넌트 키, 비밀, 인증서에 대한 책임 또는 잠재적인 법적 책임이 없습니다.
- 애플리케이션에서 서명 및 암호화를 위해 키를 사용할 수 있지만, 키 관리는 애플리케이션 외부에 유지할 수 있습니다. 키에 대한 자세한 내용은 [키 정보](../keys/about-keys.md)를 참조하세요.
- 암호, 액세스 키, sas 토큰 등의 자격 증명을 Key Vault에 비밀로 저장하여 관리할 수 있습니다. [비밀 정보](../secrets/about-secrets.md)를 참조하세요.
- 인증서를 관리합니다. 자세한 내용은 [인증서 정보](../certificates/about-certificates.md)를 참조하세요.

Azure Key Vault에 대한 일반적인 내용은 [Key Vault란?](overview.md)을 참조하세요.

## <a name="public-previews"></a>공개 미리 보기

새로운 Key Vault 기능의 공개 미리 보기가 정기적으로 릴리스됩니다. 퍼블릭 미리 보기 기능을 사용해 보고, 피드백 메일 주소인 azurekeyvault@microsoft.com을 통해 의견을 알려주세요.

## <a name="creating-and-managing-key-vaults"></a>주요 자격 증명 모음 만들기 및 관리

다른 Azure 서비스와 마찬가지로 Key Vault 관리는 Azure Resource Manager 서비스를 통해 수행됩니다. Azure Resource Manager는 Azure용 배포 및 관리 서비스입니다. Azure 계정에서 리소스를 만들고, 업데이트하고, 삭제할 수 있는 관리 계층을 제공합니다. 자세한 내용은 [Azure Resource Manager](../../azure-resource-manager/management/overview.md)를 참조하세요.

관리 계층 액세스는 [Azure 역할 기반 액세스 제어](../../role-based-access-control/overview.md)를 통해 제어됩니다. Key Vault에서 관리 계층(관리 또는 컨트롤 플레인이라고도 함)을 사용하여 키 자격 증명 모음 및 액세스 정책을 비롯한 해당 특성을 만들고 관리할 수 있지만 키, 비밀, 인증서는 데이터 평면에서 관리됩니다. 미리 정의된 `Key Vault Contributor` 역할을 사용하여 Key Vault에 대한 관리 권한을 부여할 수 있습니다.     

**Key Vault 관리를 위한 API 및 SDK:**

| Azure CLI | PowerShell | REST API | 리소스 관리자 | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[참조](/cli/azure/keyvault)<br>[빠른 시작](quick-create-cli.md)|[참조](/powershell/module/az.keyvault)<br>[빠른 시작](quick-create-powershell.md)|[참조](/rest/api/keyvault/)|[참조](/azure/templates/microsoft.keyvault/vaults)<br>[빠른 시작](./vault-create-template.md)|[참조](/dotnet/api/microsoft.azure.management.keyvault)|[참조](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault)|[참조](/java/api/com.microsoft.azure.management.keyvault)|[참조](/javascript/api/@azure/arm-keyvault)|

설치 패키지 및 소스 코드는 [클라이언트 라이브러리](client-libraries.md)를 참조하세요.

Key Vault 관리 평면에 대한 자세한 내용은 [Azure Key Vault 보안 기능](security-features.md)을 참조하세요.

## <a name="authenticate-to-key-vault-in-code"></a>코드에서 Key Vault에 인증

Key Vault는 액세스 권한을 부여할 Azure AD 보안 주체가 필요한 Azure AD 인증을 사용합니다. Azure AD 보안 주체는 사용자, 애플리케이션 서비스 주체, [Azure 리소스의 관리 ID](../../active-directory/managed-identities-azure-resources/overview.md), 보안 주체 유형의 그룹일 수 있습니다.

### <a name="authentication-best-practices"></a>인증 모범 사례

Azure에 배포된 애플리케이션에는 관리 ID를 사용하는 것이 좋습니다. 관리 ID를 지원하지 않는 Azure 서비스를 사용하거나 애플리케이션을 온-프레미스에 배포하는 경우 [인증서를 사용하는 서비스 주체](../../active-directory/develop/howto-create-service-principal-portal.md)가 가능한 대체 방법입니다. 이 시나리오에서는 인증서를 Key Vault 저장하고 자주 회전해야 합니다. 비밀은 사용하는 서비스 주체는 개발 및 테스트 환경에 사용할 수 있고 로컬에서나 Cloud Shell에서는 사용자 계정을 사용하는 것이 좋습니다.

환경별로 권장되는 보안 주체:
- **프로덕션 환경**:
  - 관리 ID 또는 인증서를 사용하는 서비스 주체
- **테스트 및 개발 환경**:
  - 관리 ID, 인증서를 사용하는 서비스 주체, 비밀을 사용하는 서비스 주체
- **로컬 개발**:
  - 사용자 계정 또는 비밀을 사용하는 서비스 주체

위의 인증 시나리오는 **Azure ID 클라이언트 라이브러리** 에서 지원되며 Key Vault SDK에 통합되어 있습니다. Azure ID 라이브러리는 다양한 환경과 플랫폼에서 코드를 변경하지 않고 사용할 수 있습니다. 또한 Azure ID는 Azure CLI, Visual Studio, Visual Studio Code 등을 사용하여 Azure에 로그인한 사용자의 인증 토큰을 자동으로 검색합니다. 

Azure ID 클라이언트 라이브러리에 대한 자세한 내용은 다음을 참조하세요.

**Azure ID 클라이언트 라이브러리**

| .NET | Python | Java | JavaScript |
|--|--|--|--|
|[Azure ID SDK .NET](/dotnet/api/overview/azure/identity-readme)|[Azure ID SDK Python](/python/api/overview/azure/identity-readme)|[Azure ID SDK Java](/java/api/overview/azure/identity-readme)|[Azure ID SDK JavaScript](/javascript/api/overview/azure/identity-readme)|     

>[!Note]
> Key Vault .NET SDK 버전 3에 권장되던 [앱 인증 라이브러리](/dotnet/api/overview/azure/service-to-service-authentication)는 현재 더 이상 사용되지 않습니다. [AppAuthentication to Azure.Identity Migration Guidance](/dotnet/api/overview/azure/app-auth-migration)(AppAuthentication에서 Azure.Identity로 마이그레이션 지침)에 따라 Key Vault .NET SDK 버전 4로 마이그레이션하세요.

애플리케이션에서 Key Vault에 인증하는 방법에 관한 자습서는 다음을 참조하세요.
- [.NET의 VM에 호스트된 애플리케이션에서 Key Vault에 인증](./tutorial-net-virtual-machine.md)
- [Python에서 VM에 호스트된 애플리케이션에서 Key Vault에 인증](./tutorial-python-virtual-machine.md)
- [App Service를 사용하여 Key Vault에 인증](./tutorial-net-create-vault-azure-web-app.md)

## <a name="manage-keys-certificates-and-secrets"></a>키, 인증서, 비밀 관리

키, 비밀, 인증서에 대한 액세스는 데이터 평면에서 제어됩니다. 데이터 평면 액세스 제어는 로컬 자격 증명 모음 액세스 정책 또는 Azure RBAC를 사용하여 수행할 수 있습니다.

### <a name="keys-apis-and-sdks"></a>키 API 및 SDK

| Azure CLI | PowerShell | REST API | 리소스 관리자 | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[참조](/cli/azure/keyvault/key)<br>[빠른 시작](../keys/quick-create-cli.md)|[참조](/powershell/module/az.keyvault/)<br>[빠른 시작](../keys/quick-create-powershell.md)|[참조](/rest/api/keyvault/#key-operations)|[참조](/azure/templates/microsoft.keyvault/vaults/keys)<br>[빠른 시작](../keys/quick-create-template.md)|[참조](/dotnet/api/azure.security.keyvault.keys)<br>[빠른 시작](../keys/quick-create-net.md)|[참조](/python/api/azure-mgmt-keyvault/azure.mgmt.keyvault)<br>[빠른 시작](../keys/quick-create-python.md)|[참조](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-keys/4.2.0/index.html)<br>[빠른 시작](../keys/quick-create-java.md)|[참조](/javascript/api/@azure/keyvault-keys/)<br>[빠른 시작](../keys/quick-create-node.md)|

### <a name="certificates-apis-and-sdks"></a>인증서 API 및 SDK

| Azure CLI | PowerShell | REST API | 리소스 관리자 | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[참조](/cli/azure/keyvault/certificate)<br>[빠른 시작](../certificates/quick-create-cli.md)|[참조](/powershell/module/az.keyvault)<br>[빠른 시작](../certificates/quick-create-powershell.md)|[참조](/rest/api/keyvault/#certificate-operations)|해당 없음|[참조](/dotnet/api/azure.security.keyvault.certificates)<br>[빠른 시작](../certificates/quick-create-net.md)|[참조](/python/api/overview/azure/keyvault-certificates-readme)<br>[빠른 시작](../certificates/quick-create-python.md)|[참조](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-certificates/4.1.0/index.html)<br>[빠른 시작](../certificates/quick-create-java.md)|[참조](/javascript/api/@azure/keyvault-certificates/)<br>[빠른 시작](../certificates/quick-create-node.md)|

### <a name="secrets-apis-and-sdks"></a>비밀 API 및 SDK

| Azure CLI | PowerShell | REST API | 리소스 관리자 | .NET | Python | Java | JavaScript |  
|--|--|--|--|--|--|--|--|
|[참조](/cli/azure/keyvault/secret)<br>[빠른 시작](../secrets/quick-create-cli.md)|[참조](/powershell/module/az.keyvault/)<br>[빠른 시작](../secrets/quick-create-powershell.md)|[참조](/rest/api/keyvault/#secret-operations)|[참조](/azure/templates/microsoft.keyvault/vaults/secrets)<br>[빠른 시작](../secrets/quick-create-template.md)|[참조](/dotnet/api/azure.security.keyvault.secrets)<br>[빠른 시작](../secrets/quick-create-net.md)|[참조](/python/api/overview/azure/keyvault-secrets-readme)<br>[빠른 시작](../secrets/quick-create-python.md)|[참조](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-security-keyvault-secrets/4.2.0/index.html)<br>[빠른 시작](../secrets/quick-create-java.md)|[참조](/javascript/api/@azure/keyvault-secrets/)<br>[빠른 시작](../secrets/quick-create-node.md)|

### <a name="secrets-usage"></a>비밀 사용
Azure Key Vault 비밀은 애플리케이션에 대한 비밀을 저장하는 데만 사용해야 합니다. 

Key Vault 저장해야 하는 비밀의 예:
    - 클라이언트 애플리케이션 비밀
    - 연결 문자열
    - 암호
    - 공유 액세스 키
    - SSH 키

또한 사용자 이름, 애플리케이션 ID와 같은 비밀 관련 정보는 비밀에 태그로 저장할 수 있습니다. 다른 중요한 구성 설정의 경우 [Azure App Configuration](../../azure-app-configuration/overview.md)사용해야 합니다.
 
### <a name="references"></a>참조 

설치 패키지 및 소스 코드는 [클라이언트 라이브러리](client-libraries.md)를 참조하세요.

Key Vault 데이터 평면 보안에 대한 자세한 내용은 [Azure Key Vault 보안 기능](security-features.md)을 참조하세요.

### <a name="using-key-vault-in-applications"></a>애플리케이션에서 Key Vault 사용

Key Vault 최신 기능을 활용하려면 애플리케이션에서 비밀, 인증서 및 키를 사용하는 데 사용 가능한 Key Vault SDK를 사용하는 것이 좋습니다. Key Vault SDK 및 REST API 제품에 대한 새로운 기능을 릴리스하고 모범 사례 및 지침을 따르면서 팀에서 릴리스됩니다.

#### <a name="libraries-and-integration-solutions-for-limited-usage-scenarios"></a>제한된 사용 시나리오에 대한 라이브러리 및 통합 솔루션
기본 시나리오의 경우 파트너 팀 또는 오픈 소스 커뮤니티에서 제공하는 지원을 통해 간소화된 사용을 위한 다른 솔루션이 있습니다.

##### <a name="certificates"></a>Certificates:
- Key Vault VM 확장은 Azure Key Vault에 저장된 인증서의 자동 새로 고침을 제공합니다. 
    - [Windows용 Key Vault 가상 머신 확장](../../virtual-machines/extensions/key-vault-windows.md)
    - [Linux용 Key Vault 가상 머신 확장](../../virtual-machines/extensions/key-vault-linux.md)
    - [Azure Arc 지원 서버용 가상 머신 확장 Key Vault](../../azure-arc/servers/manage-vm-extensions.md#azure-key-vault-vm-extension)
 - Key Vault 인증서를 가져오고 자동으로 새로 고칠 수 있는 Azure App Service Key Vault 인증서 통합
     - [Key Vault를 통해 Azure Web App 인증서 배포](../../app-service/configure-ssl-certificate.md#import-a-certificate-from-key-vault)

##### <a name="secrets"></a>비밀:
- App Service 애플리케이션 설정에서 Key Vault 비밀 사용
    - [App Service 및 Azure Functions의 Key Vault 참조 사용](../../app-service/app-service-key-vault-references.md)
- Azure VM에서 호스트되는 애플리케이션에 App Configuration 서비스에서 Key Vault 비밀 사용
    - [App Configuration 및 Key Vault 애플리케이션 구성](/samples/azure/azure-sdk-for-net/app-secrets-configuration/)

## <a name="code-examples"></a>코드 예제

애플리케이션에서 Key Vault를 사용하는 전체 예제는 다음을 참조하세요.

- [Azure Key Vault 코드 샘플](https://azure.microsoft.com/resources/samples/?service=key-vault) - Azure Key Vault에 대한 코드 샘플입니다. 

## <a name="how-tos"></a>방법

다음 문서 및 시나리오에서는 Azure Key Vault 사용에 대한 작업별 지침을 제공합니다.

- [방화벽 뒤 Key Vault 액세스](access-behind-firewall.md) - 주요 자격 증명 모음에 액세스하려면 주요 자격 증명 모음 클라이언트 애플리케이션은 다양한 기능에 대한 여러 엔드포인트에 액세스할 수 있어야 합니다.
- Key Vault에서 VM에 인증서를 배포하는 방법 - [Windows](../../virtual-machines/extensions/key-vault-windows.md), [Linux](../../virtual-machines/extensions/key-vault-linux.md) - Azure의 VM에서 실행 중인 클라우드 애플리케이션에는 인증서가 필요합니다. 지금 이 VM으로 인증서를 가져오려면 어떻게 하나요?
- 액세스 정책 할당([CLI](assign-access-policy-cli.md) | [PowerShell](assign-access-policy-powershell.md) | [Portal](assign-access-policy-portal.md)) 
- [CLI로 Key Vault 일시 삭제를 사용하는 방법](./key-vault-recovery.md)에서는 활성화된 일시 삭제를 사용하는 Key Vault 및 다양한 Key Vault 개체의 사용 및 수명 주기에 대해 설명합니다.
- [배포하는 동안 보안 값(예: 암호)을 전달하는 방법](../../azure-resource-manager/templates/key-vault-parameter.md) - 배포하는 동안 보안 값(예: 암호)을 매개 변수로 전달해야 하는 경우 이 값을 Azure Key Vault에 암호로 저장하고 다른 Resource Manager 템플릿에서 이 값을 참조할 수 있습니다.

## <a name="integrated-with-key-vault"></a>주요 자격 증명 모음과 통합됨

다음 문서에서는 사용하거나 Key Vault와 통합하는 다른 시나리오 및 서비스에 대한 정보를 다룹니다.

- [미사용 데이터 암호화](../../security/fundamentals/encryption-atrest.md)를 사용하면 데이터가 유지될 때 데이터를 인코딩(암호화)할 수 있습니다. 데이터 암호화 키는 종종 액세스를 더욱 제한하기 위해 Azure Key Vault의 키 암호화 키로 암호화됩니다.
- [Azure Information Protection](/azure/information-protection/plan-implement-tenant-key)을 통해 테넌트 키를 직접 관리할 수 있습니다. 예를 들어, Microsoft가 테넌트 키를 관리하는 대신(기본값) 테넌트 키를 직접 관리하여 해당 조직에 적용되는 특정 규정을 준수할 수 있습니다. 테넌트 키 직접 관리는 BYOK(Bring Your Own Key)라고 하기도 합니다.
- [Azure Private Link 서비스](private-link-service.md)를 사용하면 가상 네트워크의 프라이빗 엔드포인트를 통해 Azure 서비스(예: Azure Key Vault, Azure Storage, Azure Cosmos DB)와 Azure 호스팅 고객/파트너 서비스에 액세스할 수 있습니다.
- [Event Grid](../../event-grid/event-schema-key-vault.md)와 Key Vault를 통합하면 키 자격 증명 모음에 저장된 비밀의 상태가 변경될 때 사용자에게 알릴 수 있습니다. 새 버전의 비밀을 애플리케이션에 배포하거나 만료 직전인 비밀을 회전하여 중단을 방지할 수 있습니다.
- Key Vault에서 원치 않는 액세스 로부터 [Azure DevOps](/azure/devops/pipelines/release/azure-key-vault) 비밀을 보호할 수 있습니다.
- [DataBricks의 Key Vault에 저장된 비밀을 사용하여 Azure Storage에 연결](./integrate-databricks-blob-storage.md)
- Kubernetes에서 [비밀 저장소 CSI 드라이버](./key-vault-integrate-kubernetes.md)에 대한 Azure Key Vault 공급자 구성 및 실행

## <a name="key-vault-overviews-and-concepts"></a>Key Vault 개요 및 개념

- [Key Vault 일시 삭제 동작](soft-delete-overview.md)에서는 삭제가 실수인지, 의도적인 것인지에 관계없이 삭제된 개체를 복구할 수 있는 기능에 대해 설명합니다.
- [Key Vault 클라이언트 제한](overview-throttling.md)에서는 제한의 기본 개념을 소개하고 앱에 맞는 접근 방식을 제공합니다.
- [Key Vault 보안 권역](overview-security-worlds.md)에서는 지역과 보안 영역 간의 관계를 설명합니다.

## <a name="social"></a>소셜

- [키 자격 증명 모음 블로그](/archive/blogs/kv/)
- [키 자격 증명 모음 포럼](https://aka.ms/kvforum)
- [Key Vault에 대 한 Stack Overflow](https://stackoverflow.com/questions/tagged/azure-keyvault)

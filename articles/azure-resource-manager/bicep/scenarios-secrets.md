---
title: Bicep을 사용하여 비밀 관리
description: Bicep 및 Azure Key Vault 사용하여 비밀을 관리하는 방법을 설명합니다.
author: johndowns
ms.author: jodowns
ms.topic: conceptual
ms.date: 12/01/2021
ms.openlocfilehash: 29979d76fcc1d8988dbdd94fd8841623d0cacf9c
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133386808"
---
# <a name="manage-secrets-by-using-bicep"></a>Bicep을 사용하여 비밀 관리

배포를 수행하려면 비밀을 Azure 환경 전체에 안전하게 저장하고 전파해야 하는 경우가 많습니다. Bicep 및 Azure는 배포에서 비밀을 관리하는 데 도움이 될 수 있는 많은 기능을 제공합니다.

## <a name="avoid-secrets-where-you-can"></a>할 수 있는 비밀 방지

대부분의 경우 비밀을 전혀 사용하지 않을 수 있습니다. [많은 Azure 리소스는 자격 증명을](../../active-directory/managed-identities-azure-resources/overview.md)처리하거나 관리할 필요 없이 Azure 내의 다른 리소스에 액세스할 수 있도록 인증하고 권한을 부여할 수 있는 관리 ID를 지원합니다. 또한 일부 Azure 서비스는 인증서 및 프라이빗 키를 처리하지 않도록 자동으로 HTTPS 인증서를 생성할 수 있습니다. 가능하면 관리 ID 및 서비스 관리 인증서를 사용합니다.

## <a name="use-secure-parameters"></a>보안 매개 변수 사용

Bicep 배포에 비밀을 매개 변수로 제공해야 하는 경우 [ `@secure()` 데코레이터 를 사용합니다.](parameters.md#secure-parameters) 매개 변수를 안전한 것으로 표시하면 Azure Resource Manager 값을 기록하거나 Azure Portal, Azure CLI 또는 Azure PowerShell 표시하지 않습니다.

## <a name="avoid-outputs-for-secrets"></a>비밀에 대한 출력 방지

보안 데이터에는 Bicep 출력을 사용하지 마세요. 출력은 배포 기록에 기록되며, 배포에 액세스할 수 있는 모든 사용자는 배포 출력의 값을 볼 수 있습니다.

Bicep 배포 내에서 비밀을 생성하고 호출자 또는 다른 리소스에 사용할 수 있도록 해야 하는 경우 다음 방법 중 하나를 사용하는 것이 좋습니다.

## <a name="look-up-secrets-dynamically"></a>동적으로 비밀 조회

경우에 따라 한 리소스에서 비밀에 액세스하여 다른 리소스를 구성해야 합니다.

예를 들어 다른 배포에서 스토리지 계정을 만들었으며 기본 키에 액세스하여 Azure Functions 앱을 구성해야 할 수 있습니다. `existing`키워드를 사용하여 미리 만든 스토리지 계정에 대한 강력한 형식의 참조를 가져온 다음 스토리지 계정의 메서드를 사용하여 `listKeys()` 기본 키로 연결 문자열을 만들 수 있습니다.

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/scenarios-secrets/function-app.bicep" range="8-46" highlight="1-3, 5, 22, 34" :::

이 방법을 사용하면 Bicep 파일 내/외부로 비밀을 전달하지 않도록 방지할 수 있습니다.

이 방법을 사용하여 키 자격 증명 모음에 비밀을 저장할 수도 있습니다.

## <a name="use-key-vault"></a>Key Vault 사용

[Azure Key Vault](../../key-vault/general/overview.md) 보안 데이터를 저장하고 관리하도록 설계되었습니다. 키 자격 증명 모음을 사용하여 보호 및 공유해야 하는 비밀, 인증서, 키 및 기타 데이터를 관리합니다.

Bicep을 사용하여 자격 증명 모음 및 비밀을 만들고 관리할 수 있습니다. 형식의 리소스를 만들어 자격 증명 모음을 [`Microsoft.KeyVault/vaults`](/azure/templates/microsoft.keyvault/vaults?tabs=bicep) 정의합니다.

자격 증명 모음을 만들 때 해당 데이터에 액세스할 수 있는 사람과 사람을 결정해야 합니다. Bicep 파일 내에서 자격 증명 모음의 비밀을 읽으려는 경우 속성을 로 `enabledForTemplateDeployment` `true` 설정합니다.

### <a name="add-secrets-to-a-key-vault"></a>키 자격 증명 모음에 비밀 추가

비밀은 [자식 리소스이며](child-resource-name-type.md) 형식을 사용하여 만들 수 [`Microsoft.KeyVault/vaults/secrets`](/azure/templates/microsoft.keyvault/vaults/secrets?tabs=bicep) 있습니다. 다음 예제에서는 자격 증명 모음 및 비밀을 만드는 방법을 보여줍니다.

::: code language="bicep" source="~/azure-docs-bicep-samples/samples/scenarios-secrets/key-vault-secret.bicep" range="4-25" :::

> [!TIP]
> 자동화된 배포 파이프라인을 사용하는 경우 배포를 위해 키 자격 증명 모음 비밀을 부트스트랩하는 방법을 결정하는 것이 어려울 수 있습니다. 예를 들어 외부 API와 통신할 때 사용할 API 키가 제공된 경우 배포에서 사용하려면 비밀을 자격 증명 모음에 추가해야 합니다.
> 
> 타사에서 제공되는 비밀을 사용하는 경우 자격 증명 모음에 수동으로 추가해야 할 수 있으며 이후의 모든 용도에 대해 비밀을 참조할 수 있습니다.

### <a name="use-a-key-vault-with-modules"></a>모듈과 함께 키 자격 증명 모음 사용

Bicep 모듈을 사용하는 경우 [ `getSecret` 함수를](bicep-functions-resource.md#getsecret)사용하여 보안 매개 변수를 제공할 수 있습니다.

및 키워드를 함께 사용하여 다른 리소스 그룹에 정의된 키 자격 증명 모음을 참조할 수도 `existing` `scope` 있습니다. 다음 예제에서는 Bicep 파일이 *네트워킹이라는* 리소스 그룹에 배포됩니다. 모듈의 매개 변수 *mySecret* 값은 *비밀* 리소스 그룹에 있는 *contosonetworkingsecrets라는* 키 자격 증명 모음에 정의됩니다.

```bicep
resource networkingSecretsKeyVault 'Microsoft.KeyVault/vaults@2019-09-01' existing = {
  scope: resourceGroup('Secrets')
  name: 'contosonetworkingsecrets'
}

module exampleModule 'module.bicep' = {
  name: 'exampleModule'
  params: {
    mySecret: networkingSecretsKeyVault.getSecret('mySecret')
  }
}
```

## <a name="work-with-secrets-in-pipelines"></a>파이프라인에서 비밀 작업

파이프라인을 사용하여 Azure 리소스를 배포하는 경우 비밀을 적절하게 처리하도록 주의해야 합니다.

- 코드 리포지토리에 비밀을 저장하지 않습니다. 예를 들어 매개 변수 파일 또는 파이프라인 정의 YAML 파일에 비밀을 추가하지 마세요.
- GitHub 작업에서 [암호화된 비밀을](https://docs.github.com/actions/security-guides/encrypted-secrets) 사용하여 보안 데이터를 저장합니다. [비밀 검색을](https://docs.github.com/code-security/secret-scanning/about-secret-scanning) 사용하여 비밀의 실수로 인한 커밋을 검색합니다.
- Azure Pipelines 비밀 변수를 사용하여 보안 데이터를 [저장합니다.](/azure/devops/pipelines/process/variables#secret-variables)

## <a name="related-resources"></a>관련 참고 자료

- 리소스 설명서
  - [`Microsoft.KeyVault/vaults`](/azure/templates/microsoft.keyvault/vaults?tabs=bicep)
  - [`Microsoft.KeyVault/vaults/secrets`](/azure/templates/microsoft.keyvault/vaults/secrets?tabs=bicep)
- 사용하여 매핑
  - [관리 ID](../../active-directory/managed-identities-azure-resources/overview.md)
  - [Azure Key Vault](../../key-vault/general/overview.md)
- Bicep 기능
  - [보안 매개 변수](parameters.md#secure-parameters)
  - [기존 리소스 참조](resource-declaration.md#existing-resources)
  - [`getSecret` 함수](bicep-functions-resource.md#getsecret)
- 빠른 시작 템플릿
  - [사용자 할당 관리 ID 및 역할 할당 만들기](https://github.com/Azure/azure-quickstart-templates/tree/master/modules/Microsoft.ManagedIdentity/user-assigned-identity-role-assignment/1.0)
  - [Azure Key Vault 및 비밀 만들기](https://azure.microsoft.com/resources/templates/key-vault-create/)
  - [Key Vault 및 비밀 목록 만들기](https://azure.microsoft.com/resources/templates/key-vault-secret-create/)
  - [Front Door를 사용하여 사용자 지정 도메인 및 관리형 TLS 인증서 등록](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/front-door-custom-domain)
- Azure Pipelines
  - [비밀 변수](/azure/devops/pipelines/process/variables#secret-variables)
- GitHub 작업
  - [암호화된 암호](https://docs.github.com/actions/security-guides/encrypted-secrets)
  - [비밀 검사](https://docs.github.com/code-security/secret-scanning/about-secret-scanning)

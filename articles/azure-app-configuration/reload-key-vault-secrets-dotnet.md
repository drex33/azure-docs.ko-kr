---
title: 자동으로 비밀 및 인증서 다시 로드
titleSuffix: Azure App Configuration
description: Key Vault에서 비밀 및 인증서를 자동으로 다시 로드하도록 애플리케이션을 설정하는 방법을 알아봅니다.
services: azure-app-configuration
author: avanigupta
ms.assetid: ''
ms.service: azure-app-configuration
ms.devlang: csharp
ms.topic: how-to
ms.date: 05/25/2021
ms.author: avgupta
ms.openlocfilehash: f133c3184186ef46f7d4579a64d22733b773df7f
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/04/2021
ms.locfileid: "111415471"
---
# <a name="reload-secrets-and-certificates-from-key-vault-automatically"></a>Key Vault에서 자동으로 비밀 및 인증서 다시 로드

App Configuration 및 Key Vault는 많은 애플리케이션에서 함께 사용되는 보완 서비스입니다. 앱 구성은 Key Vault에 저장된 비밀 또는 인증서를 참조하는 앱 구성 저장소에 키를 만들어 서비스를 함께 사용하는 데 도움이 됩니다. Key Vault는 인증서의 공개 및 프라이빗 키 쌍을 비밀로 저장하므로 애플리케이션은 Key Vault에서 모든 인증서를 비밀로 검색할 수 있습니다.

우수한 보안 방법으로 [비밀](../key-vault/secrets/tutorial-rotation.md) 및 [인증서](../key-vault/certificates/tutorial-rotate-certificates.md)를 주기적으로 교체해야 합니다. Key Vault에서 순환된 후에는 애플리케이션이 최신 비밀 및 인증서 값을 선택하기를 원할 것입니다. 애플리케이션을 다시 시작하지 않고 이를 수행할 수 있는 두 가지 방법이 있습니다.
- Sentinel 키-값을 업데이트하여 전체 구성 새로 고침을 트리거함으로써 모든 Key Vault 비밀 및 인증서를 다시 로드합니다. 자세한 내용은 [ASP.NET Core 앱에서 동적 구성 사용](./enable-dynamic-configuration-aspnet-core.md) 방법을 참조하세요.
- Key Vault에서 일부 또는 모든 비밀 및 인증서를 주기적으로 다시 로드합니다.

첫 번째 옵션에서는 Key Vault에서 비밀 및 인증서를 교체할 때마다 앱 구성에서 Sentinel 키-값을 업데이트해야 합니다. 이 접근 방식은 애플리케이션에서 비밀 및 인증서를 즉시 다시 로드하려는 경우에 적합합니다. 그러나 Key Vault에서 비밀과 인증서가 자동으로 순환될 때 Sentinel 키-값을 제때 업데이트하지 않으면 애플리케이션에 오류가 발생할 수 있습니다. 두 번째 옵션을 사용하면 이 프로세스를 완전히 자동화할 수 있습니다. 순환 시간으로부터 허용 가능한 대기 시간 내에 Key Vault에서 비밀 및 인증서를 다시 로드하도록 애플리케이션을 구성할 수 있습니다. 이 자습서에서는 두 번째 옵션을 안내합니다.


## <a name="prerequisites"></a>필수 구성 요소

- 이 자습서에서는 Key Vault에서 비밀 및 인증서를 자동으로 다시 로드하도록 애플리케이션을 설정하는 방법을 보여 줍니다. 코드에서 Key Vault 참조를 구현하기 위한 자습서를 기반으로 합니다. 계속하기 전에 먼저 [자습서: ASP.NET Core 앱에서 Key Vault 참조 사용](./use-key-vault-references-dotnet-core.md)을 완료하세요.

- [Microsoft.Azure.AppConfiguration.AspNetCore](https://www.nuget.org/packages/Microsoft.Azure.AppConfiguration.AspNetCore) 패키지 v4.4.0 이상.


## <a name="add-an-auto-rotating-certificate-to-key-vault"></a>Key Vault에 자동 순환 인증서 추가

 [자습서: Key Vault에서 인증서 자동 순환 구성](../key-vault/certificates/tutorial-rotate-certificates.md)에 따라 이전 자습서에서 만든 Key Vault에 **ExampleCertificate** 라는 자동 순환 인증서를 추가합니다.


## <a name="add-a-reference-to-the-key-vault-certificate-in-app-configuration"></a>앱 구성에서 Key Vault 인증서에 대한 참조 추가

1. Azure Portal에서 **모든 리소스** 를 선택한 다음 이전 자습서에서 만든 App Configuration 저장소 인스턴스를 선택합니다.

1. **구성 탐색기** 를 선택합니다.

1. **+ 만들기** > **Key Vault 참조** 를 선택하고 다음 값을 지정합니다.
    - **키**: **TestApp:Settings:KeyVaultCertificate** 를 선택합니다.
    - **레이블**: 이 값은 빈 상태로 둡니다.
    - **구독**, **리소스 그룹** 및 **Key Vault**: 이전 자습서에서 만든 Key Vault에 해당하는 값을 입력합니다.
    - **비밀**: 이전 섹션에서 만든 **ExampleCertificate** 라는 보안 비밀을 선택합니다.
    - **비밀 버전**: **최신 버전** 입니다.

> [!Note]
> 특정 버전을 참조하는 경우 Key Vault에서 비밀 또는 인증서를 다시 로드하면 항상 동일한 값이 반환됩니다.


## <a name="update-code-to-reload-key-vault-secrets-and-certificates"></a>Key Vault 비밀 및 인증서를 다시 로드하도록 코드 업데이트

*Program.cs* 파일에서 `AddAzureAppConfiguration` 메서드를 업데이트하여 `SetSecretRefreshInterval` 메서드를 통해 Key Vault 인증서의 새로 고침 간격을 설정합니다. 이 변경으로 애플리케이션은 12시간마다 **ExampleCertificate** 에 대한 공개-프라이빗 키 쌍을 다시 로드합니다.

```csharp
config.AddAzureAppConfiguration(options =>
{
    options.Connect(settings["ConnectionStrings:AppConfig"])
            .ConfigureKeyVault(kv =>
            {
                kv.SetCredential(new DefaultAzureCredential());
                kv.SetSecretRefreshInterval("TestApp:Settings:KeyVaultCertificate", TimeSpan.FromHours(12));
            });
});
```

`SetSecretRefreshInterval` 메서드의 첫 번째 인수는 앱 구성의 Key Vault 참조 키입니다. 이 인수는 선택 사항입니다. 키 매개 변수를 생략하면 새로 고침 간격이 개별 새로 고침 간격이 없는 모든 비밀 및 인증서에 적용됩니다.

새로 고침 간격은 Key Vault 또는 앱 구성의 값 변경에 관계없이 비밀 및 인증서가 Key Vault에서 다시 로드되는 빈도를 정의합니다. 앱 구성에서 값이 변경될 때 비밀과 인증서를 다시 로드하려면 `ConfigureRefresh` 메서드를 사용하여 모니터링할 수 있습니다. 자세한 내용은 [ASP.NET Core 앱에서 동적 구성 사용](./enable-dynamic-configuration-aspnet-core.md) 방법을 참조하세요.

Key Vault에서 비밀 및 인증서가 업데이트된 후 허용 가능한 대기 시간에 따라 새로 고침 간격을 선택합니다. 제한되지 않도록 [Key Vault 서비스 제한](../key-vault/general/service-limits.md)을 고려하는 것도 중요합니다.


## <a name="clean-up-resources"></a>리소스 정리

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]


## <a name="next-steps"></a>다음 단계

이 자습서에서는 Key Vault에서 비밀 및 인증서를 자동으로 다시 로드하도록 애플리케이션을 설정하는 방법을 알아보았습니다. 관리 ID를 사용하여 App Configuration 및 Key Vault에 대한 액세스를 간소화하는 방법을 알아보려면 다음 자습서를 계속 진행하세요.

> [!div class="nextstepaction"]
> [관리 ID 통합](./howto-integrate-azure-managed-service-identity.md)

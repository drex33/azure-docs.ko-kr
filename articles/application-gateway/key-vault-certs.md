---
title: Azure Key Vault 인증서를 사용한 TLS 종료
description: HTTPS 지원 수신기에 연결된 서버 인증서를 위해 Azure Application Gateway를 Key Vault와 통합하는 방법을 알아봅니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: victorh
ms.openlocfilehash: 972b0050a75379f937cee32644620ed813f55a94
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128678975"
---
# <a name="tls-termination-with-key-vault-certificates"></a>Key Vault 인증서를 사용한 TLS 종료

[Azure Key Vault](../key-vault/general/overview.md)는 비밀, 키 및 TLS/SSL 인증서를 보호하는 데 사용할 수 있는 플랫폼 관리 비밀 저장소입니다. Azure Application Gateway는 HTTPS 지원 수신기에 연결된 서버 인증서에 대해 Key Vault와의 통합을 지원합니다. 이 지원은 Application Gateway v2 SKU로 제한됩니다.

Key Vault 통합은 TLS 종료를 위한 두 가지 모델을 제공합니다.

- 수신기에 연결된 TLS/SSL 인증서를 명시적으로 제공합니다. 이 모델은 TLS 종료를 위해 Application Gateway에 TLS/SSL 인증서를 전달하는 일반적인 방법입니다.
- 필요에 따라 HTTPS 사용 수신기를 만들 때 기존 Key Vault 인증서 또는 비밀에 대한 참조를 제공합니다.

Key Vault와 Application Gateway를 통합하면 다음과 같은 여러 이점이 제공됩니다.

- TLS/SSL 인증서가 애플리케이션 개발 팀에서 직접 처리되지 않으므로 보다 강화된 보안 통합을 통해 별도의 보안 팀에서 다음을 수행할 수 있습니다.
  * Application Gateway를 설정합니다.
  * Application Gateway 수명 주기를 제어합니다.
  * Key Vault에 저장된 인증서에 액세스할 수 있도록 선택한 Application Gateway에 대한 사용 권한을 부여합니다.
- Key Vault로 기존 인증서를 가져오도록 지원 또는 Key Vault API를 사용하여 신뢰할 수 있는 Key Vault 파트너와 함께 새 인증서를 만들고 관리할 수 있습니다.
- Key Vault에 저장된 인증서의 자동 갱신 지원

## <a name="supported-certificates"></a>지원되는 인증서

Application Gateway는 현재 소프트웨어 유효성 검사 인증서만 지원합니다. HSM(하드웨어 보안 모듈)에서 검증된 인증서는 지원되지 않습니다. 

Application Gateway가 Key Vault 인증서를 사용하도록 구성된 경우 해당 인스턴스는 Key Vault에서 인증서를 검색하고 TLS 종료를 위해 로컬로 설치합니다. 또한 인스턴스는 4시간 간격으로 Key Vault 폴링하여 인증서의 갱신된 버전(있는 경우)을 검색합니다. 업데이트된 인증서가 발견되면 현재 HTTPS 수신기와 연결된 TLS/SSL 인증서가 자동으로 회전됩니다. 

Application Gateway Key Vault 비밀 식별자를 사용하여 인증서를 참조합니다. Azure PowerShell, Azure CLI 또는 Azure Resource Manager 버전을 지정하지 않는 비밀 식별자를 사용하는 것이 좋습니다. 이러한 방식으로 Application Gateway 키 자격 증명 모음에서 최신 버전을 사용할 수 있는 경우 인증서를 자동으로 회전합니다. 버전이 없는 비밀 URI의 예는 `https://myvault.vault.azure.net/secrets/mysecret/` 입니다.

Azure Portal 비밀이 아닌 Key Vault 인증서만 지원합니다. Application Gateway 여전히 powerShell, Azure CLI, API 및 ARM 템플릿(Azure Resource Manager 템플릿)과 같은 포털이 아닌 리소스를 통해서만 Key Vault 비밀 참조를 지원합니다. 

> [!WARNING]
> Azure Application Gateway 현재 Application Gateway 리소스와 동일한 구독의 Key Vault 계정만 지원합니다. Application Gateway 다른 구독에서 키 자격 증명 모음을 선택하면 오류가 발생합니다.

## <a name="certificate-settings-in-key-vault"></a>Key Vault 인증서 설정

TLS 종료의 경우 Application Gateway PFX(개인 정보 Exchange) 형식의 인증서를 지원합니다. 기존 인증서를 가져오거나 키 자격 증명 모음에 새 인증서를 만들 수 있습니다. 오류를 방지하려면 인증서의 상태가 Key Vault **사용으로** 설정되어 있는지 확인합니다.

## <a name="how-integration-works"></a>통합의 작동 방식

Key Vault Application Gateway 통합은 3단계 구성 프로세스입니다.

![Application Gateway Key Vault 통합하기 위한 세 단계를 보여 주는 다이어그램](media/key-vault-certs/ag-kv.png)

### <a name="create-a-user-assigned-managed-identity"></a>사용자 할당 관리 ID 만들기

사용자가 할당한 관리 ID를 만들거나 기존 ID를 다시 사용할 수 있습니다. Application Gateway 관리 ID를 사용하여 사용자 대신 Key Vault 인증서를 검색합니다. 자세한 내용은 [Azure Portal을 사용하여 사용자 할당 관리 ID를 생성, 나열, 삭제 또는 할당](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)을 참조하세요. 

이 단계에서는 Azure Active Directory 테넌트에 새 ID를 만듭니다. ID를 만드는 데 사용되는 구독에서 ID가 신뢰됩니다.

### <a name="configure-your-key-vault"></a>Key Vault 구성

키 자격 증명 모음에서 사용자 할당 관리 ID를 사용하도록 액세스 정책을 정의합니다.
    
1. Azure Portal **Key Vault** 이동합니다.
1. 액세스 **정책** 창을 엽니다.
1. 사용 권한 모델 자격 증명 **모음 액세스 정책:** **+ 액세스 정책 추가를** 선택하고 **비밀 권한에** 대해 **Get을** 선택하고 보안 **주체 선택에서** 사용자 할당 관리 ID를 선택합니다. 그런 다음 **저장** 을 선택합니다.
   
   권한 모델 Azure 역할 **기반 액세스 제어를** 사용하는 경우: 사용자 할당 관리 ID에 대한 역할 할당을 비밀 사용자 **Key Vault** 역할에 대한 Azure Key Vault에 추가합니다.

2021년 3월 15일부터 Key Vault Application Gateway 신뢰할 수 있는 서비스로 인식하므로 Azure에서 보안 네트워크 경계를 구축할 수 있습니다. 모든 네트워크(인터넷 트래픽 포함)에서 Key Vault 트래픽에 대한 액세스를 거부할 수 있지만 구독에서 Application Gateway 리소스에 Key Vault 액세스할 수 있습니다.

제한된 키 자격 증명 모음을 사용하는 경우 다음 단계를 사용하여 방화벽 및 가상 네트워크를 사용하도록 Application Gateway 구성합니다. 

1. Azure Portal 키 자격 증명 모음에서 **네트워킹을** 선택합니다.
1. **방화벽 및 가상 네트워크** 탭에서 **프라이빗 엔드포인트 및 선택한 네트워크를 선택합니다.**
1. **가상 네트워크의** 경우 + 기존 가상 **네트워크 추가를** 선택한 다음, Application Gateway 인스턴스에 대한 가상 네트워크 및 서브넷을 추가합니다. 프로세스 중에 `Microsoft.KeyVault` 확인란을 선택하여 서비스 엔드포인트도 구성합니다.
1. 신뢰할 수 있는 서비스가 키 자격 증명 모음의 방화벽을 무시하도록 허용하려면 **예를** 선택합니다.
  
![방화벽 및 가상 네트워크를 사용하도록 Application Gateway 구성하기 위한 선택 항목을 보여 주는 스크린샷](media/key-vault-certs/key-vault-firewall.png)

Azure CLI 또는 PowerShell을 사용하거나 Azure Portal 배포된 Azure 애플리케이션을 통해 ARM 템플릿을 통해 Application Gateway 인스턴스를 배포하는 경우 SSL 인증서는 키 자격 증명 모음에 Base64로 인코딩된 PFX 파일로 저장됩니다. [배포 중에 Azure Key Vault를 사용하여 보안 매개 변수 값 전달](../azure-resource-manager/templates/key-vault-parameter.md)의 단계를 완료해야 합니다. 

특히 `enabledForTemplateDeployment`를 `true`로 설정하는 것이 중요합니다. 인증서에 암호가 있거나 없을 수 있습니다. 암호가 있는 인증서의 경우 다음 예제에서는 Application Gateway ARM `sslCertificates` 템플릿 구성에 대한 의 항목에 대한 가능한 구성을 보여 `properties` 줍니다. 

```
"sslCertificates": [
    {
        "name": "appGwSslCertificate",
        "properties": {
            "data": "[parameters('appGatewaySSLCertificateData')]",
            "password": "[parameters('appGatewaySSLCertificatePassword')]"
        }
    }
]
```

및 의 `appGatewaySSLCertificateData` `appGatewaySSLCertificatePassword` 값은 동적 ID를 가진 [비밀 참조에](../azure-resource-manager/templates/key-vault-parameter.md#reference-secrets-with-dynamic-id)설명된 대로 키 자격 증명 모음에서 조회됩니다. `parameters('secretName')`부터 뒤쪽으로 참조를 따라 이동하면서 조회가 진행되는 방법을 확인합니다. 인증서에 암호가 없는 경우 `password` 항목을 생략합니다.

### <a name="configure-application-gateway"></a>Application Gateway 구성

사용자 할당 관리 ID를 만들고 키 자격 증명 모음을 구성한 후에는 IAM(ID 및 액세스 관리)을 통해 Application Gateway 인스턴스에 대한 관리 ID를 할당할 수 있습니다. PowerShell의 경우 [Set-AzApplicationGatewayIdentity를 참조하세요.](/powershell/module/az.network/set-azapplicationgatewayidentity)

## <a name="investigating-and-resolving-key-vault-errors"></a>Key Vault 오류 조사 및 해결

Azure Application Gateway 4시간 간격으로 Key Vault 갱신된 인증서 버전을 폴링하지 않습니다. 또한 오류를 기록하고 Azure Advisor 통합되어 잘못된 구성을 권장 사항으로 제시합니다. 권장 사항에는 문제 및 관련 Key Vault 리소스에 대한 세부 정보가 포함되어 있습니다. 이 정보를 문제 해결 [가이드와](../application-gateway/application-gateway-key-vault-common-errors.md) 함께 사용하여 이러한 구성 오류를 신속하게 해결할 수 있습니다. 

문제가 검색될 때 [Advisor 경고를](../advisor/advisor-alerts-portal.md) 계속 업데이트하도록 구성하는 것이 좋습니다. 이 특정 사례에 대한 경고를 설정하려면 권장 사항 유형으로 **Application Gateway Azure Key Vault 문제 해결을** 사용합니다. 

## <a name="next-steps"></a>다음 단계

[Azure PowerShell을 사용하여 Key Vault 인증서로 TLS 종료 구성](configure-keyvault-ps.md)

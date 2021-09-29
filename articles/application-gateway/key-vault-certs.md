---
title: Azure Key Vault 인증서를 사용한 TLS 종료
description: HTTPS 지원 수신기에 연결된 서버 인증서를 위해 Azure Application Gateway를 Key Vault와 통합하는 방법을 알아봅니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: victorh
ms.openlocfilehash: 55ad3c97fd70db854faf3d2181a55630d04f5e41
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129215095"
---
# <a name="tls-termination-with-key-vault-certificates"></a>Key Vault 인증서를 사용한 TLS 종료

[Azure Key Vault](../key-vault/general/overview.md)는 비밀, 키 및 TLS/SSL 인증서를 보호하는 데 사용할 수 있는 플랫폼 관리 비밀 저장소입니다. Azure Application Gateway는 HTTPS 지원 수신기에 연결된 서버 인증서에 대해 Key Vault와의 통합을 지원합니다. 이 지원은 Application Gateway v2 SKU로 제한됩니다.

Key Vault 통합은 TLS 종료를 위한 두 가지 모델을 제공합니다.

- 수신기에 연결 된 TLS/SSL 인증서를 명시적으로 제공 합니다. 이 모델은 TLS 종료를 위해 Application Gateway에 TLS/SSL 인증서를 전달하는 일반적인 방법입니다.
- 필요에 따라 HTTPS 사용 수신기를 만들 때 기존 Key Vault 인증서 또는 암호에 대 한 참조를 제공 합니다.

Key Vault와 Application Gateway를 통합하면 다음과 같은 여러 이점이 제공됩니다.

- TLS/SSL 인증서가 애플리케이션 개발 팀에서 직접 처리되지 않으므로 보다 강화된 보안 통합을 통해 별도의 보안 팀에서 다음을 수행할 수 있습니다.
  * Application Gateway를 설정합니다.
  * Application Gateway 수명 주기를 제어합니다.
  * Key Vault에 저장된 인증서에 액세스할 수 있도록 선택한 Application Gateway에 대한 사용 권한을 부여합니다.
- Key Vault로 기존 인증서를 가져오도록 지원 또는 Key Vault API를 사용하여 신뢰할 수 있는 Key Vault 파트너와 함께 새 인증서를 만들고 관리할 수 있습니다.
- Key Vault에 저장된 인증서의 자동 갱신 지원

## <a name="supported-certificates"></a>지원되는 인증서

Application Gateway는 현재 소프트웨어 유효성 검사 인증서만 지원합니다. HSM(하드웨어 보안 모듈)에서 검증된 인증서는 지원되지 않습니다. 

Application Gateway가 Key Vault 인증서를 사용하도록 구성된 경우 해당 인스턴스는 Key Vault에서 인증서를 검색하고 TLS 종료를 위해 로컬로 설치합니다. 또한 인스턴스는 갱신 된 버전의 인증서 (있는 경우)를 검색 하기 위해 4 시간 간격으로 Key Vault를 폴링합니다. 업데이트 된 인증서가 있는 경우 현재 HTTPS 수신기와 연결 된 TLS/SSL 인증서가 자동으로 순환 됩니다. 

Application Gateway는 Key Vault에서 암호 식별자를 사용 하 여 인증서를 참조 합니다. Azure PowerShell, Azure CLI 또는 Azure Resource Manager의 경우 버전을 지정 하지 않는 암호 식별자를 사용 하는 것이 좋습니다. 이러한 방식으로 키 자격 증명 모음에서 최신 버전을 사용할 수 있는 경우 Application Gateway에서 자동으로 인증서를 회전 합니다. 버전이 없는 비밀 URI의 예는 `https://myvault.vault.azure.net/secrets/mysecret/` 입니다.

Azure Portal는 비밀이 아닌 Key Vault 인증서만 지원 합니다. Application Gateway는 PowerShell, Azure CLI, Api 및 Azure Resource Manager 템플릿 (ARM 템플릿)과 같은 포털이 아닌 리소스를 통해서만 Key Vault에서 비밀을 참조할 수 있습니다. 

> [!WARNING]
> Azure 애플리케이션 게이트웨이는 현재 Application Gateway 리소스와 동일한 구독의 Key Vault 계정만 지원 합니다. Application Gateway 아닌 다른 구독에서 키 자격 증명 모음을 선택 하면 오류가 발생 합니다.

## <a name="certificate-settings-in-key-vault"></a>Key Vault의 인증서 설정

TLS 종료의 경우 Application Gateway는 PFX (개인 정보 Exchange) 형식의 인증서를 지원 합니다. 기존 인증서를 가져오거나 키 자격 증명 모음에 새 인증서를 만들 수 있습니다. 오류를 방지 하려면 Key Vault에서 인증서의 상태가 **사용** 으로 설정 되어 있는지 확인 합니다.

## <a name="how-integration-works"></a>통합의 작동 방식

Key Vault와 Application Gateway 통합은 3 단계 구성 프로세스입니다.

![Application Gateway Key Vault와 통합 하는 3 단계를 보여 주는 다이어그램입니다.](media/key-vault-certs/ag-kv.png)

### <a name="create-a-user-assigned-managed-identity"></a>사용자 할당 관리 ID 만들기

사용자 할당 관리 id를 만들거나 기존 id를 다시 사용 합니다. Application Gateway는 관리 id를 사용 하 여 사용자 대신 Key Vault에서 인증서를 검색 합니다. 자세한 내용은 [Azure Portal을 사용하여 사용자 할당 관리 ID를 생성, 나열, 삭제 또는 할당](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)을 참조하세요. 

이 단계에서는 Azure Active Directory 테넌트에 새 ID를 만듭니다. ID를 만드는 데 사용되는 구독에서 ID가 신뢰됩니다.

### <a name="configure-your-key-vault"></a>Key Vault 구성

키 자격 증명 모음에 사용자 할당 관리 id를 사용 하는 액세스 정책을 정의 합니다.
    
1. Azure Portal에서 **Key Vault** 로 이동 합니다.
1. **액세스 정책** 창을 엽니다.
1. 권한 모델 **자격 증명 모음 액세스 정책** 을 사용 하는 경우: **액세스 정책 추가** 를 선택 하 고 보안 **권한** 으로 **가져오기** 를 선택 하 고 **보안 주체** 에 대 한 사용자 할당 관리 id를 선택 합니다. 그런 다음 **저장** 을 선택합니다.
   
   권한 모델 **azure 역할 기반 액세스 제어** 를 사용 하는 경우: 사용자 할당 관리 id에 대 한 역할 할당을 azure 주요 자격 증명 모음에 추가 하 **Key Vault 암호 사용자** 를 역할에 추가 합니다.

2021 년 3 월 15 일부 터 Key Vault는 인증에 대 한 사용자 관리 Id를 활용 하 여 Application Gateway를 신뢰할 수 있는 서비스로 인식 Azure Key Vault 합니다.  서비스 끝점을 사용 하 고 key vault의 방화벽에 대해 신뢰할 수 있는 서비스 옵션을 사용 하도록 설정 하면 Azure에서 보안 네트워크 경계를 구축할 수 있습니다. Key Vault에 대 한 모든 네트워크 (인터넷 트래픽 포함)의 트래픽에 대 한 액세스를 거부할 수 있지만 구독 하는 Application Gateway 리소스에 대 한 Key Vault 액세스할 수 있습니다.

제한 된 키 자격 증명 모음을 사용 하는 경우 다음 단계를 사용 하 여 방화벽 및 가상 네트워크를 사용 하도록 Application Gateway를 구성 합니다. 

1. Azure Portal의 키 자격 증명 모음에서 **네트워킹** 을 선택 합니다.
1. **방화벽 및 가상 네트워크** 탭에서 **개인 끝점 및 선택한 네트워크** 를 선택 합니다.
1. **가상 네트워크** 의 경우 **+ 기존 가상 네트워크 추가** 를 선택한 다음 Application Gateway 인스턴스에 대 한 가상 네트워크 및 서브넷을 추가 합니다. 프로세스 중에 `Microsoft.KeyVault` 확인란을 선택 하 여 서비스 끝점을 구성 합니다.
1. 신뢰할 수 있는 서비스에서 키 자격 증명 모음의 방화벽을 무시 하도록 허용 하려면 **예** 를 선택 합니다.
  
![방화벽 및 가상 네트워크를 사용 하도록 Application Gateway를 구성 하기 위한 선택 항목을 보여 주는 스크린샷](media/key-vault-certs/key-vault-firewall.png)

Azure CLI 또는 PowerShell을 사용 하 여 ARM 템플릿을 통해 Application Gateway 인스턴스를 배포 하거나 Azure Portal에서 배포 된 Azure 응용 프로그램을 통해 배포 하는 경우 SSL 인증서는 키 자격 증명 모음에 Base64 인코딩 PFX 파일로 저장 됩니다. [배포 중에 Azure Key Vault를 사용하여 보안 매개 변수 값 전달](../azure-resource-manager/templates/key-vault-parameter.md)의 단계를 완료해야 합니다. 

특히 `enabledForTemplateDeployment`를 `true`로 설정하는 것이 중요합니다. 인증서에 암호가 있을 수도 있고 없을 수도 있습니다. 암호를 사용 하는 인증서의 경우 다음 예제에서는 `sslCertificates` `properties` Application Gateway에 대 한 ARM 템플릿 구성에 대 한의 항목에 대 한 구성을 보여 줍니다. 

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

및의 값 `appGatewaySSLCertificateData` 은 `appGatewaySSLCertificatePassword` [동적 ID를 사용 하는 참조 암호](../azure-resource-manager/templates/key-vault-parameter.md#reference-secrets-with-dynamic-id)에 설명 된 대로 키 자격 증명 모음에서 조회 됩니다. `parameters('secretName')`부터 뒤쪽으로 참조를 따라 이동하면서 조회가 진행되는 방법을 확인합니다. 인증서에 암호가 없는 경우 `password` 항목을 생략합니다.

### <a name="configure-application-gateway"></a>Application Gateway 구성

사용자 할당 관리 id를 만들고 키 자격 증명 모음을 구성한 후에는 id 및 액세스 관리 (IAM)를 통해 Application Gateway 인스턴스에 대 한 관리 되는 id를 할당할 수 있습니다. PowerShell의 경우 [AzApplicationGatewayIdentity](/powershell/module/az.network/set-azapplicationgatewayidentity)를 참조 하세요.

## <a name="investigating-and-resolving-key-vault-errors"></a>Key Vault 오류 조사 및 해결

Azure 애플리케이션 게이트웨이는 4 시간 간격으로 Key Vault에서 갱신 된 인증서 버전을 폴링하기만 하지 않습니다. 또한 모든 오류를 기록 하 고 Azure Advisor와 통합 되어 구성을 권장 구성으로 노출 합니다. 권장 사항에는 문제 및 관련 Key Vault 리소스에 대 한 세부 정보가 포함 됩니다. [문제 해결 가이드](../application-gateway/application-gateway-key-vault-common-errors.md) 와 함께이 정보를 사용 하 여 이러한 구성 오류를 신속 하 게 해결할 수 있습니다. 

문제가 검색 되 면 업데이트 된 상태로 유지 하도록 [Advisor 경고를 구성](../advisor/advisor-alerts-portal.md) 하는 것이 좋습니다. 이 특정 사례에 대 한 경고를 설정 하려면 **Application Gateway에 대 한 Azure Key Vault 문제 해결** 을 권장 사항 유형으로 사용 합니다. 

## <a name="next-steps"></a>다음 단계

[Azure PowerShell을 사용하여 Key Vault 인증서로 TLS 종료 구성](configure-keyvault-ps.md)

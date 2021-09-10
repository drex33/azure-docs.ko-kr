---
title: Azure Key Vault 인증서를 사용한 TLS 종료
description: HTTPS 지원 수신기에 연결된 서버 인증서를 위해 Azure Application Gateway를 Key Vault와 통합하는 방법을 알아봅니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: victorh
ms.openlocfilehash: cf0afce3c5495199b23059df226115154dade7f0
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122528691"
---
# <a name="tls-termination-with-key-vault-certificates"></a>Key Vault 인증서를 사용한 TLS 종료

[Azure Key Vault](../key-vault/general/overview.md)는 비밀, 키 및 TLS/SSL 인증서를 보호하는 데 사용할 수 있는 플랫폼 관리 비밀 저장소입니다. Azure Application Gateway는 HTTPS 지원 수신기에 연결된 서버 인증서에 대해 Key Vault와의 통합을 지원합니다. 이 지원은 Application Gateway v2 SKU로 제한됩니다.

Key Vault 통합은 TLS 종료를 위한 두 가지 모델을 제공합니다.

- 수신기에 연결된 TLS/SSL 인증서를 명시적으로 제공할 수 있습니다. 이 모델은 TLS 종료를 위해 Application Gateway에 TLS/SSL 인증서를 전달하는 일반적인 방법입니다.
- HTTPS 지원 수신기를 만들 때 필요에 따라 기존 Key Vault 인증서 또는 암호에 대한 참조를 제공할 수 있습니다.

Key Vault와 Application Gateway를 통합하면 다음과 같은 여러 이점이 제공됩니다.

- TLS/SSL 인증서가 애플리케이션 개발 팀에서 직접 처리되지 않으므로 보다 강화된 보안 통합을 통해 별도의 보안 팀에서 다음을 수행할 수 있습니다.
  * Application Gateway를 설정합니다.
  * Application Gateway 수명 주기를 제어합니다.
  * Key Vault에 저장된 인증서에 액세스할 수 있도록 선택한 Application Gateway에 대한 사용 권한을 부여합니다.
- Key Vault로 기존 인증서를 가져오도록 지원 또는 Key Vault API를 사용하여 신뢰할 수 있는 Key Vault 파트너와 함께 새 인증서를 만들고 관리할 수 있습니다.
- Key Vault에 저장된 인증서의 자동 갱신 지원

Application Gateway는 현재 소프트웨어 유효성 검사 인증서만 지원합니다. HSM(하드웨어 보안 모듈)에서 검증된 인증서는 지원되지 않습니다. Application Gateway가 Key Vault 인증서를 사용하도록 구성된 경우 해당 인스턴스는 Key Vault에서 인증서를 검색하고 TLS 종료를 위해 로컬로 설치합니다. 또한 인스턴스는 갱신된 버전의 인증서(있는 경우)를 검색하기 위해 4시간 간격으로 Key Vault를 폴링합니다. 업데이트된 인증서가 있는 경우 현재 HTTPS 수신기와 연결된 TLS/SSL 인증서가 자동으로 순환됩니다.

> [!NOTE]
> Azure Portal는 비밀이 아닌 KeyVault 인증서만 지원합니다. Application Gateway는 여전히 KeyVault의 비밀을 참조할 수 있지만 PowerShell, CLI, API, ARM 템플릿 등과 같은 Portal 이외 리소스를 통해서만 가능합니다. 

> [!WARNING]
> Azure Application Gateway는 현재 Application Gateway 리소스와 동일한 구독의 Key Vault 계정만 지원합니다. Application Gateway와 다른 구독의 Key Vault를 선택하면 실패하게 됩니다.

## <a name="how-integration-works"></a>통합의 작동 방식

Key Vault와 Application Gateway를 통합하려면 다음의 3단계 구성 프로세스가 필요합니다.

1. **사용자 할당 관리 ID 만들기**

   Application Gateway가 사용자 대신 Key Vault에서 인증서를 검색하는 데 사용하는 기존 사용자 할당 관리형 ID를 생성하거나 재사용합니다. 자세한 내용은 [Azure Portal을 사용하여 사용자 할당 관리 ID를 생성, 나열, 삭제 또는 할당](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)을 참조하세요. 이 단계에서는 Azure Active Directory 테넌트에 새 ID를 만듭니다. ID를 만드는 데 사용되는 구독에서 ID가 신뢰됩니다.

1. **Key Vault 구성**

   그런 다음, 기존 인증서를 가져오거나 Key Vault에 새 인증서를 만듭니다. 인증서는 Application Gateway를 통해 실행되는 애플리케이션에서 사용됩니다. 이 단계에서는 암호 없는 64로 인코딩된 PFX 파일을 저장할 수 있는 Key Vault 암호를 사용할 수도 있습니다. Key Vault에서 이 유형의 개체를 사용할 수 있는 자동 갱신 기능 때문에 "인증서" 유형을 사용하는 것이 좋습니다. 인증서 또는 비밀을 만든 후에는 Key Vault에 액세스 정책을 정의하여 ID에 비밀에 대한 get 액세스 권한을 부여해야 합니다.
   
   > [!IMPORTANT]
   > 2021년 3월 15일부터 Key Vault는 Azure Application Gateway를 신뢰할 수 있는 서비스 중 하나로 인식하므로 Azure에서 보안 네트워크 경계를 구축할 수 있습니다. 이렇게 하면 모든 네트워크(인터넷 트래픽 포함)에서 Key Vault로의 트래픽에 대한 액세스를 거부할 수 있지만 구독 중인 Application Gateway 리소스에는 여전히 액세스할 수 있습니다. 

   > 다음과 같은 방법으로 제한된 Key Vault 네트워크에서 Application Gateway를 구성할 수 있습니다. <br />
   > a) Key Vault의 네트워킹 블레이드에서 <br />
   > b) "방화벽 및 가상 네트워크" 탭에서 프라이빗 엔드포인트 및 선택한 네트워크를 선택합니다. <br/>
   > c) 가상 네트워크를 사용하여 Application Gateway의 가상 네트워크 및 서브넷을 추가합니다. 이 프로세스 동안 해당 확인란을 선택하여 'Microsoft.KeyVault' 서비스 엔드포인트도 구성합니다. <br/>
   > d) 마지막으로, "예"를 선택하여 신뢰할 수 있는 서비스가 Key Vault의 방화벽을 우회하도록 허용합니다. <br/>
   > 
   > ![Key Vault 방화벽](media/key-vault-certs/key-vault-firewall.png)


   > [!NOTE]
   > Azure CLI 또는 PowerShell을 사용하거나 Azure Portal에서 배포된 Azure 애플리케이션을 통해 ARM 템플릿을 사용하여 애플리케이션 게이트웨이를 배포하는 경우 SSL 인증서는 Key Vault에 base64 인코딩 PFX 파일로 저장됩니다. [배포 중에 Azure Key Vault를 사용하여 보안 매개 변수 값 전달](../azure-resource-manager/templates/key-vault-parameter.md)의 단계를 완료해야 합니다. 
   >
   > 특히 `enabledForTemplateDeployment`를 `true`로 설정하는 것이 중요합니다. 인증서에는 암호가 없거나 암호가 있을 수 있습니다. 암호가 있는 인증서의 경우 다음 예제에서는 앱 게이트웨이에 대한 ARM 템플릿 구성의 `properties`에서 `sslCertificates` 항목의 가능한 구성을 보여 줍니다. `appGatewaySSLCertificateData` 및 `appGatewaySSLCertificatePassword` 값은 [동적 ID로 비밀 참조](../azure-resource-manager/templates/key-vault-parameter.md#reference-secrets-with-dynamic-id) 섹션에 설명된 것처럼 에서 조회됩니다. `parameters('secretName')`부터 뒤쪽으로 참조를 따라 이동하면서 조회가 진행되는 방법을 확인합니다. 인증서에 암호가 없는 경우 `password` 항목을 생략합니다.
   >   
   > ```
   > "sslCertificates": [
   >     {
   >         "name": "appGwSslCertificate",
   >         "properties": {
   >             "data": "[parameters('appGatewaySSLCertificateData')]",
   >             "password": "[parameters('appGatewaySSLCertificatePassword')]"
   >         }
   >     }
   > ]
   > ```

1. **애플리케이션 게이트웨이 구성**

   위의 두 단계를 완료한 후에는 사용자 할당 관리 ID를 사용하도록 기존 Application Gateway를 설정하거나 수정할 수 있습니다. 자세한 내용은 [Set-AzApplicationGatewayIdentity](/powershell/module/az.network/set-azapplicationgatewayidentity)를 참조하세요.

   Key Vault 인증서 또는 비밀 ID의 전체 URI를 가리키도록 HTTP 수신기의 TLS/SSL 인증서를 구성할 수도 있습니다.

   ![Key Vault 인증서](media/key-vault-certs/ag-kv.png)

## <a name="next-steps"></a>다음 단계

[Azure PowerShell을 사용하여 Key Vault 인증서로 TLS 종료 구성](configure-keyvault-ps.md)

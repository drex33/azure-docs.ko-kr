---
title: App Service Environment의 인증서
description: App Service Environment의 인증서와 관련된 항목을 설명합니다. 인증서 바인딩이 ASE의 단일 테넌트 앱에서 작동하는 방식에 대해 알아봅니다.
author: madsd
ms.topic: overview
ms.date: 11/15/2021
ms.author: madsd
ms.openlocfilehash: a86a5e67faafc88c837f7401db473b0164125a82
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132526830"
---
# <a name="certificates-and-the-app-service-environment"></a>인증서 및 App Service Environment 
> [!NOTE]
> 이 문서에서는 격리된 v2 App Service 요금제와 함께 사용되는 App Service Environment v3에 관해 설명합니다.
>

ASE(App Service Environment)는 Azure 가상 네트워크 내에서 실행되는 Azure App Service의 배포입니다. 인터넷 액세스 가능 애플리케이션 엔드포인트 또는 가상 네트워크에 있는 애플리케이션 엔드포인트를 사용하여 배포할 수 있습니다. 인터넷 액세스 가능 엔드포인트를 사용하여 ASE를 배포하는 경우 해당 배포를 외부 ASE라고 합니다. 가상 네트워크의 엔드포인트를 사용하여 ASE를 배포하는 경우 해당 배포를 ILB ASE라고 합니다. [ILB ASE 만들기 및 사용](./creation.md) 문서에서 ILB ASE에 대해 자세히 알아볼 수 있습니다.

## <a name="application-certificates"></a>애플리케이션 인증서

ASE에서 호스팅되는 앱은 다중 테넌트 App Service에서 사용할 수 있는 앱 중심 인증서 기능을 사용할 수 있습니다. 이러한 기능은 다음과 같습니다.

- SNI 인증서
- KeyVault 호스팅 인증서

이러한 인증서를 업로드하고 관리하기 위한 요구 사항 및 지침은 [Azure App Service의 TLS/SSL 인증서 추가](../configure-ssl-certificate.md)에서 사용할 수 있습니다.

인증서가 App Service 앱 또는 함수 앱에 추가되면 [사용자 지정 도메인 이름을 보호](../configure-ssl-bindings.md)하거나 [애플리케이션 코드에서 사용할 수 있습니다](../configure-ssl-certificate-in-code.md).

## <a name="tls-settings"></a>TLS 설정

[TLS 설정은 앱 수준에서 구성](../configure-ssl-bindings.md#enforce-tls-versions)할 수 있습니다.

## <a name="private-client-certificate"></a>프라이빗 클라이언트 인증서

일반적인 사용 사례는 클라이언트-서버 모델에서 앱을 클라이언트로 구성하는 것입니다. 프라이빗 CA 인증서를 사용하여 서버를 보호하는 경우 클라이언트 인증서를 앱에 업로드해야 합니다. 다음 지침에서는 앱이 실행되는 작업자의 신뢰 저장소(truststore)에 인증서를 로드합니다. 하나의 앱에 인증서를 로드하는 경우 인증서를 다시 업로드하지 않고 동일한 App Service 계획에서 다른 앱과 함께 사용할 수 있습니다.

>[!NOTE]
> 프라이빗 클라이언트 인증서는 앱 외부에서 지원되지 않습니다. 이렇게 하면 프라이빗 인증서를 사용하여 레지스트리에서 앱 컨테이너 이미지를 끌어오고 프라이빗 인증서를 사용하여 프런트 엔드 서버를 통해 유효성을 검사하는 TLS와 같은 시나리오에서 사용이 제한됩니다.

다음 단계에 따라 인증서( *.cer* 파일)를 ASE의 앱에 업로드합니다. *.cer* 파일은 인증서에서 내보낼 수 있습니다. 테스트를 위해 임시 자체 서명된 인증서를 생성하는 PowerShell 예제가 끝에 있습니다.

1. Azure Portal에서 인증서가 필요한 앱으로 이동합니다.
1. 앱에서 **TLS/SSL 설정** 으로 이동합니다. **공개 키 인증서(.cer)** 를 클릭합니다. **공개 키 인증서 업로드** 를 선택합니다. 이름을 입력합니다. *.cer* 파일을 찾아서 선택합니다. 업로드를 선택합니다. 
1. 지문을 복사합니다.
1. **애플리케이션 설정** 으로 이동합니다. 지문을 값으로 사용하여 WEBSITE_LOAD_ROOT_CERTIFICATES 앱 설정을 만듭니다. 인증서가 여러 개 있으면 동일한 설정에 공백 없이 쉼표로 구분하여 배치할 수 있습니다. 예를 들어 다음과 같습니다. 

    84EC242A4EC7957817B8E48913E50953552DAFA6,6A5C65DC9247F762FE17BF8D4906E04FE6B31819

인증서는 해당 설정을 구성한 앱과 동일한 App Service 계획에 있는 모든 앱에서 사용할 수 있습니다. 다른 App Service 요금제의 앱에서 사용할 수 있어야 하는 경우 해당 App Service 요금제의 앱에서 앱 설정 작업을 반복해야 합니다. 인증서가 설정되었는지 확인하려면 Kudu 콘솔로 이동하여 PowerShell 디버그 콘솔에서 다음 명령을 실행합니다.

```azurepowershell-interactive
dir Cert:\LocalMachine\Root
```

테스트를 수행하려면 자체 서명된 인증서를 만들고 다음 PowerShell을 사용하여 *.cer* 파일을 생성할 수 있습니다. 

```azurepowershell-interactive
$certificate = New-SelfSignedCertificate -CertStoreLocation "Cert:\LocalMachine\My" -DnsName "*.internal-contoso.com","*.scm.internal-contoso.com"

$certThumbprint = "Cert:\LocalMachine\My\" + $certificate.Thumbprint
$fileName = "exportedcert.cer"
Export-Certificate -Cert $certThumbprint -FilePath $fileName -Type CERT
```

## <a name="next-steps"></a>다음 단계

* [애플리케이션 코드에서 인증서를 사용](../configure-ssl-certificate-in-code.md)하는 방법에 대한 정보
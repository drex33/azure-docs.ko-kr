---
title: 인증서 바인딩
description: App Service Environment v2의 인증서와 관련된 다양한 항목을 설명합니다. 인증서 바인딩이 ASE의 단일 테넌트 앱에서 작동하는 방식에 대해 알아봅니다.
author: madsd
ms.topic: overview
ms.date: 11/15/2021
ms.author: madsd
ms.openlocfilehash: 0ecc9f29ae469cea01c29a23c5491320c37e7cc5
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132521000"
---
# <a name="certificates-and-the-app-service-environment-v2"></a>인증서 및 App Service Environment v2
> [!NOTE]
> 이 문서에서는 격리된 App Service 요금제와 함께 사용되는 App Service Environment v2에 관해 설명합니다.
> 
ASE(App Service Environment)는 Azure VNet(Virtual Network) 내에서 실행되는 Azure App Service의 배포입니다. 인터넷 액세스 가능 애플리케이션 엔드포인트 또는 VNet에 있는 애플리케이션 엔드포인트를 사용하여 배포할 수 있습니다. 인터넷 액세스 가능 엔드포인트를 사용하여 ASE를 배포하는 경우 해당 배포를 외부 ASE라고 합니다. VNet에 엔드포인트가 있는 ASE를 배포하면 해당 배포를 ILB ASE라고 합니다. [ILB ASE 만들기 및 사용](./create-ilb-ase.md) 문서에서 ILB ASE에 대해 자세히 알아볼 수 있습니다.

ASE는 단일 테넌트 시스템입니다. 단일 테넌트이므로 다중 테넌트 App Service에서 사용할 수 없는 ASE에서만 사용할 수 있는 일부 기능이 있습니다. 

## <a name="ilb-ase-certificates"></a>ILB ASE 인증서 

외부 ASE를 사용하는 경우 앱이 &lt;appname&gt;.&lt;asename&gt;.p.azurewebsites.net에서 연결됩니다. 기본적으로 모든 ASE, 심지어 ILB ASE도 해당 형식에 따라 인증서를 사용하여 만들어집니다. ILB ASE가 있는 경우 ILB ASE를 만들 때 지정한 도메인 이름에 따라 앱이 연결됩니다. 앱에서 TLS을 지원하려면 인증서를 업로드해야 합니다. 내부 인증 기관을 사용하거나, 외부 발급자로부터 인증서를 구입하거나, 자체 서명된 인증서를 사용하는 등의 방법으로 유효한 TLS/SSL 인증서를 구합니다. 

ILB ASE를 사용하여 인증서를 구성하는 두 가지 옵션이 있습니다.  ILB ASE에 대한 와일드카드 기본 인증서를 설정하거나 ASE의 개별 웹앱에 인증서를 설정할 수 있습니다.  선택한 항목에 관계없이 다음 인증서 특성을 올바르게 구성해야 합니다.

- **주체**: 와일드카드 ILB ASE 인증서의 경우 이 특성은 *.[your-root-domain-here]로 설정해야 합니다. 앱에 대한 인증서를 만드는 경우 [appname].[your-root-domain-here]여야 합니다.
- **주체 대체 이름**: 이 특성에는 와일드카드 ILB ASE 인증서에 대한 *.[your-root-domain-here] 및 *.scm.[your-root-domain-here]가 모두 포함되어야 합니다. 앱에 대한 인증서를 만드는 경우 [appname].[your-root-domain-here] 및 [appname].scm.[your-root-domain-here]여야 합니다.

세 번째 변형으로, 와일드카드 참조를 사용하는 대신 인증서의 SAN에 있는 개별 앱 이름이 모두 포함된 ILB ASE 인증서를 만들 수 있습니다. 이 방법의 문제점은 ASE에 넣는 앱의 이름을 미리 알아야 하거나 ILB ASE 인증서를 계속 업데이트해야 한다는 것입니다.

### <a name="upload-certificate-to-ilb-ase"></a>ILB ASE에 인증서 업로드 

ILB ASE가 포털에서 만들어지면 ILB ASE에 대한 인증서를 설정해야 합니다. 인증서가 설정될 때까지 ASE에는 인증서를 설정하지 않았다는 배너가 표시됩니다.  

업로드하는 인증서는 .pfx 파일이어야 합니다. 인증서가 업로드된 후 인증서가 사용되기까지 약 20분의 시간 지연이 있습니다. 

ASE를 만들고 포털 또는 하나의 템플릿에서 인증서를 하나의 작업으로 업로드할 수 없습니다. 별도의 작업으로 [템플릿에서 ASE 만들기](./create-from-template.md) 문서에서 설명한 대로 템플릿을 사용하여 인증서를 업로드할 수 있습니다.  

테스트를 위해 자체 서명된 인증서를 빠르게 만들려면 다음과 같은 PowerShell 비트를 사용할 수 있습니다.

```azurepowershell-interactive
$certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

$certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
$password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

$fileName = "exportedcert.pfx"
Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password
```

자체 서명된 인증서를 만들 때 주체 이름에 CN={ASE_NAME_HERE}_InternalLoadBalancingASE 형식이 있는지 확인해야 합니다.

## <a name="application-certificates"></a>애플리케이션 인증서 

ASE에서 호스팅되는 앱은 다중 테넌트 App Service에서 사용할 수 있는 앱 중심 인증서 기능을 사용할 수 있습니다. 이러한 기능은 다음과 같습니다.  

- SNI 인증서 
- IP 기반 SSL - 외부 ASE에서만 지원되며,  ILB ASE는 IP 기반 SSL을 지원하지 않습니다.
- KeyVault 호스팅 인증서 

이러한 인증서의 업로드 및 관리 지침은 [Azure App Service의 TLS/SSL 인증서 추가](../configure-ssl-certificate.md)에서 사용할 수 있습니다.  웹앱에 할당한 사용자 지정 도메인 이름과 일치하도록 인증서를 간단히 구성하는 경우 이러한 지침만으로도 충분합니다. 기본 도메인 이름을 사용하여 ILB ASE 웹앱에 대한 인증서를 업로드하는 경우 앞에서 설명한 대로 인증서의 SAN에 scm 사이트를 지정합니다. 

## <a name="tls-settings"></a>TLS 설정 

TLS 설정은 앱 수준에서 구성할 수 있습니다.  

## <a name="private-client-certificate"></a>프라이빗 클라이언트 인증서 

일반적인 사용 사례는 클라이언트-서버 모델에서 앱을 클라이언트로 구성하는 것입니다. 프라이빗 CA 인증서를 사용하여 서버를 보호하는 경우 클라이언트 인증서를 앱에 업로드해야 합니다.  다음 지침에서는 앱이 실행되는 작업자의 신뢰 저장소(truststore)에 인증서를 로드합니다. 하나의 앱에 인증서를 로드하는 경우 인증서를 다시 업로드하지 않고 동일한 App Service 계획에서 다른 앱과 함께 사용할 수 있습니다.

ASE에서 인증서를 앱에 업로드하려면 다음을 수행합니다.

1. 인증서에 대한 *.cer* 파일을 생성합니다. 
2. Azure Portal에서 인증서가 필요한 앱으로 이동합니다.
3. 앱에서 SSL 설정으로 이동합니다. [인증서 업로드]를 클릭합니다. [공용]을 선택합니다. [로컬 머신]을 선택합니다. 이름을 입력합니다. *.cer* 파일을 찾아서 선택합니다. 업로드를 선택합니다. 
4. 지문을 복사합니다.
5. [애플리케이션 설정]으로 이동합니다. 지문을 값으로 사용하여 WEBSITE_LOAD_ROOT_CERTIFICATES 앱 설정을 만듭니다. 인증서가 여러 개 있으면 동일한 설정에 공백 없이 쉼표로 구분하여 배치할 수 있습니다. 예를 들어 다음과 같습니다. 

    84EC242A4EC7957817B8E48913E50953552DAFA6,6A5C65DC9247F762FE17BF8D4906E04FE6B31819

인증서는 해당 설정을 구성한 앱과 동일한 App Service 계획에 있는 모든 앱에서 사용할 수 있습니다. 다른 App Service 계획의 응용 프로그램에서 이 인증서를 사용할 수 있도록 하려면 해당 App Service 계획의 앱에서 앱 설정 작업을 반복해야 합니다. 인증서가 설정되었는지 확인하려면 Kudu 콘솔로 이동하여 PowerShell 디버그 콘솔에서 다음 명령을 실행합니다.

```azurepowershell-interactive
dir cert:\localmachine\root
```

테스트를 수행하려면 자체 서명된 인증서를 만들고 다음 PowerShell을 사용하여 *.cer* 파일을 생성할 수 있습니다. 

```azurepowershell-interactive
$certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

$certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
$password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

$fileName = "exportedcert.cer"
export-certificate -Cert $certThumbprint -FilePath $fileName -Type CERT
```

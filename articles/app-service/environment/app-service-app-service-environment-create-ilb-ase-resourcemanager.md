---
title: ILB App Service Environment v1 만들기
description: ILB (내부 부하 분산 장치)를 사용 하 여 ASE (App Service 환경)를 만듭니다. 이 문서는 레거시 v1 ASE를 사용하는 고객에게만 제공됩니다.
author: madsd
ms.assetid: 091decb6-b0de-42a1-9f2f-c18d9b2e67df
ms.topic: article
ms.date: 10/10/2021
ms.author: madsd
ms.custom: seodec18
ms.openlocfilehash: 46a6cbedda92e5dce9f7c3cb7500c6632326c944
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129999856"
---
# <a name="how-to-create-an-ilb-asev1-using-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿을 사용 하 여 ILB ASEv1을 만드는 방법

> [!NOTE] 
> 이 문서는 ASE(App Service Environment) v1에 관한 내용입니다. 사용하기가 더 쉽고 더 강력한 인프라에서 실행되는 최신 버전의 App Service Environment가 있습니다. 새 버전에 대한 자세한 내용은 [App Service Environment 소개](intro.md)를 참조하세요.
>

## <a name="overview"></a>개요
App Service Environment는 공용 VIP 대신 가상 네트워크 내부 주소를 사용하여 만들 수 있습니다. 이 내부 주소는 ILB(내부 부하 분산 장치)라고 하는 Azure 구성 요소에서 제공됩니다. Azure 포털을 사용하여 ILB ASE를 만들 수 있습니다. Azure Resource Manager 템플릿을 통해 자동화 방식으로 만들 수도 있습니다. 이 문서에서는 Azure Resource Manager 템플릿으로 ILB ASE를 만드는 데 필요한 단계와 구문을 안내합니다.

ILB ASE 생성을 자동화하는 과정은 세 단계로 진행됩니다.

1. 먼저 공용 VIP 대신 내부 부하 분산 장치 주소를 사용하여 가상 네트워크에 기본 ASE가 생성됩니다. 이 단계의 일부로 루트 도메인 이름이 ILB ASE에 할당됩니다.
2. ILB ASE가 만들어지면 TLS/SSL 인증서가 업로드됩니다.
3. 업로드된 TLS/SSL 인증서는 “기본” TLS/SSL 인증서로 해당 ILB ASE에 명시적으로 할당됩니다. 이 TLS/SSL 인증서는 ASE에 할당 된 공용 루트 도메인 (예:)을 사용 하 여 앱의 주소를 지정할 때 ILB ASE의 앱에 대 한 TLS 트래픽에 사용 됩니다. `https://someapp.mycustomrootcomain.com`

## <a name="creating-the-base-ilb-ase"></a>기본 ILB ASE 만들기
예제 Azure Resource Manager 템플릿 및 관련 매개 변수 파일을 [여기][quickstartilbasecreate]에서 사용할 수 있습니다.

*Azuredeploy* 파일에 있는 대부분의 매개 변수는 Ilb 및 public VIP에 바인딩된 ase를 만드는 데 일반적입니다. 아래 목록에서는 ILB ASE를 만들 때 특별한 고려 사항이 있거나 고유한 매개 변수를 호출합니다.

* *internalLoadBalancingMode*: 제어 및 데이터 포트가 노출 되는 방법을 결정 합니다.
    * *3* 은 포트 80/443에 대 한 HTTP/HTTPS 트래픽과 ASE의 FTP 서비스에서 수신 하는 제어/데이터 채널 포트는 ilb 할당 가상 네트워크 내부 주소에 바인딩됩니다.
    * *2* 는 FTP 서비스 관련 포트 (제어 및 데이터 채널 모두)가 ilb 주소에 바인딩되기 때문에 HTTP/HTTPS 트래픽은 공용 VIP에 남아 있습니다.
    * *0* 은 모든 트래픽이 ASE 외부를 만드는 공용 VIP에 바인딩되어 있음을 의미 합니다.
* *dnsSuffix*: 이 매개 변수는 ASE에 할당될 기본 루트 도메인을 정의합니다. Azure App Service의 공용 변형에서 모든 웹앱용 기본 루트 도메인은 *azurewebsites.net* 입니다. 그러나 ILB ASE는 고객의 가상 네트워크 내부에 있으므로 공용 서비스의 기본 루트 도메인을 사용하는 것은 적합하지 않습니다. 대신, ILB ASE에는 회사의 내부 가상 네트워크 내에서 사용하기 적합한 기본 루트 도메인이 있어야 합니다. 예를 들어 가상의 Contoso Corporation은 Contoso의 가상 네트워크 내에서만 확인 가능하고 액세스할 수 있는 앱에 기본 루트 도메인 *internal-contoso.com* 을 사용할 수 있습니다. 
* *ipSslAddressCount*: 이 매개 변수는 ILB ASE가 단일 ILB 주소만 가지므로 *azuredeploy.json* 파일에서 자동으로 기본값인 0을 갖습니다. ILB ASE에 대 한 명시적인 IP SSL 주소가 없으므로 ILB ASE에 대 한 IP SSL 주소 풀을 0으로 설정 해야 합니다. 그렇지 않으면 프로 비전 오류가 발생 합니다. 

ILB ASE에 사용할 *azuredeploy.parameters.json* 파일이 채워지면 다음 PowerShell 코드 조각을 사용하여 ILB ASE를 만들 수 있습니다.  머신에서 Azure Resource Manager 템플릿 파일이 있는 위치와 일치하도록 파일 경로를 변경합니다.  또한 Azure Resource Manager 배포 이름 및 리소스 그룹 이름에 대해 고유한 값을 제공해야 합니다.

```azurepowershell-interactive
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

Azure Resource Manager 템플릿이 제출 된 후 ILB ASE를 만드는 데 몇 시간이 소요 됩니다. 다 만들어지면 배포를 트리거한 구독에 대한 App Service Environment 목록의 포털 UX에 ILB ASE가 표시됩니다.

## <a name="uploading-and-configuring-the-default-tlsssl-certificate"></a>“기본” TLS/SSL 인증서 업로드 및 구성
ILB ASE를 만든 후에는 TLS/SSL 인증서를 앱에 TLS/SSL 연결을 설정하는 데 사용하는 “기본” TLS/SSL 인증서로 ASE와 연결해야 합니다.  가상의 Contoso Corporation 예제를 계속 살펴보면, ASE의 기본 DNS 접미사가 *internal-contoso.com* 이고 *`https://some-random-app.internal-contoso.com`* 에 연결하려는 경우 * *.internal-contoso.com* 에 유효한 TLS/SSL 인증서가 있어야 합니다. 

내부 Ca, 외부 발급자 로부터 인증서를 구입 하 고 자체 서명 된 인증서를 사용 하 여 유효한 TLS/SSL 인증서를 얻을 수 있는 다양 한 방법이 있습니다.  TLS/SSL 인증서 소스에 관계없이 다음 인증서 특성을 올바르게 구성해야 합니다.

* *Subject*: 이 특성은 **.your-root-domain-here.com* 으로 설정되어야 합니다.
* *주체 대체 이름*: 이 특성에는 **.your-root-domain-here.com* 및 **.scm.your-root-domain-here.com* 둘 다 포함되어야 합니다.  두 번째 항목을 포함해야 하는 이유는 각 앱과 연결된 SCM/Kudu 사이트에 대한 TLS 연결이 *your-app-name.scm.your-root-domain-here.com* 형식의 주소를 사용하여 만들어지기 때문입니다.

유효한 TLS/SSL 인증서가 있는 경우 두 가지 추가 준비 단계가 필요합니다. TLS/SSL 인증서를 .pfx 파일로 변환/저장해야 합니다. .pfx 파일에는 모든 중간 인증서 및 루트 인증서가 포함되어야 하며 암호로 보호되어야 합니다.

TLS/SSL 인증서는 Azure Resource Manager 템플릿을 사용하여 업로드되므로 결과 .pfx 파일을 Base64 문자열로 변환해야 합니다.  Azure Resource Manager 템플릿은 텍스트 파일이므로 .pfx 파일은 템플릿의 매개 변수로 포함될 수 있게 Base64 문자열로 변환해야 합니다.

아래의 PowerShell 코드 조각은 자체 서명된 인증서를 생성하고, 인증서를 .pfx 파일로 내보내고, .pfx 파일을 Base64 인코딩 문자열로 변환한 다음 Base64 인코딩 문자열을 개별 파일로 저장하는 예를 보여 줍니다. Base64 인코딩을 위한 PowerShell 코드는 [PowerShell 스크립트 블로그][examplebase64encoding]에서 조정되었습니다.

```azurepowershell-interactive
$certificate = New-SelfSignedCertificate -certstorelocation cert:\localmachine\my -dnsname "*.internal-contoso.com","*.scm.internal-contoso.com"

$certThumbprint = "cert:\localMachine\my\" + $certificate.Thumbprint
$password = ConvertTo-SecureString -String "CHANGETHISPASSWORD" -Force -AsPlainText

$fileName = "exportedcert.pfx"
Export-PfxCertificate -cert $certThumbprint -FilePath $fileName -Password $password     

$fileContentBytes = get-content -encoding byte $fileName
$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
$fileContentEncoded | set-content ($fileName + ".b64")
```

TLS/SSL 인증서가 성공적으로 생성 되 고 base64 인코딩 문자열로 변환 되 면 [기본 tls/ssl 인증서를 구성][configuringDefaultSSLCertificate] 하기 위한 예제 Azure Resource Manager 템플릿을 사용할 수 있습니다.

*azuredeploy.parameters.json* 파일의 매개 변수는 다음과 같습니다.

* *appServiceEnvironmentName*: 구성하는 ILB ASE의 이름입니다.
* *existingAseLocation*: ILB ASE가 배포된 Azure 지역을 포함하는 텍스트 문자열입니다.  예를 들어 "미국 중남부"입니다.
* *pfxBlobString*: .pfx 파일의 Base64 인코딩 문자열 표현입니다.  앞서 표시된 코드 조각을 사용하여 "exportedcert.pfx.b64"에 포함된 문자열을 복사한 후 *pfxBlobString* 특성의 값으로 붙여 넣습니다.
* *password*: .pfx 파일을 보호하는 데 사용되는 암호입니다.
* *certificateThumbprint*: 인증서의 지문입니다.  PowerShell에서이 값을 검색 하는 경우 (예: 이전 코드 조각의 *$certThumbprint* ) 값을 그대로 사용할 수 있습니다.  그러나 Windows 인증서 대화 상자의 값을 복사하는 경우 불필요한 공백을 제거해야 합니다.  *certificateThumbprint* 는 AF3143EB61D43F6727842115BB7F17BBCECAECAE와 같이 나타납니다.
* *certificateName*: 인증서를 식별하는 데 사용되는 직접 선택한 친숙한 문자열 식별자입니다. 이 이름은 TLS/SSL 인증서를 나타내는 *Microsoft.Web/certificates* 엔터티에 대한 고유한 Azure Resource Manager 식별자의 일부로 사용됩니다. 이름은 **반드시**\_yourASENameHere_InternalLoadBalancingASE 접미사로 끝나야 합니다. 이 접미사는 포털에서 인증서가 ILB 지원 ASE 보안에 사용되는 표시기로 사용됩니다.

*azuredeploy.parameters.json* 을 축약한 예는 다음과 같습니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "appServiceEnvironmentName": {
            "value": "yourASENameHere"
        },
        "existingAseLocation": {
            "value": "East US 2"
        },
        "pfxBlobString": {
            "value": "MIIKcAIBAz...snip...snip...pkCAgfQ"
        },
        "password": {
            "value": "PASSWORDGOESHERE"
        },
        "certificateThumbprint": {
            "value": "AF3143EB61D43F6727842115BB7F17BBCECAECAE"
        },
        "certificateName": {
            "value": "DefaultCertificateFor_yourASENameHere_InternalLoadBalancingASE"
        }
    }
}
```

*azuredeploy.parameters.json* 파일이 채워지면 다음 PowerShell 코드 조각을 사용하여 기본 TLS/SSL 인증서를 구성할 수 있습니다. 머신에서 Azure Resource Manager 템플릿 파일이 있는 위치와 일치하도록 파일 경로를 변경합니다. 또한 Azure Resource Manager 배포 이름 및 리소스 그룹 이름에 대해 고유한 값을 제공해야 합니다.

```azurepowershell-interactive
$templatePath="PATH\azuredeploy.json"
$parameterPath="PATH\azuredeploy.parameters.json"

New-AzResourceGroupDeployment -Name "CHANGEME" -ResourceGroupName "YOUR-RG-NAME-HERE" -TemplateFile $templatePath -TemplateParameterFile $parameterPath
```

Azure Resource Manager 템플릿이 제출 되 면 변경 내용을 적용 하기 위해 ASE 프런트 엔드에서 약 40 분이 소요 됩니다. 예를 들어 두 개의 프런트 엔드를 사용 하 여 기본 크기의 ASE를 사용 하는 경우 템플릿을 완료 하는 데 약 1 시간이 소요 되 고 20 분이 소요 됩니다. 템플릿이 실행되는 동안에는 ASE 크기를 조정할 수 없습니다.

템플릿이 완료되면 ILB ASE의 앱에 HTTPS를 통해 액세스할 수 있으며 기본 TLS/SSL 인증서를 사용하여 연결이 보호됩니다. 기본 TLS/SSL 인증서는 애플리케이션 이름 및 기본 호스트 이름 조합을 사용하여 ILB ASE의 앱 주소를 지정할 때 사용됩니다.  예를 들어는 *`https://mycustomapp.internal-contoso.com`* **. internal-contoso.com* 에 대 한 기본 TLS/SSL 인증서를 사용 합니다.

그러나 공용 다중 테넌트 서비스에서 실행 중인 앱의 경우와 마찬가지로, 개발자는 개별 앱에 대한 사용자 지정 호스트 이름을 구성한 다음 개별 앱에 대해 고유한 SNI TLS/SSL 인증서 바인딩을 구성할 수 있습니다.

## <a name="getting-started"></a>시작
App Service 환경을 시작하려면 [App Service 환경 소개](app-service-app-service-environment-intro.md)

[!INCLUDE [app-service-web-try-app-service](../../../includes/app-service-web-try-app-service.md)]

<!-- LINKS -->
[quickstartilbasecreate]: https://azure.microsoft.com/resources/templates/web-app-ase-ilb-create/
[examplebase64encoding]: https://powershellscripts.blogspot.com/2007/02/base64-encode-file.html 
[configuringDefaultSSLCertificate]: https://azure.microsoft.com/resources/templates/web-app-ase-ilb-configure-default-ssl/


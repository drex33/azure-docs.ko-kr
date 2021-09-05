---
title: 코드에서 TLS/SSL 인증서 사용
description: 코드에서 클라이언트 인증서를 사용하는 방법을 알아봅니다. 클라이언트 인증서를 사용하여 원격 리소스를 사용하여 인증하거나 암호화 작업을 실행합니다.
ms.topic: article
ms.date: 09/22/2020
ms.reviewer: yutlin
ms.custom: seodec18
ms.openlocfilehash: 3e5aab3d38e4f981e27ceb59db1511c54ed89381
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122537234"
---
# <a name="use-a-tlsssl-certificate-in-your-code-in-azure-app-service"></a>Azure App Service의 코드에서 TLS/SSL 인증서 사용

애플리케이션 코드에서 [App Service에 추가하는 공용 또는 개인 인증서](configure-ssl-certificate.md)에 액세스할 수 있습니다. 앱 코드는 클라이언트 역할을 하며 인증서 인증을 필요로 하는 외부 서비스에 액세스하거나 암호화 작업을 수행해야 할 수 있습니다. 이 방법 가이드에서는 애플리케이션 코드에서 공용 또는 개인 인증서를 사용하는 방법을 보여 줍니다.

코드에서 인증서를 사용하는 이 방법을 사용하면 App Service에서 TLS 기능을 사용하게 됩니다. 이 경우에 앱은 **기본** 계층 이상이어야 합니다. 앱이 **무료** 또는 **공유** 계층에 있는 경우 [앱 리포지토리에 인증서 파일을 포함할](#load-certificate-from-file) 수 있습니다.

App Service에서 TLS/SSL 인증서를 관리할 수 있는 경우 인증서 및 애플리케이션 코드를 별도로 유지 관리하여 중요한 데이터를 보호할 수 있습니다.

## <a name="prerequisites"></a>사전 요구 사항

이 방법 가이드를 수행하려면 다음이 필요합니다.

- [App Service 앱 만들기](./index.yml)
- [앱에 인증서 추가](configure-ssl-certificate.md)

## <a name="find-the-thumbprint"></a>지문 찾기

<a href="https://portal.azure.com" target="_blank">Azure Portal</a>의 왼쪽 메뉴에서 **App Services** >  **\<app-name>** 를 선택합니다.

앱의 왼쪽 탐색에서 **TLS/SSL 설정** 을 선택하고 **개인 키 인증서(.pfx)** 또는 **공개 키 인증서(.cer)** 를 선택합니다.

사용하려는 인증서를 찾아 지문을 복사합니다.

![인증서 지문 복사](./media/configure-ssl-certificate/create-free-cert-finished.png)

## <a name="make-the-certificate-accessible"></a>인증서에 액세스할 수 있도록 설정

앱 코드에서 인증서에 액세스하려면 <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>에서 다음 명령을 실행하여 `WEBSITE_LOAD_CERTIFICATES` 앱 설정에 해당 지문을 추가합니다.

```azurecli-interactive
az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_CERTIFICATES=<comma-separated-certificate-thumbprints>
```

모든 인증서에 액세스할 수 있도록 하려면 값을 `*`로 설정합니다.

## <a name="load-certificate-in-windows-apps"></a>Windows 앱에서 인증서 로드

`WEBSITE_LOAD_CERTIFICATES`앱 설정을 사용하면 Windows 인증서 저장소에 있는 [Current User\My](/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores)의 Windows 호스팅된 앱에서 지정된 인증서에 액세스할 수 있습니다.

C# 코드에서는 인증서 지문으로 인증서에 액세스합니다. 다음 코드는 `E661583E8FABEF4C0BEF694CBC41C28FB81CD870` 지문으로 인증서를 로드합니다.

```csharp
using System;
using System.Linq;
using System.Security.Cryptography.X509Certificates;

string certThumbprint = "E661583E8FABEF4C0BEF694CBC41C28FB81CD870";
bool validOnly = false;

using (X509Store certStore = new X509Store(StoreName.My, StoreLocation.CurrentUser))
{
  certStore.Open(OpenFlags.ReadOnly);

  X509Certificate2Collection certCollection = certStore.Certificates.Find(
                              X509FindType.FindByThumbprint,
                              // Replace below with your certificate's thumbprint
                              certThumbprint,
                              validOnly);
  // Get the first cert with the thumbprint
  X509Certificate2 cert = certCollection.OfType<X509Certificate2>().FirstOrDefault();

  if (cert is null)
      throw new Exception($"Certificate with thumbprint {certThumbprint} was not found");

  // Use certificate
  Console.WriteLine(cert.FriendlyName);
  
  // Consider to call Dispose() on the certificate after it's being used, avaliable in .NET 4.6 and later
}
```

Java 코드에서는 주제 일반 이름 필드([공개 키 인증서](https://en.wikipedia.org/wiki/Public_key_certificate) 참조)를 사용하여 "WINDOWS-MY" 저장소에서 인증서에 액세스합니다. 다음 코드에서는 개인 키 인증서를 로드하는 방법을 보여 줍니다.

```java
import org.springframework.web.bind.annotation.RestController;
import org.springframework.web.bind.annotation.RequestMapping;
import java.security.KeyStore;
import java.security.cert.Certificate;
import java.security.PrivateKey;

...
KeyStore ks = KeyStore.getInstance("Windows-MY");
ks.load(null, null); 
Certificate cert = ks.getCertificate("<subject-cn>");
PrivateKey privKey = (PrivateKey) ks.getKey("<subject-cn>", ("<password>").toCharArray());

// Use the certificate and key
...
```

지원하지 않거나 Windows 인증서 저장소에 대한 충분한 지원을 제공하지 않는 언어의 경우 [파일에서 인증서 로드](#load-certificate-from-file)를 참조하세요.

## <a name="load-certificate-from-file"></a>파일에서 인증서 로드

수동으로 업로드하는 인증서 파일을 로드해야 하는 경우 예를 들어, [Git](deploy-local-git.md) 대신 [FTPS](deploy-ftp.md)를 사용하여 인증서를 업로드하는 것이 좋습니다. 개인 인증서와 같이 중요한 데이터는 원본 제어에서 제외해야 합니다.

> [!NOTE]
> 파일에서 인증서를 로드하는 경우에도 Windows의 ASP.NET 및 ASP.NET Core는 인증서 저장소에 액세스해야 합니다. Windows .NET 앱에서 인증서 파일을 로드하려면 <a target="_blank" href="https://shell.azure.com" >Cloud Shell</a>에서 다음 명령을 사용하여 현재 사용자 프로필을 로드합니다.
>
> ```azurecli-interactive
> az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings WEBSITE_LOAD_USER_PROFILE=1
> ```
>
> 코드에서 인증서를 사용하는 이 방법을 사용하면 App Service에서 TLS 기능을 사용하게 됩니다. 이 경우에 앱은 **기본** 계층 이상이어야 합니다.

다음 C# 예제에서는 앱의 상대 경로에서 공용 인증서를 로드합니다.

```csharp
using System;
using System.IO;
using System.Security.Cryptography.X509Certificates;

...
var bytes = File.ReadAllBytes("~/<relative-path-to-cert-file>");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

Node.js, PHP, Python, Java 또는 Ruby의 파일에서 TLS/SSL 인증서를 로드하는 방법을 보려면 해당 언어 또는 웹 플랫폼에 대한 설명서를 참조하세요.

## <a name="load-certificate-in-linuxwindows-containers"></a>Linux/Windows 컨테이너에서 인증서 로드

`WEBSITE_LOAD_CERTIFICATES` 앱 설정을 사용하면 지정된 인증서를 Windows 또는 Linux 컨테이너 앱(기본 제공 Linux 컨테이너 포함)에서 파일로 액세스할 수 있습니다. 파일은 다음 디렉터리에 있습니다.

| 컨테이너 플랫폼 | 공용 인증서 | 프라이빗 인증서 |
| - | - | - |
| Windows 컨테이너 | `C:\appservice\certificates\public` | `C:\appservice\certificates\private` |
| Linux 컨테이너 | `/var/ssl/certs` | `/var/ssl/private` |

인증서 파일 이름은 인증서 지문입니다. 

> [!NOTE]
> App Service는 `WEBSITE_PRIVATE_CERTS_PATH`, `WEBSITE_INTERMEDIATE_CERTS_PATH`, `WEBSITE_PUBLIC_CERTS_PATH` 및 `WEBSITE_ROOT_CERTS_PATH` 환경 변수로 인증서 경로를 Windows 컨테이너에 삽입합니다. 나중에 인증서 경로가 변경되는 경우 인증서 경로를 하드 코딩하는 대신 환경 변수를 사용하여 인증서 경로를 참조하는 것이 좋습니다.
>

또한 [Windows Server Core 컨테이너](configure-custom-container.md#supported-parent-images)는 **LocalMachine\My** 에 있는 인증서 저장소에 인증서를 자동으로 로드합니다. 인증서를 로드하려면 [Windows 앱에서 인증서를 로드](#load-certificate-in-windows-apps)하는 것과 동일한 패턴을 따릅니다. Windows Nano 기반 컨테이너의 경우 위에 제공된 파일 경로를 사용하여 [파일에서 직접 인증서를 로드](#load-certificate-from-file)합니다.

다음 C# 코드는 Linux 앱에서 공용 인증서를 로드하는 방법을 보여 줍니다.

```csharp
using System;
using System.IO;
using System.Security.Cryptography.X509Certificates;

...
var bytes = File.ReadAllBytes("/var/ssl/certs/<thumbprint>.der");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

다음 C# 코드는 Linux 앱에서 개인 인증서를 로드하는 방법을 보여 줍니다.

```csharp
using System;
using System.IO;
using System.Security.Cryptography.X509Certificates;
...
var bytes = File.ReadAllBytes("/var/ssl/private/<thumbprint>.p12");
var cert = new X509Certificate2(bytes);

// Use the loaded certificate
```

Node.js, PHP, Python, Java 또는 Ruby의 파일에서 TLS/SSL 인증서를 로드하는 방법을 보려면 해당 언어 또는 웹 플랫폼에 대한 설명서를 참조하세요.

## <a name="more-resources"></a>추가 리소스

* [Azure App Service에서 TLS/SSL 바인딩으로 사용자 지정 DNS 이름 보호](configure-ssl-bindings.md)
* [HTTPS 적용](configure-ssl-bindings.md#enforce-https)
* [TLS 1.1/1.2 적용](configure-ssl-bindings.md#enforce-tls-versions)
* [FAQ: App Service Certificate](./faq-configuration-and-management.yml)
* [환경 변수 및 앱 설정 참조](reference-app-settings.md)
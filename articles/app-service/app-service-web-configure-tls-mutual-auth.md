---
title: TLS 상호 인증 구성
description: TLS에서 클라이언트 인증서를 인증하는 방법에 대해 알아보세요. Azure App Service를 통해 클라이언트 인증서를 앱 코드에서 확인을 위해 사용할 수 있습니다.
ms.assetid: cd1d15d3-2d9e-4502-9f11-a306dac4453a
ms.topic: article
ms.date: 12/11/2020
ms.custom: devx-track-csharp, seodec18
ms.openlocfilehash: a89d4356e6ea0a2f471d741f0223e1ade1100a9d
ms.sourcegitcommit: e9e332a512ed615a3c8ad5a11baa21649f14116d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133096152"
---
# <a name="configure-tls-mutual-authentication-for-azure-app-service"></a>Azure App Service에 대한 TLS 상호 인증 구성

다양한 유형의 인증을 사용하여 Azure App Service 앱에 대한 액세스를 제한할 수 있습니다. 이 작업을 수행하는 한 가지 방법은 클라이언트 요청이 TLS/SSL을 초과하고 인증서 요청 시 인증서를 검증하는 것입니다. 이 메커니즘을 TLS 상호 인증 또는 클라이언트 인증서 인증이라고 합니다. 이 문서에는 클라이언트 인증서 인증을 사용하도록 앱을 설정하는 방법이 나와 있습니다.

> [!NOTE]
> HTTP를 통해 사이트에 액세스하고 HTTPS를 통해서는 액세스하지 않는 경우 클라이언트 인증서가 제공되지 않습니다. 따라서 애플리케이션에 클라이언트 인증서가 필요한 경우 HTTP를 통한 애플리케이션 요청을 허용해서는 안 됩니다.
>

[!INCLUDE [Prepare your web app](../../includes/app-service-ssl-prepare-app.md)]

## <a name="enable-client-certificates"></a>클라이언트 인증서 사용하도록 설정

클라이언트 인증서를 사용해야 하도록 앱을 설정하려면:

1. 앱 관리 페이지의 왼쪽 탐색 영역에서 **구성** > **일반 설정** 을 선택하세요.

1. **클라이언트 인증서 모드** 를 **필수** 로 설정하세요. 페이지 위쪽에서 **저장** 을 클릭합니다.

Azure CLI와 동일하게 작업을 수행하려면 [Cloud Shell](https://shell.azure.com)에서 다음 명령을 실행하세요.

```azurecli-interactive
az webapp update --set clientCertEnabled=true --name <app-name> --resource-group <group-name>
```

## <a name="exclude-paths-from-requiring-authentication"></a>인증 요구에서 경로 제외

프로그램에 대해 상호 인증을 사용하도록 설정하면 앱 루트의 모든 경로에 액세스할 수 있는 클라이언트 인증서가 필요합니다. 특정 경로에 대한 해당 요구 사항을 제거하려면 애플리케이션 구성의 일부로 제외 경로를 정의하세요.

1. 앱 관리 페이지의 왼쪽 탐색 영역에서 **구성** > **일반 설정** 을 선택하세요.

1. 인증서 **제외 경로** 옆에 있는 편집 아이콘을 클릭합니다.

1. **새 경로** 를 클릭하고, 경로를 지정하거나 `,` 또는 `;`으로 구분된 경로 목록을 지정한 다음, **확인** 을 클릭합니다.

1. 페이지 위쪽에서 **저장** 을 클릭합니다.

다음 스크린샷에서 `/public`으로 시작하는 앱의 경로는 클라이언트 인증서를 요청하지 않습니다. 경로 일치 시 대/소문자를 구분하지 않습니다.

![인증서 제외 경로][exclusion-paths]

## <a name="access-client-certificate"></a>클라이언트 인증서 액세스

App Service에서 요청 TLS 종료는 프런트 엔드 부하 분산 장치에서 수행됩니다. [클라이언트 인증서를 사용](#enable-client-certificates)하여 요청을 앱 코드에 전달하면 App Service가 클라이언트 인증서로 `X-ARR-ClientCert`요청 헤더를 삽입합니다. 앱 서비스는 이 클라이언트 인증서를 사용하여 앱에 전달하는 것 외에 다른 작업을 수행하지 않습니다. 앱 코드는 클라이언트 인증서 유효성 검사를 담당합니다.

ASP용.NET의 경우, 클라이언트 인증서는 **HttpRequest.ClinentCertificate** 를 통해 사용할 수 있습니다.

다른 애플리케이션 스택(Node.js, PHP 등)의 경우 `X-ARR-ClientCert`요청 헤더의 base64 인코딩 값을 통해 클라이언트 인증서를 앱에서 사용할 수 있습니다.

## <a name="aspnet-5-aspnet-core-31-sample"></a>ASP.NET 5+, ASP.NET Core 3.1 샘플

ASP.NET Core의 경우 전달된 인증서를 구문 분석할 수 있도록 미들웨어가 제공됩니다. 전달된 프로토콜 헤더를 사용할 수 있도록 별도의 미들웨어가 제공됩니다. 전달된 인증서를 수락하려면 둘 다 있어야 합니다. [CertificateAuthentication 옵션](/aspnet/core/security/authentication/certauth)에 사용자 지정 인증서 유효성 검사 논리를 배치할 수 있습니다.

```csharp
public class Startup
{
    public Startup(IConfiguration configuration)
    {
        Configuration = configuration;
    }

    public IConfiguration Configuration { get; }

    public void ConfigureServices(IServiceCollection services)
    {
        services.AddControllersWithViews();
        // Configure the application to use the protocol and client ip address forwared by the frontend load balancer
        services.Configure<ForwardedHeadersOptions>(options =>
        {
            options.ForwardedHeaders =
                ForwardedHeaders.XForwardedFor | ForwardedHeaders.XForwardedProto;
        });       
        
        // Configure the application to client certificate forwarded the frontend load balancer
        services.AddCertificateForwarding(options => { options.CertificateHeader = "X-ARR-ClientCert"; });

        // Add certificate authentication so when authorization is performed the user will be created from the certificate
        services.AddAuthentication(CertificateAuthenticationDefaults.AuthenticationScheme).AddCertificate();
    }

    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        if (env.IsDevelopment())
        {
            app.UseDeveloperExceptionPage();
        }
        else
        {
            app.UseExceptionHandler("/Home/Error");
            app.UseHsts();
        }
        
        app.UseForwardedHeaders();
        app.UseCertificateForwarding();
        app.UseHttpsRedirection();

        app.UseAuthentication()
        app.UseAuthorization();

        app.UseStaticFiles();

        app.UseRouting();
        
        app.UseEndpoints(endpoints =>
        {
            endpoints.MapControllerRoute(
                name: "default",
                pattern: "{controller=Home}/{action=Index}/{id?}");
        });
    }
}
```

## <a name="aspnet-webforms-sample"></a>ASP.NET WebForms 샘플

```csharp
    using System;
    using System.Collections.Specialized;
    using System.Security.Cryptography.X509Certificates;
    using System.Web;

    namespace ClientCertificateUsageSample
    {
        public partial class Cert : System.Web.UI.Page
        {
            public string certHeader = "";
            public string errorString = "";
            private X509Certificate2 certificate = null;
            public string certThumbprint = "";
            public string certSubject = "";
            public string certIssuer = "";
            public string certSignatureAlg = "";
            public string certIssueDate = "";
            public string certExpiryDate = "";
            public bool isValidCert = false;

            //
            // Read the certificate from the header into an X509Certificate2 object
            // Display properties of the certificate on the page
            //
            protected void Page_Load(object sender, EventArgs e)
            {
                NameValueCollection headers = base.Request.Headers;
                certHeader = headers["X-ARR-ClientCert"];
                if (!String.IsNullOrEmpty(certHeader))
                {
                    try
                    {
                        byte[] clientCertBytes = Convert.FromBase64String(certHeader);
                        certificate = new X509Certificate2(clientCertBytes);
                        certSubject = certificate.Subject;
                        certIssuer = certificate.Issuer;
                        certThumbprint = certificate.Thumbprint;
                        certSignatureAlg = certificate.SignatureAlgorithm.FriendlyName;
                        certIssueDate = certificate.NotBefore.ToShortDateString() + " " + certificate.NotBefore.ToShortTimeString();
                        certExpiryDate = certificate.NotAfter.ToShortDateString() + " " + certificate.NotAfter.ToShortTimeString();
                    }
                    catch (Exception ex)
                    {
                        errorString = ex.ToString();
                    }
                    finally 
                    {
                        isValidCert = IsValidClientCertificate();
                        if (!isValidCert) Response.StatusCode = 403;
                        else Response.StatusCode = 200;
                    }
                }
                else
                {
                    certHeader = "";
                }
            }

            //
            // This is a SAMPLE verification routine. Depending on your application logic and security requirements, 
            // you should modify this method
            //
            private bool IsValidClientCertificate()
            {
                // In this example we will only accept the certificate as a valid certificate if all the conditions below are met:
                // 1. The certificate is not expired and is active for the current time on server.
                // 2. The subject name of the certificate has the common name nildevecc
                // 3. The issuer name of the certificate has the common name nildevecc and organization name Microsoft Corp
                // 4. The thumbprint of the certificate is 30757A2E831977D8BD9C8496E4C99AB26CB9622B
                //
                // This example does NOT test that this certificate is chained to a Trusted Root Authority (or revoked) on the server 
                // and it allows for self signed certificates
                //

                if (certificate == null || !String.IsNullOrEmpty(errorString)) return false;

                // 1. Check time validity of certificate
                if (DateTime.Compare(DateTime.Now, certificate.NotBefore) < 0 || DateTime.Compare(DateTime.Now, certificate.NotAfter) > 0) return false;

                // 2. Check subject name of certificate
                bool foundSubject = false;
                string[] certSubjectData = certificate.Subject.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
                foreach (string s in certSubjectData)
                {
                    if (String.Compare(s.Trim(), "CN=nildevecc") == 0)
                    {
                        foundSubject = true;
                        break;
                    }
                }
                if (!foundSubject) return false;

                // 3. Check issuer name of certificate
                bool foundIssuerCN = false, foundIssuerO = false;
                string[] certIssuerData = certificate.Issuer.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
                foreach (string s in certIssuerData)
                {
                    if (String.Compare(s.Trim(), "CN=nildevecc") == 0)
                    {
                        foundIssuerCN = true;
                        if (foundIssuerO) break;
                    }

                    if (String.Compare(s.Trim(), "O=Microsoft Corp") == 0)
                    {
                        foundIssuerO = true;
                        if (foundIssuerCN) break;
                    }
                }

                if (!foundIssuerCN || !foundIssuerO) return false;

                // 4. Check thumprint of certificate
                if (String.Compare(certificate.Thumbprint.Trim().ToUpper(), "30757A2E831977D8BD9C8496E4C99AB26CB9622B") != 0) return false;

                return true;
            }
        }
    }
```

## <a name="nodejs-sample"></a>Node.js 샘플

다음 Node.js 샘플 코드는 `X-ARR-ClientCert`헤더를 가져오고 [node-forge](https://github.com/digitalbazaar/forge)를 사용하여 기본 64 인코딩 PEM 문자열을 인증서 개체로 변환하고 유효성을 검사합니다.

```javascript
import { NextFunction, Request, Response } from 'express';
import { pki, md, asn1 } from 'node-forge';

export class AuthorizationHandler {
    public static authorizeClientCertificate(req: Request, res: Response, next: NextFunction): void {
        try {
            // Get header
            const header = req.get('X-ARR-ClientCert');
            if (!header) throw new Error('UNAUTHORIZED');

            // Convert from PEM to pki.CERT
            const pem = `-----BEGIN CERTIFICATE-----${header}-----END CERTIFICATE-----`;
            const incomingCert: pki.Certificate = pki.certificateFromPem(pem);

            // Validate certificate thumbprint
            const fingerPrint = md.sha1.create().update(asn1.toDer(pki.certificateToAsn1(incomingCert)).getBytes()).digest().toHex();
            if (fingerPrint.toLowerCase() !== 'abcdef1234567890abcdef1234567890abcdef12') throw new Error('UNAUTHORIZED');

            // Validate time validity
            const currentDate = new Date();
            if (currentDate < incomingCert.validity.notBefore || currentDate > incomingCert.validity.notAfter) throw new Error('UNAUTHORIZED');

            // Validate issuer
            if (incomingCert.issuer.hash.toLowerCase() !== 'abcdef1234567890abcdef1234567890abcdef12') throw new Error('UNAUTHORIZED');

            // Validate subject
            if (incomingCert.subject.hash.toLowerCase() !== 'abcdef1234567890abcdef1234567890abcdef12') throw new Error('UNAUTHORIZED');

            next();
        } catch (e) {
            if (e instanceof Error && e.message === 'UNAUTHORIZED') {
                res.status(401).send();
            } else {
                next(e);
            }
        }
    }
}
```

## <a name="java-sample"></a>Java 샘플

다음 Java 클래스는 인증서를 `X-ARR-ClientCert`에서 `X509Certificate` 인스턴스로 인코딩합니다. `certificateIsValid()`은 인증서의 지문이 생성자에 지정된 지문과 일치하고 인증서가 만료되지 않았는지 확인합니다.


```java
import java.io.ByteArrayInputStream;
import java.security.NoSuchAlgorithmException;
import java.security.cert.*;
import java.security.MessageDigest;

import sun.security.provider.X509Factory;

import javax.xml.bind.DatatypeConverter;
import java.util.Base64;
import java.util.Date;

public class ClientCertValidator { 

    private String thumbprint;
    private X509Certificate certificate;

    /**
     * Constructor.
     * @param certificate The certificate from the "X-ARR-ClientCert" HTTP header
     * @param thumbprint The thumbprint to check against
     * @throws CertificateException If the certificate factory cannot be created.
     */
    public ClientCertValidator(String certificate, String thumbprint) throws CertificateException {
        certificate = certificate
                .replaceAll(X509Factory.BEGIN_CERT, "")
                .replaceAll(X509Factory.END_CERT, "");
        CertificateFactory cf = CertificateFactory.getInstance("X.509");
        byte [] base64Bytes = Base64.getDecoder().decode(certificate);
        X509Certificate X509cert =  (X509Certificate) cf.generateCertificate(new ByteArrayInputStream(base64Bytes));

        this.setCertificate(X509cert);
        this.setThumbprint(thumbprint);
    }

    /**
     * Check that the certificate's thumbprint matches the one given in the constructor, and that the
     * certificate has not expired.
     * @return True if the certificate's thumbprint matches and has not expired. False otherwise.
     */
    public boolean certificateIsValid() throws NoSuchAlgorithmException, CertificateEncodingException {
        return certificateHasNotExpired() && thumbprintIsValid();
    }

    /**
     * Check certificate's timestamp.
     * @return Returns true if the certificate has not expired. Returns false if it has expired.
     */
    private boolean certificateHasNotExpired() {
        Date currentTime = new java.util.Date();
        try {
            this.getCertificate().checkValidity(currentTime);
        } catch (CertificateExpiredException | CertificateNotYetValidException e) {
            return false;
        }
        return true;
    }

    /**
     * Check the certificate's thumbprint matches the given one.
     * @return Returns true if the thumbprints match. False otherwise.
     */
    private boolean thumbprintIsValid() throws NoSuchAlgorithmException, CertificateEncodingException {
        MessageDigest md = MessageDigest.getInstance("SHA-1");
        byte[] der = this.getCertificate().getEncoded();
        md.update(der);
        byte[] digest = md.digest();
        String digestHex = DatatypeConverter.printHexBinary(digest);
        return digestHex.toLowerCase().equals(this.getThumbprint().toLowerCase());
    }

    // Getters and setters

    public void setThumbprint(String thumbprint) {
        this.thumbprint = thumbprint;
    }

    public String getThumbprint() {
        return this.thumbprint;
    }

    public X509Certificate getCertificate() {
        return certificate;
    }

    public void setCertificate(X509Certificate certificate) {
        this.certificate = certificate;
    }
}
```

[exclusion-paths]: ./media/app-service-web-configure-tls-mutual-auth/exclusion-paths.png

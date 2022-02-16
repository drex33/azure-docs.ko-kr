---
title: '빠른 시작: Java 웹앱에 Microsoft로 로그인 추가 | Azure'
titleSuffix: Microsoft identity platform
description: 이 빠른 시작에서는 OpenID Connect를 사용하여 Java 웹 애플리케이션에 Microsoft 로그인을 추가하는 방법을 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: portal
ms.workload: identity
ms.date: 11/22/2021
ROBOTS: NOINDEX
ms.author: marsma
ms.custom: aaddev, "scenarios:getting-started", "languages:Java", devx-track-java, mode-api
ms.openlocfilehash: 800b0770ea57b597a282684e1f52bc55355068fc
ms.sourcegitcommit: 8d56da997b0b3ab07f91f6bef0c5661847758c4a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/05/2022
ms.locfileid: "138036397"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-a-java-web-app"></a>빠른 시작: Java 웹앱에 Microsoft로 로그인 추가


> [!div renderon="docs"]
> 환영합니다! 아마도 기대했던 페이지는 아닐 것입니다. 수정 작업을 진행하는 동안 이 링크를 통해 올바른 문서로 이동해야 합니다.
>
> > [빠른 시작: 사용자 로그인이 포함된 Java 웹앱](web-app-quickstart.md?pivots=devlang-java)
> 
> 이 문제를 해결하는 동안 불편을 끼쳐 드려 죄송하며 양해해 주셔서 감사합니다.

> [!div renderon="portal" class="sxs-lookup"]
> 이 빠른 시작에서는 애플리케이션이 사용자를 로그인하고 Microsoft Graph API를 호출할 수 있는 방법을 보여주는 코드 샘플을 다운로드하고 실행합니다. 모든 Azure AD(Azure Active Directory) 조직의 사용자는 애플리케이션에 로그인할 수 있습니다.
> 
>  개요는 [샘플 작동 방식에 대한 다이어그램](#how-the-sample-works)을 참조하세요.
> 
> ## <a name="prerequisites"></a>사전 요구 사항
> 
> 이 샘플을 실행하려면 다음이 필요합니다.
> 
> - [JDK(Java Development Kit)](https://openjdk.java.net/) 8 이상
> - [Maven](https://maven.apache.org/)
> 
> 
> #### <a name="step-1-configure-your-application-in-the-azure-portal"></a>1단계: Azure Portal에서 애플리케이션 구성
> 
> 이 빠른 시작의 코드 샘플을 사용하려면 다음을 수행합니다.
> 
> 1. 회신 URL `https://localhost:8443/msal4jsample/secure/aad` 및 `https://localhost:8443/msal4jsample/graph/me`를 추가합니다.
> 1. 클라이언트 암호를 만듭니다.
> > [!div class="nextstepaction"]
> > [이러한 변경 내용 적용]()
> 
> > [!div class="alert alert-info"]
> > ![이미 구성됨](media/quickstart-v2-aspnet-webapp/green-check.png) 이러한 특성을 사용하여 애플리케이션을 구성합니다.
> 
> #### <a name="step-2-download-the-code-sample"></a>2단계: 코드 샘플 다운로드
> 
> 프로젝트를 다운로드하고 드라이브 루트 근처의 폴더로 .zip 파일을 추출합니다. 예를 들면 *C:\Azure-Samples* 입니다.
> 
> localhost와 함께 HTTPS를 사용하려면 `server.ssl.key` 속성을 제공합니다. 자체 서명된 인증서를 생성하려면 JRE에 포함된 keytool 유틸리티를 사용합니다.
> 
> 예를 들면 다음과 같습니다.
> ```
>   keytool -genkeypair -alias testCert -keyalg RSA -storetype PKCS12 -keystore keystore.p12 -storepass password
> 
>   server.ssl.key-store-type=PKCS12
>   server.ssl.key-store=classpath:keystore.p12
>   server.ssl.key-store-password=password
>   server.ssl.key-alias=testCert
>   ```
>   생성된 키 저장소 파일을 *resources* 폴더에 배치합니다.
> 
> > [!div class="sxs-lookup nextstepaction"]
> > [코드 샘플 다운로드](https://github.com/Azure-Samples/ms-identity-java-webapp/archive/master.zip)
> 
> > [!div class="sxs-lookup"]
> > > [!NOTE]
> > > `Enter_the_Supported_Account_Info_Here`
> 
> > [!div class="sxs-lookup"]
> 
> #### <a name="step-3-run-the-code-sample"></a>3단계: 코드 샘플 실행
> 
> 프로젝트를 실행하려면 다음 단계 중 하나를 수행합니다.
> 
> - 포함된 Spring Boot 서버를 사용하여 IDE에서 직접 실행합니다.
> - [Maven](https://maven.apache.org/plugins/maven-war-plugin/usage.html)을 사용하여 WAR 파일에 패키징한 다음, [Apache Tomcat](http://tomcat.apache.org/)과 같은 J2EE 컨테이너 솔루션에 배포합니다.
> 
> ##### <a name="running-the-project-from-an-ide"></a>IDE에서 프로젝트 실행
> 
> IDE에서 웹 애플리케이션을 실행하려면 실행을 선택한 다음, 프로젝트의 홈페이지로 이동합니다. 이 샘플의 경우 표준 홈 페이지 URL은 https://localhost:8443 입니다.
> 
> 1. 전면 페이지에서 **로그인** 단추를 선택하여 사용자를 Azure Active Directory로 리디렉션하고 자격 증명을 입력하라는 메시지를 표시합니다.
> 
> 1. 사용자가 인증되면 `https://localhost:8443/msal4jsample/secure/aad`로 리디렉션됩니다. 이제 사용자가 로그인되었으며, 사용자 계정에 대한 정보가 페이지에 표시됩니다. 샘플 UI에는 다음과 같은 단추가 있습니다.
>     - **로그아웃**: 애플리케이션에서 현재 사용자를 로그아웃하고 홈페이지로 리디렉션합니다.
>     - **사용자 정보 표시**: Microsoft Graph에 대한 토큰을 획득하고, 로그인한 사용자에 대한 기본 정보를 반환하는 토큰을 포함하는 요청을 사용하여 Microsoft Graph를 호출합니다.
> 
> ##### <a name="running-the-project-from-tomcat"></a>Tomcat에서 프로젝트 실행
> 
> 웹 샘플을 Tomcat에 배포하려면 소스 코드를 몇 가지 변경합니다.
> 
> 1. *ms-identity-java-webapp/src/main/java/com.microsoft.azure.msalwebsample/MsalWebSampleApplication* 을 엽니다.
> 
>     - 모든 소스 코드를 삭제하고 다음 코드로 바꿉니다.
> 
>       ```Java
>        package com.microsoft.azure.msalwebsample;
> 
>        import org.springframework.boot.SpringApplication;
>        import org.springframework.boot.autoconfigure.SpringBootApplication;
>        import org.springframework.boot.builder.SpringApplicationBuilder;
>        import org.springframework.boot.web.servlet.support.SpringBootServletInitializer;
> 
>        @SpringBootApplication
>        public class MsalWebSampleApplication extends SpringBootServletInitializer {
> 
>         public static void main(String[] args) {
>          SpringApplication.run(MsalWebSampleApplication.class, args);
>         }
> 
>         @Override
>         protected SpringApplicationBuilder configure(SpringApplicationBuilder builder) {
>          return builder.sources(MsalWebSampleApplication.class);
>         }
>        }
>       ```
> 
> 2.   Tomcat의 기본 HTTP 포트는 8080이지만 포트 8443을 통한 HTTPS 연결이 필요합니다. 이 설정을 구성하려면 다음을 수행합니다.
>         - *tomcat/conf/server.xml* 로 이동합니다.
>         - `<connector>` 태그를 검색하고 기존 커넥터를 다음 커넥터로 바꿉니다.
> 
>           ```xml
>           <Connector
>                    protocol="org.apache.coyote.http11.Http11NioProtocol"
>                    port="8443" maxThreads="200"
>                    scheme="https" secure="true" SSLEnabled="true"
>                    keystoreFile="C:/Path/To/Keystore/File/keystore.p12" keystorePass="KeystorePassword"
>                    clientAuth="false" sslProtocol="TLS"/>
>           ```
> 
> 3. 명령 프롬프트 창을 엽니다. 이 샘플의 루트 폴더(pom.xml 파일이 있는 위치)로 이동하고 `mvn > package`를 실행하여 프로젝트를 빌드합니다.
>     - 이 명령은 */targets* 디렉터리에 *msal-web-sample-0.1.0.war* 파일을 생성합니다.
>     - 이 파일의 이름을 *msal4jsample.war* 로 바꿉니다.
>     - Tomcat 또는 기타 J2EE 컨테이너 솔루션을 사용하여 WAR 파일을 배포합니다.
>         - msal4jsample.war 파일을 배포하려면 Tomcat 설치의 */webapps/* 디렉터리에 복사한 다음, Tomcat 서버를 시작합니다.
> 
> 4. 파일이 배포된 후 브라우저를 사용하여 https://localhost:8443/msal4jsample 로 이동합니다.
> 
> > [!IMPORTANT]
> > 이 빠른 시작 애플리케이션에서는 클라이언트 암호를 사용하여 자체를 기밀 클라이언트로 식별합니다. 클라이언트 암호는 보안상의 이유로 프로젝트 파일에 일반 텍스트로 추가되므로, 이 애플리케이션을 프로덕션 환경으로 사용하기 전에 클라이언트 암호 대신 인증서를 사용하는 것이 좋습니다. 인증서를 사용하는 방법에 대한 자세한 내용은 [애플리케이션 인증을 위한 인증서 자격 증명](./active-directory-certificate-credentials.md)을 참조하세요.
> 
> ## <a name="more-information"></a>자세한 정보
> 
> ### <a name="how-the-sample-works"></a>샘플 작동 방법
> ![이 빠른 시작에서 생성된 샘플 앱의 작동 방식을 보여 주는 다이어그램](media/quickstart-v2-java-webapp/java-quickstart.svg)
> 
> ### <a name="get-msal"></a>MSAL 가져오기
> 
> Java용 MSAL(MSAL4J)은 사용자를 로그인시키고 Microsoft ID 플랫폼으로 보호되는 API 액세스에 사용되는 토큰을 요청할 때 사용되는 Java 라이브러리입니다.
> 
> Maven이나 Gradle을 사용하여 MSAL4J를 애플리케이션에 추가하고 > 애플리케이션의 pom.xml(Maven) 또는 build.gradle(Gradle) 파일을 다음과 같이 변경하여 종속성을 관리합니다.
> 
> pom.xml에서:
> 
> ```xml
> <dependency>
>     <groupId>com.microsoft.azure</groupId>
>     <artifactId>msal4j</artifactId>
>     <version>1.0.0</version>
> </dependency>
> ```
> 
> build.gradle에서:
> 
> ```$xslt
> compile group: 'com.microsoft.azure', name: 'msal4j', version: '1.0.0'
> ```
> 
> ### <a name="initialize-msal"></a>MSAL 초기화
> 
> MSAL4J를 사용할 파일의 시작 부분에 다음 코드를 추가하여 Java용 MSAL에 대한 참조를 추가합니다.
> 
> ```Java
> import com.microsoft.aad.msal4j.*;
> ```
> 
> [!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]
> 
> ## <a name="next-steps"></a>다음 단계
> 
> Microsoft ID 플랫폼에서 사용자를 로그인시키는 웹앱을 빌드하는 방법에 대한 자세한 내용은 다중 파트 시나리오 시리즈를 참조하세요.
> 
> > [!div class="nextstepaction"]
> > [시나리오: 사용자를 로그인하는 웹앱](scenario-web-app-sign-user-overview.md?tabs=java)

---
title: '자습서: 기존 사용자 지정 DNS 이름 매핑'
description: Azure App Service의 웹앱, 모바일 앱 백 엔드 또는 API 앱에 기존 사용자 지정 DNS 도메인 이름(베니티 도메인)을 추가하는 방법을 알아봅니다.
keywords: App Service, Azure App Service, 도메인 매핑, 도메인 이름, 기존 도메인, 호스트 이름, 사용자 지정 도메인
ms.assetid: dc446e0e-0958-48ea-8d99-441d2b947a7c
ms.topic: tutorial
ms.date: 05/27/2021
ms.custom: mvc, seodec18, devx-track-azurepowershell
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./app-service-web-tutorial-custom-domain-uiex
ms.openlocfilehash: 35d2a35475c6a22608c28f3ffdf3dc1abc3065a1
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128657408"
---
# <a name="tutorial-map-an-existing-custom-dns-name-to-azure-app-service"></a>자습서: Azure App Service에 기존 사용자 지정 DNS 이름 매핑

[Azure App Service](overview.md)는 확장성 높은 자체 패치 웹 호스팅 서비스를 제공합니다. 이 자습서에서는 기존 사용자 지정 DNS(Domain Name System) 이름을 App Service에 매핑하는 방법을 보여 줍니다.

이 자습서에서는 다음과 같은 작업을 수행하는 방법을 살펴봅니다.

> [!div class="checklist"]
> * [CNAME 레코드](https://en.wikipedia.org/wiki/CNAME_record)를 사용하여 하위 도메인 매핑
> * [A 레코드](https://en.wikipedia.org/wiki/List_of_DNS_record_types#A)를 사용하여 루트 도메인 매핑
> * CNAME 레코드를 사용하여 [와일드카드 도메인](https://en.wikipedia.org/wiki/Wildcard_DNS_record) 매핑
> * 사용자 지정 디렉터리로 기본 URL 리디렉션


## <a name="1-prepare-your-environment"></a>1. 환경 준비

* [App Service 앱을 만들거나](./index.yml) 다른 자습서에서 만든 앱을 사용합니다.
* 사용자 지정 도메인의 DNS 레코드를 편집할 수 있는지 확인합니다. 사용자 지정 도메인이 아직 없는 경우 [App Service 도메인을 구입](manage-custom-dns-buy-domain.md)할 수 있습니다.

    > [!NOTE]
    > DNS 레코드를 편집하려면 GoDaddy와 같은 도메인 공급자의 DNS 레지스트리에 대한 액세스 권한이 필요합니다. 예를 들어 `contoso.com` 및 `www.contoso.com`에 대한 DNS 항목을 추가하려면 `contoso.com` 루트 도메인에 대한 DNS 설정을 구성할 수 있어야 합니다.


## <a name="2-prepare-the-app"></a>2. 앱 준비

사용자 지정 DNS 이름을 웹앱에 매핑하려면 웹앱의 [App Service 요금제](overview-hosting-plans.md)가 유료 계층이어야 합니다(**체험(F1)** 계층이 아님).

#### <a name="sign-in-to-azure"></a>Azure에 로그인

[Azure Portal](https://portal.azure.com)에서 Azure 계정으로 로그인합니다.

#### <a name="select-the-app-in-the-azure-portal"></a>Azure Portal에서 앱 선택

1. **App Services** 를 검색하여 선택합니다.

   ![App Services 선택을 보여 주는 스크린샷](./media/app-service-web-tutorial-custom-domain/app-services.png)

1. **App Service** 페이지에서 Azure 앱의 이름을 선택합니다.

   ![Azure 앱에 대한 포털 탐색을 보여 주는 스크린샷](./media/app-service-web-tutorial-custom-domain/select-app.png)

    App Service 앱의 관리 페이지가 표시됩니다.

<a name="checkpricing" aria-hidden="true"></a>

#### <a name="check-the-pricing-tier"></a>가격 책정 계층 확인

1. 앱 페이지의 왼쪽 창에서 **설정** 섹션으로 스크롤하고 **강화(App Service 요금제)** 를 선택합니다.

   ![스케일 업(App Service 요금제) 메뉴를 보여 주는 스크린샷](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

1. 앱의 현재 계층은 파란색 테두리로 강조 표시됩니다. 앱이 **F1** 계층에 속해 있지 않은지 확인합니다. 사용자 지정 DNS는 **F1** 계층에서는 지원되지 않습니다.

   ![권장 가격 책정 계층을 보여 주는 스크린샷](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

1. App Service 요금제가 **F1** 계층이 아닌 경우 **스케일 업** 페이지를 닫고 [3. 도메인 확인 ID 가져오기](#3-get-a-domain-verification-id)로 건너뜁니다.

<a name="scaleup" aria-hidden="true"></a>

#### <a name="scale-up-the-app-service-plan"></a>강화 - App Service 계획

1. 유료 계층(**D1**, **B1**, **B2**, **B3** 또는 **프로덕션** 범주의 모든 계층) 중 하나를 선택합니다. 추가 옵션을 보려면 **추가 옵션 보기** 를 선택합니다.

1. **적용** 을 선택합니다.

   ![가격 책정 계층 확인을 보여 주는 스크린샷](./media/app-service-web-tutorial-custom-domain/choose-pricing-tier.png)

   다음 알림이 표시되면 강화 작업이 완료됩니다.

   ![크기 조정 작업 확인을 보여 주는 스크린샷](./media/app-service-web-tutorial-custom-domain/scale-notification.png)


## <a name="3-get-a-domain-verification-id"></a>3. 도메인 확인 ID 가져오기

앱에 사용자 지정 도메인을 추가하려면 도메인 공급자를 사용하여 확인 ID를 TXT 레코드로 추가하여 도메인 소유권을 확인해야 합니다. 

1. 앱 페이지의 왼쪽 창에서 **사용자 지정 도메인** 을 선택합니다. 
1. 다음 단계에 대한 **사용자 지정 도메인** 페이지에서 **사용자 지정 도메인 확인 ID** 상자에서 ID를 복사합니다.

    ![사용자 지정 도메인 확인 ID 상자에 있는 ID를 보여 주는 스크린샷](./media/app-service-web-tutorial-custom-domain/get-custom-domain-verification-id.png)

    > [!WARNING]
    > 도메인 확인 ID를 사용자 지정 도메인에 추가하면 현수 DNS 항목을 방지하고 하위 도메인 인수를 피할 수 있습니다. 이 확인 ID 없이 이전에 구성한 사용자 지정 도메인의 경우 DNS 레코드에 확인 ID를 추가하여 동일한 위험으로부터 보호해야 합니다. 이 일반적인 높은 심각도 위협에 대한 자세한 내용은 [하위 도메인 인수](../security/fundamentals/subdomain-takeover.md)을 참조하세요.
    
<a name="info"></a>

3. **(A 레코드만 해당)** [A 레코드](https://en.wikipedia.org/wiki/List_of_DNS_record_types#A)를 매핑하려면 앱의 외부 IP 주소가 필요합니다. **사용자 지정 도메인** 페이지에서 **IP 주소** 값을 복사합니다.

   ![Azure 앱에 대한 포털 탐색을 보여 주는 스크린샷](./media/app-service-web-tutorial-custom-domain/mapping-information.png)


## <a name="4-create-the-dns-records"></a>4. DNS 레코드 만들기

1. 도메인 공급자의 웹 사이트에 로그인합니다.

    > [!NOTE]
    > 원한다면 Azure DNS를 사용하여 도메인의 DNS 레코드를 관리하고 Azure App Service의 사용자 지정 DNS 이름을 구성할 수 있습니다. 자세한 내용은 [자습서: Azure DNS에서 도메인 호스트](../dns/dns-delegate-domain-azure-dns.md)를 참조하세요.

1. DNS 레코드를 관리하기 위한 페이지를 찾습니다. 

    > [!NOTE]
    > 각 도메인 공급자마다 고유한 DNS 레코드 인터페이스가 있으므로 공급자의 설명서를 참조하세요. **도메인 이름**, **DNS** 또는 **이름 서버 관리** 라는 레이블이 지정된 사이트 영역을 찾습니다.
    >
    > 종종 계정 정보를 확인한 다음, **내 도메인** 과 같은 링크를 검색하여 DNS 레코드 페이지를 찾을 수 있습니다. 해당 페이지로 이동한 다음, **영역 파일**, **DNS 레코드** 또는 **고급 구성** 과 같이 이름이 지정된 링크를 찾습니다.

   다음 스크린샷은 DNS 레코드 페이지의 예입니다.

   ![예제 DNS 레코드 페이지를 보여주는 스크린샷.](../../includes/media/app-service-web-access-dns-records-no-h/example-record-ui.png)

1. **추가** 또는 적절한 위젯을 선택하여 레코드를 만듭니다. 

1. 만들 레코드 유형을 선택하고 지침을 따릅니다. [CNAME 레코드](https://en.wikipedia.org/wiki/CNAME_record) 또는 [A 레코드](https://en.wikipedia.org/wiki/List_of_DNS_record_types#A)를 사용하여 사용자 지정 DNS 이름을 App Service에 매핑할 수 있습니다.

    > [!NOTE]
    > **선택할 레코드**
    > 
    > * 루트 도메인(예: `contoso.com`)을 매핑하려면 [A 레코드](https://en.wikipedia.org/wiki/List_of_DNS_record_types#A)를 사용합니다. 루트 레코드에는 CNAME 레코드를 사용하지 마세요(자세한 내용은 [RFC 1912 섹션 2.4](https://datatracker.ietf.org/doc/html/rfc1912#section-2.4) 참조).
    > * 하위 도메인(예: `www.contoso.com`)을 매핑하려면 [CNAME 레코드](https://en.wikipedia.org/wiki/CNAME_record)를 사용합니다.
    > * A 레코드를 사용하여 하위 도메인을 앱의 IP 주소로 직접 매핑해도 되지만 [IP 주소가 변경](overview-inbound-outbound-ips.md#when-inbound-ip-changes)될 가능성이 있습니다. CNAME은 앱의 호스트 이름에 대신 매핑되며, 변경 가능성이 낮습니다.
    > * [와일드카드 도메인](https://en.wikipedia.org/wiki/Wildcard_DNS_record)(예: `*.contoso.com`)을 매핑하려면 CNAME 레코드를 사용합니다.
    
# <a name="cname"></a>[CNAME](#tab/cname)

`www.contoso.com`의 `www` 같은 하위 도메인의 경우 다음 표에 따라 두 개의 레코드를 만듭니다.

| 레코드 형식 | 호스트 | 값 | 주석 |
| - | - | - |
| CNAME | `<subdomain>`(예: `www`) | `<app-name>.azurewebsites.net` | 도메인 자체 매핑. |
| TXT | `asuid.<subdomain>`(예: `asuid.www`) | [이전에 가져온 확인 ID](#3-get-a-domain-verification-id) | App Service는 `asuid.<subdomain>` TXT 레코드에 액세스하여 사용자 지정 도메인의 소유권을 확인합니다. |

![Azure 앱에 대한 포털 탐색을 보여 주는 스크린샷](./media/app-service-web-tutorial-custom-domain/cname-record.png)
    
# <a name="a"></a>[A](#tab/a)

- `contoso.com` 같은 루트 도메인의 경우 다음 표에 따라 두 개의 레코드를 만듭니다.

    | 레코드 형식 | 호스트 | 값 | 주석 |
    | - | - | - |
    | A | `@` | [앱의 IP 주소 복사](#3-get-a-domain-verification-id)에서 가져온 IP 주소 | 도메인 자체 매핑(`@`는 일반적으로 루트 도메인을 나타냄). |
    | TXT | `asuid` | [이전에 가져온 확인 ID](#3-get-a-domain-verification-id) | App Service는 `asuid.<subdomain>` TXT 레코드에 액세스하여 사용자 지정 도메인의 소유권을 확인합니다. 루트 도메인의 경우 `asuid`를 사용합니다. |
    
    ![DNS 레코드 페이지를 보여주는 스크린샷](./media/app-service-web-tutorial-custom-domain/a-record.png)
    
- 권장되는 CNAME 레코드 대신 A 레코드를 사용하여 `www.contoso.com`과 같은 하위 도메인을 매핑하려면 A 레코드와 TXT 레코드가 대신 다음 표와 같이 표시되어야 합니다.

    |레코드 형식|호스트|값|
    |--- |--- |--- |
    |A|\<subdomain\>(예: www)|앱의 IP 주소 복사에서 가져온 IP 주소|
    |TXT|asuid.\<subdomain\> (예: asuid.www)|이전에 가져온 확인 ID|
    
# <a name="wildcard-cname"></a>[와일드 카드(CNAME)](#tab/wildcard)

`*.contoso.com`의 `*` 같은 와일드 카드 이름의 경우 다음 표에 따라 두 개의 레코드를 만듭니다.

| 레코드 형식 | 호스트 | 값 | 주석 |
| - | - | - |
| CNAME | `*` | `<app-name>.azurewebsites.net` | 도메인 자체 매핑. |
| TXT | `asuid` | [이전에 가져온 확인 ID](#3-get-a-domain-verification-id) | App Service는 `asuid` TXT 레코드에 액세스하여 사용자 지정 도메인의 소유권을 확인합니다. |

![Azure 앱에 대한 탐색을 보여 주는 스크린샷](./media/app-service-web-tutorial-custom-domain/cname-record-wildcard.png)
    
-----

> [!NOTE]
> GoDaddy와 같은 특정 공급자의 경우 먼저 별도의 **변경 내용 저장** 링크를 클릭해야 DNS 레코드의 변경 내용이 적용됩니다.

<a name="a" aria-hidden="true"></a>

<a name="enable-a" aria-hidden="true"></a>

<a name="wildcard" aria-hidden="true"></a>

<a name="cname" aria-hidden="true"></a>

## <a name="5-enable-the-mapping-in-your-app"></a>5. 앱에서 매핑 사용

1. Azure Portal의 앱 페이지 왼쪽 창에서 **사용자 지정 도메인** 을 선택합니다.

    ![사용자 지정 도메인 메뉴를 보여 주는 스크린샷](./media/app-service-web-tutorial-custom-domain/custom-domain-menu.png)

1. **사용자 지정 도메인 추가** 를 선택합니다.

    ![호스트 이름 추가 항목을 보여주는 스크린샷](./media/app-service-web-tutorial-custom-domain/add-host-name-cname.png)

# <a name="cname"></a>[CNAME](#tab/cname)

3. `www.contoso.com`과 같이 CNAME 레코드를 추가한 정규화된 도메인 이름을 입력합니다.

1. **유효성 검사** 를 선택합니다. **사용자 지정 도메인 추가** 페이지가 나타납니다.

1. **호스트 이름 레코드 형식** 이 **CNAME(www\.example.com 또는 하위 도메인)** 으로 설정되어 있는지 확인합니다. **사용자 지정 도메인 추가** 를 선택합니다.

    ![사용자 지정 도메인 추가 단추를 보여 주는 스크린샷](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname.png)

    새 사용자 지정 도메인이 앱의 **사용자 지정 도메인** 페이지에 반영될 때까지 약간의 시간이 걸릴 수 있습니다. 데이터를 업데이트하려면 브라우저를 새로 고칩니다.

    ![CNAME 레코드 추가를 보여 주는 스크린샷](./media/app-service-web-tutorial-custom-domain/cname-record-added.png)

    > [!NOTE]
    > 사용자 지정 도메인에 대한 경고 레이블은 아직 TLS/SSL 인증서에 바인딩되지 않았다는 뜻입니다. 브라우저에서 사용자 지정 도메인으로 전송되는 HTTPS 요청은 브라우저에 따라 오류 또는 경고를 수신합니다. TLS 바인딩을 추가하려면 [Azure App Service에서 TLS/SSL 바인딩으로 사용자 지정 DNS 이름 보호](configure-ssl-bindings.md)를 참조하세요.

    이전에 단계를 잊었거나 철자를 잘못 입력한 경우에는 페이지 아래쪽에 확인 오류가 표시됩니다.

    ![확인 오류를 보여 주는 스크린샷](./media/app-service-web-tutorial-custom-domain/verification-error-cname.png)

# <a name="a"></a>[A](#tab/a)

3. `contoso.com`과 같이 A 레코드를 구성한 정규화된 도메인 이름을 입력합니다. 

1. **유효성 검사** 를 선택합니다. **사용자 지정 도메인 추가** 페이지가 표시됩니다.

1. **호스트 이름 레코드 형식** 이 **A 레코드(example.com)** 로 설정되어 있는지 확인합니다. **사용자 지정 도메인 추가** 를 선택합니다.

    ![앱에 DNS 이름 추가를 보여 주는 스크린샷](./media/app-service-web-tutorial-custom-domain/validate-domain-name.png)

    새 사용자 지정 도메인이 앱의 **사용자 지정 도메인** 페이지에 반영될 때까지 약간의 시간이 걸릴 수 있습니다. 데이터를 업데이트하려면 브라우저를 새로 고칩니다.

    ![A 레코드 추가를 보여 주는 스크린샷](./media/app-service-web-tutorial-custom-domain/a-record-added.png)

    > [!NOTE]
    > 사용자 지정 도메인에 대한 경고 레이블은 아직 TLS/SSL 인증서에 바인딩되지 않았다는 뜻입니다. 브라우저에서 사용자 지정 도메인으로 전송되는 HTTPS 요청은 브라우저에 따라 오류 또는 경고를 수신합니다. TLS 바인딩을 추가하려면 [Azure App Service에서 TLS/SSL 바인딩으로 사용자 지정 DNS 이름 보호](configure-ssl-bindings.md)를 참조하세요.
    
    이전에 단계를 잊었거나 철자를 잘못 입력한 경우에는 페이지 아래쪽에 확인 오류가 표시됩니다.
    
    ![확인 오류를 보여 주는 스크린샷](./media/app-service-web-tutorial-custom-domain/verification-error.png)
    
# <a name="wildcard-cname"></a>[와일드 카드(CNAME)](#tab/wildcard)

3. 와일드카드 도메인과 일치하는 정규화된 도메인 이름을 입력합니다. 예를 들어 `*.contoso.com`에는 `sub1.contoso.com`, `sub2.contoso.com`, `*.contoso.com` 또는 와일드 카드 패턴과 일치하는 다른 문자열을 사용할 수 있습니다. 그런 다음, **유효성 검사** 를 선택합니다.

    **사용자 지정 도메인 추가** 단추가 활성화됩니다.

1. **호스트 이름 레코드 형식** 이 **CNAME 레코드(www\.example.com or any subdomain)** 로 설정되어 있는지 확인합니다. **사용자 지정 도메인 추가** 를 선택합니다.

    ![앱에 DNS 이름 추가를 보여 주는 스크린샷](./media/app-service-web-tutorial-custom-domain/validate-domain-name-cname-wildcard.png)

    새 사용자 지정 도메인이 앱의 **사용자 지정 도메인** 페이지에 반영될 때까지 약간의 시간이 걸릴 수 있습니다. 데이터를 업데이트하려면 브라우저를 새로 고칩니다.

    > [!NOTE]
    > 사용자 지정 도메인에 대한 경고 레이블은 아직 TLS/SSL 인증서에 바인딩되지 않았다는 뜻입니다. 브라우저에서 사용자 지정 도메인으로 전송되는 HTTPS 요청은 브라우저에 따라 오류 또는 경고를 수신합니다. TLS 바인딩을 추가하려면 [Azure App Service에서 TLS/SSL 바인딩으로 사용자 지정 DNS 이름 보호](configure-ssl-bindings.md)를 참조하세요.

-----


## <a name="6-test-in-a-browser"></a>6. 브라우저에서 테스트

앞에서 구성한 DNS 이름을 찾습니다.

![Azure 앱에 대한 탐색을 보여 주는 스크린샷](./media/app-service-web-tutorial-custom-domain/app-with-custom-dns.png)

<a name="resolve-404-not-found" aria-hidden="true"></a>

사용자 지정 도메인의 URL로 이동할 때 HTTP 404(찾을 수 없음) 오류가 발생하는 경우 가장 일반적인 두 가지 원인은 다음과 같습니다.

* 구성된 사용자 지정 도메인에 A 레코드 또는 CNAME 레코드가 없습니다. 앱에서 매핑이 사용하도록 설정되면 DNS 레코드를 삭제했을 수 있습니다. <a href="https://www.nslookup.io/">온라인 DNS 조회</a> 도구를 사용하여 DNS 레코드가 올바르게 구성되었는지 확인합니다.
* 브라우저 클라이언트가 도메인의 이전 IP 주소를 캐시했습니다. 캐시 및 테스트 DNS 확인을 다시 지웁니다. Windows 컴퓨터에서 캐시를 `ipconfig /flushdns`로 지웁니다.


## <a name="migrate-an-active-domain"></a>활성 도메인 마이그레이션

라이브 사이트 및 해당 DNS 도메인 이름을 가동 중지 시간 없이 App Service로 마이그레이션하려면 [활성 DNS 이름을 Azure App Service로 마이그레이션](manage-custom-dns-migrate-domain.md)을 참조하세요.

<a name="virtualdir" aria-hidden="true"></a>

## <a name="redirect-to-a-custom-directory"></a>사용자 지정 디렉터리로 리디렉션

> [!NOTE]
> App Service는 기본적으로 웹 요청을 앱 코드의 루트 디렉터리로 보냅니다. 그러나 특정 웹 프레임워크는 루트 디렉터리에서 시작하지 않습니다. 예를 들어 [Laravel](https://laravel.com/)은 `public` 하위 디렉터리에서 시작합니다. `contoso.com` DNS 예제를 계속하려면 `http://contoso.com/public`에서 이러한 앱에 액세스할 수 있지만, 일반적으로 `http://contoso.com`을 `public` 디렉터리에 전달하려고 합니다.

이는 일반적인 시나리오이지만 사용자 지정 DNS 매핑을 실제로 포함하지 않는 대신 앱 내에서 가상 디렉터리를 사용자 지정하는 것입니다.

1. 웹앱 페이지의 왼쪽 창에서 **애플리케이션 설정** 을 선택합니다.

1. 페이지의 하단에서 기본적으로 루트 가상 디렉터리`/`는 앱 코드의 루트 디렉터리인 `site\wwwroot`를 가리킵니다. 예를 들어 대신 `site\wwwroot\public`을 가리키도록 변경하고 변경 내용을 저장합니다.

    ![가상 디렉터리 사용자 지정을 보여 주는 스크린샷](./media/app-service-web-tutorial-custom-domain/customize-virtual-directory.png)

1. 작업이 완료되면 브라우저에서 앱의 루트 경로(예: `http://contoso.com` 또는 `http://<app-name>.azurewebsites.net`)로 이동하여 확인합니다.


## <a name="automate-with-scripts"></a>스크립트를 사용하여 자동화

[Azure CLI](/cli/azure/install-azure-cli) 또는 [Azure PowerShell](/powershell/azure/)을 사용하여 스크립트를 통해 사용자 지정 도메인의 관리를 자동화할 수 있습니다.

#### <a name="azure-cli"></a>Azure CLI

다음 명령은 구성된 사용자 지정 DNS 이름을 App Service 앱에 추가합니다.

```azurecli 
az webapp config hostname add \
    --webapp-name <app-name> \
    --resource-group <resource_group_name> \
    --hostname <fully_qualified_domain_name>
``` 

자세한 내용은 [웹앱에 사용자 지정 도메인 매핑](scripts/cli-configure-custom-domain.md)을 참조하세요.

#### <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

다음 명령은 구성된 사용자 지정 DNS 이름을 App Service 앱에 추가합니다.

```powershell  
Set-AzWebApp `
    -Name <app-name> `
    -ResourceGroupName <resource_group_name> ` 
    -HostNames @("<fully_qualified_domain_name>","<app-name>.azurewebsites.net")
```

자세한 내용은 [Web App에 사용자 지정 도메인 할당](scripts/powershell-configure-custom-domain.md)을 참조하세요.


## <a name="next-steps"></a>다음 단계

다음 자습서로 이동하여 사용자 지정 TLS/SSL 인증서를 웹앱에 바인딩하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [Azure App Service에서 TLS/SSL 바인딩으로 사용자 지정 DNS 이름 보호](configure-ssl-bindings.md)

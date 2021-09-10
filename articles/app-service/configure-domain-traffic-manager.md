---
title: Traffic Manager를 사용한 DNS 이름 구성
description: 부하 분산을 위해 Traffic Manager와 통합하는 Azure App Service 앱에 대해 사용자 지정 도메인을 구성하는 방법을 알아봅니다.
ms.assetid: 0f96c0e7-0901-489b-a95a-e3b66ca0a1c2
ms.topic: article
ms.date: 03/05/2020
ms.custom: seodec18
ms.openlocfilehash: c75848cf173254a43e294a01b9b81f1122d85b36
ms.sourcegitcommit: 16580bb4fbd8f68d14db0387a3eee1de85144367
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/24/2021
ms.locfileid: "112678733"
---
# <a name="configure-a-custom-domain-name-in-azure-app-service-with-traffic-manager-integration"></a>Traffic Manager 통합을 사용하여 Azure App Service에서 사용자 지정 도메인 이름 구성

[!INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

> [!NOTE]
> Cloud Services의 경우 [Azure 클라우드 서비스에 대한 사용자 지정 도메인 이름 구성](../cloud-services/cloud-services-custom-domain-name-portal.md)을 참조하세요.

[Azure Traffic Manager](../traffic-manager/index.yml)을 사용하여 [Azure App Service](overview.md)에 대한 트래픽 부하를 분산하는 경우 **\<traffic-manager-endpoint>.trafficmanager.net** 를 사용하여 App Service 앱에 액세스할 수 있습니다. 사용자가 더욱 쉽게 도메인 이름을 알 수 있도록 www\.contoso.com과 같은 사용자 지정 도메인 이름을 App Service 앱으로 할당할 수도 있습니다.

이 문서에서는 [Traffic Manager](../traffic-manager/traffic-manager-overview.md)와 통합된 App Service 앱을 사용하여 사용자 지정 도메인 이름을 구성하는 방법을 보여줍니다.

> [!NOTE]
> Traffic Manager 엔드포인트를 사용하여 도메인 이름을 구성하는 경우에는 [CNAME](https://en.wikipedia.org/wiki/CNAME_record) 레코드만 지원됩니다. 레코드가 지원되지 않으므로 contoso.com과 같은 루트 도메인 매핑도 지원되지 않습니다.
> 

## <a name="prepare-the-app"></a>앱 준비

Azure Traffic Manager와 통합된 앱에 사용자 지정 DNS 이름을 매핑하려면 웹앱의 [App Service 요금제](https://azure.microsoft.com/pricing/details/app-service/)가 **표준** 계층 이상이어야 합니다. 이 단계에서는 App Service 앱이 지원되는 가격 책정 계층에 속하는지 확인해야 합니다.

### <a name="check-the-pricing-tier"></a>가격 책정 계층 확인

[Azure Portal](https://portal.azure.com)에서 **App Services** 를 검색하여 선택합니다.

**App Service** 페이지에서 Azure 앱의 이름을 선택합니다.

![Azure 앱에 대한 포털 탐색](./media/app-service-web-tutorial-custom-domain/select-app.png)

앱 페이지의 왼쪽 탐색 영역에서 **강화(App Service 요금제)** 를 선택합니다.

![강화 메뉴](./media/app-service-web-tutorial-custom-domain/scale-up-menu.png)

앱의 현재 계층은 파란색 테두리로 강조 표시됩니다. 앱이 **표준** 계층 이상(**프로덕션** 또는 **격리** 범주의 모든 계층)에 있는지 확인합니다. 그렇다면 **확장** 페이지를 닫고 [CNAME 매핑 만들기](#create-the-cname-mapping)로 건너뜁니다.

![가격 책정 계층 확인](./media/app-service-web-tutorial-custom-domain/check-pricing-tier.png)

### <a name="scale-up-the-app-service-plan"></a>강화 - App Service 계획

앱을 확장해야 하는 경우 **프로덕션** 범주의 가격 책정 계층을 선택합니다. 추가 옵션을 보려면 **추가 옵션 보기** 를 클릭합니다.

**적용** 을 클릭합니다.

## <a name="create-traffic-manager-endpoint"></a>Traffic Manager 엔드포인트 만들기

[엔드포인트 추가 또는 삭제](../traffic-manager/traffic-manager-manage-endpoints.md)의 단계에 따라 Traffic Manager 프로필에서 App Service 앱을 엔드포인트로 추가합니다.

App Service 앱이 지원되는 가격 책정 계층에 있으면 엔드포인트를 추가할 때 사용 가능한 App Service 대상 목록에 표시됩니다. 앱이 목록에 없는 경우 [앱의 가격 책정 계층을 확인](#prepare-the-app)합니다.

## <a name="create-the-cname-mapping"></a>CNAME 매핑 만들기
> [!NOTE]
> [구매한 App Service 도메인](manage-custom-dns-buy-domain.md)을 구성하려면 이 섹션을 건너뛰고 [사용자 지정 도메인 사용](#enable-custom-domain)으로 이동합니다.
> 

[!INCLUDE [Access DNS records with domain provider](../../includes/app-service-web-access-dns-records-no-h.md)]

각 도메인 공급자에 대한 구체적인 설명은 다르지만 [루트가 아닌 사용자 지정 도메인 이름](#what-about-root-domains)(예: **www.contoso.com**)‘에서’ 앱과 통합된 Traffic Manager 도메인 이름(**contoso.trafficmanager.net**)‘으로’ 매핑합니다.  

> [!NOTE]
> 레코드를 이미 사용 중이고 앱을 우선적으로 바인딩해야 하는 경우 추가 CNAME 레코드를 만들 수 있습니다. 예를 들어 앱에 **www\.contoso.com** 을 우선적으로 바인딩하려면 **awverify.www** 에서 **contoso.trafficmanager.net** 으로 CNAME 레코드를 만듭니다. 그런 다음에는 "www" CNAME 레코드를 변경하지 않고도 앱에 "www\.contoso.com"을 추가할 수 있습니다. 자세한 내용은 [Azure App Service로 활성 DNS 이름 마이그레이션](manage-custom-dns-migrate-domain.md)을 참조하세요.

도메인 공급자에서 DNS 레코드를 추가하거나 수정했다면 변경 내용을 저장합니다.

### <a name="what-about-root-domains"></a>루트 도메인이란 무엇인가요?

Traffic Manager는 CNAME 레코드를 사용하는 사용자 지정 도메인 매핑만 지원하고 DNS 표준은 루트 도메인(예: **contoso.com**) 매핑을 위한 CNAME 레코드를 지원하지 않으므로 Traffic Manager 루트 도메인에 대한 매핑을 지원하지 않습니다. 이 문제를 해결하려면 앱 수준에서 URL 리디렉션을 사용합니다. 예를 들어 ASP.NET Core에서 [URL 다시 쓰기](/aspnet/core/fundamentals/url-rewriting)를 사용할 수 있습니다. 그런 다음 Traffic Manager를 사용하여 하위 도메인(**www.contoso.com**)의 부하를 분산합니다. [도메인 이름 apex에 대한 별칭 레코드를 만들어 Azure Traffic Manager 프로필을 참조](../dns/tutorial-alias-tm.md)할 수도 있습니다. 예를 들어 contoso.com이 있습니다. 리디렉션 서비스를 사용하는 대신, 영역에서 직접 Traffic Manager 프로필을 참조하도록 Azure DNS를 구성할 수 있습니다. 

고가용성 시나리오의 경우 루트 도메인에서 각 앱 복사본의 IP 주소로 향하는 여러 ‘A 레코드’를 만들어 Traffic Manager 없이도 부하 분산 DNS 설정을 구현할 수 있습니다. 그런 다음 [동일한 루트 도메인을 모든 앱 복사본에 매핑](app-service-web-tutorial-custom-domain.md#4-create-the-dns-records)합니다. 동일한 도메인 이름이 동일한 지역에 있는 두 개의 다른 앱에 매핑될 수 없으므로 이 설정은 앱 복사본이 다른 지역에 있는 경우에만 작동합니다.

## <a name="enable-custom-domain"></a>사용자 지정 도메인 사용
도메인 이름의 레코드가 전파된 후 브라우저를 사용하여 사용자 지정 도메인 이름이 App Service 앱에 대해 해석되었는지 확인합니다.

> [!NOTE]
> CNAME이 DNS 시스템을 통해 전파되는 데 약간의 시간이 걸릴 수 있습니다. <a href="https://www.digwebinterface.com/">https://www.digwebinterface.com/</a> 등의 서비스를 사용하여 CNAME을 사용할 수 있는지 확인할 수 있습니다.
> 
> 

1. 도메인 확인에 성공하면 [Azure Portal](https://portal.azure.com)의 앱 페이지로 돌아갑니다.
2. 왼쪽 탐색 영역에서 **사용자 지정 도메인** > **호스트 이름 추가** 를 선택합니다.
4. 이전에 매핑한 사용자 지정 도메인 이름을 입력하고 **유효성 검사** 를 선택합니다.
5. **호스트 이름 레코드 형식** 이 **CNAME(www\.example.com 또는 하위 도메인)** 으로 설정되어 있는지 확인합니다.

6. App Service 앱은 이제 Traffic Manager 엔드포인트와 통합되므로 **CNAME 구성** 아래에 Traffic Manager 도메인 이름이 표시됩니다. 이를 선택하고 **사용자 지정 도메인 추가** 를 클릭합니다.

    ![앱에 DNS 이름 추가](./media/configure-domain-traffic-manager/enable-traffic-manager-domain.png)

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure App Service에서 TLS/SSL 바인딩으로 사용자 지정 DNS 이름 보호](configure-ssl-bindings.md)
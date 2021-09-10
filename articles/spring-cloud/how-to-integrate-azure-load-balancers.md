---
title: 자습서 - Azure 부하 분산 솔루션과 Azure Spring Cloud 통합
description: Azure 부하 분산 솔루션과 Azure Spring Cloud를 통합하는 방법
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 04/20/2020
ms.custom: devx-track-java
ms.openlocfilehash: 058d8ea63c931f60f27b827874726791f79a31db
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122537446"
---
# <a name="integrate-azure-spring-cloud-with-azure-load-balance-solutions"></a>Azure 부하 분산 솔루션과 Azure Spring Cloud 통합

**이 문서는 다음에 적용됩니다.** ✔️ Java ✔️ C#

Azure Spring Cloud는 Azure에서 마이크로 서비스를 지원합니다.  비즈니스를 성장시키려면 Azure Spring Cloud의 여러 인스턴스를 관리하는 여러 데이터 센터가 필요할 수 있습니다.

Azure는 이미 다양한 부하 분산 솔루션을 제공합니다. Azure Spring Cloud를 Azure 부하 분산 솔루션과 통합하는 데는 세 가지 옵션이 있습니다.

1. Azure Spring Cloud를 Azure Traffic Manager와 통합
2. Azure Spring Cloud를 Azure App Gateway와 통합
3. Azure Spring Cloud를 Azure Front Door와 통합

## <a name="prerequisites"></a>사전 요구 사항

* Azure Spring Cloud: [Azure Spring Cloud 서비스를 만드는 방법](./quickstart.md)
* Azure Traffic Manager: [Traffic Manager를 만드는 방법](../traffic-manager/quickstart-create-traffic-manager-profile.md)
* Azure App Gateway: [애플리케이션 게이트웨이를 만드는 방법](../application-gateway/quick-create-portal.md)
* Azure Front 도어: [Front Door를 만드는 방법](../frontdoor/quickstart-create-front-door.md)

## <a name="integrate-azure-spring-cloud-with-azure-traffic-manager"></a>Azure Spring Cloud를 Azure Traffic Manager와 통합

Azure Spring Cloud를 Traffic Manager와 통합하려면 해당 공용 엔드포인트를 Traffic Manager의 엔드포인트로 추가한 다음 Traffic Manager와 Azure Spring Cloud 모두에 대해 사용자 지정 도메인을 구성합니다.

### <a name="add-endpoint-in-traffic-manager"></a>Traffic Manager에 엔드포인트 추가

Traffic Manager에 엔드포인트 추가:

1. *외부 엔드포인트* 가 될 **형식** 을 지정합니다.
1. 각 Azure Spring Cloud 공용 엔드포인트의 FQDN(정규화된 도메인 이름)을 입력합니다.
1. **확인** 을 선택합니다.

    ![Traffic Manager 1](media/spring-cloud-load-balancers/traffic-manager-1.png) ![Traffic Manager 2](media/spring-cloud-load-balancers/traffic-manager-2.png)

### <a name="configure-custom-domain"></a>사용자 지정 도메인 구성

구성을 완료하려면 다음과 같이 합니다.

1. 도메인 공급자의 웹 사이트에 로그인하고 사용자 지정 도메인에서 Traffic Manager의 Azure 기본 도메인 이름으로 CNAME 레코드 매핑을 만듭니다.
1. [Azure Spring Cloud에 사용자 지정 도메인을 추가하는 방법](./tutorial-custom-domain.md)에 대한 지침을 따르세요.
1. 위의 사용자 지정 도메인 바인딩을 Traffic Manager에 추가하여 Azure Spring Cloud에 해당 App Service가 추가되도록 하고 여기에 SSL 인증서를 업로드합니다.

    ![Traffic Manager 3](media/spring-cloud-load-balancers/traffic-manager-3.png)

## <a name="integrate-azure-spring-cloud-with-azure-app-gateway"></a>Azure Spring Cloud를 Azure App Gateway와 통합

Azure Spring Cloud 서비스와 통합하려면 다음 구성을 완료합니다.

### <a name="configure-backend-pool"></a>백 엔드 풀 구성

1. **대상 유형** 을 *IP 주소* 또는 *FQDN* 으로 지정합니다.
1. Azure Spring Cloud 공용 엔드포인트를 입력합니다.

    ![App Gateway 1](media/spring-cloud-load-balancers/app-gateway-1.png)

### <a name="add-custom-probe"></a>사용자 지정 프로브 추가

1. **상태 프로브** 를 선택하고 **추가** 를 선택하여 사용자 지정 **프로브** 대화 상자를 엽니다.
1. 핵심은 **백 엔드 HTTP 설정에서 호스트 이름 선택** 옵션에 대해 *예* 를 선택하는 것입니다.

    ![App Gateway 2](media/spring-cloud-load-balancers/app-gateway-2.png)

### <a name="configure-http-setting"></a>HTTP 설정 구성

1. **HTTP 설정** 을 선택하고 **추가** 를 선택하여 HTTP 설정을 추가합니다.
1. **새 호스트 이름으로 재정의:** *예* 를 선택합니다.
1. **호스트 이름 재정의**: **백 엔드 대상에서 호스트 이름 선택** 을 선택합니다.
1. **사용자 지정 프로브 사용**: *예* 를 선택하고 위에서 만든 사용자 지정 프로브를 선택합니다.

    ![App Gateway 3](media/spring-cloud-load-balancers/app-gateway-3.png)

### <a name="configure-rewrite-set"></a>다시 쓰기 집합 구성

1. **다시 쓰기** 를 선택한 다음 **다시 쓰기 집합** 을 선택하여 다시 쓰기 집합을 추가합니다.
1. Azure Spring Cloud 공용 엔드포인트에 요청을 라우팅하는 라우팅 규칙을 선택합니다.
1. **재작성 규칙 구성** 탭에서 **재작성 규칙 추가** 를 선택합니다.
1. **재작성 유형**: **요청 헤더** 를 선택
1. **작업 유형**: **삭제** 를 선택
1. **헤더 이름**: **공용 헤더** 를 선택
1. **공용 헤더**: **X-Forwarded-Proto** 를 선택

    ![App Gateway 4](media/spring-cloud-load-balancers/app-gateway-4.png)

## <a name="integrate-azure-spring-cloud-with-azure-front-door"></a>Azure Spring Cloud를 Azure Front Door와 통합

Azure Spring Cloud 서비스와 통합하고 백 엔드 풀을 구성하려면 다음 단계를 사용합니다.

1. **백 엔드 풀을 추가합니다**.
1. 호스트를 추가하여 백 엔드 엔드포인트를 지정합니다.

    ![Front Door 1](media/spring-cloud-load-balancers/front-door-1.png)

1. **백 엔드 호스트 유형** 을 *사용자 지정 호스트* 로 지정합니다.
1. **백 엔드 호스트 이름** 에 Azure Spring Cloud 공용 엔드포인트의 FQDN을 입력합니다.
1. **백 엔드 호스트 이름** 과 같은 **백 엔드 호스트 헤더** 기본값을 적용합니다.

    ![Front Door 2](media/spring-cloud-load-balancers/front-door-2.png)

## <a name="next-steps"></a>다음 단계

* [Traffic Manager 프로필 만드는 방법](../traffic-manager/quickstart-create-traffic-manager-profile.md)
* [애플리케이션 게이트웨이 만드는 방법](../application-gateway/quick-create-portal.md)
* [Front Door 만드는 방법](../frontdoor/quickstart-create-front-door.md)

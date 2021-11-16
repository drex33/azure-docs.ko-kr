---
title: Azure AD B2C 사용자 지정 도메인 사용
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C의 리디렉션 URL에 사용자 지정 도메인을 사용하도록 설정하는 방법을 알아봅니다.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/15/2021
ms.author: kengaderdus
ms.subservice: B2C
ms.custom: b2c-support
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: edddf44509e6eeff6f50b4361fe8c40a7832a8a8
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130222877"
---
# <a name="enable-custom-domains-for-azure-active-directory-b2c"></a>Azure Active Directory B2C에 사용자 지정 도메인 사용

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

이 문서에서는 Azure AD B2C(Azure Active Directory B2C)의 리디렉션 Url에 사용자 지정 도메인을 사용하도록 설정하는 방법을 설명합니다. 애플리케이션에 사용자 지정 도메인을 사용하면 보다 원활한 사용자 환경을 제공할 수 있습니다. 사용자의 관점에서 이들은 로그인 과정에서 Azure AD B2C 기본 도메인인 *&lt;tenant-name&gt;.b2clogin.com* 으로 리디렉션되지 않고 도메인에 그대로 있습니다.

![스크린샷은 Azure AD B2C 사용자 지정 도메인 사용자 환경을 보여 줍니다.](./media/custom-domain/custom-domain-user-experience.png)

## <a name="custom-domain-overview"></a>사용자 지정 도메인 개요

[Azure Front Door](https://azure.microsoft.com/services/frontdoor/)를 사용하여 Azure AD B2C에서 사용자 지정 도메인을 사용하도록 설정할 수 있습니다. Azure Front Door는 Microsoft 글로벌 경계 네트워크를 사용하여 빠르고, 안전하고, 스케일링 가능한 웹 애플리케이션을 만드는 글로벌 진입점입니다. Azure Front Door 뒤에 Azure AD B2C 콘텐츠를 렌더링한 다음, 애플리케이션 URL의 사용자 지정 도메인을 통해 콘텐츠를 제공하도록 Azure Front Door에서 옵션을 구성할 수 있습니다.

다음 다이어그램은 Azure Front Door 통합을 보여 줍니다.

1. 애플리케이션에서 사용자는 로그인 단추를 선택하여 Azure AD B2C 로그인 페이지로 이동합니다. 이 페이지에서는 사용자 지정 도메인 이름을 지정합니다.
1. 웹 브라우저에서는 Azure Front Door IP 주소에 대한 사용자 지정 도메인 이름을 확인합니다. DNS를 확인하는 동안 사용자 지정 도메인 이름이 포함된 CNAME(정식 이름) 레코드는 Front Door 기본 프런트 엔드 호스트(예: `contoso-frontend.azurefd.net`)를 가리킵니다. 
1. 사용자 지정 도메인(예: `login.contoso.com`)으로 가는 트래픽은 지정된 Front Door 기본 프런트 엔드 호스트(`contoso-frontend.azurefd.net`)로 라우팅됩니다.
1. Azure Front Door가 Azure AD B2C `<tenant-name>.b2clogin.com` 기본 도메인을 사용하여 Azure AD B2C 콘텐츠를 호출합니다. Azure AD B2C 엔드포인트에 대한 요청은 원래 사용자 지정 도메인 이름을 포함합니다.
1. Azure AD B2C는 관련 콘텐츠와 원래 사용자 지정 도메인을 표시하여 요청에 응답합니다.

![사용자 지정 도메인 네트워킹 흐름을 보여 주는 다이어그램](./media/custom-domain/custom-domain-network-flow.png)

> [!IMPORTANT]
> 브라우저에서 Azure Front Door로의 연결은 항상 IPv6 대신 IPv4를 사용해야 합니다.

사용자 지정 도메인을 사용하는 경우 다음을 고려합니다.

- 여러 사용자 지정 도메인을 설정할 수 있습니다. 지원되는 사용자 지정 도메인의 최대 개수에 대한 자세한 내용은 Azure AD B2C의 경우 [Azure AD 서비스 제한 및 제한 사항](../active-directory/enterprise-users/directory-service-limits-restrictions.md)을, Azure Front Door의 경우 [Azure 구독 및 서비스 제한, 할당량, 제약 조건](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-front-door-service-limits)을 참조하세요.
- Azure Front Door는 별도의 Azure 서비스이므로 추가 요금이 발생합니다. 자세한 내용은 [Front Door 가격 책정](https://azure.microsoft.com/pricing/details/frontdoor)을 참조하세요.
- Azure Front Door [웹 애플리케이션 방화벽](../web-application-firewall/afds/afds-overview.md)을 사용하려면 Azure AD B2C 사용자 흐름에서 방화벽 구성 및 규칙이 올바르게 작동하는지 확인해야 합니다.
- 사용자 지정 도메인을 구성한 후에도 사용자 지정 정책을 사용하고 [액세스를 차단](#block-access-to-the-default-domain-name)하지 않는 한 Azure AD B2C 기본 도메인 이름인 *&lt;tenant-name&gt;.b2clogin.com* 을 계속 사용할 수 있습니다.
- 여러 애플리케이션이 있는 경우 브라우저에서 현재 사용 중인 도메인 이름으로 Azure AD B2C 세션을 저장하므로 사용자 지정 도메인으로 애플리케이션을 모두 마이그레이션합니다.

## <a name="prerequisites"></a>필수 구성 요소

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]


## <a name="step-1-add-a-custom-domain-name-to-your-azure-ad-b2c-tenant"></a>1단계. Azure AD B2C 테넌트에 사용자 지정 도메인 이름 추가

모든 새로운 Azure AD B2C 테넌트에는 &lt;domainname&gt;.onmicrosoft.com이라는 초기 도메인 이름이 제공됩니다. 초기 도메인 이름을 변경하거나 삭제할 수 없지만 사용자 지정 도메인을 추가할 수 있습니다. 

다음 단계에 따라 Azure AD B2C 테넌트에 사용자 지정 도메인을 추가합니다.

1. [Azure AD에 사용자 지정 도메인 이름을 추가](../active-directory/fundamentals/add-custom-domain.md#add-your-custom-domain-name-to-azure-ad)합니다.

    > [!IMPORTANT]
    > 관련 단계를 수행하려면 **Azure AD B2C** 테넌트에 로그인하고 **Azure Active Directory** 서비스를 선택해야 합니다.

1. [도메인 등록 기관에 DNS 정보를 추가](../active-directory/fundamentals/add-custom-domain.md#add-your-dns-information-to-the-domain-registrar)합니다. Azure AD에 사용자 지정 도메인 이름을 추가한 후 도메인의 DNS `TXT` 또는 `MX` 레코드를 만듭니다. 도메인의 이 DNS 레코드를 만들면 도메인 이름의 소유권이 확인됩니다.

    다음 예제에서는 *login.contoso.com* 및 *account.contoso.com* 의 TXT 레코드를 보여 줍니다.

    |이름(호스트 이름)  |형식  |데이터  |
    |---------|---------|---------|
    |로그인   | TXT  | MS=ms12345678  |
    |account | TXT  | MS=ms87654321  |
    
    TXT 레코드는 도메인의 하위 도메인 또는 호스트 이름과 연결해야 합니다. 예를 들어, *contoso.com* 도메인의 *login* 부분입니다. 호스트 이름이 비어 있거나 `@`이면 Azure AD는 추가된 사용자 지정 도메인을 확인할 수 없습니다. 다음 예제에서는 두 레코드가 모두 잘못 구성되었습니다.
    
    |이름(호스트 이름)  |형식  |데이터  |
    |---------|---------|---------|
    | | TXT  | MS=ms12345678  |
    | @ | TXT  | MS=ms12345678  | 
    
    > [!TIP]
    > GoDaddy와 같이 공개적으로 사용할 수 있는 DNS 서비스를 사용하여 사용자 지정 도메인을 관리할 수 있습니다. DNS 서버가 없는 경우 [Azure DNS 영역](../dns/dns-getstarted-portal.md) 또는 [App Service 도메인](../app-service/manage-custom-dns-buy-domain.md)을 사용할 수 있습니다.

1. [사용자 지정 도메인 이름을 확인](../active-directory/fundamentals/add-custom-domain.md#verify-your-custom-domain-name)합니다. 사용할 계획인 각 하위 도메인이나 호스트 이름을 확인합니다. 예를 들어, *login.contoso.com* 및 *account.contoso.com* 을 사용하여 로그인하려면 최상위 도메인인 *contoso.com* 이 아니라 두 하위 도메인을 모두 확인해야 합니다. 

    도메인을 확인한 후에는 만든 DNS TXT 레코드를 **삭제** 합니다.

    
## <a name="step-2-create-a-new-azure-front-door-instance"></a>2단계. Azure Front Door 인스턴스 만들기

다음 단계에 따라 Azure AD B2C 테넌트의 Front Door를 만듭니다. 자세한 내용은 [애플리케이션의 Front Door 만들기](../frontdoor/quickstart-create-front-door.md#create-a-front-door-for-your-application)를 참조하세요.
  

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. Azure AD B2C 테넌트를 포함하는 디렉터리가 *아니라* Azure Front Door에 사용할 Azure 구독을 포함하는 디렉터리를 선택하려면 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD 디렉터리를 찾은 다음 **전환** 을 선택합니다. 
1. 홈 페이지 또는 Azure 메뉴에서 **리소스 만들기** 를 선택합니다. **네트워킹** > **모두 보기** > **Front Door** 를 선택합니다.
1. **Front Door 만들기** 페이지의 **기본 사항** 탭에서 다음 정보를 입력하거나 선택한 다음, **다음: 구성** 을 선택합니다.

    | 설정 | 값 |
    | --- | --- |
    | **구독** | Azure 구독을 선택합니다. |    
    | **리소스 그룹** | 기존 리소스 그룹을 선택하거나 **새로 만들기** 를 선택하여 새 리소스 그룹을 만듭니다.|
    | **리소스 그룹 위치** | 리소스 그룹의 위치를 선택합니다. 예: **미국 중부** |

### <a name="21-add-frontend-host"></a>2.1 프런트 엔드 호스트 추가

프런트 엔드 호스트는 애플리케이션에서 사용되는 도메인 이름입니다. Front Door를 만들 때 기본 프런트 엔드 호스트는 `azurefd.net`의 하위 도메인입니다.

Azure Front Door는 사용자 지정 도메인을 프런트 엔드 호스트에 연결하는 옵션을 제공합니다. 이 옵션을 사용하여 Azure AD B2C 사용자 인터페이스를 Front Door 소유 도메인 이름이 아닌 URL의 사용자 지정 도메인과 연결합니다. `https://login.contoso.com`)을 입력합니다.

프런트 엔드 호스트를 추가하려면 다음 단계를 수행합니다.

1. **프런트 엔드/도메인** 에서 **+** 를 선택하여 **프런트 호스트 추가** 를 엽니다.
1. **호스트 이름** 에 대해 전역적으로 고유한 호스트 이름을 입력합니다. 호스트 이름이 사용자 지정 도메인이 아닙니다. 이 예에서는 *contoso-frontend* 를 사용합니다. **추가** 를 선택합니다.

    ![프런트 엔드 호스트를 추가하는 방법을 보여 주는 스크린샷](./media/custom-domain/add-frontend-host-azure-front-door.png)

### <a name="22-add-backend-and-backend-pool"></a>2.2 백 엔드 및 백 엔드 풀 추가

백 엔드는 [Azure AD B2C 테넌트 이름](tenant-management.md#get-your-tenant-name)인 `tenant-name.b2clogin.com`을 참조합니다. 백 엔드 풀을 추가하려면 다음 단계를 수행합니다.

1. 여전히 **Front Door 만들기** 의 **백 엔드 풀** 에서 **+** 를 선택하여 **백 엔드 풀 추가** 를 엽니다.

1. **이름** 을 입력합니다. 예를 들면, *myBackendPool* 입니다. **백 엔드 추가** 를 선택합니다.
    
    다음 스크린샷은 백 엔드 풀을 만드는 방법을 보여 줍니다.
    
    ![프런트 엔드 백 엔드 풀을 추가하는 방법을 보여 주는 스크린샷](./media/custom-domain/front-door-add-backend-pool.png)

1. **백 엔드 추가** 블레이드에서 다음 정보를 선택한 다음, **추가** 를 선택합니다.

    | 설정 | 값 |
    | --- | --- |
    | **백 엔드 호스트 유형**| **사용자 지정 호스트** 를 선택합니다.| 
    | **백 엔드 호스트 이름**| [Azure AD B2C](tenant-management.md#get-your-tenant-name) 이름인 `<tenant-name>.b2clogin.com`을 선택합니다. 예를 들어 contoso.b2clogin.com을 입력합니다.|
    | **백 엔드 호스트 헤더**| **백 엔드 호스트 이름** 으로 선택한 것과 동일한 값을 선택합니다.|
    
    **기타 모든 필드는 기본값으로 둡니다.*
    
    다음 스크린샷은 Azure AD B2C 테넌트와 연결된 사용자 지정 호스트 백 엔드를 만드는 방법을 보여 줍니다.
    
    ![사용자 지정 호스트 백 엔드를 추가하는 방법을 보여 주는 스크린샷](./media/custom-domain/add-a-backend.png)

1. 백 엔드 풀의 구성을 완료하려면 **백 엔드 풀 추가** 블레이드에서 **추가** 를 선택합니다.

1. 백 엔드 **풀** 에 **백 엔드** 를 추가한 후 **상태 프로브** 를 사용하지 않습니다.

    ![백 엔드 풀을 추가하고 상태 프로브를 사용하지 않도록 설정하는 방법을 보여 주는 스크린샷](./media/custom-domain/add-a-backend-pool.png)

### <a name="23-add-a-routing-rule"></a>2.3 라우팅 규칙 추가

마지막으로, 회람 규칙을 추가합니다. 라우팅 규칙은 프런트 엔드 호스트를 백 엔드 풀에 매핑합니다. 이 규칙은 [프런트 엔드 호스트](#21-add-frontend-host)에 대한 요청을 Azure AD B2C [백 엔드](#22-add-backend-and-backend-pool)에 전달합니다. 라우팅 규칙을 추가하려면 다음 단계를 수행합니다.

1. **규칙 추가** 에서 **이름** 에 대해 *LocationRule* 을 입력합니다. 모든 기본값을 적용한 다음, **추가** 를 선택하여 회람 규칙을 추가합니다.
1. **검토 + 만들기** 를 선택한 다음, **만들기** 를 선택합니다.
    
     ![Azure Front Door를 만드는 방법을 보여 주는 스크린샷](./media/custom-domain/configuration-azure-front-door.png)


## <a name="step-3-set-up-your-custom-domain-on-azure-front-door"></a>3단계. Azure Front Door에 사용자 지정 도메인 설정

이 단계에서는 [1단계](#step-1-add-a-custom-domain-name-to-your-azure-ad-b2c-tenant)에서 등록한 사용자 지정 도메인을 Front Door에 추가합니다. 

### <a name="31-create-a-cname-dns-record"></a>3.1 CNAME DNS 레코드 만들기

Front Door에 사용자 지정 도메인을 사용하려면 먼저 도메인 공급 기업을 사용하여 Front Door의 기본 프런트 엔드 호스트(contoso-frontend.azurefd.net으로 가정)를 가리키는 CNAME(정식 이름) 레코드를 만들어야 합니다.

CNAME 레코드는 원본 도메인을 대상 도메인 이름(별칭)에 매핑하는 DNS 레코드의 형식입니다. Azure Front Door의 경우 원본 도메인 이름은 사용자 지정 도메인 이름이고, 대상 도메인 이름은 [2.1단계](#21-add-frontend-host)에서 구성한 Front Door 기본 호스트 이름입니다. 

Front Door가 사용자가 만든 CNAME 레코드를 확인하면, 원본 사용자 지정 도메인(예: login.contoso.com)에 전달되는 트래픽은 지정된 대상 Front Door 기본 프런트 엔드 호스트(예: `contoso-frontend.azurefd.net`)로 라우팅됩니다. 자세한 내용은 [Front Door에 사용자 지정 도메인 추가](../frontdoor/front-door-custom-domain.md)를 참조하세요. 

사용자 지정 도메인에 대한 CNAME 레코드를 만들려면:

1. 사용자 지정 도메인에 대한 도메인 공급자의 웹 사이트에 로그인합니다.

1. 공급자의 설명서를 참조하거나 이름이 **도메인 이름**, **DNS**, 또는 **이름 서버 관리** 인 웹 사이트 부분을 검색하여 DNS 레코드 관리 페이지를 찾습니다. 

1. 사용자 지정 도메인에 대한 CNAME 레코드 항목을 만들고 다음 표와 같이 필드에 입력합니다(필드 이름 다를 수 있음).

    | 원본          | Type  | 대상           |
    |-----------------|-------|-----------------------|
    | `<login.contoso.com>` | CNAME | `contoso-frontend.azurefd.net` |

   - 원본: 사용자 지정 도메인 이름(예: login.contoso.com)을 입력합니다.

   - 유형: *CNAME* 를 입력합니다.

   - 대상: [2.1단계](#21-add-frontend-host)에서 만드는 기본 Front Door 프런트 엔드 호스트를 입력합니다. format: _&lt;hostname&gt;_ .azurefd.net 형식이어야 합니다. `contoso-frontend.azurefd.net`)을 입력합니다.

1. 변경 내용을 저장합니다.

### <a name="32-associate-the-custom-domain-with-your-front-door"></a>3.2 사용자 지정 도메인을 Front Door와 연결

사용자 지정 도메인을 등록한 후에는 Front Door에 추가할 수 있습니다.
    
1. **Front Door 디자이너** 페이지의 **프런트 엔드/도메인** 에서 **+** 를 선택하여 사용자 지정 도메인을 추가합니다.

    ![사용자 지정 도메인을 추가하는 방법을 보여 주는 스크린샷](./media/custom-domain/azure-front-door-add-custom-domain.png) 
    
1. **프런트 엔드 호스트** 의 경우 CNAME 레코드의 대상 도메인으로 사용할 프런트 엔드 호스트는 미리 채워져 있으며 Front Door *&lt;default hostname&gt;* .azurefd.net에서 파생됩니다. 이는 변경할 수 없습니다.

1. **사용자 지정 호스트 이름** 의 경우 CNAME 레코드의 원본 도메인으로 사용하려면 하위 도메인을 포함하여 사용자 지정 도메인을 입력합니다. 예를 들면, login.contoso.com입니다.

    ![사용자 지정 도메인을 확인하는 방법을 보여 주는 스크린샷](./media/custom-domain/azure-front-door-add-custom-domain-verification.png)

    Azure에서 입력한 사용자 지정 도메인 이름에 대한 CNAME 레코드가 있는지 확인합니다. CNAME이 올바르면 사용자 지정 도메인의 유효성이 검사됩니다.

    
1. 사용자 지정 도메인 이름이 확인된 후 **사용자 지정 도메인 이름 HTTPS** 에서 **사용** 을 선택합니다. 
    
    ![Azure Front Door 인증서를 사용하여 HTTPS를 사용하도록 설정하는 방법을 보여 주는 스크린샷](./media/custom-domain/azure-front-door-add-custom-domain-https-settings.png)

1. **인증서 관리 유형** 에서 [Front Door 관리](../frontdoor/front-door-custom-domain-https.md#option-1-default-use-a-certificate-managed-by-front-door) 또는 [내 인증서 사용](../frontdoor/front-door-custom-domain-https.md#option-2-use-your-own-certificate)을 선택합니다. ‘Front Door 관리됨’ 옵션을 선택하는 경우 인증서가 완전 프로비저닝될 때까지 기다립니다.

1. **추가** 를 선택합니다.

### <a name="33-update-the-routing-rule"></a>3.3 라우팅 규칙 업데이트

1. **라우팅 규칙** 에서는 [2.3단계](#23-add-a-routing-rule)에서 만든 라우팅 규칙을 선택합니다.

    ![라우팅 규칙을 선택하는 방법을 보여 주는 스크린샷](./media/custom-domain/select-routing-rule.png)

1. **프런트 엔드/도메인** 에서 사용자 지정 도메인 이름을 선택합니다.
    
    ![Azure Front Door 라우팅 규칙을 업데이트하는 방법을 보여 주는 스크린샷](./media/custom-domain/update-routing-rule.png)

1. **업데이트** 를 선택합니다.
1. 기본 창에서 **저장** 을 선택합니다.

## <a name="step-4-configure-cors"></a>4단계. CORS 구성

HTML 템플릿을 사용하여 [Azure AD B2C 사용자 인터페이스를 사용자 지정](customize-ui-with-html.md)하는 경우 사용자 지정 도메인을 사용하여 [CORS를 구성](customize-ui-with-html.md?pivots=b2c-user-flow.md#3-configure-cors)해야 합니다.

다음 단계를 수행하여 CORS(원본 간 리소스 공유)에 Azure Blob Storage를 구성합니다.

1. [Azure Portal](https://portal.azure.com)에서 스토리지 계정으로 이동합니다.
1. 메뉴에서 **CORS** 를 선택합니다.
1. **허용된 원본** 에 `https://your-domain-name`을 입력합니다. `your-domain-name`을 실제 도메인 이름으로 바꿉니다. 예들 들어 `https://login.contoso.com`입니다. 테넌트 이름을 입력할 때는 모두 소문자를 사용합니다.
1. **허용된 메소드** 에서 `GET`과 `OPTIONS`를 모두 선택합니다.
1. **허용된 헤더** 에 별표(*)를 입력합니다.
1. **노출된 헤더** 에 별표(*)를 입력합니다.
1. **최대 기간** 에 200을 입력합니다.
1. **저장** 을 선택합니다.

## <a name="test-your-custom-domain"></a>사용자 지정 도메인 테스트

1. [Azure Portal](https://portal.azure.com)에 로그인합니다.
1. Azure AD B2C 테넌트가 포함된 디렉터리를 사용하고 있는지 확인합니다. 포털 도구 모음에서 **디렉터리 + 구독** 아이콘을 선택합니다.
1. **포털 설정 | 디렉터리 + 구독** 페이지의 **디렉터리 이름** 목록에서 Azure AD B2C 디렉터리를 찾은 다음, **전환** 을 선택합니다.
1. Azure Portal에서 **Azure AD B2C** 를 검색하고 선택합니다.
1. **정책** 에서 **사용자 흐름(정책)** 을 선택합니다.
1. 사용자 흐름을 선택한 다음 **사용자 흐름 실행** 을 선택합니다.
1. **애플리케이션** 으로 이전에 등록한 *webapp1* 이라는 웹 애플리케이션을 선택합니다. **회신 URL** 에는 `https://jwt.ms`가 표시되어야 합니다.
1. **사용자 흐름 엔드포인트 실행** 아래에 있는 URL을 복사합니다.

    ![인증 요청 URI를 복사하는 방법을 보여 주는 스크린샷](./media/custom-domain/user-flow-run-now.png)

1. 사용자 지정 도메인을 사용하여 로그인을 시뮬레이션하려면 웹 브라우저를 열고 복사한 URL을 사용합니다. Azure AD B2C 도메인( _&lt;tenant-name&gt;_ .b2clogin.com)을 사용자 지정 도메인으로 바꿉니다.   

    예를 들어 다음 식을 사용하는 대신

    ```http
    https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1_susi&client_id=63ba0d17-c4ba-47fd-89e9-31b3c2734339&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login
    ```

    다음을 사용합니다.

    ```http
    https://login.contoso.com/contoso.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1_susi&client_id=63ba0d17-c4ba-47fd-89e9-31b3c2734339&nonce=defaultNonce&redirect_uri=https%3A%2F%2Fjwt.ms&scope=openid&response_type=id_token&prompt=login    
    ```

1. Azure AD B2C 올바르게 로드되었는지 확인합니다. 그런 다음, 로컬 계정으로 로그인합니다.
1. 나머지 정책에 대한 테스트를 반복합니다.

## <a name="configure-your-identity-provider"></a>ID 공급자 구성

사용자가 소셜 ID 공급자를 통한 로그인을 선택하면 Azure AD B2C는 권한 부여 요청을 시작하고 선택한 ID 공급자로 사용자를 이동하여 로그인 프로세스를 완료합니다. 권한 부여 요청은 `redirect_uri`를 Azure AD B2C 기본 도메인 이름으로 지정합니다. 

```http
https://<tenant-name>.b2clogin.com/<tenant-name>/oauth2/authresp
```

외부 ID 공급자를 사용하여 로그인할 수 있도록 정책을 구성한 경우에는 OAuth 리디렉션 Uri를 사용자 지정 도메인으로 업데이트합니다. 대부분의 ID 공급자는 여러 리디렉션 Uri 등록을 허용합니다. Azure AD B2C 기본 도메인 이름을 사용하는 애플리케이션에 영향을 주지 않고 사용자 지정 정책을 테스트할 수 있도록 리디렉션 Uri를 바꾸는 것보다는 추가하는 것이 좋습니다. 

다음 리디렉션 URI에서 다음과 같이 바꿉니다.

```http
https://<custom-domain-name>/<tenant-name>/oauth2/authresp
``` 

- **&lt;custom-domain-name&gt;** 을 사용자 지정 도메인 이름으로 바꿉니다.
- **&lt;tenant-name&gt;** 을 테넌트 이름 또는 테넌트 ID로 바꿉니다.

다음 예제에서는 유효한 OAuth 리디렉션 URI를 보여 줍니다.

```http
https://login.contoso.com/contoso.onmicrosoft.com/oauth2/authresp
```

[테넌트 ID](#optional-use-tenant-id)를 사용하도록 선택하는 경우 유효한 OAuth 리디렉션 URI는 다음 샘플과 같습니다.

```http
https://login.contoso.com/11111111-1111-1111-1111-111111111111/oauth2/authresp
```

[SAML ID 공급자](saml-identity-provider-technical-profile.md) 메타데이터는 다음 샘플과 같습니다.

```http
https://<custom-domain-name>.b2clogin.com/<tenant-name>/<your-policy>/samlp/metadata?idptp=<your-technical-profile>
```

## <a name="configure-your-application"></a>애플리케이션 구성 

사용자 지정 도메인을 구성하고 테스트한 후에는 Azure AD B2C 도메인 대신 호스트 이름으로 사용자 지정 도메인을 지정하는 URL을 로드하도록 애플리케이션을 업데이트할 수 있습니다. 

사용자 지정 도메인 통합은 Azure AD B2C 정책(사용자 흐름 또는 사용자 지정 정책)을 사용하여 사용자를 인증하는 인증 엔드포인트에 적용됩니다. 해당 엔드포인트는 다음 샘플과 같이 나타낼 수 있습니다.

- <code>https://\<custom-domain\>/\<tenant-name\>/<b>\<policy-name\></b>/v2.0/.well-known/openid-configuration</code>

- <code>https://\<custom-domain\>/\<tenant-name\>/<b>\<policy-name\></b>/oauth2/v2.0/authorize</code>

- <code>https://\<custom-domain\>/<tenant-name\>/<b>\<policy-name\></b>/oauth2/v2.0/token</code>

다음을
- **custom-domain** 을 사용자 도메인으로
- **tenant-name** 을 테넌트 이름 또는 테넌트 ID로
- **policy-name** 을 정책 이름으로. [Azure AD B2C 정책에 대해 자세히 알아보세요](technical-overview.md#identity-experiences-user-flows-or-custom-policies). 


[SAML 서비스 공급자](./saml-service-provider.md) 메타데이터는 다음 샘플과 같이 나타낼 수 있습니다. 

```html
https://custom-domain-name/tenant-name/policy-name/Samlp/metadata
```

### <a name="optional-use-tenant-id"></a>(선택 사항) 필드 테넌트 ID 사용

URL의 “B2C”에 대한 모든 참조를 제거하기 위해 URL의 B2C 테넌트 이름을 테넌트 ID GUID로 바꿀 수 있습니다. Azure Portal의 B2C 개요 페이지에서 테넌트 ID GUID를 찾을 수 있습니다.
예를 들어 `https://account.contosobank.co.uk/contosobank.onmicrosoft.com/`를 `https://account.contosobank.co.uk/<tenant ID GUID>/`로 변경하세요.

테넌트 이름 대신 테넌트 ID를 사용하도록 선택하는 경우 ID 공급자의 **OAuth 리디렉션 URI** 도 그에 따라 업데이트해야 합니다. 자세한 내용은 [ID 공급자 구성](#configure-your-identity-provider)을 참조하세요.

### <a name="token-issuance"></a>토큰 발급

토큰 발급자 이름(iss) 클레임은 사용 중인 사용자 지정 도메인에 따라 변경됩니다. 예를 들면 다음과 같습니다.

```http
https://<domain-name>/11111111-1111-1111-1111-111111111111/v2.0/
```

::: zone pivot="b2c-custom-policy"

## <a name="block-access-to-the-default-domain-name"></a>기본 도메인 이름에 대한 액세스 차단

사용자 지정 도메인을 추가하고 애플리케이션을 구성한 후에도 사용자는 &lt;tenant-name&gt;.b2clogin.com 도메인에 액세스할 수 있습니다. 액세스를 방지하기 위해 허용되는 도메인 목록에 대해 “호스트 이름” 권한 부여 요청을 확인하는 정책을 구성할 수 있습니다. 호스트 이름은 URL에 표시되는 도메인 이름입니다. 호스트 이름은 `{Context:HostName}` [클레임 확인자](claim-resolver-overview.md)를 통해 확인할 수 있습니다. 그런 다음 사용자 지정 오류 메시지를 표시할 수 있습니다. 

1. [GitHub](https://github.com/azure-ad-b2c/samples/blob/master/policies/check-host-name)에서 호스트 이름을 확인하는 조건부 액세스 정책의 예를 가져옵니다.
1. 각 파일에서 `yourtenant` 문자열을 Azure AD B2C 테넌트의 이름으로 바꿉니다. 예를 들어 B2C 테넌트의 이름이 *contosob2c* 인 경우 모든 `yourtenant.onmicrosoft.com` 인스턴스는 `contosob2c.onmicrosoft.com`이 됩니다.
1. 정책 파일, `B2C_1A_TrustFrameworkExtensions_HostName.xml`과 `B2C_1A_signup_signin_HostName.xml`을 차례대로 업로드합니다.

::: zone-end

## <a name="troubleshooting"></a>문제 해결

### <a name="azure-ad-b2c-returns-a-page-not-found-error"></a>Azure AD B2C에서 페이지를 찾을 수 없다는 오류 반환

- **증상** - 사용자 지정 도메인을 구성한 후 사용자 지정 도메인을 사용하여 로그인하려고 하면 HTTP 404 오류 메시지가 표시됩니다.
- **가능한 원인** - 이 문제는 DNS 구성 또는 Azure Front Door 백 엔드 구성과 관련이 있을 수 있습니다. 
- **해결 방법**:  
    1. Azure AD B2C 테넌트에서 사용자 지정 도메인을 [등록하고 확인](#step-1-add-a-custom-domain-name-to-your-azure-ad-b2c-tenant)합니다.
    1. [사용자 지정 도메인](../frontdoor/front-door-custom-domain.md)을 올바르게 구성합니다. 사용자 지정 도메인의 `CNAME` 레코드는 Azure Front Door 기본 프런트 엔드 호스트(예: contoso-frontend.azurefd.net)를 가리켜야 합니다.
    1. [Azure Front Door 백 엔드 풀 구성](#22-add-backend-and-backend-pool)이 사용자 지정 도메인 이름을 설정하고 사용자 흐름 또는 사용자 지정 정책이 저장되는 테넌트를 가리키도록 합니다.


### <a name="azure-ad-b2c-returns-the-resource-you-are-looking-for-has-been-removed-had-its-name-changed-or-is-temporarily-unavailable"></a>Azure AD B2C는 찾고 있는 리소스가 제거되었거나, 해당 이름이 변경되었거나, 일시적으로 사용할 수 없음을 반환합니다.

- **증상** - 사용자 지정 도메인을 구성한 후 사용자 지정 도메인으로 로그인하려고 할 때 ‘찾고 있는 리소스가 제거되었거나, 이름이 변경되었거나, 일시적으로 사용할 수 없음’ 오류 메시지가 표시됩니다.
- **가능한 원인** - 이 문제는 Azure AD 사용자 지정 도메인 확인과 관련이 있을 수 있습니다. 
- **해결 방법**: Azure AD B2C 테넌트에서 사용자 지정 도메인이 [등록되고 **성공적으로 확인** 되었는지](#step-1-add-a-custom-domain-name-to-your-azure-ad-b2c-tenant) 확인합니다.

### <a name="identify-provider-returns-an-error"></a>ID 공급자가 오류 반환

- **증상** - 사용자 지정 도메인을 구성한 후 로컬 계정을 사용하여 로그인할 수 있습니다. 그러나 외부 [소셜 또는 엔터프라이즈 ID 공급자](add-identity-provider.md)의 자격 증명을 사용하여 로그인하는 경우 ID 공급자가 오류 메시지를 표시합니다.
- **가능한 원인** - Azure AD B2C에서 사용자가 페더레이션 ID 공급자를 사용하여 로그인하도록 하는 경우 Azure AD B2C는 리디렉션 URI를 지정합니다. 리디렉션 URI는 ID 공급자가 토큰을 반환하는 엔드포인트입니다. 리디렉션 URI는 애플리케이션이 권한 부여 요청에 사용하는 것과 동일한 도메인입니다. 리디렉션 URI가 아직 ID 공급자에 등록되지 않은 경우 새 리디렉션 URI를 신뢰하지 않을 수 있으며, 이로 인해 오류 메시지가 발생합니다. 
- **해결 방법** - [ID 공급자 구성](#configure-your-identity-provider)의 단계에 따라 새 리디렉션 URI를 추가합니다. 

## <a name="frequently-asked-questions"></a>질문과 대답

### <a name="can-i-use-azure-front-door-advanced-configuration-such-as-web-application-firewall-rules"></a>Azure Front Door 고급 구성(예: ‘웹 애플리케이션 방화벽 규칙’)을 사용할 수 있나요? 
  
Azure Front Door 고급 구성 설정은 공식적으로 지원되지 않지만 사용자의 책임으로 사용할 수 있습니다. 

### <a name="when-i-use-run-now-to-try-to-run-my-policy-why-i-cant-see-the-custom-domain"></a>지금 실행을 사용하여 내 정책을 실행하려고 할 때 사용자 지정 도메인이 표시되지 않는 이유는 무엇인가요?

URL을 복사하고 도메인 이름을 수동으로 변경한 다음 브라우저에 다시 붙여넣으세요.

### <a name="which-ip-address-is-presented-to-azure-ad-b2c-the-users-ip-address-or-the-azure-front-door-ip-address"></a>Azure AD B2C에 어떤 IP 주소가 제공되나요? 사용자의 IP 주소인가요? 아니면 Azure Front Door IP 주소인가요?

Azure Front Door는 사용자의 원래 IP 주소를 전달합니다. 이는 감사 보고나 사용자 지정 정책에 표시되는 IP 주소입니다.

### <a name="can-i-use-a-third-party-web-application-firewall-waf-with-b2c"></a>B2C에 타사 WAF(웹 애플리케이션 방화벽)를 사용할 수 있나요?

Azure Front Door 앞에서 자체 웹 애플리케이션 방화벽을 사용하려면 Azure AD B2C 사용자 흐름이나 사용자 지정 정책에서 모든 것이 올바르게 작동하도록 구성하고 유효성을 검사해야 합니다.  

### <a name="can-my-azure-front-door-instance-be-hosted-in-a-different-subscription-than-my-azure-ad-b2c-tenant"></a>내 Azure Front Door 인스턴스를 내 Azure AD B2C 테넌트와 다른 구독에서 호스팅할 수 있나요?
    
예, Azure Front Door는 다른 구독에 있을 수 있습니다.
    
## <a name="next-steps"></a>다음 단계

[OAuth 권한 부여 요청](protocols-overview.md)에 대해 알아봅니다.

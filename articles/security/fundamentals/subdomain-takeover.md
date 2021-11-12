---
title: Azure DNS 별칭 레코드 및 Azure App Service의 사용자 지정 도메인 확인을 사용하여 하위 도메인 인수 방지
description: 일반적이고 심각도가 높은 위협인 하위 도메인 인수를 방지하는 방법을 알아봅니다.
services: security
author: memildin
manager: rkarlin
ms.assetid: ''
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/04/2021
ms.author: memildin
ms.openlocfilehash: 665cb2c8db360f061023294d563082321aae70e8
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132305810"
---
# <a name="prevent-dangling-dns-entries-and-avoid-subdomain-takeover"></a>현수 DNS 항목 방지 및 하위 도메인 인수 방지

이 문서에서는 하위 도메인 인수라는 일반적인 보안 위협에 대해 설명하고 이를 완화하기 위해 수행할 수 있는 단계를 설명합니다.


## <a name="what-is-a-subdomain-takeover"></a>하위 도메인 인수란?

하위 도메인 인수는 정기적으로 많은 리소스를 만들고 삭제하는 조직에 자주 발생하는 높은 심각도의 위협입니다. 프로비전 해제된 Azure 리소스를 가리키는 [DNS 레코드](../../dns/dns-zones-records.md#dns-records)가 있는 경우 하위 도메인 인수가 발생할 수 있습니다. 이러한 DNS 레코드는 "현수 DNS" 항목으로도 알려져 있습니다. CNAME 레코드는 특히 이 위협에 취약합니다. 하위 도메인 인수를 통해 악의적인 행위자는 조직의 도메인에 대한 트래픽을 악의적인 활동을 수행하는 사이트로 리디렉션할 수 있습니다.

하위 도메인 인수의 일반적인 시나리오는 다음과 같습니다.

1. **생성:**

    1. FQDN(정규화된 도메인 이름) `app-contogreat-dev-001.azurewebsites.net`을 사용하여 Azure 리소스를 프로비전합니다.

    1. Azure 리소스에 트래픽을 라우팅하는 하위 도메인 `greatapp.contoso.com`을 사용하여 DNS 영역에 CNAME 레코드를 할당합니다.

1. **프로비전 해제:**

    1. Azure 리소스는 더 이상 필요하지 않으면 프로비전 해제 또는 삭제됩니다. 
    
        이 시점에서 CNAME 레코드 `greatapp.contoso.com`은 DNS 영역에서 제거되어야 합니다. CNAME 레코드가 제거되지 않은 경우 활성 도메인으로 보급되지만 활성 Azure 리소스로 트래픽을 라우팅하지 않습니다. 이것이 '현수' DNS 레코드에 대한 정의입니다.

    1. 현수 하위 도메인 `greatapp.contoso.com`은 이제 취약해지고 다른 Azure 구독의 리소스에 할당되어 인수될 수 있습니다.

1. **인수:**

    1. 위협 행위자가 일반적으로 사용 가능한 방법 및 도구를 사용하여 현수 하위 도메인을 검색합니다.  

    1. 위협 행위자는 사용자가 이전에 제어했던 동일한 리소스 FQDN을 사용하여 Azure 리소스를 프로비전합니다. 이 예제에서 해당 이름은 `app-contogreat-dev-001.azurewebsites.net`입니다.

    1. 하위 도메인 `greatapp.contoso.com`으로 전송되는 트래픽은 이제 콘텐츠를 제어하는 악의적 행위자의 리소스로 라우팅됩니다.



![프로비전 해제된 웹 사이트에서 인수된 하위 도메인](./media/subdomain-takeover/subdomain-takeover.png)



## <a name="the-risks-of-subdomain-takeover"></a>하위 도메인 위험의 인수

DNS 레코드가 사용할 수 없는 리소스를 가리키는 경우에는 레코드 자체가 DNS 영역에서 제거되어야 합니다. 삭제되지 않으면 '현수 DNS' 레코드가 되어 하위 도메인 인수 가능성을 초래합니다.

현수 DNS 항목을 통해 위협 행위자는 연결된 DNS 이름을 제어하여 악성 웹 사이트 또는 서비스를 호스트할 수 있습니다. 조직의 하위 도메인에 악성 페이지 및 서비스가 있을 경우 다음과 같은 결과를 낳을 수 있습니다.

- **하위 도메인의 콘텐츠에 대한 제어 손실** - 조직이 콘텐츠를 보호할 능력이 없다는 부정적인 여론뿐 아니라 브랜드 손상 및 신뢰 상실을 초래합니다.

- **의심하지 않는 방문자로부터 쿠키 수집** - 웹앱은 세션 쿠키를 하위 도메인(*.contoso.com)에 노출하는 것이 일반적이므로 모든 하위 도메인에서 쿠키에 액세스할 수 있습니다. 위협 행위자는 하위 도메인 인수를 사용하여 진짜처럼 보이는 페이지를 빌드하고 의심하지 않는 사용자가 방문하도록 유도하여 쿠키를(심지어 보안 쿠키까지) 수집할 수 있습니다. 일반적인 오해는 SSL 인증서 사용이 사이트 및 사용자의 쿠키를 인수로부터 보호한다는 것입니다. 그러나 위협 행위자는 하이재킹된 하위 도메인을 사용하여 유효한 SSL 인증서를 신청하고 발급받을 수 있습니다. 유효한 SSL 인증서는 위협 행위자에게 보안 쿠키에 대한 액세스 권한을 부여하며 악성 사이트를 더 진짜처럼 보이게 할 수 있습니다.

- **피싱 캠페인** - 진짜처럼 보이는 하위 도메인이 피싱 캠페인에 사용될 수 있습니다. 위협 행위자가 안전한 것으로 알려진 브랜드의 합법적인 하위 도메인으로 주소가 지정된 메일을 수신할 수 있는 악성 사이트 및 MX 레코드가 이에 해당됩니다.

- **추가 위험** - 악성 사이트는 XSS, CSRF, CORS 바이패스 등 다른 클래식 공격으로 에스컬레이션하는 데 이용될 수 있습니다.



## <a name="identify-dangling-dns-entries"></a>현수 DNS 항목 식별

조직에서 현수 DNS 항목을 식별하려면 Microsoft의 GitHub 호스팅 PowerShell 도구 ['Get-DanglingDnsRecords'](https://aka.ms/DanglingDNSDomains)를 사용합니다.

Azure 고객은 이 도구를 사용하여 구독 또는 테넌트에서 만든 기존 Azure 리소스에 연결된 CNAME이 있는 모든 도메인을 나열할 수 있습니다.

CNAME이 다른 DNS 서비스에 있고 Azure 리소스를 가리키는 경우 입력 파일의 CNAME을 도구에 제공합니다.

이 도구는 다음 표에 나열된 Azure 리소스를 지원합니다. 도구는 모든 테넌트의 CNAME을 추출하거나 입력으로 사용합니다.


| 서비스                   | Type                                        | FQDNproperty                               | 예제                         |
|---------------------------|---------------------------------------------|--------------------------------------------|---------------------------------|
| Azure Front Door          | microsoft.network/frontdoors                | properties.cName                           | `abc.azurefd.net`               |
| Azure Blob Storage        | microsoft.storage/storageaccounts           | properties.primaryEndpoints.blob           | `abc. blob.core.windows.net`    |
| Azure CDN                 | microsoft.cdn/profiles/endpoints            | properties.hostName                        | `abc.azureedge.net`             |
| 퍼블릿 IP 주소       | microsoft.network/publicipaddresses         | properties.dnsSettings.fqdn                | `abc.EastUs.cloudapp.azure.com` |
| Azure Traffic Manager     | microsoft.network/trafficmanagerprofiles    | properties.dnsConfig.fqdn                  | `abc.trafficmanager.net`        |
| Azure Container Instance  | microsoft.containerinstance/containergroups | properties.ipAddress.fqdn                  | `abc.EastUs.azurecontainer.io`  |
| Azure API Management      | microsoft.apimanagement/service             | properties.hostnameConfigurations.hostName | `abc.azure-api.net`             |
| Azure App Service         | microsoft.web/sites                         | properties.defaultHostName                 | `abc.azurewebsites.net`         |
| Azure App Service - 슬롯 | microsoft.web/sites/slots                   | properties.defaultHostName                 | `abc-def.azurewebsites.net`     |



### <a name="prerequisites"></a>필수 조건

다음 권한을 가진 사용자로 쿼리를 실행합니다.

- Azure 구독에 대한 읽기 권한자 수준 이상의 액세스 권한
- Azure Resource Graph에 대한 읽기 액세스 권한

조직의 테넌트에 대한 전역 관리자인 경우 [모든 Azure 구독 및 관리 그룹을 관리할 수 있도록 액세스 권한 상승](../../role-based-access-control/elevate-access-global-admin.md)의 지침을 사용하여 조직 내 모든 구독에 액세스할 수 있도록 계정을 승격합니다.


> [!TIP]
> Azure Resource Graph에는 Azure 환경이 클 경우 고려해야 하는 제한 및 페이징 제한이 있습니다. 
> 
> [대규모 Azure 리소스 데이터 세트 작업에 대해 자세히 알아보세요.](../../governance/resource-graph/concepts/work-with-data.md)
> 
> 이 도구는 구독 일괄 처리를 사용하여 이러한 제한 사항을 방지합니다.

### <a name="run-the-script"></a>스크립트 실행

PowerShell 스크립트 **Get-DanglingDnsRecords.ps1** 에 대해 자세히 알아보고 GitHub에서 다운로드하세요. https://aka.ms/Get-DanglingDnsRecords.

## <a name="remediate-dangling-dns-entries"></a>현수 DNS 항목 수정 

DNS 영역을 검토하고 현수 또는 인수된 CNAME 레코드를 식별합니다. 하위 도메인이 현수 항목 또는 인수된 것으로 확인되는 경우 다음 단계를 통해 취약한 하위 도메인을 제거하고 위험을 완화합니다.

1. DNS 영역에서 더 이상 프로비전되지 않는 리소스의 FQDN을 가리키는 모든 CNAME 레코드를 제거합니다.

1. 트래픽이 사용자가 제어하는 리소스로 라우팅될 수 있도록 하려면 현수 하위 도메인의 CNAME 레코드에 지정된 FQDN을 사용하여 추가 리소스를 프로비전합니다.

1. 애플리케이션 코드에서 특정 하위 도메인에 대한 참조를 검토하여 잘못되었거나 오래된 하위 도메인 참조를 업데이트합니다.

1. 손상이 발생했는지 여부를 조사하고 조직의 인시던트 대응 절차에 따라 조치를 취합니다. 이 문제를 조사하기 위한 팁과 모범 사례는 아래에서 찾을 수 있습니다.

    애플리케이션 논리에서 OAuth 자격 증명과 같은 비밀이 현수 하위 도메인에 전송되거나, 개인 정보/중요한 정보가 현수 하위 도메인으로 전송되는 경우 해당 데이터가 제3자에게 노출될 수 있습니다.

1. 리소스가 프로비전 해제되었을 때 CNAME 레코드가 DNS 영역에서 제거되지 않은 이유를 파악하고 나중에 Azure 리소스를 프로비전 해제할 때 DNS 레코드가 적절하게 업데이트되도록 조치를 취합니다.


## <a name="prevent-dangling-dns-entries"></a>현수 DNS 항목 방지

조직에서 현수 DNS 항목 및 그로 인한 하위 도메인 인수를 방지하는 프로세스를 구현하는 것은 보안 프로그램의 중요한 부분입니다.

일부 Azure 서비스는 예방 조치를 구현하는 데 도움이 되는 기능을 제공하며 아래에 자세히 설명되어 있습니다. 이 문제를 방지하는 다른 방법은 조직의 모범 사례 또는 표준 운영 절차를 통해 정립해야 합니다.

### <a name="enable-microsoft-defender-for-app-service"></a>Microsoft Defender for App Service 사용

클라우드용 Microsoft Defender의 CWPP(통합 클라우드 워크로드 보호 플랫폼) Microsoft Defender for Cloud는 Azure, 하이브리드 및 다중 클라우드 리소스와 워크로드를 보호하기 위한 다양한 계획을 제공합니다.

**microsoft Defender for App Service** 계획에는 기존 DNS 검색이 포함됩니다. 이 플랜을 사용하도록 설정하면 App Service 웹 사이트를 서비스 해제했지만 DNS 등록 기관에서 해당 사용자 지정 도메인을 제거하지 않은 경우 보안 경고가 발생합니다.

클라우드용 Microsoft Defender의 기존 DNS 보호는 도메인이 Azure DNS 또는 외부 도메인 등록 기관으로 관리되는지 여부에 관계없이 사용할 수 있으며 Windows Linux의 App Service 적용됩니다.

이 Microsoft Defender 플랜의 이점 및 기타 이점에 대해 자세히 [알아보려면 App Service Microsoft Defender 소개를](../../security-center/defender-for-app-service-introduction.md)참조합니다.

### <a name="use-azure-dns-alias-records"></a>Azure DNS 별칭 레코드 사용

Azure DNS의 [별칭 레코드](../../dns/dns-alias.md#scenarios)는 DNS 레코드의 수명 주기를 Azure 리소스와 결합하여 현수 참조를 방지합니다. 공용 IP 주소 또는 Traffic Manager 프로필을 가리키는 별칭 레코드로 정규화된 DNS 레코드를 예로 들어보겠습니다. 이러한 기본 리소스를 삭제하면 DNS 별칭 레코드가 빈 레코드 집합이 됩니다. 삭제된 리소스를 더 이상 참조하지 않습니다. 별칭 레코드로 보호할 수 있는 항목에는 제한이 있다는 점에 유의해야 합니다. 현재 이 목록은 다음으로 제한됩니다.

- Azure Front Door
- Traffic Manager 프로필
- Azure CDN(Content Delivery Network) 엔드포인트
- 공용 IP

현재 제한된 서비스 제공 사항에도 불구하고, 가능하면 항상 별칭 레코드를 사용하여 하위 도메인 인수로부터 보호할 것을 권장합니다.

[Azure DNS의 별칭 레코드 기능에 대해 자세히 알아보세요.](../../dns/dns-alias.md#capabilities)



### <a name="use-azure-app-services-custom-domain-verification"></a>Azure App Service의 사용자 지정 도메인 확인 사용

Azure App Service에 대한 DNS 항목을 만들 때 도메인 확인 ID를 사용하여 asuid.{subdomain} TXT 레코드를 만듭니다. 이러한 TXT 레코드가 있는 경우 다른 Azure 구독이 사용자 지정 도메인을 확인, 즉 인수할 수 없습니다. 

이러한 레코드는 다른 사용자가 CNAME 항목에 있는 것과 동일한 이름을 사용하여 Azure App Service를 만드는 것을 방지하지 않습니다. 도메인 이름의 소유권을 증명할 수 없으면 위협 행위자는 트래픽을 수신하거나 콘텐츠를 제어할 수 없습니다.

[기존 사용자 지정 DNS 이름을 Azure App Service에 매핑하는 방법을 자세히 알아보세요.](../../app-service/app-service-web-tutorial-custom-domain.md)



### <a name="build-and-automate-processes-to-mitigate-the-threat"></a>위협을 완화하는 프로세스 구축 및 자동화

일반적으로 현수 DNS 위협을 방지하기 위해 정리 프로세스를 실행하는 것은 개발자 및 운영 팀 몫이 될 것입니다. 다음 관행은 조직이 이 위협으로부터의 피해를 방지하는 데 도움이 됩니다. 

- **예방 절차 구축:**

    - 애플리케이션 개발자에게 리소스를 삭제할 때마다 주소를 다시 라우팅하도록 교육합니다.

    - 서비스를 해제할 때 필수 검사 목록에 'DNS 항목 제거'를 포함합니다.

    - 사용자 지정 DNS 항목이 있는 모든 리소스에 [삭제 잠금](../../azure-resource-manager/management/lock-resources.md)을 설정합니다. 삭제 잠금은 리소스가 프로비전 해제되기 전에 매핑을 제거해야 함을 나타내는 표시기 역할을 합니다. 이와 같은 조치는 내부 교육 프로그램과 함께 사용할 경우에만 유효할 수 있습니다.

- **검색 절차 구축:**

    - DNS 레코드를 정기적으로 검토하여 하위 도메인이 모두 Azure 리소스에 매핑되는지 확인합니다.

        - 존재 - *.azurewebsites.net 또는 *.cloudapp.azure.com 같은 Azure 하위 도메인을 가리키는 리소스의 DNS 영역을 쿼리합니다([Azure 도메인의 참조 목록](azure-domains.md) 참조).
        - 소유 - DNS 하위 도메인이 대상으로 하는 모든 리소스를 소유하는지 확인합니다.

    - Azure FQDN(정규화된 도메인 이름) 엔드포인트 및 애플리케이션 소유자의 서비스 카탈로그를 유지 관리합니다. 서비스 카탈로그를 빌드하려면 다음 Azure Resource Graph 쿼리 스크립트를 실행합니다. 이 스크립트는 사용자가 액세스할 수 있는 리소스의 FQDN 엔드포인트 정보를 프로젝션하고 CSV 파일로 출력합니다. 테넌트의 모든 구독에 대한 액세스 권한이 있는 경우 스크립트는 다음 샘플 스크립트와 같이 해당 구독을 모두 고려합니다. 결과를 특정 구독 집합으로 제한하려면 스크립트를 다음과 같이 편집합니다.


- **수정 절차 구축:**
    - 현수 DNS 항목이 발견되면 팀에서 손상이 발생했는지 여부를 조사해야 합니다.
    - 리소스를 서비스 해제할 때 주소가 다시 라우팅되지 않은 이유를 조사합니다.
    - 더 이상 사용하지 않는 경우 DNS 레코드를 삭제하거나 조직에서 소유한 올바른 Azure 리소스(FQDN)를 가리키도록 합니다.
 

## <a name="next-steps"></a>다음 단계

하위 도메인 인수로부터 보호하는 데 사용할 수 있는 관련 서비스 및 Azure 기능에 대해 자세히 알아보려면 다음 페이지를 참조하세요.

- [microsoft Defender for App Service 사용](../../security-center/defender-for-app-service-introduction.md) - 기존 DNS 항목이 검색될 때 경고를 받습니다.

- [Azure DNS를 사용하여 현수 DNS 레코드 방지](../../dns/dns-alias.md#prevent-dangling-dns-records)

- [Azure App Service에서 사용자 지정 도메인을 추가할 때 도메인 확인 ID 사용](../../app-service/app-service-web-tutorial-custom-domain.md#3-get-a-domain-verification-id)

- [빠른 시작: Azure PowerShell을 사용하여 첫 번째 Resource Graph 쿼리 실행](../../governance/resource-graph/first-query-powershell.md)

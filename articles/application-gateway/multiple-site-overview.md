---
title: Azure Application Gateway에서 여러 사이트 호스팅
description: 이 문서에서는 Azure Application Gateway 다중 사이트 지원에 대한 개요를 제공합니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.date: 08/31/2021
ms.author: azhussai
ms.topic: conceptual
ms.openlocfilehash: 5feb112a9d1c9b7eb229c65f8bcce3845a3f23ad
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2021
ms.locfileid: "131844984"
---
# <a name="application-gateway-multiple-site-hosting"></a>Application Gateway 다중 사이트 호스팅

다중 사이트 호스팅을 사용하면 퍼블릭 연결 수신기를 사용하여 동일한 애플리케이션 게이트웨이 포트에 둘 이상의 웹 애플리케이션을 구성할 수 있습니다. 이 기능을 사용하면 최대 100개의 웹 사이트를 하나의 애플리케이션 게이트웨이에 추가하여 배포에 대해 보다 효율적인 토폴로지를 구성할 수 있습니다. 각 웹 사이트는 고유한 백 엔드 풀로 이동할 수 있습니다. 예를 들어 contoso.com, fabrikam.com 및 adatum.com이라는 세 개의 도메인이 애플리케이션 게이트웨이의 IP 주소를 가리킵니다. 3개의 다중 사이트 수신기를 만들고 각 포트 및 프로토콜 설정에 대해 각각의 수신기를 구성합니다. 

또한 다중 사이트 수신기에서 와일드카드 호스트 이름을 정의하고 수신기당 최대 5개의 호스트 이름을 정의할 수 있습니다. 자세한 내용은 [수신기의 와일드카드 호스트 이름](#wildcard-host-names-in-listener-preview)을 참조하세요.

:::image type="content" source="./media/multiple-site-overview/multisite.png" alt-text="다중 사이트 Application Gateway":::

> [!IMPORTANT]
> 규칙은 v1 SKU에 대한 포털에 나열된 순서대로 처리됩니다. v2 SKU의 경우, [규칙 우선 순위](#request-routing-rules-evaluation-order)를 사용하여 처리 순서를 지정합니다. 기본 수신기를 구성하기 전에 먼저 다중 사이트 수신기를 구성하는 것이 좋습니다.  그러면 트래픽이 올바른 백 엔드로 라우팅됩니다. 기본 수신기가 먼저 나열되고 들어오는 요청과 일치하면 해당 수신기에서 처리합니다.

`http://contoso.com`에 대한 요청은 ContosoServerPool로 라우팅되고, `http://fabrikam.com`에 대한 요청은 FabrikamServerPool로 라우팅됩니다.

마찬가지로, 동일한 애플리케이션 게이트웨이 배포에 동일한 부모 도메인의 여러 하위 도메인을 호스트할 수 있습니다. 예를 들어 단일 애플리케이션 게이트웨이 배포에 `http://blog.contoso.com` 및 `http://app.contoso.com`을 호스트할 수 있습니다.

## <a name="request-routing-rules-evaluation-order"></a>요청 회람 규칙 평가 순서

다중 사이트 수신기를 사용하는 동안, 클라이언트 트래픽이 정확한 백 엔드로 라우팅되도록 하려면 요청 회람 규칙을 올바른 순서로 지정하는 것이 중요합니다.
예를 들어, 연결된 호스트 이름이 각각 `*.contoso.com` 및 `shop.contoso.com`인 수신기가 2개 있는 경우, `shop.contoso.com` 호스트 이름을 가진 수신기는 `*.contoso.com`를 사용하여 수신기보다 먼저 처리되어야 합니다. `*.contoso.com`이 있는 수신기가 먼저 처리되는 경우, 더 구체적인 `shop.contoso.com` 수신기에서 클라이언트 트래픽을 수신하지 않습니다.

이 순서는 수신기와 연결된 요청 회람 규칙에 '우선 순위' 필드 값을 입력하여 설정할 수 있습니다. 1에서 20000까지의 정수 값을 지정할 수 있습니다. 1은 가장 높은 우선 순위이고 20000은 가장 낮은 우선 순위입니다. 들어오는 클라이언트 트래픽이 여러 수신기와 일치하는 경우, 우선 순위가 가장 높은 요청 회람 규칙이 요청을 제공하는 데 사용됩니다. 각 요청 라우팅 규칙에는 고유한 우선 순위 값이 있어야 합니다.

우선 순위 필드는 `PathBasedRouting` 요청 회람 규칙의 평가 순서에만 영향을 미치며, 요청 회람 규칙 내에서 경로 기반 규칙의 평가 순서는 변경되지 않습니다.

>[!NOTE]
>이 기능은 현재 [Azure PowerShell](tutorial-multiple-sites-powershell.md#add-priority-to-routing-rules) 및 [Azure CLI](tutorial-multiple-sites-cli.md#add-priority-to-routing-rules)를 통해서만 사용할 수 있습니다. 포털 지원은 곧 제공될 예정입니다.

>[!NOTE]
>규칙 우선 순위를 사용하려면, 모든 기존 요청 회람 규칙에 대한 규칙 우선 순위 필드 값을 지정해야 합니다. 일단 규칙 우선 순위 필드를 사용하면, 생성된 모든 새 회람 규칙에도 해당 구성의 일부로 규칙 우선 순위 필드 값이 있어야 합니다.

## <a name="wildcard-host-names-in-listener-preview"></a>수신기의 와일드카드 호스트 이름(미리 보기)

애플리케이션 게이트웨이에서는 다중 사이트 HTTP(S) 수신기를 사용하여 호스트 기반 라우팅을 수행할 수 있습니다. 이제 호스트 이름에 별표(*) 및 물음표(?) 같은 와일드카드 문자를 사용할 수 있으며 다중 사이트 HTTP(S) 수신기마다 최대 5개의 호스트 이름을 사용할 수 있습니다. 예들 들어 `*.contoso.com`입니다.

호스트 이름에 와일드카드 문자를 사용하면 단일 수신기에서 여러 호스트 이름을 일치시킬 수 있습니다. 예를 들어, `*.contoso.com`은 `ecom.contoso.com`, `b2b.contoso.com`, `customer1.b2b.contoso.com` 등과 일치시킬 수 있습니다. 호스트 이름 배열을 사용하면 하나의 수신기에 대해 두 개 이상의 호스트 이름을 구성하여 요청을 백 엔드 풀로 라우팅할 수 있습니다. 예를 들어 수신기에 `contoso.com, fabrikam.com`을 포함할 수 있으며, 이 경우 호스트 이름 둘 다에 대해 요청을 수락합니다.

:::image type="content" source="./media/multiple-site-overview/wildcard-listener-diag.png" alt-text="와일드카드 수신기":::

>[!NOTE]
> 이 기능은 미리 보기로 제공되며 Application Gateway Standard_v2 및 WAF_v2 SKU에만 사용할 수 있습니다. 미리 보기에 대한 자세한 내용은 [사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

[Azure PowerShell](tutorial-multiple-sites-powershell.md)에서는 `-HostName` 대신 `-HostNames`를 사용해야 합니다. HostNames를 사용하면 최대 5개의 호스트 이름을 쉼표로 구분된 값으로 사용할 수 있고 와일드카드 문자를 사용할 수 있습니다. 예를 들어 `-HostNames "*.contoso.com","*.fabrikam.com"`

[Azure CLI](tutorial-multiple-sites-cli.md)에서는 `--host-name` 대신 `--host-names`를 사용해야 합니다. host-names를 사용하면 최대 5개의 호스트 이름을 쉼표로 구분된 값으로 사용할 수 있고 와일드카드 문자를 사용할 수 있습니다. 예를 들어 `--host-names "*.contoso.com,*.fabrikam.com"`

Azure Portal 다중 사이트 수신기에서 **다중/와일드카드** 호스트 유형을 선택하여 허용되는 와일드카드 문자가 있는 최대 5개의 호스트 이름을 기재해야 합니다.

:::image type="content" source="./media/multiple-site-overview/wildcard-listener-example.png" alt-text="와일드카드 수신기 UI":::

### <a name="allowed-characters-in-the-host-names-field"></a>호스트 이름 필드에 허용되는 문자

* `(A-Z,a-z,0-9)` - 영숫자 문자
* `-` - 하이픈 또는 빼기
* `.` - 구분 기호로 사용되는 마침표
* `*` - 허용되는 범위에서 여러 문자와 일치 가능함
* `?` - 허용되는 범위에서 단일 문자와 일치 가능함

### <a name="conditions-for-using-wildcard-characters-and-multiple-host-names-in-a-listener"></a>수신기에서 와일드카드 문자 및 여러 호스트 이름을 사용하는 경우의 조건

* 단일 수신기에서는 최대 5개의 호스트 이름만 사용할 수 있습니다.
* 별표(`*`)는 도메인 스타일 이름 또는 호스트 이름의 구성 요소에 한 번만 사용될 수 있습니다. 예를 들어 component1 *.component2*.component3이 있습니다. `(*.contoso-*.com)`는 유효합니다.
* 호스트 이름에는 최대 두 개의 별표(`*`)가 있을 수 있습니다. 예를 들어 `*.contoso.*`는 유효하고 `*.contoso.*.*.com`은 잘못된 것입니다.
* 호스트 이름에는 와일드카드 문자가 최대 4개만 사용될 수 있습니다. 예를 들어 `????.contoso.com`, `w??.contoso*.edu.*`는 유효하지만 `????.contoso.*`는 잘못된 것입니다.
* 호스트 이름 구성 요소에 별표(`*`)와 물음표(`?`)를 함께 사용하는 것(`*?`, `?*` 또는 `**`)은 잘못된 것입니다. 예를 들어 `*?.contoso.com` 및 `**.contoso.com`은 잘못된 것입니다.

### <a name="considerations-and-limitations-of-using-wildcard-or-multiple-host-names-in-a-listener"></a>수신기에서 와일드카드 또는 여러 호스트 이름을 사용하는 경우의 고려 사항 및 제한 사항

* [SSL 종료 및 엔드투엔드 SSL](ssl-overview.md)을 사용하려면 프로토콜을 HTTPS로 구성하고 수신기 구성에 사용할 인증서를 업로드해야 합니다. 다중 사이트 수신기인 경우 호스트 이름도 입력할 수 있습니다. 일반적으로 SSL 인증서의 CN입니다. 수신기에 여러 호스트 이름을 지정하거나 와일드카드 문자를 사용하는 경우 다음 사항을 고려해야 합니다.
    * *.contoso.com 같은 와일드카드 호스트 이름인 경우 *.contoso.com 같은 CN이 포함된 와일드카드 인증서를 업로드해야 합니다.
    * 동일한 수신기에서 여러 호스트 이름을 사용하는 경우 언급한 호스트 이름과 일치하는 CN이 있는 SAN 인증서(주체 대체 이름)를 업로드해야 합니다.
* 호스트 이름을 언급하는 데 정규식을 사용할 수 없습니다. 호스트 이름 패턴을 구성하는 데는 별표(*) 및 물음표(?) 같은 와일드카드 문자만 사용할 수 있습니다.
* 백 엔드 상태 검사의 경우 HTTP 설정에 대해 여러 [사용자 지정 프로브](application-gateway-probe-overview.md)를 연결할 수 없습니다. 대신 백 엔드에서 웹 사이트 중 하나를 검색하거나 “127.0.0.1”을 사용하여 백 엔드 서버의 localhost를 검색할 수 있습니다. 하지만 수신기에서 와일드카드 또는 여러 호스트 이름을 사용할 경우 모든 지정된 도메인 패턴에 대한 요청이 규칙 유형(기본 또는 경로 기반)에 따라 백 엔드 풀로 라우팅됩니다.
* “hostname” 속성은 하나의 문자열을 입력으로 사용합니다. 이 경우 와일드카드가 없는 도메인 이름 하나만 사용할 수 있습니다. “hostnames”는 문자열 배열을 입력으로 사용합니다. 이 경우 와일드카드 도메인 이름을 5개까지 사용할 수 있습니다. 하지만 두 속성을 동시에 사용할 수는 없습니다.
* 와일드카드 또는 여러 호스트 이름을 사용하는 대상 수신기가 포함된 [리디렉션](redirect-overview.md) 규칙은 생성할 수 없습니다.

다중 사이트 수신기에서 와일드카드 호스트 이름을 구성하는 방법에 대한 단계별 가이드는 [Azure PowerShell을 사용하여 다중 사이트 만들기](tutorial-multiple-sites-powershell.md) 또는 [Azure CLI 사용](tutorial-multiple-sites-cli.md)을 참조하세요.



## <a name="host-headers-and-server-name-indication-sni"></a>호스트 헤더 및 SNI(서버 이름 표시)

동일한 인프라에서 여러 사이트를 호스트하도록 설정할 수 있는 세 가지 일반적인 메커니즘이 있습니다.

1. 여러 웹 애플리케이션 각각을 고유한 IP 주소에서 호스트합니다.
2. 동일한 IP 주소에서 호스트 이름을 사용하여 여러 웹 애플리케이션을 호스트합니다.
3. 동일한 IP 주소에서 다른 포트를 사용하여 여러 웹 애플리케이션을 호스트합니다.

현재 애플리케이션 게이트웨이는 단일 공용 IP 주소를 지원하며 이 위치에서 트래픽을 수신 대기합니다. 따라서 각각 고유한 IP 주소가 있는 여러 애플리케이션은 현재 지원되지 않습니다.

애플리케이션 게이트웨이는 각각 서로 다른 포트에서 수신 대기하는 여러 애플리케이션을 지원하지만 이 시나리오에서는 애플리케이션이 비표준 포트에서 트래픽을 허용해야 합니다.

Application Gateway는 HTTP 1.1 호스트 헤더를 기반으로 동일한 공용 IP 주소 및 포트에서 둘 이상의 웹 사이트를 호스트합니다. 애플리케이션 게이트웨이에서 호스트되는 사이트는 SNI(서버 이름 표시) TLS 확장을 통해 TLS 오프로드를 지원할 수도 있습니다. 따라서 이 시나리오는 클라이언트 브라우저 및 백 엔드 웹 팜은 RFC 6066에 정의된 대로 HTTP/1.1 및 TLS 확장을 지원해야 함을 의미합니다.

## <a name="next-steps"></a>다음 단계

애플리케이션 게이트웨이에서 다중 사이트 호스트를 구성하는 방법을 알아봅니다.
* [Azure Portal 사용](create-multiple-sites-portal.md)
* [Azure PowerShell 사용](tutorial-multiple-sites-powershell.md)
* [Azure CLI 사용](tutorial-multiple-sites-cli.md)

종단 간 템플릿 기반 배포는 [다중 사이트 호스팅을 사용하는 Resource Manager 템플릿](https://github.com/Azure/azure-quickstart-templates/tree/master/quickstarts/microsoft.network/application-gateway-multihosting)을 방문하세요.

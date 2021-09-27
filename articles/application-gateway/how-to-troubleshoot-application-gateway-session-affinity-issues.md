---
title: 세션 선호도 문제 해결
titleSuffix: Azure Application Gateway
description: 이 문서에서는 Azure Application Gateway에서 세션 선호도 문제를 해결하는 방법에 대한 정보를 제공합니다.
services: application-gateway
author: KumudD
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 11/14/2019
ms.author: kumud
ms.openlocfilehash: b175aedc743eb724b42a025839b7e32f59dea5da
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124737896"
---
# <a name="troubleshoot-azure-application-gateway-session-affinity-issues"></a>Azure Application Gateway 세션 선호도 문제 해결

Azure Application Gateway를 사용하여 세션 선호도 문제를 진단하고 해결하는 방법을 알아봅니다.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>개요

쿠키 기반 세션 선호도 기능은 동일한 서버에서 사용자 세션을 유지하려는 경우에 유용합니다. Application Gateway는 게이트웨이 관리형 쿠키를 사용하여 사용자 세션에서 동일한 서버에 후속 트래픽을 처리하도록 지시할 수 있습니다. 이는 세션 상태가 사용자 세션의 서버에 로컬로 저장된 경우에 특히 중요합니다.

## <a name="possible-problem-causes"></a>가능한 문제 원인

쿠키 기반 세션 선호도 유지 문제는 다음과 같은 주요 원인으로 인해 발생할 수 있습니다.

- "쿠키 기반 선호도" 설정이 사용하도록 설정되어 있지 않습니다.
- 애플리케이션이 쿠키 기반 선호도를 처리할 수 없습니다.
- 애플리케이션이 쿠키 기반 선호도를 사용하고 있지만 요청이 백 엔드 서버 간에 계속 반송됩니다.

### <a name="check-whether-the-cookie-based-affinity-setting-is-enabled"></a>"쿠키 기반 선호도" 설정이 사용하도록 설정되어 있는지 확인합니다.

경우에 따라 "쿠키 기반 선호도" 설정을 사용하도록 설정하는 것을 잊은 경우 세션 선호도 문제가 발생할 수 있습니다. Azure Portal의 HTTP 설정 탭에서 "쿠키 기반 선호도" 설정을 사용하도록 설정했는지 여부를 확인하려면 다음 지침을 따르세요.

1. [Azure 포털](https://portal.azure.com/)에 로그온합니다.

2. **왼쪽 탐색** 창에서 **모든 리소스** 를 클릭합니다. 모든 리소스 블레이드에서 Application Gateway 이름을 클릭합니다. 선택한 구독에 이미 여러 리소스가 있는 경우 **이름으로 필터링...** 에 Application Gateway 이름을 입력할 수 있습니다. 애플리케이션 게이트웨이에 간편하게 액세스할 수 있는 상자입니다.

3. **설정** 에서 **HTTP 설정** 탭을 선택합니다.

   ![H T T P 설정이 선택된 설정을 보여 주는 스크린샷.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-1.png)

4. 오른쪽에서 **appGatewayBackendHttpSettings** 를 클릭하여 쿠키 기반 선호도를 **사용하도록 설정** 했는지 확인합니다.

   ![쿠키 기반 선호도가 선택되었는지 여부를 포함하여 앱 게이트웨이에 대한 게이트웨이 설정을 보여 주는 스크린샷.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-2.jpg)



다음 방법 중 하나를 사용하여 "**backendHttpSettingsCollection**"에서 "**CookieBasedAffinity**"의 값이 *사용하도록 설정* 된 것을 확인할 수도 있습니다.

- PowerShell에서 [Get-AzApplicationGatewayBackendHttpSetting](/powershell/module/az.network/get-azapplicationgatewaybackendhttpsetting) 실행
- Azure Resource Manager 템플릿을 사용하여 JSON 파일 살펴보기

```
"cookieBasedAffinity": "Enabled", 
```

### <a name="the-application-cannot-handle-cookie-based-affinity"></a>애플리케이션은 쿠키 기반 선호도를 처리할 수 없습니다.

#### <a name="cause"></a>원인

애플리케이션 게이트웨이는 쿠키를 사용하여 세션 기반 선호도만 수행할 수 있습니다.

#### <a name="workaround"></a>해결 방법

애플리케이션이 쿠키 기반 선호도를 처리할 수 없는 경우 외부 또는 내부 Azure Load Balancer 또는 다른 타사 솔루션을 사용해야 합니다.

### <a name="application-is-using-cookie-based-affinity-but-requests-still-bouncing-between-back-end-servers"></a>애플리케이션이 쿠키 기반 선호도를 사용하고 있지만 요청이 백 엔드 서버 간에 계속 반송됩니다.

#### <a name="symptom"></a>증상

쿠키 기반 선호도 설정을 사용하도록 설정한 경우에도 Internet Explorer에서 짧은 이름 URL(예: `http://website`)을 사용하여 Application Gateway에 액세스할 때 요청은 여전히 백 엔드 서버 간에 반송됩니다.

이 문제를 식별하려면 다음 지침을 따르세요.

1. Application Gateway 뒤의 애플리케이션에 연결하는 "클라이언트"에서 웹 디버거 추적을 사용합니다(이 예제에서는 Fiddler 사용).
    **팁** Fiddler를 사용하는 방법을 모르는 경우 맨 아래에 있는 "**네트워크 트래픽을 수집하고 웹 디버거를 사용하여 분석하려고 합니다**." 옵션을 선택합니다.

2. 세션 로그를 확인하고 분석하여 클라이언트가 제공한 쿠키에 ARRAffinity 세부 정보가 있는지 확인합니다. 쿠키 집합 내에서 "**ARRAffinity=** *ARRAffinityValue*"와 같은 ARRAffinity 세부 정보를 찾지 못하면 클라이언트가 Application Gateway에 의해 제공된 ARRA 쿠키를 사용하여 응답하지 않음을 의미합니다.
    예를 들면 다음과 같습니다.

    ![단일 항목이 강조 표시된 세션 로그를 보여 주는 스크린샷.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-3.png)

    ![쿠키 정보를 포함하여 H T T P에 대한 요청 헤더를 보여 주는 스크린샷.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-4.png)

애플리케이션은 회신을 받을 때까지 각 요청에 쿠키를 설정하려고 계속 시도합니다.

#### <a name="cause"></a>원인

이 문제는 Internet Explorer 및 기타 브라우저에서 짧은 이름 URL로 쿠키를 저장하거나 사용하지 않을 수 있기 때문에 발생합니다.

#### <a name="resolution"></a>해결 방법

이 이슈를 해결하려면 FQDN을 사용하여 Application Gateway에 액세스해야 합니다. 예를 들어 [http://website.com](https://website.com/) 또는 [http://appgw.website.com](http://website.com/)을 사용합니다.

## <a name="additional-logs-to-troubleshoot"></a>문제 해결을 위한 추가 로그

추가 로그를 수집하고 분석하여 쿠키 기반 세션 선호도와 관련된 문제를 해결할 수 있습니다.

### <a name="analyze-application-gateway-logs"></a>Application Gateway 로그 분석

Application Gateway 로그를 수집하려면 다음 지침을 따르세요.

Azure Portal을 통한 로깅 사용

1. [Azure Portal](https://portal.azure.com/)에서 리소스를 찾고 **진단 로그** 를 클릭합니다.

   Application Gateway의 경우 액세스 로그, 성능 로그, 방화벽 로그의 세 가지 로그를 사용할 수 있습니다.

2. 데이터 수집을 시작하려면 **진단 켜기** 를 클릭합니다.

   ![진단 로그가 선택된 애플리케이션 게이트웨이를 보여 주는 스크린샷.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-5.png)

3. **진단 설정** 블레이드에서는 진단 로그에 대한 설정을 제공합니다. 이 예제에서 Log Analytics는 로그를 저장합니다. **Log Analytics** 에서 **구성** 을 클릭하여 작업 영역을 설정합니다. 또한 이벤트 허브 및 스토리지 계정을 사용하여 진단 로그를 저장할 수도 있습니다.

   ![Log Analytics 구성이 선택된 진단 설정 창을 보여 주는 스크린샷.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-6.png)

4. 설정을 확인하고 **저장** 을 클릭합니다.

   ![저장이 선택된 진단 설정 창을 보여 주는 스크린샷.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-7.png)

#### <a name="view-and-analyze-the-application-gateway-access-logs"></a>Application Gateway 액세스 로그 보기 및 분석

1. Application Gateway 리소스 보기의 Azure Portal에서 **모니터링** 섹션의 **진단 로그** 를 선택합니다.

   ![진단 로그가 선택된 모니터링을 보여 주는 스크린샷.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-8.png)

2. 오른쪽에서 **로그 범주** 의 드롭다운 목록에서 "**ApplicationGatewayAccessLog**"를 선택합니다.  

   ![ApplicationGatewayAccessLog가 선택된 로그 범주 드롭다운 목록을 보여 주는 스크린샷.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-9.png)

3. Application Gateway 액세스 로그 목록에서 분석하고 내보낼 로그를 클릭한 다음 JSON 파일을 내보냅니다.

4. 3단계에서 내보낸 JSON 파일을 CSV 파일로 변환하고 Excel, Power BI 또는 기타 데이터 시각화 도구에서 봅니다.

5. 다음 데이터를 확인합니다.

- **ClientIP** - 연결 클라이언트의 클라이언트 IP 주소입니다.
- **ClientPort** - 요청에 대한 연결 클라이언트의 소스 포트입니다.
- **RequestQuery** - 요청이 수신된 대상 서버를 나타냅니다.
- **Server-Routed**: 요청이 수신된 백 엔드 풀 인스턴스입니다.
- **X-AzureApplicationGateway-LOG-ID**: 요청에 사용된 상관 관계 ID입니다. 백 엔드 서버에서 트래픽 문제를 해결하는 데 사용할 수 있습니다. 예: X-AzureApplicationGateway-CACHE-HIT=0&SERVER-ROUTED=10.0.2.4.

  - **SERVER-STATUS**: Application Gateway에서 백 엔드로부터 받은 HTTP 응답 코드

  ![일반 텍스트(대부분 가려짐)로 서버 상태를 보여 주고 clientPort와 SERVER-ROUTED가 강조 표시된 스크린샷.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-11.png)

두 항목이 동일한 ClientIP 및 클라이언트 포트에서 제공되고 동일한 백 엔드 서버로 전송되는 경우 Application Gateway가 올바르게 구성되었음을 의미합니다.

두 항목이 동일한 ClientIP 및 클라이언트 포트에서 발생하고 다른 백 엔드 서버에 전송되는 경우, 이는 백 엔드 서버 간에 요청이 반송된다는 것을 의미합니다. "**애플리케이션에서 쿠키 기반 선호도를 사용하고 있지만 백 엔드 서버 간에 요청이 여전히 반송됨**"을 선택하여 문제를 해결합니다.

### <a name="use-web-debugger-to-capture-and-analyze-the-http-or-https-traffics"></a>웹 디버거를 사용하여 HTTP 또는 HTTPS 트래픽 캡처 및 분석

Fiddler와 같은 웹 디버깅 도구를 사용하면 인터넷과 테스트 컴퓨터 간의 네트워크 트래픽을 캡처하여 웹 애플리케이션을 디버그할 수 있습니다. 이러한 도구를 사용하면 브라우저가 데이터를 수신하거나 전송할 때의 수신 및 발신 데이터를 검사할 수 있습니다. 이 예에서 Fiddler에는 특히 인증 관련 문제에 대해 웹 애플리케이션의 클라이언트 측 문제를 해결하는 데 도움이 될 수 있는 HTTP 다시 재생 옵션이 있습니다.

원하는 웹 디버거를 사용합니다. 이 샘플에서는 Fiddler를 사용하여 http 또는 https 트래픽을 캡처하고 분석합니다. 다음 지침을 따릅니다.

1. <https://www.telerik.com/download/fiddler>에서 Fiddler 도구를 다운로드합니다.

    > [!NOTE]
    > 캡처하는 컴퓨터에 .NET 4가 설치된 경우 Fiddler4를 선택합니다. 그렇지 않으면 Fiddler2를 선택합니다.

2. 설치 실행 파일을 마우스 오른쪽 단추로 클릭하고 관리자 권한으로 실행하여 설치합니다.

    ![관리자 권한으로 실행이 선택된 상황에 맞는 메뉴가 있는 Fiddler 도구 설정 프로그램을 보여 주는 스크린샷.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-12.png)

3. Fiddler를 열면 트래픽 캡처가 자동으로 시작됩니다(왼쪽 하단 모서리의 캡처 참고). F12 키를 눌러 트래픽 캡처를 시작하거나 중지합니다.

    ![캡처 표시기가 강조 표시된 Fiddler 웹 디버거를 보여 주는 스크린샷.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-13.png)

4. 대부분의 경우 암호 해독된 HTTPS 트래픽에 관심이 있으며 **도구** > **Fiddler 옵션** 을 선택하고 "**HTTPS 트래픽 암호 해독**" 확인란을 선택하여 HTTPS 암호 해독을 사용하도록 설정할 수 있습니다.

    ![H T T P가 선택되고 HTTPS 트래픽 암호 해독이 선택된 Fiddler의 옵션을 보여 주는 스크린샷.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-14.png)

5. 다음 스크린샷과 같이 **X**(아이콘) > **모두 제거** 를 클릭하여 문제를 재현하기 전에 관련 없는 이전 세션을 제거할 수 있습니다. 

    ![X 아이콘이 선택되고 모두 제거 옵션이 표시된 상태를 보여 주는 스크린샷.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-15.png)

6. 문제를 재현한 후 **파일** > **저장** > **모든 세션..** 을 선택하여 검토할 파일을 저장합니다. 

    ![파일 저장 모든 세션 옵션이 선택된 상태를 보여 주는 스크린샷.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-16.png)

7. 세션 로그를 확인하고 분석하여 문제가 무엇인지 확인합니다.

    예:

- **예제 A:** 요청이 클라이언트에서 전송되고 Application Gateway의 공개 IP 주소로 이동하는 세션 로그를 찾은 경우 이 로그를 클릭하여 세부 정보를 확인합니다.  오른쪽 하단 상자의 데이터는 Application Gateway가 클라이언트에 반환하는 데이터입니다. "원시" 탭을 선택하고 클라이언트가 "**Set-Cookie: ARRAffinity =** *ARRAffinityValue*"를 수신하는지 여부를 확인합니다. 쿠키가 없으면 세션 선호도가 설정되지 않았거나 Application Gateway가 클라이언트에 쿠키를 다시 적용하지 않는 것입니다.

   > [!NOTE]
   > 이 ARRAffinity 값은 Application Gateway에서 클라이언트가 특정 백 엔드 서버로 전송되도록 설정하는 쿠키 ID입니다.

   ![Set-Cookie 값이 강조 표시된 로그 항목의 세부 정보 예를 보여 주는 스크린샷.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-17.png)

- **예제 B:** 이전 세션 로그 다음에 오는 다음 세션 로그는 ARRAAFFINITY를 설정한 Application Gateway에 다시 응답하는 클라이언트입니다. ARRAffinity 쿠키 ID가 일치하면 패킷은 이전에 사용한 것과 동일한 백 엔드 서버로 보내져야 합니다. 다음 몇 줄의 http 통신을 확인하여 클라이언트의 ARRAffinity 쿠키가 변경되는지 확인합니다.

   ![쿠키가 강조 표시된 로그 항목의 세부 정보 예를 보여 주는 스크린샷.](./media/how-to-troubleshoot-application-gateway-session-affinity-issues/troubleshoot-session-affinity-issues-18.png)

> [!NOTE]
> 동일한 통신 세션에 대해 쿠키가 변경되지 않아야 합니다. 오른쪽 상단의 상자를 선택하고, "쿠키" 탭을 선택하여 클라이언트에서 쿠키를 사용하고 다시 Application Gateway으로 보내는지 여부를 확인합니다. 그렇지 않은 경우 클라이언트 브라우저는 쿠키를 유지하거나 대화를 위해 사용하고 있지 않은 것입니다. 때로는 클라이언트가 거짓말을 할 수도 있습니다.

 

## <a name="next-steps"></a>다음 단계

앞의 단계에서 문제가 해결되지 않으면 [지원 티켓](https://azure.microsoft.com/support/options/)을 엽니다.
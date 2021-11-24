---
title: Azure Firewall 프리미엄 배포 및 구성
description: Azure Firewall 프리미엄을 배포 및 구성하는 방법에 대해 알아봅니다.
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 11/24/2021
ms.author: victorh
ms.openlocfilehash: bb4bbe05db857e11cc0af00944446c76206e7476
ms.sourcegitcommit: e9e332a512ed615a3c8ad5a11baa21649f14116d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133097984"
---
# <a name="deploy-and-configure-azure-firewall-premium"></a>Azure Firewall 프리미엄 배포 및 구성


 Azure Firewall 프리미엄은 매우 민감하고 통제된 환경에 필요한 기능을 갖춘 차세대 방화벽입니다. 이 관리 팩에는 다음과 같은 기능이 포함되어 있습니다.

- **TLS 검사** - 아웃바운드 트래픽을 해독하고, 데이터를 처리한 다음 데이터를 암호화하고 대상에 보냅니다.
- **IDPS** - 네트워크 침입 감지 및 방지 시스템(IDPS)을 사용하면 악의적인 활동의 네트워크 활동을 모니터링 하고, 이 활동에 대한 정보를 기록하고, 보고하고, 필요 시 차단할 수 있습니다.
- **URL 필터링** - 전체 URL을 고려하도록 Azure Firewall의 FQDN 필터링 기능을 확장합니다. 예를 들어 `www.contoso.com`이 아닌 `www.contoso.com/a/c`입니다.
- **웹 범주** - 관리자는 도박 웹 사이트, 소셜 미디어 웹 사이트 등의 웹 사이트 범주에 대한 사용자 액세스를 허용하거나 거부할 수 있습니다.

자세한 내용은 [Azure Firewall 프리미엄 기능](premium-features.md)을 참조하세요.

템플릿을 사용하여 세 개의 서브넷이 있는 중앙 VNet(10.0.0.0/16)을 포함하는 테스트 환경을 배포합니다.
- 작업자 서브넷(10.0.10.0/24)
- Azure Bastion 서브넷(10.0.20.0/24)
- 방화벽 서브넷(10.0.100.0/24)

이 테스트 환경에서는 간단한 단일 중앙 VNet을 사용합니다. 프로덕션 용도로는 피어링된 VNet을 사용하는 [허브 및 스포크 토폴로지](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)가 더 일반적입니다.

:::image type="content" source="media/premium-deploy/premium-topology.png" alt-text="중앙 VNet 토폴로지":::

작업자 가상 머신은 방화벽을 통해 HTTP/S 요청을 전송하는 클라이언트입니다.

## <a name="prerequisites"></a>필수 구성 요소

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="deploy-the-infrastructure"></a>인프라 배포

이 템플릿은 IDPS, TLS 검사, URL 필터링 및 웹 범주를 사용하여 Azure Firewall 프리미엄을 위한 전체 테스트 환경을 배포합니다.

- 핵심 기능(IDPS, TLS 검사, URL 필터링 및 웹 범주)의 유효성을 쉽게 검사할 수 있도록 미리 정의된 설정이 포함된 새로운 Azure Firewall 프리미엄 및 방화벽 정책은
- Key Vault 및 관리 ID를 비롯한 모든 종속성을 배포합니다. 프로덕션 환경에서는 해당 리소스가 이미 만들어져 동일한 템플릿에서 필요하지 않을 수도 있습니다.
- 자체 서명된 루트 CA를 생성하고 이를 생성된 Key Vault에 배포합니다.
-  파생된 중간 CA를 생성하고 Windows 테스트 가상 머신(WorkerVM)에 배포합니다.
- 베스천 호스트(BastionHost)도 배포되며 이를 Windows 테스트 머신(WorkerVM)에 연결하는 데 사용할 수 있습니다.



[![Azure에 배포](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.network%2Fazurefirewall-premium%2Fazuredeploy.json)

## <a name="test-the-firewall"></a>방화벽 테스트

이제 IDPS, TLS 검사, 웹 필터링 및 웹 범주를 테스트할 수 있습니다.

### <a name="add-firewall-diagnostics-settings"></a>방화벽 진단 설정 추가

방화벽 로그를 수집하려면 방화벽 로그를 수집하는 진단 설정을 추가해야 합니다.

1. **DemoFirewall** 을 선택하고 **모니터링** 에서 **진단 설정** 을 선택합니다.
2. **진단 설정 추가** 를 선택합니다.
3. **진단 설정 이름** 에 *fw-diag* 를 입력합니다.
4. **로그** 에서 **AzureFirewallApplicationRule** 및 **AzureFirewallNetworkRule** 을 선택합니다.
5. **대상 세부 정보** 에서 **Log Analytics 작업 영역으로 보내기** 를 선택합니다.
6. **저장** 을 선택합니다.

### <a name="idps-tests"></a>IDPS 테스트

IDPS를 테스트하려면 적절한 서버 인증서를 사용하여 자체 내부 웹 서버를 배포해야 합니다. Azure Firewall 프리미엄 인증서 요구 사항에 대한 자세한 내용은 [Azure Firewall 프리미엄 인증서](premium-certificates.md)를 참조하세요.

`curl`을 사용하여 다양한 HTTP 헤더를 제어하고 악의적인 트래픽을 시뮬레이션할 수 있습니다.

#### <a name="to-test-idps-for-http-traffic"></a>HTTP 트래픽에 대해 IDPS를 테스트하려면 다음을 수행합니다.

1. WorkerVM 가상 머신에서 관리자 명령 프롬프트 창을 엽니다.
2. 명령 프롬프트에서 다음 명령을 입력합니다.

   `curl -A "BlackSun" <your web server address>`
3. 웹 서버 응답이 표시됩니다.
4. Azure Portal의 방화벽 네트워크 규칙 로그로 이동하여 다음 메시지와 유사한 경고를 찾습니다.

   :::image type="content" source="media/premium-deploy/alert-message.png" alt-text="경고 메시지":::

   > [!NOTE]
   > 데이터가 로그에 표시되기 시작하는 데 다소 시간이 걸릴 수 있습니다. 로그가 데이터를 표시 하기 시작 하는 데 몇 분 정도 걸릴 수 있습니다.
5. 서명 2008983에 대한 서명 규칙을 추가합니다.

   1. **DemoFirewallPolicy** 를 선택하고 **설정** 에서 **IDPS** 를 선택합니다.
   1. **서명 규칙** 탭을 선택합니다.
   1. **서명 ID** 에서 열린 텍스트 상자에 *2008983* 을 입력합니다.
   1. **모드** 에서 **거부** 를 선택합니다.
   1. **저장** 을 선택합니다.
   1. 배포가 완료될 때까지 기다렸다가 계속 진행합니다.



6. WorkerVM에서 `curl` 명령을 다시 실행합니다.

   `curl -A "BlackSun" <your web server address>`

   이제 HTTP 요청이 방화벽에 의해 차단되므로 연결 시간 제한이 만료되면 다음 출력이 표시됩니다.

   `read tcp 10.0.100.5:55734->10.0.20.10:80: read: connection reset by peer`

7. Azure Portal의 모니터 로그로 이동하여 차단된 요청에 대한 메시지를 찾습니다.
<!---8. Now you can bypass the IDPS function using the **Bypass list**.

   1. On the **IDPS (preview)** page, select the **Bypass list** tab.
   2. Edit **MyRule** and set **Destination** to *10.0.20.10, which is the ServerVM private IP address.
   3. Select **Save**.
1. Run the test again: `curl -A "BlackSun" http://server.2020-private-preview.com` and now you should get the `Hello World` response and no log alert. --->

#### <a name="to-test-idps-for-https-traffic"></a>IDPS를 HTTPS 트래픽에 대해 테스트하려는 경우

HTTP 대신 HTTPS를 사용하여 관련 curl 테스트를 반복합니다. 예를 들면 다음과 같습니다.

`curl --ssl-no-revoke -A "BlackSun" <your web server address>`

HTTP 테스트와 동일한 결과가 표시되어야 합니다.

### <a name="tls-inspection-with-url-filtering"></a>URL 필터링을 사용하여 TLS 검사

URL 필터링을 사용하여 TLS 검사를 테스트하려면 다음 단계를 따릅니다.

1. 방화벽 정책 애플리케이션 규칙을 편집하고 `AllowURL`이라는 새 규칙을 `AllowWeb` 규칙 컬렉션에 추가합니다. 대상 URL(`www.nytimes.com/section/world`), 원본 IP 주소 **\* *_, 대상 유형 _* URL** 을 구성하고 **TLS 검사** 및 프로토콜(**http, https**)을 선택합니다.

3. 배포가 완료되면 WorkerVM에서 브라우저를 열고 `https://www.nytimes.com/section/world`로 이동하여 브라우저에서 HTML 응답이 예상대로 표시되는지 확인합니다.
4. Azure Portal에서는 애플리케이션 규칙 모니터링 로그에서 전체 URL을 볼 수 있습니다.

      :::image type="content" source="media/premium-deploy/alert-message-url.png" alt-text="URL을 표시하는 경고 메시지":::

일부 HTML 페이지는 거부된 다른 URL을 참조하므로 불완전하게 보일 수 있습니다. 이 문제를 해결하기 위해 다음 방법을 사용할 수 있습니다.

- HTML 페이지에 다른 도메인에 대한 링크가 포함되어 있는 경우 해당 FQDN에 대한 액세스를 허용하는 새 애플리케이션 규칙에 관련 도메인을 추가할 수 있습니다.
- HTML 페이지에 하위 URL에 대한 링크가 포함되어 있는 경우 규칙을 수정하고 URL에 별표를 추가할 수 있습니다. 예: `targetURLs=www.nytimes.com/section/world*`

   또는 규칙에 새 URL을 추가할 수 있습니다. 예를 들면 다음과 같습니다. 

   `www.nytimes.com/section/world, www.nytimes.com/section/world/*`

### <a name="web-categories-testing"></a>웹 범주 테스트

스포츠 웹 사이트에 대한 액세스를 허용하는 애플리케이션 규칙을 만들어 보겠습니다.
1. 포털에서 리소스 그룹을 열고 **DemoFirewallPolicy** 를 선택합니다.
2. **애플리케이션 규칙** 을 선택한 다음 **규칙 컬렉션을 추가** 합니다.
3. **이름** 에 *GeneralWeb*, **우선 순위** *103*, **규칙 컬렉션 그룹** 을 입력하고 **DefaultApplicationRuleCollectionGroup** 을 입력합니다.
4. **규칙** 에서 **이름** 에 *AllowSports*, **원본** *\** , **프로토콜** *http, https* 를 입력하고 **TLS 검사**, **대상 유형** 을 선택하고 *웹 범주*, **대상** 을 선택하고 *스포츠* 를 선택합니다.
5. **추가** 를 선택합니다.

      :::image type="content" source="media/premium-deploy/web-categories.png" alt-text="스포츠 웹 범주":::
6. 배포가 완료되면 **WorkerVM** 으로 이동하여 웹 브라우저를 열고 `https://www.nfl.com`으로 이동합니다.

   NFL 웹 페이지가 표시되고 애플리케이션 규칙 로그에 **웹 범주: 스포츠** 규칙이 일치하고 요청이 허용되었음이 표시됩니다.

## <a name="next-steps"></a>다음 단계

- [Azure Portal의 Azure Firewall 프리미엄](premium-portal.md)
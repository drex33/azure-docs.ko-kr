---
title: 포털을 사용하여 사용자 지정 프로브 만들기
titleSuffix: Azure Application Gateway
description: 포털을 사용하여 Application Gateway에 대한 사용자 지정 프로브를 만드는 방법을 알아봅니다.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 07/09/2020
ms.author: victorh
ms.openlocfilehash: 1244cc0fdd9a5c978ee64c1aa7ce4af20272c818
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2021
ms.locfileid: "122634443"
---
# <a name="create-a-custom-probe-for-application-gateway-by-using-the-portal"></a>포털을 사용하여 Application Gateway에 대한 사용자 지정 프로브 만들기

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-create-probe-portal.md)
> * [Azure Resource Manager PowerShell](application-gateway-create-probe-ps.md)
> * [Azure 클래식 PowerShell](application-gateway-create-probe-classic-ps.md)

이 문서에서는 Azure Portal을 통해 기존 응용 프로그램 게이트웨이에 사용자 지정 상태 프로브를 추가합니다. 상태 프로브를 사용하여 Azure Application Gateway는 백 엔드 풀의 리소스 상태를 모니터링합니다.

## <a name="before-you-begin"></a>시작하기 전에

Application Gateway 아직 없는 경우 방문 [Application Gateway 만들기](./quick-create-portal.md)를 참조하여 사용할 Application Gateway를 만듭니다.

## <a name="create-probe-for-application-gateway-v2-sku"></a>Application Gateway v2 SKU용 프로브 만들기

프로브는 포털을 통해 두 단계 프로세스로 구성됩니다. 첫 번째 단계는 프로브 구성에 필요한 값을 입력하는 것입니다. 두 번째 단계에서는 이 프로브 구성을 사용하여 백 엔드 상태를 테스트하고 프로브를 저장합니다. 

### <a name="enter-probe-properties"></a><a name="createprobe"></a>프로브 속성 입력

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 아직 계정이 없는 경우 [1개월 평가판](https://azure.microsoft.com/free)에 등록할 수 있습니다.

2. Azure Portal의 [즐겨찾기] 창에서 [모든 리소스]를 클릭합니다. [모든 리소스] 블레이드에서 애플리케이션 게이트웨이를 클릭합니다. 선택한 구독에 이미 여러 리소스가 있는 경우 [이름으로 필터링...]에서 partners.contoso.net을 입력합니다. 애플리케이션 게이트웨이에 간편하게 액세스할 수 있는 상자입니다.

3. **상태 프로브** 를 선택한 다음 **추가** 를 선택하여 새 상태 프로브를 추가합니다.

   ![새 프로브 추가][4]

4. **상태 프로브 추가** 페이지에서 프로브에 필요한 정보를 입력하고, 완료되면 **확인** 을 클릭합니다.

   |**설정** | **값** | **세부 정보**|
   |---|---|---|
   |**이름**|customProbe|이 값은 포털에서 액세스할 수 있는 프로브에 주어지는 식별 이름입니다.|
   |**프로토콜**|HTTP 또는 HTTPS | 상태 프로브에서 사용하는 프로토콜입니다. |
   |**Host**|예: contoso.com|이 값은 응용 프로그램 서버에서 실행 중인 가상 호스트의 이름(VM 호스트 이름과는 다름)입니다. 프로브가 \<protocol\>://\<host name\>:\<port\>/\<urlPath\>에 전송됩니다. 이는 서버의 개인 IP, 공용 IP 주소 또는 공용 IP 주소의 DNS 항목일 수도 있습니다.  여기에서는 파일 기반 경로 항목과 함께 사용할 때 서버에 액세스하려고 시도하며 상태 검사로 서버에 특정 파일이 있는지 확인합니다.|
   |**백 엔드 HTTP 설정에서 호스트 이름 선택**|Yes 또는 No|프로브의 *호스트* 헤더를 이 프로브가 연결된 HTTP 설정의 호스트 이름으로 설정합니다. Azure 앱 서비스와 같은 다중 테넌트 백 엔드의 경우 특별히 필요합니다. [자세히 알아보기](./configuration-http-settings.md#pick-host-name-from-back-end-address)|
   |**백 엔드 HTTP 설정에서 포트 선택**| Yes 또는 No|상태 프로브의 *포트* 를 이 프로브가 연결된 HTTP 설정의 포트로 설정합니다. 아니요를 선택하면 사용할 사용자 지정 대상 포트를 입력할 수 있습니다 |
   |**포트**| 1-65535 | 상태 프로브에 사용할 사용자 지정 포트 | 
   |**경로**|/ 또는 모든 유효한 경로|사용자 지정 프로브의 전체 url 중 나머지 부분입니다. 유효한 경로는 '/'로 시작합니다. http:\//contoso.com의 기본 경로는 '/'만 사용합니다.  또한 웹 기반이 아닌 정적 상태 검사를 위해 파일에 서버 경로를 입력할 수 있습니다.  파일 경로는 공용/개인 IP 또는 공용 IP DNS 항목을 호스트 이름 항목으로 사용하는 동안 사용되어야 합니다.|
   |**간격(초)**|30|상태를 확인하기 위해 프로브가 실행되는 주기입니다. 30초 이하 값을 설정하는 것은 권장되지 않습니다.|
   |**시간 제한(초)**|30|시간이 초과되기 전에 프로브가 대기하는 시간입니다. 이 시간 제한 내에 유효한 응답을 받지 못하면 프로브가 실패로 표시됩니다. 시간 제한 간격은 http 호출이 백 엔드 상태 페이지를 사용하도록 설정할 수 있을만큼 충분히 높아야 합니다. 시간 제한 값은 이 프로브 설정에 사용된 '간격' 값 또는 이 프로브와 연결될 HTTP 설정의 '요청 시간 제한' 값보다 커서는 안 됩니다.|
   |**비정상 임계값**|3|비정상으로 간주되는 연속으로 실패한 시도 횟수입니다. 임계값은 1 이상으로 설정할 수 있습니다.|
   |**프로브 일치 조건 사용**|Yes 또는 No|기본적으로 상태 코드가 200-399인 HTTP(S) 응답은 정상으로 간주됩니다. 백 엔드 응답 코드 또는 백 엔드 응답 본문의 허용 가능한 범위를 변경할 수 있습니다. [자세히 알아보기](./application-gateway-probe-overview.md#probe-matching)|
   |**HTTP 설정**|드롭다운에서 선택|프로브는 여기에서 선택한 HTTP 설정과 연결되므로 선택한 HTTP 설정과 연결된 해당 백 엔드 풀의 상태를 모니터링합니다. 선택한 HTTP 설정에서 사용되는 것과 같은 검색 요청에 대해 동일한 포트를 사용합니다. 다른 사용자 지정 프로브와 연결되지 않은 HTTP 설정만 선택할 수 있습니다. <br>이 프로브 구성에서 선택한 프로토콜과 동일한 프로토콜이 있고 *백 엔드 HTTP 설정에서 호스트 이름 선택* 스위치의 상태가 동일한 HTTP 설정만 연결에 사용할 수 있습니다.|
   
   > [!IMPORTANT]
   > 프로브는 하나 이상의 HTTP 설정에 연결된 경우에만 백 엔드의 상태를 모니터링합니다. 이 프로브가 연결된 HTTP 설정과 연결된 해당 백 엔드 풀의 백 엔드 리소스를 모니터링합니다. 프로브 요청은 \<protocol\>://\<hostName\>:\<port\>/\<urlPath\>으로 전송됩니다.

### <a name="test-backend-health-with-the-probe"></a>프로브를 사용하여 백 엔드 상태 테스트

프로브 속성을 입력한 후 백 엔드 리소스의 상태를 테스트하여 프로브 구성이 올바르고 백 엔드 리소스가 예상대로 작동하는지 확인할 수 있습니다.

1. **테스트** 를 선택하고 프로브 결과를 확인합니다. Application 게이트웨이는 이 프로브에 사용되는 HTTP 설정과 연결된 백 엔드 풀에 있는 모든 백 엔드 리소스의 상태를 테스트합니다. 

   ![백 엔드 상태 테스트][5]

2. 비정상 백 엔드 리소스가 있는 경우 **세부 정보** 열을 확인하여 리소스의 비정상 상태에 대한 이유를 파악합니다. 잘못된 프로브 구성으로 인해 리소스가 비정상으로 표시되면 **프로브로 돌아가기** 링크를 선택해 프로브 구성을 편집합니다. 반대로 백 엔드에서의 문제로 인해 리소스가 비정상으로 표시되면 백 엔드 리소스와 관련된 문제를 해결한 다음, **프로브로 돌아가기** 링크를 선택하고 **테스트** 를 선택하여 백 엔드를 다시 테스트합니다.

   > [!NOTE]
   > 비정상 백 엔드 리소스를 사용하는 경우에도 프로브를 저장하도록 선택할 수 있지만 권장하지 않습니다. Application Gateway는 프로브에 의해 비정상으로 확인되는 백 엔드 풀에서 백 엔드 서버로 요청을 전달하지 않기 때문입니다. 백 엔드 풀에 정상 리소스가 없는 경우 응용 프로그램에 액세스할 수 없으며 HTTP 502 오류가 발생합니다.

   ![프로브 결과 보기][6]

3. **추가** 를 선택하여 프로브를 저장합니다. 

## <a name="create-probe-for-application-gateway-v1-sku"></a>Application Gateway v1 SKU용 프로브 만들기

프로브는 포털을 통해 두 단계 프로세스로 구성됩니다. 첫 번째 단계는 프로브를 만드는 것입니다. 두 번째 단계로 애플리케이션 게이트웨이의 백 엔드 http 설정에 프로브를 추가합니다.

### <a name="create-the-probe"></a><a name="createprobe"></a>프로브 만들기

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 아직 계정이 없는 경우 [1개월 평가판](https://azure.microsoft.com/free)에 등록할 수 있습니다.

2. Azure Portal의 즐겨찾기 창에서 **모든 리소스** 를 선택합니다. 응용 프로그램 게이트웨이를 **모든 리소스** 페이지에서 선택합니다. 선택한 구독에 이미 여러 리소스가 있는 경우 [이름으로 필터링...]에서 partners.contoso.net을 입력합니다. 애플리케이션 게이트웨이에 간편하게 액세스할 수 있는 상자입니다.

3. **프로브** 를 선택한 다음 **추가** 를 선택하여 프로브를 추가합니다.

   ![정보가 입력된 프로브 추가 블레이드][1]

4. **상태 프로브 추가** 블레이드에서 프로브에 필요한 정보를 입력하고, 완료되면 **확인** 을 클릭합니다.

   |**설정** | **값** | **세부 정보**|
   |---|---|---|
   |**이름**|customProbe|이 값은 포털에서 액세스할 수 있는 프로브에 주어지는 식별 이름입니다.|
   |**프로토콜**|HTTP 또는 HTTPS | 상태 프로브에서 사용하는 프로토콜입니다. |
   |**Host**|예: contoso.com|이 값은 응용 프로그램 서버에서 실행 중인 가상 호스트의 이름(VM 호스트 이름과는 다름)입니다. 프로브는(protocol)://(host name):(port from httpsetting)/urlPath로 전송됩니다.  이는 다중 사이트가 Application Gateway에 구성된 경우에 적용됩니다. Application Gateway가 단일 사이트에 대해 구성된 경우 '127.0.0.1'을 입력합니다.  또한 웹 기반이 아닌 정적 상태 검사를 위해 파일에 서버 경로를 입력할 수 있습니다. 파일 경로는 공용/개인 IP 또는 공용 IP DNS 항목을 호스트 이름 항목으로 사용하는 동안 사용되어야 합니다.|
   |**백 엔드 HTTP 설정에서 호스트 이름 선택**|Yes 또는 No|프로브의 *호스트* 헤더를 이 프로브가 연결된 HTTP 설정과 연결된 백 엔드 풀의 백 엔드 리소스 호스트 이름으로 설정합니다. Azure 앱 서비스와 같은 다중 테넌트 백 엔드의 경우 특별히 필요합니다. [자세히 알아보기](./configuration-http-settings.md#pick-host-name-from-back-end-address)|
   |**경로**|/ 또는 모든 유효한 경로|사용자 지정 프로브의 전체 url 중 나머지 부분입니다. 유효한 경로는 '/'로 시작합니다. http:\//contoso.com의 기본 경로는 '/'만 사용합니다. 또한 웹 기반 대신 정적 상태 검사를 위해 파일에 대한 서버 경로를 입력할 수 있습니다.  파일 경로는 공용/개인 IP 또는 공용 IP DNS 항목을 호스트 이름 항목으로 사용하는 동안 사용되어야 합니다.|
   |**간격(초)**|30|상태를 확인하기 위해 프로브가 실행되는 주기입니다. 30초 이하 값을 설정하는 것은 권장되지 않습니다.|
   |**시간 제한(초)**|30|시간이 초과되기 전에 프로브가 대기하는 시간입니다. 이 시간 제한 내에 유효한 응답을 받지 못하면 프로브가 실패로 표시됩니다. 시간 제한 간격은 http 호출이 백 엔드 상태 페이지를 사용하도록 설정할 수 있을만큼 충분히 높아야 합니다. 시간 제한 값은 이 프로브 설정에 사용된 '간격' 값 또는 이 프로브와 연결될 HTTP 설정의 '요청 시간 제한' 값보다 커서는 안 됩니다.|
   |**비정상 임계값**|3|비정상으로 간주되는 연속으로 실패한 시도 횟수입니다. 임계값은 1 이상으로 설정할 수 있습니다.|
   |**프로브 일치 조건 사용**|Yes 또는 No|기본적으로 상태 코드가 200-399인 HTTP(S) 응답은 정상으로 간주됩니다. 백 엔드 응답 코드 또는 백 엔드 응답 본문의 허용 가능한 범위를 변경할 수 있습니다. [자세히 알아보기](./application-gateway-probe-overview.md#probe-matching)|

   > [!IMPORTANT]
   > 호스트 이름은 서버 이름과 같지 않습니다. 이 값은 애플리케이션 서버에서 실행 중인 가상 호스트의 이름입니다. 프로브는 \<protocol\>://\<hostName\>\<port from http settings\>/\<urlPath\> 로 전송됩니다

### <a name="add-probe-to-the-gateway"></a>게이트웨이에 프로브 추가

프로브를 만들었으므로 이제 게이트웨이에 추가해야 합니다. 프로브 설정은 애플리케이션 게이트웨이의 백 엔드 http 설정에 지정됩니다.

1. 애플리케이션 게이트웨이에서 **HTTP 설정** 을 클릭하여 구성 블레이드를 불러온 다음, 창에 나열된 현재 백 엔드 http 설정을 클릭합니다.

   ![https 설정 창][2]

2. **appGatewayBackEndHttpSettings** 설정 페이지에서 **사용자 지정 프로브 사용** 확인란을 선택하고 [프로브 만들기](#createprobe) 섹션에서 만든 프로브를 **사용자 지정 프로브** 드롭다운에서 선택합니다.
   완료되면 **저장** 을 클릭하여 해당 설정을 적용합니다.

## <a name="next-steps"></a>다음 단계

[백 엔드 상태 보기](./application-gateway-diagnostics.md#back-end-health)를 사용하여 프로브에 의해 결정된 대로 백 엔드 리소스의 상태를 봅니다.

[1]: ./media/application-gateway-create-probe-portal/figure1.png
[2]: ./media/application-gateway-create-probe-portal/figure2.png
[4]: ./media/application-gateway-create-probe-portal/figure4.png
[5]: ./media/application-gateway-create-probe-portal/figure5.png
[6]: ./media/application-gateway-create-probe-portal/figure6.png

---
title: Azure Application Gateway의 백 엔드 상태 문제 해결
description: Azure Application Gateway의 백 엔드 상태 문제를 해결하는 방법 설명
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 06/09/2020
ms.author: victorh
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: b78119f5be4d3ee3cf4fab0a8363d27abd224efc
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/23/2021
ms.locfileid: "132934594"
---
# <a name="troubleshoot-backend-health-issues-in-application-gateway"></a>Application Gateway의 백 엔드 상태 문제 해결

## <a name="overview"></a>개요


기본적으로 Azure Application Gateway는 백 엔드 서버를 검색하여 상태를 확인하고 요청을 처리할 준비가 되었는지 여부를 확인합니다. 사용자는 호스트 이름, 검색할 경로 및 정상으로 허용할 상태 코드를 언급하는 사용자 지정 프로브를 만들 수도 있습니다. 각 경우에 백 엔드 서버가 성공적으로 응답하지 않으면 Application Gateway는 서버를 비정상으로 표시하고 요청을 서버로 전달하는 것을 중지합니다. 서버 응답이 성공적으로 시작되면 Application Gateway에서 요청 전달을 다시 시작합니다.

### <a name="how-to-check-backend-health"></a>백 엔드 상태를 확인하는 방법

백 엔드 풀의 상태를 확인하기 위해 Azure Portal에서 **백 엔드 상태** 페이지를 사용할 수 있습니다. 또는 [Azure PowerShell](/powershell/module/az.network/get-azapplicationgatewaybackendhealth), [CLI](/cli/azure/network/application-gateway#az_network_application_gateway_show_backend_health) 또는 [REST API](/rest/api/application-gateway/applicationgateways/backendhealth)를 사용할 수 있습니다.

이러한 방법으로 검색되는 상태는 다음 중 하나일 수 있습니다.

- Healthy

- 비정상

- 알 수 없음

서버의 백 엔드 상태가 정상이면 Application Gateway가 해당 서버에 요청을 전달하는 것입니다. 그러나 백 엔드 풀의 모든 서버에 대한 백 엔드 상태가 비정상이거나 알 수 없음인 경우 애플리케이션에 액세스하려고 할 때 문제가 발생할 수 있습니다. 이 문서에서는 표시되는 각 오류에 대한 증상, 원인 및 해결 방법을 설명합니다.

## <a name="backend-health-status-unhealthy"></a>백 엔드 상태: 비정상

백 엔드 상태가 비정상이면 포털 보기는 다음 스크린샷과 유사하게 표시됩니다.

![Application Gateway 백 엔드 상태 - 비정상](./media/application-gateway-backend-health-troubleshooting/appgwunhealthy.png)

또는 Azure PowerShell, CLI 또는 Azure REST API 쿼리를 사용하는 경우 다음과 같은 응답을 받게 됩니다.
```azurepowershell
PS C:\Users\testuser\> Get-AzApplicationGatewayBackendHealth -Name "appgw1" -ResourceGroupName "rgOne"
BackendAddressPools :
{Microsoft.Azure.Commands.Network.Models.PSApplicationGatewayBackendHealthPool}
BackendAddressPoolsText : [
{
                              "BackendAddressPool": {
                                "Id": "/subscriptions/536d30b8-665b-40fc-bd7e-68c65f816365/resourceGroups/rgOne/providers/Microsoft.Network/applicationGateways/appgw1/b
                          ackendAddressPools/appGatewayBackendPool"
                              },
                              "BackendHttpSettingsCollection": [
                                {
                                  "BackendHttpSettings": {
                                    "TrustedRootCertificates": [],
                                    "Id": "/subscriptions/536d30b8-665b-40fc-bd7e-68c65f816365/resourceGroups/rgOne/providers/Microsoft.Network/applicationGateways/appg
                          w1/backendHttpSettingsCollection/appGatewayBackendHttpSettings"
                                  },
                                  "Servers": [
                                    {
                                      "Address": "10.0.0.5",
                                      "Health": "Healthy"
                                    },
                                    {
                                      "Address": "10.0.0.6",
                                      "Health": "Unhealthy"
                                    }
                                  ]
                                }
                              ]
                            }
                        ]
```

백 엔드 풀의 모든 서버에 대한 비정상 백 엔드 서버 상태를 받으면 요청이 서버로 전달되지 않고 Application Gateway는 요청하는 클라이언트에 "502 잘못된 게이트웨이" 오류를 반환합니다. 이 문제를 해결하려면 **백 엔드 상태** 탭에서 **세부 정보** 열을 확인합니다.

**세부 정보** 열에 표시되는 메시지는 문제에 대한 보다 자세한 인사이트를 제공하고, 이에 따라 문제 해결을 시작할 수 있습니다.

> [!NOTE]
> 기본 프로브 요청은 \<protocol\>://127.0.0.1:\<port\>/ 형식으로 전송됩니다. 예를 들어 포트 80에 대한 http 프로브의 경우 http://127.0.0.1:80 입니다. 200~399의 HTTP 상태 코드만 정상으로 간주됩니다. 프로토콜 및 대상 포트는 HTTP 설정에서 상속됩니다. Application Gateway가 다른 프로토콜, 호스트 이름 또는 경로를 검색하고 다른 상태 코드를 정상으로 인식하기를 원하면 사용자 지정 프로브를 구성하고 HTTP 설정과 연결합니다.

## <a name="error-messages"></a>오류 메시지


### <a name="backend-server-timeout"></a>백 엔드 서버 시간 초과

**메시지:** 백 엔드가 Application Gateway의 상태 검색에 응답하는 데 걸리는 시간이 프로브 설정의 제한 시간 임계값보다 깁니다.

**원인:** Application Gateway는 백 엔드 서버에 대한 HTTP(S) 프로브 요청을 보낸 후 구성된 기간 동안 백 엔드 서버의 응답을 기다립니다. 백 엔드 서버가 구성된 기간(제한 시간 값) 내에 응답하지 않으면 구성된 제한 시간 기간 내에 응답을 다시 시작할 때까지 비정상으로 표시됩니다.

**해결 방법:** 구성된 제한 시간 기간 내에 백 엔드 서버 또는 애플리케이션이 응답하지 않는 이유를 확인하고 애플리케이션 종속성도 확인합니다. 예를 들어 데이터베이스에 응답 지연 시간을 트리거할 수 있는 문제가 있는지 확인합니다. 애플리케이션의 동작을 알고 있고 제한 시간 값 이후에만 응답해야 하는 경우 사용자 지정 프로브 설정에서 제한 시간 값을 늘립니다. 제한 시간 값을 변경하려면 사용자 지정 프로브가 있어야 합니다. 사용자 지정 프로브를 구성하는 방법에 대한 자세한 내용은 [설명서 페이지](./application-gateway-create-probe-portal.md)를 참조하세요.

제한 시간 값을 늘리려면 다음 단계를 수행합니다.

1.  백 엔드 서버에 직접 액세스하여 서버가 해당 페이지에서 응답하는 데 걸린 시간을 확인합니다. 개발자 도구를 사용하는 브라우저를 비롯한 모든 도구를 사용하여 백 엔드 서버에 액세스할 수 있습니다.

1.  애플리케이션이 응답하는 데 걸린 시간을 확인한 후 **상태 프로브** 탭을 선택하고 HTTP 설정과 연결된 프로브를 선택합니다.

1.  애플리케이션 응답 시간(초)보다 큰 제한 시간 값을 입력합니다.

1.  사용자 지정 프로브 설정을 저장하고 백 엔드 상태가 지금 정상으로 표시되는지 확인합니다.

### <a name="dns-resolution-error"></a>DNS 확인 오류

**메시지:** Application Gateway에서 이 백 엔드에 대한 프로브를 만들 수 없습니다. 이 오류는 일반적으로 백 엔드의 FQDN이 올바르게 입력되지 않은 경우에 발생합니다. 

**원인:** 백 엔드 풀이 IP 주소/FQDN 또는 App Service 유형인 경우 Application Gateway는 DNS(Domain Name System)(사용자 지정 또는 Azure 기본값)를 통해 입력된 FQDN의 IP 주소로 확인되고 HTTP 설정에서 언급된 TCP 포트의 서버에 연결을 시도합니다. 그러나 이 메시지가 표시되는 경우는 Application Gateway에서 입력한 FQDN의 IP 주소를 성공적으로 확인할 수 없다는 것을 나타냅니다.

**해결 방법:**

1.  백 엔드 풀에 입력한 FQDN이 올바르고 공용 도메인인지 확인한 다음 로컬 컴퓨터에서 확인합니다.

1.  IP 주소를 확인할 수 있는 경우 가상 네트워크의 DNS 구성에 문제가 있을 수 있습니다.

1.  가상 네트워크가 사용자 지정 DNS 서버로 구성되어 있는지 확인합니다. 이 경우 지정된 FQDN의 IP 주소로 확인할 수 없는 이유에 대해 DNS 서버에서 확인합니다.

1.  Azure 기본 DNS를 사용하는 경우 도메인 이름 등록 기관에서 적절한 A 레코드 또는 CNAME 레코드 매핑이 완료되었는지 여부에 대해 확인합니다.

1.  도메인이 개인 또는 내부인 경우 동일한 가상 네트워크의 VM에서 확인해 보세요. 확인이 가능한 경우 Application Gateway를 다시 시작하고 다시 확인합니다. Application Gateway를 다시 시작하려면 연결된 이들 리소스에 설명된 PowerShell 명령을 사용하여 [중지](/powershell/module/azurerm.network/stop-azurermapplicationgateway)하고 [시작](/powershell/module/azurerm.network/start-azurermapplicationgateway)해야 합니다.

### <a name="updates-to-the-dns-entries-of-the-backend-pool"></a>백 엔드 풀의 DNS 항목에 대 한 업데이트

**메시지:** 백 엔드 상태를 검색할 수 없습니다. 이는 응용 프로그램 게이트웨이 서브넷의 NSG/UDR/방화벽이 v1 SKU의 경우 포트 65503-65534에서 트래픽을 차단 하는 경우 또는 v2 SKU의 경우 포트 65200-65535에서 IP 주소로 구성 된 FQDN을 확인할 수 없는 경우에 발생 합니다. 자세한 내용은을 참조 https://aka.ms/UnknownBackendHealth 하세요.

**원인:** Application Gateway는 시작 시 백 엔드 풀에 대 한 DNS 항목을 확인 하 고 실행 하는 동안 동적으로 업데이트 하지 않습니다.

**해결 방법:**

새 IP 주소 사용을 시작 하려면 백 엔드 서버 DNS 항목을 수정한 후에 Application Gateway를 다시 시작 해야 합니다.  이 작업은 Azure PowerShell 또는 Azure CLI를 통해 완료할 수 있습니다.

#### <a name="azure-powershell"></a>Azure PowerShell
```
# Get Azure Application Gateway
$appgw=Get-AzApplicationGateway -Name <appgw_name> -ResourceGroupName <rg_name>
 
# Stop the Azure Application Gateway
Stop-AzApplicationGateway -ApplicationGateway $appgw
 
# Start the Azure Application Gateway
Start-AzApplicationGateway -ApplicationGateway $appgw
```

#### <a name="azure-cli"></a>Azure CLI
```
# Stop the Azure Application Gateway
az network application-gateway stop -n <appgw_name> -g <rg_name>

# Start the Azure Application Gateway
az network application-gateway start -n <appgw_name> -g <rg_name>
```

### <a name="tcp-connect-error"></a>TCP 연결 오류

**메시지:** Application Gateway는 백 엔드에 연결할 수 없습니다.
프로브에 사용되는 포트에서 백 엔드가 응답하는지 확인하세요.
또한 NSG/UDR/방화벽이 이 백 엔드의 IP 및 포트에 대한 액세스를 차단하는지 여부도 확인해야 합니다.

**원인:** DNS 확인 단계 후에는 Application Gateway는 HTTP 설정에서 구성된 TCP 포트의 백 엔드 서버에 연결을 시도합니다. Application Gateway가 지정된 포트에서 TCP 세션을 설정할 수 없는 경우 프로브는 이 메시지와 함께 비정상으로 표시됩니다.

**해결 방법:** 이 오류가 표시되면 다음 단계를 수행합니다.

1.  브라우저 또는 PowerShell을 사용하여 HTTP 설정에 설명된 포트에서 백 엔드 서버에 연결할 수 있는지 여부를 확인합니다. 예를 들어 다음 명령을 실행합니다. `Test-NetConnection -ComputerName
    www.bing.com -Port 443`

1.  설명된 포트가 원하는 포트가 아닌 경우 백 엔드 서버에 연결할 Application Gateway의 올바른 포트 번호를 입력합니다.

1.  로컬 컴퓨터에서 포트에 연결할 수 없는 경우 다음을 수행합니다.

    a.  백 엔드 서버의 네트워크 어댑터 및 서브넷에 대한 NSG(네트워크 보안 그룹) 설정 및 구성된 포트에 대한 인바운드 연결이 허용되는지 확인합니다. 해당 연결이 허용되지 않은 경우 연결을 허용하는 새 규칙을 만듭니다. NSG 규칙을 만드는 방법을 알아보려면 [설명서 페이지를 참조](../virtual-network/tutorial-filter-network-traffic.md#create-security-rules)하세요.

    b.  Application Gateway 서브넷의 NSG 설정에서 아웃바운드 공용 및 개인 트래픽을 허용하는지 확인하여 연결을 설정할 수 있도록 합니다. NSG 규칙을 만드는 방법에 대해 자세히 알아보려면 3a 단계에서 제공하는 문서 페이지를 확인합니다.
    ```azurepowershell
            $vnet = Get-AzVirtualNetwork -Name "vnetName" -ResourceGroupName "rgName"
            Get-AzVirtualNetworkSubnetConfig -Name appGwSubnet -VirtualNetwork $vnet
    ```

    다.  모든 라우팅 비정상에 대해 Application Gateway의 UDR(사용자 정의 경로) 설정과 백 엔드 서버의 서브넷을 확인합니다. UDR이 백 엔드 서브넷에서 트래픽을 멀리 이동하지 않는지 확인합니다. 예를 들어 Azure ExpressRoute 및/또는 VPN을 통해 Application Gateway 서브넷에 보급되는 네트워크 가상 어플라이언스 경로 또는 기본 경로를 확인합니다.

    d.  네트워크 어댑터에 대한 유효 경로 및 규칙을 확인하기 위해 다음 PowerShell 명령을 사용할 수 있습니다.
    ```azurepowershell
            Get-AzEffectiveNetworkSecurityGroup -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
            Get-AzEffectiveRouteTable -NetworkInterfaceName "nic1" -ResourceGroupName "testrg"
    ```
1.  NSG 또는 UDR 관련 문제를 찾을 수 없는 경우 클라이언트에서 구성된 포트에 TCP 세션을 설정할 수 없도록 하는 애플리케이션 관련 문제가 있는지 백 엔드 서버를 확인합니다. 확인해야 할 몇 가지 사항은 다음과 같습니다.

    a.  명령 프롬프트(Win + R-\> cmd)를 열고 `netstat`를 입력한 다음 Enter 키를 선택합니다.

    b.  서버가 구성된 포트에서 수신 대기하고 있는지 여부를 확인합니다. 다음은 그 예입니다.
    ```
            Proto Local Address Foreign Address State PID
            TCP 0.0.0.0:80 0.0.0.0:0 LISTENING 4
    ```
    다.  구성된 포트에서 수신 대기하고 있지 않으면 웹 서버 설정을 확인합니다. 예: IIS의 사이트 바인딩, NGINX의 서버 블록 및 Apache의 가상 호스트

    d.  OS 방화벽 설정을 점검하여 포트에 들어오는 트래픽이 허용되는지 확인합니다.

#### <a name="http-status-code-mismatch"></a>HTTP 상태 코드 불일치

**메시지:** 백 엔드의 HTTP 응답의 상태 코드가 프로브 설정과 일치하지 않습니다. Expected:{HTTPStatusCode0} Received:{HTTPStatusCode1}.

**원인:** TCP 연결을 설정하고 TLS 핸드셰이크를 수행한 후(TLS를 사용하도록 설정된 경우) Application Gateway는 프로브를 백 엔드 서버에 HTTP GET 요청으로 보냅니다. 앞에서 설명한 것처럼 기본 프로브는 \<protocol\>://127.0.0.1:\<port\>/로 하고 200~399 범위의 응답 상태 코드를 정상으로 간주합니다. 서버에서 다른 상태 코드를 반환하는 경우 이 메시지와 함께 비정상으로 표시됩니다.

**해결 방법:** 백 엔드 서버의 응답 코드에 따라 다음 단계를 수행할 수 있습니다. 몇 가지 일반적인 상태 코드는 다음과 같습니다.

| **오류** | **actions** |
| --- | --- |
| 프로브 상태 코드 불일치: 401 수신됨 | 백 엔드 서버에서 인증이 필요한지 여부를 확인합니다. Application Gateway 프로브는 인증을 위해 자격 증명을 전달할 수 없습니다. 프로브 상태 코드 일치에서 \"HTTP 401\"을 허용하거나 서버가 인증을 요구하지 않는 경로를 검색합니다. |
| 프로브 상태 코드 불일치: 403 수신됨 | 액세스가 금지되었습니다. 백 엔드 서버에서 경로에 대한 액세스가 허용되는지 여부를 확인합니다. |
| 프로브 상태 코드 불일치: 404 수신됨 | 페이지를 찾을 수 없습니다. 백 엔드 서버에서 호스트 이름 경로에 액세스할 수 있는지 여부를 확인합니다. 호스트 이름 또는 경로 매개 변수를 액세스할 수 있는 값으로 변경합니다. |
| 프로브 상태 코드 불일치: 405 수신됨 | Application Gateway에 대한 프로브 요청은 HTTP GET 메서드를 사용합니다. 서버에서 이 메서드를 허용하는지 여부를 확인합니다. |
| 프로브 상태 코드 불일치: 500 수신됨 | 내부 서버 오류. 백 엔드 서버의 상태와 서비스가 실행 중인지 여부를 확인합니다. |
| 프로브 상태 코드 불일치: 503 수신됨 | 서비스를 사용할 수 없습니다. 백 엔드 서버의 상태와 서비스가 실행 중인지 여부를 확인합니다. |

또는 응답이 합법적인 것으로 생각하고 Application Gateway가 다른 상태 코드를 정상으로 허용하기를 원하는 경우 사용자 지정 프로브를 만들 수 있습니다. 이 방법은 백 엔드 웹 사이트에 인증이 필요한 경우에 유용합니다. 프로브 요청이 사용자 자격 증명을 전달하지 않기 때문에 실패하고 백 엔드 서버에서 HTTP 401 상태 코드가 반환됩니다.

사용자 지정 프로브를 만들려면 [다음 단계](./application-gateway-create-probe-portal.md)를 수행합니다.

### <a name="http-response-body-mismatch"></a>HTTP 응답 본문 불일치

**메시지:** 백 엔드의 HTTP 응답 본문이 프로브 설정과 일치하지 않습니다. 받은 응답 본문에 {string}이(가) 없습니다.

**원인:** 사용자 지정 프로브를 만들 때 응답 본문의 문자열을 일치시켜 백 엔드 서버를 정상으로 표시하는 옵션을 사용할 수 있습니다. 예를 들어, 일치하는 문자열로 "권한 없음"을 허용하도록 Application Gateway를 구성할 수 있습니다. 프로브 요청에 대한 백 엔드 서버 응답에 **권한 없음** 문자열이 포함된 경우 정상으로 표시됩니다. 그렇지 않으면 이 메시지와 함께 비정상으로 표시됩니다.

**해결 방법:** 이 문제를 해결하려면 다음 단계를 따릅니다.

1.  검색 경로에서 로컬로 또는 클라이언트 컴퓨터에서 백 엔드 서버에 액세스하고 응답 본문을 확인합니다.

1.  Application Gateway 사용자 지정 프로브 구성의 응답 본문이 구성된 것과 일치하는지 확인합니다.

1.  일치하지 않는 경우 허용되는 올바른 문자열 값을 갖도록 프로브 구성을 변경합니다.

[Application Gateway 프로브 일치](./application-gateway-probe-overview.md#probe-matching)에 대해 자세히 알아봅니다.

>[!NOTE]
> 모든 TLS 관련 오류 메시지의 경우, SNI 동작 및 v1과 v2 SKU 간의 차이점에 대한 자세한 내용을 보려면 [TLS 개요](ssl-overview.md) 페이지를 확인하세요.


### <a name="backend-server-certificate-invalid-ca"></a>백 엔드 서버 인증서의 잘못된 CA

**메시지:** 백 엔드에서 사용되는 서버 인증서가 잘 알려진 CA(인증 기관)에서 서명하지 않았습니다. 백 엔드에서 사용되는 서버 인증서의 루트 인증서를 업로드하여 Application Gateway의 백 엔드를 허용합니다.

**원인:** Application Gateway v2를 사용하는 엔드투엔드 SSL을 사용하려면 백 엔드 서버의 인증서를 확인하여 서버가 정상 상태인지 확인해야 합니다.
TLS/SSL 인증서를 신뢰할 수 있도록 하려면 Application Gateway의 신뢰할 수 있는 저장소에 포함된 CA에서 백 엔드 서버의 인증서를 발급해야 합니다. 인증서가 신뢰할 수 있는 CA에서 발급되지 않은 경우(예: 자체 서명된 인증서 사용) 사용자는 발급자의 인증서를 Application Gateway에 업로드해야 합니다.

**해결 방법:** 다음 단계를 수행하여 신뢰할 수 있는 루트 인증서를 Application Gateway로 내보내고 업로드합니다. (이러한 단계는 Windows 클라이언트용입니다.)

1.  해당 애플리케이션이 호스트되는 컴퓨터에 로그인합니다.

1.  Win+R을 선택하거나 **시작** 단추를 마우스 오른쪽 단추로 클릭한 다음 **실행** 을 선택합니다.

1.  `certmgr.msc`를 입력하고 Enter 키를 선택합니다. **시작** 메뉴에서 인증서 관리자를 검색할 수도 있습니다.

1.  인증서(일반적으로 `\Certificates - Current User\\Personal\\Certificates\`에 위치)를 찾아 엽니다.

1.  루트 인증서를 선택한 다음 **인증서 보기** 를 선택합니다.

1.  인증서 속성에서 **세부 정보** 탭을 선택합니다.

1.  **세부 정보** 탭에서 **파일에 복사** 옵션을 선택하고 Base-64로 인코딩된 x.509(.CER) 형식으로 파일을 저장합니다.

1.  Azure Portal의 Application Gateway HTTP **설정** 페이지를 엽니다.

1. HTTP 설정을 열고 **인증서 추가** 를 선택하고 방금 저장한 인증서 파일을 찾습니다.

1. **저장** 을 선택하여 HTTP 설정을 저장합니다.

또는 브라우저를 통해 서버에 직접 액세스하고(Application Gateway 무시) 브라우저에서 루트 인증서를 내보내 클라이언트 컴퓨터에서 루트 인증서를 내보낼 수 있습니다.

Application Gateway에서 신뢰할 수 있는 루트 인증서를 추출하고 업로드하는 방법에 대한 자세한 내용은 [신뢰할 수 있는 루트 인증서 내보내기(v2 SKU용)](./certificates-for-backend-authentication.md#export-trusted-root-certificate-for-v2-sku)를 참조하세요.

### <a name="trusted-root-certificate-mismatch"></a>신뢰할 수 있는 루트 인증서 불일치

**메시지:** 백 엔드에서 사용되는 서버 인증서의 루트 인증서가 Application Gateway에 추가된 신뢰할 수 있는 루트 인증서와 일치하지 않습니다. 올바른 루트 인증서를 추가하여 백 엔드를 허용합니다.

**원인:** Application Gateway v2를 사용하는 엔드투엔드 SSL을 사용하려면 백 엔드 서버의 인증서를 확인하여 서버가 정상 상태인지 확인해야 합니다.
TLS/SSL 인증서를 신뢰할 수 있도록 하려면 Application Gateway의 신뢰할 수 있는 저장소에 포함된 CA에서 백 엔드 서버의 인증서를 발급해야 합니다. 인증서가 신뢰할 수 있는 CA에서 발급되지 않은 경우(예: 자체 서명된 인증서 사용) 사용자는 발급자의 인증서를 Application Gateway에 업로드해야 합니다.

Application Gateway HTTP 설정에 업로드된 인증서는 백 엔드 서버 인증서의 루트 인증서와 일치해야 합니다.

**해결 방법:** 이 오류 메시지가 표시되면 Application Gateway에 업로드된 인증서와 백 엔드 서버에 업로드된 인증서가 일치하지 않는 것입니다.

Application Gateway에 신뢰할 수 있는 올바른 루트 인증서를 업로드하려면 이전 메서드의 1-11 단계를 수행합니다.

Application Gateway에서 신뢰할 수 있는 루트 인증서를 추출하고 업로드하는 방법에 대한 자세한 내용은 [신뢰할 수 있는 루트 인증서 내보내기(v2 SKU용)](./certificates-for-backend-authentication.md#export-trusted-root-certificate-for-v2-sku)를 참조하세요.

> [!NOTE]
> 이 오류는 백 엔드 서버가 TLS 핸드셰이크 중 루트 > 중간(해당하는 경우) > 리프를 포함하여 전체 인증서 체인을 교환하지 않는 경우에도 발생할 수 있습니다. 확인하려면 모든 클라이언트에서 OpenSSL 명령을 사용하고 Application Gateway 프로브에서 구성된 설정을 사용하여 백 엔드 서버에 연결할 수 있습니다.

다음은 그 예입니다.
```
OpenSSL> s_client -connect 10.0.0.4:443 -servername www.example.com -showcerts
```

출력에 반환되는 인증서의 전체 체인이 표시되지 않으면 루트 인증서를 포함하여 전체 체인으로 인증서를 다시 내보냅니다. 백 엔드 서버에서 해당 인증서를 구성합니다. 

```
  CONNECTED(00000188)\
  depth=0 OU = Domain Control Validated, CN = \*.example.com\
  verify error:num=20:unable to get local issuer certificate\
  verify return:1\
  depth=0 OU = Domain Control Validated, CN = \*.example.com\
  verify error:num=21:unable to verify the first certificate\
  verify return:1\
  \-\-\-\
  Certificate chain\
   0 s:/OU=Domain Control Validated/CN=*.example.com\
     i:/C=US/ST=Arizona/L=Scottsdale/O=GoDaddy.com, Inc./OU=http://certs.godaddy.com/repository//CN=Go Daddy Secure Certificate Authority - G2\
  \-----BEGIN CERTIFICATE-----\
  xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx\
  \-----END CERTIFICATE-----
```

### <a name="backend-certificate-invalid-common-name-cn"></a>백 엔드 인증서의 잘못된 CN(일반 이름)

**메시지:** 백 엔드 인증서의 CN(일반 이름)이 프로브의 호스트 헤더와 일치하지 않습니다.

**원인:** Application Gateway는 백 엔드 HTTP 설정에 지정된 호스트 이름이 백 엔드 서버의 TLS/SSL 인증서가 제공하는 CN의 호스트 이름과 일치하는지 여부를 확인합니다. Standard_v2 및 WAF_v2 SKU(V2) 동작입니다. 표준 및 WAF SKU의 (v1) SNI(서버 이름 표시)는 백 엔드 풀 주소에서 FQDN으로 설정됩니다. SNI 동작과 v1과 v2 SKU 간의 차이점에 대한 자세한 내용은 [Application Gateway를 사용한 TLS 종료 및 엔드투엔드 TLS 개요](ssl-overview.md)를 참조하세요.

v2 SKU에서 기본 프로브(사용자 지정 프로브를 구성하고 연결하지 않음)가 있는 경우 HTTP 설정에서 언급된 호스트 이름에서 SNI가 설정됩니다. 또는 백 엔드 주소 풀에 올바른 FQDN이 포함된 HTTP 설정에 “백 엔드 주소에서 호스트 이름 선택”이 언급되어 있으면 이 설정이 적용됩니다.

HTTP 설정과 연결된 사용자 지정 프로브가 있는 경우 사용자 지정 프로브 구성에서 언급된 호스트 이름에서 SNI가 설정됩니다. 또는 사용자 지정 프로브에서 **백 엔드 HTTP 설정에서 호스트 이름 선택** 이 선택된 경우 HTTP 설정에 언급된 호스트 이름에서 SNI가 설정됩니다.

HTTP 설정에서 **백 엔드 주소에서 호스트 이름 선택** 이 설정된 경우 백 엔드 주소 풀에 유효한 FQDN이 포함되어야 합니다.

이 오류 메시지가 표시되면 백 엔드 인증서의 CN이 사용자 지정 프로브 또는 HTTP 설정에 구성된 호스트 이름과 일치하지 않는 것입니다(**백 엔드 HTTP 설정에서 호스트 이름 선택** 이 선택된 경우). 기본 프로브를 사용하는 경우 호스트 이름이 **127.0.0.1** 로 설정됩니다. 원하는 값이 아닌 경우 사용자 지정 프로브를 만들어 HTTP 설정과 연결해야 합니다.

**해결 방법:**

이 문제를 해결하려면 다음 단계를 수행합니다.

Windows의 경우:

1.  해당 애플리케이션이 호스트되는 컴퓨터에 로그인합니다.

1.  Win+R을 선택하거나 **시작** 단추를 마우스 오른쪽 단추로 클릭하고 **실행** 을 선택합니다.

1.  **certmgr.exe** 를 입력하고 Enter 키를 선택합니다. **시작** 메뉴에서 인증서 관리자를 검색할 수도 있습니다.

1.  인증서를 찾아(일반적으로 `\Certificates - Current User\\Personal\\Certificates`에 위치) 엽니다.

1.  **세부 정보** 탭에서 인증서 **주제** 를 확인합니다.

1.  세부 정보에서 인증서의 CN을 확인하고 사용자 지정 프로브의 호스트 이름 필드에 또는 HTTP 설정에 동일한 이름을 입력합니다(**백 엔드 HTTP 설정에서 호스트 이름 선택** 이 선택된 경우). 웹 사이트에 원하는 호스트 이름이 아닌 경우 해당 도메인에 대한 인증서를 얻거나 사용자 지정 프로브 또는 HTTP 설정 구성에 올바른 호스트 이름을 입력해야 합니다.

OpenSSL을 사용하는 Linux의 경우:

1.  OpenSSL에서 다음 명령을 실행합니다.
    ```
    openssl x509 -in certificate.crt -text -noout
    ```

2.  표시된 속성에서 인증서의 CN을 찾고 http 설정의 호스트 이름 필드에 동일하게 입력합니다. 웹 사이트에 원하는 호스트 이름이 아닌 경우 해당 도메인에 대한 인증서를 얻거나 사용자 지정 프로브 또는 HTTP 설정 구성에 올바른 호스트 이름을 입력해야 합니다.

### <a name="backend-certificate-is-invalid"></a>잘못된 백 엔드 인증서

**메시지:** 백 엔드 인증서가 잘못되었습니다. 현재 날짜가 인증서의 \"유효 기간(시작)\" 및 \"유효 기간(끝)\" 날짜 범위 내에 없습니다.

**원인:** 모든 인증서에는 유효 범위가 제공되며 서버의 TLS/SSL 인증서가 유효하지 않으면 HTTPS 연결이 안전하지 않습니다. 현재 데이터는 **유효 기간(시작)** 및 **유효 기간(끝)** 범위 내에 있어야 합니다. 그렇지 않으면 인증서가 유효하지 않은 것으로 간주되고, Application Gateway가 백 엔드 서버를 비정상으로 표시하는 보안 문제가 발생합니다.

**해결 방법:** TLS/SSL 인증서가 만료된 경우 공급업체에 인증서를 갱신하고 새 인증서를 사용하여 서버 설정을 업데이트합니다. 자체 서명된 인증서인 경우 유효한 인증서를 생성하고 Application Gateway HTTP 설정에 루트 인증서를 업로드해야 합니다. 이렇게 하려면 다음 단계를 수행하세요.

1.  포털에서 Application Gateway HTTP 설정을 엽니다.

1.  만료된 인증서가 있는 설정을 선택하고 **인증서 추가** 를 선택하고 새 인증서 파일을 엽니다.

1.  인증서 옆에 있는 **삭제** 아이콘을 사용하여 이전 인증서를 제거한 다음 **저장** 을 선택합니다.

### <a name="certificate-verification-failed"></a>인증서 확인 실패

**메시지:** 백 엔드 인증서의 유효성을 확인할 수 없습니다. 이유를 확인하려면 오류 코드{errorCode}와 관련된 메시지에 대한 OpenSSL 진단을 확인합니다.

**원인:** 이 오류는 Application Gateway가 인증서의 유효성을 확인할 수 없는 경우에 발생합니다.

**해결 방법:** 이 문제를 해결하려면 서버의 인증서가 제대로 생성되었는지 확인합니다. 예를 들어 [OpenSSL](https://www.openssl.org/docs/manmaster/man1/verify.html)을 사용하여 인증서 및 해당 속성을 확인한 다음 인증서를 Application Gateway HTTP 설정으로 다시 업로드할 수 있습니다.

## <a name="backend-health-status-unknown"></a>백 엔드 상태: 알 수 없음

백 엔드 상태가 알 수 없음으로 표시되면 포털 보기는 다음 스크린샷과 유사하게 표시됩니다.

![Application Gateway 백 엔드 상태 - 알 수 없음](./media/application-gateway-backend-health-troubleshooting/appgwunknown.png)

다음 이유 중 하나 이상으로 인해 이 동작이 발생할 수 있습니다.

1.  Application Gateway 서브넷의 NSG가 "인터넷"에서 65503-65534(v1 SKU) 또는 65200-65535(v2 SKU) 포트에 대한 인바운드 액세스를 차단하고 있습니다.
1.  Application Gateway 서브넷의 UDR은 기본 경로(0.0.0.0/0)로 설정되고 다음 홉은 "인터넷"으로 지정되지 않습니다.
1.  기본 경로는 BGP를 통한 가상 네트워크에 대한 ExpressRoute/VPN 연결에 의해 보급됩니다.
1.  사용자 지정 DNS 서버가 공용 도메인 이름을 확인할 수 없는 가상 네트워크에 구성되어 있습니다.
1.  Application Gateway는 비정상 상태입니다.

**해결 방법:**

1.  NSG가 **인터넷** 에서 65503-65534(v1 SKU) 또는 65200-65535(v2 SKU) 포트에 대한 액세스를 차단하는지 여부를 확인합니다.

    a.  Application Gateway **개요** 탭에서 **가상 네트워크/서브넷** 링크를 선택합니다.

    b.  가상 네트워크의 **서브넷** 탭에서 Application Gateway가 배포된 서브넷을 선택합니다.

    다.  NSG가 구성되어 있는지 여부를 확인합니다.

    d.  NSG가 구성된 경우 **검색** 탭 또는 **모든 리소스** 에서 해당 NSG 리소스를 검색합니다.

    e.  **인바운드 규칙** 섹션에서 **원본** 이 **Any** 또는 **인터넷** 으로 설정된 65503-65534 v1 SKU 또는 65200-65535 v2 SKU에 대한 대상 포트 범위를 허용하는 인바운드 규칙을 추가합니다.

    f.  **저장** 을 선택하고 백 엔드를 정상으로 볼 수 있는지 확인합니다. 또는 [PowerShell/CLI](../virtual-network/manage-network-security-group.md)를 통해 수행할 수 있습니다.

1.  UDR에 다음 홉이 **인터넷** 으로 설정되지 않은 기본 경로(0.0.0.0/0)가 있는지 여부를 확인합니다.
    
    a.  1a 및 1b 단계를 수행하여 서브넷을 결정합니다.

    b.  구성된 UDR이 있는지 여부를 확인합니다. 있는 경우 검색 창에서 또는 **모든 리소스** 에서 리소스를 검색합니다.

    다.  다음 홉이 **인터넷** 으로 설정되지 않은 기본 경로(0.0.0.0/0)가 있는지 여부를 확인합니다. 설정이 **가상 어플라이언스** 또는 **가상 네트워크 게이트웨이** 인 경우에는 가상 어플라이언스 또는 온-프레미스 디바이스에서 패킷을 수정하지 않고 다시 인터넷 대상으로 패킷을 올바르게 라우팅할 수 있는지 확인해야 합니다.

    d.  그렇지 않으면 다음 홉을 **인터넷** 으로 변경하고 **저장** 을 선택하고 백 엔드 상태를 확인합니다.

1.  기본 경로는 BGP를 통한 가상 네트워크에 대한 ExpressRoute/VPN 연결에 의해 보급됩니다.

    a.  BGP를 통해 가상 네트워크에 대한 ExpressRoute/VPN 연결이 있는 경우 및 기본 경로를 보급하는 경우에는 패킷을 수정하지 않고 인터넷 대상으로 다시 라우팅해야 합니다. Application Gateway 포털에서 **연결 문제 해결** 옵션을 사용하여 확인할 수 있습니다.

    b.  1\.1.1.1과 같은 인터넷 라우팅 가능한 IP 주소로 대상을 수동으로 선택합니다. 대상 포트를 모든 항목으로 설정하고 연결을 확인합니다.

    다.  다음 홉이 가상 네트워크 게이트웨이인 경우 ExpressRoute 또는 VPN을 통해 보급된 기본 경로가 있을 수 있습니다.

1.  가상 네트워크에 구성된 사용자 지정 DNS 서버가 있으면 서버에서 공용 도메인을 확인할 수 있는지 확인합니다. 공용 도메인 이름 확인은 Application Gateway에서 OCSP 서버와 같은 외부 도메인에 연결하거나 인증서의 해지 상태를 확인해야 하는 경우에 필요할 수 있습니다.

1.  Application Gateway가 정상 상태이고 실행 중인지 확인하려면 포털에서 **Resource Health** 옵션으로 이동하여 상태가 **정상** 인지 확인합니다. **비정상** 또는 **저하** 상태가 표시되는 경우 [고객 지원팀에 문의하세요](https://azure.microsoft.com/support/options/).

## <a name="next-steps"></a>다음 단계

[Application Gateway 진단 및 로깅](./application-gateway-diagnostics.md)에 대해 자세히 알아보세요.

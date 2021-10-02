---
title: Azure Service Bus 문제 해결 가이드| Microsoft Docs
description: Azure Service Bus를 사용할 때 나타날 수 있는 몇 가지 문제에 대한 문제 해결 팁 및 권장 사항에 대해 알아봅니다.
ms.topic: article
ms.date: 03/03/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 433383845771fca2b3df1ce1da81e070dd8e75c1
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/01/2021
ms.locfileid: "129359398"
---
# <a name="troubleshooting-guide-for-azure-service-bus"></a>Azure Service Bus 문제 해결 가이드
이 문서에서는 Azure Service Bus를 사용할 때 나타날 수 있는 몇 가지 문제에 대한 문제 해결 팁 및 권장 사항을 제공합니다. 

## <a name="connectivity-certificate-or-timeout-issues"></a>연결, 인증서 또는 시간 초과 문제
다음 단계는 *.servicebus.windows.net에 있는 모든 서비스의 연결/인증서/시간 초과 문제를 해결하는 데 도움이 될 수 있습니다. 

- `https://<yournamespace>.servicebus.windows.net/`으로 이동하거나 [wget](https://www.gnu.org/software/wget/)합니다. java SDK를 사용할 때 일반적인 IP 필터링, 가상 네트워크 또는 인증서 체인 문제가 있는지 여부를 확인하는 데 도움이 됩니다.

    성공 메시지의 예:
    
    ```xml
    <feed xmlns="http://www.w3.org/2005/Atom"><title type="text">Publicly Listed Services</title><subtitle type="text">This is the list of publicly-listed services currently available.</subtitle><id>uuid:27fcd1e2-3a99-44b1-8f1e-3e92b52f0171;id=30</id><updated>2019-12-27T13:11:47Z</updated><generator>Service Bus 1.1</generator></feed>
    ```
    
    실패 오류 메시지의 예:

    ```xml
    <Error>
        <Code>400</Code>
        <Detail>
            Bad Request. To know more visit https://aka.ms/sbResourceMgrExceptions. . TrackingId:b786d4d1-cbaf-47a8-a3d1-be689cda2a98_G22, SystemTracker:NoSystemTracker, Timestamp:2019-12-27T13:12:40
        </Detail>
    </Error>
    ```
- 다음 명령을 실행하여 방화벽에서 차단된 포트가 있는지 확인합니다. 사용되는 포트는 443(HTTPS), 5671(AMQP), 9354(Net Messaging/SBMP)입니다. 사용하는 라이브러리에 따라 다른 포트도 사용됩니다. 다음은 5671 포트가 차단되었는지 여부를 확인하는 샘플 명령입니다. 

    ```powershell
    tnc <yournamespacename>.servicebus.windows.net -port 5671
    ```

    Linux에서:

    ```shell
    telnet <yournamespacename>.servicebus.windows.net 5671
    ```
- 일시적 연결 문제가 있는 경우 다음 명령을 실행하여 삭제된 패킷이 있는지 확인합니다. 이 명령은 1초마다 서비스와 25개의 서로 다른 TCP 연결을 구축하려고 시도합니다. 그 후 성공/실패 횟수를 확인하고 TCP 연결 대기 시간을 확인할 수도 있습니다. [여기](/sysinternals/downloads/psping)서 `psping` 도구를 다운로드할 수 있습니다.

    ```shell
    .\psping.exe -n 25 -i 1 -q <yournamespace>.servicebus.windows.net:5671 -nobanner     
    ```
    `tnc`, `ping` 등과 같은 다른 도구를 사용하는 경우 동등한 명령을 사용할 수 있습니다. 
- 이전 단계가 도움이 되지 않는 경우 네트워크 추적을 가져와서 [Wireshark](https://www.wireshark.org/)와 같은 도구를 사용하여 분석합니다. 필요한 경우 [Microsoft 지원](https://support.microsoft.com/)에 문의하세요. 
- 연결 허용 목록에 추가할 올바른 IP 주소를 찾으려면 [허용 목록에 추가해야 하는 IP 주소](service-bus-faq.yml#what-ip-addresses-do-i-need-to-add-to-allowlist-)를 참조하세요. 


## <a name="issues-that-may-occur-with-service-upgradesrestarts"></a>서비스 업그레이드/다시 시작 시 발생할 수 있는 문제

### <a name="symptoms"></a>증상
- 요청이 일시적으로 제한될 수 있습니다.
- 들어오는 메시지/요청이 삭제될 수 있습니다.
- 로그 파일에 오류 메시지가 포함될 수 있습니다.
- 몇 초 동안 서비스에서 애플리케이션의 연결이 끊어질 수 있습니다.

### <a name="cause"></a>원인
백 엔드 서비스 업그레이드 및 다시 시작으로 인해 애플리케이션에서 이러한 문제가 발생할 수 있습니다.

### <a name="resolution"></a>해결 방법
애플리케이션 코드에서 SDK를 사용하는 경우 재시도 정책이 이미 기본 제공되며 활성 상태입니다. 애플리케이션/워크플로에 큰 영향 없이 애플리케이션이 다시 연결됩니다.

## <a name="unauthorized-access-send-claims-are-required"></a>무단 액세스: 클레임 보내기가 필요합니다.

### <a name="symptoms"></a>증상 
보내기 권한이 있는 사용자 할당 관리 ID를 사용하여 온-프레미스 컴퓨터의 Visual Studio에서 Service Bus 토픽에 액세스하려고 할 때 이 오류가 표시될 수 있습니다.

```bash
Service Bus Error: Unauthorized access. 'Send' claim\(s\) are required to perform this operation.
```

### <a name="cause"></a>원인
ID에 Service Bus 토픽에 액세스할 수 있는 권한이 없습니다. 

### <a name="resolution"></a>해결 방법
이 오류를 해결하려면 [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication/) 라이브러리를 설치하세요.  자세한 내용은 [로컬 개발 인증](/dotnet/api/overview/azure/service-to-service-authentication#local-development-authentication)을 참조하세요. 

역할에 권한을 할당하는 방법에 대한 자세한 내용은 [Azure Active Directory에 관리 ID를 인증하여 Azure Service Bus 리소스에 액세스](service-bus-managed-service-identity.md)를 참조하세요.

## <a name="service-bus-exception-put-token-failed"></a>Service Bus 예외: Put 토큰이 실패했습니다.

### <a name="symptoms"></a>증상
동일한 Service Bus 연결을 사용하여 1,000개 이상의 메시지를 보내려고 하면 다음과 같은 오류 메시지가 표시됩니다. 

`Microsoft.Azure.ServiceBus.ServiceBusException: Put token failed. status-code: 403, status-description: The maximum number of '1000' tokens per connection has been reached.` 

### <a name="cause"></a>원인
Service Bus 네임스페이스에 대한 단일 연결을 사용하여 메시지를 보내고 받는 데 사용되는 토큰 수에는 한도가 있습니다. 한도는 1,000개입니다. 

### <a name="resolution"></a>해상도
더 많은 메시지를 보내려면 Service Bus 네임스페이스에 대한 새 연결을 엽니다.

## <a name="adding-virtual-network-rule-using-powershell-fails"></a>PowerShell을 사용하여 가상 네트워크 규칙을 추가하지 못함

### <a name="symptoms"></a>증상
가상 네트워크 규칙에서 단일 가상 네트워크에 두 개의 서브넷을 구성했습니다. [Remove-AzServiceBusVirtualNetworkRule](/powershell/module/az.servicebus/remove-azservicebusvirtualnetworkrule) cmdlet을 사용하여 한 서브넷을 제거하려고 하면 가상 네트워크 규칙에서 해당 서브넷이 제거되지 않습니다. 

```azurepowershell-interactive
Remove-AzServiceBusVirtualNetworkRule -ResourceGroupName $resourceGroupName -Namespace $serviceBusName -SubnetId $subnetId
```

### <a name="cause"></a>원인
서브넷에 대해 지정한 Azure Resource Manager ID가 잘못되었을 수 있습니다. 이 문제는 Service Bus 네임스페이스가 있는 리소스 그룹과 다른 리소스 그룹에 가상 네트워크가 있는 경우 발생할 수 있습니다. 가상 네트워크의 리소스 그룹을 명시적으로 지정하지 않으면 CLI 명령은 Service Bus 네임스페이스의 리소스 그룹을 사용하여 Azure Resource Manager ID를 생성합니다. 따라서 네트워크 규칙에서 서브넷을 제거하지 못합니다. 

### <a name="resolution"></a>해결 방법
가상 네트워크가 있는 리소스 그룹의 이름이 포함된 서브넷의 전체 Azure Resource Manager ID를 지정합니다. 예를 들면 다음과 같습니다.

```azurepowershell-interactive
Remove-AzServiceBusVirtualNetworkRule -ResourceGroupName myRG -Namespace myNamespace -SubnetId "/subscriptions/SubscriptionId/resourcegroups/ResourceGroup/myOtherRG/providers/Microsoft.Network/virtualNetworks/myVNet/subnets/mySubnet"
```

## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요. 

- [Azure Resource Manager 예외](service-bus-resource-manager-exceptions.md). 템플릿 또는 직접 호출을 통해 Azure Resource Manager를 사용하여 Azure Service Bus와 상호 작용할 때 생성되는 예외가 나열되어 있습니다.
- [메시징 예외](service-bus-messaging-exceptions.md) Azure Service Bus용 .NET Framework에서 생성된 예외 목록을 제공합니다.

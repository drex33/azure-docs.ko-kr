---
title: 연결 문제 해결 - Azure Event Hubs | Microsoft Docs
description: 이 문서에서는 Azure Event Hubs의 연결 문제 해결에 대한 정보를 제공합니다.
ms.topic: article
ms.date: 09/15/2021
ms.openlocfilehash: a07b98f9913e0ef82edd88e2aa0dad6c5f3b238b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128600421"
---
# <a name="troubleshoot-connectivity-issues---azure-event-hubs"></a>연결 문제 해결 - Azure Event Hubs
클라이언트 애플리케이션에서 이벤트 허브에 연결할 수 없는 다양한 이유가 있습니다. 발생하는 연결 문제는 영구적이거나 일시적일 수 있습니다. 문제가 항상(영구) 발생하는 경우 연결 문자열, 조직의 방화벽 설정, IP 방화벽 설정, 네트워크 보안 설정(예: 서비스 엔드포인트, 프라이빗 엔드포인트) 등을 확인할 수 있습니다. 일시적인 문제의 경우 최신 버전의 SDK로 업그레이드하고, 삭제된 패킷을 확인하는 명령을 실행하고, 네트워크 추적을 가져오면 문제 해결에 도움이 될 수 있습니다. 

이 문서에서는 Azure Event Hubs의 연결 문제 해결에 대한 팁을 제공합니다. 

## <a name="troubleshoot-permanent-connectivity-issues"></a>영구 연결 문제 해결
애플리케이션에서 이벤트 허브에 연결할 수 없는 경우 이 섹션의 단계에 따라 문제를 해결합니다. 

### <a name="check-if-there-is-a-service-outage"></a>서비스 중단이 있는지 확인합니다.
[Azure 서비스 상태 사이트](https://azure.microsoft.com/status/)에서 Azure Event Hubs 서비스 중단을 검사합니다.

### <a name="verify-the-connection-string"></a>연결 문자열 확인 
사용 중인 연결 문자열이 올바른지 확인합니다. Azure Portal, CLI 또는 PowerShell을 사용하여 연결 문자열을 가져오려면 [연결 문자열 가져오기](event-hubs-get-connection-string.md)를 참조하세요. 

Kafka 클라이언트의 경우 producer.config 또는 consumer.config 파일이 제대로 구성되어 있는지 확인합니다. 자세한 내용은 [Event Hubs에서 Kafka를 사용하여 메시지 보내기 및 받기](event-hubs-quickstart-kafka-enabled-event-hubs.md#send-and-receive-messages-with-kafka-in-event-hubs)를 참조하세요.

[!INCLUDE [event-hubs-connectivity](./includes/event-hubs-connectivity.md)]

### <a name="verify-that-eventhub-service-tag-is-allowed-in-your-network-security-groups"></a>네트워크 보안 그룹에서 EventHub 서비스 태그가 허용 되는지 확인 합니다.
응용 프로그램이 서브넷 내에서 실행 중이 고 연결 된 네트워크 보안 그룹이 있는 경우, 인터넷 아웃 바운드 허용 여부 또는 EventHub 서비스 태그 허용 여부를 확인 합니다. [가상 네트워크 서비스 태그](../virtual-network/service-tags-overview.md)를 참조하고 `EventHub`를 검색합니다.

### <a name="check-if-the-application-needs-to-be-running-in-a-specific-subnet-of-a-vnet"></a>애플리케이션이 vnet의 특정 서브넷에서 실행되어야 하는지 확인
네임스페이스에 대한 액세스 권한이 있는 가상 네트워크 서브넷에서 애플리케이션이 실행되고 있는지 확인합니다. 그렇지 않은 경우 네임스페이스에 대한 액세스 권한이 있는 서브넷에서 애플리케이션을 실행하거나 애플리케이션이 실행 중인 시스템의 IP 주소를 [IP 방화벽](event-hubs-ip-filtering.md)에 추가합니다. 

이벤트 허브 네임스페이스에 대한 가상 네트워크 서비스 엔드포인트를 만들 때 네임스페이스는 서비스 엔드포인트에 바인딩된 서브넷의 트래픽만 허용합니다. 이 동작에는 예외가 있습니다. IP 방화벽의 특정 IP 주소를 추가하여 Event Hub 공용 엔드포인트에 액세스할 수 있습니다. 자세한 내용은 [네트워크 서비스 엔드포인트](event-hubs-service-endpoints.md)를 참조하세요.

### <a name="check-the-ip-firewall-settings-for-your-namespace"></a>네임스페이스에 대한 IP 방화벽 설정을 확인합니다.
애플리케이션이 실행되는 컴퓨터의 공용 IP 주소가 IP 방화벽에 의해 차단되지 않았는지 확인합니다.  

기본적으로 요청에 유효한 인증 및 권한 부여가 제공되는 한 Event Hubs 네임스페이스는 인터넷에서 액세스할 수 있습니다. IP 방화벽을 사용하면 [CIDR(Classless Inter-Domain Routing)](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) 표기법으로 IPv4 주소 또는 IPv4 주소 범위 세트로만 제한할 수 있습니다.

IP 방화벽 규칙은 Event Hubs 네임스페이스 수준에 적용됩니다. 따라서 해당 규칙은 지원되는 모든 프로토콜을 사용하는 클라이언트의 모든 연결에 적용됩니다. Event Hubs 네임스페이스에서 허용된 IP 규칙과 일치하지 않는 IP 주소의 연결 시도는 권한이 없는 것으로 거부됩니다. 응답은 IP 규칙을 언급하지 않습니다. IP 필터 규칙은 순서대로 적용되며 IP 주소와 일치하는 첫 번째 규칙이 수락 또는 거부 작업을 결정합니다.

자세한 내용은 [Azure Event Hubs 네임스페이스에 대한 IP 방화벽 규칙 구성](event-hubs-ip-filtering.md)을 참조하세요. IP 필터링, 가상 네트워크 또는 인증서 체인 문제가 있는지 확인하려면 [네트워크 관련 문제 해결](#troubleshoot-network-related-issues)을 참조하세요.

### <a name="check-if-the-namespace-can-be-accessed-using-only-a-private-endpoint"></a>프라이빗 엔드포인트만 사용하여 네임스페이스에 액세스할 수 있는지 확인합니다.
Event Hubs 네임스페이스가 프라이빗 엔드포인트를 통해서만 액세스할 수 있도록 구성된 경우 클라이언트 애플리케이션이 프라이빗 엔드포인트를 통해 네임스페이스에 액세스하고 있는지 확인합니다. 

[Azure Private Link 서비스](../private-link/private-link-overview.md)를 사용하면 가상 네트워크의 **프라이빗 엔드포인트** 를 통해 Azure Event Hubs에 액세스할 수 있습니다. 프라이빗 엔드포인트는 Azure Private Link가 제공하는, 서비스에 비공개로 안전하게 연결하는 네트워크 인터페이스입니다. 프라이빗 엔드포인트는 가상 네트워크의 개인 IP 주소를 사용하여 효과적으로 가상 네트워크에 서비스를 제공합니다. 서비스에 대한 모든 트래픽은 프라이빗 엔드포인트를 통해 라우팅할 수 있으므로 게이트웨이, NAT 디바이스, ExpressRoute 또는 VPN 연결 또는 공용 IP 주소가 필요하지 않습니다. 가상 네트워크와 서비스 간의 트래픽은 Microsoft 백본 네트워크를 통해 이동하여 공용 인터넷에서 노출을 제거합니다. Azure 리소스의 인스턴스에 연결하여 액세스 제어에서 가장 높은 수준의 세분성을 제공할 수 있습니다.

자세한 내용은 [프라이빗 엔드포인트 구성](private-link-service.md)을 참조하세요. 프라이빗 엔드포인트가 사용되는지 확인하려면 **프라이빗 엔드포인트 연결이 작동하는지 확인** 섹션을 참조하세요. 

### <a name="troubleshoot-network-related-issues"></a>네트워크 관련 문제 해결
Event Hubs의 네트워크 관련 문제를 해결하려면 다음 단계를 수행합니다. 

찾아보거나 [wget](https://www.gnu.org/software/wget/)합니다 `https://<yournamespacename>.servicebus.windows.net/`. IP 필터링, 가상 네트워크 또는 인증서 체인 문제가 있는지 여부를 확인하는 데 도움이 됩니다(Java SDK를 사용하는 경우 가장 일반적임).

**성공 메시지** 의 예:

```xml
<feed xmlns="http://www.w3.org/2005/Atom"><title type="text">Publicly Listed Services</title><subtitle type="text">This is the list of publicly-listed services currently available.</subtitle><id>uuid:27fcd1e2-3a99-44b1-8f1e-3e92b52f0171;id=30</id><updated>2019-12-27T13:11:47Z</updated><generator>Service Bus 1.1</generator></feed>
```

**실패 오류 메시지** 의 예:

```json
<Error>
    <Code>400</Code>
    <Detail>
        Bad Request. To know more visit https://aka.ms/sbResourceMgrExceptions. . TrackingId:b786d4d1-cbaf-47a8-a3d1-be689cda2a98_G22, SystemTracker:NoSystemTracker, Timestamp:2019-12-27T13:12:40
    </Detail>
</Error>
```

## <a name="troubleshoot-transient-connectivity-issues"></a>일시적인 연결 문제 해결
간헐적 연결 문제가 발생하는 경우 문제 해결 팁을 보려면 다음 섹션을 참조하세요. 

### <a name="use-the-latest-version-of-the-client-sdk"></a>최신 버전의 클라이언트 SDK 사용
일부 일시적인 연결 문제는 현재 사용하는 버전 이후의 SDK에서 수정되었을 수 있습니다. 애플리케이션에서 최신 버전의 클라이언트 SDK를 사용하고 있는지 확인합니다. SDK는 새로운/업데이트된 기능 및 버그 수정으로 지속적으로 개선되므로 항상 최신 패키지를 사용하여 테스트합니다. 수정된 문제 및 추가된/업데이트된 기능에 대한 릴리스 정보를 확인합니다. 

클라이언트 SDK에 대한 자세한 내용은 [Azure Event Hubs-클라이언트 SDK](sdks.md) 문서를 참조하세요. 

### <a name="run-the-command-to-check-dropped-packets"></a>명령을 실행하여 삭제된 패킷을 확인합니다.
일시적인 연결 문제가 있는 경우 다음 명령을 실행하여 드롭된 패킷이 있는지 확인합니다. 이 명령은 1초마다 서비스와 25개의 서로 다른 TCP 연결을 구축하려고 시도합니다. 그 후 성공/실패 횟수를 확인하고 TCP 연결 대기 시간을 확인할 수도 있습니다. [여기](/sysinternals/downloads/psping)서 `psping` 도구를 다운로드할 수 있습니다.

```shell
.\psping.exe -n 25 -i 1 -q <yournamespacename>.servicebus.windows.net:5671 -nobanner     
```
`tnc`, `ping` 등과 같은 다른 도구를 사용하는 경우 동등한 명령을 사용할 수 있습니다. 

이전 단계가 도움이 되지 않는 경우 네트워크 추적을 가져와서 [Wireshark](https://www.wireshark.org/)와 같은 도구를 사용하여 분석합니다. 필요한 경우 [Microsoft 지원](https://support.microsoft.com/)에 문의하세요. 

### <a name="service-upgradesrestarts"></a>서비스 업그레이드/다시 시작
백엔드 서비스 업그레이드 및 다시 시작으로 인해 일시적인 연결 문제가 발생할 수 있습니다. 문제가 발생하면 다음과 같은 증상이 나타날 수 있습니다. 

- 들어오는 메시지/요청이 끊길 수 있습니다.
- 로그 파일에 오류 메시지가 포함될 수 있습니다.
- 몇 초 동안 서비스에서 애플리케이션의 연결이 끊어질 수 있습니다.
- 요청이 일시적으로 제한될 수 있습니다.

애플리케이션 코드에서 SDK를 활용하는 경우 다시 시도 정책은 이미 기본 제공되어 활성 상태입니다. 애플리케이션/워크플로에 상당한 영향 없이 애플리케이션이 다시 연결됩니다. 이러한 일시적인 오류를 포착하고 백오프한 다음 호출을 다시 시도하면 코드가 이러한 일시적인 문제에 복원력 있음을 확인할 수 있습니다.

## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요.

* [인증 및 권한 부여 문제 해결](troubleshoot-authentication-authorization.md)

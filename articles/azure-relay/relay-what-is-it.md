---
title: Azure Relay란? | Microsoft Docs
description: 이 문서에서는 Azure Relay 서비스에 대한 개요를 제공합니다. 이 서비스를 사용하면 방화벽 연결을 열거나 네트워크 인프라를 강제로 변경하지 않고도 회사 네트워크에서 실행되는 온-프레미스 서비스를 사용하는 클라우드 애플리케이션을 개발할 수 있습니다.
ms.topic: overview
ms.date: 09/02/2021
ms.custom: contperf-fy22q1
ms.openlocfilehash: e7555e44af9192141e9588361b905773cd56e2e6
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129545061"
---
# <a name="what-is-azure-relay"></a>Azure Relay란?
Azure Relay 서비스를 사용하면 회사 네트워크에서 실행되는 서비스를 퍼블릭 클라우드에 안전하게 공개할 수 있습니다. 방화벽에서 포트를 열거나 회사 네트워크 인프라를 강제로 변경하지 않고도 이 작업을 수행할 수 있습니다. 

릴레이 서비스에서 지원하는 클라우드 또는 다른 온-프레미스 환경에서 실행되는 온-프레미스 서비스와 애플리케이션 간의 시나리오는 다음과 같습니다. 

- 기존의 단방향 요청/응답 및 피어 투 피어 통신 
- 게시/구독 시나리오를 사용할 수 있게 하는 인터넷 범위의 이벤트 배포 
- 네트워크 경계를 가로지르는 양방향 비버퍼링 소켓 통신

Azure Relay는 VPN과 같은 네트워크 수준의 통합 기술과 다릅니다. Azure Relay의 범위는 단일 머신의 단일 애플리케이션 엔드포인트로 지정될 수 있습니다. VPN 기술은 네트워크 환경 변화에 따라 결정되므로 훨씬 더 많이 개입해야 합니다. 

## <a name="basic-flow"></a>기본 흐름
릴레이된 데이터 전송 패턴에서 기본적인 관련 단계는 다음과 같습니다.

1. 온-프레미스 서비스에서 아웃바운드 포트를 통해 릴레이 서비스에 연결합니다. 
2. 특정 주소에 연결된 통신용 양방향 소켓을 만듭니다. 
3. 다음으로, 클라이언트에서 해당 주소를 대상으로 하는 릴레이 서비스에 트래픽을 전송하여 온-프레미스 서비스와 통신할 수 있습니다. 
4. 그런 다음, 릴레이 서비스에서 클라이언트 전용 양방향 소켓을 통해 온-프레미스 서비스에 데이터를 *릴레이* 합니다. 클라이언트는 온-프레미스 서비스에 직접 연결할 필요가 없습니다. 즉 서비스의 위치를 인식할 필요가 없습니다. 그리고 온-프레미스 서비스는 방화벽에서 인바운드 포트를 열 필요가 없습니다.


## <a name="features"></a>기능 
Azure 릴레이에는 다음과 같은 두 가지 기능이 있습니다.

- [하이브리드 연결](#hybrid-connections) - 다중 플랫폼 시나리오를 가능하게 하는 공개 표준 웹 소켓을 사용합니다.
- [WCF 릴레이](#wcf-relay) - 원격 프로시저 호출이 가능하도록 WCF(Windows Communication Foundation)를 사용합니다. WCF 릴레이는 레거시 릴레이로 많은 고객이 기존의 WCF 프로그래밍 모델과 함께 사용합니다.

## <a name="hybrid-connections"></a>하이브리드 연결

Azure Relay의 하이브리드 연결 기능은 이전에 있던 Relay 기능의 안전한 개방형 프로토콜이 진화한 것입니다. 모든 플랫폼에서 모든 언어로 사용할 수 있습니다. Azure Relay의 하이브리드 연결 기능은 HTTP 및 WebSockets 프로토콜을 기반으로 합니다. 웹 소켓 또는 HTTP(S)를 통해 요청을 보내고 응답을 받을 수 있습니다. 이 기능은 일반적인 웹 브라우저의 WebSocket API와 호환됩니다. 

하이브리드 연결 프로토콜에 대한 자세한 내용은 [하이브리드 연결 프로토콜 가이드](relay-hybrid-connections-protocol.md)를 참조하세요. 모든 런타임/언어의 모든 웹 소켓 라이브러리에서 하이브리드 연결을 사용할 수 있습니다.

> [!NOTE]
> Azure Relay의 하이브리드 연결은 BizTalk Services의 이전 하이브리드 연결 기능을 대체합니다. BizTalk Services의 하이브리드 연결 기능은 Azure Service Bus WCF Relay를 기반으로 합니다. Azure Relay의 하이브리드 연결 기능은 기존 WCF Relay 기능을 보완합니다. 이러한 두 가지 서비스 기능(WCF Relay 및 하이브리드 연결)은 Azure Relay 서비스에 함께 있습니다. 일반적인 게이트웨이를 공유하지만 구현 방식은 서로 다릅니다.

Azure Relay에서 하이브리드 연결 사용을 시작하려면 다음 빠른 시작을 참조하세요. 

- [하이브리드 연결 - .NET WebSockets](relay-hybrid-connections-dotnet-get-started.md)
- [하이브리드 연결 - 노드 WebSockets](relay-hybrid-connections-node-get-started.md)
- [하이브리드 연결 - .NET HTTP](relay-hybrid-connections-http-requests-dotnet-get-started.md)
- [하이브리드 연결 - 노드 HTTP](relay-hybrid-connections-http-requests-node-get-started.md)

추가 샘플은 [Azure Relay - GitHub의 하이브리드 연결 샘플](https://github.com/Azure/azure-relay/tree/master/samples/hybrid-connections)을 참조하세요.

## <a name="wcf-relay"></a>WCF 릴레이
WCF Relay는 전체 .NET Framework 및 WCF에서 작동합니다. WCF "릴레이" 바인딩 모음을 사용하여 온-프레미스 서비스와 릴레이 서비스 사이의 연결을 만듭니다. 릴레이 바인딩은 클라우드에서 Service Bus와 통합하는 WCF 채널 구성 요소를 생성하도록 설계된 새로운 전송 바인딩 요소에 매핑합니다.

WCF Relay 사용을 시작하려면 다음 빠른 시작을 참조하세요. 

- [클라우드의 웹앱에 온-프레미스 WCF 서비스 노출](service-bus-dotnet-hybrid-app-using-service-bus-relay.md)
- [네트워크 외부의 WCF 클라이언트에 온-프레미스 WCF 서비스 노출](service-bus-relay-tutorial.md)
- [네트워크 외부의 클라이언트에 온-프레미스 WCF REST 서비스 노출](service-bus-relay-rest-tutorial.md)

추가 샘플은 [Azure Relay - GitHub의 WCF Relay 샘플](https://github.com/Azure/azure-relay/tree/master/samples/wcf-relay)을 참조하세요.

## <a name="hybrid-connections-vs-wcf-relay"></a>하이브리드 연결과 WCF Relay
하이브리드 연결과 WCF Relay는 모두 회사 네트워크 내에 있는 자산에 안전하게 연결할 수 있게 합니다. 다음 표에서 설명한 대로 이들 중 무엇을 사용할지는 특정 요구 사항에 따라 달라집니다.

|  | WCF 릴레이 | 하이브리드 연결 |
| --- |:---:|:---:|
| **WCF** |x | |
| **.NET Core** | |x |
| **.NET Framework** |x |x |
| **JavaScript/Node.js** | |x |
| **표준 기반 개방형 프로토콜** | |x |
| **RPC 프로그래밍 모델** | |x |

## <a name="architecture-processing-of-incoming-relay-requests"></a>아키텍처: 들어오는 릴레이 요청 처리
다음 다이어그램에서는 Azure Relay 서비스에서 들어오는 릴레이 요청을 처리하는 방법을 보여 줍니다.

![들어오는 WCF 릴레이 요청 처리](./media/relay-what-is-it/ic690645.png)

1. 수신 클라이언트에서 Azure Relay 서비스에 수신 요청을 보냅니다. Azure 부하 분산 장치에서 이 요청을 게이트웨이 노드 중 하나로 라우팅합니다. 
2. Azure Relay 서비스에서 게이트웨이 저장소에 릴레이를 만듭니다. 
3. 송신 클라이언트에서 수신 서비스에 연결하라는 요청을 보냅니다. 
4. 요청을 수신한 게이트웨이에서 게이트웨이 저장소에 있는 릴레이를 조회합니다. 
5. 게이트웨이에서 연결 요청을 게이트웨이 저장소에 언급된 올바른 게이트웨이로 전달합니다. 
6. 게이트웨이에서 송신 클라이언트에 가장 가까운 게이트웨이 노드에 대한 임시 채널을 만들도록 요구하는 요청을 수신 클라이언트에 보냅니다. 
7. 수신 클라이언트는 송신 클라이언트에서 가장 가까운 게이트웨이에 대한 임시 채널을 만듭니다. 이제 게이트웨이를 통해 클라이언트 간의 연결이 수립되었으므로 클라이언트가 서로 메시지를 교환할 수 있습니다. 
8. 게이트웨이는 메시지를 송신 클라이언트에서 수신 대기 클라이언트로 전달합니다. 
9. 게이트웨이는 메시지를 송신 클라이언트에서 수신 대기 클라이언트로 전달합니다.  

## <a name="next-steps"></a>다음 단계
다음 빠른 시작 중 하나 이상을 수행하거나 [GitHub의 Azure Relay 샘플](https://github.com/Azure/azure-relay/tree/master/samples)을 참조하세요.

- 하이브리드 연결
    - [하이브리드 연결 - .NET WebSockets](relay-hybrid-connections-dotnet-get-started.md)
    - [하이브리드 연결 - 노드 WebSockets](relay-hybrid-connections-node-get-started.md)
    - [하이브리드 연결 - .NET HTTP](relay-hybrid-connections-http-requests-dotnet-get-started.md)
    - [하이브리드 연결 - 노드 HTTP](relay-hybrid-connections-http-requests-node-get-started.md)
- WCF 릴레이
    - [클라우드의 웹앱에 온-프레미스 WCF 서비스 노출](service-bus-dotnet-hybrid-app-using-service-bus-relay.md)
    - [네트워크 외부의 WCF 클라이언트에 온-프레미스 WCF 서비스 노출](service-bus-relay-tutorial.md)
    - [네트워크 외부의 클라이언트에 온-프레미스 WCF REST 서비스 노출](service-bus-relay-rest-tutorial.md)

질문과 대답 및 그에 대한 답변 목록은 [릴레이 FAQ](relay-faq.yml)를 참조하세요.


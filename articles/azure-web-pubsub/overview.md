---
title: Azure Web PubSub 서비스는 무엇인가요?
description: Azure Web PubSub을 사용하기 위한 일반적인 사용 사례 시나리오를 더 잘 이해하고 Azure Web PubSub의 주요 이점에 대해 알아보세요.
author: yjin81
ms.author: yajin1
ms.service: azure-web-pubsub
ms.topic: overview
ms.date: 01/27/2021
ms.openlocfilehash: ed48335b0417cacce9a1e408de06c0c2a9ec9b44
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121751119"
---
# <a name="what-is-azure-web-pubsub-service"></a>Azure Web PubSub 서비스는 무엇인가요? 

Azure Web PubSub 서비스를 사용하면 WebSocket 및 게시-구독 패턴을 사용하여 실시간 메시징 웹 애플리케이션을 쉽게 빌드할 수 있습니다. 이 실시간 기능을 사용하면 서버와 연결된 클라이언트(예: 단일 페이지 웹 애플리케이션 또는 모바일 애플리케이션) 간에 콘텐츠 업데이트를 게시할 수 있습니다. 클라이언트는 최신 업데이트를 폴링하거나 업데이트에 대한 새 HTTP 요청을 제출할 필요가 없습니다.

이 문서에서는 Azure Web PubSub 서비스에 대한 개요를 제공합니다.

## <a name="what-is-azure-web-pubsub-service-used-for"></a>Azure Web PubSub 서비스의 용도는 무엇인가요?

서버와 클라이언트 간 또는 클라이언트 간에 실시간 게시-구독 메시징이 필요한 시나리오에서는 Azure Web PubSub 서비스를 사용할 수 있습니다. 서버에서 폴링하거나 HTTP 요청을 제출해야 하는 경우가 많은 기존의 실시간 기능도 Azure Web PubSub 서비스를 사용할 수 있습니다.

Azure Web PubSub 서비스는 실시간 콘텐츠 업데이트가 필요한 모든 애플리케이션 유형에서 사용할 수 있습니다. Azure Web PubSub 서비스를 유용하게 활용할 수 있는 예제는 다음과 같습니다.

* **높은 빈도 데이터 업데이트:** 게임, 투표, 폴링, 경매
* **라이브 대시보드 및 모니터링:** 회사 대시보드, 금융 시장 데이터, 인스턴트 판매 업데이트, 다중 플레이어 게임 리더 보드, IoT 모니터링
* **플랫폼 간 라이브 채팅:** 라이브 채팅방, 채팅 봇, 온라인 고객 지원, 실시간 쇼핑 지원, 메신저, 인게임 채팅 등
* **지도의 실시간 위치:** 물류 추적, 배달 상태 추적, 운송 상태 업데이트, GPS 앱
* **실시간 타겟팅 광고:** 맞춤형 실시간 푸시 광고 및 제품, 대화형 광고
* **협업 앱:** 공동 작성, 화이트보드 앱, 팀 모임 소프트웨어
* **푸시 인스턴트 알림:** 소셜 네트워크, 메일, 게임, 여행 경보
* **실시간 브로드캐스트:** 라이브 오디오/비디오 브로드캐스트, 실시간 캡션, 번역, 이벤트/뉴스 브로드캐스트
* **IoT 및 연결된 디바이스:**: 실시간 IoT 메트릭, 원격 제어, 실시간 상태, 위치 추적
* **자동화:** 업스트림 이벤트의 실시간 트리거

## <a name="what-are-the-benefits-using-azure-web-pubsub-service"></a>Azure Web PubSub 서비스를 사용하면 어떤 이점이 있나요?

**대규모 클라이언트 연결 및 고가용성 아키텍처에 대한 기본 제공 지원.**

Azure Web PubSub 서비스는 대규모 실시간 애플리케이션용으로 설계되었습니다. 이 서비스를 사용하면 여러 인스턴스가 함께 작동하여 수백만 개의 클라이언트 연결로 확장될 수 있습니다. 또한 그동안 분할, 고가용성 또는 재해 복구 용도로 여러 글로벌 지역을 지원합니다.

**다양한 클라이언트 SDK 및 프로그래밍 언어에 대한 지원:**

Azure Web PubSub 서비스는 웹 및 모바일 브라우저, 데스크톱 앱, 모바일 앱, 서버 프로세스, IoT 디바이스 및 게임 콘솔과 같은 광범위한 클라이언트에서 작동합니다. 이 서비스는 게시-구독 패턴을 사용하는 표준 WebSocket 연결을 지원하므로 다른 언어로 된 표준 WebSocket 클라이언트 SDK를 이 서비스에서 쉽게 사용할 수 있습니다. 

**다양한 메시징 패턴을 위한 다양한 API 제공:**

Azure Web PubSub 서비스는 서버와 클라이언트 간에 서로 다른 메시징 패턴을 허용하는 양방향 메시징 서비스입니다. 예를 들면 다음과 같습니다.

* 서버가 특정 클라이언트, 모든 클라이언트 또는 특성 사용자에 속하거나 임의 그룹에 배치된 클라이언트의 하위 집합에 메시지를 보냅니다. 
* 클라이언트가 임의 그룹에 속한 클라이언트에 메시지를 보냅니다.
* 클라이언트가 서버에 메시지를 보냅니다.


## <a name="how-to-use-the-azure-web-pubsub-service"></a>Azure Web PubSub 서비스를 사용하는 방법은 무엇인가요?

여기에 나열된 일부 샘플과 같이 Azure Web PubSub 서비스를 통해 프로그래밍하는 다양한 방법이 있습니다.

- **서버리스 실시간 애플리케이션 빌드**: Azure Web PubSub 서비스와 Azure Functions의 통합을 사용하여 JavaScript, C#, Java 및 Python과 같은 언어로 서버리스 실시간 애플리케이션을 빌드합니다. 
- **WebSocket 하위 프로토콜을 사용하여 클라이언트 쪽 전용 구독/게시 수행** - Azure Web PubSub 서비스는 WebSocket 하위 프로토콜을 제공하여 권한 있는 클라이언트가 편리한 방식으로 다른 클라이언트에 게시할 수 있도록 합니다.
- **제공된 SDK를 사용하여 자체 호스트 앱 서버에서 WebSocket 연결 관리** - Azure Web PubSub 서비스는 C#, JavaScript, Java 및 Python에서 SDK를 제공하여 연결에 메시지 브로드캐스트, 일부 그룹에 연결 추가, 연결 닫기 등 WebSocket 연결 관리를 쉽게 수행할 수 있습니다.
- **REST API를 통해 서버에서 클라이언트로 메시지 보내기** - Azure Web PubSub 서비스는 REST API를 제공하여 애플리케이션이 모든 REST 지원 프로그래밍 언어로 연결된 클라이언트에 메시지를 게시할 수 있도록 합니다.
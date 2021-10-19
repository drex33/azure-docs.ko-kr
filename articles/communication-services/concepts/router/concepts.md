---
title: Azure Communication Services 대한 작업 라우터 개요
titleSuffix: An Azure Communication Services concept document
description: Azure Communication Services 작업 라우터에 대해 알아봅니다.
author: jasonshave
manager: phans
services: azure-communication-services
ms.author: jassha
ms.date: 10/14/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 1178eaa37170228c58f044a616a2bfe230bd3f4c
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130166693"
---
# <a name="job-router-concepts"></a>작업 라우터 개념

[!INCLUDE [Private Preview Disclaimer](../../includes/private-preview-include-section.md)]

Azure Communication Services 작업 라우터는 일부 추상 공급과 시스템의 일부 추상 수요를 일치시키는 문제를 해결합니다. Azure Event Grid 통합된 작업 라우터는 거의 실시간으로 알림을 제공하므로 작업 라우터 인스턴스의 동작을 제어하는 반응형 애플리케이션을 빌드할 수 있습니다.

## <a name="job-router-overview"></a>작업 라우터 개요

작업 라우터 SDK를 사용하여 작업 단위를 특정 리소스와 일치시켜야 하는 다양한 비즈니스 시나리오를 빌드할 수 있습니다. 예를 들어 작업은 여러 잠재적인 연락처 센터 에이전트가 있는 일련의 전화 통화 또는 다른 사람과의 여러 동시 세션을 처리하는 라이브 에이전트가 있는 웹 채팅 요청으로 정의될 수 있습니다. 일부 추상 작업 단위를 사용 가능한 리소스로 라우팅해야 하므로 [작업,](#job) [큐,](#queue) [작업자](#worker)및 [정책](#policies)집합이라고 하는 작업을 정의해야 합니다. 이 작업은 이러한 구성 요소가 상호 작용하는 방식의 동작 측면을 정의합니다.

## <a name="job-router-architecture"></a>작업 라우터 아키텍처

Azure Communication Services 작업 라우터는 이벤트를 사용하여 서비스 내의 작업에 대해 애플리케이션에 알립니다. 다음 다이어그램에서는 작업 라우터에 공통된 간소화된 흐름을 보여 줍니다. 작업 제출, 작업자 등록, 작업 제안 처리

### <a name="job-submission-flow"></a>작업 제출 흐름

1. Contoso 애플리케이션은 Azure Communication Services 인스턴스의 작업 라우터에 작업을 제출합니다.
2. Job이 분류되고 **RouterJobClassified라는** 이벤트가 발생합니다. 여기에는 Job에 대한 모든 정보와 분류 프로세스에서 해당 속성을 수정할 수 있는 방법이 포함됩니다.
 
    :::image type="content" source="../media/router/acs-router-job-submission.png" alt-text="작업을 제출하는 Communication Services 작업 라우터를 보여 주는 다이어그램":::

### <a name="worker-registration-flow"></a>작업자 등록 흐름

1. 작업자가 작업을 수락할 준비가 되면 Contoso의 애플리케이션을 통해 작업 라우터에 등록합니다.
2. 그런 다음, 작업 라우터가 **RouterWorkerRegistered를 다시 보냅니다.**

    :::image type="content" source="../media/router/acs-router-worker-registration.png" alt-text="Communication Services 작업 라우터 작업자 등록을 보여 주는 다이어그램":::

### <a name="matching-and-accepting-a-job-flow"></a>작업 흐름 일치 및 수락

1. 작업 라우터는 작업에 대해 일치하는 작업자를 찾은 경우 Contoso 애플리케이션이 수신할 **RouterWorkerOfferIssued를** 보내고 Azure SignalR Service 같은 플랫폼을 사용하여 연결된 사용자에게 신호를 전송하여 작업을 제공합니다.
2. 작업자가 제안을 수락합니다.
3. 작업 라우터는 Worker가 작업에 할당된 Contoso 애플리케이션에 **RouterWorkerOfferAccepted를** 보냅니다.

    :::image type="content" source="../media/router/acs-router-accept-offer.png" alt-text="Communication Services Job Router 수락 제안을 보여 주는 다이어그램":::

## <a name="real-time-notifications"></a>실시간 알림

Azure Communication Services Event Grid 메시징 플랫폼을 사용하여 작업 라우터가 보내는 워크로드에 대해 어떤 작업을 수행 중인지에 대한 알림을 보냅니다. 작업 라우터는 작업 만들기, 완료, 제안 수락 등과 같은 작업 수명 주기 이벤트와 같은 중요한 작업이 발생할 때마다 이벤트 형태로 메시지를 보냅니다.

## <a name="job"></a>작업

작업은 사용 가능한 작업자로 라우팅해야 하는 작업 단위를 나타냅니다. 작업은 Azure Communication Services 작업 라우터 SDK를 사용하거나 인증된 요청을 REST API 제출하여 정의됩니다. 작업에는 수행 중인 작업의 특성과 함께 호출 ID 또는 티켓 번호와 같이 가질 수 있는 일부 고유 식별자 참조가 포함되는 경우가 많습니다.

## <a name="queue"></a>큐

작업이 만들어지면 제출 시 정적으로 또는 분류 정책 적용을 통해 동적으로 큐에 할당됩니다. 작업은 할당된 큐별로 그룹화되며 워크로드를 배포하려는 방법에 따라 다양한 특성을 사용할 수 있습니다. 큐에는 자격이 있는 작업자에게 작업을 제공하는 방법을 결정하는 **배포 정책이** 필요합니다.

작업 라우터의 큐에는 특정 조건이 발생할 때 작업의 동작을 결정하는 예외 정책이 포함될 수도 있습니다. 예를 들어 타이머 또는 다른 조건에 따라 작업을 다른 큐로 이동하거나, 우선 순위를 늘리거나, 둘 다로 이동하려고 할 수 있습니다.

## <a name="worker"></a>작업자

작업자는 특정 큐에 대한 작업을 처리하는 데 사용할 수 있는 제공을 나타냅니다. 작업 라우터에 등록된 각 작업자에는 **레이블** 집합, 연결된 **큐,** **채널 구성** 및 총 **용량 점수** 가 함께 제공됩니다. 작업 라우터는 이러한 요소를 사용하여 실시간으로 작업자에 작업을 라우팅하는 시기와 방법을 결정합니다.

Azure Communication Services 작업 라우터는 단순 **활성,** **비활성** 또는 **드레닝** 상태를 사용하여 작업자의 상태를 유지 관리하고 사용하여 사용 가능한 작업을 작업자와 일치시킬 수 있는 시기를 결정합니다. 작업 라우터는 상태, 채널 구성 및 총 용량 점수와 함께 실행 가능한 작업자를 계산하고 작업과 관련된 제안 문제를 계산합니다.

## <a name="policies"></a>정책

Azure Communication Services 작업 라우터는 유연한 정책을 적용하여 동적 동작을 시스템의 다양한 측면에 연결합니다. 정책에 따라 작업의 레이블을 & 사용하여 작업의 우선 순위를 변경하고 큐에 대기하는 등의 작업을 평가할 수 있습니다. 작업 라우터의 특정 정책은 PowerFx를 사용하여 인라인 함수 처리를 제공하거나 더 복잡한 시나리오의 경우 Azure 함수에 대한 콜백을 제공합니다.

**분류 정책 -** 분류 정책은 작업 라우터가 큐, 우선 순위를 정의하는 데 도움이 되며, 전송자가 제출 시 이러한 매개 변수를 인식할 수 없거나 인식하지 못하는 경우 작업자 선택기를 변경할 수 있습니다. 분류에 대한 자세한 내용은 분류 개념 페이지를 [참조하세요.](classification-concepts.md)

**배포 정책 -** 작업 라우터가 새 작업을 받으면 배포 정책을 사용하여 적합한 작업자를 찾고 작업 제품을 관리합니다. 작업자는 다른 **모드를** 사용하여 선택되며 정책에 따라 작업 라우터는 하나 이상의 작업자에게 동시에 알릴 수 있습니다.

**예외 정책 -** 예외 정책은 트리거에 따라 작업의 동작을 제어하고 원하는 작업을 실행합니다. 예외 정책은 큐에서 작업의 동작을 제어할 수 있도록 큐에 연결됩니다.

## <a name="next-steps"></a>다음 단계

- [라우터 규칙 개념](router-rule-concepts.md)
- [분류 개념](classification-concepts.md)
- [배포 개념](distribution-concepts.md)
- [빠른 시작 가이드](../../quickstarts/router/get-started-router.md)
- [큐 관리](../../how-tos/router-sdk/manage-queue.md)
- [작업 분류](../../how-tos/router-sdk/job-classification.md)
- [작업 에스컬레이션](../../how-tos/router-sdk/escalate-job.md)
- [이벤트 구독](../../how-tos/router-sdk/subscribe-events.md)
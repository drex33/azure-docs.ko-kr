---
title: Azure Web PubSub 서비스의 청구 모델
description: Azure Web PubSub 서비스 청구 모델의 주요 개념에 대한 개요입니다.
author: yjin81
ms.author: yajin1
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 03/29/2021
ms.openlocfilehash: 7f937e833a909f15cdc86af35c27bf5240abcfd8
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131426832"
---
# <a name="billing-model-of-azure-web-pubsub-service"></a>Azure Web PubSub 서비스의 청구 모델

Azure Web PubSub 서비스의 청구 모델은 단위 수와 아웃바운드 트래픽 크기(메시지 수)를 기준으로 합니다. 이 문서에서는 단위와 아웃바운드 트래픽(메시지 수)을 정의하고 청구를 위해 계산하는 방법을 설명합니다.

## <a name="how-units-are-counted-with-billing-model"></a>청구 모델에서 단위를 계산하는 방법

### <a name="what-is-the-connection"></a>연결이란?

클라이언트와 서비스 간의 연결입니다. Azure Portal에서 연결 수를 모니터링할 수 있습니다. “연결(최대)”의 보기에는 특정 기간의 최대 연결 수가 표시됩니다. 

### <a name="what-is-the-unit"></a>단위란?

단위는 Azure Web PubSub 서비스의 기능에 대한 추상적 개념입니다. 각 단위는 최대 1,000개의 동시 연결을 지원합니다. 각 Azure Web PubSub 서비스 인스턴스는 1, 2, 5, 10, 20, 50 또는 100단위일 수 있습니다. 따라서 단위 수는 Web PubSub 서비스 인스턴스가 허용할 수 있는 연결 수를 지정합니다.

###  <a name="how-units-are-counted-with-billing-model"></a>청구 모델에서 단위를 계산하는 방법

단위는 단위 수와 단위의 사용 시간(초)을 기준으로 계산되며 매일 청구됩니다. 

예를 들어 5단위가 있는 Azure Web PubSub 서비스 인스턴스가 하나 있고, 오전 10시에서 오후 4시 사이에 최대 10단위까지 스케일 업한 다음, 오후 4시 이후에 다시 5단위로 스케일 다운한다고 가정합니다. 특정 일에 18시간 동안 5단위, 6시간 동안 10단위가 됩니다.

> 청구 단위 사용량 = (5단위 * 18시간 + 10단위 * 6시간) / 24시간 = 6.25단위/일

## <a name="how-outbound-traffic-is-counted-with-billing-model"></a>청구 모델에서 아웃바운드 트래픽을 계산하는 방법

### <a name="what-is-inboundoutbound-traffic"></a>인바운드/아웃바운드 트래픽이란? 

아웃바운드 트래픽은 Azure Web PubSub 서비스에서 전송하는 메시지입니다. Azure Portal의 아웃바운드 트래픽을 모니터링할 수 있습니다. “아웃바운드 트래픽(합계)”의 보기에는 특정 기간에 집계된 아웃바운드 메시지 크기(바이트)가 표시됩니다.

- 서비스에서 받는 사람에게 브로드캐스트되는 메시지입니다.
- 서비스에서 업스트림 웹후크로 전송된 메시지입니다.
- [라이브 추적 도구](./howto-troubleshoot-resource-logs.md#capture-resource-logs-with-live-trace-tool)를 사용 하는 리소스 로그입니다. 

인바운드 트래픽은 Azure Web PubSub 서비스로 전송되는 메시지입니다. 

- 클라이언트에서 서비스로 전송된 메시지입니다.
- 서버 또는 함수에서 서비스로 전송된 메시지입니다.

### <a name="what-is-message-count"></a>메시지 수란?

청구 목적의 메시지 수는 추상적 개념이며 아웃바운드 트래픽(바이트)을 2KB로 나눈 크기로 정의됩니다. 예를 들어 100KB 트래픽은 50개 메시지로 계산됩니다.  

### <a name="how-traffic-is-counted-with-billing-model"></a>청구 모델에서 트래픽을 계산하는 방법

청구 시 아웃바운드 트래픽만 계산됩니다. 

예를 들어 Azure Web PubSub 서비스와 Azure Functions가 포함된 애플리케이션이 있다고 가정합니다. 한 사용자가 4KB 데이터를 그룹의 10개 연결로 브로드캐스트합니다. 서비스에서 함수로의 업스트림의 경우 4KB, 서비스에서 10개 연결로의 브로드캐스트의 경우 40KB가 됩니다.

> 청구할 아웃바운드 트래픽 = 4KB(업스트림 트래픽) + 4KB * 10(클라이언트 트래픽으로 서비스 브로드캐스트) = 44KB

> 해당 메시지 수 = 44KB/2KB = 22

또한 Azure Web PubSub 서비스는 단위 사용량에 따라 아웃바운드 트래픽(메시지 수)의 일일 무료 할당량을 제공합니다. 무료 할당량을 초과하는 아웃바운드 트래픽(메시지 수)은 추가 아웃바운드 트래픽(추가 메시지)입니다. 예를 들어 표준 계층에서 일일 무료 할당량은 단위당 2,000,000KB 아웃바운드 트래픽(1,000,000개 메시지)입니다.

이전 단위 사용량 예제를 기반으로 애플리케이션은 일일 6.25단위를 사용합니다. 이는 일일 무료 할당량을 12,500,000KB 아웃바운드 트래픽(625만 개 메시지)으로 보장합니다. 일일 아웃바운드 트래픽이 30,000,000KB(1,500만 개 메시지)인 경우 추가 메시지는 17,500,000KB 아웃바운드 트래픽(875만 개 메시지)이 됩니다. 결과적으로 6.25 표준 단위와 8.75 추가 메시지 단위로 요금이 청구됩니다.

## <a name="pricing"></a>가격 책정 

Azure Web PubSub 서비스는 가격 책정이 다른 여러 계층을 제공합니다. 청구 모델에서 단위 수와 아웃바운드 트래픽의 크기(메시지 수)를 계산하는 방법을 이해한 후에는 [Azure Web PubSub 서비스 가격 책정](https://azure.microsoft.com/pricing/details/web-pubsub)에서 자세한 가격 책정 정보를 확인할 수 있습니다.






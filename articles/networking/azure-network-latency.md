---
title: Azure 네트워크 왕복 대기 시간 통계 | Microsoft Docs
description: Azure 지역 간의 왕복 대기 시간 통계에 대해 알아봅니다.
services: networking
author: nayak-mahesh
ms.service: virtual-network
ms.topic: article
ms.date: 06/08/2021
ms.author: kumud
ms.openlocfilehash: e86f0e38c71265761e0faddbbf1cc783557cd5df
ms.sourcegitcommit: 91fdedcb190c0753180be8dc7db4b1d6da9854a1
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112297769"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Azure 네트워크 왕복 대기 시간 통계

Azure는 [ThousandEyes](https://thousandeyes.com)(제3자 종합 모니터링 서비스)에서 수집하는 측정과 내부 모니터링 도구를 사용하여 자체 네트워크의 핵심 영역에 대한 대기 시간(속도)을 지속적으로 모니터링합니다.

## <a name="how-are-the-measurements-collected"></a>측정값이 어떻게 수집되나요?

대기 시간 측정값은 전 세계 Azure 클라우드 지역에서 호스트되는 ThousandEyes 에이전트들에서 수집 되며, 이 에이전트들에서 1분 간격으로 자체 에이전트들 사이에 네트워크 프로브를 지속적으로 보냅니다. 월간 대기 시간 통계는 해당 월에 수집된 샘플의 평균을 계산하여 파생됩니다.

## <a name="may-2021-round-trip-latency-figures"></a>2021년 5월 왕복 대기 시간 수치

지난 31일간(2021년 5월 31일에 종료) Azure 지역들 간의 월별 평균 왕복 시간은 다음과 같습니다. 다음 측정은 [ThousandEyes](https://thousandeyes.com)에 의해 구동됩니다.

[![Azure 지역 간 대기 시간 통계](media/azure-network-latency/azure-network-latency.png)](media/azure-network-latency/azure-network-latency.png#lightbox)

## <a name="next-steps"></a>다음 단계

[Azure 지역](https://azure.microsoft.com/global-infrastructure/regions/)에 대해 알아봅니다.

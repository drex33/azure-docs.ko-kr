---
title: Azure DDoS Protection 시뮬레이션 테스트
description: 시뮬레이션을 통해 테스트하는 방법 알아보기
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: e8429933e70ca0e83d8cf0b89789fda811b7122d
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130132059"
---
# <a name="test-through-simulations"></a>시뮬레이션을 통해 테스트

주기적으로 시뮬레이션을 수행하여 서비스가 공격에 대응하는 방법에 대한 가정을 테스트하는 것이 좋습니다. 테스트하는 동안 서비스 또는 애플리케이션이 계속해서 예상대로 작동하는지, 사용자 환경이 중단되지 않는지 검사해야 합니다. 기술과 프로세스 관점 간의 격차를 식별하여 DDoS 대응 전략에 통합해야 합니다. 프로덕션 환경에 미치는 영향을 최소화하기 위해 스테이징 환경에서 또는 피크 시간대 이외의 시간에 테스트를 수행하는 것이 좋습니다.

Microsoft는 [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud), 셀프 서비스 트래픽 생성기와 협력하여 고객이 DDoS Protection이 활성화된 공용 엔드포인트에 대한 트래픽을 생성하여 시뮬레이션을 수행할 수 있는 인터페이스를 개발했습니다. 시뮬레이션을 사용하여 다음을 수행할 수 있습니다.

- Azure DDoS Protection을 통해 DDoS 공격으로부터 Azure 리소스를 보호하는 방법을 확인합니다.
- DDoS 공격을 받는 동안 인시던트 응답 프로세스를 최적화합니다.
- DDoS 규정 준수 문서화
- 네트워크 보안 팀을 교육합니다.

> [!NOTE]
> BreakingPoint Cloud는 퍼블릭 클라우드용으로만 사용할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

- 이 자습서의 단계를 완료하려면 먼저 보호되는 공용 IP 주소를 사용하여 [Azure DDoS Standard 보호 계획](manage-ddos-protection.md)을 만들고
- [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud)를 사용하여 계정을 만들어야 합니다. 

## <a name="configure-a-ddos-test-attack"></a>DDoS 테스트 공격 구성

1. 다음 값을 입력하거나 선택하고 **테스트 시작** 을 선택합니다.

    |설정        |값                                              |
    |---------      |---------                                          |
    |대상 IP 주소           | 테스트하려는 공용 IP 주소 중 하나를 입력합니다.                     |
    |포트 번호   | _443_ 을 입력합니다.                       |
    |DDoS 프로필 | 가능한 값으로는 `DNS Flood`, `NTPv2 Flood`, `SSDP Flood`, `TCP SYN Flood`, `UDP 64B Flood`, `UDP 128B Flood`, `UDP 256B Flood`, `UDP 512B Flood`, `UDP 1024B Flood`, `UDP 1514B Flood`, `UDP Fragmentation`, `UDP Memcached` 등이 있습니다.|
    |테스트 크기       | 가능한 값으로는 `100K pps, 50 Mbps and 4 source IPs`, `200K pps, 100 Mbps and 8 source IPs`, `400K pps, 200Mbps and 16 source IPs`, `800K pps, 400 Mbps and 32 source IPs` 등이 있습니다.                                  |
    |테스트 지속 시간 | 가능한 값으로는 `10 Minutes`, `15 Minutes`, `20 Minutes`, `25 Minutes`, `30 Minutes` 등이 있습니다.|

이제 다음과 같이 표시됩니다.

![DDoS 공격 시뮬레이션 예: BreakingPoint Cloud](./media/ddos-attack-simulation/ddos-attack-simulation-example-1.png)

## <a name="monitor-and-validate"></a>모니터링 및 유효성 검사

1. https://portal.azure.com 에 로그인하여 구독으로 이동합니다.
1. 공격을 테스트한 공용 IP 주소를 선택합니다.
1. **모니터링** 에서 **메트릭** 을 선택합니다.
1. **메트릭** 에 _DDoS 공격 진행 여부_ 를 선택합니다.

리소스가 공격을 받으면 다음 그림과 같이 값이 **0** 에서 **1** 로 변경되는 것을 볼 수 있습니다.

![DDoS 공격 시뮬레이션 예: 포털](./media/ddos-attack-simulation/ddos-attack-simulation-example-2.png)

### <a name="breakingpoint-cloud-api-script"></a>BreakingPoint Cloud API 스크립트

이 [API 스크립트](https://aka.ms/ddosbreakingpoint)를 사용하여 한 번 실행하거나 cron을 사용하여 정기적인 테스트를 예약해 DDoS 테스트를 자동화할 수 있습니다. 이는 로깅이 올바르게 구성되었고 감지 및 응답 절차가 효과적인지 확인하는 데 유용합니다. 스크립트에는 Linux OS(Ubuntu 18.04 LTS로 테스트) 및 Python 3이 필요합니다. 포함된 스크립트를 사용하거나 [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) 웹 사이트의 설명서를 사용하여 필수 구성 요소 및 API 클라이언트를 설치합니다.

## <a name="next-steps"></a>다음 단계

- [DDoS 보호 원격 분석을 보고 구성](telemetry.md)하는 방법을 알아봅니다.
- [DDoS 진단 로깅을 보고 구성](diagnostic-logging.md)하는 방법을 알아봅니다.
- [DDoS 빠른 응답에 문의](ddos-rapid-response.md)하는 방법을 알아봅니다.

---
author: wesmc7777
ms.service: iot-dps
ms.topic: include
ms.date: 10/10/2021
ms.author: wesmc
ms.openlocfilehash: 44be0cb5b9715dac2aa59908d045da99f370b3f4
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132754078"
---
> [!NOTE]
> 이 서비스의 일부 영역에는 조정 가능한 한도가 있습니다. 아래 표에 조정 가능? 열과 함께 표시되어 있습니다. 제한을 조정할 수 있는 경우 ‘조정 가능 여부’ 값은 ‘예’입니다. 
>
>제한을 조정할 수 있는 실제 값은 각 고객의 배포에 따라 달라질 수 있습니다. 대규모 배포에는 여러 DPS 인스턴스가 필요할 수 있습니다.
>
> 비즈니스에서 조정 가능한 제한 또는 기본 제한보다 높은 할당량이 필요한 경우 [지원 티켓을 열어](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) 추가 리소스를 요청할 수 있습니다.

다음 표에는 Azure IoT Hub Device Provisioning Service 리소스에 적용되는 제한이 나열되어 있습니다.

| 리소스 | 제한 | 조정 가능 |
| --- | --- | --- |
| Azure 구독당 최대 디바이스 프로비저닝 서비스 | 10 | 예 |
| 최대 등록 수 | 1,000,000 | 예 |
| 개별 등록의 최대 수 | 1,000,000 | 예 |
| 최대 등록 그룹 수 *(X.509 인증서)* | 100 | 예 |
| 최대 등록 그룹 수 *(대칭 키)* | 100 | 아니요 |
| 최대 CA 수 | 25 | 아니요 |
| 연결된 최대 IoT 허브 수 | 50 | 아니요 |
| 최대 메시지 크기 | 96KB| 아니요 |

> [!TIP]
> 대칭 키 등록 그룹에 대한 하드 제한이 차단 문제인 경우 해결 방안으로 개별 등록을 사용하는 것이 좋습니다.

Device Provisioning Service에는 다음과 같은 속도 제한이 있습니다.

| 비용 | 단위당 값 | 조정 가능 |
| --- | --- | --- |
| 작업 | 200/분/서비스 | 예 |
| 디바이스 등록 | 200/분/서비스 | 예 |
| 디바이스 폴링 작업 | 5/10초/디바이스 | 아니요 |


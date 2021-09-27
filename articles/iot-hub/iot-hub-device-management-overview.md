---
title: Microsoft Azure IoT Hub 디바이스 관리 개요
description: 'Azure IoT Hub의 디바이스 관리 개요 - 엔터프라이즈 디바이스 수명 주기 및 디바이스 관리 패턴(예: 다시 부팅, 공장 다시 설정, 펌웨어 업데이트, 구성, 디바이스 쌍, 쿼리, 작업, 위협 감지)'
author: anastasia-ms
ms.service: iot-hub
services: iot-hub
ms.author: v-stharr
ms.topic: conceptual
ms.date: 09/13/2021
ms.custom:
- 'Role: Cloud Development'
- 'Role: IoT Device'
- 'Role: System Architecture'
ms.openlocfilehash: f76996f721287679982f92345ecc075c3db55aba
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128612931"
---
# <a name="overview-of-device-management-with-iot-hub"></a>IoT Hub를 사용한 디바이스 관리 개요

Azure IoT Hub는 디바이스 및 백 엔드 개발자가 강력한 디바이스 관리 솔루션을 빌드할 수 있도록 하는 기능 및 확장성 모델을 제공합니다. 디바이스의 범위는 제한된 센서 및 단일 목적 마이크로컨트롤러에서 다수의 디바이스에 대한 통신을 라우팅하는 강력한 게이트웨이까지를 포함합니다.  또한 IoT 운영자의 사용 사례 및 요구 사항은 여러 산업에서 서로 크게 다릅니다.  IoT Hub 디바이스 관리는 각기 다른 요구 사항을 충족하면서도 기능, 패턴 및 코드 라이브러리를 다양한 디바이스 및 최종 사용자에게 제공합니다.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

성공적인 기업 IoT 솔루션을 만드는 데 있어 중요한 부분은 운영자가 다수의 디바이스를 지속적으로 관리하는 방법에 대한 전략을 제공하는 것입니다. IoT 운영자는 업무 중 보다 전략적인 측면에 중점을 둘 수 있는 간단하고 안정적인 도구와 애플리케이션을 필요로 합니다. 이 문서에서는 다음을 제공합니다.

* 디바이스 관리에 대한 Azure IoT Hub 접근 방식의 간략한 개요
* 일반적인 디바이스 관리 원칙에 대한 설명
* 디바이스 수명 주기에 대한 설명
* 일반적인 디바이스 관리 패턴의 개요

## <a name="device-lifecycle"></a>디바이스 수명 주기

일반 디바이스 관리 단계는 대부분의 엔터프라이즈 IoT 프로젝트에서 일반적입니다. Azure IoT의 디바이스 수명 주기 내에는 5단계가 있습니다.

![5개의 Azure IoT 디바이스 수명 주기 단계는 계획, 구축, 구성, 모니터링, 사용 중지입니다.](./media/iot-hub-device-management-overview/image5.png)

다섯 단계 각각에서 완벽한 솔루션을 제공하기 위해 충족해야 하는 여러 가지 디바이스 연산자 요구 사항이 있습니다.

* **계획**: 운영자가 대량 관리 작업을 위해 디바이스 그룹을 간편하고 정확하게 대상화하고 쿼리하기 위한 디바이스 메타데이터 구성표를 만들 수 있습니다. 디바이스 쌍을 사용하여 이 디바이스 메타데이터를 태그 및 속성 형식으로 저장할 수 있습니다.
  
    *추가 참고 자료*: 
  * [쌍 디바이스 시작](iot-hub-node-node-twin-getstarted.md)
  * [쌍 디바이스 이해](iot-hub-devguide-device-twins.md)
  * [디바이스 쌍 속성을 사용하는 방법](tutorial-device-twins.md)
  * [IoT 솔루션 내에서 디바이스 구성에 대한 모범 사례](iot-hub-configuration-best-practices.md)

* **프로비전**: 새 디바이스를 IoT Hub에 안전하게 프로비전하고 운영자가 디바이스의 기능을 즉시 검색할 수 있도록 합니다.  IoT Hub ID 레지스트리를 사용하여 유연한 디바이스 ID 및 자격 증명을 만들고 작업을 사용하여 대량으로 이 작업을 수행합니다. 디바이스 쌍에서 디바이스 속성을 통해 기능 및 조건을 보고하도록 디바이스를 빌드합니다.
  
    *추가 참고 자료*: 
    * [디바이스 ID 관리](iot-hub-devguide-identity-registry.md)
    * [디바이스 ID의 대량 관리](iot-hub-bulk-identity-mgmt.md)
    * [디바이스 쌍 속성을 사용하는 방법](tutorial-device-twins.md)
    * [IoT 솔루션 내에서 디바이스 구성에 대한 모범 사례](iot-hub-configuration-best-practices.md)
    * [Azure IoT Hub Device Provisioning Service](../iot-dps/index.yml)

* **구성**: 정상적인 상태와 보안을 유지하면서 디바이스에 대한 일괄 구성 변경 및 펌웨어 업데이트를 가능하게 합니다. 원하는 속성 또는 직접 메서드와 브로드캐스트 작업을 사용하여 대량으로 이러한 디바이스 관리 작업을 수행합니다.
  
    *추가 참고 자료*:
    * [디바이스 쌍 속성을 사용하는 방법](tutorial-device-twins.md)
    * [대규모로 IoT 디바이스 구성 및 모니터링](./iot-hub-automatic-device-management.md)
    * [IoT 솔루션 내에서 디바이스 구성에 대한 모범 사례](iot-hub-configuration-best-practices.md)

* **모니터링**: 전반적인 다수의 디바이스 상태와 현재 작업 상태를 모니터링하고 주의가 필요한 문제를 운영자에게 알립니다.  디바이스 쌍을 적용하여 디바이스가 업데이트 작업의 실시간 작동 조건 및 상태를 보고할 수 있도록 합니다. 디바이스 쌍 쿼리를 사용하여 즉각적인 문제를 노출하는 강력한 대시보드 보고서를 작성합니다. 완전 온-프레미스, 클라우드 연결 또는 하이브리드를 비롯한 여러 배포 옵션을 통해 위협으로부터 IoT 환경을 보호합니다.
  
    *추가 참고 자료*: 
    * [디바이스 쌍 속성을 사용하는 방법](tutorial-device-twins.md)
    * [디바이스 쌍, 작업 및 메시지 라우팅에 대한 IoT Hub 쿼리 언어](iot-hub-devguide-query-language.md)
    * [대규모로 IoT 디바이스 구성 및 모니터링](./iot-hub-automatic-device-management.md)
    * [조직에서 포괄적인 위협 탐지를 제공하는 Azure Defender for IoT](../defender-for-iot/organizations/overview.md)
    * [IoT 솔루션 내에서 디바이스 구성에 대한 모범 사례](iot-hub-configuration-best-practices.md)

* **사용 중지**: 오류가 발생하거나, 업그레이드 주기 후에 또는 서비스 수명 주기가 끝나면 디바이스를 교체하거나 서비스를 해제합니다.  디바이스 쌍을 사용하여 물리적 디바이스를 바꾸는 경우 디바이스 정보를 유지하거나 사용이 중지될 경우 보관합니다. IoT Hub ID 레지스트리를 사용하여 디바이스 ID 및 자격 증명을 안전하게 해지합니다.
  
    *추가 참고 자료*: 
    * [디바이스 쌍 속성을 사용하는 방법](tutorial-device-twins.md)
    * [디바이스 ID 관리](iot-hub-devguide-identity-registry.md)

## <a name="device-management-patterns"></a>디바이스 관리 패턴

IoT Hub는 다음과 같은 디바이스 관리 패턴을 가능하게 합니다. [디바이스 관리 자습서](iot-hub-node-node-device-management-get-started.md)에서는 정확한 시나리오에 맞도록 이러한 패턴을 확장하는 방법 및 이러한 핵심 템플릿을 기반으로 새 패턴을 디자인하는 방법을 자세히 알아보겠습니다.

* **다시 부팅:** 백 엔드 앱은 직접 메서드를 통해 다시 부팅을 시작했다고 디바이스에 알릴 수 있습니다.  디바이스는 보고된 속성을 사용하여 디바이스의 재부팅 상태를 업데이트합니다.
  
    ![디바이스 관리 다시 부팅 패턴 그래픽](./media/iot-hub-device-management-overview/reboot-pattern.png)

* **공장 재설정:** 백 엔드 앱은 직접 메서드를 통해 디바이스가 공장 재설정을 시작했다고 알릴 수 있습니다. 디바이스는 보고된 속성을 사용하여 디바이스의 공장 재설정 상태를 업데이트합니다.
  
    ![디바이스 관리 공장 재설정 패턴 그래픽](./media/iot-hub-device-management-overview/facreset-pattern.png)

* **구성**: 백 엔드 앱은 필요한 속성을 사용하여 디바이스에서 실행 중인 소프트웨어를 구성합니다. 디바이스는 보고된 속성을 사용하여 디바이스의 구성 상태를 업데이트합니다.
  
    ![디바이스 관리 구성 패턴 그래픽](./media/iot-hub-device-management-overview/configuration-pattern.png)

* **진행률 및 상태 보고**: 솔루션 백 엔드는 디바이스에서 실행 중인 작업의 상태와 진행률을 보고하기 위하여 일련의 디바이스 전반에 대해 디바이스 쌍 쿼리를 실행합니다.
  
    ![디바이스 관리 보고 진행률 및 상태 패턴 그래픽](./media/iot-hub-device-management-overview/report-progress-pattern.png)

## <a name="device-updates"></a>디바이스 업데이트

[IoT Hub 대한 디바이스 업데이트는](../iot-hub-device-update/understand-device-update.md)  고객이 작은 센서에서 게이트웨이 수준 디바이스에 이르는 모든 것에 대한 무선 업데이트를 게시, 배포 및 관리하는 데 사용할 수 있는 포괄적인 플랫폼입니다. IoT Hub 대한 디바이스 업데이트를 사용하면 고객은 사용자 지정 업데이트 플랫폼을 빌드하는 데 더 많은 개발 및 유지 관리 비용을 발생시키지 않고도 보안 위협에 신속하게 대응하고 비즈니스 목표를 충족하는 기능을 배포할 수 있습니다.

IoT Hub 대한 디바이스 업데이트는 Azure IoT Hub와의 통합을 통해 최적화된 업데이트 배포 및 간소화된 작업을 제공합니다. Azure IoT Edge를 통한 확장된 도달 범위를 통해 거의 모든 디바이스를 연결하는 클라우드 호스팅 솔루션을 제공합니다. Linux 및 Azure RTOS(실시간 운영 체제)를 비롯한 광범위한 IoT 운영 체제를 지원하며 오픈 소스를 통해서도 더 다양합니다. 일부 기능은 다음과 같습니다.

* Azure IoT Edge의 호스트 수준 구성 요소를 포함하여 에지 디바이스 업데이트 지원
* Azure IoT Hub와 통합된 관리 UX 업데이트
* 디바이스 그룹화 및 업데이트 일정 제어를 통한 점진적 업데이트 출시
* 자동화 및 사용자 지정 포털 환경을 사용하도록 설정하는 프로그래밍 API
* 많은 이기종 디바이스에 대한 업데이트 준수 및 상태 요약 보기
* 원활한 롤백을 제공하기 위한 복원력 디바이스 업데이트(A/B) 지원
* 기본 제공 Microsoft 연결된 캐시 및 Azure IoT Edge와의 통합을 통해 중첩된 구성에 있는 디바이스를 포함하여 콘텐츠 캐싱 및 연결이 끊긴 디바이스 지원
* Azure.com 포털을 통해 사용할 수 있는 구독 및 역할 기반 액세스 제어
* 포괄적인 클라우드-에지 보안 기능 및 개인 정보 보호 제어

자세한 내용은 [IoT Hub 대한 디바이스 업데이트를](../iot-hub-device-update/index.yml)참조하세요.

## <a name="next-steps"></a>다음 단계

IoT Hub에서 디바이스 관리를 위해 제공하는 기능, 패턴 및 코드 라이브러리를 사용하면 각 디바이스 수명 주기 단계에서 기업 IoT 운영자 요구 사항을 충족하는 IoT 애플리케이션을 만들 수 있습니다.

IoT Hub 디바이스 관리 기능에 대해 계속 알아보려면 [디바이스 관리 시작](iot-hub-node-node-device-management-get-started.md) 자습서를 참조하세요.
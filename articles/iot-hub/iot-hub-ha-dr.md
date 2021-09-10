---
title: Azure IoT Hub 고가용성 및 재해 복구 | Microsoft Docs
description: 재해 복구 기능을 사용하여 고가용성 Azure IoT 솔루션을 빌드할 수 있도록 지원하는 Azure and IoT Hub 기능을 설명합니다.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: robinsh
ms.openlocfilehash: 6b7fea611eeb3701bc624be8354b4639966dfaa6
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122567441"
---
# <a name="iot-hub-high-availability-and-disaster-recovery"></a>IoT Hub 고가용성 및 재해 복구

복원력 있는 IoT 솔루션을 구현하기 위한 첫 단계로 설계자, 개발자 및 비즈니스 소유자는 구축하는 솔루션의 가동 시간 목표를 정의해야 합니다. 이러한 목표는 기본적으로 각 시나리오에서의 특정 비즈니스 목표에 따라 정의될 수 있습니다. 이러한 컨텍스트에서 [Azure 비즈니스 연속성 기술 지침](/azure/architecture/framework/resiliency/app-design) 문서는 비즈니스 연속성과 재해 복구에 대해 고민해 볼 수 있는 일반 프레임워크를 설명합니다. [Azure 애플리케이션에 대한 재해 복구 및 고가용성](/azure/architecture/reliability/disaster-recovery) 문서는 Azure 애플리케이션에서 HA(고가용성) 및 DR(재해 복구)을 수행하는 전략에 대한 아키텍처 지침을 제공합니다.

이 문서에서는 특히 IoT Hub 서비스에서 제공하는 HA 및 DR 기능을 설명합니다. 이 문서에서 설명하는 영역은 크게 다음과 같습니다.

- 역내 HA
- 지역 간 DR
- 지역 간 HA 달성

IoT 솔루션에 대해 정의한 가동 시간 목표에 따라 아래에 설명된 옵션 중 비즈니스 목표에 가장 적합한 옵션을 결정해야 합니다. 이러한 HA/DR 대안을 IoT 솔루션에 통합할 때는 다음의 장단점을 신중하게 평가해야 합니다.

- 필요한 복원력 수준 
- 구현 및 유지 관리 복잡성
- COGS 영향

## <a name="intra-region-ha"></a>역내 HA

IoT Hub 서비스는 거의 모든 서비스 계층에서 중복성을 구현하여 역내 HA를 제공합니다. [IoT Hub 서비스에 의해 게시된 SLA](https://azure.microsoft.com/support/legal/sla/iot-hub)는 이러한 중복성을 통해 구현됩니다. IoT 솔루션 개발자는 추가적인 작업 없이 이러한 HA 특성을 활용할 수 있습니다. IoT Hub는 비교적 높은 가동 시간을 보장하지만 분산 컴퓨팅 플랫폼에서와 마찬가지로 일시적인 오류는 여전히 발생합니다. 온-프레미스 솔루션에서 클라우드로의 솔루션 마이그레이션을 막 시작했다면 “오류 간 평균 시간”이 아닌 “평균 복구 시간” 최적화로 초점을 이동할 필요가 있습니다. 즉, 클라우드 혼합 운영 중에 일시적 오류는 정상적인 것으로 간주됩니다. 일시적 오류 처리를 위해 클라우드 애플리케이션과 상호 작용하는 적절한 [다시 시도 정책](iot-hub-reliability-features-in-sdks.md)을 구성해야 합니다.

> [!NOTE]
> 또한 일부 Azure 서비스는 [AZ(가용성 영역)](../availability-zones/az-overview.md)과의 통합을 통해 영역 내에서 추가적인 가용성 게층을 제공합니다. AZ는 현재 IoT Hub 서비스에서 지원되지 않습니다.

## <a name="cross-region-dr"></a>지역 간 DR

데이터 센터에서 정전 또는 물리적 자산 관련 오류로 인해 중단 시간이 늘어나는 경우도 드물지만 있을 수 있습니다. 이러한 이벤트는 드물며, 위에서 설명한 역내 HA가 도움이 되지 못할 수도 있습니다. IoT Hub는 이렇게 늘어난 가동 중단에서의 복구를 위한 여러 솔루션을 제공합니다. 

이런 상황에서 고객이 사용할 수 있는 복구 옵션은 [Microsoft 시작 장애 조치(failover)](#microsoft-initiated-failover) 및 [수동 장애 조치(failover)](#manual-failover)입니다. 두 옵션 사이의 기본적인 차이점은 전자는 Microsoft가, 후자는 사용자가 시작하는 점입니다. 또한 수동 장애 조치(failover)는 Microsoft 시작 장애 조치(failover)보다 RTO(복구 시간 목표)가 더 낮습니다. 각 옵션에서 제공하는 특정 RTO는 아래 섹션에서 설명합니다. 이 옵션 중 하나가 주 지역의 IoT Hub 장애 조치(failover)를 실행할 때 허브는 해당 [Azure 지역과 쌍을 이루는 지역](../best-practices-availability-paired-regions.md)에서 완전히 작동하게 됩니다.

두 장애 조치(failover) 옵션 모두 다음 RPO(복구 지점 목표)를 제공합니다.

| 데이터 형식 | RPO(복구 지점 목표) |
| --- | --- |
| ID 레지스트리 |0-5분 데이터 손실 |
| 디바이스 쌍 데이터 |0-5분 데이터 손실 |
| 클라우드-디바이스 메시지<sup>1</sup> |0-5분 데이터 손실 |
| 부모<sup>1</sup> 및 디바이스 작업 |0-5분 데이터 손실 |
| 디바이스-클라우드 메시지 |읽지 않은 메시지가 모두 손실됨 |
| 작업 모니터링 메시지 |읽지 않은 메시지가 모두 손실됨 |
| 클라우드-디바이스 피드백 메시지 |읽지 않은 메시지가 모두 손실됨 |

<sup>1</sup>클라우드-디바이스 메시지와 부모 작업은 수동 장애 조치(failover)의 일환으로 복구되지 않습니다.

IoT Hub에 대한 장애 조치(failover) 작업이 완료되면 해당 디바이스와 백엔드 애플리케이션의 모든 작업이 수동 개입 없이 계속 작동해야 합니다. 즉, 디바이스-클라우드 메시지는 계속 작동하고 전체 디바이스 레지스트리는 그대로 유지되어야 합니다. Event Grid를 통해 내보낸 이벤트는 해당 Event Grid 구독을 계속 사용할 수 있는 한 앞서 구성된 것과 같은 구독을 통해 사용할 수 있습니다. 사용자 지정 엔드포인트에 대한 추가 처리는 필요하지 않습니다.

> [!CAUTION]
> - 장애 조치(failover) 후에는 Event Hub 호환 이름 및 IoT Hub 기본 제공 이벤트 엔드포인트가 변경됩니다. Event Hub 클라이언트나 이벤트 프로세서 호스트를 사용하여 기본 제공 엔드포인트로부터 원격 분석 메시지를 수신할 때는 [IoT 허브 연결 문자열을 사용](iot-hub-devguide-messages-read-builtin.md#read-from-the-built-in-endpoint)하여 연결을 설정해야 합니다. 이를 통해 장애 조치(failover) 후에 수동 개입 없이 백엔드 애플리케이션이 계속 작동하게 됩니다. 애플리케이션에서 직접 Event Hub 호환 이름과 엔드포인트를 사용할 경우, 계속 작동하려면 장애 조치(failover) 후 [새 Event Hub 호환 엔드포인트를 가져와야](iot-hub-devguide-messages-read-builtin.md#read-from-the-built-in-endpoint) 합니다. 자세한 내용은 [수동 장애 조치(failover) 및 이벤트 허브](#manual-failover-and-event-hub)를 참조하세요.
>
> - Azure Functions 또는 Azure Stream Analytics를 사용하여 기본 제공 이벤트 엔드포인트를 연결하는 경우, **다시 시작** 을 수행해야 할 수 있습니다. 이는 장애 조치(failover) 중 이전 오프셋이 더 이상 유효하지 않기 때문입니다.
>
> - 스토리지로 라우팅하는 경우, Blob 또는 파일을 나열한 다음, 이를 반복하여 파티션을 가정하지 않고 모든 Blob 또는 파일을 읽을 수 있도록 하는 것이 좋습니다. 파티션 범위는 Microsoft 시작 장애 조치 또는 수동 장애 조치 중에 변경할 수 있습니다. Blob 목록을 열거하기 위해 [Blob API 나열](/rest/api/storageservices/list-blobs)을 사용하거나 파일 목록에 대해 [ADLS Gen2 API 나열](/rest/api/storageservices/datalakestoragegen2/filesystem/list)을 사용할 수 있습니다. 자세한 내용은 [라우팅 엔드포인트로서의 Azure Storage](iot-hub-devguide-messages-d2c.md#azure-storage-as-a-routing-endpoint)를 참조하세요.

## <a name="microsoft-initiated-failover"></a>Microsoft 시작 장애 조치

Microsoft 시작 장애 조치(failover)는 해당 지역의 모든 IoT Hub를 상응하는 지역 쌍 지역으로 장애 조치(failover)하는 드문 상황에서 Microsoft에 의해 실행됩니다. 이 프로세스는 기본 옵션(사용자가 옵트아웃할 수 없음)이며 사용자 개입이 필요하지 않습니다. Microsoft는 이 옵션을 실행할 시기를 판단할 권리를 보유합니다. 이 메커니즘에는 사용자의 허브가 장애 조치(failover)되기 전 사용자 동의가 포함되지 않습니다. Microsoft 시작 장애 조치(failover)의 RTO(복구 시간 목표)는 2-26시간입니다. 

Microsoft가 해당 지역의 영양을 받는 모든 고객을 대신해 장애 조치(failover)를 수행하게 되므로 RTO가 큽니다. 대략 하루 정도의 가동 중지 시간을 용인할 수 있는 중요도 낮은 IoT 솔루션을 실행 중인 경우, IoT 솔루션의 전체 재해 복구 목표를 만족하기 위해 이 옵션을 사용할 수 있습니다. 이 프로세스가 트리거된 후 런타임 작업이 완전히 작동하게 되는 총 시간은 "복구 시간" 섹션에서 설명합니다.

## <a name="manual-failover"></a>수동 장애 조치(failover)

Microsoft 시작 장애 조치가 제공하는 RTO로는 비즈니스 가동 시간 목표를 만족할 수 없다면 사용자 자체의 장애 조치 프로세스를 트리거하는 수동 장애 조치를 고려합니다. 이 옵션 사용의 RTO는 10분에서 몇 시간 사이입니다. RTO는 현재 장애 조치(failover)되는 IoT Hub에 대해 등록된 디바이스 수의 함수입니다. 약 100,000대의 디바이스를 호스팅하는 허브의 RTO는 15분 전후로 예상할 수 있습니다. 이 프로세스가 트리거된 후 런타임 작업이 완전히 작동하게 되는 총 시간은 "복구 시간" 섹션에서 설명합니다.

수동 장애 조치(failover) 옵션은 주 지역의 가동 중지 시간 발생 여부에 관계없이 항상 사용할 수 있습니다. 따라서 이 옵션을 통해 계획된 장애 조치(failover)를 수행하는 데 사용할 수 있습니다. 계획된 장애 조치(Failover)의 한 사용 예로 정기 장애 조치(failover) 훈련을 들 수 있습니다. 그러나 계획된 장애 조치(Failover) 작업은 이 옵션에 대한 RTO에서 정의한 기간에 대해 허브 가동 중지 시간이 발생하고 위의 RPO 테이블에서 정의한 데이터 손실을 초래합니다. 실제 재해가 발생했을 때 엔드투엔드 솔루션이 가동되어 실행되는 상태를 확실히 유지할 수 있도록 주기적으로 계획된 장애 조치(failover) 옵션을 실행하는 테스트 IoT Hub 인스턴스를 설정할 수 있습니다.

2017년 5월 18일 이후, 생성된 IoT 허브에 대한 추가 비용 없이 수동 장애 조치를 사용할 수 있습니다.

단계별 지침은 [자습서: IoT 허브에 대한 수동 장애 조치 수행](tutorial-manual-failover.md)을 참조하세요.

## <a name="manual-failover-and-event-hub"></a>수동 장애 조치(failover) 및 이벤트 허브

위의 **주의** 섹션에서 설명한 대로 수동 장애 조치(failover) 후에는 IoT Hub 기본 제공 이벤트 엔드포인트의 Event Hub 호환 이름 및 엔드포인트가 변경됩니다. 이는 이벤트 허브 클라이언트가 IoT Hub 이벤트를 볼 수 없기 때문입니다. Functions 및 Azure Stream Analytics 같은 다른 클라우드 기반 클라이언트의 경우도 마찬가지입니다. 엔드포인트 및 이름을 검색하려면 Azure Portal을 사용하거나 포함된 샘플을 활용할 수 있습니다.

### <a name="use-the-portal"></a>포털 사용

포털을 사용하여 Event Hub 호환 엔드포인트 및 Event Hub 호환 이름을 검색하는 방법에 대한 자세한 내용은 [기본 제공 엔드포인트에서 읽기](iot-hub-devguide-messages-read-builtin.md#read-from-the-built-in-endpoint)를 참조하세요.

### <a name="use-the-included-sample"></a>포함된 샘플 사용

IoT Hub 연결 문자열을 사용하여 Event Hub 호환 엔드포인트를 다시 캡처하려면 IoT Hub 연결 문자열을 사용하여 EventHub 호환 엔드포인트를 다시 캡처하는 방법을 보여 주는 샘플([https://github.com/Azure/azure-sdk-for-net/tree/main/samples/iothub-connect-to-eventhubs](https://github.com/Azure/azure-sdk-for-net/tree/main/samples/iothub-connect-to-eventhubs))을 활용합니다. 코드 예제에서는 연결 문자열을 사용하여 새 이벤트 허브 엔드포인트를 가져오고 연결을 다시 설정합니다. Visual Studio가 설치되어 있어야 합니다.

### <a name="running-test-drills"></a>테스트 드릴 실행

프로덕션 환경에서 사용 중인 IoT Hub에서는 테스트 훈련을 수행하면 안 됩니다.

### <a name="dont-use-manual-failover-to-migrate-iot-hub-to-a-different-region"></a>수동 장애 조치를 사용하여 다른 지역으로 IoT hub 마이그레이션하지 마십시오

수동 장애 조치는 Azure 지역 쌍을 이루는 지역 간에 허브를 영구적으로 마이그레이션하는 메커니즘으로 사용하지 *않아야* 합니다. 이렇게 하면 이전 주 지역에 상주하는 디바이스로부터 IoT 허브에 대해 작업을 수행하는 데 대기 시간이 길어집니다.

## <a name="failback"></a>장애 복구

이전 주 지역으로의 장애 복구는 다른 시간에 장애 조치(failover)를 트리거하여 수행할 수 있습니다. 원래의 장애 조치(failover) 작업이 원래의 주 지역에서 연장된 중단으로부터 복구하기 위해 수행되었다면 위치가 중단 상황에서 복구된 후에는 원래의 위치로 허브를 장애 복구하는 것이 좋습니다.

> [!IMPORTANT]
> - 사용자는 매일 2회의 성공적인 장애 조치와 2개의 성공적인 장애 복구 작업이 허용됩니다.
>
> - 역방향 장애 조치(failover)/장애 복구(failback) 작업은 허용되지 않습니다. 이러한 작업 사이에 1시간 동안 기다려야 합니다.

## <a name="time-to-recover"></a>복구 시간

장애 조치(failover) 후에 IoT 허브의 FQDN(및 따라서 연결 문자열)는 동일하지만 기본 IP 주소가 변경됩니다. 따라서 장애 조치(failover) 프로세스가 트리거된 후 완전히 작동하게 되는 IoT hub 인스턴스에 대해 수행되는 런타임 작업의 전체 시간은 다음 함수를 통해 나타낼 수 있습니다.

복구 시간 = RTO [수동 장애 조치(failover)의 경우 10분~2시간 | Microsoft 시작 장애 조치(failover)의 경우 2~26시간 ] + DNS 전파 지연 + 클라이언트 애플리케이션이 캐시된 IoT Hub IP 주소를 새로 고치는 데 걸리는 시간

> [!IMPORTANT]
> IoT SDK는 IoT Hub의 IP 주소를 캐시하지 않습니다. SDK와 상호 작용하는 사용자 코드는 IoT Hub의 IP 주소를 캐시하지 않는 것이 좋습니다.

## <a name="achieve-cross-region-ha"></a>지역 간 HA 달성

Microsoft 시작 장애 조치(failover)나 수동 장애 조치(failover) 옵션이 제공하는 RTO로 비즈니스 가동 시간 목표가 충족되지 않는 경우 디바이스별 자동 지역 간 장애 조치(failover) 메커니즘의 구현을 고려해야 합니다.
IoT 솔루션으로 배포 토폴로지를 완벽하게 수행하는 것은 이 문서의 범위를 벗어납니다. 이 문서는 고가용성 및 재해 복구를 목적으로 하는 *국가별 장애 조치* 배포 모델을 설명합니다.

지역적 장애 조치(failover) 모델에서 솔루션 백 엔드는 기본적으로 하나의 데이터센터 위치에서 실행됩니다. 보조 IoT 허브 및 백 엔드는 다른 데이터 센터 위치에 배포됩니다. 기본 지역의 IoT Hub에 중단이 발생하거나 디바이스에서 기본 지역으로의 네트워크 연결이 중단되는 경우, 디바이스는 보조 서비스 엔드포인트를 사용합니다. 단일 지역 내에 머무르지 않고 지역 간 장애 조치(failover) 모델을 구현하여 솔루션 가용성을 향상할 수 있습니다. 

높은 수준에서 IoT Hub로 국가별 장애 조치를 구현하려면 다음 단계를 수행해야 합니다.

* **보조 IoT Hub 및 디바이스 라우팅 논리**: 주 지역에서 서비스 중단이 발생하는 경우 디바이스는 보조 지역으로 연결을 시작해야 합니다. 관련된 대부분의 서비스가 상태를 인식하는 특성이 있으므로 일반적으로 솔루션 관리자는 지역 간 장애 조치(failover) 프로세스를 트리거합니다. 프로세스에 대한 제어를 유지하면서 새 엔드포인트에서 디바이스로 통신하는 가장 좋은 방법은 현재 활성 엔드포인트에 대해 *안내자* 서비스를 정기적으로 확인하는 것입니다. 안내자 서비스는 DNS-리디렉션 기술(예: [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md))을 사용하여 복제되고 연결을 유지할 수 있는 웹 애플리케이션입니다.

   > [!NOTE]
   > IoT Hub 서비스는 Azure Traffic Manager에서 지원되는 엔드포인트 유형이 아닙니다. 엔드포인트 상태 프로브 API를 구현하여 Azure Traffic Manage와 제안된 컨시어지 서비스를 통합하는 것이 좋습니다.

* **ID 레지스트리 복제**: 사용하려면 보조 IoT Hub가 솔루션에 연결할 수 있는 모든 디바이스 ID를 포함해야 합니다. 솔루션은 지역에서 복제된 디바이스 ID의 백업을 유지하고 이를 보조 IoT Hub로 업로드한 후 디바이스에 대한 활성 엔드포인트를 전환해야 합니다. 이 경우 IoT Hub의 디바이스 ID 내보내기 기능은 유용합니다. 자세한 내용은 [IoT Hub 개발자 가이드 - ID 레지스트리](iot-hub-devguide-identity-registry.md)를 참조하세요.

* **논리 병합**: 주 지역을 다시 사용할 수 있게 된 경우 보조 사이트에 생성된 모든 상태 및 데이터를 주 지역으로 다시 마이그레이션해야 합니다. 이러한 상태 및 데이터는 주로 디바이스 ID 및 애플리케이션 메타데이터와 관련되며 기본 IoT Hub 및 주 지역의 기타 가능한 애플리케이션별 스토리지에 병합되어야 합니다. 

이러한 단계를 간소화하려면 멱등 연산을 사용해야 합니다. 멱등 연산은 최후의 일관적인 이벤트 배포 및 중복되거나 비순차적인 이벤트 배달로 인한 부작용을 최소화합니다. 또한 애플리케이션 논리는 잠재적인 불일치 또는 약간 오래된 상태를 허용할 수 있도록 설계되어야 합니다. 이러한 상황은 시스템이 RPO(복구 지점 목표)에 기반하여 치료하는 데 추가로 소요되는 시간으로 인해 발생할 수 있습니다.

## <a name="choose-the-right-hadr-option"></a>적절한 HA/DR 옵션 선택

여기서는 이 문서에서 설명한 HA/DR 옵션을 요약하며, 솔루션에 대해 작동하는 올바른 옵션을 선택하기 위한 참조로 사용할 수 있습니다.

| HA/DR 옵션 | RTO | RPO | 수동 개입 필요 여부 | 구현 복잡성 | 추가 비용 영향|
| --- | --- | --- | --- | --- | --- |
| Microsoft 시작 장애 조치 |2~26시간|위의 RPO 표 참조|예|None|없음|
| 수동 장애 조치(failover) |10분~2시간|위의 RPO 표 참조|예|매우 낮음. 포털에서 이 작업을 트리거하기만 하면 됩니다.|없음|
| 지역 간 HA |1분 미만|사용자 지정 HA 솔루션의 복제 빈도에 따라 다름|예|높은|> IoT Hub 1개의 비용 미만|

## <a name="next-steps"></a>다음 단계

* [Azure IoT Hub란?](about-iot-hub.md)
* [IoT Hub 시작(빠른 시작)](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-csharp)
* [자습서: IoT Hub에 대해 수동 장애 조치(failover) 수행](tutorial-manual-failover.md)

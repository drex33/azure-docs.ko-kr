---
title: 사용자 지정 메트릭을 추가하는 방법 - Azure IoT Edge
description: 사용자 지정 모듈에서 시나리오 관련 메트릭을 사용하여 기본 제공 메트릭 확대
author: veyalla
ms.author: veyalla
ms.date: 08/11/2021
ms.topic: conceptual
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 8e9c5b74b19af00228f03b26450b987d87283376
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122529579"
---
# <a name="add-custom-metrics-preview"></a>사용자 지정 메트릭 추가(미리 보기)

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

시스템 모듈에서 제공하는 기본 제공 메트릭 외에도 IoT Edge 모듈에서 사용자 지정 메트릭을 수집합니다. [기본 제공 메트릭](how-to-access-built-in-metrics.md)을 사용하면 기본적인 수준의 배포 상태를 파악할 수 있습니다. 그러나 전체적인 상황을 좀 더 완벽하게 파악하려면 사용자 지정 모듈의 추가 정보가 필요할 수 있습니다. 적절한 [Prometheus 클라이언트 라이브러리](https://prometheus.io/docs/instrumenting/clientlibs/)를 사용하여 메트릭을 내보내 사용자 지정 모듈을 모니터링 솔루션에 통합할 수 있습니다. 이 추가 정보를 통해 사용자의 요구 사항에 맞게 조정된 새로운 보기 또는 경고를 사용할 수 있습니다.

## <a name="sample-modules-repository"></a>샘플 모듈 리포지토리

메트릭을 내보내기 위해 계측된 사용자 지정 모듈의 예제는 [azure-samples 리포지토리](https://github.com/Azure-Samples/iotedge-module-prom-custom-metrics)를 참조하세요. 선택한 언어로 된 샘플이 아직 제공되지 않은 경우에도 일반적인 방법으로 도움을 얻을 수 있습니다.

## <a name="naming-conventions"></a>명명 규칙

일반적인 지침은 Prometheus 문서의 [모범 사례](https://prometheus.io/docs/practices/naming/)를 참조하세요. 다음 추가 권장 사항은 IoT Edge 시나리오에 유용할 수 있습니다.

* 메트릭 이름 시작 부분에 모듈 이름을 포함하여 메트릭을 내보낸 모듈을 명확히 나타냅니다.

* 모든 메트릭에는 IoT Hub 이름 또는 IoT Central 애플리케이션 이름, IoT Edge 디바이스 ID 및 모듈 ID를 레이블(‘태그/차원’이라고도 함)로 포함합니다/. 이 정보는 IoT Edge 에이전트가 시작한 모든 모듈에 대해 환경 변수로 사용할 수 있습니다. 이 접근 방식은 샘플 리포지토리에 [예시되어 있습니다](https://github.com/Azure-Samples/iotedge-module-prom-custom-metrics/blob/b6b8501adb484521b76e6f317fefee57128834a6/csharp/Program.cs#L49). 이 컨텍스트가 없으면 지정된 메트릭 값을 특정 디바이스에 연결할 수 없습니다.

* 레이블에 인스턴스 ID를 포함합니다. 인스턴스 ID는 모듈을 시작하는 동안 생성된 [GUID](https://en.wikipedia.org/wiki/Universally_unique_identifier)와 같은 고유한 ID일 수 있습니다. 인스턴스 ID 정보는 백 엔드에서 모듈의 메트릭을 처리할 때 모듈 다시 시작을 조정하는 데 도움이 됩니다.

## <a name="configure-the-metrics-collector-to-collect-custom-metrics"></a>메트릭 수집기를 구성하여 사용자 지정 메트릭 수집

사용자 지정 모듈이 메트릭을 내보내는 경우 다음 단계는 사용자 지정 메트릭을 수집하고 전송하도록 [메트릭-수집기 모듈](how-to-collect-and-transport-metrics.md#metrics-collector-module)을 구성하는 것입니다.

사용자 지정 모듈 메트릭 엔드포인트의 URL을 포함하도록 환경 변수 `MetricsEndpointsCSV`를 업데이트해야 합니다. 환경 변수를 업데이트하는 경우 [메트릭 수집기 구성](how-to-collect-and-transport-metrics.md#metrics-collector-configuration) 예제에 표시된 대로 시스템 모듈 엔드포인트를 포함해야 합니다.

>[!NOTE]
>기본적으로 메트릭 수집기의 액세스를 허용하기 위해 사용자 지정 모듈의 메트릭 엔드포인트를 호스트 포트에 매핑할 필요는 없습니다. Linux에서 명시적으로 재정의되지 않는 한, 두 모듈은 모두 *azure-iot-edge* 라는 [사용자 정의 Docker 브리지 네트워크](https://docs.docker.com/network/bridge/#differences-between-user-defined-bridges-and-the-default-bridge)에서 시작됩니다.
>
>사용자 정의 Docker 네트워크에는 모듈(컨테이너) 이름을 사용한 모듈 간 통신을 허용하는 기본 DNS 확인자가 포함됩니다. 예를 들어, 이름이 ‘module1’인 사용자 지정 모듈이 경로 ‘/metrics’에서 http 포트 ‘9600’에서 메트릭을 내보내는 경우 수집기는 엔드포인트 *http://module1:9600/metrics* 에서 수집하도록 구성해야 합니다.

IoT Edge 디바이스에서 다음 명령을 실행하여 경로 ‘/metrics’의 http 포트  ‘9600’에서 사용자 지정 모듈이 내보낸 메트릭에 액세스할 수 있는지 테스트합니다. .

```bash
sudo docker exec replace-with-metrics-collector-module-name curl http://replace-with-custom-module-name:9600/metrics
```

## <a name="add-custom-visualizations"></a>사용자 지정 시각화 추가

Log Analytics에서 사용자 지정 메트릭을 받으면 사용자 지정 시각화 및 경고를 만들 수 있습니다. 모니터링 통합 문서를 확대하여 쿼리 지원 시각화를 추가할 수 있습니다.

모든 메트릭은 IoT Hub 또는 IoT Central 애플리케이션의 리소스 ID와 연결됩니다. 따라서 Log Analytics 작업 영역을 지원하는 대신 연결된 IoT Hub 또는 IoT Central 애플리케이션의 **로그** 페이지에서 사용자 지정 메트릭이 올바르게 수집되었는지 확인할 수 있습니다. 이 기본 KQL 쿼리를 사용하여 다음을 확인합니다.

```KQL
InsightsMetrics
| where Name == 'replace-with-custom-metric-name'
```

수집을 확인한 후에는 새 통합 문서를 만들거나 기존 통합 문서를 확대할 수 있습니다. 큐레이팅된 [IoT Edge 통합 문서](how-to-explore-curated-visualizations.md)의 [통합 문서 설명서](../azure-monitor/visualize/workbooks-overview.md)를 지침으로 사용합니다.

결과가 만족스러울 경우 팀과 [통합 문서](../azure-monitor/visualize/workbooks-access-control.md)를 공유하거나 조직 리소스 배포의 일부로 [프로그래밍 방식으로 배포](../azure-monitor/visualize/workbooks-automate.md)할 수 있습니다.

## <a name="next-steps"></a>다음 단계

[큐레이팅된 통합 문서](how-to-explore-curated-visualizations.md)를 사용하여 추가 메트릭 시각화 옵션을 살펴봅니다.

---
title: 모니터링 문제 해결 및 FAQ - Azure IoT Edge
description: Azure Monitor 통합 및 FAQ 문제 해결
author: veyalla
ms.author: veyalla
ms.date: 06/09/2021
ms.topic: conceptual
ms.reviewer: kgremban
ms.service: iot-edge
services: iot-edge
zone_pivot_groups: how-to-troubleshoot-monitoring-and-faq-zpg
ms.openlocfilehash: 8c4e74bbd72abdbfaf365afe720149f0eb7fe8ae
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130215394"
---
# <a name="faq-and-troubleshooting"></a>FAQ 및 문제 해결

:::zone pivot="metrics-collection"

## <a name="collector-module-is-unable-to-collect-metrics-from-built-in-endpoints"></a>수집기 모듈이 기본 제공 엔드포인트에서 메트릭을 수집할 수 없습니다.

### <a name="check-if-modules-are-on-the-same-docker-network"></a>모듈이 동일한 Docker 네트워크에 있는지 확인

메트릭 수집기 모듈은 사용자 정의 네트워크에 대해 Docker의 포함된 DNS 확인자를 사용합니다. DNS 확인자는 모듈 이름을 포함하는 메트릭 엔드포인트에 대한 IP 주소를 제공합니다. 예: *http://**edgeHub**:9600/metrics*.

모듈이 동일한 네트워크 네임스페이스에서 실행되지 않는 경우 이 메커니즘은 실패합니다. 예를 들어 일부 시나리오에서는 호스트 네트워크에서 모듈을 실행해야 합니다. 메트릭 수집기 모듈이 다른 네트워크에 있는 경우 이러한 시나리오에서 수집이 실패합니다.

### <a name="verify-that-httpsettings__enabled-environment-variable-isnt-set-to-false"></a>*httpSettings__enabled* 환경 변수가 *false* 로 설정되어 있지 않은지 확인합니다.

IoT Edge 시스템 모듈에서 노출되는 기본 제공 메트릭 엔드포인트는 http 프로토콜을 사용합니다. Edge Hub 또는 Edge Agent 모듈에서 환경 변수 설정을 통해 http를 명시적으로 사용하지 않도록 설정한 경우 모듈 네트워크 내에서도 사용할 수 없습니다.

### <a name="set-no_proxy-environment-variable-if-using-http-proxy-server"></a>http 프록시 서버를 사용하는 경우 *NO_PROXY* 환경 변수 설정

자세한 내용은 [프록시 고려 사항](how-to-collect-and-transport-metrics.md#proxy-considerations)을 참조하세요.

### <a name="update-moby-engine"></a>Moby 엔진 업데이트

Linux 호스트에서 최신 버전의 컨테이너 엔진을 사용하고 있는지 확인합니다. [설치 지침](how-to-provision-single-device-linux-symmetric.md#install-iot-edge)에 따라 최신 버전으로 업데이트하는 것이 좋습니다.

## <a name="how-do-i-collect-logs-along-with-metrics"></a>로그를 메트릭과 함께 수집하려면 어떻게 해야 하나요?

[기본 제공 로그 끌어오기 기능](how-to-retrieve-iot-edge-logs.md)을 사용할 수 있습니다. 기본 제공 로그 검색 기능을 사용하는 샘플 솔루션은 [ **https://aka.ms/iot-elms**](https://aka.ms/iot-elms)에서 사용할 수 있습니다.

## <a name="why-cant-i-see-device-metrics-in-the-metrics-page-in-azure-portal"></a>Azure Portal의 메트릭 페이지에서 디바이스 메트릭이 표시되지 않는 이유는 무엇인가요?

Azure Monitor의 [기본 메트릭](../azure-monitor/essentials/data-platform-metrics.md) 기술은 아직 Prometheus 데이터 형식을 직접 지원하지 않습니다. 로그 기반 메트릭은 현재 다음과 같은 이유로 IoT Edge 메트릭에 더 적합합니다.

* 표준 *InsightsMetrics* 테이블을 통해 Prometheus 메트릭 형식을 기본적으로 지원합니다.
* 시각화 및 경고를 위해 [KQL](/azure/data-explorer/kusto/query/)을 통한 고급 데이터 처리.

메트릭 데이터베이스로 Log Analytics를 사용하는 것은 **메트릭** 이 아닌 Azure Portal의 **로그** 페이지에 메트릭이 표시되는 이유입니다.

## <a name="how-do-i-configure-metrics-collector-in-a-layered-deployment"></a>계층화된 배포에서 메트릭 수집기를 구성하려면 어떻게 해야 하나요?

메트릭 수집기에는 서비스 검색 기능이 없습니다. 기본 또는 *하위* 배포 계층에 모듈을 포함하는 것이 좋습니다. 모듈의 구성 내에 모듈이 배포될 수 있는 모든 메트릭 엔드포인트를 포함합니다. 모듈이 최종 배포에 표시되지 않지만 엔드포인트가 컬렉션 목록에 표시되는 경우 수집기는 수집, 실패 및 이동을 시도합니다.

:::zone-end

:::zone pivot="custom-metrics"

## <a name="how-do-i-augment-the-monitoring-solution-with-custom-metrics"></a>사용자 지정 메트릭을 사용하여 모니터링 솔루션을 어떻게 확장하나요?

[사용자 지정 메트릭](how-to-add-custom-metrics.md) 문서를 참조하세요.

## <a name="how-can-i-tell-which-device-a-particular-metric-belongs-to"></a>특정 메트릭이 속한 디바이스를 어떻게 알 수 있나요?

메트릭 레이블에서 디바이스 정보를 인코딩합니다. 자세한 내용은 [명명 규칙](how-to-add-custom-metrics.md#naming-conventions)을 참조하세요.

:::zone-end

:::zone pivot="alerts"

## <a name="how-do-i-create-a-alert-rule-that-spans-devices-from-multiple-iot-hubs"></a>여러 IoT 허브에서 확장하는 경고 규칙을 만들려면 어떻게 해야 하나요?

[경고 규칙을 만들](how-to-create-alerts.md#create-an-alert-rule) 때 리소스 그룹 또는 구독에 대한 [범위를 변경](how-to-create-alerts.md#select-alert-rule-scope)할 수 있습니다. 그러면 경고 규칙이 해당 범위의 모든 IoT 허브에 적용됩니다.

## <a name="alerts-arent-firing-when-they-should"></a>경고가 발생해야 할 때 발생하지 않음

경고 규칙을 만들 때 미리 보기 그래프를 확인하여 경고 논리가 트리거되는 지 확인합니다.

문제를 찾을 수 없는 경우 **Log Analytics** 서비스에 대한 [기술 지원 인시던트](https://azure.microsoft.com/support/create-ticket/)를 만듭니다.

:::zone-end

:::zone pivot="workbooks"

## <a name="my-device-isnt-showing-up-in-the-monitoring-workbook"></a>내 디바이스가 모니터링 통합 문서에 표시되지 않습니다.

통합 문서는 *ResourceId* 를 사용하여 올바른 IoT Hub 또는 IoT Central 애플리케이션에 연결되는 디바이스 메트릭에 따라 다릅니다. 메트릭 수집기가 올바른 *ResourceId* 로 [구성](how-to-collect-and-transport-metrics.md#metrics-collector-configuration)되어 있는지 확인합니다.

메트릭 수집기 모듈 로그를 사용하여 선택한 시간 범위 동안 디바이스가 메트릭을 보냈는지 확인합니다.

메트릭이 표시되기까지 몇 분의 수집 지연이 있을 수 있습니다.

## <a name="i-found-a-bug-or-have-a-question-about-metrics-being-shown-in-the-workbook"></a>버그를 발견했거나 통합 문서에 표시되는 메트릭에 대한 질문이 있습니다.

제목에 '[monitor-workbook]'이 있는 [Azure IoT Edge GitHub 리포지토리](https://github.com/azure/iotedge/issues)에서 문제를 엽니다.

통합 문서의 템플릿은 [GitHub에서 공개적으로 사용할 수 있습니다](https://github.com/microsoft/Application-Insights-Workbooks/tree/master/Workbooks/IoTHub). 개선 사항이나 수정 사항이 있는 끌어오기 요청은 매우 유용합니다.

## <a name="i-cannot-see-the-workbooks-in-the-public-templates"></a>공용 템플릿에서 통합 문서를 볼 수 없습니다.

Log Analytics 작업 영역이 아닌 포털의 IoT 허브 또는 IoT Central 애플리케이션 페이지에 있는 **통합 문서** 페이지를 보고 있는지 확인합니다.

그래도 통합 문서를 볼 수 없다면 사전 프로덕션 Azure Portal 환경([`https://ms.portal.azure.com`](https://ms.portal.azure.com))을 사용해 보세요. 통합 문서 업데이트가 프로덕션 환경에 표시되는 데 추가 시간이 걸리는 경우가 있지만 사전 프로덕션 환경에서 사용할 수 있습니다.

:::zone-end
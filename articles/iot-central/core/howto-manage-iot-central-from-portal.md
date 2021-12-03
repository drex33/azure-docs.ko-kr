---
title: Azure Portal에서 IoT Central 관리 및 모니터링 | Microsoft Docs
description: 이 문서에서는 IoT Central 응용 프로그램을 만들고, 관리 하 고, 모니터링 하 고 Azure Portal에서 관리 되는 id를 사용 하도록 설정 하는 방법을 설명 합니다.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 07/06/2021
ms.topic: how-to
ms.openlocfilehash: b39db67bade33d015dc92dc0495042f2864d3bdb
ms.sourcegitcommit: 5b25f76d0fd0ffb6784a2afab808fa55b3eac07b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2021
ms.locfileid: "133518103"
---
# <a name="manage-and-monitor-iot-central-from-the-azure-portal"></a>Azure Portal에서 IoT Central 관리 및 모니터링

[Azure Portal](https://portal.azure.com)을 사용하여 IoT Central 애플리케이션을 만들고 관리하고 모니터링할 수 있습니다.

## <a name="create-iot-central-applications"></a>IoT Central 애플리케이션 만들기

[!INCLUDE [Warning About Access Required](../../../includes/iot-central-warning-contribitorrequireaccess.md)]

애플리케이션을 만들려면 Azure Portal의 [IoT Central 애플리케이션](https://ms.portal.azure.com/#create/Microsoft.IoTCentral) 페이지로 이동합니다.

![IoT Central 양식 만들기](media/howto-manage-iot-central-from-portal/create-form.png)

* **리소스 이름** 은 Azure 리소스 그룹의 IoT Central 애플리케이션에 대해 선택할 수 있는 고유한 이름입니다.

* **애플리케이션 URL** 은 애플리케이션 액세스에 사용할 수 있는 URL입니다.

* **템플릿** 은 만들려는 IoT Central 애플리케이션의 형식입니다. 업계 관련 템플릿 목록에서 새 애플리케이션을 만들어 빠르게 시작하거나, **사용자 지정 애플리케이션** 템플릿을 사용하여 처음부터 시작할 수 있습니다.

* **Location** 은 응용 프로그램을 만들려는 [Azure 지역](https://azure.microsoft.com/global-infrastructure/geographies/) 입니다. 일반적으로 최적의 성능을 얻으려면 디바이스와 물리적으로 가장 가까운 위치를 선택해야 합니다. Azure IoT Central는 현재 다음 위치에서 사용할 수 있습니다.
    
    * 오스트레일리아
    * 미국 중부 미국
    * 미국 동부
    * 미국 동부 2
    * 일본 동부
    * 북유럽
    * 동남아시아
    * 영국 남부
    * 서유럽
    * 미국 서부

  위치를 선택하면 나중에 다른 위치로 애플리케이션을 이동할 수 없습니다.

모든 필드를 채운 후 **만들기** 를 선택합니다. 자세한 내용은 [IoT Central 애플리케이션 만들기](howto-create-iot-central-application.md)를 참조하세요.

## <a name="manage-existing-iot-central-applications"></a>기존 IoT Central 애플리케이션 관리

Azure IoT Central 애플리케이션이 이미 있으면 삭제하거나 Azure Portal의 다른 구독 또는 리소스 그룹으로 이동할 수 있습니다.

> [!NOTE]
> *무료* 요금제를 사용하여 만든 애플리케이션에는 Azure 구독이 필요하지 않으므로 Azure Portal의 Azure 구독에 나열되지 않습니다. IoT Central 포털에서만 무료 앱을 보고 관리할 수 있습니다.

시작하려면 Azure Portal 맨 위에 있는 검색 창에서 애플리케이션을 검색합니다. _IoT Central 애플리케이션_ 을 검색하고 서비스를 선택하여 모든 애플리케이션을 볼 수도 있습니다.

![첫 번째 서비스를 선택한 상태에서 "IoT Central 애플리케이션"에 대한 검색 결과를 보여 주는 스크린샷](media/howto-manage-iot-central-from-portal/search-iot-central.png)

검색 결과에서 애플리케이션을 선택하면 Azure Portal에 해당 개요가 표시됩니다. **IoT Central 애플리케이션 URL** 을 선택하여 해당 애플리케이션으로 이동할 수 있습니다.

!["IoT Central 애플리케이션 URL"이 강조 표시된 "개요" 페이지를 보여주는 스크린샷.](media/howto-manage-iot-central-from-portal/highlight-application.png)

> [!NOTE]
> **IoT Central 응용 프로그램 URL** 을 사용 하 여 응용 프로그램에 처음으로 액세스 합니다.

다른 리소스 그룹으로 애플리케이션을 이동하려면 리소스 그룹 옆에 있는 **변경** 을 선택합니다. **리소스 이동** 페이지에서 이 애플리케이션을 이동할 리소스 그룹을 선택합니다.

!["리소스 그룹(변경)"이 강조 표시된 "개요" 페이지를 보여주는 스크린샷.](media/howto-manage-iot-central-from-portal/highlight-resource-group.png)

다른 구독으로 애플리케이션을 이동하려면 구독 옆에 있는 **변경** 을 선택합니다. **리소스 이동** 페이지에서 이 애플리케이션을 이동할 구독을 선택합니다.

![관리 포털: 리소스 관리](media/howto-manage-iot-central-from-portal/highlight-subscription.png)

## <a name="configure-a-managed-identity"></a>관리 ID 구성

IoT Central 응용 프로그램에서 데이터 내보내기를 구성할 때 *연결 문자열* 또는 [관리 id](../../active-directory/managed-identities-azure-resources/overview.md)를 사용 하 여 대상에 대 한 연결을 구성 하도록 선택할 수 있습니다. 관리 되는 id는 다음과 같은 이유로 더 안전 합니다.

* IoT Central 응용 프로그램의 연결 문자열에 리소스에 대 한 자격 증명을 저장 하지 않습니다.
* 자격 증명은 자동으로 IoT Central 응용 프로그램의 수명에 연결 됩니다.
* 관리 id는 자동으로 보안 키를 정기적으로 회전 합니다.

IoT Central 현재 [시스템 할당 관리 id](../../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types)를 사용 합니다. 응용 프로그램에 대 한 관리 되는 id를 만들려면 Azure Portal 또는 REST API를 사용 합니다.

> [!NOTE]
> 지역에서 만든 IoT Central 응용 프로그램에만 관리 되는 id를 추가할 수 있습니다. 모든 새 응용 프로그램은 한 지역에 생성 됩니다. 자세히 알아보려면 [업데이트](https://azure.microsoft.com/updates/azure-iot-central-new-and-updated-features-august-2021/)를 참조 하세요.

관리 id를 구성 하는 경우 구성에는 *범위* 와 *역할이* 포함 됩니다.

* 범위는 관리 되는 id를 사용할 수 있는 위치를 정의 합니다. 예를 들어 Azure 리소스 그룹을 범위로 사용할 수 있습니다. 이 경우 IoT Central 응용 프로그램과 대상이 동일한 리소스 그룹에 있어야 합니다.
* 역할은 대상 서비스에서 IoT Central 응용 프로그램에 부여 되는 사용 권한을 정의 합니다. 예를 들어 이벤트 허브로 데이터를 전송 하 IoT Central 응용 프로그램의 경우 관리 되는 id에는 **Azure Event Hubs 데이터 발신자** 역할 할당이 필요 합니다.

[!INCLUDE [iot-central-managed-identity](../../../includes/iot-central-managed-identity.md)]

Azure Portal에서 역할 할당을 구성 하거나 Azure CLI를 사용할 수 있습니다.

* 특정 대상에 대 한 Azure Portal에서 역할 할당을 구성 하는 방법에 대 한 자세한 내용은 [데이터 내보내기를 사용 하 여 클라우드 대상으로 IoT 데이터 내보내기](howto-export-data.md)를 참조 하세요.
* Azure CLI를 사용 하 여 역할 할당을 구성 하는 방법에 대해 자세히 알아보려면 [Azure CLI 또는 PowerShell에서 IoT Central 관리](howto-manage-iot-central-from-cli.md)를 참조 하세요.

## <a name="monitor-application-health"></a>애플리케이션 상태 모니터링

> [!NOTE]
> 메트릭은 버전 3 IoT Central 애플리케이션에만 사용할 수 있습니다. 애플리케이션 버전을 확인하는 방법을 알아보려면 [내 애플리케이션에 대한 정보를 얻으려면 어떻게 해야 하나요?](howto-faq.yml#how-do-i-get-information-about-my-application-)를 참조하세요.

IoT Central에서 제공하는 메트릭 집합을 사용하여 IoT Central 애플리케이션에 연결된 디바이스의 상태와 실행 중인 데이터 내보내기의 상태를 평가할 수 있습니다.

메트릭은 IoT Central 애플리케이션에 대해 기본적으로 사용하도록 설정되며 [Azure Portal](https://portal.azure.com/)에서 액세스할 수 있습니다. [Azure Monitor 데이터 플랫폼은 이러한 메트릭을 노출](../../azure-monitor/essentials/data-platform-metrics.md)하고 메트릭과 상호 작용할 수 있는 여러 가지 방법을 제공합니다. 예를 들어 Azure Portal, REST API의 차트를 사용하거나 PowerShell 또는 Azure CLI의 쿼리를 사용할 수 있습니다.

> [!TIP]
> 평가판을 사용하는 애플리케이션에는 Azure 구독이 연결되어 있지 않으므로 Azure Monitor 메트릭을 지원하지 않습니다. [애플리케이션을 표준 가격 책정 플랜으로 변환](./howto-faq.yml#how-do-i-move-from-a-free-to-a-standard-pricing-plan-)하고 이러한 메트릭에 액세스할 수 있습니다.

### <a name="view-metrics-in-the-azure-portal"></a>Azure Portal에서 메트릭 보기

다음 단계에서는 일부 [연결된 디바이스](./tutorial-connect-device.md) 또는 실행 중인 [데이터 내보내기](howto-export-data.md)가 있는 [IoT Central 애플리케이션](./howto-create-iot-central-application.md)을 사용한다고 가정합니다.

포털에서 IoT Central 메트릭을 보려면:

1. 포털에서 IoT Central 애플리케이션 리소스로 이동합니다. 기본적으로 IoT Central 리소스는 **IOTC** 라는 리소스 그룹에 있습니다.
1. 애플리케이션의 메트릭에서 차트를 만들려면 **모니터링** 섹션에서 **메트릭** 을 선택합니다.

![Azure Metrics](media/howto-manage-iot-central-from-portal/metrics.png)

### <a name="azure-portal-permissions"></a>Azure Portal 사용 권한

Azure Portal의 메트릭에 대한 액세스는 [Azure 역할 기반 액세스 제어](../../role-based-access-control/overview.md)를 통해 관리됩니다. Azure Portal을 사용하여 IoT Central 애플리케이션/리소스 그룹/구독에 사용자를 추가하여 액세스 권한을 부여합니다. 사용자가 이미 IoT Central 애플리케이션에 추가된 경우에도 포털에서 사용자를 추가해야 합니다. 세분화된 액세스 제어를 위해 [Azure 기본 제공 역할](../../role-based-access-control/built-in-roles.md)을 사용합니다.

### <a name="iot-central-metrics"></a>IoT Central 메트릭

현재 IoT Central에 사용할 수 있는 메트릭 목록은 [Azure Monitor를 사용하여 지원되는 메트릭](../../azure-monitor/essentials/metrics-supported.md#microsoftiotcentraliotapps)을 참조하세요.

### <a name="metrics-and-invoices"></a>메트릭 및 청구서

메트릭은 Azure IoT Central 청구서에 표시된 숫자와 다를 수 있습니다. 이러한 상황은 다음과 같은 여러 가지 이유로 발생합니다.

* IoT Central [표준 가격 책정 플랜](https://azure.microsoft.com/pricing/details/iot-central/)에는 두 개의 디바이스와 다양한 메시지 할당량이 무료로 포함됩니다. 무료 항목은 청구에서 제외되지만 여전히 메트릭에 반영됩니다.

* IoT Central은 애플리케이션의 각 디바이스 템플릿에 대해 하나의 테스트 디바이스 ID를 자동으로 생성합니다. 이 디바이스 ID는 디바이스 템플릿에 대한 **테스트 디바이스 관리** 페이지에 표시됩니다. 이러한 테스트 디바이스 ID를 사용하는 코드를 생성하여 게시하기 전에 디바이스 템플릿의 유효성을 검사하도록 선택할 수 있습니다. 이러한 디바이스는 청구에서 제외되지만 메트릭에 여전히 반영됩니다.

* 메트릭은 디바이스-클라우드 통신의 하위 집합을 표시할 수 있지만 디바이스와 클라우드 간의 모든 통신은 [요금 청구되는 메시지로 계산](https://azure.microsoft.com/pricing/details/iot-central/)됩니다.

## <a name="monitor-connected-iot-edge-devices"></a>연결된 IoT Edge 디바이스 모니터링

Azure Monitor 및 기본 제공 메트릭 통합을 사용하여 IoT Edge 집합을 원격으로 모니터링하는 방법을 알아보려면 [메트릭 수집 및 전송](../../iot-edge/how-to-collect-and-transport-metrics.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure Portal에서 Azure IoT Central 애플리케이션을 관리하고 모니터링하는 방법을 알아보았으므로 다음 단계를 진행하는 것이 좋습니다.

> [!div class="nextstepaction"]
> [애플리케이션 관리](howto-administer.md)
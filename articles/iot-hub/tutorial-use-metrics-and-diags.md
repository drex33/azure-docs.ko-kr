---
title: 자습서 - Azure IoT 허브를 사용하여 메트릭 및 로그 설정 및 사용
description: 자습서 - Azure IoT 허브를 사용하여 메트릭 및 로그를 설정하고 사용하는 방법을 알아봅니다. 이렇게 하면 허브에서 발생할 수 있는 문제를 진단하는 데 도움이 되는 분석 데이터를 제공합니다.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 10/29/2020
ms.author: robinsh
ms.custom:
- mvc
- mqtt
- devx-track-azurecli
- devx-track-csharp
ms.openlocfilehash: 139244b8519dcbbc02800b8e22e9c438d1cc2b80
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129458930"
---
# <a name="tutorial-set-up-and-use-metrics-and-logs-with-an-iot-hub"></a>자습서: IoT 허브를 사용하여 메트릭 및 진단 로그 설정 및 사용

Azure Monitor를 사용하여 IoT 허브에 대한 메트릭 및 로그를 수집할 수 있습니다. 그러면 솔루션 작동을 모니터링하고 문제가 발생할 경우 해결하는 데 유용합니다. 이 문서에서는 메트릭을 기반으로 차트를 만드는 방법, 메트릭에 따라 트리거되는 경고를 만드는 방법, IoT Hub 작업 및 오류를 Azure Monitor 로그로 보내는 방법, 로그에서 오류를 확인하는 방법을 살펴봅니다.

이 자습서에서는 [.NET 원격 분석 보내기 빠른 시작](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-csharp)의 Azure 샘플을 사용하여 IoT 허브로 메시지를 보냅니다. 언제든지 디바이스 또는 다른 샘플을 사용하여 메시지를 보낼 수 있지만 그에 맞게 몇 단계를 수정해야 할 수도 있습니다.

이 자습서를 시작하기 전에 Azure Monitor 개념에 익숙하면 도움이 될 수 있습니다. 자세히 알아보려면 [IoT Hub 모니터링](monitor-iot-hub.md)을 참조하세요. IoT Hub에서 내보내는 메트릭 및 리소스 로그에 대해 자세히 알아보려면 [모니터링 데이터 참조](monitor-iot-hub-reference.md)를 참조하세요.

이 자습서에서는 다음 작업을 수행합니다.

> [!div class="checklist"]
>
> * Azure CLI를 사용하여 IoT 허브를 만들고, 시뮬레이션된 디바이스를 등록하고, Log Analytics 작업 영역을 만듭니다.  
> * Log Analytics 작업 영역에서 Azure Monitor 로그에 IoT Hub 연결 및 디바이스 원격 분석 리소스 로그를 보냅니다.
> * 메트릭 탐색기를 사용하여 선택한 메트릭을 기반으로 차트를 만들고 대시보드에 고정합니다.
> * 중요한 조건이 발생할 때 이메일로 알림을 받을 수 있도록 메트릭 경고를 만듭니다.
> * IoT 허브로 메시지를 보내는 IoT 디바이스를 시뮬레이션하는 앱을 다운로드하고 실행합니다.
> * 조건이 발생하는 경우 경고를 봅니다.
> * 대시보드에서 메트릭 차트를 봅니다.
> * Azure Monitor 로그에서 IoT Hub 오류 및 작업을 봅니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure 구독 Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

- 개발 머신에 .NET Core SDK 2.1 이상이 필요합니다. [.NET](https://dotnet.microsoft.com/download)에서 여러 플랫폼에 대한 .NET Core SDK를 다운로드할 수 있습니다.

  다음 명령을 사용하여 개발 컴퓨터에서 C#의 현재 버전을 확인할 수 있습니다.

  ```cmd/sh
  dotnet --version
  ```

- 이메일을 받을 수 있는 이메일 계정입니다.

- 방화벽에서 포트 8883이 열려 있는지 확인합니다. 이 자습서의 디바이스 샘플은 포트 8883을 통해 통신하는 MQTT 프로토콜을 사용합니다. 이 포트는 일부 회사 및 교육용 네트워크 환경에서 차단될 수 있습니다. 이 문제를 해결하는 자세한 내용과 방법은 [IoT Hub에 연결(MQTT)](iot-hub-mqtt-support.md#connecting-to-iot-hub)을 참조하세요.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="set-up-resources"></a>리소스 설정

이 자습서에서는 IoT 허브, Log Analytics 작업 영역 및 시뮬레이션된 IoT 디바이스가 필요합니다. 이러한 리소스는 Azure CLI 또는 Azure PowerShell을 사용하여 만들 수 있습니다. 모든 리소스에 대해 동일한 리소스 그룹 및 위치를 사용합니다. 그런 다음, 자습서를 마치면 리소스 그룹을 삭제하여 모든 항목을 한 번에 제거할 수 있습니다.

필요한 단계는 다음과 같습니다.

1. [리소스 그룹](../azure-resource-manager/management/overview.md)을 만듭니다.

2. IoT Hub를 만듭니다.

3. Log Analytics 작업 영역을 만듭니다.

4. IoT 허브에 메시지를 보내는 시뮬레이션된 디바이스에 대한 디바이스 ID를 만듭니다. 시뮬레이션된 디바이스를 구성하는 데 사용할 디바이스 연결 문자열을 저장합니다.

### <a name="set-up-resources-using-azure-cli"></a>Azure CLI를 사용하여 리소스 설정

이 스크립트를 복사하여 Cloud Shell에 붙여넣습니다. 이미 로그인했다고 가정하면 한 번에 한 줄씩 스크립트가 실행됩니다. 일부 명령은 실행하는 데 시간이 걸릴 수 있습니다. 새 리소스는 리소스 그룹 *ContosoResources* 에 생성됩니다.

일부 리소스의 이름은 Azure에서 고유해야 합니다. 스크립트는 `$RANDOM` 함수를 사용하여 임의의 값을 생성하고 변수에 저장합니다. 이러한 리소스에 대해 스크립트는 임의의 값을 리소스의 기본 이름에 추가하여 리소스 이름을 고유하게 만듭니다.

무료 IoT 허브는 구독당 하나만 허용됩니다. 무료 IoT 허브가 구독에 이미 있으면, 스크립트를 실행하기 전에 삭제하거나 무료 IoT 허브 또는 표준 또는 기본 계층을 사용하는 IoT Hub를 사용하도록 스크립트를 수정합니다.

스크립트는 IoT 허브의 이름, Log Analytics 작업 영역의 이름, 등록하는 디바이스의 연결 문자열을 출력합니다. 이러한 정보를 기록해 두십시오. 이 문서의 뒷부분에서 필요합니다.

```azurecli-interactive

# This is the IOT Extension for Azure CLI.
# You only need to install this the first time.
# You need it to create the device identity.
az extension add --name azure-iot

# Set the values for the resource names that don't have to be globally unique.
# The resources that have to have unique names are named in the script below
#   with a random number concatenated to the name so you can probably just
#   run this script, and it will work with no conflicts.
location=westus
resourceGroup=ContosoResources
iotDeviceName=Contoso-Test-Device
randomValue=$RANDOM

# Create the resource group to be used
#   for all the resources for this tutorial.
az group create --name $resourceGroup \
    --location $location

# The IoT hub name must be globally unique, so add a random number to the end.
iotHubName=ContosoTestHub$randomValue
echo "IoT hub name = " $iotHubName

# Create the IoT hub in the Free tier. Partition count must be 2.
az iot hub create --name $iotHubName \
    --resource-group $resourceGroup \
    --partition-count 2 \
    --sku F1 --location $location

# The Log Analytics workspace name must be globally unique, so add a random number to the end.
workspaceName=contoso-la-workspace$randomValue
echo "Log Analytics workspace name = " $workspaceName


# Create the Log Analytics workspace
az monitor log-analytics workspace create --resource-group $resourceGroup \
    --workspace-name $workspaceName --location $location

# Create the IoT device identity to be used for testing.
az iot hub device-identity create --device-id $iotDeviceName \
    --hub-name $iotHubName

# Retrieve the primary connection string for the device identity, then copy it to
#   Notepad. You need this to run the device simulation during the testing phase.
az iot hub device-identity show-connection-string --device-id $iotDeviceName \
    --hub-name $iotHubName

```

>[!NOTE]
>디바이스 ID를 만들 때 다음 오류가 표시될 수도 있습니다. *IoT Hub ContosoTestHub의 iothubowner 정책에 대한 키를 찾을 수 없습니다*. 이 오류를 해결하려면 Azure CLI IoT 확장을 업데이트한 다음, 마지막 두 명령을 스크립트에서 다시 실행합니다. 
>
>확장을 업데이트하는 명령은 다음과 같습니다. Cloud Shell 인스턴스에서 다음 명령을 실행합니다.
>
>```cli
>az extension update --name azure-iot
>```

## <a name="collect-logs-for-connections-and-device-telemetry"></a>연결 및 디바이스 원격 분석에 대한 로그 수집

IoT Hub는 몇 가지 작업 범주에 대한 리소스 로그를 내보냅니다. 하지만 이러한 로그를 보려면 로그를 대상으로 보내는 진단 설정을 만들어야 합니다. 이러한 대상 중 하나는 Log Analytics 작업 영역에서 수집되는 Azure Monitor 로그입니다. IoT Hub 리소스 로그는 다양한 범주로 그룹화됩니다. Azure Monitor 로그로 보낼 범주는 진단 설정에서 선택할 수 있습니다. 이 문서에서는 연결 및 디바이스 원격 분석과 관련하여 발생하는 작업 및 오류에 대한 로그를 수집합니다. IoT Hub에 대해 지원되는 범주의 전체 목록은 [IoT Hub 리소스 로그](monitor-iot-hub-reference.md#resource-logs)를 참조하세요.

IoT Hub 리소스 로그를 Azure Monitor 로그로 보내는 진단 설정을 만들려면 다음 단계를 수행합니다.

1. 먼저, 아직 포털의 허브가 아니면 **리소스 그룹** 을 선택하고 리소스 그룹 ContosoResources를 선택합니다. 표시되는 리소스 목록에서 IoT 허브를 선택합니다.

1. IoT Hub 블레이드에서 **모니터링** 섹션을 찾습니다. **진단 설정** 을 선택합니다. 그런 다음, **진단 설정 추가** 를 선택합니다.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/open-diagnostic-settings.png" alt-text="모니터링 섹션의 진단 설정을 강조 표시하는 스크린샷.":::

1. **진단 설정** 창에서 설정을 설명하는 이름(예: "로그에 연결 및 원격 분석 보내기")을 지정합니다.

1. **범주 세부 정보** 에서 **연결** 및 **디바이스 원격 분석** 을 선택합니다.

1. **대상 세부 정보** 에서 **Log Analytics에 보내기** 를 선택한 다음, Log Analytics 작업 영역 선택기를 사용하여 이전에 기록해 둔 작업 영역을 선택합니다. 완료되면 진단 설정이 다음 스크린샷과 유사하게 보입니다.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/add-diagnostic-setting.png" alt-text="최종 진단 로그 설정을 보여주는 스크린샷.":::

1. **저장** 을 선택하여 설정을 저장합니다. **진단 설정** 창을 닫습니다. 진단 설정 목록에서 새 설정을 볼 수 있습니다.

## <a name="set-up-metrics"></a>메트릭 설정

이제 메트릭 탐색기를 사용하여 추적하려는 메트릭을 표시하는 차트를 만듭니다. 이 차트를 Azure Portal의 기본 대시보드에 고정합니다.

1. IoT 허브의 왼쪽 창에 있는 **모니터링** 섹션에서 **메트릭** 을 선택합니다.

1. 화면 맨 위에서 **지난 24시간(자동)** 을 선택합니다. 드롭다운 목록이 표시되면 **시간 범위** 에 **지난 4시간** 을 선택하고 **시간 단위** 를 **1분** 으로 설정하고 **시간 표시 형식** 에 **로컬** 을 선택합니다. **적용** 을 선택하여 이러한 설정을 저장합니다. 이제 설정에 **현지 시간: 지난 4시간(1분)** 이 표시됩니다.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-select-time-range.png" alt-text="메트릭 시간 설정을 보여주는 스크린샷.":::

1. 차트에는 IoT 허브로 범위가 지정된 부분 메트릭 설정이 표시됩니다. **범위** 및 **메트릭 네임스페이스** 값은 기본값으로 유지합니다. **메트릭** 설정을 선택하고 "원격 분석"을 입력한 다음, 드롭다운 메뉴에서 **전송된 원격 분석 메시지** 를 선택합니다. **집계** 가 자동으로 **합계** 로 설정됩니다. 차트의 제목도 변경됩니다.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-telemetry-messages-sent.png" alt-text="전송된 원격 분석 메시지 메트릭을 차트에 추가하는 방법을 보여주는 스크린샷":::

1. 이제 **메트릭 추가** 를 선택하여 다른 메트릭을 차트에 추가합니다. **메트릭** 에서 **사용된 전체 메시지 수** 를 선택합니다. **집계** 가 자동으로 **평균** 으로 설정됩니다. 다시, 차트의 제목이 이 메트릭을 포함하도록 변경됩니다.

   이제 화면에 *전송된 원격 분석 메시지* 에 대한 최소화된 메트릭과 함께 *사용된 총 메시지 수* 에 대해 새 메트릭이 표시됩니다.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-total-number-of-messages-used.png" alt-text="사용된 총 메시지 수 메트릭을 차트에 추가하는 방법을 보여주는 스크린샷":::

1. 차트의 오른쪽 위에서 **대시보드에 고정** 을 선택합니다.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-total-number-of-messages-used-pin.png" alt-text="대시보드에 고정 단추를 강조 표시하는 스크린샷.":::

1. **대시보드에 고정** 창에서 **기존** 탭을 선택합니다. **프라이빗** 을 선택하고 대시보드 드롭다운에서 **대시보드** 를 선택합니다. 마지막으로 **고정** 을 선택하여 Azure Portal의 기본 대시보드에 차트를 고정합니다. 차트를 대시보드에 고정하지 않으면 메트릭 탐색기를 종료할 때 설정이 유지되지 않습니다.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/pin-to-dashboard.png" alt-text="대시보드에 고정에 대한 설정을 보여주는 스크린샷":::

## <a name="set-up-metric-alerts"></a>메트릭 경고 설정

이제 전송된 원격 분석 메시지 및 사용된 총 메시지 수라는 두 가지 메트릭에 대해 트리거할 경고를 설정하겠습니다. 

전송된 원격 분석 메시지는 메시지 처리량을 추적하고 제한되는 것을 방지하기 위해 모니터링하기에 좋은 메트릭입니다. 무료 계층 IoT Hub의 경우 제한 한도는 100개 메시지/초입니다. 단일 디바이스로는 이러한 종류의 처리량을 달성할 수 없으므로, 그 대신 메시지 수가 5분 동안 1000개를 초과하면 트리거할 경고를 설정합니다. 프로덕션에서는 IoT 허브의 계층, 버전 및 단위 수를 기반으로 더 중요한 값으로 신호를 설정할 수 있습니다.

사용된 총 메시지 수는 일일 사용되는 메시지 수를 추적합니다. 이 메트릭은 매일 00:00 UTC에 재설정됩니다. 특정 임계값을 지나 일일 할당량을 초과하면 IoT Hub는 더 이상 메시지를 수락하지 않습니다. 무료 계층의 IoT Hub의 경우 일일 메시지 할당량은 8000입니다. 총 메시지 수가 4000(할당량의 50%)을 초과하면 트리거할 경고를 설정합니다. 실제로 이 백분율을 더 큰 값으로 설정하는 것이 좋습니다. 일일 할당량 값은 IoT 허브의 계층, 버전 및 단위 수에 따라 달라집니다.

IoT Hub의 할당량 및 제한 한도에 대한 자세한 내용은 [할당량 및 제한](iot-hub-devguide-quotas-throttling.md)을 참조하세요.

메트릭 경고를 설정하려면 다음을 수행합니다.

1. Azure Portal에서 IoT 허브로 이동합니다.

1. **모니터링** 에서 **경고** 를 선택합니다. 그런 다음, **새로운 경고 규칙** 을 선택합니다.  **경고 규칙 만들기** 창이 열립니다.

    :::image type="content" source="media/tutorial-use-metrics-and-diags/create-alert-rule-pane.png" alt-text="경고 규칙 만들기 창을 보여주는 스크린샷":::

    **경고 규칙 만들기** 창에는 네 가지 섹션이 있습니다.

    * **범위** 는 이미 IoT 허브로 설정되어 있으므로 이 섹션은 그대로 두겠습니다.
    * **조건** 은 경고를 트리거할 신호와 조건을 설정합니다.
    * **작업** 은 경고가 트리거될 때 수행되는 작업을 구성합니다.
    * **경고 규칙 세부 정보** 를 통해 경고에 대한 이름과 설명을 설정할 수 있습니다.

1. 먼저 경고가 트리거되는 조건을 구성합니다.

    1. **조건** 에서 **조건 추가** 를 선택합니다. **신호 논리 구성** 창에서 검색 상자에 "원격 분석"을 입력하고 **전송된 원격 분석 메시지** 를 선택합니다.

       :::image type="content" source="media/tutorial-use-metrics-and-diags/configure-signal-logic-telemetry-messages-sent.png" alt-text="메트릭 선택을 보여주는 스크린샷":::

    1. **신호 논리 구성** 창의 **경고 논리** 에서 다음 필드를 설정하거나 확인합니다(차트는 무시할 수 있음).

       **임계값**:  정적

       **연산자**: 다음 보다 큼

       **집계 유형**: 총계

       **임계값**: 1000.

       **집계 세분성(기간)** : 5분

       **평가 빈도**: 1분마다

        :::image type="content" source="media/tutorial-use-metrics-and-diags/configure-signal-logic-set-conditions.png" alt-text="경고 조건 설정을 보여주는 스크린샷":::

       이러한 설정은 5분 동안 총 메시지 수에 대한 신호를 설정합니다. 이 총계는 1분마다 평가되며 이전 5분 동안의 총계가 1000개 메시지를 초과하면 경고가 트리거됩니다.

       **완료** 를 선택하여 신호 논리를 저장합니다.

1. 이제 경고에 대한 작업을 구성합니다.

    1. **경고 규칙 만들기** 창으로 돌아가서 **작업** 에서 **작업 그룹 추가** 를 선택합니다. **이 경고 규칙에 연결할 작업 그룹 선택** 창에서 **작업 그룹 만들기** 를 선택합니다.

    1. **작업 그룹 만들기** 창의 **기본** 탭에서 작업 그룹에 이름 및 표시 이름을 지정합니다.

        :::image type="content" source="media/tutorial-use-metrics-and-diags/create-action-group-basics.png" alt-text="작업 그룹 만들기 창의 기본 탭을 보여주는 스크린샷":::

    1. **알림** 탭을 선택합니다. **알림 유형** 의 경우 드롭다운에서 **이메일/SMS 메시지/푸시/음성** 을 선택합니다. **이메일/SMS 메시지/푸시/음성** 창이 열립니다.

    1. **이메일/SMS 메시지/푸시/음성** 창에서 이메일을 선택하고 이메일 주소를 입력한 다음, **확인** 을 선택합니다.

        :::image type="content" source="media/tutorial-use-metrics-and-diags/set-email-address.png" alt-text="이메일 주소 설정을 보여주는 스크린샷":::

    1. **알림** 창으로 돌아가서 알림의 이름을 입력합니다.

        :::image type="content" source="media/tutorial-use-metrics-and-diags/create-action-group-notification-complete.png" alt-text="완료된 알림 창을 보여주는 스크린샷":::

    1. (선택 사항) **작업** 탭을 선택한 다음, **작업 유형** 드롭다운을 선택하면 경고를 통해 트리거할 수 있는 작업의 종류를 볼 수 있습니다. 이 문서에서는 알림만 사용하므로 이 탭의 설정은 무시해도 됩니다.

        :::image type="content" source="media/tutorial-use-metrics-and-diags/action-types.png" alt-text="작업 창에서 사용할 수 있는 작업 유형을 보여주는 스크린샷":::

    1. **검토 및 만들기** 탭을 선택하고, 설정을 확인하고, **만들기** 를 선택합니다.

        :::image type="content" source="media/tutorial-use-metrics-and-diags/create-action-group-review-and-create.png" alt-text="검토 및 만들기 창을 보여주는 스크린샷":::

    1. **경고 규칙 만들기** 창으로 돌아가서 새 작업 그룹이 경고에 대한 작업에 추가되었는지 확인합니다.  

1. 마지막으로 경고 규칙 세부 정보를 구성하고 경고 규칙을 저장합니다.

    1. **경고 규칙 만들기** 창의 경고 규칙 세부 정보에서 경고의 이름과 설명을 입력합니다(예: "5분 동안 메시지가 1000개를 초과하면 경고"). **Enable alert rule upon creation**(만들 때 경고 규칙을 사용하도록 설정)이 선택되어 있는지 확인합니다. 완료된 경고 규칙은 이 스크린샷과 유사합니다.

        :::image type="content" source="media/tutorial-use-metrics-and-diags/create-alert-rule-final.png" alt-text="완료된 경고 규칙 만들기 창을 보여주는 스크린샷":::

    1. **경고 규칙 만들기** 를 선택하여 새 규칙을 저장합니다.

1. 이제 *사용된 전체 메시지 수* 에 대한 다른 경고를 설정합니다. 이 메트릭은 사용된 메시지 수가 IoT 허브의 일일 할당량에 근접할 때 경고를 보내려는 경우에 유용하며, 이 시점에 IoT 허브는 메시지를 거부하기 시작합니다. 이전에 수행한 단계를 따르되, 다음과 같은 차이를 적용합니다.

    * **신호 논리 구성** 창의 신호에 대해 **사용된 총 메시지 수** 를 선택합니다.

    * **신호 논리 구성** 창에서 다음 필드를 설정하거나 확인합니다(차트는 무시할 수 있음).

       **임계값**:  정적

       **운영자**: 다음 보다 큼

       **집계 유형**: 최대

       **임계값**: 4000

       **집계 세분성(기간)** : 1분

       **평가 빈도**: 1분마다

       이러한 설정은 메시지 수가 4000에 도달할 때 신호를 발생하도록 설정합니다. 메트릭은 1분마다 평가됩니다.

    * 경고 규칙에 대한 작업을 지정할 때 이전에 만든 작업 그룹을 선택하기만 하면 됩니다.

    * 경고 세부 정보를 보려면 이전과 다른 이름과 설명을 선택합니다.

1. IoT 허브의 왼쪽 창에 있는 **모니터링** 에서 **경고** 를 선택합니다. 이제 **경고** 창의 맨 위에 있는 메뉴에서 **경고 규칙 관리** 를 선택합니다. **규칙** 창이 열립니다. 이제 두 가지 경고가 표시됩니다.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/rules-management.png" alt-text="새 경고 규칙이 있는 규칙 창을 보여주는 스크린샷":::

1. **규칙** 창을 닫습니다.

이러한 설정을 사용하면 5분이라는 시간 범위 내에 1000개를 초과하는 메시지가 전송되는 경우 및 사용된 총 메시지 수가 4000(무료 계층에서 IoT 허브에 대한 일일 할당량의 50%)을 초과하는 경우에도 경고가 트리거되고 이메일 알림을 받게 됩니다.

## <a name="run-the-simulated-device-app"></a>시뮬레이션된 디바이스 앱 실행

[리소스 설정](#set-up-resources) 섹션에서는 IoT 디바이스를 사용하여 시뮬레이션하는 데 사용할 디바이스 ID를 등록했습니다. 이 섹션에서는 디바이스-클라우드 메시지를 IoT Hub로 보내는 디바이스를 시뮬레이션하는 .NET 콘솔 앱을 다운로드하고 이 메시지를 IoT 허브로 보내도록 구성한 다음, 실행합니다.

> [!IMPORTANT]
>
> IoT Hub에서 경고를 완전히 구성하고 사용하도록 설정하는 데 최대 10분이 걸릴 수 있습니다. 마지막 경고를 구성하고 10분 이상 기다린 다음, 시뮬레이션된 디바이스 앱을 실행합니다.

[IoT 디바이스 시뮬레이션](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/main.zip)에 대한 솔루션을 다운로드합니다. 이 링크는 여러 애플리케이션이 포함된 리포지토리를 다운로드합니다. 찾는 항목은 iot-hub/Quickstarts/simulated-device/에 있습니다.

1. 로컬 터미널 창에서 솔루션의 루트 폴더로 이동합니다. 그런 다음, **iot-hub\Quickstarts\simulated-device** 폴더로 이동합니다.

1. 원하는 텍스트 편집기에서 **SimulatedDevice.cs** 파일을 엽니다.

    1. `s_connectionString` 변수의 값을 리소스를 설정하기 위해 스크립트를 실행할 때 기록해 둔 디바이스 연결 문자열로 바꿉니다.

    1. `SendDeviceToCloudMessagesAsync` 메서드에서 `Task.Delay`를 1000에서 1로 변경합니다. 그러면 메시지 전송 사이의 시간이 1초에서 0.001초로 단축됩니다. 이러한 지연 시간을 단축하면 전송된 메시지 수가 증가합니다. (초당 메시지 100개의 속도를 얻지 못할 가능성이 높습니다.)

        ```csharp
        await Task.Delay(1);
        ```

    1. 변경 내용을 **SimulatedDevice.cs** 에 저장합니다.

1. 로컬 터미널 창에서 다음 명령을 실행하여 시뮬레이션된 디바이스 애플리케이션에 필요한 패키지를 설치합니다.

    ```cmd/sh
    dotnet restore
    ```

1. 로컬 터미널 창에서 다음 명령을 실행하여 시뮬레이션된 디바이스 애플리케이션을 빌드하고 실행합니다.

    ```cmd/sh
    dotnet run
    ```

    다음 스크린샷에서는 시뮬레이션된 디바이스 애플리케이션에서 IoT 허브에 원격 분석을 보낼 때의 출력을 보여 줍니다.

    :::image type="content" source="media/tutorial-use-metrics-and-diags/simulated-device-output.png" alt-text="시뮬레이션된 디바이스 출력을 보여주는 스크린샷":::

애플리케이션이 10~15분 이상 실행되도록 둡니다. 이상적으로는 메시지 전송이 중지될 때까지(약 20~30분) 실행되도록 둡니다. IoT 허브에 대한 일일 메시지 할당량을 초과하고 메시지를 더 이상 수신하지 못하도록 중지한 경우에 발생합니다.

> [!NOTE]
> 디바이스 앱이 메시지 전송을 중지한 후 디바이스 앱을 장시간 실행 상태로 두면, 예외가 발생할 수 있습니다. 이 예외를 무시하고 앱 창을 닫아도 됩니다.

## <a name="view-metrics-chart-on-your-dashboard"></a>대시보드에서 메트릭 차트 보기

1. Azure Portal의 왼쪽 위 모서리에서 포털 메뉴를 연 다음, **대시보드** 를 선택합니다.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/select-dashboard.png" alt-text="대시보드를 선택하는 방법 스크린샷":::

1. 이전에 고정한 차트를 찾아서 차트 데이터 외부 타일의 아무 곳이나 클릭하여 확장합니다. 전송된 원격 분석 메시지와 사용된 총 메시지 수가 차트에 표시됩니다. 가장 최근의 숫자가 차트의 아래쪽에 표시됩니다. 차트에서 커서를 이동하면 특정 시간에 대한 메트릭 값을 볼 수 있습니다. 차트 맨 위에서 시간 값과 세분성을 변경하여 궁금한 기간으로 데이터를 좁히거나 확장할 수도 있습니다.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/metrics-on-dashboard-last-hour.png" alt-text="메트릭 차트를 보여주는 스크린샷":::

   이 시나리오에서 시뮬레이션된 디바이스의 메시지 처리량은 IoT Hub가 메시지를 제한할 만큼 충분히 크지 않습니다. 실제로 제한을 수반하는 시나리오에서, 전송된 원격 분석 메시지가 제한된 시간 동안 IoT 허브에 대한 제한 한도를 초과하는 것을 볼 수 있습니다. 이는 버스트 트래픽을 수용하기 위한 것입니다. 자세한 내용은 [트래픽 셰이핑](iot-hub-devguide-quotas-throttling.md#traffic-shaping)을 참조하세요.

## <a name="view-the-alerts"></a>경고 보기

보낸 메시지 수가 경고 규칙에 설정한 제한을 초과하면 이메일 경고를 받기 시작합니다.

활성 경고가 있는지 확인하려면 IoT 허브의 왼쪽 창에 있는 **모니터링** 에서 **경고** 를 선택합니다. **경고** 창에는 발생한 경고 수가 지정된 시간 범위 동안 심각도별로 정렬되어 표시됩니다.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/view-alerts.png" alt-text="경고 요약을 보여주는 스크린샷":::

심각도 Sev 3에 대한 행을 선택합니다. **모든 경고** 창이 열리고 발생한 Sev 3 경고가 나열됩니다.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/view-all-alerts.png" alt-text="모든 경고 창을 보여주는 스크린샷":::

경고 중 하나를 선택하여 경고 세부 정보를 봅니다.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/view-individual-alert.png" alt-text="경고 세부 정보를 보여주는 스크린샷":::

Microsoft Azure의 받은 편지함에서 이메일을 확인합니다. 제목 줄은 트리거된 경고를 설명합니다. 예를 들어 *Azure: Activated Severity: 3* 5분 동안 메시지가 1000개를 초과하는 경우 경고입니다. 본문은 다음 이미지와 유사합니다.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/alert-mail.png" alt-text="경고가 발생하였음을 보여주는 이메일의 스크린샷.":::

## <a name="view-azure-monitor-logs"></a>Azure Monitor 로그 보기

[연결 및 디바이스 원격 분석에 대한 로그 수집](#collect-logs-for-connections-and-device-telemetry) 섹션에서, 연결 및 디바이스 원격 분석 작업을 위해 IoT 허브에서 내보낸 리소스 로그를 Azure Monitor 로그로 보내는 진단 설정을 만들었습니다. 이 섹션에서는 Azure Monitor 로그에 대해 Kusto 쿼리를 실행하여 발생한 오류를 관찰합니다.

1. Azure Portal에서 IoT 허브의 왼쪽 창에 있는 **모니터링** 에서 **로그** 를 선택합니다. 초기 **쿼리** 창이 열리면 닫습니다.

1. 새 쿼리 창에서 **쿼리** 탭을 선택한 다음, **IoT Hub** 를 확장하여 기본 쿼리 목록을 표시합니다.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/new-query-pane.png" alt-text="IoT Hub 기본 쿼리 스크린샷":::

1. *오류 요약* 쿼리를 선택합니다. 쿼리 편집기 창에 쿼리가 나타납니다. 편집기 창에서 **실행** 을 선택하고 쿼리 결과를 확인합니다. 행 중 하나를 확장하여 세부 정보를 봅니다.

   :::image type="content" source="media/tutorial-use-metrics-and-diags/logs-errors.png" alt-text="오류 요약 쿼리에서 반환된 로그 스크린샷":::

   > [!NOTE]
   > 오류가 보이지 않으면 *최근 연결된 디바이스* 쿼리를 실행해봅니다. 그러면 시뮬레이션된 디바이스에 대한 행이 반환됩니다.

## <a name="clean-up-resources"></a>리소스 정리

이 자습서에서 만든 리소스를 모두 제거하려면 리소스 그룹을 삭제합니다. 이렇게 하면 그룹 내에 포함된 모든 리소스가 삭제됩니다. 이 경우 IoT 허브, Log Analytics 작업 영역 및 리소스 그룹 자체가 제거됩니다. 메트릭 차트를 대시보드에 고정한 경우에는 각 차트의 오른쪽 위 모서리에 있는 세 점을 클릭하고 **제거** 를 선택하여 수동으로 제거해야 합니다. 차트를 삭제한 후에는 변경 사항을 저장해야 합니다.

리소스 그룹을 제거하려면 [az group delete](/cli/azure/group#az_group_delete) 명령을 사용합니다.

```azurecli-interactive
az group delete --name ContosoResources
```

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업을 수행하여 IoT Hub 메트릭 및 로그를 사용하는 방법을 알아보았습니다.

> [!div class="checklist"]
>
> * Azure CLI를 사용하여 IoT 허브를 만들고, 시뮬레이션된 디바이스를 등록하고, Log Analytics 작업 영역을 만듭니다.  
> * Log Analytics 작업 영역에서 Azure Monitor 로그에 IoT Hub 연결 및 디바이스 원격 분석 리소스 로그를 보냅니다.
> * 메트릭 탐색기를 사용하여 선택한 메트릭을 기반으로 차트를 만들고 대시보드에 고정합니다.
> * 중요한 조건이 발생할 때 이메일로 알림을 받을 수 있도록 메트릭 경고를 만듭니다.
> * IoT 허브로 메시지를 보내는 IoT 디바이스를 시뮬레이션하는 앱을 다운로드하고 실행합니다.
> * 조건이 발생하는 경우 경고를 봅니다.
> * 대시보드에서 메트릭 차트를 봅니다.
> * Azure Monitor 로그에서 IoT Hub 오류 및 작업을 봅니다.

IoT 디바이스의 상태를 관리하는 방법에 대해 알아보려면 다음 자습서로 이동합니다. 

> [!div class="nextstepaction"]
> [백 엔드 서비스에서 디바이스 구성](tutorial-device-twins.md)
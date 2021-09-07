---
title: 사용자 지정 분석으로 Azure IoT Central 확장 | Microsoft Docs
description: 솔루션 개발자는 사용자 지정 분석 및 시각화를 수행하도록 IoT Central 애플리케이션을 구성합니다. 이 솔루션에서는 Azure Databricks를 사용합니다.
author: dominicbetts
ms.author: dobett
ms.date: 03/15/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: 5f046920ce711ac6b1e6b9a461d71d9d49b2ddc7
ms.sourcegitcommit: 5d605bb65ad2933e03b605e794cbf7cb3d1145f6
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122597666"
---
# <a name="extend-azure-iot-central-with-custom-analytics-using-azure-databricks"></a>Azure Databricks를 사용하여 사용자 지정 분석으로 Azure IoT Central 확장

이 방법 가이드에서는 사용자 지정 분석 및 시각화를 사용하여 IoT Central 애플리케이션을 확장하는 방법을 보여줍니다. 이 예제에서는 [Azure Databricks](/azure/azure-databricks/) 작업 영역을 사용하여 IoT Central 원격 분석 스트림을 분석하고 [상자 그림](https://wikipedia.org/wiki/Box_plot) 같은 시각화를 생성합니다.  

이 방법 가이드에서는 [기본 제공 분석 도구](./howto-create-custom-analytics.md)로 이미 수행할 수 있는 작업 이상으로 IoT Central을 확장하는 방법을 보여 줍니다.

이 방법 가이드에서 배울 내용은 다음과 같습니다.

* *연속 데이터 내보내기* 를 사용하여 IoT Central 애플리케이션에서 원격 분석을 스트림합니다.
* 디바이스 원격 분석을 분석하고 그리는 Azure Databricks 환경을 만듭니다.

## <a name="prerequisites"></a>필수 구성 요소

이 가이드의 수행 단계를 완료하려면 활성 Azure 구독이 필요합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

### <a name="iot-central-application"></a>IoT Central 애플리케이션

다음 설정을 사용하여 [Azure IoT Central 애플리케이션 관리자](https://aka.ms/iotcentral) 웹 사이트에서 IoT Central 애플리케이션을 만듭니다.

| 설정 | 값 |
| ------- | ----- |
| 요금제 | Standard |
| 애플리케이션 템플릿 | 매장 내 분석 - 조건 모니터링 |
| 애플리케이션 이름 | 기본값을 그대로 적용하거나 고유한 이름 선택 |
| URL | 기본값을 그대로 적용하거나 고유한 URL 접두사 선택 |
| 디렉터리 | Azure Active Directory 테넌트 |
| Azure 구독 | Azure 구독 |
| 지역 | 가장 가까운 지역 |

이 문서의 예제와 스크린샷은 **미국** 지역을 사용합니다. 가까운 위치를 선택하고 동일한 지역에 모든 리소스를 만들어 두어야 합니다.

이 애플리케이션 템플릿에는 원격 분석을 보내는 시뮬레이션된 자동 온도 조절 디바이스 두 개가 포함되어 있습니다.

### <a name="resource-group"></a>Resource group

만들어진 기타 리소스를 포함하는 **IoTCentralAnalysis** 라는 [리소스 그룹을 만드는 데 Azure Portal을 사용](https://portal.azure.com/#create/Microsoft.ResourceGroup)합니다. IoT Central 애플리케이션과 동일한 위치에 Azure 리소스를 만듭니다.

### <a name="event-hubs-namespace"></a>Event Hubs 네임스페이스

[Azure Portal을 사용하여 Event Hubs 네임스페이스를 만들](https://portal.azure.com/#create/Microsoft.EventHub) 때 다음 설정을 따릅니다.

| 설정 | 값 |
| ------- | ----- |
| Name    | 네임스페이스 이름 선택 |
| 가격 책정 계층 | Basic |
| Subscription | 사용자의 구독 |
| Resource group | IoTCentralAnalysis |
| 위치 | 미국 동부 |
| 처리량 단위 | 1 |

### <a name="azure-databricks-workspace"></a>Azure Databricks 작업 영역

다음 설정을 사용하여 [Azure Portal에서 Azure Databricks 서비스를 생성](https://portal.azure.com/#create/Microsoft.Databricks)합니다.

| 설정 | 값 |
| ------- | ----- |
| 작업 영역 이름    | 작업 영역 이름 선택 |
| Subscription | 사용자의 구독 |
| Resource group | IoTCentralAnalysis |
| 위치 | 미국 동부 |
| 가격 책정 계층 | Standard |

필요한 리소스를 만든 경우 **IoTCentralAnalysis** 리소스 그룹은 다음 스크린샷처럼 보입니다.

:::image type="content" source="media/howto-create-custom-analytics/resource-group.png" alt-text="IoT Central 분석 리소스 그룹의 이미지.":::

## <a name="create-an-event-hub"></a>이벤트 허브 만들기

IoT Central 애플리케이션을 구성하여 원격 분석을 이벤트 허브로 내보낼 수 있습니다. 이 섹션에서는 IoT Central 애플리케이션에서 원격 분석을 수신하는 이벤트 허브를 만듭니다. 이벤트 허브는 프로세스를 위해 원격 분석을 Stream Analytics 작업에 전달합니다.

1. Azure Portal에서 Event Hubs 네임스페이스로 이동하고 **+ 이벤트 허브** 를 선택합니다.
1. 이벤트 허브의 이름을 **centralexport** 로 지정합니다.
1. 네임스페이스의 이벤트 허브 목록에서 **centralexport** 를 선택합니다. 그런 다음, **공유 액세스 정책** 을 선택합니다.
1. **+추가** 를 선택합니다. **Send** 및 **Listen** 클레임을 사용하여 **SendListen** 이라는 정책을 만듭니다.
1. 정책이 준비되면 목록에서 해당 정책을 선택한 후 **연결 문자열 - 기본 키** 값을 복사합니다.
1. 이 연결 문자열을 기록해 두었다가 나중에 이벤트 허브에서 읽도록 Databricks Notebook을 구성할 때 사용합니다.

Event Hubs 네임스페이스는 다음 스크린샷과 같습니다.

:::image type="content" source="media/howto-create-custom-analytics/event-hubs-namespace.png" alt-text="Event Hubs 네임스페이스 이미지.":::

## <a name="configure-export-in-iot-central"></a>IoT Central에서 내보내기 구성

이 섹션에서는 시뮬레이션된 디바이스에서 이벤트 허브로 원격 분석을 스트림하는 애플리케이션을 구성합니다.

[Azure IoT Central 애플리케이션 관리자](https://aka.ms/iotcentral) 웹 사이트에서 이전에 만든 IoT Central 애플리케이션으로 이동합니다. 내보내기를 구성하려면 먼저 대상을 만듭니다.

1. **데이터 내보내기** 페이지로 이동한 후 **대상** 을 선택합니다.
1. **+ 새 대상** 을 선택합니다.
1. 다음 표의 값을 사용하여 대상을 만듭니다.

    | 설정 | 값 |
    | ----- | ----- |
    | 대상 이름 | 원격 분석 이벤트 허브 |
    | 대상 형식 | Azure Event Hubs |
    | 연결 문자열 | 앞에서 기록해 둔 이벤트 허브 연결 문자열 |

    **이벤트 허브** 는 **centralexport** 로 표시됩니다.

    :::image type="content" source="media/howto-create-custom-analytics/data-export-1.png" alt-text="데이터 내보내기 대상을 보여 주는 스크린샷.":::

1. **저장** 을 선택합니다.

내보내기 정의를 만들려면 다음을 수행합니다.

1. **데이터 내보내기** 페이지로 이동하고 **+ 새 내보내기** 를 선택합니다.

1. 다음 표의 값을 사용하여 내보내기를 구성합니다.

    | 설정 | 값 |
    | ------- | ----- |
    | 내보내기 이름 | 이벤트 허브 내보내기 |
    | 사용 | 켜기 |
    | 내보낼 데이터 형식 | 원격 분석 |
    | 대상 | **+ 대상** 을 선택한 다음, **원격 분석 이벤트 허브** 를 선택합니다. |

1. **저장** 을 선택합니다.

    :::image type="content" source="media/howto-create-custom-analytics/data-export-2.png" alt-text="데이터 내보내기 대상을 보여 주는 스크린샷.":::

계속하기 전에 **데이터 내보내기** 페이지에서 내보내기 상태가 **정상** 으로 표시될 때까지 기다립니다.

## <a name="configure-databricks-workspace"></a>Databricks 작업 영역 구성

Azure Portal에서 Azure Databricks 서비스로 이동하고 **작업 영역 시작** 을 선택합니다. 브라우저에서 새 탭이 열리고 작업 영역에 로그인됩니다.

### <a name="create-a-cluster"></a>클러스터 만들기

**Azure Databricks** 페이지의 일반 태스크 목록에서 **새 클러스터** 를 선택합니다.

다음 표의 정보를 사용하여 클러스터를 만듭니다.

| 설정 | 값 |
| ------- | ----- |
| 클러스터 이름 | centralanalysis |
| 클러스터 모드 | Standard |
| Databricks Runtime 버전 | 5.5 LTS(Scala 2.11, Spark 2.4.5) |
| Python 버전 | 3 |
| 자동 크기 조정 사용 | 예 |
| 다음 시간(분) 동안 비활성 상태이면 종료 | 30 |
| 작업자 유형 | Standard_DS3_v2 |
| 작업자 | 1 |
| 드라이버 유형 | 작업자와 동일 |

클러스터를 만들려면 몇 분 정도 걸릴 수 있습니다. 계속하기 전에 클러스터 만들기가 완료될 때까지 기다립니다.

### <a name="install-libraries"></a>라이브러리 설치

**클러스터** 페이지에서 클러스터 상태가 **실행 중** 이 될 때까지 기다립니다.

다음 단계는 샘플에 필요한 라이브러리를 클러스터로 가져오는 방법을 보여 줍니다.

1. **클러스터** 페이지에서 **centralanalysis** 대화형 클러스터의 상태가 **실행 중** 이 될 때까지 기다립니다.

1. 클러스터를 선택한 후 **라이브러리** 탭을 선택합니다.

1. **라이브러리** 탭에서 **새로 설치** 를 선택합니다.

1. **라이브러리 설치** 페이지에서 라이브러리 원본으로 **Maven** 을 선택합니다.

1. **좌표** 텍스트 상자에 `com.microsoft.azure:azure-eventhubs-spark_2.11:2.3.10` 값을 입력합니다.

1. **설치** 를 선택하여 클러스터에 라이브러리를 설치합니다.

1. 이제 라이브러리 상태가 **설치됨** 이 됩니다.

:::image type="content" source="media/howto-create-custom-analytics/cluster-libraries.png" alt-text="설치된 라이브러리의 스크린샷.":::

### <a name="import-a-databricks-notebook"></a>Databricks Notebook 가져오기

다음 단계를 사용하여 IoT Central 원격 분석을 분석하고 시각화할 Python 코드가 포함된 Databricks Notebook을 가져옵니다.

1. Databricks 환경의 **작업 영역** 페이지로 이동 합니다. 계정 이름 옆에 있는 드롭다운을 선택한 후 **가져오기** 를 선택합니다.

1. URL에서 가져오도록 선택하고 다음 주소를 입력합니다. [https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true](https://github.com/Azure-Samples/iot-central-docs-samples/blob/master/databricks/IoT%20Central%20Analysis.dbc?raw=true)

1. Notebook을 가져오려면 **가져오기** 를 선택합니다.

1. 가져온 Notebook을 보려면 **작업 영역** 을 선택합니다.

:::image type="content" source="media/howto-create-custom-analytics/import-notebook.png" alt-text="가져온 Notebook의 스크린샷.":::

5. 첫 번째 Python 셀의 코드를 편집하여 이전에 저장한 Event Hubs 연결 문자열을 추가합니다.

    ```python
    from pyspark.sql.functions import *
    from pyspark.sql.types import *

    ###### Event Hub Connection strings ######
    telementryEventHubConfig = {
      'eventhubs.connectionString' : '{your Event Hubs connection string}'
    }
    ```

## <a name="run-analysis"></a>분석 실행

분석을 실행하려면 Notebook을 클러스터에 연결해야 합니다.

1. **분리됨** 을 선택한 다음, **centralanalysis** 클러스터를 선택합니다.
1. 클러스터가 실행되고 있지 않은 경우 클러스터를 시작합니다.
1. Notebook을 시작하려면 실행 단추를 선택합니다.

마지막 셀에 오류가 표시될 수 있습니다. 그렇다면 이전 셀이 실행 중인지 확인하고, 일부 데이터가 스토리지에 기록될 때까지 잠시 기다린 후 마지막 셀을 다시 실행합니다.

### <a name="view-smoothed-data"></a>완만한 데이터 보기

Notebook에서 14셀까지 아래로 스크롤하여 디바이스 유형별 이동 평균 습도 플롯을 표시합니다. 이 플롯은 스트리밍 원격 분석이 수신되면 지속적으로 업데이트됩니다.

:::image type="content" source="media/howto-create-custom-analytics/telemetry-plot.png" alt-text="완만한 원격 분석 플롯의 스크린샷.":::

Notebook에서 차트의 크기를 조정할 수 있습니다.

### <a name="view-box-plots"></a>상자 그림 보기

Notebook에서 20셀까지 아래로 스크롤하여 [상자 그림](https://en.wikipedia.org/wiki/Box_plot)을 표시합니다. 상자 그림은 정적 데이터를 기반으로 하므로 업데이트하려면 셀을 다시 실행해야 합니다.

:::image type="content" source="media/howto-create-custom-analytics/box-plots.png" alt-text="상자 그림의 스크린샷.":::

Notebook에서 그림의 크기를 조정할 수 있습니다.

## <a name="tidy-up"></a>정리

이 방법 가이드를 완료한 후 정리하고 불필요한 비용을 방지하려면 Azure Portal에서 **IoTCentralAnalysis** 리소스 그룹을 삭제합니다.

애플리케이션 내의 **관리** 페이지에서 IoT Central 애플리케이션을 삭제할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 방법 가이드에서는 다음 작업 방법을 배웁니다.

* *연속 데이터 내보내기* 를 사용하여 IoT Central 애플리케이션에서 원격 분석을 스트림합니다.
* 원격 분석 데이터를 분석하고 그리는 Azure Databricks 환경을 만듭니다.

이제 사용자 지정 분석을 만드는 방법을 배웠으므로 제안되는 다음 단계는 [IoT Central 디바이스 브리지를 사용하여 다른 IoT 클라우드를 IoT Central에 연결](howto-build-iotc-device-bridge.md)하는 방법을 배우는 것입니다.

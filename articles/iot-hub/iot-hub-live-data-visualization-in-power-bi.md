---
title: Azure IoT Hub에서 데이터의 실시간 데이터 시각화 – Power BI
description: Power BI를 사용하여 센서에서 수집하여 Azure IoT Hub로 보낸 온도 및 습도 데이터를 시각화합니다.
author: eross-msft
keywords: 실시간 데이터 시각화, 라이브 데이터 시각화, 센서 데이터 시각화
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 7/23/2021
ms.author: lizross
ms.openlocfilehash: 15e297f65aad93cd3999ae44953a3410c7281788
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132555053"
---
# <a name="tutorial-visualize-real-time-sensor-data-from-azure-iot-hub-using-power-bi"></a>자습서: Power BI를 사용하여 Azure IoT Hub에서 실시간 센서 데이터 시각화

Microsoft Power BI를 사용하여 Azure IoT 허브에서 수신하는 실시간 센서 데이터를 시각화할 수 있습니다. 이렇게 하려면 IoT Hub의 데이터를 사용하고 Power BI의 데이터 세트로 라우팅하는 Azure Stream Analytics 작업을 구성합니다. 

:::image type="content" source="./media/iot-hub-live-data-visualization-in-power-bi/end-to-end-diagram.png" alt-text="엔드투엔드 다이어그램" border="false":::

 [Microsoft Power BI](https://powerbi.microsoft.com/)는 대용량 데이터 세트를 통해 셀프 서비스 및 엔터프라이즈 BI(비즈니스 인텔리전스)를 수행하는 데 사용할 수 있는 데이터 시각화 도구입니다. [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/#overview)는 인사이트를 얻거나, 보고서를 빌드하거나, 경고 및 작업을 트리거하는 데 사용할 수 있는 데이터의 빠른 이동 스트림을 분석하고 처리하는 데 도움이 되도록 설계된 완전 관리형 실시간 분석 서비스입니다. 

이 자습서에서는 다음 작업을 수행합니다.

> [!div class="checklist"]
> * IoT 허브에서 소비자 그룹 만들기
> * 소비자 그룹에서 온도 원격 분석을 읽고 Power BI로 보내도록 Azure Stream Analytics 작업을 만들고 구성합니다.
> * Power BI에서 온도 데이터 보고서를 만들어 웹에 공유합니다.

## <a name="prerequisites"></a>필수 구성 요소

* 선택한 개발 언어로 [원격 분석 전송](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-csharp) 빠른 시작 중 하나를 완료합니다. 또는 온도 원격 분석을 전송하는 디바이스 앱(예: [Raspberry Pi 온라인 시뮬레이터](iot-hub-raspberry-pi-web-simulator-get-started.md) 또는 [포함된 디바이스](../iot-develop/quickstart-devkit-mxchip-az3166.md) 빠른 시작 중 하나)을 사용할 수 있습니다. 이러한 문서는 다음 요구 사항을 다룹니다.
  
  * 활성화된 Azure 구독.
  * 구독의 Azure IoT 허브.
  * 메시지를 Azure IoT 허브로 보내는 클라이언트 앱.

* Power BI 계정. ([Power BI를 무료로 사용해 보기](https://powerbi.microsoft.com/))

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Stream Analytics 작업 만들기, 구성 및 실행

Stream Analytics 작업을 만들어 시작해 보겠습니다. 작업을 만든 후 데이터를 검색하는 데 사용된 입력, 출력 및 쿼리를 정의합니다.

### <a name="create-a-stream-analytics-job"></a>Stream Analytics 작업 만들기

1. [Azure Portal](https://portal.azure.com)에서 **리소스 만들기** 를 선택합니다. 검색 상자에 *Stream Analytics Job* 을 입력하고 드롭다운 목록에서 선택합니다. **Stream Analytics 작업** 개요 창에서 **만들기** 를 선택합니다.

2. 작업에 대한 다음 정보를 입력합니다.

   **작업 이름**: 작업의 이름입니다. 이름은 전역적으로 고유해야 합니다.

   **리소스 그룹**: IoT Hub에서 사용하는 것과 동일한 리소스 그룹을 사용합니다.

   **위치**: 리소스 그룹과 동일한 위치를 사용합니다.

   :::image type="content" source="./media/iot-hub-live-data-visualization-in-power-bi/create-stream-analytics-job.png" alt-text="Azure에서 Stream Analytics 작업 만들기":::

3. **만들기** 를 선택합니다.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Stream Analytics 작업에 입력 추가

1. Stream Analytics 작업을 엽니다.

2. **작업 토폴로지** 에서 **입력** 을 선택합니다.

3. **입력** 창에서 **스트림 입력 추가** 를 선택하고 드롭다운 목록에서 **IoT Hub** 를 선택합니다. 새 입력 창에서 다음 정보를 입력합니다.

   **입력 별칭**: 입력의 고유한 별칭을 입력합니다.

   **구독에서 IoT Hub를 선택합니다.** : 이 라디오 단추를 선택합니다.

   **구독**: 이 자습서를 위해 사용 중인 Azure 구독을 선택합니다.

   **IoT Hub**: 이 자습서에 사용하고 있는 IoT Hub를 선택합니다.

   **엔드포인트**: **메시징** 을 선택합니다.

   **공유 액세스 정책 이름**: Stream Analytics 작업에서 IoT 허브에 사용할 공유 액세스 정책의 이름을 선택합니다. 이 자습서에서는 ‘서비스’를 선택할 수 있습니다. ‘서비스’ 정책은 기본적으로 새 IoT 허브에서 만들어지고 IoT 허브를 통해 공개되는 클라우드 쪽 엔드포인트에서 보내고 받을 수 있는 권한을 부여합니다. 자세히 알아보려면 [액세스 제어 및 권한](iot-hub-dev-guide-sas.md#access-control-and-permissions)을 참조하세요.

   **공유 액세스 정책 키**: 이 필드는 선택한 공유 액세스 정책 이름에 따라 자동으로 채워집니다.

   **소비자 그룹**: 이전에 만든 소비자 그룹을 선택합니다.

   다른 모든 필드는 기본값으로 그대로 둡니다.

   :::image type="content" source="./media/iot-hub-live-data-visualization-in-power-bi/add-input-to-stream-analytics-job.png" alt-text="Azure에서 Stream Analytics 작업에 입력 추가":::

4. **저장** 을 선택합니다.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Stream Analytics 작업에 출력 추가

1. **작업 토폴로지** 에서 **출력** 을 선택합니다.

2. **출력** 창에서 **추가** 를 선택한 다음, 드롭다운 목록에서 **Power BI** 를 선택합니다.

3. **Power BI - 새 출력** 창에서 **권한 부여** 를 선택하고 프롬프트에 따라 Power BI 계정에 로그인합니다.

4. Power BI에 로그인한 후 다음 정보를 입력합니다.

   **출력 별칭**: 출력의 고유한 별칭입니다.

   **그룹 작업 영역**: 대상 그룹 작업 영역을 선택합니다.

   **데이터 세트 이름**: 데이터 세트 이름을 입력합니다.

   **테이블 이름**: 테이블 이름을 입력합니다.

   **인증 모드**: 기본값으로 유지합니다.

   :::image type="content" source="./media/iot-hub-live-data-visualization-in-power-bi/add-output-to-stream-analytics-job.png" alt-text="Azure에서 Stream Analytics 작업에 출력 추가":::

5. **저장** 을 선택합니다.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Stream Analytics 작업의 쿼리 구성

1. **작업 토폴로지** 에서 **쿼리** 를 선택합니다.

2. `[YourInputAlias]`를 작업의 입력 별칭으로 바꿉니다.

3. `[YourOutputAlias]`를 작업의 출력 별칭으로 바꿉니다.

1. 다음 `WHERE` 절을 쿼리의 마지막 줄로 추가합니다. 이 줄은 **온도** 속성이 있는 메시지만 Power BI에 전달되도록 합니다.

    ```sql
    WHERE temperature IS NOT NULL
    ```
1. 쿼리는 다음 스크린샷과 비슷합니다. **쿼리 저장** 을 선택합니다.

    :::image type="content" source="./media/iot-hub-live-data-visualization-in-power-bi/add-query-to-stream-analytics-job.png" alt-text="Stream Analytics 작업에 쿼리 추가":::

### <a name="run-the-stream-analytics-job"></a>Stream Analytics 작업 실행

Stream Analytics 작업에서 **개요** 를 선택한 다음, **시작** > **지금** > **시작** 을 선택합니다. 작업이 성공적으로 시작되면 작업 상태가 **중지됨** 에서 **실행 중** 으로 변경됩니다.

:::image type="content" source="./media/iot-hub-live-data-visualization-in-power-bi/run-stream-analytics-job.png" alt-text="Azure에서 Stream Analytics 작업 실행":::

## <a name="create-and-publish-a-power-bi-report-to-visualize-the-data"></a>Power BI 보고서를 만들고 게시하여 데이터 시각화

다음 단계에서는 Power BI 서비스를 사용하여 보고서를 만들고 게시하는 방법을 보여 줍니다. Power BI에서 “새 모양”을 사용하려는 경우 일부 내용을 수정하여 다음 단계를 수행할 수 있습니다. “새 모양”의 차이점 및 탐색 방법을 이해하려면 [Power BI 서비스의 ‘새 모양’](/power-bi/fundamentals/desktop-latest-update)을 참조하세요.

1. 클라이언트 앱이 디바이스에서 실행 중인지 확인합니다.

2. [Power BI](https://powerbi.microsoft.com/) 계정에 로그인하고 상단 메뉴에서 **Power BI 서비스** 를 선택합니다.

3. **내 작업 영역** 측면 메뉴에서 사용한 작업 영역을 선택합니다.

4. **전체** 탭이나 **데이터 세트 + 데이터 흐름** 탭에 Stream Analytics 작업의 출력을 만들 때 지정한 데이터 세트가 표시됩니다.

5. 마우스로 만든 데이터 세트를 가리키고 **추가 옵션** 메뉴(데이터 세트 오른쪽에 있는 점 3개)를 선택한 다음, **보고서 만들기** 를 선택합니다.

    :::image type="content" source="./media/iot-hub-live-data-visualization-in-power-bi/power-bi-create-report.png" alt-text="Microsoft Power BI 보고서 만들기":::

6. 시간이 지남에 따라 실시간 온도를 표시하는 꺾은선형 차트를 만듭니다.

   1. 보고서 만들기 페이지의 **시각화** 창에서 꺾은선형 차트 아이콘을 선택하여 꺾은선형 차트를 추가합니다. 차트의 측면과 모서리에 있는 안내선을 사용하여 크기와 위치를 조정합니다.

   2. **필드** 창에서 Stream Analytics 작업의 출력을 만들 때 지정한 테이블을 확장합니다.

   3. **시각화** 창에서 **EventEnqueuedUtcTime** 을 **축** 으로 끌어갑니다.

   4. **온도** 를 **값** 으로 끌어갑니다.

      꺾은선형 차트가 만들어집니다. x축은 UTC 표준 시간대의 날짜와 시간을 표시하고, y축은 센서의 온도를 표시합니다.

      :::image type="content" source="./media/iot-hub-live-data-visualization-in-power-bi/power-bi-add-temperature.png" alt-text="온도에 대한 꺾은선형 차트를 Microsoft Power BI 보고서에 추가":::

     > [!NOTE]
     > 원격 분석 데이터를 전송하는 데 사용하는 디바이스나 시뮬레이션된 디바이스에 따라 필드 목록이 약간 다를 수 있습니다.
     >

8. **저장** 을 선택하여 보고서를 저장합니다. 메시지가 표시되면 보고서 이름을 입력합니다. 민감도 레이블에 대한 메시지가 표시되면 **공용** 을 선택한 다음, **저장** 을 선택합니다.

10. 보고서 창에서 **파일** > **포함된 보고서** > **웹 사이트 또는 포털** 을 선택합니다.

    :::image type="content" source="./media/iot-hub-live-data-visualization-in-power-bi/power-bi-select-embed-report.png" alt-text="Microsoft Power BI 보고서의 embed 보고서 웹 사이트 선택":::

    > [!NOTE]
    > Embed 태그 만들기를 사용하려면 관리자에게 문의하라는 알림이 표시되면 관리자에게 문의해야 할 수 있습니다. 이 단계를 완료하려면 먼저 embed 태그 만들기를 사용하도록 설정해야 합니다.
    >
    > :::image type="content" source="./media/iot-hub-live-data-visualization-in-power-bi/contact-admin.png" alt-text="관리자에게 문의 알림":::


11. 보고서에 액세스할 수 있도록 모든 사람과 공유할 수 있는 보고서 링크 및 보고서를 블로그나 웹 사이트에 통합하는 데 사용할 수 있는 코드 조각이 제공됩니다. **보안 embed 코드** 창의 링크를 복사한 다음, 창을 닫습니다.

    :::image type="content" source="./media/iot-hub-live-data-visualization-in-power-bi/copy-secure-embed-code.png" alt-text="embed 보고서 링크 복사":::

12. 웹 브라우저를 열고 주소 표시줄에 링크를 붙여넣습니다.

    :::image type="content" source="./media/iot-hub-live-data-visualization-in-power-bi/power-bi-web-output.png" alt-text="Microsoft Power BI 보고서 게시":::

모바일 디바이스에서 Power BI 대시보드 및 보고서를 보고 상호 작용할 수 있는 [Power BI 모바일 앱](https://powerbi.microsoft.com/documentation/powerbi-power-bi-apps-for-mobile-devices/)도 제공합니다.

## <a name="cleanup-resources"></a>리소스 정리

이 자습서에서는 Power BI에 리소스 그룹, IoT 허브, Stream Analytics 작업 및 데이터 세트를 만들었습니다. 

다음 자습서를 완료하려면 리소스 그룹과 IoT 허브를 그대로 두고 나중에 다시 사용하면 됩니다. 

더 이상 IoT 허브나 만든 다른 리소스가 필요 없으면 포털에서 리소스 그룹을 삭제할 수 있습니다. 이렇게 하려면 리소스 그룹을 선택한 다음, **리소스 그룹 삭제** 를 선택합니다. IoT 허브를 보관하려면 리소스 그룹의 **개요** 창에서 다른 리소스를 삭제하면 됩니다. 이렇게 하려면 리소스를 마우스 오른쪽 단추로 클릭하고 바로 가기 메뉴에서 **삭제** 를 선택한 다음, 프롬프트를 따릅니다. 

### <a name="use-the-azure-cli-to-clean-up-azure-resources"></a>Azure CLI를 사용하여 Azure 리소스 정리

리소스 그룹과 관련된 모든 리소스를 제거하려면 [az group delete](/cli/azure/group#az_group_delete) 명령을 사용합니다.

```azurecli-interactive
az group delete --name {your resource group}
```

### <a name="clean-up-power-bi-resources"></a>Power BI 리소스 정리

Power BI에 **PowerBiVisualizationDataSet** 데이터 세트를 만들었습니다. 제거하려면 [Power BI](https://powerbi.microsoft.com/) 계정에 로그인합니다. **작업 영역** 의 왼쪽 메뉴에서 **내 작업 영역** 을 선택합니다. **데이터 세트 + 데이터 흐름** 탭의 데이터 세트 목록에서 마우스로 **PowerBiVisualizationDataSet** 데이터 세트를 가리킵니다. 데이터 세트 오른쪽에 있는 세로로 나열된 점 3개를 선택하여 **추가 옵션** 메뉴를 연 다음, **삭제** 를 선택하고 프롬프트를 따릅니다. 데이터 세트를 제거하면 보고서도 제거됩니다.

## <a name="next-steps"></a>다음 단계

이 자습서에서는 다음 작업을 수행함으로써 Power BI를 사용하여 Azure IoT 허브에서 실시간 센서 데이터를 시각화하는 방법을 알아보았습니다.

> [!div class="checklist"]
> * IoT 허브에서 소비자 그룹 만들기
> * 소비자 그룹에서 온도 원격 분석을 읽고 Power BI로 보내도록 Azure Stream Analytics 작업을 만들고 구성합니다.
> * Power BI에서 온도 데이터 보고서를 구성하고 웹에 공유합니다.

Azure IoT Hub의 데이터를 시각화하는 또 다른 방법은 다음 문서를 참조하세요.

> [!div class="nextstepaction"]
> [웹앱을 사용하여 Azure IoT Hub의 실시간 센서 데이터 시각화](iot-hub-live-data-visualization-in-web-apps.md)
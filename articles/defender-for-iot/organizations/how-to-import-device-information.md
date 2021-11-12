---
title: 디바이스 정보 가져오기
description: Defender for IoT 센서는 미러링된 트래픽을 모니터링하고 분석합니다. 이러한 경우에는 이미 검색된 디바이스에 대한 정보를 보강하기 위해 데이터를 가져올 수 있습니다.
ms.date: 11/09/2021
ms.topic: how-to
ms.openlocfilehash: 3446a82ec71b03c8e1647b6f95db937e7ef7f488
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132293253"
---
# <a name="import-device-information-to-a-sensor"></a>디바이스 정보를 센서로 가져오기

IoT 센서에 대 한 Microsoft Defender는 미러된 트래픽을 모니터링 하 고 분석 합니다. 일부 경우에는 조직별 네트워크 구성 정책 때문에 일부 정보가 전송되지 않을 수 있습니다.

이러한 경우 이미 검색된 디바이스에 대한 정보를 보강하기 위해 데이터를 가져올 수 있습니다. 센서에 정보를 가져오는 데 사용할 수 있는 두 가지 옵션은 다음과 같습니다.

- **맵에서 가져오기**: 디바이스 이름, 유형, 그룹 또는 Purdue 계층을 맵으로 업데이트합니다.

- **가져오기 설정에서 가져오기**: 디바이스 OS, IP 주소, 패치 수준 또는 권한 부여 상태를 가져옵니다.

## <a name="import-from-the-map"></a>맵에서 가져오기

이 섹션에서는 디바이스 이름, 유형, 그룹 또는 Purdue 계층을 디바이스 맵으로 가져오는 방법을 설명합니다. 이 작업은 맵에서 수행합니다.

가져오기 요구 사항은 다음과 같습니다.

- **이름**: 최대 30자까지 가능합니다.

- **유형** 또는 **Purdue 계층**: **디바이스 속성** 대화 상자에 표시되는 옵션을 사용합니다. (디바이스를 마우스 오른쪽 단추로 클릭하고 **속성 보기** 를 선택합니다.)

- **디바이스 그룹**: 최대 30자의 새 그룹을 만듭니다. 

> [!NOTE]
> 충돌을 방지하려면 한 센서에서 내보낸 데이터를 다른 센서로 가져오지 마세요.

가져오려면 다음을 수행합니다.

1. 측면 메뉴에서 **디바이스** 를 선택합니다.

2. **디바이스** 창의 오른쪽 위 구석에서 :::image type="icon" source="media/how-to-import-device-information/file-icon.png" border="false":::을 선택합니다.

   :::image type="content" source="media/how-to-import-device-information/device-window-v2.png" alt-text="디바이스 창 스크린샷":::

3. **디바이스 내보내기** 를 선택합니다. 내보낸 파일에는 광범위한 정보가 표시됩니다. 이 정보에는 디바이스에서 사용하는 프로토콜과 디바이스 권한 부여 상태가 포함됩니다.

   :::image type="content" source="media/how-to-import-device-information/sample-exported-file.png" alt-text="내보낸 파일의 정보입니다.":::

4. CSV 파일에서는 디바이스 이름, 유형, 그룹 및 Purdue 계층만 변경합니다. 그런 다음, 파일을 저장합니다. 

   내보낸 파일에 표시된 대/소문자 표준을 사용합니다. 예를 들어, Purdue 계층의 경우 모든 첫 글자를 대문자로 표시합니다.

5. **디바이스** 창의 **가져오기/내보내기** 드롭다운 메뉴에서 **디바이스 가져오기** 를 선택합니다.

   :::image type="content" source="media/how-to-import-device-information/import-assets-v2.png" alt-text="디바이스 창을 통해 디바이스를 가져옵니다.":::

6. **디바이스 가져오기** 를 선택하고 가져올 CSV 파일을 선택합니다. 가져오기 상태 메시지는 **디바이스 가져오기** 대화 상자를 닫을 때까지 화면에 표시됩니다.

## <a name="import-from-import-settings"></a>가져오기 설정에서 가져오기

이 섹션에서는 디바이스 IP 주소, OS, 패치 수준 또는 권한 부여 상태를 디바이스 맵으로 가져오는 방법을 설명합니다. **가져오기 설정** 대화 상자에서 이 작업을 수행합니다.

IP 주소, OS 및 패치 수준을 가져오려면

1. [도움말 센터](https://cyberx-labs.zendesk.com/hc/en-us)에서 [devices_info_2.2.8 및 up.csv](https://cyberx-labs.zendesk.com/hc/en-us/articles/360008658272-How-To-Import-Data) 파일을 다운로드하고 다음과 같이 정보를 입력합니다.

   - **IP 주소**: 디바이스 IP 주소를 입력합니다.

   - **운영 체제**: 드롭다운 목록에서 선택합니다.

   - **마지막 업데이트**: YYYY-MM-DD 형식을 사용합니다.

     :::image type="content" source="media/how-to-import-device-information/last-update-screen.png" alt-text="옵션 화면입니다.":::

2. 측면 메뉴에서 **가져오기 설정** 을 선택합니다.

   :::image type="content" source="media/how-to-import-device-information/import-settings-screen-v2.png" alt-text="설정을 가져옵니다.":::

3. 필요한 구성을 업로드하려면 **디바이스 정보** 섹션에서 **추가** 를 선택하고 준비한 CSV 파일을 업로드합니다.

권한 부여 상태를 가져오려면

1. Defender for IoT 도움말 센터에서 [authorized_devices.csv](https://cyberx-labs.zendesk.com/hc/en-us/articles/360008658272-How-To-Import-Data) 파일을 다운로드하고 저장합니다. 파일을 CSV로 저장했는지 확인합니다.

2. 다음과 같이 정보를 입력합니다.

   - **IP 주소**: 디바이스 IP 주소입니다.

   - **이름**: 권한이 부여된 디바이스 이름입니다. 이름이 정확한지 확인합니다. 가져온 목록의 디바이스에 지정된 이름은 디바이스 맵에 표시되는 이름을 덮어씁니다.

     :::image type="content" source="media/how-to-import-device-information/device-map-file.png" alt-text="가져온 디바이스 목록이 포함된 Excel 파일":::

3. 측면 메뉴에서 **가져오기 설정** 을 선택합니다.

4. **권한 있는 디바이스** 섹션에서 **추가** 를 선택하고 저장한 CSV 파일을 업로드합니다.

이 정보를 가져오면 이 목록에 표시되지 않은 모든 디바이스에 대한 권한 없는 디바이스에 대한 경고가 표시됩니다.

## <a name="import-device-information-to-the-sensor"></a>디바이스 정보를 센서로 가져오기

센서가 미러링된 트래픽을 모니터링하고 분석합니다. 일부 경우에는 조직별 네트워크 구성 정책 때문에 일부 정보가 전송되지 않을 수 있습니다.

이러한 경우 이미 검색된 디바이스에 대한 디바이스 정보를 보강하기 위해 데이터를 가져올 수 있습니다. 센서에 정보를 가져오는 데 사용할 수 있는 두 가지 옵션은 다음과 같습니다.

- **맵에서 가져오기**: 디바이스 이름, 유형, 그룹 또는 Purdue 계층을 맵으로 업데이트합니다.

- **가져오기 설정에서 가져오기**: 디바이스 OS, IP 주소, 패치 수준 또는 권한 부여 상태를 가져옵니다.

### <a name="import-from-the-map"></a>맵에서 가져오기

이 섹션에서는 디바이스 이름, 유형, 그룹 또는 Purdue 계층을 디바이스 맵으로 가져오는 방법을 설명합니다. 이 작업은 맵에서 수행합니다.

가져오기 요구 사항은 다음과 같습니다.

- **이름**: 최대 30자까지 가능합니다.

- **유형** 또는 **Purdue 계층**: **디바이스 속성** 대화 상자에 표시되는 옵션을 사용합니다. (디바이스를 마우스 오른쪽 단추로 클릭하고 **속성 보기** 를 선택합니다.)

- **디바이스 그룹**: 최대 30자의 새 그룹을 만듭니다. 

> [!NOTE]
> 충돌을 방지하려면 한 센서에서 내보낸 데이터를 다른 센서로 가져오지 마세요.

가져오려면 다음을 수행합니다.

1. 측면 메뉴에서 **디바이스** 를 선택합니다.

2. **디바이스** 창의 오른쪽 위 구석에서 :::image type="icon" source="media/how-to-import-device-information/file-icon.png" border="false":::을 선택합니다.

   :::image type="content" source="media/how-to-import-device-information/device-window-v2.png" alt-text="디바이스를 선택하는 창":::

3. **디바이스 내보내기** 를 선택합니다. 내보낸 파일에는 광범위한 정보가 표시됩니다. 디바이스에서 사용하는 프로토콜과 디바이스 권한 부여 상태를 예로 들 수 있습니다.

   :::image type="content" source="media/how-to-import-device-information/sample-exported-file.png" alt-text="내보낸 파일의 정보입니다.":::

4. CSV 파일에서는 디바이스 이름, 유형, 그룹 및 Purdue 계층만 변경합니다. 그런 다음, 파일을 저장합니다. 

   내보낸 파일에 표시된 대/소문자 표준을 사용합니다. 예를 들어, Purdue 계층의 경우 모든 첫 글자를 대문자로 표시합니다.

5. **디바이스** 창의 **가져오기/내보내기** 드롭다운 메뉴에서 **디바이스 가져오기** 를 선택합니다.

   :::image type="content" source="media/how-to-import-device-information/import-assets-v2.png" alt-text="디바이스를 가져옵니다.":::

6. **디바이스 가져오기** 를 선택하고 가져올 CSV 파일을 선택합니다. 가져오기 상태 메시지는 **디바이스 가져오기** 대화 상자를 닫을 때까지 화면에 표시됩니다.

### <a name="import-from-import-settings"></a>가져오기 설정에서 가져오기 

이 섹션에서는 디바이스 IP 주소, OS, 패치 수준 또는 권한 부여 상태를 디바이스 맵으로 가져오는 방법을 설명합니다. **가져오기 설정** 대화 상자에서 이 작업을 수행합니다.

IP 주소, OS 및 패치 수준을 가져오려면

1. [도움말 센터](https://cyberx-labs.zendesk.com/hc/en-us)에서 [devices_info_2.2.8 및 up.csv](https://cyberx-labs.zendesk.com/hc/en-us/articles/360008658272-How-To-Import-Data) 파일을 다운로드하고 다음과 같이 정보를 입력합니다.

   - **IP 주소**: 디바이스 IP 주소입니다.

   - **운영 체제**: 드롭다운 목록에서 선택합니다.

   - **마지막 업데이트 날짜**: YYYY-MM-DD 형식을 사용합니다.

    :::image type="content" source="media/how-to-import-device-information/last-update-screen.png" alt-text="화면 콘텐츠입니다.":::

2. 측면 메뉴에서 **가져오기 설정** 을 선택합니다.

   :::image type="content" source="media/how-to-import-device-information/import-settings-screen-v2.png" alt-text="가져오기 설정 화면을 채웁니다.":::

3. 필요한 구성을 업로드하려면 **디바이스 정보** 섹션에서 **추가** 를 선택하고 준비한 CSV 파일을 업로드합니다.

권한 부여 상태를 가져오려면

1. Defender for IoT 도움말 센터에서 [authorized_devices - examples.csv](https://cyberx-labs.zendesk.com/hc/en-us/articles/360008658272-How-To-Import-Data) 파일을 다운로드하고 저장합니다. 파일을 CSV로 저장했는지 확인합니다.

2. 다음과 같이 정보를 입력합니다.

   - **IP 주소**: 디바이스 IP 주소입니다.

   - **이름**: 권한이 부여된 디바이스 이름입니다. 이름이 정확한지 확인합니다. 가져온 목록의 디바이스에 지정된 이름은 디바이스 맵에 표시되는 이름을 덮어씁니다.

     :::image type="content" source="media/how-to-import-device-information/device-map-file.png" alt-text="디바이스 맵에 대한 가져오기 목록입니다.":::

3. 측면 메뉴에서 **가져오기 설정** 을 선택합니다.

4. **권한 있는 디바이스** 섹션에서 **추가** 를 선택하고 저장한 CSV 파일을 업로드합니다.

이 정보를 가져오면 이 목록에 표시되지 않은 모든 디바이스에 대한 권한 없는 디바이스에 대한 경고가 표시됩니다.

## <a name="see-also"></a>참고 항목

[모니터링된 트래픽 제어](how-to-control-what-traffic-is-monitored.md)

[디바이스 인벤토리에서 센서 감지 조사](how-to-investigate-sensor-detections-in-a-device-inventory.md)

---
title: 모든 엔터프라이즈 센서가 검색하는 디바이스에 대해 알아보기
description: 온-프레미스 관리 콘솔에서 디바이스 인벤토리를 사용하여 연결된 센서에서 디바이스 정보에 대한 포괄적인 보기를 가져옵니다. 가져오기, 내보내기, 필터링 도구를 사용하여 이 정보를 관리합니다.
ms.date: 11/09/2021
ms.topic: how-to
ms.openlocfilehash: 0025016dd449bf4083c707d4a6db5cd25bf66cfd
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132343670"
---
# <a name="investigate-all-enterprise-sensor-detections-in-the-device-inventory"></a>디바이스 인벤토리에서 모든 엔터프라이즈 센서 검색 조사

연결된 센서의 디바이스 정보는 온-프레미스 관리 콘솔에서 디바이스 인벤토리를 사용하여 볼 수 있습니다. 이 기능은 모든 네트워크 정보에 대한 포괄적인 보기를 제공합니다. 가져오기, 내보내기, 필터링 도구를 사용하여 이 정보를 관리합니다. 연결된 센서 버전에 대한 상태 정보도 표시됩니다.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/device-inventory-data-table.png" alt-text="디바이스 인벤토리 데이터 테이블의 스크린샷.":::

다음 표에서는 디바이스 인벤토리의 테이블 열을 설명합니다.

| 매개 변수 | Description |
|--|--|
| **승인되지 않은 경고** | 이 디바이스와 연결된 처리되지 않은 경고의 수입니다. |
| **사업부** | 이 디바이스가 포함된 사업부입니다. |
| **지역** | 이 디바이스가 포함된 지역입니다. |
| **사이트** | 이 디바이스가 포함된 사이트입니다. |
| **영역** | 이 디바이스가 포함된 영역입니다. |
| **어플라이언스** | 이 장치를 보호 하는 IoT 용 Microsoft Defender 센서입니다. |
| **이름** | Defender for IoT가 검색한 이 디바이스의 이름입니다. |
| **유형** | PLC 또는 HMI와 같은 디바이스 유형입니다. |
| **공급업체** | MAC 주소에 정의된 디바이스 공급업체의 이름입니다. |
| **운영 체제** | 디바이스의 OS입니다. |
| **펌웨어** | 디바이스의 펌웨어입니다. |
| **IP 주소** | 디바이스의 IP 주소입니다. |
| **VLAN** | 디바이스의 VLAN입니다. |
| **MAC 주소** | 디바이스의 Mac 주소입니다. |
| **프로토콜** | 디바이스에서 사용하는 프로토콜입니다. |
| **승인되지 않은 경고** | 이 디바이스와 연결된 처리되지 않은 경고의 수입니다. |
| **권한 부여됨** | 디바이스의 권한 부여 상태:<br />- **True**: 디바이스에 권한이 부여되었습니다.<br />- **False**: 디바이스에 권한이 부여되지 않았습니다. |
| **스캐너로 알려짐** | 이 디바이스가 네트워크에서 스캔과 비슷한 작업을 수행하는지 여부입니다. |
| **프로그래밍 디바이스** | 프로그래밍 디바이스인지 여부:<br />- **True**: 디바이스에서 엔지니어링 스테이션과 관련된 PLC, RTU 및 컨트롤러에 대한 프로그래밍 작업을 수행합니다.<br />- **False**: 디바이스가 프로그래밍 디바이스가 아닙니다. |
| **그룹** | 이 디바이스가 참여하는 그룹입니다. |
| **마지막 작업** | 디바이스가 수행한 마지막 작업입니다. |
| **Discovered** | 이 디바이스가 네트워크에서 처음으로 검색된 시기입니다. |
| **PLC 모드 (미리 보기)** | PLC 운영 모드는 키 상태 (물리적) 및 실행 상태 (논리적)를 포함 합니다. 가능한 **주요** 상태는, 실행, 프로그램, 원격, 중지, 잘못 됨, 프로그래밍 사용 안 함입니다. 실행할 수 있습니다. 가능한 **실행** 상태는 실행, 프로그램, 중지, 일시 중지 됨, 예외, 중지 됨, 트랩 됨, 유휴 상태, 오프 라인 상태입니다. 두 상태가 동일 하면 oe 상태만 표시 됩니다. |

## <a name="what-is-an-inventory-device"></a>재고 장치인 이란?

IoT 장치 인벤토리에 대 한 Defender는 조직 네트워크 및 관리 되는 끝점을 모니터링 하는 센서에 의해 검색 되는 광범위 한 장치 특성을 표시 합니다. IoT 용 Defender는 다음의 인벤토리에 있는 단일 고유 네트워크 장치로 장치를 식별 하 고 분류 합니다.

1. 독립 실행형 IT/온/IoT 장치 (w/1 또는 여러 Nic)
1. 여러 후면판 구성 요소로 구성 된 장치 (모든 랙/슬롯/모듈 포함)
1. 스위치/라우터 (여러 Nic)와 같은 네트워크 인프라 역할을 하는 장치입니다. 

공용 인터넷 IP 주소, 멀티 캐스트 그룹 및 브로드캐스트 그룹은 인벤토리 장치로 간주 되지 않습니다.
60 일 넘게 비활성 상태인 장치는 비활성 인벤토리 장치로 분류 됩니다.

## <a name="integrate-data-into-the-enterprise-device-inventory"></a>엔터프라이즈 디바이스 인벤토리에 데이터 통합

데이터 통합 기능을 사용하면 다른 엔터프라이즈 리소스의 정보를 사용하여 디바이스 인벤토리의 데이터를 향상시킬 수 있습니다. 이러한 원본에는 CMDB, DNS, 방화벽 및 Web API가 포함됩니다.

이 정보를 사용하여 다음을 알 수 있습니다. 예를 들면 다음과 같습니다.

- 디바이스 구매 날짜 및 보증 종료 날짜

- 각 디바이스를 담당하는 사용자

- 디바이스에 대해 열린 티켓

- 펌웨어가 마지막으로 업그레이드된 날짜

- 인터넷 액세스가 허용된 디바이스

- 활성 바이러스 백신 애플리케이션을 실행하는 디바이스

- 디바이스에 로그인한 사용자

:::image type="content" source="media/how-to-work-with-asset-inventory-information/asset-inventory-screen-with-items-highlighted-v2.png" alt-text="디바이스 인벤토리 화면의 데이터 테이블.":::

다음 중 하나를 수행하여 데이터를 통합할 수 있습니다.

- 수동으로 데이터 추가

- Defender for IoT에서 제공하는 사용자 지정 스크립트 실행

:::image type="content" source="media/how-to-work-with-asset-inventory-information/enterprise-data-integrator-graph.png" alt-text="엔터프라이즈 데이터 통합자의 다이어그램.":::

Defender for IoT 기술 지원 팀과 협력하여 웹 API 쿼리를 수신하도록 시스템을 설정할 수 있습니다.

수동으로 데이터를 추가하려면:

1. 측면 메뉴에서 **디바이스 인벤토리** 를 선택한 다음 :::image type="icon" source="media/how-to-work-with-asset-inventory-information/menu-icon.png" border="false":::를 선택합니다.

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/asset-inventory-settings-v2.png" alt-text="디바이스의 인벤토리 설정 편집.":::

2. **디바이스 인벤토리 설정** 대화 상자에서 **사용자 지정 열 추가** 를 선택합니다.

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/add-custom-column.png" alt-text="인벤토리에 사용자 지정 열 추가.":::

3. **사용자 지정 열 추가** 대화 상자에서 새 열 이름(최대 250자)을 추가하고 **수동** 을 선택한 다음 **저장** 을 선택합니다. **디바이스 인벤토리 설정** 대화 상자에 새 항목이 나타납니다.

4. **디바이스 인벤토리** 창의 오른쪽 위 모서리에서 :::image type="icon" source="media/how-to-work-with-asset-inventory-information/menu-icon-device-inventory.png" border="false":::를 선택하고 **모든 디바이스 인벤토리 내보내기** 를 선택합니다. CSV 파일이 생성됩니다.

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/sample-exported-csv-file.png" alt-text="내보낸 CSV 파일.":::

5. 새 열에 수동으로 정보를 추가하고 파일을 저장합니다.

6. **디바이스 인벤토리** 창의 오른쪽 위 모서리에서 :::image type="icon" source="media/how-to-work-with-asset-inventory-information/menu-icon-device-inventory.png" border="false":::를 선택하고 **수동 입력 열 가져오기** 를 선택한 다음 CSV 파일을 찾습니다. 새 데이터가 **디바이스 인벤토리** 테이블에 표시됩니다.

다른 엔터프라이즈 엔터티의 데이터를 통합하려면:

1. **디바이스 인벤토리** 창의 오른쪽 위 모서리에서 :::image type="icon" source="media/how-to-work-with-asset-inventory-information/menu-icon-device-inventory.png" border="false":::를 선택하고 **모든 디바이스 인벤토리 내보내기** 를 선택합니다.

2. **디바이스 인벤토리 설정** 대화 상자에서 **사용자 지정 열 추가** 를 선택합니다.

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/add-custom-column.png" alt-text="인벤토리에 사용자 지정 열 추가.":::

3. **사용자 지정 열 추가** 대화 상자에서 새 열 이름(최대 250자 UTF)을 추가한 다음 **자동** 을 선택합니다. **스크립트 업로드** 및 **스크립트 테스트** 옵션이 나타납니다.

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/add-custom-column-automatic.png" alt-text="사용자 지정 열을 자동으로 추가.":::

4. [Microsoft 지원](https://support.serviceshub.microsoft.com/supportforbusiness/create?sapId=82c88f35-1b8e-f274-ec11-c6efdd6dd099)에서 받은 스크립트를 업로드하고 테스트합니다.

## <a name="retrieve-information-from-the-device-inventory"></a>디바이스 인벤토리에서 정보 검색

관리되는 센서에서 검색된 광범위한 디바이스 정보를 검색하여 파트너 시스템과 통합할 수 있습니다. 예를 들어 센서, 영역, 사이트 ID, IP 주소, MAC 주소, 펌웨어, 프로토콜 및 공급업체 정보를 검색할 수 있습니다. 검색한 정보를 다음 기준으로 필터링합니다.

- 권한 있는 디바이스와 권한이 없는 디바이스.

- 특정 사이트와 연결된 디바이스.

- 특정 영역과 연결된 디바이스.

- 특정 센서와 연결된 디바이스.

Defender for IoT API를 사용하여 이 정보를 검색하고 통합합니다. 자세한 내용은 [Defender for IoT API 센서 및 관리 콘솔 API](references-work-with-defender-for-iot-apis.md)를 참조하세요.

## <a name="filter-the-device-inventory"></a>디바이스 인벤토리 필터링

디바이스 인벤토리를 필터링하여 관심 있는 열을 표시할 수 있습니다. 예를 들어 PLC 디바이스 정보를 볼 수 있습니다.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/asset-inventory-view-v2.png" alt-text="디바이스 인벤토리의 스크린샷.":::

창에서 나가면 필터가 지워집니다.

동일한 필터를 여러 번 사용하려면 필요한 필터 또는 필터 조합을 저장하면 됩니다. 왼쪽 창을 열어 저장한 필터를 확인할 수 있습니다.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/view-your-asset-inventories-v2.png" alt-text="디바이스 인벤토리 화면.":::

디바이스 인벤토리를 필터링하려면:

1. 필터링할 열에서 :::image type="icon" source="media/how-to-work-with-asset-inventory-information/filter-a-column-icon.png" border="false":::를 선택합니다.

2. **필터** 대화 상자에서 필터 형식을 선택합니다.

   - **같음**: 열을 필터링하는 데 사용할 정확한 값입니다. 예를 들어 **같음** 및 `value=ICMP`에 따라 프로토콜 열을 필터링하는 경우 해당 열은 ICMP 프로토콜만 사용하는 디바이스를 표시합니다.

   - **포함**: 열의 다른 값 사이에 포함된 값입니다. 예를 들어 **포함** 및 `value=ICMP`에 따라 프로토콜 열을 필터링하는 경우 이 열은 디바이스에서 사용하는 프로토콜 목록의 일부로 ICMP 프로토콜을 사용하는 디바이스를 표시합니다.

3. 사전순으로 열 정보를 구성하려면 :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-order-icon.png" border="false":::를 선택합니다. :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-a-z-order-icon.png" border="false"::: 및 :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-z-a-order-icon.png" border="false"::: 화살표를 선택하여 순서를 정렬합니다.

4. 새 필터를 저장하려면 필터를 정의하고 **다른 이름으로 저장** 을 선택합니다.

5. 필터 정의를 변경하려면 정의를 변경하고 **변경 내용 저장** 을 선택합니다.

## <a name="next-steps"></a>다음 단계

[디바이스 인벤토리에서 센서 감지 조사](how-to-investigate-sensor-detections-in-a-device-inventory.md)

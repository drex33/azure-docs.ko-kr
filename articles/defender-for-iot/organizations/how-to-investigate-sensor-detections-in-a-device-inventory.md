---
title: 특정 센서에서 검색된 디바이스에 대한 인사이트 얻기
description: 디바이스 인벤토리는 센서가 감지하는 광범위한 디바이스 특성을 표시합니다.
ms.date: 12/06/2020
ms.topic: how-to
ms.openlocfilehash: 10df10a64059e5f5673a9e4ae6f18a09e4c41838
ms.sourcegitcommit: 555ea0d06da38dea1de6ecbe0ed746cddd4566f5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/08/2021
ms.locfileid: "113515276"
---
# <a name="investigate-sensor-detections-in-a-device-inventory"></a>디바이스 인벤토리에서 센서 감지 조사

디바이스 인벤토리는 센서가 감지하는 광범위한 디바이스 특성을 표시합니다. 옵션은 다음에서 사용할 수 있습니다.

 - 쉽게 정보를 필터링합니다.

 - 정보를 CSV 파일로 내보냅니다.

 - Windows 레지스트리 세부 정보를 가져옵니다.

 - 디바이스 맵에 표시할 그룹을 만듭니다.

## <a name="view-device-attributes-in-the-device-inventory"></a>디바이스 인벤토리에서 디바이스 특성 보기

다음 특성은 디바이스 인벤토리 테이블에 표시됩니다.

| 매개 변수 | 설명 |
|--|--|
| 이름 | 센서가 검색한 디바이스 이름이거나 사용자가 입력한 디바이스 이름입니다. |
| 유형 | 센서에 의해 결정되거나 사용자가 입력한 디바이스의 유형입니다. |
| Vendor | MAC 주소에 정의된 디바이스 공급업체의 이름입니다. |
| 운영 체제 | 디바이스의 OS입니다(감지된 경우). |
| 펌웨어 버전 | 디바이스의 펌웨어입니다(감지된 경우). |
| IP 주소 | 정의된 디바이스의 IP 주소입니다. |
| VLAN | 디바이스의 VLAN입니다. 센서에서 VLAN을 검색하도록 지시하는 방법에 대한 자세한 내용은 [VLAN 이름 정의](how-to-manage-the-on-premises-management-console.md#define-vlan-names)(how-to-define-management-console-network-settings.md#define-vlan-names)를 참조하세요. |
| MAC 주소 | 디바이스의 Mac 주소입니다. |
| 프로토콜 | 디바이스에서 사용하는 프로토콜입니다. |
| 승인되지 않은 경고 | 이 디바이스와 연결된 승인되지 않은 경고의 수입니다. |
| 권한 부여됨 | 사용자가 정의한 권한 부여 상태<br />- **True**: 디바이스에 권한이 부여되었습니다.<br />- **False**: 디바이스에 권한이 부여되지 않았습니다. |
| 스캐너 | 사용자에 의해 네트워크 검색 디바이스로 정의됩니다. |
| 프로그래밍 디바이스 | 사용자에 의해 권한 있는 프로그래밍 디바이스로 정의됩니다. <br />- **True**: 디바이스에서 엔지니어링 스테이션과 관련된 PLC, RTU, 컨트롤러에 대한 프로그래밍 작업을 수행합니다. <br />- **False**: 디바이스가 프로그래밍 디바이스가 아닙니다. |
| 그룹 | 이 디바이스가 참여하는 그룹입니다. |
| 마지막 작업 | 디바이스에서 수행하는 마지막 작업입니다. |
| Discovered | 이 디바이스가 네트워크에서 처음으로 표시되는 경우 |

디바이스 인벤토리를 보려면 다음을 수행합니다.

1. 왼쪽 창에서 **디바이스** 를 선택합니다. 오른쪽에 **디바이스** 창이 열립니다.

2. **디바이스** 창에서 :::image type="icon" source="media/how-to-work-with-asset-inventory-information/device-pane-icon.png" border="false":::를 선택합니다.

열을 숨기거나 표시하려면 디바이스 인벤토리 테이블을 사용자 지정합니다.

1. 디바이스 인벤토리의 오른쪽 상단 메뉴에서 :::image type="icon" source="media/how-to-work-with-asset-inventory-information/settings-icon.png" border="false":::를 선택합니다.

    :::image type="content" source="media/how-to-work-with-asset-inventory-information/device-inventory-settings-screens-v2.png" alt-text="디바이스 인벤토리 설정 화면.":::

2. **디바이스 인벤토리 설정** 창에서 디바이스 인벤토리 테이블에 표시하려는 열을 선택합니다.

3. 화살표를 사용하여 테이블의 열 위치를 변경합니다.

4. **저장** 을 선택합니다. **디바이스 인벤토리 설정** 창이 닫히고 새 설정이 표에 표시됩니다.

### <a name="create-temporary-device-inventory-filters"></a>임시 디바이스 인벤토리 필터 만들기

테이블에 표시되는 정보를 정의하는 필터를 설정할 수 있습니다. 예를 들어 PLC 디바이스의 정보만 보도록 설정할 수 있습니다.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/devices-learning-v2.png" alt-text="디바이스 학습.":::

인벤토리를 나갈 때 필터는 저장되지 않습니다.

### <a name="save-device-inventory-filters"></a>디바이스 인벤토리 필터 저장

필요한 필터 또는 필터 조합을 저장하고 디바이스 인벤토리에 다시 적용할 수 있습니다. 특정 디바이스에 기반한 더 광범위한 필터 또는 특정 유형 및 프로토콜에 기반한 더 좁혀진 필터를 만듭니다.

저장하는 필터는 디바이스 맵 그룹으로도 저장됩니다. 이 기능은 맵에서 네트워크 디바이스를 보는 추가적인 세분화 수준을 제공합니다.

필터를 만들려면 다음을 수행합니다.

1. 필터링할 열에서 :::image type="icon" source="media/how-to-work-with-asset-inventory-information/filter-icon.png" border="false":::을 선택합니다.

2. **필터** 대화 상자에서 필터 형식을 선택합니다.

   - **같음**: 열을 필터링하는 데 사용할 정확한 값입니다. 예를 들어 **같음** 및 `value=ICMP`에 따라 프로토콜 열을 필터링하는 경우 해당 열은 ICMP 프로토콜만 사용하는 디바이스를 표시합니다.

   - **포함**: 열의 다른 값 사이에 포함된 값입니다. 예를 들어 **포함** 및 `value=ICMP`에 따라 프로토콜 열을 필터링하는 경우 이 열은 디바이스에서 사용하는 프로토콜 목록의 일부로 ICMP 프로토콜을 사용하는 디바이스를 표시합니다.

3. 사전순으로 열 정보를 구성하려면 :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-order-icon.png" border="false":::를 선택합니다. :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-a-z-order-icon.png" border="false"::: 및 :::image type="icon" source="media/how-to-work-with-asset-inventory-information/alphabetical-z-a-order-icon.png" border="false"::: 화살표를 선택하여 순서를 정렬합니다.

4. 새 필터를 저장하려면 필터를 정의하고 **다른 이름으로 저장** 을 선택합니다.

5. 필터 정의를 변경하려면 정의를 변경하고 **변경 내용 저장** 을 선택합니다.

필터를 보려면 다음을 수행합니다.

- 왼쪽 창을 열고 저장한 필터를 확인합니다.

  :::image type="content" source="media/how-to-work-with-asset-inventory-information/filters-from-left-pane-v2.png" alt-text="왼쪽 창에서 필터 보기.":::

### <a name="view-filtered-information-as-a-map-group"></a>필터링된 정보를 맵 그룹으로 보기

맵 보기로 전환하면 필터링된 디바이스가 강조 표시되고 필터링됩니다. 저장한 필터 그룹이 **디바이스 인벤토리 필터** 그룹 아래의 측면 메뉴에 표시됩니다.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/filters-in-the-map-view-v2.png" alt-text="맵 보기에서 필터 보기.":::

## <a name="learn-windows-registry-details"></a>Windows 레지스트리 정보 알아보기

OT 디바이스를 학습하는 것 외에도 Microsoft Windows 워크스테이션과 서버를 검색할 수 있습니다. 이러한 디바이스는 디바이스 인벤토리에도 표시됩니다. 디바이스를 학습한 후 다음과 같은 자세한 Windows 정보를 사용하여 디바이스 인벤토리를 보강할 수 있습니다.

- 설치된 Windows 버전

- 설치된 애플리케이션

- 패치 수준 정보

- 포트 열기

- OS 버전에 대한 보다 강력한 정보

이 정보를 검색하는 데 사용할 수 있는 두 가지 옵션은 다음과 같습니다.

- 예약된 WMI 검사를 사용하는 활성 폴링. 

- 디바이스에서 스크립트를 배포 및 실행하여 로컬 설문 조사. 로컬 스크립트로 작업하면 엔드포인트에서 WMI 폴링을 실행하는 위험이 무시됩니다. 폭포 및 단방향 요소를 사용하여 규제된 네트워크에도 유용합니다.

이 문서에서는 스크립트를 사용하여 Windows 엔드포인트 레지스트리를 로컬에서 조사하는 방법을 설명합니다. 이 정보는 경고, 알림, 데이터 마이닝 보고서, 위험 평가, 공격 벡터 보고서를 생성하는 데 사용됩니다.

:::image type="content" source="media/how-to-work-with-asset-inventory-information/data-mining-screen.png" alt-text="데이터 마이닝 스크린샷.":::

다음 Windows 운영 체제를 조사할 수 있습니다.

- Windows XP

- Windows 2000

- Windows NT

- Windows 7

- Windows 10

- Windows Server 2003/2008/2012/2016

### <a name="before-you-begin"></a>시작하기 전에

스크립트를 사용하려면 다음 요구 사항을 충족해야 합니다.

- 디바이스에서 스크립트를 실행하려면 관리자 권한이 필요합니다.

- 센서가 Windows 디바이스를 이미 학습했어야 합니다. 즉, 디바이스가 이미 존재하는 경우 스크립트에서 해당 정보를 검색합니다.

- 센서는 Windows PC가 연결된 네트워크를 모니터링합니다.

### <a name="acquire-the-script"></a>스크립트 취득

스크립트를 수신하려면 [고객 지원에 문의하세요](mailto:support.microsoft.com).

### <a name="deploy-the-script"></a>스크립트 배포

스크립트를 한 번 배포하거나 표준 자동화된 배포 방법 및 도구를 사용하여 진행 중인 쿼리를 예약할 수 있습니다.

### <a name="about-the-script"></a>스크립트 정보

- 스크립트는 설치된 프로그램이 아닌 유틸리티로 실행됩니다. 스크립트를 실행해도 엔드포인트에는 영향을 주지 않습니다.

- 스크립트가 생성하는 파일은 사용자가 삭제할 때까지 로컬 드라이브에 남아 있습니다.

- 스크립트가 생성하는 파일은 서로 옆에 있습니다. 해당 파일을 분리하지 마세요.

- 동일한 위치에서 스크립트를 다시 실행하는 경우 이러한 파일을 덮어씁니다.

스크립트를 실행하려면  

1. 스크립트를 로컬 드라이브에 복사하고 압축을 풉니다. 다음 파일이 표시됩니다.

    - start.bat

    - settings.json

    - data.bin

    - run.bat

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/files-in-file-explorer.png" alt-text="파일 탐색기에서 파일 보기.":::

2. `run.bat` 파일을 실행합니다.

3. 레지스트리를 검색한 후에는 레지스트리 정보와 함께 CX-스냅샷 파일이 표시됩니다.

4. 파일 이름은 스냅샷의 시스템 이름 및 날짜와 시간을 나타냅니다. 예제 파일 이름은 `CX-snaphot_SystemName_Month_Year_Time`입니다.

### <a name="import-device-details"></a>디바이스 세부 정보 가져오기

각 엔드포인트에서 학습한 정보를 센서로 가져와야 합니다.

쿼리에서 생성된 파일을 센서에서 액세스할 수 있는 하나의 폴더에 배치할 수 있습니다. 표준, 자동화된 방법 및 도구를 사용하여 각 Windows 엔드포인트에서 센서로 가져올 위치로 파일을 이동합니다.

파일 이름을 업데이트하지 마세요.

가져오려면 다음을 수행합니다.

1. **Windows 구성 가져오기** 대화 상자에서 **설정 가져오기** 를 선택합니다.

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/import-windows-configuration-v2.png" alt-text="Windows 구성을 가져옵니다.":::

2. **추가** 를 선택한 다음 모든 파일(Ctrl + A)을 선택합니다.

3. **닫기** 를 선택합니다. 디바이스 레지스트리 정보를 가져옵니다. 파일을 업로드하는 데 문제가 있는 경우 업로드에 실패한 파일이 표시됩니다.

   :::image type="content" source="media/how-to-work-with-asset-inventory-information/add-new-file.png" alt-text="추가된 파일을 업로드했습니다.":::

## <a name="view-and-delete-inactive-devices-from-the-inventory"></a>인벤토리에서 비활성 디바이스 보기 및 삭제

네트워크에서 비활성화된 디바이스를 보고 삭제할 수 있습니다.
다음과 같은 이유로 디바이스가 비활성화될 수 있습니다.
- 잘못 구성된 SPAN 포트
- 네트워크 범위 변경
- 네트워크에서 분리

비활성 디바이스를 삭제하면 다음과 같은 이점이 있습니다.

- Defender for IoT는 현재 네트워크 작업의 보다 정확한 표현을 만듭니다.
- 구독을 관리할 때 커밋된 디바이스 평가 향상
- 화면의 복잡도 줄이기

### <a name="view-inactive-devices"></a>비활성 디바이스 보기

인벤토리를 필터링하여 비활성화된 디바이스를 표시할 수 있습니다.

- 7일 이상인 경우
- 14일 이상인 경우
- 30일 이상
- 90일 이상

**인벤토리를 필터링하려면 다음을 수행합니다.**
  
1. 인벤토리에서 **마지막으로 본 항목** 필터 아이콘을 선택합니다.
1. 필터 옵션을 선택합니다.
1. **적용** 을 선택합니다.

### <a name="delete-inactive-devices"></a>비활성 디바이스 삭제

인벤토리에서 삭제하는 디바이스는 맵에서 제거되며 Defender for IoT 보고서(예: 데이터 마이닝, 위험 평가 및 공격 벡터 보고서)를 생성할 때 계산되지 않습니다.

디바이스를 삭제하는 이유를 기록하라는 메시지가 표시됩니다. 이 정보와 삭제된 디바이스의 시간/날짜 및 수는 이벤트 타임라인에 표시됩니다.

**인벤토리에서 디바이스를 삭제하려면 다음을 수행합니다.**
  
1. 인벤토리에서 **마지막으로 본 항목** 필터 아이콘을 선택합니다.
1. 필터 옵션을 선택합니다.
1. **적용** 을 선택합니다.
1. **비활성 디바이스 삭제** 를 선택합니다.
1. 열리는 확인 대화 상자에서 삭제 이유를 입력하고 **삭제** 를 선택합니다. 필터 범위 내에서 검색된 모든 디바이스가 삭제됩니다. 많은 수의 디바이스를 삭제하는 경우 삭제 프로세스는 몇 분 정도 걸릴 수 있습니다.

## <a name="export-device-inventory-information"></a>디바이스 인벤토리 정보 내보내기

디바이스 인벤토리 정보를 Excel 파일로 내보낼 수 있습니다.

CSV 파일을 내보내려면 다음을 수행합니다.

- 디바이스 인벤토리의 오른쪽 상단 메뉴에서 :::image type="icon" source="media/how-to-work-with-asset-inventory-information/csv-excel-export-icon.png" border="false":::를 선택합니다. CSV 보고서가 생성되어 다운로드됩니다.

## <a name="see-also"></a>참고 항목

[디바이스 인벤토리에서 모든 엔터프라이즈 센서 감지 조사](how-to-investigate-all-enterprise-sensor-detections-in-a-device-inventory.md)

[사이트 맵 보기 작업](how-to-gain-insight-into-global-regional-and-local-threats.md#work-with-site-map-views)

---
title: 경고 페이지에서 경고 필터링 및 관리
description: 다양한 범주에 따라 경고를 확인하고, 검색 기능을 사용하여 관심 있는 경고를 쉽게 찾을 수 있습니다.
ms.date: 11/09/2021
ms.topic: how-to
ms.openlocfilehash: d7c8207a8dcd36fd52d9271bcc840ee6730f76da
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132278629"
---
# <a name="filter-and-manage-alerts-from-the-alerts-page"></a>경고 페이지에서 경고 필터링 및 관리 

이 문서에서는 센서에 의해 트리거되는 경고를 확인하고 경고 도구를 사용하여 경고를 관리하는 방법을 설명합니다.

보관 또는 고정된 경고와 같이 다양한 범주를 기준으로 경고를 볼 수 있습니다. 또한 IP 또는 MAC 주소를 기반으로 하는 경고와 같이 관심 있는 경고를 검색할 수 있습니다.  

센서 대시보드에서 경고를 볼 수도 있습니다.

경고를 보려면

- 측면 메뉴에서 **경고** 를 선택합니다. 경고 창에 센서가 검색한 경고가 표시됩니다.

  :::image type="content" source="media/how-to-work-with-alerts-sensor/alerts-screen.png" alt-text="경고 화면 뷰":::

## <a name="view-alerts-by-category"></a>범주별로 경고 보기

**경고** 기본 보기에서 다양한 범주에 따라 경고를 볼 수 있습니다. 경고를 선택하여 세부 정보를 검토하고 이벤트를 관리합니다.

| 유형별 정렬 | Description |
|--|--|
| **중요한 경고** | 경고가 중요도별로 정렬되었습니다. |
| **고정된 경고** | 추가 조사를 위해 사용자가 고정한 경고입니다. 고정된 경고는 보관되지 않으며 고정됨 폴더에 14일 동안 저장됩니다. |
| **최근 경고** | 시간별로 정렬된 경고입니다. |
| **확인된 경고** | 확인되었지만 처리되지 않았거나, 음소거되었다가 음소거가 해제된 경고입니다. |
| **보관된 경고** | 시스템이 자동으로 보관한 경고입니다. 기본적으로 경고는 경고가 트리거된 후 14일 후에 보관됩니다. 해당 경고에는 관리자 사용자만 액세스할 수 있습니다. |

## <a name="search-for-alerts-of-interest"></a>관심 경고 검색

경고 기본 보기는 관심 있는 경고를 찾는 데 도움이 되는 다양한 검색 기능을 제공합니다.

:::image type="content" source="media/how-to-work-with-alerts-sensor/main-alerts-view.png" alt-text="경고 학습 스크린샷":::

### <a name="text-search"></a>텍스트 검색

사용 가능한 검색 옵션을 사용하여 텍스트, 숫자 또는 문자로 경고를 검색합니다.

검색하려면 다음을 따릅니다.

- 사용 가능한 검색 필드에 필요한 텍스트를 입력하고 키보드에서 Enter 키를 누릅니다.

검색을 지우려면 다음을 따릅니다.

- 사용 가능한 필드에서 텍스트를 삭제하고 키보드에서 Enter 키를 누릅니다.

### <a name="device-group-or-device-ip-address-search"></a>디바이스 그룹 또는 디바이스 IP 주소 검색

특정 IP 주소 또는 디바이스 그룹을 참조하는 경고를 검색합니다. 디바이스 그룹은 디바이스 맵에 생성됩니다.

고급 필터를 사용하려면 다음을 따릅니다.

1. **경고** 기본 보기에서 **고급 필터** 를 선택합니다.

2. 디바이스 그룹 또는 디바이스를 선택합니다.

3. **확인** 을 선택합니다.

4. 검색을 지우려면 **모두 지우기** 를 선택합니다.

### <a name="security-versus-operational-alert-search"></a>보안 대 운영 경고 검색

다음을 통해 운영 및 보안 경고 간의 전환을 수행합니다.

- **보안** 경고는 잠재적인 맬웨어 트래픽, 네트워크 변칙, 정책 위반, 프로토콜 위반을 나타냅니다.

- **운영** 경고는 네트워크 비정상, 정책 위반 및 프로토콜 위반을 나타냅니다.

이 옵션 중에서 하나도 선택하지 않으면 모든 경고가 표시됩니다.

:::image type="content" source="media/how-to-work-with-alerts-sensor/alerts-security.png" alt-text="경고의 보안 화면":::

## <a name="alert-page-options"></a>경고 페이지 옵션

경고 메시지는 다음 작업을 제공합니다.

- :::image type="icon" source="media/how-to-work-with-alerts-sensor/acknowledge-an-alert-icon.png" border="false":::를 선택하여 경고를 확인합니다.

- :::image type="icon" source="media/how-to-work-with-alerts-sensor/unacknowledge-an-alert-icon.png" border="false":::를 선택하여 경고의 확인을 취소합니다.

- :::image type="icon" source="media/how-to-work-with-alerts-sensor/pin-an-alert-icon.png" border="false":::를 선택하여 경고를 고정합니다.

- :::image type="icon" source="media/how-to-work-with-alerts-sensor/unpin-an-alert-icon.png" border="false":::를 선택하여 경고의 고정을 해제합니다.

- :::image type="icon" source="media/how-to-work-with-alerts-sensor/acknowledge-all-alerts-icon.png" border="false":::를 선택하여 모든 경고를 확인합니다.

- :::image type="icon" source="media/how-to-work-with-alerts-sensor/learn-and-acknowledge-all-alerts.png" border="false":::를 선택하여 모든 경고를 살펴보고 확인합니다.

- :::image type="icon" source="media/how-to-work-with-alerts-sensor/export-to-csv.png" border="false":::를 선택하여 경고의 정보를 .csv 파일로 내보냅니다. 여러 디바이스에 적용되는 각 경고에 대해 별도의 행으로 경고 정보를 내보내려면 **확장된 경고 내보내기** 를 사용합니다.

## <a name="alert-pop-up-window-options"></a>경고 팝업 창 옵션

- :::image type="icon" source="media/how-to-work-with-alerts-sensor/export-to-pdf.png" border="false"::: 아이콘을 선택하여 PDF 파일로 경고 보고서를 다운로드합니다.

- :::image type="icon" source="media/how-to-work-with-alerts-sensor/download-pcap.png" border="false"::: 아이콘을 선택하여 PCAP 파일을 다운로드합니다. 무료 네트워크 프로토콜 분석기인 Wireshark로 파일을 볼 수 있습니다.

- :::image type="icon" source="media/how-to-work-with-alerts-sensor/download-filtered-pcap.png" border="false":::를 선택하여 관련 경고 패킷만 포함하는 필터링된 PCAP 파일을 다운로드하면 출력 파일 크기를 줄이고 보다 집중적으로 분석을 수행할 수 있습니다. Wireshark를 사용하여 파일을 볼 수 있습니다.

- :::image type="icon" source="media/how-to-work-with-alerts-sensor/show-alert-in-timeline.png" border="false"::: 아이콘을 선택하여 이벤트 타임라인에 경고를 표시합니다.

- :::image type="icon" source="media/how-to-work-with-alerts-sensor/pin-an-alert-icon.png" border="false"::: 아이콘을 선택하여 경고를 고정합니다.

- :::image type="icon" source="media/how-to-work-with-alerts-sensor/unpin-an-alert-icon.png" border="false"::: 아이콘을 선택하여 경고의 고정을 해제합니다.

- :::image type="icon" source="media/how-to-work-with-alerts-sensor/learn-icon.png" border="false":::를 선택하여 트래픽을 승인합니다(보안 분석가 및 관리자만 가능).

- 디바이스를 선택하여 디바이스 맵에 디바이스를 표시합니다.

## <a name="next-steps"></a>다음 단계

[경고 이벤트 관리](how-to-manage-the-alert-event.md)

[경고 워크플로 가속화](how-to-accelerate-alert-incident-response.md)

---
title: 센서 디바이스 맵 작업
description: 디바이스 맵은 검색된 네트워크 디바이스를 그래픽으로 표시합니다. 이 맵을 사용하여 디바이스 정보, 네트워크 슬라이스를 분석 및 관리하고 보고서를 생성합니다.
ms.date: 1/7/2021
ms.topic: how-to
ms.openlocfilehash: 58ccb69606f41a279424d0faa0241befbe9914bf
ms.sourcegitcommit: 5361d9fe40d5c00f19409649e5e8fed660ba4800
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130137796"
---
# <a name="investigate-sensor-detections-in-the-device-map"></a>디바이스 맵에서 센서 검색 조사

디바이스 맵은 검색된 네트워크 디바이스를 그래픽으로 표시합니다. 맵을 사용하여 다음을 수행합니다.

  - 디바이스 정보를 검색, 분석, 관리합니다.

  - 네트워크 슬라이스(예: 관심 있는 특정 그룹 또는 Purdue 계층)를 분석합니다.

  - 보고서를 생성합니다(예: 디바이스 세부 정보 및 요약 내보내기).

:::image type="content" source="media/how-to-work-with-maps/device-map-v2.png" alt-text="디바이스 맵 스크린샷.":::

맵에 액세스하려면:

  - 콘솔 주 화면에서 **디바이스 맵** 을 선택합니다.

## <a name="map-search-and-layout-tools"></a>맵 검색 및 레이아웃 도구

맵에서 작업하는 데 사용되는 도구는 다음과 같습니다.

디바이스 맵 창에서 사용할 수 있는 도구는 사용자 역할에 따라 결정됩니다. 사용자 역할에 대한 자세한 내용은 [사용자 만들기 및 관리](how-to-create-and-manage-users.md)를 참조하세요.

| 기호 | Description |
|---|---|
| :::image type="icon" source="media/how-to-work-with-maps/search-bar-icon-v2.png" border="false":::| IP 주소 또는 MAC 주소로 특정 디바이스를 검색합니다. 텍스트 상자에 IP 주소 또는 MAC 주소를 입력합니다. 맵에는 연결된 디바이스와 함께 검색한 디바이스가 표시됩니다. |
| 그룹 강조 표시 및 필터 <br /> :::image type="content" source="media/how-to-work-with-maps/group-highlight-and-filters-v2.png" alt-text="그룹 강조 표시 및 필터 스크린샷."::: | 기본 및 사용자 지정 디바이스 그룹을 기준으로 맵을 필터링하거나 강조 표시합니다. |
| :::image type="icon" source="media/how-to-work-with-maps/collapse-view-icon.png" border="false"::: | OT 디바이스에 포커스가 있는 보기를 사용하고 IT 디바이스를 그룹화하기 위한 IT 축소 보기입니다.  |
| :::image type="icon" source="media/how-to-work-with-maps/device-management-icon.png" border="false"::: | 맵에서 현재 디바이스 정렬을 유지합니다. 예를 들어 맵의 새 위치로 디바이스를 끌면 맵을 종료할 때 디바이스가 이러한 위치에 그대로 유지됩니다. |
| :::image type="icon" source="media/how-to-work-with-maps/fit-to-screen-icon.png" border="false"::: | 화면에 맞춤 |
| :::image type="icon" source="media/how-to-work-with-maps/layer-icon.png" border="false"::: :::image type="icon" source="media/how-to-work-with-maps/layouts-icon-v2.png" border="false"::: | - 자동, 공정 제어, 감독, 엔터프라이즈 등 이 디바이스에 대해 식별된 Purdue 계층을 봅니다. <br /> - 디바이스 간의 연결을 봅니다.|
| :::image type="icon" source="media/how-to-work-with-maps/broadcast-icon.png" border="false"::: | 브로드캐스트와 멀티캐스트 사이에 표시하거나 숨깁니다. |
| :::image type="icon" source="media/how-to-work-with-maps/time-icon.png" border="false"::: | 다른 디바이스와 마지막으로 통신한 시간에 따라 맵의 디바이스를 필터링합니다. |
| :::image type="icon" source="media/how-to-work-with-maps/notifications-icon.png" alt-text="알림" border="false":::: | 디바이스에 대한 알림을 봅니다. 기존 MAC 주소를 사용하는 디바이스의 새 IP가 검색되는 경우를 예로 들 수 있습니다. |
| :::image type="icon" source="media/how-to-work-with-maps/export-import.png" alt-text="내보내기" border="false"::: | 디바이스 정보를 내보내고 가져옵니다. |
| :::image type="icon" source="media/how-to-work-with-maps/properties-icon.png" alt-text="properties" border="false"::: | 선택한 디바이스의 기본 디바이스 속성을 봅니다. |
| :::image type="icon" source="media/how-to-work-with-maps/zoom-in-icon-v2.png" alt-text="확대" border="false"::: 또는 :::image type="icon" source="media/how-to-work-with-maps/zoom-out-icon-v2.png" alt-text="축소" border="false"::: | 맵에서 디바이스를 확대하거나 축소합니다. |

## <a name="view-ot-elements-only"></a>OT 요소만 보기

기본적으로 IT 디바이스는 서브넷을 기준으로 자동으로 집계되므로 맵 보기의 포커스는 OT 및 ICS 네트워크에 있습니다. IT 네트워크 요소의 표시가 최소한으로 축소되어 맵에 표시되는 총 디바이스 수가 줄어들고 OT 및 ICS 네트워크 요소를 명확하게 파악할 수 있습니다.

각 서브넷은 IT 서브넷과 백의 세부 정보를 볼 수 있는 대화형 축소 및 확장 기능을 포함하여 단일 엔터티로 디바이스 맵에 표시됩니다.

아래 그림은 27개의 IT 네트워크 요소가 있는 축소된 IT 서브넷을 보여 줍니다.

:::image type="content" source="media/how-to-work-with-maps/shrunk-it-subnet-v2.png" alt-text="27개의 IT 네트워크 요소가 있는 축소된 IT 서브넷":::

IT 네트워크 축소 기능을 사용하려면:

- **시스템 설정** 창에서 IT 네트워크 그룹화 기능 설정/해제가 사용하도록 설정되어 있는지 확인합니다.

:::image type="content" source="media/how-to-work-with-maps/shrunk-it-subnet-v2.png" alt-text="시스템 설정 창":::

IT 서브넷을 확장하려면:

1. IT 네트워크와 OT 네트워크를 구분하려면 시스템 설정 화면에서 **서브넷** 을 선택합니다.

   > [!NOTE]
   > IT 네트워크와 OT 네트워크를 구분하려면 각 서브넷에 사용자가 쉽게 식별할 수 있는 의미 있는 이름을 지정하는 것이 좋습니다.

   :::image type="content" source="media/how-to-work-with-maps/subnet-list.png" alt-text="서브넷 구성":::

2. **서브넷 구성 편집** 창에서 IT 서브넷으로 정의하려는 각 서브넷의 **ICS 서브넷** 확인란 선택을 취소합니다. IT 서브넷은 IT 네트워크에서 컨트롤러 또는 PLC와 같은 ICS 디바이스에 대한 알림과 함께 디바이스 맵에 축소된 상태로 표시됩니다.

   :::image type="content" source="media/how-to-work-with-maps/edit-config.png" alt-text="서브넷 구성 편집":::

3. 맵에서 IT 네트워크를 확장하려면 디바이스 창에서 해당 네트워크를 마우스 오른쪽 단추로 클릭하고 **네트워크 확장** 을 선택합니다.

   :::image type="content" source="media/how-to-work-with-maps/expand-network.png" alt-text="네트워크 보기를 확장합니다.":::

4. 레이아웃 변경을 다시 실행할 수 없음을 알리는 확인 상자가 표시됩니다.

5. **확인** 을 선택합니다. 맵에 IT 서브넷 요소가 표시됩니다.

   :::image type="content" source="media/how-to-work-with-maps/fixed-map.png" alt-text="확인":::

IT 서브넷을 축소하려면:

1.  왼쪽 창에서 **디바이스** 를 선택합니다.

2. 디바이스 창에서 축소 아이콘을 선택합니다. 빨간색 숫자는 현재 맵에 표시되는 확장된 IT 서브넷 수를 나타냅니다.

   :::image type="content" source="media/how-to-work-with-maps/devices-notifications.png" alt-text="디바이스 창":::

3. 축소하려는 서브넷을 선택하거나 **모두 축소** 를 선택합니다. 선택한 서브넷은 축소된 상태로 맵에 표시됩니다.

   :::image type="content" source="media/how-to-work-with-maps/close-all-subnets.png" alt-text="모두 축소":::

축소 아이콘은 확장된 IT 서브넷의 업데이트된 수로 업데이트됩니다.

## <a name="view-or-highlight-device-groups"></a>디바이스 그룹 보기 또는 강조 표시

디바이스 그룹을 기준으로 맵 표시를 사용자 지정할 수 있습니다. 특정 OT 프로토콜, VLAN 또는 서브넷과 연결된 디바이스 그룹을 예로 들 수 있습니다. 미리 정의된 그룹을 사용할 수 있으며, 사용자 지정 그룹을 만들 수 있습니다.

그룹을 보는 방법은 다음과 같습니다.

  - **강조 표시:** 특정 그룹에 속한 디바이스를 파란색으로 강조 표시합니다.

  - **필터링:** 특정 그룹에 속하는 디바이스만 표시합니다.

:::image type="content" source="media/how-to-work-with-maps/port-standard.png" alt-text="포트의 표준 보기":::

다음과 같은 미리 정의된 그룹을 사용할 수 있습니다.

| 그룹 이름 | 설명 |
|--|--|
| **알려진 애플리케이션** | TCP와 같이 예약된 포트를 사용하는 디바이스입니다.  |
| **비표준 포트(기본값)** | 비표준 포트 또는 별칭이 할당되지 않은 포트를 사용하는 디바이스입니다. |
| **OT 프로토콜(기본값)** | 알려진 OT 트래픽을 처리하는 디바이스입니다. |
| **인증(기본값)** | 학습 프로세스 중에 네트워크에서 검색되었거나 네트워크에서 공식적으로 인증된 디바이스입니다. |
| **디바이스 인벤토리 필터** | 필터에 따라 그룹화된 디바이스는 디바이스 인벤토리 테이블에 저장됩니다. |
| **폴링 간격** | 폴링 간격을 기준으로 그룹화된 디바이스입니다. 폴링 간격은 순환 채널 또는 기간에 따라 자동으로 생성됩니다. 예를 들어 15.0초, 3.0초, 1.5초 또는 어떤 간격도 될 수 있습니다. 이 정보를 검토하면 시스템이 너무 빠르게 폴링하는지 느리게 폴링하는지 알 수 있습니다. |
| **프로그래밍** | 엔지니어링 스테이션 및 프로그래밍 컴퓨터입니다. |
| **서브넷** | 특정 서브넷에 속하는 디바이스입니다. |
| **VLAN** | 특정 VLAN ID와 연결된 디바이스입니다. |
| **서브넷 간 연결** | 한 서브넷에서 다른 서브넷으로 통신하는 디바이스입니다. |
| **고정된 경고** | 사용자가 경고를 고정한 디바이스입니다. |
| **공격 벡터 시뮬레이션** | 공격 벡터 보고서에서 검색된 취약한 디바이스입니다. 맵에서 이러한 디바이스를 보려면 공격 벡터를 생성할 때 **디바이스 맵에 표시** 확인란을 선택합니다. :::image type="content" source="media/how-to-work-with-maps/add-attack-v2.png" alt-text="공격 벡터 시뮬레이션 추가"::: |
| **마지막 확인 날짜** | 마지막으로 발견된 시간 프레임을 기준으로 그룹화된 디바이스입니다(예: 1시간, 6시간, 1일, 7일). |
| **Active Directory에 없음** | Active Directory와 통신하지 않는 모든 비 PLC 디바이스입니다. |

디바이스를 강조 표시하거나 필터링하려면:

1. 측면 메뉴에서 **디바이스 맵** 을 선택합니다.

2. 필터 아이콘을 선택합니다. :::image type="content" source="media/how-to-work-with-maps/menu-icon.png" alt-text="메뉴":::

3. 그룹 창에서 디바이스를 강조 표시하거나 필터링할 그룹을 선택합니다.

4. **강조 표시** 또는 **필터** 를 선택합니다. 강조 표시 또는 필터를 제거하려면 동일한 선택을 토글합니다.

## <a name="define-custom-groups"></a>사용자 지정 그룹 정의

미리 정의된 그룹을 보는 것 외에도 사용자 지정 그룹을 정의할 수 있습니다. 이 그룹은 디바이스 맵, 디바이스 인벤토리 및 데이터 마이닝 보고서에 표시됩니다.

> [!NOTE]
> 디바이스 인벤토리에서 그룹을 만들 수도 있습니다.

그룹 만들기:

1. 측면 메뉴에서 **디바이스** 를 선택합니다. 디바이스 맵이 표시됩니다.

1. :::image type="content" source="media/how-to-work-with-maps/menu-icon.png" alt-text="그룹 설정":::을 선택하여 그룹 설정을 표시합니다.

1. :::image type="content" source="media/how-to-work-with-maps/create-group-v2.png" alt-text="그룹":::을 선택하여 새 사용자 지정 그룹을 만듭니다.

:::image type="content" source="media/how-to-work-with-maps/custom-group-v2.png" alt-text="사용자 지정 그룹 만들기 화면":::

1. 그룹의 이름을 추가합니다(최대 30자 사용).

1. 다음과 같이 관련 디바이스를 선택합니다.

   - 목록에서 선택하여 이 메뉴에서 디바이스를 추가합니다(화살표 단추 선택).<br /> 또는, <br /> 
   - 선택한 그룹에서 복사하여 이 메뉴에서 디바이스를 추가합니다(화살표 단추 선택).

1. 사용자 지정 그룹에 기존 그룹을 추가하려면 **그룹 추가** 를 선택합니다.

### <a name="add-devices-to-a-custom-group"></a>사용자 지정 그룹에 디바이스 추가

사용자 지정 그룹에 디바이스를 추가하거나 새 사용자 지정 그룹과 디바이스를 만들 수 있습니다.

1. 맵에서 마우스 오른쪽 단추로 디바이스를 클릭합니다.

1. **그룹에 추가** 를 선택합니다.

1. 그룹 필드에 그룹 이름을 입력하고 +를 선택합니다. 새 그룹이 나타납니다. 그룹이 이미 있는 경우 기존 사용자 지정 그룹에 추가됩니다.

   :::image type="content" source="media/how-to-work-with-maps/groups-section-v2.png" alt-text="그룹 이름":::

1. 1~3단계를 반복하여 그룹에 디바이스를 추가합니다.

## <a name="map-zoom-views"></a>맵 확대/축소 보기

맵 보기를 사용하면 대규모 네트워크를 분석할 때 신속한 포렌식에 도움이 됩니다.

다음 세 가지 디바이스 세부 정보 보기를 표시할 수 있습니다.

  - [조감도](#birds-eye-view)

  - [디바이스 유형 및 연결 보기](#device-type-and-connection-view)

  - [상세 보기](#detailed-view)

### <a name="birds-eye-view"></a>조감도

이 보기는 다음과 같이 표시되는 디바이스 한눈에 보기를 제공합니다.

  - 빨간색 점은 경고가 있는 디바이스를 나타냅니다

  - 별표 점은 중요함으로 표시된 디바이스를 나타냅니다

  - 검은색 점은 경고가 없는 디바이스를 나타냅니다

:::image type="content" source="media/how-to-work-with-maps/colored-dots-v2.png" alt-text="조감도":::

### <a name="device-type-and-connection-view"></a>디바이스 유형 및 연결 보기 

이 보기는 경고, 디바이스 유형, 연결된 디바이스와 함께 디바이스를 강조 표시하기 위해 맵에 디바이스를 아이콘으로 표시합니다.

  - 경고가 있는 디바이스는 빨간색 링으로 표시됩니다

  - 경고가 없는 디바이스는 회색 링으로 표시됩니다

  - 별로 표시된 디바이스는 중요함으로 표시되었습니다

디바이스 유형 아이콘은 연결된 디바이스와 함께 표시됩니다

:::image type="content" source="media/how-to-work-with-maps/colored-rings.png" alt-text="연결 보기":::

### <a name="detailed-view"></a>상세 보기 

상세 보기는 다음 정보와 함께 디바이스 및 디바이스 레이블과 표시기를 표시합니다.

:::image type="content" source="media/how-to-work-with-maps/device-map-v2.png" alt-text="상세 보기":::

### <a name="control-the-zoom-view"></a>확대/축소 보기 제어

표시되는 맵 보기는 맵 확대/축소 수준에 따라 달라집니다. 확대/축소 수준을 변경하면 맵 보기 간에 전환할 수 있습니다.

:::image type="content" source="media/how-to-work-with-maps/zoom-in-out.png" alt-text="확대/축소 보기 제어":::

### <a name="enable-simplified-zoom-views"></a>단순화된 확대/축소 보기 사용

보안 분석가와 RO 사용자에게 조감도 및 디바이스 유형 및 연결 보기에 대한 액세스 권한을 부여하려는 관리자는 단순화된 보기 옵션을 사용하도록 설정해야 합니다.

단순화된 맵 보기 옵션을 사용하도록 설정하려면:

  - **시스템 설정** 을 선택한 다음 **단순화된 맵 보기** 옵션을 설정/해제합니다.

:::image type="content" source="media/how-to-work-with-maps/simplify-view-v2.png" alt-text="맵 보기 단순화":::

## <a name="learn-more-about-devices"></a>디바이스에 대해 자세히 알아보기

다음과 같은 다양한 도구를 사용하여 디바이스 맵의 디바이스에 대해 자세히 알아볼 수 있습니다.

- [디바이스 레이블 및 표시기](#device-labels-and-indicators)

- [디바이스 빠른 보기](#device-quick-views)

- [디바이스 속성 보기 및 관리](#view-and-manage-device-properties)

- [디바이스 유형 보기](#view-device-types)

- [백플레인](#backplane-properties)

- [디바이스 이벤트의 타임라인 보기](#view-a-timeline-of-events-for-the-device)

- [프로그래밍 세부 정보 및 변경 내용 분석](#analyze-programming-details-and-changes)

### <a name="device-labels-and-indicators"></a>디바이스 레이블 및 표시기

맵의 디바이스에 다음 레이블과 표시기가 나타날 수 있습니다.

| 디바이스 레이블 | Description |
|--|--|
| :::image type="content" source="media/how-to-work-with-maps/host-v2.png" alt-text="IP 호스트 이름"::: | IP 주소 호스트 이름 및 IP 주소 또는 서브넷 주소입니다. |
| :::image type="content" source="media/how-to-work-with-maps/amount-alerts-v2.png" alt-text="경고 수"::: | 디바이스와 연결된 경고 수입니다. |
| :::image type="icon" source="media/how-to-work-with-maps/type-v2.png" border="false"::: | 디바이스 유형 아이콘입니다(예: 스토리지, PLC 또는 히스토리언). |
| :::image type="content" source="media/how-to-work-with-maps/grouped-v2.png" alt-text="그룹화된 디바이스"::: | IT 네트워크의 서브넷에 그룹화된 디바이스 수입니다. 이 예에서는 8입니다. |
| :::image type="content" source="media/how-to-work-with-maps/not-authorized-v2.png" alt-text="디바이스 학습 기간"::: | 학습 기간 후에 검색되었고 네트워크 디바이스로 인증되지 않은 디바이스입니다. |
| 실선 | 디바이스 간의 논리적 연결입니다. |
| :::image type="content" source="media/how-to-work-with-maps/new-v2.png" alt-text="새 디바이스"::: | 학습을 완료한 후 검색된 새 디바이스입니다. |

### <a name="device-quick-views"></a>디바이스 빠른 보기

맵에서 디바이스 속성 및 연결에 액세스합니다.

빠른 속성 메뉴를 열려면:

  - :::image type="content" source="media/how-to-work-with-maps/properties.png" alt-text="빠른 속성 메뉴":::를 선택합니다.

#### <a name="quick-device-properties"></a>빠른 디바이스 속성

빠른 속성 화면이 열려 있는 동안 하나 또는 여러 디바이스를 선택하여 해당 디바이스의 강조 표시를 확인합니다.

:::image type="content" source="media/how-to-work-with-maps/device-information.png" alt-text="빠른 디바이스 속성":::

#### <a name="quick-connection-properties"></a>빠른 연결 속성

빠른 속성 화면이 열려 있는 동안 연결을 선택하여 이 연결에서 사용되는 프로토콜과 마지막으로 발견된 시간을 확인합니다.

:::image type="content" source="media/how-to-work-with-maps/connection-details-v2.png" alt-text="빠른 연결 속성":::

## <a name="view-and-manage-device-properties"></a>디바이스 속성 보기 및 관리

맵에 표시되는 각 디바이스의 디바이스 속성을 볼 수 있습니다. 예를 들어 디바이스 이름, 유형 또는 OS, 펌웨어 또는 공급업체를 볼 수 있습니다.

:::image type="content" source="media/how-to-work-with-maps/device-properties-v2.png" alt-text="디바이스 속성 보기 및 관리":::

다음 정보를 수동으로 업데이트할 수 있습니다. 수동으로 입력한 정보는 Defender for IoT에서 검색된 정보를 재정의합니다.

  - 이름

  - 유형

  - OS

  - Purdue 계층

  - Description

| 항목 | Description |
|--|--|
| 기본 정보 | 필요한 기본 정보입니다. |
| 이름 | 디바이스 이름. <br /> 기본적으로 센서는 네트워크에 정의된 디바이스 이름을 검색합니다. 예를 들어 DNS 서버에 정의된 이름입니다. <br /> 이러한 이름을 정의하지 않으면 디바이스 IP 주소가 이 필드에 표시됩니다. <br /> 디바이스 이름은 수동으로 변경할 수 있습니다. 디바이스의 기능을 반영하는 의미 있는 이름을 디바이스에 지정합니다. |
| 유형 | 센서에서 검색한 디바이스 유형입니다. <br /> 자세한 내용은 [디바이스 유형 보기](#view-device-types)를 참조하세요. |
| Vendor | 디바이스 공급업체입니다. 디바이스 MAC 주소의 선행 문자에 따라 결정됩니다. 이 필드는 읽기 전용입니다. |
| 운영 체제 | 센서에서 검색한 디바이스 OS입니다. |
| Purdue 계층 | 다음을 포함하여 이 디바이스에서 센서에 의해 식별된 Purdue 계층입니다. <br /> - 자동 <br /> - 공정 제어 <br /> - 감독 <br /> - 엔터프라이즈 |
| Description | 자유 텍스트 필드입니다. <br /> 디바이스에 대한 자세한 정보를 추가합니다. |
| 특성 | 디바이스에서 추가 정보가 발견되었습니다. 예를 들어, 다음을 확인 합니다. 실행 하 고 키 상태, 보안 상태, 또는 상태 변경 된 경우에 대 한 정보입니다.  <br /> 정보는 읽기 전용이며 특성 섹션에서 업데이트할 수 없습니다. |
| 설정 | 가양성을 방지하기 위해 디바이스 설정을 수동으로 변경할 수 있습니다. <br /> - **인증된 디바이스**: 학습 기간 중에 네트워크에서 검색된 모든 디바이스가 인증된 디바이스로 식별됩니다. 학습 기간 후에 검색되는 디바이스는 기본적으로 인증되지 않은 디바이스로 표시됩니다. 이 정의는 수동으로 변경할 수 있습니다. <br /> - **스캐너로 알려짐**:이 디바이스가 스캐너로 알려져 있고 이에 관해 경고할 필요가 없음을 알고 있는 경우 이 옵션을 사용하도록 설정합니다. <br /> - **프로그래밍 디바이스**:이 디바이스가 프로그래밍 디바이스로 알려져 있고 프로그래밍 변경을 수행하는 데 사용되는 것을 알고 있는 경우 이 옵션을 사용하도록 설정합니다. 이 디바이스를 프로그래밍 디바이스로 식별하면 이 자산에서 발생한 프로그래밍 변경에 대한 경고가 방지됩니다. |
| 사용자 지정 그룹 | 이 디바이스가 참여하는 디바이스 맵의 사용자 지정 그룹입니다. |
| 시스템 상태 | 디바이스의 보안 및 인증 상태: <br /> - 경고가 없는 경우 상태는 `Secured`입니다 <br /> - 디바이스에 대한 경고가 있으면 경고 수가 표시됩니다 <br /> - 학습 기간 후 네트워크에 추가된 디바이스는 `Unauthorized` 상태가 표시됩니다 설정에서 수동으로 디바이스를 `Authorized Device`로 정의할 수 있습니다. <br /> - 이 디바이스의 주소가 동적 주소로 정의되는 경우 `DHCP`가 상태에 추가됩니다. |


| 네트워크 | Description |
|--|--|
| 인터페이스 | 디바이스 인터페이스입니다. RO 필드입니다. |
| 프로토콜 | 디바이스가 사용하는 프로토콜입니다. RO 필드입니다. |
| 펌웨어 | 백플레인 정보를 사용할 수 있는 경우 펌웨어 정보가 표시되지 않습니다. |
| 주소 | 디바이스 IP 주소입니다. |
| 직렬 | 디바이스 일련 번호입니다. |
| 모듈 주소 | 디바이스 모델 및 슬롯 번호 또는 ID입니다. |
| 모델 | 디바이스 모델 번호입니다. |
| 펌웨어 버전 | 펌웨어 버전 번호입니다. |

디바이스 정보를 보려면:

1. 측면 메뉴에서 **디바이스** 를 선택합니다.

2. 디바이스를 마우스 오른쪽 단추로 클릭하고 **속성 보기** 를 선택합니다. 디바이스 속성 창이 표시됩니다.

3. 이 디바이스의 경고에 대한 자세한 정보를 보려면 이 창의 맨 아래에서 필요한 경고를 선택합니다.

### <a name="view-device-types"></a>디바이스 유형 보기

디바이스 유형은 디바이스 검색 프로세스 중에 센서에 의해 자동으로 식별됩니다. 유형을 수동으로 변경할 수 있습니다.

:::image type="content" source="media/how-to-work-with-maps/type-of-device.png" alt-text="디바이스 유형 보기":::

다음 표는 시스템의 모든 유형을 보여 줍니다.

| 범주 | 디바이스 유형 |
|--|--|
| ICS | 엔지니어링 스테이션 <br /> PLC <br />히스토리언 <br />HMI <br />IED <br />DCS 컨트롤러 <br />RTU <br />산업용 포장 시스템 <br />산업용 저울 <br />산업용 로봇 <br />슬롯 <br />미터 <br />가변 주파수 드라이브  <br />로봇 컨트롤러 <br />서보 드라이브 <br />공압 디바이스 <br />Marquee |
| IT | 도메인 컨트롤러 <br />DB 서버 <br />워크스테이션 <br />서버 <br />단말국 <br />스토리지 <br />스마트폰 <br />태블릿 <br />백업 서버 |
| IoT | IP 카메라 <br />프린터  <br />출퇴근 시간 기록 시계 <br />ATM <br />스마트 TV <br />게임 콘솔 <br />DVR <br />도어 제어판 <br />HVAC <br />Thermostat <br />화재 경보기 <br />스마트 조명 <br />스마트 스위치 <br />화재 탐지기 <br />IP 전화기 <br />경보 시스템 <br />경보 사이렌 <br />동작 감지기 <br />엘리베이터 <br />습도 센서 <br />바코드 스캐너 <br />무정전 전원 공급 장치 <br />피플 카운터 시스템 <br />Intercom <br />개집표기 |
| 네트워크 | 무선 액세스 지점 <br />라우터 <br />스위치 <br />방화벽 <br />VPN Gateway <br />NTP 서버 <br />Wifi Pineapple <br />물리적 위치 <br />I/O 어댑터 <br /> 프로토콜 변환기 |

디바이스 정보를 보려면:

1.  측면 메뉴에서 **디바이스** 를 선택합니다.

2. 디바이스를 마우스 오른쪽 단추로 클릭하고 **속성 보기** 를 선택합니다. 디바이스 속성 창이 표시됩니다.

3. 이 디바이스의 경고에 대한 자세한 정보를 보려면 필요한 경고를 선택합니다.

### <a name="backplane-properties"></a>백플레인 속성

PLC에 랙과 슬롯으로 분리된 여러 모듈이 있으면 모듈 카드 간에 특성이 다를 수 있습니다. 예를 들어 IP 주소와 MAC 주소가 같으면 펌웨어가 다를 수 있습니다.

백플레인 옵션을 사용하면 여러 컨트롤러/카드 및 중첩된 디바이스를 다양한 정의를 가진 하나의 엔터티로 검토할 수 있습니다. 백플레인 보기의 각 슬롯은 기본 디바이스(백플레인 뒤에서 검색된 디바이스)를 나타냅니다.

:::image type="content" source="media/how-to-work-with-maps/backplane-image-v2.png" alt-text="백플레인 속성":::

:::image type="content" source="media/how-to-work-with-maps/backplane-details-v2.png" alt-text="백플레인 디바이스 속성":::

백플레인에는 최대 30개의 컨트롤러 카드와 최대 30개의 랙 장치가 포함될 수 있습니다. 여러 수준에 포함된 총 디바이스 수는 최대 200개가 될 수 있습니다.

백플레인 세부 정보가 검색되면 디바이스 속성 창에 백플레인 창이 표시됩니다.

각 슬롯은 기본 디바이스 수 및 모듈 유형을 보여 주는 아이콘과 함께 표시됩니다.

| 아이콘 | 모듈 유형 |
|--|--|
| :::image type="content" source="media/how-to-work-with-maps/power.png" alt-text="전원 공급 장치"::: | 전원 공급 장치 |
| :::image type="content" source="media/how-to-work-with-maps/analog.png" alt-text="아날로그 I/O"::: | 아날로그 I/O |
| :::image type="content" source="media/how-to-work-with-maps/comms.png" alt-text="통신 어댑터"::: | 통신 어댑터 |
| :::image type="content" source="media/how-to-work-with-maps/digital.png" alt-text="디지털 I/O"::: | 디지털 I/O |
| :::image type="content" source="media/how-to-work-with-maps/computer-processor.png" alt-text="CPU"::: | CPU |
| :::image type="content" source="media/how-to-work-with-maps/HMI-icon.png" alt-text="HMI"::: | HMI |
| :::image type="content" source="media/how-to-work-with-maps/average.png" alt-text="일반"::: | 일반 |

슬롯을 선택하면 슬롯 세부 정보가 표시됩니다.

:::image type="content" source="media/how-to-work-with-maps/slot-selection-v2.png" alt-text="슬롯 선택":::

슬롯 뒤에 있는 기본 디바이스를 보려면 **맵에서 보기** 를 선택합니다. 슬롯은 모든 기본 모듈 및 슬롯에 연결된 디바이스와 함께 디바이스 맵에 표시됩니다.

:::image type="content" source="media/how-to-work-with-maps/map-appearance-v2.png" alt-text="맵에서 보기":::

## <a name="view-a-timeline-of-events-for-the-device"></a>디바이스 이벤트의 타임라인 보기

디바이스에 연결된 이벤트의 타임라인을 봅니다.

타임라인을 보려면:

1. 맵에서 디바이스를 마우스 오른쪽 단추로 클릭합니다.

2. **이벤트 표시** 를 선택합니다. 선택한 디바이스에 대해 검색된 이벤트에 관한 정보와 함께 이벤트 타임라인 창이 열립니다.

자세한 내용은 [이벤트 타임라인](#event-timeline)을 참조하세요.

## <a name="analyze-programming-details-and-changes"></a>프로그래밍 세부 정보 및 변경 내용 분석

네트워크 디바이스에서 수행된 프로그래밍 이벤트를 표시하고 코드 변경 내용을 분석하여 포렌식을 향상시킵니다. 이 정보는 다음과 같은 의심스러운 프로그래밍 활동을 검색하는 데 도움이 됩니다.

  - 사용자 오류: 엔지니어가 잘못된 디바이스를 프로그래밍하고 있습니다.

  - 손상된 프로그래밍 자동화: 자동화 실패 때문에 프로그래밍이 잘못 수행됩니다.

  - 해킹된 시스템: 인증되지 않은 사용자가 프로그래밍 디바이스에 로그인했습니다.

프로그래밍된 디바이스를 표시하고 다른 디바이스에 의해 해당 디바이스에서 수행된 다양한 프로그래밍 변경 내용을 스크롤할 수 있습니다.

프로그래밍 디바이스에 의해 추가, 변경, 제거되었거나 다시 로드된 코드를 봅니다. 관심이 있는 파일 형식, 날짜 또는 시간을 기준으로 프로그래밍 변경 내용을 검색합니다.

### <a name="when-to-review-programming-activity"></a>프로그래밍 활동을 검토해야 하는 시기 

프로그래밍 활동을 검토해야 하는 시기는 다음과 같습니다.

  - 인증되지 않은 프로그래밍에 관한 경고를 본 후

  - 계획된 컨트롤러 업데이트 후

  - 프로세스 또는 컴퓨터가 올바르게 작동하지 않을 때(마지막 업데이트를 수행한 사용자 및 시기를 확인하기 위해)

:::image type="content" source="media/how-to-work-with-maps/differences.png" alt-text="프로그래밍 변경 로그":::

다른 옵션을 사용하여 다음을 수행할 수 있습니다.

  - 관심 있는 이벤트를 별로 표시합니다.

  - 현재 코드와 함께 *.txt 파일을 다운로드합니다.

### <a name="about-authorized-vs-unauthorized-programming-events"></a>인증된 프로그래밍 이벤트와 인증되지 않은 프로그래밍 이벤트 비교 정보 

인증되지 않은 프로그래밍 이벤트는 프로그래밍 디바이스로 학습되거나 수동으로 정의되지 않은 디바이스에 의해 수행됩니다. 인증된 프로그래밍 이벤트는 프로그래밍 디바이스로 확인되거나 수동으로 정의된 디바이스에 의해 수행됩니다.

프로그래밍 분석 창에는 인증된 프로그래밍 이벤트와 인증되지 않은 프로그래밍 이벤트가 모두 표시됩니다.

### <a name="accessing-programming-details-and-changes"></a>프로그래밍 세부 정보 및 변경 내용에 액세스

다음에서 프로그래밍 분석 창에 액세스합니다.

- [이벤트 타임라인](#event-timeline)

- [인증되지 않은 프로그래밍 경고](#unauthorized-programming-alerts)

### <a name="event-timeline"></a>이벤트 타임라인

이벤트 타임라인을 사용하여 프로그래밍 변경이 검색된 이벤트의 타임라인을 표시합니다.

:::image type="content" source="media/how-to-work-with-maps/timeline.png" alt-text="이벤트 타임라인의 보기.":::

### <a name="unauthorized-programming-alerts"></a>인증되지 않은 프로그래밍 경고

인증되지 않은 프로그래밍 디바이스가 프로그래밍 활동을 수행하면 경고가 트리거됩니다.

:::image type="content" source="media/how-to-work-with-maps/unauthorized.png" alt-text="인증되지 않은 프로그래밍 경고":::

> [!NOTE]
> 디바이스 속성 창과 디바이스 인벤토리에서 기본 프로그래밍 정보를 볼 수도 있습니다.

### <a name="working-in-the-programming-timeline-window"></a>프로그래밍 타임라인 창에서 작업

이 섹션에서는 프로그래밍 파일을 보고 버전을 비교하는 방법을 설명합니다. 프로그래밍된 디바이스로 전송되는 특정 파일을 검색합니다. 파일 검색 기준:

  - Date

  - 파일 형식

:::image type="content" source="media/how-to-work-with-maps/timeline-view.png" alt-text="프로그래밍 타임라인 창":::

|프로그래밍 타임라인 창 | Description |
|--|--|
| 프로그래밍된 디바이스 | 호스트 이름과 파일을 비롯하여 프로그래밍된 디바이스에 대한 세부 정보를 제공합니다. |
| 최근 이벤트 | 센서에서 검색된 가장 최근 이벤트 50개를 표시합니다. <br />이벤트를 강조 표시하려면 해당 이벤트를 커서로 가리키고 별을 클릭합니다. :::image type="icon" source="media/how-to-work-with-maps/star.png" border="false"::: <br /> 최근 50개의 이벤트를 볼 수 있습니다. |
| 파일 | 프로그래밍된 디바이스에서 선택한 날짜 및 파일 크기로 검색된 파일을 표시합니다. <br /> 기본적으로 디바이스당 표시할 수 있는 파일의 최대 수는 300개입니다. <br /> 기본적으로 각 파일의 최대 파일 크기는 15MB입니다. |
| 파일 상태 :::image type="icon" source="media/how-to-work-with-maps/status-v2.png" border="false"::: | 파일 레이블은 다음을 비롯하여 디바이스에 있는 파일의 상태를 표시합니다. <br /> **추가됨**: 선택한 날짜 또는 시간에 파일이 엔드포인트에 추가되었습니다. <br /> **업데이트됨**: 선택한 날짜 또는 시간에 파일이 업데이트되었습니다. <br /> **삭제됨**: 이 파일이 제거되었습니다. <br /> **레이블 없음**: 파일이 변경되지 않았습니다.   |
| 프로그래밍 디바이스 | 프로그래밍을 변경한 디바이스입니다. 프로그래밍된 디바이스 하나에서 여러 디바이스가 프로그래밍 변경을 수행했을 수 있습니다. 호스트 이름, 날짜 또는 변경 시간 및 로그인한 사용자가 표시됩니다. |
| :::image type="icon" source="media/how-to-work-with-maps/current.png" border="false"::: | 프로그래밍된 디바이스에 설치된 현재 파일을 표시합니다. |
| :::image type="icon" source="media/how-to-work-with-maps/download-text.png" border="false"::: | 표시된 코드의 텍스트 파일을 다운로드합니다. |
| :::image type="icon" source="media/how-to-work-with-maps/compare.png" border="false"::: | 선택한 날짜에 검색된 파일과 현재 파일을 비교합니다. |

### <a name="choose-a-file-to-review"></a>검토할 파일 선택

이 섹션에서는 검토할 파일을 선택하는 방법을 설명합니다.

검토할 파일을 선택하려면:

1. **최근 이벤트** 창에서 이벤트를 선택합니다.

2. 파일 창에서 파일을 선택합니다. 파일이 현재 창에 표시됩니다.

:::image type="content" source="media/how-to-work-with-maps/choose-file.png" alt-text="작업할 파일을 선택합니다.":::

### <a name="compare-files"></a>파일 비교

이 섹션에서는 프로그래밍 파일을 비교하는 방법을 설명합니다.

비교하려면:

1. 최근 이벤트 창에서 이벤트를 선택합니다.

2. 파일 창에서 파일을 선택합니다. 파일이 현재 창에 표시됩니다. 이 파일을 다른 파일과 비교할 수 있습니다.

3. 비교 표시기를 선택합니다.

   :::image type="content" source="media/how-to-work-with-maps/compare.png" alt-text="비교 표시기":::

   선택한 파일이 프로그래밍된 디바이스에서 검색된 모든 날짜가 창에 표시됩니다. 파일이 프로그래밍 디바이스에서 여러 프로그래밍 디바이스에 의해 업데이트되었을 수 있습니다.

   검색된 차이점 수가 창의 오른쪽 위 모서리에 표시됩니다. 차이점을 보려면 아래로 스크롤해야 할 수도 있습니다.

   :::image type="content" source="media/how-to-work-with-maps/scroll.png" alt-text="선택 영역까지 아래로 스크롤":::

   이 숫자는 변경된 텍스트의 인접한 줄로 계산됩니다. 예를 들어 연속하는 8개의 코드 줄이 변경(삭제, 업데이트 또는 추가)된 경우 이는 차이점 하나로 계산됩니다.

   :::image type="content" source="media/how-to-work-with-maps/program-timeline.png" alt-text="프로그래밍 타임라인 보기.":::

4. 날짜를 선택합니다. 선택한 날짜에 검색된 파일이 창에 표시됩니다.

5. 최근 이벤트/파일 창에서 선택한 파일은 항상 오른쪽에 표시됩니다.

### <a name="device-programming-information-other-locations"></a>디바이스 프로그래밍 정보: 다른 위치

프로그래밍 타임라인에서 세부 정보를 검토하는 것 외에도 디바이스 속성 창과 디바이스 인벤토리에서 프로그래밍 정보에 액세스할 수 있습니다.

| 디바이스 유형 | Description |
|--|--|
| 디바이스 속성 | 디바이스 속성 창은 디바이스에서 마지막으로 검색된 프로그래밍 이벤트에 대한 정보를 제공합니다. :::image type="content" source="media/how-to-work-with-maps/information-from-device-v2.png" alt-text="디바이스의 속성"::: |
| 디바이스 인벤토리 | 디바이스 인벤토리는 디바이스가 프로그래밍 디바이스인지 여부를 나타냅니다. :::image type="content" source="media/how-to-work-with-maps/inventory-v2.png" alt-text="디바이스의 인벤토리"::: |

## <a name="manage-device-information-from-the-map"></a>맵에서 디바이스 정보 관리

센서는 네트워크에서 직접 디바이스를 업데이트하거나 영향을 주지 않습니다. 여기에서 수행한 변경은 디바이스를 분석하는 방법에만 영향을 줍니다.

### <a name="delete-devices"></a>디바이스 삭제

학습된 정보가 관련이 없는 경우 디바이스를 삭제할 수 있습니다. 예제:

  - 엔지니어링 워크스테이션의 파트너 계약자가 구성 업데이트를 수행하기 위해 임시로 연결합니다. 작업이 완료되면 디바이스가 제거됩니다.

  - 네트워크의 변경으로 인해 일부 디바이스는 더 이상 연결되지 않습니다.

디바이스를 삭제하지 않으면 센서가 계속 디바이스를 모니터링하게 됩니다. 60일 후에 삭제를 권장하는 알림이 표시됩니다.

다른 디바이스에서 액세스하려고 하는 경우 디바이스가 응답하지 않음을 나타내는 경고가 표시될 수 있습니다. 이 경우 네트워크가 잘못 구성되었을 수 있습니다.

디바이스는 디바이스 맵, 디바이스 인벤토리 및 데이터 마이닝 보고서에서 제거됩니다. 기타 정보(예: 위젯에 저장된 정보)는 유지됩니다.

디바이스를 삭제하려면 디바이스가 10분 이상 비활성 상태여야 합니다.

디바이스 맵에서 디바이스를 삭제하려면:

1. 측면 메뉴에서 **디바이스** 를 선택합니다.

2. 디바이스를 마우스 오른쪽 단추로 클릭하고 **삭제** 를 선택합니다.

### <a name="merge-devices"></a>디바이스 병합

특정 상황에서는 디바이스를 병합해야 할 수 있습니다. 하나의 고유한 디바이스에 연결된 별도의 네트워크 엔터티를 센서가 검색한 경우에 병합이 필요할 수 있습니다. 예제:

  - 4개의 네트워크 카드가 있는 PLC

  - WiFi 및 실제 카드가 있는 노트북
  
  - 2개 이상의 네트워크 카드가 있는 워크스테이션

병합할 때 두 디바이스의 디바이스 속성을 하나로 결합하도록 센서에 지시합니다. 이렇게 하면 디바이스 속성 창과 센서 보고서가 새 디바이스 속성 세부 정보로 업데이트됩니다.

예를 들어 각각 IP 주소가 있는 두 개의 디바이스를 병합하면 두 IP 주소 모두 디바이스 속성 창에 별도의 인터페이스로 표시됩니다. 인증된 디바이스만 병합할 수 있습니다.

:::image type="content" source="media/how-to-work-with-maps/device-properties-v2.png" alt-text="디바이스 속성 창":::

이벤트 타임라인은 병합 이벤트를 표시합니다.

:::image type="content" source="media/how-to-work-with-maps/events-time.png" alt-text="병합된 이벤트가 있는 이벤트 타임라인":::

디바이스 병합은 실행 취소할 수 없습니다. 실수로 두 디바이스를 병합한 경우 디바이스를 삭제하고 센서가 두 디바이스를 다시 검색할 때까지 기다립니다.

디바이스를 병합하려면:

1. 두 개의 디바이스를 선택하고(shift 클릭) 그중 하나를 마우스 오른쪽 단추로 클릭합니다.

2. **병합** 을 선택하여 디바이스를 병합합니다. 병합을 완료하는 데 최대 2분이 걸릴 수 있습니다.

3. 병합 디바이스 특성 설정 대화 상자에서 디바이스 이름을 선택합니다.

   :::image type="content" source="media/how-to-work-with-maps/name-the-device-v2.png" alt-text="특성 대화 상자":::

4. **저장** 을 선택합니다.

### <a name="authorize-and-unauthorize-devices"></a>디바이스 인증 및 인증 취소

학습 기간 중에 네트워크에서 검색된 모든 디바이스는 인증된 디바이스로 식별됩니다. **인증됨** 레이블은 디바이스 맵에서 이러한 디바이스에 표시되지 않습니다.

학습 기간 후에 검색되는 디바이스는 인증되지 않은 디바이스로 표시됩니다. 맵에서는 물론 디바이스 인벤토리에서도 인증되지 않은 디바이스를 볼 수 있습니다.

:::image type="content" source="media/how-to-work-with-maps/inventory-icon.png" alt-text="디바이스 인벤토리":::

**새 디바이스와 인증되지 않은 디바이스 비교**

학습 기간 후 검색된 새 디바이스는 `New` 및 `Unauthorized` 레이블과 함께 표시됩니다.

맵에서 디바이스를 이동하거나 디바이스 속성을 수동으로 변경하는 경우 `New` 레이블이 디바이스 아이콘에서 제거 됩니다.

#### <a name="unauthorized-devices---attack-vectors-and-risk-assessment-reports"></a>인증되지 않은 디바이스 - 공격 벡터 및 위험 평가 보고서

인증되지 않은 디바이스는 위험 평가 보고서 및 공격 벡터 보고서에 포함됩니다.

- **공격 벡터 보고서:** 인증되지 않음으로 표시된 디바이스는 네트워크에 위협이 될 수 있는 의심스러운 악성 디바이스로 공격 벡터에서 확인됩니다.

   :::image type="content" source="media/how-to-work-with-maps/attack-vector-reports.png" alt-text="공격 벡터 보고서 보기":::

- **위험 평가 보고서:** 인증되지 않음으로 표시되는 디바이스의 경우

  - 위험 평가 보고서에서 식별됩니다

디바이스를 수동으로 인증하거나 인증 취소하려면:

1. 맵에서 디바이스를 마우스 오른쪽 단추로 클릭하고 **인증 취소** 를 선택합니다.

### <a name="mark-devices-as-important"></a>디바이스를 중요함으로 표시

중요 비즈니스용 서버와 같은 중요한 네트워크 디바이스를 중요함으로 표시할 수 있습니다. 이러한 디바이스는 맵에서 별로 표시됩니다. 별은 맵의 확대/축소 수준에 따라 달라집니다.

:::image type="icon" source="media/how-to-work-with-maps/star-one.png" border="false"::: :::image type="icon" source="media/how-to-work-with-maps/star-two.png" border="false"::: :::image type="icon" source="media/how-to-work-with-maps/star-3.png" border="false":::

### <a name="important-devices---attack-vectors-and-risk-assessment-reports"></a>중요한 디바이스 - 공격 벡터 및 위험 평가 보고서

중요한 디바이스는 위험 평가 보고서 및 공격 벡터 보고서를 생성할 때 계산됩니다.

  - 공격 벡터 보고서: 중요한 것으로 표시된 디바이스는 공격 벡터에서 공격 대상으로 확인됩니다. 

  - 위험 평가 보고서: 중요한 것으로 표시된 디바이스는 위험 평가 보고서에서 보안 점수를 제공할 때 계산됩니다.

## <a name="generate-activity-reports-from-the-map"></a>맵에서 활동 보고서 생성

1, 6, 12 또는 24시간 동안 선택한 디바이스에 대한 활동 보고서를 생성합니다. 다음과 같은 정보가 제공됩니다.

  - 범주: 트래픽 시나리오를 기반으로 하는 기본 검색 정보

  - 원본 및 대상 디바이스

  - 데이터: 검색된 추가 정보

  - 마지막으로 발견된 시간 및 날짜

Microsoft Excel 또는 Word 파일로 보고서를 저장할 수 있습니다.

:::image type="content" source="media/how-to-work-with-maps/historical-information.png" alt-text="최근 활동":::

디바이스의 활동 보고서를 생성하려면:

1. 맵에서 디바이스를 마우스 오른쪽 단추로 클릭합니다.

2. 활동 보고서를 선택합니다.

   :::image type="content" source="media/how-to-work-with-maps/activity-report.png" alt-text="활동 보고서를 봅니다.":::

## <a name="generate-attack-vector-reports-from-the-map"></a>맵에서 공격 벡터 보고서 생성

공격 벡터 보고서를 시뮬레이트하여 맵에서 선택한 디바이스가 취약한 공격 대상인지 알아보세요.

공격 벡터 보고서는 악용 가능한 디바이스의 취약성 체인을 그래픽으로 표시합니다. 이러한 취약성으로 인해 주요 네트워크 디바이스에 공격자가 액세스할 수 있습니다. 공격 벡터 시뮬레이터는 공격 벡터를 실시간으로 계산하고 특정 대상의 모든 공격 벡터를 분석합니다.

공격 벡터 보고서에서 디바이스를 보려면:

1. 맵에서 디바이스를 마우스 오른쪽 단추로 클릭합니다.

2. **공격 벡터 시뮬레이트** 를 선택합니다. 공격 대상으로 선택한 디바이스와 함께 공격 벡터 대화 상자가 열립니다.

   :::image type="content" source="media/how-to-work-with-maps/simulation.png" alt-text="공격 벡터 시뮬레이션 추가":::

3. 대화 상자에 나머지 매개 변수를 추가하고 **시뮬레이션 추가** 를 선택합니다.

## <a name="export-device-information-from-the-map"></a>맵에서 디바이스 정보 내보내기

맵에서 다음 디바이스 정보를 내보냅니다.

  - 디바이스 세부 정보(Microsoft Excel)

  - 디바이스 요약(Microsoft Excel)

  - 그룹이 포함된 Word 파일(Microsoft Word)

내보내려면:

1. 맵에서 내보내기 아이콘을 선택합니다.

1. 내보내기 옵션을 선택합니다.

## <a name="see-also"></a>참고 항목

[디바이스 인벤토리에서 센서 검색 조사](how-to-investigate-sensor-detections-in-a-device-inventory.md)

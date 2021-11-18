---
title: Azure Communication Services Insights 미리 보기
titleSuffix: An Azure Communication Services concept document
description: 통합 문서를 통해 Communications Services에 사용할 수 있는 데이터 시각화에 대한 설명
author: timmitchell
services: azure-communication-services
ms.author: timmitchell
ms.date: 10/25/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: data
ms.openlocfilehash: bfe0d0a6fbc47ff1cdce238d6c30c3f50c118bdd
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2021
ms.locfileid: "132716546"
---
# <a name="communications-services-insights-preview"></a>Communications Services Insights 미리 보기

## <a name="overview"></a>개요
Communications Resource 내에서 Communications Services에 대해 모니터링되는 Azure Monitor 로그 및 메트릭의 인사이트를 전달하는 다양한 데이터 시각화를 표시하는 Insights **미리 보기** 기능을 제공했습니다. Insights 내의 시각화는 Azure Monitor 통합 문서를 통해 [가능합니다.](../../../azure-monitor/visualize/workbooks-overview.md) 통합 문서를 활용하려면 [진단 설정 Azure Monitor 사용](enable-logging.md)에 설명된 지침을 따르고 통합 문서를 사용하도록 설정하려면 Log [Analytics 작업 영역](../../../azure-monitor/logs/log-analytics-overview.md) 대상으로 로그를 보내야 합니다. 

:::image type="content" source="media\workbooks\insights-overview-2.png" alt-text="Communication Services Insights":::

## <a name="accessing-azure-insights-for-communication-services"></a>Communication Services 대한 Azure Insights 액세스

1. Azure **Portal** 홈페이지에서 **Communication Service** 리소스를 선택합니다.

    :::image type="content" source="media\workbooks\azure-portal-home-browser.png" alt-text="Azure Portal 홈":::

2. 리소스 내부에 있으면 왼쪽 탐색 모음에서 **모니터** 범주까지 아래로 스크롤하고 **Insights** 탭을 클릭합니다.

    :::image type="content" source="media\workbooks\acs-insights-nav.png" alt-text="Insights 탐색":::

3. 그러면 Communication Service 리소스에 대한 **Insights** 대시보드가 표시됩니다.

    :::image type="content" source="media\workbooks\acs-insights-tab.png" alt-text="Communication Services Insights 탭":::

## <a name="insights-dashboard-navigation"></a>대시보드 탐색 Insights

Communication Service Insights 대시보드는 사용자에게 리소스의 로그 데이터를 탐색하는 직관적이고 명확한 방법을 제공합니다. **개요** 섹션에서는 모든 형식에 대한 보기를 제공하므로 사용자는 특정 시간 범위에서 리소스가 사용된 다양한 방법을 볼 수 있습니다.

:::image type="content" source="media\workbooks\overview.png" alt-text="인사이트 개요":::

사용자는 맨 위에 표시된 매개 변수를 사용하여 표시할 시간 범위 및 시간 세분성을 제어할 수 있습니다.

:::image type="content" source="media\workbooks\time-range-param.png" alt-text="시간 범위 매개 변수":::

이러한 매개 변수는 전역 매개 변수입니다. 즉, 전체 대시보드에 표시되는 데이터를 업데이트합니다.

**개요** 섹션에는 표시되는 시각화 유형을 제어하는 추가 매개 변수가 포함되어 있습니다.

:::image type="content" source="media\workbooks\plot-type-param.png" alt-text="플롯 형식 매개 변수":::

이 매개 변수는 로컬입니다. 즉, 이 섹션의 플롯에만 영향을 미칩니다.

탭의 나머지 부분에는 특정 형식과 관련된 로그 데이터가 표시됩니다.

:::image type="content" source="media\workbooks\main-nav.png" alt-text="기본 탐색":::

**음성 및 비디오** 로그는 본질적으로 가장 복잡하기 때문에 이 형식은 다음 네 가지 하위 섹션으로 세분화됩니다.

:::image type="content" source="media\workbooks\voice-and-video-nav.png" alt-text="음성 및 비디오 탐색":::

**요약** 탭에는 공유된 미디어 스트림 유형, 통화에 참여하는 엔드포인트 유형(예: VoIP, Bot, Application, PSTN 또는 Server), OS 사용량 및 참가자 종료 이유를 포함하여 음성 및 비디오 사용에 대한 일반 정보가 포함됩니다.

:::image type="content" source="media\workbooks\voice-and-video-summary.png" alt-text="음성 및 비디오 요약":::

**음성 및 비디오** 형식 아래의 **볼륨** 탭에는 시간 bin(시간 **세분성** 매개 변수)으로 세분화된 특정 기간(시간 **범위** 매개 변수)의 통화 수와 참가자 수가 표시됩니다.

:::image type="content" source="media\workbooks\voice-and-video-volume.png" alt-text="음성 및 비디오 볼륨":::

**볼륨** 탭에는 호출 유형(P2P 및 그룹 호출) 및 Interop 호출(ACS(순수 Azure Communication Services)과 Teams Interop 중 하나를 통해 분할된 호출 및 참가자 수를 시각화하는 데 도움이 되는 **Grouping** 매개 변수가 포함되어 있습니다.

:::image type="content" source="media\workbooks\voice-and-video-volume-grouping.png" alt-text="음성 및 비디오 볼륨 그룹화":::

음성 및 **비디오** 아래의 **품질** 탭에서는 사용자가 통화의 품질 분포를 검사할 수 있습니다. 여기서 품질은 이 대시보드의 세 가지 수준에서 정의됩니다.

- 품질이 낮은 미디어 **스트림(스트림 품질** 플롯)의 비율입니다. 여기서 스트림의 품질은 비정상 범위가 다음과 같이 정의된 비정상 원격 분석 값이 하나 이상 있는 경우 불량으로 분류됩니다.
  - 지터 > 30밀리초
  - 패킷 손실률 > 10%
  - 왕복 시간 > 500밀리초

- **영향을 받은 호출의** 비율은 영향을 받은 호출이 하나 이상의 품질이 낮은 스트림이 있는 호출로 정의됩니다.

- **참가자는 참가자가** 전화를 끊은 이유를 추적하는 이유를 종료합니다. 최종 이유는 신호 요청의 특정 상태를 설명하는 숫자 코드인 [SIP 코드입니다.](https://en.wikipedia.org/wiki/List_of_SIP_response_codes) SIP 코드는 *성공,* *클라이언트 오류,* 서버 오류, *전역* *오류,* *리디렉션* 및 임시 의 6가지 범주로 그룹화할 수 *있습니다.* SIP 코드 범주의 분포는 왼쪽의 원형 차트에 표시되고, 참가자 종료 이유에 대한 특정 SIP 코드 목록은 오른쪽에 제공됩니다.

:::image type="content" source="media\workbooks\voice-and-video-quality.png" alt-text="음성 및 비디오 품질":::

또한 통화에 사용되는 미디어 스트림 **유형(미디어 형식** 매개 변수)을 기준으로 품질을 필터링할 수 있습니다. 예를 들어 비디오 스트림 품질 측면에서만 영향을 받는 호출을 얻을 수 있습니다.

:::image type="content" source="media\workbooks\voice-and-video-quality-params.png" alt-text="음성 및 비디오 품질 미디어 유형 매개 변수":::

또한 엔드포인트 유형(**엔드포인트 형식** 매개 변수)을 기준으로 필터링할 수도 있습니다( 예: PSTN 참가자에 대한 참가자 종료 이유 얻기). 이러한 필터를 사용하면 여러 항목을 선택할 수 있습니다.

:::image type="content" source="media\workbooks\voice-and-video-params-2.png" alt-text="음성 및 비디오 품질 엔드포인트 유형 매개 변수":::

**세부 정보** 탭은 날짜별로 호출을 그룹화하고 해당 통화의 **참가자와** 참가자당 나가는 스트림을 기준으로 수행된 모든 호출의 세부 정보를 시간 범위에서 수행된 음성 및 영상 통화와 기간에 대한 시간 및 원격 분석 값으로 빠르게 탐색할 수 있는 방법을 제공합니다.

:::image type="content" source="media\workbooks\voice-and-video-details.png" alt-text="음성 및 비디오 세부 정보":::

호출의 세부 정보는 처음에 숨겨집니다. 전화를 클릭하면 참가자 목록이 표시됩니다.

:::image type="content" source="media\workbooks\voice-and-video-details-participants.png" alt-text="음성 및 비디오 참가자 세부 정보":::

참가자를 클릭하면 해당 참가자의 나가는 스트림 목록이 해당 기간(전체 호출 기간에 비례) 및 원격 분석 값과 함께 표시됩니다. 여기서 비정상 값은 빨간색으로 표시됩니다.

:::image type="content" source="media\workbooks\voice-and-video-details-streams.png" alt-text="음성 및 비디오 스트림 세부 정보":::

**인증** 탭에는 액세스 토큰 발급 또는 ID 만들기와 같은 작업을 통해 생성되는 인증 로그가 표시됩니다. 표시되는 데이터에는 수행되는 작업 유형 및 해당 작업의 결과가 포함됩니다.

:::image type="content" source="media\workbooks\auth.png" alt-text="인증 탭":::

**채팅** 탭에는 모든 채팅 관련 작업 및 해당 결과 유형에 대한 데이터가 표시됩니다.

:::image type="content" source="media\workbooks\chat.png" alt-text="채팅 탭":::

**SMS** 탭에는 ACS 리소스를 통한 SMS 사용에 대한 작업 및 결과가 표시됩니다(현재 이 형식에 대한 데이터가 없습니다).

:::image type="content" source="media\workbooks\sms.png" alt-text="SMS 탭":::

## <a name="editing-dashboards"></a>대시보드 편집

**Communication Service** 리소스와 함께 제공되는 **Insights** 대시보드는 위쪽 탐색 모음에서 **편집** 단추를 클릭하여 사용자 지정할 수 있습니다.

:::image type="content" source="media\workbooks\dashboard-editing.png" alt-text="대시보드 편집":::

이러한 대시보드를 편집하면 **Insights** 탭이 수정되지 않고 리소스의 통합 문서 탭에서 액세스할 수 있는 별도의 통합 문서가 만들어집니다.

:::image type="content" source="media\workbooks\workbooks-tab.png" alt-text="통합 문서 탭":::

통합 문서에 대한 자세한 설명은 Azure Monitor 통합 [문서](../../../azure-monitor/visualize/workbooks-overview.md) 설명서를 참조하세요.
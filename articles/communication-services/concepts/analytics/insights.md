---
title: Azure Communication Services Insights 미리 보기
titleSuffix: An Azure Communication Services concept document
description: 통합 문서를 통해 통신 서비스에 사용할 수 있는 데이터 시각화 설명
author: timmitchell
services: azure-communication-services
ms.author: timmitchell
ms.date: 10/25/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: data
ms.openlocfilehash: 1a11cc68d197d53ab2348c7b6ae625c6659a6884
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/10/2021
ms.locfileid: "132139856"
---
# <a name="communications-services-insights-preview"></a>통신 서비스 Insights 미리 보기

## <a name="overview"></a>개요
통신 리소스 내에서 다양 한 데이터 시각화를 표시 하는 **Insights 미리 보기** 기능을 제공 하 여 통신 서비스에 대해 모니터링 되는 Azure Monitor 로그 및 메트릭에 대 한 정보를 전달 합니다. Insights 내의 시각화는 [Azure Monitor 통합 문서](/azure/azure-monitor/visualize/workbooks-overview)를 통해 수행할 수 있습니다. 통합 문서를 활용 하려면 [진단 설정에서 Azure Monitor 사용](enable-logging.md)에 설명 된 지침에 따라 통합 문서를 사용 하도록 설정 하려면 [Log Analytics 작업 영역](/azure/azure-monitor/logs/log-analytics-overview) 대상으로 로그를 보내야 합니다. 

:::image type="content" source="media\workbooks\insights-overview-2.png" alt-text="통신 서비스 Insights":::

## <a name="accessing-azure-insights-for-communication-services"></a>통신 서비스용 Azure Insights 액세스

1. **Azure Portal** 홈페이지에서 **통신 서비스** 리소스를 선택 합니다.

    :::image type="content" source="media\workbooks\azure-portal-home-browser.png" alt-text="Azure 포털 홈":::

2. 리소스 내부에 있는 경우 왼쪽 탐색 모음에서 **모니터** 범주로 아래로 스크롤하고 **Insights** 탭을 클릭 합니다.

    :::image type="content" source="media\workbooks\acs-insights-nav.png" alt-text="Insights 탐색":::

3. 통신 서비스 리소스에 대 한 **Insights** 대시보드가 표시 됩니다.

    :::image type="content" source="media\workbooks\acs-insights-tab.png" alt-text="통신 서비스 Insights 탭":::

## <a name="insights-dashboard-navigation"></a>Insights 대시보드 탐색

통신 서비스 Insights 대시보드는 사용자에 게 리소스의 로그 데이터를 탐색 하는 직관적이 고 명확한 방법을 제공 합니다. **개요** 섹션에서는 모든 소프트웨어나에 대 한 보기를 제공 하므로 사용자는 특정 시간 범위에서 리소스를 사용 하는 다양 한 방법을 볼 수 있습니다.

:::image type="content" source="media\workbooks\overview.png" alt-text="인사이트 개요":::

사용자는 맨 위에 표시 되는 매개 변수를 사용 하 여 표시할 시간 범위 및 시간 세분성을 제어할 수 있습니다.

:::image type="content" source="media\workbooks\time-range-param.png" alt-text="시간 범위 매개 변수":::

이러한 매개 변수는 전역적 이므로 전체 대시보드에 표시 되는 데이터를 업데이트 합니다.

**개요** 섹션에는 표시 되는 시각화 유형을 제어 하는 추가 매개 변수가 포함 되어 있습니다.

:::image type="content" source="media\workbooks\plot-type-param.png" alt-text="플롯 유형 매개 변수":::

이 매개 변수는 로컬입니다. 즉,이 섹션의 플롯에만 영향을 줍니다.

나머지 탭은 특정 모달에 관련 된 로그 데이터를 표시 합니다.

:::image type="content" source="media\workbooks\main-nav.png" alt-text="주 탐색":::

**음성 및 비디오** 로그는 기본적으로 가장 복잡 하므로이 모달은 네 개의 하위 섹션으로 분류 됩니다.

:::image type="content" source="media\workbooks\voice-and-video-nav.png" alt-text="음성 및 비디오 탐색":::

**요약** 탭에는 공유 된 미디어 스트림 유형, 호출에 참여 하는 끝점 유형 (예: VoIP, 봇, 응용 프로그램, PSTN 또는 서버), OS 사용 및 참가자 최종 이유를 비롯 하 여 음성 및 비디오 사용에 대 한 일반 정보가 포함 되어 있습니다.

:::image type="content" source="media\workbooks\voice-and-video-summary.png" alt-text="음성 및 비디오 요약":::

**음성 및 비디오** 에 표시 되는 **볼륨** 탭에는 특정 기간 (**시간 범위** 매개 변수)의 호출 수와 참가자 수가 표시 되 고 시간 bin (**시간 세분성** 매개 변수)으로 세분화 됩니다.

:::image type="content" source="media\workbooks\voice-and-video-volume.png" alt-text="음성 및 비디오 볼륨":::

**볼륨** 탭에는 호출 형식 (P2P와 그룹 호출) 및 interop 호출 (순수 Azure Communication Services (ACS) 및 Teams interop)에 의해 분할 된 호출 및 참가자 수를 시각화 하는 데 도움이 되는 **Grouping** 매개 변수가 포함 되어 있습니다.

:::image type="content" source="media\workbooks\voice-and-video-volume-grouping.png" alt-text="음성 및 비디오 볼륨 그룹화":::

**음성 및 비디오** 의 **품질** 탭에서는 사용자가 호출의 품질 분포를 검사할 수 있습니다. 여기서 품질은이 대시보드에 대 한 세 가지 수준으로 정의 됩니다.

- 품질이 낮은 미디어 스트림 (**스트림 품질** 플롯)의 비율입니다 .이 경우 비정상 범위는 하나 이상의 비정상 원격 분석 값이 있을 때 스트림의 품질이 저하 된 것으로 분류 됩니다. 여기서 비정상 범위는 다음과 같이 정의 됩니다.
  - 지터 > 30 밀리초
  - 패킷 손실 비율 > 10%
  - 왕복 시간 > 500 밀리초

- 영향을 받는 **호출의 비율** 입니다 .이 경우 영향을 받는 호출이 하나 이상의 잘못 된 품질 스트림을 포함 하는 호출로 정의 됩니다.

- 참가자가 전화를 떠난 이유를 추적 하는 **참가자의 종료 이유** 입니다. 최종 이유는 신호 요청의 특정 상태를 설명 하는 숫자 코드 인 [SIP 코드](https://en.wikipedia.org/wiki/List_of_SIP_response_codes)입니다. SIP 코드는 *성공*, *클라이언트 오류*, *서버 오류*, *전역 오류*, *리디렉션* 및 *Provisional* 의 6 가지 범주로 그룹화 할 수 있습니다. SIP 코드 범주의 분포는 왼쪽에 있는 원형 차트에 표시 되 고, 참가자 최종 이유에 대 한 특정 SIP 코드의 목록이 오른쪽에 제공 됩니다.

:::image type="content" source="media\workbooks\voice-and-video-quality.png" alt-text="음성 및 비디오 품질":::

또한 품질은 호출에 사용 되는 미디어 스트림 유형 (**미디어 유형** 매개 변수)을 기준으로 필터링 할 수 있습니다. 예를 들어 비디오 스트림 품질을 기준으로 영향을 받는 호출만 가져올 수 있습니다.

:::image type="content" source="media\workbooks\voice-and-video-quality-params.png" alt-text="음성 및 비디오 품질 미디어 유형 매개 변수":::

또한 및은 끝점 형식 (**끝점 형식** 매개 변수)으로 필터링 될 수 있습니다. 예를 들어, PSTN 참가자에 대 한 참가자 종료 이유를 가져올 수 있습니다. 이러한 필터는 여러 항목을 선택할 수 있습니다.

:::image type="content" source="media\workbooks\voice-and-video-params-2.png" alt-text="음성 및 비디오 품질 끝점 형식 매개 변수":::

**세부 정보** 탭에서는 날짜를 기준으로 통화를 그룹화 하 여 시간 범위에서 수행한 **음성 및 비디오** 호출을 신속 하 게 탐색 하 고, 해당 호출의 참가자와 관련 된 모든 호출에 대 한 세부 정보를 표시 하 고 해당 호출에 대 한 기간 및 원격 분석 값과 참가자 당 나가는 스트림을 확인할 수 있습니다.

:::image type="content" source="media\workbooks\voice-and-video-details.png" alt-text="음성 및 비디오 세부 정보":::

호출의 세부 정보는 처음에 숨겨집니다. 호출을 클릭 하면 참가자 목록이 표시 됩니다.

:::image type="content" source="media\workbooks\voice-and-video-details-participants.png" alt-text="음성 및 비디오 참가자 세부 정보":::

참가자를 클릭 하면 해당 참가자에 대 한 나가는 스트림 목록이 전체 호출 기간에 비례하여 해당 참가자의 시간 및 원격 분석 값 (비정상 값이 빨간색으로 표시 됨)과 함께 표시 됩니다.

:::image type="content" source="media\workbooks\voice-and-video-details-streams.png" alt-text="음성 및 비디오 스트림 세부 정보":::

**인증** 탭에는 액세스 토큰을 발급 하거나 id를 만드는 등의 작업을 통해 생성 되는 인증 로그가 표시 됩니다. 표시 되는 데이터에는 수행 되는 작업 유형 및 해당 작업의 결과가 포함 됩니다.

:::image type="content" source="media\workbooks\auth.png" alt-text="인증 탭":::

**채팅** 탭은 모든 채팅 관련 작업 및 해당 결과 유형에 대 한 데이터를 표시 합니다.

:::image type="content" source="media\workbooks\chat.png" alt-text="채팅 탭":::

**Sms** 탭에는 ACS 리소스를 통한 sms 사용에 대 한 작업 및 결과가 표시 됩니다 (현재이 모달에 대 한 데이터가 없음).

:::image type="content" source="media\workbooks\sms.png" alt-text="SMS 탭":::

## <a name="editing-dashboards"></a>대시보드 편집

위쪽 탐색 모음에서 **편집** 단추를 클릭 하 여 **통신 서비스** 리소스와 함께 제공 되는 **Insights** 대시보드를 사용자 지정할 수 있습니다.

:::image type="content" source="media\workbooks\dashboard-editing.png" alt-text="대시보드 편집":::

이러한 대시보드를 편집 해도 **Insights** 탭은 수정 되지 않고, 리소스의 통합 문서 탭에서 액세스할 수 있는 별도의 통합 문서를 만듭니다.

:::image type="content" source="media\workbooks\workbooks-tab.png" alt-text="통합 문서 탭":::

통합 문서에 대 한 자세한 설명은 [통합 문서 Azure Monitor](/azure/azure-monitor/visualize/workbooks-overview) 설명서를 참조 하세요.
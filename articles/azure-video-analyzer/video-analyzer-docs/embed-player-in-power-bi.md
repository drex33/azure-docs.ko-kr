---
title: Power BI에 플레이어 위젯 포함 - Azure Video Analyzer
description: 연속 비디오 녹화 또는 이벤트 기반 기록을 위해 Azure Video Analyzer를 사용할 수 있습니다. 이 문서에서는 Microsoft Power BI에 비디오를 포함하여 사용자에게 사용자 지정 가능한 UI를 제공하는 방법을 설명합니다.
ms.service: azure-video-analyzer
ms.topic: how-to
ms.date: 08/06/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: fb6014649e6fe71a2d2de5e4d7adb72ba9b66a37
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131012165"
---
# <a name="embed-player-widget-in-power-bi"></a>Power BI에 플레이어 위젯 포함


Azure Video Analyzer를 사용하면 비디오 및 연결된 유추 메타데이터를 Video Analyzer 클라우드 리소스에 [기록](detect-motion-record-video-clips-cloud.md)할 수 있습니다. Video Analyzer에는 쉽게 포함할 수 있는 [플레이어 위젯](player-widget.md)이 있으며, 이 위젯을 통해 클라이언트 앱은 비디오 및 유추 메타데이터를 재생할 수 있습니다.

대시보드를 통해 유용하게 비즈니스를 모니터링하고 가장 중요한 모든 메트릭을 한눈에 볼 수 있습니다. Power BI 대시보드는 IoT Hub의 원격 분석을 비롯하여 여러 데이터 원본과 비디오를 결합할 수 있는 강력한 도구입니다. 이 자습서에서는 [Microsoft Power BI](https://powerbi.microsoft.com/) 웹 서비스를 사용하여 하나 이상의 플레이어 위젯을 대시보드에 추가하는 방법을 알아봅니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/power-bi/embed-block-diagram.svg" alt-text="Microsoft Power BI Azure Video Analyzer 플레이어 위젯을 포함하도록 다이어그램 차단":::

## <a name="suggested-pre-reading"></a>추천 참고 자료

- Azure Video Analyzer [플레이어 위젯](player-widget.md)
- [Power BI 대시보드](/power-bi/create-reports/service-dashboards) 소개

## <a name="prerequisites"></a>필수 구성 요소

- 활성 구독이 있는 Azure 계정. 계정이 아직 없는 경우 [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [동작 감지 및 비디오 녹화](detect-motion-record-video-clips-cloud.md) 또는 [연속 비디오 녹화](continuous-video-recording.md) 완료 - 비디오 싱크가 있는 파이프라인이 필요합니다.

  > [!NOTE]
  > 계속하려면 Video Analyzer 계정에 최소 하나의 녹화된 비디오가 있어야 합니다. Azure Video Analyzer 계정 > 비디오 > Video Analyzer 섹션에 로그인하여 비디오 목록을 확인하세요.

- [Power BI](https://powerbi.microsoft.com/) 계정

## <a name="create-a-token"></a>토큰 만들기

1. 단계에 따라 [토큰을 만듭니다](access-policies.md#creating-a-token).
2. ‘발급자, 대상 그룹, 키 유형, 알고리즘, 키 ID, RSA 키 모듈러스, RSA 키 엔드포인트, 토큰’에 대해 생성된 값을 저장해야 합니다. 이러한 값은 아래 액세스 정책을 만들 때 필요합니다.

## <a name="get-embed-code-for-player-widget"></a>플레이어 위젯에 대한 embed 태그 가져오기

1. 자격 증명을 사용하여 [Azure Portal](https://portal.azure.com/)에 로그인합니다. 필수 조건을 완료하는 데 사용된 Video Analyzer 계정을 찾고 Video Analyzer 계정 창을 엽니다.
2. 단계에 따라 [액세스 정책을 만듭니다](access-policies.md#creating-an-access-policy).
3. **Video Analyzer** 목록에서 **비디오** 를 선택합니다.
4. 목록에서 원하는 비디오를 선택합니다.
5. **위젯** 설정을 클릭합니다. **Use widget in your application**(애플리케이션에서 위젯 사용) 창이 오른쪽에 열립니다. **Option 2 – using HTML**(옵션 2 - HTML 사용)까지 아래로 스크롤하고 코드를 복사하여 텍스트 편집기에 붙여넣습니다. **닫기** 단추를 클릭합니다.

   :::image type="content" source="./media/power-bi/widget-code.png" alt-text="AVA 포털에서 위젯 HTML 코드를 복사하고 나중을 위해 저장하는 스크린샷":::

6. 5단계에서 복사한 HTML 코드를 편집하여 다음에 대한 값을 바꿉니다.
   - 토큰 **AVA-API-JWT-TOKEN** - “토큰 만들기” 단계에서 저장한 토큰 값으로 바꿉니다. 이때 꺾쇠괄호는 제거해야 합니다.
   - 선택 사항 - 예를 들어 헤더를 “예제 플레이어 위젯”에서 “연속 비디오 녹화”로 변경하는 경우처럼 이 코드에서 다른 UI를 변경할 수 있습니다.

## <a name="add-widget-in-power-bi-dashboard"></a>Power BI 대시보드에서 위젯 추가

1. 브라우저에서 [Power BI 서비스](http://app.powerbi.com/)를 엽니다. 탐색 창에서 **내 작업 영역** 을 선택합니다.

   :::image type="content" source="./media/power-bi/powerbi-ws.png" alt-text="Power BI 작업 영역 홈페이지의 스크린샷":::

2. **새로 만들기** > **대시보드** 를 클릭하여 새 대시보드를 만들거나 기존 대시보드를 엽니다. **편집** 드롭다운 화살표를 선택한 다음, **타일 추가** 를 선택합니다. **웹 콘텐츠** > **다음** 을 선택합니다.
3. **웹 콘텐츠 타일 추가** 에서 이전 섹션의 **embed 태그** 를 입력합니다. **적용** 을 클릭합니다.

   :::image type="content" source="./media/power-bi/embed-code.png" alt-text="Power BI 대시보드 타일에 html 코드를 포함하는 스크린샷":::

4. 대시보드에 고정된 플레이어 위젯이 비디오에 표시됩니다.

   :::image type="content" source="./media/power-bi/one-player.png" alt-text="하나의 비디오 플레이어 위젯이 추가된 스크린샷":::

5. Azure Video Analyzer 비디오 섹션에서 더 많은 비디오를 추가하려면 이 섹션의 단계를 동일하게 수행합니다.

> [!NOTE]
> 동일한 Video Analyzer 계정에서 여러 비디오를 추가하려면 단일 액세스 정책 및 토큰 세트로 충분합니다.

다음은 단일 Power BI 대시보드에 고정된 여러 비디오의 샘플입니다.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/power-bi/two-players.png" alt-text="예제로 추가된 두 비디오 플레이어 위젯의 스크린샷":::

## <a name="next-steps"></a>다음 단계

- [Power BI AI 유추 이벤트의 실시간 시각화](visualize-ai-events-power-bi.md)
- [위젯 API](https://github.com/Azure/video-analyzer/tree/main/widgets)에 대해 자세히 알아보기

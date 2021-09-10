---
title: 문제 해결을 위한 브라우저 추적 캡처
description: 브라우저 추적에서 네트워크 정보를 캡처하여 Azure Portal 문제를 해결할 수 있습니다.
ms.date: 08/16/2021
ms.topic: troubleshooting
ms.openlocfilehash: d1db82c9671879c435a6dba73929d9a4eb183f7f
ms.sourcegitcommit: da9335cf42321b180757521e62c28f917f1b9a07
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/16/2021
ms.locfileid: "122538266"
---
# <a name="capture-a-browser-trace-for-troubleshooting"></a>문제 해결을 위한 브라우저 추적 캡처

Azure Portal 문제를 해결하고 Microsoft 지원에 문의해야 하는 경우 먼저 브라우저 추적 및 몇 가지 추가 정보를 캡처하는 것이 좋습니다. 수집하는 정보는 문제가 발생할 때 포털에 대한 중요한 세부 정보를 제공할 수 있습니다. 사용하는 브라우저의 개발자 도구에 대해 이 문서의 단계를 수행합니다. Google Chrome 또는 Microsoft Edge(Chromium), Microsoft Edge(EdgeHTML), Apple Safari 또는 Firefox.

> [!IMPORTANT]
> Microsoft 지원은 문제 해결을 위해서만 이러한 추적을 사용합니다. 환경에 관한 중요한 정보가 포함될 수 있으므로 누구와 추적을 공유할지 주의하세요.

## <a name="google-chrome-and-microsoft-edge-chromium"></a>Google Chrome 및 Microsoft Edge(Chromium)

Google Chrome 및 Microsoft Edge(Chromium)는 모두 [Chromium 오픈 소스 프로젝트](https://www.chromium.org/Home)를 기반으로 합니다. 다음 단계에서는 두 브라우저에서 매우 유사한 개발자 도구를 사용하는 방법을 보여 줍니다. 자세한 내용은 [Chrome DevTools](https://developers.google.com/web/tools/chrome-devtools) 및 [Microsoft Edge(Chromium) 개발자 도구](/microsoft-edge/devtools-guide-chromium)를 참조하세요.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 추적이 로그인과 관련된 중요한 정보를 포함하지 않도록 추적을 시작하기 _전에_ 로그인해야 합니다.

1. [단계 레코더](https://support.microsoft.com/help/22878/windows-10-record-steps)를 사용하여 포털에서 수행하는 단계를 기록하기 시작합니다.

1. 포털에서 문제가 발생하는 단계의 바로 앞 단계로 이동합니다.

1. F12를 누르거나, ![브라우저 설정 아이콘을 표시하는 스크린샷](media/capture-browser-trace/chromium-icon-settings.png)을 선택합니다. > **도구 더보기** > **개발자 도구** 를 선택합니다.

1. 기본적으로 브라우저는 현재 로드된 페이지에 대해서만 추적 정보를 유지합니다. 브라우저가 모든 추적 정보를 유지하도록 하려면 다음 옵션을 설정합니다. 재현하기 위해 두 개 이상의 페이지로 이동해야 하는 경우에도 마찬가지입니다.

    1. **네트워크** 탭을 선택한 다음 **로그 유지** 를 선택합니다.

          ![네트워크 탭의 로그 유지 옵션을 강조 표시하는 스크린샷.](media/capture-browser-trace/chromium-network-preserve-log.png)

    1. **콘솔** 탭을 선택하고 **콘솔 설정** 을 선택한 다음 **로그 유지** 를 선택합니다. **콘솔 설정** 을 다시 선택하여 설정 창을 닫습니다.

          ![콘솔 탭의 로그 유지 옵션을 강조 표시하는 스크린샷.](media/capture-browser-trace/chromium-console-preserve-log.png)

1. **네트워크** 탭을 선택한 다음 **네트워크 로그 기록 중지** 및 **지우기** 를 선택합니다.

    !["네트워크 로그 기록 중지" 및 "지우기"의 스크린샷](media/capture-browser-trace/chromium-stop-clear-session.png)

1. **네트워크 로그 기록** 을 선택한 다음 포털에서 문제를 재현합니다.

    ![네트워크 로그를 기록하는 방법을 보여 주는 스크린샷.](media/capture-browser-trace/chromium-start-session.png)

    다음 이미지와 비슷한 세션 출력이 표시됩니다.

    ![세션 출력을 보여 주는 스크린샷.](media/capture-browser-trace/chromium-browser-trace-results.png)

1. 예기치 않은 포털 동작을 재현한 후 **네트워크 로그 기록 중지** 를 선택하고 **HAR 내보내기** 를 선택한 다음 파일을 저장합니다.

    ![네트워크 탭에서 HAR를 내보내는 방법을 보여 주는 스크린샷.](media/capture-browser-trace/chromium-network-export-har.png)

1. 단계 레코더를 중지하고 기록을 저장합니다.

1. 브라우저 개발자 도구 창으로 돌아가서 **콘솔** 탭을 선택합니다. 메시지 중 하나를 마우스 오른쪽 단추로 클릭한 다음 **다른 이름으로 저장...** 을 선택하고 콘솔 출력을 텍스트 파일로 저장합니다.

    ![콘솔 탭 및 다른 이름으로 저장...메뉴를 강조 표시하는 스크린샷](media/capture-browser-trace/chromium-console-select.png)

1. HAR 파일, 콘솔 출력 및 화면 기록을 압축된 형식(예: .zip)으로 패키징하고 Microsoft 지원과 공유합니다.

## <a name="microsoft-edge-edgehtml"></a>Microsoft Edge(EdgeHTML)

다음 단계에서는 Microsoft Edge(EdgeHTML)에서 개발자 도구를 사용하는 방법을 보여 줍니다. 자세한 내용은 [Microsoft Edge(EdgeHTML) 개발자 도구](/microsoft-edge/devtools-guide)를 참조하세요.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 추적이 로그인과 관련된 중요한 정보를 포함하지 않도록 추적을 시작하기 _전에_ 로그인해야 합니다. 

1. [단계 레코더](https://support.microsoft.com/help/22878/windows-10-record-steps)를 사용하여 포털에서 수행하는 단계를 기록하기 시작합니다.

1. 포털에서 문제가 발생하는 단계의 바로 앞 단계로 이동합니다.

1. F12를 누르거나, ![브라우저 설정 아이콘 스크린샷](media/capture-browser-trace/edge-icon-settings.png)을 선택합니다. > **도구 더보기** > **개발자 도구** 를 선택합니다.

1. 기본적으로 브라우저는 현재 로드된 페이지에 대해서만 추적 정보를 유지합니다. 브라우저가 모든 추적 정보를 유지하도록 하려면 다음 옵션을 설정합니다. 재현하기 위해 두 개 이상의 페이지로 이동해야 하는 경우에도 마찬가지입니다.

    1. **네트워크** 탭을 선택한 다음 **탐색 시 항목 지우기** 옵션을 선택 취소합니다.

          !["탐색 시 항목 지우기"의 스크린샷](media/capture-browser-trace/edge-network-clear-entries.png)

    1. **콘솔** 탭을 선택한 다음 **로그 유지** 를 선택합니다.

          !["로그 유지"의 스크린샷](media/capture-browser-trace/edge-console-preserve-log.png)

1. **네트워크** 탭을 선택한 다음 **프로파일링 세션 중지** 및 **세션 지우기** 를 선택합니다.

    !["프로파일링 세션 중지" 및 "세션 지우기"의 스크린샷](media/capture-browser-trace/edge-stop-clear-session.png)

1. **프로파일링 세션 시작** 을 선택하면 포털에 문제가 재현됩니다.

    !["프로파일링 세션 시작"의 스크린샷](media/capture-browser-trace/edge-start-session.png)

    다음 이미지와 비슷한 세션 출력이 표시됩니다.

    ![프로파일링 세션의 출력을 보여 주는 스크린샷.](media/capture-browser-trace/edge-browser-trace-results.png)

1. 예기치 않은 포털 동작을 재현한 후 **프로파일링 세션 중지** 를 선택한 다음 **HAR로 내보내기** 를 선택하고 파일을 저장합니다.

    !["HAR로 내보내기"의 스크린샷](media/capture-browser-trace/edge-network-export-har.png)

1. 단계 레코더를 중지하고 기록을 저장합니다.

1. 브라우저 개발자 도구 창으로 돌아가서 **콘솔** 탭을 선택하고 창을 확장합니다. 콘솔 출력의 시작 부분에 커서를 놓고 출력의 전체 내용을 끌어서 선택합니다. 마우스 오른쪽 단추로 클릭한 다음 **복사** 를 선택하고 콘솔 출력을 텍스트 파일에 저장합니다.

    ![복사 메뉴 옵션을 강조 표시하는 스크린샷.](media/capture-browser-trace/edge-console-select.png)

1. HAR 파일, 콘솔 출력 및 화면 기록을 압축된 형식(예: .zip)으로 패키징하고 Microsoft 지원과 공유합니다.

## <a name="apple-safari"></a>Apple Safari

다음 단계에서는 Apple Safari에서 개발자 도구를 사용하는 방법을 보여 줍니다. 자세한 내용은 [Safari 개발자 도구 개요](https://support.apple.com/guide/safari-developer/safari-developer-tools-overview-dev073038698/11.0/mac)를 참조하세요.

1. Apple Safari에서 개발자 도구를 사용하도록 설정합니다.

    1. **Safari** 를 선택한 다음 **기본 설정** 을 선택합니다.

        ![Safari 기본 설정의 스크린샷](media/capture-browser-trace/safari-preferences.png)

    1. **고급** 탭을 선택한 다음 **메뉴 모음에서 개발자용 메뉴 보기** 를 선택합니다.

        ![Safari 고급 기본 설정의 스크린샷](media/capture-browser-trace/safari-show-develop-menu.png)

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 추적이 로그인과 관련된 중요한 정보를 포함하지 않도록 추적을 시작하기 _전에_ 로그인해야 합니다. 

1. 포털에서 수행하는 단계를 기록하기 시작합니다. 자세한 내용은 [Mac에서 화면을 기록하는 방법](https://support.apple.com/HT208721)을 참조하세요.

1. 포털에서 문제가 발생하는 단계의 바로 앞 단계로 이동합니다.

1. **개발** 을 선택한 다음 **웹 검사기 표시** 를 선택합니다.

    !["웹 검사기 표시"의 스크린샷](media/capture-browser-trace/safari-show-web-inspector.png)

1. 기본적으로 브라우저는 현재 로드된 페이지에 대해서만 추적 정보를 유지합니다. 브라우저가 모든 추적 정보를 유지하도록 하려면 다음 옵션을 설정합니다. 재현하기 위해 두 개 이상의 페이지로 이동해야 하는 경우에도 마찬가지입니다.

    1. **네트워크** 탭을 선택한 다음 **로그 유지** 를 선택합니다.

          ![로그 유지 옵션을 보여 주는 스크린샷.](media/capture-browser-trace/safari-network-preserve-log.png)

    1. **콘솔** 탭을 선택한 다음 **로그 유지** 를 선택합니다.

          ![콘솔 탭의 로그 유지를 보여 주는 스크린샷.](media/capture-browser-trace/safari-console-preserve-log.png)

1. **네트워크** 탭을 선택한 다음 **네트워크 항목 지우기** 를 선택합니다.

    !["네트워크 항목 지우기"의 스크린샷](media/capture-browser-trace/safari-clear-session.png)

1. 포털에서 문제를 재현합니다. 다음 이미지와 비슷한 세션 출력이 표시됩니다.

    ![문제를 재현한 후 출력을 보여 주는 스크린샷.](media/capture-browser-trace/safari-browser-trace-results.png)

1. 예기치 않은 포털 동작을 재현한 후 **내보내기** 를 선택하고 파일을 저장합니다.

    !["내보내기"의 스크린샷](media/capture-browser-trace/safari-network-export-har.png)

1. 화면 레코더를 중지하고 기록을 저장합니다.

1. 브라우저 개발자 도구 창으로 돌아가서 **콘솔** 탭을 선택하고 창을 확장합니다. 콘솔 출력의 시작 부분에 커서를 놓고 출력의 전체 내용을 끌어서 선택합니다. Command-C를 사용하여 출력을 복사하고 텍스트 파일로 저장합니다.

    ![출력을 보고 복사할 수 있음을 강조 표시하는 스크린샷](media/capture-browser-trace/safari-console-select.png)

1. HAR 파일, 콘솔 출력 및 화면 기록을 압축된 형식(예: .zip)으로 패키징하고 Microsoft 지원과 공유합니다.

## <a name="firefox"></a>Firefox

다음 단계에서는 Firefox에서 개발자 도구를 사용하는 방법을 보여 줍니다. 자세한 내용은 [Firefox 개발자 도구](https://developer.mozilla.org/docs/Tools)를 참조하세요.

1. [Azure Portal](https://portal.azure.com)에 로그인합니다. 추적이 로그인과 관련된 중요한 정보를 포함하지 않도록 추적을 시작하기 _전에_ 로그인해야 합니다.

1. 포털에서 수행하는 단계를 기록하기 시작합니다. Windows에서 [단계 레코더](https://support.microsoft.com/help/22878/windows-10-record-steps)를 사용하거나 [Mac에서 화면을 기록하는 방법](https://support.apple.com/HT208721)을 참조하세요.

1. 포털에서 문제가 발생하는 단계의 바로 앞 단계로 이동합니다.

1. F12 키를 누르거나 ![브라우저 설정 아이콘의 스크린샷](media/capture-browser-trace/firefox-icon-settings.png) > **웹 개발자** > **도구 설정/해제** 를 선택합니다.

1. 기본적으로 브라우저는 현재 로드된 페이지에 대해서만 추적 정보를 유지합니다. 브라우저가 모든 추적 정보를 유지하도록 하려면 다음 옵션을 설정합니다. 재현하기 위해 두 개 이상의 페이지로 이동해야 하는 경우에도 마찬가지입니다.

    1. **네트워크** 탭을 선택한 다음 **로그 유지** 를 선택합니다.

          ![로그 유지 옵션을 강조 표시하는 스크린샷.](media/capture-browser-trace/firefox-network-persist-logs.png)

    1. **콘솔** 탭을 선택하고 **콘솔 설정** 을 선택한 다음 **로그 유지** 를 선택합니다.

          !["로그 유지"의 스크린샷](media/capture-browser-trace/firefox-console-persist-logs.png)

1. **네트워크** 탭을 선택한 다음 **지우기** 를 선택합니다.

    !["지우기"의 스크린샷](media/capture-browser-trace/firefox-clear-session.png)

1. 포털에서 문제를 재현합니다. 다음 이미지와 비슷한 세션 출력이 표시됩니다.

    ![브라우저 추적 결과의 스크린샷](media/capture-browser-trace/firefox-browser-trace-results.png)

1. 예기치 않은 포털 동작을 재현한 후, **HAR로 모두 저장** 을 선택합니다.

    !["HAR 내보내기"의 스크린샷](media/capture-browser-trace/firefox-network-export-har.png)

1. Windows에서 단계 레코더 또는 Mac에서 화면 녹화를 중지하고 녹화를 저장합니다.

1. 브라우저 개발자 도구 창으로 돌아가서 **콘솔** 탭을 선택합니다. 메시지 중 하나를 마우스 오른쪽 단추로 클릭한 다음, **표시되는 메시지 내보내기** 를 선택하고 콘솔 출력을 텍스트 파일로 저장합니다.

    ![콘솔 출력의 스크린샷](media/capture-browser-trace/firefox-console-select.png)

1. HAR 파일, 콘솔 출력 및 화면 기록을 압축된 형식(예: .zip)으로 패키징하고 Microsoft 지원과 공유합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Portal](azure-portal-overview.md)에 관해 자세히 알아봅니다.
- Azure Portal에서 [지원 요청을 여는](supportability/how-to-create-azure-support-request.md) 방법을 알아봅니다.

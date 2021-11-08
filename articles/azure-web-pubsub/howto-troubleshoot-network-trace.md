---
title: 네트워크 추적을 수집하는 방법
description: 문제 해결에 도움이 되는 네트워크 추적을 가져오는 방법 알아보기
author: yjin81
ms.author: yajin1
ms.service: azure-web-pubsub
ms.topic: how-to
ms.date: 11/08/2021
ms.openlocfilehash: 48209fc5f4876c097d30ce6bcf464ba40ee94a7a
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/08/2021
ms.locfileid: "131997612"
---
# <a name="how-to-collect-a-network-trace"></a>네트워크 추적을 수집하는 방법

문제가 발생했을 때 네트워크 추적이 많은 유용한 정보를 제공하는 경우가 많습니다. 이 방법 가이드에서는 네트워크 추적을 수집하는 옵션을 보여 줍니다.

> [!WARNING]
> 네트워크 추적에는 앱에서 보낸 모든 메시지의 전체 콘텐츠가 포함됩니다. 프로덕션 앱의 원시 네트워크 추적을 GitHub와 같은 공개 포럼에 게시하면 **안 됩니다**.

## <a name="collect-a-network-trace-with-fiddler"></a>Fiddler를 사용하여 네트워크 추적 수집

Fiddler는 HTTP 추적을 수집하는 데 사용되는 강력한 도구입니다. [telerik.com/fiddler](https://www.telerik.com/fiddler)에서 설치하고 시작한 다음, 앱을 실행하고 문제를 재현합니다. Fiddler는 Windows, macOS 및 Linux에서 사용할 수 있습니다. 

HTTPS를 사용하여 연결하는 경우 Fiddler에서 HTTPS 트래픽을 해독할 수 있는지 확인하기 위한 몇 가지 추가 단계가 있습니다. 자세한 내용은 [Fiddler 설명서](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS)를 참조하세요.

추적을 수집한 후에는 메뉴 모음에서 **파일** > **저장** > **모든 세션** 을 선택하여 추적을 내보낼 수 있습니다.

## <a name="collect-a-network-trace-with-tcpdump-macos-and-linux-only"></a>tcpdump를 사용하여 네트워크 추적 수집(macOS 및 Linux에만 해당)

이 방법은 모든 앱에 사용할 수 있습니다.

명령 셸에서 다음 명령을 실행하여 tcpdump를 통해 원시 TCP 추적을 수집할 수 있습니다. 사용 권한 오류가 발생하는 경우 `root`이거나 명령에 `sudo` 접두사를 사용해야 할 수 있습니다.

```console
tcpdump -i [interface] -w trace.pcap
```

`[interface]`를 캡처할 네트워크 인터페이스로 바꿉니다. 일반적으로 이는 `/dev/eth0`(표준 이더넷 인터페이스의 경우) 또는 `/dev/lo0`(localhost 트래픽의 경우)과 같은 항목입니다. 자세한 내용은 호스트 시스템의 `tcpdump` 기본 페이지를 참조하세요.

```console
man tcpdump
```

## <a name="collect-a-network-trace-in-the-browser-browser-based-apps-only"></a>브라우저에서 네트워크 추적 수집(브라우저 기반 앱에만 해당)

대부분의 브라우저 개발자 도구에는 브라우저와 서버 간의 네트워크 활동을 캡처할 수 있는 "네트워크" 탭이 있습니다. 

### <a name="microsoft-edge-chromium"></a>Microsoft Edge(Chromium)

1. [DevTools](/microsoft-edge/devtools-guide-chromium/) 열기
    * `F12` 선택 
    * `Ctrl`+`Shift`+`I` \(Windows/Linux\) 또는 `Command`+`Option`+`I` \(macOS\) 선택
    * `Settings and more`, `More Tools > Developer Tools`를 차례로 선택  
1. `Network` 탭 선택
1. 페이지를 새로 고치고(필요한 경우) 문제 재현
1. 도구 모음에서 `Export HAR...`을 선택하여 추적을 "HAR" 파일로 내보내기

    :::image type="content" source="./media/howto-troubleshoot-network-trace/edge-export-network-trace.png" alt-text="Microsoft Edge를 사용하여 네트워크 추적 수집":::

### <a name="google-chrome"></a>Google Chrome

1. [Chrome DevTools](https://developers.google.com/web/tools/chrome-devtools) 열기
    * `F12` 선택 
    * `Ctrl`+`Shift`+`I` \(Windows/Linux\) 또는 `Command`+`Option`+`I` \(macOS\) 선택  
    * `Customize and control Google Chrome`, `More Tools > Developer Tools`를 차례로 선택
1. `Network` 탭 선택
1. 페이지를 새로 고치고(필요한 경우) 문제 재현
1. 도구 모음에서 `Export HAR...`을 선택하여 추적을 "HAR" 파일로 내보내기

    :::image type="content" source="./media/howto-troubleshoot-network-trace/chrome-export-network-trace.png" alt-text="Google Chrome을 사용하여 네트워크 추적 수집":::

### <a name="mozilla-firefox"></a>Mozilla Firefox

1. [Firefox 개발자 도구](https://developer.mozilla.org/en-US/docs/Tools) 열기
    * `F12` 선택
    * `Ctrl`+`Shift`+`I` \(Windows/Linux\) 또는 `Command`+`Option`+`I` \(macOS\) 선택 
    * `Open menu`, `Web Developer > Toggle Tools`를 차례로 선택
1. `Network` 탭 선택
1. 페이지를 새로 고치고(필요한 경우) 문제 재현
1. 요청 목록에서 아무 곳이나 마우스 오른쪽 단추로 클릭하고 "모두 HAR로 저장" 선택

    :::image type="content" source="./media/howto-troubleshoot-network-trace/firefox-export-network-trace.png" alt-text="Mozilla Firefox를 사용하여 네트워크 추적 수집":::

### <a name="safari"></a>Safari

1. [웹 개발 도구](https://developer.apple.com/safari/tools/) 열기
    * `Command`+`Option`+`I` 선택
    * `Developer` 메뉴, `Show Web Inspector`를 차례로 선택 
1. `Network` 탭 선택
1. 페이지를 새로 고치고(필요한 경우) 문제 재현
1. 요청 목록에서 아무 곳이나 마우스 오른쪽 단추로 클릭하고 "모두 HAR로 저장" 선택
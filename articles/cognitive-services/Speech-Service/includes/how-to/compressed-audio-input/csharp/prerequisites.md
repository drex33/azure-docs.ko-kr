---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: 2be7255d753cef729266e42df49c6b3eaff73e7a
ms.sourcegitcommit: 67cdbe905eb67e969d7d0e211d87bc174b9b8dc0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111854511"
---
압축된 오디오 처리는 [GStreamer](https://gstreamer.freedesktop.org)를 사용하여 구현됩니다. 라이선스 때문에 GStreamer 이진 파일이 컴파일되지 않고 Speech SDK로 연결되지 않습니다. 개발자는 몇 가지 종속성 및 플러그 인을 설치해야 합니다. [Windows에 설치](https://gstreamer.freedesktop.org/documentation/installing/on-windows.html?gi-language=c) 또는 [Linux에 설치](https://gstreamer.freedesktop.org/documentation/installing/on-linux.html?gi-language=c)를 참조하세요. Speech SDK가 런타임 중에 이진 파일을 로드할 수 있도록 GStreamer 이진 파일이 시스템 경로에 있어야 합니다. 예를 들어 Windows에서 Speech SDK가 런타임 중에 `libgstreamer-1.0-0.dll` 또는 `gstreamer-1.0-0.dll`(최신 GStreamer의 경우)을 찾을 수 있다면 GStreamer 이진 파일이 시스템 경로에 있음을 의미합니다.


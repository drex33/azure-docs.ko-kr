---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: trbye
ms.openlocfilehash: 745ff6fddb3b175b4b046be71668f0c85ddd1c63
ms.sourcegitcommit: 67cdbe905eb67e969d7d0e211d87bc174b9b8dc0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/09/2021
ms.locfileid: "111854529"
---
압축된 오디오 처리는 [GStreamer](https://gstreamer.freedesktop.org)를 사용하여 구현됩니다. 라이선스 때문에 GStreamer 이진 파일이 컴파일되지 않고 Speech SDK로 연결되지 않습니다. 개발자는 여러 종속성 및 플러그 인을 설치해야 합니다.

> [!NOTE]
> Linux에 대한 필수 일반 설정은 [시스템 요구 사항 및 설치 지침](~/articles/cognitive-services/speech-service/speech-sdk.md#get-the-speech-sdk)을 참조하세요.

# <a name="ubuntudebian"></a>[Ubuntu/Debian](#tab/debian)

```sh
sudo apt install libgstreamer1.0-0 \
gstreamer1.0-plugins-base \
gstreamer1.0-plugins-good \
gstreamer1.0-plugins-bad \
gstreamer1.0-plugins-ugly
```

# <a name="rhelcentos"></a>[RHEL/CentOS](#tab/centos)

```sh
sudo yum install gstreamer1 \
gstreamer1-plugins-base \
gstreamer1-plugins-good \
gstreamer1-plugins-bad-free \
gstreamer1-plugins-ugly-free
```

> [!NOTE]
> - RHEL/CentOS 7 및 RHEL/CentOS 8에서 “ANY” 압축 형식을 사용하는 경우 스트림 미디어 형식 플러그 인이 위의 설치된 플러그 인에 없는 경우 추가 GStreamer 플러그 인을 설치해야 합니다. 


---
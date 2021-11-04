---
author: eric-urban
ms.service: cognitive-services
ms.topic: include
ms.date: 03/09/2020
ms.author: eur
ms.openlocfilehash: fa43fbf314fc59c54f5d5e25f93340eed2830abb
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131520157"
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

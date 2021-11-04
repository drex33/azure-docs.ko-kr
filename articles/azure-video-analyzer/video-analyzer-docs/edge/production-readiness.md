---
title: 프로덕션 준비 및 모범 사례
description: 이 문서에서는 프로덕션 환경에서 Azure Video Analyzer edge 모듈을 구성 하 고 배포 하는 방법에 대 한 지침을 제공 합니다.
ms.topic: reference
ms.date: 10/01/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 1baf85b21c9b6b2792eb93ca2d4c94ef50170395
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131103074"
---
# <a name="production-readiness-and-best-practices"></a>프로덕션 준비 및 모범 사례

[!INCLUDE [header](includes/edge-env.md)]

이 문서에서는 프로덕션 환경에서 Azure Video Analyzer 에지 모듈 및 클라우드 서비스를 구성하고 배포하는 방법에 대한 지침을 제공합니다. IoT Edge 솔루션 준비에 대해서는 [IoT Edge 솔루션을 프로덕션 단계에서 배포하도록 준비](../../../iot-edge/production-checklist.md) 문서도 검토해야 합니다.

조직의 IT 부서에 보안과 관련 된 측면을 문의 해야 합니다.

## <a name="creating-the-video-analyzer-account"></a>Video Analyzer 계정 만들기

Video Analyzer 계정을 [만들](../create-video-analyzer-account.md) 때 다음을 수행하는 것이 좋습니다.

1. 구독 소유자는 Video Analyzer에 필요한 모든 리소스를 만들 리소스 그룹을 만들어야 합니다.
1. 그런 다음 소유자는 사용자에게 해당 리소스 그룹에 대한 [기여자](../../../role-based-access-control/built-in-roles.md#contributor) 및 [사용자 액세스 관리자](../../../role-based-access-control/built-in-roles.md#user-access-administrator) 역할을 부여해야 합니다.
1. 그런 다음 해당 리소스 그룹 아래에 Storage 계정, IoT Hub, 사용자 할당 관리 id 및 비디오 분석기 계정 등의 관련 리소스를 만들 수 있습니다.

## <a name="running-the-module-as-a-local-user"></a>로컬 사용자로 모듈 실행

Video Analyzer 에지 모듈을 IoT Edge 디바이스에 배포하는 경우 기본적으로 상승된 권한으로 실행됩니다. 다음을 표시하는 모듈(`sudo iotedge logs {name-of-module}`)의 로그를 사용하여 이를 확인할 수 있습니다.

```
!! production readiness: user accounts – Warning
       LOCAL_USER_ID and LOCAL_GROUP_ID environment variables are not set. The program will run as root!
       For optimum security, make sure to set LOCAL_USER_ID and LOCAL_GROUP_ID environment variables to a non-root user and group.
```

아래 섹션에는 위의 경고를 해결하는 방법이 설명되어 있습니다.

### <a name="creating-and-using-a-local-user-account"></a>로컬 사용자 계정 만들기 및 사용

가능하면 약간의 권한이 있는 계정을 사용하여 프로덕션 환경에서 Video Analyzer 에지 모듈을 실행할 수 있으며 실행해야 합니다. 예를 들어 다음 명령을 사용하여 Linux VM에서 로컬 사용자 계정을 만드는 방법을 보여 줍니다.

```
sudo groupadd -g 1010 localedgegroup
sudo useradd --home-dir /home/localedgeuser --uid 1010 --gid 1010 localedgeuser
```

그런 다음, 배포 매니페스트에서 LOCAL_USER_ID 및 LOCAL_GROUP_ID 환경 변수를 루트가 아닌 사용자 및 그룹으로 설정할 수 있습니다.

```
"avaedge": {
  "version": "1.0",
  …
  "env": {
    "LOCAL_USER_ID": {
       "value": "1010"
    },
    "LOCAL_GROUP_ID": {
     "value": "1010"
    }
  }
},
…
```

### <a name="granting-permissions-to-device-storage"></a>디바이스 스토리지에 대한 사용 권한 부여

Video Analyzer 에지 모듈에는 다음과 같은 경우 로컬 파일 시스템에 파일을 쓸 수 있는 기능이 필요합니다.

- 모듈 쌍 속성 [`applicationDataDirectory`](module-twin-configuration-schema.md)를 사용하는 경우. 이 경우에는 구성 데이터를 저장하기 위해 로컬 파일 시스템의 디렉터리를 지정해야 합니다.
- 파이프라인을 사용하여 클라우드에 비디오를 기록하려면 모듈에서 에지 디바이스의 디렉터리를 캐시로 사용해야 합니다(자세한 내용은 [연속 비디오 녹화](../continuous-video-recording.md) 문서 참조).
- [로컬 파일에 기록](../event-based-video-recording-concept.md)하는 경우. 녹화된 비디오의 파일 경로를 지정합니다.

위의 방법 중 하나를 사용하려는 경우 위의 사용자 계정에 관련 디렉터리에 대한 액세스 권한이 있는지 확인해야 합니다. 예를 들어 `applicationDataDirectory`를 고려합니다. 에지 디바이스에서 디렉터리를 만들고 디바이스 스토리지를 모듈 스토리지에 연결할 수 있습니다.

```
sudo mkdir /var/lib/videoanalyzer
sudo chown -R localedgeuser:localedgegroup /var/lib/videoanalyzer
```

그런 다음, 배포 매니페스트의 에지 모듈에 대한 만들기 옵션에서 위의 디렉터리("/var/lib/videoanalyzer")를 모듈의 디렉터리(예: "/var/lib/videoanalyzer")에 매핑하는 `binds` 설정을 추가할 수 있습니다. 그리고 후자의 디렉터리를 `applicationDataDirectory`의 값으로 사용합니다.

```
        "modules": {
          "avaedge": {
            "version": "1.1",
            "type": "docker",
            "status": "running",
            "restartPolicy": "always",
            "settings": {
              "image": "mcr.microsoft.com/media/video-analyzer:1",
              "createOptions": "{ \"HostConfig\": { \"LogConfig\": { \"Type\": \"\", \"Config\": { \"max-size\": \"10m\", \"max-file\": \"10\" } }, \"Binds\": [ \"/var/media/:/var/media/\", \"/var/lib/videoanalyzer/:/var/lib/videoanalyzer\" ], \"IpcMode\": \"host\", \"ShmSize\": 1536870912 } }"
            },
            "env": {
              "LOCAL_USER_ID": {
                "value": "1010"
              },
              "LOCAL_GROUP_ID": {
                "value": "1010"
              }
            }
          },
          …
        },
        
    …
    
    "avaedge": {
       "properties.desired": {
          "applicationDataDirectory": "/var/lib/videoanalyzer",
          "ProvisioningToken": "{your-token}",
          "diagnosticsEventsOutputName": "diagnostics",
          "operationalEventsOutputName": "operational",
          "logLevel": "information",
          "LogCategories": "Application,Events",
          "allowUnsecuredEndpoints": false,
          "telemetryOptOut": false
    }
}
```

빠른 시작의 샘플 파이프라인과 [연속 동영상 녹화](use-continuous-video-recording.md)와 같은 자습서를 보면 미디어 캐시 디렉터리(`localMediaCachePath`)가 `applicationDataDirectory` 아래의 하위 디렉터리를 사용한다는 것을 알 수 있습니다. 캐시에 임시 데이터가 포함되어 있기 때문에 권장되는 방법입니다.

또한 TLS 암호화를 사용하여 트래픽을 보호하는 프로덕션 환경에 권장되는 대로 `allowedUnsecuredEndpoints`는 `false`로 설정됩니다.

### <a name="tips-about-maintaining-your-edge-device"></a>에지 디바이스 유지 관리에 대한 팁

아래 팁은 완전한 목록이 아니지만 일반적으로 발생한 알려진 문제에 도움이 됩니다.

IoT Edge 디바이스로 사용하는 Linux VM은 주기적으로 관리되지 않는 경우 응답하지 않을 수 있습니다. 캐시를 정리하고, 불필요한 패키지를 제거하고, VM에서 사용하지 않은 컨테이너를 제거하는 것이 중요합니다. 이 작업을 수행하기 위해 여기서는 에지 VM에서 사용할 수 있는 권장 명령 세트를 제공합니다.

- `sudo apt-get clean`

apt-get clean 명령은 /var/cache에 남아 있는 검색된 패키지 파일의 로컬 리포지토리를 지웁니다. 정리하는 디렉터리는 /var/cache/apt/archives/ 및 /var/cache/apt/archives/partial/입니다. /var/cache/apt/archives에 남아 있는 유일한 파일은 잠금 파일과 부분 하위 디렉터리입니다. apt-get clean 명령은 일반적으로 정기적으로 예약된 유지 관리의 일환으로 필요에 따라 디스크 공간을 정리하는 데 사용됩니다. 자세한 내용은 [apt-get을 사용하여 정리](https://www.networkworld.com/article/3453032/cleaning-up-with-apt-get.html)를 참조하세요.

- `sudo apt-get autoclean`

apt-get autoclean 옵션(예: apt-get clean)은 검색된 패키지 파일의 로컬 리포지토리를 지우므로 더 이상 다운로드할 수 없고 유용하지 않은 파일만 제거합니다. 캐시가 너무 커지지 않도록 하는 데 도움이 됩니다.

- `sudo apt-get autoremove`

자동 제거 옵션을 사용하면 다른 패키지에 필요하지만 해당 다른 패키지가 제거되면 더 이상 필요하지 않게 되는 자동으로 설치된 패키지가 제거됩니다.

- `sudo docker image ls` – 에지 시스템의 Docker 이미지 목록을 제공합니다.

- `sudo docker system prune`

Docker는 이미지, 컨테이너, 볼륨 및 네트워크와 같은 사용되지 않는 개체("가비지 수집"이라고도 함)를 정리하는 일반적인 접근 방법을 사용합니다. Docker가 명시적으로 요청하지 않는 한, 이러한 개체는 일반적으로 제거되지 않습니다. 이로 인해 Docker에서 추가 디스크 공간을 사용할 수 있습니다. 각 개체 형식에 대해 Docker는 정리 명령을 제공합니다. 또한 `docker system prune`을 사용하여 여러 유형의 오브젝트를 한 번에 정리할 수 있습니다. 자세한 내용은 [사용하지 않은 Docker 개체 정리](https://docs.docker.com/config/pruning/)를 참조하세요.

- `sudo docker rmi REPOSITORY:TAG`

에지 모듈에서 업데이트가 수행되면 docker에는 이전 버전의 에지 모듈이 계속 제공될 수 있습니다. 이 경우 `docker rmi` 명령을 사용하여 이미지 버전 태그로 식별된 특정 이미지를 제거하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

[빠른 시작: 시작하기 – Azure Video Analyzer](get-started-detect-motion-emit-events.md)

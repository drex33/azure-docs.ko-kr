---
title: OSM 클라이언트 라이브러리 다운로드
description: OSM (Open Service 메시) 클라이언트 라이브러리를 다운로드 하 고 구성 합니다.
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.author: pgibson
zone_pivot_groups: client-operating-system
ms.openlocfilehash: fa6374a51e566bf28c0835beb9fd86a8e476b861
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132753901"
---
# <a name="download-and-configure-the-open-service-mesh-osm-client-library"></a>OSM (Open Service 메시) 클라이언트 라이브러리를 다운로드 하 고 구성 합니다.

이 문서에서는 AKS에 대 한 OSM 추가 기능을 운영 및 구성 하는 데 사용할 OSM 클라이언트 라이브러리를 다운로드 하는 방법 및 사용자 환경에 맞게 이진 파일을 구성 하는 방법을 설명 합니다.

::: zone pivot="client-operating-system-linux"

[!INCLUDE [Linux - download and install client binary](includes/servicemesh/osm/open-service-mesh-binary-install-linux.md)]

::: zone-end

::: zone pivot="client-operating-system-macos"

[!INCLUDE [macOS - download and install client binary](includes/servicemesh/osm/open-service-mesh-binary-install-macos.md)]

::: zone-end

::: zone pivot="client-operating-system-windows"

[!INCLUDE [Windows - download and install client binary](includes/servicemesh/osm/open-service-mesh-binary-install-windows.md)]

::: zone-end

> [!WARNING]
> `osm install`을 사용하여 이진에서 OSM을 설치하지 마세요. 이렇게 하면 AKS에 대한 추가 기능으로 통합되지 않은 OSM 설치가 발생합니다.

## <a name="configure-osm-cli-variables-with-an-osm_config-file"></a>OSM_CONFIG 파일을 사용 하 여 OSM CLI 변수 구성

사용자는 기본 OSM CLI 구성을 재정의 하 여 추가 기능 환경을 향상 시킬 수 있습니다. 이와 같이 구성 파일을 만들어이 작업을 수행할 수 있습니다 `kubeconfig` . 구성 파일은 `$HOME/.osm/config.yaml` 또는 환경 변수를 사용 하 여 내보내는 다른 경로에서 만들 수 있습니다 `OSM_CONFIG` .

파일에는 다음 YAML 형식 콘텐츠가 포함 되어야 합니다.

```yaml
install:
  kind: managed
  distribution: AKS
  namespace: kube-system
```

에서 파일을 만들지 않은 경우 `$HOME/.osm/config.yaml` `OSM_CONFIG` 구성 파일이 만들어진 경로를 가리키도록 환경 변수를 설정 해야 합니다.

OSM_CONFIG 설정한 후 명령의 출력은 다음과 같아야 합니다 `osm env` .

```console
$ osm env
---
install:
  kind: managed
  distribution: AKS
  namespace: kube-system
```

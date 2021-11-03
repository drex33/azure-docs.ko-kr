---
title: OSM 클라이언트 라이브러리 다운로드
description: OSM(Open Service Mesh) 클라이언트 라이브러리 다운로드 및 구성
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 2524dca92126ac0fdd6c0ace502cc16d78b6d480
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131066846"
---
# <a name="download-and-configure-the-open-service-mesh-osm-client-library"></a>OSM(Open Service Mesh) 클라이언트 라이브러리 다운로드 및 구성

이 문서에서는 AKS용 OSM 추가 기능의 작동 및 구성에 사용할 OSM 클라이언트 라이브러리를 다운로드하는 방법과 사용자 환경에 대한 이진 파일을 구성하는 방법을 설명합니다.

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

## <a name="configure-osm-cli-variables-with-an-osm_config-file"></a>OSM_CONFIG 파일을 통해 OSM CLI 변수 구성

사용자는 기본 OSM CLI 구성을 재정의하여 추가 기능 환경을 향상시킬 수 있습니다. 이 작업은 유사한 구성 파일을 만들어 수행할 수 `kubeconfig` 있습니다. 구성 파일은 에서 `$HOME/.osm/config.yaml` 만들거나 환경 변수를 사용하여 내보낸 다른 경로에 만들 수 `OSM_CONFIG` 있습니다.

파일에는 다음 YAML 형식의 콘텐츠가 포함되어야 합니다.

```yaml
install:
  kind: managed
  distribution: AKS
  namespace: kube-system
```

에서 파일을 만들지 않은 경우 `$HOME/.osm/config.yaml` 구성 `OSM_CONFIG` 파일이 만들어지는 경로를 가리키도록 환경 변수를 설정해야 합니다.

OSM_CONFIG 설정하면 `osm env` 명령의 출력은 다음과 같습니다.

```console
$ osm env
---
install:
  kind: managed
  distribution: AKS
  namespace: kube-system
```

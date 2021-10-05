---
title: OSM CLI 환경 사용자 지정
description: osmconfig 파일을 통해 OSM CLI 환경을 사용자 지정합니다.
services: container-service
ms.topic: article
ms.date: 9/30/2021
ms.custom: mvc, devx-track-azurecli
ms.author: nshankar
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 1e5b39424b8e162feda65783eecc3a1a95fdaa7f
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/05/2021
ms.locfileid: "129539029"
---
# <a name="configure-osm-cli-variables-with-an-osm_config-file"></a>OSM_CONFIG 파일을 통해 OSM CLI 변수 구성

사용자는 기본 OSM CLI 구성을 재정의하여 추가 기능 환경을 향상시킬 수 있습니다. 이 작업은 유사한 구성 파일을 만들어 수행할 수 `kubeconfig` 있습니다. 구성 파일은 에서 `$HOME/.osm/config.yaml` 만들거나 환경 변수를 사용하여 내보낸 다른 경로에 만들 수 `OSM_CONFIG` 있습니다.

파일에는 다음 YAML 형식의 콘텐츠가 포함되어야 합니다.

```yaml
install:
  kind: managed
  distribution: AKS
  namespace: kube-system
```

에서 파일을 만들지 않은 경우 `$HOME/.osm/config.yaml` 구성 `OSM_CONFIG` 파일이 만들어진 경로를 가리키도록 환경 변수를 설정해야 합니다.

OSM_CONFIG 설정하면 `osm env` 명령의 출력은 다음과 여야 합니다. 

```console
$ osm env
--- 
install:
  kind: managed
  distribution: AKS
  namespace: kube-system
```
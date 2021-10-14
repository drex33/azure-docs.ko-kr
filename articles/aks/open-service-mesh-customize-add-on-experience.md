---
title: OSM CLI 환경 사용자 지정
description: Osmconfig 파일을 사용 하 여 OSM CLI 환경을 사용자 지정 합니다.
services: container-service
ms.topic: article
ms.date: 9/30/2021
ms.custom: mvc, devx-track-azurecli
ms.author: nshankar
ms.openlocfilehash: 9a18db63f247ed3508f09426841e84ae65c34923
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "129994768"
---
# <a name="configure-osm-cli-variables-with-an-osm_config-file"></a>OSM_CONFIG 파일을 사용 하 여 OSM CLI 변수 구성

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
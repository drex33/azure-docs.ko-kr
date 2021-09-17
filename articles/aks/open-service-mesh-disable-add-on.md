---
title: OSM 사용 안 함
description: 서비스 메시 열기 사용 안 함
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
ms.openlocfilehash: b2ad12db78451e2313494ccf9df6613443e9652b
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123441482"
---
# <a name="disable-open-service-mesh-osm-add-on-for-your-aks-cluster"></a>AKS 클러스터에 대한 OSM(Open Service Mesh) 추가 기능 사용 안 함

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

OSM 추가 기능을 사용하지 않도록 설정하려면 다음 명령을 실행합니다.

```azurecli-interactive
az aks disable-addons -n <AKS-cluster-name> -g <AKS-resource-group-name> -a open-service-mesh
```
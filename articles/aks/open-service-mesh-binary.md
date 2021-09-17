---
title: OSM 클라이언트 라이브러리 다운로드
description: OSM(Open Service Mesh) 클라이언트 라이브러리 다운로드
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
zone_pivot_groups: client-operating-system
ms.openlocfilehash: 128d0371e65d06e413f503094b56522c67ea83c0
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123441298"
---
# <a name="download-the-open-service-mesh-osm-client-library"></a>OSM(Open Service Mesh) 클라이언트 라이브러리 다운로드
이 문서에서는 AKS용 OSM 추가 기능의 작동 및 구성에 사용할 OSM 클라이언트 라이브러리를 다운로드하는 방법을 설명합니다.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

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
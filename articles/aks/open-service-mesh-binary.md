---
title: OSM 클라이언트 라이브러리 다운로드
description: OSM (Open Service 메시) 클라이언트 라이브러리를 다운로드 합니다.
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
zone_pivot_groups: client-operating-system
ms.openlocfilehash: fb3619ed95f0636ae83829e1ee1c818545dc86c9
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130227343"
---
# <a name="download-the-open-service-mesh-osm-client-library"></a>OSM (Open Service 메시) 클라이언트 라이브러리를 다운로드 합니다.
이 문서에서는 AKS에 대 한 OSM 추가 기능을 작동 하 고 구성 하는 데 사용할 OSM 클라이언트 라이브러리를 다운로드 하는 방법을 설명 합니다.

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

> [!NOTE]
> OSM CLI를 사용 하기 전에 이진을 설치한 후 [OSM AKS addon 환경을 사용자 지정](./open-service-mesh-customize-add-on-experience.md) 하도록 OSM CLI를 구성 하는 것이 좋습니다.
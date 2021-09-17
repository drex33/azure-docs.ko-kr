---
title: Azure Monitor 및 Application Insights 사용
description: 서비스 메시를 사용 하 여 Azure Monitor 및 Application Insights를 사용 하는 방법
services: container-service
ms.topic: article
ms.date: 8/26/2021
ms.custom: mvc, devx-track-azurecli
ms.author: pgibson
ms.openlocfilehash: e3a180e0dbe39bd078868d879d8eb78dc5136aec
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123441270"
---
# <a name="open-service-mesh-osm-monitoring-and-observability-using-azure-monitor-and-applications-insights"></a>Azure Monitor 및 Application Insights를 사용한 OSM(Open Service Mesh) 모니터링 및 가시성

Azure Monitor 및 Azure 애플리케이션는 모두 응용 프로그램 및 서비스의 가용성과 성능을 극대화 하는 데 도움이 Insights. 클라우드 및 온-프레미스 환경에서 원격 분석을 수집, 분석 및 작동 하기 위한 종합적인 솔루션을 제공 하 여이 작업을 수행 합니다.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

OSM AKS 추가 기능은 이러한 Azure 서비스 둘 다에 긴밀 하 게 통합 되어 있으며 OSM 메트릭에 의해 제공 되는 중요 Kpi를 보고 응답 하기 위한 원활한 Azure 환경을 제공 합니다. OSM AKS 추가 기능에 대해 이러한 서비스를 사용하도록 설정하고 구성하는 방법에 대한 자세한 내용은 [OSM에 대한 Azure Monitor](https://aka.ms/azmon/osmpreview) 페이지를 참조하세요.
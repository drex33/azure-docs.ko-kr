---
title: IP 및 포트 범위 제외
description: IP 및 포트 범위 제외 구현
services: container-service
ms.topic: article
ms.date: 10/8/2021
ms.author: kochhars
ms.openlocfilehash: 429c9278211b5355fe3b76ec4f79caaea72fc7d8
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132753781"
---
# <a name="implement-ip-and-span-classx-x-first-x-lastport-range-exclusionspan"></a>IP 및 <span class="x x-first x-last">포트 범위 제외</span> 구현

애플리케이션의 아웃바운드 TCP 기반 트래픽은 기본적으로 `iptables` OSM에서 프로그래밍한 규칙을 사용하여 가로채고 Envoy 프록시 사이드카로 리디렉션됩니다. OSM은 필요한 경우 트래픽 가로채기에서 제외할 IP 범위 및 포트 목록을 지정하는 방법을 제공합니다. IP 및 포트 범위를 제외하는 방법에 대한 지침은 [이 설명서를 참조하세요.](https://docs.openservicemesh.io/docs/guides/traffic_management/iptables_redirection/)

> [!NOTE]
>
> - Open Service Mesh AKS 추가 기능의 경우 **포트 제외는 를 사용하여 설치한 후에만 구현할 수 `kubectl patch` 있으며 OSM CLI `--set` 플래그를 사용하는 설치 중에는 구현할 수 없습니다.**
> - 메시의 일부인 애플리케이션 Pod가 IMDS, Azure DNS 또는 Kubernetes API 서버에 액세스해야 하는 경우 사용자는 위의 명령을 사용하여 이러한 IP 주소를 글로벌 아웃바운드 IP 범위 목록에 명시적으로 추가해야 합니다. [여기에서](https://docs.openservicemesh.io/docs/guides/app_onboarding/#onboard-services)Kubernetes API 서버 포트 제외의 예를 참조하세요.

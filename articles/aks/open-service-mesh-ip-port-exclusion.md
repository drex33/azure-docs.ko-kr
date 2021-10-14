---
title: IP 및 포트 범위 제외
description: IP 및 포트 범위 제외 구현
services: container-service
ms.topic: article
ms.date: 10/8/2021
ms.custom: mvc, devx-track-azurecli
ms.author: kochhars
ms.openlocfilehash: b12ec518aa46800778973524b4302ebed083d38f
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130008700"
---
# <a name="implement-ip-and-span-classx-x-first-x-lastport-range-exclusionspan"></a>IP 및 <span class="x x-first x-last">포트 범위 제외</span> 구현

응용 프로그램의 아웃 바운드 TCP 기반 트래픽은 기본적으로 OSM에서 프로그래밍 하는 규칙을 사용 하 여 가로채 `iptables` 고, 엔보이 프록시 사이드카로 리디렉션됩니다. OSM은 필요한 경우 트래픽 가로채기에서 제외할 IP 범위 및 포트 목록을 지정 하는 수단을 제공 합니다. IP 및 포트 범위를 제외 하는 방법에 대 한 지침은 [이 설명서](https://release-v0-11.docs.openservicemesh.io/docs/guides/traffic_management/iptables_redirection/)를 참조 하세요. 

**Open Service 메시 AKS 추가 기능에 대 한 포트 제외는를 사용 하 여 설치 하 `kubectl patch` 고 OSM CLI 플래그를 사용 하 여 설치 하는 경우에만 구현할 수 있습니다 `--set` .**

> [!NOTE]
> 메시의 일부인 응용 프로그램 pod IMDS, Azure DNS 또는 Kubernetes API 서버에 액세스 해야 하는 경우 사용자는 위의 명령을 사용 하 여이 IP 주소를 글로벌 아웃 바운드 IP 범위 목록에 명시적으로 추가 해야 합니다. Kubernetes API 서버 포트 제외의 예제는 [여기](https://release-v0-11.docs.openservicemesh.io/docs/guides/app_onboarding/#onboard-services)를 참조 하세요.

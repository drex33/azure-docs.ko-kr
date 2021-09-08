---
title: Kubernetes 클라이언트 애플리케이션 호스팅 모범 사례
titleSuffix: Azure Cache for Redis
description: Azure Cache for Redis를 사용하는 Kubernetes 클라이언트 애플리케이션을 호스트하는 방법을 알아봅니다.
author: shpathak-msft
ms.service: cache
ms.topic: conceptual
ms.date: 08/25/2021
ms.author: shpathak
ms.openlocfilehash: 27117f8839c6ea0d98c2f15e01ee6a3ab3ec36c6
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/27/2021
ms.locfileid: "123117111"
---
# <a name="kubernetes-hosted-client-application"></a>Kubernetes 호스트 클라이언트 애플리케이션

## <a name="multiple-pods"></a>여러 Pod

Redis 서버에 연결하는 여러 Pod가 있는 경우 Pod의 새 연결이 시차를 두고 만들어졌는지 확인합니다. 여러 Pod가 시차 없이 짧은 시간 안에 시작되면 만들어지는 클라이언트 연결 수가 급증합니다. 연결 수가 많으면 Redis 서버의 로드가 높아지고 시간이 초과될 수 있습니다.

동시에 여러 Pod를 종료하는 시나리오를 방지합니다. 시차 종료에 실패하면 CPU 압력으로 이어지는 연결 수가 급격히 감소할 수 있습니다.

## <a name="sufficient-resources"></a>충분한 리소스

Redis 서버에 연결하는 Pod를 호스팅하는 Kubernetes 노드에 충분한 메모리, CPU 및 네트워크 대역폭이 있는지 확인합니다.  

## <a name="noisy-neighbor-problem"></a>노이지 네이버 문제

*노이지 네이버* 문제에 주의합니다. 클라이언트를 실행하는 Pod는 동일한 노드에서 실행되는 다른 Pod의 영향을 받고 Redis 연결 또는 IO 작업을 제한할 수 있습니다.

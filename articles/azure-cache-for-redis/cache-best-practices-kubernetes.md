---
title: Kubernetes 클라이언트 애플리케이션 호스팅 모범 사례
titleSuffix: Azure Cache for Redis
description: Azure Cache for Redis를 사용하는 Kubernetes 클라이언트 애플리케이션을 호스트하는 방법을 알아봅니다.
author: shpathak-msft
ms.service: cache
ms.topic: conceptual
ms.date: 10/11/2021
ms.author: shpathak
ms.openlocfilehash: 33628fc16dfc2693cad0f8cd13f555cc10e55f25
ms.sourcegitcommit: 54e7b2e036f4732276adcace73e6261b02f96343
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/12/2021
ms.locfileid: "129808737"
---
# <a name="kubernetes-hosted-client-application"></a>Kubernetes 호스트 클라이언트 애플리케이션

## <a name="client-connections-from-multiple-pods"></a>여러 pod의 클라이언트 연결

여러 pod가 Redis 서버에 연결 하는 경우 pod의 새 연결이 지연 된 방식으로 만들어졌는지 확인 합니다. 여러 pod가 시차 없이 짧은 시간 내에 시작 되는 경우 생성 되는 클라이언트 연결 수가 갑자기 급증 합니다. 연결 수가 많으면 Redis 서버의 로드가 높아지고 시간이 초과될 수 있습니다.

동시에 여러 Pod를 종료하는 시나리오를 방지합니다. 시차 종료에 실패하면 CPU 압력으로 이어지는 연결 수가 급격히 감소할 수 있습니다.

## <a name="sufficient-pod-resources"></a>충분 한 pod 리소스

클라이언트 응용 프로그램을 실행 하는 pod에 충분 한 CPU 및 메모리 리소스가 제공 되는지 확인 합니다. 클라이언트 응용 프로그램이 해당 리소스 제한에 근접 하 여 실행 되는 경우 시간 초과가 발생할 수 있습니다.

## <a name="sufficient-node-resources"></a>충분 한 노드 리소스

클라이언트 응용 프로그램을 실행 하는 pod는 동일한 노드에서 실행 중인 다른 pod의 영향을 받을 수 있으며 Redis 연결 또는 IO 작업을 제한할 수 있습니다. 따라서 항상 클라이언트 응용 프로그램 pod 실행 되는 노드에 메모리, CPU 및 네트워크 대역폭이 충분 한지 확인 합니다. 이러한 리소스를 부족 하 게 실행 하면 연결 문제가 발생할 수 있습니다.

## <a name="linux-hosted-client-applications-and-tcp-settings"></a>Linux 호스팅 클라이언트 응용 프로그램 및 TCP 설정

Redis client 응용 프로그램에 대 한 Azure 캐시가 Linux 기반 컨테이너에서 실행 되는 경우 [linux 호스팅 클라이언트 응용 프로그램에 대 한 tcp 설정](cache-best-practices-connection.md#tcp-settings-for-linux-hosted-client-applications)에 설명 된 대로 일부 TCP 설정을 업데이트 하는 것이 좋습니다.

## <a name="next-steps"></a>다음 단계

- [개발](cache-best-practices-development.md)
- [Azure Cache for Redis 개발 FAQ](cache-development-faq.yml)

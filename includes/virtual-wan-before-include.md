---
title: 파일 포함
description: 포함 파일
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 07/30/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4b1f2b5edf2c9efb79f84c123c0df921dbf9c259
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128909107"
---
* Azure 구독이 있습니다. Azure 구독이 아직 없는 경우 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

* 연결하려는 가상 네트워크가 있습니다. 

   * 온-프레미스 네트워크의 어떤 서브넷도 연결하려는 가상 네트워크 서브넷과 중첩되지 않는지 확인합니다. 
   * Azure Portal에서 가상 네트워크를 만들려면 [빠른 시작](../articles/virtual-network/quick-create-portal.md) 문서를 참조하세요.

* 가상 네트워크에는 기존 가상 네트워크 게이트웨이가 없어야 합니다. 

   * 가상 네트워크에 이미 게이트웨이(VPN 또는 ExpressRoute)가 있는 경우 계속하기 전에 모든 게이트웨이를 제거해야 합니다. 
   * 이 구성을 사용하려면 가상 네트워크가 Virtual WAN 허브 게이트웨이에만 연결되어야 합니다.

* 가상 허브 프라이빗 주소 공간에 사용할 IP 주소 범위를 결정합니다. 이 정보는 가상 허브를 구성할 때 사용됩니다. 가상 허브는 Virtual WAN에서 만들고 사용하는 가상 네트워크입니다. 이는 지역에서 Virtual WAN 네트워크의 핵심입니다. 주소 공간 범위는 특정 규칙을 따라야 합니다.

   * 허브에 지정하는 주소 범위는 연결하는 기존 가상 네트워크와 겹칠 수 없습니다. 
   * 주소 범위는 연결하는 온-프레미스 주소 범위와 겹칠 수 없습니다. 
   * 온-프레미스 네트워크 구성에 있는 IP 주소 범위를 잘 모른다면 세부 정보를 알고 있는 다른 사람의 도움을 받으세요.

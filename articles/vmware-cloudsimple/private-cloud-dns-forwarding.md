---
title: Azure VMware Solution - 프라이빗 클라우드에서 온-프레미스로 DNS 전달
description: CloudSimple 프라이빗 클라우드 DNS 서버를 활성화하여 온-프레미스 리소스의 조회를 전달하는 방법을 설명합니다.
author: suzizuber
ms.author: v-szuber
ms.date: 02/29/2020
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 96810b4653cc6ce2bff92e1fa8455d39f6f13fda
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132322097"
---
# <a name="enable-cloudsimple-private-cloud-dns-servers-to-forward-dns-lookup-of-on-premises-resources-to-your-dns-servers"></a>CloudSimple 프라이빗 클라우드 DNS 서버를 활성화하여 온-프레미스 리소스의 DNS 조회를 DNS 서버로 전달합니다.

프라이빗 클라우드 DNS 서버는 온-프레미스 리소스에 대한 DNS 조회를 DNS 서버로 전달할 수 있습니다.  조회를 사용하도록 설정하면 프라이빗 클라우드 vSphere 구성 요소가 온-프레미스 환경에서 실행되는 모든 서비스를 조회하고 FQDN(정규화된 도메인 이름)을 사용하여 통신할 수 있습니다.

## <a name="scenarios"></a>시나리오 

온-프레미스 DNS 서버에 대한 DNS 조회를 전달하면 다음과 같은 시나리오에서 프라이빗 클라우드를 사용할 수 있습니다.

* 온-프레미스 VMware 솔루션에 대한 재해 복구 설정으로 프라이빗 클라우드 사용
* 프라이빗 클라우드 vSphere의 ID 원본으로 온-프레미스 Active Directory 사용
* 온-프레미스에서 프라이빗 클라우드로 가상 머신을 마이그레이션하는 데 HCX 사용

## <a name="before-you-begin"></a>시작하기 전에

DNS 전달이 작동하려면 프라이빗 클라우드 네트워크에서 온-프레미스 네트워크로 네트워크 연결이 있어야 합니다.  다음을 사용하여 네트워크 연결을 설정할 수 있습니다.

* [ExpressRoute를 사용하여 온-프레미스에서 CloudSimple로 연결](on-premises-connection.md)
* [사이트 간 VPN 게이트웨이 설정](./vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)

DNS 전달이 작동하려면 이 연결에서 방화벽 포트를 열어야 합니다.  사용되는 포트는 TCP 포트 53 또는 UDP 포트 53입니다.

> [!NOTE]
> 사이트 간 VPN을 사용하는 경우 온-프레미스 DNS 서버 서브넷을 온-프레미스 접두사의 일부로 추가해야 합니다.

## <a name="request-dns-forwarding-from-private-cloud-to-on-premises"></a>프라이빗 클라우드에서 온-프레미스로 DNS 전달 요청

프라이빗 클라우드에서 온-프레미스로 DNS 전달을 사용하도록 설정하려면 다음 정보를 제공하여 [지원 요청](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)을 제출합니다.

* 문제 유형: **기술**
* 구독: **CloudSimple 서비스가 배포된 구독**
* 서비스: **VMware Solution by CloudSimple**
* 문제 유형: **권고 또는 방법...**
* 문제 하위 유형: **NW에 대한 도움 필요**
* 세부 정보 창에서 온-프레미스 도메인의 도메인 이름을 입력합니다.
* 세부 정보 창의 프라이빗 클라우드에서 조회를 전달할 온-프레미스 DNS 서버 목록을 제공합니다.

## <a name="next-steps"></a>다음 단계

* [온-프레미스 방화벽 구성에 대한 자세한 정보](on-premises-firewall-configuration.md)
* [온-프레미스 DNS 서버 구성](on-premises-dns-setup.md)

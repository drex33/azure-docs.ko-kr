---
title: CloudSimple의 Azure VMware 솔루션-프라이빗 클라우드에 대한 워크로드 DNS 및 DHCP 설정
description: CloudSimple 프라이빗 클라우드 환경에서 실행되는 애플리케이션 및 워크로드에 대해 DNS 및 DHCP를 설정하는 방법을 설명합니다.
author: suzizuber
ms.author: v-szuber
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: dec9ea878b0403cec1c19f039a945a8145a64bd9
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132322363"
---
# <a name="set-up-dns-and-dhcp-applications-and-workloads-in-your-cloudsimple-private-cloud"></a>CloudSimple 프라이빗 클라우드에서 DNS 및 DHCP 애플리케이션 및 워크로드 설정

프라이빗 클라우드 환경에서 실행되는 애플리케이션 및 워크로드에는 조회 및 IP 주소 할당을 위한 이름 확인 및 DHCP 서비스가 필요합니다.  이러한 서비스를 제공하려면 적절한 DHCP 및 DNS 인프라가 필요합니다.  프라이빗 클라우드 환경에서 이러한 서비스를 제공하도록 가상 머신을 구성할 수 있습니다.  

## <a name="prerequisites"></a>사전 요구 사항

* VLAN이 구성된 분산 포트 그룹
* 온-프레미스 또는 인터넷 기반 DNS 서버로 설정 라우팅
* 가상 컴퓨터 템플릿 또는 가상 컴퓨터를 생성할 ISO

## <a name="linux-based-dns-server-setup"></a>Linux 기반의 DNS 서버 설정

Linux에서는 DNS 서버를 설정하기 위한 다양한 패키지를 제공합니다.  다음은 오픈 소스 BIND DNS 서버를 설정하는 방법에 대한 지침과 함께 [DigitalOcean의 설정 예제](https://www.digitalocean.com/community/tutorials/how-to-configure-bind-as-a-private-network-dns-server-on-ubuntu-18-04) 입니다.

## <a name="windows-based-setup"></a>Windows 기반 설정

이 Microsoft 항목에서는 Windows 서버를 DNS 서버 및 DHCP 서버로 설정하는 방법을 설명합니다.

* [Windows 서버를 DNS 서버로](/windows-server/networking/dns/dns-top)
* [Windows 서버를 DHCP 서버로](/windows-server/networking/technologies/dhcp/dhcp-top)

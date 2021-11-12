---
title: Avere vFXT DNS - Azure
description: Avere vFXT for Azure를 사용하여 라운드 로빈 부하 분산을 위한 DNS 서버를 구성합니다.
author: ekpgh
ms.service: avere-vfxt
ms.topic: conceptual
ms.date: 10/07/2021
ms.author: rohogue
ms.openlocfilehash: 26f85b4d3a2cc49e55d228fbd73ae7cb3b66bf5d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132337873"
---
# <a name="avere-cluster-dns-configuration"></a>Avere 클러스터 DNS 구성

이 섹션에서는 Avere vFXT 클러스터의 부하 분산을 위한 DNS 시스템 구성의 기본 사항에 대해 설명합니다.

이 문서에는 Azure 환경에서 DNS 서버를 설정하고 관리하는 방법에 대한 지침이 *포함되어 있지 않습니다*.

라운드 로빈 DNS를 사용하여 Azure에서 vFXT 클러스터의 부하를 분산하는 대신, 수동 방법을 사용하여 탑재할 때 클라이언트 간에 IP 주소를 균등하게 할당하는 것이 좋습니다. 몇 가지 방법은 [Avere 클러스터 탑재](avere-vfxt-mount-clients.md)에서 설명하고 있습니다.

DNS 서버의 사용 여부를 결정할 때 다음 사항에 유의하세요.

* NFS 클라이언트만 시스템에 액세스하는 경우 DNS를 사용할 필요가 없습니다. 숫자 IP 주소를 사용하여 모든 네트워크 주소를 지정할 수 있습니다.

* 시스템에서 SMB(CIFS) 액세스를 지원하는 경우 Active Directory 서버에 대한 DNS 도메인을 지정해야 하므로 DNS가 필요합니다.

* Kerberos 인증을 사용하려면 DNS가 필요합니다.

## <a name="load-balancing"></a>부하 분산

전체 부하를 분산시키려면 클라이언트 측 IP 주소에 대해 라운드 로빈 부하 분산을 사용하도록 DNS 도메인을 구성합니다.

## <a name="configuration-details"></a>구성 세부 정보

클라이언트에서 클러스터에 액세스 하는 경우 라운드 로빈 DNS (RRDNS)는 사용 가능한 모든 인터페이스 간에 요청을 자동으로 분산 합니다.

이 시스템을 설정 하려면 DNS 서버의 구성 파일을 사용자 지정 하 여 vFXT 클러스터의 주 도메인 주소로 탑재 요청을 가져오는 경우 모든 vFXT 클러스터의 탑재 지점의 트래픽을 할당 해야 합니다. 클라이언트는 서버 인수로 해당 도메인 이름을 사용 하 여 vFXT 클러스터를 탑재 하 고 자동으로 다음 탑재 IP로 라우팅됩니다.

RRDNS를 구성하는 두 가지 주요 단계는 다음과 같습니다.

1. ``named.conf``VFXT 클러스터에 대 한 쿼리의 순환 순서를 설정 하도록 DNS 서버 파일을 수정 합니다. 이 옵션을 선택하면 서버에서 사용 가능한 모든 IP 값을 순환합니다. 다음과 같은 명령문을 추가합니다.

   ```bash
   options {
       rrset-order {
           class IN A name "vfxt.contoso.com" order cyclic;
       };
   };
   ```

1. 다음 예제와 같이 사용 가능한 각 IP 주소에 대해 A 레코드 및 포인터(PTR) 레코드를 구성합니다.

   이러한 ``nsupdate`` 명령은 도메인 이름 vfxt.contoso.com 및 3 개의 탑재 주소 (10.0.0.10, 10.0.0.11)과 및 10.0.0.12)를 사용 하 여 vFXT 클러스터에 대해 DNS를 올바르게 구성 하는 예제를 제공 합니다.

   ```bash
   update add vfxt.contoso.com. 86400 A 10.0.0.10
   update add vfxt.contoso.com. 86400 A 10.0.0.11
   update add vfxt.contoso.com. 86400 A 10.0.0.12
   update add client-IP-10.contoso.com. 86400 A 10.0.0.10
   update add client-IP-11.contoso.com. 86400 A 10.0.0.11
   update add client-IP-12.contoso.com. 86400 A 10.0.0.12
   update add 10.0.0.10.in-addr.arpa. 86400 PTR client-IP-10.contoso.com
   update add 11.0.0.10.in-addr.arpa. 86400 PTR client-IP-11.contoso.com
   update add 12.0.0.10.in-addr.arpa. 86400 PTR client-IP-12.contoso.com
   ```

   이러한 명령은 클러스터의 각 탑재 주소에 대해 A 레코드를 만들고 역방향 DNS 검사를 적절하게 지원하도록 포인터 레코드를 설정합니다.

   아래 다이어그램은 이 구성의 기본 구조를 보여줍니다.

   :::image type="complex" source="media/round-robin-dns-diagram-vfxt.png" alt-text="클라이언트 탑재 지점 DNS 구성을 보여주는 다이어그램.":::
   <다이어그램에는 세 가지 범주의 요소 간 연결이 표시 됩니다. 단일 vFXT 클러스터 도메인 이름 (왼쪽), 3 개의 IP 주소 (중간 열) 및 세 개의 내부용 역방향 DNS 클라이언트 인터페이스 (오른쪽 열)를 사용 합니다. 왼쪽에 "vfxt.contoso.com" 레이블이 지정 된 단일 타원이 IP 주소 (10.0.0.10, 10.0.0.11)과 및 10.0.0.12)로 레이블이 지정 된 3 개의 타원이 가리키는 화살표로 연결 됩니다. Vfxt.contoso.com oval에서 3 개의 IP 타원 까지의 화살표에는 "A" 레이블이 지정 됩니다. 각 IP 주소 타원은 클라이언트 인터페이스로 레이블이 지정된 타원에 두 개의 화살표로 연결됩니다. IP가 10.0.0.10인 타원은 "client-IP-10.contoso.com"에 연결되고, IP가 10.0.0.11인 타원은 "client-IP-11.contoso.com"에 연결되고, IP가 10.0.0.12인 타원은 "client-IP-11.contoso.com"에 연결됩니다. IP 주소 타원과 클라이언트 인터페이스 타원 사이는 두 개의 화살표로 연결됩니다. 하나는 IP 주소 타원에서 클라이언트 인터페이스 타원을 가리키는 "PTR"로 레이블이 지정된 화살표이고 다른 하나는 클라이언트 인터페이스 타원에서 IP 주소 타원을 가리키는 "A"로 레이블이 지정된 화살표입니다.> :::image-end:::

RRDNS 시스템이 구성 된 후 클라이언트 컴퓨터에서 탑재 명령에 있는 클러스터 주소를 확인 하는 데 사용 하도록 지시 합니다.

## <a name="cluster-dns-settings"></a>클러스터 DNS 설정

**클러스터** > **관리 네트워크** 설정 페이지에서 vFXT 클러스터가 사용하는 DNS 서버를 지정합니다. 해당 페이지의 설정은 다음과 같습니다.

* DNS 서버 주소
* DNS 도메인 이름
* DNS 검색 도메인

이 페이지를 사용하는 방법에 대한 자세한 내용은 Avere 클러스터 구성 가이드의 [DNS 설정](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html#gui-dns)을 참조하세요.

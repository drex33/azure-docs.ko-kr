---
title: '자습서: Azure FXT Edge Filer 클러스터에서 네트워크 구성'
description: Azure FXT Edge Filer 클러스터를 만든 후 네트워크 설정을 사용자 지정하는 방법
author: ekpgh
ms.author: v-erkel
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.openlocfilehash: 8d349a0faa2cfc97f029e496b9bd92b1e5057018
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/25/2021
ms.locfileid: "122867506"
---
# <a name="tutorial-configure-the-clusters-network-settings"></a>자습서: 클러스터의 네트워크 설정 구성

새로 만든 Azure FXT Edge Filer 클러스터를 사용하기 전에 워크플로에 대해 몇 가지 설정을 확인 및 사용자 지정해야 합니다.

이 자습서에서는 새 클러스터에 대해 조정이 필요할 수 있는 네트워크 설정을 설명합니다.

다음 내용을 배웁니다.

> [!div class="checklist"]
>
> * 클러스터를 만든 후 업데이트해야 할 수 있는 네트워크 설정
> * AD 서버 또는 DNS 서버가 필요한 Azure FXT Edge Filer 사용 사례
> * 자동으로 클라이언트 요청의 부하를 FXT 클러스터로 분산하도록 RRDNS(라운드 로빈 DNS)를 구성하는 방법

이 단계를 완료하는 데 소요되는 시간은 시스템에 필요한 구성 변경 내용 수에 따라 다릅니다.

* 이 자습서를 읽고 몇 가지 설정만 확인하는 경우 10-15분이 걸립니다.
* 라운드 로빈 DNS를 구성할 경우 해당 작업이 1시간 이상 걸릴 수 있습니다.

## <a name="adjust-network-settings"></a>네트워크 설정 조정

새 Azure FXT Edge Filer 클러스터 설정 과정에는 몇 가지 네트워크 관련 작업이 포함됩니다. 이 목록을 확인하고 해당 시스템에 적용되는 작업을 결정합니다.

클러스터의 네트워크 설정에 대한 자세한 정보는 클러스터 구성 가이드의 [네트워크 서비스 구성](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html)을 참조하세요.

* 클라이언트 측 네트워크에 대한 라운드 로빈 DNS 구성(선택 사항)

  [FXT Edge Filer 클러스터에 대한 DNS 구성](#configure-dns-for-load-balancing)에서 설명한 대로 DNS 시스템을 구성하여 클러스터 트래픽의 부하를 분산합니다.

* NTP 설정 확인

* Active Directory 및 사용자/그룹 이름 다운로드 구성(필요한 경우)

  네트워크 호스트가 Active Directory 또는 다른 종류의 외부 디렉터리 서비스를 사용할 경우 클러스터가 사용자 이름과 그룹 정보를 다운로드하는 방법을 설정하기 위해 클러스터의 디렉터리 서비스 구성을 수정해야 합니다. 세부 정보는 클러스터 구성 가이드의 **클러스터** > **디렉터리 서비스** 를 참조하세요.

  SMB 지원에는 AD 서버가 필요합니다. SMB 설치를 시작하기 전에 AD를 구성합니다.

* VLAN 정의(선택 사항)
  
  클러스터의 vserver 및 글로벌 네임스페이스를 정의하기 전에 필요한 모든 추가 VLAN을 구성합니다. 자세한 정보는 클러스터 구성 가이드의 [VLAN 사용](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html#vlan-overview)을 참조하세요.

* 프록시 서버 구성(필요한 경우)

  클러스터에서 프록시 서버를 사용하여 외부 주소에 도달한 경우 다음 단계에 따라 설정합니다.

  1. **프록시 구성** 설정 페이지에서 프록시 서버를 정의합니다.
  1. **클러스터** > **일반 설정** 페이지 또는 **코어 파일러 세부 정보** 페이지를 통해 프록시 서버 구성을 적용합니다.
  
  자세한 내용은 클러스터 구성 가이드의 [웹 프록시 사용](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/proxy_overview.html)을 참조하세요.

* 클러스터가 사용할 [암호화 인증서](#encryption-certificates) 업로드(선택 사항)

### <a name="encryption-certificates"></a>암호화 인증서

FXT Edge Filer 클러스터는 다음 기능에 X.509 인증서를 사용합니다.

* 클러스터 관리 트래픽 암호화

* 타사 KMIP 서버에 대해 클라이언트를 대신하여 인증

* 클라우드 공급자의 서버 인증서 확인

클러스터에 인증서를 업로드해야 할 경우 **클러스터** > **인증서** 를 사용합니다. 세부 정보는 클러스터 구성 가이드의 [클러스터 > 인증서](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_certificates.html) 페이지를 참조하세요.

클러스터 관리 통신을 암호화하려면 **클러스터** > **일반 설정** 페이지를 사용하여 관리 TLS에 사용할 인증서를 선택합니다.

관리 머신이 클러스터의 [암호화 표준](supported-ciphers.md)을 충족하는지 확인합니다.

> [!Note]
> 클라우드 서비스 액세스 키는 **클라우드 자격 증명** 구성 페이지를 사용하여 저장됩니다. 위의 [코어 파일러 추가](add-storage.md#add-a-core-filer) 섹션에서 예제를 제공합니다. 세부 정보는 클러스터 구성 가이드의 [클라우드 자격 증명](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_credentials.html) 섹션을 참조하세요.

## <a name="configure-dns-for-load-balancing"></a>부하 분산에 대한 DNS 구성

이 섹션에서는 FXT Edge Filer 클러스터에서 모든 클라이언트 측 IP 주소 간에 클라이언트 부하를 배분하도록 RRDNS(라운드 로빈 DNS)를 구성하는 기본 사항을 설명합니다.

### <a name="decide-whether-or-not-to-use-dns"></a>DNS 사용 여부 결정

부하 분산은 항상 권장되지만 DNS를 항상 사용할 필요는 없습니다. 예를 들어 일부 클라이언트 워크플로 형식에서는 클러스터를 탑재할 때 스크립트를 사용하여 클라이언트 간에 균등하게 클러스터 IP 주소를 사용하는 것이 더 나을 수 있습니다. 몇 가지 방법은 [클러스터 탑재](mount-clients.md)에 설명되어 있습니다.

DNS 서버의 사용 여부를 결정할 때 다음 사항에 유의하세요.

* NFS 클라이언트에서만 시스템을 액세스하는 경우 DNS가 필요하지 않습니다. 숫자 IP 주소를 사용하여 모든 네트워크 주소를 지정할 수 있습니다.

* 시스템에서 SMB(CIFS) 액세스를 지원하는 경우 Active Directory 서버에 대한 DNS 도메인을 지정해야 하므로 DNS가 필요합니다.

* Kerberos 인증을 사용하려면 DNS가 필요합니다.

### <a name="round-robin-dns-configuration-details"></a>라운드 로빈 DNS 구성 세부 정보

클라이언트에서 클러스터에 액세스하는 경우 RRDNS는 사용 가능한 모든 인터페이스 간에 요청을 자동으로 분산시킵니다.

최적의 성능을 위해 다음 다이어그램과 같이 클라이언트 측 클러스터 주소를 처리하도록 DNS 서버를 구성합니다.

vserver 클러스터는 왼쪽에 표시되고, IP 주소는 가운데와 오른쪽에 표시되어 있습니다. 그림과 같이 A 레코드와 포인터를 사용하여 각 클라이언트 액세스 지점을 구성합니다.

:::image type="complex" source="media/fxt-cluster-config/fxt-rrdns-diagram.png" alt-text="클러스터 라운드 로빈 DNS 구성을 보여 주는 다이어그램.":::
   <이 다이어그램은 단일 가상 서버(왼쪽), 세 개의 IP 주소(가운데 열) 및 세 개의 클라이언트 인터페이스(오른쪽 열)의 세 가지 범주 요소 간의 연결을 보여 줍니다. "vserver1"이라는 레이블이 지정된 왼쪽의 단일 원은 IP 주소(10.0.0.10, 10.0.0.11, 10.0.0.12)가 있는 레이블이 지정된 세 개의 원을 가리키는 화살표로 연결됩니다. vserver 원에서 세 개의 IP 원으로 가는 화살표에는 "A"라는 캡션이 있습니다. 각 IP 주소 원은 클라이언트 인터페이스로 레이블이 지정된 원에 두 개의 화살표로 연결됩니다. IP가 10.0.0.10인 원은 "vs1-client-IP-10"에 연결되고, IP가 10.0.0.11인 원은 "vs1-client-IP-11"에 연결되고, IP가 10.0.0.12인 원은 "vs1-client-IP-11"에 연결됩니다. IP 주소 원과 클라이언트 인터페이스 원 사이는 두 개의 화살표로 연결됩니다. 하나는 IP 주소 원에서 클라이언트 인터페이스 원을 가리키는 "PTR"로 레이블이 지정된 화살표이고 다른 하나는 클라이언트 인터페이스 원에서 IP 주소 원을 가리키는 "A"로 레이블이 지정된 화살표입니다.> :::image-end:::

각 클라이언트 측 IP 주소에는 클러스터에서 내부적으로 사용할 수 있도록 고유한 이름이 있어야 합니다. (이 다이어그램에서 클라이언트 IP는 명확성을 위해 vs1-client-IP-*로 명명되었지만, 프로덕션에서는 client*와 같이 좀 더 간결하게 사용해야 합니다.)

클라이언트는 vserver 이름을 서버 인수로 사용하여 클러스터를 탑재합니다.

DNS 서버의 ``named.conf`` 파일을 수정하여 vserver에 대한 쿼리의 순환 순서를 설정합니다. 이 옵션을 사용하면 사용 가능한 모든 값이 순환됩니다. 다음과 같은 명령문을 추가합니다.

```
options {
    rrset-order {
        class IN A name "vserver1.example.com" order cyclic;
    };
};
```

다음 ``nsupdate`` 명령은 DNS를 올바르게 구성하는 예제를 제공합니다.

```
update add vserver1.example.com. 86400 A 10.0.0.10
update add vserver1.example.com. 86400 A 10.0.0.11
update add vserver1.example.com. 86400 A 10.0.0.12
update add vs1-client-IP-10.example.com. 86400 A 10.0.0.10
update add vs1-client-IP-11.example.com. 86400 A 10.0.0.11
update add vs1-client-IP-12.example.com. 86400 A 10.0.0.12
update add 10.0.0.10.in-addr.arpa. 86400 PTR vs1-client-IP-10.example.com
update add 11.0.0.10.in-addr.arpa. 86400 PTR vs1-client-IP-11.example.com
update add 12.0.0.10.in-addr.arpa. 86400 PTR vs1-client-IP-12.example.com
```

### <a name="enable-dns-in-the-cluster"></a>클러스터에서 DNS를 사용하도록 설정

**클러스터** > **관리 네트워크** 설정 페이지에서 클러스터가 사용하는 DNS 서버를 지정합니다. 해당 페이지의 설정은 다음과 같습니다.

* DNS 서버 주소
* DNS 도메인 이름
* DNS 검색 도메인

자세한 정보는 클러스터 구성 가이드의 [DNS 설정](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html#gui-dns>)을 참조하세요.

## <a name="next-steps"></a>다음 단계

Azure FXT Edge Filer 클러스터에 대한 마지막 기본 구성 단계입니다.

* [하드웨어 상태 모니터링](monitor.md)에서 시스템 LED 및 다른 표시기에 대해 알아봅니다.
* [클러스터 탑재](mount-clients.md)에서 클라이언트가 FXT Edge Filer 클러스터를 탑재하는 방법에 대해 알아봅니다.
* FXT Edge Filer클러스터의 운영 및 관리에 대한 자세한 정보는 [클러스터 구성 가이드](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html)를 참조하세요.

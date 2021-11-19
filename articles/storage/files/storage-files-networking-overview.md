---
title: Azure Files 네트워킹 고려 사항 | Microsoft Docs
description: Azure Files의 네트워킹 옵션에 대한 개요입니다.
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 07/02/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: d3f3bb466156b08ae90561d5e02e5b5ddf469755
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132518511"
---
# <a name="azure-files-networking-considerations"></a>Azure Files 네트워킹 고려 사항 
다음 두 가지 방법으로 Azure 파일 공유에 연결할 수 있습니다.

- SMB(서버 메시지 블록), NFS(네트워크 파일 시스템) 또는 FileREST 프로토콜을 통해 공유에 직접 액세스합니다. 이 액세스 패턴은 최대한 많은 온-프레미스 서버를 제거할 때 주로 사용됩니다.
- Azure 파일 동기화를 사용하여 온-프레미스 서버(또는 Azure VM)에서 Azure 파일 공유 캐시를 만들고, 사용 사례에 원하는 프로토콜(SMB, NFS, FTPS 등)을 사용하여 온-프레미스 서버에서 파일 공유의 데이터에 액세스합니다. 이 액세스 패턴은 온-프레미스 성능 및 클라우드 규모의 연결 가능한 서버리스 서비스(예: Azure Backup) 모두를 가장 효율적으로 결합하므로 편리합니다.

이 문서에서는 사용 사례에서 Azure 파일 동기화를 사용하지 않고 Azure 파일 공유에 직접 액세스하도록 호출하는 경우에 네트워킹을 구성하는 방법에 대해 중점적으로 설명합니다. Azure File Sync 배포의 네트워킹 고려 사항에 대한 자세한 내용은 [Azure File Sync 네트워킹 고려 사항](../file-sync/file-sync-networking-overview.md)을 참조하세요.

Azure 파일 공유에 대한 네트워킹 구성은 Azure 스토리지 계정에서 수행됩니다. 스토리지 계정은 여러 파일 공유뿐만 아니라 다른 스토리지 리소스(예: Blob 컨테이너 또는 큐)도 배포할 수 있는 공유 스토리지 풀을 나타내는 관리 구조입니다. 스토리지 계정은 파일 공유에 대한 네트워크 액세스를 보호하는 데 도움이 되는 여러 설정(네트워크 엔드포인트, 스토리지 계정 방화벽 설정 및 전송 중 암호화)을 공개합니다. 

> [!Important]  
> Azure Files는 여러 네트워크 라우팅 옵션을 지원합니다. 기본 옵션인 Microsoft 라우팅은 모든 Azure Files 구성에서 작동합니다. 인터넷 라우팅 옵션은 AD 도메인 가입 시나리오 또는 Azure 파일 동기화를 지원하지 않습니다.

이 개념 가이드를 읽기 전에 [Azure Files 배포 계획](storage-files-planning.md)을 읽는 것이 좋습니다.

:::row:::
    :::column:::
        <iframe width="560" height="315" src="https://www.youtube-nocookie.com/embed/jd49W33DxkQ" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
    :::column-end:::
    :::column:::
        이 비디오는 간단한 다섯 가지 단계를 통해 Azure 파일 공유를 정보 근로자 및 앱에 직접 안전하게 노출하는 방법에 대한 가이드 및 데모입니다. 아래 섹션에서는 비디오에서 참조되는 설명서에 대한 링크와 추가 컨텍스트를 제공합니다.
   :::column-end:::
:::row-end:::

## <a name="applies-to"></a>적용 대상
| 파일 공유 유형 | SMB | NFS |
|-|:-:|:-:|
| 표준 파일 공유(GPv2), LRS/ZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |
| 표준 파일 공유(GPv2), GRS/GZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |
| 프리미엄 파일 공유(FileStorage), LRS/ZRS | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png) |

## <a name="accessing-your-azure-file-shares"></a>Azure 파일 공유에 액세스
SMB Azure 파일 공유는 SMB 3.1.1 및 SMB 3.0이 있는 스토리지 계정의 퍼블릭 엔드포인트를 통해 즉시 액세스할 수 있습니다. 즉, 사용자의 로그온 ID로 권한이 부여된 요청과 같은 인증된 요청이 Azure 내부 또는 외부에서 안전하게 시작될 수 있습니다. NFS Azure 파일 공유는 퍼블릭 엔드포인트가 Azure 가상 네트워크로 제한된 경우 스토리지 계정의 퍼블릭 엔드포인트를 통해서만 액세스할 수 있습니다.

많은 환경에서 Azure 파일 공유에 추가 네트워크 구성을 적용할 수 있습니다.

- SMB 파일 공유와 관련하여 많은 조직과 ISP(인터넷 서비스 공급자)에서 SMB를 통해 통신하는 포트인 445 포트를 차단합니다. 이 방법은 더 이상 사용되지 않고 인터넷에 안전하지 않은 버전의 SMB 프로토콜에 대한 레거시 보안 지침에서 비롯됩니다. SMB 3.x는 인터넷 안전 프로토콜이지만 조직 또는 ISP 정책을 변경하지 못할 수 있습니다. 

- NFS 파일 공유와 관련하여 제한된 퍼블릭 엔드포인트 액세스는 탑재를 Azure 내부로만 제한합니다.

- 일부 조직에서는 결정적 경로를 따르려면 Azure로의 트래픽이 필요합니다.

### <a name="tunneling-traffic-over-a-virtual-private-network-or-expressroute"></a>가상 개인 네트워크 또는 ExpressRoute를 통해 트래픽 터널링
온-프레미스 네트워크와 Azure 간에 네트워크 터널을 설정하는 경우 온-프레미스 네트워크를 Azure에 있는 하나 이상의 가상 네트워크와 피어링합니다. [가상 네트워크](../../virtual-network/virtual-networks-overview.md) 또는 VNet은 온-프레미스에서 작동하는 기존 네트워크와 비슷합니다. Azure 스토리지 계정 또는 Azure VM과 마찬가지로 VNet은 리소스 그룹에 배포된 Azure 리소스입니다. 

Azure Files에서 지원하는 온-프레미스 워크스테이션/서버와 Azure SMB/NFS 파일 공유 간의 트래픽을 터널링하는 메커니즘은 다음과 같습니다.

- VPN Gateway는 인터넷을 통해 Azure 가상 네트워크와 대체 위치(예: 온-프레미스) 간에 암호화된 트래픽을 보내는 데 사용되는 특정 유형의 가상 네트워크 게이트웨이입니다. Azure VPN Gateway는 스토리지 계정 또는 다른 Azure 리소스와 함께 리소스 그룹에 배포할 수 있는 Azure 리소스입니다. VPN 게이트웨이는 다음 두 가지 유형의 연결을 공개합니다.
    - [P2S(지점 및 사이트 간) VPN](../../vpn-gateway/point-to-site-about.md) 게이트웨이 연결 - Azure와 개별 클라이언트 간의 VPN 연결입니다. 이 솔루션은 이동 중에 집, 커피숍 또는 호텔에서 Azure 파일 공유를 탑재할 수 있도록 하려는 재택 근무자와 같이 조직의 온-프레미스 네트워크에 속하지 않은 디바이스에 주로 유용합니다. Azure Files에서 P2S VPN 연결을 사용하려면 연결하려는 각 클라이언트에 대해 P2S VPN 연결을 구성해야 합니다. P2S VPN 연결의 배포를 간소화하려면 [Azure Files에서 사용하도록 Windows에서 P2S(지점 및 사이트 간) VPN 구성](storage-files-configure-p2s-vpn-windows.md) 및 [Azure Files에서 사용하도록 Linux에서 P2S(지점 및 사이트 간) VPN 구성](storage-files-configure-p2s-vpn-linux.md)을 참조하세요.
    - [S2S(사이트 간) VPN](../../vpn-gateway/design.md#s2smulti) - Azure와 조직의 네트워크 간의 VPN 연결입니다. S2S VPN 연결을 사용하면 VPN 연결을 Azure 파일 공유에 액세스해야 하는 모든 클라이언트 디바이스에 대해 구성하는 것이 아니라 조직의 네트워크에서 호스팅되는 VPN 서버 또는 디바이스에 대해 한 번만 구성할 수 있습니다. S2S VPN 연결의 배포를 간소화하려면 [Azure Files에서 사용하도록 S2S(사이트 간) VPN 구성](storage-files-configure-s2s-vpn.md)을 참조하세요.
- [ExpressRoute](../../expressroute/expressroute-introduction.md) - 이를 사용하면 인터넷을 트래버스하지 않는 온-프레미스 네트워크와 Azure 간에 정의된 경로를 만들 수 있습니다. ExpressRoute는 온-프레미스 데이터 센터와 Azure 간의 전용 경로를 제공하므로 네트워크 성능을 고려할 때 ExpressRoute가 유용할 수 있습니다. ExpressRoute는 조직의 정책 또는 규정 요구 사항에서 클라우드의 리소스에 대한 결정적 경로가 필요한 경우에도 유용한 옵션입니다.

Azure 파일 공유에 액세스하는 데 사용하는 터널링 방법에 관계없이 스토리지 계정에 대한 트래픽이 일반 인터넷 연결이 아니라 터널을 통해 이동하도록 하는 메커니즘이 필요합니다. 스토리지 계정의 퍼블릭 엔드포인트로 라우팅하는 것이 기술적으로 가능하지만, 스토리지 계정은 언제든지 스토리지 클러스터 간에 이동할 수 있으므로 지역의 Azure 스토리지 클러스터에 대한 모든 IP 주소를 하드 코딩해야 합니다. 또한 이 경우 항상 새 클러스터가 추가되므로 IP 주소 매핑을 지속적으로 업데이트해야 합니다.

스토리지 계정의 IP 주소를 VPN 라우팅 규칙에 하드 코딩하는 대신, Azure 가상 네트워크의 주소 공간에 있는 IP 주소를 스토리지 계정에 제공하는 프라이빗 엔드포인트를 사용하는 것이 좋습니다. Azure로의 터널을 만들면 온-프레미스 네트워크와 하나 이상의 가상 네트워크 간에 피어링이 설정되므로 이를 통해 지속적인 방법으로 올바르게 라우팅할 수 있습니다.

### <a name="private-endpoints"></a>프라이빗 엔드포인트
스토리지 계정에 대한 기본 퍼블릭 엔드포인트 외에도 Azure Files는 하나 이상의 프라이빗 엔드포인트를 포함할 수 있는 옵션을 제공합니다. 프라이빗 엔드포인트는 Azure 가상 네트워크 내에서만 액세스할 수 있는 엔드포인트입니다. 스토리지 계정에 대한 프라이빗 엔드포인트를 만들면 스토리지 계정은 가상 네트워크의 주소 공간 내에서 개인 IP 주소를 가져옵니다. 이는 온-프레미스 파일 서버 또는 NAS 디바이스에서 온-프레미스 네트워크의 전용 주소 공간 내에 있는 IP 주소를 받는 것과 비슷합니다. 

개별 프라이빗 엔드포인트는 특정 Azure 가상 네트워크 서브넷과 연결됩니다. 스토리지 계정에는 둘 이상의 가상 네트워크에서 프라이빗 엔드포인트가 있을 수 있습니다.

Azure Files에서 프라이빗 엔드포인트를 사용하면 다음을 수행할 수 있습니다.
- 프라이빗 피어링을 통한 VPN 또는 ExpressRoute 연결을 사용하여 온-프레미스 네트워크에서 Azure 파일 공유에 안전하게 연결합니다.
- 퍼블릭 엔드포인트의 모든 연결을 차단하도록 스토리지 계정 방화벽을 구성하여 Azure 파일 공유를 보호합니다. 기본적으로 프라이빗 엔드포인트를 만드는 경우에도 퍼블릭 엔드포인트에 대한 연결이 차단되지 않습니다.
- 가상 네트워크(및 피어링 경계)에서 데이터 반출을 차단할 수 있도록 하여 가상 네트워크의 보안을 강화합니다.

프라이빗 엔드포인트를 만들려면 [Azure Files에 대한 프라이빗 엔드포인트 구성](storage-files-networking-endpoints.md)을 참조하세요.

### <a name="private-endpoints-and-dns"></a>프라이빗 엔드포인트 및 DNS
프라이빗 엔드포인트를 만드는 경우 기본적으로 `privatelink` 하위 도메인에 해당하는 프라이빗 DNS 영역을 만들거나 기존 영역을 업데이트합니다. 엄격히 말해, 프라이빗 DNS 영역을 만드는 것은 스토리지 계정에 대한 프라이빗 엔드포인트를 사용하는 데 필요하지 않지만, Active Directory 사용자 주체 권한으로 Azure 파일 공유를 탑재하거나 FileREST API에서 액세스하는 경우에는 일반적이고 명시적으로 필요합니다.

> [!Note]  
> 이 문서에서는 `core.windows.net` Azure 퍼블릭 지역에 대한 스토리지 계정 DNS 접미사를 사용합니다. 이는 Azure US Government 클라우드 및 Azure 중국 클라우드와 같은 Azure 소버린 클라우드에도 적용되며, 사용자 환경에 적합한 접미사로 바꾸기만 하면 됩니다. 

프라이빗 DNS 영역에서 `storageaccount.privatelink.file.core.windows.net`에 대한 A 레코드 및 `storageaccount.file.core.windows.net` 패턴을 따르는 스토리지 계정의 일반 이름에 대한 CNAME 레코드를 만듭니다. Azure 프라이빗 DNS 영역이 프라이빗 엔드포인트가 포함된 가상 네트워크에 연결되어 있으므로 Azure VM의 PowerShell에서 `Resolve-DnsName` cmdlet(또는 Windows 및 Linux의 `nslookup`)을 호출하여 DNS 구성을 관찰할 수 있습니다.

```powershell
Resolve-DnsName -Name "storageaccount.file.core.windows.net"
```

이 예제에서는 `storageaccount.file.core.windows.net` 스토리지 계정이 프라이빗 엔드포인트의 개인 IP 주소(`192.168.0.4`)로 확인됩니다.

```Output
Name                              Type   TTL   Section    NameHost
----                              ----   ---   -------    --------
storageaccount.file.core.windows. CNAME  29    Answer     csostoracct.privatelink.file.core.windows.net
net

Name       : storageaccount.privatelink.file.core.windows.net
QueryType  : A
TTL        : 1769
Section    : Answer
IP4Address : 192.168.0.4


Name                   : privatelink.file.core.windows.net
QueryType              : SOA
TTL                    : 269
Section                : Authority
NameAdministrator      : azureprivatedns-host.microsoft.com
SerialNumber           : 1
TimeToZoneRefresh      : 3600
TimeToZoneFailureRetry : 300
TimeToExpiration       : 2419200
DefaultTTL             : 300
```

온-프레미스에서 동일한 명령을 실행하면 동일한 스토리지 계정 이름이 스토리지 계정의 공용 IP 주소로 확인됩니다. `storageaccount.file.core.windows.net`은 `storageaccount.privatelink.file.core.windows.net`에 대한 CNAME 레코드이며, 스토리지 계정을 호스팅하는 Azure 스토리지 클러스터에 대한 CNAME 레코드입니다.

```Output
Name                              Type   TTL   Section    NameHost
----                              ----   ---   -------    --------
storageaccount.file.core.windows. CNAME  60    Answer     storageaccount.privatelink.file.core.windows.net
net
storageaccount.privatelink.file.c CNAME  60    Answer     file.par20prdstr01a.store.core.windows.net
ore.windows.net

Name       : file.par20prdstr01a.store.core.windows.net
QueryType  : A
TTL        : 60
Section    : Answer
IP4Address : 52.239.194.40
```

이는 스토리지 계정에서 퍼블릭 엔드포인트와 하나 이상의 프라이빗 엔드포인트를 모두 공개할 수 있다는 사실을 반영합니다. 스토리지 계정 이름이 프라이빗 엔드포인트의 개인 IP 주소로 확인되도록 하려면 온-프레미스 DNS 서버의 구성을 변경해야 합니다. 이 작업은 다음과 같이 여러 가지 방법으로 수행할 수 있습니다.

- `storageaccount.file.core.windows.net`이 원하는 프라이빗 엔드포인트의 개인 IP 주소로 확인되도록 클라이언트에서 호스트 파일을 수정합니다. 이는 프로덕션 환경에 추천되지 않습니다. Azure 파일 공유를 탑재하려는 모든 클라이언트에 대해 이러한 변경을 수행해야 하며, 스토리지 계정 또는 프라이빗 엔드포인트에 대한 변경이 자동으로 처리되지 않기 때문입니다.
- 온-프레미스 DNS 서버에서 `storageaccount.file.core.windows.net`에 대한 A 레코드를 만듭니다. 이는 온-프레미스 환경의 클라이언트에서 각 클라이언트를 구성하지 않고도 스토리지 계정을 자동으로 확인할 수 있다는 이점이 있지만, 이 솔루션은 변경 내용을 반영하지 않으므로 호스트 파일을 수정하는 것과 비슷하게 불안정합니다. 이 솔루션은 불안정하지만 일부 환경에는 가장 적합할 수 있습니다.
- `core.windows.net` 영역을 온-프레미스 DNS 서버에서 Azure 프라이빗 DNS 영역으로 전달합니다. Azure 프라이빗 DNS 호스트는 Azure 프라이빗 DNS 영역에 연결된 가상 네트워크 내에서만 액세스할 수 있는 특수 IP 주소(`168.63.129.16`)를 통해 연결할 수 있습니다. 이 제한을 해결하려면 `core.windows.net`을 Azure 프라이빗 DNS 영역으로 전달하는 추가 DNS 서버를 가상 네트워크 내에서 실행할 수 있습니다. 이 설정을 간소화하기 위해 DNS 서버를 Azure 가상 네트워크에 자동으로 배포하고 원하는 대로 구성할 수 있는 PowerShell cmdlet을 제공했습니다. DNS 전달을 설정하는 방법에 대한 자세한 내용은 [Azure Files를 사용하여 DNS 구성](storage-files-networking-dns.md)을 참조하세요.

## <a name="storage-account-firewall-settings"></a>스토리지 계정 방화벽 설정
방화벽은 스토리지 계정의 퍼블릭 엔드포인트에 액세스할 수 있는 요청을 제어하는 네트워크 정책입니다. 스토리지 계정 방화벽을 사용하면 스토리지 계정의 퍼블릭 엔드포인트에 대한 액세스를 특정 IP 주소/범위 또는 가상 네트워크로 제한할 수 있습니다. 일반적으로 스토리지 계정에 대한 대부분의 방화벽 정책은 하나 이상의 가상 네트워크에 대한 네트워킹 액세스를 제한합니다. 

스토리지 계정에 대한 액세스를 가상 네트워크로 제한하는 두 가지 방법은 다음과 같습니다.
- 스토리지 계정에 대한 하나 이상의 프라이빗 엔드포인트를 만들고, 모든 액세스를 퍼블릭 엔드포인트로 제한합니다. 이렇게 하면 원하는 가상 네트워크 내에서 시작된 트래픽만 스토리지 계정 내의 Azure 파일 공유에 액세스할 수 있습니다.
- 퍼블릭 엔드포인트를 하나 이상의 가상 네트워크로 제한합니다. 이는 *서비스 엔드포인트* 라는 가상 네트워크의 기능을 사용하여 작동합니다. 서비스 엔드포인트를 통해 스토리지 계정으로의 트래픽을 제한하는 경우에도 공용 IP 주소를 통해 스토리지 계정에 액세스하게 됩니다.

> [!NOTE]
> NFS 파일 공유는 가상 네트워크를 통해서만 스토리지 계정의 퍼블릭 엔드포인트에 액세스할 수 있습니다. NFS 공유는 스토리지 계정의 프라이빗 엔드포인트에 자유롭게 액세스할 수 있습니다.

스토리지 계정 방화벽을 구성하는 방법에 대한 자세한 내용은 [Azure Storage 방화벽 및 가상 네트워크 구성](../common/storage-network-security.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)을 참조하세요.

## <a name="see-also"></a>참고 항목
- [Azure Files 개요](storage-files-introduction.md)
- [Azure 파일 배포에 대한 계획](storage-files-planning.md)

---
title: Azure HPC Cache NFS 스토리지 대상 문제 해결
description: NFS 스토리지 대상을 만들 때 오류를 발생시킬 수 있는 구성 오류 및 기타 문제를 방지하고 해결하기 위한 팁
author: ekpgh
ms.service: hpc-cache
ms.topic: troubleshooting
ms.date: 03/18/2020
ms.author: v-erkel
ms.openlocfilehash: a8fb169db79281240e2aab854bc6079a60054625
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/24/2021
ms.locfileid: "122771448"
---
# <a name="troubleshoot-nas-configuration-and-nfs-storage-target-issues"></a>NAS 구성 및 NFS 스토리지 대상 문제 해결

이 문서에서는 Azure HPC Cache에서 NFS 스토리지 시스템을 스토리지 대상으로 추가할 수 없도록 하는 몇 가지 일반적인 구성 오류 및 기타 문제에 대한 솔루션을 제공합니다.

이 문서에서는 포트를 확인하는 방법 및 NAS 시스템에 대한 루트 액세스를 사용하도록 설정하는 방법에 대한 자세한 정보도 제공합니다. 또한 NFS 스토리지 대상을 만들 수 없도록 하는 일반적이지 않은 문제에 대한 자세한 정보도 제공합니다.

> [!TIP]
> 이 가이드를 사용하기 전에 [NFS 스토리지 대상의 필수 구성 요소](hpc-cache-prerequisites.md#nfs-storage-requirements)를 읽어 보세요.

문제에 대한 솔루션이 여기에 포함되지 않은 경우 Microsoft 서비스 및 지원에서 사용자와 협력하여 문제를 조사하고 해결할 수 있도록 [지원 티켓을 여세요](hpc-cache-support-ticket.md).

## <a name="check-port-settings"></a>포트 설정 확인

Azure HPC Cache에는 백 엔드 NAS 스토리지 시스템의 여러 UDP/TCP 포트에 대한 읽기/쓰기 권한이 필요합니다. NAS 시스템에서 이러한 포트에 액세스할 수 있는지 확인하고 스토리지 시스템과 캐시 서브넷 사이의 방화벽을 통해 이러한 포트에 대한 트래픽이 허용되는지도 확인합니다. 데이터 센터에서 이 구성을 확인하려면 방화벽 및 네트워크 관리자와 협력해야 할 수 있습니다.

포트는 스토리지 시스템의 공급업체에 따라 달라지므로 스토리지 대상을 설정할 때 시스템의 요구 사항을 확인해야 합니다.

일반적으로 캐시는 다음 포트에 액세스해야 합니다.

| 프로토콜 | 포트  | 서비스  |
|----------|-------|----------|
| TCP/UDP  | 111   | rpcbind  |
| TCP/UDP  | 2049  | NFS      |
| TCP/UDP  | 4045  | nlockmgr |
| TCP/UDP  | 4046  | mountd   |
| TCP/UDP  | 4047  | 상태   |

시스템에 필요한 특정 포트를 알아보려면 다음 ``rpcinfo`` 명령을 사용하세요. 아래 명령은 포트를 나열하고 관련 결과를 테이블 형식으로 지정합니다. *<storage_IP>* 대신 시스템의 IP 주소를 사용하세요.

NFS 인프라가 설치된 모든 Linux 클라이언트에서 이 명령을 실행할 수 있습니다. 클러스터 서브넷 내에서 클라이언트를 사용하는 경우 이 명령은 서브넷과 스토리지 시스템 간의 연결을 확인하는 데도 도움이 될 수 있습니다.

```bash
rpcinfo -p <storage_IP> |egrep "100000\s+4\s+tcp|100005\s+3\s+tcp|100003\s+3\s+tcp|100024\s+1\s+tcp|100021\s+4\s+tcp"| awk '{print $4 "/" $3 " " $5}'|column -t
```

``rpcinfo`` 쿼리에서 반환된 모든 포트가 Azure HPC Cache의 서브넷에서 무제한 트래픽을 허용하는지 확인합니다.

이러한 설정은 NAS 자체에서 확인하고 스토리지 시스템과 캐시 서브넷 사이의 방화벽에서도 확인합니다.

## <a name="check-root-access"></a>루트 액세스 확인

Azure HPC Cache에서 스토리지 대상을 만들려면 스토리지 시스템의 내보내기에 액세스해야 합니다. 특히 내보내기를 사용자 ID 0으로 탑재합니다.

스토리지 시스템마다 다른 방법을 사용하여 이 액세스를 사용하도록 설정합니다.

* Linux 서버는 일반적으로 ``/etc/exports``의 내보낸 경로에 ``no_root_squash``를 추가합니다.
* NetApp 및 EMC 시스템은 일반적으로 특정 IP 주소 또는 네트워크에 연결된 내보내기 규칙을 사용하여 액세스를 제어합니다.

내보내기 규칙을 사용하는 경우 캐시가 캐시 서브넷의 여러 IP 주소를 사용할 수 있어야 합니다. 가능한 서브넷 IP 주소의 전체 범위에서 액세스를 허용합니다.

> [!NOTE]
> 캐시에는 백 엔드 스토리지 시스템에 대한 루트 액세스 권한이 필요하지만 캐시를 통해 연결하는 클라이언트에 대한 액세스를 제한할 수 있습니다. 자세한 내용은 [클라이언트 액세스 제어](access-policies.md#root-squash)를 참조하세요.

NAS 스토리지 공급업체와 협력하여 캐시에 적절한 수준의 액세스 권한을 사용하도록 설정합니다.

### <a name="allow-root-access-on-directory-paths"></a>디렉터리 경로에서 루트 액세스 허용
<!-- linked in prereqs article -->

계층적 디렉터리를 내보내는 NAS 시스템의 경우 Azure HPC Cache에 각 내보내기 수준에 대한 루트 액세스 권한이 필요합니다.

예를 들어 시스템에 다음과 같은 세 가지 내보내기가 표시될 수 있습니다.

* ``/ifs``
* ``/ifs/accounting``
* ``/ifs/accounting/payroll``

내보내기 ``/ifs/accounting/payroll``은 ``/ifs/accounting``의 자식이고, ``/ifs/accounting`` 자체는 ``/ifs``의 자식입니다.

``payroll`` 내보내기를 HPC Cache 스토리지 대상으로 추가하면 캐시는 실제로 ``/ifs/``를 탑재하고 여기에서 급여 디렉터리에 액세스합니다. 따라서 Azure HPC Cache에서 ``/ifs/accounting/payroll`` 내보내기에 액세스하려면 ``/ifs``에 대한 루트 액세스 권한이 필요합니다.

이 요구 사항은 스토리지 시스템에서 제공하는 파일 핸들을 사용하여 캐시에서 파일을 인덱싱하고 파일 충돌을 방지하는 방법과 관련이 있습니다.

계층적 내보내기를 사용하는 NAS 시스템은 파일이 다른 내보내기에서 검색되는 경우 동일한 파일에 대해 서로 다른 파일 핸들을 제공할 수 있습니다. 예를 들어 한 클라이언트는 ``/ifs/accounting``을 탑재하고 ``payroll/2011.txt`` 파일에 액세스할 수 있습니다. 다른 클라이언트는 ``/ifs/accounting/payroll``을 탑재하고 ``2011.txt`` 파일에 액세스합니다. 스토리지 시스템에서 파일 핸들을 할당하는 방법에 따라 두 클라이언트는 서로 다른 파일 핸들(각각 ``<mount2>/payroll/2011.txt`` 및 ``<mount3>/2011.txt``에 대한 파일 핸들)을 사용하는 동일한 파일을 받을 수 있습니다.

백 엔드 스토리지 시스템은 파일 핸들에 대한 내부 별칭을 유지하지만 Azure HPC Cache는 동일한 항목을 참조하는 인덱스의 파일 핸들을 구분할 수 없습니다. 따라서 캐시는 동일한 파일에 대해 서로 다른 쓰기를 캐시할 수 있으며, 해당 파일이 동일하다는 점을 알 수 없기 때문에 변경 내용을 잘못 적용할 수 있습니다.

여러 내보내기의 파일에서 발생할 수 있는 파일 충돌을 방지하기 위해 Azure HPC Cache는 가장 단순한 내보내기를 경로(예제의 ``/ifs``)에 자동으로 탑재하고 해당 내보내기에서 제공된 파일 핸들을 사용합니다. 여러 내보내기에서 동일한 기본 경로를 사용하는 경우 Azure HPC Cache에는 해당 경로에 대한 루트 액세스 권한이 필요합니다.

<!-- ## Enable export listing

The NAS must list its exports when the Azure HPC Cache queries it.

On most NFS storage systems, you can test this by sending the following query from a Linux client: ``showmount -e <storage IP address>``

Use a Linux client from the same virtual network as your cache, if possible.

If that command doesn't list the exports, the cache will have trouble connecting to your storage system. Work with your NAS vendor to enable export listing.  -->

## <a name="adjust-vpn-packet-size-restrictions"></a>VPN 패킷 크기 제한 조정
<!-- link in prereqs article and configuration article -->

캐시와 NAS 디바이스 사이에 VPN이 있는 경우 VPN은 전체 크기의 1500바이트 이더넷 패킷을 차단할 수 있습니다. NAS와 Azure HPC Cache 인스턴스 간의 대량 교환이 완료되지 않았는데 더 작은 업데이트가 예상대로 작동하는 경우 이 문제가 발생할 수 있습니다.

VPN 구성의 세부 정보를 알 수 없는 경우 시스템에 이 문제가 있는지 여부를 알 수 있는 간단한 방법이 없습니다. 다음은 이 문제를 확인하는 데 도움이 될 수 있는 몇 가지 방법입니다.

* VPN의 양쪽에서 패킷 스니퍼를 사용하여 성공적으로 전송되는 패킷을 검색합니다.
* VPN에서 ping 명령을 허용하는 경우 전체 크기의 패킷 보내기를 테스트할 수 있습니다.

  다음 옵션을 사용하여 VPN을 통해 NAS에 대해 ping 명령을 실행합니다. *<storage_IP>* 값 대신 스토리지 시스템의 IP 주소를 사용합니다.

   ```bash
   ping -M do -s 1472 -c 1 <storage_IP>
   ```

  명령의 옵션은 다음과 같습니다.

  * ``-M do`` - 조각화하지 않습니다.
  * ``-c 1`` - 패킷을 하나만 보냅니다.
  * ``-s 1472`` - 페이로드의 크기를 1472바이트로 설정합니다. 이더넷 오버헤드를 고려한 후 1500바이트 패킷의 최대 크기 페이로드입니다.

  성공적인 응답은 다음과 같습니다.

  ```bash
  PING 10.54.54.11 (10.54.54.11) 1472(1500) bytes of data.
  1480 bytes from 10.54.54.11: icmp_seq=1 ttl=64 time=2.06 ms
  ```

  ping이 1472바이트로 실패하는 경우 패킷 크기 문제가 있을 수 있습니다.

문제를 해결하려면 VPN에서 MSS 제한을 구성하여 원격 시스템이 최대 프레임 크기를 제대로 검색하도록 해야 합니다. 자세히 알아보려면 [VPN Gateway IPsec/IKE 매개 변수 설명서](../vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec)를 읽어 보세요.

경우에 따라 Azure HPC Cache의 MTU 설정을 1400으로 변경하면 도움이 될 수 있습니다. 그러나 캐시에서 MTU를 제한하는 경우 캐시와 상호 작용하는 백 엔드 스토리지 시스템 및 클라이언트에 대한 MTU 설정도 제한해야 합니다. 자세한 내용은 [추가 Azure HPC Cache 설정 구성](configuration.md#adjust-mtu-value)을 참조하세요.

## <a name="check-for-acl-security-style"></a>ACL 보안 스타일 확인

일부 NAS 시스템은 기존 POSIX 또는 UNIX 보안과 ACL(액세스 제어 목록)을 결합하는 하이브리드 보안 스타일을 사용합니다.

시스템에서 “ACL” 머리글자어를 포함하지 않고 UNIX 또는 POSIX로 해당 보안 스타일을 보고하는 경우 이 문제는 영향을 주지 않습니다.

ACL을 사용하는 시스템인 경우 Azure HPC Cache에서 파일 액세스를 제어하려면 사용자 관련 값을 추가로 추적해야 합니다. 이렇게 하려면 액세스 캐시를 사용하도록 설정합니다. 액세스 캐시를 설정할 수 있도록 사용자에게 표시되는 컨트롤은 없지만, 지원 티켓을 열어 캐시 시스템에서 영향을 받는 스토리지 대상에 대해 액세스 캐시 사용을 설정하도록 요청할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서 해결되지 않은 문제가 있는 경우 [고객 지원팀에 문의하여](hpc-cache-support-ticket.md) 전문가의 도움을 받으세요.

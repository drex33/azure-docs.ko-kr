---
title: Azure HPC Cache 클라이언트 연결 부하 분산
description: Azure HPC Cache 라운드 로빈 부하 분산을 위해 DNS 서버를 구성하는 방법
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/20/2021
ms.author: v-erkel
ms.openlocfilehash: 2a8c35db125b80223cbb4f07e8c01ca45428a3a8
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/14/2021
ms.locfileid: "130004942"
---
# <a name="load-balance-hpc-cache-client-traffic"></a>HPC Cache 클라이언트 트래픽 부하 분산

이 문서에서는 클라이언트 트래픽을 Azure HPC Cache 모든 탑재 지점으로 분산하는 몇 가지 기본 방법을 설명합니다.

모든 HPC Cache 세 개 이상의 서로 다른 IP 주소를 가지며, 처리량 값이 더 큰 캐시는 최대 12개의 주소를 가질 수 있습니다. 모든 IP 주소를 사용하여 Azure HPC Cache 모든 이점을 얻는 것이 중요합니다.

클라이언트 탑재 부하를 분산하는 다양한 옵션이 있습니다.

* 각 클라이언트에 대해 다른 탑재 IP를 수동으로 선택
* 클라이언트 탑재 스크립트에 IP 주소 회전 포함
* 사용 가능한 모든 주소(라운드 로빈 DNS) 간에 클라이언트 요청을 자동으로 라우팅하도록 DNS 시스템 구성

적합한 부하 분산 시스템은 워크플로의 복잡성, 캐시의 IP 주소 수 및 기타 많은 요인에 따라 달라집니다. 가장 적합한 방법을 결정하는 데 도움이 필요한 경우 Azure Advisor에 문의하세요.

## <a name="assign-ip-addresses-manually"></a>수동으로 IP 주소 할당

캐시의 탑재 IP 주소는 Azure Portal 캐시 **개요** 및 **탑재 지침** 페이지와 Azure CLI 또는 PowerShell을 사용하여 캐시를 만들 때 출력되는 성공 메시지에 표시됩니다.

**탑재 지침** 페이지를 사용하여 각 클라이언트에 대해 사용자 지정된 탑재 명령을 생성할 수 있습니다. 여러 명령을 만들 때  **캐시 탑재 주소** 값을 Select all.

자세한 내용은 [Azure HPC Cache 탑재를](hpc-cache-mount.md) 읽어 읽어 주십시오.

## <a name="use-scripted-load-balancing"></a>스크립팅된 부하 분산 사용

사용 가능한 IP 주소 간에 클라이언트 탑재를 프로그래밍 방식으로 회전하는 방법에는 여러 가지가 있습니다. 다음은 두 가지 예입니다.

### <a name="mount-command-script-cksum-example"></a>탑재 명령 스크립트 cksum 예제

이 예제 mount 명령은 해시 함수와 클라이언트 호스트 이름을 사용하여 ``cksum`` HPC Cache 사용 가능한 모든 IP 주소 간에 클라이언트 연결을 자동으로 분산합니다. 모든 클라이언트 컴퓨터에 고유한 호스트 이름이 있는 경우 각 클라이언트에서 이 명령을 실행하여 사용 가능한 모든 탑재 지점이 사용되도록 할 수 있습니다.

```bash
mount -o hard,proto=tcp,mountproto=tcp,retry=30 $(X=(10.0.0.{1..3});echo ${X[$(($(hostname|cksum|cut -f 1 -d ' ')%3))]}):/${NAMESPACE} /mnt
```

워크플로에서 이 예제를 사용하려면 다음 용어를 사용자 지정합니다.

* ```X=```식에서 정렬된 순서대로 캐시의 모든 탑재 주소에 대한 공백으로 구분된 목록을 사용합니다.

  식은 ``(X=(10.0.0.{7..9})`` 변수 X를 {10.0.0.7, 10.0.0.8, 10.0.0.9}의 탑재 주소 집합으로 설정합니다. 캐시의 기본 IP 주소와 캐시 개요 페이지에 표시된 정확한 주소를 사용합니다. 주소가 연속되지 않으면 모두 숫자 순서로 나열합니다.

* ```%3```용어로 캐시에 있는 실제 탑재 IP 주소 수(일반적으로 3, 6, 9 또는 12)를 사용합니다.

  예를 들어 ``%9`` 캐시가 9개의 클라이언트 탑재 IP 주소를 노출하는 경우 를 사용합니다.

* 식의 경우 ``${NAMESPACE}`` 클라이언트가 액세스할 스토리지 대상 네임스페이스 경로를 사용합니다.

  정의한 변수(예제에서는 *NAMESPACE)를* 사용하거나 리터럴 값을 대신 전달할 수 있습니다.
  
  이 섹션의 끝에 있는 명령 예제에서는 네임스페이스 경로 에 리터럴 값을 ``/blob-target-1`` 사용합니다.

* 클라이언트 컴퓨터에서 사용자 지정 로컬 경로를 사용하려면 값을 ``/mnt`` 원하는 경로로 변경합니다.

다음은 채워진 클라이언트 탑재 명령의 예입니다.

```bash
mount -o hard,proto=tcp,mountproto=tcp,retry=30 $(X=(10.7.0.{1..3});echo ${X[$(($(hostname|cksum|cut -f 1 -d ' ')%3))]}):/blob-target-1 /hpc-cache/blob1 
```

### <a name="round-robin-function-example"></a>라운드 로빈 함수 예제

이 코드 예제에서는 클라이언트 IP 주소를 임의로 지정 요소로 사용하여 클라이언트를 HPC Cache 사용 가능한 모든 IP 주소에 배포합니다.

```bash
function mount_round_robin() {

  # to ensure the clients are spread out somewhat evenly the default
  # mount point is based on this client's IP octet4 % number of HPC cache mount IPs.

  declare -a MOUNT_IPS="($(echo ${NFS_IP_CSV} | sed "s/,/ /g"))"
  HASH=$(hostname | cksum | cut -f 1 -d ' ')
  DEFAULT_MOUNT_INDEX=$((${HASH} % ${#MOUNT_IPS[@]}))
  ROUND_ROBIN_IP=${MOUNT_IPS[${DEFAULT_MOUNT_INDEX}]}

  DEFAULT_MOUNT_POINT="${BASE_DIR}/default"

  # no need to write again if it is already there
  if ! grep --quiet "${DEFAULT_MOUNT_POINT}" /etc/fstab; then
      echo "${ROUND_ROBIN_IP}:${NFS_PATH} ${DEFAULT_MOUNT_POINT} nfs hard,proto=tcp,mountproto=tcp,retry=30 0 0" >> /etc/fstab
      mkdir -p "${DEFAULT_MOUNT_POINT}"
      chown nfsnobody:nfsnobody "${DEFAULT_MOUNT_POINT}"
  fi
  if ! grep -qs "${DEFAULT_MOUNT_POINT} " /proc/mounts; then
      retrycmd_if_failure 12 20 mount "${DEFAULT_MOUNT_POINT}" || exit 1
  fi
}
```

## <a name="use-dns-load-balancing"></a>DNS 부하 분산 사용

이 섹션에서는 Azure HPC Cache 모든 탑재 지점에 클라이언트 연결을 배포하도록 DNS 시스템을 구성하는 기본 사항에 대해 설명합니다. 이 메서드는 각 클라이언트가 생성하는 트래픽 양을 고려하지 않지만 클라이언트가 하나 또는 두 개만 사용하는 대신 모든 캐시의 인터페이스에 균등하게 분산되도록 합니다.

이 문서에는 Azure 환경에서 클라이언트에 대한 DNS 서버를 설정하고 관리하기 위한 지침이 포함되어 있지 않습니다.

NFS 프로토콜 및 IP 주소를 사용하여 클라이언트를 탑재하기 위해 DNS가 필요하지 않습니다. IP  주소 대신 도메인 이름을 사용하여 하드웨어 NAS 시스템에 연결하려는 경우 또는 워크플로에 특정 고급 프로토콜 설정이 포함된 경우 DNS가 필요합니다.

클라이언트에 주소를 배포하는 데 사용하는 DNS 시스템은 HPC Cache 액세스할 필요가 없습니다. 경우에 따라 캐시 자체에 사용자 지정 DNS 시스템을 사용하려고 할 수 있지만 해당 시스템을 구성하는 것은 이러한 종류의 클라이언트 라운드 로빈 시스템을 설정하는 것보다 훨씬 더 복잡합니다. [ *HPC Cache* DNS 서버를](configuration.md#set-a-custom-dns-configuration) 사용자 지정 시스템으로 변경하려는 경우 Azure 지원을 참조해야 합니다.

### <a name="configure-round-robin-distribution-for-cache-mount-points"></a>캐시 탑재 지점에 대한 라운드 로빈 배포 구성

RRDNS(라운드 로빈 DNS) 시스템은 클라이언트 요청을 여러 주소 간에 자동으로 라우팅합니다.

이 시스템을 설정하려면 DNS 서버의 구성 파일을 사용자 지정하여 HPC Cache 주 도메인 주소에 탑재 요청을 받을 때 모든 HPC Cache 시스템의 탑재 지점 간에 트래픽을 할당하도록 해야 합니다. 클라이언트는 도메인 이름을 서버 인수로 사용하여 HPC Cache 탑재하고 다음 탑재 IP로 자동으로 라우팅됩니다.

RRDNS를 구성하는 두 가지 주요 단계가 있습니다.

1. DNS 서버의 ``named.conf`` 파일을 수정하여 HPC Cache 쿼리에 대한 순환 순서를 설정합니다. 이 옵션을 사용하면 서버가 사용 가능한 모든 IP 값을 순환합니다. 다음과 같은 명령문을 추가합니다.

   ```bash
   options {
       rrset-order {
           class IN A name "hpccache.contoso.com" order cyclic;
       };
   };
   ```

1. 다음 예제와 같이 사용 가능한 각 IP 주소에 대한 PTR(레코드 및 포인터) 레코드를 구성합니다.

   이러한 명령은 도메인 이름 hpccache.contoso.com 세 개의 ``nsupdate`` 탑재 주소(10.0.0.10, 10.0.0.11 및 10.0.0.12)가 있는 HPC Cache 대해 DNS를 올바르게 구성하는 예제를 제공합니다.

   ```bash
   update add hpccache.contoso.com. 86400 A 10.0.0.10
   update add hpccache.contoso.com. 86400 A 10.0.0.11
   update add hpccache.contoso.com. 86400 A 10.0.0.12
   update add client-IP-10.contoso.com. 86400 A 10.0.0.10
   update add client-IP-11.contoso.com. 86400 A 10.0.0.11
   update add client-IP-12.contoso.com. 86400 A 10.0.0.12
   update add 10.0.0.10.in-addr.arpa. 86400 PTR client-IP-10.contoso.com
   update add 11.0.0.10.in-addr.arpa. 86400 PTR client-IP-11.contoso.com
   update add 12.0.0.10.in-addr.arpa. 86400 PTR client-IP-12.contoso.com
   ```

   이러한 명령은 각 HPC Cache 탑재 주소에 대한 A 레코드를 만들고 역방향 DNS 검사를 적절하게 지원하도록 포인터 레코드를 설정합니다.

   아래 다이어그램은 이 구성의 기본 구조를 보여 줍니다.

   :::image type="complex" source="media/round-robin-dns-diagram-hpc.png" alt-text="클라이언트 탑재 지점 DNS 구성을 보여 주는 다이어그램":::
   <다이어그램은 단일 HPC Cache 도메인 이름(왼쪽), 3개의 IP 주소(가운데 열) 및 3개의 내부 사용 역방향 DNS 클라이언트 인터페이스(오른쪽 열)의 세 가지 요소 범주 간의 연결을 보여 제공합니다. "hpccache.contoso.com"라는 레이블이 붙은 왼쪽의 단일 타원은 IP 주소로 레이블이 지정되는 세 개의 타원(10.0.0.10, 10.0.0.11 및 10.0.0.12)을 가리키는 화살표로 연결됩니다. hpccache.contoso.com 타원에서 3개의 IP 타원까지의 화살표에는 "A"라는 레이블이 지정됩니다. 각 IP 주소 타원은 클라이언트 인터페이스로 레이블이 지정되는 타원에 두 개의 화살표로 연결됩니다. IP 10.0.0.10이 있는 타원은 "client-IP-10.contoso.com"에 연결되고, IP 10.0.0.11이 있는 타원은 "client-IP-11.contoso.com"에 연결되고 IP가 10.0.0.12인 타원은 "client-IP-11.contoso.com"에 연결됩니다. IP 주소 타원과 클라이언트 인터페이스 타원 간의 연결은 두 개의 화살표입니다. 하나는 IP 주소 타원에서 클라이언트 인터페이스 타원을 가리키는 "PTR"이라는 레이블이 지정되고 다른 하나는 클라이언트 인터페이스 타원에서 IP 주소 oval까지 가리키는 "A"라는 레이블이 지정됩니다.> :::image-end:::

RRDNS 시스템을 구성한 후 탑재 명령에서 HPC Cache 주소를 확인하도록 클라이언트 컴퓨터에 지시합니다.

## <a name="next-steps"></a>다음 단계

* 클라이언트 부하 분산에 대한 도움말은 [지원 에 문의합니다.](hpc-cache-support-ticket.md)
* 데이터를 캐시의 스토리지 대상으로 이동하려면 [새 Azure Blob Storage 채우기](hpc-cache-ingest.md)를 참조하세요.

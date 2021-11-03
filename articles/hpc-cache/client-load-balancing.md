---
title: Azure HPC 캐시로 클라이언트 연결 부하 분산
description: Azure HPC 캐시의 라운드 로빈 부하 분산을 위해 DNS 서버를 구성 하는 방법
author: femila
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/20/2021
ms.author: femila
ms.openlocfilehash: d1346a6060fe1093d7dec37f6fbefb43bee800ab
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131019973"
---
# <a name="load-balance-hpc-cache-client-traffic"></a>HPC 캐시 클라이언트 트래픽 부하 분산

이 문서에서는 Azure HPC 캐시의 모든 탑재 점으로 클라이언트 트래픽을 분산 하는 몇 가지 기본적인 방법을 설명 합니다.

모든 HPC 캐시에는 3 개 이상의 서로 다른 IP 주소가 있으며 처리량 값이 큰 캐시에는 최대 12 개까지 사용할 수 있습니다. 모든 IP 주소를 사용 하 여 Azure HPC 캐시의 모든 이점을 활용 하는 것이 중요 합니다.

클라이언트 탑재를 부하 분산 하는 다양 한 옵션이 있습니다.

* 각 클라이언트에 대해 다른 탑재 IP를 수동으로 선택
* 클라이언트 탑재 스크립트에 IP 주소 회전 포함
* 클라이언트 요청을 사용 가능한 모든 주소 (라운드 로빈 DNS) 간에 자동으로 라우팅하도록 DNS 시스템 구성

사용자를 위한 적절 한 부하 분산 시스템은 워크플로의 복잡성, 캐시의 IP 주소 수, 기타 여러 요소에 따라 달라 집니다. 가장 적합 한 방법을 결정 하는 데 도움이 필요 하면 Azure advisor를 참조 하세요.

## <a name="assign-ip-addresses-manually"></a>수동으로 IP 주소 할당

캐시의 탑재 IP 주소는 Azure Portal의 캐시 **개요** 및 **탑재 명령** 페이지와 Azure CLI 또는 PowerShell을 사용 하 여 캐시를 만들 때 인쇄 되는 성공 메시지에 표시 됩니다.

**탑재 지침** 페이지를 사용 하 여 각 클라이언트에 대해 사용자 지정 된 탑재 명령을 생성할 수 있습니다. 여러 명령을 만들 때 모든  **캐시 탑재 주소** 값을 선택 합니다.

자세한 내용은 [AZURE HPC 캐시 탑재를](hpc-cache-mount.md) 참조 하세요.

## <a name="use-scripted-load-balancing"></a>스크립팅된 부하 분산 사용

사용 가능한 IP 주소 간에 클라이언트 탑재를 프로그래밍 방식으로 회전 하는 방법에는 여러 가지가 있습니다. 다음은 두 가지 예입니다.

### <a name="mount-command-script-cksum-example"></a>Mount 명령 스크립트 cksum 예제

이 예제 탑재 명령은 hash 함수와 ``cksum`` 클라이언트 호스트 이름을 사용 하 여 HPC 캐시에서 사용 가능한 모든 IP 주소 간에 클라이언트 연결을 자동으로 배포 합니다. 모든 클라이언트 컴퓨터에 고유한 호스트 이름이 있는 경우 각 클라이언트에서이 명령을 실행 하 여 사용 가능한 모든 탑재 지점이 사용 되는지 확인할 수 있습니다.

```bash
mount -o hard,proto=tcp,mountproto=tcp,retry=30 $(X=(10.0.0.{1..3});echo ${X[$(($(hostname|cksum|cut -f 1 -d ' ')%3))]}):/${NAMESPACE} /mnt
```

워크플로에서이 예제를 사용 하려면 다음과 같은 용어를 사용자 지정 합니다.

* 식에서 ```X=``` 캐시의 탑재 주소를 모두 공백으로 구분한 목록을 정렬 된 순서로 사용 합니다.

  식은 ``(X=(10.0.0.{7..9})`` X 변수를 탑재 주소 {10.0.0.7, 10.0.0.8, 10.0.0.9가} 집합으로 설정 합니다. 캐시의 기본 IP 주소와 캐시 개요 페이지에 표시 되는 정확한 주소를 사용 합니다. 주소가 연속 되어 있지 않으면 모두 숫자 순서로 나열 합니다.

* 용어에서 ```%3``` 캐시에 있는 실제 탑재 IP 주소 수를 사용 합니다 (일반적으로 3, 6, 9 또는 12).

  예를 들어 ``%9`` 캐시가 9 개의 클라이언트 탑재 IP 주소를 노출 하는 경우를 사용 합니다.

* 식의 경우 ``${NAMESPACE}`` 클라이언트가 액세스할 저장소 대상 네임 스페이스 경로를 사용 합니다.

  사용자가 정의한 변수 (예제에서는 *네임 스페이스* )를 사용 하거나 리터럴 값을 전달할 수 있습니다.
  
  이 섹션의 끝에 있는 명령 예제에서는 네임 스페이스 경로에 대해 리터럴 값을 사용 ``/blob-target-1`` 합니다.

* 클라이언트 컴퓨터에서 사용자 지정 로컬 경로를 사용 하려는 경우 값을 ``/mnt`` 원하는 경로로 변경 합니다.

채워진 클라이언트 탑재 명령의 예는 다음과 같습니다.

```bash
mount -o hard,proto=tcp,mountproto=tcp,retry=30 $(X=(10.7.0.{1..3});echo ${X[$(($(hostname|cksum|cut -f 1 -d ' ')%3))]}):/blob-target-1 /hpc-cache/blob1 
```

### <a name="round-robin-function-example"></a>라운드 로빈 함수 예제

이 코드 예제에서는 클라이언트 IP 주소를 임의로 선택할 요소로 사용 하 여 모든 HPC 캐시의 사용 가능한 IP 주소에 클라이언트를 배포 합니다.

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

이 섹션에서는 Azure HPC 캐시의 모든 탑재 점에 클라이언트 연결을 배포 하도록 DNS 시스템을 구성 하는 기본 사항을 설명 합니다. 이 메서드는 각 클라이언트에서 생성 하는 트래픽 양을 고려 하지 않지만 클라이언트는 하나 또는 두 개를 사용 하는 대신 캐시의 모든 인터페이스에 대해 균일 하 게 분산 되어 있는지 확인 합니다.

이 문서에는 Azure 환경에서 클라이언트에 대 한 DNS 서버를 설정 및 관리 하는 방법에 대 한 지침이 포함 되어 있지 않습니다.

DNS는 NFS 프로토콜 및 IP 주소를 사용 하 여 클라이언트를 탑재 하는 데 필요 하지 않습니다. DNS *는* IP 주소 대신 도메인 이름을 사용 하 여 하드웨어 NAS 시스템에 연결 하려는 경우 또는 워크플로에 특정 고급 프로토콜 설정이 포함 되어 있는 경우에 필요 합니다.

클라이언트에 주소를 배포 하는 데 사용 하는 DNS 시스템은 HPC 캐시에서 액세스할 필요가 없습니다. 캐시 자체에 사용자 지정 DNS 시스템을 사용 해야 하는 경우도 있지만이 시스템을 구성 하는 것은 이러한 종류의 클라이언트 라운드 로빈 시스템을 설정 하는 것 보다 훨씬 복잡 합니다. [ *HPC 캐시의* DNS 서버](configuration.md#set-a-custom-dns-configuration) 를 사용자 지정 시스템으로 변경 하는 것을 고려 하는 경우 Azure 지원을 참조 해야 합니다.

### <a name="configure-round-robin-distribution-for-cache-mount-points"></a>캐시 탑재 지점의 라운드 로빈 배포 구성

라운드 로빈 DNS(RRDNS) 시스템은 클라이언트 요청을 여러 주소 간에 자동으로 라우팅합니다.

이 시스템을 설정 하려면 DNS 서버의 구성 파일을 사용자 지정 해야 합니다. 이렇게 하면 HPC 캐시의 주 도메인 주소로 탑재 요청을 가져오는 경우 모든 HPC 캐시 시스템의 탑재 지점의 트래픽이 할당 됩니다. 클라이언트는 서버 인수로 해당 도메인 이름을 사용 하 여 HPC 캐시를 탑재 하 고 자동으로 다음 탑재 IP로 라우팅됩니다.

RRDNS를 구성하는 두 가지 주요 단계는 다음과 같습니다.

1. DNS 서버의 파일을 수정 ``named.conf`` 하 여 HPC 캐시로 쿼리의 순환 순서를 설정 합니다. 이 옵션을 선택하면 서버에서 사용 가능한 모든 IP 값을 순환합니다. 다음과 같은 명령문을 추가합니다.

   ```bash
   options {
       rrset-order {
           class IN A name "hpccache.contoso.com" order cyclic;
       };
   };
   ```

1. 다음 예제와 같이 사용 가능한 각 IP 주소에 대해 A 레코드 및 포인터(PTR) 레코드를 구성합니다.

   이러한 ``nsupdate`` 명령은 도메인 이름이 hpccache.contoso.com이 고 3 개의 탑재 주소 (10.0.0.10, 10.0.0.11)과 및 10.0.0.12)가 있는 HPC 캐시에 대해 DNS를 올바르게 구성 하는 예제를 제공 합니다.

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

   이러한 명령은 HPC 캐시의 탑재 주소 각각에 대해 A 레코드를 만들고 역방향 DNS 검사를 적절 하 게 지원 하도록 포인터 레코드를 설정 합니다.

   아래 다이어그램은 이 구성의 기본 구조를 보여줍니다.

   :::image type="complex" source="media/round-robin-dns-diagram-hpc.png" alt-text="클라이언트 탑재 지점 DNS 구성을 보여주는 다이어그램.":::
   다이어그램에는 단일 HPC 캐시 도메인 이름 (왼쪽), 3 개의 IP 주소 (중간 열) 및 세 개의 내부용 역방향 DNS 클라이언트 인터페이스 (오른쪽 열)와 같은 세 가지 범주의 요소 간 연결이 표시 <. 왼쪽에 "hpccache.contoso.com" 레이블이 지정 된 단일 타원이 IP 주소 (10.0.0.10, 10.0.0.11)과 및 10.0.0.12)로 레이블이 지정 된 3 개의 타원이 가리키는 화살표로 연결 됩니다. Hpccache.contoso.com oval에서 3 개의 IP 타원 까지의 화살표에는 "A" 레이블이 지정 됩니다. 각 IP 주소 타원은 클라이언트 인터페이스로 레이블이 지정된 타원에 두 개의 화살표로 연결됩니다. IP가 10.0.0.10인 타원은 "client-IP-10.contoso.com"에 연결되고, IP가 10.0.0.11인 타원은 "client-IP-11.contoso.com"에 연결되고, IP가 10.0.0.12인 타원은 "client-IP-11.contoso.com"에 연결됩니다. IP 주소 타원과 클라이언트 인터페이스 타원 사이는 두 개의 화살표로 연결됩니다. 하나는 IP 주소 타원에서 클라이언트 인터페이스 타원을 가리키는 "PTR"로 레이블이 지정된 화살표이고 다른 하나는 클라이언트 인터페이스 타원에서 IP 주소 타원을 가리키는 "A"로 레이블이 지정된 화살표입니다.> :::image-end:::

RRDNS 시스템이 구성 된 후 클라이언트 컴퓨터에서 해당 탑재 명령의 HPC 캐시 주소를 확인 하는 데 사용 하도록 지시 합니다.

## <a name="next-steps"></a>다음 단계

* 클라이언트 부하를 분산 하는 데 도움이 필요 하면 [지원 담당자에 게 문의 하세요](hpc-cache-support-ticket.md).
* 데이터를 캐시의 스토리지 대상으로 이동하려면 [새 Azure Blob Storage 채우기](hpc-cache-ingest.md)를 참조하세요.

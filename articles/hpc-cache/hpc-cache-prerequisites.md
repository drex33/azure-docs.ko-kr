---
title: Azure HPC Cache 필수 조건
description: Azure HPC Cache를 사용하기 위한 필수 조건
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 05/06/2021
ms.author: v-erkel
ms.openlocfilehash: 202f942bc4fb47b2c71802667e31e08de7cfe13e
ms.sourcegitcommit: b5508e1b38758472cecdd876a2118aedf8089fec
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2021
ms.locfileid: "113588816"
---
# <a name="prerequisites-for-azure-hpc-cache"></a>Azure HPC Cache의 필수 조건

Azure Portal을 사용하여 새 Azure HPC Cache를 만들기 전에 사용자 환경이 관련 요구 사항을 충족하도록 조치합니다.

## <a name="video-overviews"></a>동영상 개요

시스템의 구성 요소와 이들이 함께 작동하는 데 필요한 사항을 빠르게 살펴보는 다음 동영상을 시청하세요.

(시청할 동영상 이미지 또는 링크를 클릭하세요.)

* [작동 방식](https://azure.microsoft.com/resources/videos/how-hpc-cache-works/) - Azure HPC Cache가 스토리지 및 클라이언트와 상호 작용하는 방식 설명

  [![동영상 썸네일 이미지: Azure HPC Cache: 작동 방식(동영상 페이지를 방문하려면 클릭)](media/video-2-components.png)](https://azure.microsoft.com/resources/videos/how-hpc-cache-works/)  

* [필수 조건](https://azure.microsoft.com/resources/videos/hpc-cache-prerequisites/) - NAS 스토리지, Azure Blob Storage, 네트워크 액세스 및 클라이언트 액세스를 위한 요구 사항 설명

  [![동영상 썸네일 이미지: Azure HPC Cache: 필수 조건(동영상 페이지를 방문하려면 클릭)](media/video-3-prerequisites.png)](https://azure.microsoft.com/resources/videos/hpc-cache-prerequisites/)

이 문서의 나머지 부분에서 특정 권장 사항을 읽어 보세요.

## <a name="azure-subscription"></a>Azure 구독

유료 구독이 권장됩니다.

## <a name="network-infrastructure"></a>네트워크 인프라

캐시를 사용하려면 먼저 두 개의 네트워크 관련 필수 구성 요소를 설정해야 합니다.

* Azure HPC Cache 인스턴스의 전용 서브넷
* 캐시에서 스토리지 및 기타 리소스에 액세스할 수 있도록 하는 DNS 지원

### <a name="cache-subnet"></a>캐시 서브넷

Azure HPC Cache에는 다음을 특징으로 하는 전용 서브넷이 필요합니다.

* 서브넷은 사용 가능한 IP 주소가 64개 이상이어야 합니다.
* 서브넷은 클라이언트 머신과 같은 관련 서비스에 대해서도 다른 VM을 호스트할 수 없습니다.
* 여러 Azure HPC Cache 인스턴스를 사용하는 경우 각각에 자체 서브넷이 필요합니다.

가장 좋은 방법은 각 캐시에 대해 새 서브넷을 만드는 것입니다. 캐시를 만드는 과정의 일부로 새 가상 네트워크 및 서브넷을 만들 수 있습니다.

### <a name="dns-access"></a>DNS 액세스

가상 네트워크 외부의 리소스에 액세스하려면 캐시에 DNS가 필요합니다. 사용 중인 리소스에 따라 사용자 지정된 DNS 서버를 설정하고 해당 서버와 Azure DNS 서버 간에 전달을 구성해야 할 수도 있습니다.

* Azure Blob Storage 엔드포인트 및 기타 내부 리소스에 액세스하려면 Azure 기반 DNS 서버가 필요합니다.
* 온-프레미스 스토리지에 액세스하려면 스토리지 호스트 이름을 확인할 수 있는 사용자 지정 DNS 서버를 구성해야 합니다. 캐시를 만들기 전에 이 작업을 수행해야 합니다.

Blob Storage만 사용하는 경우 Azure에서 기본 제공하는 DNS 서버를 캐시에 사용할 수 있습니다. 그러나 Azure 외부에 있는 스토리지나 다른 리소스에 액세스해야 하는 경우 사용자 지정 DNS 서버를 만들고 Azure DNS 서버에 Azure 관련 확인 요청을 전달하도록 구성해야 합니다.

사용자 지정 DNS 서버를 사용하려면 캐시를 만들기 전에 다음 설정 단계를 수행해야 합니다.

* Azure HPC Cache를 호스트하는 가상 네트워크를 만듭니다.
* DNS 서버를 만듭니다.
* 캐시의 가상 네트워크에 DNS 서버를 할당합니다.

  Azure Portal에서 가상 네트워크에 DNS 서버를 추가하려면 다음 단계를 수행합니다.

  1. Azure Portal에서 가상 네트워크를 엽니다.
  1. 사이드바의 설정 메뉴에서 DNS 서버를 선택합니다.
  1. 사용자 지정을 선택합니다.
  1. 필드에 DNS 서버의 IP 주소를 입력합니다.

또한 단순형 DNS 서버를 사용하여 사용 가능한 모든 캐시 탑재 지점에서 발생하는 클라이언트 연결 부하를 분산할 수 있습니다.

[Azure 가상 네트워크의 리소스에 대한 이름 확인](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)에서 Azure 가상 네트워크 및 DNS 서버 구성에 대해 자세히 알아보세요.

## <a name="permissions"></a>사용 권한

캐시 만들기를 시작하기 전에 다음과 같은 권한 관련 필수 조건을 확인합니다.

* 캐시 인스턴스는 가상 NIC(네트워크 인터페이스)를 만들 수 있어야 합니다. 캐시를 만드는 사용자에게는 NIC를 만들기에 충분한 권한이 구독에 있어야 합니다.

* Blob Storage를 사용하는 경우 Azure HPC Cache에는 스토리지 계정에 액세스하기 위한 권한 부여가 필요합니다. Azure RBAC(역할 기반 액세스 제어)를 사용하여 Blob Storage에 대한 캐시 액세스를 제공합니다. 스토리지 계정 기여자 및 스토리지 Blob 데이터 기여자라는 두 개의 역할이 필요합니다.

  [스토리지 대상 추가](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account)의 지침에 따라 역할을 추가합니다.

## <a name="storage-infrastructure"></a>스토리지 인프라
<!-- heading is linked in create storage target GUI as aka.ms/hpc-cache-prereq#storage-infrastructure - make sure to fix that if you change the wording of this heading -->

캐시는 Azure Blob 컨테이너, NFS 하드웨어 스토리지 내보내기 및 NFS 탑재 ADLS Blob 컨테이너를 지원합니다. 캐시를 만든 후 스토리지 대상을 추가합니다.

캐시 크기는 지원할 수 있는 스토리지 대상 수를 결정합니다. 대부분의 캐시에 대해 최대 10개의 스토리지 대상 또는 가장 큰 크기에 대해 최대 20개의 스토리지 대상이 지원됩니다. 자세한 내용은 [스토리지 대상을 지원하도록 캐시 크기를 올바르게 조정](hpc-cache-add-storage.md#size-your-cache-correctly-to-support-your-storage-targets)을 참조하세요.

각 스토리지 유형에는 특정 필수 구성 요소가 있습니다.

### <a name="blob-storage-requirements"></a>Blob Storage 요구 사항

캐시에 Azure Blob Storage를 사용하려면 [Azure Blob Storage로 데이터 이동](hpc-cache-ingest.md)에서 설명한 대로 호환되는 스토리지 계정 및 빈 Blob 컨테이너 또는 Azure HPC Cache 형식의 데이터로 채워진 컨테이너가 필요합니다.

> [!NOTE]
> NFS 탑재 Blob Storage에는 다른 요구 사항이 적용됩니다. 자세한 내용은 [ADLS-NFS 스토리지 요구 사항](#nfs-mounted-blob-adls-nfs-storage-requirements)을 참조하세요.

스토리지 대상 추가를 시도하기 전에 계정을 만듭니다. 대상을 추가할 때 새 컨테이너를 만들 수 있습니다.

호환되는 스토리지 계정을 만들려면 다음 조합 중 하나를 사용합니다.

| 성능 | Type | 복제 | 액세스 계층 |
|--|--|--|--|
| Standard | StorageV2(범용 v2)| LRS(로컬 중복 스토리지), ZRS(영역 중복 스토리지) | 핫 |
| Premium | 블록 Blob | LRS(로컬 중복 스토리지) | 핫 |

스토리지 계정은 캐시의 프라이빗 서브넷에서 액세스할 수 있어야 합니다. 계정이 프라이빗 엔드포인트 또는 특정 가상 네트워크로 제한된 퍼블릭 엔드포인트를 사용하는 경우 캐시의 서브넷에서 액세스를 사용하도록 설정해야 합니다. (공개 퍼블릭 엔드포인트는 권장되지 않습니다.)

캐시와 동일한 Azure 지역에 있는 스토리지 계정을 사용하는 것이 좋습니다.

또한 위의 [권한](#permissions)에서 설명한 대로 Azure 스토리지 계정에 대한 캐시 애플리케이션 액세스 권한을 부여해야 합니다. [스토리지 대상 추가](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account)의 절차에 따라 캐시에 필요한 액세스 역할을 제공합니다. 스토리지 계정 소유자가 아닌 경우 소유자가 이 단계를 수행하도록 합니다.

### <a name="nfs-storage-requirements"></a>NFS 스토리지 요구 사항
<!-- linked from configuration.md and add storage -->

NFS 스토리지 시스템(예: 온-프레미스 하드웨어 NAS 시스템)을 사용하는 경우 관련 요구 사항을 충족하도록 조치합니다. 해당 설정을 확인하기 위해 스토리지 시스템(또는 데이터 센터) 담당 네트워크 관리자 또는 방화벽 관리자와 협력해야 할 수도 있습니다.

> [!NOTE]
> 캐시에 NFS 스토리지 시스템에 대한 액세스 권한이 없으면 스토리지 대상 만들기가 실패합니다.

[NAS 구성 문제 및 NFS 스토리지 대상 문제 해결](troubleshoot-nas.md)에 자세한 내용이 나와 있습니다.

* 네트워크 연결: Azure HPC Cache는 캐시 서브넷과 NFS 시스템의 데이터 센터 간의 고대역폭 네트워크 액세스를 필요로 합니다. [ExpressRoute](../expressroute/index.yml) 또는 유사한 액세스 수단을 사용하는 것이 좋습니다. VPN을 사용하는 경우 대량 패킷이 차단되지 않도록 TCP MSS를 1350에 고정하도록 구성해야 할 수도 있습니다. VPN 설정 문제 해결에 대한 더 많은 도움말은 [VPN 패킷 크기 제한](troubleshoot-nas.md#adjust-vpn-packet-size-restrictions)을 참조하세요.

* 포트 액세스: 캐시에는 스토리지 시스템의 특정 TCP/UDP 포트에 대한 액세스 권한이 필요합니다. 스토리지 유형에 따라 서로 다른 포트 요구 사항이 있습니다.

  스토리지 시스템의 설정을 확인하려면 다음 절차를 따르세요.

  * 스토리지 시스템에 `rpcinfo` 명령을 실행하여 필요한 포트를 확인합니다. 아래 명령은 포트를 나열하고 관련 결과를 테이블 형식으로 지정합니다. ( *<storage_IP>* 용어를 시스템의 IP 주소로 바꿉니다.)

    NFS 인프라가 설치된 모든 Linux 클라이언트에서 이 명령을 실행할 수 있습니다. 클러스터 서브넷 내에서 클라이언트를 사용하는 경우 이 명령은 서브넷과 스토리지 시스템 간의 연결을 확인하는 데도 도움이 될 수 있습니다.

    ```bash
    rpcinfo -p <storage_IP> |egrep "100000\s+4\s+tcp|100005\s+3\s+tcp|100003\s+3\s+tcp|100024\s+1\s+tcp|100021\s+4\s+tcp"| awk '{print $4 "/" $3 " " $5}'|column -t
    ```

  ``rpcinfo`` 쿼리에서 반환된 모든 포트가 Azure HPC Cache 서브넷의 무제한 트래픽을 허용하는지 확인합니다.

  * `rpcinfo` 명령을 사용할 수 없는 경우 일반적으로 사용되는 포트에서 인바운드 및 아웃바운드 트래픽을 허용하도록 합니다.

    | 프로토콜 | 포트  | 서비스  |
    |----------|-------|----------|
    | TCP/UDP  | 111   | rpcbind  |
    | TCP/UDP  | 2049  | NFS      |
    | TCP/UDP  | 4045  | nlockmgr |
    | TCP/UDP  | 4046  | mountd   |
    | TCP/UDP  | 4047  | 상태   |

    일부 시스템은 해당 서비스에 대해 서로 다른 포트 번호를 사용합니다. 이를 확인하려면 스토리지 시스템의 설명서를 참조하세요.

  * 방화벽 설정을 확인하여 필요한 모든 포트에서 트래픽을 허용하도록 합니다. 데이터 센터의 온-프레미스 방화벽뿐만 아니라 Azure에서 사용되는 방화벽도 확인해야 합니다.

* 루트 액세스(읽기/쓰기): 캐시는 사용자 ID 0으로 백 엔드 시스템에 연결합니다. 스토리지 시스템에서 다음 설정을 확인합니다.
  
  * `no_root_squash`을 사용하도록 설정합니다. 이 옵션을 사용하면 원격 루트 사용자가 루트 소유의 파일에 액세스할 수 있습니다.

  * 정책 내보내기를 확인하여 캐시 서브넷의 루트 액세스에 대한 제한을 포함하지 않도록 합니다.

  * 스토리지에 다른 내보내기의 하위 디렉터리인 내보내기가 있는 경우 캐시가 경로의 최하위 세그먼트에 대한 루트 액세스 권한을 갖도록 합니다. 자세한 내용은 NFS 스토리지 대상 문제 해결 문서의 [디렉터리 경로에 대한 루트 액세스](troubleshoot-nas.md#allow-root-access-on-directory-paths)를 참조하세요.

* NFS 백 엔드 스토리지는 호환되는 하드웨어/소프트웨어 플랫폼이어야 합니다. 자세한 내용은 Azure HPC Cache 팀에 문의하세요.

### <a name="nfs-mounted-blob-adls-nfs-storage-requirements"></a>NFS 탑재 Blob(ADLS-NFS) 스토리지 요구 사항

Azure HPC Cache는 NFS 프로토콜과 함께 탑재된 Blob 컨테이너를 스토리지 대상으로 사용할 수 있습니다.

[Azure Blob Storage에서 NFS 3.0 프로토콜 지원](../storage/blobs/network-file-system-protocol-support.md)에서 이 기능에 대해 자세히 알아보세요.

스토리지 계정 요구 사항은 ADLS-NFS Blob Storage 대상 및 표준 Blob Storage 대상에 따라 다릅니다. NFS 사용 스토리지 계정을 만들고 구성할 때에는 [NFS(네트워크 파일 시스템) 3.0 프로토콜을 사용하여 Blob Storage 탑재](../storage/blobs/network-file-system-protocol-support-how-to.md)의 지침을 주의를 기울여 따릅니다.

이는 관련 단계에 대한 일반적인 개요입니다. 해당 단계는 변경될 수 있으므로 항상 [ADLS-NFS 지침](../storage/blobs/network-file-system-protocol-support-how-to.md)에서 현재 세부 정보를 참조하세요.

1. 필요한 기능을 작업할 지역에서 사용할 수 있는지 확인합니다.

1. 구독에 NFS 프로토콜 기능을 사용하도록 설정합니다. 스토리지 계정을 만들기 ‘전에’ 이 작업을 수행합니다.

1. 스토리지 계정에 대한 보안 VNet(가상 네트워크)을 만듭니다. NFS 사용 스토리지 계정 및 Azure HPC Cache에 동일한 가상 네트워크를 사용해야 합니다. (캐시와 동일한 서브넷을 사용하지 마세요.)

1. 스토리지 계정을 만듭니다.

   * 표준 Blob Storage 계정에 스토리지 계정 설정을 사용하는 대신 [방법 문서](../storage/blobs/network-file-system-protocol-support-how-to.md)의 지침을 따릅니다. 지원되는 스토리지 계정 유형은 Azure 지역에 따라 다를 수 있습니다.

   * 네트워킹 섹션에서 직접 만든 보안 가상 네트워크의 프라이빗 엔드포인트를 선택하거나(권장), 보안 VNet에서 액세스가 제한된 퍼블릭 엔드포인트를 선택합니다.

   * NFS 액세스를 사용하도록 설정하는 고급 섹션을 완료해야 합니다.

   * 위의 [권한](#permissions)에서 설명한 대로 Azure 스토리지 계정에 대한 캐시 애플리케이션 액세스 권한을 부여해야 합니다. 스토리지 대상을 처음 만들 때 이 작업을 수행할 수 있습니다. [스토리지 대상 추가](hpc-cache-add-storage.md#add-the-access-control-roles-to-your-account)의 절차에 따라 캐시에 필요한 액세스 역할을 제공합니다.

     스토리지 계정 소유자가 아닌 경우 소유자가 이 단계를 수행하도록 합니다.

[Azure HPC Cache와 함께 NFS 탑재 Blob 스토리지 사용](nfs-blob-considerations.md)에서 Azure HPC Cache와 함께 ADLS-NFS 스토리지 대상 사용에 대해 자세히 알아봅니다.

## <a name="set-up-azure-cli-access-optional"></a>Azure CLI 액세스 설정(선택 사항)

Azure CLI(Azure 명령줄 인터페이스)에서 Azure HPC Cache를 만들거나 관리하려면 CLI 소프트웨어와 HPC Cache 확장을 설치해야 합니다. [Azure HPC Cache용 Azure CLI 설정](az-cli-prerequisites.md)의 지침을 따릅니다.

## <a name="next-steps"></a>다음 단계

* Azure Portal에서 [Azure HPC Cache 인스턴스 만들기](hpc-cache-create.md)

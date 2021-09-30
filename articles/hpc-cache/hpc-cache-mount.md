---
title: Azure HPC Cache 탑재
description: Azure HPC Cache 서비스에 클라이언트를 연결하는 방법
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 09/27/2021
ms.author: v-erkel
ms.openlocfilehash: ab66fc29a29461e6808686aa53b520144b39a1e1
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/29/2021
ms.locfileid: "129274200"
---
# <a name="mount-the-azure-hpc-cache"></a>Azure HPC Cache 마운트

캐시를 만든 후에는 NFS 클라이언트에서 간단한 `mount` 명령으로 캐시에 액세스할 수 있습니다. 이 명령은 Azure HPC Cache의 특정 스토리지 대상 경로를 클라이언트 머신의 로컬 디렉터리에 연결합니다.

mount 명령은 다음 요소로 구성됩니다.

* 캐시의 탑재 주소 중 하나(캐시 개요 페이지에 나열됨)
* 스토리지 대상에 대해 설정한 가상 네임스페이스 경로(캐시 네임스페이스 페이지에 나열됨)
* 클라이언트에서 사용할 로컬 경로
* 이 종류의 NFS 탑재 성공을 최적화하는 명령 매개 변수

캐시의 **탑재 지침** 페이지에서는 정보 및 권장 옵션을 자동으로 수집하고 복사할 수 있는 프로토타입 mount 명령을 만듭니다. 자세한 내용은 [탑재 지침 유틸리티 사용](#use-the-mount-instructions-utility)을 참조하세요.

## <a name="prepare-clients"></a>클라이언트 준비

이 섹션의 지침에 따라 클라이언트가 Azure HPC Cache를 탑재할 수 있는지 확인합니다.

### <a name="provide-network-access"></a>네트워크 액세스 제공

클라이언트 머신에 캐시의 가상 네트워크와 프라이빗 서브넷에 대한 네트워크 액세스 권한이 있어야 합니다.

예를 들어 동일한 가상 네트워크 내에서 클라이언트 VM을 만들거나 가상 네트워크의 엔드포인트, 게이트웨이 또는 기타 솔루션을 외부에서 액세스하는 데 사용합니다. 캐시 자체 이외의 다른 항목을 캐시 서브넷 내에서 호스트하면 안 됩니다.

### <a name="install-utilities"></a>유틸리티 설치

NFS mount 명령을 지원할 적절한 Linux 유틸리티 소프트웨어를 설치합니다.

* Red Hat Enterprise Linux 또는 SuSE의 경우: `sudo yum install -y nfs-utils`
* Ubuntu 또는 Debian의 경우: `sudo apt-get install nfs-common`

### <a name="create-a-local-path"></a>로컬 경로 만들기

캐시에 연결할 각 클라이언트에서 로컬 디렉터리 경로를 만듭니다. 탑재할 각 네임스페이스 경로의 경로를 만듭니다.

예: `sudo mkdir -p /mnt/hpc-cache-1/target3`

Azure Portal의 [탑재 지침](#use-the-mount-instructions-utility) 페이지에는 복사할 수 있는 프로토타입 명령이 포함되어 있습니다.

클라이언트 머신을 캐시에 연결할 때 스토리지 대상 내보내기를 나타내는 가상 네임스페이스 경로와 이 경로를 연결합니다. 클라이언트에서 사용할 각 가상 네임스페이스 경로의 디렉터리를 만듭니다.

## <a name="use-the-mount-instructions-utility"></a>탑재 지침 유틸리티 사용

Azure Portal의 **탑재 지침** 페이지를 사용하여 복사 가능한 mount 명령을 만들 수 있습니다. 포털의 캐시 보기 **구성** 섹션에서 해당 페이지를 엽니다.

클라이언트에서 이 명령을 사용하기 전에 위의 [클라이언트 준비](#prepare-clients)에 설명된 대로 클라이언트가 필수 조건을 충족하고 NFS `mount` 명령을 사용하는 데 필요한 소프트웨어가 있는지 확인합니다.

![구성 > 탑재 지침 페이지가 로드된, 포털의 Azure HPC Cache 인스턴스 스크린샷](media/mount-instructions.png)

mount 명령을 만들려면 다음 절차를 수행합니다.

1. **클라이언트 경로** 필드를 사용자 지정합니다. 이 필드에는 클라이언트에서 로컬 경로를 만드는 데 사용할 수 있는 예제 명령이 제공됩니다. 클라이언트는 이 디렉터리에서 Azure HPC Cache의 콘텐츠에 로컬로 액세스합니다.

   필드를 클릭하고 원하는 디렉터리 이름이 포함되도록 명령을 편집합니다. 이름은 `sudo mkdir -p` 뒤의 문자열 끝에 표시됩니다.

   ![커서가 끝에 배치된 클라이언트 경로 필드 스크린샷](media/mount-edit-client.png)

   필드 편집을 완료하면 페이지 맨 아래에 있는 mount 명령이 새 클라이언트 경로로 업데이트됩니다.

1. 목록에서 **캐시 탑재 주소** 를 선택합니다. 이 메뉴에는 캐시의 [클라이언트 탑재 지점](#find-mount-command-components)이 모두 나열됩니다.

   캐시 성능 향상을 위해 사용 가능한 모든 탑재 주소에서 클라이언트 부하를 분산합니다.

   ![선택할 수 있는 IP 주소 3개가 표시된 선택기가 있는 캐시 탑재 주소 필드 스크린샷](media/mount-select-ip.png)

1. 클라이언트에 사용할 **가상 네임스페이스 경로** 를 선택합니다. 이 경로는 백 엔드 스토리지 시스템의 내보내기에 연결됩니다.

   ![선택기가 열려 있는 “가상 네임스페이스 경로” 필드 스크린샷](media/mount-select-target.png)

   **네임스페이스** 포털 페이지에서 가상 네임스페이스 경로를 확인하고 변경할 수 있습니다. 자세한 내용은 [집계 네임스페이스 설정](add-namespace-paths.md)을 참조하세요.

   Azure HPC Cache의 집계 네임스페이스 기능에 대해 자세히 알아보려면 [집계 네임스페이스 계획](hpc-cache-namespace.md)을 참조하세요.

1. **탑재 명령** 필드는 탑재 주소, 가상 네임스페이스 경로 및 이전 필드에서 설정한 클라이언트 경로를 사용하는 사용자 지정된 탑재 명령으로 자동으로 채워집니다.

   필드 오른쪽에 있는 복사 기호를 클릭하여 클립보드에 자동으로 복사합니다.

   ![“클립보드로 복사” 단추를 가리킬 때 텍스트가 표시되는 프로토타입 mount 명령 필드 스크린샷](media/mount-command-copy.png)

   그 아래에는 클라이언트 경로와 네임스페이스 경로가 동일하지만 다른 캐시 탑재 주소를 사용하는 대체 탑재 명령이 표시됩니다. 최상의 성능을 위해 HPC Cache 사용 가능한 모든 주소 간에 클라이언트를 균등하게 탑재해야 합니다.

1. 클라이언트 머신에서 복사한 mount 명령을 사용하여 머신을 Azure HPC Cache에 연결합니다. 클라이언트 명령줄에서 직접 명령을 실행하거나 클라이언트 설치 스크립트 또는 템플릿에 mount 명령을 포함할 수 있습니다.

## <a name="understand-mount-command-syntax"></a>mount 명령 구문 이해

mount 명령의 형식은 다음과 같습니다.

> sudo mount {*options*} *cache_mount_address*:/*namespace_path* *local_path*

예:

```bash
root@test-client:/tmp# mkdir hpccache
root@test-client:/tmp# sudo mount -o hard,proto=tcp,mountproto=tcp,retry=30 10.0.0.28:/blob-demo-0722 hpccache
root@test-client:/tmp#
```

이 명령이 성공하면 스토리지 내보내기의 내용이 클라이언트의 ``hpccache`` 디렉터리에 표시됩니다.

### <a name="mount-command-options"></a>mount 명령 옵션

강력한 클라이언트 탑재를 위해 mount 명령에 다음 설정과 인수를 전달합니다.

> mount -o hard,proto=tcp,mountproto=tcp,retry=30 ${CACHE_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}

| 권장되는 mount 명령 설정 | Description |
--- | ---
``hard`` | Azure HPC Cache에 소프트 탑재 시 애플리케이션 오류와 데이터 손실이 발생할 수 있습니다.
``proto=tcp`` | NFS 네트워크 오류를 적절하게 처리하도록 지원합니다.
``mountproto=tcp`` | 탑재 작업에 대한 네트워크 오류를 적절하게 처리하도록 지원합니다.
``retry=<value>`` | 일시적인 탑재 오류를 방지하려면 ``retry=30``을 설정합니다. (포그라운드 탑재에는 다른 값이 권장됩니다.)

### <a name="find-mount-command-components"></a>mount 명령 구성 요소 찾기

**탑재 지침** 페이지를 사용하지 않고 mount 명령을 만들려는 경우 캐시 **개요** 페이지에서 탑재 주소를 찾고 **네임스페이스** 페이지에서 가상 네임스페이스 경로를 찾을 수 있습니다.

![오른쪽 아래에 있는 탑재 주소 목록이 강조 표시 상자로 둘러싸인 Azure HPC Cache 인스턴스의 개요 페이지 스크린샷](media/hpc-cache-mount-addresses.png)

> [!NOTE]
> 캐시 탑재 주소는 캐시 서브넷 내의 네트워크 인터페이스에 해당합니다. 리소스 그룹에서 해당 NIC는 `-cluster-nic-`와 숫자로 끝나는 이름으로 나열됩니다. 이 인터페이스를 변경하거나 삭제하면 캐시를 사용할 수 없게 되므로 변경하거나 삭제하지 않도록 합니다.

가상 네임스페이스 경로는 캐시의 **네임스페이스** 설정 페이지에 표시됩니다.

![테이블의 첫 번째 열인 “네임스페이스 경로”가 강조 상자로 둘러싸인 포털 설정 > 네임스페이스 페이지 스크린샷](media/view-namespace-paths.png)

## <a name="use-all-available-mount-addresses"></a>사용 가능한 모든 탑재 주소 사용

캐시에 대해 나열된 모든 IP 주소 간에 클라이언트 트래픽을 분산해야 합니다. 모든 클라이언트를 하나의 주소에만 탑재하면 캐시 성능이 저하됩니다.

다른 클라이언트에 대해 다른 탑재 주소를 수동으로 선택하거나 스크립트를 만들어 선택할 수 있습니다. RRDNS(라운드 로빈 DNS)에 대해 구성된 DNS 서버를 사용하여 사용 가능한 모든 주소 간에 클라이언트 탑재를 자동으로 회전할 수도 있습니다. 자세한 내용은 [HPC Cache 트래픽 부하 분산을](client-load-balancing.md) 읽어보세요.

## <a name="next-steps"></a>다음 단계

* [클라이언트 부하를 분산하여](client-load-balancing.md)캐시의 모든 처리량을 사용하는 방법에 대해 자세히 알아봅니다.
* 데이터를 캐시의 스토리지 대상으로 이동하려면 [새 Azure Blob Storage 채우기](hpc-cache-ingest.md)를 참조하세요.

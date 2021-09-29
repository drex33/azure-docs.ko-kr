---
title: Microsoft Azure FXT Edge Filer 클러스터에 클라이언트 탑재
description: NFS 클라이언트 머신이 Azure FXT Edge Filer 하이브리드 스토리지 캐시를 탑재하는 방법
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: v-erkel
ms.openlocfilehash: 77663500c6a3e4c432506a5859892506f8995de1
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128552607"
---
# <a name="tutorial-mount-the-cluster"></a>자습서: 클러스터 탑재

이 자습서에서는 Azure FXT Edge Filer 클러스터에 NFS 클라이언트를 탑재하는 방법을 설명합니다. 클라이언트는 백 엔드 스토리지를 추가할 때 할당된 가상 네임스페이스 경로를 탑재합니다.

이 자습서에서는 다음 내용을 설명합니다.

> [!div class="checklist"]
>
> * 클라이언트 측 IP 주소 범위에 클라이언트 부하를 분산하는 전략
> * 클라이언트 측 IP 주소 및 네임스페이스 접합으로 탑재 경로를 생성하는 방법
> * 탑재 명령에서 사용할 인수

이 자습서를 완료하는 데 약 45분이 소요됩니다.

## <a name="steps-to-mount-the-cluster"></a>클러스터를 탑재하는 단계

클라이언트 머신을 Azure FXT Edge Filer 클러스터에 연결하려면 다음 단계를 수행합니다.

1. 클러스터 노드 간에 클라이언트 트래픽의 부하를 분산하는 방법을 결정합니다. 자세한 내용은 아래의 [클라이언트 부하 분산](#balance-client-load)을 참조하세요.
1. 탑재할 클러스터 IP 주소와 접합 경로를 식별합니다.
1. 탑재용 클라이언트 측 경로를 확인합니다.
1. 적절한 인수가 포함된 [mount 명령](#use-recommended-mount-command-options)을 실행합니다.

## <a name="balance-client-load"></a>클라이언트 부하 분산

클러스터의 모든 노드 간에 클라이언트 요청의 부하를 분산하려면 클라이언트를 모든 범위의 클라이언트 측 IP 주소 범위에 탑재해야 합니다. 이 작업을 자동화하는 몇 가지 방법이 있습니다.

클러스터의 라운드 로빈 DNS 부하 분산에 대해 알아보려면 [Azure FXT Edge Filer 클러스터용 DNS 구성](configure-network.md#configure-dns-for-load-balancing)을 참조하세요. 이 방법을 사용하려면 DNS 서버를 유지 관리해야 합니다. 그 방법은 이 문서에서 설명하지 않습니다.

더 간단한 소규모 설치 방법은 스크립트를 사용하여 클라이언트 탑재 시 전체 범위에 IP 주소를 할당하는 것입니다.

다른 부하 분산 방법은 크거나 복잡한 시스템에 적합할 수 있습니다. Microsoft 담당자에게 문의하거나 [지원 요청](support-ticket.md)을 열어 도움을 받으세요. (Azure Load Balancer는 현재 Azure FXT Edge Filer에서 *지원되지 않습니다*.)

## <a name="create-the-mount-command"></a>mount 명령 만들기

클라이언트에서 ``mount`` 명령은 Azure FXT Edge Filer 클러스터의 가상 서버(vserver)를 로컬 파일 시스템의 경로에 매핑합니다.

형식은 ``mount <FXT cluster path> <local path> {options}``입니다.

mount 명령에는 다음 세 가지 요소가 있습니다.

* 클러스터 경로 - 아래에서 설명하는 IP 주소와 네임스페이스 접합 경로의 조합
* 로컬 경로 - 클라이언트의 경로
* 탑재 명령 옵션 - ([권장되는 탑재 명령 옵션 사용](#use-recommended-mount-command-options)에 나열되어 있음)

### <a name="create-the-cluster-path"></a>클러스터 경로 만들기

클러스터 경로는 *IP 주소* 와 *네임스페이스 접합* 에 대한 경로의 조합입니다. 네임스페이스 접합은 [스토리지 시스템을 추가](add-storage.md#create-a-junction)할 때 정의된 가상 경로입니다.

예를 들어 ``/fxt/files``를 네임스페이스 경로로 사용한 경우 클라이언트에서 *IP_address*:/fxt/files를 해당 로컬 탑재 지점에 탑재합니다.

![네임스페이스 경로 필드에 /avere/files가 있는 "새 접합 추가" 대화 상자](media/fxt-mount/fxt-junction-example.png)

IP 주소는 vserver에 대해 정의된 클라이언트 측 IP 주소 중 하나입니다. 클러스터 제어판의 두 곳에서 클라이언트 측 IP 범위를 찾을 수 있습니다.

* **VServers** 테이블(대시보드 탭)

  ![그래프 아래의 데이터 테이블에서 VServer 탭이 선택되고 IP 주소 섹션이 원으로 표시된 제어판의 대시보드 탭](media/fxt-mount/fxt-ip-addresses-dashboard.png)

* **클라이언트 측 네트워크** 설정 페이지

  ![특정 vserver에 대한 테이블의 주소 범위 섹션 주위가 원으로 표시된 설정 > VServer > 클라이언트 측 네트워크 구성 페이지](media/fxt-mount/fxt-ip-addresses-settings.png)

IP 주소와 네임스페이스 경로를 결합하여 탑재 명령의 클러스터 경로를 생성합니다.

클라이언트 탑재 명령 예: ``mount 10.0.0.12:/sd-access /mnt/fxt {options}``

### <a name="create-the-local-path"></a>로컬 경로 만들기

탑재 명령의 로컬 경로는 사용자가 정할 수 있습니다. 가상 네임 스페이스의 일부로 원하는 모든 경로 구조를 설정할 수 있습니다. 클라이언트 워크플로에 편리한 네임스페이스 및 로컬 경로를 설계합니다.

클라이언트 측 네임스페이스에 대한 자세한 내용은 클러스터 구성 가이드의 [네임스페이스 개요](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gns_overview.html)를 참조하세요.

각 클라이언트를 탑재할 때 경로 외에도 아래에서 설명하는 [탑재 명령 옵션](#use-recommended-mount-command-options)을 포함합니다.

### <a name="use-recommended-mount-command-options"></a>권장되는 탑재 명령 옵션 사용

클라이언트 탑재를 원활하게 수행하려면 mount 명령에 다음 설정 및 인수를 전달합니다.

`mount -o hard,nointr,proto=tcp,mountproto=tcp,retry=30 ${VSERVER_IP_ADDRESS}:/${NAMESPACE_PATH} ${LOCAL_FILESYSTEM_MOUNT_POINT}`

| 필수 설정 | Description |
--- | ---
``hard`` | Azure FXT Edge Filer 클러스터에 대한 소프트 탑재는 애플리케이션 오류 및 가능한 데이터 손실과 관련이 있습니다.
``proto=netid`` | NFS 네트워크 오류를 적절하게 처리하도록 지원합니다.
``mountproto=netid`` | 탑재 작업에 대한 네트워크 오류를 적절하게 처리하도록 지원합니다.
``retry=n`` | 일시적인 탑재 오류를 방지하려면 ``retry=30``을 설정합니다. (포그라운드 탑재에는 다른 값이 권장됩니다.)

| 기본 설정 | Description |
| --- | --- |
| `nointr` | 클라이언트가 이 옵션을 지원하는 이전 버전의 OS 커널(2008년 4월 이전)을 사용하는 경우에는 해당 옵션을 사용하세요. "intr" 옵션이 기본값입니다. |

## <a name="next-steps"></a>다음 단계

클라이언트를 탑재한 후 워크플로를 테스트하고 클러스터를 시작할 수 있습니다.

데이터를 새 클라우드 핵심 파일러(filer)로 이동해야 할 경우 병렬 데이터 수집을 통해 캐시 구조를 활용하세요. [vFXT 클러스터로 데이터 이동](../avere-vfxt/avere-vfxt-data-ingest.md)에 몇 가지 전략이 설명되어 있습니다. (Avere vFXT for Azure는 Azure FXT Edge Filer와 매우 유사한 캐싱 기술을 사용하는 클라우드 기반 제품입니다.)

하드웨어 문제를 해결해야 하는 경우에는 [Azure FXT Edge Filer 하드웨어 상태 모니터링](monitor.md)을 참조하세요.

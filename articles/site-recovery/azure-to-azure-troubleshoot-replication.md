---
title: Azure Site Recovery를 사용하여 Azure VM 복제 문제 해결
description: Azure Site Recovery를 사용하여 Azure VM 재해 복구에서 복제 문제 해결
author: sideeksh
manager: rochakm
ms.topic: troubleshooting
ms.date: 04/03/2020
ms.openlocfilehash: 891c86c15c8335f9e28271912f26cbc757f6d09e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131055012"
---
# <a name="troubleshoot-replication-in-azure-vm-disaster-recovery"></a>Azure VM 재해 복구에서 복제 문제 해결

이 문서에서는 지역 간에 Azure VM(가상 머신)을 복제 및 복구할 때 Azure Site Recovery에서 발생하는 일반적인 문제를 설명합니다. 일반적인 문제를 해결하는 방법에 대해서도 설명합니다. 지원되는 구성에 대한 자세한 내용은 [Azure VM을 복제하기 위한 지원 매트릭스](./azure-to-azure-support-matrix.md)를 참조하세요.

Azure Site Recovery는 원본 지역에서 재해 복구 지역으로 데이터를 일관되게 복제합니다. 또한 5분 마다 크래시 일치 복구 지점을 만듭니다. Site Recovery에서 60분 동안 복구 지점을 만들 수 없는 경우 다음 정보를 알려줍니다.

```plaintext
Error message: "No crash consistent recovery point available for the VM in the last 60 minutes."

Error ID: 153007
```

다음 섹션에서는 원인 및 해결 방법을 설명합니다.

## <a name="high-data-change-rate-on-the-source-virtual-machine"></a>원본 가상 머신의 높은 데이터 변경률

Azure Site Recovery는 원본 가상 머신의 데이터 변경률이 지원되는 제한보다 높은 경우 이벤트를 만듭니다. 높은 이탈로 인한 문제인지 확인하려면 **복제된 항목** > **VM** > **이벤트 - 지난 72시간** 으로 이동하세요.
**지원되는 제한을 초과하는 데이터 변경률** 이라는 이벤트가 표시됩니다.

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/data_change_event.png" alt-text="너무 높은 데이터 변경률을 보여 주는 Azure Site Recovery 페이지입니다.":::

이벤트를 선택하면 정확한 디스크 정보가 표시됩니다.

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/data_change_event2.png" alt-text="데이터 변경률 이벤트 세부 정보를 보여 주는 페이지입니다.":::

### <a name="azure-site-recovery-limits"></a>Azure Site Recovery 제한

다음 테이블에는 Azure Site Recovery 제한이 제공됩니다. 이러한 한도는 테스트를 기반으로 하지만 모든 가능한 애플리케이션 I/O(입력-출력) 조합을 다룰 수는 없습니다. 실제 결과는 애플리케이션 I/O 조합에 따라 달라질 수 있습니다.

고려해야 할 두 가지 제한은 디스크당 데이터 변동 및 가상 머신당 데이터 변동입니다. 예를 들어 다음 표의 Premium P20 디스크를 살펴보겠습니다. 단일 VM의 경우 Site Recovery는 이러한 디스크가 최대 5개 있는 디스크당 5MB/s의 변동을 처리할 수 있습니다. Site Recovery는 VM당 총 변동이 54MB/s로 제한됩니다.

**복제 스토리지 대상** | **원본 디스크의 평균 I/O 크기** |**원본 디스크의 평균 데이터 변동** | **원본 데이터 디스크의 일별 총 데이터 변동**
---|---|---|---
Standard Storage | 8KB    | 2MB/초 | 디스크당 168GB
프리미엄 P10 또는 P15 디스크 | 8KB    | 2MB/초 | 디스크당 168GB
프리미엄 P10 또는 P15 디스크 | 16KB | 4MB/초 |    디스크당 336GB
프리미엄 P10 또는 P15 디스크 | 32KB 이상 | 8MB/초 | 디스크당 672GB
프리미엄 P20 또는 P30 또는 P40 또는 P50 디스크 | 8KB    | 5MB/초 | 디스크당 421GB
프리미엄 P20 또는 P30 또는 P40 또는 P50 디스크 | 16KB 이상 |20MB/s | 디스크당 1,684GB

### <a name="solution"></a>해결 방법

Azure Site Recovery에는 디스크 유형에 따라 데이터 변경률 제한이 있습니다. 이 문제가 되풀이되는지 또는 일시적인지 알려면 영향을 받는 가상 머신의 데이터 변동률을 찾으세요. 원본 가상 머신으로 이동하여 **모니터링** 아래의 메트릭을 찾은 다음, 이 스크린샷과 같이 메트릭을 추가합니다.

:::image type="content" source="./media/site-recovery-azure-to-azure-troubleshoot/churn.png" alt-text="데이터 변경률을 찾는 3단계 프로세스를 보여 주는 페이지입니다.":::

1. **메트릭 추가** 를 선택하고 **OS 디스크 쓰기 바이트/초** 및 **데이터 디스크 쓰기 바이트/초** 를 추가합니다.
1. 스크린샷에 표시된 것처럼 스파이크를 모니터링합니다.
1. OS 디스크 및 결합된 모든 데이터 디스크에서 발생하는 총 쓰기 작업을 보여줍니다. 이러한 메트릭은 디스크별 수준에서 정보를 제공하지는 않지만 데이터 변동의 총 패턴을 나타냅니다.

데이터 변경률의 급증은 간헐적인 데이터 버스트에서 비롯될 수 있습니다. 데이터 변경률이 10MB/s(Premium의 경우) 또는 2MB/s(Standard의 경우)를 초과했다가 낮아지면 복제가 처리됩니다. 변동이 지속적으로 지원되는 한도를 훨씬 초과하는 경우 다음 옵션 중 하나를 고려합니다.

- 높은 데이터 변경률을 일으키는 디스크 제외: 먼저 복제를 사용하지 않도록 설정합니다. 그런 다음 [PowerShell](azure-to-azure-exclude-disks.md)을 사용하여 디스크를 제외할 수 있습니다.
- 재해 복구 스토리지 디스크의 계층 변경: 이 옵션은 디스크 데이터 변동이 20MB/s 미만인 경우에만 가능합니다. 얘를 들어 P10 디스크가 있는 VM에 8MB/s보다 크지만 10MB/s보다 작은 데이터 변동이 있습니다. 고객이 보호 중에 대상 스토리지에 대해 P30 디스크를 사용할 수 있는 경우 문제를 해결할 수 있습니다. 이 솔루션은 프리미엄 관리 디스크를 사용하는 컴퓨터에만 사용할 수 있습니다. 다음 단계를 수행합니다.

  1. 영향을 받는 복제된 컴퓨터의 **디스크** 로 이동하여 복제 디스크 이름을 복사합니다.
  1. 관리 디스크의 이 복제본으로 이동합니다.
  1. SAS URL이 생성되었다는 배너가 **개요** 에 표시될 수 있습니다. 이 배너를 선택하고 내보내기를 취소합니다. 배너가 표시되지 않으면 이 단계를 무시합니다.
  1. SAS URL이 호출되는 즉시 관리 디스크의 **구성** 으로 이동합니다. Site Recovery가 원본 디스크에서 관찰된 변동률을 지원하도록 크기를 늘립니다.

## <a name="network-connectivity-problems"></a>네트워크 연결 문제

### <a name="network-latency-to-a-cache-storage-account"></a>캐시 스토리지 계정의 네트워크 대기 시간

Site Recovery는 복제된 데이터를 캐시 스토리지 계정으로 보냅니다. 가상 머신에서 캐시 스토리지 계정으로 데이터를 업로드하는 데 3초 동안 4MB보다 느리게 업로드하면 네트워크 대기 시간이 표시될 수 있습니다.

대기 시간과 관련된 문제를 확인하려면 [AzCopy](../storage/common/storage-use-azcopy-v10.md)를 사용합니다. 이 명령줄 유틸리티를 사용하여 가상 머신에서 캐시 스토리지 계정으로 데이터를 업로드할 수 있습니다. 대기 시간이 높은 경우 VM에서 아웃바운드 네트워크 트래픽을 제어하는 데 NVA(네트워크 가상 어플라이언스)를 사용 중인지 확인합니다. 모든 복제 트래픽이 NVA를 통과하는 경우 어플라이언스가 제한될 수 있습니다.

복제 트래픽이 NVA로 이동하지 않도록 "Storage"에 대한 가상 네트워크에서 네트워크 서비스 엔드포인트를 만드는 것이 좋습니다. 자세한 내용은 [네트워크 가상 어플라이언스 구성](azure-to-azure-about-networking.md#network-virtual-appliance-configuration)을 참조하세요.

### <a name="network-connectivity"></a>네트워크 연결

Site Recovery 복제가 작동하려면 VM에서 특정 URL 또는 IP 범위에 대한 아웃바운드 연결을 제공해야 합니다. VM이 방화벽 뒤에 있거나 NSG(네트워크 보안 그룹) 규칙을 사용하여 아웃바운드 연결을 제어할 수 있습니다. 이 경우 문제가 발생할 수 있습니다. 모든 URL이 연결되었는지 확인하려면 [URL에 대한 아웃바운드 연결](azure-to-azure-about-networking.md#outbound-connectivity-for-urls)을 참조하세요.

## <a name="error-id-153006---no-app-consistent-recovery-point-available-for-the-vm-in-the-past-x-minutes"></a>오류 ID 153006 - 지난 "X"분 동안 VM에 사용할 수 있는 앱 일관성 복구 지점이 없습니다.

다음은 가장 일반적인 문제 중 일부입니다.

### <a name="known-issue-in-sql-server-20082008-r2"></a>SQL Server 2008/2008 R2의 알려진 문제

**해결 방법**: SQL Server 2008/2008 R2의 알려진 문제가 있습니다. 기술 자료 문서 [Azure Site Recovery 에이전트 또는 기타 구성 요소가 아닌 VSS 백업이 SQL Server 2008 R2를 호스트하는 서버에서 실패함](https://support.microsoft.com/help/4504103/non-component-vss-backup-fails-for-server-hosting-sql-server-2008-r2)을 참조하세요.

### <a name="azure-site-recovery-jobs-fail-on-servers-hosting-any-version-of-sql-server-instances-with-auto_close-dbs"></a>AUTO_CLOSE DB를 사용하여 모든 버전의 SQL Server 인스턴스를 호스트하는 서버에서 Azure Site Recovery 작업이 실패함

**해결 방법:** [AUTO_CLOSE DB를 사용하여 SQL Server 인스턴스를 호스팅하는 서버에서 Azure Site Recovery 작업과 같은 구성 요소가 아닌 VSS 백업 실패](https://support.microsoft.com/help/4504104/non-component-vss-backups-such-as-azure-site-recovery-jobs-fail-on-ser) 문서를 참조하세요.

### <a name="known-issue-in-sql-server-2016-and-2017"></a>SQL Server 2016 및 2017의 알려진 이슈

**해결 방법**: [SQL Server 2017의 누적 업데이트 16](https://support.microsoft.com/help/4508218/cumulative-update-16-for-sql-server-2017) 문서를 참조하세요.

### <a name="youre-using-azure-storage-spaces-direct-configuration"></a>Azure Storage 공간 다이렉트 구성을 사용하고 있습니다.

**해결 방법**: Azure Site Recovery는 스토리지 공간 다이렉트 구성에 대한 애플리케이션 일관된 복구 지점을 만들 수 없습니다. [복제 정책을 구성합니다](azure-to-azure-how-to-enable-replication-s2d-vms.md).

### <a name="app-consistency-not-enabled-on-linux-servers"></a>Linux 서버에서 앱 일관성을 사용하도록 설정하지 않음

**해결 방법**: Linux 운영 체제용 Azure Site Recovery는 앱 일관성을 위한 애플리케이션 사용자 지정 스크립트를 지원합니다. 사전 및 사후 옵션이 포함된 사용자 지정 스크립트는 앱 일관성을 위해 Azure Site Recovery 모바일 에이전트에서 사용됩니다. 사용하도록 설정하는 단계는 [여기](./site-recovery-faq.yml)를 참조하세요.

### <a name="more-causes-because-of-vss-related-issues"></a>VSS 관련 문제로 인한 추가 원인:

추가로 문제를 해결하려면 원본 머신의 파일을 확인하여 오류에 대한 정확한 오류 코드를 확인합니다.

`C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\Application Data\ApplicationPolicyLogs\vacp.log`

오류를 찾으려면 텍스트 편집기에서 _vcp.log_ 파일을 열고 **vcpError** 문자열을 검색합니다.

```plaintext
Ex: vacpError:220#Following disks are in FilteringStopped state [\\.\PHYSICALDRIVE1=5, ]#220|^|224#FAILED: CheckWriterStatus().#2147754994|^|226#FAILED to revoke tags.FAILED: CheckWriterStatus().#2147754994|^|
```

앞의 예에서 **2147754994** 는 이 문장 다음의 실패에 대해 알려주는 오류 코드입니다.

#### <a name="vss-writer-is-not-installed---error-2147221164"></a>VSS 기록기가 설치되어 있지 않음 - 오류 2147221164

**해결 방법**:애플리케이션 일관성 태그를 생성하려면 Azure Site Recovery에서 VSS(볼륨 섀도 복사본 서비스)를 사용합니다. Site Recovery가 애플리케이션 일관성 스냅샷을 찍는 작업을 위한 VSS 공급자를 설치합니다. Azure Site Recovery가 VSS 공급자를 서비스로 설치합니다. VSS 공급자가 설치되지 않은 경우 애플리케이션 일관성 스냅샷 만들기가 실패합니다. **오류 ID 0x80040154 클래스가 등록되지 않음** 이 표시됩니다. [VSS 기록기 설치 문제 해결](vmware-azure-troubleshoot-push-install.md#vss-installation-failures)에 대한 문서를 참조하세요.

#### <a name="vss-writer-is-disabled---error-2147943458"></a>VSS 기록기를 사용할 수 없음 - 오류 2147943458

**해결 방법**: 애플리케이션 일관성 태그를 생성하기 위해 Azure Site Recovery는 VSS를 사용합니다. Site Recovery가 애플리케이션 일관성 스냅샷을 찍는 작업을 위한 VSS 공급자를 설치합니다. 이 VSS 공급자는 서비스로 설치됩니다. VSS 공급자 서비스를 사용하도록 설정하지 않은 경우 애플리케이션 일관성 스냅샷 만들기가 실패합니다. 다음 오류가 표시됩니다. **지정한 서비스가 사용하지 않도록 설정되어 시작할 수 없습니다(0x80070422)** .

VSS를 사용할 수 없는 경우:

- VSS 공급자 서비스의 시작 유형이 **자동** 으로 설정되어 있는지 확인합니다.
- 다음 서비스를 다시 시작합니다.
  - VSS 서비스
  - Azure Site Recovery VSS 공급자
  - VDS 서비스.

#### <a name="vss-provider-not_registered---error-2147754756"></a>VSS PROVIDER NOT_REGISTERED - 오류 2147754756

**해결 방법**: 애플리케이션 일관성 태그를 생성하기 위해 Azure Site Recovery는 VSS를 사용합니다. Azure Site Recovery VSS 공급자 서비스가 설치되었는지 확인합니다.

다음 명령을 사용하여 VSS 공급자를 다시 설치합니다.

1. 기존 공급자 제거:

   `"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Uninstall.cmd"`

1. VSS 공급자 다시 설치:

   `"C:\Program Files (x86)\Microsoft Azure Site Recovery\agent\InMageVSSProvider_Install.cmd"`

VSS 공급자 서비스의 시작 유형이 **자동** 으로 설정되어 있는지 확인합니다.

다음 서비스를 다시 시작합니다.

- VSS 서비스
- Azure Site Recovery VSS 공급자
- VDS 서비스.

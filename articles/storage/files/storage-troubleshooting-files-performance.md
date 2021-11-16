---
title: Azure 파일 공유 성능 문제 해결 가이드
description: Azure 파일 공유의 알려진 성능 문제를 해결합니다. 이러한 문제가 발생할 경우 잠재적 원인과 관련 해결 방법을 검색합니다.
author: jeffpatt24
ms.service: storage
ms.topic: troubleshooting
ms.date: 07/06/2021
ms.author: jeffpatt
ms.subservice: files
ms.openlocfilehash: 44cbae10fc83ddbacf9abc8fd6510667c1f27e00
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132524382"
---
# <a name="troubleshoot-azure-file-shares-performance-issues"></a>Azure 파일 공유 성능 문제 해결

이 문서에는 Azure 파일 공유와 관련된 몇 가지 일반적인 문제가 나와 있습니다. 이러한 문제가 발생하는 경우에 대한 잠재적 원인과 해결 방법을 제공합니다.

## <a name="applies-to"></a>적용 대상
| 파일 공유 유형 | SMB | NFS |
|-|:-:|:-:|
| 표준 파일 공유(GPv2), LRS/ZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |
| 표준 파일 공유(GPv2), GRS/GZRS | ![예](../media/icons/yes-icon.png) | ![아니요](../media/icons/no-icon.png) |
| 프리미엄 파일 공유(FileStorage), LRS/ZRS | ![예](../media/icons/yes-icon.png) | ![예](../media/icons/yes-icon.png) |

## <a name="high-latency-low-throughput-and-general-performance-issues"></a>대기 시간이 짧고 처리량이 높으며 일반 성능 문제가 발생합니다.

### <a name="cause-1-share-was-throttled"></a>원인 1: 공유가 제한됨

파일 공유에 대한 IOPS(초당 I/O 작업 수), 수신 또는 송신 제한에 도달하면 요청이 제한됩니다. 표준 및 프리미엄 파일 공유에 대한 제한을 알아보려면 [파일 공유 및 파일 배율 목표](./storage-files-scale-targets.md#azure-file-share-scale-targets)를 참조하세요.

공유가 제한 중인지 확인하려면 포털에서 Azure 메트릭에 액세스하여 사용하면 됩니다.

1. Azure Portal에서 스토리지 계정으로 이동합니다.

1. 왼쪽 창의 **모니터링** 에서 **메트릭** 을 선택합니다.

1. **파일** 을 스토리지 계정 범위에 대한 메트릭 네임스페이스로 선택합니다.

1. **트랜잭션** 을 메트릭으로 선택합니다.

1. **응답 형식** 에 대한 필터를 추가한 다음, 요청이 제한되었는지 여부를 확인합니다. 

    표준 파일 공유의 경우 요청이 제한되면 다음 응답 형식이 기록됩니다.

    - SuccessWithThrottling
    - SuccessWithShareIopsThrottling
    - ClientShareIopsThrottlingError

    프리미엄 파일 공유의 경우 요청이 제한되면 다음 응답 형식이 기록됩니다.

    - SuccessWithShareEgressThrottling
    - SuccessWithShareIngressThrottling
    - SuccessWithShareIopsThrottling
    - ClientShareEgressThrottlingError
    - ClientShareIngressThrottlingError
    - ClientShareIopsThrottlingError

    각 응답 형식에 대해 자세히 알아보려면 [메트릭 차원](./storage-files-monitoring-reference.md#metrics-dimensions)을 참조하세요.

    !["응답 형식" 속성 필터를 표시하는 프리미엄 파일 공유에 대한 메트릭 옵션의 스크린샷입니다.](media/storage-troubleshooting-premium-fileshares/metrics.png)

    > [!NOTE]
    > 경고를 수신하려면 이 문서의 뒷부분에 나오는 ["파일 공유가 제한된 경우 경고를 만드는 방법"](#how-to-create-an-alert-if-a-file-share-is-throttled) 섹션을 참조하세요.

#### <a name="solution"></a>솔루션

- 표준 파일 공유를 사용하는 경우 스토리지 계정에서 [대용량 파일 공유를 사용하도록 설정](storage-how-to-create-file-share.md#enable-large-files-shares-on-an-existing-account)하고 [파일 공유 할당량의 크기를 늘려 대용량 파일 공유 지원을 활용](storage-how-to-create-file-share.md#expand-existing-file-shares)합니다. 대용량 파일 공유는 뛰어난 IOPS 및 대역폭 제한을 지원합니다. 자세한 내용은 [Azure Files 확장성 및 성능 목표](storage-files-scale-targets.md)를 참조하세요.
- 프리미엄 파일 공유를 사용하는 경우 프로비전된 파일 공유 크기를 늘려 IOPS 제한을 늘립니다. 자세히 알아보려면 [프리미엄 파일 공유에 대한 프로비전 이해](./understanding-billing.md#provisioned-model)를 참조하세요.

### <a name="cause-2-metadata-or-namespace-heavy-workload"></a>원인 2: 메타데이터 또는 네임스페이스에 워크로드가 과도함

대부분의 요청이 메타데이터 중심이라면(예: `createfile`, `openfile`, `closefile`, `queryinfo` 또는 `querydirectory`) 대기 시간이 읽기/쓰기 작업에서보다 늘어날 수 있습니다.

대부분의 요청이 메타데이터 중심인지 확인하려면 앞서 원인 1에 설명한 것처럼 1~4단계를 수행하여 시작합니다. 5단계의 경우 **응답 형식** 에 대한 필터를 추가하는 대신, **API 이름** 에 대한 속성 필터를 추가합니다.

!["API 이름" 속성 필터를 표시하는 프리미엄 파일 공유에 대한 메트릭 옵션의 스크린샷입니다.](media/storage-troubleshooting-premium-fileshares/MetadataMetrics.png)

#### <a name="workaround"></a>해결 방법

- 메타데이터 작업의 수를 줄이기 위해 애플리케이션을 수정할 수 있는지 여부를 확인합니다.
- 파일 공유에 VHD(가상 하드 디스크)를 추가하고 클라이언트에서 VHD를 탑재하여 데이터에 대한 파일 작업을 수행합니다. 이 방법은 단일 작성자/판독자 시나리오나, 작성자는 여럿이고 판독자는 없는 시나리오에 적합합니다. 파일 시스템은 Azure Files가 아닌 클라이언트의 소유이므로 메타데이터 작업을 로컬로 사용할 수 있습니다. 설치 프로그램은 로컬에서 직접 연결된 스토리지와 비슷한 성능을 제공합니다.
    -   Windows 클라이언트에 VHD를 탑재하려면 [Mount-DiskImage](/powershell/module/storage/mount-diskimage) PowerShell cmdlet을 사용합니다.
    -   Linux에 VHD를 탑재하려면 Linux 배포에 대한 설명서를 참조하세요.     

### <a name="cause-3-single-threaded-application"></a>원인 3: 단일 스레드 애플리케이션

사용 중인 애플리케이션이 단일 스레드인 경우 프로비전된 공유 크기에 따라 이 설정으로 인해 가능한 최대 처리량보다 IOPS 처리량이 상당히 줄어들 수 있습니다.

#### <a name="solution"></a>해결 방법

- 스레드 수를 늘려 애플리케이션 병렬 처리를 늘립니다.
- 병렬 처리를 사용할 수 있는 애플리케이션으로 전환합니다. 예를 들어 복사 작업의 경우 Windows 클라이언트 또는 Linux 클라이언트의 **병렬** 명령에서 AzCopy 또는 RoboCopy를 사용할 수 있습니다.

### <a name="cause-4-number-of-smb-channels-exceeds-four"></a>원인 4: SMB 채널 수가 4개를 초과함

SMB MultiChannel을 사용하는 경우 채널 수가 4개를 초과하면 성능이 저하됩니다. 연결 수가 4개를 초과하는지 확인하려면 PowerShell cmdlet `get-SmbClientConfiguration`을 사용하여 현재 연결 수 설정을 확인합니다.

#### <a name="solution"></a>솔루션

총 채널이 4개를 초과하지 않도록 SMB에 대한 NIC당 Windows 설정을 지정합니다. 예를 들어 2개의 NIC가 있는 경우 `Set-SmbClientConfiguration -ConnectionCountPerRssNetworkInterface 2` PowerShell cmdlet을 사용하여 NIC당 최댓값을 2로 설정할 수 있습니다.

## <a name="very-high-latency-for-requests"></a>요청에 대한 대기 시간 매우 김

### <a name="cause"></a>원인

클라이언트 VM(가상 머신)이 파일 공유와는 다른 지역에 있을 수 있습니다. 대기 시간이 긴 다른 이유는 클라이언트나 네트워크에서 초래하는 대기 시간으로 인해 발생할 수 있습니다.

### <a name="solution"></a>해결 방법

- 파일 공유와 동일한 지역에 있는 VM에서 애플리케이션을 실행합니다.
- 스토리지 계정의 경우 Azure Portal에서 **Azure Monitor** 를 통해 트랜잭션 메트릭 **SuccessE2ELatency** 및 **SuccessServerLatency** 를 검토합니다. SuccessE2ELatency와 SuccessServerLatency 메트릭 값 간의 큰 차이는 네트워크 또는 클라이언트로 인해 발생할 수 있는 대기 시간을 나타냅니다. Azure Files 모니터링 데이터 참조의 [트랜잭션 메트릭](storage-files-monitoring-reference.md#transaction-metrics)을 참조하세요.

## <a name="client-unable-to-achieve-maximum-throughput-supported-by-the-network"></a>클라이언트에서 네트워크를 통해 지원되는 최대 처리량을 달성할 수 없음

### <a name="cause"></a>원인
가능한 한 가지 원인은 표준 파일 공유에 대한 SMB 다중 채널 지원이 부족한 것입니다. 현재 Azure Files는 단일 채널만 지원하므로 클라이언트 VM에서 서버로의 연결은 하나뿐입니다. 이 단일 연결은 클라이언트 VM의 단일 코어에 고정되므로 VM에서 달성할 수 있는 최대 처리량은 단일 코어에 의해 바인딩됩니다.

### <a name="workaround"></a>해결 방법

- 프리미엄 파일 공유의 경우 [SMB 다중 채널을 사용하도록 설정](files-smb-protocol.md#smb-multichannel)합니다.
- 코어가 더 큰 VM을 가져오면 처리량을 향상시킬 수 있습니다.
- 여러 VM에서 클라이언트 애플리케이션을 실행하면 처리량이 증가합니다.
- 가능한 경우 REST API를 사용합니다.
- NFS 파일 공유의 경우 미리 보기에서 nconnect를 사용할 수 있습니다. 프로덕션 워크로드에 권장되지 않습니다.

## <a name="throughput-on-linux-clients-is-significantly-lower-than-that-of-windows-clients"></a>Linux 클라이언트의 처리량이 Windows 클라이언트의 처리량보다 훨씬 적음

### <a name="cause"></a>원인

이는 Linux에서 SMB 클라이언트를 구현하는 것과 관련하여 알려진 문제입니다.

### <a name="workaround"></a>해결 방법

- 부하를 여러 VM에 분산합니다.
- 동일한 VM에서 `nosharesock` 옵션을 사용하여 여러 탑재 위치를 사용하고 이러한 탑재 지점의 부하를 분산합니다.
- Linux에서 `nostrictsync` 옵션으로 탑재를 시도하여 모든 `fsync` 호출에서 SMB 강제 플러시를 방지합니다. Azure Files의 경우 이 옵션은 데이터 일관성을 방해하지 않지만 디렉터리 목록(`ls -l` 명령)에 오래된 파일 메타데이터가 발생할 수 있습니다. `stat` 명령을 사용하여 파일 메타데이터를 직접 쿼리하면 최신 파일 메타데이터가 반환됩니다.

## <a name="high-latencies-for-metadata-heavy-workloads-involving-extensive-openclose-operations"></a>메타데이터에 대한 긴 대기 시간-광범위한 열기/닫기 작업과 관련된 과도한 워크로드

### <a name="cause"></a>원인

디렉터리 임대에 대한 지원이 부족합니다.

### <a name="workaround"></a>해결 방법

- 가능하면 짧은 시간 내에 동일한 디렉터리에서 열기/닫기 핸들을 과도하게 사용하지 마세요.
- Linux VM의 경우 `actimeo=<sec>`를 탑재 옵션으로 지정하여 디렉터리 항목 캐시 제한 시간을 늘립니다. 기본적으로 제한 시간은 1초이므로 3초 또는 5초와 같은 큰 값이 도움이 될 수 있습니다.
- RHEL(CentOS Linux 또는 Red Hat Enterprise Linux) VM의 경우 시스템을 CentOS Linux 8.2 또는 RHEL 8.2로 업그레이드합니다. 다른 Linux VM의 경우 커널을 5.0 이상으로 업그레이드합니다.

## <a name="low-iops-on-centos-linux-or-rhel"></a>CentOS Linux 또는 RHEL에서의 낮은 IOPS

### <a name="cause"></a>원인

CentOS Linux 또는 RHEL에서는 1 보다 큰 I/O 깊이가 지원되지 않습니다.

### <a name="workaround"></a>해결 방법

- CentOS Linux 8 또는 RHEL 8로 업그레이드합니다.
- Ubuntu로 변경합니다.

## <a name="slow-file-copying-to-and-from-azure-file-shares-in-linux"></a>Linux에서 Azure Files 공유와 서로 파일을 복사하는 속도 느림

파일 복사 속도가 느려지는 경우 [Linux 문제 해결 가이드](storage-troubleshoot-linux-file-connection-problems.md#slow-file-copying-to-and-from-azure-files-in-linux)의 "Linux에서 Azure Files 공유와 서로 복사하는 속도 느림" 섹션을 참조하세요.

## <a name="jittery-or-sawtooth-pattern-for-iops"></a>IOPS에 대한 떨림 또는 톱니 모양 패턴

### <a name="cause"></a>원인

클라이언트 애플리케이션이 지속적으로 기준 IOPS를 초과합니다. 현재는 요청 부하의 서비스 측 다듬기가 없습니다. 클라이언트가 기준 IOPS를 초과하는 경우 서비스에 의해 제한됩니다. 제한으로 인해 클라이언트에 떨림 또는 톱니 모양의 IOPS 패턴이 나타날 수 있습니다. 이 경우 클라이언트에서 달성하는 평균 IOPS는 기준 IOPS보다 낮을 수 있습니다.

### <a name="workaround"></a>해결 방법
- 공유가 제한되지 않도록 클라이언트 애플리케이션에서 요청 부하를 줄입니다.
- 공유가 제한되지 않도록 공유의 할당량을 늘립니다.

## <a name="excessive-directoryopendirectoryclose-calls"></a>과도한 DirectoryOpen/DirectoryClose 호출

### <a name="cause"></a>원인

**DirectoryOpen/DirectoryClose** 호출 수가 최상위 API 호출 중 하나이고 클라이언트가 많은 호출을 수행할 할 것으로 예상되지 않는 경우 Azure 클라이언트 VM에 설치된 바이러스 백신 소프트웨어로 인해 문제가 발생할 수 있습니다.

### <a name="workaround"></a>해결 방법

- 이 문제에 대한 해결 방법은 [Windows용 4월 플랫폼 업데이트](https://support.microsoft.com/help/4052623/update-for-windows-defender-antimalware-platform)에서 확인할 수 있습니다.

## <a name="file-creation-is-slower-than-expected"></a>파일 만들기가 예상보다 느림

### <a name="cause"></a>원인

많은 수의 파일을 생성하는 워크로드는 프리미엄 파일 공유와 표준 파일 공유 간의 성능에 큰 차이가 없습니다.

### <a name="workaround"></a>해결 방법

- 없음

## <a name="slow-performance-from-windows-81-or-server-2012-r2"></a>Windows 8.1 또는 Server 2012 R2의 성능 저하

### <a name="cause"></a>원인

I/O를 많이 사용하는 워크로드를 위해 Azure 파일 공유에 액세스하는 대기 시간이 예상보다 깁니다.

### <a name="workaround"></a>해결 방법

- 사용 가능한 [핫픽스](https://support.microsoft.com/help/3114025/slow-performance-when-you-access-azure-files-storage-from-windows-8-1)를 설치합니다.

## <a name="smb-multichannel-is-not-being-triggered"></a>SMB 다중 채널이 트리거되지 않습니다.

### <a name="cause"></a>원인

다시 탑재하지 않았는데 최근 SMB 다중 채널 구성 설정에 변경 사항이 있습니다.

### <a name="solution"></a>해결 방법
 
-   Windows SMB 클라이언트 또는 계정 SMB 다중 채널 구성 설정을 변경한 후에는 공유를 탑재 해제하고 60초 동안 기다린 후 공유를 다시 탑재하여 다중 채널을 트리거해야 합니다.
-   Windows 클라이언트 OS의 경우 큐 깊이가 높은 IO 부하(QD=8)를 생성합니다. 예를 들어 파일을 복사하여 SMB 다중 채널을 트리거합니다.  서버 OS의 경우 SMB 다중 채널은 QD=1로 트리거되고, 이는 공유에 대한 IO를 시작하는 즉시 발생합니다.

## <a name="high-latency-on-web-sites-hosted-on-file-shares"></a>파일 공유에서 호스트되는 웹 사이트의 대기 시간이 김 

### <a name="cause"></a>원인  

파일 공유에 대한 파일 변경 알림 수가 많으면 상당한 대기 시간이 발생할 수 있습니다. 이는 일반적으로 심층 중첩 디렉터리 구조를 가진 파일 공유에서 호스트되는 웹 사이트에서 발생합니다. 일반적인 시나리오는 IIS에서 호스트되는 웹 애플리케이션으로, 기본 구성의 각 디렉터리에 대해 파일 변경 알림이 설정됩니다. 클라이언트에서 등록 된 공유에 대 한 각 변경 내용 ([Readdirectorychanges w](/windows/win32/api/winbase/nf-winbase-readdirectorychangesw))은 파일 서비스에서 클라이언트로의 변경 알림을 푸시하 며,이는 시스템 리소스를 사용 하 고 변경 내용 수를 사용 하 여 증가를 발급 합니다. 이로 인해 공유 제한이 발생할 수 있으므로 클라이언트 쪽 대기 시간이 더 길어질 수 있습니다. 

이를 확인하려면 포털에서 Azure 메트릭을 사용하면 됩니다. 

1. Azure Portal에서 스토리지 계정으로 이동합니다. 
1. 왼쪽 메뉴의 모니터링 아래에서 메트릭을 선택합니다. 
1. 파일을 스토리지 계정 범위에 대한 메트릭 네임스페이스로 선택합니다. 
1. 트랜잭션을 메트릭으로 선택합니다. 
1. ResponseType에 대 한 필터를 추가 하 고 요청에 SuccessWithThrottling (SMB 또는 NFS 용) 또는 ClientThrottlingError (REST)의 응답 코드가 있는지 확인 합니다.

### <a name="solution"></a>해결 방법 

- 파일 변경 알림이 사용되지 않으면 파일 변경 알림(기본 설정)을 사용하지 않도록 설정합니다.
    - FCNMode를 업데이트하여 [파일 변경 알림을 사용하지 않도록 설정](https://support.microsoft.com/help/911272/fix-asp-net-2-0-connected-applications-on-a-web-site-may-appear-to-sto)합니다. 
    - 레지스트리에서 `HKEY_LOCAL_MACHINE\System\CurrentControlSet\Services\W3SVC\Parameters\ConfigPollMilliSeconds `를 설정하고 W3WP 프로세스를 다시 시작하여 IIS 작업자 프로세스(W3WP) 폴링 간격을 0으로 업데이트합니다. 이 설정에 대한 자세한 내용은 [IIS의 여러 부분에서 사용되는 일반적인 레지스트리 키](/troubleshoot/iis/use-registry-keys#registry-keys-that-apply-to-iis-worker-process-w3wp)를 참조하세요.
- 볼륨을 줄이려면 파일 변경 알림 폴링 간격의 빈도를 늘립니다.
    - 사용자 요구 사항에 따라 W3WP 작업자 프로세스 폴링 간격을 더 높은 값(예: 10분 또는 30분)으로 업데이트합니다. [에서](/troubleshoot/iis/use-registry-keys#registry-keys-that-apply-to-iis-worker-process-w3wp) `HKEY_LOCAL_MACHINE\System\CurrentControlSet\Services\W3SVC\Parameters\ConfigPollMilliSeconds `를 설정하고 W3WP 프로세스를 다시 시작합니다.
- 웹 사이트의 매핑된 실제 디렉터리에 중첩된 디렉터리 구조가 있는 경우 파일 변경 알림의 범위를 제한하여 알림 볼륨을 줄일 수 있습니다. 기본적으로 IIS는 가상 디렉터리가 매핑된 실제 디렉터리와 해당 실제 디렉터리의 모든 자식 디렉터리에 있는 Web.config 파일의 구성을 사용합니다. 자식 디렉터리에서 Web.config 파일을 사용하지 않으려면 가상 디렉터리의 allowSubDirConfig 특성에 대해 false를 지정합니다. 자세한 내용은 [여기](/iis/get-started/planning-your-iis-architecture/understanding-sites-applications-and-virtual-directories-on-iis#virtual-directories)에서 찾을 수 있습니다. 
    - Web.Config의 IIS 가상 디렉터리 "allowSubDirConfig" 설정을 *false* 로 설정하여 매핑된 실제 자식 디렉터리를 범위에서 제외합니다.  

## <a name="how-to-create-an-alert-if-a-file-share-is-throttled"></a>파일 공유를 제한하는 경우 경고를 만드는 방법

1. **Azure portal** 에서 **스토리지 계정** 으로 이동합니다.
2. **모니터링** 섹션에서 **경고** 를 클릭한 다음, **+ 새 경고 규칙** 을 클릭합니다.
3. **리소스 편집** 을 클릭하고 스토리지 계정에 대 한 **파일 리소스 유형을** 선택한 다음, **완료** 를 클릭합니다. 예를 들어 스토리지 계정 이름이 `contoso`인 경우 `contoso/file` 리소스를 선택합니다.
4. **조건 추가** 를 클릭하여 조건을 추가합니다.
5. 스토리지 계정에 지원되는 신호 목록이 표시되면 **트랜잭션** 메트릭을 선택합니다.
6. **신호 논리 구성** 블레이드에서 **차원 이름** 드롭다운을 클릭하고, **응답 형식** 을 선택합니다.
7. **차원 값** 드롭다운을 클릭하고 파일 공유에 대한 적절한 응답 형식을 선택합니다.

    표준 파일 공유의 경우 다음과 같은 응답 형식을 선택합니다.

    - SuccessWithThrottling
    - SuccessWithShareIopsThrottling
    - ClientShareIopsThrottlingError

    프리미엄 파일 공유의 경우 다음과 같은 응답 형식을 선택합니다.

    - SuccessWithShareEgressThrottling
    - SuccessWithShareIngressThrottling
    - SuccessWithShareIopsThrottling
    - ClientShareEgressThrottlingError
    - ClientShareIngressThrottlingError
    - ClientShareIopsThrottlingError

   > [!NOTE]
   > 응답 형식이 **차원 값** 드롭다운에 나열되지 않는 경우는 리소스가 제한되지 않았음을 의미합니다. 차원 값을 추가하려면 **차원 값** 드롭다운 목록 옆에 있는 **사용자 지정 값 추가** 를 선택하고 응답 형식(예: **SuccessWithThrottling**)을 입력한 다음, **확인** 을 선택하고 이 단계를 반복하여 해당 파일 공유에 적용 가능한 모든 응답 형식을 추가합니다.

8. **프리미엄 파일 공유** 의 경우 **차원 이름** 드롭다운을 클릭하고 **파일 공유** 를 선택합니다. **표준 파일 공유** 의 경우 **#10단계** 로 건너뜁니다.

   > [!NOTE]
   > 파일 공유가 표준 파일 공유인 경우 표준 파일 공유에 대한 공유 메트릭을 사용할 수 없으므로 **파일 공유** 차원에 파일 공유가 나열되지 않습니다. 표준 파일 공유에 대한 제한 경고는 스토리지 계정 내의 파일 공유가 제한되는 경우 트리거되며 이 경고는 제한된 파일 공유를 식별하지 않습니다. 표준 파일 공유에는 공유별 메트릭을 사용할 수 없으므로 스토리지 계정마다 하나의 파일 공유를 사용하는 것이 좋습니다.

9. **차원 값** 드롭다운을 클릭하고 경고를 표시할 파일 공유를 선택합니다.
10. **경고 매개 변수**(임계값, 연산자, 집계 세분성 및 평가 빈도)를 정의하고 **완료** 를 클릭합니다.

    > [!TIP]
    > 정적 임계값을 사용하는 경우 메트릭 차트는 파일 공유가 현재 제한되는 경우 적절한 임계값을 결정하는 데 도움이 될 수 있습니다. 동적 임계값을 사용하는 경우 메트릭 차트는 최근 데이터를 기반으로 계산한 임계값을 표시합니다.

11. **작업 그룹 추가** 를 클릭하고, 기존 작업 그룹을 선택하거나 새 작업 그룹을 만들어 **작업 그룹**(이메일, SMS 등)을 경고에 추가합니다.
12. **경고 세부 정보**(예: **경고 규칙 이름**, **설명** 및 **심각도**)를 입력합니다.
13. **경고 규칙 만들기** 를 클릭하여 경고를 만듭니다.

Azure Monitor에서 경고를 구성하는 방법에 대한 자세한 내용은 [Microsoft Azure의 경고 개요](../../azure-monitor/alerts/alerts-overview.md)를 참조하세요.

## <a name="how-to-create-alerts-if-a-premium-file-share-is-trending-toward-being-throttled"></a>프리미엄 파일 공유가 제한되는 경향이 있는 경우 경고를 생성하는 방법

1. Azure Portal에서 스토리지 계정으로 이동합니다.
2. **모니터링** 섹션에서 **경고** 를 선택한 다음 **새 경고 규칙** 을 선택합니다.
3. **리소스 편집** 을 선택하고 스토리지 계정에 대 한 **파일 리소스 형식** 을 선택한 다음, **완료** 를 선택합니다. 예를 들어 스토리지 계정 이름이 *contoso* 인 경우 contoso/file 리소스를 선택합니다.
4. **조건 선택** 을 선택하여 조건을 추가합니다.
5. 스토리지 계정에 지원되는 신호 목록에서 **송신** 메트릭을 선택합니다.

   > [!NOTE]
   > 수신, 송신 또는 트랜잭션 값이 설정된 임계값을 초과하는 경우 경고를 표시하려면 세 가지의 개별 경고를 만들어야 합니다. 이는 모든 조건이 충족되는 경우에만 경고가 트리거되기 때문입니다. 예를 들어 모든 조건을 하나의 경고에 배치하는 경우 수신, 송신 및 트랜잭션이 임계값을 초과하는 경우에만 경고가 표시됩니다.

6. 아래로 스크롤합니다. **차원 이름** 드롭다운 목록에서 **파일 공유** 를 선택합니다.
7. **차원 값** 드롭다운 목록에서 경고를 표시할 파일 공유 또는 공유를 선택합니다.
8. **연산자**, **임계값**, **집계 세분성** 및 **평가 빈도** 드롭다운 목록에서 값을 선택하여 경고 매개 변수를 정의한 다음, **완료** 를 선택합니다.

   송신, 수신 및 트랜잭션 메트릭은 초당 송신, 수신 및 I/O를 프로비전하는 경우 분 단위로 표현됩니다. 따라서, 예를 들어 프로비전된 송신이 초당 90&nbsp;MiB/s이고 프로비전된 송신의 80&nbsp;%를 임계값으로 하려면 다음과 같은 경고 매개 변수를 선택합니다. 
   - **임계값**: *75497472* 
   - **연산자**: *크거나 같음*
   - **집계 유형**: *평균*
   
   경고를 표시하려는 경우에 따라 **집계 세분성** 및 **평가 빈도** 값을 선택할 수도 있습니다. 예를 들어 알림에서 1시간 동안의 평균 수신을 확인하고 알림 규칙이 1시간마다 실행되도록 하려면 다음을 선택합니다.
   - **집계 세분성**: *1시간*
   - **평가 빈도**: *1시간*

9. **작업 그룹 추가** 를 선택한 다음, 기존 작업 그룹을 선택하거나 새 작업 그룹을 만들어 경고에 대한 작업 그룹(예: 메일 또는 SMS)을 추가합니다.
10. **경고 규칙 이름**, **설명** 및 **심각도** 와 같은 경고 세부 정보를 입력합니다.
11. **경고 규칙 만들기** 를 선택하여 경고를 만듭니다.

    > [!NOTE]
    > - 프리미엄 파일 공유가  *프로비전된 수신으로 인해* 제한에 가까워지고 있다는 알림을 받으려면 이전 지침을 따르되 다음과 같이 변경합니다.
    >    - 5단계에서 **송신** 대신 **수신** 메트릭을 선택합니다.
    >
    > - 프리미엄 파일 공유가  *프로비전된 IOPS로 인해* 제한에 가까워지고 있다는 알림을 받으려면 이전 지침을 따르되 다음과 같이 변경합니다.
    >    - 5단계에서 **송신** 대신 **트랜잭션** 메트릭을 선택합니다.
    >    - 10단계에서 **집계 유형에** 대한 유일한 옵션은 *합계* 입니다. 따라서 임계값은 선택한 집계 세분성에 따라 달라집니다. 예를 들어 임계값을 &nbsp; 프로비전된 기준 IOPS의 80%로 선택하고 **집계 세분성** 으로 *1 시간* 을 선택하는 경우 **임계값** 은 기준 IOPS(바이트) &times;&nbsp;0.8 &times;&nbsp;3600이 됩니다. 

Azure Monitor에서 경고를 구성하는 방법에 대한 자세한 내용은 [Microsoft Azure의 경고 개요](../../azure-monitor/alerts/alerts-overview.md)를 참조하세요.

## <a name="see-also"></a>참고 항목
- [Windows에서 Azure Files 문제 해결](storage-troubleshoot-windows-file-connection-problems.md)  
- [Linux에서 Azure Files 문제 해결](storage-troubleshoot-linux-file-connection-problems.md)  
- [Azure Files FAQ](storage-files-faq.md)

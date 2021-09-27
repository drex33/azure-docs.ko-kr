---
title: Azure에서 Linux VM의 시간 동기화
description: Linux 가상 머신의 시간 동기화입니다.
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 09/03/2021
ms.author: cynthn
ms.openlocfilehash: 324373fc56ae1a57adfb522ca77f06f2c080074c
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124776522"
---
# <a name="time-sync-for-linux-vms-in-azure"></a>Azure에서 Linux VM의 시간 동기화

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 균일 확장 집합 

시간 동기화는 보안 및 이벤트 상관 관계에서 중요합니다. 경우에 따라 시간 동기화는 분산 트랜잭션 구현에 사용됩니다. 여러 컴퓨터 시스템 간에 시간 정확도는 동기화를 통해 이루어집니다. 동기화는 재부팅 및 시간 원본 서버와 시간을 페치하는 컴퓨터 간의 네트워크 트래픽을 비롯해 여러 작업에서 영향을 받을 수 있습니다. 

Azure는 Windows Server 2016을 실행하는 인프라의 지원을 받습니다. Windows Server 2016은 시간을 수정하고 로컬 시계에 영향을 미치는 데 사용된 알고리즘을 개선하여 UTC와 동기화했습니다.  Windows Server 2016의 정확한 시간 기능은 정확한 시간을 위해 VMICTimeSync 서비스가 호스트를 통해 VM을 제어하는 방법을 크게 개선했습니다. 이러한 개선은 VM 시작 또는 VM 복원 시 시작 시간의 정확도 향상을 포함하며 대기 시간 수정을 중단합니다. 

> [!NOTE]
> Windows 시간 서비스에 대한 간략한 개요는 이 [고급 개요 비디오](https://aka.ms/WS2016TimeVideo)를 살펴보세요.
>
> 자세한 내용은 [Windows Server 2016의 정확한 시간](/windows-server/networking/windows-time-service/accurate-time)을 참조하세요. 

## <a name="overview"></a>개요

컴퓨터 시계의 정확도는 컴퓨터 시계가 UTC(협정 세계시) 표준 시간에 근접한 정도로 측정됩니다. UTC는 300년에 1초밖에 차이가 나지 않는 여러 국가의 정밀한 원자 시계 샘플로 정의됩니다. 그러나 UTC를 직접 읽으려면 특수화된 하드웨어가 필요합니다. 대신 시간 서버가 UTC로 동기화되고 다른 컴퓨터에서 액세스되어 확장성 및 안정성을 제공합니다. 모든 컴퓨터에는 사용할 시간 서버를 알고 있으며 주기적으로 컴퓨터 시계를 수정해야 하는지 여부를 확인하고 필요한 경우 시간을 조정하는 시간 동기화 서비스가 실행되고 있습니다. 

Azure 호스트는 GPS 안테나가 있는 Microsoft 소유의 Stratum 1 디바이스에서 시간을 사용하는 내부 Microsoft 시간 서버에 동기화됩니다. Azure의 가상 머신은 정확한 시간(*호스트 시간*)을 VM에 전달하기 위해 호스트에 의존할 수 있거나, VM이 직접 시간 서버 또는 둘의 조합에서 시간을 가져올 수 있습니다. 

독립 실행형 하드웨어에서 Linux OS는 부팅 시 호스트 하드웨어 시계만 읽습니다. 그 이후에는 Linux 커널의 인터럽트 타이머를 사용하여 시계를 유지 관리합니다. 이 구성에서 시계는 시간이 지나면서 변경됩니다. Azure의 최신 Linux 배포에서 VM은 LIS(Linux 통합 서비스)에 포함된 VMICTimeSync 공급자를 사용하여 호스트에서 시계 업데이트를 더 자주 쿼리할 수 있습니다.

호스트와 가상 머신의 상호 작용은 시계에도 영향을 줄 수 있습니다. [메모리 보존 유지 관리](../maintenance-and-updates.md#maintenance-that-doesnt-require-a-reboot) 중에는 VM이 최대 30초 동안 일시 중지됩니다. 예를 들어 유지 관리를 시작하면 먼저 VM 시계는 오전 10:00:00시를 표시한 후, 28초간 지속됩니다. VM이 다시 시작되면 VM 시계는 여전히 오전 10:00:00시를 표시한 다음, 28초가 해제됩니다. 이를 수정하려면 VMICTimeSync 서비스가 호스트에서 발생하는 상황을 모니터링하고 보완하기 위해 Linux VM의 하루 중 시간 시계를 업데이트합니다.

시간 동기화가 작동하지 않으면 VM 시계는 오류를 누적하게 됩니다. 단 하나의 VM이 있는 경우 워크로드가 상당히 정확한 시간 기록을 요구하지 않는 한 영향은 크지 않을 수 있습니다. 하지만 대부분의 경우에 트랜잭션을 추적하는 데 시간을 사용하고 전체 배포 과정에 걸쳐 시간이 일관되어야 하는 상호 연결된 VM이 여러 개 있습니다. VM 간의 시간이 다른 경우 다음과 같은 영향이 표시됩니다.

- 인증이 실패합니다. Kerberos 또는 인증서 종속 기술 같은 보안 프로토콜은 시스템에서 일관성이 유지되는 시간에 의존합니다.
- 로그(또는 기타 데이터)가 시간마다 다를 경우 시스템에서 발생한 상황을 파악하기는 매우 어렵습니다. 동일한 이벤트가 서로 다른 시간대에 발생되는 것 같아 상관 관계를 어렵게 만듭니다.
- 시계가 꺼지면 청구가 부정확하게 계산될 수 있습니다.


## <a name="configuration-options"></a>구성 옵션

시간 동기화를 사용하려면 Linux VM에서 시간 동기화 서비스가 실행되어야 하며 동기화할 정확한 시간 정보 원본이 필요합니다.
일반적으로 ntpd 또는 chronyd는 시간 동기화 서비스로 사용되지만 다른 오픈 소스 시간 동기화 서비스도 사용할 수 있습니다.
정확한 시간 정보 원본은 Azure 호스트 또는 공용 인터넷을 통해 액세스되는 외부 시간 서비스가 될 수 있습니다.
VMICTimeSync 서비스는 위 설명대로 호스트 유지 관리를 위해 일시 중지한 후를 제외하고 Azure 호스트와 Linux VM 간에 지속적으로 시간을 동기화하지 않습니다. 

지금까지 Linux에서 대부분의 Azure Marketplace 이미지를 다음 두 가지 방법 중 하나로 구성했습니다.
- 기본적으로 실행되는 시간 동기화 서비스가 없습니다.
- ntpd는 시간 동기화 서비스로 실행되고 네트워크를 통해 액세스되는 외부 NTP 시간 원본 간에 동기화됩니다. 예를 들어 Ubuntu 18.04 LTS Marketplace 이미지는 **ntp.ubuntu.com** 을 사용합니다.

ntpd가 올바르게 동기화하고 있는지 확인하려면 `ntpq -p` 명령을 실행합니다.

2021년 초부터 Linux를 사용하는 최신 Azure Marketplace 이미지는 시간 동기화 서비스로 chronyd를 사용하도록 변경되고 있으며 chronyd는 외부 NTP 시간 원본이 아닌 Azure 호스트와 동기화하도록 구성됩니다. Azure 호스트 시간은 매우 정확하고 안정적으로 유지 관리되며 공용 인터넷을 통해 외부 NTP 시간 원본에 액세스하는 데 내재된 가변 네트워크 지연 없이 액세스할 수 있으므로 일반적으로 동기화에 가장 좋은 시간 원본입니다.

VMICTimeSync는 병렬로 사용되며 다음 두 가지 기능을 제공합니다.
- 호스트 유지 관리 이벤트 후 Linux VM 하루 중 시간 시계를 즉시 업데이트합니다.
- IEEE 1588 PTP(Precision Time Protocol) 하드웨어 시계 원본을 Azure 호스트에서 정확한 하루 중 시간을 제공하는 /dev/ptp 디바이스로 인스턴스화합니다.  chronyd는 최신 Linux 이미지의 기본 구성인 이 시간 원본과 동기화하도록 구성될 수 있습니다. 커널 버전 4.11 이상(또는 RHEL/CentOS 7의 경우 버전 3.10.0-693 이상)이 있는 Linux 배포는 /dev/ptp 디바이스를 지원합니다.  Azure 호스트 시간에 /dev/ptp를 지원하지 않는 이전 커널 버전의 경우 외부 시간 원본 간의 동기화만 가능합니다.

물론 기본 구성을 변경할 수 있습니다. ntpd 및 외부 시간 원본을 사용하도록 구성된 이전 이미지가 Azure 호스트 시간에 chronyd 및 /dev/ptp 디바이스를 사용하도록 변경할 수 있습니다.  마찬가지로, /dev/ptp 디바이스를 통해 Azure 호스트 시간을 사용하는 이미지는 애플리케이션이나 워크로드에 필요한 경우 외부 NTP 시간 원본을 사용하도록 구성될 수 있습니다.


## <a name="tools-and-resources"></a>도구 및 리소스

시간 동기화 구성을 검사하기 위한 몇 가지 기본 명령이 있습니다. Linux 배포 설명서는 해당 배포의 시간 동기화를 구성하는 최상의 방법에 대한 자세한 내용을 제공합니다.

### <a name="integration-services"></a>통합 서비스

통합 서비스(hv_utils)가 로드되었는지 확인합니다.

```bash
lsmod | grep hv_utils
```
다음과 유사한 내용이 표시되어야 합니다.

```
hv_utils               24418  0
hv_vmbus              397185  7 hv_balloon,hyperv_keyboard,hv_netvsc,hid_hyperv,hv_utils,hyperv_fb,hv_storvsc
```

### <a name="check-for-ptp-clock-source"></a>PTP 클록 원본 확인

Azure 호스트에 해당하는 최신 버전의 Linux, PTP(Precision Time Protocol) 시계 원본을 VMICTimeSync 공급자의 일부로 사용할 수 있습니다.
이전 버전의 Red Hat Enterprise Linux 또는 CentOS 7.x에서 [Linux 통합 서비스](https://github.com/LIS/lis-next)를 다운로드하여 업데이트된 드라이버를 설치하는 데 사용할 수 있습니다. PTP 클록 원본을 사용할 수 있는 경우 Linux 디바이스는 /dev/ptp *x* 형식이 됩니다. 

사용 가능한 PTP 시계 원본을 확인합니다.

```bash
ls /sys/class/ptp
```

이 예제에서 반환된 값은 *ptp0* 이므로 이를 사용하여 시계 이름을 확인합니다. 디바이스를 확인하려면 시계 이름을 확인합니다.

```bash
cat /sys/class/ptp/ptp0/clock_name
```

이는 Azure 호스트를 의미하는 `hyperv`를 반환해야 합니다.

가속화된 네트워킹을 사용하는 Linux VM에서 Mellanox mlx5 드라이버가 /dev/ptp 디바이스를 만들므로 PTP 디바이스 여러 개가 나열될 수 있습니다.
Linux가 부팅될 때마다 초기화 순서가 다를 수 있으므로 Azure 호스트에 해당하는 PTP 디바이스가 /dev/ptp0 또는 /dev/ptp1이 될 수 있으며 이로 인해 올바른 시계 원본을 사용하여 chronyd를 구성하기가 어렵습니다. 이 문제가 해결되도록 최신 Linux 이미지에는 Azure 호스트에 해당하는 /dev/ptp 항목에 대한 symlink /dev/ptp_hyperv를 만드는 udev 규칙이 있습니다. chrony는 /dev/ptp0 또는/dev/ptp1 대신 이 symlink을 사용하도록 구성되어야 합니다.

### <a name="chrony"></a>chrony

Ubuntu 19.10 이상 버전, Red Hat Enterprise Linux 및 CentOS 8.x, [chrony](https://chrony.tuxfamily.org/)는 PTP 원본 클록을 사용하도록 구성됩니다. Chrony가 아닌 이전 버전의 Linux 릴리스에서는 NTPD(Network Time Protocol Daemon)를 사용하며, 이는 PTP 원본을 지원하지 않습니다. 해당 릴리스에서 PTP를 사용하도록 설정하려면 다음 문을 사용하여 chrony.conf에서 chrony를 수동으로 설치하고 구성해야 합니다.

```bash
refclock PHC /dev/ptp0 poll 3 dpoll -2 offset 0 stratum 2
```

/dev/ptp_hyperv symlink를 사용할 수 있는 경우 /dev/ptp0 대신 사용하여 Mellanox mlx5 드라이버에서 만든 /dev/ptp 디바이스와 혼동하지 않도록 합니다.

계층 정보는 Azure 호스트에서 Linux 게스트로 자동으로 전달되지 않습니다. 앞의 구성 줄은 Azure 호스트 시간 원본이 계층 2로 처리되도록 지정합니다. 그러면 Linux 게스트가 자체적으로 Stratum 3으로 보고됩니다. Linux 게스트가 자신을 다르게 보고하도록 하려면 구성 줄에서 계층 설정을 변경할 수 있습니다.

기본적으로 chronyd는 시스템 클록을 가속화하거나 느리게 하여 시간 드리프트를 수정합니다. 드리프트가 너무 커지면 Chrony는 드리프트를 수정하지 못합니다. 이를 해결하기 위해 드리프트가 지정된 임계값을 초과하는 경우 시간 동기화를 강제로 수행하도록 **/etc/chrony.conf** 의 `makestep` 매개 변수를 변경할 수 있습니다.

 ```bash
makestep 1.0 -1
```

여기서 Chrony는 드리프트가 1초보다 큰 경우 시간 업데이트를 강제로 수행합니다. 변경 내용을 적용하려면 chronyd 서비스를 다시 시작합니다.

```bash
systemctl restart chronyd
```

Ubuntu 및 NTP에 대한 자세한 내용은 [시간 동기화](https://ubuntu.com/server/docs/network-ntp)를 참조하세요.

Red Hat 및 NTP에 대한 자세한 내용은 [NTP 구성](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/ch-configuring_ntp_using_ntpd#s1-Configure_NTP)을 참조하세요. 

Chrony에 대한 자세한 내용은 [chrony 사용](https://access.redhat.com/documentation/en-us/red_hat_enterprise_linux/7/html/system_administrators_guide/ch-configuring_ntp_using_the_chrony_suite#sect-Using_chrony)을 참조하세요.

### <a name="systemd"></a>systemd 

19.10 이전 SUSE 및 Ubuntu 릴리스에서는 [systemd](https://www.freedesktop.org/wiki/Software/systemd/)를 사용하여 시간 동기화를 구성합니다. Ubuntu에 대한 자세한 내용은 [시간 동기화](https://help.ubuntu.com/lts/serverguide/NTP.html)를 참조하세요. SUSE에 대한 자세한 내용은 [SUSE Linux Enterprise Server 12 SP3 릴리스 정보](https://www.suse.com/releasenotes/x86_64/SUSE-SLES/12-SP3/#InfraPackArch.ArchIndependent.SystemsManagement)의 4.5.8 섹션을 참조하세요.

## <a name="next-steps"></a>다음 단계

자세한 내용은 [Windows Server 2016의 정확한 시간](/windows-server/networking/windows-time-service/accurate-time)을 참조하세요.



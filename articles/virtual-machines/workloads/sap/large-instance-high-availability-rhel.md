---
title: RHEL의 SAP를 위한 Azure 대규모 인스턴스 고가용성
description: Red Hat Enterprise Linux에서 Pacemaker 클러스터를 사용하여 SAP HANA 데이터베이스 장애 조치(failover)를 자동화하는 방법을 알아봅니다.
author: jaawasth
ms.author: jaawasth
ms.service: virtual-machines-sap
ms.topic: how-to
ms.date: 04/19/2021
ms.openlocfilehash: 199d38a4c6ddca96c745342bcef0b07dc78b48bd
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/03/2021
ms.locfileid: "129401297"
---
# <a name="azure-large-instances-high-availability-for-sap-on-rhel"></a>RHEL의 SAP를 위한 Azure 대규모 인스턴스 고가용성

> [!NOTE]
> 이 문서에는 Microsoft에서 더 이상 사용 하지 않는 용어 *블랙 리스트* 및  *슬레이브* 용어에 대 한 참조가 포함 되어 있습니다. 소프트웨어에서 용어가 제거되면 이 문서에서 해당 용어가 제거됩니다.

이 문서에서는 RHEL 7에서 Pacemaker 클러스터를 구성하여 SAP HANA 데이터베이스 장애 조치(failover)를 자동화하는 방법을 알아봅니다. 이 가이드의 단계를 진행하려면 Linux, SAP HANA 및 Pacemaker에 대한 지식이 있어야 합니다.

다음 표에는 이 문서에서 사용되는 호스트 이름이 나와 있습니다. 이 문서의 코드 블록에서는 실행해야 하는 명령과 명령을 실행한 결과를 보여 줍니다. 각 명령에서 어느 노드를 참조하는지 주의 깊게 확인하세요.

| 유형 | 호스트 이름 | 노드|
|-------|-------------|------|
|기본 호스트|`sollabdsm35`|노드 1|
|보조 호스트|`sollabdsm36`|노드 2|

## <a name="configure-your-pacemaker-cluster"></a>Pacemaker 클러스터 구성


클러스터 구성을 시작하려면 먼저 노드 간에 SSH 키 교환을 설정하여 신뢰를 구현해야 합니다.

1. 다음 명령을 사용하여 두 노드에서 동일한 `/etc/hosts`를 만듭니다.

    ```
    root@sollabdsm35 ~]# cat /etc/hosts
    27.0.0.1 localhost localhost.azlinux.com
    10.60.0.35 sollabdsm35.azlinux.com sollabdsm35 node1
    10.60.0.36 sollabdsm36.azlinux.com sollabdsm36 node2
    10.20.251.150 sollabdsm36-st
    10.20.251.151 sollabdsm35-st
    10.20.252.151 sollabdsm36-back
    10.20.252.150 sollabdsm35-back
    10.20.253.151 sollabdsm36-node
    10.20.253.150 sollabdsm35-node
    ```

2.  SSH 키를 만들고 교환합니다.
    1. ssh 키를 생성합니다.

    ```
       [root@sollabdsm35 ~]# ssh-keygen -t rsa -b 1024
       [root@sollabdsm36 ~]# ssh-keygen -t rsa -b 1024
    ```
    2. 암호 없는 ssh를 사용할 수 있도록 키를 다른 호스트에 복사합니다.
    
       ```
       [root@sollabdsm35 ~]# ssh-copy-id -i /root/.ssh/id_rsa.pub sollabdsm35
       [root@sollabdsm35 ~]# ssh-copy-id -i /root/.ssh/id_rsa.pub sollabdsm36
       [root@sollabdsm36 ~]# ssh-copy-id -i /root/.ssh/id_rsa.pub sollabdsm35
       [root@sollabdsm36 ~]# ssh-copy-id -i /root/.ssh/id_rsa.pub sollabdsm36
       ```

3.  두 노드에서 selinux를 사용하지 않도록 설정합니다.
    ```
    [root@sollabdsm35 ~]# vi /etc/selinux/config

    ...

    SELINUX=disabled

    [root@sollabdsm36 ~]# vi /etc/selinux/config

    ...

    SELINUX=disabled

    ```  

4. 서버를 재부팅하고 다음 명령을 사용하여 selinux의 상태를 확인합니다.
    ```
    [root@sollabdsm35 ~]# sestatus

    SELinux status: disabled

    [root@sollabdsm36 ~]# sestatus

    SELinux status: disabled
    ```

5. NTP(네트워크 시간 프로토콜)를 구성합니다. 두 클러스터 노드의 시간과 표준 시간대가 일치해야 합니다. 다음 명령을 사용하여 `chrony.conf`를 열고 파일의 내용을 확인합니다.
    1. 구성 파일에 다음 내용이 추가되어 있을 것입니다. 환경에 맞게 값을 변경합니다.
        ```
        vi /etc/chrony.conf
    
         Use public servers from the pool.ntp.org project.
    
         Please consider joining the pool (http://www.pool.ntp.org/join.html).
    
        server 0.rhel.pool.ntp.org iburst
       ```
    
    2. chrony 서비스를 사용하도록 설정합니다. 
      
        ```
        systemctl enable chronyd
    
        systemctl start chronyd
    
        
    
        chronyc tracking
    
        Reference ID : CC0BC90A (voipmonitor.wci.com)
    
        Stratum : 3
    
        Ref time (UTC) : Thu Jan 28 18:46:10 2021
    
        chronyc sources
    
        210 Number of sources = 8
    
        MS Name/IP address Stratum Poll Reach LastRx Last sample
    
        ===============================================================================
    
        ^+ time.nullroutenetworks.c> 2 10 377 1007 -2241us[-2238us] +/- 33ms
    
        ^* voipmonitor.wci.com 2 10 377 47 +956us[ +958us] +/- 15ms
    
        ^- tick.srs1.ntfo.org 3 10 177 801 -3429us[-3427us] +/- 100ms
        ```

6. 시스템을 업데이트합니다.
    1. 먼저 시스템에 최신 업데이트를 철시한 후에 SBD 디바이스의 설치를 시작합니다.
    1. 고객은 [RHEL 고가용성 클러스터 지원 정책 - 클러스터 내 SAP HANA 관리](https://access.redhat.com/articles/3397471)에 설명된 대로 resource-agents-sap-hana 패키지 버전 4.1.1-12.el7_6.26 이상이 설치되어 있는지 확인해야 합니다.
    1. 시스템의 전체 업데이트를 원하지 않는 경우에는 최소한 다음 패키지를 업데이트 하는 것이 좋습니다.
        1. `resource-agents-sap-hana`
        1. `selinux-policy`
        1. `iscsi-initiator-utils`

  
        ```
        node1:~ # yum update
        ```

7. SAP HANA 및 RHEL-HA 리포지토리를 설치합니다.

    ```
    subscription-manager repos –list

    subscription-manager repos
    --enable=rhel-sap-hana-for-rhel-7-server-rpms

    subscription-manager repos --enable=rhel-ha-for-rhel-7-server-rpms
    ```
      

8. 모든 노드에 Pacemaker, SBD, OpenIPMI, ipmitool 및 fencing_sbd 도구를 설치 합니다.

    ``` 
    yum install pcs sbd fence-agent-sbd.x86_64 OpenIPMI
    ipmitool
    ```

  ## <a name="configure-watchdog"></a>Watchdog 구성

이 섹션에서는 Watchdog를 구성하는 방법을 알아봅니다. 이 섹션에서는 이 문서의 시작 부분에서 언급했던 두 개의 호스트 `sollabdsm35`와 `sollabdsm36`을 그대로 사용합니다.

1. 모든 시스템에서 watchdog 디먼이 실행 중이 아닌지 확인합니다.
    ```
    [root@sollabdsm35 ~]# systemctl disable watchdog
    [root@sollabdsm36 ~]# systemctl disable watchdog
    [root@sollabdsm35 ~]# systemctl stop watchdog
    [root@sollabdsm36 ~]# systemctl stop watchdog
    [root@sollabdsm35 ~]# systemctl status watchdog

    ● watchdog.service - watchdog daemon

    Loaded: loaded (/usr/lib/systemd/system/watchdog.service; disabled;
    vendor preset: disabled)

    Active: inactive (dead)

    Nov 28 23:02:40 sollabdsm35 systemd[1]: Collecting watchdog.service

    ```

2. 설치 중에 설치 되는 기본 Linux watchdog은 UCS 및 HPE SDFlex 시스템에서 지원 되지 않는 iTCO watchdog입니다. 따라서 이 watchdog를 사용하지 않도록 설정해야 합니다.
    1. 시스템에 잘못된 watchdog가 설치되어서 로드되어 있습니다.
       ```
       sollabdsm35:~ # lsmod |grep iTCO
   
       iTCO_wdt 13480 0
   
       iTCO_vendor_support 13718 1 iTCO_wdt
       ```

    2. 환경에서 잘못된 드라이버를 언로드합니다.
       ```  
       sollabdsm35:~ # modprobe -r iTCO_wdt iTCO_vendor_support
   
       sollabdsm36:~ # modprobe -r iTCO_wdt iTCO_vendor_support
       ```  
        
    3. 다음번 시스템 부팅 때 드라이버가 로드되지 않도록 하려면 드라이버를 차단 목록에 추가해야 합니다. iTCO 모듈을 차단 목록에 추가하려면 `50-blacklist.conf` 파일의 끝에 다음을 추가합니다.
       ```
       sollabdsm35:~ # vi /etc/modprobe.d/50-blacklist.conf
   
        unload the iTCO watchdog modules
   
       blacklist iTCO_wdt
   
       blacklist iTCO_vendor_support
       ```
    4. 파일을 보조 호스트에 복사합니다.
       ```
       sollabdsm35:~ # scp /etc/modprobe.d/50-blacklist.conf sollabdsm35:
       /etc/modprobe.d/50-blacklist.conf
       ```  

    5. ipmi 서비스가 시작되었는지 테스트합니다. IPMI 타이머가 실행 중이 아니어야 합니다. 타이머 관리는 SBD pacemaker 서비스에서 수행합니다.
       ```
       sollabdsm35:~ # ipmitool mc watchdog get
   
       Watchdog Timer Use: BIOS FRB2 (0x01)
   
       Watchdog Timer Is: Stopped
   
       Watchdog Timer Actions: No action (0x00)
   
       Pre-timeout interval: 0 seconds
   
       Timer Expiration Flags: 0x00
   
       Initial Countdown: 0 sec
   
       Present Countdown: 0 sec
   
       ``` 

3. 기본적으로 필요한 디바이스인 /dev/watchdog는 만들어지지 않습니다.

    ```
    sollabdsm35:~ # ls -l /dev/watchdog

    ls: cannot access /dev/watchdog: No such file or directory
    ```

4. IPMI watchdog를 구성합니다.

    ``` 
    sollabdsm35:~ # mv /etc/sysconfig/ipmi /etc/sysconfig/ipmi.org

    sollabdsm35:~ # vi /etc/sysconfig/ipmi

    IPMI_SI=yes
    DEV_IPMI=yes
    IPMI_WATCHDOG=yes
    IPMI_WATCHDOG_OPTIONS="timeout=20 action=reset nowayout=0
    panic_wdt_timeout=15"
    IPMI_POWEROFF=no
    IPMI_POWERCYCLE=no
    IPMI_IMB=no
    ```
5. watchdog 구성 파일을 보조 호스트에 복사합니다.
    ```
    sollabdsm35:~ # scp /etc/sysconfig/ipmi
    sollabdsm36:/etc/sysconfig/ipmi
    ```
6.  ipmi 서비스를 사용하도록 설정하고 시작합니다.
    ```
    [root@sollabdsm35 ~]# systemctl enable ipmi

    Created symlink from
    /etc/systemd/system/multi-user.target.wants/ipmi.service to
    /usr/lib/systemd/system/ipmi.service.

    [root@sollabdsm35 ~]# systemctl start ipmi

    [root@sollabdsm36 ~]# systemctl enable ipmi

    Created symlink from
    /etc/systemd/system/multi-user.target.wants/ipmi.service to
    /usr/lib/systemd/system/ipmi.service.

    [root@sollabdsm36 ~]# systemctl start ipmi
    ```
     IPMI 서비스가 시작되고 디바이스 /dev/watchdog가 만들어집니다. 하지만 타이머는 여전히 중지되어 있는데, 나중에 SBD가 watchdog 재설정을 관리하고 IPMI 타이머를 사용하도록 설정합니다.
7.  /dev/watchdog가 존재하지만 사용 중이 아닌지 확인합니다.
    ```
    [root@sollabdsm35 ~]# ipmitool mc watchdog get
    Watchdog Timer Use: SMS/OS (0x04)
    Watchdog Timer Is: Stopped
    Watchdog Timer Actions: No action (0x00)
    Pre-timeout interval: 0 seconds
    Timer Expiration Flags: 0x10
    Initial Countdown: 20 sec
    Present Countdown: 20 sec

    [root@sollabdsm35 ~]# ls -l /dev/watchdog
    crw------- 1 root root 10, 130 Nov 28 23:12 /dev/watchdog
    [root@sollabdsm35 ~]# lsof /dev/watchdog
    ```

## <a name="sbd-configuration"></a>SBD 구성
이 섹션에서는 SBD를 구성하는 방법을 알아봅니다. 이 섹션에서는 이 문서의 시작 부분에서 언급했던 두 개의 호스트 `sollabdsm35`와 `sollabdsm36`을 그대로 사용합니다.

1.  두 노드에서 iSCSI 또는 FC 디스크가 보이는지 확인합니다. 이 예제에서는 FC 기반 SBD 디바이스를 사용합니다. SBD 펜싱에 대한 자세한 내용은 [RHEL 고가용성 클러스터를 위한 설계 지침 - SBD 고려 사항](https://access.redhat.com/articles/2941601) 및 [RHEL 고가용성 클러스터에 대한 지원 정책 - sbd 및 fence_sbd](https://access.redhat.com/articles/2800691)를 참조하세요.
2.  모든 노드에서 LUN-ID가 동일해야 합니다.
  
3.  sbd 디바이스의 다중 경로 상태를 확인합니다.
    ```
    multipath -ll
    3600a098038304179392b4d6c6e2f4b62 dm-5 NETAPP ,LUN C-Mode
    size=1.0G features='4 queue_if_no_path pg_init_retries 50
    retain_attached_hw_handle' hwhandler='1 alua' wp=rw
    |-+- policy='service-time 0' prio=50 status=active
    | |- 8:0:1:2 sdi 8:128 active ready running
    | `- 10:0:1:2 sdk 8:160 active ready running
    `-+- policy='service-time 0' prio=10 status=enabled
    |- 8:0:3:2 sdj 8:144 active ready running
    `- 10:0:3:2 sdl 8:176 active ready running
    ```

4.  SBD 디스크를 만들고 클러스터 기본 fence를 설정 합니다. 이 단계는 첫 번째 노드에서 실행해야 합니다.
    ```
    sbd -d /dev/mapper/3600a098038304179392b4d6c6e2f4b62 -4 20 -1 10 create 

    Initializing device /dev/mapper/3600a098038304179392b4d6c6e2f4b62
    Creating version 2.1 header on device 4 (uuid:
    ae17bd40-2bf9-495c-b59e-4cb5ecbf61ce)

    Initializing 255 slots on device 4

    Device /dev/mapper/3600a098038304179392b4d6c6e2f4b62 is initialized.
    ```

5.  SBD 구성을 node2로 복사합니다.
    ```
    vi /etc/sysconfig/sbd

    SBD_DEVICE="/dev/mapper/3600a09803830417934d6c6e2f4b62" 
    SBD_PACEMAKER=yes
    SBD_STARTMODE=always
    SBD_DELAY_START=no
    SBD_WATCHDOG_DEV=/dev/watchdog
    SBD_WATCHDOG_TIMEOUT=15
    SBD_TIMEOUT_ACTION=flush,reboot
    SBD_MOVE_TO_ROOT_CGROUP=auto
    SBD_OPTS=

    scp /etc/sysconfig/sbd node2:/etc/sysconfig/sbd
    ```

6.  두 노드에서 SBD 디스크가 보이는지 확인합니다.
    ```
    sbd -d /dev/mapper/3600a098038304179392b4d6c6e2f4b62 dump

    ==Dumping header on disk /dev/mapper/3600a098038304179392b4d6c6e2f4b62

    Header version : 2.1

    UUID : ae17bd40-2bf9-495c-b59e-4cb5ecbf61ce

    Number of slots : 255
    Sector size : 512
    Timeout (watchdog) : 5
    Timeout (allocate) : 2
    Timeout (loop) : 1
    Timeout (msgwait) : 10

    ==Header on disk /dev/mapper/3600a098038304179392b4d6c6e2f4b62 is dumped
    ```

7.  SBD 구성 파일에 SBD 디바이스를 추가합니다.

    ```
    # SBD_DEVICE specifies the devices to use for exchanging sbd messages
    # and to monitor. If specifying more than one path, use ";" as
    # separator.
    #

    SBD_DEVICE="/dev/mapper/3600a098038304179392b4d6c6e2f4b62"
    ## Type: yesno
     Default: yes
     # Whether to enable the pacemaker integration.
    SBD_PACEMAKER=yes
    ```

## <a name="cluster-initialization"></a>클러스터 초기화
이 섹션에서는 클러스터를 초기화합니다. 이 섹션에서는 이 문서의 시작 부분에서 언급했던 두 개의 호스트 `sollabdsm35`와 `sollabdsm36`을 그대로 사용합니다.

1.  모든 노드에서 클러스터 사용자 암호를 설정합니다.
    ```
    passwd hacluster
    ```
2.  모든 시스템에서 PC를 시작합니다.
    ```
    systemctl enable pcsd
    ```
  

3.  방화벽을 중지하고 모든 노드에서 사용하지 않도록 설정합니다.
    ```
    systemctl disable firewalld 

     systemctl mask firewalld

    systemctl stop firewalld
    ```

4.  pcsd 서비스를 시작합니다.
    ```
    systemctl start pcsd
    ```

5.  node1에서만 클러스터 인증을 실행합니다.

    ```
    pcs cluster auth sollabdsm35 sollabdsm36

        Username: hacluster

            Password:
            sollabdsm35.localdomain: Authorized
            sollabdsm36.localdomain: Authorized

     ``` 

6.  클러스터를 만듭니다.
    ```
    pcs cluster setup --start --name hana sollabdsm35 sollabdsm36
    ```
  

7.  클러스터 상태를 확인합니다.

    ```
    pcs cluster status

    Cluster name: hana

    WARNINGS:

    No stonith devices and stonith-enabled is not false

    Stack: corosync

    Current DC: sollabdsm35 (version 1.1.20-5.el7_7.2-3c4c782f70) -
    partition with quorum

    Last updated: Sat Nov 28 20:56:57 2020

    Last change: Sat Nov 28 20:54:58 2020 by hacluster via crmd on
    sollabdsm35

    2 nodes configured

    0 resources configured

    Online: [ sollabdsm35 sollabdsm36 ]

    No resources

    Daemon Status:

    corosync: active/disabled

    pacemaker: active/disabled

    pcsd: active/disabled
    ```

8. 클러스터에 1개의 노드에 가입되지 않는다면 방화벽이 아직 실행 중인지 확인합니다.

9. SBD 디바이스를 만들고 사용하도록 설정합니다.
    ```
    pcs stonith create SBD fence_sbd devices=/dev/mapper/3600a098038303f4c467446447a
    ```
  
10. 모든 노드에서 클러스터를 중지하고 클러스터 서비스를 다시 시작합니다.

    ```
    pcs cluster stop --all
    ```

11. 모든 노드에서 클러스터 서비스를 다시 시작합니다.

    ```
    systemctl stop pcsd
    systemctl stop pacemaker
    systemctl stop corocync
    systemctl enable sbd
    systemctl start corosync
    systemctl start pacemaker
    systemctl start pcsd
    ```

12. corosync가 SBD 서비스를 시작합니다.

    ```
    systemctl status sbd

    ● sbd.service - Shared-storage based fencing daemon

    Loaded: loaded (/usr/lib/systemd/system/sbd.service; enabled; vendor
    preset: disabled)

    Active: active (running) since Wed 2021-01-20 01:43:41 EST; 9min ago
    ```

13. 클러스터가 pcsd에서 자동으로 시작되지 않았다면 클러스터를 다시 시작합니다.

    ```
    pcs cluster start –-all

    sollabdsm35: Starting Cluster (corosync)...

    sollabdsm36: Starting Cluster (corosync)...

    sollabdsm35: Starting Cluster (pacemaker)...

    sollabdsm36: Starting Cluster (pacemaker)...
    ```
  

14. Stonith 설정을 사용하도록 설정합니다.
    ```
    pcs stonith enable SBD --device=/dev/mapper/3600a098038304179392b4d6c6e2f4d65
    pcs property set stonith-watchdog-timeout=20
    pcs property set stonith-action=reboot
    ```
  

15. 이제 1개의 리소스가 있는 새 클러스터의 상태를 확인합니다.
    ```
    pcs status

    Cluster name: hana

    Stack: corosync

    Current DC: sollabdsm35 (version 1.1.16-12.el7-94ff4df) - partition
    with quorum

    Last updated: Tue Oct 16 01:50:45 2018

    Last change: Tue Oct 16 01:48:19 2018 by root via cibadmin on
    sollabdsm35

    2 nodes configured

    1 resource configured

    Online: [ sollabdsm35 sollabdsm36 ]

    Full list of resources:

    SBD (stonith:fence_sbd): Started sollabdsm35

    Daemon Status:

    corosync: active/disabled

    pacemaker: active/disabled

    pcsd: active/enabled

    sbd: active/enabled

    [root@node1 ~]#
    ```
  

16. 이제 IPMI 타이머가 실행되고 sbd에 의해 /dev/watchdog 디바이스가 실행됩니다.

    ```
    ipmitool mc watchdog get

    Watchdog Timer Use: SMS/OS (0x44)

    Watchdog Timer Is: Started/Running

    Watchdog Timer Actions: Hard Reset (0x01)

    Pre-timeout interval: 0 seconds

    Timer Expiration Flags: 0x10

    Initial Countdown: 20 sec

    Present Countdown: 19 sec

    [root@sollabdsm35 ~] lsof /dev/watchdog

    COMMAND PID USER FD TYPE DEVICE SIZE/OFF NODE NAME

    sbd 117569 root 5w CHR 10,130 0t0 323812 /dev/watchdog
    ```

17. SBD 상태를 확인합니다.

    ```
    sbd -d /dev/mapper/3600a098038304445693f4c467446447a list

    0 sollabdsm35 clear

    1 sollabdsm36 clear
    ```
  

18. 커널을 크래시하여 SBD 펜싱을 테스트합니다.

    * 커널 크래시를 트리거합니다.

      ```
      echo c > /proc/sysrq-trigger

      System must reboot after 5 Minutes (BMC timeout) or the value which is
      set as panic_wdt_timeout in the /etc/sysconfig/ipmi config file.
      ```
  
    * 실행할 두 번째 테스트는 PCS 명령을 사용하여 노드를 펜싱하는 것입니다.

      ```
      pcs stonith fence sollabdsm36
      ```
  

19. 나머지 SAP HANA 클러스터링에 대해 다음과 같이 설정하여 STONITH를 사용하지 않도록 설정할 수 있습니다.

   * pcs 속성 집합 `stonith-enabled=false`
   * 클러스터 설정 중에 STONITH를 비활성화된 상태로 유지하는 것이 더 쉬운 경우가 있습니다. 이렇게 하면 시스템의 예기치 않은 재부팅이 방지되기 때문입니다.
   * 생산적으로 사용하려면 이 매개 변수를 true로 설정해야 합니다. 이 매개 변수를 true로 설정하지 않으면 클러스터가 지원되지 않습니다.
   * pcs 속성 집합 `stonith-enabled=true`

## <a name="hana-integration-into-the-cluster"></a>클러스터에 HANA 통합

이 섹션에서는 클러스터에 HANA를 통합합니다. 이 섹션에서는 이 문서의 시작 부분에서 언급했던 두 개의 호스트 `sollabdsm35`와 `sollabdsm36`을 그대로 사용합니다.

기본 및 지원되는 방법은 데이터베이스를 직접 전환할 수 있는 성능 최적화 시나리오를 만드는 것입니다. 이 문서에서는 이 시나리오만 설명합니다. 이 경우 QAS 시스템용 클러스터 하나와 별도의 PRD 시스템용 클러스터를 설치하는 것이 좋습니다. 이 경우에만 생산에 들어가기 전에 모든 구성 요소를 테스트할 수 있습니다.


* 이 프로세스는 다음 페이지에 있는 RHEL 설명의 빌드입니다.

  * https://access.redhat.com/articles/3004101

 ### <a name="steps-to-follow-to-configure-hsr"></a>HSR을 구성하기 위해 수행해야 하는 단계

 | **로그 복제 모드**            | **설명**                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| ----------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **동기식 메모리 내(기본값)** | 동기식 메모리 내(mode=syncmem)는 로그 항목이 기본의 로그 볼륨에 기록되고 로그를 보내는 것이 메모리에 복사된 후 보조 인스턴스에 의해 확인된 경우 로그 쓰기가 성공한 것으로 간주됨을 의미합니다. 보조 시스템에 대한 연결이 끊어지면 기본 시스템은 트랜잭션 처리를 계속하고 변경 사항을 로컬 디스크에만 씁니다. 보조 시스템이 연결 되는 동안 또는 인수가 실행 되는 동안 주 데이터베이스와 보조 데이터베이스가 동시에 실패 하는 경우와 보조 시스템의 연결이 끊어지면 데이터 손실이 발생할 수 있습니다. 이 옵션은 보조 인스턴스에서 디스크 I/O를 기다릴 필요가 없기 때문에 더 나은 성능을 제공하지만 데이터 손실에 더 취약합니다.                                                                                                                                                                                                                                                                                                                     |
| **동기**                     | 동기식(mode=sync)은 로그 항목이 기본 인스턴스 및 보조 인스턴스의 로그 볼륨에 기록되었을 때 로그 쓰기가 성공한 것으로 간주됨을 의미합니다. 보조 시스템에 대한 연결이 끊어지면 기본 시스템은 트랜잭션 처리를 계속하고 변경 사항을 로컬 디스크에만 씁니다. 보조 시스템이 연결되어 있는 한 이 시나리오에서는 데이터 손실이 발생하지 않습니다. 보조 시스템의 연결이 끊어지면 인수가 실행 될 때 데이터가 손실 될 수 있습니다. 또한이 복제 모드는 전체 동기화 옵션을 사용 하 여 실행할 수 있습니다. 즉, 기본 인스턴스와 보조 인스턴스의 로그 파일에 로그 버퍼가 기록되어야 로그 쓰기가 성공한 것입니다. 또한 보조 시스템의 연결이 끊어지면 (예: 네트워크 오류로 인해) 주 시스템은 보조 시스템에 대 한 연결이 다시 설정 될 때까지 트랜잭션 처리를 일시 중단 합니다. 이 시나리오에서는 데이터 손실이 발생 하지 않습니다. 매개 변수 \[system\_replication\]/enable\_full\_sync)만 사용하여 시스템 복제에 대한 전체 동기화 옵션을 설정할 수 있습니다. 전체 동기화 옵션을 사용하도록 설정하는 방법에 대한 자세한 내용은 시스템 복제를 위한 전체 동기화 옵션을 사용하도록 설정을 참조하세요.                                                                                                                                                                                                                                                                                                              |
| **비동기**                    | 비동기식(mode=async)은 기본 시스템이 다시 실행 로그 버퍼를 보조 시스템으로 비동기식으로 보내는 것을 의미합니다. 기본 시스템은 트랜잭션이 기본 시스템의 로그 파일에 기록되고 네트워크를 통해 보조 시스템으로 전송되면 트랜잭션을 커밋합니다. 보조 시스템의 확인을 기다리지 않습니다. 이 옵션은 보조 시스템에서 로그 I/O를 기다릴 필요가 없기 때문에 더 나은 성능을 제공합니다. 보조 시스템의 모든 서비스에서 데이터베이스 일관성이 보장됩니다. 그러나 데이터 손실에 더 취약합니다. 데이터 변경 사항은 인수 시 손실될 수 있습니다.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |

1.  다음은 node1(기본)에서 실행할 작업입니다.
    1. 데이터베이스 로그 모드가 normal로 설정되어 있는지 확인합니다.

       ```  
   
       * su - hr2adm
   
       * hdbsql -u system -p $YourPass -i 00 "select value from
       "SYS"."M_INIFILE_CONTENTS" where key='log_mode'"
   
       
   
       VALUE
   
       "normal"
       ```
    2. SAP HANA 시스템 복제는 초기 백업이 수행된 후에만 작동합니다. 다음 명령은 `/tmp/` 디렉터리에 초기 백업을 만듭니다. 데이터베이스에 사용할 올바른 백업 파일 시스템을 선택합니다. 
       ```
       * hdbsql -i 00 -u system -p $YourPass "BACKUP DATA USING FILE
       ('/tmp/backup')"
   
   
   
       Backup files were created
   
       ls -l /tmp
   
       total 2031784
   
       -rw-r----- 1 hr2adm sapsys 155648 Oct 26 23:31 backup_databackup_0_1
   
       -rw-r----- 1 hr2adm sapsys 83894272 Oct 26 23:31 backup_databackup_2_1
   
       -rw-r----- 1 hr2adm sapsys 1996496896 Oct 26 23:31 backup_databackup_3_1
   
       ```  

    3. 이 데이터베이스의 모든 데이터베이스 컨테이너를 백업합니다.
       ``` 
       * hdbsql -i 00 -u system -p $YourPass -d SYSTEMDB "BACKUP DATA USING
       FILE ('/tmp/sydb')"     
   
       * hdbsql -i 00 -u system -p $YourPass -d SYSTEMDB "BACKUP DATA FOR HR2
       USING FILE ('/tmp/rh2')"
   
       ```

    4. 원본 시스템에서 HSR 프로세스를 사용하도록 설정합니다.
       ```
       hdbnsutil -sr_enable --name=DC1

       nameserver is active, proceeding ...

       successfully enabled system as system replication source site

       done.
       ```

    5. 기본 시스템의 상태를 확인합니다.
       ```
       hdbnsutil -sr_state
    
       System Replication State
   
   
       online: true
   
       mode: primary
   
       operation mode: primary
   
       site id: 1
   
       site name: DC1
   
       
   
       is source system: true
   
       is secondary/consumer system: false
   
       has secondaries/consumers attached: false
   
       is a takeover active: false
   
       
   
       Host Mappings:
   
       ~~~~~~~~~~~~~~
   
       Site Mappings:
   
       ~~~~~~~~~~~~~~
   
       DC1 (primary/)
   
       Tier of DC1: 1
   
       Replication mode of DC1: primary
   
       Operation mode of DC1:
   
       done.
       ```

 2. 다음은 node2(보조)에서 실행할 작업입니다.
     1. 데이터베이스를 중지합니다.
       ```
       su – hr2adm
    
       sapcontrol -nr 00 -function StopSystem
       ```
    

     2. SAP HANA2.0에 대해서만 기본 노드에서 보조 노드로 SAP HANA 시스템 `PKI SSFS_HR2.KEY` 및 `SSFS_HR2.DAT` 파일을 복사합니다.
       ```
       scp
       root@node1:/usr/sap/HR2/SYS/global/security/rsecssfs/key/SSFS_HR2.KEY
       /usr/sap/HR2/SYS/global/security/rsecssfs/key/SSFS_HR2.KEY
   
       
   
       scp
       root@node1:/usr/sap/HR2/SYS/global/security/rsecssfs/data/SSFS_HR2.DAT
       /usr/sap/HR2/SYS/global/security/rsecssfs/data/SSFS_HR2.DAT
       ```

     3. 보조 노드를 복제 사이트로 사용하도록 설정합니다.
       ``` 
       su - hr2adm
   
       hdbnsutil -sr_register --remoteHost=node1 --remoteInstance=00
       --replicationMode=syncmem --name=DC2
   
       
   
       adding site ...
   
       --operationMode not set; using default from global.ini/[system_replication]/operation_mode: logreplay
   
       nameserver node2:30001 not responding.
   
       collecting information ...
   
       updating local ini files ...
   
       done.
   
       ```

     4. 데이터베이스를 시작합니다.
       ```
       sapcontrol -nr 00 -function StartSystem 
       ```
    
     5. 데이터베이스 상태를 확인합니다.
       ```
       hdbnsutil -sr_state
   
       ~~~~~~~~~
       System Replication State
   
       online: true
   
       mode: syncmem
   
       operation mode: logreplay
   
       site id: 2
   
       site name: DC2   
   
       is source system: false
   
       is secondary/consumer system: true
   
       has secondaries/consumers attached: false
   
       is a takeover active: false
   
       active primary site: 1
   
       
   
       primary primarys: node1
   
       Host Mappings:
   
   
   
       node2 -> [DC2] node2
   
       node2 -> [DC1] node1
   
       
   
       Site Mappings:
   
   
   
       DC1 (primary/primary)
   
       |---DC2 (syncmem/logreplay)
   
       
   
       Tier of DC1: 1
   
       Tier of DC2: 2
   
       
   
       Replication mode of DC1: primary
   
       Replication mode of DC2: syncmem
   
       Operation mode of DC1: primary
   
       Operation mode of DC2: logreplay
   
       
   
       Mapping: DC1 -> DC2
   
       done.
       ~~~~~~~~~~~~~~
       ```

3. 복제 상태에 대한 자세한 정보를 가져올 수도 있습니다.
    ```
    ~~~~~
    hr2adm@node1:/usr/sap/HR2/HDB00> python
    /usr/sap/HR2/HDB00/exe/python_support/systemReplicationStatus.py

    | Database | Host | Port | Service Name | Volume ID | Site ID | Site
    Name | Secondary | Secondary | Secondary | Secondary | Secondary |
    Replication | Replication | Replication |

    | | | | | | | | Host | Port | Site ID | Site Name | Active Status |
    Mode | Status | Status Details |

    | SYSTEMDB | node1 | 30001 | nameserver | 1 | 1 | DC1 | node2 | 30001
    | 2 | DC2 | YES | SYNCMEM | ACTIVE | |

    | HR2 | node1 | 30007 | xsengine | 2 | 1 | DC1 | node2 | 30007 | 2 |
    DC2 | YES | SYNCMEM | ACTIVE | |

    | HR2 | node1 | 30003 | indexserver | 3 | 1 | DC1 | node2 | 30003 | 2
    | DC2 | YES | SYNCMEM | ACTIVE | |


    status system replication site "2": ACTIVE

    overall system replication status: ACTIVE

    Local System Replication State
    

    mode: PRIMARY

    site id: 1

    site name: DC1
    ```
  

#### <a name="log-replication-mode-description"></a>로그 복제 모드 설명

로그 복제 모드에 대한 자세한 내용은 [공식 SAP 설명서](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/627bd11e86c84ec2b9fcdf585d24011c.html)를 참조하세요.
  

#### <a name="network-setup-for-hana-system-replication"></a>HANA 시스템 복제를 위한 네트워크 설정


복제 트래픽이 복제를 위해 올바른 VLAN을 사용하도록 하려면 `global.ini`에서 올바르게 구성해야 합니다. 이 단계를 건너뛰면 HANA는 복제를 위해 액세스 VLAN을 사용하는데, 이는 바람직하지 않습니다.


다음 예제에서는 보조 사이트로의 시스템 복제를 위한 호스트 이름 확인 구성을 보여 줍니다. 세 가지 고유한 네트워크를 식별할 수 있습니다.

* 주소가 10.0.1.* 범위에 있는 공용 네트워크

* 각 사이트 간의 내부 SAP HANA 통신을 위한 네트워크: 192.168.1.*

* 시스템 복제를 위한 전용 네트워크: 10.5.1.*

첫 번째 예제에서는 `[system_replication_communication]listeninterface` 매개 변수가 `.global`로 설정되었고 이웃 복제 사이트의 호스트만 지정되었습니다.

다음 예제에서는 `[system_replication_communication]listeninterface` 매개 변수가 `.internal`로 설정되었고 두 사이트의 모든 호스트가 지정되었습니다.

  

자세한 내용은 [Network Configuration for SAP HANA System Replication](https://www.sap.com/documents/2016/06/18079a1c-767c-0010-82c7-eda71af511fa.html)(SAP HANA 시스템 복제를 위한 네트워크 구성)을 참조하세요.

  

시스템 복제를 위해서는 `/etc/hosts` 파일을 편집할 필요가 없습니다. 시스템 복제를 위한 전용 네트워크를 만들려면 `global.ini` 파일에서 내부(‘가상’) 호스트 이름이 IP 주소로 매핑되어 있어야 합니다. 이를 위한 구문은 다음과 같습니다.

global.ini

[system_replication_hostname_resolution]

<ip-address_site>=<internal-host-name_site>


## <a name="configure-sap-hana-in-a-pacemaker-cluster"></a>Pacemaker 클러스터에서 SAP HANA 구성
이 섹션에서는 Pacemaker 클러스터에서 SAP HANA를 구성하는 방법을 알아봅니다. 이 섹션에서는 이 문서의 시작 부분에서 언급했던 두 개의 호스트 `sollabdsm35`와 `sollabdsm36`을 그대로 사용합니다.

다음 필수 조건이 충족되었는지 확인합니다.  

* Pacemaker 클러스터가 설명서에 따라 구성되었고 정상 작동하는 올바른 펜싱이 있습니다.

* 시작 및 중지는 클러스터가 관리하므로 모든 클러스터 노드에서 부팅 시 SAP HANA 시작이 사용하지 않도록 설정되어 있습니다.

* 클러스터 노드 간에 SAP의 도구를 사용한 SAP HANA 시스템 복제 및 인수가 올바르게 작동합니다.

* SAP HANA에 두 클러스터 노드에서 클러스터가 사용할 수 있는 모니터링 계정이 있습니다.

* 두 노드가 ‘High-availability’ 및 ‘RHEL for SAP HANA’(RHEL 6,RHEL 7) 채널을 구독하고 있습니다.

  

* CIB는 pcs 셸에서 자동으로 업데이트되므로 모든 pcs 명령은 노드에서만 실행하세요.

* [quorum 정책에 대한 자세한 정보](https://access.redhat.com/solutions/645843)

### <a name="steps-to-configure"></a>구성 단계 
1. pcs를 구성합니다.
    ```
    [root@node1 ~]# pcs property unset no-quorum-policy (optional – only if it was set before)
    [root@node1 ~]# pcs resource defaults resource-stickiness=1000
    [root@node1 ~]# pcs resource defaults migration-threshold=5000
    ```
2.  corosync를 구성합니다.
    자세한 내용은 [How can I configure my RHEL 7 High Availability Cluster with pacemaker and corosync](https://access.redhat.com/solutions/1293523)(pacemaker 및 corosync를 사용하여 RHEL 7 고가용성 클러스터를 구성하는 방법)를 참조하세요.
    ```
    cat /etc/corosync/corosync.conf

    totem {

    version: 2

    secauth: off

    cluster_name: hana

    transport: udpu

    }

    

    nodelist {

    node {

    ring0_addr: node1.localdomain

    nodeid: 1

    }

    

    node {

    ring0_addr: node2.localdomain

    nodeid: 2

    }

    }

    

    quorum {

    provider: corosync_votequorum

    two_node: 1

    }

    

    logging {

    to_logfile: yes

    logfile: /var/log/cluster/corosync.log

    to_syslog: yes

    }

    ```
  

3.  복제된 SAPHanaTopology 리소스를 만듭니다.
    SAPHanaTopology 리소스가 각 노드에서 SAP HANA 시스템 복제의 상태와 구성을 수집하고 있습니다. SAPHanaTopology를 구성하려면 다음과 같은 특성이 필요합니다.
       ```
       pcs resource create SAPHanaTopology_HR2_00 SAPHanaTopology SID=HR2 op start timeout=600 \
       op stop timeout=300 \
       op monitor interval=10 timeout=600 \
       clone clone-max=2 clone-node-max=1 interleave=true

       ```

    | 특성 이름 | 설명  |
    |---|---|
    | SID | SAP HANA 설치의 SAP SID(시스템 ID)입니다. 모든 노드에서 동일해야 합니다. |
    | InstanceNumber | 2 자리 SAP 인스턴스 식별자입니다.|

    * 리소스 상태
       ```
       pcs resource show SAPHanaTopology_HR2_00
   
       Clone: SAPHanaTopology_HR2_00-clone
        Meta Attrs: clone-max=2 clone-node-max=1 interleave=true
        Resource: SAPHanaTopology_HR2_00 (class=ocf provider=heartbeat type=SAPHanaTopology)
         Attributes: InstanceNumber=00 SID=HR2
         Operations: monitor interval=60 timeout=60 (SAPHanaTopology_HR2_00-monitor-interval-60)
                     start interval=0s timeout=180 (SAPHanaTopology_HR2_00-start-interval-0s)
                     stop interval=0s timeout=60 (SAPHanaTopology_HR2_00-stop-interval-0s)
       
         
       ```

4.  기본/보조 SAPHana 리소스를 만듭니다.
    * SAPHana 리소스는 SAP HANA 데이터베이스의 시작, 중지 및 위치 이동을 담당합니다. 이 리소스는 기본/보조 클러스터 리소스로서 실행해야 합니다. 이 리소스는 다음과 같은 특성을 갖습니다.

| 특성 이름            | 필수 여부 | 기본값 | Description                                                                                                                                                                                                                                                                                                                                                                                                                                                             |
|---------------------------|-----------|---------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| SID                       | 예       | 없음          | SAP HANA 설치의 SAP SID(시스템 ID)입니다. 모든 노드에서 동일해야 합니다.                                                                                                                                                                                                                                                                                                                                                                                       |
| InstanceNumber            | 예       | 없음          | 2자리 SAP 인스턴스 ID입니다.                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| PREFER_SITE_TAKEOVER      | 아니요        | 예           | 클러스터가 로컬에서 기본 인스턴스를 다시 시작하는 것보다 보조 인스턴스로 전환하는 것을 선호해야 할까요? (“no”: 로컬에서 다시 시작 선호, “yes”: 원격 사이트로의 전환 선호)                                                                                                                                                                                                                                                                                            |
|                           |           |               |                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| AUTOMATED_REGISTER        | 아니요        | FALSE         | 인수가 이루어지고 DUPLICATE_PRIMARY_TIMEOUT이 지나면 이전 SAP HANA 기본을 보조로 등록해야 할까요? (“false”: 아니요. 수동 작업이 필요합니다, “true”: 예. 리소스 에이전트가 이전 기본을 보조로 등록합니다)                                                                                                                                                                                                                        |
| DUPLICATE_PRIMARY_TIMEOUT | 아니요        | 7200          | 이중 기본 상황이 발생할 경우 기본 타임 스탬프 간에 필요한 시간 차이입니다(단위: 초). 시간 차이가 시간 간격보다 작은 경우 클러스터가 하나 또는 두 개의 인스턴스를 “대기” 상태로 유지합니다. 이는 관리자가 장애 조치에 대해 조치를 취할 수 있도록 하기 위한 것입니다. 시간 차이가 경과하면 실패한 이전 기본이 등록됩니다. 새 기본으로 이 등록이 이루어지면 시스템 복제가 모든 데이터를 덮어씁니다. |

5.  HANA 리소스를 만듭니다.
    ```
    pcs resource create SAPHana_HR2_00 SAPHana SID=HR2 InstanceNumber=00 PREFER_SITE_TAKEOVER=true DUPLICATE_PRIMARY_TIMEOUT=7200 AUTOMATED_REGISTER=true op start timeout=3600 \
    op stop timeout=3600 \
    op monitor interval=61 role="Slave" timeout=700 \
    op monitor interval=59 role="Master" timeout=700 \
    op promote timeout=3600 \
    op demote timeout=3600 \
    master meta notify=true clone-max=2 clone-node-max=1 interleave=true


    pcs resource show SAPHana_HR2_00-primary


    Primary: SAPHana_HR2_00-primary
     Meta Attrs: clone-max=2 clone-node-max=1 interleave=true notify=true
     Resource: SAPHana_HR2_00 (class=ocf provider=heartbeat type=SAPHana)
      Attributes: AUTOMATED_REGISTER=false DUPLICATE_PRIMARY_TIMEOUT=7200 InstanceNumber=00 PREFER_SITE_TAKEOVER=true SID=HR2
      Operations: demote interval=0s timeout=320 (SAPHana_HR2_00-demote-interval-0s)
                  monitor interval=120 timeout=60 (SAPHana_HR2_00-monitor-interval-120)
                  monitor interval=121 role=Secondary timeout=60 (SAPHana_HR2_00-monitor-
                  interval-121)
                  monitor interval=119 role=Primary timeout=60 (SAPHana_HR2_00-monitor-
                  interval-119)
                  promote interval=0s timeout=320 (SAPHana_HR2_00-promote-interval-0s)
                  start interval=0s timeout=180 (SAPHana_HR2_00-start-interval-0s)
                  stop interval=0s timeout=240 (SAPHana_HR2_00-stop-interval-0s)
   

    
    
    crm_mon -A1

    ....

    2 nodes configured

    5 resources configured

    Online: [ node1.localdomain node2.localdomain ]

    Active resources:

    .....

    Node Attributes:

    * Node node1.localdomain:

    + hana_hr2_clone_state : PROMOTED

    + hana_hr2_remoteHost : node2

    + hana_hr2_roles : 4:P:primary1:primary:worker:primary

    + hana_hr2_site : DC1

    + hana_hr2_srmode : syncmem

    + hana_hr2_sync_state : PRIM

    + hana_hr2_version : 2.00.033.00.1535711040

    + hana_hr2_vhost : node1

    + lpa_hr2_lpt : 1540866498

    + primary-SAPHana_HR2_00 : 150

    * Node node2.localdomain:

    + hana_hr2_clone_state : DEMOTED

    + hana_hr2_op_mode : logreplay

    + hana_hr2_remoteHost : node1

    + hana_hr2_roles : 4:S:primary1:primary:worker:primary

    + hana_hr2_site : DC2

    + hana_hr2_srmode : syncmem

    + hana_hr2_sync_state : SOK

    + hana_hr2_version : 2.00.033.00.1535711040

    + hana_hr2_vhost : node2

    + lpa_hr2_lpt : 30

    + primary-SAPHana_HR2_00 : 100
    ```

6.  가상 IP 주소 리소스를 만듭니다.
    클러스터는 SAP HANA의 기본 인스턴스에 도달하기 위한 가상 IP 주소를 포함합니다. 다음은 IP 10.7.0.84/24를 사용하여 IPaddr2 리소스를 만드는 명령 예제입니다.
    ```
    pcs resource create vip_HR2_00 IPaddr2 ip="10.7.0.84"
    pcs resource show vip_HR2_00

    Resource: vip_HR2_00 (class=ocf provider=heartbeat type=IPaddr2)

        Attributes: ip=10.7.0.84

        Operations: monitor interval=10s timeout=20s
    (vip_HR2_00-monitor-interval-10s)

        start interval=0s timeout=20s (vip_HR2_00-start-interval-0s)

        stop interval=0s timeout=20s (vip_HR2_00-stop-interval-0s)
    ```

7.  제약 조건을 만듭니다.
    * 올바른 작업을 위해 SAPHana 리소스를 시작하기 전에 SAPHanaTopology 리소스가 시작되었는지 확인해야 하며, SAPHana의 기본 리소스가 실행 중인 노드에 가상 IP 주소가 있는지도 확인해야 합니다. 이를 위해 다음과 같은 2가지 제약 조건을 만듭니다.
       ```
       pcs constraint order SAPHanaTopology_HR2_00-clone then SAPHana_HR2_00-primary symmetrical=false
       pcs constraint colocation add vip_HR2_00 with primary SAPHana_HR2_00-primary 2000
       ```

###  <a name="testing-the-manual-move-of-saphana-resource-to-another-node"></a>다른 노드로의 SAPHana 리소스 수동 이동 테스트

#### <a name="sap-hana-takeover-by-cluster"></a>(클러스터에 의한 SAP Hana 인수)


하나의 노드에서 다른 노드로의 SAPHana 리소스 이동을 테스트하려면 다음 명령을 사용합니다. SAPHana 리소스가 내부적으로 작동하는 방식 때문에 다음 명령을 실행할 때 `--primary` 옵션은 사용하지 않습니다.

`pcs resource move SAPHana_HR2_00-primary`

각 pcs 리소스 이동 명령을 호출하면 클러스터가 위치 제약 조건을 만들어서 리소스의 이동을 달성합니다. 나중에 자동 장애 조치를 허용하려면 이 제약 조건을 제거해야 합니다.
제약 조건을 제거하려면 다음 명령을 사용하면 됩니다.

```
pcs resource clear SAPHana_HR2_00-primary
crm_mon -A1
Node Attributes:
    * Node node1.localdomain:
    + hana_hr2_clone_state : DEMOTED
    + hana_hr2_remoteHost : node2
    + hana_hr2_roles : 2:P:primary1::worker:
    + hana_hr2_site : DC1
    + hana_hr2_srmode : syncmem
    + hana_hr2_sync_state : PRIM
    + hana_hr2_version : 2.00.033.00.1535711040
    + hana_hr2_vhost : node1
    + lpa_hr2_lpt : 1540867236
    + primary-SAPHana_HR2_00 : 150
    * Node node2.localdomain:
    + hana_hr2_clone_state : PROMOTED
    + hana_hr2_op_mode : logreplay
    + hana_hr2_remoteHost : node1
    + hana_hr2_roles : 4:S:primary1:primary:worker:primary
    + hana_hr2_site : DC2
    + hana_hr2_srmode : syncmem
    + hana_hr2_sync_state : SOK
    + hana_hr2_version : 2.00.033.00.1535711040
    + hana_hr2_vhost : node2
    + lpa_hr2_lpt : 1540867311
    + primary-SAPHana_HR2_00 : 100
```
  

* 확인을 위해 HANA에 로그인합니다.

  * 강등된 호스트:

    ```
    hdbsql -i 00 -u system -p $YourPass -n 10.7.0.82

    result:

            * -10709: Connection failed (RTE:[89006] System call 'connect'
    failed, rc=111:Connection refused (10.7.0.82:30015))
    ```
  
  * 승격된 호스트:

    ```
    hdbsql -i 00 -u system -p $YourPass -n 10.7.0.84
    
    Welcome to the SAP HANA Database interactive terminal.
    
    
    
    Type: \h for help with commands
    
    \q to quit
    
    
    
    hdbsql HR2=>
    
    
    
    
    DB is online
    ```
  

`AUTOMATED_REGISTER=false` 옵션을 사용하면 서로 간에 전환할 수 없습니다.

이 옵션을 false로 설정한 경우 노드를 다시 등록해야 합니다.
```
hdbnsutil -sr_register --remoteHost=node2 --remoteInstance=00 --replicationMode=syncmem --name=DC1
```

이제 기본 노드였던 node2가 보조 호스트로 기능합니다.

강등된 호스트의 등록을 자동화하려면 이 옵션을 true로 설정하세요.
  
```
pcs resource update SAPHana_HR2_00-primary AUTOMATED_REGISTER=true
pcs cluster node clear node1
```

자동 등록을 선호하는지 여부는 고객 시나리오에 따라 달라집니다. 운영 팀의 편의를 위해서는 인수가 이루어진 후에 노드를 자동으로 다시 등록하는 것이 좋을 수 있습니다. 그러나 먼저 추가 테스트를 실행하여 모든 것이 예상대로 작동하는지 확인하려면 노드를 수동으로 등록하는 것이 좋을 수 있습니다.

##  <a name="references"></a>참조

1. [pacemaker 클러스터의 스케일 업에서 자동화된 SAP HANA 시스템 복제](https://access.redhat.com/articles/3397471)
2. [RHEL 고가용성 클러스터 지원 정책 - 클러스터에서 SAP HANA 관리](https://access.redhat.com/articles/3397471)
3. [Azure의 RHEL에서 Pacemaker 설정 - Azure Virtual Machines](high-availability-guide-rhel-pacemaker.md)
4. [Azure Portal을 통한 Azure HANA 대규모 인스턴스 제어 - Azure Virtual Machines](hana-li-portal.md)

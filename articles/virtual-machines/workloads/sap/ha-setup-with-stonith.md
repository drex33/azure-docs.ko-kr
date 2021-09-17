---
title: STONITH를 사용하여 Azure(대규모 인스턴스)의 SAP HANA에 대한 고가용성 설정 | Microsoft Docs
description: STONITH 디바이스를 사용하여 SUSE에서 Azure(Large Instances)의 SAP HANA에 대한 고가용성 설정 방법을 알아봅니다.
services: virtual-machines-linux
documentationcenter: ''
author: Ajayan1008
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 9/01/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 0ae2b1ebd0b48b38a0f5b7a27097c89f94205d88
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/03/2021
ms.locfileid: "123423685"
---
# <a name="high-availability-set-up-in-suse-using-the-stonith-device"></a>STONITH 디바이스를 사용하여 SUSE에서 고가용성 설정

이 문서에서는 STONITH 디바이스를 사용하여 SUSE 운영 체제의 HANA 대규모 인스턴스에서 고가용성을 설정하는 단계를 진행합니다.

**책임 부인:** *이 가이드는 Microsoft HANA Large Instances 환경에서 설정을 성공적으로 테스트하여 파생되었습니다. HANA Large Instances에 대한 Microsoft 서비스 관리 팀에서는 운영 체제를 지원하지 않으므로 운영 체제 계층에 대한 추가 문제 해결 또는 확인이 필요한 경우 SUSE에 문의해야 할 수 있습니다. Microsoft 서비스 관리 팀은 STONITH 디바이스를 설정하고 STONITH 디바이스 문제를 완벽하게 지원하며 문제 해결에 참여할 수 있습니다.*

## <a name="prerequisites"></a>필수 구성 요소

SUSE 클러스터링을 사용하여 HA(고가용성)를 설정하려면 다음 필수 구성을 충족해야 합니다.

- HANA 대규모 인스턴스 프로비전
- OS(운영 체제) 등록
- 패치/패키지를 얻기 위해 SMT 서버에 연결된 HANA 대규모 인스턴스 서버
- 최신 패치가 설치된 운영 체제
- 네트워크 시간 프로토콜(NTP 시간 서버) 설정
- HA 설정에 관한 SUSE 설명서의 최신 버전 이해

## <a name="setup-details"></a>설정 정보
이 가이드에서 사용하는 설정은 다음과 같습니다.
- 운영 체제: SAP용 SLES 12 SP1
- HANA(대규모 인스턴스): 2xS192(소켓 4개, TB 2개)
- HANA 버전: HANA 2.0 SP1
- 서버 이름: sapprdhdb95(노드 1) 및 sapprdhdb96(노드 2)
- STONITH 디바이스: iSCSI 기반 STONITH 디바이스
- HANA 큰 인스턴스 노드 중 하나에 대한 NTP 설정

HSR(HANA 시스템 복제)을 사용하여 HANA 대규모 인스턴스를 설정하는 경우 Microsoft 서비스 관리 팀이 STONITH 디바이스를 설정하도록 요청할 수 있습니다. 프로비전 시 이 작업을 바로 수행합니다. 

HANA Large Instances가 이미 프로비전된 기존 고객의 경우에도 STONITH 디바이스를 설정할 수 있습니다. 서비스 요청 양식(SRF)에서 Microsoft 서비스 관리 팀에 다음 정보를 제공합니다. 기술 계정 관리자 또는 Microsoft의 HANA Large Instance 온보딩 담당자를 통해 SRF 양식을 요청할 수 있습니다.

- 서버 이름 및 서버 IP 주소(예: myhanaserver1, 10.35.0.1)
- 위치(예: 미국 동부)
- 고객 이름(예: Microsoft)
- SID - HANA 시스템 식별자(예: H11)

STONITH 디바이스가 구성되면 Microsoft 서비스 관리 팀에서 iSCSI 스토리지의 STONITH 블록 장치(SBD) 이름과 IP 주소를 제공합니다. 이 정보를 사용하여 STONITH 설정을 구성할 수 있습니다. 

다음 단계에 따라 STONITH를 사용하여 HA를 설정합니다.

1.  SBD 디바이스를 식별합니다.
2.  SBD 디바이스를 초기화합니다.
3.  클러스터를 구성합니다.
4.  Softdog Watchdog을 설정합니다.
5.  클러스터에 노드를 조인합니다.
6.  클러스터 유효성을 검사합니다.
7.  클러스터에 대한 리소스를 구성합니다.
8.  장애 조치(failover) 프로세스를 테스트합니다.

## <a name="identify-the-sbd-device"></a>SBD 디바이스 식별

> [!NOTE]
> 이 섹션은 기존 고객에게만 적용됩니다. 새 고객인 경우, Microsoft 서비스 관리 팀에서 SBD 디바이스 이름을 제공하므로 이 섹션을 건너뜁니다.

기존 고객의 경우에만: Microsoft 서비스 관리 팀이 STONITH 디바이스를 구성한 후에는 설정에 사용할 SBD 디바이스를 결정해야 합니다.

1. */etc/iscsi/initiatorname.isci를* 다음으로 수정합니다. 

    ``` 
    iqn.1996-04.de.suse:01:<Tenant><Location><SID><NodeNumber> 
    ```
    
    Microsoft 서비스 관리에서 이 문자열을 제공합니다. 노드 **둘 다** 에서 이 파일을 수정합니다. 노드 번호는 노드마다 다릅니다.
    
    ![스크린샷에는 노드에 대한 InitiatorName 값을 갖는 initiatorname 파일이 표시됩니다.](media/HowToHLI/HASetupWithStonith/initiatorname.png)

2. */etc/iscsi/iscsid.conf* 수정: *node.session.timeo.replacement_timeout=5* 및 *node.startup = automatic* 을 설정합니다. 노드 **둘 다** 에서 파일을 수정합니다.

3. 검색 명령을 실행합니다. 4개의 세션이 표시됩니다. 두 노드에서 모두 실행합니다.

    ```
    iscsiadm -m discovery -t st -p <IP address provided by Service Management>:3260
    ```
    
    ![스크린샷에는 isciadm 검색 명령의 결과가 포함된 콘솔 창이 표시됩니다.](media/HowToHLI/HASetupWithStonith/iSCSIadmDiscovery.png)

4. 명령을 실행하여 iSCSI 디바이스에 로그인합니다. 4개의 세션이 표시됩니다. **두** 노드에서 모두 실행합니다.

    ```
    iscsiadm -m node -l
    ```
    ![스크린샷에는 iscsiadm 노드 명령의 결과가 포함된 콘솔 창이 표시됩니다.](media/HowToHLI/HASetupWithStonith/iSCSIadmLogin.png)

5. 다시 검색 스크립트를 실행합니다. *rescan-scsi-bus.sh.*  이 스크립트는 생성된 새 디스크를 보여줍니다.  두 노드에서 모두 실행합니다. 0보다 큰 LUN 번호가 표시됩니다(예: 1, 2 등).

    ```
    rescan-scsi-bus.sh
    ```
    ![스크린샷에는 스크립트 결과가 포함된 콘솔 창이 표시됩니다.](media/HowToHLI/HASetupWithStonith/rescanscsibus.png)

6. 디바이스 이름을 얻으려면 *fdisk –l* 명령을 실행합니다. 두 노드에서 모두 실행합니다. **178MiB** 크기를 가진 디바이스를 선택합니다.

    ```
      fdisk –l
    ```
    
    ![스크린샷에는 f 디스크 명령의 결과가 포함된 콘솔 창이 표시됩니다.](media/HowToHLI/HASetupWithStonith/fdisk-l.png)

## <a name="initialize-the-sbd-device"></a>SBD 디바이스 초기화

이 섹션과 다음 섹션은 신규 및 기존 고객에게 모두 적용됩니다.

1. **두** 노드에서 SBD 디바이스를 초기화합니다.

    ```
    sbd -d <SBD Device Name> create
    ```
    ![스크린 샷에는 s b d 생성 명령의 결과가 포함된 콘솔 창이 표시됩니다.](media/HowToHLI/HASetupWithStonith/sbdcreate.png)

2. 디바이스에 기록된 내용을 확인합니다. 두 노드에서 **모두** 수행합니다.

    ```
    sbd -d <SBD Device Name> dump
    ```

## <a name="configure-the-cluster"></a>클러스터 구성

이 섹션에서는 SUSE HA 클러스터를 설정하는 단계를 설명합니다.

1. 먼저 패키지를 설치합니다. ha_sles 및 SAPHanaSR-doc 패턴이 설치되어 있는지 확인합니다. 설치되지 않은 경우 설치합니다. **두** 노드 모두에 설치합니다.

    ```
    zypper in -t pattern ha_sles
    zypper in SAPHanaSR SAPHanaSR-doc
    ```
    ![스크린샷은 패턴 명령의 결과가 포함된 콘솔 창을 보여줍니다.](media/HowToHLI/HASetupWithStonith/zypperpatternha_sles.png)
    ![스크린샷은 SAPHanaSR-doc 명령의 결과가 포함된 콘솔 창을 보여줍니다.](media/HowToHLI/HASetupWithStonith/zypperpatternSAPHANASR-doc.png)
    
2. 이제 클러스터를 설정합니다. *ha-cluster-init* 명령을 사용하거나 yast2 마법사를 사용하여 클러스터를 설정할 수 있습니다. 이 예제에서는 yast2 마법사를 사용했습니다. **주 노드에 대해서만** 이 단계를 수행합니다.

    1. **yast2**  >  **고가용성**  >  **클러스터** 팔로우
    
        ![고가용성 및 클러스터가 선택된 YaST 제어 센터를 보여주는 스크린샷.](media/HowToHLI/HASetupWithStonith/yast-control-center.png)
        
        ![치 및 취소 옵션이 있는 대화 상자를 보여주는 스크린샷.](media/HowToHLI/HASetupWithStonith/yast-hawk-install.png)
        
    1. halk2 패키지가 이미 설치되어 있으므로 **취소** 를 선택합니다.
    
        ![스크린샷에는 취소 옵션에 대한 메시지가 표시됩니다.](media/HowToHLI/HASetupWithStonith/yast-hawk-continue.png)
    
    1. **계속** 을 선택합니다.
    
        예상 값=배포된 노드 수(이 경우 2).
        
        ![스크린샷에는 보안 인증 사용 확인란이 있는 클러스터 보안이 표시됩니다.](media/HowToHLI/HASetupWithStonith/yast-Cluster-Security.png)
        
    1. **다음** 을 선택합니다.
    
        ![스크린샷에는 호스트 동기화 및 파일 목록 동기화가 포함된 클러스터 구성 창이 표시됩니다.](media/HowToHLI/HASetupWithStonith/yast-cluster-configure-csync2.png)
        
        노드 이름을 추가한 다음 제안된 파일 추가를 선택합니다.
        
    1. **csync2 켜기** 를 선택합니다.
    
    1. **사전 공유 키 생성** 을 선택합니다. 아래 팝업이 표시됩니다.
    
        ![스크린샷에는 키가 생성되었다는 메시지가 표시됩니다.](media/HowToHLI/HASetupWithStonith/yast-key-file.png)
        
    1. **확인** 을 선택합니다.
    
        IP 주소 및 Csync2의 미리 공유한 키를 사용하여 인증을 수행합니다. csync2 -k /etc/csync2/key_hagroup을 사용하여 키 파일을 생성합니다. key_hagroup 파일을 생성한 후 클러스터의 모든 멤버에 수동으로 복사해야 합니다. **반드시 노드1에서 노드2로 파일을 복사해야 합니다**.
        
        ![스크린샷에는 클러스터의 모든 구성원에 키를 복사하는 데 필요한 옵션이 있는 클러스터 구성 대화 상자가 표시됩니다.](media/HowToHLI/HASetupWithStonith/yast-cluster-conntrackd.png)
        
    1. **다음** 을 선택합니다.
        ![클러스터 서비스 창을 보여주는 스크린샷.](media/HowToHLI/HASetupWithStonith/yast-cluster-service.png)
        
        기본 옵션에서 부팅이 해제되었습니다. 부팅 시 pacemaker가 시작되도록 켜기로 변경합니다. 설정 요구 사항에 따라 선택할 수 있습니다.
    
    1. **다음** 을 선택하면 클러스터 구성이 완료됩니다.

## <a name="set-up-the-softdog-watchdog"></a>소프트 dog watchdog 설정

이 섹션에서는 Watchdog(softdog) 구성을 설명합니다.

1. **두** 노드의 */etc/init.d/boot.local* 에 다음 줄을 추가 합니다.
    
    ```
    modprobe softdog
    ```
    ![스크린샷에는 softdog 줄이 추가된 부팅 파일이 표시됩니다.](media/HowToHLI/HASetupWithStonith/modprobe-softdog.png)
    
2. **두 노드에서 모두** 다음과 같이 *노드에서/etc/sysconfig/sbd* 파일을 업데이트 합니다.
    
    ```
    SBD_DEVICE="<SBD Device Name>"
    ```
    ![스크린샷에는 S B D_DEVICE 값이 추가된 s b d 파일이 표시됩니다.](media/HowToHLI/HASetupWithStonith/sbd-device.png)
    
3. 다음 명령을 실행 하 여 **두** 노드에서 커널 모듈을 로드 합니다.
    
    ```
    modprobe softdog
    ```
    ![스크린샷에는 modprobe softdog 명령이 있는 콘솔 창의 일부가 표시됩니다.](media/HowToHLI/HASetupWithStonith/modprobe-softdog-command.png)

4. **두 노드에서 모두** 다음과 같이 소프트 노드가 실행 되 고 있는지 확인 합니다.
    
    ```
    lsmod | grep dog
    ```
    ![스크린샷에는 l s mod 명령을 실행한 결과가 포함된 콘솔 창의 일부가 표시됩니다.](media/HowToHLI/HASetupWithStonith/lsmod-grep-dog.png)
    
5. **두 노드에서 모두** SBD 장치를 시작 합니다.

    ```
    /usr/share/sbd/sbd.sh start
    ```
    ![스크린샷에는 시작 명령이 포함된 콘솔 창의 일부가 표시됩니다.](media/HowToHLI/HASetupWithStonith/sbd-sh-start.png)
    
6. **두 노드에서 모두** SBD 디먼을 테스트 합니다. 두 노드에서 모두 구성 하 고 나면 두 개의 항목이 표시 됩니다.
    
    ```
    sbd -d <SBD Device Name> list
    ```
    ![스크린샷에는 두 개의 항목이 표시된 콘솔 창의 일부가 표시됩니다.](media/HowToHLI/HASetupWithStonith/sbd-list.png)
    
7. 노드 중 **하나** 에 테스트 메시지를 보냅니다.

    ```
    sbd  -d <SBD Device Name> message <node2> <message>
    ```
    ![스크린샷에는 두 개의 항목이 표시된 콘솔 창의 일부가 표시됩니다.](media/HowToHLI/HASetupWithStonith/sbd-list.png)
    
8. **두 번째** 노드 (노드 2)에서 메시지 상태를 확인할 수 있습니다.
    
    ```
    sbd  -d <SBD Device Name> list
    ```
    ![스크린샷에는 다른 구성원에 대한 테스트 값을 나타내는 구성원 중 하나를 사용하는 콘솔 창의 일부가 표시됩니다.](media/HowToHLI/HASetupWithStonith/sbd-list-message.png)
    
9. Sbd config를 채택 하려면 다음과 같이 파일 *노드에서/etc/sysconfig/sbd* 를 업데이트 합니다. 노드 **둘 다** 에서 파일을 업데이트합니다.

    ```
    SBD_DEVICE=" <SBD Device Name>" 
    SBD_WATCHDOG="yes" 
    SBD_PACEMAKER="yes" 
    SBD_STARTMODE="clean" 
    SBD_OPTS=""
    ```
10. **주 노드** (node1)에서 pacemaker 서비스를 시작 합니다.

    ```
    systemctl start pacemaker
    ```
    ![스크린샷에는 Pacemaker를 시작한 후 상태를 나타내는 콘솔 창이 표시됩니다.](media/HowToHLI/HASetupWithStonith/start-pacemaker.png)
    
    Pacemaker 서비스가 *실패* 한 경우, *시나리오 5: Pacemaker 서비스 실패* 를 참조하세요.

## <a name="join-the-cluster"></a>클러스터에 가입

다음으로 클러스터에 노드를 조인 합니다.

- 노드를 추가 합니다. 다음 명령을 **노드 2** 에서 실행하여 노드 2를 클러스터에 조인합니다.

    ```
    ha-cluster-join
    ```
    클러스터 조인 중에 *오류* 가 표시되는 경우 *시나리오 6: 노드 2가 클러스터를 조인할 수 없는 경우* 를 참조하세요.

## <a name="validate-the-cluster"></a>클러스터 유효성 검사

1. 클러스터 서비스를 시작 합니다. **두** 노드에서 처음으로 클러스터를 확인 하 고 필요에 따라 시작 합니다.
    
     ```
    systemctl status pacemaker
    systemctl start pacemaker
    ```
    ![스크린샷에는 Pacemaker의 상태가 포함된 콘솔 창이 표시됩니다.](media/HowToHLI/HASetupWithStonith/systemctl-status-pacemaker.png)
        
2. 상태를 모니터링 합니다. 두 노드에서 **모두***crm_mon* 명령을 실행하여 노드가 온라인인지 확인합니다. 이 작업은 클러스터의 **임의 노드** 에서 실행할 수 있습니다.
    
    ```
    crm_mon
    ```
    ![스크린샷에는 c r m_mon의 결과가 포함된 콘솔 창이 표시됩니다.](media/HowToHLI/HASetupWithStonith/crm-mon.png)
    
    hawk에 로그인하여 클러스터 상태 *https://\<node IP>:7630* 을 확인할 수도 있습니다. 기본 사용자는 hacluster이며 암호는 linux입니다. 필요한 경우 *passwd* 명령을 사용하여 암호를 변경할 수 있습니다.
    
## <a name="configure-cluster-properties-and-resources"></a>클러스터 속성 및 리소스 구성

이 섹션에서는 클러스터 리소스를 구성하는 단계를 설명합니다.
이 예제에서는 다음 리소스를 설정하였으며, 나머지는 SUSE HA 가이드를 참조하여 구성할 수 있습니다(필요한 경우).

- 클러스터 부트스트랩
- STONITH 디바이스
- 가상 IP 주소

**노드 중 하나** 에서만 구성을 수행 합니다. 주 노드에서 수행합니다.

1. 클러스터 부트스트랩 및 추가 구성: 클러스터 부트스트랩을 추가 합니다. 파일을 만들고 다음 텍스트를 추가합니다.
    
    ```
    sapprdhdb95:~ # vi crm-bs.txt
    # enter the following to crm-bs.txt
    property $id="cib-bootstrap-options" \
    no-quorum-policy="ignore" \
    stonith-enabled="true" \
    stonith-action="reboot" \
    stonith-timeout="150s"
    rsc_defaults $id="rsc-options" \
    resource-stickiness="1000" \
    migration-threshold="5000"
    op_defaults $id="op-options" \
    timeout="600"
    ```
2. 클러스터에 구성을 추가 합니다.

    ```
    crm configure load update crm-bs.txt
    ```
    ![스크린샷에는 c r m 명령을 실행하는 콘솔 창의 일부가 표시됩니다.](media/HowToHLI/HASetupWithStonith/crm-configure-crmbs.png)
    
3. STONITH 장치 구성: 리소스를 추가 하 고, 파일을 만들고, 다음과 같이 텍스트를 추가 합니다.

    ```
    # vi crm-sbd.txt
    # enter the following to crm-sbd.txt
    primitive stonith-sbd stonith:external/sbd \
    params pcmk_delay_max="15"
    ```
      - 클러스터에 구성을 추가합니다.
        
          ```
          crm configure load update crm-sbd.txt
          ```
        
4. 가상 IP 주소 구성: 리소스 가상 IP 추가를 추가 합니다. 파일을 만들고 다음과 같이 텍스트를 추가 합니다.

    ```
    # vi crm-vip.txt
    primitive rsc_ip_HA1_HDB10 ocf:heartbeat:IPaddr2 \
    operations $id="rsc_ip_HA1_HDB10-operations" \
    op monitor interval="10s" timeout="20s" \
    params ip="10.35.0.197"
    ```
    - 클러스터에 구성을 추가 합니다.
    
        ```
        crm configure load update crm-vip.txt
        ```
        
5. 이제 리소스의 유효성을 검사 합니다. *Crm_mon* 명령을 실행 하면 두 리소스를 볼 수 있습니다.

    ![스크린샷에는 두 개의 리소스가 포함된 콘솔 창이 표시됩니다.](media/HowToHLI/HASetupWithStonith/crm_mon_command.png)

    - *https://\<node IP address>:7630/cib/live/state* 에서 상태를 확인할 수 있습니다.
    
        ![스크린샷에는 두 개의 리소스 상태가 표시됩니다.](media/HowToHLI/HASetupWithStonith/hawlk-status-page.png)
    
## <a name="test-the-failover-process"></a>장애 조치(failover) 프로세스 테스트

1. 장애 조치 프로세스를 테스트하려면 노드 1에서 Pacemaker 서비스를 중단하고 리소스를 노드 2에 장애 조치합니다.

    ```
    Service pacemaker stop
    ```
2. 이제 **노드 2** 에서 Pacemaker 서비스를 중단하고 리소스를 **노드 1** 에 대해 장애 조치합니다.

    **장애 조치(failover) 전**  
    ![스크린샷에는 장애 조치(failover) 전 두 개의 리소스 상태가 표시됩니다.](media/HowToHLI/HASetupWithStonith/Before-failover.png)  
    
    **장애 조치(failover) 후**  
    ![스크린샷에는 장애 조치(failover) 후 두 개의 리소스 상태가 표시됩니다.](media/HowToHLI/HASetupWithStonith/after-failover.png)
    
    ![스크린샷에는 장애 조치(failover) 후 리소스 상태가 포함된 콘솔 창이 표시됩니다.](media/HowToHLI/HASetupWithStonith/crm-mon-after-failover.png)  
    

## <a name="troubleshooting"></a>문제 해결
이 섹션에서는 설치 중에 발생할 수 있는 몇 가지 실패 시나리오를 설명합니다. 이 문제가 발생하지 않을 수도 있습니다.

### <a name="scenario-1-cluster-node-not-online"></a>시나리오 1: 클러스터 노드가 온라인이 아닌 경우

노드 중 하나라도 클러스터 관리자에서 온라인으로 표시되지 않으면 다음을 시도하여 온라인으로 전환할 수 있습니다.

1. iSCSI 서비스 시작

    ```
    service iscsid start
    ```
    
2. 이제 해당 iSCSI 노드에 로그인 합니다.

    ```
    iscsiadm -m node -l
    ```
    예상 출력은 다음과 같습니다.

    ```
    sapprdhdb45:~ # iscsiadm -m node -l
    Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.11,3260] (multiple)
    Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.12,3260] (multiple)
    Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.22,3260] (multiple)
    Logging in to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.21,3260] (multiple)
    Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.11,3260] successful.
    Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.12,3260] successful.
    Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.22,3260] successful.
    Login to [iface: default, target: iqn.1992-08.com.netapp:hanadc11:1:t020, portal: 10.250.22.21,3260] successful.
    ```
### <a name="scenario-2-yast2-doesnt-show-graphical-view"></a>시나리오 2: yast2가 그래픽 보기에 표시되지 않는 경우

yast2 그래픽 화면이 이 문서의 고가용성 클러스터를 설정하는 데 사용됩니다. yast2가 그림과 같은 그래픽 창과 함께 열리지 않고 Qt 오류가 표시되면 아래 단계를 수행합니다. 그래픽 창과 함께 열리면 이 단계를 건너뛸 수 있습니다.

**오류**

![스크린샷에는 오류 메시지가 포함된 콘솔 창의 일부가 표시됩니다.](media/HowToHLI/HASetupWithStonith/yast2-qt-gui-error.png)

**예상 출력**

![스크린샷에는 고가용성 및 클러스터가 강조 표시된 YaST 제어 센터가 표시됩니다.](media/HowToHLI/HASetupWithStonith/yast-control-center.png)

yast2가 그래픽 창과 함께 열리지 않는 경우, 다음 단계를 수행합니다.

1. 필요한 패키지를 설치합니다. “루트” 사용자로 로그인해야 하며 SMT 설치 프로그램을 다운로드하거나/패키지를 설치해야 합니다.

2. 패키지를 설치하려면 yast>소프트웨어>소프트웨어 관리>종속성> 옵션 “권장 패키지 설치...”를 사용합니다. 다음 스크린 샷은 예상 화면을 보여 줍니다.

    >[!NOTE]
    >두 노드에서 모두 yast2 그래픽 보기에 액세스할 수 있도록 두 노드에서 모두 이 단계를 수행해야 합니다.
    
    ![스크린샷에는 YaST 제어 센터를 나타내는 콘솔 창이 표시됩니다.](media/HowToHLI/HASetupWithStonith/yast-sofwaremanagement.png)
    
3. 종속성 아래에서 **권장 패키지 설치** 를 선택 합니다.

    ![권장 패키지 설치가 선택된 콘솔 창을 보여주는 스크린샷.](media/HowToHLI/HASetupWithStonith/yast-dependencies.png)

4. 변경 내용을 검토하고 **확인** 을 선택합니다.

    ![yast](media/HowToHLI/HASetupWithStonith/yast-automatic-changes.png)

    패키지 설치가 진행 됩니다.

    ![설치 진행률이 표시된 콘솔 창을 보여주는 스크린샷.](media/HowToHLI/HASetupWithStonith/yast-performing-installation.png)

5. **다음** 을 선택합니다.

    ![스크린샷에는 성공 메시지가 포함된 콘솔 창이 표시됩니다.](media/HowToHLI/HASetupWithStonith/yast-installation-report.png)

6. **마침** 을 선택합니다.

7. libqt4 및 libyui-qt 패키지도 설치해야 합니다.
    
    ```
    zypper -n install libqt4
    ```
    ![스크린샷에는 libqt4 패키지를 설치하는 콘솔 창이 표시됩니다.](media/HowToHLI/HASetupWithStonith/zypper-install-libqt4.png)
    
    ```
    zypper -n install libyui-qt
    ```
    ![libyui-qt 패키지를 설치하는 콘솔 창을 보여주는 스크린샷.](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui.png)
    
    ![libyui-qt 패키지를 설치하는 콘솔 창을 보여주는 스크린샷, 계속.](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui_part2.png)
    
    이제 Yast2는 여기에 표시된 대로 그래픽 보기를 열 수 있습니다.

    ![스크린샷에 소프트웨어 및 온라인 업데이트가 선택된 YaST 제어 센터가 표시됩니다.](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

### <a name="scenario-3-yast2-doesnt-show-the-high-availability-option"></a>시나리오 3: yast2에 고가용성 옵션이 표시되지 않습니다

고가용성 옵션이 yast2 제어 센터에 표시되도록 하려면 다른 패키지를 설치해야 합니다.

1. Yast2>소프트웨어>소프트웨어 관리>를 사용하여 다음 패턴을 선택합니다.

    - SAP HANA 서버 베이스
    - C/C++ 컴파일러 및 도구
    - 고가용성
    - SAP 애플리케이션 서버 베이스

    다음 화면은 패턴을 설치하는 단계를 나타냅니다.

    yast2 > 소프트웨어 > 소프트웨어 관리 사용

    ![스크린샷에는 설치를 시작하기 위해 소프트웨어 및 온라인 업데이트가 선택된 YaST 제어 센터가 표시됩니다.](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

2. 패턴을 선택합니다.

    ![C/C++ 컴파일러 및 도구 항목의 첫 번째 패턴을 선택하는 방법을 보여주는 스크린샷.](media/HowToHLI/HASetupWithStonith/yast-pattern1.png)

    ![C/C++ 컴파일러 및 도구 항목의 두 번째 패턴을 선택하는 방법을 보여주는 스크린샷.](media/HowToHLI/HASetupWithStonith/yast-pattern2.png)

3. **수락** 을 선택합니다.

    ![스크린샷에는 종속성을 확인하도록 변경된 패키지가 있는 변경된 패키지 대화 상자가 표시됩니다.](media/HowToHLI/HASetupWithStonith/yast-changed-packages.png)

4. **계속** 을 선택합니다.

    ![스크린샷에는 설치 수행 상태 페이지가 표시됩니다.](media/HowToHLI/HASetupWithStonith/yast2-performing-installation.png)

5. 설치가 완료되면 **다음** 을 선택합니다.

    ![스크린샷에는 설치 보고서가 표시됩니다.](media/HowToHLI/HASetupWithStonith/yast2-installation-report.png)

### <a name="scenario-4-hana-installation-fails-with-gcc-assemblies-error"></a>시나리오 4: HANA 설치가 실패하고 gcc 어셈블리 오류가 발생하는 경우
HANA 설치가 실패하고 다음 오류가 발생합니다.

![스크린샷에는 운영 체제가 g c c 5 어셈블리를 수행할 준비가 되지 않았다는 오류 메시지가 표시됩니다.](media/HowToHLI/HASetupWithStonith/Hana-installation-error.png)

- 문제를 해결 하려면 다음 스크린샷에 따라 라이브러리 (libgcc_sl 및 libstdc + + 6)를 설치 해야 합니다.

    ![스크린샷에는 필요한 라이브러리를 설치하는 콘솔 창이 표시됩니다.](media/HowToHLI/HASetupWithStonith/zypper-install-lib.png)

### <a name="scenario-5-pacemaker-service-fails"></a>시나리오 5: Pacemaker 서비스 실패

Pacemaker 서비스를 시작하는 동안 다음 문제가 발생했습니다.

```
sapprdhdb95:/ # systemctl start pacemaker
A dependency job for pacemaker.service failed. See 'journalctl -xn' for details.
```
```
sapprdhdb95:/ # journalctl -xn
-- Logs begin at Thu 2017-09-28 09:28:14 EDT, end at Thu 2017-09-28 21:48:27 EDT. --
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync configuration map
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync configuration ser
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync cluster closed pr
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [QB    ] withdrawing server sockets
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync cluster quorum se
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [SERV  ] Service engine unloaded: corosync profile loading s
Sep 28 21:48:27 sapprdhdb95 corosync[68812]: [MAIN  ] Corosync Cluster Engine exiting normally
Sep 28 21:48:27 sapprdhdb95 systemd[1]: Dependency failed for Pacemaker High Availability Cluster Manager
-- Subject: Unit pacemaker.service has failed
-- Defined-By: systemd
-- Support: https://lists.freedesktop.org/mailman/listinfo/systemd-devel
--
-- Unit pacemaker.service has failed.
--
-- The result is dependency.
```
```
sapprdhdb95:/ # tail -f /var/log/messages
2017-09-28T18:44:29.675814-04:00 sapprdhdb95 corosync[57600]:   [QB    ] withdrawing server sockets
2017-09-28T18:44:29.676023-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync cluster closed process group service v1.01
2017-09-28T18:44:29.725885-04:00 sapprdhdb95 corosync[57600]:   [QB    ] withdrawing server sockets
2017-09-28T18:44:29.726069-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync cluster quorum service v0.1
2017-09-28T18:44:29.726164-04:00 sapprdhdb95 corosync[57600]:   [SERV  ] Service engine unloaded: corosync profile loading service
2017-09-28T18:44:29.776349-04:00 sapprdhdb95 corosync[57600]:   [MAIN  ] Corosync Cluster Engine exiting normally
2017-09-28T18:44:29.778177-04:00 sapprdhdb95 systemd[1]: Dependency failed for Pacemaker High Availability Cluster Manager.
2017-09-28T18:44:40.141030-04:00 sapprdhdb95 systemd[1]: [/usr/lib/systemd/system/fstrim.timer:8] Unknown lvalue 'Persistent' in section 'Timer'
2017-09-28T18:45:01.275038-04:00 sapprdhdb95 cron[57995]: pam_unix(crond:session): session opened for user root by (uid=0)
2017-09-28T18:45:01.308066-04:00 sapprdhdb95 CRON[57995]: pam_unix(crond:session): session closed for user root
```

- 문제를 해결하려면 */usr/lib/systemd/system/fstrim.timer* 파일에서 다음 줄을 삭제합니다.

    ```
    Persistent=true
    ```
    
    ![스크린샷에는 삭제할 Persistent=true의 값이 포함된 fstrim 파일이 표시됩니다.](media/HowToHLI/HASetupWithStonith/Persistent.png)

### <a name="scenario-6-node-2-unable-to-join-the-cluster"></a>시나리오 6: 노드 2가 클러스터에 조인할 수 없는 경우

*ha-cluster-join* 명령을 사용하여 노드 2를 기존 클러스터에 조인할 때 다음 오류가 발생했습니다.

```
ERROR: Can’t retrieve SSH keys from <Primary Node>
```

![스크린샷에는 IP 주소에서 SSH 키를 검색할 수 없다는 오류 메시지와 함께 콘솔 창이 표시됩니다.](media/HowToHLI/HASetupWithStonith/ha-cluster-join-error.png)

1. 이 문제를 해결 하려면 두 노드에서 모두 다음을 실행 합니다.

    ```
    ssh-keygen -q -f /root/.ssh/id_rsa -C 'Cluster Internal' -N ''
    cat /root/.ssh/id_rsa.pub >> /root/.ssh/authorized_keys
    ```

    ![스크린샷에는 첫 번째 노드에서 명령을 실행하는 콘솔 창의 일부가 표시됩니다.](media/HowToHLI/HASetupWithStonith/ssh-keygen-node1.PNG)

    ![스크린샷에는 두 번째 노드에서 명령을 실행하는 콘솔 창의 일부가 표시됩니다.](media/HowToHLI/HASetupWithStonith/ssh-keygen-node2.PNG)

2. 앞의 문제 해결 후 노드 2가 클러스터에 추가되었을 것입니다.

    ![스크린샷에는 ha-cluster-join 명령이 성공적으로 실행된 콘솔 창이 표시됩니다.](media/HowToHLI/HASetupWithStonith/ha-cluster-join-fix.png)

## <a name="next-steps"></a>다음 단계

다음 문서에서 SUSE HA 설정에 관한 추가 정보를 찾을 수 있습니다. 

- [SAP HANA SR 성능 최적화된 시나리오](https://www.suse.com/support/kb/doc/?id=000019450 )
- [스토리지 기반 울타리](https://www.suse.com/documentation/sle_ha/book_sleha/data/sec_ha_storage_protect_fencing.html)
- [블로그 - SAP HANA에 Pacemaker 클러스터 사용- 1부](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-1-basics/)
- [블로그 - SAP HANA에 Pacemaker 클러스터 사용- 2부](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-2-failure-of-both-nodes/)

운영 체제 파일 수준 백업 및 복원을 수행하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [OS 백업 및 복원](large-instance-os-backup.md)

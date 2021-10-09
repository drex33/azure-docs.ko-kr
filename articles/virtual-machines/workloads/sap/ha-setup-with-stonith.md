---
title: STONITH를 사용하여 Azure(대규모 인스턴스)의 SAP HANA에 대한 고가용성 설정 | Microsoft Docs
description: STONITH 디바이스를 사용하여 SUSE에서 Azure(큰 인스턴스)에서 SAP HANA 고가용성을 설정하는 방법을 알아봅니다.
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
ms.openlocfilehash: 5866fbb227477b0079f5f2ac314357ca8e67a364
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129710834"
---
# <a name="high-availability-setup-in-suse-using-the-stonith-device"></a>STONITH 디바이스를 사용하여 SUSE의 고가용성 설정

이 문서에서는 STONITH 디바이스를 사용하여 SUSE 운영 체제의 HANA 큰 인스턴스에서 HA(고가용성)를 설정하는 단계를 진행합니다.

> [!NOTE]
> 이 가이드는 Microsoft HANA 큰 인스턴스 환경에서 설치 프로그램을 성공적으로 테스트한 후 파생되었습니다. HANA 대규모 인스턴스에 대한 Microsoft 서비스 관리 팀은 운영 체제를 지원하지 않습니다. 운영 체제 계층에 대한 문제 해결 또는 설명은 SUSE에 문의합니다. 
>
> Microsoft 서비스 관리 팀은 STONITH 디바이스를 설정하고 완벽하게 지원합니다. STONITH 디바이스 문제를 해결하는 데 도움이 될 수 있습니다.

## <a name="prerequisites"></a>필수 조건

SUSE 클러스터링을 사용하여 고가용성을 설정하려면 다음을 수행해야 합니다.

- HANA 큰 인스턴스를 프로비전합니다.
- 최신 패치를 설치하고 운영 체제를 등록합니다.
- 커넥트 HANA 대규모 인스턴스 서버에서 SMT 서버로 패치 및 패키지를 가져옵니다.
- 네트워크 시간 프로토콜(NTP 시간 서버)을 설정합니다.
- HA 설정에 대한 최신 SUSE 설명서를 읽고 이해합니다.

## <a name="setup-details"></a>설정 정보

이 가이드에서 사용하는 설정은 다음과 같습니다.

- 운영 체제: SAP용 SLES 12 SP1
- HANA 대규모 인스턴스: 2xS192(4개 소켓, 2TB)
- HANA 버전: HANA 2.0 SP1
- 서버 이름: sappr도b95(node1) 및 sappr 상태b96(node2)
- STONITH 디바이스: iSCSI 기반
- HANA 큰 인스턴스 노드 중 하나의 NTP

HANA 시스템 복제를 사용하여 HANA 대규모 인스턴스를 설정하는 경우 Microsoft 서비스 관리 팀이 STONITH 디바이스를 설정하도록 요청할 수 있습니다. 프로비전 시 이 작업을 수행합니다. 

HANA Large Instances가 이미 프로비전된 기존 고객의 경우에도 STONITH 디바이스를 설정할 수 있습니다. 서비스 요청 양식(SRF)에서 Microsoft 서비스 관리 팀에 다음 정보를 제공합니다. 기술 계정 관리자 또는 HANA 큰 인스턴스 온보딩에 대한 Microsoft 연락처를 통해 SRF를 얻을 수 있습니다.

- 서버 이름 및 서버 IP 주소(예: myhanaserver1 및 10.35.0.1)
- 위치(예: 미국 동부)
- 고객 이름(예: Microsoft)
- HANA SID(시스템 식별자)(예: H11)

STONITH 디바이스가 구성되면 Microsoft 서비스 관리 팀에서 iSCSI 스토리지의 STONITH 블록 디바이스(SBD) 이름 및 IP 주소를 제공합니다. 이 정보를 사용하여 STONITH 설정을 구성할 수 있습니다. 

다음 섹션의 단계에 따라 STONITH를 사용하여 HA를 설정합니다.

## <a name="identify-the-sbd-device"></a>SBD 디바이스 식별

> [!NOTE]
> 이 섹션은 기존 고객에게만 적용됩니다. 새 고객인 경우, Microsoft 서비스 관리 팀에서 SBD 디바이스 이름을 제공하므로 이 섹션을 건너뜁니다.

1. */etc/iscsi/initiatorname.isci를* 다음으로 수정합니다. 

    ``` 
    iqn.1996-04.de.suse:01:<Tenant><Location><SID><NodeNumber> 
    ```
    
    Microsoft 서비스 관리에서 이 문자열을 제공합니다. 노드 *둘 다* 에서 파일을 수정합니다. 그러나 노드 번호는 노드마다 다릅니다.
    
    ![노드에 대한 InitiatorName 값이 있는 initiatorname 파일을 보여 주는 스크린샷](media/HowToHLI/HASetupWithStonith/initiatorname.png)

2. 및 *를 설정하여 /etc/iscsi/iscsid.conf를* 수정합니다. `node.session.timeo.replacement_timeout=5` `node.startup = automatic` 노드 *둘 다* 에서 파일을 수정합니다.

3. *두 노드에서* 다음 검색 명령을 실행합니다.

    ```
    iscsiadm -m discovery -t st -p <IP address provided by Service Management>:3260
    ```
    
    결과에는 4개의 세션이 표시됩니다.
    
    ![검색 명령의 결과가 포함된 콘솔 창을 보여 주는 스크린샷.](media/HowToHLI/HASetupWithStonith/iSCSIadmDiscovery.png)

4. *두 노드에서* 다음 명령을 실행하여 iSCSI 디바이스에 로그인합니다. 

    ```
    iscsiadm -m node -l
    ```
    
    결과에는 4개의 세션이 표시됩니다.
    
    ![노드 명령의 결과가 포함된 콘솔 창을 보여 주는 스크린샷.](media/HowToHLI/HASetupWithStonith/iSCSIadmLogin.png)

5. 다음 명령을 사용하여 *rescan-scsi-bus.sh* 다시 검색 스크립트를 실행합니다. 이 스크립트는 생성된 새 디스크를 보여줍니다.  *두* 노드에서 모두 실행합니다.

    ```
    rescan-scsi-bus.sh
    ```
    
    결과에 0보다 큰 LUN 번호가 표시됩니다(예: 1, 2 등).
     
    ![스크립트 결과가 포함된 콘솔 창을 보여 주는 스크린샷.](media/HowToHLI/HASetupWithStonith/rescanscsibus.png)

6. 디바이스 이름을 얻으려면 *두* 노드에서 다음 명령을 실행합니다. 

    ```
      fdisk –l
    ```
    
    결과에서 크기가 178 MiB인 디바이스를 선택합니다.
    
    ![f disk 명령의 결과가 포함된 콘솔 창을 보여 주는 스크린샷.](media/HowToHLI/HASetupWithStonith/fdisk-l.png)

## <a name="initialize-the-sbd-device"></a>SBD 디바이스 초기화

1. 다음 명령을 사용하여 *두* 노드에서 SBD 디바이스를 초기화합니다.

    ```
    sbd -d <SBD Device Name> create
    ```
    ![s b d create 명령의 결과가 포함된 콘솔 창을 보여 주는 스크린샷.](media/HowToHLI/HASetupWithStonith/sbdcreate.png)

2. *두 노드에서* 다음 명령을 사용하여 디바이스에 기록된 내용을 확인합니다.

    ```
    sbd -d <SBD Device Name> dump
    ```

## <a name="configure-the-suse-ha-cluster"></a>SUSE HA 클러스터 구성

1. 다음 명령을 사용하여 ha_sles 및 SAPHanaSR-doc 패턴이 *두 노드에 모두* 설치되어 있는지 확인합니다. 설치되지 않은 경우 설치합니다.

    ```
    zypper in -t pattern ha_sles
    zypper in SAPHanaSR SAPHanaSR-doc
    ```
    ![패턴 명령의 결과가 포함된 콘솔 창을 보여 주는 스크린샷.](media/HowToHLI/HASetupWithStonith/zypperpatternha_sles.png)
    
    ![SAPHanaSR-doc 명령의 결과가 포함된 콘솔 창을 보여 주는 스크린샷](media/HowToHLI/HASetupWithStonith/zypperpatternSAPHANASR-doc.png)
    
2. `ha-cluster-init`명령 또는 yast2 마법사를 사용하여 클러스터를 설정합니다. 이 예제에서는 yast2 마법사를 사용합니다. 이 단계는 주 노드 에서만 *수행합니다.*

    1. **yast2**  >  **고가용성**  >  **클러스터로** 이동합니다.
    
        ![고가용성 및 클러스터가 선택된 YaST 제어 센터를 보여 주는 스크린샷.](media/HowToHLI/HASetupWithStonith/yast-control-center.png)        
                
    1. 이 패키지 설치에 대한 대화 상자에서 halk2 패키지가 이미 설치되어 있으므로 **취소를** 선택합니다.
    
        ![설치 및 취소 옵션이 있는 대화 상자를 보여 주는 스크린샷.](media/HowToHLI/HASetupWithStonith/yast-hawk-install.png)
                    
    1. 계속하는 것에 대한 대화 상자에서 **계속을** 선택합니다.
    
        ![필수 패키지를 설치하지 않고 계속하는 것에 대한 메시지를 보여 주는 스크린샷.](media/HowToHLI/HASetupWithStonith/yast-hawk-continue.png)        
        
    1. 예상 값은 배포된 노드 수(이 경우 2)입니다. **다음** 을 선택합니다.  

     
        
    1. 노드 이름을 추가한 **다음, 제안된 파일 추가를** 선택합니다.

        ![동기화 호스트 및 동기화 파일 목록이 있는 클러스터 구성 창을 보여 주는 스크린샷.](media/HowToHLI/HASetupWithStonith/yast-cluster-configure-csync2.png)
        
    1. **csync2 켜기** 를 선택합니다.
    
    1. **미리 공유한 키 생성을** 선택합니다. 
    
    1. 표시되는 팝업 메시지에서 **확인을** 선택합니다.
    
        ![키가 생성되었다는 메시지를 보여 주는 스크린샷.](media/HowToHLI/HASetupWithStonith/yast-key-file.png)
        
    1. 인증은 Csync2의 IP 주소 및 미리 공유한 키를 사용하여 수행됩니다. 키 파일은 를 통해 `csync2 -k /etc/csync2/key_hagroup` 생성됩니다. 
    
        파일을 만든 후 클러스터의 모든 멤버에 *파일 key_hagroup* 수동으로 복사합니다. node1에서 node2로 파일을 복사해야 합니다. 그런 후 **다음** 을 선택합니다.
        
        ![클러스터의 모든 멤버에 키를 복사하는 데 필요한 옵션이 있는 클러스터 구성 대화 상자를 보여 주는 스크린샷.](media/HowToHLI/HASetupWithStonith/yast-cluster-conntrackd.png)
        
    1. 기본 옵션에서 **부팅은** **꺼져** 있습니다. 부팅 **시** pacemaker 서비스가 시작되도록 켜기로 변경합니다. 설정 요구 사항에 따라 선택할 수 있습니다.

        ![부팅이 켜져 있는 클러스터 서비스 창을 보여 주는 스크린샷.](media/HowToHLI/HASetupWithStonith/yast-cluster-service.png)
    
    1. **다음** 을 선택하면 클러스터 구성이 완료됩니다.

## <a name="set-up-the-softdog-watchdog"></a>softdog watchdog 설정

1. *두* 노드의 */etc/init.d/boot.local에* 다음 줄을 추가합니다.
    
    ```
    modprobe softdog
    ```
    ![softdog 줄이 추가된 부팅 파일을 보여 주는 스크린샷.](media/HowToHLI/HASetupWithStonith/modprobe-softdog.png)
    
2. 다음 명령을 사용 하 여 *두* 노드에서 *노드에서/etc/sysconfig/sbd* 파일을 업데이트 합니다.
    
    ```
    SBD_DEVICE="<SBD Device Name>"
    ```
    ![S B D_DEVICE 값이 추가 된 s b d 파일을 보여 주는 스크린샷](media/HowToHLI/HASetupWithStonith/sbd-device.png)
    
3. 다음 명령을 실행 하 여 *두* 노드에서 커널 모듈을 로드 합니다.
    
    ```
    modprobe softdog
    ```
    ![Modprobe 소프트 dog 명령을 사용 하 여 콘솔 창의 일부를 보여 주는 스크린샷](media/HowToHLI/HASetupWithStonith/modprobe-softdog-command.png)

4. 다음 명령을 사용 하 여 *두* 노드에서 소프트 노드가 실행 되 고 있는지 확인 합니다.
    
    ```
    lsmod | grep dog
    ```
    ![L s mod 명령을 실행 한 결과와 함께 콘솔 창의 일부를 보여 주는 스크린샷](media/HowToHLI/HASetupWithStonith/lsmod-grep-dog.png)
    
5. 다음 명령을 사용 하 여 *두 노드에서 모두* SBD 장치를 시작 합니다.

    ```
    /usr/share/sbd/sbd.sh start
    ```
    ![시작 명령이 있는 콘솔 창의 일부를 보여 주는 스크린샷](media/HowToHLI/HASetupWithStonith/sbd-sh-start.png)
    
6. 다음 명령을 사용 하 여 *두* 노드에서 SBD 디먼을 테스트 합니다. 
    
    ```
    sbd -d <SBD Device Name> list
    ```
    두 노드 모두 구성 후 결과에 두 개의 항목이 표시 됩니다.    
    
    ![두 개의 항목을 표시 하는 콘솔 창의 일부를 보여 주는 스크린샷](media/HowToHLI/HASetupWithStonith/sbd-list.png)
    
7. 다음 테스트 메시지를 노드 *중 하나* 에 보냅니다.

    ```
    sbd  -d <SBD Device Name> message <node2> <message>
    ```
    
8. *두 번째* 노드 (노드 2)에서 다음 명령을 사용 하 여 메시지 상태를 확인 합니다.
    
    ```
    sbd  -d <SBD Device Name> list
    ```
    ![다른 멤버에 대 한 테스트 값을 표시 하는 멤버 중 하나를 사용 하 여 콘솔 창의 일부를 보여 주는 스크린샷](media/HowToHLI/HASetupWithStonith/sbd-list-message.png)
    
9. SBD 구성을 채택 하려면 *두* 노드에서 다음과 같이 *노드에서/etc/sysconfig/sbd* 파일을 업데이트 합니다.

    ```
    SBD_DEVICE=" <SBD Device Name>" 
    SBD_WATCHDOG="yes" 
    SBD_PACEMAKER="yes" 
    SBD_STARTMODE="clean" 
    SBD_OPTS=""
    ```
10. 다음 명령을 사용 하 여 *주 노드* (node1)에서 pacemaker 서비스를 시작 합니다.

    ```
    systemctl start pacemaker
    ```
    ![Pacemaker를 시작한 후 상태를 표시 하는 콘솔 창을 보여 주는 스크린샷](media/HowToHLI/HASetupWithStonith/start-pacemaker.png)
    
    Pacemaker 서비스가 실패 하는 경우이 문서 뒷부분의 [시나리오 5: pacemaker 서비스 실패](#scenario-5-pacemaker-service-fails) 섹션을 참조 하십시오.

## <a name="join-the-node-to-the-cluster"></a>클러스터에 노드 조인

노드 2에서 다음 명령을 실행 *하 여 노드가* 클러스터에 연결 하도록 합니다.

```
ha-cluster-join
```

클러스터를 조인 하는 동안 오류가 발생 하는 경우이 문서 뒷부분의 [시나리오 6: 노드 2에서 클러스터에 연결할 수 없습니다](#scenario-6-node2-cant-join-the-cluster) . 섹션을 참조 하세요.

## <a name="validate-the-cluster"></a>클러스터 유효성 검사

1. 다음 명령을 사용 하 여 *두* 노드에서 처음으로 클러스터를 확인 하 고 필요에 따라 시작할 수 있습니다.
    
     ```
    systemctl status pacemaker
    systemctl start pacemaker
    ```
    ![Pacemaker 상태의 콘솔 창을 보여 주는 스크린샷](media/HowToHLI/HASetupWithStonith/systemctl-status-pacemaker.png)
        
2. 다음 명령을 실행 하 여 *두* 노드가 온라인 상태 인지 확인 합니다. 이 작업은 클러스터의 *임의 노드* 에서 실행할 수 있습니다.
    
    ```
    crm_mon
    ```
    ![C r m_mon 명령의 결과가 포함 된 콘솔 창을 보여 주는 스크린샷](media/HowToHLI/HASetupWithStonith/crm-mon.png)
    
    Hawk에 로그인 하 여 클러스터 상태를 확인할 수도 있습니다 `https://\<node IP>:7630` . 기본 사용자는 **hacluster** 이 고 암호는 **linux** 입니다. 필요한 경우 명령을 사용 하 여 암호를 변경할 수 있습니다 `passwd` .
    
## <a name="configure-cluster-properties-and-resources"></a>클러스터 속성 및 리소스 구성

이 섹션에서는 클러스터 리소스를 구성하는 단계를 설명합니다.
이 예제에서는 다음 리소스를 설정 합니다. SUSE HA 가이드를 참조 하 여 나머지 (필요한 경우)를 구성할 수 있습니다.

- 클러스터 부트스트랩
- STONITH 디바이스
- 가상 IP 주소

*주 노드에서만* 구성을 수행 합니다.

1. 클러스터 부트스트랩 파일을 만들고 다음 텍스트를 추가 하 여 구성 합니다.
    
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

2. 다음 명령을 사용 하 여 클러스터에 구성을 추가 합니다.

    ```
    crm configure load update crm-bs.txt
    ```
    ![C r m 명령을 실행 하는 콘솔 창의 일부를 보여 주는 스크린샷](media/HowToHLI/HASetupWithStonith/crm-configure-crmbs.png)
    
3. 리소스를 추가 하 고, 파일을 만들고, 다음과 같이 텍스트를 추가 하 여 STONITH 장치를 구성 합니다.

    ```
    # vi crm-sbd.txt
    # enter the following to crm-sbd.txt
    primitive stonith-sbd stonith:external/sbd \
    params pcmk_delay_max="15"
    ```
    다음 명령을 사용 하 여 클러스터에 구성을 추가 합니다.
        
    ```
    crm configure load update crm-sbd.txt
    ```
        
4. 파일을 만들고 다음 텍스트를 추가 하 여 리소스에 대 한 가상 IP 주소를 추가 합니다.

    ```
    # vi crm-vip.txt
    primitive rsc_ip_HA1_HDB10 ocf:heartbeat:IPaddr2 \
    operations $id="rsc_ip_HA1_HDB10-operations" \
    op monitor interval="10s" timeout="20s" \
    params ip="10.35.0.197"
    ```
    
    다음 명령을 사용 하 여 클러스터에 구성을 추가 합니다.
    
    ```
    crm configure load update crm-vip.txt
    ```
        
5. 명령을 사용 `crm_mon` 하 여 리소스의 유효성을 검사 합니다. 

    결과는 두 개의 리소스를 보여 줍니다.

    ![두 리소스를 포함 하는 콘솔 창을 보여 주는 스크린샷](media/HowToHLI/HASetupWithStonith/crm_mon_command.png)

    *Https:// \<node IP address> : 7630/cib/live/state* 에서 상태를 확인할 수도 있습니다.
    
    ![두 리소스의 상태를 보여 주는 스크린샷](media/HowToHLI/HASetupWithStonith/hawlk-status-page.png)
    
## <a name="test-the-failover-process"></a>장애 조치(failover) 프로세스 테스트

1. 장애 조치 (failover) 프로세스를 테스트 하려면 다음 명령을 사용 하 여 노드 1에서 pacemaker 서비스를 중지 합니다.

    ```
    Service pacemaker stop
    ```
    
    리소스는 노드 2로 장애 조치 (failover) 됩니다.

2. 노드 2에서 pacemaker 서비스를 중지 하 고 리소스가 node1로 장애 조치 (failover) 됩니다.

    장애 조치 (failover) 전의 상태는 다음과 같습니다.  
    ![장애 조치 (failover) 전에 두 리소스의 상태를 보여 주는 스크린샷](media/HowToHLI/HASetupWithStonith/Before-failover.png)  
    
    장애 조치 (failover) 후의 상태는 다음과 같습니다.  
    ![장애 조치 (failover) 후 두 리소스의 상태를 보여 주는 스크린샷](media/HowToHLI/HASetupWithStonith/after-failover.png)
    
    ![장애 조치 (failover) 후 리소스 상태를 포함 하는 콘솔 창을 보여 주는 스크린샷](media/HowToHLI/HASetupWithStonith/crm-mon-after-failover.png)  
    

## <a name="troubleshooting"></a>문제 해결

이 섹션에서는 설치 중 발생할 수 있는 오류 시나리오에 대해 설명 합니다.

### <a name="scenario-1-cluster-node-not-online"></a>시나리오 1: 클러스터 노드가 온라인이 아닌 경우

노드가 클러스터 관리자에서 온라인으로 표시 되지 않는 경우이 절차를 수행 하 여 온라인 상태로 만들 수 있습니다.

1. 다음 명령을 사용 하 여 iSCSI 서비스를 시작 합니다.

    ```
    service iscsid start
    ```
    
2. 다음 명령을 사용 하 여 해당 iSCSI 노드에 로그인 합니다.

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
### <a name="scenario-2-yast2-doesnt-show-graphical-view"></a>시나리오 2: Yast2에서 그래픽 보기를 표시 하지 않음

Yast2 그래픽 화면은이 문서의 고가용성 클러스터를 설정 하는 데 사용 됩니다. 표시 된 대로 그래픽 창에서 yast2가 열리지 않고 Qt 오류가 발생 하면 다음 단계를 수행 하 여 필요한 패키지를 설치 합니다. 그래픽 창과 함께 열리면 이 단계를 건너뛸 수 있습니다.

다음은 Qt 오류의 예입니다.

![오류 메시지와 함께 콘솔 창의 일부를 보여 주는 스크린샷](media/HowToHLI/HASetupWithStonith/yast2-qt-gui-error.png)

예상 출력의 예는 다음과 같습니다.

![고가용성 및 클러스터가 강조 표시 된 YaST 제어 센터를 보여 주는 스크린샷](media/HowToHLI/HASetupWithStonith/yast-control-center.png)

1. 사용자 "root"로 로그인 하 고 패키지를 다운로드 하 여 설치 하도록 SMT를 설정 했는지 확인 합니다.

2. **Yast**  >  **소프트웨어**  >  **소프트웨어 관리**  >  **종속성** 으로 이동한 후 **권장 패키지 설치** 를 선택 합니다. 

    >[!NOTE]
    >두 노드에서 모두 yast2 그래픽 보기에 액세스할 수 있도록 *두 노드에서 모두* 이 단계를 수행 합니다.
    
    다음 스크린샷에서는 예상 되는 화면을 보여 줍니다.
    
    ![YaST 제어 센터를 표시 하는 콘솔 창을 보여 주는 스크린샷](media/HowToHLI/HASetupWithStonith/yast-sofwaremanagement.png)
    
3. **종속성** 아래에서 **권장 패키지 설치** 를 선택 합니다.

    ![권장 패키지 설치가 선택 된 콘솔 창을 보여 주는 스크린샷](media/HowToHLI/HASetupWithStonith/yast-dependencies.png)

4. 변경 내용을 검토하고 **확인** 을 선택합니다.

    ![설치 하도록 선택한 패키지 목록이 포함 된 콘솔 창을 보여 주는 스크린샷](media/HowToHLI/HASetupWithStonith/yast-automatic-changes.png)

    패키지 설치가 진행 됩니다.

    ![설치 진행률을 표시 하는 콘솔 창을 보여 주는 스크린샷](media/HowToHLI/HASetupWithStonith/yast-performing-installation.png)

5. **다음** 을 선택합니다.    

6. **설치가 완료 되었습니다** 화면이 표시 되 면 **마침** 을 선택 합니다.

    ![성공 메시지를 포함 하는 콘솔 창을 보여 주는 스크린샷](media/HowToHLI/HASetupWithStonith/yast-installation-report.png)

7. 다음 명령을 사용 하 여 libqt4 및 libyui 패키지를 설치 합니다.
    
    ```
    zypper -n install libqt4
    ```
    ![첫 번째 패키지를 설치 하는 콘솔 창을 보여 주는 스크린샷](media/HowToHLI/HASetupWithStonith/zypper-install-libqt4.png)
    
    ```
    zypper -n install libyui-qt
    ```
    ![두 번째 패키지를 설치 하는 콘솔 창을 보여 주는 스크린샷](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui.png)
    
    ![계속 해 서 두 번째 패키지를 설치 하는 콘솔 창을 보여 주는 스크린샷](media/HowToHLI/HASetupWithStonith/zypper-install-ligyui_part2.png)
    
    이제 Yast2에서 그래픽 보기를 열 수 있습니다.

    ![소프트웨어 및 온라인 업데이트가 선택 된 YaST 제어 센터를 보여 주는 스크린샷](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

### <a name="scenario-3-yast2-doesnt-show-the-high-availability-option"></a>시나리오 3: Yast2는 고가용성 옵션을 표시 하지 않습니다.

Yast2 제어 센터에서 고가용성 옵션을 표시 하려면 다른 패키지를 설치 해야 합니다.

1. **Yast2**  >  **software**  >  **software Management** 로 이동 합니다. 그런 다음 **소프트웨어**  >  **온라인 업데이트** 를 선택 합니다.  

    ![소프트웨어 및 온라인 업데이트가 선택 된 YaST 제어 센터를 보여 주는 스크린샷](media/HowToHLI/HASetupWithStonith/yast2-control-center.png)

2. 다음 항목에 대 한 패턴을 선택 합니다. 그런 **다음, 동의를** 선택합니다.

    - SAP HANA 서버 베이스
    - C/C++ 컴파일러 및 도구
    - 고가용성
    - SAP 애플리케이션 서버 기반

    ![컴파일러 및 도구에 대한 항목에서 첫 번째 패턴을 선택하는 것을 보여 주는 스크린샷.](media/HowToHLI/HASetupWithStonith/yast-pattern1.png)

    ![컴파일러 및 도구에 대한 항목에서 두 번째 패턴을 선택하는 것을 보여 주는 스크린샷](media/HowToHLI/HASetupWithStonith/yast-pattern2.png)

4. Dependencies를 해결하기 위해 변경된 패키지 목록에서 **계속을** 선택합니다.

    ![패키지가 변경되어 dependencies를 해결하는 변경된 패키지 대화 상자를 보여 주는 스크린샷.](media/HowToHLI/HASetupWithStonith/yast-changed-packages.png)

4. 설치 **수행** 중 상태 페이지에서 **다음을** 선택합니다.

    ![설치 수행 상태 페이지를 보여 주는 스크린샷.](media/HowToHLI/HASetupWithStonith/yast2-performing-installation.png)

5. 설치가 완료되면 설치 보고서가 나타납니다. **마침** 을 선택합니다.

    ![설치 보고서를 보여 주는 스크린샷.](media/HowToHLI/HASetupWithStonith/yast2-installation-report.png)

### <a name="scenario-4-hana-installation-fails-with-gcc-assemblies-error"></a>시나리오 4: HANA 설치가 실패하고 gcc 어셈블리 오류가 발생하는 경우

HANA 설치에 실패하면 다음 오류가 발생할 수 있습니다.

![운영 체제가 g c c 5 어셈블리를 수행할 준비가 되지 않았다는 오류 메시지를 보여 주는 스크린샷.](media/HowToHLI/HASetupWithStonith/Hana-installation-error.png)

이 문제를 해결하려면 다음 스크린샷과 같이 libgcc_sl 및 libstdc++6 라이브러리를 설치합니다.

![필요한 라이브러리를 설치하는 콘솔 창을 보여 주는 스크린샷.](media/HowToHLI/HASetupWithStonith/zypper-install-lib.png)

### <a name="scenario-5-pacemaker-service-fails"></a>시나리오 5: Pacemaker 서비스 실패

Pacemaker 서비스를 시작할 수 없는 경우 다음 정보가 표시됩니다.

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

이 문제를 해결하려면 */usr/lib/systemd/system/fstrim.timer* 파일에서 다음 줄을 삭제합니다.

```
Persistent=true
```
    
![삭제할 영구=true 값이 있는 f s 트리밍 파일을 보여 주는 스크린샷](media/HowToHLI/HASetupWithStonith/Persistent.png)

### <a name="scenario-6-node2-cant-join-the-cluster"></a>시나리오 6: Node2가 클러스터에 조인할 수 없습니다.

*ha-cluster-join* 명령을 통해 node2를 기존 클러스터에 조인하는 데 문제가 있는 경우 다음 오류가 나타납니다.

```
ERROR: Can’t retrieve SSH keys from <Primary Node>
```

![특정 I P 주소에서 SS H 키를 검색할 수 없다는 오류 메시지가 포함된 콘솔 창을 보여 주는 스크린샷.](media/HowToHLI/HASetupWithStonith/ha-cluster-join-error.png)

이 문제를 해결하려면 다음을 수행합니다.

1. 두 노드 모두에서 다음 명령을 *실행합니다.*

    ```
    ssh-keygen -q -f /root/.ssh/id_rsa -C 'Cluster Internal' -N ''
    cat /root/.ssh/id_rsa.pub >> /root/.ssh/authorized_keys
    ```

    ![첫 번째 노드에서 명령을 실행하는 콘솔 창의 일부를 보여 주는 스크린샷](media/HowToHLI/HASetupWithStonith/ssh-keygen-node1.PNG)

    ![두 번째 노드에서 명령을 실행하는 콘솔 창의 일부를 보여 주는 스크린샷](media/HowToHLI/HASetupWithStonith/ssh-keygen-node2.PNG)

2. node2가 클러스터에 추가되어 있는지 확인합니다.

    ![성공적인 조인 명령이 있는 콘솔 창을 보여 주는 스크린샷.](media/HowToHLI/HASetupWithStonith/ha-cluster-join-fix.png)

## <a name="next-steps"></a>다음 단계

다음 문서에서 SUSE HA 설정에 관한 추가 정보를 찾을 수 있습니다. 

- [SAP HANA SR 성능 최적화](https://www.suse.com/support/kb/doc/?id=000019450) 시나리오(SUSE 웹 사이트)
- [펜싱 및 STONITH(SUSE](https://documentation.suse.com/sle-ha/15-SP1/html/SLE-HA-all/cha-ha-fencing.html) 웹 사이트)
- [SAP HANA Pacemaker 클러스터를 사용하기 위한 준비 - 1부: 기본 사항(SAP](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-1-basics/) 블로그)
- [SAP HANA Pacemaker 클러스터 사용 준비 - 2부: 두 노드 모두 실패(SAP](https://blogs.sap.com/2017/11/19/be-prepared-for-using-pacemaker-cluster-for-sap-hana-part-2-failure-of-both-nodes/) 블로그)

운영 체제에 대한 파일 수준 백업 및 복원을 수행하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [OS 백업 및 복원](large-instance-os-backup.md)

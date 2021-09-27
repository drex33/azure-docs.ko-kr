---
title: SAP HANA on Azure(대형 인스턴스)에 대한 운영 체제 업그레이드 | Microsoft Docs
description: Azure의 SAP HANA(대규모 인스턴스)에 대한 운영 체제 업그레이드를 수행하는 방법을 알아봅니다.
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
ms.date: 06/24/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4e980417c2c48455e43143fb2f870a63d6395f94
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128546209"
---
# <a name="operating-system-upgrade"></a>운영 체제 업그레이드
이 문서에서는 BareMetal Infrastructure라고도 하는 HLI(HANA(대규모 인스턴스))에서 OS(운영 체제) 업그레이드에 관해 자세히 설명합니다.

>[!NOTE]
>OS 업그레이드는 사용자의 책임입니다. Microsoft Operations 지원이 업그레이드의 주요 영역에서 안내를 제공할 수 있지만 업그레이드를 계획할 때는 운영 체제 공급업체에도 문의하세요.

HLI 프로비저닝 중에 Microsoft Operations 팀은 운영 체제를 설치합니다.
사용자가 운영 체제를 유지 관리해야 합니다. 예를 들어, 사용자는 HLI에서 패치, 튜닝, 업그레이드 등을 수행해야 합니다. 운영 체제를 대폭 변경하기 전에(예: SP1을 SP2로 업그레이드) 지원 티켓을 열어 Microsoft Operations 팀에 문의하세요. 사용자에 게 문의 합니다. 업그레이드하기 1주 이상 전에 이 티켓을 여는 것이 좋습니다. 

티켓에 포함된 내용은 다음과 같습니다.

* HLI 구독 ID
* 서버 이름
* 적용하려는 패치 수준
* 변경하려는 날짜 

Linux 버전이 다른 여러 SAP HANA 버전에 대한 지원 매트릭스는 [SAP Note #2235581](https://launchpad.support.sap.com/#/notes/2235581)을 참조하세요.

## <a name="known-issues"></a>알려진 문제

업그레이드에는 몇 가지 알려진 문제가 있습니다.
- SKU Type II 클래스 SKU에서 SFS(Software Foundation Server)는 OS 업그레이드 중에 제거됩니다. OS 업그레이드가 완료된 후 호환되는 SFS를 다시 설치해야 합니다.
- 이더넷 카드 드라이버(ENIC 및 FNIC)가 이전 버전으로 롤백되었습니다. 업그레이드 후 호환되는 버전의 드라이버를 다시 설치해야 합니다.

## <a name="sap-hana-large-instance-type-i-recommended-configuration"></a>SAP HANA Large Instance(Type I) 권장 구성

OS 구성은 시간이 지남에 따라 권장 설정에서 드리프트될 수 있습니다. 이 드리프트는 패치, 시스템 업그레이드 및 기타 변경 작업으로 인해 발생할 수 있습니다. Microsoft는 HANA(대규모 인스턴스)가 최고의 성능과 복원력을 발휘하도록 최적으로 구성되어 있는지 확인하는 데 필요한 업데이트를 식별합니다. 다음 지침에서는 네트워크 성능, 시스템 안정성 및 최적의 HANA 성능을 다루는 권장 사항을 간략하게 설명합니다.

### <a name="compatible-enicfnic-driver-versions"></a>호환되는 eNIC/fNIC 드라이버 버전
  네트워크 성능과 시스템 안정성을 적절하게 유지하려면 다음 호환성 표에 따라 적절한 OS 관련 버전의 eNIC 및 fNIC 드라이버가 설치되어 있는지 확인합니다. 서버는 호환되는 버전이 있는 고객에게 제공됩니다. 그러나 OS/커널 패치 중에 드라이버가 기본 버전으로 롤백될 수 있습니다. 적절한 드라이버 버전이 OS/커널 패치 후 작업을 실행 중인지 확인합니다.
       
      
  |  OS 공급업체    |  OS 패키지 버전     |  펌웨어 버전  |  eNIC 드라이버 |  fNIC 드라이버 | 
  |---------------|-------------------------|--------------------|--------------|--------------|
  |   SuSE        |  SLES 12 SP2            |   3.1.3h           |  2.3.0.40    |   1.6.0.34   |
  |   SuSE        |  SLES 12 SP3            |   3.1.3h           |  2.3.0.44    |   1.6.0.36   |
  |   SuSE        |  SLES 12 SP2            |   3.2.3i           |  2.3.0.45    |   1.6.0.37   |
  |   SuSE        |  SLES 12 SP3            |   3.2.3i           |  2.3.0.43    |   1.6.0.36   |
  |   SuSE        |  SLES 12 SP4            |   3.2.3i           |  4.0.0.6     |   2.0.0.60   |  
  |   SuSE        |  SLES 12 SP4            |   4.1.1b           |  4.0.0.6     |   2.0.0.60   |  
  |   SuSE        |  SLES 12 SP5            |   3.2.3i           |  4.0.0.8     |   2.0.0.60   |
  |   SuSE        |  SLES 12 SP5            |   4.1.1b           |  4.0.0.6     |   2.0.0.59   |
  |   SuSE        |  SLES 15 SP1            |   4.1.1b           |  4.0.0.8     |   2.0.0.60   |
  |   Red Hat     |  RHEL 7.2               |   3.1.3h           |  2.3.0.39    |   1.6.0.34   |
  |   Red Hat     |  RHEL 7.6               |   3.2.3i           |  3.1.137.5   |   2.0.0.50   |
  |   Red Hat     |  RHEL 7.6               |   4.1.1b           |  4.0.0.8     |   2.0.0.60   |
 

### <a name="commands-for-driver-upgrade-and-to-clean-old-rpm-packages"></a>드라이버를 업그레이드하고 이전 rpm 패키지를 삭제하는 명령

#### <a name="command-to-check-existing-installed-drivers"></a>기존에 설치된 드라이버를 확인하는 명령
```
rpm -qa | grep enic/fnic 
```
#### <a name="delete-existing-enicfnic-rpm"></a>기존 eNIC/fNIC rpm 삭제
```
rpm -e <old-rpm-package>
```
#### <a name="install-recommended-enicfnic-driver-packages"></a>권장되는 eNIC/fNIC 드라이버 패키지 설치
```
rpm -ivh <enic/fnic.rpm> 
```

#### <a name="commands-to-confirm-installation"></a>설치를 확인하는 명령
```
modinfo enic
modinfo fnic
```

#### <a name="steps-for-enicfnic-drivers-installation-during-os-upgrade"></a>OS 업그레이드 중 eNIC/fNIC 드라이버 설치 단계

* OS 버전 업그레이드
* 이전 rpm 패키지 제거
* 설치된 OS 버전에 따라 호환되는 eNIC/fNIC 드라이버 설치
* 시스템 재부팅
* 재부팅 후 eNIC/fNIC 버전 확인


### <a name="suse-hlis-grub-update-failure"></a>SuSE HLIs GRUB 업데이트 실패
SAP on Azure HANA Large Instances(Type I)는 업그레이드 후 부팅할 수 없는 상태가 될 수 있습니다. 다음 절차에서 이 문제를 해결합니다.

#### <a name="execution-steps"></a>실행 단계

-   `multipath -ll` 명령을 실행합니다.
-   LUN(논리 단위 번호) ID를 가져오거나 `fdisk -l | grep mapper` 명령을 사용합니다.
-   `/etc/default/grub_installdevice` 줄을 사용하여 `/dev/mapper/<LUN ID>` 파일을 업데이트합니다. 예: /dev/mapper/3600a09803830372f483f495242534a56

>[!NOTE]
>LUN ID는 서버마다 다릅니다.


### <a name="disable-error-detection-and-correction"></a>오류 검색 및 수정 사용 안 함 
   EDAC(오류 검색 및 수정) 모듈은 메모리 오류를 검색하고 수정하는 데 도움이 됩니다. 그러나 기본 HLI Type I 하드웨어는 이미 메모리 오류를 검색하고 수정합니다. 하드웨어 및 OS 수준에서 동일한 기능을 사용하도록 설정하면 충돌이 발생하고 예기치 않은 서버 종료를 초래할 수 있습니다. OS에서 EDAC 모듈을 사용하지 않도록 설정하는 것이 좋습니다.

#### <a name="execution-steps"></a>실행 단계

- EDAC 모듈을 사용할 수 있는지 확인합니다. 다음 명령에서 출력이 반환되면 모듈이 사용하도록 설정됩니다.

```
lsmod | grep -i edac 
```
- 파일에 `/etc/modprobe.d/blacklist.conf` 줄을 추가하여 모듈을 사용하지 않도록 설정합니다.
```
blacklist sb_edac
blacklist edac_core
```
변경 내용을 적용하려면 다시 부팅해야 합니다. 다시 부팅한 후 `lsmod` 명령을 다시 실행하고 모듈이 사용되지 않는지 확인합니다.

### <a name="kernel-parameters"></a>커널 매개 변수
`transparent_hugepage`, `numa_balancing`, `processor.max_cstate`, `ignore_ce` 및 `intel_idle.max_cstate`에 대한 올바른 설정이 적용되었는지 확인합니다.

* intel_idle.max_cstate=1
* processor.max_cstate=1
* transparent_hugepage=never
* numa_balancing=disable
* mce=ignore_ce

#### <a name="execution-steps"></a>실행 단계

- `/etc/default/grub` 파일의 `GRB_CMDLINE_LINUX` 줄에 다음 매개 변수를 추가합니다.

```
intel_idle.max_cstate=1 processor.max_cstate=1 transparent_hugepage=never numa_balancing=disable mce=ignore_ce
```
- 새 grub 파일을 만듭니다.
```
grub2-mkconfig -o /boot/grub2/grub.cfg
```
- 시스템을 다시 부팅합니다.

## <a name="next-steps"></a>다음 단계

SUSE Linux용 SMT 서버를 설정하는 방법을 알아봅니다.

> [!div class="nextstepaction"]
> [SUSE Linux용 SMT 서버 설정](hana-setup-smt.md)
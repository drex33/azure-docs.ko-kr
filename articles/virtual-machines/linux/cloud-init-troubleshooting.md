---
title: cloud-init를 사용하여 문제 해결
description: cloud-init를 사용하여 Azure VM 프로비전 문제를 해결합니다.
author: danielsollondon
ms.service: virtual-machines
ms.topic: troubleshooting
ms.date: 07/06/2020
ms.author: danis
ms.reviewer: cynthn
ms.subservice: cloud-init
ms.openlocfilehash: b9a113c4c7f54bfb47d10795325f163cd6f35429
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122687930"
---
# <a name="troubleshooting-vm-provisioning-with-cloud-init"></a>cloud-init를 사용하여 VM 프로비전 문제 해결

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: 유연한 확장 집합 

cloud-init를 사용하여 프로비전을 수행하는 일반화된 사용자 지정 이미지를 만들었으나 VM이 올바르게 생성되지 않은 것을 발견한 경우 사용자 지정 이미지 문제를 해결해야 합니다.

프로비전과 관련된 문제의 예는 다음과 같습니다.
- VM이 40분 동안 '만들기' 상태로 중단되어 있으며 VM 만들기는 실패로 표시됨
- `CustomData`가 처리되지 않음
- 임시 디스크를 탑재 실패
- 사용자가 만들어지지 않았거나 사용자 액세스 문제가 있음
- 네트워킹이 올바르게 설정되지 않음
- 스왑 파일 또는 파티션 오류

이 문서에서는 cloud-init 문제를 해결하는 방법을 단계별로 설명합니다. 자세한 내용은 [cloud-init 심층 분석](./cloud-init-deep-dive.md)을 참조하세요.

## <a name="step-1-test-the-deployment-without-customdata"></a>1단계: `customData` 없이 배포 테스트

VM을 만들 때 cloud-init는 `customData`를 수락하고 여기로 전달합니다. 먼저 배포에 문제가 발생하지 않는지 확인해야 합니다. 구성을 전달하지 않고 VM을 프로비전해 보세요. VM을 프로비전하지 못할 경우 아래 단계를 계속 진행합니다. 전달하는 구성이 적용되지 않으면 [4단계]()로 이동합니다. 

## <a name="step-2-review-image-requirements"></a>2단계: 이미지 요구 사항 검토
VM 프로비전 실패의 주요 원인은 OS 이미지를 Azure에서 실행하기 위한 사전 요구 사항을 충족하지 않는다는 것입니다. Azure에 프로비전하기 전에 이미지가 제대로 준비되었는지 확인합니다. 


다음 문서에서는 Azure에서 지원되는 다양한 linux 배포를 준비하는 단계를 보여 줍니다.

- [CentOS 기반 배포](create-upload-centos.md)
- [Debian Linux](debian-create-upload-vhd.md)
- [Flatcar 컨테이너 Linux](flatcar-create-upload-vhd.md)
- [Oracle Linux](oracle-create-upload-vhd.md)
- [Red Hat Enterprise Linux](redhat-create-upload-vhd.md)
- [SLES 및 openSUSE](suse-create-upload-vhd.md)
- [Ubuntu](create-upload-ubuntu.md)
- [기타: 보증되지 않는 배포](create-upload-generic.md)

[지원되는 Azure cloud-init 이미지](./using-cloud-init.md)의 경우 Linux 배포에는 이미 Azure에서 이미지를 올바르게 프로비전하는 데 필요한 모든 패키지 및 구성이 있습니다. 사용자 고유의 큐레이팅된 이미지에서 VM을 만들 수 없는 경우 옵션 `customData`를 통해 cloud-init에 대해 사전에 구성된 Azure Marketplace 이미지를 사용해 보세요. `customData`가 Azure Marketplace 이미지를 사용했을 때 제대로 작동하는 경우 큐레이팅된 이미지에 문제가 있을 수 있습니다.

## <a name="step-3-collect--review-vm-logs"></a>3단계: VM 로그 수집 및 검토

VM 프로비저닝에 실패하면 Azure는 20분 동안 '만들기' 상태를 표시한 다음 VM을 다시 부팅하고, 마지막으로 VM 배포를 실패로 표시할 때까지 20분 동안 기다린 후 마지막으로 `OSProvisioningTimedOut` 오류로 표시합니다.

VM이 실행되는 동안 VM의 로그가 있어야 프로비전이 실패한 이유를 파악할 수 있습니다.  VM을 프로비전하지 못한 이유를 파악하려면 VM을 중지하지 마십시오. VM이 계속 실행되도록 합니다. 로그를 수집하려면 실패한 VM을 실행 중 상태로 두어야 합니다. 로그를 수집하려면 다음 방법 중 하나를 사용합니다.

- [직렬 콘솔](/troubleshoot/azure/virtual-machines/serial-console-grub-single-user-mode)

- VM을 만들기 전에 [부팅 진단을 사용하도록 설정](/previous-versions/azure/virtual-machines/linux/tutorial-monitor#enable-boot-diagnostics)하고 부팅하는 동안 이를 [확인](/previous-versions/azure/virtual-machines/linux/tutorial-monitor#view-boot-diagnostics)합니다.

- [AZ VM Repair를 실행](/troubleshoot/azure/virtual-machines/repair-linux-vm-using-azure-virtual-machine-repair-commands)하여 OS 디스크를 연결하고 탑재합니다. 그러면 다음 로그를 수집할 수 있습니다.
```bash
/var/log/cloud-init*
/var/log/waagent*
/var/log/syslog*
/var/log/rsyslog*
/var/log/messages*
/var/log/kern*
/var/log/dmesg*
/var/log/boot*
```
초기 문제 해결을 시작하려면 cloud-init 로그를 시작하고 오류가 발생한 위치를 파악한 다음, 다른 로그를 사용하여 심층 분석을 수행하고 추가 인사이트를 제공합니다. 
* /var/log/cloud-init.log
* /var/log/cloud-init-output.log
* 직렬/부팅 로그

모든 로그에서 "Failed", "WARNING", "WARN", "err", "error", "ERROR"를 검색합니다. 대/소문자 구분 검색을 무시하도록 구성을 설정하는 것이 좋습니다. 

> [!TIP]
> 사용자 지정 이미지 문제를 해결하는 경우 이미지 중에 사용자를 추가하는 것을 고려해야 합니다. 프로비저닝을 통해 관리 사용자를 설정하지 못한 경우에도 OS에 로그인할 수 있습니다.

## <a name="analyzing-the-logs"></a>로그 분석

각 cloud-init 로그에서 찾을 내용에 대한 자세한 내용은 다음과 같습니다.

### <a name="varlogcloud-initlog"></a>/var/log/cloud-init.log

기본적으로 우선 순위가 debug 이상인 모든 cloud-init 이벤트는 `/var/log/cloud-init.log`에 기록됩니다. 기록된 이벤트는 cloud-init를 초기화하는 동안 발생한 모든 이벤트에 대해 자세한 로그를 제공합니다. 

예를 들면 다음과 같습니다.

```console
2019-10-10 04:51:25,321 - util.py[DEBUG]: Failed mount of '/dev/sr0' as 'auto': Unexpected error while running command.
Command: ['mount', '-o', 'ro,sync', '-t', 'auto', u'/dev/sr0', '/run/cloud-init/tmp/tmpLIrklc']
Exit code: 32
Reason: -
Stdout:
Stderr: mount: unknown filesystem type 'udf'
2020-01-31 00:21:53,352 - DataSourceAzure.py[WARNING]: /dev/sr0 was not mountable
```


오류나 경고를 발견한 후에는 cloud-init 로그에서 역방향으로 읽어 오류나 경고가 발생하기 전에 cloud-init에서 어떤 작업을 시도했는지 파악합니다. 대부분의 경우 cloud-init은 오류가 발생하기 전에 OS 명령을 실행하거나 프로비전 작업을 수행합니다. 이를 통해 로그에서 오류가 발생한 이유의 인사이트를 제공할 수 있습니다. 다음 예제에서는 오류가 발생하기 직전에 cloud-init에서 디바이스를 탑재하려 했음을 보여 줍니다.

```output
2019-10-10 04:51:24,010 - util.py[DEBUG]: Running command ['mount', '-o', 'ro,sync', '-t', 'auto', u'/dev/sr0', '/run/cloud-init/tmp/tmpXXXXX'] with allowed return codes [0] (shell=False, capture=True)
```

[직렬 콘솔](/troubleshoot/azure/virtual-machines/serial-console-grub-single-user-mode)에 대한 액세스 권한이 있는 경우 cloud-init에서 실행하려 한 명령을 다시 실행할 수 있습니다.

/etc/cloud/cloud.cfg.d/05_logging.cfg 내에서 `/var/log/cloud-init.log`에 대한 로깅을 다시 구성할 수도 있습니다. cloud-init 로깅에 대한 자세한 내용은 [cloud-init 설명서](https://cloudinit.readthedocs.io/en/latest/topics/logging.html)를 참조하세요. 

### <a name="varlogcloud-init-outputlog"></a>/var/log/cloud-init-output.log

[cloud-init 단계](cloud-init-deep-dive.md) 중에 `stdout`과 `stderr`에서 정보를 가져올 수 있습니다. 여기에는 일반적으로 각 단계의 타임스탬프를 비롯하여 cloud-init의 각 단계에 대한 `stdout` 및 `stderr`와 라우팅 테이블 정보, 네트워킹 정보, SSH 호스트 키 확인 정보 등이 포함됩니다. 원하는 경우 `stderr` 및 `stdout` 로깅을 `/etc/cloud/cloud.cfg.d/05_logging.cfg`에서 다시 구성할 수 있습니다.

### <a name="serialboot-logs"></a>직렬/부팅 로그 

cloud-init에는 여러 종속성이 있습니다. 이러한 종속성은 네트워킹, 스토리지, ISO 탑재 기능, 임시 디스크 탑재 및 포맷 등 Azure의 이미지에 대한 사전 요구 사항에 설명되어 있습니다. 이러한 방법 중 하나를 실행하면 오류가 발생하여 cloud-init이 실패할 수 있습니다. 예를 들어 VM이 DHCP 임대를 가져올 수 없는 경우 cloud-init이 실패합니다.

여전히 cloud-init을 프로비전하지 못한 이유를 구분할 수 없는 경우 cloud-init 단계가 무엇인지 및 모듈을 언제 실행하는지 이해해야 합니다. 자세한 내용은 [cloud-init에 대해 자세히 알아보기](cloud-init-deep-dive.md)를 참조하세요.


## <a name="step-4-investigate-why-the-configuration-isnt-being-applied"></a>4단계: 구성이 적용되지 않는 이유 조사
모든 cloud-init 오류가 심각한 프로비전 오류로 이어지지는 않습니다. 예를 들어 cloud-init 구성에서 `runcmd` 모듈을 사용하는 경우 실행중인 명령에서 0이 아닌 종료 코드가 발생하면 VM 프로비전이 실패합니다. 이는 종료 코드가 cloud-init의 처음 3단계에서 발생하는 코어 프로비전 기능 후에 실행되기 때문입니다. 구성이 적용되지 않는 이유를 해결하려면 3단계에서 로그를 검토하고 cloud-init 모듈을 수동으로 검토합니다. 예를 들면 다음과 같습니다.

- `runcmd` -스크립트가 오류 없이 실행되나요? 터미널에서 구성을 수동으로 실행하여 예상과 같이 실행되는지 확인합니다.
- 패키지 설치 중 - VM이 패키지 리포지토리에 액세스할 수 있나요?
- VM에 제공된 `customData` 데이터 구성도 확인해야 합니다. 이는 `/var/lib/cloud/instances/<unique-instance-identifier>/user-data.txt`에 있습니다.


## <a name="next-steps"></a>다음 단계

여전히 cloud-init에서 구성을 실행하지 않은 이유를 구분할 수 없는 경우 각 cloud-init 단계에서 발생하는 작업과 모듈이 실행되는 시기를 더 자세히 확인해야 합니다. 자세한 내용은 [cloud-init 구성에 대해 자세히 알아보기](./cloud-init-deep-dive.md)를 참조하세요.
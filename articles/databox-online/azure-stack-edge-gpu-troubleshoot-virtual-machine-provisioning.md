---
title: Azure Stack Edge Pro GPU에서 가상 머신 프로비저닝 문제 해결 | Microsoft Docs
description: Azure Stack Edge Pro GPU에서 새 가상 머신을 프로비저닝할 때 발생하는 문제를 해결하는 방법을 설명합니다.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: troubleshooting
ms.date: 08/03/2021
ms.author: alkohli
ms.openlocfilehash: e04d3a0a5b7c48117af1f597606658a878a3a6ff
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536357"
---
# <a name="troubleshoot-vm-deployment-in-azure-stack-edge-pro-gpu"></a>Azure Stack Edge Pro GPU에서 VM 배포 문제 해결

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

이 문서에서는 Azure Stack Edge Pro GPU 디바이스에 가상 머신을 배포할 때 발생하는 일반적인 오류를 해결하는 방법을 설명합니다. 이 문서에서는 네트워크 인터페이스 및 VM을 만드는 동안 VM 프로비저닝 시간 제한과 문제를 일으키는 가장 일반적인 문제를 조사하기 위한 지침을 제공합니다.

VM 프로비저닝 실패를 진단하려면 실패한 가상 머신에 대한 게스트 로그를 검토합니다. VM 게스트 로그를 수집하고 지원 패키지에 포함하는 단계는 [Azure Stack Edge Pro에서 VM에 대한 게스트 로그 수집](azure-stack-edge-gpu-collect-virtual-machine-guest-logs.md)을 참조하세요.

VM 배포 전에 VM 이미지를 성공적으로 업로드하지 못하는 문제에 대한 지침은 [Azure Stack Edge Pro GPU에서 가상 머신 이미지 업로드 문제 해결](azure-stack-edge-gpu-troubleshoot-virtual-machine-image-upload.md)을 참조하세요.


## <a name="vm-provisioning-timeout"></a>VM 프로비저닝 시간 제한

이 섹션에서는 VM 프로비저닝 시간 제한의 가장 일반적인 원인에 대한 문제 해결을 제공합니다.

VM 프로비저닝 시간이 초과되면 다음과 같은 오류가 표시됩니다. 

![Azure Stack Edge VM 프로비저닝 시간이 초과될 때 Azure Portal에 표시되는 오류의 스크린샷.](./media/azure-stack-edge-gpu-troubleshoot-virtual-machine-provisioning/vm-provisioning-timeout-01.png) 

VM 프로비저닝 시간 제한의 가장 높은 원인은 다음과 같습니다.
- VM에 할당한 IP 주소가 이미 사용 중입니다. [자세한 정보](#vm-provisioning-timeout)
- VM을 배포하는 데 사용한 VM 이미지가 제대로 준비되지 않았습니다. [자세한 정보](#vm-image-not-prepared-correctly)
- 게스트 VM에서 기본 게이트웨이 및 DNS 서버에 연결할 수 없습니다. [자세한 정보](#gateway-dns-server-couldnt-be-reached-from-guest-vm)
- `cloud init`를 설치하는 동안 `cloud init`가 실행되지 않았거나 실행 중에 문제가 발생했습니다. (Linux VM에만 해당) [자세히 알아보기](#cloud-init-issues-linux-vms)
- 사용자 지정 VM 이미지를 사용하여 배포된 Linux VM의 경우 /etc/waagent.conf 파일의 프로비저닝 플래그가 올바르지 않습니다. (Linux VM에만 해당) [자세히 알아보기](#provisioning-flags-set-incorrectly-linux-vms)

### <a name="ip-assigned-to-the-vm-is-already-in-use"></a>VM에 할당된 IP가 이미 사용 중임

**오류 설명:** VM에 이미 사용 중인 고정 IP 주소가 할당되었으며 VM을 프로비저닝하지 못했습니다. 이 오류는 VM이 배포된 서브넷에서 IP 주소를 사용하는 경우에 발생합니다. Azure Portal을 통해 VM을 배포하는 경우 프로세스는 디바이스 내의 기존 IP 주소를 확인하지만 서브넷에 있을 수 있는 다른 서비스 또는 가상 머신의 IP 주소를 확인할 수 없습니다. 

**제안된 해결 방법:** 사용 중이 아닌 고정 IP 주소를 사용하거나 DHCP 서버에서 제공하는 동적 IP 주소를 사용합니다.

중복 IP 주소를 확인하려면 다음을 수행합니다.

- 동일한 네트워크의 모든 어플라이언스에서 다음 `ping` 및 Test-NetConnection(`tnc`) 명령을 실행합니다.

  ```
  ping <IP address>
  tnc <IP address>
  tnc <IP address> -CommonTCPPort “RDP”
  ```

응답이 수신되면 새 VM에 할당한 IP 주소는 이미 사용 중입니다.

### <a name="vm-image-not-prepared-correctly"></a>VM 이미지가 제대로 준비되지 않음

**오류 설명:** Azure Stack Edge Pro GPU 디바이스에서 사용할 VM 이미지를 준비하려면 특정 워크플로를 따라야 합니다. Azure에서 gen1 가상 머신을 만들고, VM을 사용자 지정하고, VHD를 일반화한 다음, 해당 가상 머신에 대한 OS VHD를 다운로드해야 합니다. 준비 이미지는 “vhd” 파일 이름 확장명 및 고정 형식이 포함된 gen1 VHD여야 합니다.

요구 사항에 대한 개요는 [Azure Stack Edge Pro GPU 디바이스에 대한 사용자 지정 VM 이미지 만들기](azure-stack-edge-gpu-create-virtual-machine-image.md)를 참조하세요. VM 이미지 문제를 해결하는 방법에 대한 지침은 [Azure Stack Edge Pro GPU에서 가상 머신 이미지 업로드 문제 해결](azure-stack-edge-gpu-troubleshoot-virtual-machine-image-upload.md)을 참조하세요.  

**제안된 해결 방법:** VM 이미지 준비를 위한 워크플로를 완료합니다. 지침은 다음 문서 중 하나를 참조하세요.

* [Windows 및 Linux VM에 대한 사용자 지정 VM 이미지 워크플로](azure-stack-edge-gpu-create-virtual-machine-image.md)
* [Windows VHD에서 범용 이미지 준비](azure-stack-edge-gpu-prepare-windows-vhd-generalized-image.md)
* [ISO를 사용하여 일반화된 이미지 준비](azure-stack-edge-gpu-prepare-windows-generalized-image-iso.md)
* [특수 이미지를 사용하여 VM 배포](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)


### <a name="gateway-dns-server-couldnt-be-reached-from-guest-vm"></a>게스트 VM에서 게이트웨이, DNS 서버에 연결할 수 없음

**오류 설명:** VM을 배포하는 동안 기본 게이트웨이 및 DNS 서버에 연결할 수 없는 경우 VM 프로비저닝 시간이 초과되고 VM 배포에 실패합니다.

**제안된 해결 방법:** VM에서 기본 게이트웨이 및 DNS 서버에 연결할 수 있는지 확인합니다. 그런 다음, VM 배포를 반복합니다.

VM에서 기본 게이트웨이 및 DNS 서버에 연결할 수 있는지 확인하려면 다음 단계를 수행합니다.
1. [VM에 연결합니다](azure-stack-edge-gpu-deploy-virtual-machine-portal.md#connect-to-a-vm).
2. 다음 명령을 실행합니다.

   ```
   ping <default gateway IP address>
   ping <DNS server IP address>
   ```

   기본 게이트웨이 및 DNS 서버에 대한 IP 주소를 확인하려면 디바이스에 대한 로컬 UI로 이동합니다. 원하는 포트를 선택하고 네트워크 설정을 확인합니다.

   ![포트 2에 대한 네트워크 설정이 표시된 Azure Stack Edge 디바이스의 네트워크 페이지 스크린샷.](./media/azure-stack-edge-gpu-troubleshoot-virtual-machine-provisioning/gateway-dns-server-settings-01.png) 


### <a name="cloud-init-issues-linux-vms"></a>`cloud init` 문제(Linux VM)

**오류 설명:** `cloud init`가 실행되지 않았거나 `cloud init`를 실행하는 동안 문제가 발생했습니다. `cloud-init`는 VM이 처음 부팅될 때 Linux VM을 사용자 지정하는 데 사용됩니다. 자세한 내용은 [Azure의 가상 머신에 대한 cloud-init 지원](../virtual-machines/linux/using-cloud-init.md)을 참조하세요.

**제안된 해결 방법:** `cloud init`가 실행될 때 발생한 문제를 찾으려면 다음을 수행합니다.
1. [VM에 연결합니다](azure-stack-edge-gpu-deploy-virtual-machine-portal.md#connect-to-a-vm).
1. 다음 로그 파일에서 `cloud init` 오류를 확인합니다.

   - /var/log/cloud-init-output.log
   - /var/log/cloud-init.log
   - /var/log/waagent/log 

`cloud init`가 성공적으로 실행되지 않는 가장 일반적인 문제를 확인하려면 다음 단계를 수행합니다.

1. VM 이미지가 `cloud init` 기반인지 확인합니다. 다음 명령을 실행합니다.

   `cloud-init --version`

   명령은 cloud init 버전 번호를 반환해야 합니다. 이미지가 `cloud init`를 기반으로 하지 않는 경우 명령은 버전 정보를 반환하지 않습니다.

   `cloud init` 옵션에 대한 도움말을 보려면 다음 명령을 실행합니다.

   `cloud-init --help` 

2. *Azure* 로 설정된 데이터 원본을 사용하여 `cloud init` 인스턴스를 성공적으로 실행할 수 있는지 확인합니다. 

   데이터 원본을 *Azure* 로 설정하면 *cloud init* 로그의 항목이 다음과 같이 표시됩니다.

   ![데이터 원본이 Azure로 설정된 VM 이미지에 대한 cloud-init 로그 항목의 그림입니다. 식별 텍스트가 강조 표시됩니다.](./media/azure-stack-edge-gpu-troubleshoot-virtual-machine-provisioning/cloud-init-log-entry-01.png)

   데이터 원본을 Azure로 설정하지 않은 경우 `cloud init` 스크립트를 수정해야 할 수 있습니다. 자세한 내용은 [cloud-init에 대해 자세히 알아보기](../virtual-machines/linux/cloud-init-deep-dive.md)를 참조하세요.


### <a name="provisioning-flags-set-incorrectly-linux-vms"></a>프로비저닝 플래그가 잘못 설정됨(Linux VM)

**오류 설명:** Azure에서 Linux VM을 성공적으로 배포하려면 이미지에서 프로비저닝을 사용하지 않도록 설정하고 `cloud init`를 사용한 프로비저닝을 사용하도록 설정해야 합니다. 이러한 값을 설정한 프로비저닝 플래그는 표준 VM 이미지에 대해 올바르게 구성됩니다. 사용자 지정 VM 이미지를 사용하는 경우 올바른지 확인해야 합니다. 

**제안된 해결 방법:** */etc/waagent.conf* 파일의 프로비저닝 플래그 값이 다음과 같은지 확인합니다.<!--Move details to "Create a custom VM image" when the 2 active PRs against that article have been merged. Not before Friday release.-->

   | 기능                      | 필수 값                |
   |---------------------------------|-------------------------------|
   | 프로비저닝 사용             | `Provisioning.Enabled=n`      |
   | cloud-init를 사용하여 프로비전 | `Provisioning.UseCloudInit=y` |


##  <a name="network-interface-creation-issues"></a>네트워크 인터페이스 만들기 문제

이 섹션에서는 VM을 배포하는 동안 네트워크 인터페이스를 만드는 데 실패하는 문제에 대한 지침을 제공합니다.


### <a name="nic-creation-timeout"></a>NIC 만들기 시간 제한

**오류 설명:** VM에서 네트워크 인터페이스 만들기가 허용되는 제한 시간 내에 완료되지 않았습니다. 이 오류는 사용자 환경의 DHCP 서버 문제로 인해 발생할 수 있습니다. 

네트워크 인터페이스가 성공적으로 만들어졌는지 여부를 확인하려면 다음 단계를 수행합니다.

1. Azure Portal에서 내 디바이스의 Azure Stack Edge 리소스로 이동합니다(**Edge Services** > **가상 머신** 으로 이동). 그런 다음, **배포** 를 선택하고 VM 배포로 이동합니다. 

1. 네트워크 인터페이스가 성공적으로 만들어지지 않은 경우 다음과 같은 오류가 표시됩니다.

   ![Azure Stack Edge 디바이스에서 VM을 배포하는 동안 네트워크 인터페이스를 만들 수 없는 경우 Azure Portal에 표시되는 오류의 스크린샷.](./media/azure-stack-edge-gpu-troubleshoot-virtual-machine-provisioning/nic-creation-failed-01.png)

**제안된 해결 방법:** VM을 다시 만들고 고정 IP 주소를 할당합니다.


## <a name="vm-creation-issues"></a>VM 만들기 문제

이 섹션에서는 VM을 만드는 동안 발생하는 일반적인 문제에 대해 설명합니다.

### <a name="not-enough-memory-to-create-the-vm"></a>VM을 만들기 위한 메모리가 부족함

**오류 설명:** 메모리가 부족하여 VM을 만들 수 없는 경우 다음 오류가 표시됩니다.
 
![Azure Stack Edge 디바이스에서 VM을 만들 수 없는 경우 Azure Portal에 표시되는 오류의 스크린샷.](./media/azure-stack-edge-gpu-troubleshoot-virtual-machine-provisioning/vm-creation-failed-01.png)

**제안된 해결 방법:** 디바이스에서 사용 가능한 메모리를 확인하고 적절하게 VM 크기를 선택합니다. 자세한 내용은 [Azure Stack Edge에서 지원되는 가상 머신 크기](azure-stack-edge-gpu-virtual-machine-sizes.md)를 참조하세요.

VM을 배포하는 데 사용할 수 있는 메모리는 다음과 같은 몇 가지 요인에 의해 제한됩니다.

- 디바이스에서 사용 가능한 총 메모리입니다. 자세한 내용은 [Azure Stack Edge Pro GPU 기술 사양](azure-stack-edge-gpu-technical-specifications-compliance.md#compute-and-memory-specifications) 및 [Azure Stack Edge Mini R 기술 사양](azure-stack-edge-mini-r-technical-specifications-compliance.md#compute-memory)의 컴퓨팅 및 메모리 사양을 참조하세요.

- Kubernetes를 사용하는 경우 Kubernetes 및 Kubernetes 클러스터의 앱에 필요한 컴퓨팅 메모리가 필요합니다.
- Hyper-V의 각 가상 머신에 대한 오버헤드.

**제안된 해결 방법:**

- 더 작은 메모리를 필요로 하는 VM 크기를 사용합니다.
- 새 VM을 배포하기 전에 포털에서 사용하지 않는 모든 VM을 중지합니다.
- 더 이상 사용하지 않는 VM을 모두 삭제합니다.


### <a name="insufficient-number-of-gpus-to-create-gpu-vm"></a>GPU 수가 부족하여 GPU VM을 만들 수 없음

이미 Kubernetes를 사용하도록 설정된 GPU 디바이스에서 VM을 배포하려고 하면 GPU를 사용할 수 없으며 다음 오류가 발생하여 VM 프로비저닝에 실패합니다.

![Azure Stack Edge 디바이스에 사용 가능한 GPU가 없어서 GPU VM이 생성되지 않을 때 Azure Portal에 표시되는 오류 스크린샷.](./media/azure-stack-edge-gpu-troubleshoot-virtual-machine-provisioning/gpu-vm-creation-failed-01.png)

**가능한 원인:** VM을 만들기 전에 Kubernetes를 사용하도록 설정하면 Kubernetes는 사용 가능한 모든 GPU를 사용하므로 사용자가 GPU 크기 VM을 만들 수 없습니다. 사용 가능한 GPU 수 만큼 GPU 크기 VM을 여러 개 만들 수 있습니다. Azure Stack Edge 디바이스는 1 또는 2개의 GPU로 장착될 수 있습니다.

**제안된 해결 방법:** Kubernetes가 구성된 1-GPU 또는 2-GPU 디바이스의 VM 배포 옵션에 대해서는 [GPU VM 및 Kubernetes](azure-stack-edge-gpu-overview-gpu-virtual-machines.md#gpu-vms-and-kubernetes)를 참조하세요.


## <a name="next-steps"></a>다음 단계

- [실패한 VM에 대한 게스트 로그를 포함하는 지원 패키지 수집](azure-stack-edge-gpu-collect-virtual-machine-guest-logs.md)<!--Does a failed VM have a guest log? Does it have GPU and memory metrics?-->
- [실패한 GPU 확장 설치 관련 문제 해결](azure-stack-edge-gpu-collect-virtual-machine-guest-logs.md)
- [Azure Resource Manager로 오류 문제 해결](azure-stack-edge-gpu-troubleshoot-azure-resource-manager.md)

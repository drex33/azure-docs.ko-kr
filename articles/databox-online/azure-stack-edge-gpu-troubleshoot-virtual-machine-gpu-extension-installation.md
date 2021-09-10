---
title: Azure Stack Edge Pro GPU에서 GPU VM에 대한 GPU 확장 문제 해결
description: Azure Stack Edge Pro GPU에서 GPU VM에 대한 GPU 확장 설치 문제를 해결하는 방법을 설명합니다.
services: databox
author: v-dalc
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/02/2021
ms.author: alkohli
ms.openlocfilehash: a2e6996bd2b86da470b364da1349248e26b2fa58
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536722"
---
# <a name="troubleshoot-gpu-extension-issues-for-gpu-vms-on-azure-stack-edge-pro-gpu"></a>Azure Stack Edge Pro GPU에서 GPU VM에 대한 GPU 확장 문제 해결

[!INCLUDE [applies-to-gpu-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

이 문서에서는 Azure Stack Edge Pro GPU 디바이스에서 GPU VM의 GPU 확장 설치가 실패하는 가장 일반적인 문제를 해결하기 위한 지침을 제공합니다.

설치 단계는 [GPU 확장 설치](./azure-stack-edge-gpu-deploy-virtual-machine-install-gpu-extension.md?tabs=linux)를 참조하세요.

## <a name="vm-size-is-not-gpu-vm-size"></a>VM 크기가 GPU VM 크기가 아닙니다.

**오류 설명:** GPU VM은 Standard_NC4as_T4_v3 또는 Standard_NC8as_T4_v3 크기여야 합니다. 다른 VM 크기를 사용하는 경우 GPU 확장이 연결되지 않습니다.

**권장 솔루션:** Standard_NC4as_T4_v3 또는 Standard_NC8as_T4_v3 VM 크기로 VM을 만듭니다. 자세한 내용은 [GPU VM에 지원되는 VM 크기](azure-stack-edge-gpu-virtual-machine-sizes.md#ncast4_v3-series-preview)를 참조하세요. 크기 지정에 대한 자세한 내용은 [GPU VM 만들기](./azure-stack-edge-gpu-deploy-gpu-virtual-machine.md#create-gpu-vms)를 참조하세요.


## <a name="image-os-is-not-supported"></a>이미지 OS는 지원되지 않습니다.

**오류 설명:** GPU 확장은 VM 이미지에 설치된 운영 체제를 지원하지 않습니다. 

**권장 솔루션:** GPU 확장이 지원하는 운영 체제가 있는 새 VM 이미지를 준비합니다. 

* 지원되는 운영체제 목록은 [GPU VM에 지원되는 OS 및 GPU 드라이버](./azure-stack-edge-gpu-overview-gpu-virtual-machines.md#supported-os-and-gpu-drivers)를 참조하세요.

* GPU VM에 대한 이미지 준비 요구 사항은 [GPU VM 만들기](./azure-stack-edge-gpu-deploy-gpu-virtual-machine.md#create-gpu-vms)를 참조하세요.


## <a name="extension-parameter-is-incorrect"></a>확장 매개 변수가 잘못되었습니다.

**오류 설명:** Linux VM에 GPU 확장을 배포할 때 잘못된 확장 설정이 사용되었습니다. 

**권장 솔루션:** GPU 확장을 배포하기 전에 매개 변수 파일을 편집합니다. 자세한 내용은 [GPU 확장 설치](./azure-stack-edge-gpu-deploy-virtual-machine-install-gpu-extension.md?tabs=linux)를 참조하세요.


## <a name="vm-extension-installation-failed-in-downloading-package"></a>패키지를 다운로드할 때 VM 확장 설치 실패

**오류 설명:** 확장 설치 중 또는 사용 상태에서 확장 프로비저닝에 실패했습니다.

1. 게스트 로그에서 관련 오류를 확인합니다. 게스트 로그를 수집하려면 [Azure Stack Edge Pro에서 VM에 대한 게스트 로그 수집](azure-stack-edge-gpu-collect-virtual-machine-guest-logs.md)을 참조하세요.

   Linux VM에서:
   * `/var/log/waagent.log` 또는 `/var/log/azure/nvidia-vmext-status`에서 찾습니다.

   Windows VM에서
   * `C:\Packages\Plugins\Microsoft.HpcCompute.NvidiaGpuDriverWindows\1.3.0.0\Status`에서 오류 상태를 찾으세요.
   * 전체 실행 로그를 검토합니다. `C:\WindowsAzure\Logs\WaAppAgent.txt`.

   패키지를 다운로드하는 동안 설치에 실패한 경우 해당 오류는 VM이 공용 네트워크에 액세스하여 드라이버를 다운로드할 수 없다는 것을 나타냅니다.

**추천 솔루션:**

1.  인터넷에 연결된 포트에서 컴퓨팅을 사용하도록 설정합니다. 지침은 [GPU VM 만들기](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md#create-gpu-vms)를 참조하세요.

1.  포털에서 VM을 중지하여 VM 할당을 중단합니다. VM을 중지하려면 **가상 머신** > **개요** 로 이동하여 VM을 선택합니다. 그런 다음 VM 속성 페이지에서 **중지** 를 선택합니다.<!--Follow-up (formatting): Create an include file for stopping a VM. Use it here and in prerequisites for "Use the Azure portal to manage network interfaces on the VMs" (https://docs.microsoft.com/azure/databox-online/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal#prerequisites).-->
 
1.  새 VM을 만듭니다.


## <a name="vm-extension-failed-with-error-dpkg-is-usedyum-lock-is-used-linux-vm"></a>`dpkg is used/yum lock is used` 오류로 인해 VM 확장이 실패했습니다(Linux VM).

**오류 설명:** 다른 프로세스가 `dpkg`를 사용 중이거나 다른 프로세스가 `yum lock`을 생성했기 때문에 Linux VM에서 GPU 확장을 배포하지 못했습니다. 

**제안된 솔루션:** 문제를 해결하려면 다음 단계를 수행합니다.

1.  잠금을 적용하는 프로세스를 확인하려면 \var\log\azure\nvidia-vmext-status 로그에서 "dpkg가 다른 프로세스에서 사용됨" 또는 "다른 앱이 `yum lock` 잠금을 보유하고 있음"과 같은 오류를 검색합니다.

1. 프로세스가 완료되기를 기다리거나 프로세스를 종료합니다.

1.  다시 [GPU 확장을 설치](./azure-stack-edge-gpu-deploy-virtual-machine-install-gpu-extension.md?tabs=linux)합니다.

1.  확장 배포가 다시 실패하면 새 VM을 만들고 GPU 확장을 설치하기 전에 잠금이 없는지 확인합니다.


## <a name="next-steps"></a>다음 단계

[게스트 로그 수집 및 지원 패키지 만들기](azure-stack-edge-gpu-collect-virtual-machine-guest-logs.md)

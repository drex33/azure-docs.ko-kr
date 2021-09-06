---
title: Azure Stack Edge Pro GPU 디바이스에 GPU VM 배포 개요
description: GPU를 사용하는 Azure Stack Edge Pro에서 GPU 가속 작업에 최적화된 가상 머신을 사용하는 방법을 설명합니다.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 07/13/2021
ms.author: alkohli
ms.openlocfilehash: d8660ace369fe0da0da384dceae6bff35594df74
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536359"
---
# <a name="gpu-virtual-machines-for-azure-stack-edge-pro-gpu-devices"></a>Azure Stack Edge Pro GPU 디바이스용 GPU 가상 머신

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

Azure Stack Edge Pro GPU 디바이스의 GPU 가속화 워크로드에는 GPU 가상 머신이 필요합니다. 이 문서는 지원되는 OS, GPU 드라이버 및 VM 크기를 포함하여 GPU VM에 대한 개요를 제공합니다. Kubernetes 클러스터에 사용되는 GPU VM에 대한 배포 옵션도 설명합니다.

## <a name="about-gpu-vms"></a>GPU VM 정보

Azure Stack Edge 디바이스에는 Nvidia의 Tesla T4 GPU 중 1개 또는 2개가 장착될 수 있습니다. 이러한 디바이스에 GPU 가속 VM 워크로드를 배포하려면 GPU 최적화 VM 크기를 사용합니다. 예를 들어, T4 GPU를 사용하는 추론 워크로드를 배포하려면 NC T4 v3 시리즈를 사용해야 합니다. 자세한 내용은 [NC T4 v3 시리즈 VM](../virtual-machines/nct4-v3-series.md)를 참조하세요.

Azure N 시리즈 VM의 GPU 기능을 최대한 활용하려면 Nvidia GPU 드라이버를 설치해야 합니다. Nvidia GPU 드라이버 확장은 Nvidia CUDA 또는 GRID 드라이버를 설치합니다. [템플릿을 사용하거나 Azure Portal을 통해 GPU 확장을 설치](#gpu-vm-deployment)할 수 있습니다.

VM 배포 후 [Azure Resource Manager 템플릿을 사용하여 확장을 설치 및 관리](azure-stack-edge-gpu-deploy-virtual-machine-install-gpu-extension.md)할 수 있습니다. Azure Portal에서 VM 배포 도중 또는 그 이후 GPU 확장을 설치합니다. 해당 지침은 [Azure Stack Edge 디바이스에서 GPU VM 배포](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md)를 참조하세요.

디바이스에서 Kubernetes 클러스터를 구성하는 경우 GPU VM을 배포하기 전에 [Kubernetes 클러스터에 대한 배포 고려 사항](#gpu-vms-and-kubernetes)을 검토해야 합니다.

## <a name="supported-os-and-gpu-drivers"></a>지원되는 OS 및 GPU 드라이버 

Windows 및 Linux용 Nvidia GPU 드라이버 확장은 다음 OS 버전을 지원합니다.

### <a name="supported-os-for-gpu-extension-for-windows"></a>Windows용 GPU 확장에 대해 지원되는 OS

이 확장은 다음 운영 체제(OS)를 지원합니다. 다른 버전은 작동할 수 있지만 Azure Stack Edge 디바이스에서 실행되는 GPU VM 내에서 테스트되지 않았습니다.

| 배포 | 버전 |
|---|---|
| Windows Server 2019 | 핵심 |
| Windows Server 2016 | 핵심 |

### <a name="supported-os-for-gpu-extension-for-linux"></a>Linux용 GPU 확장에 대해 지원되는 OS

이 확장은 특정 OS 버전의 드라이버 지원에 따라 다음 OS 배포판을 지원합니다. 다른 버전은 작동할 수 있지만 Azure Stack Edge 디바이스에서 실행되는 GPU VM 내에서 테스트되지 않았습니다.

| 배포 | 버전 |
|---|---|
| Ubuntu | 18.04 LTS |
| Red Hat Enterprise Linux | 7.4 |

## <a name="gpu-vm-deployment"></a>GPU VM 배포

Azure Portal을 통해 또는 Azure Resource Manager 템플릿을 사용하여 GPU VM을 배포할 수 있습니다. GPU 확장은 VM을 만든 후에 설치됩니다.<!--Wording still needs work!-->

- **포털:** Azure Portal에서 [VM을 만들 때 GPU 확장을 설치](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md#create-gpu-vms)하거나 [VM 배포 후]() 빠르게 설치할 수 있습니다.<!--Can they remove the GPU extension. Tomorrow, create a new GPU VM to test.-->

- **템플릿:** Azure Resource Manager 템플릿을 사용하여 [VM을 만들고](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md#install-gpu-extension-after-deployment), 이후 [GPU 확장을 설치](azure-stack-edge-gpu-deploy-virtual-machine-install-gpu-extension.md)합니다.


## <a name="gpu-vms-and-kubernetes"></a>GPU VM 및 Kubernetes

디바이스에 GPU VM을 배포하기 전에 디바이스에 Kubernetes가 구성된 경우 다음 고려 사항을 검토하세요.

#### <a name="for-1-gpu-device"></a>1-GPU 디바이스: 

- **GPU VM을 만들고 디바이스에서 Kubernetes 구성**: 이 시나리오에서는 GPU VM 만들기 및 Kubernetes 구성이 모두 성공합니다. 이 경우 Kubernetes는 GPU에 액세스할 수 없습니다.

- **디바이스에서 Kubernetes 구성 이후 GPU VM 만들기**: 이 시나리오에서 Kubernetes는 디바이스에서 GPU를 클레임하고 사용 가능한 GPU 리소스가 없으므로 VM 만들기는 실패합니다.

#### <a name="for-2-gpu-device"></a>2-GPU 디바이스

- **GPU VM을 만든 다음, 디바이스에서 Kubernetes 구성**: 이 시나리오에서는 만든 GPU VM이 디바이스에서 하나의 GPU를 클레임하고 Kubernetes 구성도 성공하고 남은 하나의 GPU를 클레임합니다. 

- **2개의 GPU VM을 만든 다음, 디바이스에서 Kubernetes 구성**: 이 시나리오에서는 2개의 GPU VM이 디바이스에서 2개의 GPU를 클레임하고 Kubernetes가 GPU 없이 성공적으로 구성됩니다. 

- **디바이스에서 Kubernetes 구성 후 GPU VM 만들기**: 이 시나리오에서 Kubernetes는 디바이스의 GPU를 모두 클레임하고 사용 가능한 GPU 리소스가 없으므로 VM 만들기가 실패합니다.

<!--Li indicated that this is fixed. If you have GPU VMs running on your device and Kubernetes is also configured, then anytime the VM is deallocated (when you stop or remove a VM using Stop-AzureRmVM or Remove-AzureRmVM), there is a risk that the Kubernetes cluster will claim all the GPUs available on the device. In such an instance, you will not be able to restart the GPU VMs deployed on your device or create GPU VMs. -->

## <a name="next-steps"></a>다음 단계
- [GPU VM을 배포](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md)하는 방법을 알아봅니다.
- 디바이스에서 실행 중인 GPU VM에서 [GPU 확장을 설치](azure-stack-edge-gpu-deploy-virtual-machine-install-gpu-extension.md)하는 방법을 알아봅니다.

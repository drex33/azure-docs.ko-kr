---
title: Azure 가상 머신 확장 및 기능
description: Azure VM 확장에 대한 자세한 정보
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.date: 08/03/2020
ms.openlocfilehash: a7a11dde69554ae732590ddf36f8cf65929197fd
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122536172"
---
# <a name="azure-virtual-machine-extensions-and-features"></a>Azure 가상 머신 확장 및 기능
확장은 Azure VM에서 배포 후 구성 및 자동화를 제공하는 작은 애플리케이션입니다. Azure 플랫폼은 VM 구성, 모니터링, 보안 및 유틸리티 애플리케이션을 포함하는 다양한 확장을 호스팅합니다. 게시자는 애플리케이션을 사용하여 확장으로 래핑하고 설치를 간소화합니다. 필수 매개 변수를 제공하기만 하면 됩니다. 

## <a name="how-can-i-find-what-extensions-are-available"></a>사용할 수 있는 확장을 찾으려면 어떻게 해야 하나요?
VM을 선택하고 왼쪽 메뉴에서 **확장** 을 선택하여 사용 가능한 확장을 볼 수 있습니다. 전체 확장 목록을 가져오려면 [Linux용 VM 확장 검색](features-linux.md) 및 [Windows용 VM 확장 검색](features-windows.md)을 참조하세요.

## <a name="how-can-i-install-an-extension"></a>확장을 설치하려면 어떻게 해야 할까요?
Azure CLI, PowerShell, Resource Manager 템플릿 및 Azure Portal을 사용하여 Azure VM 확장을 관리할 수 있습니다. 확장을 시도하려면 Azure Portal로 이동하여 사용자 지정 스크립트 확장을 선택한 다음, 명령 또는 스크립트를 전달하여 확장을 실행합니다.

자세한 내용은 [Windows 사용자 지정 스크립트 확장](custom-script-windows.md) 및 [Linux 사용자 지정 스크립트 확장](custom-script-linux.md)을 참조하세요.

## <a name="how-do-i-manage-extension-application-lifecycle"></a>확장 애플리케이션 수명 주기를 관리하려면 어떻게 해야 할까요?
확장을 설치하거나 삭제하기 위해 VM에 직접 연결할 필요가 없습니다. Azure 확장 수명 주기는 VM 외부에서 관리되며 Azure 플랫폼에 통합됩니다.

## <a name="anything-else-i-should-be-thinking-about-for-extensions"></a>확장에 대해 고려해야 할 다른 사항은 무엇인가요?
일부 개별 VM 확장 애플리케이션에는 엔드포인트 액세스와 같은 자체 환경 필수 구성 요소가 있을 수 있습니다. 각 확장에는 지원되는 운영 체제를 비롯하여 모든 필수 구성 요소를 설명하는 문서가 있습니다.

## <a name="troubleshoot-extensions"></a>확장 문제 해결

Windows VM 확장에 대한 일반적인 문제 해결 단계를 원하는 경우 [Azure Windows VM 확장 오류 문제 해결](troubleshoot.md)을 참조하세요.

또는 각 확장에 대한 구체적인 문제 해결 정보는 확장 개요의 **문제 해결 및 지원** 섹션에서 확인할 수 있습니다. 다음은 사용 가능한 문제 해결 정보 목록입니다.

| 네임스페이스 | 문제 해결 |
|-----------|-----------------|
| microsoft.azure.monitoring.dependencyagent.dependencyagentlinux | [Linux용 Azure Monitor Linux 종속성](agent-dependency-linux.md#troubleshoot-and-support) |
| microsoft.azure.monitoring.dependencyagent.dependencyagentwindows | [Windows용 Azure Monitor 종속성](agent-dependency-windows.md#troubleshoot-and-support) |
| microsoft.azure.security.azurediskencryptionforlinux | [Linux용 Azure Disk Encryption](azure-disk-enc-linux.md#troubleshoot-and-support) |
| microsoft.azure.security.azurediskencryption | [Windows용 Azure Disk Encryption](azure-disk-enc-windows.md#troubleshoot-and-support) |
| microsoft.compute.customscriptextension | [Windows용 사용자 지정 스크립트](custom-script-windows.md#troubleshoot-and-support) |
| microsoft.ostcextensions.customscriptforlinux | [Linux용 Desired State Configuration](dsc-linux.md#troubleshoot-and-support) |
| microsoft.powershell.dsc | [Windows용 Desired State Configuration](dsc-windows.md#troubleshoot-and-support) |
| microsoft.hpccompute.nvidiagpudriverlinux | [Linux용 NVIDIA GPU 드라이버 확장](hpccompute-gpu-linux.md#troubleshoot-and-support) |
| microsoft.hpccompute.nvidiagpudriverwindows | [Windows용 NVIDIA GPU 드라이버 확장](hpccompute-gpu-windows.md#troubleshoot-and-support) |
| microsoft.azure.security.iaasantimalware | [Windows용 맬웨어 방지 확장](iaas-antimalware-windows.md#troubleshoot-and-support) |
| microsoft.enterprisecloud.monitoring.omsagentforlinux | [Linux용 Azure Monitor](oms-linux.md#troubleshoot-and-support)
| microsoft.enterprisecloud.monitoring.microsoftmonitoringagent | [Windows용 Azure Monitor](oms-windows.md#troubleshoot-and-support) |
| stackify.linuxagent.extension.stackifylinuxagentextension | [Linux용 Stackify Retrace](stackify-retrace-linux.md#troubleshoot-and-support) |
| vmaccessforlinux.microsoft.ostcextensions | [Linux용 암호 재설정](vmaccess.md#troubleshoot-and-support) |
| microsoft.recoveryservices.vmsnapshot | [Linux용 스냅샷](vmsnapshot-linux.md#troubleshoot-and-support) |
| microsoft.recoveryservices.vmsnapshot | [Windows용 스냅샷](vmsnapshot-windows.md#troubleshoot-and-support) |


## <a name="next-steps"></a>다음 단계
* Linux 에이전트 및 확장의 작동 방식에 대한 자세한 내용은 [Linux용 Azure VM 확장 및 기능](features-linux.md)을 참조하세요.
* Windows 게스트 에이전트 및 확장의 작동 방식에 대한 자세한 내용은 [Windows용 Azure VM 확장 및 기능](features-windows.md)을 참조하세요.  
* Windows 게스트 에이전트를 설치하려면 [Azure Windows Virtual Machine 에이전트 개요](agent-windows.md)를 참조하세요.  
* Linux 에이전트를 설치하려면 [Azure Linux Virtual Machine 에이전트 개요](agent-linux.md)를 참조하세요.  


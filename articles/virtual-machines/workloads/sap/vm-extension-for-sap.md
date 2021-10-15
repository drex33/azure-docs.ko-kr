---
title: SAP 솔루션에 대 한 Azure VM 확장 구현 | Microsoft Docs
description: SAP 용 VM 확장을 배포 하는 방법을 알아봅니다.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: OliverDoll
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 1c4f1951-3613-4a5a-a0af-36b85750c84e
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 06/22/2021
ms.author: oldoll
ms.openlocfilehash: bc85e68ed643e8a39f22343cfb939ab803fcdaef
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130067909"
---
# <a name="implement-the-azure-vm-extension-for-sap-solutions"></a>SAP 솔루션에 대 한 Azure VM 확장 구현

[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[1999351]:https://launchpad.support.sap.com/#/notes/1999351

[sap-resources]:vm-extension-for-sap.md#42ee2bdb-1efc-4ec7-ab31-fe4c22769b94 (SAP 리소스)
[new-monitoring]:vm-extension-for-sap.md#38d9f33f-d0af-4b8f-8134-f1f97d656fb6 (SAP 용 VM 확장의 새 버전)
[std-extension]:vm-extension-for-sap-standard.md (SAP 솔루션용 Azure VM 확장의 표준 버전)
[new-extension]:vm-extension-for-sap-new.md (SAP 솔루션에 대 한 새 버전의 Azure VM 확장 )
[azure-ps]:/powershell/azure/
[azure-cli]:/cli/azure/install-classic-cli
[azure-cli-2]:/cli/azure/install-azure-cli

[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e (Microsoft Azure에서의 SAP용 VM 배포 시나리오)
[planning-guide-9.1]:planning-guide.md#6f0a47f3-a289-4090-a053-2521618a28c3 (SAP용 Azure 모니터링 솔루션)

> [!NOTE]
> 일반 지원 문: SAP 용 Azure 확장에 대 한 지원은 SAP 지원 채널을 통해 제공 됩니다. SAP 솔루션용 Azure VM 확장에 대 한 지원이 필요한 경우 SAP 지원에 대 한 지원 사례를 여세요.
 
[Azure의 SAP용 VM 배포 시나리오][deployment-guide-3]의 설명대로 VM을 준비한 경우, Azure VM 에이전트가 가상 머신에 설치됩니다. 다음 단계는 글로벌 Azure 데이터 센터의 Azure 확장 리포지토리에서 사용할 수 있는 SAP용 Azure 확장을 배포하는 것입니다. 자세한 내용은 [SAP NetWeaver에 대한 Azure Virtual Machines 계획 및 구현][planning-guide-9.1]을 참조하세요.
 
Sap에서 사용자 환경을 지원 하도록 하려면 SAP 용 Azure 확장 구성에 설명 된 대로 SAP 솔루션에 대 한 Azure VM 확장을 사용 하도록 설정 합니다.

## <a name="sap-resources"></a><a name="42ee2bdb-1efc-4ec7-ab31-fe4c22769b94"></a>SAP 리소스

SAP 소프트웨어 배포를 설정하는 경우 다음과 같은 SAP 리소스가 필요합니다.

* SAP Note [1928533], 다음 항목을 포함합니다.
  * SAP 소프트웨어 배포에 지원되는 Azure VM 크기 목록
  * Azure VM 크기에 대한 중요한 용량 정보
  * 지원되는 SAP 소프트웨어 및 운영 체제(OS)와 데이터베이스 조합
  * Microsoft Azure에서 Windows 및 Linux에 필요한 SAP 커널 버전

* SAP Note [2015553]는 Azure에서 SAP을 지원하는 SAP 소프트웨어 배포에 대한 필수 구성 요소를 나열합니다.
* SAP Note [2178632]는 Azure에서 SAP에 대해 보고된 모든 모니터링 메트릭에 대한 자세한 정보를 포함하고 있습니다.
* SAP Note [1409604]는 Azure에서 Windows에 필요한 SAP Host Agent 버전을 포함하고 있습니다.
* SAP Note [2191498]는 Azure에서 Linux에 필요한 SAP Host Agent 버전을 포함하고 있습니다.
* SAP Note [2243692]는 Azure에서 Linux의 SAP 라이선스에 대한 정보를 포함하고 있습니다.
* SAP Note [1999351]은 SAP용 Azure 확장에 대한 추가 문제 해결 정보를 포함하고 있습니다.
* [Azure PowerShell][azure-ps]에 포함된 SAP 관련 PowerShell cmdlet입니다.
* [Azure CLI][azure-cli-2]에 포함된 SAP 관련 Azure CLI 명령입니다.
 
## <a name="differences-between-the-two-versions-of-the-azure-vm-extension-for-sap-solutions"></a>SAP 솔루션에 대 한 Azure VM 확장의 두 버전 간 차이점

SAP 용 VM 확장에는 두 가지 버전이 있습니다. Sap [리소스][sap-resources]에 나열 된 리소스에서 sap 및 필요한 최소 버전의 sap 커널 및 Sap 호스트 에이전트에 대 한 필수 구성 요소를 확인 합니다.

### <a name="standard-version-of-vm-extension-for-sap"></a>SAP 용 VM 확장의 표준 버전

이 버전은 SAP 용 현재 표준 VM 확장입니다. Microsoft에서 SAP 용 새 VM 확장을 설치 하는 것을 권장 하는 몇 가지 예외가 있습니다. 또한 지원 사례를 열 때 SAP Support는 새 VM 확장을 설치 하도록 요청할 수 있습니다. SAP 용 VM 확장의 새 버전을 사용 하는 경우에 대 한 자세한 내용은 [sap 용 Vm 확장의 새 버전][new-monitoring] 장을 참조 하세요.
 
### <a name="new-version-of-vm-extension-for-sap"></a><a name="38d9f33f-d0af-4b8f-8134-f1f97d656fb6"></a>SAP 용 VM 확장의 새 버전

이 버전은 SAP 솔루션에 대 한 새로운 Azure VM 확장입니다. 추가 개선 사항 및 새로운 Azure 서비스를 사용 하 여 가상 머신의 모든 Azure 리소스를 모니터링할 수 있도록 새 확장을 빌드 했습니다. 이 확장은 URL "management.azure.com"에 대 한 인터넷 액세스가 필요 합니다. 표준 디스크 및 운영 체제와 같은 추가 저장소 옵션을 지원 합니다. 다음 중 하나가 적용 되는 경우 VM 확장의 새 버전을 선택 하세요.
 
* Terraform, Azure Resource Manager 템플릿 또는 Azure CLI 또는 다른 방법으로 VM 확장을 설치 하는 것이 좋습니다 Azure PowerShell
* SUSE SLES 15 이상에 확장을 설치하려는 경우
* Red Hat Enterprise Linux 8.1 이상에 확장을 설치 하려고 합니다.
* Azure Ultra Disk 또는 Standard Managed Disks를 사용하려는 경우
* Microsoft 또는 SAP 지원에서 새 확장 설치를 요청하는 경우
 
## <a name="recommendation"></a>권장

현재 확장의 새 버전에 대 한 사용 사례가 적용 되지 않는 경우 각 설치에 대해 표준 버전의 확장을 사용 하는 것이 좋습니다. 현재 VM 확장의 새 버전을 향상 시키기 위해 노력 하 고 있으며,이를 기본값으로 설정 하 고 확장의 표준 버전을 사용 중단 수 있습니다. 이 시간 동안에는 새 버전을 사용할 수 있습니다. 그러나 VM 확장이 management.azure.com에 액세스할 수 있는지 확인 해야 합니다.
 
> [!NOTE]
> 두 버전 간에 전환 하기 전에 VM 확장을 제거 해야 합니다.

## <a name="next-steps"></a>다음 단계
* [SAP 솔루션용 Azure VM 확장의 표준 버전][std-extension]
* [SAP 솔루션에 대 한 새 버전의 Azure VM 확장][new-extension]


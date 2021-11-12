---
title: CloudSimple의 Azure VMware 솔루션-프라이빗 클라우드로 워크로드 VM 마이그레이션
description: 온-프레미스 vCenter에서 CloudSimple 프라이빗 클라우드 vCenter로 가상 컴퓨터를 마이그레이션하는 방법에 대해 설명합니다.
author: suzizuber
ms.author: v-szuber
ms.date: 08/20/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 2bebb09f144b36e5c9ac54d8e5a54dd0aac39e27
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132322230"
---
# <a name="migrate-workload-vms-from-on-premises-vcenter-to-private-cloud-vcenter-environment"></a>온-프레미스 vCenter에서 프라이빗 클라우드 vCenter로 워크로드 VM을 마이그레이션합니다.

온-프레미스 데이터 센터에서 CloudSimple 프라이빗 클라우드로 VM을 마이그레이션하려면 몇 가지 옵션을 사용할 수 있습니다.  프라이빗 클라우드는 VMware vCenter에 대한 기본 액세스를 제공하며 VMware에서 지원되는 도구는 워크로드 마이그레이션에 사용할 수 있습니다. 이 문서에서는 몇 가지 vCenter 마이그레이션 옵션을 설명합니다.

## <a name="prerequisites"></a>사전 요구 사항

온-프레미스 데이터 센터에서 VM 및 데이터를 마이그레이션하려면 데이터 센터에서 프라이빗 클라우드 환경으로 네트워크를 연결해야 합니다.  다음 방법 중 하나를 사용하여 네트워크 연결을 설정합니다.

* 온-프레미스 환경과 프라이빗 클라우드 간에 [사이트 간 VPN 연결](vpn-gateway.md#set-up-a-site-to-site-vpn-gateway)을 구성합니다.
* 온-프레미스 ExpressRoute 회로와 CloudSimple ExpressRoute 회로 간의 ExpressRoute GlobalReach 연결.

온-프레미스 vCenter 환경에서 프라이빗 클라우드로의 네트워크 경로는 vMotion을 사용하여 VM을 마이그레이션하는 데 사용할 수 있어야 합니다.  온-프레미스 vCenter의 vMotion 네트워크에는 라우팅 기능이 있어야 합니다.  방화벽이 온-프레미스 vCenter와 프라이빗 클라우드 vCenter 간의 모든 vMotion 트래픽을 허용하는지 확인합니다. (프라이빗 클라우드에서 vMotion 네트워크의 라우팅은 기본적으로 구성됩니다.)

## <a name="migrate-isos-and-templates"></a>ISO 및 템플릿 마이그레이션

프라이빗 클라우드에서 새 가상 컴퓨터를 만들려면 ISO 및 VM 템플릿을 사용합니다.  ISO 및 템플릿을 프라이빗 클라우드 vCenter에 업로드하고 사용할 수 있게 하려면 다음 방법을 사용합니다.

1. VCenter UI에서 프라이빗 클라우드 vCenter에 ISO를 업로드합니다.
2. 프라이빗 클라우드 vCenter에 [콘텐츠 라이브러리를 게시](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A0F1C13-7336-45CE-B211-610D39A6E1F4.html)합니다.

    1. 온-프레미스 콘텐츠 라이브러리를 게시합니다.
    2. 프라이빗 클라우드 vCenter에 콘텐츠 라이브러리를 게시합니다.
    3. 게시된 온-프레미스 콘텐츠 라이브러리를 구독합니다.
    4. 구독한 콘텐츠에 대한 액세스를 위해 콘텐츠 라이브러리를 동기화합니다.

## <a name="migrate-vms-using-powercli"></a>PowerCLI를 사용하여 VM 마이그레이션

온-프레미스 vCenter에서 프라이빗 클라우드 vCenter로 VM을 마이그레이션하려면 VMware Labs에서 사용할 수 있는 VMware PowerCLI 또는 크로스 vCenter 워크로드 마이그레이션 유틸리티를 사용합니다.  다음 샘플 스크립트는 PowerCLI 마이그레이션 명령을 보여줍니다.

```
$sourceVC = Connect-VIServer -Server <source-vCenter name> -User <source-vCenter user name> -Password <source-vCenter user password>
$targetVC = Connect-VIServer -Server <target-vCenter name> -User <target-vCenter user name> -Password <target-vCenter user password>
$vmhost = <name of ESXi host on destination>
$vm = Get-VM -Server $sourceVC <name of VM>
Move-VM -VM $vm -VMotionPriority High -Destination (Get-VMhost -Server $targetVC -Name $vmhost) -Datastore (Get-Datastore -Server $targetVC -Name <name of tgt vc datastore>)
```

> [!NOTE]
> 대상 vCenter 서버 및 ESXi 호스트의 이름을 사용하려면 온-프레미스에서 프라이빗 클라우드로의 DNS 전달을 구성합니다.

## <a name="migrate-vms-using-nsx-layer-2-vpn"></a>NSX Layer 2 VPN을 사용하여 VM 마이그레이션

이 옵션은 온-프레미스 VMware 환경에서 Azure의 프라이빗 클라우드로 워크로드의 실시간 마이그레이션을 가능하게 합니다.  이 늘어난 계층 2 네트워크를 사용하여 온-프레미스의 서브넷은 프라이빗 클라우드에서 사용할 수 있습니다.  마이그레이션 후 VM에 대한 새 IP 주소 할당은 필요하지 않습니다.

[계층 2 스트레치 네트워크를 사용한 워크로드 마이그레이션](migration-layer-2-vpn.md)은 계층 2 VPN을 사용하여 온-프레미스 환경에서 프라이빗 클라우드로 계층 2 네트워크를 확장하는 방법을 설명합니다.

## <a name="migrate-vms-using-backup-and-disaster-recovery-tools"></a>백업 및 재해 복구 도구를 사용하여 VM 마이그레이션

백업/복원 도구 및 재해 복구 도구를 사용하여 프라이빗 클라우드로 VM을 마이그레이션할 수 있습니다.  타사 도구를 사용하여 만든 백업에서 복원하려면 프라이빗 클라우드를 대상으로 사용합니다.  또한 프라이빗 클라우드는 VMware SRM 또는 타사 도구를 사용하여 재해 복구를 위한 대상으로 사용할 수 있습니다.

이러한 태스크에 대한 자세한 내용은 다음 토픽을 참조하십시오.

* [Veeam B&R을 사용하여 CloudSimple 프라이빗 클라우드에서 워크로드 가상 머신 백업](backup-workloads-veeam.md)
* [온-프레미스 VMware 워크로드를 사용하여 CloudSimple 프라이빗 클라우드를 재해 복구 사이트로 설정](disaster-recovery-zerto.md)

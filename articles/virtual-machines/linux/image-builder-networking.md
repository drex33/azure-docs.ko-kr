---
title: Azure Image Builder 서비스 네트워킹 옵션
description: Azure VM Image Builder 서비스를 배포하는 경우 네트워킹 옵션 이해
author: kof-f
ms.author: kofiforson
ms.reviewer: cynthn
ms.date: 08/10/2020
ms.topic: article
ms.service: virtual-machines
ms.subservice: image-builder
ms.openlocfilehash: 1285f3673851e5784a5bc35b606e2dc30d7cfd09
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122691872"
---
# <a name="azure-image-builder-service-networking-options"></a>Azure Image Builder 서비스 네트워킹 옵션

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: 유연한 확장 집합 

기존 VNET을 사용하거나 사용하지 않고 Azure Image Builder를 배포하도록 선택해야 합니다.

## <a name="deploy-without-specifying-an-existing-vnet"></a>기존 VNET을 지정하지 않고 배포

기존 VNET을 지정하지 않으면 Azure Image Builder는 준비 리소스 그룹에 VNET 및 서브넷을 만듭니다. 공용 IP 리소스는 네트워크 보안 그룹과 함께 Azure Image Builder 서비스에 대한 인바운드 트래픽을 제한하는 데 사용됩니다. 공용 IP를 통해 이미지 빌드 중에 Azure Image Builder 명령의 채널을 쉽게 사용할 수 있습니다. 빌드가 완료되면 VM, 공용 IP, 디스크 및 VNET이 삭제됩니다. 이 옵션을 사용하려면 VNET 속성을 지정하지 마세요.

## <a name="deploy-using-an-existing-vnet"></a>기존 VNET을 사용하여 배포

VNET 및 서브넷을 지정하면 Azure Image Builder는 선택한 VNET에 빌드 VM을 배포합니다. VNET에서 액세스 가능한 리소스에 액세스할 수 있습니다. 다른 VNET에 연결되지 않은 분리된 VNET을 만들 수도 있습니다. VNET을 지정하면 Azure Image Builder는 공용 IP 주소를 사용하지 않습니다. Azure Image Builder 서비스에서 빌드 VM에 대한 통신은 Azure Private Link 기술을 사용합니다.

자세한 내용은 다음 예제 중 하나를 참조하세요.

* [기존 Azure VNET에 액세스를 허용하는 Windows VM용 Azure Image Builder 사용](../windows/image-builder-vnet.md)
* [기존 Azure VNET에 액세스를 허용하는 Linux VM용 Azure Image Builder 사용](image-builder-vnet.md)

### <a name="what-is-azure-private-link"></a>Azure Private Link란?

Azure Private Link는 가상 네트워크에서 Azure PaaS(Platform as a Service), 고객 소유 또는 Microsoft 파트너 서비스에 대한 프라이빗 연결을 제공합니다. 네트워크 아키텍처를 간소화하고 퍼블릭 인터넷에 대한 데이터 노출을 예방하여 Azure에서 엔드포인트 간 연결을 보호합니다. 자세한 내용은 [Private Link 설명서](../../private-link/index.yml)를 참조하세요.

### <a name="required-permissions-for-an-existing-vnet"></a>기존 VNET에 필요한 권한

Azure Image Builder가 기존 VNET을 사용하려면 특정 권한이 필요합니다. 자세한 내용은 [Azure CLI를 사용하여 Azure Image Builder 서비스 권한 구성](image-builder-permissions-cli.md) 또는 [PowerShell을 사용하여 Azure Image Builder 서비스 권한 구성](image-builder-permissions-powershell.md)을 참조하세요.

### <a name="what-is-deployed-during-an-image-build"></a>이미지 빌드 중에 배포되는 것은 무엇인가요?

기존 VNET을 사용하면 Azure Image Builder는 Private Link에 연결된 ALB(Azure Load Balancer) 및 추가 VM(프록시 VM)을 배포합니다. AIB 서비스의 트래픽은 ALB에 대한 프라이빗 링크를 통과합니다. ALB는 Linux OS의 경우 포트 60001 또는 Windows OS의 경우 포트 60000을 사용하여 프록시 VM과 통신합니다. 프록시는 Linux OS의 경우 포트 22 또는 Windows OS의 경우 포트 5986을 사용하여 빌드 VM에 명령을 전달합니다.

> [!NOTE]
> VNET은 Azure Image Builder 서비스 지역과 동일한 지역에 있어야 합니다.
> 

### <a name="why-deploy-a-proxy-vm"></a>프록시 VM을 배포하는 이유

공용 IP가 없는 VM은 내부 Load Balancer 뒤에 있는 경우 인터넷에 액세스할 수 없습니다. VNET에 사용되는 Azure Load Balancer는 내부용입니다. 프록시 VM은 빌드 중에 빌드 VM의 인터넷 액세스를 허용합니다. 연결된 네트워크 보안 그룹을 사용하여 빌드 VM 액세스를 제한할 수 있습니다.

배포된 프록시 VM 크기는 빌드 VM 외에도 표준 A1_v2입니다. 프록시 VM은 Azure Image Builder 서비스와 빌드 VM 간에 명령을 보내는 데 사용됩니다. 크기 또는 OS를 포함한 프록시 VM 속성은 변경할 수 없습니다. Windows 및 Linux 이미지 빌드에 대해 둘 다 프록시 VM 속성을 변경할 수 없습니다.

### <a name="image-template-parameters-to-support-vnet"></a>VNET을 지원하는 이미지 템플릿 매개 변수
```json
"VirtualNetworkConfig": {
        "name": "",
        "subnetName": "",
        "resourceGroupName": ""
        },
```

| 설정 | Description |
|---------|---------|
| name | (선택 사항) 기존 가상 네트워크의 이름입니다. |
| subnetName | 지정된 가상 네트워크 내에 있는 서브넷의 이름입니다. *name* 이 지정된 경우에만 지정해야 합니다. |
| resourceGroupName | 지정된 가상 네트워크를 포함하는 리소스 그룹의 이름입니다. *name* 이 지정된 경우에만 지정해야 합니다. |

Private Link 서비스에는 지정된 VNET 및 서브넷의 IP가 필요합니다. 현재 Azure는 이 IP에서 네트워크 정책을 지원하지 않습니다. 따라서 서브넷에서 네트워크 정책을 사용하지 않도록 설정해야 합니다. 자세한 내용은 [Private Link 설명서](../../private-link/index.yml)를 참조하세요.

### <a name="checklist-for-using-your-vnet"></a>VNET 사용을 위한 검사 목록

1. ALB(Azure Load Balancer)가 NSG에서 프록시 VM과 통신하도록 허용
    * [AZ CLI 예제](image-builder-vnet.md#add-network-security-group-rule)
    * [PowerShell 예제](../windows/image-builder-vnet.md#add-network-security-group-rule)
2. 서브넷에서 프라이빗 서비스 정책 사용 안함
    * [AZ CLI 예제](image-builder-vnet.md#disable-private-service-policy-on-subnet)
    * [PowerShell 예제](../windows/image-builder-vnet.md#disable-private-service-policy-on-subnet)
3. Azure Image Builder가 ALB를 만들고 VNET에 VM을 추가하도록 허용
    * [AZ CLI 예제](image-builder-permissions-cli.md#existing-vnet-azure-role-example)
    * [PowerShell 예제](image-builder-permissions-powershell.md#permission-to-customize-images-on-your-vnets)
4. Azure Image Builder가 원본 이미지를 읽고 쓰고 이미지를 만들도록 허용
    * [AZ CLI 예제](image-builder-permissions-cli.md#custom-image-azure-role-example)
    * [PowerShell 예제](image-builder-permissions-powershell.md#custom-image-azure-role-example)
5. Azure Image Builder 서비스 지역과 동일한 지역에서 VNET을 사용 중인지 확인합니다.


## <a name="next-steps"></a>다음 단계

자세한 내용은 [Azure Image Builder 개요](../image-builder-overview.md)를 참조하세요.
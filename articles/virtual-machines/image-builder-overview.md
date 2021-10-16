---
title: Azure Image Builder에 대해 알아보기
description: Azure의 가상 머신에 대한 Azure Image Builder에 대해 자세히 알아보세요.
author: sumit-kalra
ms.author: sukalra
ms.date: 10/15/2021
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: image-builder
ms.custom: references_regions
ms.reviewer: cynthn
ms.openlocfilehash: af390012d2f433b580f66187889c680e3de01a86
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/15/2021
ms.locfileid: "130074288"
---
# <a name="azure-image-builder-overview"></a>Azure 이미지 작성기 개요

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 균일한 확장 집합

표준화된 VM(가상 머신) 이미지를 사용하면 조직이 클라우드로 마이그레이션하고 배포에서 일관성을 유지할 수 있습니다. 이미지에는 일반적으로 미리 정의된 보안, 구성 설정 및 필요한 소프트웨어가 포함됩니다. 사용자 고유의 이미징 파이프라인을 설정하려면 시간, 인프라 및 설정이 필요합니다. Azure VM Image Builder(Image Builder)를 사용하면 이미지를 설명하는 구성을 만들고 이미지가 빌드된 다음 배포되는 서비스에 제출하기만 하면 됩니다.

Image Builder 사용하면 기존 스크립트, 명령 및 프로세스를 계속 사용하여 이미지를 사용자 지정하면서 기존 이미지 사용자 지정 파이프라인을 Azure로 마이그레이션할 수 있습니다. Image Builder 사용하면 VM을 만든 후 한 번에 워크로드를 사용할 수 있도록 핵심 애플리케이션을 VM 이미지에 통합할 수 있습니다. Azure Stack 사용하거나 쉽게 내보낼 수 있도록 Azure Virtual Desktop 또는 VHD에 대한 이미지를 빌드하는 구성을 추가할 수도 있습니다.

Image Builder Azure Marketplace 또는 기존 사용자 지정 이미지에서 Windows 또는 Linux 이미지로 시작하고 사용자 지정을 추가할 수 있습니다. [Azure Shared Image Gallery](shared-image-galleries.md)호스팅되는 결과 이미지를 관리형 이미지 또는 VHD로 지정할 수도 있습니다.

## <a name="features"></a>기능

직접 또는 다른 도구로 사용자 지정 VM 이미지를 만들 수 있지만 프로세스는 번거롭고 불안정할 수 있습니다. [HashiCorp Packer를](https://www.packer.io/)기반으로 하는 Azure VM Image Builder 관리형 서비스의 이점을 제공합니다.

### <a name="simplicity"></a>단순함

- VM 이미지를 만들기 위해 복잡한 도구, 프로세스 및 수동 단계를 사용할 필요가 없습니다. Image Builder 이러한 모든 세부 정보를 추상화하고 이미지(sysprep)를 일반화해야 하는 필요성과 같은 Azure 특정 요구 사항을 숨기면서 고급 사용자에게 재정의 기능을 제공합니다.
- Image Builder 클릭 및 이동 환경을 위해 기존 이미지 빌드 파이프라인과 통합할 수 있습니다. 파이프라인에서 Image Builder 호출하거나 [Azure Image Builder Service DevOps 작업(미리 보기)](./linux/image-builder-devops-task.md)을 사용할 수 있습니다.
- Image Builder 다양한 원본에서 사용자 지정 데이터를 가져와서 VM 이미지를 빌드하기 위해 한 곳에서 모두 수집할 필요가 없습니다.
- azure Shared Image Gallery Image Builder 통합하면 이미지를 전역적으로 배포, 복제, 버전 관리 및 크기 조정하는 데 사용할 수 있는 이미지 관리 시스템을 제공합니다. 또한 처음부터 다시 빌드하지 않고 VHD와 동일한 결과 이미지를 배포하거나 하나 이상의 관리되는 이미지로 배포할 수 있습니다.

### <a name="infrastructure-as-code"></a>코드로서의 인프라

- 장기 *인프라(예: 사용자 지정 데이터를 보유하는 Storage 계정)* 또는 임시 인프라(예:*이미지를 빌드하기 위한 임시 Virtual Machine)를* 관리할 필요가 없습니다. 
- Image Builder VM 이미지 빌드 사양 및 사용자 지정 아티팩트 를 Azure 리소스로 저장하여 오프라인 정의를 유지 관리해야 하는 필요성과 실수로 인한 삭제 또는 업데이트로 인한 환경 드리프트의 위험을 제거합니다.

### <a name="security"></a>보안

- Image Builder 사용하면 *기준 이미지(최소 보안 및 회사 구성을 포함할 수* 있음)를 만들 수 있으며 여러 부서에서 이를 추가로 사용자 지정할 수 있습니다. 이러한 이미지는 Image Builder 사용하여 보안 및 규격을 유지하여 원본 이미지의 최신 패치 버전을 사용하여 골든 이미지를 신속하게 다시 빌드할 수 있습니다. 또한 Image Builder 사용하면 Azure Windows 기준을 충족하는 이미지를 더 쉽게 빌드할 수 있습니다. 자세한 내용은 [Image Builder - 기준 템플릿 Windows 참조하세요.](https://github.com/Azure/azure-quickstart-templates/tree/master/demos/imagebuilder-windowsbaseline)
- Image Builder 사용자 지정 아티팩트에서 가져올 수 있도록 사용자 지정 아티팩트에서 공개적으로 액세스할 수 있도록 할 필요가 없습니다. Image Builder [Azure 관리 ID를](../active-directory/managed-identities-azure-resources/overview.md) 사용하여 이러한 리소스를 가져올 수 있으며, Azure-RBAC를 사용하여 필요에 따라 이 ID의 권한을 엄격하게 제한할 수 있습니다. 이는 아티팩트 비밀을 유지할 수 있을 뿐만 아니라 권한이 없는 행위자에 의해 변조될 수도 없음을 의미합니다.
- 사용자 지정 아티팩트, 임시 컴퓨팅 & 스토리지 리소스 및 결과 이미지의 복사본은 모두 Azure-RBAC에 의해 제어되는 액세스 권한으로 구독 내에 안전하게 저장됩니다. 여기에는 사용자 지정 이미지를 만들고 사용자 지정 스크립트 및 파일이 알 수 없는 구독의 알 수 없는 VM에 복사되지 않도록 하는 데 사용되는 빌드 VM이 포함됩니다. 또한 빌드 [VM에 대한 격리된 VM 제품을](./isolation.md) 사용하여 다른 고객의 워크로드에서 높은 수준의 격리를 달성할 수 있습니다.
- 기존 가상 네트워크에 Image Builder 연결할 수 있으므로 기존 구성 서버(DSC, Chef, Puppet 등), 파일 공유 또는 기타 라우팅 가능한 서버 & 서비스와 통신할 수 있습니다.

## <a name="regions"></a>영역

Azure Image Builder Service는 지역 지역에서 사용할 수 있습니다. 

>[!NOTE]
> 이미지는 이러한 지역 외부에 계속 배포할 수 있습니다.
> 
- 미국 동부
- 미국 동부 2
- 미국 중서부
- 미국 서부
- 미국 서부 2
- 미국 중남부
- 북유럽
- 서유럽
- 동남아시아
- 오스트레일리아 남동부
- 오스트레일리아 동부
- 영국 남부
- 영국 서부

## <a name="os-support"></a>OS 지원
Azure Image Builder Azure Marketplace 기본 OS 이미지를 지원합니다.
- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 7.6, 7.7
- CentOS 7.6, 7.7
- SLES 12 SP4
- SLES 15, SLES 15 SP1
- Windows 10 RS5 Enterprise/Enterprise 다중 세션/Professional
- Windows 2016
- Windows 2019

>[!IMPORTANT]
> 나열된 운영 체제가 테스트되었으며 이제 Azure Image Builder 작동합니다. 그러나 Azure Image Builder는 마켓플레이스의 모든 Linux 또는 Windows 이미지에서 작동해야 합니다.

## <a name="how-it-works"></a>작동 방법

Azure VM Image Builder는 Azure 리소스 공급자가 액세스할 수 있는 완전 관리형 Azure 서비스입니다. 원본 이미지, 수행할 사용자 지정, 새 이미지를 배포할 위치를 지정하는 서비스에 대한 구성을 제공합니다. 아래 다이어그램은 상위 수준 워크플로를 보여 줍니다.

![Azure 공유 이미지 갤러리를 사용하여 원본(Windows/Linux), 사용자 지정(셸, PowerShell, Windows 다시 시작 및 업데이트, 파일 추가) 및 글로벌 배포를 표시하는 Azure Image Builder 프로세스의 개념 그리기](./media/image-builder-overview/image-builder-flow.png)

템플릿 구성은 PowerShell, Azure CLI Azure Resource Manager 템플릿을 사용하고 Azure VM Image Builder DevOps 작업을 사용하여 전달할 수 있습니다. 서비스에 제출하면 이미지 템플릿 리소스를 만듭니다. 이미지 템플릿 리소스가 만들어지면 구독에서 만든 스테이징 리소스 그룹이 형식으로 `IT_\<DestinationResourceGroup>_\<TemplateName>_\(GUID)` 표시됩니다. 준비 리소스 그룹에는 ScriptURI 속성의 File, Shell, PowerShell 사용자 지정에서 참조되는 파일 및 스크립트가 포함됩니다.

`Run` 이미지 템플릿 리소스에서 호출하는 빌드를 실행하기 위해 서비스는 VM, 네트워크, 디스크, 네트워크 어댑터 등 빌드에 대한 추가 리소스를 배포합니다. 기존 VNET을 사용하지 않고 이미지를 작성하는 경우에도 공용 IP 및 NSG가 배포됩니다. 서비스는 SSH 또는 WinRM을 사용하여 빌드 VM에 연결합니다. 기존 VNET을 선택하면 서비스가 Azure Private Link 사용하여 배포되고 공용 IP 주소가 필요하지 않습니다. 자세한 내용은 [Image Builder 네트워킹 개요를](./linux/image-builder-networking.md)참조하세요.

빌드를 완료하면 준비 리소스 그룹 및 스토리지 계정을 제외한 모든 리소스가 삭제되고, 제거하려면 이미지 템플릿 리소스를 삭제하거나, 다시 빌드를 실행하기 위해 해당 리소스를 그대로 둘 수 있습니다.

이 설명서에는 [Azure Image Builder GitHub 리포지토리](https://github.com/azure/azvmimagebuilder) 구성 템플릿과 솔루션을 참조하는 여러 예제 및 단계별 가이드가 있습니다.

### <a name="move-support"></a>이동 지원
이미지 템플릿 리소스는 변경할 수 없으며, 리소스 및 준비 리소스 그룹에 대한 링크를 포함하므로 리소스 종류 이동을 지원하지 않습니다. 이미지 템플릿 리소스를 이동하려면 구성 템플릿의 복사본이 있는지 확인합니다(없는 경우 리소스에서 기존 구성 추출). 새 리소스 그룹에 새 이름으로 새 이미지 템플릿 리소스를 만들고 이전 이미지 템플릿 리소스를 삭제합니다. 

## <a name="permissions"></a>사용 권한
(AIB)에 등록하면 AIB 서비스에 스테이징 리소스 그룹을 만들고 관리 및 삭제할 수 있는 권한이 `(IT_*)` 부여되고 이미지 빌드에 필요한 리소스를 추가할 수 있는 권한이 부여됩니다. 성공적으로 등록하는 동안 구독에서 사용할 수 있는 AIB SPN(서비스 주체 이름)이 이 작업을 수행합니다.

Azure VM Image Builder에서 관리형 이미지 또는 공유 이미지 갤러리에 이미지를 배포하도록 허용하려면 이미지를 읽고 쓸 수 있는 권한이 있는 Azure 사용자 할당 ID를 만들어야 합니다. Azure Storage에 액세스하는 경우 프라이빗 및 퍼블릭 컨테이너를 읽을 권한이 필요합니다.

권한은 [PowerShell](./linux/image-builder-permissions-powershell.md) 및 [AZ CLI](./linux/image-builder-permissions-cli.md)에 대해 자세히 설명되어 있습니다.

## <a name="costs"></a>비용
Azure Image Builder를 사용하여 이미지를 만들고, 빌드하고, 저장하는 경우 컴퓨팅, 네트워킹 및 스토리지 비용이 일부 발생합니다. 이러한 비용은 사용자 지정 이미지를 수동으로 만들 때 발생하는 비용과 비슷합니다. 리소스의 경우 Azure 요금에 청구됩니다. 

이미지 생성 프로세스 동안 파일이 다운로드되어 `IT_<DestinationResourceGroup>_<TemplateName>` 리소스 그룹에 저장되므로 스토리지 비용이 약간 발생합니다. 이러한 상황을 유지하지 않으려면 이미지를 빌드한 후 **이미지 템플릿** 을 삭제합니다.
 
Image Builder는 VM에 필요한 D1v2 VM 크기, 스토리지 및 네트워킹을 사용하여 VM을 만듭니다. 이러한 리소스는 빌드 프로세스가 지속되는 동안 유지되며, Image Builder에서 이미지 만들기를 완료하면 삭제됩니다. 
 
Azure Image Builder에서는 선택한 지역에 이미지를 배포하므로 네트워크 송신 요금이 발생할 수 있습니다.

## <a name="hyper-v-generation"></a>Hyper-V 세대
Image Builder는 현재 기본적으로 Azure SIG(Shared Image Gallery) 또는 관리되는 이미지에 대한 Hyper-V 세대(Gen1) 1 이미지 만들기만 지원합니다. 
 
## <a name="next-steps"></a>다음 단계 
 
Azure Image Builder를 사용해 보려면 [Linux](./linux/image-builder.md) 또는 [Windows](./windows/image-builder.md) 이미지 빌드에 대한 문서를 참조하세요.

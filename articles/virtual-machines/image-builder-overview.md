---
title: Azure Image Builder에 대해 알아보기
description: Azure의 가상 머신에 대한 Azure Image Builder에 대해 자세히 알아보세요.
author: kof-f
ms.author: kofiforson
ms.date: 05/24/2021
ms.topic: conceptual
ms.service: virtual-machines
ms.subservice: image-builder
ms.custom: references_regions
ms.reviewer: cynthn
ms.openlocfilehash: 805b2a8bf29edfd76804aec3607acc11155edd16
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122697072"
---
# <a name="azure-image-builder-overview"></a>Azure 이미지 작성기 개요

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: Windows VM :heavy_check_mark: 유연한 확장 집합 :heavy_check_mark: 균일한 확장 집합

조직에서는 표준화된 VM(가상 머신) 이미지를 사용하여 클라우드로 마이그레이션하고 배포의 일관성을 유지할 수 있습니다. 이미지에는 일반적으로 미리 정의된 보안 및 구성 설정과 필수 소프트웨어가 포함되어 있습니다. 사용자 고유의 이미징 파이프라인을 설정하려면 시간, 인프라 및 설정이 필요하지만, Azure VM Image Builder를 사용하면 이미지를 설명하는 구성을 제공하고, 서비스에 제출하고, 이미지를 빌드하고 배포하기만 하면 됩니다.
 
Azure VM Image Builder(Azure Image Builder)를 사용하여 Windows 또는 Linux 기반 Azure Marketplace 이미지, 기존 사용자 지정 이미지로 시작하고 사용자 지정 항목을 추가할 수 있습니다. Image Builder는 [HashiCorp Packer](https://packer.io/)를 기반으로 하기 때문에 일부 유사성을 발견할 수 있지만 관리되는 서비스의 혜택을 누릴 수 있습니다. [Azure Shared Image Gallery](shared-image-galleries.md)에서 이미지를 호스트하는 위치를 관리형 이미지 또는 VHD로 지정할 수도 있습니다.


## <a name="features"></a>기능

Azure Image Builder는 다음과 같은 기능을 지원합니다.

- 기준 이미지를 만들기: 최소 보안 및 회사 구성을 포함하는 이러한 이미지를 만들 수 있으므로 부서에서 추가로 사용자 지정할 수 있습니다.
- 핵심 애플리케이션을 통합하여 VM이 생성된 후 작업을 수행하거나 구성을 추가하여 Azure Virtual Desktop 이미지를 지원할 수 있습니다.
- 기존 이미지 패치: Image Builder에서 기존 사용자 지정 이미지를 지속적으로 패치할 수 있습니다.
- Image Builder를 기존 가상 네트워크에 연결: 기존 구성 서버(DSC, Chef, Puppet 등), 파일 공유 또는 라우팅할 수 있는 다른 서버/서비스에 연결할 수 있습니다.
- Azure Shared Image Gallery와 통합: 이미지를 전역적으로 배포하고, 버전을 관리하고, 크기를 조정할 수 있으며 이미지 관리 시스템을 사용할 수 있습니다.
- 기존 이미지 빌드 파이프라인과 통합: 파이프라인에서 Image Builder를 호출하거나 간단한 Image Builder Azure DevOps 작업을 사용합니다.
- 기존 이미지 사용자 지정 파이프라인을 Azure로 마이그레이션 기존 스크립트, 명령 및 프로세스를 사용하여 이미지 사용자 지정
- VHD 형식의 이미지를 만들어 Azure Stack 지원
 

## <a name="regions"></a>영역

Azure Image Builder 서비스는 [이러한](./linux/image-builder-json.md#location) 지역에서 사용할 수 있습니다. 이러한 영역 외부로 이미지를 배포할 수 있습니다.

## <a name="os-support"></a>OS 지원
AIB는 다음과 같은 Azure Marketplace 기본 OS 이미지를 지원합니다.
- Ubuntu 18.04
- Ubuntu 16.04
- RHEL 7.6, 7.7
- CentOS 7.6, 7.7
- SLES 12 SP4
- SLES 15, SLES 15 SP1
- Windows 10 RS5 Enterprise/Enterprise 다중 세션/Professional
- Windows 2016
- Windows 2019

Azure Image Builder에서 테스트하고 작동하는 것으로 알려진 운영 체제를 소개했습니다. 그러나 Azure Image Builder는 마켓플레이스의 모든 Linux 또는 Windows 이미지에서 작동해야 합니다.

## <a name="how-it-works"></a>작동 방법

Azure VM Image Builder는 Azure 리소스 공급자가 액세스할 수 있는 완전 관리형 Azure 서비스입니다. 원본 이미지, 수행할 사용자 지정, 새 이미지를 배포할 위치를 지정하는 서비스에 대한 구성을 제공합니다. 아래 다이어그램은 상위 수준 워크플로를 보여 줍니다.

![Azure 공유 이미지 갤러리를 사용하여 원본(Windows/Linux), 사용자 지정(셸, PowerShell, Windows 다시 시작 및 업데이트, 파일 추가) 및 글로벌 배포를 표시하는 Azure Image Builder 프로세스의 개념 그리기](./media/image-builder-overview/image-builder-flow.png)

PowerShell, Az CLI, ARM 템플릿을 사용하여 템플릿 구성을 전달하고, Azure VM Image Builder DevOps 작업을 사용하여 서비스에 제출할 때 이미지 템플릿 리소스를 만들 수 있습니다. 이미지 템플릿 리소스가 만들어지면 구독에서 생성된 준비 리소스 그룹이 (IT_\<DestinationResourceGroup> _\<TemplateName>_ \(GUID) 형식으로 표시됩니다. 준비 리소스 그룹에는 ScriptURI 속성의 File, Shell, PowerShell 사용자 지정에서 참조되는 파일 및 스크립트가 포함됩니다.

`Run` 이미지 템플릿 리소스에서 호출하는 빌드를 실행하기 위해 서비스는 VM, 네트워크, 디스크, 네트워크 어댑터 등 빌드에 대한 추가 리소스를 배포합니다. 기존 VNET을 사용하지 않고 이미지를 작성하는 경우에도 공용 IP 및 NSG가 배포됩니다. 서비스는 SSH 또는 WinRM을 사용하여 빌드 VM에 연결합니다. 기존 VNET을 선택하는 경우 서비스는 Azure 프라이빗 링크를 사용하여 배포되며, 공용 IP 주소는 필요하지 않습니다. Image Builder 네트워킹에 대한 자세한 내용은 [세부 정보](./linux/image-builder-networking.md)를 검토하세요.

빌드를 완료하면 준비 리소스 그룹 및 스토리지 계정을 제외한 모든 리소스가 삭제되고, 제거하려면 이미지 템플릿 리소스를 삭제하거나, 다시 빌드를 실행하기 위해 해당 리소스를 그대로 둘 수 있습니다.

이 설명서에는 [Azure Image Builder GitHub 리포지토리](https://github.com/azure/azvmimagebuilder) 구성 템플릿과 솔루션을 참조하는 여러 예제 및 단계별 가이드가 있습니다.

### <a name="move-support"></a>이동 지원
이미지 템플릿 리소스는 변경할 수 없으며, 리소스 및 준비 리소스 그룹에 대한 링크를 포함하므로 리소스 종류 이동을 지원하지 않습니다. 이미지 템플릿 리소스를 이동하려면 구성 템플릿의 복사본이 있는지 확인합니다(없는 경우 리소스에서 기존 구성 추출). 새 리소스 그룹에 새 이름으로 새 이미지 템플릿 리소스를 만들고 이전 이미지 템플릿 리소스를 삭제합니다. 

## <a name="permissions"></a>사용 권한
(AIB)에 등록하면 AIB 서비스에 스테이징 리소스 그룹(IT_*)을 만들고, 관리하고, 삭제할 수 있는 권한이 부여되며, 이미지 빌드에 필요한 리소스를 추가할 수 있는 권한이 부여됩니다. 성공적으로 등록하는 동안 구독에서 사용할 수 있는 AIB SPN(서비스 주체 이름)이 이 작업을 수행합니다.

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

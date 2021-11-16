---
title: Azure Automation State Configuration을 위해 STIG를 기준으로 데이터 구성
description: 이 문서에서는 Azure Automation State Configuration을 위해 DoD STIG를 기준으로 데이터를 구성하는 방법을 설명합니다.
keywords: dsc,powershell,configuration,setup
services: automation
ms.subservice: dsc
ms.date: 08/08/2019
ms.topic: conceptual
ms.openlocfilehash: ba875eb6fd132a6f5bcf916936545ac76c1656d3
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132488586"
---
# <a name="configure-data-based-on-security-technical-information-guide-stig"></a>STIG(보안 기술 구현 가이드)를 기반으로 데이터 구성

> 적용 대상: Windows PowerShell 5.1

처음으로 구성 콘텐츠를 만드는 것은 어려울 수 있습니다.
대부분의 경우 목표는 업계 권장 사항에 부합하는 "기준"에 따라 서버 구성을 자동화하는 것입니다.

> [!NOTE]
> 이 문서에서는 오픈 소스 커뮤니티에서 유지 관리하는 솔루션을 참조합니다.
> 지원은 Microsoft가 아닌 GitHub 협업 형태로만 제공됩니다.

## <a name="community-project-powerstig"></a>커뮤니티 프로젝트: PowerSTIG

[PowerSTIG](https://github.com/microsoft/powerstig)라는 커뮤니티 프로젝트는 STIG(보안 기술 구현 가이드)에 대해 제공된 [공개 정보](https://public.cyber.mil/stigs/)를 기반으로 DSC 콘텐츠를 생성하여 이 문제를 해결하는 것을 목표로 합니다.

기준에 대한 처리는 생각하는 것보다는 복잡합니다.
많은 조직이 규칙에 대한 [예외를 문서화](https://github.com/microsoft/powerstig#powerstigdata)하고 해당 데이터를 대규모로 관리해야 합니다.
PowerSTIG는 단일 파일에서 전체 설정 범위를 처리하는 대신 구성의 각 영역을 처리하는 [복합 리소스](https://github.com/microsoft/powerstig#powerstigdsc)를 제공하여 문제를 해결합니다.

구성이 생성되면 [DSC 구성 스크립트](/powershell/scripting/dsc/configurations/configurations)를 사용하여 MOF 파일을 생성하고 [MOF 파일을 Azure Automation에 업로드](./tutorial-configure-servers-desired-state.md#create-and-upload-a-configuration-to-azure-automation)할 있습니다.
그런 다음, [온-프레미스](./automation-dsc-onboarding.md#enable-physicalvirtual-linux-machines) 또는 [Azure](./automation-dsc-onboarding.md#enable-azure-vms)에서 서버를 등록하여 구성을 가져옵니다.

PowerSTIG를 사용해 보려면 [PowerShell 갤러리](https://www.powershellgallery.com)를 방문하여 솔루션을 다운로드하거나 "프로젝트 사이트"를 클릭하여 [설명서](https://github.com/microsoft/powerstig)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- PowerShell DSC를 이해하려면 [Windows PowerShell Desired State Configuration 개요](/powershell/scripting/dsc/overview/overview)를 참조하세요.
- [DSC 리소스](/powershell/scripting/dsc/resources/resources)에서 PowerShell DSC 리소스에 대해 알아봅니다.
- 로컬 Configuration Manager 구성에 대한 자세한 내용은 [로컬 Configuration Manager 구성](/powershell/scripting/dsc/managing-nodes/metaconfig)을 참조하세요.

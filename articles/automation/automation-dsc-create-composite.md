---
title: Azure Automation State Configuration을 위해 구성을 복합 리소스로 변환
description: 이 문서에서는 Azure Automation State Configuration을 위해 구성을 복합 리소스로 변환하는 방법에 대해 설명합니다.
keywords: dsc,powershell,configuration,setup
services: automation
ms.subservice: dsc
ms.date: 08/08/2019
ms.topic: conceptual
ms.openlocfilehash: 5c3fc5ece7eac812bdf60a310e7777ea411ab6b6
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132493460"
---
# <a name="convert-configurations-to-composite-resources"></a>구성을 복합 리소스로 변환

> 적용 대상: Windows PowerShell 5.1

구성 작성을 시작하면 설정 그룹을 관리하는 "시나리오"를 빠르게 만들 수 있습니다.
예제는 다음과 같습니다.

- 웹 서버 만들기
- DNS 서버 만들기
- SharePoint를 실행하는 서버 만들기
- SQL 클러스터 구성
- 방화벽 설정 관리
- 암호 설정 관리

이 작업을 다른 사용자와 공유하려는 경우 최상의 옵션은 구성을 [복합 리소스](/powershell/scripting/dsc/resources/authoringresourcecomposite)로 패키징하는 것입니다.
처음으로 복합 리소스를 만드는 것은 매우 어려울 수 있습니다.

> [!NOTE]
> 이 문서는 오픈 소스 커뮤니티에서 유지 관리하는 솔루션을 의미합니다.
> 지원은 Microsoft가 아닌 GitHub 협업 형태로만 제공됩니다.

## <a name="community-project-compositeresource"></a>커뮤니티 프로젝트: CompositeResource

이 과제를 해결하기 위해 [CompositeResource](https://github.com/microsoft/compositeresource)라는 커뮤니티 유지 관리 솔루션이 만들어졌습니다.

CompositeResource는 구성에서 새 모듈을 만드는 프로세스를 자동화합니다.
워크스테이션(또는 빌드 서버)에서 구성 스크립트를 [도트 소싱](https://devblogs.microsoft.com/scripting/how-to-reuse-windows-powershell-functions-in-scripts/)으로 시작하여 메모리에 로드되도록 합니다.
그런 다음, 구성을 실행하여 MOF 파일을 생성하는 대신 CompositeResource 모듈에서 제공하는 함수를 사용하여 변환을 자동화합니다.
cmdlet은 구성 콘텐츠를 로드하고, 매개 변수 목록을 가져오고, 필요한 모든 항목을 포함하는 새 모듈을 생성합니다.

모듈을 생성한 후에는 버전을 증분하고 변경할 때마다 릴리스 정보를 추가하여 [PowerShellGet 리포지토리](https://powershellexplained.com/2018-03-03-Powershell-Using-a-NuGet-server-for-a-PSRepository/?utm_source=blog&utm_medium=blog&utm_content=psscriptrepo)에 게시할 수 있습니다.

구성(또는 여러 구성)을 포함하는 복합 리소스 모듈을 만든 후에는 Azure의 [구성 가능한 작성 환경](./compose-configurationwithcompositeresources.md)에서 사용하거나 [DSC 구성 스크립트](/powershell/scripting/dsc/configurations/configurations)에 추가하여 MOF 파일을 생성하고 [MOF 파일을 Azure Automation에 업로드](./tutorial-configure-servers-desired-state.md#create-and-upload-a-configuration-to-azure-automation)할 수 있습니다.
그런 다음, [온-프레미스](./automation-dsc-onboarding.md#enable-physicalvirtual-linux-machines) 또는 [Azure](./automation-dsc-onboarding.md#enable-azure-vms)에서 서버를 등록하여 구성을 가져옵니다.
프로젝트에 대한 최신 업데이트는 PowerShell 갤러리에서 구성을 가져오는 프로세스를 자동화하는 Azure Automation용 [Runbook](https://www.powershellgallery.com/packages?q=DscGallerySamples)도 게시했습니다.

DSC에 대한 복합 리소스 생성을 자동화하려면 [PowerShell 갤러리](https://www.powershellgallery.com/packages/compositeresource/)를 방문하여 솔루션을 다운로드하거나 "프로젝트 사이트"를 클릭하여 [설명서](https://github.com/microsoft/compositeresource)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- PowerShell DSC를 이해하려면 [Windows PowerShell Desired State Configuration 개요](/powershell/scripting/dsc/overview/overview)를 참조하세요.
- [DSC 리소스](/powershell/scripting/dsc/resources/resources)에서 PowerShell DSC 리소스에 대해 알아봅니다.
- 로컬 Configuration Manager 구성에 대한 자세한 내용은 [로컬 Configuration Manager 구성](/powershell/scripting/dsc/managing-nodes/metaconfig)을 참조하세요.

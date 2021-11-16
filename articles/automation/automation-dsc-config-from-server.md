---
title: Azure Automation 상태 구성을 위해 기존 서버에서 구성 만들기
description: 이 문서에서는 Azure Automation 상태 구성을 위해 기존 서버에서 구성을 만드는 방법을 설명합니다.
keywords: dsc,powershell,configuration,setup
services: automation
ms.subservice: dsc
ms.date: 08/08/2019
ms.topic: conceptual
ms.openlocfilehash: 0240fd14a8e5dd5975fd499839e10caa0756953f
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132491510"
---
# <a name="create-configurations-from-existing-servers"></a>기존 서버에서 구성 만들기

> 적용 대상: Windows PowerShell 5.1

기존 서버에서 구성을 만드는 작업은 어려울 수 있습니다.
*모든* 설정이 아니라 원하는 것만 설정할 수 있습니다.
이 경우에도 구성을 성공적으로 적용하려면 설정을 적용해야 하는 순서를 알아야 합니다.

> [!NOTE]
> 이 문서는 오픈 소스 커뮤니티에서 유지 관리하는 솔루션을 의미합니다.
> 지원은 Microsoft가 아닌 GitHub 협업 형태로만 제공됩니다.

## <a name="community-project-reversedsc"></a>커뮤니티 프로젝트: ReverseDSC

[ReverseDSC](https://github.com/microsoft/reversedsc)라는 커뮤니티 유지 관리 솔루션은 SharePoint를 시작하는 이 영역에서 작동하도록 만들어졌습니다.

이 솔루션은 [SharePointDSC 리소스](https://github.com/powershell/sharepointdsc)를 기반으로 하며 SharePoint를 실행하는 기존 서버에서 [정보 수집](https://github.com/Microsoft/sharepointDSC.reverse#how-to-use)을 오케스트레이션하도록 확장됩니다.
최신 버전에는 포함할 정보 수준을 결정하는 여러 [추출 모드](https://github.com/Microsoft/SharePointDSC.Reverse/wiki/Extraction-Modes)가 있습니다.

솔루션을 사용한 결과로 SharePointDSC 구성 스크립트와 함께 사용할 [구성 데이터](https://github.com/Microsoft/sharepointDSC.reverse#configuration-data)가 생성됩니다.

데이터 파일이 생성되면 [DSC 구성 스크립트](/powershell/scripting/dsc/overview/overview)와 함께 이 파일을 사용하여 MOF 파일을 생성하고 [MOF 파일을 Azure Automation에 업로드](./tutorial-configure-servers-desired-state.md#create-and-upload-a-configuration-to-azure-automation)할 수 있습니다.
그런 다음, [온-프레미스](./automation-dsc-onboarding.md#enable-physicalvirtual-linux-machines) 또는 [Azure](./automation-dsc-onboarding.md#enable-azure-vms)에서 서버를 등록하여 구성을 가져옵니다.

ReverseDSC를 사용해 보려면 [PowerShell 갤러리](https://www.powershellgallery.com/packages/ReverseDSC/)를 방문하여 솔루션을 다운로드하거나 "프로젝트 사이트"를 클릭하여 [설명서](https://github.com/Microsoft/sharepointDSC.reverse)를 참조하세요.

## <a name="next-steps"></a>다음 단계

- PowerShell DSC를 이해하려면 [Windows PowerShell Desired State Configuration 개요](/powershell/scripting/dsc/overview/overview)를 참조하세요.
- [DSC 리소스](/powershell/scripting/dsc/resources/resources)에서 PowerShell DSC 리소스에 대해 알아봅니다.
- 로컬 Configuration Manager 구성에 대한 자세한 내용은 [로컬 Configuration Manager 구성](/powershell/scripting/dsc/managing-nodes/metaconfig)을 참조하세요.

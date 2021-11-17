---
title: Azure Automation State Configuration을 위해 대규모로 데이터 구성
description: 이 문서에서는 Azure Automation State Configuration을 위해 대규모로 데이터를 구성하는 방법을 설명합니다.
keywords: dsc,powershell,configuration,setup
services: automation
ms.subservice: dsc
ms.date: 08/08/2019
ms.topic: conceptual
ms.openlocfilehash: 4dd1c7eceb86d1153d08414a5c81ed09b395916c
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/15/2021
ms.locfileid: "132484535"
---
# <a name="configure-data-at-scale-for-azure-automation-state-configuration"></a>Azure Automation State Configuration을 위해 대규모로 데이터 구성

> 적용 대상: Windows PowerShell 5.1

수백 또는 수천 대의 서버를 관리하는 것은 어려운 과제일 수 있습니다.
고객이 가장 어려운 점은 실제로 [구성 데이터](/powershell/scripting/dsc/configurations/configdata)를 관리하는 것이라는 피드백을 제공했습니다.
위치, 형식, 환경과 같은 논리적 구문으로 정보를 구성합니다.

> [!NOTE]
> 이 문서에서는 오픈 소스 커뮤니티에서 유지 관리하는 솔루션을 의미합니다.
> 지원은 Microsoft가 아닌 GitHub 협업 형태로만 제공됩니다.

## <a name="community-project-datum"></a>커뮤니티 프로젝트: Datum

이 과제를 해결하기 위해 [Datum](https://github.com/gaelcolas/Datum)이라는 커뮤니티 유지 관리 솔루션이 만들어졌습니다.
Datum은 다른 구성 관리 플랫폼의 뛰어난 아이디어를 기반으로 하며 PowerShell DSC에 대해 동일한 유형의 솔루션을 구현합니다.
정보는 논리적 아이디어를 기반으로 [텍스트 파일로 구성](https://github.com/gaelcolas/Datum#3-intended-usage)됩니다.
예제는 다음과 같습니다.

- 전역적으로 적용해야 하는 설정
- 한 위치의 모든 서버에 적용해야 하는 설정
- 모든 데이터베이스 서버에 적용해야 하는 설정
- 개별 서버 설정

이 정보는 원하는 파일 형식(JSON, Yaml 또는 PSD1)으로 구성됩니다.
그런 다음, 각 파일에서 서버 또는 서버 역할의 단일 보기로 [정보를 통합](https://github.com/gaelcolas/Datum#datum-tree)하여 구성 데이터 파일을 생성하는 cmdlet이 제공됩니다.

데이터 파일이 생성되면 [DSC 구성 스크립트](/powershell/scripting/dsc/configurations/write-compile-apply-configuration)와 함께 이 파일을 사용하여 MOF 파일을 생성하고 [MOF 파일을 Azure Automation에 업로드](./tutorial-configure-servers-desired-state.md#create-and-upload-a-configuration-to-azure-automation)할 수 있습니다.
그런 다음, [온-프레미스](./automation-dsc-onboarding.md#enable-physicalvirtual-linux-machines) 또는 [Azure](./automation-dsc-onboarding.md#enable-azure-vms)에서 서버를 등록하여 구성을 가져옵니다.

Datum을 사용해 보려면 [PowerShell 갤러리](https://www.powershellgallery.com/packages/datum/)를 방문하여 솔루션을 다운로드하거나 "프로젝트 사이트"를 클릭하여 [설명서](https://github.com/gaelcolas/Datum#2-getting-started--concepts)를 확인하세요.

## <a name="next-steps"></a>다음 단계

- PowerShell DSC를 이해하려면 [Windows PowerShell Desired State Configuration 개요](/powershell/scripting/dsc/overview/overview)를 참조하세요.
- [DSC 리소스](/powershell/scripting/dsc/resources/resources)에서 PowerShell DSC 리소스에 대해 알아봅니다.
- 로컬 Configuration Manager 구성에 대한 자세한 내용은 [로컬 Configuration Manager 구성](/powershell/scripting/dsc/managing-nodes/metaconfig)을 참조하세요.

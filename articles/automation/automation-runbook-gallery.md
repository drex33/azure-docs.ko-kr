---
title: PowerShell 갤러리에서 Azure Automation Runbook 및 모듈 사용
description: 이 문서에서는 Microsoft GitHub 리포지토리 및 PowerShell 갤러리에서 Runbook 및 모듈을 사용하는 방법을 설명합니다.
services: automation
ms.subservice: process-automation
ms.date: 04/07/2021
ms.topic: conceptual
ms.openlocfilehash: 1f7a3f31ed67817252f7ba33b9f4a83ef8ccd5c6
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114455625"
---
# <a name="use-existing-runbooks-and-modules"></a>기존 Runbook 및 모듈 사용

Azure Automation에서 사용자 고유의 Runbook 및 모듈을 만드는 대신 Microsoft 및 커뮤니티에서 이미 빌드한 시나리오에 액세스할 수 있습니다. Azure Portal의 Runbook 갤러리에서 Azure 관련 PowerShell 및 Python Runbook을 가져오고, PowerShell 갤러리에서 [모듈](#modules-in-the-powershell-gallery) 및 [Runbook](#runbooks-in-the-powershell-gallery)(Azure 관련 또는 비관련)을 가져올 수 있습니다. [자신이 개발한 시나리오를 공유](#contribute-to-the-community)하여 커뮤니티에 기여할 수도 있습니다.

> [!NOTE]
> TechNet 스크립트 센터는 사용이 중단됩니다. Runbook 갤러리에 있는 스크립트 센터의 모든 RunbooK은 [Automation GitHub 조직](https://github.com/azureautomation)으로 이동되었습니다. 자세한 내용은 [GitHub로 Azure Automation Runbook 이동](https://techcommunity.microsoft.com/t5/azure-governance-and-management/azure-automation-runbooks-moving-to-github/ba-p/2039337)을 참조하세요.

## <a name="import-runbooks-from-github-with-the-azure-portal"></a>Azure Portal을 사용하여 GitHub에서 Runbook 가져오기

1. Azure Portal에서 Automation 계정을 엽니다.
2. **프로세스 자동화** 에서 **Runbook 갤러리** 를 선택합니다.
3. **원본: GitHub** 를 선택합니다.
4. 목록 위의 필터를 사용하여 게시자, 유형 및 정렬별로 표시 범위를 좁힐 수 있습니다. 원하는 갤러리 항목을 찾아 선택하여 세부 정보를 확인합니다.

   :::image type="content" source="./media/automation-runbook-gallery/browse-gallery-github.png" alt-text="Runbook 갤러리 찾아보기." lightbox="./media/automation-runbook-gallery/browse-gallery-github-expanded.png":::

5. 항목을 가져오려면 세부 정보 페이지에서 **가져오기** 를 클릭합니다.

   :::image type="content" source="./media/automation-runbook-gallery/gallery-item-import.png" alt-text="갤러리 항목 가져오기.":::

6. 선택적으로 가져오기 블레이드에서 Runbook 이름을 변경한 후 **확인** 을 클릭하여 Runbook을 가져옵니다.

   :::image type="content" source="./media/automation-runbook-gallery/gallery-item-import-blade.png" alt-text="갤러리 항목 가져오기 블레이드.":::

7. 이 Runbook은 Automation 계정의 **Runbook** 탭에 표시됩니다.
 
## <a name="runbooks-in-the-powershell-gallery"></a>PowerShell 갤러리의 Runbook

> [!IMPORTANT]
> PowerShell 갤러리에서 가져오는 모든 Runbook의 콘텐츠에 대해 유효성을 검사해야 합니다. 프로덕션 환경에서 이를 설치하고 실행할 때는 특히 주의해야 합니다.

[PowerShell 갤러리](https://www.powershellgallery.com/packages)는 Microsoft 및 커뮤니티에서 Azure Automation으로 가져올 수 있는 다양한 Runbook을 제공합니다. Runbook을 사용하려면 갤러리에서 Runbook을 다운로드하거나, Azure Portal을 사용하여 갤러리에서 또는 Automation 계정에서 Runbook을 직접 가져올 수 있습니다.

> [!NOTE]
> 그래픽 Runbook은 PowerShell 갤러리에서 지원되지 않습니다.

PowerShell 갤러리에서 직접 가져오는 것은 Azure Portal을 사용하는 경우에만 가능하며, PowerShell을 사용하여 직접 가져올 수 없습니다. 이 절차는 [Azure Portal을 사용하여 GitHub에서 Runbook 가져오기](#import-runbooks-from-github-with-the-azure-portal)에 표시된 것과 동일합니다. 단, **원본** 이 **PowerShell 갤러리** 입니다.

:::image type="content" source="./media/automation-runbook-gallery/source-runbook-gallery-small.png" alt-text="Runbook 갤러리 원본 선택 표시." lightbox="./media/automation-runbook-gallery/source-runbook-gallery-large.png":::

## <a name="modules-in-the-powershell-gallery"></a>PowerShell 갤러리의 모듈

PowerShell 모듈에는 Runbook에서 사용할 수 있는 cmdlet이 포함되어 있습니다. Azure Automation에 설치할 수 있는 기존 모듈은 [PowerShell 갤러리](https://www.powershellgallery.com)에서 사용할 수 있습니다. Azure Portal에서 이 갤러리를 시작하여 모듈을 Azure Automation에 직접 설치하거나, 수동으로 다운로드하여 설치할 수 있습니다.

또한 Azure Portal에서 가져올 모듈을 찾을 수 있습니다. 이것들은 **공유 리소스** 아래의 **모듈 갤러리** 에서 해당 Automation 계정에 대해 나열되어 있습니다.

> [!IMPORTANT] 
> Az 모듈을 통해 실행되도록 설계된 스크립트에는 "AzureRm" 키워드를 포함하지 마십시오. 주석에도 키워드를 포함하면 AzureRm이 로드된 다음, Az 모듈과 충돌할 수 있습니다.

## <a name="common-scenarios-available-in-the-powershell-gallery"></a>PowerShell 갤러리에서 사용 가능한 일반 시나리오

아래 목록에는 일반적인 시나리오를 지원하는 몇 가지 Runbook이 포함되어 있습니다. Azure Automation 팀에서 만든 Runbook의 전체 목록은 [AzureAutomationTeam 프로필](https://www.powershellgallery.com/profiles/AzureAutomationTeam)을 참조하세요.

   * [Update-ModulesInAutomationToLatestVersion](https://www.powershellgallery.com/packages/Update-ModulesInAutomationToLatestVersion/) - PowerShell 갤러리에서 Automation 계정에 있는 모든 모듈의 최신 버전을 가져옵니다.
   * [Enable-AzureDiagnostics](https://www.powershellgallery.com/packages/Enable-AzureDiagnostics/) - Azure Diagnostics 및 Log Analytics가 작업 상태와 작업 스트림을 포함하는 Azure Automation 로그를 받도록 구성합니다.
   * [Copy-ItemFromAzureVM](https://www.powershellgallery.com/packages/Copy-ItemFromAzureVM/) - Microsoft Azure 가상 머신에서 원격 파일을 복사합니다.
   * [Copy-ItemToAzureVM](https://www.powershellgallery.com/packages/Copy-ItemToAzureVM/) - Azure 가상 머신에 로컬 파일을 복사합니다.

## <a name="contribute-to-the-community"></a>커뮤니티에 참여

Azure Automation 커뮤니티에 참여하고 커뮤니티 성장에 도움을 주시기 바랍니다. 귀하가 만든 놀라운 Runbook을 커뮤니티에 공유해주세요. 많은 참여를 부탁드립니다!

### <a name="add-a-runbook-to-the-github-runbook-gallery"></a>GitHub Runbook 갤러리에 Runbook 추가

이 GitHub 워크플로를 사용하여 Runbook 갤러리에 새로운 PowerShell 또는 Python Runbook을 추가할 수 있습니다.

1. GitHub에서 공용 리포지토리를 만들고, Runbook 및 기타 모든 필수 파일(예: readme.md, 설명 등)을 추가하세요.
1. 리포지토리가 서비스에서 검색되도록 `azureautomationrunbookgallery` 토픽을 추가하여, Automation Runbook 갤러리에 표시될 수 있도록 하세요.
1. 사용자가 만든 Runbook이 PowerShell 워크플로인 경우에는 `PowerShellWorkflow` 토픽을 추가합니다. Python 3 Runbook의 경우에는`Python3`를 추가합니다. Azure Runbook의 경우 다른 특정 토픽이 필요하지 않지만 Runbook 갤러리에서 분류 및 검색 목적으로 사용될 수 있는 다른 토픽들을 추가하는 것이 좋습니다.

   >[!NOTE]
   >갤러리의 기존 Runbook에서 형식 지정, 헤더, 사용될 수 있는 기존 태그(예: `Azure Automation` 또는 `Linux Azure Virtual Machines`)와 같은 것들을 확인하세요.

기존 Runbook에 변경사항을 제안하려면 끌어오기 요청을 등록하세요. 

기존 Runbook을 복제하고 편집해야 할 경우 가장 좋은 방법은 이를 다른 이름으로 저장하는 것입니다. 이전 이름을 다시 사용하면 Runbook 갤러리 목록에 두 번 표시됩니다.

>[!NOTE]
>GitHub와 Automation Runbook 갤러리 사이에 업데이트된 내용과 신규 Runbook이 반영되기 위해서는 최소 12시간 이상의 동기화 시간이 필요합니다.

### <a name="add-a-powershell-runbook-to-the-powershell-gallery"></a>PowerShell 갤러리에 PowerShell Runbook 추가

Microsoft에서는 다른 고객에게 유용하다고 생각하는 Runbook을 PowerShell 갤러리에 추가하도록 장려하고 있습니다. PowerShell 갤러리에서는 PowerShell 모듈 및 PowerShell 스크립트를 게시할 수 있습니다. Runbook을 [PowerShell 갤러리에 업로드](/powershell/scripting/gallery/how-to/publishing-packages/publishing-a-package)하여 추가할 수 있습니다.

## <a name="import-a-module-from-the-modules-gallery-in-the-azure-portal"></a>Azure Portal을 사용하여 모듈 갤러리에서 모듈 가져오기

1. Azure Portal에서 Automation 계정을 엽니다.
1. **공유 리소스** 아래에서 **모듈 갤러리** 를 선택하여 모듈 목록을 엽니다.

      :::image type="content" source="media/automation-runbook-gallery/modules-blade-sm.png" alt-text="모듈 갤러리의 보기." lightbox="media/automation-runbook-gallery/modules-blade-lg.png":::

1. 갤러리 찾아보기 페이지에서 다음 필드로 검색할 수 있습니다.

   * 모듈 이름
   * 태그들
   * 작성자
   * Cmdlet/DSC 리소스 이름

1. 관심이 있는 모듈을 찾아 선택하여 세부 내용을 확인합니다.

   특정 모듈로 드릴하면 자세한 정보를 확인할 수 있습니다. 이 정보에는 PowerShell 갤러리로 돌아가는 링크, 필요한 종속성 및 모듈에 속하는 모든 cmdlet 또는 DSC 리소스가 포함됩니다.

   :::image type="content" source="media/automation-runbook-gallery/gallery-item-details-blade-sm.png" alt-text="갤러리의 모듈 상세 보기." lightbox="media/automation-runbook-gallery/gallery-item-details-blade-lg.png":::

1. Azure Automation에 직접 모듈을 설치하려면 **가져오기** 를 클릭합니다.
1. 가져오기 창에서 가져올 모듈의 이름을 확인할 수 있습니다. 모든 종속성이 설치되면 **확인** 단추가 활성화됩니다. 종속성이 없는 경우 해당 종속성을 가져와야만 이 모듈을 가져올 수 있습니다.
1. 가져오기 창에서 **확인** 을 클릭하여 모듈을 가져옵니다. Azure Automation에서 모듈을 계정에 가져오는 동안 모듈 및 cmdlet에 대한 메타데이터를 추출합니다. 각 활동을 추출해야 하므로 이 작업에는 몇 분 정도 걸릴 수 있습니다.
1. 모듈이 배포 중임을 알리는 초기 알림 및 완료 시의 다른 알림이 표시됩니다.
1. 모듈 가져오기가 완료되면 사용 가능한 활동을 볼 수 있습니다. Runbook 및 DSC 리소스에서 모듈 리소스를 사용할 수 있습니다.

> [!NOTE]
> PowerShell 코어만 지원하는 모듈은 Azure Automation에서 지원되지 않으며, Azure Portal에서 가져오거나 PowerShell 갤러리에서 직접 배포할 수 없습니다.

## <a name="request-a-runbook-or-module"></a>Runbook 또는 모듈 요청

[사용자 음성](https://feedback.azure.com/forums/246290-azure-automation/)에 요청을 보낼 수 있습니다.  Runbook을 작성하는 데 도움이 필요하거나 PowerShell에 대한 질문이 있으면 [Microsoft Q&A 질문 페이지](/answers/topics/azure-automation.html)에 질문을 게시하세요.

## <a name="next-steps"></a>다음 단계

* PowerShell Runbook을 시작하려면 [자습서: PowerShell Runbook 만들기](learn/automation-tutorial-runbook-textual-powershell.md)를 참조하세요.
* Runbook을 사용하려면 [Azure Automation에서 Runbook 관리](manage-runbooks.md)를 참조하세요.
* PowerShell 스크립팅에 대한 자세한 내용은 [PowerShell 문서](/powershell/scripting/overview)를 참조하세요.
* PowerShell cmdlet 참조는 [Az.Automation](/powershell/module/az.automation)을 참조하세요.

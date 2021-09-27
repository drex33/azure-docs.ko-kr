---
title: Azure DevTest Labs 가상 머신에서 아티팩트 실패 진단
description: DevTest Labs는 아티팩트 실패를 진단하는 데 사용할 수 있는 정보를 제공합니다. 이 문서에서는 아티팩트 실패 문제를 해결하는 방법을 안내합니다.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: e7c343eee8c3484e12139c2bef963fc463ffc28d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128567452"
---
# <a name="diagnose-artifact-failures-in-the-lab"></a>랩에서 아티팩트 실패 진단 
아티팩트를 만든 후에 성공 또는 실패 여부를 확인할 수 있습니다. Azure DevTest Labs의 아티팩트 로그는 아티팩트 실패를 진단하는 데 사용할 수 있는 정보를 제공합니다. Windows VM에 대한 아티팩트 로그 정보를 보기 위한 두 가지 옵션이 있습니다.

* Azure Portal에서
* VM에서

> [!NOTE]
> 실패가 올바르게 식별되고 설명되었는지 확인하려면 아티팩트가 제대로 구성되는 것이 중요합니다. 아티팩트를 올바르게 만드는 방법에 대한 정보는 [사용자 지정 아티팩트 만들기](devtest-lab-artifact-author.md)를 참조하세요. 올바르게 구조화된 아티팩트의 예를 보려면 [테스트 매개 변수 형식](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-test-paramtypes) 아티팩트를 확인하세요.

## <a name="troubleshoot-artifact-failures-by-using-the-azure-portal"></a>Azure Portal을 사용하여 아티팩트 실패 문제 해결

1. Azure Portal의 리소스 목록에서 랩을 선택합니다.
2. 조사하려는 아티팩트를 포함하는 Windows VM을 선택합니다.
3. 왼쪽 패널의 **일반** 아래에서 **아티팩트** 를 선택합니다. 해당 VM과 연결된 아티팩트 목록이 표시됩니다. 아티팩트 이름 및 아티팩트 상태가 표시됩니다.

   ![아티팩트 상태](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifacts-failure-new.png)

4. **실패** 상태를 보여 주는 아티팩트를 선택합니다. 아티팩트가 열립니다. 아티팩트의 실패에 대한 세부 정보를 포함하는 확장 메시지가 표시됩니다.

   ![아티팩트 오류 메시지](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error.png)


## <a name="troubleshoot-artifact-failures-from-within-the-virtual-machine"></a>가상 머신 내에서 아티팩트 실패 문제 해결

1. 진단하려는 아티팩트를 포함하는 VM에 로그인합니다.
2. C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\\*1.9*\Status로 이동합니다. 여기서 *1.9* 는 Azure Custom Script Extension 버전 번호입니다.

   ![상태 파일](./media/devtest-lab-troubleshoot-artifact-failure/devtest-lab-artifact-error-vm-status-new.png)

3. **상태** 파일을 엽니다.

**Linux** VM에서 로그 파일을 찾는 방법에 대한 지침은 다음 문서를 참조하세요: [Linux 가상 머신에서 Azure 사용자 지정 스크립트 확장 버전 2 사용](../virtual-machines/extensions/custom-script-linux.md#troubleshooting)


## <a name="related-blog-posts"></a>관련 블로그 게시물
* [DevTest Labs에서 Resource Manager 템플릿을 사용하여 기존 Active Directory 도메인에 VM 가입](https://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>다음 단계
* [랩에 Git 리포지토리를 추가](devtest-lab-add-artifact-repo.md)하는 방법에 대해 알아봅니다.

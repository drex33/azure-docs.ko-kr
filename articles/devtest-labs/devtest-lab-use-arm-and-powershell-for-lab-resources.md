---
title: Azure Resource Manager 템플릿을 사용하여 랩 만들기 또는 수정
description: PowerShell에서 Azure Resource Manager 템플릿을 사용 하 여 랩을 자동으로 만들거나 수정 하는 방법에 대해 알아봅니다.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 61f605e5b6c65c57caf37bdc5178143e0304f02d
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132398538"
---
# <a name="create-or-modify-labs-automatically-using-azure-resource-manager-templates-and-powershell"></a>Azure Resource Manager 템플릿 및 PowerShell을 사용하여 랩 자동 생성 또는 수정

Azure DevTest Labs에서는 많은 Azure Resource Manager 템플릿과 PowerShell 스크립트를 제공 합니다. 이러한 템플릿과 스크립트를 통해 실습 및 랩 리소스를 신속 하 고 자동으로 생성, 수정 및 배포할 수 있습니다.

이 문서는 이러한 템플릿과 스크립트를 사용하여 랩의 생성, 수정, 배포를 자동화하는 프로세스를 안내합니다. 이 문서는 PowerShell을 사용하여 DevTest Labs에서 몇 가지 일반적인 작업을 수행하는 방법에 대한 자세한 정보를 찾을 수 있는 위치를 안내합니다.

## <a name="step-1-gather-your-templates-and-scripts"></a>1단계: 템플릿 및 스크립트 수집
공용 [GitHub 리포지토리](https://github.com/Azure/azure-devtestlab)에서 미리 만들어진 [Azure Resource Manager 템플릿](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/QuickStartTemplates) 및 [PowerShell 스크립트](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts)를 찾을 수 있습니다. 그대로 사용하거나 요구 사항에 맞게 사용자 지정하고 [프라이빗 Git 리포지토리](devtest-lab-add-artifact-repo.md)에 저장합니다.

## <a name="step-2-modify-your-azure-resource-manager-template"></a>2단계: Azure Resource Manager 템플릿 수정
이전에 템플릿을 만든 적이 없는 경우 [첫 번째 Azure Resource Manager 템플릿 만들기](../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) 의 단계를 따를 수 있습니다.

[Azure Resource Manager 템플릿 작성에 대한 모범 사례](../azure-resource-manager/templates/best-practices.md)에 안정적이고 사용하기 쉬운 Azure Resource Manager 템플릿을 만드는 데 도움이 되는 다양한 지침과 제안이 나와 있습니다. 제공 된 방법 중 하나를 사용 하 여 요구에 맞게 템플릿을 수정할 수 있습니다.

## <a name="step-3-deploy-resources-with-powershell"></a>3단계: PowerShell로 리소스 배포
템플릿과 스크립트를 사용자 지정한 후 [리소스 관리자 템플릿 및 Azure PowerShell를 사용 하 여 리소스를 배포](../azure-resource-manager/templates/deploy-powershell.md)하는 데 필요한 단계를 수행 합니다. 이 문서는 Azure Resource Manager 템플릿과 Azure PowerShell을 사용하여 Azure에 리소스를 배포하는 방법에 대한 일반 정보를 제공합니다.


## <a name="common-tasks-you-can-perform-in-devtest-labs-using-powershell"></a>PowerShell을 사용 하 여 DevTest Labs에서 수행할 수 있는 일반적인 작업
PowerShell을 사용하여 자동화할 수 있는 다양한 일반 작업이 있습니다. 다음 섹션에서는 이러한 작업을 수행하는 데 필요한 단계에 대해 간단히 설명합니다.

* [PowerShell을 사용하여 VHD 파일에서 사용자 지정 이미지 만들기](devtest-lab-create-custom-image-from-vhd-using-powershell.md)
* [PowerShell을 사용하여 랩의 스토리지 계정에 VHD 파일 업로드](devtest-lab-upload-vhd-using-powershell.md)
* [PowerShell을 사용하여 랩에 외부 사용자 추가](devtest-lab-add-devtest-user.md#add-an-external-user-to-a-lab-using-powershell)
* [PowerShell을 사용하여 랩 사용자 지정 역할 만들기](devtest-lab-grant-user-permissions-to-specific-lab-policies.md#creating-a-lab-custom-role-using-powershell)

### <a name="next-steps"></a>다음 단계
* 사용자 지정한 템플릿 또는 스크립트를 저장할 수 있는 [프라이빗 Git 리포지토리](devtest-lab-add-artifact-repo.md)를 만드는 방법에 대해 배웁니다.
* [Azure 빠른 시작 템플릿 갤러리의 Azure Resource Manager 템플릿](https://github.com/Azure/azure-quickstart-templates)(영문)을 탐색합니다.
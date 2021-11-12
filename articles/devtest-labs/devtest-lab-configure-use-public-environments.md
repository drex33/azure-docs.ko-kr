---
title: 공용 환경 구성 및 사용
description: 이 문서에서는 Azure DevTest Labs에서 공용 환경(Git 리포지토리의 Azure Resource Manager 템플릿)을 구성하고 사용하는 방법을 설명합니다.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 3570f88ae8fe88740721b04783a8689e22c7bb7d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132286377"
---
# <a name="configure-and-use-public-environments-in-azure-devtest-labs"></a>Azure DevTest Labs에서 공용 환경 구성 및 사용
Azure DevTest Labs 환경을 만드는 데 사용할 수 [있는 Azure Resource Manager 템플릿의 공용 리포지토리가](https://github.com/Azure/azure-devtestlab/tree/master/Environments) 있습니다. 외부 GitHub 원본에 직접 연결할 필요가 없습니다. 이 리포지토리에는 Azure Web Apps, Service Fabric 클러스터 및 개발 SharePoint 팜과 같이 자주 사용되는 템플릿이 포함되어 있습니다. 이 기능은 사용자가 만든 모든 랩에 포함된 아티팩트의 공용 리포지토리와 비슷합니다. 환경 리포지토리는 미리 작성된 환경 템플릿에 최소 입력 매개 변수를 제공합니다. 템플릿을 사용하면 랩 내에서 PaaS(Platform as a Service) 리소스를 원활하게 시작할 수 있습니다.
  
## <a name="configuring-public-environments"></a>공용 환경 구성
랩 소유자는 랩을 만드는 동안 랩의 공용 환경 리포지토리를 활성화할 수 있습니다. 랩의 공용 환경을 활성화하려면 랩을 만들 때 **공용 환경** 필드에 **켜기** 를 선택합니다. 

![새 랩에 공용 환경 활성화](media/devtest-lab-configure-use-public-environments/enable-public-environment-new-lab.png)

기존 랩의 경우 공용 환경 리포지토리를 사용할 수 없습니다. 수동으로 리포지토리에서 템플릿을 사용하도록 설정합니다. Resource Manager 템플릿을 사용하여 만든 랩의 경우 리포지토리도 기본적으로 사용하지 않도록 설정됩니다.

랩에 대해 공용 환경을 사용하거나 사용하지 않도록 설정할 수 있으며, 다음 단계를 사용하여 랩 사용자가 특정 환경만 사용할 수 있도록 할 수도 있습니다. 

1. 랩의 **구성 및 정책** 을 선택합니다. 
2. 가상 **머신 기본** 섹션에서 **공용 환경 을 선택합니다.**
3. 랩에 공용 환경을 활성화하려면 **예** 를 선택합니다. 그렇지 않은 경우 **아니요** 를 선택합니다. 
4. 공용 환경을 사용하도록 설정한 경우 리포지토리의 모든 환경이 기본적으로 사용하도록 설정됩니다. 랩 사용자가 사용할 수 없도록 환경을 선택 취소할 수 있습니다. 

![공용 환경 페이지를 보여 주는 스크린샷.](media/devtest-lab-configure-use-public-environments/public-environments-page.png)

## <a name="use-environment-templates-as-a-lab-user"></a>랩 사용자 권한으로 환경 템플릿 사용
랩 사용자는 랩 페이지의 도구 모음에서 **추가를** 선택하여 활성화된 환경 템플릿 목록에서 새 환경을 만들 수 있습니다. 베이스 목록에는 랩 관리자가 목록 상단에 활성화한 공용 환경 템플릿이 포함되어 있습니다.

![공용 환경 템플릿을 보여 주는 스크린샷](media/devtest-lab-configure-use-public-environments/public-environment-templates.png)

## <a name="next-steps"></a>다음 단계
이 리포지토리는 기여할 수 있는 오픈 소스 리포지토리입니다. 자주 사용되며 유용한 Resource Manager 템플릿을 추가하려면 리포지토리에 대해 끌어오기 요청을 제출합니다.

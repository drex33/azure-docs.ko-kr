---
title: 보존 정책 설정
description: 보존 정책을 구성하고 센터를 정리하며 DevTest Labs의 이전 이미지를 사용 중지하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 4cb4a343aacda48791293507a95c4c9dfa0f5f47
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132397664"
---
# <a name="set-up-retention-policy-in-azure-devtest-labs"></a>Azure DevTest Labs에서 보존 정책 설정
이 문서에서는 보존 정책 설정, 센터 정리, 조직의 다른 모든 DevTest Labs의 이전 이미지를 사용 중지하는 데 관해 다룹니다. 

## <a name="prerequisites"></a>필수 구성 요소
계속 진행하기 전에 다음 문서를 따라야 합니다.

- [이미지 팩터리 만들기](image-factory-create.md)
- [Azure DevOps에서 이미지 팩터리 실행](image-factory-set-up-devops-lab.md)
- [사용자 지정 이미지 저장 및 여러 랩에 배포](image-factory-save-distribute-custom-images.md)

다음 항목이 이미 준비되어 있어야 합니다.

- Azure DevTest Labs에 있는 이미지 센터의 랩
- 센터에서 골든 이미지를 배포하는 하나 이상의 대상 Azure DevTest Labs
- 이미지 센터를 자동화하는 데 사용되는 Azure DevOps 프로젝트입니다.
- 스크립트 및 구성이 포함된 소스 코드 위치(이 예제에서는 위에서 사용된 것과 같은 DevOps 프로젝트에 있음)
- Azure PowerShell 작업을 오케스트레이션하는 빌드 정의
 
## <a name="setting-the-retention-policy"></a>보존 정책 설정
정리 단계를 구성하기 전에 DevTest Labs에 유지할 기록 이미지의 개수를 정의합니다. [Azure DevOps에서 이미지 센터 실행](image-factory-set-up-devops-lab.md) 문서에 따라 다양한 빌드 변수를 구성했습니다. 그 중 하나가 **ImageRetention** 입니다. 이 변수를 로 설정하면 `1` DevTest Labs에서 사용자 지정 이미지의 기록을 유지 관리하지 않습니다. 최신 분산 이미지만 사용할 수 있습니다. 이 변수를 `2`로 변경하면 최근 배포된 이미지와 이전 이미지가 유지 관리됩니다. 이 값을 설정하여 DevTest Labs에서 유지 관리하려는 기록 이미지 수를 정의할 수 있습니다.

## <a name="cleaning-up-the-factory"></a>센터 정리
센터 정리의 첫 번째 단계는 이미지 센터에서 골든 이미지 VM을 제거하는 것입니다. 이전 스크립트와 마찬가지로 이 작업을 수행하는 스크립트가 있습니다. 첫 번째 단계는 다음 이미지와 같이 빌드 정의에 다른 **Azure PowerShell** 작업을 추가하는 것입니다.

![PowerShell 단계를 보여 주는 스크린샷.](./media/set-retention-policy-cleanup/powershell-step.png)

목록에 새 작업이 있으면 항목을 선택하고 다음 이미지와 같이 모든 세부 정보를 입력합니다.

![이전 이미지 정리 PowerShell 작업을 보여 주는 스크린샷.](./media/set-retention-policy-cleanup/configure-powershell-task.png)

스크립트 매개 변수는 `-DevTestLabName $(devTestLabName)`입니다.

## <a name="retire-old-images"></a>이전 이미지 사용 중지 
이 작업은 이전 이미지를 제거하고, **ImageRetention** 빌드 변수와 일치하는 기록만 유지합니다. 빌드 정의에 **추가 Azure PowerShell** 빌드 작업을 추가합니다. 추가되면 작업을 선택하고 다음 이미지와 같이 세부 정보를 입력합니다. 

![이전 이미지 PowerShell 작업 사용 안 을 보여 주는 스크린샷.](./media/set-retention-policy-cleanup/retire-old-image-task.png)

스크립트 매개 변수는 `-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -SubscriptionId $(SubscriptionId) -DevTestLabName $(devTestLabName) -ImagesToSave $(ImageRetention)`입니다.

## <a name="queue-the-build"></a>빌드를 큐에 대기시킵니다.
빌드 정의를 완료했습니다. 이제 모든 것이 작동하는지 확인하기 위해 새 빌드를 큐에 대기합니다. 빌드가 성공적으로 완료되면 새 사용자 지정 이미지가 대상 랩에 표시됩니다. 이미지 팩터리 랩을 확인하면 프로비전된 VM이 표시되지 않습니다. 추가 빌드를 큐에 대기하는 경우 DevTest Labs에서 이전 사용자 지정 이미지를 사용 중지하는 정리 작업이 표시됩니다. 중지는 빌드 변수에 설정된 보존 값과 일치합니다.

> [!NOTE]
> 시리즈의 마지막 문서 끝에 빌드 파이프라인을 실행한 경우 새 빌드를 큐에 대기하기 전에 이미지 팩터리 랩에서 만든 가상 머신을 수동으로 삭제합니다.  모든 것을 설정하고 작동하는지 확인하는 동안에는 수동 정리 단계만 필요합니다.



## <a name="summary"></a>요약
이제 주문형으로 사용자 지정 이미지를 생성하고 랩에 배포할 수 있는 실행 중인 이미지 센터가 있습니다. 이 시점에서는 이미지를 올바르게 설정하고 대상 랩을 식별하기만 하면 됩니다. 이전 문서에서 언급했듯이 **Configuration** 폴더에 있는 **Labs.json** 파일은 각 대상 랩에서 사용할 수 있어야 하는 이미지를 지정합니다. 조직에 다른 DevTest Labs를 추가할 때 새로운 랩의 Labs.json에 항목을 추가하기만 하면 됩니다.

센터에 새 이미지를 추가하는 것도 간단합니다. 팩터리에서 새 이미지를 포함하려면 [Azure Portal](https://portal.azure.com) 열고 팩터리 랩으로 이동합니다. 단추를 선택하여 VM을 추가하고, 원하는 마켓플레이스 이미지 및 아티팩트 를 선택합니다. **만들기** 단추를 선택하여 새 VM을 만드는 대신 템플릿 **Azure Resource Manager 보기를** 선택합니다. 템플릿을 리포지토리의 **GoldenImages** 폴더 내 어딘가에 .json 파일로 저장합니다. 다음에 이미지 센터를 실행하면 사용자 지정 이미지가 생성됩니다.


## <a name="next-steps"></a>다음 단계
1. [빌드/릴리스를 예약](/azure/devops/pipelines/build/triggers?tabs=designer)하여 이미지 센터를 정기적으로 실행합니다. 팩터리에서 생성된 이미지를 정기적으로 새로 고칩니다.
2. 센터의 골든 이미지를 더 많이 만듭니다. 더 많은 VM 설치 작업을 스크립팅하고 팩터리 이미지에 아티팩트 포함을 위한 아티팩트 [만들기를](devtest-lab-artifact-author.md) 고려할 수도 있습니다.
4. [별도의 빌드/릴리스](/azure/devops/pipelines/overview)를 만들어 **DistributeImages** 스크립트를 별도로 실행합니다. Labs.json을 변경할 때 이 스크립트를 실행하고 모든 이미지를 다시 만들지 않고도 대상 랩에 이미지를 복사할 수 있습니다.

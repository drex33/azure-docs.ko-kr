---
title: 이미지 팩터리 만들기
description: 이 문서에서는 Git 리포지토리에서 사용할 수 있는 샘플 스크립트(Azure DevTest Labs)를 사용하여 사용자 지정 이미지 팩터리를 설정하는 방법을 보여 줍니다.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 82662ee3b7127fe52d8a91d71652e906800b61b2
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2021
ms.locfileid: "132397266"
---
# <a name="create-a-custom-image-factory-in-azure-devtest-labs"></a>Azure DevTest Labs에서 사용자 지정 이미지 팩터리 만들기
이 문서에서는 [Git 리포지토리](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory)에서 사용할 수 있는 샘플 스크립트를 사용하여 사용자 지정 이미지 팩터리를 설정하는 방법을 보여 줍니다.

## <a name="whats-an-image-factory"></a>이미지 팩터리란?
이미지 팩터리 는 원하는 모든 구성으로 이미지를 자동으로 정기적으로 빌드하고 배포하는 코드로서의 구성 솔루션입니다. 이미지 팩터리에서 이미지는 항상 최신이며, 전체 프로세스가 자동화되면 진행 중인 유지 관리는 거의 0입니다. 필요한 모든 구성이 이미 이미지에 있기 때문에 기본 OS를 사용하여 VM을 만든 후 시스템을 수동으로 구성하는 시간을 절약할 수 있습니다.

개발자 데스크톱이 DevTest Labs에서 준비된 상태가 되도록 하는 중요한 가속기에서는 사용자 지정 이미지를 사용합니다. 사용자 지정 이미지의 단점은 랩에서 추가로 유지 관리할 사항이 있다는 것입니다. 예를 들어 제품의 평가판 버전이 시간이 지남에 따라 만료되거나 새로 릴리스된 보안 업데이트가 적용되지 않으므로 정기적으로 사용자 지정 이미지를 새로 고쳐야 합니다. 이미지 팩터리를 사용하면 이미지 정의가 소스 코드 제어에 체크인되어 있으며 자동화된 프로세스에서 이 정의에 따라 사용자 지정 이미지를 생성합니다.

이 솔루션을 사용하면 사용자 지정 이미지에서 가상 머신을 만드는 속도를 유지하면서 지속적인 유지 관리 비용을 추가로 제거할 수 있습니다. 이 솔루션이 있으면 사용자 지정 이미지를 자동으로 만들고, 다른 DevTest Labs에 배포하고, 이전 이미지를 사용 중지할 수 있습니다. 다음 비디오에서는 이미지 팩터리에 대해 알아보고 DevTest Labs를 사용하여 이미지 팩터리를 구현하는 방법도 알아봅니다.  모든 Azure PowerShell 스크립트는 무료로 사용할 수 있으며 여기에 [https://aka.ms/dtlimagefactory](https://aka.ms/dtlimagefactory) 있습니다.

<br/>

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Custom-Image-Factory-with-Azure-DevTest-Labs/player]


## <a name="high-level-view-of-the-solution"></a>솔루션 개요 보기
이 솔루션을 사용하면 사용자 지정 이미지에서 가상 머신을 만드는 속도를 유지하면서 지속적인 유지 관리 비용을 추가로 제거할 수 있습니다. 이 솔루션이 있으면 사용자 지정 이미지를 자동으로 만들고 다른 DevTest Labs에 배포할 수 있습니다. DevTest Labs의 모든 작업을 자동화하는 데는 Azure DevOps(이전의 Visual Studio Team Services)를 오케스트레이션 엔진으로 사용합니다.

![솔루션의 상위 수준 보기.](./media/create-image-factory/high-level-view-of-solution.png)

다음 개별 단계를 실행할 수 있도록 지원하는 [DevTest Labs용 VSTS 확장](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)이 있습니다.

- 사용자 지정 이미지 만들기
- VM 만들기
- VM 삭제
- 환경 만들기
- 환경 삭제
- 환경 채우기

DevTest Labs 확장을 사용하면 DevTest Labs에서 사용자 지정 이미지를 자동으로 만드는 작업을 쉽게 시작할 수 있습니다.

더 복잡한 시나리오를 위한 대체 구현으로는 PowerShell 스크립트를 사용하는 방법이 있습니다. PowerShell을 사용하면 CI/CD(연속 통합 및 지속적인 업데이트) 도구 체인에서 사용할 수 있는 DevTest Labs 기반 이미지 팩터리를 완전히 자동화할 수 있습니다. 이 대체 솔루션에서 준수하는 원칙은 다음과 같습니다.

- 일반적인 업데이트에서는 이미지 팩터리를 변경할 필요가 없습니다. (예: 새 형식의 사용자 지정 이미지 추가, 자동으로 이전 이미지 사용 중지, 새 ‘엔드포인트’ DevTest Labs를 추가하여 사용자 지정 이미지 수신 등)
- 일반적인 변경 내용은 소스 코드 제어(Infrastructure as Code)에서 지원됩니다.
- 사용자 지정 이미지를 수신하는 DevTest Labs가 동일한 Azure 구독(랩 범위 구독)에 있지 않을 수 있습니다.
- 필요에 따라 더 많은 팩터리 가동을 할 수 있도록 PowerShell 스크립트를 재사용할 수 있어야 합니다.

## <a name="next-steps"></a>다음 단계
이 섹션의 다음 문서인 [Azure DevOps 에서 이미지 팩터리 실행으로](image-factory-set-up-devops-lab.md)이동합니다.

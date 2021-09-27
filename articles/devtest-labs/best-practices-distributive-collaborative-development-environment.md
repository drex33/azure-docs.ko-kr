---
title: Azure DevTest Labs 리소스의 분산 공동 작업 개발
description: DevTest Labs 리소스를 개발에 필요한 분산 공동 작업 개발 환경을 설정할 수 있도록 모범 사례를 제공합니다.
ms.topic: conceptual
ms.date: 06/26/2020
ms.openlocfilehash: f24ab6e612762df5ee0a0f869507c51ac9e5f538
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128644318"
---
# <a name="best-practices-for-distributed-and-collaborative-development-of-azure-devtest-labs-resources"></a>Azure DevTest Labs 리소스의 분산 공동 작업 개발의 모범 사례
분산 공동 작업 개발을 사용하면 다른 팀이나 사용자가 코드 베이스를 개발하고 유지 관리할 수 있습니다. 개발 프로세스의 성공은 정보를 만들고, 공유하며, 통합하는 능력에 달려 있습니다. 이 주요 개발 원칙은 Azure DevTest Labs 내에서 사용할 수 있습니다. 랩 내에는 기업 내 여러 랩 간에 일반적으로 분산되는 여러 유형의 리소스가 있습니다. 여러 리소스 유형은 다음과 같은 두 영역으로 집중됩니다.

- 랩에 내부적으로 저장된 리소스(랩 기반)
- [랩에 연결된 외부 리포지토리](devtest-lab-add-artifact-repo.md)에 저장된 리소스(코드 리포지토리 기반) 

이 문서에서는 모든 수준에서 사용자 지정 및 품질을 보장하는 동시에 여러 팀 간에 공동 작업 및 배포도 허용하는 몇 가지 모범 사례에 대해 설명합니다.

## <a name="lab-based-resources"></a>랩 기반 리소스

### <a name="custom-virtual-machine-images"></a>사용자 지정 가상 머신 이미지
매일 밤마다 배포되는 사용자 지정 이미지의 공통 원본을 사용할 수 있습니다. 자세한 내용은 [이미지 팩터리](image-factory-create.md)를 참조하세요.    

### <a name="formulas"></a>수식
[수식](devtest-lab-manage-formulas.md)은 랩에만 적용되며 배포 메커니즘은 없습니다. 랩 멤버가 모든 수식 개발을 수행합니다. 

## <a name="code-repository-based-resources"></a>코드 리포지토리 기반 리소스
코드 리포지토리, 아티팩트 및 환경을 기반으로 하는 두 가지 다른 기능이 있습니다. 이 문서에서는 기능에 대해 설명하고, 조직 수준 또는 팀 수준에서 사용 가능한 아티팩트와 환경을 사용자 지정하는 기능을 허용하도록 리포지토리 및 워크플로를 가장 효과적으로 설정하는 방법을 살펴봅니다.  이 워크플로는 표준 [소스 코드 제어 분기 전략](/azure/devops/repos/tfvc/branching-strategies-with-tfvc)을 기반으로 합니다. 

### <a name="key-concepts"></a>주요 개념
아티팩트에 대한 원본 정보에는 메타데이터와 스크립트가 포함됩니다. 환경에 대한 원본 정보에는 PowerShell 스크립트, DSC 스크립트, Zip 파일 등의 모든 지원 파일을 포함하는 메타데이터 및 리소스 관리자 템플릿이 포함되어 있습니다.  

### <a name="repository-structure"></a>리포지토리 구조  
SCC(소스 코드 제어)에 대한 가장 일반적인 구성은 랩에서 사용되는 코드 파일(리소스 관리자 템플릿, 메타데이터 및 스크립트)을 저장할 수 있도록 다중 계층 구조를 설정하는 구성입니다. 특히 서로 다른 비즈니스 수준에서 관리되는 리소스를 저장할 수 있는 다른 리포지토리를 만듭니다.   

- 회사 차원의 리소스
- 사업부 차원의 리소스
- 팀 특정 리소스

이러한 각 수준에서 기본 분기는 프로덕션 품질이어야 하는 다른 리포지토리로 연결됩니다. 각 리포지토리의 [분기](/azure/devops/repos/git/git-branching-guidance) 는 해당 특정 리소스(아티팩트 또는 템플릿)를 개발하는 데 쓰입니다. 이 구조체는 여러 리포지토리 및 여러 분기를 동시에 조직의 랩에 쉽게 연결할 수 있으므로 DevTest Labs와 잘 맞습니다. 동일한 이름, 설명 및 게시자가 있는 경우 혼동을 피하기 위해 리포지토리 이름은 UI(사용자 인터페이스)에 포함됩니다.
     
다음 다이어그램은 두 가지 리포지토리를 보여 줍니다. 하나는 IT 부서에서 유지 관리하는 회사 리포지토리, 다른 하나는 R&D 부서가 유지 관리하는 부서 리포지토리입니다.

![샘플 분배 및 공동 개발 환경](./media/best-practices-distributive-collaborative-dev-env/distributive-collaborative-dev-env.png)
   
이 계층화된 구조체를 사용하면 랩에 여러 리포지토리가 연결되는 동시에 기본 분기에서 더 높은 수준의 품질을 유지하며 개발을 수행할 수 있으므로 유연성이 향상됩니다.

## <a name="next-steps"></a>다음 단계    
다음 문서를 참조하세요.

- [Azure Portal](devtest-lab-add-artifact-repo.md)을 사용하거나 [Azure 리소스 관리 템플릿을](add-artifact-repository.md) 통해 랩에 리포지토리를 추가합니다.
- [DevTest 랩 아티팩트](devtest-lab-artifact-author.md)
- [DevTest 랩 환경](devtest-lab-create-environment-from-arm.md).

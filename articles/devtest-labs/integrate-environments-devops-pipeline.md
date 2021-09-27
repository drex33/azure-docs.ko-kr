---
title: 환경을 Azure Pipelines에 통합
description: Azure DevOps CI(연속 통합) 및 CD(지속적인 업데이트) 파이프라인에 Azure DevTest Labs 환경을 통합하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 4f205fb70978a667df8a8fe82beea38e492828a0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128651123"
---
# <a name="integrate-environments-into-your-azure-devops-cicd-pipelines"></a>Azure DevOps CI/CD 파이프라인에 환경 통합
Azure DevOps Services(이전의 Visual Studio Team Services)에 설치된 Azure DevTest Labs Tasks 확장을 사용하여 CI(연속 통합)/CD(지속적인 업데이트) 빌드 및 릴리스 파이프라인을 Azure DevTest Labs와 손쉽게 통합할 수 있습니다. 이 확장을 사용하면 특정 테스트 작업을 위한 [환경](devtest-lab-test-env.md)을 빠르고 쉽게 배포한 후 테스트가 완료되면 삭제할 수 있습니다. 

이 문서에서는 환경을 만들어 배포한 후 해당 환경을 삭제하는 방법을 하나의 완전한 파이프라인으로 보여 줍니다. 일반적으로 고유한 사용자 지정 빌드-테스트-배포 파이프라인에서는 해당 작업을 각각 개별적으로 수행합니다. 이 문서에 사용된 확장은 [DTL VM 작업 만들기/삭제](devtest-lab-integrate-ci-cd.md) 외에도 다음과 같은 작업을 수행합니다.

- 환경 만들기
- 환경 삭제

## <a name="before-you-begin"></a>시작하기 전에
CI/CD 파이프라인을 Azure DevTest Labs와 통합하려면 먼저 Visual Studio Marketplace에서 [Azure DevTest Labs Tasks](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) 확장을 설치합니다. 

## <a name="create-and-configure-the-lab-for-environments"></a>환경에 대한 랩 만들기 및 구성
이 섹션에서는 Azure 환경을 배포할 랩을 만들고 구성하는 방법을 설명합니다.

1. 랩이 아직 없는 경우 [랩을 만듭니다](devtest-lab-create-lab.md). 
2. [Azure Resource Manager 템플릿으로 다중 VM 환경 및 PaaS 리소스 만들기](devtest-lab-create-environment-from-arm.md) 문서의 지침에 따라 랩을 구성하고 환경 템플릿을 만듭니다.
3. 이 예에서는 기존의 Azure 빠른 시작 템플릿을 사용합니다([https://azure.microsoft.com/resources/templates/web-app-redis-cache-sql-database](https://azure.microsoft.com/resources/templates/web-app-redis-cache-sql-database)).
4. **web-app-redis-cache-sql-database** 폴더를 2단계에서 구성한 리포지토리의 **ArmTemplate** 폴더에 복사합니다.

## <a name="create-a-release-definition"></a>릴리스 정의 만들기
릴리스 정의를 만들려면 다음을 수행합니다.

1.  **빌드 및 릴리스 허브** 의 **릴리스** 탭에서 **더하기 기호(+)** 단추를 선택합니다.
2.  **릴리스 정의 만들기** 창에서 **빈** 템플릿을 선택한 후, **다음** 을 선택합니다.
3.  **나중에 선택** 을 선택한 후 **만들기** 를 선택하여 하나의 기본 환경이 있고 연결된 아티팩트가 없는 새 릴리스 정의를 만듭니다.
4.  바로 가기 메뉴를 열려면 새 릴리스 정의에서 환경 이름 옆에 있는 **줄임표(...)** 를 선택한 다음 **변수 구성** 을 선택합니다.
5.  **구성 - 환경** 창에서 릴리스 정의 작업에 사용하는 변수에 다음 값을 입력합니다.
1.  **administratorLogin** 에 SQL 관리자 로그인 이름을 입력합니다.
2.  **administratorLoginPassword** 에 SQL 관리자 로그인에서 사용할 암호를 입력합니다. 암호를 숨기고 보호하려면 “자물쇠” 아이콘을 사용합니다.
3.  **databaseName** 에 SQL Database 이름을 입력합니다.
4.  이 변수는 예제 환경에 따라 다릅니다. 환경이 다르면 변수가 다를 수 있습니다.

## <a name="create-an-environment"></a>환경 만들기
배포의 다음 단계에서는 개발 또는 테스트 목적으로 사용할 환경을 만듭니다.

1. 릴리스 정의에서 **작업 추가** 를 선택합니다.
2. **작업** 탭에서 Azure DevTest Labs 환경 만들기 작업을 추가합니다. 아래와 같이 작업을 구성합니다.
    1. **Azure RM 구독** 의 경우 **사용할 수 있는 Azure 서비스 연결** 목록에서 연결을 선택하거나, Azure 구독에 좀 더 제한된 권한 연결을 만듭니다. 자세한 내용은 [Azure Resource Manager 서비스 엔드포인트](/azure/devops/pipelines/library/service-endpoints)를 참조하세요.
2. **랩 이름** 은 앞에서 만든 인스턴스의 이름을 선택합니다*.
3. **리포지토리 이름** 은 Resource Manager 템플릿(201)을 푸시한 리포지토리를 선택합니다*.
4. **템플릿 이름** 은 소스 코드 리포지토리에 저장한 환경의 이름을 선택합니다*. 
5. **랩 이름**, **리포지토리 이름**, **템플릿 이름** 은 Azure 리소스 ID에 대한 친숙한 표현입니다. 식별 이름을 수동으로 입력하면 오류가 발생하므로 드롭다운 목록을 사용하여 정보를 선택합니다.
6. **환경 이름** 은 랩 내 환경 인스턴스를 고유하게 식별하는 이름을 입력합니다.  이름은 랩 내에서 고유해야 합니다.
7. **매개 변수 파일** 및 **매개 변수** 를 사용하면 사용자 지정 매개 변수를 환경에 전달할 수 있습니다. 둘 중 하나 또는 둘 다를 사용하여 매개 변수 값을 설정할 수 있습니다. 이 예에서는 매개 변수 섹션을 사용합니다. 다음 예와 같이 환경에서 정의한 변수의 이름을 사용합니다. `-administratorLogin "$(administratorLogin)" -administratorLoginPassword "$(administratorLoginPassword)" -databaseName "$(databaseName)" -cacheSKUCapacity 1`
8. 환경 템플릿 내 정보는 템플릿 출력 섹션을 통해 전달될 수 있습니다. 다른 작업에서 데이터를 사용할 수 있도록 **환경 템플릿 출력을 기반으로 출력 변수 만들기** 를 선택합니다. 따라야 할 패턴은 `$(Reference name.Output Name)`입니다. 예를 들어 참조 이름이 DTL이고 템플릿의 출력 이름이 location인 경우 변수는 `$(DTL.location)`입니다.

## <a name="delete-the-environment"></a>환경 삭제
최종 단계에서는 Azure DevTest Labs 인스턴스에 배포된 환경을 삭제합니다. 일반적으로 배포된 리소스에서 필요한 개발 작업을 실행하거나 테스트를 실행한 후에는 환경을 삭제합니다.

릴리스 정의에서 **작업 추가** 를 선택한 후 **배포** 탭에서 **Azure DevTest Labs 환경 삭제** 작업을 추가합니다. 다음과 같이 구성합니다.

1. VM을 삭제하려면 [Azure DevTest Labs Tasks](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks)를 참조하세요.
    1. **Azure RM 구독** 의 경우 **사용할 수 있는 Azure 서비스 연결** 목록에서 연결을 선택하거나, Azure 구독에 좀 더 제한된 권한 연결을 만듭니다. 자세한 내용은 [Azure Resource Manager 서비스 엔드포인트](/azure/devops/pipelines/library/service-endpoints)를 참조하세요.
    2. **랩 이름** 은 환경이 있는 랩을 선택합니다.
    3. **환경 이름** 은 제거할 환경의 이름을 입력합니다.
2. 릴리스 정의 대한 이름을 입력한 다음, 저장합니다.

## <a name="next-steps"></a>다음 단계
다음 문서를 참조하세요. 
- [Resource Manager 템플릿으로 다중 VM 환경 만들기](devtest-lab-create-environment-from-arm.md)
- [DevTest Labs GitHub 리포지토리](https://github.com/Azure/azure-quickstart-templates)의 DevTest Labs 자동화를 위한 빠른 시작 Resource Manager 템플릿
- [VSTS 문제 해결 페이지](/azure/devops/pipelines/troubleshooting)

---
title: Azure Functions에 대한 연속 배포
description: Azure App Service의 지속적인 배포 기능을 사용하여 함수를 게시합니다.
ms.assetid: 361daf37-598c-4703-8d78-c77dbef91643
ms.topic: conceptual
ms.date: 09/25/2019
ms.openlocfilehash: 0018f1c74708035206a8a2bb9a540f00f98f43a4
ms.sourcegitcommit: 6a3096e92c5ae2540f2b3fe040bd18b70aa257ae
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2021
ms.locfileid: "112321032"
---
# <a name="continuous-deployment-for-azure-functions"></a>Azure Functions에 대한 연속 배포

Azure Functions를 사용하여 [소스 제어 통합](functions-deployment-technologies.md#source-control)을 통해 코드를 지속적으로 배포할 수 있습니다. 원본 제어 통합을 사용하면 코드 업데이트에서 Azure로 배포를 트리거하는 워크플로를 사용할 수 있습니다. Azure Functions를 처음 접하는 경우 [Azure Functions 개요](functions-overview.md)를 검토하여 시작하세요.

지속적인 배포는 여러 차례의 자주 하는 기여를 통합하는 프로젝트에 적합한 옵션입니다. 지속적인 배포를 사용하는 경우 팀에서 간편하게 공동 작업을 수행할 수 있도록 하는 코드의 단일 데이터 소스를 유지 관리합니다. 다음 원본 코드 위치에서 Azure Functions의 지속적인 배포를 구성할 수 있습니다.

* [Azure Repos](https://azure.microsoft.com/services/devops/repos/)
* [GitHub](https://github.com)
* [Bitbucket](https://bitbucket.org/)

Azure에서 함수에 대한 배포 단위는 함수 앱입니다. 함수 앱의 모든 함수는 동시에 배포됩니다. 지속적인 배포를 사용하도록 설정하면 단일 데이터 소스가 다른 곳으로 설정되어 있으므로 Azure Portal의 함수 코드에 대한 액세스가 *읽기 전용* 으로 구성됩니다.

## <a name="requirements-for-continuous-deployment"></a>지속적인 배포에 대한 요구 사항

지속적인 배포를 성공적으로 수행하려면 디렉터리 구조가 Azure Functions에서 예상하는 기본 폴더 구조와 호환되어야 합니다.

[!INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

>[!NOTE]  
> 사용 계획에서 실행되는 Linux 앱에 대해서는 아직 지속적인 배포가 지원되지 않습니다. 

## <a name="set-up-continuous-deployment"></a><a name="credentials"></a>연속 배포 설정

기존 함수 앱에 대한 지속적인 배포를 구성하려면 다음 단계를 완료합니다. 다음 단계에서는 GitHub 리포지토리와의 통합을 보여 주지만 Azure Repos 또는 기타 소스 코드 리포지토리에도 유사한 단계가 적용됩니다.

1. [Azure Portal](https://portal.azure.com)의 함수 앱에서 **배포 센터** 를 선택하고 **GitHub** 를 선택한 다음 **권한 부여** 를 선택합니다. 이미 GitHub에 권한을 부여한 경우 **계속** 을 선택하고 다음 단계를 건너뜁니다. 

    :::image type="content" source="./media/functions-continuous-deployment/github.png" alt-text="Azure App Service 배포 센터":::

3. GitHub에서 **AzureAppService 권한 부여** 를 선택합니다.

    :::image type="content" source="./media/functions-continuous-deployment/authorize.png" alt-text="Azure App Service에 권한 부여":::

    GitHub 암호를 입력한 다음 **계속** 을 선택합니다.

4. 다음 빌드 공급자 중 하나를 선택합니다.

    * **App Service 빌드 서비스**: 빌드가 필요하지 않거나 일반 빌드가 필요한 경우에 가장 적합합니다.
    * **Azure Pipelines(미리 보기)** : 빌드를 보다 세부적으로 제어해야 하는 경우에 가장 적합합니다. 공급자는 현재 미리 보기로 제공됩니다.

    **계속** 을 선택합니다.

5. 지정한 원본 제어 옵션과 관련된 정보를 구성합니다. GitHub의 경우 **조직**, **리포지토리**, **분기** 에 대한 값을 입력하거나 선택해야 합니다. 값은 코드의 위치를 기반으로 합니다. 그런 다음, **계속** 을 선택합니다.

    :::image type="content" source="./media/functions-continuous-deployment/github-specifics.png" alt-text="GitHub 구성":::

6. 모든 세부 정보를 검토한 다음 **마침** 을 선택하여 배포 구성을 완료합니다.

프로세스가 완료되면 지정된 원본의 모든 코드가 앱에 배포됩니다. 이때 배포 원본의 변경 내용은 Azure의 함수 앱에 대한 변경 내용 배포를 트리거합니다.

> [!NOTE]
> 지속적인 통합을 구성한 후에는 Functions 포털에서 더 이상 원본 파일을 편집할 수 없습니다. 원래 로컬 컴퓨터에서 코드를 게시한 경우 함수 앱의 `WEBSITE_RUN_FROM_PACKAGE` 설정을 `0` 값으로 변경해야 할 수 있습니다. 

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Functions에 대한 모범 사례](functions-best-practices.md)

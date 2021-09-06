---
title: Visual Studio를 사용하여 예제 서버리스 앱 만들기
description: Visual Studio에서 Azure Logic Apps 및 Azure Functions를 사용하여 Azure 빠른 시작 템플릿으로 예제 서버리스 앱을 만들고, 배포하고, 관리합니다
services: logic-apps
ms.suite: integration
ms.reviewer: estfan, azla
ms.topic: how-to
ms.date: 07/15/2021
ms.openlocfilehash: df395fcffc1a7e675921f2ff397d488c301c703a
ms.sourcegitcommit: 47ac63339ca645096bd3a1ac96b5192852fc7fb7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/16/2021
ms.locfileid: "114363858"
---
# <a name="create-an-example-serverless-app-with-azure-logic-apps-and-azure-functions-in-visual-studio"></a>Visual Studio에서 Azure Logic Apps와 Azure Functions를 사용하여 예제 서버리스 앱 만들기

Azure에서 Azure Logic Apps나 Azure Functions 같은 서비스 및 기능을 사용하여 클라우드 기반 "서버리스" 앱을 빠르게 만들고, 빌드하고, 배포할 수 있습니다. Azure Logic Apps를 사용하면 최소한의 코드 또는 코드 없는 접근 방식을 통해 워크플로를 빠르고 쉽게 빌드하여 결합 작업의 오케스트레이션을 간소화할 수 있습니다. 이러한 상호 작용을 코딩하거나, 붙이기 코드를 유지 관리하거나, 새로운 API 또는 사양을 학습할 필요 없이 다양한 서비스, 클라우드, 온-프레미스 또는 하이브리드를 통합할 수 있습니다. Azure Functions를 사용하면 이벤트 기반 모델을 통해 개발 시간을 단축할 수 있습니다. 사용자 고유의 코드를 자동으로 실행하여 이벤트에 응답하는 트리거를 사용할 수 있습니다. 바인딩을 사용하여 다른 서비스를 원활하게 통합할 수 있습니다.

이 문서에서는 Azure 빠른 시작 템플릿을 사용하여 다중 테넌트 Azure에서 실행되는 예제 서버리스 앱을 만드는 방법을 보여줍니다. 이 템플릿은 Azure Resource Manager 배포 템플릿이 포함된 Azure 리소스 그룹 프로젝트를 만듭니다. 이 템플릿은 사용자가 정의하는 Azure 함수에 대한 호출이 미리 정의된 워크플로에 포함되는 기본 논리 앱 리소스를 정의합니다. 워크플로 정의에는 다음 구성 요소가 포함됩니다.

* HTTP 요청을 수신하는 요청 트리거. 이 트리거를 시작하려면 트리거의 URL에 요청을 보냅니다.
* 나중에 정의할 수 있는 Azure 함수를 호출하는 Azure Functions 동작
* 함수의 결과가 포함된 HTTP 응답을 반환하는 응답 동작

자세한 내용은 다음 문서를 검토하세요.

* [서버리스 컴퓨팅: 서버리스 기술 소개](https://azure.microsoft.com/overview/serverless-computing/)
* [Azure Logic Apps 정보](logic-apps-overview.md)
* [Azure Functions 정보](../azure-functions/functions-overview.md)
* [Azure 서버리스: Azure Logic Apps와 Azure Functions를 사용하여 클라우드 기반 앱과 솔루션을 빌드하는 것에 대한 개요](logic-apps-serverless-overview.md)

## <a name="prerequisites"></a>사전 요구 사항

* Azure 계정 및 구독 Azure 구독이 없는 경우 [체험 Azure 계정에 등록](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)합니다.

* 다음 도구가 없는 경우 다음 도구를 다운로드하여 설치합니다.

  * [Visual Studio 2019, 2017 또는 2015(Community 또는 기타 버전)](https://aka.ms/download-visual-studio). 이 빠른 시작에서는 무료로 제공되는 Visual Studio Community 2019를 사용합니다.

    > [!IMPORTANT]
    > Visual Studio 2019 또는 2017을 설치할 때 **Azure 개발** 워크로드를 선택해야 합니다.

  * [.NET용 Microsoft Azure SDK(버전 2.9.1 이상)](https://azure.microsoft.com/downloads/). [Azure SDK for .NET](/dotnet/azure/intro)에 대해 자세히 알아보세요.

  * [Azure PowerShell](https://github.com/Azure/azure-powershell#installation).

  * 사용 중인 Visual Studio 버전에 해당하는 Azure Logic Apps Tools: [Visual Studio 내에서 이 확장을 설치하는 방법](/visualstudio/ide/finding-and-using-visual-studio-extensions)을 알아볼 수도 있고, Visual Studio Marketplace에서 해당하는 버전의 Azure Logic Apps Tools를 다운로드할 수도 있습니다.

    * [Visual Studio 2019](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2019)
    * [Visual Studio 2017](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2017)
    * [Visual Studio 2015](https://aka.ms/download-azure-logic-apps-tools-visual-studio-2015)

    > [!IMPORTANT]
    > 설치를 완료하면 Visual Studio를 다시 시작하도록 합니다.

  * [Azure Functions Core Tools](https://www.npmjs.com/package/azure-functions-core-tools). Azure 함수를 로컬로 디버그하는 데 필요합니다. 자세한 내용은 [Azure Functions Core Tools 사용](../azure-functions/functions-run-local.md)을 참조하세요.

* 포함된 워크플로 디자이너를 사용하는 동안 인터넷 연결

  이 디자이너를 사용하려면 Azure에서 리소스를 만들고 워크플로의 [관리형 커넥터](../connectors/managed.md)에서 속성과 데이터를 읽을 수 있도록 인터넷 연결이 필요합니다. 예를 들어 SQL 커넥터를 사용하는 경우 이 디자이너는 서버 인스턴스에서 사용 가능한 기본 속성 및 사용자 지정 속성을 확인합니다.

## <a name="create-a-resource-group-project"></a>리소스 그룹 프로젝트 만들기

시작하려면 서버리스 앱의 컨테이너로 사용할 Azure 리소스 그룹 프로젝트를 만듭니다. Azure에서 *리소스 그룹* 은 전체 앱의 리소스를 구성하는 데 사용되는 논리적 컬렉션입니다. 그런 다음, 이러한 리소스를 단일 자산으로 관리하고 배포할 수 있습니다. Azure에 있는 서버리스 앱의 경우 리소스 그룹에 Azure Logic Apps *및* Azure Functions의 리소스가 포함됩니다. 자세한 내용은 [Resource Manager 용어](../azure-resource-manager/management/overview.md#terminology)를 참조하세요.

1. Visual Studio를 열고, 로그인하라는 메시지가 표시되면 Azure 계정으로 로그인합니다.

1. 시작 창이 열리면 **새 프로젝트 만들기** 를 선택합니다.

   !["새 프로젝트 만들기"가 선택된 Visual Studio 시작 창을 보여주는 스크린샷](./media/create-serverless-apps-visual-studio/start-window.png)

1. 시작 창이 열리지 않으면 **파일** 메뉴에서 **새로 만들기** > **프로젝트** 를 선택합니다.

   !["새로 만들기" 메뉴와 "프로젝트"가 선택된 "파일" 메뉴를 보여주는 스크린샷](./media/create-serverless-apps-visual-studio/create-new-project-visual-studio.png)

1. **새 프로젝트 만들기** 창이 열리면 검색 창에서 C# 또는 Visual Basic용 **Azure 리소스 그룹** 프로젝트 템플릿을 선택합니다. 준비가 되면 **다음** 을 선택합니다. 이 예제에서는 C#을 계속 사용합니다.

   ![검색 창에서 "리소스 그룹"을 선택하고 "Azure 리소스 그룹" 프로젝트 템플릿을 선택한 "새 프로젝트 만들기"창을 보여주는 스크린샷](./media/create-serverless-apps-visual-studio/start-window-find-project-template.png)

1. **새 프로젝트 구성** 창이 열리면 이름 등의 프로젝트 정보를 입력합니다. 완료되면 **만들기** 를 선택합니다.

   !["새 프로젝트 구성" 창과 프로젝트 세부 정보를 보여주는 스크린샷](./media/create-serverless-apps-visual-studio/start-window-create-new-project-details.png)

1. **Azure 템플릿 선택** 창이 열리면 **이 위치의 템플릿 표시** 목록에서 템플릿 위치로 **Azure 빠른 시작(github.com/Azure/azure-quickstart-templates)** 을 선택합니다.

1. 검색 상자에 `logic-app-and-function-app`를 입력합니다. 결과에서 **quickstarts\microsoft.logic\logic-app-and-function-app** 이라는 템플릿을 선택합니다. 완료되면 **확인** 을 선택합니다.

   ![템플릿 위치로 "Azure 빠른 시작"을 선택하고 "quickstarts\microsoft.logic\logic-app-and-function-app"을 선택한 "Azure 템플릿 선택" 창을 보여주는 스크린샷](./media/create-serverless-apps-visual-studio/select-template.png)

   Visual Studio가 프로젝트의 솔루션 컨테이너를 포함한 리소스 그룹 프로젝트를 만듭니다.

   ![프로젝트와 솔루션이 만들어진 것을 보여주는 스크린샷](./media/create-serverless-apps-visual-studio/create-serverless-solution.png)

1. 다음으로 Azure에 솔루션을 배포합니다.

   > [!IMPORTANT]
   > 배포 단계를 완료해야 합니다. 그렇지 않으면 Visual Studio에서 디자이너를 사용하여 논리 앱의 워크플로를 열고, 검토하고, 편집할 수 없습니다.

## <a name="deploy-your-solution"></a>솔루션 배포

Visual Studio에서 디자이너를 사용하여 논리 앱을 열려면 Azure에 앱을 배포해야 합니다. 그러면 디자이너가 논리 앱의 워크플로에 사용되는 서비스 및 리소스에 연결할 수 있습니다.

1. 솔루션 탐색기에서 리소스 프로젝트의 바로 가기 메뉴를 연 다음, **배포** > **새로 만들기** 를 차례로 선택합니다.

   ![프로젝트 바로 가기 메뉴가 열려 있고, "배포" 메뉴가 열려 있고, "새로 만들기"가 선택된 솔루션 탐색기를 보여주는 스크린샷](./media/create-serverless-apps-visual-studio/deploy.png)

1. **리소스 그룹에 배포** 창이 열리면 다음 단계에 따라 배포 정보를 제공합니다.

   1. 이 창에서 현재 구독을 탐지한 후, Azure 구독을 확인하거나 원한다면 다른 구독을 선택합니다.

   1. Azure에 새 리소스 그룹을 만듭니다. **리소스 그룹** 목록에서 **새로 만들기** 를 선택합니다.

      !["새로 만들기"가 선택된 "리소스 그룹에 배포" 창을 보여주는 스크린샷](./media/create-serverless-apps-visual-studio/create-resource-group-start.png)

   1. **리소스 그룹 만들기** 창이 열리면 다음 정보를 제공합니다.

      | 속성 | 설명 |
      |----------|-------------|
      | 리소스 그룹 이름 | 리소스 그룹에 지정할 이름 |
      | 리소스 그룹 위치 | 논리 앱 리소스를 호스트할 Azure 데이터 센터 지역 |
      |||

      이 예제에서는 계속해서 미국 서부 지역에 리소스 그룹을 만듭니다.

      ![새 리소스 그룹 정보가 포함된 "리소스 그룹 만들기" 창을 보여주는 스크린샷](./media/create-serverless-apps-visual-studio/create-resource-group.png)

   1. 다음과 같이 솔루션 만들기 및 배포를 완료합니다.

      ![배포 설정](./media/create-serverless-apps-visual-studio/deploy-to-resource-group.png)

1. **매개 변수 편집** 창이 나타나면 배포된 논리 앱 리소스와 함수 앱 리소스에 사용할 리소스 이름을 입력하고 변경 내용을 저장합니다.

   > [!IMPORTANT]
   > 논리 앱 및 함수 앱에 전역적으로 고유한 이름을 사용해야 합니다.

   ![논리 앱 리소스 및 함수 앱 리소스의 이름이 표시된 "매개 변수 편집" 창을 보여주는 스크린샷](./media/create-serverless-apps-visual-studio/logic-function-app-name-parameters.png)

   Visual Studio가 리소스 그룹에 배포를 시작하면 솔루션의 배포 상태가 Visual Studio **출력** 창에 표시됩니다. 배포가 완료되면 Azure Portal에서 해당 논리 앱이 활성 상태로 있습니다.

## <a name="open-and-edit-your-deployed-logic-app"></a>배포된 논리 앱을 열어서 편집

1. 솔루션 탐색기에서, **azuredeploy.json** 파일의 바로 가기 메뉴에서 **논리 앱 디자이너를 사용하여 열기** 를 선택합니다.

   !["논리 앱 디자이너를 사용하여 열기"가 선택된 "azuredeploy.json" 바로 가기 메뉴를 보여주는 스크린샷](./media/create-serverless-apps-visual-studio/open-designer.png)

   > [!TIP]
   > Visual Studio 2019에 이 명령이 없으면 Visual Studio 최신 업데이트가 설치되었는지 확인합니다.

1. 워크플로 디자이너가 열리면 워크플로를 편집하거나 단계를 추가하여 계속 진행할 수 있습니다. 모두 마쳤으면 **azuredeploy.json** 파일에 변경 내용을 저장합니다.

   ![디자이너의 논리 앱 워크플로를 보여주는 스크린샷](./media/create-serverless-apps-visual-studio/opened-logic-app-workflow.png)

## <a name="create-an-azure-functions-project"></a>Azure Functions 프로젝트 만들기

솔루션 내에서 C#기반 Azure 함수를 만들려면 [빠른 시작: Visual Studio를 사용하여 Azure에서 첫 번째 C# 함수 만들기](../azure-functions/functions-create-your-first-function-visual-studio.md)의 지침에 따라 C# 클래스 라이브러리 프로젝트를 만듭니다. 그렇지 않고 다른 언어를 사용하여 Azure Functions 프로젝트 및 함수를 만들려면 다음 빠른 시작의 지침을 따릅니다.

* [빠른 시작: Visual Studio Code를 사용하여 Azure에서 Java 함수 만들기](../azure-functions/create-first-function-vs-code-java.md)
* [빠른 시작: Visual Studio Code를 사용하여 Azure에서 JavaScript 함수 만들기](../azure-functions/create-first-function-vs-code-node.md)
* [빠른 시작: Visual Studio Code를 사용하여 Azure에서 PowerShell 함수 만들기](../azure-functions/create-first-function-vs-code-powershell.md)
* [빠른 시작: Visual Studio Code를 사용하여 Azure에서 Python 함수 만들기](../azure-functions/create-first-function-vs-code-python.md)
* [빠른 시작: Visual Studio Code를 사용하여 Azure에서 TypeScript 함수 만들기](../azure-functions/create-first-function-vs-code-typescript.md)

## <a name="deploy-functions-from-visual-studio"></a>Visual Studio에서 함수 배포

솔루션의 배포 템플릿은 **azuredeploy.json** 파일의 변수를 통해 지정된 Git 리포지토리의 솔루션에 있는 모든 Azure 함수를 배포할 수 있습니다. 솔루션에서 Functions 프로젝트를 만들고 작성하는 경우 GitHub 또는 Azure DevOps와 같은 Git 소스 제어에 프로젝트를 체크 인한 다음, 템플릿에서 Azure 함수를 배포하도록 **azuredeploy.json** 파일의 `repo` 변수를 업데이트할 수 있습니다.

## <a name="manage-logic-apps-and-view-run-history"></a>논리 앱 관리 및 실행 기록 보기

이미 논리 앱 리소스가 Azure에 배포된 경우에는 Visual Studio에서 해당 앱을 편집하고, 관리하고, 실행 기록을 보고, 사용하지 않도록 설정할 수 있습니다. 자세한 내용은 [Visual Studio로 논리 앱 관리](manage-logic-apps-with-visual-studio.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* Azure Logic Apps 및 Azure Functions를 사용하는 다른 예제는 [자습서: Azure Logic Apps, Azure Functions 및 Azure Storage를 사용하여 이메일을 처리하는 작업 자동화](tutorial-process-email-attachments-workflow.md)를 참조하세요.
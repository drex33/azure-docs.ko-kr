---
title: Visual Studio Code에서 컴퓨팅 인스턴스에 연결(미리 보기)
titleSuffix: Azure Machine Learning
description: Visual Studio Code에서 Azure Machine Learning 컴퓨팅 인스턴스에 연결하여 대화형 Jupyter Notebook 및 원격 개발 워크로드를 실행하는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: luquinta
author: luisquintanilla
ms.date: 07/15/2021
ms.openlocfilehash: b3e30a4914ac52c9d9661d3b6d6e07f54f0afdc6
ms.sourcegitcommit: 92dd25772f209d7d3f34582ccb8985e1a099fe62
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/15/2021
ms.locfileid: "114230418"
---
# <a name="connect-to-an-azure-machine-learning-compute-instance-in-visual-studio-code-preview"></a>Visual Studio Code에서 Azure Machine Learning 컴퓨팅 인스턴스에 연결(미리 보기)

이 문서에서는 Visual Studio Code를 사용하여 Azure Machine Learning 컴퓨팅 인스턴스에 연결하는 방법을 알아봅니다.

[Azure Machine Learning 컴퓨팅 인스턴스](concept-compute-instance.md)는 데이터 과학자를 위한 완전히 관리되는 클라우드 기반 워크스테이션이며 IT 관리자를 위해 관리 및 엔터프라이즈 준비 기능을 제공합니다.

Visual Studio Code에서 컴퓨팅 인스턴스에 연결하는 방법에는 두 가지가 있습니다.

* 원격 컴퓨팅 인스턴스. 이 옵션은 기계 학습 프로젝트를 빌드하기 위한 완전한 기능을 갖춘 개발 환경을 제공합니다.
* 원격 Jupyter Notebook 서버. 이 옵션을 사용하여 컴퓨팅 인스턴스를 원격 Jupyter Notebook 서버로 설정할 수 있습니다.

## <a name="configure-a-remote-compute-instance"></a>원격 컴퓨팅 인스턴스 구성

개발용 원격 컴퓨팅 인스턴스를 구성하려면 몇 가지 필수 구성 요소가 필요합니다.

* Azure Machine Learning Visual Studio Code 확장. 자세한 내용은 [Azure Machine Learning Visual Studio Code 확장 설정](how-to-setup-vs-code.md)을 참조하세요.
* Azure Machine Learning 작업 영역. 아직 없는 경우 [Azure Machine Learning Visual Studio Code 확장을 사용하여 새 작업 영역을 만듭니다](how-to-manage-resources-vscode.md#create-a-workspace).
* Azure Machine Learning 컴퓨팅 인스턴스. 아직 없는 경우 [Azure Machine Learning Visual Studio Code 확장을 사용하여 새 컴퓨팅 인스턴스를 만듭니다](how-to-manage-resources-vscode.md#create-compute-instance).

> [!IMPORTANT]
> 방화벽 뒤의 컴퓨팅 인스턴스에 연결하려면 [Azure Machine Learning 방화벽 뒤에 있는 작업 영역 사용](how-to-access-azureml-behind-firewall.md#visual-studio-code-hosts)을 참조하세요.

원격 컴퓨팅 인스턴스에 연결하려면:

# <a name="studio"></a>[스튜디오](#tab/studio)

[ml.azure.com](https://ml.azure.com)으로 이동합니다.

> [!IMPORTANT]
> Visual Studio Code에서 원격 컴퓨팅 인스턴스에 연결하려면 Azure Machine Learning 스튜디오에서 로그인한 계정이 Visual Studio Code에서 사용하는 계정과 동일한지 확인합니다.

### <a name="compute"></a>Compute

1. **컴퓨팅** 탭을 선택합니다.
1. *애플리케이션 URI* 열에서 연결하려는 컴퓨팅 인스턴스에 대한 **VS Code** 를 선택합니다.

:::image type="content" source="media/how-to-set-up-vs-code-remote/studio-compute-instance-vs-code-launch.png" alt-text="컴퓨팅 인스턴스 VS Code Azure ML 스튜디오에 연결" lightbox="media/how-to-set-up-vs-code-remote/studio-compute-instance-vs-code-launch.png":::

### <a name="notebook"></a>Notebook

1. **Notebook** 탭을 선택합니다.
1. *Notebook* 탭에서 편집하려는 파일을 선택합니다.
1. **편집기 > VS Code에서 편집(미리 보기)** 을 선택합니다.

:::image type="content" source="media/how-to-set-up-vs-code-remote/studio-notebook-compute-instance-vs-code-launch.png" alt-text="컴퓨팅 인스턴스 VS Code Azure ML Notebook에 연결" lightbox="media/how-to-set-up-vs-code-remote/studio-notebook-compute-instance-vs-code-launch.png":::

# <a name="vs-code"></a>[VS 코드](#tab/extension)

### <a name="azure-machine-learning-extension"></a>Azure Machine Learning 확장

1. VS Code에서 Azure Machine Learning 확장을 시작합니다.
1. 확장에서 **컴퓨팅 인스턴스** 노드를 확장합니다.
1. 연결하려는 컴퓨팅 인스턴스를 마우스 오른쪽 단추로 클릭하고 **컴퓨팅 인스턴스에 연결** 을 선택합니다.

:::image type="content" source="media/how-to-set-up-vs-code-remote/vs-code-compute-instance-launch.png" alt-text="컴퓨팅 인스턴스 Visual Studio Code Azure ML 확장에 연결" lightbox="media/how-to-set-up-vs-code-remote/vs-code-compute-instance-launch.png":::

### <a name="command-palette"></a>명령 팔레트

1. VS Code에서 **보기 > 명령 팔레트** 를 선택하여 명령 팔레트를 엽니다.
1. **AZURE ML: 컴퓨팅 인스턴스에 연결** 텍스트 상자에 입력합니다.
1. 구독을 선택합니다.
1. 작업 영역을 선택합니다.
1. 컴퓨팅 인스턴스를 선택하거나 새로운 컴퓨팅 인스턴스를 만듭니다.

---

원격 컴퓨팅 인스턴스에 대한 새 창이 표시됩니다. 원격 컴퓨팅 인스턴스에 연결하려고 하면 다음 작업이 수행됩니다.

1. 권한 부여 일부 검사를 수행하여 연결을 시도하는 사용자에게 컴퓨팅 인스턴스를 사용할 수 있는 권한이 있는지 확인합니다.
1. VS Code 원격 서버가 컴퓨팅 인스턴스에 설치됩니다.
1. 실시간 상호 작용을 위해 WebSocket 연결이 설정됩니다.

연결이 설정되면 유지됩니다. 토큰은 세션을 시작할 때 실행되며 컴퓨팅 인스턴스와의 연결을 유지하기 위해 자동으로 새로 고쳐집니다.

원격 컴퓨팅 인스턴스에 연결한 후 편집기를 사용하여 다음을 수행합니다.

* [원격 컴퓨팅 인스턴스 또는 파일 공유에서 파일을 작성하고 관리합니다](https://code.visualstudio.com/docs/editor/codebasics).
* [VS Code 통합 터미널](https://code.visualstudio.com/docs/editor/integrated-terminal)을 사용하여 [원격 컴퓨팅 인스턴스에서 명령 및 애플리케이션을 실행합니다](how-to-access-terminal.md).
* [스크립트 및 애플리케이션을 디버그합니다](https://code.visualstudio.com/Docs/editor/debugging).
* [VS Code를 사용하여 Git 리포지토리를 관리합니다](concept-train-model-git-integration.md).

## <a name="configure-compute-instance-as-remote-notebook-server"></a>컴퓨팅 인스턴스를 원격 Notebook 서버로 구성

컴퓨팅 인스턴스를 원격 Jupyter Notebook 서버로 구성하려면 몇 가지 필수 구성 요소가 필요합니다.

* Azure Machine Learning Visual Studio Code 확장. 자세한 내용은 [Azure Machine Learning Visual Studio Code 확장 설정](how-to-setup-vs-code.md)을 참조하세요.
* Azure Machine Learning 작업 영역. 아직 없는 경우 [Azure Machine Learning Visual Studio Code 확장을 사용하여 새 작업 영역을 만듭니다](how-to-manage-resources-vscode.md#create-a-workspace).

컴퓨팅 인스턴스에 연결하려면:

1. Visual Studio Code에서 Jupyter Notebook을 엽니다.
1. 통합된 Notebook 환경이 로드되면 **Jupyter 서버** 를 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![Azure Machine Learning 원격 Jupyter Notebook 서버 시작 드롭다운](media/how-to-set-up-vs-code-remote/launch-server-selection-dropdown.png)

    또는 명령 팔레트를 사용할 수도 있습니다.

    1. 메뉴 모음에서 **보기 > 명령 팔레트** 를 차례로 선택하여 명령 팔레트를 엽니다.
    1. 텍스트 상자 `Azure ML: Connect to Compute instance Jupyter server`에 입력합니다.

1. Jupyter 서버 옵션 목록에서 `Azure ML Compute Instances`를 선택합니다.
1. 구독 목록에서 구독을 선택합니다. 이전에 기본 Azure Machine Learning 작업 영역을 구성한 경우 이 단계를 건너뜁니다.
1. 작업 영역을 선택합니다.
1. 목록에서 컴퓨팅 인스턴스를 선택합니다. 없는 경우 **새 Azure ML 컴퓨팅 인스턴스 만들기** 를 선택하고 표시되는 메시지에 따라 하나를 만듭니다.
1. 변경 내용을 적용하려면 Visual Studio Code을 다시 로드해야 합니다.
1. Jupyter Notebook을 열고 셀을 실행합니다.

> [!IMPORTANT]
> 연결을 설정하려면 셀을 실행 **해야** 합니다.

이 시점에서는 Jupyter Notebook에서 셀을 계속 실행할 수 있습니다.

> [!TIP]
> Jupyter와 같은 코드 셀을 포함하는 Python 스크립트 파일(.py)로 작업할 수도 있습니다. 자세한 내용은 [Visual Studio Code Python 대화형 문서](https://code.visualstudio.com/docs/python/jupyter-support-py)를 참조하세요.

## <a name="next-steps"></a>다음 단계

이제 VS Code 원격을 설정했으므로 컴퓨팅 인스턴스를 Visual Studio Code의 원격 컴퓨팅으로 사용하여 [코드를 대화형으로 디버그](how-to-debug-visual-studio-code.md)할 수 있습니다.

[자습서: 첫 번째 ML 모델 학습](tutorial-1st-experiment-sdk-train.md)에서는 통합 Notebook으로 컴퓨팅 인스턴스를 사용하는 방법을 보여 줍니다.

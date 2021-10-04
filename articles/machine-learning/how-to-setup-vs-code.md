---
title: Visual Studio Code 확장(미리 보기) 설정
titleSuffix: Azure Machine Learning
description: Azure Machine Learning Visual Studio Code 확장을 설정 하는 방법에 대해 알아봅니다.
services: machine-learning
author: luisquintanilla
ms.author: luquinta
ms.service: machine-learning
ms.subservice: core
ms.date: 05/25/2021
ms.topic: how-to
ms.custom: devplatv2
ms.openlocfilehash: 2b175d209c6506cb802e0105dff03c7c0f6e6ad9
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/04/2021
ms.locfileid: "129425034"
---
# <a name="set-up-the-visual-studio-code-azure-machine-learning-extension-preview"></a>Visual Studio Code Azure Machine Learning 확장(미리 보기) 설정

기계 학습 워크플로에 대한 Azure Machine Learning Visual Studio Code 확장을 설정하는 방법을 알아봅니다.

> [!div class="mx-imgBorder"]
> ![VS Code 확장](./media/how-to-setup-vs-code/vs-code-extension.PNG)

VS Code용 Azure Machine Learning 확장은 다음에 대한 사용자 인터페이스를 제공합니다.

- Azure Machine Learning 리소스(실험, 가상 머신, 모델, 배포 등) 관리
- 원격 컴퓨팅 인스턴스를 사용하여 로컬로 개발
- 기계 학습 모델 학습
- 로컬로 기계 학습 실험 디버그
- 사양 파일 작성을 위한 스키마 기반 언어 지원, 자동 완성 및 진단
- 일반적인 작업에 대한 코드 조각

## <a name="prerequisites"></a>필수 구성 요소

- 동작합니다. 구독이 없는 경우 [Azure Machine Learning의 평가판 또는 유료 버전](https://azure.microsoft.com/free/)에 가입하여 사용해 보세요.
- Visual Studio Code. 없는 경우 [설치](https://code.visualstudio.com/docs/setup/setup-overview)합니다.
- [Python](https://www.python.org/downloads/)
- (선택 사항) 확장을 사용하여 리소스를 만들려면 CLI(v2)를 설치해야 합니다. 설치 지침은 [CLI(v2) 설치, 설정 및 사용(미리 보기)](how-to-configure-cli.md)을 참조하세요.

## <a name="install-the-extension"></a>확장 설치

1. Visual Studio Code를 엽니다.
1. **작업 막대** 에서 **확장** 아이콘을 선택하여 [확장] 보기를 엽니다.
1. [확장] 보기에서 "Azure Machine Learning"을 검색합니다.
1. **설치** 를 선택합니다.

    > [!div class="mx-imgBorder"]
    > ![Azure Machine Learning VS Code 확장 설치](./media/how-to-setup-vs-code/install-aml-vscode-extension.PNG)

> [!NOTE]
> 또는 [설치 관리자를 직접 다운로드](https://aka.ms/vscodetoolsforai)하여 Visual Studio Marketplace를 통해 Azure Machine Learning 확장을 설치할 수 있습니다.

이 자습서의 나머지 단계는 최신 버전의 확장으로 테스트되었습니다.

> [!NOTE]
> Azure Machine Learning VS Code 확장은 기본적으로 CLI(v2)를 사용합니다. 1\.0 CLI로 전환하려면 Visual Studio Code의 `azureML.CLI Compatibility Mode` 설정을 `1.0`으로 설정합니다. Visual Studio에서 설정을 수정하는 방법에 대한 자세한 내용은 [사용자 및 작업 영역 설정 설명서](https://code.visualstudio.com/docs/getstarted/settings)를 참조하세요.

## <a name="sign-in-to-your-azure-account"></a>Azure 계정에 로그인

Azure에서 리소스를 프로비저닝하고 워크로드를 실행하려면 Azure 계정 자격 증명을 사용하여 로그인해야 합니다. 계정 관리를 지원하기 위해 Azure Machine Learning에서 Azure 계정 확장을 자동으로 설치합니다. 다음 사이트를 방문하여 [Azure 계정 확장에 대해 자세히 알아보세요](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

Azure 계정에 로그인하려면 Visual Studio Code 상태 표시줄에서 **Azure: 로그인** 단추를 선택하여 로그인 프로세스를 시작합니다.

또는 명령 팔레트를 사용합니다.

1. 메뉴 모음에서 **보기 > 명령 팔레트** 를 차례로 선택하여 명령 팔레트를 엽니다.
1. 명령 팔레트에 "> Azure: 로그인" 명령을 입력하여 로그인 프로세스를 시작합니다.

## <a name="choose-your-default-workspace"></a>기본 작업 영역 선택

기본 Azure Machine Learning 작업 영역을 선택하면 CLI(v2) YAML 사양 파일을 작성할 때 다음을 사용할 수 있습니다.

- 스키마 유효성 검사
- 자동 완성
- 진단

작업 영역이 없으면 새로 만듭니다. 자세한 내용은 [VS Code 확장을 사용하여 Azure Machine Learning 리소스 관리](how-to-manage-resources-vscode.md)를 참조하세요.

기본 작업 영역을 선택하려면 Visual Studio Code 상태 표시줄에서 **Azure ML 작업 영역 설정 단추** 를 선택하고 프롬프트에 따라 작업 영역을 설정합니다.

또는 명령 팔레트에서 `> Azure ML: Set Default Workspace` 명령을 사용하고 프롬프트에 따라 작업 영역을 설정합니다.

## <a name="next-steps"></a>다음 단계

- [Azure Machine Learning 리소스 관리](how-to-manage-resources-vscode.md)
- [원격 컴퓨팅 인스턴스에서 로컬로 개발](how-to-set-up-vs-code-remote.md)
- [컴퓨팅 인스턴스를 원격 Jupyter 서버로 사용](how-to-set-up-vs-code-remote.md)
- [Visual Studio Code 확장을 사용하여 이미지 분류 모델 학습](tutorial-train-deploy-image-classification-model-vscode.md)
- [로컬로 기계 학습 실험 실행 및 디버그](how-to-debug-visual-studio-code.md)
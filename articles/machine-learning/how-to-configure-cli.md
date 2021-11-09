---
title: CLI(v2) 설치 및 설정
titleSuffix: Azure Machine Learning
description: Machine Learning용 Azure CLI 확장을 설치하고 설정하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
ms.topic: how-to
author: lostmygithubaccount
ms.author: copeters
ms.date: 10/21/2021
ms.reviewer: laobri
ms.custom: devx-track-azurecli, devplatv2
ms.openlocfilehash: 06e623887c1ff503982df19764876cb9e3db2f0e
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132061461"
---
# <a name="install-and-set-up-the-cli-v2"></a>CLI(v2) 설치 및 설정

[Azure CLI](/cli/azure/)에 대한 `ml` 확장(미리 보기)은 Azure Machine Learning을 위한 향상된 인터페이스입니다. 이를 통해 모델 수명 주기를 추적하는 동안 데이터 과학 확장을 가속화하는 기능을 사용하여 명령줄에서 모델을 학습하고 배포할 수 있습니다.

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>사전 요구 사항

- CLI를 사용하려면 Azure 구독이 있어야 합니다. Azure 구독이 없는 경우 시작하기 전에 체험 계정을 만듭니다. 지금 [Azure Machine Learning 평가판 또는 유료 버전](https://azure.microsoft.com/free/)을 사용해 보세요.
- **로컬 환경** 에서 이 문서의 CLI 명령을 사용하려면 [Azure CLI](/cli/azure/install-azure-cli)가 필요합니다.

## <a name="installation"></a>설치

새 Machine Learning 확장에는 **Azure CLI 버전이 필요`>=2.15.0`** 합니다. 다음 요구 사항이 충족되는지 확인합니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="az_version":::

충족되지 않으면 [Azure CLI를 업그레이드](/cli/azure/update-azure-cli)합니다.

설치한 Azure CLI 확장을 확인합니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="az_extension_list":::

`azure-cli-ml` 확장을 포함하여 `ml` 네임스페이스를 사용하는 충돌 확장이 설치되어 있지 않은지 확인합니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="az_extension_remove":::

이제 `ml` 확장을 설치합니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/setup.sh" id="az_ml_install":::

도움말 명령을 실행하여 설치를 확인하고 사용 가능한 하위 명령을 확인합니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="az_ml_verify":::

확장을 최신 버전으로 업그레이드할 수 있습니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="az_ml_update":::

### <a name="installation-on-linux"></a>Linux에서 설치

Linux를 사용하는 경우 필요한 CLI 버전과 Machine Learning 확장을 설치하는 가장 빠른 방법은 다음과 같습니다.

:::code language="bash" source="~/azureml-examples-main/cli/misc.sh" id="az_extension_install_linux":::

자세한 내용은 [Linux용 Azure CLI 설치](/cli/azure/install-azure-cli-linux)를 참조하세요.

## <a name="set-up"></a>설정

로그인:

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="az_login":::

여러 Azure 구독에 대한 액세스 권한이 있는 경우 활성 구독을 설정할 수 있습니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="az_account_set":::

필요에 따라 다음 명령에서 사용 하기 위해 셸에서 일반적인 변수를 설정 합니다.

:::code language="azurecli" source="~/azureml-examples-main/setup-repo/azure-github.sh" id="set_variables":::

> [!WARNING]
> 이는 변수 설정에 Bash 구문을 사용 합니다.--셸에 필요에 따라 조정 합니다. 변수를 사용 하는 대신 아래 명령의 값을 인라인으로 바꿀 수도 있습니다.

Azure 리소스 그룹이 아직 없는 경우 만들 수 있습니다.

:::code language="azurecli" source="~/azureml-examples-main/setup-repo/azure-github.sh" id="az_group_create":::

그리고 machine learning 작업 영역을 만듭니다.

:::code language="azurecli" source="~/azureml-examples-main/setup-repo/azure-github.sh" id="az_ml_workspace_create":::

기계 학습 하위 명령에는 `--workspace/-w` 및 `--resource-group/-g` 매개 변수가 필요합니다. 반복적으로 입력하지 않으려면 기본값을 구성합니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/setup.sh" id="az_configure_defaults":::

> [!TIP]
> 대부분의 코드 예제에서는 기본 작업 영역 및 리소스 그룹을 설정했다고 가정합니다. 명령줄에서 이러한 항목을 재정의할 수 있습니다.

다음을 사용 하 여 현재 기본값을 표시할 수 있습니다 `--list-defaults/-l` .

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="list_defaults":::

> [!TIP]
> 와를 함께 사용 `--output/-o` 하면 더 읽기 쉬운 출력 형식을 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [CLI를 사용 하 여 모델 학습 (v2)](how-to-train-cli.md)
- [Visual Studio Code Azure Machine Learning 확장 설정](how-to-setup-vs-code.md)
- [Azure Machine Learning Visual Studio Code 확장을 사용하여 이미지 분류 TensorFlow 모델 학습](tutorial-train-deploy-image-classification-model-vscode.md)

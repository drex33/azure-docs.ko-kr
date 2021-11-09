---
title: 관리형 온라인 엔드포인트를 사용하여 AutoML 모델 배포(미리 보기)
titleSuffix: Azure Machine Learning
description: AutoML 모델을 Azure에서 자동으로 관리되는 웹 서비스로 배포하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: ssambare
ms.reviewer: laobri
author: shivanissambare
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: how-to, devplatv2
ms.openlocfilehash: 2a2c04795b3cfcf38e3a2b84ccc36c0ee488f5f6
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132059396"
---
# <a name="how-to-deploy-an-automl-model-to-an-online-endpoint-preview"></a>온라인 엔드포인트에 AutoML 모델을 배포하는 방법(미리 보기)

이 문서에서는 AutoML 학습 기계 학습 모델을 온라인 엔드포인트에 배포하는 방법을 알아봅니다. 자동화된 기계 학습(자동화된 ML 또는 AutoML이라고도 함)은 기계 학습 모델을 개발하는 데 시간이 오래 걸리고 반복적인 작업을 자동화하는 프로세스입니다. 자세한 내용은 [AutoML(자동화된 기계 학습)란?을 참조하세요.](concept-automated-ml.md)

이 문서에서는 다음을 사용하여 AutoML 학습 기계 학습 모델을 온라인 엔드포인트에 배포하는 방법을 알아두겠습니다. 

- Azure Machine Learning Studio
- Azure Machine Learning CLI 2.0

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>전제 조건

AutoML 학습 기계 학습 모델입니다. 자세한 내용은 [자습서: Azure Machine Learning Studio에서 코드 없는 AutoML을](tutorial-first-experiment-automated-ml.md) 사용하여 분류 모델 학습 또는 [자습서: 자동화된 기계 학습으로 수요 예측을 참조하세요.](tutorial-automated-ml-forecast.md)

## <a name="deploy-from-azure-machine-learning-studio-and-no-code"></a>Azure Machine Learning Studio에서 배포 및 코드 없음

자동화된 ML 페이지에서 AutoML 학습 모델을 배포하는 것은 코드가 없는 환경입니다. 즉, 점수 매기기 스크립트와 환경을 준비할 필요가 없으며 둘 다 자동으로 생성됩니다. 

1. 스튜디오에서 자동화된 ML 페이지로 이동합니다.
1. 실험을 선택하고 실행
1. 모델 탭 선택
1. 배포하려는 모델 선택 
1. 모델을 선택하면 배포 단추가 드롭다운 메뉴와 함께 켜집니다.
1. *실시간 엔드포인트에 배포(미리 보기)* 옵션을 선택합니다.

   :::image type="content" source="media/how-to-deploy-automl-endpoint/deploy-button.png" lightbox="media/how-to-deploy-automl-endpoint/deploy-button.png" alt-text="배포 단추의 드롭다운 메뉴를 보여주는 스크린샷":::

   시스템은 배포에 필요한 모델 및 환경을 생성합니다. 

   :::image type="content" source="media/how-to-deploy-automl-endpoint/model.png" lightbox="media/how-to-deploy-automl-endpoint/model.png" alt-text="생성된 모델을 보여주는 스크린샷":::

   :::image type="content" source="media/how-to-deploy-automl-endpoint/environment.png" lightbox="media/how-to-deploy-automl-endpoint/environment.png" alt-text="생성된 환경을 보여주는 스크린샷":::

5. 마법사를 완료하여 실시간 엔드포인트에 모델 배포

 :::image type="content" source="media/how-to-deploy-automl-endpoint/complete-wizard.png" lightbox="media/how-to-deploy-automl-endpoint/complete-wizard.png"  alt-text="검토 및 만들기 페이지를 보여주는 스크린샷":::


## <a name="deploy-manually-from-the-studio-or-command-line"></a>스튜디오 또는 명령줄에서 수동으로 배포

배포를 보다 세분화하려는 경우 학습 아티팩트 다운로드 및 배포를 할 수 있습니다. 

구성 요소를 다운로드하려면 배포에 필요합니다.

1. 자동화된 ML 실험으로 이동하여 기계 학습 작업 영역에서 실행
1. 모델 탭 선택
1. 사용하려는 모델을 선택합니다. 모델을 선택하면 *다운로드* 단추가 사용하도록 설정됩니다.
1. *다운로드* 선택

:::image type="content" source="media/how-to-deploy-automl-endpoint/download-model.png" lightbox="media/how-to-deploy-automl-endpoint/download-model.png" alt-text="모델 선택 및 다운로드 단추를 보여주는 스크린샷":::

다음을 포함하는 zip 파일을 받게 됩니다.
* 라는 conda 환경 사양 파일 `conda_env_<VERSION>.yml`
* 라는 Python 점수 매기기 파일 `scoring_file_<VERSION>.py`
* 라는 Python 파일의 모델 자체 `.pkl``model.pkl`

이러한 파일을 사용하여 배포하려면 studio 또는 Azure 명령줄 인터페이스를 사용할 수 있습니다.

# <a name="studio"></a>[스튜디오](#tab/Studio)

1. Azure Machine Learning Studio의 모델 페이지로 이동합니다.

1. + 모델 등록 옵션을 클릭합니다.

1. 자동화된 ML 실행에서 다운로드한 모델 등록

1. 환경 페이지로 이동하여 사용자 지정 환경을 선택하고 + 만들기 옵션을 선택하여 배포 환경을 만듭니다. 다운로드한 conda yaml을 사용하여 사용자 지정 환경 만들기

1. 모델을 선택하고 배포 드롭다운 옵션에서 실시간 엔드포인트에 배포를 선택합니다.

1. 마법사의 모든 단계를 완료하여 온라인 엔드포인트 및 배포 만들기

 
# <a name="cli"></a>[CLI](#tab/CLI)

## <a name="configure-the-cli"></a>CLI 구성 

CLI에서 배포를 만들려면 ML v2 확장이 있는 Azure CLI 필요합니다. 다음 명령을 실행하여 둘 다 확인합니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="az_version":::

오류 메시지가 표시되거나 응답에 표시되지 않는 경우 `Extensions: ml` CLI 설치 [및 설정(v2)](how-to-configure-cli.md)의 단계를 수행합니다.

로그인:

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="az_login":::

여러 Azure 구독에 액세스한 경우 활성 구독을 설정할 수 있습니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="az_account_set":::

배포를 만들려는 위치로 기본 리소스 그룹 및 작업 영역을 설정합니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/setup.sh" id="az_configure_defaults":::

## <a name="put-the-scoring-file-in-its-own-directory"></a>점수 매기기 파일을 자체 디렉터리에 배치

라는 디렉터리를 만들고 다운로드한 점수 매기기 파일을 이 디렉터리에 `src/` 배치합니다. 이 디렉터리 Azure에 업로드되며 유추를 수행하는 데 필요한 모든 소스 코드를 포함합니다. AutoML 모델의 경우 단일 점수 매기기 파일만 있습니다. 

## <a name="create-the-endpoint-and-deployment-yaml-file"></a>엔드포인트 및 배포 yaml 파일 만들기

명령줄에서 관리되는 온라인 엔드포인트를 만들려면 *endpoint.yml* 및 *deployment.yml* 파일을 만들어야 합니다. [Azure Machine Learning 예제 리포지션에서](https://github.com/Azure/azureml-examples) 가져온 다음 코드는 필요한 모든 입력을 캡처하는 _엔드포인트/online/managed/sample/을_ 보여줍니다.

__automl_endpoint.yml__

::: code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/managed/sample/endpoint.yml" :::

__automl_deployment.yml__

::: code language="yaml" source="~/azureml-examples-main/cli/endpoints/online/managed/sample/blue-deployment.yml" :::

AutoML 모델 페이지에서 다운로드한 파일을 사용하도록 이 파일을 수정해야 합니다.

1. 파일을 만들고 `automl_endpoint.yml` `automl_deployment.yml` 위 예제의 내용을 붙여넣습니다.

1. `name`엔드포인트의 값을 변경합니다. 엔드포인트 이름은 Azure 지역 내에서 고유해야 합니다. 엔드포인트의 이름은 대문자 또는 소문자로 시작해야 하며 ' 및 영숫자 문자로만 구성되어야 합니다.

1. `automl_deployment`파일에서 다음 경로의 키 값을 변경합니다.

    | 경로 | 다음으로 변경 |
    | --- | --- |
    | `model:local_path` | `model.pkl`다운로드한 파일의 경로입니다. |
    | `code_configuration:code:local_path` | 점수 매기기 파일을 배치한 디렉터리입니다. | 
    | `code_configuration:scoring_script` | Python 점수 매기기 파일의 이름( `scoring_file_<VERSION>.py` )입니다. |
    | `environment:conda_file` | 다운로드한 conda 환경 파일()에 대한 파일 `conda_env_<VERSION>.yml` URL입니다. |

    > [!NOTE]
    > YAML에 대한 전체 설명은 [관리형 온라인 엔드포인트(미리 보기) YAML 참조](reference-yaml-endpoint-managed-online.md)를 참조하세요.

3. 명령줄에서 다음을 실행합니다. 

    ```azurecli
    az ml online-endpoint create -f automl_endpoint.yml
    az ml online-deployment create -f automl_deployment.yml
    ```
    
---

배포를 만든 [후에는 모델을 사용하여 엔드포인트를 호출하여 데이터 점수 매기기에서 설명한](how-to-deploy-managed-online-endpoints.md#invoke-the-endpoint-to-score-data-by-using-your-model)대로 점수를 매할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [관리형 온라인 엔드포인트 배포 문제 해결](how-to-troubleshoot-managed-online-endpoints.md)
- [온라인 엔드포인트에 대한 안전한 롤아웃](how-to-safely-rollout-managed-endpoints.md)

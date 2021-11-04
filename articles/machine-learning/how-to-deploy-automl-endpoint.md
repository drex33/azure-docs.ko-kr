---
title: 관리 되는 온라인 끝점 (미리 보기)을 사용 하 여 AutoML 모델 배포
titleSuffix: Azure Machine Learning
description: AutoML 모델을 Azure에서 자동으로 관리 하는 웹 서비스로 배포 하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: ssambare
ms.reviewer: laobri
author: shivanissambare
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: how-to, devplatv2
ms.openlocfilehash: 5bc5208de912a6f11271681b754736bfafe17e8b
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131570143"
---
# <a name="how-to-deploy-an-automl-model-to-an-online-endpoint-preview"></a>온라인 끝점에 AutoML 모델을 배포 하는 방법 (미리 보기)

이 문서에서는 온라인 끝점에 AutoML 학습 된 machine learning 모델을 배포 하는 방법에 대해 알아봅니다. 자동화 된 ML 또는 automl이 라고도 하는 자동화 된 machine learning은 기계 학습 모델을 개발 하는 시간이 많이 걸리는 반복적인 작업을 자동화 하는 프로세스입니다. 자세한 내용은 [AutoML (자동화 된 기계 학습) 이란?](concept-automated-ml.md)을 참조 하세요.

이 문서에서는 다음을 사용 하 여 온라인 끝점에 AutoML 학습 된 machine learning 모델을 배포 하는 방법을 알아봅니다. 

- Azure Machine Learning Studio
- Azure Machine Learning CLI 2.0

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

## <a name="prerequisites"></a>사전 요구 사항

AutoML 학습 된 기계 학습 모델입니다. 자세한 내용은 [자습서: Azure Machine Learning studio에서 코드 없는 automl을 사용 하 여 분류 모델 학습](tutorial-first-experiment-automated-ml.md) 또는 [자습서: 자동화 된 기계 학습으로 수요 예측](tutorial-automated-ml-forecast.md)을 참조 하세요.

## <a name="deploy-from-azure-machine-learning-studio-and-no-code"></a>Azure Machine Learning studio에서 배포 및 코드 없음

자동화 된 ML 페이지에서 자동 ML 학습 된 모델을 배포 하는 것은 코드 없는 환경입니다. 즉, 점수 매기기 스크립트 및 환경을 준비할 필요가 없습니다. 둘 다 자동 생성 됩니다. 

1. 스튜디오에서 자동화 된 ML 페이지로 이동
1. 실험을 선택 하 고 실행 합니다.
1. 모델 탭을 선택 합니다.
1. 배포 하려는 모델을 선택 합니다. 
1. 모델을 선택 하면 배포 단추가 드롭다운 메뉴와 함께 표시 됩니다.
1. *실시간 끝점에 배포 (미리 보기) 옵션을 선택 합니다* .

   :::image type="content" source="media/how-to-deploy-automl-endpoint/deploy-button.png" lightbox="media/how-to-deploy-automl-endpoint/deploy-button.png" alt-text="배포 단추의 드롭다운 메뉴를 보여 주는 스크린샷":::

   시스템은 배포에 필요한 모델 및 환경을 생성 합니다. 

   :::image type="content" source="media/how-to-deploy-automl-endpoint/model.png" lightbox="media/how-to-deploy-automl-endpoint/model.png" alt-text="생성 된 모델을 보여 주는 스크린샷":::

   :::image type="content" source="media/how-to-deploy-automl-endpoint/environment.png" lightbox="media/how-to-deploy-automl-endpoint/environment.png" alt-text="생성 된 환경을 보여 주는 스크린샷":::

5. 모델을 실시간 끝점에 배포 하는 마법사 완료

 :::image type="content" source="media/how-to-deploy-automl-endpoint/complete-wizard.png" lightbox="media/how-to-deploy-automl-endpoint/complete-wizard.png"  alt-text="검토 및 만들기 페이지를 보여 주는 스크린샷":::


## <a name="deploy-manually-from-the-studio-or-command-line"></a>스튜디오 또는 명령줄에서 수동으로 배포

배포를 보다 효율적으로 제어 하려는 경우 학습 아티팩트를 다운로드 하 고 배포할 수 있습니다. 

배포에 필요한 구성 요소를 다운로드 하려면 다음을 수행 합니다.

1. 자동화 된 ML 실험으로 이동 하 여 machine learning 작업 영역에서 실행
1. 모델 탭을 선택 합니다.
1. 사용할 모델을 선택 합니다. 모델을 선택 하면 *다운로드* 단추를 사용할 수 있게 됩니다.
1. *다운로드* 선택

:::image type="content" source="media/how-to-deploy-automl-endpoint/download-model.png" lightbox="media/how-to-deploy-automl-endpoint/download-model.png" alt-text="모델 및 다운로드 단추의 선택을 보여 주는 스크린샷":::

다음을 포함 하는 zip 파일을 받게 됩니다.
* Conda 환경 사양 파일 이름 `conda_env_<VERSION>.yml`
* 이라는 Python 점수 매기기 파일 `scoring_file_<VERSION>.py`
* 라는 Python 파일의 모델 자체 `.pkl``model.pkl`

이러한 파일을 사용 하 여 배포 하려면 스튜디오 또는 Azure 명령줄 인터페이스를 사용할 수 있습니다.

# <a name="studio"></a>[스튜디오](#tab/Studio)

1. Azure machine learning studio의 모델 페이지로 이동 합니다.

1. + 모델 등록 옵션을 클릭 합니다.

1. 자동화 된 ML 실행에서 다운로드 한 모델 등록

1. 환경 페이지로 이동 하 여 사용자 지정 환경을 선택 하 고 + 만들기 옵션을 선택 하 여 배포를 위한 환경을 만듭니다. 다운로드 한 conda yaml을 사용 하 여 사용자 지정 환경 만들기

1. 모델을 선택 하 고 배포 드롭다운 옵션에서 실시간 끝점에 배포를 선택 합니다.

1. 온라인 끝점 및 배포를 만들기 위해 마법사의 모든 단계를 완료 합니다.

 
# <a name="cli"></a>[CLI](#tab/CLI)

## <a name="configure-the-cli"></a>CLI 구성 

CLI에서 배포를 만들려면 ML v2 확장을 사용 하는 Azure CLI 필요 합니다. 다음 명령을 실행 하 여 둘 다 있는지 확인 합니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="az_version":::

오류 메시지가 표시 되거나 응답이 표시 되지 않으면 `Extensions: ml` [CLI 설치 및 설정 (v2)](how-to-configure-cli.md)의 단계를 따르세요.

로그인:

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="az_login":::

여러 Azure 구독에 액세스할 수 있는 경우 활성 구독을 설정할 수 있습니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/misc.sh" id="az_account_set":::

배포를 만들려는 기본 리소스 그룹 및 작업 영역을 설정 합니다.

:::code language="azurecli" source="~/azureml-examples-main/cli/setup.sh" id="az_configure_defaults":::

## <a name="put-the-scoring-file-in-its-own-directory"></a>자신의 디렉터리에 점수 매기기 파일 저장

라는 디렉터리를 만들고 `src/` 이 디렉터리에 다운로드 한 점수 매기기 파일을 저장 합니다. 이 디렉터리는 Azure에 업로드 되 고 유추를 수행 하는 데 필요한 모든 소스 코드를 포함 합니다. AutoML 모델의 경우 점수 매기기 파일은 하나 뿐입니다. 

## <a name="create-the-endpoint-and-deployment-yaml-file"></a>끝점 및 배포 yaml 파일 만들기

명령줄에서 관리 되는 온라인 끝점을 만들려면 *.yml* 및 *배포 .yml* 파일을 만들어야 합니다. [Azure Machine Learning 예제](https://github.com/Azure/azureml-examples) 리포지토리에서 가져온 다음 코드는 모든 필수 입력을 캡처하는 _끝점/온라인/관리/샘플/_ 을 보여 줍니다.

__automl_endpoint .yml__

::: code language="yaml" source="~/azureml-examples-cli-preview/cli/endpoints/online/managed/sample/endpoint.yml" :::

__automl_deployment .yml__

::: code language="yaml" source="~/azureml-examples-cli-preview/cli/endpoints/online/managed/sample/blue-deployment.yml" :::

AutoML 모델 페이지에서 다운로드 한 파일을 사용 하려면이 파일을 수정 해야 합니다.

1. 파일을 만들고 `automl_endpoint.yml` 위의 예에 나와 있는 `automl_deployment.yml` 내용을 붙여넣습니다.

1. 끝점의 값을 변경 합니다 `name` . 끝점 이름은 Azure 지역 내에서 고유 해야 합니다. 끝점의 이름은 대문자 또는 소문자를 사용 하 여 시작 해야 하 고 '-' 및 영숫자 문자로만 구성 됩니다.

1. 파일에서 `automl_deployment` 다음 경로에 있는 키의 값을 변경 합니다.

    | 경로 | 다음으로 변경 |
    | --- | --- |
    | `model:local_path` | `model.pkl`다운로드 한 파일의 경로입니다. |
    | `code_configuration:code:local_path` | 점수 매기기 파일을 저장 한 디렉터리입니다. | 
    | `code_configuration:scoring_script` | Python 점수 매기기 파일의 이름 ( `scoring_file_<VERSION>.py` )입니다. |
    | `environment:conda_file` | 다운로드 한 conda 환경 파일 ()에 대 한 파일 URL `conda_env_<VERSION>.yml` 입니다. |

    > [!NOTE]
    > YAML에 대한 전체 설명은 [관리형 온라인 엔드포인트(미리 보기) YAML 참조](reference-yaml-endpoint-managed-online.md)를 참조하세요.

3. 명령줄에서 다음을 실행 합니다. 

    ```azurecli
    az ml online-endpoint create -f automl_endpoint.yml
    az ml online-deployment create -f automl_deployment.yml
    ```
    
---

배포를 만든 후에 [는 모델을 사용 하 여 데이터의 점수를 매기는 끝점 호출](how-to-deploy-managed-online-endpoints.md#invoke-the-endpoint-to-score-data-by-using-your-model)에 설명 된 대로 점수를 매길 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [관리형 온라인 엔드포인트 배포 문제 해결](how-to-troubleshoot-managed-online-endpoints.md)
- [온라인 엔드포인트에 대한 안전한 롤아웃](how-to-safely-rollout-managed-endpoints.md)

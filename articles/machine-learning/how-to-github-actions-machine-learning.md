---
title: CI/CD에 대한 GitHub Actions
titleSuffix: Azure Machine Learning
description: Azure Machine Learning에서 모델을 학습하는 GitHub Actions 워크플로를 만드는 방법에 대해 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: mlops
author: juliakm
ms.author: jukullam
ms.date: 10/19/2020
ms.topic: how-to
ms.custom: github-actions-azure
ms.openlocfilehash: ba3ac45c04ef359db56086ff8ddf75b428574062
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/04/2021
ms.locfileid: "129427460"
---
# <a name="use-github-actions-with-azure-machine-learning"></a>Azure Machine Learning에서 GitHub Actions 사용

Azure Machine Learning에서 모델을 학습하려면 [GitHub Actions](https://docs.github.com/en/actions)로 시작하세요. 

> [!NOTE]
> Azure Machine Learning을 위한 GitHub Actions은 있는 그대로 제공되며, Microsoft에서 완벽하게 지원하지는 않습니다. 특정 작업에 문제가 발생하는 경우, 작업의 리포지토리에서 이슈를 만듭니다. 예를 들어 aml-deploy 작업에 문제가 발생하는 경우, [https://github.com/Azure/aml-deploy]( https://github.com/Azure/aml-deploy) 리포지토리의 문제를 보고합니다.

## <a name="prerequisites"></a>사전 요구 사항 

- 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- GitHub 계정. 없는 경우 [평가판](https://github.com/join)에 등록하세요.  

## <a name="workflow-file-overview"></a>워크플로 파일 개요

워크플로는 리포지토리의 `/.github/workflows/` 경로에 있는 YAML(.yml) 파일에서 정의됩니다. 이 정의는 워크플로를 구성하는 다양한 단계와 매개 변수를 포함합니다.

이 파일에는 다음 네 가지 섹션이 있습니다.

|섹션  |작업  |
|---------|---------|
|**인증** | 1. 서비스 주체를 정의합니다. <br /> 2. GitHub 비밀을 만듭니다. |
|**연결** | 1. 기계 학습 작업 영역에 연결합니다. <br /> 2. 컴퓨팅 대상에 연결합니다. |
|**실행** | 1. 학습 실행을 제출합니다. |
|**배포** | 1. Azure Machine Learning 레지스트리에 모델을 등록합니다. 1. 모델을 배포합니다. |

## <a name="create-repository"></a>리포지토리 만들기

[GitHub Actions 및 Azure Machine Learning 템플릿을 사용하여 ML Ops](https://github.com/machine-learning-apps/ml-template-azure)에서 새 리포지토리를 만듭니다. 

1. GitHub에서 [템플릿을](https://github.com/machine-learning-apps/ml-template-azure) 엽니다. 
2. **이 템플릿 사용** 을 선택합니다. 

    :::image type="content" source="media/how-to-github-actions-machine-learning/gh-actions-use-template.png" alt-text="이 템플릿 사용을 선택합니다.":::
3. 템플릿에서 새 리포지토리를 만듭니다. 리포지토리 이름을 `ml-learning` 또는 원하는 이름으로 설정합니다. 


## <a name="generate-deployment-credentials"></a>배포 자격 증명 생성

[Azure CLI](/cli/azure/)에서 [az ad sp create-for-rbac](/cli/azure/ad/sp#az_ad_sp_create_for_rbac) 명령을 사용하여 [서비스 주체](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object)를 만들 수 있습니다. 이 명령은 Azure Portal에서 [Azure Cloud Shell](https://shell.azure.com/)을 사용하거나 **사용해 보세요** 단추를 선택하여 실행합니다.

```azurecli-interactive
az ad sp create-for-rbac --name "myML" --role contributor \
                            --scopes /subscriptions/<subscription-id>/resourceGroups/<group-name> \
                            --sdk-auth
```

위의 예시에서 자리 표시자를 구독 ID, 리소스 그룹 이름 및 앱 이름으로 바꿉니다. 출력은 아래와 비슷한 App Service 앱에 대한 액세스를 제공하는 역할 할당 자격 증명이 있는 JSON 개체입니다. 나중에 사용할 수 있도록 이 JSON 개체를 복사합니다.

```output 
  {
    "clientId": "<GUID>",
    "clientSecret": "<GUID>",
    "subscriptionId": "<GUID>",
    "tenantId": "<GUID>",
    (...)
  }
```

## <a name="configure-the-github-secret"></a>GitHub 비밀 구성

1. [GitHub](https://github.com/)에서 리포지토리를 검색하고 **설정 > 비밀 > 새 비밀 추가** 를 선택합니다.

2. Azure CLI 명령의 전체 JSON 출력을 비밀의 값 필드에 붙여넣습니다. 비밀 이름을 `AZURE_CREDENTIALS`로 지정합니다.

## <a name="connect-to-the-workspace"></a>작업 영역에 연결

[Azure Machine Learning 작업 영역 작업](https://github.com/marketplace/actions/azure-machine-learning-workspace)을 사용하여 Azure Machine Learning 작업 영역에 연결합니다. 

```yaml
    - name: Connect/Create Azure Machine Learning Workspace
      id: aml_workspace
      uses: Azure/aml-workspace@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
```

기본값으로 이 작업에는 `workspace.json` 파일이 필요합니다. JSON 파일에 다른 이름이 있는 경우, `parameters_file` 입력 매개 변수를 사용하여 지정할 수 있습니다. 파일이 없는 경우 리포지토리 이름으로 새 파일이 생성됩니다.


```yaml
    - name: Connect/Create Azure Machine Learning Workspace
      id: aml_workspace
      uses: Azure/aml-workspace@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          parameters_file: "alternate_workspace.json"
```
작업은 구성 파일에 작업 영역 Azure Resource Manager (ARM) 속성을 기록합니다. 이 파일은 이후의 모든 Azure Machine Learning GitHub Actions에서 선택할 수 있습니다. 파일은 `GITHUB_WORKSPACE/aml_arm_config.json`에 저장됩니다. 

## <a name="connect-to-a-compute-target-in-azure-machine-learning"></a>Azure Machine Learning에서 컴퓨팅 대상에 연결

[Azure Machine Learning 컴퓨팅 작업](https://github.com/Azure/aml-compute)을 사용하여 Azure Machine Learning에서 컴퓨팅 대상에 연결합니다.  컴퓨팅 대상이 있는 경우 작업이 해당 대상에 연결됩니다. 그렇지 않으면 작업에서 새 컴퓨팅 대상이 생성됩니다. [AML 컴퓨팅 작업](https://github.com/Azure/aml-compute)은 Azure ML 컴퓨팅 클러스터 및 Azure Kubernetes Service (AKS)만 지원합니다. 

```yaml
    - name: Connect/Create Azure Machine Learning Compute Target
      id: aml_compute_training
      uses: Azure/aml-compute@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
```
## <a name="submit-training-run"></a>교육 실행 제출

[Azure Machine Learning 학습 작업](https://github.com/Azure/aml-run)을 사용하여 Azure Machine Learning에 대해 ScriptRun, 예측 도구 또는 파이프라인을 제출합니다. 

```yaml
    - name: Submit training run
      id: aml_run
      uses: Azure/aml-run@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
```

## <a name="register-model-in-registry"></a>레지스트리에 모델 등록

[Azure Machine Learning 모델 등록 작업](https://github.com/Azure/aml-registermodel)을 사용하여 Azure Machine Learning에 모델을 등록합니다.

```yaml
    - name: Register model
      id: aml_registermodel
      uses: Azure/aml-registermodel@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          run_id:  ${{ steps.aml_run.outputs.run_id }}
          experiment_name: ${{ steps.aml_run.outputs.experiment_name }}
```

## <a name="deploy-model-to-azure-machine-learning-to-aci"></a>Azure Machine Learning의 모델을 ACI에 배포

[Azure Machine Learning 배포 작업](https://github.com/Azure/aml-deploy)을 사용하여 모델을 배포하고 모델의 엔드포인트를 만들 수 있습니다. Azure Machine Learning 배포를 사용하여 Azure Kubernetes Service를 배포할 수도 있습니다. Azure Kubernetes Service에 배포하는 모델에 대한 [이 샘플 워크플로](https://github.com/Azure-Samples/mlops-enterprise-template)를 참조하세요.

```yaml
    - name: Deploy model
      id: aml_deploy
      uses: Azure/aml-deploy@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          model_name:  ${{ steps.aml_registermodel.outputs.model_name }}
          model_version: ${{ steps.aml_registermodel.outputs.model_version }}

```

## <a name="complete-example"></a>전체 예제

Azure Machine Learning 모델 학습 및 배포 

```yaml
# Actions train a model on Azure Machine Learning
name: Azure Machine Learning training and deployment
on:
  push:
    branches:
      - master
    # paths:
    #   - 'code/*'
jobs:
  train:
    runs-on: ubuntu-latest
    steps:
    # Checks-out your repository under $GITHUB_WORKSPACE, so your job can access it
    - name: Check Out Repository
      id: checkout_repository
      uses: actions/checkout@v2
        
    # Connect or Create the Azure Machine Learning Workspace
    - name: Connect/Create Azure Machine Learning Workspace
      id: aml_workspace
      uses: Azure/aml-workspace@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
    
    # Connect or Create a Compute Target in Azure Machine Learning
    - name: Connect/Create Azure Machine Learning Compute Target
      id: aml_compute_training
      uses: Azure/aml-compute@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
    
    # Submit a training run to the Azure Machine Learning
    - name: Submit training run
      id: aml_run
      uses: Azure/aml-run@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}

    # Register model in Azure Machine Learning model registry
    - name: Register model
      id: aml_registermodel
      uses: Azure/aml-registermodel@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          run_id:  ${{ steps.aml_run.outputs.run_id }}
          experiment_name: ${{ steps.aml_run.outputs.experiment_name }}

    # Deploy model in Azure Machine Learning to ACI
    - name: Deploy model
      id: aml_deploy
      uses: Azure/aml-deploy@v1
      with:
          azure_credentials: ${{ secrets.AZURE_CREDENTIALS }}
          model_name:  ${{ steps.aml_registermodel.outputs.model_name }}
          model_version: ${{ steps.aml_registermodel.outputs.model_version }}

```

## <a name="clean-up-resources"></a>리소스 정리

리소스 그룹 및 리포지토리가 더 이상 필요하지 않은 경우 리소스 그룹과 GitHub 리포지토리를 삭제하여 배포한 리소스를 정리합니다. 

## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [Azure Machine Learning SDK를 사용하여 기계 학습 파이프라인 만들기 및 실행](./how-to-create-machine-learning-pipelines.md)
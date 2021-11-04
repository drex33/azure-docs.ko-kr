---
title: 소프트웨어 환경 사용
titleSuffix: Azure Machine Learning
description: 모델 학습 및 배포를 위한 환경을 만들고 관리합니다. Python 패키지 및 환경에 대한 기타 설정을 관리합니다.
services: machine-learning
author: saachigopal
ms.author: sagopal
ms.reviewer: nibaccam
ms.service: machine-learning
ms.subservice: core
ms.date: 10/21/2021
ms.topic: how-to
ms.custom: devx-track-python
ms.openlocfilehash: ca2a58e9498a6ce2ef04d1ce568b965341b2dff5
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131553244"
---
# <a name="create--use-software-environments-in-azure-machine-learning"></a>Azure Machine Learning에서 소프트웨어 환경 만들기 및 사용


이 문서에서는 Azure Machine Learning [환경](/python/api/azureml-core/azureml.core.environment.environment)을 만들고 관리하는 방법에 대해 알아봅니다. 환경을 사용하면 프로젝트의 소프트웨어 종속성이 진화함에 따라 추적하고 재현할 수 있습니다.

소프트웨어 종속성 관리는 개발자의 일반적인 작업입니다. 광범위한 수동 소프트웨어 구성 없이 빌드를 재현할 수 있는지 확인하려고 합니다. Azure Machine Learning `Environment` 클래스는 pip, Conda와 같은 로컬 개발 솔루션과 Docker 기능을 통한 분산 클라우드 개발을 고려합니다.

이 문서의 예제는 다음 방법을 보여 줍니다.

* 환경을 만들고 패키지 종속성을 지정합니다.
* 환경을 검색하고 업데이트합니다.
* 학습을 위해 환경을 사용합니다.
* 웹 서비스 배포를 위한 환경을 사용합니다.

Azure Machine Learning에서 환경이 작동하는 방식에 대한 개요는 [ML 환경이란?](concept-environments.md)을 참조하세요. 개발 환경을 구성하는 방법에 대한 자세한 내용은 [여기](how-to-configure-environment.md)를 참조하세요.

## <a name="prerequisites"></a>필수 구성 요소

* [Python용 Azure Machine Learning SDK](/python/api/overview/azure/ml/install) (>= 1.13.0)
* [Azure Machine Learning 작업 영역](how-to-manage-workspace.md)

## <a name="create-an-environment"></a>환경 만들기

다음 섹션에서는 실험을 위한 환경을 만들 수 있는 여러 가지 방법을 살펴봅니다.

### <a name="instantiate-an-environment-object"></a>환경 개체 인스턴스화

환경을 수동으로 만들려면 SDK에서 `Environment` 클래스를 가져옵니다. 다음 코드를 사용하여 환경 개체를 인스턴스화합니다.

```python
from azureml.core.environment import Environment
Environment(name="myenv")
```

### <a name="use-a-curated-environment"></a>큐레이팅된 환경 사용

큐레이팅된 환경에는 Python 패키지 컬렉션이 포함되며, 기본적으로 작업 영역에서 사용할 수 있습니다. 이러한 환경은 캐시된 Docker 이미지로 지원되므로 실행 준비 비용이 줄어듭니다. 인기 있는 큐레이팅된 환경 중 하나를 선택하여 시작할 수 있습니다. 

* _AzureML-lightgbm-3.2-ubuntu18.04-py37-cpu_ 환경에는 Scikit-learn, LightGBM, XGBoost, Dask는 물론 기타 AzureML Python SDK 및 추가 패키지가 포함되어 있습니다.

* _AzureML-sklearn-0.24-ubuntu18.04-py37-cpu_ 환경에는 일반적인 데이터 과학 패키지가 포함되어 있습니다. 이러한 패키지에는 Scikit-Learn, Pandas, Matplotlib, 대규모 azureml-sdk 패키지 집합이 포함됩니다.

큐레이팅된 환경 목록은 [큐레이팅된 환경 문서](resource-curated-environments.md)를 참조하세요.

`Environment.get` 메서드를 사용하여 큐레이팅된 환경 중 하나를 선택합니다.

```python
from azureml.core import Workspace, Environment

ws = Workspace.from_config()
env = Environment.get(workspace=ws, name="AzureML-sklearn-0.24-ubuntu18.04-py37-cpu")
```

다음 코드를 사용하여 큐레이팅된 환경과 해당 패키지를 나열할 수 있습니다.

```python
envs = Environment.list(workspace=ws)

for env in envs:
    if env.startswith("AzureML"):
        print("Name",env)
        print("packages", envs[env].python.conda_dependencies.serialize_to_string())
```

> [!WARNING]
>  _AzureML_ 접두사로 자체 환경 이름을 시작하지 마세요. 이 접두사는 큐레이팅된 환경용으로 예약되어 있습니다.

큐레이팅된 환경을 사용자 지정하려면 환경을 복제하고 이름을 바꿉니다. 
```python 
env = Environment.get(workspace=ws, name="AzureML-sklearn-0.24-ubuntu18.04-py37-cpu")
curated_clone = env.clone("customize_curated")
```


### <a name="use-conda-dependencies-or-pip-requirements-files"></a>Conda 종속성 또는 pip 요구 사항 파일 사용

Conda 사양 또는 pip 요구 사항 파일에서 환경을 만들 수 있습니다. [`from_conda_specification()`](/python/api/azureml-core/azureml.core.environment.environment#from-conda-specification-name--file-path-) 메서드 또는 [`from_pip_requirements()`](/python/api/azureml-core/azureml.core.environment.environment#from-pip-requirements-name--file-path-) 메서드를 사용합니다. 메서드 인수에 사용자 환경 이름과 원하는 파일의 파일 경로를 포함합니다. 

```python
# From a Conda specification file
myenv = Environment.from_conda_specification(name = "myenv",
                                             file_path = "path-to-conda-specification-file")

# From a pip requirements file
myenv = Environment.from_pip_requirements(name = "myenv",
                                          file_path = "path-to-pip-requirements-file")                                          
```

### <a name="enable-docker"></a>Docker 사용

Azure Machine Learning는 사용자의 사양을 고려 하 여 Docker 이미지를 빌드하고 해당 컨테이너 내에서 Python 환경을 만듭니다. Docker 이미지가 캐시되고 재사용됩니다. 이미지가 빌드되므로 새 환경의 첫 번째 실행은 일반적으로 더 오래 걸립니다. 로컬 실행의 경우 [Runconfiguration](/python/api/azureml-core/azureml.core.runconfig.runconfiguration?view=azure-ml-py&preserve-view=true#variables)내에서 Docker를 지정 합니다. 

기본적으로 새로 빌드된 Docker 이미지는 작업 영역과 연결된 컨테이너 레지스트리에 나타납니다.  리포지토리 이름의 형식은 *azureml/azureml_\<uuid\>* 입니다. 이름의 고유 식별자(*uuid*) 부분은 환경 구성에서 계산된 해시에 해당합니다. 이러한 대응을 통해 서비스는 주어진 환경의 이미지가 재사용을 위해 이미 존재하는지를 판단할 수 있습니다.

#### <a name="use-a-prebuilt-docker-image"></a>미리 빌드된 Docker 이미지 사용

기본적으로 서비스는 Ubuntu Linux 기반 [기본 이미지](https://github.com/Azure/AzureML-Containers) 중 하나를 자동으로 사용하며, 특히 `azureml.core.environment.DEFAULT_CPU_IMAGE`에 의해 정의된 이미지를 사용합니다. 그런 다음 제공된 Azure ML 환경에 의해 정의되었으며 지정된 모든 Python 패키지를 설치합니다. 다른 Azure ML CPU 및 GPU 기본 이미지는 컨테이너 [리포지토리](https://github.com/Azure/AzureML-Containers)에서 사용할 수 있습니다. 또한 [사용자 지정 Docker 기본 이미지](./how-to-deploy-custom-container.md)를 사용할 수도 있습니다.

```python
# Specify custom Docker base image and registry, if you don't want to use the defaults
myenv.docker.base_image="your_base-image"
myenv.docker.base_image_registry="your_registry_location"
```

>[!IMPORTANT]
> Azure Machine Learning은 다음 소프트웨어를 제공하는 Docker 이미지만 지원합니다.
> * Ubuntu 18.04 이상
> * Conda 4.7.# 이상
> * Python 3.6 이상.
> * /bin/sh에서 사용할 수 있는 POSIX 규격 셸은 학습에 사용되는 모든 컨테이너 이미지에 필요합니다. 

#### <a name="use-your-own-dockerfile"></a>자체 Dockerfile 사용 

사용자 지정 Dockerfile을 지정할 수도 있습니다. Docker ```FROM``` 명령을 사용하여 Azure Machine Learning 기본 이미지 중 하나에서 시작한 후 자체 사용자 지정 단계를 추가하는 것이 가장 간단합니다. Python이 아닌 패키지를 종속성으로 설치해야 하는 경우 이 방법을 사용합니다. 기본 이미지를 None으로 설정해야 합니다.

Python은 Azure Machine Learning의 암시적 종속성이므로 사용자 지정 dockerfile에 Python이 설치되어 있어야 합니다.

```python
# Specify docker steps as a string. 
dockerfile = r"""
FROM mcr.microsoft.com/azureml/openmpi3.1.2-ubuntu18.04
RUN echo "Hello from custom container!"
"""

# Set base image to None, because the image is defined by dockerfile.
myenv.docker.base_image = None
myenv.docker.base_dockerfile = dockerfile

# Alternatively, load the string from a file.
myenv.docker.base_image = None
myenv.docker.base_dockerfile = "./Dockerfile"
```

사용자 지정 Docker 이미지를 사용하는 경우 재현성을 더 효율적으로 보장하기 위해 패키지 버전을 고정하는 것이 좋습니다.

#### <a name="specify-your-own-python-interpreter"></a>자체 Python 인터프리터 지정

사용자 지정 기본 이미지에 사용하려는 패키지가 포함된 Python 환경이 이미 있는 경우도 있습니다.

설치된 자체 패키지를 사용하고 Conda를 사용하지 않도록 설정하려면 `Environment.python.user_managed_dependencies = True` 매개 변수를 설정합니다. 기본 이미지에 Python 인터프리터가 포함되어 있고 학습 스크립트에 필요한 패키지가 있는지 확인합니다.

예를 들어 NumPy 패키지가 설치된 기본 Miniconda 환경에서 실행하려면 먼저 패키지를 설치하는 단계를 통해 Dockerfile을 지정합니다. 그런 다음 사용자 관리 종속성을 `True`로 설정합니다. 

`Environment.python.interpreter_path` 변수를 설정하여 이미지 내의 특정 Python 인터프리터에 대한 경로를 지정할 수도 있습니다.

```python
dockerfile = """
FROM mcr.microsoft.com/azureml/openmpi3.1.2-ubuntu18.04:20210615.v1
RUN conda install numpy
"""

myenv.docker.base_image = None
myenv.docker.base_dockerfile = dockerfile
myenv.python.user_managed_dependencies=True
myenv.python.interpreter_path = "/opt/miniconda/bin/python"
```

> [!WARNING]
> Docker 이미지에 일부 Python 종속성을 설치하고 `user_managed_dependencies=True`를 설정하지 않은 경우 이러한 패키지는 실행 환경에 존재하지 않으므로 런타임 오류가 발생합니다. 기본적으로 Azure ML은 사용자가 지정한 종속성으로 Conda 환경을 빌드하고 기본 이미지에 설치한 Python 라이브러리를 사용하는 대신 해당 환경에서 실행합니다.

#### <a name="retrieve-image-details"></a>이미지 세부 정보 검색

등록된 환경의 경우 다음 코드를 사용하여 이미지 세부 정보를 검색할 수 있습니다. 여기에서 `details`는 [DockerImageDetails](/python/api/azureml-core/azureml.core.environment.dockerimagedetails)의 인스턴스(AzureML Python SDK >= 1.11)이고 dockerfile, registry, image name과 같은 환경 이미지에 대한 모든 정보를 제공합니다.

```python
details = environment.get_image_details(workspace=ws)
```

실행 중에 자동 저장된 환경에서 이미지 세부 정보를 가져오려면 다음 코드를 사용합니다.

```python
details = run.get_environment().get_image_details(workspace=ws)
```

### <a name="use-existing-environments"></a>기존 환경 사용

로컬 컴퓨터에 기존 Conda 환경이 있는 경우 서비스를 사용하여 환경 개체를 만들 수 있습니다. 이 전략을 사용하여 원격 실행 시 로컬 대화형 환경을 다시 사용할 수 있습니다.

다음 코드는 기존 Conda 환경에서 환경 개체 `mycondaenv`를 만듭니다. [`from_existing_conda_environment()`](/python/api/azureml-core/azureml.core.environment.environment#from-existing-conda-environment-name--conda-environment-name-) 메서드를 사용합니다.

``` python
myenv = Environment.from_existing_conda_environment(name="myenv",
                                                    conda_environment_name="mycondaenv")
```

환경 정의는 [`save_to_directory()`](/python/api/azureml-core/azureml.core.environment.environment#save-to-directory-path--overwrite-false-) 메서드를 사용하여 쉽게 편집할 수 있는 형식으로 디렉터리에 저장할 수 있습니다. 수정되면 디렉터리에서 파일을 로드하여 새 환경을 인스턴스화할 수 있습니다.

```python
# save the enviroment
myenv.save_to_directory(path="path-to-destination-directory", overwrite=False)
# modify the environment definition
newenv = Environment.load_from_directory(path="path-to-source-directory")
```

### <a name="implicitly-use-the-default-environment"></a>기본 환경의 암시적 사용

실행을 제출하기 전에 스크립트 실행 구성에서 환경을 지정하지 않으면 자동으로 기본 환경이 만들어집니다.

```python
from azureml.core import ScriptRunConfig, Experiment, Environment
# Create experiment 
myexp = Experiment(workspace=ws, name = "environment-example")

# Attach training script and compute target to run config
src = ScriptRunConfig(source_directory=".", script="example.py", compute_target="local")

# Submit the run
run = myexp.submit(config=src)

# Show each step of run 
run.wait_for_completion(show_output=True)
```

## <a name="add-packages-to-an-environment"></a>환경에 패키지 추가

Conda, pip 또는 private wheel 파일을 사용하여 환경에 패키지를 추가합니다. [`CondaDependency`](/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies) 클래스를 사용하여 각 패키지 종속성을 지정합니다. 환경의 `PythonSection`에 추가합니다.

### <a name="conda-and-pip-packages"></a>Conda 및 pip 패키지

Conda 패키지 리포지토리에서 패키지를 사용할 수 있는 경우 pip 설치 대신 Conda 설치를 사용하는 것이 좋습니다. Conda 패키지에는 설치의 안정성을 높여주는 미리 빌드된 이진 파일이 일반적으로 제공됩니다.

다음은 환경 `myenv`에 추가하는 예제입니다. 이 예제에서는 `numpy` 1.17.0 버전을 추가합니다. 또한 `pillow` 패키지도 추가합니다. 이 예제에서는 각각 [`add_conda_package()`](/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies#add-conda-package-conda-package-) 메서드 및 [`add_pip_package()`](/python/api/azureml-core/azureml.core.conda_dependencies.condadependencies#add-pip-package-pip-package-) 메서드를 사용합니다.

```python
from azureml.core.environment import Environment
from azureml.core.conda_dependencies import CondaDependencies

myenv = Environment(name="myenv")
conda_dep = CondaDependencies()

# Installs numpy version 1.17.0 conda package
conda_dep.add_conda_package("numpy==1.17.0")

# Installs pillow package
conda_dep.add_pip_package("pillow")

# Adds dependencies to PythonSection of myenv
myenv.python.conda_dependencies=conda_dep
```

>[!IMPORTANT]
> 다른 실행에 동일한 환경 정의를 사용하는 경우 Azure Machine Learning Service는 환경에서 캐시된 이미지를 재사용합니다. ```numpy```와 같이 고정 해제된 패키지 종속성을 사용하여 환경을 만드는 경우 이 환경은 환경을 만들 때 설치된 패키지 버전을 계속 사용합니다. 또한 이후에 정의가 일치하는 모든 환경은 이전 버전을 계속 사용합니다. 자세한 내용은 [환경 빌드, 캐싱, 재사용](./concept-environments.md#environment-building-caching-and-reuse)을 참조하세요.

### <a name="private-python-packages"></a>프라이빗 Python 패키지

Python 패키지를 공용 인터넷에 노출하지 않고 개인적으로 안전하게 사용하려면 [프라이빗 Python 패키지 사용 방법](how-to-use-private-python-packages.md) 문서를 참조하세요.

## <a name="manage-environments"></a>환경 관리

컴퓨팅 대상에서 작업 영역의 다른 사용자를 통해 업데이트, 추적, 재사용할 수 있도록 환경을 관리합니다.

### <a name="register-environments"></a>환경 등록

실행을 제출하거나 웹 서비스를 배포할 때 환경이 작업 영역에 자동으로 등록됩니다. [`register()`](/python/api/azureml-core/azureml.core.environment%28class%29#register-workspace-) 메서드를 사용하여 환경을 수동으로 등록할 수도 있습니다. 이 작업을 수행하면 환경이 클라우드에서 추적 및 버전 관리되는 엔터티가 됩니다. 작업 영역 사용자 간에 엔터티를 공유할 수 있습니다.

다음 코드는 `myenv` 환경을 `ws` 작업 영역에 등록합니다.

```python
myenv.register(workspace=ws)
```

처음으로 학습 또는 배포에 환경을 사용하는 경우 작업 영역에 등록됩니다. 그런 다음 컴퓨팅 대상에 빌드 및 배포됩니다. 서비스는 환경을 캐시합니다. 캐시된 환경을 다시 사용하면 새 서비스 또는 업데이트된 서비스를 사용하는 것 보다 훨씬 짧은 시간이 소요됩니다.

### <a name="get-existing-environments"></a>기존 환경 가져오기

`Environment` 클래스는 작업 영역의 기존 환경을 검색하는 데 사용할 수 있는 메서드를 제공합니다. 이름, 목록 또는 특정 학습 실행을 기준으로 환경을 검색할 수 있습니다. 이 정보는 문제 해결, 감사 및 재현 가능성에 유용합니다.

#### <a name="view-a-list-of-environments"></a>환경 목록 보기

[`Environment.list(workspace="workspace_name")`](/python/api/azureml-core/azureml.core.environment%28class%29#list-workspace-) 클래스를 사용하여 작업 영역에서 환경을 확인합니다. 그런 다음 재사용할 환경을 선택합니다.

#### <a name="get-an-environment-by-name"></a>이름으로 환경 가져오기

이름 및 버전으로 특정 환경을 가져올 수도 있습니다. 다음 코드는 [`get()`](/python/api/azureml-core/azureml.core.environment%28class%29#get-workspace--name--version-none-) 메서드를 사용하여 `ws` 작업 영역에 있는 `myenv`의 버전 `1`을 검색합니다.

```python
restored_environment = Environment.get(workspace=ws,name="myenv",version="1")
```

#### <a name="train-a-run-specific-environment"></a>실행별 환경 학습

학습을 완료한 후 특정 실행에 사용된 환경을 가져오려면 `Run` 클래스의 [`get_environment()`](/python/api/azureml-core/azureml.core.run.run#get-environment--) 메서드를 사용합니다.

```python
from azureml.core import Run
Run.get_environment()
```

### <a name="update-an-existing-environment"></a>기존 환경 업데이트

예를 들어 Python 패키지를 추가하여 기존 환경을 변경한다고 가정해 보겠습니다. 이렇게 하면 실행을 제출하거나 모델을 배포하거나, 환경을 수동으로 등록할 때 환경의 새 버전을 만드는 데 빌드하는 시간이 걸립니다. 버전 관리를 사용하면 시간에 따른 환경의 변화를 확인할 수 있습니다. 

기존 환경에서 Python 패키지 버전을 업데이트하려면 해당 패키지에 대한 버전 번호를 지정합니다. 정확한 버전 번호를 사용하지 않는 경우 Azure Machine Learning은 원래 패키지 버전과 기존 환경을 다시 사용합니다.

### <a name="debug-the-image-build"></a>이미지 빌드 디버그

다음 예제에서는 [`build()`](/python/api/azureml-core/azureml.core.environment%28class%29#build-workspace--image-build-compute-none-) 메서드를 사용하여 환경을 Docker 이미지로 직접 만듭니다. [`wait_for_completion()`](/python/api/azureml-core/azureml.core.image%28class%29#wait-for-creation-show-output-false-)를 사용하여 이미지 빌드에서 출력 로그를 모니터링합니다. 그러면 빌드된 이미지가 작업 영역의 Azure Container Registry 인스턴스에 표시됩니다. 이 정보는 디버깅에 유용합니다.

```python
from azureml.core import Image
build = env.build(workspace=ws)
build.wait_for_completion(show_output=True)
```

먼저 [`build_local()`](/python/api/azureml-core/azureml.core.environment.environment#build-local-workspace--platform-none----kwargs-) 메서드를 사용하여 이미지를 로컬로 빌드하는 것이 유용합니다. Docker 이미지를 빌드하려면 선택적 매개 변수인 `useDocker=True`를 설정합니다. 결과 이미지를 AzureML 작업 영역 컨테이너 레지스트리에 푸시하려면 `pushImageToWorkspaceAcr=True`를 설정합니다.

```python
build = env.build_local(workspace=ws, useDocker=True, pushImageToWorkspaceAcr=True)
```

> [!WARNING]
>  환경에서 종속성 또는 채널의 순서를 변경하면 새로운 환경이 생성되고 새로운 이미지 빌드가 필요합니다. 또한 새 버전이 있는 경우 기존 이미지에 대해 `build()` 메서드를 호출하면 해당 종속성이 업데이트됩니다. 

### <a name="utilize-adminless-azure-container-registry-acr-with-vnet"></a>VNet을 통해 관리 없는 ACR(Azure Container Registry) 활용

사용자가 VNet 시나리오에서 ACR에 연결된 작업 영역에 더 이상 관리 모드를 사용하도록 설정할 필요가 없습니다. 성공적인 빌드를 사용하려면 컴퓨팅에서 파생된 이미지 빌드 시간이 1시간 미만인지 확인합니다. 이미지가 작업 영역 ACR에 푸시되면 이제 컴퓨팅 ID만 사용하여 이 이미지에 액세스할 수 있습니다. 자세한 내용은 [Azure Machine Learning에서 관리 ID 사용 방법](./how-to-use-managed-identities.md)을 참조하세요.

## <a name="use-environments-for-training"></a>교육용 환경 사용

학습 실행을 제출하려면 환경, [컴퓨팅 대상](concept-compute-target.md), 학습 Python 스크립트를 실행 구성으로 결합해야 합니다. 이 구성은 실행을 전송하는 데 사용되는 래퍼 개체입니다.

학습 실행을 제출하면 새 환경을 빌드하는 데 몇 분 정도 걸릴 수 있습니다. 시간은 필요한 종속성의 크기에 따라 달라집니다. 환경은 서비스에서 캐시됩니다. 환경 정의가 변경되지 않는 한, 전체 설정을 한 번만 하면 됩니다.

다음 로컬 스크립트 실행 예제에서는 [`ScriptRunConfig`](/python/api/azureml-core/azureml.core.script_run_config.scriptrunconfig)를 래퍼 개체로 사용하는 경우를 보여 줍니다.

```python
from azureml.core import ScriptRunConfig, Experiment
from azureml.core.environment import Environment

exp = Experiment(name="myexp", workspace = ws)
# Instantiate environment
myenv = Environment(name="myenv")

# Configure the ScriptRunConfig and specify the environment
src = ScriptRunConfig(source_directory=".", script="train.py", compute_target="local", environment=myenv)

# Submit run 
run = exp.submit(src)
```

> [!NOTE]
> 실행 기록 또는 실행 스냅샷을 사용하지 않도록 설정하려면 `src.run_config.history`의 설정을 사용합니다.

>[!IMPORTANT]
> 컴퓨팅 기반 이미지 빌드에 CPU SKU를 사용합니다. 

실행 구성에서 환경을 지정하지 않을 경우 서비스가 실행을 제출할 때 기본 환경을 만듭니다.

## <a name="use-environments-for-web-service-deployment"></a>웹 서비스 배포를 위한 환경 사용

모델을 웹 서비스로 배포할 때 환경을 사용할 수 있습니다. 이 기능을 통해 재현 가능하고 연결된 워크플로를 사용할 수 있습니다. 이 워크플로에서는 학습 컴퓨팅과 유추 컴퓨팅 둘 모두에 동일한 라이브러리를 사용하여 모델을 학습, 테스트, 배포할 수 있습니다.


웹 서비스 배포를 위한 자체 환경을 정의할 경우 버전1.0.45 이상인 `azureml-defaults`를 pip 종속성으로 나열해야 합니다. 이 패키지에는 웹 서비스로 모델을 호스트하는 데 필요한 기능이 포함되어 있습니다.

웹 서비스를 배포하려면 환경, 유추 컴퓨팅, 채점 스크립트 및 등록된 모델을 배포 개체 [`deploy()`](/python/api/azureml-core/azureml.core.model.model#deploy-workspace--name--models--inference-config-none--deployment-config-none--deployment-target-none--overwrite-false-)에 결합하세요. 자세한 내용은 [모델 배포 방법 및 위치](how-to-deploy-and-where.md)를 참조하세요.

이 예제에서는 학습 실행을 완료했다고 가정합니다. 이제 Azure Container Instances에 해당 모델을 배포하려고 합니다. 웹 서비스를 빌드하면 모델 및 채점 파일이 이미지에 탑재되고 Azure Machine Learning 유추 스택이 이미지에 추가됩니다.

```python
from azureml.core.model import InferenceConfig, Model
from azureml.core.webservice import AciWebservice, Webservice

# Register the model to deploy
model = run.register_model(model_name = "mymodel", model_path = "outputs/model.pkl")

# Combine scoring script & environment in Inference configuration
inference_config = InferenceConfig(entry_script="score.py", environment=myenv)

# Set deployment configuration
deployment_config = AciWebservice.deploy_configuration(cpu_cores = 1, memory_gb = 1)

# Define the model, inference, & deployment configuration and web service name and location to deploy
service = Model.deploy(
    workspace = ws,
    name = "my_web_service",
    models = [model],
    inference_config = inference_config,
    deployment_config = deployment_config)
```

## <a name="notebooks"></a>Notebooks

이 문서의 코드 예제는 [환경 Notebook 사용](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/using-environments/using-environments.ipynb)에도 포함되어 있습니다.

Conda 환경을 Notebook에 커널로 설치하려면 [새 Jupyter 커널 추가](./how-to-access-terminal.md#add-new-kernels)를 참조하세요.

[사용자 지정 Docker 기본 이미지를 사용한 모델 배포](./how-to-deploy-custom-container.md)는 사용자 지정 Docker 기본 이미지를 사용하여 모델을 배포하는 방법을 보여 줍니다.

이 [예제 노트북](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/deployment/spark)은 Spark 모델을 웹 서비스로 배포하는 방법을 보여 줍니다.

## <a name="create-and-manage-environments-with-the-azure-cli"></a>Azure CLI로 환경 만들기 및 관리

[!INCLUDE [cli-version-info](../../includes/machine-learning-cli-version-1-only.md)]

[Azure Machine Learning CLI](reference-azure-machine-learning-cli.md)는 Python SDK의 대부분 기능을 미러링합니다. 환경을 만들고 관리하는 데 사용할 수 있습니다. 이 섹션에서 논의한 명령은 기본적인 기능을 보여 줍니다.

다음 명령은 지정된 디렉터리의 기본 환경 정의에 대한 파일을 스캐폴드합니다. 이러한 파일은 JSON 파일입니다. SDK에서 해당 클래스처럼 작동합니다. 이 파일을 사용하여 사용자 지정 설정을 포함하는 새 환경을 만들 수 있습니다. 

```azurecli-interactive
az ml environment scaffold -n myenv -d myenvdir
```

다음 명령을 실행하여 지정된 디렉터리에서 환경을 등록합니다.

```azurecli-interactive
az ml environment register -d myenvdir
```

다음 명령을 실행하여 등록된 모든 환경을 나열합니다.

```azurecli-interactive
az ml environment list
```

다음 명령을 사용하여 등록된 환경을 다운로드합니다.

```azurecli-interactive
az ml environment download -n myenv -d downloaddir
```

## <a name="create-and-manage-environments-with-visual-studio-code"></a>Visual Studio Code로 환경 만들기 및 관리

Azure Machine Learning 확장을 사용하여 Visual Studio Code에서 환경을 만들고 관리할 수 있습니다. 자세한 내용은 [VS Code 확장을 사용하여 Azure Machine Learning 리소스 관리](how-to-manage-resources-vscode.md#environments)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* 모델 학습에 관리형 컴퓨팅 대상을 사용하려면 [자습서: 모델 학습](tutorial-train-models-with-aml.md)을 참조하세요.
* 모델을 학습했으면 [모델을 배포하는 방법 및 위치](how-to-deploy-and-where.md)를 알아봅니다.
* [`Environment` 클래스 SDK 참조](/python/api/azureml-core/azureml.core.environment%28class%29)를 확인합니다.

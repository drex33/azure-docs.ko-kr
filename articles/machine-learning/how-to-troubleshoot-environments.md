---
title: 환경 이미지 관련 문제 해결
titleSuffix: Azure Machine Learning
description: 환경 이미지 빌드 및 패키지 설치와 관련된 문제를 해결하는 방법을 알아봅니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: saachigopal
ms.author: sagopal
ms.date: 10/21/2021
ms.topic: troubleshooting
ms.custom: devx-track-python
ms.openlocfilehash: 37b95d4736546721642d88d85ad88ee09d55c394
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131558310"
---
# <a name="troubleshoot-environment-image-builds"></a>환경 이미지 빌드 관련 문제 해결

Docker 환경 이미지 빌드 및 패키지 설치와 관련된 문제를 해결하는 방법을 알아봅니다.

## <a name="prerequisites"></a>필수 구성 요소

* Azure 구독 [Azure Machine Learning 평가판 또는 유료 버전](https://azure.microsoft.com/free/)을 사용해 보세요.
* [Azure Machine Learning SDK](/python/api/overview/azure/ml/install)
* [Azure CLI](/cli/azure/install-azure-cli)
* [Azure Machine Learning용 CLI 확장](reference-azure-machine-learning-cli.md)
* 로컬로 디버그하려면 로컬 시스템에서 작동하는 Docker가 설치되어 있어야 합니다.

## <a name="docker-image-build-failures"></a>Docker 이미지 빌드 실패
 
대부분의 이미지 빌드 실패의 경우 이미지 빌드 로그에서 근본 원인을 찾을 수 있습니다.
Azure Machine Learning 포털(20\_image\_build\_log.txt) 또는 Azure Container Registry 작업 실행 로그에서 이미지 빌드 로그를 찾습니다.
 
일반적으로 로컬에서 오류를 재현하는 것이 더 쉽습니다. 오류 종류를 확인하고 다음 `setuptools` 중 하나를 시도합니다.

- 로컬로 Conda 종속성 설치: `conda install suspicious-dependency==X.Y.Z`.
- 로컬로 PIP 종속성 설치: `pip install suspicious-dependency==X.Y.Z`.
- 전체 환경 구체화 시도: `conda create -f conda-specification.yml`.

> [!IMPORTANT]
> 로컬 컴퓨팅 클러스터의 플랫폼과 인터프리터가 원격 컴퓨팅 클러스터의 플랫폼과 일치하는지 확인합니다. 

### <a name="timeout"></a>제한 시간 
 
다음 네트워크 문제로 인해 시간 제한 오류가 발생할 수 있습니다.

- 낮은 인터넷 대역폭
- 서버 문제
- 지정된 Conda 또는 PIP 시간 제한 설정으로 인해 다운로드할 수 없는 많은 종속성
 
다음 예와 유사한 메시지는 문제를 나타냅니다.
 
```
('Connection broken: OSError("(104, \'ECONNRESET\')")', OSError("(104, 'ECONNRESET')"))
```
```
ReadTimeoutError("HTTPSConnectionPool(host='****', port=443): Read timed out. (read timeout=15)",)
```

오류 메시지가 표시되는 경우 다음과 같은 가능한 해결 방법 중 하나를 시도해 봅니다.
 
- 종속성에 대해 미러, Azure Blob Storage 또는 기타 Python 피드와 같은 다른 원본을 사용해 봅니다.
- Conda 또는 PIP를 업데이트합니다. 사용자 지정 Docker 파일을 사용하는 경우 시간 제한 설정을 업데이트합니다.
- 일부 PIP 버전에는 알려진 문제가 있습니다. 환경 종속성에 특정 버전의 PIP를 추가하는 것을 고려해 보세요.

### <a name="package-not-found"></a>패키지를 찾을 수 없음

다음 오류는 이미지 빌드 실패에 가장 일반적입니다.

- Conda 패키지를 찾을 수 없는 경우:

   ```
   ResolvePackageNotFound: 
   - not-existing-conda-package
   ```

- 지정된 PIP 패키지 또는 버전을 찾을 수 없는 경우:

   ```
   ERROR: Could not find a version that satisfies the requirement invalid-pip-package (from versions: none)
   ERROR: No matching distribution found for invalid-pip-package
   ```

- 잘못 중첩된 PIP 종속성:

   ```
   ERROR: No matching distribution found for bad-package==0.0 (from good-package==1.0)
   ```

지정된 원본에 패키지가 있는지 확인합니다. [PIP 검색](https://pip.pypa.io/en/stable/reference/pip_search/)을 사용하여 PIP 종속성을 확인합니다.

- `pip search azureml-core`

Conda 종속성의 경우 [Conda 검색](https://docs.conda.io/projects/conda/en/latest/commands/search.html)을 사용합니다.

- `conda search conda-forge::numpy`

추가 옵션을 보려면 다음을 수행하세요.
- `pip search -h`
- `conda search -h`

#### <a name="installer-notes"></a>설치 관리자 관련 참고 사항

지정된 플랫폼과 Python 인터프리터 버전에 필요한 배포가 있는지 확인합니다.

PIP 종속성의 경우 `https://pypi.org/project/[PROJECT NAME]/[VERSION]/#files`로 이동하여 필요한 버전을 사용할 수 있는지 확인합니다. 예를 보려면 https://pypi.org/project/azureml-core/1.11.0/#files 로 이동합니다.

Conda 종속성의 경우 채널 리포지토리에서 패키지를 확인합니다.
Anaconda, Inc.에서 유지 관리하는 채널의 경우 [Anaconda 패키지 페이지](https://repo.anaconda.com/pkgs/)를 확인합니다.

### <a name="pip-package-update"></a>PIP 패키지 업데이트

PIP 패키지를 설치하거나 업데이트하는 동안 해결 프로그램은 새 요구 사항을 충족하기 위해 이미 설치된 패키지를 업데이트해야 할 수 있습니다.
PIP 버전 또는 종속성이 설치된 방법과 관련된 다양한 이유로 인해 제거하지 못할 수 있습니다.
가장 일반적인 시나리오는 Conda에서 설치한 종속성을 PIP에서 제거할 수 없는 것입니다.
이 시나리오의 경우 `conda remove mypackage`를 사용하여 종속성을 제거하는 것을 고려해 보세요.

```
  Attempting uninstall: mypackage
    Found existing installation: mypackage X.Y.Z
ERROR: Cannot uninstall 'mypackage'. It is a distutils installed project and thus we cannot accurately determine which files belong to it which would lead to only a partial uninstall.
``` 
### <a name="installer-issues"></a>설치 관리자 문제

특정 설치 관리자 버전에는 패키지 해결 프로그램에 문제가 있어서 빌드 오류로 이어질 수 있습니다.

사용자 지정 기본 이미지 또는 Dockerfile을 사용하는 경우 Conda 버전 4.5.4 이상을 사용하는 것이 좋습니다.

PIP 종속성을 설치하려면 PIP 패키지가 필요합니다. 환경에서 버전을 지정하지 않으면 최신 버전이 사용됩니다.
최신 버전의 도구에서 발생할 수 있는 호환성이 손상되는 변경이나 일시적인 문제를 방지하려면 알려진 버전의 PIP를 사용하는 것이 좋습니다.

다음 메시지가 표시되는 경우 사용자 환경에서 PIP 버전을 고정하는 것을 고려해 보세요.

   ```
   Warning: you have pip-installed dependencies in your environment file, but you do not list pip itself as one of your conda dependencies. Conda may not use the correct pip to install your packages, and they may end up in the wrong place. Please add an explicit pip dependency. I'm adding one for you, but still nagging you.
   ```

PIP 하위 프로세스 오류:
   ```
   ERROR: THESE PACKAGES DO NOT MATCH THE HASHES FROM THE REQUIREMENTS FILE. If you have updated the package versions, update the hashes as well. Otherwise, examine the package contents carefully; someone may have tampered with them.
   ```

종속성에 해결할 수 없는 충돌이 있으면 PIP 설치가 무한 루프에서 중단될 수 있습니다. 로컬에서 작업하는 경우 PIP 버전을 20.3 미만으로 다운그레이드합니다. YAML 파일에서 만든 Conda 환경에서는 우선 순위가 가장 높은 채널이 conda-forge인 경우에만 이 문제가 발생합니다. 이 문제를 완화하려면 Conda 사양 파일에서 20.3 미만의 PIP(!=20.3 또는 =20.2.4를 다른 버전으로 고정)를 Conda 종속성으로 명시적으로 지정합니다.

### <a name="modulenotfounderror-no-module-named-distutilsdir_util"></a>ModuleNotFoundError: 'distutils.dir_util'이라는 모듈이 없음

환경을 설정할 때 **ModuleNotFoundError: 'distutils.dir_util'이라는 모듈이 없음** 문제가 발생하는 경우도 있습니다. 이 문제를 해결하려면 다음 명령을 수행합니다.

```bash
apt-get install -y --no-install-recommends python3 python3-distutils && \
ln -sf /usr/bin/python3 /usr/bin/python
```

Dockerfile을 사용하는 경우 RUN 명령의 일부로 실행합니다.

```dockerfile
RUN apt-get update && \
  apt-get install -y --no-install-recommends python3 python3-distutils && \
  ln -sf /usr/bin/python3 /usr/bin/python
```

이 명령을 실행하면 환경을 구성하기 위한 올바른 모듈 종속성이 설치됩니다. 

### <a name="build-failure-when-using-spark-packages"></a>Spark 패키지를 사용할 때 빌드 실패

패키지를 미리 캐시하지 않도록 환경을 구성합니다. 

```python
env.spark.precache_packages = False
```

## <a name="service-side-failures"></a>서비스 측 실패

가능한 서비스 측 오류 문제를 해결하려면 다음 시나리오를 참조하세요.

### <a name="youre-unable-to-pull-an-image-from-a-container-registry-or-the-address-couldnt-be-resolved-for-a-container-registry"></a>컨테이너 레지스트리에서 이미지를 풀할 수 없거나 컨테이너 레지스트리에 대한 주소를 확인할 수 없음

가능한 문제:
- 컨테이너 레지스트리에 대한 경로 이름이 올바르게 확인되지 않을 수 있습니다. 이미지 이름에 이중 슬래시를 사용하고 Linux 호스트와 Windows 호스트의 슬래시 방향이 올바른지 확인합니다.
- 가상 네트워크 뒤의 컨테이너 레지스트리가 [지원되지 않는 지역](../private-link/private-link-overview.md#availability)에서 프라이빗 엔드포인트를 사용하는 경우, 포털에서 서비스 엔드포인트(퍼블릭 액세스)를 사용하여 컨테이너 레지스트리를 구성하고 다시 시도합니다.
- 컨테이너 레지스트리를 가상 네트워크 뒤에 배치한 후에는 작업 영역이 컨테이너 레지스트리 인스턴스와 통신할 수 있도록 [Azure Resource Manager 템플릿](./how-to-network-security-overview.md)을 실행합니다.

### <a name="you-get-a-401-error-from-a-workspace-container-registry"></a>작업 영역 컨테이너 레지스트리에서 401 오류가 발생함

[ws.sync_keys()](/python/api/azureml-core/azureml.core.workspace.workspace#sync-keys--)를 사용하여 스토리지 키를 다시 동기화합니다.

### <a name="the-environment-keeps-throwing-a-waiting-for-other-conda-operations-to-finish-error"></a>환경에서 “다른 Conda 작업이 완료되기를 기다리는 중...” 오류가 계속 발생함

이미지 빌드가 진행 중일 때 SDK 클라이언트에서 Conda를 잠금 설정합니다. 프로세스가 충돌하거나 사용자에 의해 잘못 취소된 경우 Conda는 잠금 상태로 유지됩니다. 이 문제를 해결하려면 잠금 파일을 수동으로 삭제합니다. 

### <a name="your-custom-docker-image-isnt-in-the-registry"></a>사용자 지정 Docker 이미지가 레지스트리에 없음

[올바른 태그](./how-to-use-environments.md#create-an-environment)가 사용되고 있고 `user_managed_dependencies = True`인지 확인합니다. `Environment.python.user_managed_dependencies = True` Conda를 사용하지 않도록 설정하고 사용자의 설치된 패키지를 사용합니다.

### <a name="you-get-one-of-the-following-common-virtual-network-issues"></a>다음과 같은 일반적인 가상 네트워크 문제 중 하나가 발생함

- 스토리지 계정, 컴퓨팅 클러스터, 컨테이너 레지스트리가 모두 가상 네트워크의 동일한 서브넷에 있는지 확인합니다.
- 컨테이너 레지스트리가 가상 네트워크 뒤에 있는 경우 이미지를 빌드하는 데 직접 사용할 수 없습니다. 이미지를 빌드하려면 컴퓨팅 클러스터를 사용해야 합니다.
- 다음을 수행하는 경우 스토리지를 가상 네트워크 뒤에 배치해야 할 수 있습니다.
    - 추론 또는 프라이빗 휠을 사용합니다.
    - 403(권한 없음) 서비스 오류를 참조하세요.
    - Azure Container Registry에서 이미지 세부 정보를 가져올 수 없습니다.

### <a name="the-image-build-fails-when-youre-trying-to-access-network-protected-storage"></a>네트워크로 보호된 스토리지에 액세스하려고 하면 이미지 빌드가 실패함

- Azure Container Registry 작업은 가상 네트워크 뒤에서 작동하지 않습니다. 사용자에게 가상 네트워크 뒤의 컨테이너 레지스트리가 있는 경우 컴퓨팅 클러스터를 사용하여 이미지를 빌드해야 합니다.
- 스토리지는 종속성을 풀하기 위해 가상 네트워크 뒤에 배치되어야 합니다.

### <a name="you-cant-run-experiments-when-storage-has-network-security-enabled"></a>스토리지에 네트워크 보안이 사용 설정되어 있는 경우 실험을 실행할 수 없음

기본 Docker 이미지를 사용하고 사용자 관리형 종속성을 사용 설정하는 경우 MicrosoftContainerRegistry 및 AzureFrontDoor.FirstParty [서비스 태그](./how-to-network-security-overview.md)를 사용하여 Azure Container Registry 및 해당 종속성을 허용 목록에 추가합니다.

 자세한 내용은 [가상 네트워크 사용 설정](./how-to-network-security-overview.md)을 참조하세요.


## <a name="next-steps"></a>다음 단계

- [기계 학습 모델을 학습하여 꽃 범주화](how-to-train-scikit-learn.md)
- [사용자 지정 Docker 이미지를 사용하여 기계 학습 모델 학습](how-to-train-with-custom-image.md)

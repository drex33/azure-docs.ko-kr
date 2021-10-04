---
title: 학습 인증 비밀
titleSuffix: Azure Machine Learning
description: 작업 영역에 대한 Azure Key Vault를 사용하여 안전한 방식으로 학습 실행에 비밀을 전달하는 방법에 대해 알아봅니다.
services: machine-learning
author: rastala
ms.author: roastala
ms.reviewer: larryfr
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.date: 08/24/2021
ms.topic: how-to
ms.openlocfilehash: 0966d85fcab33752359c5174f6205c5556f2a137
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/04/2021
ms.locfileid: "129427935"
---
# <a name="use-authentication-credential-secrets-in-azure-machine-learning-training-runs"></a>Azure Machine Learning 학습 실행에서 인증 자격 증명 비밀 사용

이 문서에서는 학습 실행 시 안전하게 비밀을 사용하는 방법에 대해 알아봅니다. 사용자 이름 및 암호와 같은 인증 정보는 비밀입니다. 예를 들어 학습 데이터를 쿼리하기 위해 외부 데이터베이스에 연결하는 경우 사용자 이름 및 암호를 원격 실행 컨텍스트에 전달해야 합니다. 이러한 값을 일반 텍스트의 학습 스크립트에 코딩하는 것은 비밀을 노출하므로 안전하지 않습니다. 

대신 Azure Machine Learning 작업 영역에는 [Azure Key Vault](../key-vault/general/overview.md)라는 연결된 리소스가 있습니다. 이 Key Vault를 사용하여 Azure Machine Learning Python SDK의 API 집합을 통해 안전하게 원격으로 실행할 수 있습니다.

비밀을 사용하는 표준 흐름은 다음과 같습니다.
 1. 로컬 컴퓨터에서 Azure에 로그인하고 작업 영역에 연결합니다.
 2. 로컬 컴퓨터에서 작업 영역 Key Vault에 비밀을 설정합니다.
 3. 원격 실행을 제출합니다.
 4. 원격 실행 내 Key Vault에서 비밀을 가져와 사용합니다.

## <a name="set-secrets"></a>비밀 설정

Azure Machine Learning의 [Keyvault](/python/api/azureml-core/azureml.core.keyvault.keyvault) 클래스에는 비밀 설정을 위한 메서드가 포함되어 있습니다. 로컬 Python 세션에서 먼저 작업 영역 Key Vault에 대한 참조를 얻은 다음 [`set_secret()`](/python/api/azureml-core/azureml.core.keyvault.keyvault#set-secret-name--value-) 메서드를 사용하여 이름과 값에 따라 비밀을 설정합니다. __set_secret__ 메서드는 이름이 이미 존재하는 경우 비밀 값을 업데이트합니다.

```python
from azureml.core import Workspace
from azureml.core import Keyvault
import os


ws = Workspace.from_config()
my_secret = os.environ.get("MY_SECRET")
keyvault = ws.get_default_keyvault()
keyvault.set_secret(name="mysecret", value = my_secret)
```

비밀 값을 파일에 일반 텍스트로 저장하는 것은 안전하지 않으므로 Python 코드에 저장하지 마세요. 대신 환경 변수에서 비밀 값(예: Azure DevOps 빌드 비밀 또는 대화형 사용자 입력)을 가져옵니다.

[`list_secrets()`](/python/api/azureml-core/azureml.core.keyvault.keyvault#list-secrets--) 메서드를 사용하여 비밀 이름을 나열할 수 있으며 한 번에 여러 비밀을 설정할 수 있는 일괄 버전 [set_secrets()](/python/api/azureml-core/azureml.core.keyvault.keyvault#set-secrets-secrets-batch-)도 있습니다.

> [!IMPORTANT]
> `list_secrets()`를 사용하면 Azure ML SDK를 사용하여 `set_secret()` 또는 `set_secrets()`를 통해 생성된 비밀만 나열됩니다. SDK가 아닌 다른 것으로 만든 비밀은 나열되지 않습니다. 예를 들어 Azure Portal 또는 Azure PowerShell을 사용하여 만든 비밀은 나열되지 않습니다.
> 
> [`get_secret()`](#get-secrets)를 사용하여 키 자격 증명 모음에서 비밀 값을 생성 방법과 관계없이 얻을 수 있습니다. 따라서 `list_secrets()`에 나열되지 않은 비밀을 검색할 수 있습니다.

## <a name="get-secrets"></a>암호 가져오기

로컬 코드에서 [`get_secret()`](/python/api/azureml-core/azureml.core.keyvault.keyvault#get-secret-name-) 메서드를 사용하여 이름으로 비밀 값을 가져올 수 있습니다.

제출된 [`Experiment.submit`](/python/api/azureml-core/azureml.core.experiment.experiment#submit-config--tags-none----kwargs-) 실행의 경우 [`Run`](/python/api/azureml-core/azureml.core.run%28class%29) 클래스와 함께 [`get_secret()`](/python/api/azureml-core/azureml.core.run.run#get-secret-name-) 메서드를 사용합니다. 제출된 실행은 작업 영역을 인식하므로 이 메서드는 작업 영역 인스턴스화를 바로 실행하고 비밀 값을 직접 반환합니다.

```python
# Code in submitted run
from azureml.core import Experiment, Run

run = Run.get_context()
secret_value = run.get_secret(name="mysecret")
```

비밀 값을 쓰거나 인쇄하여 노출하지 않도록 주의해야 합니다.

한 번에 여러 비밀에 액세스하기 위한 일괄 버전인 [get_secrets()](/python/api/azureml-core/azureml.core.run.run#get-secrets-secrets-)도 있습니다.

## <a name="next-steps"></a>다음 단계

 * [Notebook 예 보기](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/manage-azureml-service/authentication-in-azureml/authentication-in-azureml.ipynb)
 * [Azure Machine Learning을 사용한 엔터프라이즈 보안에 대해 알아보기](concept-enterprise-security.md)
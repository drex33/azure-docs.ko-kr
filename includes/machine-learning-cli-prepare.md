---
author: blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 10/26/2021
ms.author: larryfr
ms.openlocfilehash: 2bd402b9905772b7a45be5ea433a214b6e5f76cf
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2021
ms.locfileid: "131553452"
---
이 문서의 정보는 [azureml-examples](https://github.com/azure/azureml-examples) 리포지토리에 포함된 코드 샘플을 기반으로 합니다. YAML 및 기타 파일을 복사/붙여넣지 않고도 명령을 로컬로 실행하려면 리포지토리를 복제한 `cli` 다음, 디렉터리를 리포지토리의 디렉터리로 변경합니다.

```azurecli
git clone https://github.com/Azure/azureml-examples --depth 1
cd azureml-examples
cd cli
```

Azure CLI에 대한 기본값을 아직 설정하지 않은 경우 기본 설정을 저장합니다. 구독, 작업 영역 및 리소스 그룹에 대한 값을 여러 번 전달하지 않으려면 다음 명령을 사용합니다. 다음 매개 변수를 특정 구성에 대한 값으로 대체합니다.

* `<subscription>` 를 Azure 구독 ID로 바꿉니다.
* `<workspace>`을(를) Azure Machine Learning 작업 영역 이름으로 바꿉니다.
* `<resource-group>`를 작업 영역이 포함된 Azure 리소스 그룹으로 대체합니다.
* `<location>`를 작업 영역이 포함된 Azure 지역으로 대체합니다.

> [!TIP]
> 명령을 사용하여 현재 기본값을 확인할 수 `az configure -l` 있습니다.

```azurecli
az account set --subscription <subscription>
az configure --defaults workspace=<workspace> group=<resource-group> location=<location>
```


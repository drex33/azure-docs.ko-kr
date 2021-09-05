---
title: 일괄 처리 엔드포인트(미리 보기) 문제 해결
titleSuffix: Azure Machine Learning
description: 일괄 처리 엔드포인트를 성공적으로 사용하는 데 도움이 되는 팁입니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: troubleshooting
ms.custom: troubleshooting, devplatv2
ms.reviewer: laobri
ms.author: tracych
author: tracych
ms.date: 05/05/2021
ms.openlocfilehash: 9fbbc2af3681b6f91ec3c1e0f6adfd46a8598c17
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114448317"
---
# <a name="troubleshooting-batch-endpoints-preview"></a>일괄 처리 엔드포인트(미리 보기) 문제 해결

일괄 처리 채점을 위해 [일괄 처리 엔드포인트](how-to-use-batch-endpoint.md)(미리 보기)를 사용하는 경우 발생할 수 있는 일반적인 오류를 해결하고 해결하는 방법에 대해 알아봅니다.

 [!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

다음 표에는 일괄 처리 엔드포인트 개발 및 사용 중에 표시될 수 있는 일반적인 문제와 해결 방법이 나와 있습니다.

| 문제 | 가능한 해결 방법 |
|--|--|
| 코드 구성 또는 환경이 없습니다. | 비-MLflow 모델을 사용 중인 경우 채점 스크립트 및 환경 정의를 제공해야 합니다. 비-코드 배포는 MLflow 모델에 대해서만 지원됩니다. 자세한 내용은 [MLflow 및 Azure Machine Learning을 사용하여 ML 모델 추적](how-to-use-mlflow.md)을 참조하세요.|
| 기존 일괄 처리 엔드포인트에 대한 모델, 코드, 환경 및 컴퓨팅을 업데이트하지 못했습니다. | 새 이름으로 새 일괄 처리 엔드포인트를 만듭니다. 기존 일괄 처리 엔드포인트에 대한 이러한 자산 업데이트는 아직 지원되지 않습니다. |
| 리소스를 찾을 수 없습니다. | CLI 명령에서 `--type batch`를 사용하는지 확인합니다. 이 인수를 지정하지 않으면 기본 `online` 형식이 사용됩니다.|
| 지원되지 않는 입력 데이터입니다. | 일괄 처리 엔드포인트는 다음 3개의 형식으로 입력 데이터를 허용합니다. 1) 등록된 데이터 2) 클라우드의 데이터 3) 로컬의 데이터 올바른 형식을 사용하고 있는지 확인합니다. 자세한 내용은 [일괄 처리 채점에 일괄 처리 엔드포인트(미리 보기) 사용](how-to-use-batch-endpoint.md)을 참조하세요.|
| 제공된 엔드포인트 이름이 존재하거나 삭제되고 있습니다. | 새 이름으로 새 일괄 처리 엔드포인트를 만듭니다. `endpoint delete` 명령은 엔드포인트를 삭제하도록 표시합니다. 동일한 지역에 새 엔드포인트를 만드는 데 동일한 이름을 다시 사용할 수 없습니다. |
| 출력이 이미 있습니다. | 고유한 출력 위치를 구성하는 경우 각 엔드포인트 호출에 대해 새 출력을 제공해야 합니다. |

##  <a name="scoring-script-requirements"></a>채점 스크립트 요구 사항

MLflow가 아닌 모델을 사용하는 경우 채점 스크립트를 제공해야 합니다. 채점 스크립트에 다음 두 함수가 포함되어야 합니다.

- `init()`: 이후 유추를 위해 비용이 많이 드는 준비 또는 일반적인 준비에 이 함수를 사용합니다. 예를 들어 모델을 글로벌 개체에 로드하는 데 사용합니다. 이 함수는 프로세스를 시작할 때 한 번만 호출됩니다.
-  `run(mini_batch)`: 이 함수는 각 `mini_batch` 인스턴스에 대해 실행됩니다.
    -  `mini_batch`: `mini_batch` 값은 파일 경로 목록입니다.
    -  `response`: `run()` 메서드는 pandas 데이터 프레임 또는 배열을 반환해야 합니다. 이러한 반환된 요소는 공통 출력 파일에 추가됩니다. 반환되는 각 출력 요소는 입력 미니 일괄 처리의 입력 요소에 대한 성공적인 실행 하나를 나타냅니다. 단일 입력을 실행 출력 결과에 매핑하기에 충분한 데이터가 실행 결과에 포함되어 있는지 확인합니다. 실행 출력은 출력 파일에 기록되지만 순서대로 기록된다는 보장은 없으므로, 사용자는 출력의 키를 사용하여 올바른 입력에 매핑해야 합니다.

:::code language="python" source="~/azureml-examples-cli-preview/cli/endpoints/batch/mnist/code/digit_identification.py" :::

## <a name="understanding-logs-of-a-batch-scoring-job"></a>일괄 처리 채점 작업의 로그 이해

### <a name="get-logs"></a>로그 가져오기

Azure CLI 또는 REST를 사용하여 일괄 처리 엔드포인트를 호출하면 일괄 처리 채점 작업이 비동기적으로 실행됩니다. 일괄 처리 채점 작업에 대한 로그를 가져오기 위한 두 가지 옵션이 있습니다.

옵션 1: 로컬 콘솔에 로그 스트리밍

다음 명령을 실행하여 시스템 생성 로그를 콘솔로 스트리밍할 수 있습니다. `azureml-logs` 폴더의 로그만 스트리밍됩니다.

```bash
az ml job stream -name <job_name>
```

옵션 2: 스튜디오에서 로그 보기 

스튜디오에서 실행에 대한 링크를 얻으려면 다음을 실행합니다. 

```azurecli
az ml job show --name <job_name> --query interaction_endpoints.Studio.endpoint -o tsv
```

1. 위의 명령에서 반환된 값을 사용하여 스튜디오에서 작업을 엽니다. 
1. **batchscoring** 선택
1. **출력 + 로그** 탭 열기 
1. 검토하려는 로그 선택

### <a name="understand-log-structure"></a>로그 구조 이해

`azureml-logs` 및 `logs`의 두 가지 최상위 로그 폴더가 있습니다. 

`~/azureml-logs/70_driver_log.txt` 파일에는 채점 스크립트를 시작하는 컨트롤러의 정보가 포함되어 있습니다.  

일괄 처리 채점 작업의 분산 특성으로 인해 여러 원본의 로그가 있습니다. 그러나 다음과 같이 높은 수준의 정보를 제공하는 두 병합된 파일이 생성됩니다. 

- `~/logs/job_progress_overview.txt`: 이 파일은 지금까지 생성된 미니 일괄 처리(작업)의 수와 지금까지 처리된 미니 일괄 처리 수에 대한 높은 수준의 정보를 제공합니다. 미니 일괄 처리 끝으로 로그는 작업의 결과를 기록합니다. 작업이 실패할 경우 오류 메시지와, 문제 해결을 시작할 지점이 표시됩니다.

- `~/logs/sys/master_role.txt`: 이 파일은 실행 중인 작업의 주체 노드(오케스트레이터라고도 함) 보기를 제공합니다. 이 로그는 태스크 생성, 진행률 모니터링, 실행 결과에 대한 정보를 제공합니다.

스크립트의 오류에 대한 간략한 해석은 다음과 같습니다.

- `~/logs/user/error.txt`: 이 파일은 스크립트의 오류를 요약하려 합니다.

스크립트의 오류에 대한 자세한 내용은 다음과 같습니다.

- `~/logs/user/error/`: 이 파일은 항목 스크립트를 로드하고 실행하는 동안 throw된 예외에 대한 전체 스택 추적을 포함합니다.

각 노드가 점수 스크립트를 실행하는 방법을 완전히 해석하려면 각 노드에 대한 개별 프로세스 로그를 확인합니다. 프로세스 로그는 작업자 노드에서 그룹화한 `sys/node` 폴더에 있습니다.

- `~/logs/sys/node/<ip_address>/<process_name>.txt`: 이 파일은 작업자가 선택하거나 완료할 때 각각의 미니 일괄 처리에 대한 상세 정보를 제공합니다. 각 미니 일괄 처리에 대해 이 파일에는 다음이 포함됩니다.

    - 작업자 프로세스의 IP 주소 및 PID 
    - 총 항목 수, 성공적으로 처리된 항목 수 및 실패한 항목 수입니다.
    - 시작 시간, 기간, 처리 시간 및 실행 메서드 시간입니다.

또한 각 노드의 리소스 사용량에 대한 정기 확인 결과를 볼 수 있습니다. 로그 파일 및 설정 파일은 다음 폴더에 있습니다.

- `~/logs/perf`: 확인 간격(초)을 변경하려면 `--resource_monitor_interval`을 설정합니다. 기본 간격은 `600`입니다. 이는 약 10분입니다. 모니터링을 중지하려면 값을 `0`으로 설정합니다. 각 `<ip_address>` 폴더에는 다음이 포함됩니다.

    - `os/`: 노드에서 실행 중인 모든 프로세스에 대한 정보입니다. 한 번 확인 시 운영 체제 명령을 실행하고 결과를 파일에 저장합니다. 명령은 Linux의 경우 `ps`이고
        - `%Y%m%d%H`: 하위 폴더 이름은 시간(시)입니다.
            - `processes_%M`: 파일 이름은 확인 시간(분)으로 끝납니다.
    - `node_disk_usage.csv`: 노드의 자세한 디스크 사용량입니다.
    - `node_resource_usage.csv`: 노드의 리소스 사용량 개요입니다.
    - `processes_resource_usage.csv`: 각 프로세스의 리소스 사용량 개요입니다.

### <a name="how-to-log-in-scoring-script"></a>채점 스크립트에 로그인하는 방법

채점 스크립트에서 Python 로깅을 사용할 수 있습니다. 로그는 `logs/user/stdout/<node_id>/processNNN.stdout.txt`에 저장됩니다. 

```python
import argparse
import logging

# Get logging_level
arg_parser = argparse.ArgumentParser(description="Argument parser.")
arg_parser.add_argument("--logging_level", type=str, help="logging level")
args, unknown_args = arg_parser.parse_known_args()
print(args.logging_level)

# Initialize python logger
logger = logging.getLogger(__name__)
logger.setLevel(args.logging_level.upper())
logger.info("Info log statement")
logger.debug("Debug log statement")
```
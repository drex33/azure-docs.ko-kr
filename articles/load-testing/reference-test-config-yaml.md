---
title: 부하 테스트 구성 YAML
titleSuffix: Azure Load Testing
description: YAML 파일을 사용 하 여 부하 테스트를 구성 하는 방법을 알아봅니다. YAML 구성은 CI/CD 파이프라인에서 자동화 된 부하 테스트를 설정 하는 데 사용 됩니다.
services: load-testing
ms.service: load-testing
ms.topic: reference
ms.author: nicktrog
author: ntrogh
ms.date: 11/30/2021
adobe-target: true
ms.openlocfilehash: ece607a581ed4b29cbc52e61cacac4b730ed3086
ms.sourcegitcommit: 845eb7b0ed05ef2f0dfa6f054eaf5f32c780567c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133305587"
---
# <a name="configure-load-tests-in-yaml"></a>YAML에서 부하 테스트 구성

[Yaml](https://yaml.org/)을 사용 하 여 Azure 부하 테스트 미리 보기에서 부하 테스트를 구성 하는 방법에 대해 알아봅니다. 테스트 구성 YAML 파일을 사용 하 여 CI/CD 워크플로에서 부하 테스트를 만들고 실행 합니다.

> [!IMPORTANT]
> Azure 부하 테스트는 현재 미리 보기로 제공 됩니다.
> 베타, 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="load-test-definition"></a>부하 테스트 정의

테스트 구성에서는 다음 키를 사용 합니다.

| 키 | 형식 | Description | 
| ----- | ----- | ----- | 
| `version` | 문자열 | 서비스에서 사용 하는 YAML 구성 파일의 버전입니다. 현재 유효한 값은 `v0.1`뿐입니다. |
| `testName` | 문자열 | **필수**. 실행할 테스트의 이름입니다. 다른 테스트 실행의 결과는 Azure Portal의이 테스트 이름으로 수집 됩니다. |
| `testPlan` | 문자열 | **필수**. 실행할 Apache JMeter 테스트 스크립트에 대 한 상대 경로입니다. |
| `engineInstances` | 정수 | **필수**. 제공 된 테스트 계획을 실행할 병렬 테스트 엔진 인스턴스 수입니다. 이 속성을 업데이트 하 여 서비스에서 생성할 수 있는 부하의 양을 늘릴 수 있습니다. |
| `configurationFiles` | array | Apache JMeter 스크립트에서 참조 하는 관련 구성 파일의 목록입니다. 기본적으로 *`*.csv`* 테스트 계획의 폴더에 있는 모든 *.csv* 파일을 참조 하는 와일드 카드가 생성 됩니다. |
| `description` | 문자열 | 부하 테스트 실행에 대 한 간단한 설명입니다. |
| `failureCriteria` | 개체 | 테스트 실패를 나타내는 조건입니다. 각 기준은 다음과 같은 형식입니다.<BR>`[Aggregate_function] ([client_metric]) > [value]`<BR><BR>- *`[Aggregate function] ([client_metric])`*: 또는 중 하나 `avg(response_time_ms)``percentage(error)`<BR>- *`value`*: 정수 숫자입니다. |
| `secrets` | 개체 | Apache JMeter 스크립트에서 참조 하는 암호 목록입니다. |
| `secrets.name` | 문자열 | 비밀의 이름입니다. 이 이름은 Apache JMeter 스크립트에서 사용 하는 암호 이름과 일치 해야 합니다. |
| `secrets.value` | 문자열 | 비밀 URI를 Azure Key Vault 합니다. |
| `env` | 개체 | Apache JMeter 스크립트에서 참조 하는 환경 변수 목록입니다. |
| `env.name` | 문자열 | 환경 변수의 이름입니다. 이 이름은 Apache JMeter 스크립트에서 사용 하는 암호 이름과 일치 해야 합니다. |
| `env.value` | 문자열 | 환경 변수의 값입니다. |

다음 예제에는 부하 테스트에 대 한 구성이 포함 되어 있습니다.

```yaml
version: v0.1
testName: SampleTest
testPlan: SampleTest.jmx
description: Load test website home page
engineInstances: 1
configurationFiles:
  - '*.csv'
failureCriteria:
  - avg(response_time_ms) > 300
  - percentage(error) > 50
env:
  - name: my-variable
    value: my-value
secrets:
  - name: my-secret
    value: https://akv-contoso.vault.azure.net/secrets/MySecret
```

## <a name="next-steps"></a>다음 단계

[CI/CD 워크플로에서 자동화 된 회귀 테스트](tutorial-cicd-azure-pipelines.md)를 빌드하는 방법에 대해 알아봅니다.

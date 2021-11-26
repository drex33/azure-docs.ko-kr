---
title: CI/CD 도구를 사용하여 Azure Stream Analytics 작업의 빌드, 테스트 및 배포 자동화
description: 이 문서에서는 Azure Stream Analytics CI/CD 도구를 사용하여 Azure Stream Analytics 프로젝트를 자동으로 빌드, 테스트 및 배포하는 방법을 설명합니다.
services: stream-analytics
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 06/29/2021
ms.openlocfilehash: 7cae7c02e0a304718d1b10e25643782682aac71c
ms.sourcegitcommit: 18336347498b1c0593f97ef1110f0edd202c37bd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/26/2021
ms.locfileid: "133157057"
---
# <a name="automate-builds-tests-and-deployments-of-an-azure-stream-analytics-job-using-cicd-tools"></a>CI/CD 도구를 사용하여 Azure Stream Analytics 작업의 빌드, 테스트 및 배포 자동화

Azure Stream Analytics CI/CD npm 패키지를 사용 하 여 Azure Stream Analytics Visual Studio Code 또는 Visual Studio 프로젝트를 자동으로 빌드, 테스트 및 배포할 수 있습니다. 프로젝트는 개발 도구를 사용하여 만들거나 기존 Stream Analytics 작업에서 내보낼 수 있습니다. 이 문서에서는 모든 CI/CD 시스템에서 npm 패키지를 사용하는 방법을 설명합니다. Azure Pipelines를 사용한 배포는 [Azure DevOps를 사용하여 Stream Analytics 작업용 CI/CD 파이프라인 만들기](set-up-cicd-pipeline.md)를 참조하세요.

## <a name="installation"></a>설치

[패키지를 직접 다운로드](https://www.npmjs.com/package/azure-streamanalytics-cicd)하거나 `npm install -g azure-streamanalytics-cicd` 명령을 사용하여 [ 전역적으로 ](https://docs.npmjs.com/downloading-and-installing-packages-globally) 설치할 수 있습니다. **Azure Pipelines** 에서 빌드 파이프라인의 PowerShell 또는 Azure CLI 스크립트 작업에서도 사용할 수 있는 명령을 사용하는 것이 좋습니다.

## <a name="build-the-project"></a>프로젝트 빌드

**asa-streamanalytics-cicd** npm 패키지는 Stream Analytics [ Visual Studio Code 프로젝트](./quick-create-visual-studio-code.md) 또는 [ Visual Studio 프로젝트](stream-analytics-quick-create-vs.md)의 Azure Resource Manager 템플릿을 생성하는 도구를 제공합니다. Visual Studio Code 또는 Visual Studio를 설치하지 않고도 Windows, macOS 및 Linux에서 npm 패키지를 사용할 수도 있습니다.

패키지를 설치했으면 다음 명령을 사용하여 Stream Analytics 프로젝트를 빌드합니다.

```powershell
azure-streamanalytics-cicd build -project <projectFullPath> [-outputPath <outputPath>]
```

*build* 명령은 키워드 구문 검사를 수행하고 Azure Resource Manager 템플릿을 출력합니다.

| 매개 변수 | 설명 |
|---|---|
| `-project` | Visual Studio Code 프로젝트의 경우 **asaproj.json** 파일의 절대 경로 또는 Visual Studio 프로젝트의 경우 **[내 프로젝트 이름] .asaproj**. |
| `-outputPath` | Azure Resource Manager 템플릿의 출력 폴더 경로입니다. 지정 하지 않으면 템플릿이 현재 디렉터리에 배치 됩니다. |

#### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```powershell
azure-streamanalytics-cicd build -project "/Users/username/projects/samplejob/asaproj.json"
```

#### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```powershell
azure-streamanalytics-cicd build -project "/Users/username/projects/samplejob/samplejob.asaproj"
```

--- 

Stream Analytics 프로젝트가 성공적으로 빌드되면 출력 폴더 아래에 다음 두 파일이 생성됩니다.

* Azure Resource Manager 템플릿 파일.

   `[ProjectName].JobTemplate.json`

* Azure Resource Manager 매개변수 파일

   `[ProjectName].JobTemplate.parameters.json`

parameters.json 파일의 기본 매개변수는 Visual Studio Code 또는 Visual Studio 프로젝트의 설정에서 가져옵니다. 다른 환경에 배포하려면 해당 매개 변수를 적절하게 바꾸면 됩니다.

모든 자격 증명의 기본값은 **null** 입니다. Azure에 배포하기 전에 값을 설정해야 합니다.

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```

Azure Data Lake Store Gen1용 관리 ID를 출력 싱크로 사용하려면 Azure에 배포하기 전에 PowerShell을 사용하여 서비스 주체에 대한 액세스를 제공해야 합니다. [Resource Manager 템플릿에서 관리 ID를 사용하여 ADLS Gen1을 배포](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment)하는 방법을 자세히 알아봅니다.

## <a name="local-run"></a>로컬 실행

프로젝트에 로컬 입력 파일이 지정된 경우 `localrun` 명령을 사용하여 로컬에서 Stream Analytics 스크립트를 실행할 수 있습니다.

```powershell
azure-streamanalytics-cicd localrun -project <projectFullPath> [-outputPath <outputPath>] [-customCodeZipFilePath <zipFilePath>]
```

| 매개 변수 | 설명 |
|---|---|
| `-project` | Visual Studio Code 프로젝트의 경우 **asaproj.json** 파일의 경로 또는 Visual Studio 프로젝트의 경우 **[내 프로젝트 이름] .asaproj** 의 경로입니다. |
| `-outputPath` | 출력 폴더의 경로입니다. 지정하지 않으면 출력 결과 파일이 현재 디렉토리에 배치됩니다. |
| `-customCodeZipFilePath` | UDF 또는 역직렬 변환기(사용되는 경우)와 같은 C # 사용자 정의 코드용 zip 파일의 경로입니다. Dll을 zip 파일로 패키지 하고 이 경로를 지정 합니다. |

#### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```powershell
azure-streamanalytics-cicd localrun -project "/Users/roger/projects/samplejob/asaproj.json"
```

#### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```powershell
azure-streamanalytics-cicd localrun -project "/Users/roger/projects/samplejob/samplejob.asaproj"
```

---

> [!Note] 
> JavaScript UDF는 Windows 에서만 작동 합니다.

## <a name="automated-test"></a>자동화된 테스트

CI/CD npm 패키지를 사용하여 Stream Analytics 스크립트에 대한 자동화된 테스트를 구성하고 실행할 수 있습니다.

### <a name="add-a-test-case"></a>테스트 사례 실행

테스트 사례는 테스트 구성 파일에 설명 되어 있습니다. 시작하려면 `addtestcase` 명령을 사용하여 테스트 구성 파일에 테스트 사례 템플릿을 추가합니다. 테스트 구성 파일이 없는 경우 기본적으로 생성 됩니다.

```powershell
azure-streamanalytics-cicd addtestcase -project <projectFullPath> [-testConfigPath <testConfigFileFullPath>]
```

| 매개 변수 | 설명 |
|---|---|
| `-project` | Visual Studio Code 프로젝트의 경우 **asaproj.json** 파일의 경로 또는 Visual Studio 프로젝트의 경우 **[내 프로젝트 이름] .asaproj** 의 경로입니다. |
| `-testConfigPath` | 구성 파일의 경로를 나열합니다. 지정하지 않으면 파일은 **asaproj.json** 파일의 현재 디렉토리 아래 **\test** 에서 검색되며 기본 파일 이름은 **testConfig.json** 입니다. 존재하지 않는 경우 새 파일이 만들어집니다. |

> [!NOTE]
> 생성된 **testConfig.json** 파일의 `Script` 값은 컨텍스트를 제공하기 위한 것입니다. 테스트 로직에서는 사용되지 않습니다. 

#### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```powershell
azure-streamanalytics-cicd addtestcase -project "/Users/roger/projects/samplejob/asaproj.json"
```

#### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```powershell
azure-streamanalytics-cicd addtestcase -project "/Users/roger/projects/samplejob/samplejob.asaproj"
```
---

테스트 구성 파일이 비어 있으면 다음 내용이 파일에 기록됩니다. 그렇지 않으면 테스트 사례가 **testcases** 배열에 추가 됩니다. 필요한 입력 구성은 입력 구성 파일이 있는 경우 해당 파일에 따라 자동으로 채워집니다. 그렇지 않으면 기본값이 구성 됩니다. 테스트를 실행 하기 전에 각 입력 및 예상 출력의 **FilePath** 를 지정 해야 합니다. 구성을 수동으로 수정할 수 있습니다.

```json
{
  "Script": "",
  "TestCases": [
    {
      "Name": "Case 1",
      "Inputs": [
        {
          "InputAlias": [Input alias string],
          "Type": "Data Stream",
          "Format": "JSON",
          "FilePath": [Required],
          "ScriptType": "InputMock"
        }
      ],
      "ExpectedOutputs": [
        {
          "OutputAlias": [Output alias string],
          "FilePath": [Required],
          "Required": true
        }
      ]
    }
  ]
}
```

다음 예제에서는 두 개의 입력을 사용하는 쿼리에 대한 두 가지 테스트 사례를 보여줍니다. 테스트 유효성 검사에서 특정 출력을 무시하도록 하려면 예상 출력의 **필수** 필드를 **false** 로 설정합니다. 이 경우 FilePath 속성은 비어 있지 않아야 하지만 유효할 필요는 없습니다.

```JSON
{
  "Script": "C:\\...\\...\\samplejob.asaql",
  "TestCases": [
    {
      "Name": "Case 1 - Testing all outputs at once",
      "Inputs": [
        {
          "InputAlias": "entry",
          "Type": "Data Stream",
          "Format": "Json",
          "FilePath": "C:\\...\\...\\Case1_Data_entry.json",
          "ScriptType": "InputMock"
        },
        {
          "InputAlias": "exit",
          "Type": "Data Stream",
          "Format": "Json",
          "FilePath": "C:\\...\\...\\Case1_Data_exit.json",
          "ScriptType": "InputMock"
        }
      ],
      "ExpectedOutputs": [
        {
          "OutputAlias": "output1",
          "FilePath": "C:\\...\\...\\Case1_output1.json",
          "Required": true
        },
        {
          "OutputAlias": "output2",
          "FilePath": "C:\\...\\...\\Case1_output2.json",
          "Required": true
        }
      ]
    },
    {
      "Name": "Case 2 - Testing only one output at a time (recommended)",
      "Inputs": [
        {
          "InputAlias": "entry",
          "Type": "Data Stream",
          "Format": "Json",
          "FilePath": "C:\\...\\...\\Case2_Data_entry.json",
          "ScriptType": "InputMock"
        },
        {
          "InputAlias": "exit",
          "Type": "Data Stream",
          "Format": "Json",
          "FilePath": "C:\\...\\...\Case2_Data_exit.json",
          "ScriptType": "InputMock"
        }
      ],
      "ExpectedOutputs": [
        {
          "OutputAlias": "output1",
          "FilePath": "C:\\...\\...\\Case2_output1.json",
          "Required": true
        },
        {
          "OutputAlias": "output2",
          "FilePath": "[N/A]",
          "Required": false
        }
      ]
    }
  ]
}
```

> [!NOTE]
> 현재 `ScriptType` 요소에 대해 허용되는 유일한 값은 기본값이기도 한 `InputMock`입니다. 다른 값으로 설정하면 무시되고 기본값(`InputMock`)이 사용됩니다. 

### <a name="run-a-unit-test"></a>단위 테스트를 실행합니다.

다음 명령을 사용 하 여 프로젝트에 대 한 여러 테스트 사례를 실행할 수 있습니다. 출력 폴더에서 테스트 결과 요약이 생성 됩니다. 프로세스는 통과한 모든 테스트에 대해 **0** 코드로 종료됩니다. **-1** 예외가 발생했습니다. 테스트에 대한 **-2** 이(가) 실패했습니다.

```powershell
azure-streamanalytics-cicd test -project <projectFullPath> [-testConfigPath <testConfigFileFullPath>] [-outputPath <outputPath>] [-customCodeZipFilePath <zipFilePath>]
```

| 매개 변수 | 설명 |
|---|---|
| `-project` | Visual Studio Code 프로젝트의 경우 **asaproj.json** 파일의 경로 또는 Visual Studio 프로젝트의 경우 **[내 프로젝트 이름] .asaproj** 의 경로입니다. |
| `-testConfigPath` | 구성 파일의 경로입니다. 지정하지 않으면 파일은 **asaproj.json** 파일의 현재 디렉토리 아래 **\test** 에서 검색되며 기본 파일 이름은 **testConfig.json** 입니다.
| `-outputPath` | 테스트 결과 출력 폴더의 경로입니다. 지정하지 않으면 출력 결과 파일이 현재 디렉토리에 배치됩니다. |
| `-customCodeZipFilePath` | UDF 또는 역직렬 변환기와 같은 사용자 지정 코드가 사용되는 경우 zip 파일의 경로입니다. |

예를 들어 PowerShell 사용 터미널에서 모든 테스트 자산이 프로젝트 `test` 폴더의 하위 폴더에 있는 경우 각 테스트 실행 출력이 하위 폴더의 새 타임스탬프가 있는 하위 폴더에 저장됩니다. `testResults`

```PowerShell
$projectPath = "C:\...\...\samplejob\"

azure-streamanalytics-cicd test `
    -project ($projectPath + "asaproj.json") `
    -testConfigPath ($projectPath + "test\testConfig.json") `
    -outputPath ($projectPath + "test\testResults\$(Get-Date -Format "yyyyMMddHHmmss")\")
```

모든 테스트가 완료되면 JSON 형식의 테스트 결과 요약이 출력 폴더에 생성됩니다. 요약 파일의 이름은 **testResultSummary.json** 입니다.

```json
{
  "Total": (integer) total_number_of_test_cases,
  "Passed": (integer) number_of_passed_test_cases,
  "Failed": (integer) number_of_failed_test_cases,
  "Script": (string) absolute_path_to_asaql_file,
  "Results": [ (array) detailed_results_of_test_cases
    {
      "Name": (string) name_of_test_case,
      "Status": (integer) 0(passed)_or_1(failed),
      "Time": (string) time_span_of_running_test_case,
      "OutputMatched": [ (array) records_of_actual_outputs_equal_to_expected_outputs
        {
          "OutputAlias": (string) output_alias,
          "ExpectedOutput": (string) path_to_the_expected_output_file,
          "Output": (string) path_to_the_actual_output_file
        }
      ],
      "OutputNotEqual": [ (array) records_of_actual_outputs_not_equal_to_expected_outputs
        {
          "OutputAlias": (string) output_alias,
          "ExpectedOutput": (string) path_to_the_expected_output_file,
          "Output": (string) path_to_the_actual_output_file
        }
      ],
      "OutputMissing": [ (array) records_of_actual_outputs_missing
        {
          "OutputAlias": (string) output_alias,
          "ExpectedOutput": (string) path_to_the_expected_output_file,
          "Output": ""
        }
      ],
      "OutputUnexpected": [ (array) records_of_actual_outputs_unexpected
        {
          "OutputAlias": (string) output_alias,
          "ExpectedOutput": "",
          "Output": (string) path_to_the_actual_output_file
        }
      ],
      "OutputUnrequired": [ (array) records_of_actual_outputs_unrequired_to_be_checked
        {
          "OutputAlias": (string) output_alias,
          "ExpectedOutput": (string) path_to_the_expected_output_file,
          "Output": (string) path_to_the_actual_output_file
        }
      ]
    }
  ],
  "Time": (string) time_span_of_running_all_test_cases,
}
```

## <a name="deploy-to-azure"></a>Azure에 배포

빌드에서 생성된 Azure Resource Manager 템플릿 및 매개변수 파일을 사용하여 [ 작업을 Azure에 배포](../azure-resource-manager/templates/template-tutorial-use-parameter-file.md?tabs=azure-powershell#deploy-template)할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* [Azure Stream Analytics에 대한 지속적인 통합 및 지속적인 배포](cicd-overview.md)
* [Azure Pipelines를 사용하여 Stream Analytics 작업에 대한 CI/CD 파이프라인 설정](set-up-cicd-pipeline.md)

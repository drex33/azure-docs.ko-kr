---
title: 일반적인 문제 해결
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 및 Azure Synapse Analytics 파이프라인에서 외부 제어 작업의 문제를 해결하는 방법을 알아봅니다.
author: nabhishek
ms.service: data-factory
ms.subservice: troubleshooting
ms.custom: synapse
ms.topic: troubleshooting
ms.date: 09/30/2021
ms.author: abnarain
ms.openlocfilehash: ec238a018dea8940143aa6a2483c0e7dfa0d3d63
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129705630"
---
# <a name="troubleshoot-azure-data-factory-and-synapse-pipelines"></a>Azure Data Factory 및 Synapse 파이프라인 문제 해결

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory 및 Synapse 파이프라인의 외부 제어 활동에 대한 일반적인 문제 해결 방법을 살펴봅니다.

## <a name="connector-and-copy-activity"></a>커넥터 및 복사 활동

복사 작업을 사용하여 발생하는 오류와 같은 커넥터 문제는 [커넥터 문제 해결](connector-troubleshoot-guide.md) 문서를 참조하세요.

## <a name="azure-databricks"></a>Azure Databricks

### <a name="error-code-3200"></a>오류 코드: 3200

- **메시지**: 오류 403.

- **원인**: `The Databricks access token has expired.`

- **권장 사항**: 기본적으로 Azure Databricks 액세스 토큰은 90일 동안 유효합니다. 새 토큰을 만들고 연결 서비스를 업데이트하세요.

### <a name="error-code-3201"></a>오류 코드: 3201

- **메시지**: `Missing required field: settings.task.notebook_task.notebook_path.`

- **원인**: `Bad authoring: Notebook path not specified correctly.`

- **권장 사항**: Databricks 활동에서 노트북 경로를 지정하세요.

<br/> 

- **메시지**: `Cluster... does not exist.`

- **원인**: `Authoring error: Databricks cluster does not exist or has been deleted.`

- **권장 사항**: Databricks 클러스터가 있는지 확인하세요.

<br/> 

- **메시지**: `Invalid Python file URI... Please visit Databricks user guide for supported URI schemes.`

- **원인**: `Bad authoring.`

- **권장 사항**: 작업 영역 주소 지정 스키마의 절대 경로를 지정 하거나, DFS(Databricks File System)에 저장된 파일에 대한 `dbfs:/folder/subfolder/foo.py`를 지정하세요.

<br/> 

- **메시지**: `{0} LinkedService should have domain and accessToken as required properties.`

- **원인**: `Bad authoring.`

- **권장 사항**: [연결 서비스 정의](compute-linked-services.md#azure-databricks-linked-service)를 확인하세요.

<br/> 

- **메시지**: `{0} LinkedService should specify either existing cluster ID or new cluster information for creation.`

- **원인**: `Bad authoring.`

- **권장 사항**: [연결 서비스 정의](compute-linked-services.md#azure-databricks-linked-service)를 확인하세요.

<br/> 

- **메시지**: `Node type Standard_D16S_v3 is not supported. Supported node types: Standard_DS3_v2, Standard_DS4_v2, Standard_DS5_v2, Standard_D8s_v3, Standard_D16s_v3, Standard_D32s_v3, Standard_D64s_v3, Standard_D3_v2, Standard_D8_v3, Standard_D16_v3, Standard_D32_v3, Standard_D64_v3, Standard_D12_v2, Standard_D13_v2, Standard_D14_v2, Standard_D15_v2, Standard_DS12_v2, Standard_DS13_v2, Standard_DS14_v2, Standard_DS15_v2, Standard_E8s_v3, Standard_E16s_v3, Standard_E32s_v3, Standard_E64s_v3, Standard_L4s, Standard_L8s, Standard_L16s, Standard_L32s, Standard_F4s, Standard_F8s, Standard_F16s, Standard_H16, Standard_F4s_v2, Standard_F8s_v2, Standard_F16s_v2, Standard_F32s_v2, Standard_F64s_v2, Standard_F72s_v2, Standard_NC12, Standard_NC24, Standard_NC6s_v3, Standard_NC12s_v3, Standard_NC24s_v3, Standard_L8s_v2, Standard_L16s_v2, Standard_L32s_v2, Standard_L64s_v2, Standard_L80s_v2.`

- **원인**: `Bad authoring.`

- **권장 사항**: 오류 메시지를 참조하세요.

<br/>

### <a name="error-code-3202"></a>오류 코드: 3202

- **메시지**: `There were already 1000 jobs created in past 3600 seconds, exceeding rate limit: 1000 job creations per 3600 seconds.`

- **원인**: `Too many Databricks runs in an hour.`

- **권장 사항**: 이 Databricks 작업 영역을 사용하는 모든 파이프라인의 작업 생성 비율을 확인하세요. 파이프라인이 집계에 실행되는 Databricks를 너무 많이 시작한 경우 일부 파이프라인을 새 작업 영역으로 마이그레이션하세요.

<br/> 

- **메시지**: `Could not parse request object: Expected 'key' and 'value' to be set for JSON map field base_parameters, got 'key: "..."' instead.`

- **원인**: `Authoring error: No value provided for the parameter.`

- **권장 사항**: 파이프라인 JSON을 검사하고 baseParameters 노트북의 모든 매개 변수가 비어 있지 않은 값을 지정하는지 확인하세요.

<br/> 

- **메시지**: `User: `SimpleUserContext{userId=..., name=user@company.com, orgId=...}` is not authorized to access cluster.`

- **원인**: 액세스 토큰을 생성한 사용자는 연결 서비스에 지정된 Databricks 클러스터에 액세스할 수 없습니다.

- **권장 사항**: 사용자에게 필요한 권한이 작업 영역에 있는지 확인하세요.

<br/> 

- **메시지**: `Job is not fully initialized yet. Please retry later.`

- **원인**: 작업이 초기화되지 않았습니다.

- **권장 사항**: 기다렸다가 나중에 다시 시도하세요.

### <a name="error-code-3203"></a>오류 코드: 3203

- **메시지**: `The cluster is in Terminated state, not available to receive jobs. Please fix the cluster or retry later.`

- **원인**: 클러스터가 종료되었습니다. 대화형 클러스터의 경우 이 문제로 인해 경합 상태가 발생할 수도 있습니다.

- **권장 사항**: 이 오류를 방지 하려면 작업 클러스터를 사용하세요.

### <a name="error-code-3204"></a>오류 코드: 3204

- **메시지**: `Job execution failed.`

- **원인**: 오류 메시지는 예기치 않은 클러스터 상태 또는 특정 활동과 같은 다양한 문제를 나타냅니다. 종종 오류 메시지가 나타나지 않는 경우가 있습니다.

- **권장 사항**: 해당 없음

### <a name="error-code-3208"></a>오류 코드: 3208

- **메시지**: `An error occurred while sending the request.`

- **원인**: Databricks 서비스에 대한 네트워크 연결이 중단되었습니다.

- **권장 사항**: 자체 호스팅 통합 런타임을 사용하는 경우 통합 런타임 노드에서 네트워크 연결이 안정적인지 확인합니다. Azure 통합 런타임을 사용하는 경우 다시 시도하면 일반적으로 작동합니다.
 
### <a name="the-boolean-run-output-starts-coming-as-string-instead-of-expected-int"></a>부울 실행 출력이 예상된 int 대신 문자열로 표시될 수 있습니다.

- **증상:** 부울 실행 출력이 `"0"` `"1"` 예상된 int(예: 또는 ) 대신 문자열(예: 또는 )로 표시될 수 `0` `1` 있습니다.

   :::image type="content" source="media/data-factory-troubleshoot-guide/databricks-pipeline.png" alt-text="Databricks 파이프라인의 스크린샷.":::

    이 출력을 사용하는 파이프라인이 실패하기 시작한 2021년 9월 28일 오전 9시 IST에서 이 변경이 발견되었습니다. 파이프라인에 대한 변경이 없으며 실패하기 전에 부울 출력이 예상대로 표시되었습니다. 

   :::image type="content" source="media/data-factory-troubleshoot-guide/old-and-new-output.png" alt-text="출력의 차이점을 스크린샷으로 표시합니다.":::

- **원인:** 이 문제는 의도적으로 변경된 최근 변경으로 인해 발생합니다. 변경 후 결과가 0으로 시작하는 숫자인 경우 Azure Data Factory 숫자를 버그인 8진수 값으로 변환합니다. 이 숫자는 항상 0 또는 1이며 변경 전에 문제를 발생시킨 적이 없습니다. 따라서 8진수 변환을 수정하기 위해 Notebook 실행에서 문자열 출력이 있는 그대로 전달됩니다. 

- **권장 사항:** **if** 조건을 같은 것으로 `if(value=="0")` 변경합니다.

## <a name="azure-data-lake-analytics"></a>Azure 데이터 레이크 분석

다음 표는 U-SQL에 적용됩니다.
 
### <a name="error-code-2709"></a>오류 코드: 2709

- **메시지**: `The access token is from the wrong tenant.`

- **원인**: 잘못된 Azure AD(Azure Active Directory) 테넌트입니다.

- **권장 사항**: 잘못된 Azure AD(Azure Active Directory) 테넌트입니다.

<br/>

- **메시지**: `We cannot accept your job at this moment. The maximum number of queued jobs for your account is 200. `

- **원인**: 이 오류는 Data Lake Analytics에 대한 제한으로 인해 발생합니다.

- **권장 사항**: Data Lake Analytics에 제출된 작업 수를 줄이세요. 활동에 대한 트리거 및 동시성 설정을 변경하거나 Data Lake Analytics의 제한을 늘리세요.

<br/> 

- **메시지**: `This job was rejected because it requires 24 AUs. This account's administrator-defined policy prevents a job from using more than 5 AUs.`

- **원인**: 이 오류는 Data Lake Analytics에 대한 제한으로 인해 발생합니다.

- **권장 사항**: Data Lake Analytics에 제출된 작업 수를 줄이세요. 활동에 대한 트리거 및 동시성 설정을 변경하거나 Data Lake Analytics의 제한을 늘리세요.

### <a name="error-code-2705"></a>오류 코드: 2705

- **메시지**: `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/> <br/> User is not able to access Data Lake Store. <br/> <br/> User is not authorized to use Data Lake Analytics.`

- **원인**: 서비스 주체 또는 인증서가 저장소의 파일에 액세스할 수 없습니다.

- **권장 사항**: Data Lake Analytics 작업을 위해 사용자가 제공하는 서비스 주체 또는 인증서에 Data Lake Analytics 계정에 대한 액세스 권한 및 루트 폴더의 기본 Data Lake Storage 인스턴스가 모두 있는지 확인하세요.

### <a name="error-code-2711"></a>오류 코드: 2711

- **메시지**: `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/> <br/> User is not able to access Data Lake Store. <br/> <br/> User is not authorized to use Data Lake Analytics.`

- **원인**: 서비스 주체 또는 인증서가 저장소의 파일에 액세스할 수 없습니다.

- **권장 사항**: Data Lake Analytics 작업을 위해 사용자가 제공하는 서비스 주체 또는 인증서에 Data Lake Analytics 계정에 대한 액세스 권한 및 루트 폴더의 기본 Data Lake Storage 인스턴스가 모두 있는지 확인하세요.

<br/> 

- **메시지**: `Cannot find the 'Azure Data Lake Store' file or folder.`

- **원인**: U-SQL 파일의 경로가 잘못되었거나 연결 서비스 자격 증명에 액세스 권한이 없습니다.

- **권장 사항**: 연결 서비스에서 제공되는 경로 및 자격 증명을 확인하세요.

### <a name="error-code-2704"></a>오류 코드: 2704

- **메시지**: `Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.<br/> <br/> User is not able to access Data Lake Store. <br/> <br/> User is not authorized to use Data Lake Analytics.`

- **원인**: 서비스 주체 또는 인증서가 저장소의 파일에 액세스할 수 없습니다.

- **권장 사항**: Data Lake Analytics 작업을 위해 사용자가 제공하는 서비스 주체 또는 인증서에 Data Lake Analytics 계정에 대한 액세스 권한 및 루트 폴더의 기본 Data Lake Storage 인스턴스가 모두 있는지 확인하세요.

### <a name="error-code-2707"></a>오류 코드: 2707

- **메시지**: `Cannot resolve the account of AzureDataLakeAnalytics. Please check 'AccountName' and 'DataLakeAnalyticsUri'.`

- **원인**: 연결 서비스의 Data Lake Analytics 계정이 잘못 되었습니다.

- **권장 사항**: 올바른 계정이 제공되었는지 확인하세요.

### <a name="error-code-2703"></a>오류 코드: 2703

- **메시지**: `Error Id: E_CQO_SYSTEM_INTERNAL_ERROR (or any error that starts with "Error Id:").`

- **원인**: Data Lake Analytics에서 오류가 발생했습니다.

- **권장 사항**: 작업이 Data Lake Analytics에 제출되었으나 해당 스크립트가 모두 실패했습니다. Data Lake Analytics를 조사하세요. 포털에서 Data Lake Analytics 계정으로 이동하고 Data Factory 활동 실행 ID(파이프라인 실행 ID는 사용 안 함)를 사용하여 작업을 찾으세요. 해당 작업은 오류에 대한 자세한 정보를 제공하며 문제를 해결하는 데 도움이 됩니다.

   해결 방법이 명확하지 않으면 Data Lake Analytics 지원 팀에 문의하여 계정 이름 및 작업 ID가 포함된 작업 URL(Universal Resource Locator)을 제공하세요.
 
## <a name="azure-functions"></a>Azure 함수

### <a name="error-code-3602"></a>오류 코드: 3602

- **메시지**: `Invalid HttpMethod: '%method;'.`

- **원인**: 활동 페이로드에 지정된 Httpmethod는 Azure 함수 활동에서 지원되지 않습니다.

- **권장 사항**: 지원되는 Httpmethods는 다음과 같습니다. PUT, POST, GET, DELETE, OPTIONS, HEAD 및 TRACE

### <a name="error-code-3603"></a>오류 코드: 3603

- **메시지**: `Response Content is not a valid JObject.`

- **원인**: 호출된 Azure 함수에서 응답에 JSON 페이로드를 반환하지 않았습니다. Azure Data Factory 및 Synapse 파이프라인 Azure 함수 활동은 JSON 응답 콘텐츠만 지원합니다.

- **권장 사항**: C# 함수가 반환할 수 있는 것과 같이 유효한 JSON 페이로드를 반환하도록 Azure 함수를 업데이트하세요. `(ActionResult)new OkObjectResult("{\"Id\":\"123\"}");`

### <a name="error-code-3606"></a>오류 코드: 3606

- **메시지**: Azure 함수 활동에 함수 키가 없습니다.

- **원인**: Azure 함수 활동 정의가 완료되지 않았습니다.

- **권장 사항**: 입력 Azure 함수 활동 JSON 정의에 `functionKey`라는 속성이 있는지 확인하세요.

### <a name="error-code-3607"></a>오류 코드: 3607

- **메시지**: `Azure function activity missing function name.`

- **원인**: Azure 함수 활동 정의가 완료되지 않았습니다.

- **권장 사항**: 입력 Azure 함수 활동 JSON 정의에 `functionName`이라는 속성이 있는지 확인하세요.

### <a name="error-code-3608"></a>오류 코드: 3608

- **메시지**: `Call to provided Azure function '%FunctionName;' failed with status-'%statusCode;' and message - '%message;'.`

- **원인**: 활동 정의의 Azure 함수 세부 정보가 잘못되었을 수 있습니다.

- **권장 사항**: Azure 함수 세부 정보를 수정하고 다시 시도하세요.

### <a name="error-code-3609"></a>오류 코드: 3609

- **메시지**: `Azure function activity missing functionAppUrl.`

- **원인**: Azure 함수 활동 정의가 완료되지 않았습니다.

- **권장 사항**: 입력 Azure 함수 활동 JSON 정의에 `functionAppUrl`이라는 속성이 있는지 확인하세요.

### <a name="error-code-3610"></a>오류 코드: 3610

- **메시지**: `There was an error while calling endpoint.`

- **원인**: 함수 URL이 잘못되었을 수 있습니다.

- **권장 사항**: 활동 JSON에서 `functionAppUrl`의 값이 올바른지 확인한 후 다시 시도하세요.

### <a name="error-code-3611"></a>오류 코드: 3611

- **메시지**: `Azure function activity missing Method in JSON.`

- **원인**: Azure 함수 활동 정의가 완료되지 않았습니다.

- **권장 사항**: 입력 Azure 함수 활동 JSON 정의에 `method`라는 속성이 있는지 확인하세요.

### <a name="error-code-3612"></a>오류 코드: 3612

- **메시지**: `Azure function activity missing LinkedService definition in JSON.`

- **원인**: Azure 함수 활동 정의가 완료되지 않았습니다.

- **권장 사항**: 입력 Azure 함수 활동 JSON 정의에 연결 서비스 정보가 있는지 확인하세요.

## <a name="azure-machine-learning"></a>Azure Machine Learning

### <a name="error-code-4101"></a>오류 코드: 4101

- **메시지**: `AzureMLExecutePipeline activity '%activityName;' has invalid value for property '%propertyName;'.`

- **원인**: 속성 `%propertyName;`의 형식이 잘못되었거나 정의가 누락되었습니다.

- **권장 사항**: `%activityName;` 활동에 올바른 데이터로 정의된 `%propertyName;` 속성이 있는지 확인하세요.

### <a name="error-code-4110"></a>오류 코드: 4110

- **메시지**: `AzureMLExecutePipeline activity missing LinkedService definition in JSON.`

- **원인**: AzureMLExecutePipeline 활동 정의가 완료되지 않았습니다.

- **권장 사항**: 입력 AzureMLExecutePipeline 활동 JSON 정의에 올바른 연결 서비스 세부 정보가 있는지 확인하세요.

### <a name="error-code-4111"></a>오류 코드: 4111

- **메시지**: `AzureMLExecutePipeline activity has wrong LinkedService type in JSON. Expected LinkedService type: '%expectedLinkedServiceType;', current LinkedService type: Expected LinkedService type: '%currentLinkedServiceType;'.`

- **원인**: 활동 정의가 잘못되었습니다.

- **권장 사항**: 입력 AzureMLExecutePipeline 활동 JSON 정의에 올바른 연결 서비스 세부 정보가 있는지 확인하세요.

### <a name="error-code-4112"></a>오류 코드: 4112

- **메시지**: `AzureMLService linked service has invalid value for property '%propertyName;'.`

- **원인**: '%PropertyName;' 속성의 형식이 잘못되었거나 정의가 누락되었습니다.

- **권장 사항**: 연결 서비스에 올바른 데이터로 정의된 `%propertyName;` 속성이 있는지 확인하세요.

### <a name="error-code-4121"></a>오류 코드: 4121

- **메시지**: `Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **원인**: Azure Machine Learning에 액세스하는 데 사용된 자격 증명이 만료되었습니다.

- **권장 사항**: 자격 증명이 유효한지 확인한 후 다시 시도하세요.

### <a name="error-code-4122"></a>오류 코드: 4122

- **메시지**: `Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **원인**: Azure Machine Learning 연결 서비스에 제공된 자격 증명이 유효하지 않거나 작업에 대한 권한이 없습니다.

- **권장 사항**: 연결 서비스의 자격 증명이 유효하고 Azure Machine Learning에 대한 액세스 권한이 있는지 확인하세요.

### <a name="error-code-4123"></a>오류 코드: 4123

- **메시지**: `Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **원인**: `pipelineParameters`와 같은 활동의 속성은 Azure ML(Machine Learning) 파이프라인에 유효하지 않습니다.

- **권장 사항**: 활동 속성의 값이 연결 서비스에 지정된 게시된 Azure ML 파이프라인의 예상 페이로드와 일치하는지 확인하세요.

### <a name="error-code-4124"></a>오류 코드: 4124

- **메시지**: `Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **원인**: 게시된 Azure ML 파이프라인 끝점이 존재하지 않습니다.

- **권장 사항**: 연결 서비스에 지정된 게시된 Azure Machine Learning 파이프라인 끝점이 Azure Machine Learning에 있는지 확인하세요.

### <a name="error-code-4125"></a>오류 코드: 4125

- **메시지**: `Request sent to Azure Machine Learning for operation '%operation;' failed with http status code '%statusCode;'. Error message from Azure Machine Learning: '%externalMessage;'.`

- **원인**: Azure Machine Learning에 서버 오류가 있습니다.

- **권장 사항**: 나중에 다시 시도하세요. 문제가 계속되는 경우 Azure Machine Learning 팀에 도움을 요청하세요.

### <a name="error-code-4126"></a>오류 코드: 4126

- **메시지**: `Azure ML pipeline run failed with status: '%amlPipelineRunStatus;'. Azure ML pipeline run Id: '%amlPipelineRunId;'. Please check in Azure Machine Learning for more error logs.`

- **원인**: Azure ML 파이프라인을 실행하지 못했습니다.

- **권장 사항**: 자세한 오류 로그를 알아보려면 Azure Machine Learning를 확인하고 ML 파이프라인을 수정하세요.

## <a name="common"></a>일반

### <a name="error-code-2103"></a>오류 코드: 2103

- **메시지**: `Please provide value for the required property '%propertyName;'.`

- **원인**: 속성에 필요한 값이 제공되지 않았습니다.

- **권장 사항**: 메시지의 값을 제공하고 다시 시도하세요.

### <a name="error-code-2104"></a>오류 코드: 2104

- **메시지**: `The type of the property '%propertyName;' is incorrect.`

- **원인**: 제공된 속성 형식이 올바르지 않습니다.

- **권장 사항**: 속성의 형식을 수정하고 다시 시도하세요.

### <a name="error-code-2105"></a>오류 코드: 2105

- **메시지**: `An invalid json is provided for property '%propertyName;'. Encountered an error while trying to parse: '%message;'.`

- **원인**: 속성의 값이 유효하지 않거나 필요한 형식이 아닙니다.

- **권장 사항**: 해당 속성의 설명서를 참조하고 제공된 값에 올바른 형식 및 유형이 포함되어 있는지 확인하세요.

### <a name="error-code-2106"></a>오류 코드: 2106

- **메시지**: `The storage connection string is invalid. %errorMessage;`

- **원인**: 저장소에 대한 연결 문자열이 유효하지 않거나 형식이 잘못되었습니다.

- **권장 사항**: Azure Portal로 이동하여 저장소를 찾은 다음, 연결 문자열을 복사하여 연결 서비스에 붙여넣고 다시 시도하세요.

### <a name="error-code-2110"></a>오류 코드: 2110

- **메시지**: `The linked service type '%linkedServiceType;' is not supported for '%executorType;' activities.`

- **원인**: 활동에 지정된 연결 서비스가 잘못되었습니다.

- **권장 사항**: 연결 서비스 유형이 작업에 지원되는 유형 중 하나인지 확인하세요. 예를 들어 HDI 활동에 대한 연결 서비스 유형은 HDInsight 또는 HDInsightOnDemand일 수 있습니다.

### <a name="error-code-2111"></a>오류 코드: 2111

- **메시지**: `The type of the property '%propertyName;' is incorrect. The expected type is %expectedType;.`

- **원인**: 제공된 속성의 형식이 올바르지 않습니다.

- **권장 사항**: 속성 형식 수정하고 다시 시도하세요.

### <a name="error-code-2112"></a>오류 코드: 2112

- **메시지**: `The cloud type is unsupported or could not be determined for storage from the EndpointSuffix '%endpointSuffix;'.`

- **원인**: 클라우드 유형이 지원되지 않거나 EndpointSuffix에서 저장소를 확인할 수 없습니다.

- **권장 사항**: 다른 클라우드에서 저장소를 사용하고 다시 시도하세요.

## <a name="custom"></a>사용자 지정

다음 표는 Azure Batch에 적용됩니다.
 
### <a name="error-code-2500"></a>오류 코드: 2500

- **메시지**: `Hit unexpected exception and execution failed.`

- **원인**: `Can't launch command, or the program returned an error code.`

- **권장 사항**: 실행 파일이 있는지 확인하세요. 프로그램이 시작되면 *stdout.txt* 및 *stderr.txt* 가 저장소 계정에 업로드되었는지 확인하세요. 디버깅을 위해 코드에 로그를 포함하는 것이 좋습니다.

### <a name="error-code-2501"></a>오류 코드: 2501

- **메시지**: `Cannot access user batch account; please check batch account settings.`

- **원인**: 일괄 액세스 키 또는 풀 이름이 잘못되었습니다.

- **권장 사항**: 연결 서비스의 풀 이름 및 일괄 액세스 키를 확인하세요.

### <a name="error-code-2502"></a>오류 코드: 2502

- **메시지**: `Cannot access user storage account; please check storage account settings.`

- **원인**: 저장소 계정 이름 또는 액세스 키가 잘못되었습니다.

- **권장 사항**: 연결 서비스의 저장소 계정 이름 및 액세스 키를 확인하세요.

### <a name="error-code-2504"></a>오류 코드: 2504

- **메시지**: `Operation returned an invalid status code 'BadRequest'.`

- **원인**: 사용자 지정 활동의 `folderPath`에 파일이 너무 많습니다. `resourceFiles`의 총 크기는 32,768자를 초과할 수 없습니다.

- **권장 사항**: 불필요한 파일을 제거하거나, 압축을 풀고 압축 풀기 명령을 추가하여 압축을 푸세요.
   
   예를 들어 `powershell.exe -nologo -noprofile -command "& { Add-Type -A 'System.IO.Compression.FileSystem'; [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ; $folder\yourProgram.exe`를 사용합니다.

### <a name="error-code-2505"></a>오류 코드: 2505

- **메시지**: `Cannot create Shared Access Signature unless Account Key credentials are used.`

- **원인**: 사용자 지정 활동은 액세스 키를 사용하는 저장소 계정만 지원합니다.

- **권장 사항**: 오류 설명을 참조하세요.

### <a name="error-code-2507"></a>오류 코드: 2507

- **메시지**: `The folder path does not exist or is empty: ...`

- **원인**: 지정된 경로의 저장소 계정에 파일이 없습니다.

- **권장 사항**: 폴더 경로에는 실행할 실행 파일이 포함되어 있어야 합니다.

### <a name="error-code-2508"></a>오류 코드: 2508

- **메시지**: `There are duplicate files in the resource folder.`

- **원인**: 이름이 동일한 여러 파일이 folderPath의 여러 하위 폴더에 있습니다.

- **권장 사항**: 사용자 지정 활동은 folderPath에서 폴더 구조를 평면화합니다. 폴더 구조를 유지해야 하는 경우 파일을 압축한 후 압축 풀기 명령을 사용하여 Azure Batch에서 압축을 풉니다.
   
   예를 들어 `powershell.exe -nologo -noprofile -command "& { Add-Type -A 'System.IO.Compression.FileSystem'; [IO.Compression.ZipFile]::ExtractToDirectory($zipFile, $folder); }" ; $folder\yourProgram.exe`를 사용합니다.

### <a name="error-code-2509"></a>오류 코드: 2509

- **메시지**: `Batch url ... is invalid; it must be in Uri format.`

- **원인**: 일괄 처리 URL은 `https://mybatchaccount.eastus.batch.azure.com`과 유사해야 합니다.

- **권장 사항**: 오류 설명을 참조하세요.

### <a name="error-code-2510"></a>오류 코드: 2510

- **메시지**: `An error occurred while sending the request.`

- **원인**: 일괄 처리 URL이 유효하지 않습니다.

- **권장 사항**: 일괄 처리 URL을 확인하세요.
 
## <a name="hdinsight"></a>HDInsight

### <a name="error-code-206"></a>오류 코드: 206

- **메시지**: `The batch ID for Spark job is invalid. Please retry your job.`

- **원인**: 이 오류를 초래한 서비스에 내부 문제가 발생했습니다.

- **권장 사항**: 이 문제는 일시적일 수 있습니다. 잠시 후 작업을 다시 시도하세요.

### <a name="error-code-207"></a>오류 코드: 207

- **메시지**: `Could not determine the region from the provided storage account. Please try using another primary storage account for the on demand HDI.`

- **원인**: 기본 저장소 계정에서 지역을 확인하는 동안 내부 오류가 발생했습니다.

- **권장 사항**: 다른 저장소를 사용해 보세요. 

### <a name="error-code-208"></a>오류 코드: 208

- **메시지**: `Service Principal or the MSI authenticator are not instantiated. Please consider providing a Service Principal in the HDI on demand linked service which has permissions to create an HDInsight cluster in the provided subscription and try again.`

- **원인**: 서비스 주체를 읽거나 MSI 인증을 인스턴스화하는 중 내부 오류가 발생했습니다.

- **권장 사항**: 제공된 구독에서 HDInsight 클러스터를 생성할 권한이 있는 서비스 주체를 제공하고 다시 시도하세요. [ID 관리가 올바르게 설정](../hdinsight/hdinsight-managed-identities.md)되어 있는지 확인하세요.

### <a name="error-code-2300"></a>오류 코드: 2300

- **메시지**: `Failed to submit the job '%jobId;' to the cluster '%cluster;'. Error: %errorMessage;.`

- **원인**: 오류 메시지에 `The remote name could not be resolved.`와 유사한 메시지가 포함되어 있습니다. 제공된 클러스터 URI가 유효하지 않을 수 있습니다.

- **권장 사항**: 클러스터가 삭제되지 않았고 제공된 URI가 올바른지 확인하세요. 브라우저에서 URI를 열 때 Ambari UI가 표시되어야 합니다. 클러스터가 가상 네트워크에 있는 경우 URI는 비공개 URI여야 합니다. 이 URI를 열려면 동일한 가상 네트워크에 속한 VM(가상 머신)을 사용합니다.

   자세한 내용은 [Apache Hadoop 서비스에 직접 연결](../hdinsight/hdinsight-plan-virtual-network-deployment.md#directly-connect-to-apache-hadoop-services)을 참조하세요.
 
 </br>

- **원인**: 오류 메시지에 `A task was canceled.`와 유사한 메시지가 포함되었다면 작업 제출 시간이 초과된 것입니다.

- **권장 사항**: 일반적인 HDInsight 연결 또는 네트워크 연결 문제일 수 있습니다. 먼저 브라우저에서 HDInsight Ambari UI를 사용할 수 있는지 확인하세요. 그리고 나서 자격 증명이 여전히 유효한지 확인합니다.
   
   자체 호스팅 IR(Integrated Runtime)을 사용하는 경우 자체 호스팅 IR이 설치된 VM 또는 컴퓨터에서 이 단계를 수행합니다. 그런 다음, 작업을 다시 제출해 보세요.

   자세한 내용은 [Ambari Web UI](../hdinsight/hdinsight-hadoop-manage-ambari.md#ambari-web-ui)를 참조하세요.

 </br>

- **원인**: 오류 메시지에 `User admin is locked out in Ambari` 또는 `Unauthorized: Ambari user name or password is incorrect`와 유사한 메시지가 포함되어 있는 경우 HDInsight의 자격 증명이 잘못되었거나 만료된 상태입니다.

- **권장 사항**: 자격 증명을 수정하고 연결 서비스를 다시 배포하세요. 먼저 브라우저에서 클러스터 URI를 열고 로그인을 시도하여 자격 증명이 HDInsight에서 작동하는지 확인합니다. 자격 증명이 작동하지 않으면 Azure Portal에서 재설정할 수 있습니다.

   ESP 클러스터의 경우 [셀프 서비스 암호 재설정](https://support.microsoft.com/account-billing/reset-your-work-or-school-password-using-security-info-23dde81f-08bb-4776-ba72-e6b72b9dda9e)을 통해 암호를 재설정합니다.

 </br>

- **원인**: 오류 메시지에 `502 - Web server received an invalid response while acting as a gateway or proxy server`와 유사한 메시지가 포함 되어 있으면 HDInsight 서비스에서 이 오류가 반환 됩니다.

- **권장 사항**: Ambari 서버 프로세스가 종료되면 502 오류가 자주 발생합니다. 헤드 노드를 다시 부팅하여 Ambari 서비스를 다시 시작할 수 있습니다.

    1. SSH를 사용하여 HDInsight의 노드 중 하나에 연결합니다.
    1. `ping headnodehost`를 실행하여 활성 헤드 노드 호스트를 식별합니다.
    1. SSH를 사용해 활성 헤드 노드에 Ambari 서버를 배치하여 활성 헤드 노드에 연결합니다. 
    1. 활성 헤드 노드를 다시 부팅합니다.

       자세한 내용은 Azure HDInsight 문제 해결 설명서를 참조하세요. 다음은 그 예입니다.

       * [Ambari UI 502 오류](https://hdinsight.github.io/ambari/ambari-ui-502-error.html)
       * [Apache Spark Thrift 서버에 대한 RpcTimeoutException](../hdinsight/spark/apache-spark-troubleshoot-rpctimeoutexception.md)
       * [Application Gateway의 잘못된 게이트웨이 오류 문제 해결](../application-gateway/application-gateway-troubleshooting-502.md)

 </br>

- **원인**: 오류 메시지에 `Unable to service the submit job request as templeton service is busy with too many submit job requests` 또는 `Queue root.joblauncher already has 500 applications, cannot accept submission of application`와 유사한 메시지가 포함되어 있으면 동시에 너무 많은 작업이 HDInsight에 전송됩니다.

- **권장 사항**: HDInsight에 제출된 동시 작업 수를 제한하세요. 동일한 활동으로 작업을 전송하는 경우 작업 동시성을 참조하세요. 동시 파이프라인 실행이 시간이 지남에 따라 분산되도록 트리거를 변경합니다.

   오류에서 제안된 대로 `templeton.parallellism.job.submit`을 조정하려면 [HDInsight 설명서](../hdinsight/hdinsight-hadoop-templeton-webhcat-debug-errors.md)를 참조하세요.

### <a name="error-code-2301"></a>오류 코드: 2301

- **메시지**: `Could not get the status of the application '%physicalJobId;' from the HDInsight service. Received the following error: %message;. Please refer to HDInsight troubleshooting documentation or contact their support for further assistance.`

- **원인**: HDInsight 클러스터 또는 서비스에 문제가 있습니다.

- **권장 사항**: 이 오류는 실행 중인 작업의 상태를 요청할 때 서비스가 HDInsight 클러스터로부터 응답을 받지 못하는 경우에 발생합니다. 클러스터 자체에서 이 문제가 발생할 수 있습니다. 또는 HDInsight 서비스에서 중단이 발생할 수 있습니다.

   [HDInsight 문제 해결 설명서](../hdinsight/hdinsight-troubleshoot-guide.md)를 참조하거나 Microsoft 지원에 추가 지원을 문의하세요.

### <a name="error-code-2302"></a>오류 코드: 2302

- **메시지**: `Hadoop job failed with exit code '%exitCode;'. See '%logPath;/stderr' for more details. Alternatively, open the Ambari UI on the HDI cluster and find the logs for the job '%jobId;'. Contact HDInsight team for further support.`

- **원인**: 작업이 HDI 클러스터에 제출되었지만 작업이 실패했습니다.

- **권장 사항**: 

 1. 다음과 같이 Ambari UI를 확인하세요.
    1. 모든 서비스가 계속 실행 중인지 확인합니다.
    1. Ambari UI에서 대시보드의 경고 섹션을 확인합니다.
       1. 경고 및 경고 관련 해결 방법에 대한 자세한 내용은 [클러스터 관리 및 모니터링](https://docs.cloudera.com/HDPDocuments/Ambari-2.7.5.0/managing-and-monitoring-ambari/content/amb_predefined_alerts.html)을 참조하세요.
    1. YARN 메모리를 검토합니다. YARN 메모리가 높으면 작업 처리가 지연될 수 있습니다. Spark 애플리케이션/작업을 수용할 리소스가 부족한 경우 클러스터에 메모리와 코어가 충분하도록 클러스터를 확장합니다. 
 1. 샘플 테스트 작업을 실행합니다.
    1. HDInsight 백엔드에서 동일한 작업을 실행하는 경우 작업이 성공적으로 수행되었는지 확인하세요. 샘플 실행에 대한 예제를 보려면 [ MapReduce 예제 실행](../hdinsight/hadoop/apache-hadoop-run-samples-linux.md)을 참조하세요. 
 1. HDInsight에서 작업이 계속 실패하면 지원 팀에 제공할 응용 프로그램 로그 및 정보를 확인하세요.
    1. 작업이 YARN에 제출되었는지 확인합니다. 작업이 YARN에 제출되지 않은 경우 `--master yarn`을 사용합니다.
    1. 응용 프로그램의 실행이 완료되면 YARN 응용 프로그램의 시작 시간 및 종료 시간을 수집합니다. 응용 프로그램이 실행을 완료하지 못한 경우 시작 시간/실행 시간을 수집합니다.
    1. `yarn logs -applicationId <Insert_Your_Application_ID>`로 응용 프로그램 로그를 확인 및 수집합니다.
    1. `/var/log/hadoop-yarn/yarn` 디렉터리 아래에서 YARN 리소스 관리자 로그를 확인하고 수집합니다.
    1. 이러한 단계를 수행해도 문제가 해결되지 않으면 Azure HDInsight 팀에 문의하여 지원을 요청하고 위의 로그 및 타임스탬프를 제공하세요.

### <a name="error-code-2303"></a>오류 코드: 2303

- **메시지**: `Hadoop job failed with transient exit code '%exitCode;'. See '%logPath;/stderr' for more details. Alternatively, open the Ambari UI on the HDI cluster and find the logs for the job '%jobId;'. Try again or contact HDInsight team for further support.`

- **원인**: 작업이 HDI 클러스터에 제출되었지만 작업이 실패했습니다.

- **권장 사항**: 

 1. 다음과 같이 Ambari UI를 확인하세요.
    1. 모든 서비스가 계속 실행 중인지 확인합니다.
    1. Ambari UI에서 대시보드의 경고 섹션을 확인합니다.
       1. 경고 및 경고 관련 해결 방법에 대한 자세한 내용은 [클러스터 관리 및 모니터링](https://docs.cloudera.com/HDPDocuments/Ambari-2.7.5.0/managing-and-monitoring-ambari/content/amb_predefined_alerts.html)을 참조하세요.
    1. YARN 메모리를 검토합니다. YARN 메모리가 높으면 작업 처리가 지연될 수 있습니다. Spark 애플리케이션/작업을 수용할 리소스가 부족한 경우 클러스터에 메모리와 코어가 충분하도록 클러스터를 확장합니다. 
 1. 샘플 테스트 작업을 실행합니다.
    1. HDInsight 백엔드에서 동일한 작업을 실행하는 경우 작업이 성공적으로 수행되었는지 확인하세요. 샘플 실행에 대한 예제를 보려면 [ MapReduce 예제 실행](../hdinsight/hadoop/apache-hadoop-run-samples-linux.md)을 참조하세요. 
 1. HDInsight에서 작업이 계속 실패하면 지원 팀에 제공할 응용 프로그램 로그 및 정보를 확인하세요.
    1. 작업이 YARN에 제출되었는지 확인합니다. 작업이 YARN에 제출되지 않은 경우 `--master yarn`을 사용합니다.
    1. 응용 프로그램의 실행이 완료되면 YARN 응용 프로그램의 시작 시간 및 종료 시간을 수집합니다. 응용 프로그램이 실행을 완료하지 못한 경우 시작 시간/실행 시간을 수집합니다.
    1. `yarn logs -applicationId <Insert_Your_Application_ID>`로 응용 프로그램 로그를 확인 및 수집합니다.
    1. `/var/log/hadoop-yarn/yarn` 디렉터리 아래에서 YARN 리소스 관리자 로그를 확인하고 수집합니다.
    1. 이러한 단계를 수행해도 문제가 해결되지 않으면 Azure HDInsight 팀에 문의하여 지원을 요청하고 위의 로그 및 타임스탬프를 제공하세요.

### <a name="error-code-2304"></a>오류 코드: 2304

- **메시지**: `MSI authentication is not supported on storages for HDI activities.`

- **원인**: HDI(HDInsight) 연결 서비스 또는 HDI 활동에 사용된 저장소 연결 서비스는 지원되지 않는 MSI 인증으로 구성됩니다.

- **권장 사항**: HDI 연결 서비스 또는 HDI 활동에 사용되는 저장소 계정에 대한 전체 연결 문자열을 제공하세요.

### <a name="error-code-2305"></a>오류 코드: 2305

- **메시지**: `Failed to initialize the HDInsight client for the cluster '%cluster;'. Error: '%message;'`

- **원인**: HDI 클러스터에 대한 연결 정보가 잘못되었거나, 제공된 사용자에게 필요한 작업을 수행할 수 있는 권한이 없거나, HDInsight 서비스에서 서비스의 요청에 응답하는 동안 문제가 발생했습니다.

- **권장 사항**: 사용자 정보가 올바른지 확인하고, HDI 클러스터의 Ambari UI가 IR이 설치된 VM(자체 호스팅 IR)의 브라우저에서 열리거나 어떤 컴퓨터(Azure IR)에서든 열 수 있는지 확인하세요.

### <a name="error-code-2306"></a>오류 코드: 2306

- **메시지**: `An invalid json is provided for script action '%scriptActionName;'. Error: '%message;'`

- **원인**: 스크립트 작업에 제공된 JSON이 유효하지 않습니다.

- **권장 사항**: 오류 메시지는 문제를 파악하는 데 도움이 됩니다. Json 구성을 수정하고 다시 시도하세요.

   자세한 내용은 [Azure HDInsight 주문형 연결 서비스](./compute-linked-services.md#azure-hdinsight-on-demand-linked-service)를 확인하세요.

### <a name="error-code-2310"></a>오류 코드: 2310

- **메시지**: `Failed to submit Spark job. Error: '%message;'`

- **원인**: 서비스가 Livy API(Livy/일괄 처리)를 사용하여 Spark 클러스터에서 일괄 처리를 만들려고 했지만 오류가 발생했습니다.

- **권장 사항**: 오류 메시지에 따라 문제를 해결하세요. 해결하는 데 정보가 부족한 경우 HDI 팀에 문의하여 일괄 처리 ID 및 작업 ID를 제공하세요. 이 ID는 서비스 모니터링의 활동 실행 출력 페이지에서 찾을 수 있습니다. 문제를 추가로 해결하려면 일괄 처리 작업의 전체 로그를 수집하세요.

   전체 로그를 수집하는 방법에 대한 자세한 내용은 [일괄 처리 작업의 전체 로그 가져오기](/rest/api/hdinsightspark/hdinsight-spark-batch-job#get-the-full-log-of-a-batch-job)를 참조하세요.

### <a name="error-code-2312"></a>오류 코드: 2312

- **메시지**: `Spark job failed, batch id:%batchId;. Please follow the links in the activity run Output from the service Monitoring page to troubleshoot the run on HDInsight Spark cluster. Please contact HDInsight support team for further assistance.`

- **원인**: HDInsight Spark 클러스터에서 작업이 실패했습니다.

- **권장 사항**: 서비스 모니터링의 활동 실행 출력 페이지 링크를 따라 HDInsight Spark 클러스터에서 실행 문제를 해결할 수 있습니다. 추가 지원이 필요한 경우 HDInsight 지원 팀에 문의하세요.

   전체 로그를 수집하는 방법에 대한 자세한 내용은 [일괄 처리 작업의 전체 로그 가져오기](/rest/api/hdinsightspark/hdinsight-spark-batch-job#get-the-full-log-of-a-batch-job)를 참조하세요.

### <a name="error-code-2313"></a>오류 코드: 2313

- **메시지**: `The batch with ID '%batchId;' was not found on Spark cluster. Open the Spark History UI and try to find it there. Contact HDInsight support for further assistance.`

- **원인**: HDInsight Spark 클러스터에서 일괄 처리가 삭제되었습니다.

- **권장 사항**: HDInsight Spark 클러스터에서 일괄 처리 문제를 해결하세요. 추가 지원이 필요한 경우 HDInsight 지원 팀에 문의하세요. 

   전체 로그를 수집하는 방법에 대한 자세한 내용은 [일괄 처리 작업의 전체 로그 가져오기](/rest/api/hdinsightspark/hdinsight-spark-batch-job#get-the-full-log-of-a-batch-job)를 참조하고, 추가 지원이 필요한 경우 HDInsight 지원 팀에 전체 로그를 공유하세요.

### <a name="error-code-2328"></a>오류 코드: 2328

- **메시지**: `Failed to create the on demand HDI cluster. Cluster or linked service name: '%clusterName;', error: '%message;'`

- **원인**: 발생한 문제에 대한 세부 정보가 오류 메시지에 표시됩니다.

- **권장 사항**: 오류 메시지는 문제를 해결하는 데 도움이 됩니다.

### <a name="error-code-2329"></a>오류 코드: 2329

- **메시지**: `Failed to delete the on demand HDI cluster. Cluster or linked service name: '%clusterName;', error: '%message;'`

- **원인**: 발생한 문제에 대한 세부 정보가 오류 메시지에 표시됩니다.

- **권장 사항**: 오류 메시지는 문제를 해결하는 데 도움이 됩니다.

### <a name="error-code-2331"></a>오류 코드: 2331

- **메시지**: `The file path should not be null or empty.`

- **원인**: 제공된 파일 경로가 비어 있습니다.

- **권장 사항**: 존재하는 파일 경로를 제공하세요.

### <a name="error-code-2340"></a>오류 코드: 2340

- **메시지**: `HDInsightOnDemand linked service does not support execution via SelfHosted IR. Your IR name is '%IRName;'. Please select an Azure IR instead.`

- **원인**: HDInsight 주문형 연결 서비스는 자체 호스팅 IR을 통한 실행을 지원하지 않습니다.

- **권장 사항**: Azure IR을 선택하고 다시 시도하세요.

### <a name="error-code-2341"></a>오류 코드: 2341

- **메시지**: `HDInsight cluster URL '%clusterUrl;' is incorrect, it must be in URI format and the scheme must be 'https'.`

- **원인**: 제공된 URL의 형식이 올바르지 않습니다.

- **권장 사항**: 클러스터 URL을 수정하고 다시 시도하세요.

### <a name="error-code-2342"></a>오류 코드: 2342

- **메시지**: `Failed to connect to HDInsight cluster: '%errorMessage;'.`

- **원인**: 클러스터에 제공된 자격 증명이 잘못되었거나, 네트워크 구성 또는 연결 문제가 있거나, IR에 클러스터 연결 문제가 있습니다.

- **권장 사항**: 
    1. 브라우저에서 HDInsight 클러스터의 Ambari UI를 열어 자격 증명이 올바른지 확인하세요.
    1. 클러스터가 VNet(가상 네트워크)에 있고 자체 호스팅 IR을 사용 중인 경우 HDI URL은 VNet의 프라이빗 URL이어야 하며 클러스터 이름 뒤에 `-int`가 있어야 합니다.
    
       예를 들어 `https://mycluster.azurehdinsight.net/`를 `https://mycluster-int.azurehdinsight.net/`로 변경합니다. `-int`는 `mycluster` 뒤, `.azurehdinsight.net` 앞이어야 합니다.
    1. 클러스터가 VNet에 있고 자체 호스팅 IR을 사용 중이며 비공개 URL이 사용되었지만 연결에 계속 실패하는 경우에는 IR이 설치된 VM이 HDI에 연결하는 데 문제가 있는 것입니다. 
    
       IR이 설치된 VM에 연결하고 브라우저에서 Ambari UI를 엽니다. 클러스터에 비공개 URL을 사용합니다. 이 연결은 브라우저에서 작동해야 합니다. 작동하지 않는 경우 HDInsight 지원 팀에 문의하여 추가 지원을 요청하세요.
    1. 자체 호스팅 IR을 사용하지 않는 경우에는 HDI 클러스터에 공개적으로 액세스할 수 있어야 합니다. 브라우저에서 Ambari UI를 열고 열리는지 확인합니다. 클러스터 또는 서비스에 문제가 있는 경우 HDInsight 지원 팀에 문의하여 도움을 요청하세요.

       연결 서비스에 사용된 HDI 클러스터 URL은 테스트 연결이 통과되고 실행이 작동하도록 IR(자체 호스팅 또는 Azure)에서 액세스할 수 있어야 합니다. 이 상태는 VM 또는 모든 공용 컴퓨터의 브라우저에서 URL을 열어 확인할 수 있습니다.

### <a name="error-code-2343"></a>오류 코드: 2343

- **메시지**: `User name and password cannot be null or empty to connect to the HDInsight cluster.`

- **원인**: 사용자 이름 또는 암호가 비어 있습니다.

- **권장 사항**: HDI에 연결하는 데 올바른 자격 증명을 제공하고 다시 시도 하세요.

### <a name="error-code-2345"></a>오류 코드: 2345

- **메시지**: `Failed to read the content of the hive script. Error: '%message;'`

- **원인**: 스크립트 파일이 없거나 서비스가 스크립트 위치에 연결할 수 없습니다.

- **권장 사항**: 스크립트가 존재하고 연관된 연결 서비스가 연결에 적합한 자격 증명을 가지고 있는지 확인하세요.

### <a name="error-code-2346"></a>오류 코드: 2346

- **메시지**: `Failed to create ODBC connection to the HDI cluster with error message '%message;'.`

- **원인**: 서비스가 HDI 클러스터에 대한 ODBC(Open Database Connectivity) 연결을 설정하려고 했지만 오류로 인해 실패했습니다.

- **권장 사항**: 

   1. ODBC/JDBC(Java Database Connectivity) 연결을 올바르게 설정했는지 확인하세요.
      1. JDBC의 경우 동일한 가상 네트워크를 사용한다면 다음을 통해 이 연결을 가져올 수 있습니다.<br>
        `Hive -> Summary -> HIVESERVER2 JDBC URL`
      1. JDBC를 올바르게 설정하려면 [HDInsight에서 JDBC 드라이버를 통해 Apache Hive 쿼리](../hdinsight/hadoop/apache-hadoop-connect-hive-jdbc-driver.md)를 참조하세요.
      1. ODB(Open Database)에 대한 자세한 내용은 다음 [자습서를 참조하세요. 설정이 올바른지 확인하려면 ODBC 및 PowerShell을 사용하여 Apache Hive를 쿼리](../hdinsight/interactive-query/apache-hive-query-odbc-driver-powershell.md)하세요. 
   1. Hiveserver2, Hive Metastore 및 Hiveserver2 Interactive가 활성 상태이고 작동되는지 확인합니다. 
   1. Ambari UI(사용자 인터페이스)를 확인합니다.
      1. 모든 서비스가 계속 실행 중인지 확인합니다.
      1. Ambari UI에서 대시보드의 경고 섹션을 확인합니다.
         1. 경고 및 경고 관련 해결 방법에 대한 자세한 내용은 [클러스터 관리 및 모니터링](https://docs.cloudera.com/HDPDocuments/Ambari-2.7.5.0/managing-and-monitoring-ambari/content/amb_predefined_alerts.html)을 참조하세요.
   1. 이러한 단계를 수행해도 문제를 해결할 수 없는 경우 Azure HDInsight 팀에 문의하세요.

### <a name="error-code-2347"></a>오류 코드: 2347

- **메시지**: `Hive execution through ODBC failed with error message '%message;'.`

- **원인**: 서비스는 ODBC 연결을 통해 HDI 클러스터에 실행할 하이브 스크립트를 제출했지만 HDI에서 스크립트가 실패했습니다.

- **권장 사항**: 

   1. ODBC/JDBC(Java Database Connectivity) 연결을 올바르게 설정했는지 확인하세요.
      1. JDBC의 경우 동일한 가상 네트워크를 사용한다면 다음을 통해 이 연결을 가져올 수 있습니다.<br>
        `Hive -> Summary -> HIVESERVER2 JDBC URL`
      1. JDBC를 올바르게 설정하려면 [HDInsight에서 JDBC 드라이버를 통해 Apache Hive 쿼리](../hdinsight/hadoop/apache-hadoop-connect-hive-jdbc-driver.md)를 참조하세요.
      1. ODB(Open Database)에 대한 자세한 내용은 다음 [자습서를 참조하세요. 설정이 올바른지 확인하려면 ODBC 및 PowerShell을 사용하여 Apache Hive를 쿼리](../hdinsight/interactive-query/apache-hive-query-odbc-driver-powershell.md)하세요. 
   1. Hiveserver2, Hive Metastore 및 Hiveserver2 Interactive가 활성 상태이고 작동되는지 확인합니다. 
   1. Ambari UI(사용자 인터페이스)를 확인합니다.
      1. 모든 서비스가 계속 실행 중인지 확인합니다.
      1. Ambari UI에서 대시보드의 경고 섹션을 확인합니다.
         1. 경고 및 경고 관련 해결 방법에 대한 자세한 내용은 [클러스터 관리 및 모니터링](https://docs.cloudera.com/HDPDocuments/Ambari-2.7.5.0/managing-and-monitoring-ambari/content/amb_predefined_alerts.html)을 참조하세요.
   1. 이러한 단계를 수행해도 문제를 해결할 수 없는 경우 Azure HDInsight 팀에 문의하세요.

### <a name="error-code-2348"></a>오류 코드: 2348

- **메시지**: `The main storage has not been initialized. Please check the properties of the storage linked service in the HDI linked service.`

- **원인**: 저장소 연결 서비스 속성이 올바르게 설정되지 않았습니다.

- **권장 사항**: HDI 활동에 대한 기본 저장소 연결 서비스에서는 전체 연결 문자열만 지원됩니다. MSI 권한 부여 또는 응용 프로그램을 사용하고 있지 않은지 확인합니다.

### <a name="error-code-2350"></a>오류 코드: 2350

- **메시지**: `Failed to prepare the files for the run '%jobId;'. HDI cluster: '%cluster;', Error: '%errorMessage;'`

- **원인**: 파일을 배치해야 할 저장소에 연결하기 위해 제공한 자격 증명이 올바르지 않거나 해당 파일이 존재하지 않습니다.

- **권장 사항**: 이 오류는 서비스가 HDI 활동을 준비하고, HDI에 작업을 제출하기 전에 기본 스토리지에 파일을 복사하려고 하는 경우에 발생합니다. 파일이 제공된 위치에 있고 저장소 연결이 올바른지 확인하세요. HDI 활동은 HDI 활동과 관련된 스토리지 계정에서 MSI 인증을 지원하지 않으므로 연결된 서비스에 전체 키가 있거나 Azure Key Vault를 사용하는지 확인하세요.

### <a name="error-code-2351"></a>오류 코드: 2351

- **메시지**: `Could not open the file '%filePath;' in container/fileSystem '%container;'.`

- **원인**: 파일이 지정된 경로에 없습니다.

- **권장 사항**: 파일이 실제로 존재하는지, 연결 정보가 이 파일을 가리키는 연결 서비스에 올바른 자격 증명이 있는지 확인하세요.

### <a name="error-code-2352"></a>오류 코드: 2352

- **메시지**: `The file storage has not been initialized. Please check the properties of the file storage linked service in the HDI activity.`

- **원인**: 파일 저장소 연결 서비스 속성이 올바르게 설정되지 않았습니다.

- **권장 사항**: 파일 저장소 연결 서비스의 속성이 제대로 구성되어 있는지 확인하세요.

### <a name="error-code-2353"></a>오류 코드: 2353

- **메시지**: `The script storage has not been initialized. Please check the properties of the script storage linked service in the HDI activity.`

- **원인**: 스크립트 저장소 연결 서비스 속성이 올바르게 설정되지 않았습니다.

- **권장 사항**: 스크립트 저장소 연결 서비스의 속성이 제대로 구성되어 있는지 확인하세요.

### <a name="error-code-2354"></a>오류 코드: 2354

- **메시지**: `The storage linked service type '%linkedServiceType;' is not supported for '%executorType;' activities for property '%linkedServicePropertyName;'.`

- **원인**: 저장소 연결 서비스 유형은 활동에서 지원되지 않습니다.

- **권장 사항**: 선택한 연결 서비스에 지원되는 활동 유형 중 하나가 있는지 확인하세요. HDI 활동은 AzureBlobStorage 및 AzureBlobFSStorage 연결 서비스를 지원합니다.

   자세한 내용은 [Azure HDInsight 클러스터에 사용할 저장소 옵션 비교](../hdinsight/hdinsight-hadoop-compare-storage-options.md)를 참조하세요.

### <a name="error-code-2355"></a>오류 코드: 2355

- **메시지**: `The '%value' provided for commandEnvironment is incorrect. The expected value should be an array of strings where each string has the format CmdEnvVarName=CmdEnvVarValue.`

- **원인**: `commandEnvironment`에 제공된 값이 잘못되었습니다.

- **권장 사항**: 제공된 값이 다음과 유사한지 확인하세요.
 
    ``` \"commandEnvironment\": [
    \"variableName=variableValue\"
    ]
    ```

    또한 각 변수가 목록에 한 번만 표시되는지 확인합니다.

### <a name="error-code-2356"></a>오류 코드: 2356

- **메시지**: `The commandEnvironment already contains a variable named '%variableName;'.`

- **원인**: `commandEnvironment`에 제공된 값이 잘못되었습니다.

- **권장 사항**: 제공된 값이 다음과 유사한지 확인하세요.
 
    ``` \"commandEnvironment\": [
    \"variableName=variableValue\"
    ]
    ```

    또한 각 변수가 목록에 한 번만 표시되는지 확인합니다.

### <a name="error-code-2357"></a>오류 코드: 2357

- **메시지**: `The certificate or password is wrong for ADLS Gen 1 storage.`

- **원인**: 제공된 자격 증명이 잘못되었습니다.

- **권장 사항**: ADLS Gen 1의 연결 정보가 서비스에 연결되어 있는지 확인하고 연결 테스트에 성공했는지 확인하세요.

### <a name="error-code-2358"></a>오류 코드: 2358

- **메시지**: `The value '%value;' for the required property 'TimeToLive' in the on demand HDInsight linked service '%linkedServiceName;' has invalid format. It should be a timespan between '00:05:00' and '24:00:00'.`

- **원인**: 필수 속성 `TimeToLive`에 제공된 값의 형식이 유효하지 않습니다. 

- **권장 사항**: 값을 제안된 범위로 업데이트한 후 다시 시도하세요.

### <a name="error-code-2359"></a>오류 코드: 2359

- **메시지**: `The value '%value;' for the property 'roles' is invalid. Expected types are 'zookeeper', 'headnode', and 'workernode'.`

- **원인**: 속성 `roles`에 제공된 값이 유효하지 않습니다.

- **권장 사항**: 제안 중 하나로 값을 업데이트하고 다시 시도하세요.

### <a name="error-code-2360"></a>오류 코드: 2360

- **메시지**: `The connection string in HCatalogLinkedService is invalid. Encountered an error while trying to parse: '%message;'.`

- **원인**: `HCatalogLinkedService`에 제공된 연결 문자열이 유효하지 않습니다.

- **권장 사항**: 올바른 Azure SQL 연결 문자열로 값을 업데이트하고 다시 시도하세요.

### <a name="error-code-2361"></a>오류 코드: 2361

- **메시지**: `Failed to create on demand HDI cluster. Cluster name is '%clusterName;'.`

- **원인**: 클러스터를 만들지 못했으며 서비스가 HDInsight 서비스에서 오류를 가져오지 못했습니다.

- **권장 사항**: Azure Portal을 열고 제공된 이름으로 HDI 리소스를 찾은 다음, 프로비저닝 상태를 확인하세요. 추가 지원이 필요한 경우 HDInsight 지원 팀에 문의하세요.

### <a name="error-code-2362"></a>오류 코드: 2362

- **메시지**: `Only Azure Blob storage accounts are supported as additional storages for HDInsight on demand linked service.`

- **원인**: 제공된 추가 저장소가 Azure Blob Storage가 아닙니다.

- **권장 사항**: HDInsight 주문형 연결 서비스에 대한 추가 저장소로 Azure Blob Storage 계정을 제공하세요.

### <a name="ssl-error-when-linked-service-using-hdinsight-esp-cluster"></a>HDInsight ESP 클러스터를 사용하여 연결된 서비스에 대한 SSL 오류

- **메시지**: `Failed to connect to HDInsight cluster: 'ERROR [HY000] [Microsoft][DriverSupport] (1100) SSL certificate verification failed because the certificate is missing or incorrect.`

- **원인**:이 문제는 System Trust Store와 관련이 있을 수 있습니다.

- **해결 방법**: **Microsoft Integration Runtime\4.0\Shared\ODBC Drivers\Microsoft Hive ODBC Driver\lib** 경로로 이동해 DriverConfiguration64.exe를 열어 설정을 변경할 수 있습니다.

    :::image type="content" source="./media/connector-troubleshoot-guide/system-trust-store-setting.png" alt-text="System Trust Store 사용 선택 취소":::

## <a name="web-activity"></a>웹 작업

### <a name="error-code-2128"></a>오류 코드: 2128

- **메시지**: `No response from the endpoint. Possible causes: network connectivity, DNS failure, server certificate validation or timeout.`

- **원인**: 이 문제는 네트워크 연결, DNS 오류, 서버 인증서 유효성 검사 또는 시간 초과로 인해 발생합니다.

- **권장 사항**: 실행하려는 끝점이 요청에 응답하는지 확인하세요. **Fiddler/Postman/Netmon/Wireshark** 와 같은 도구를 사용할 수 있습니다.

### <a name="error-code-2108"></a>오류 코드: 2108

- **메시지**: `Error calling the endpoint '%url;'. Response status code: '%code;'`

- **원인**: 네트워크 연결, DNS 오류, 서버 인증서 유효성 검사 또는 시간 초과와 같은 기본 문제로 인해 요청이 실패했습니다.

- **권장 사항**: Fiddler/Postman/Netmon/Wireshark를 사용하여 요청의 유효성을 검사합니다.

#### <a name="more-details"></a>자세한 내용
**Fiddler** 를 사용하여 모니터링되는 웹 응용 프로그램의 HTTP 세션을 만들려면 다음을 수행하세요.

1. [Fiddler](https://www.telerik.com/download/fiddler)를 다운로드하여 설치하고 엽니다.

1. 웹 응용 프로그램에서 HTTPS를 사용하는 경우 **도구** > **Fiddler 옵션** > **HTTPS** 로 이동합니다.

   1. HTTPS 탭에서 **HTTPS 연결 캡처** 및 **HTTPS 트래픽 암호 해독** 을 모두 선택합니다.

      :::image type="content" source="media/data-factory-troubleshoot-guide/fiddler-options.png" alt-text="Fiddler 옵션":::

1. 응용 프로그램에서 TLS/SSL 인증서를 사용하는 경우 Fiddler 인증서를 디바이스에 추가합니다.

   다음으로 이동합니다. **도구** > **Fiddler 옵션** > **HTTPS** > **작업** > **데스크톱으로 루트 인증서 내보내기**

1. **파일** > **트래픽 캡처** 로 이동하여 캡처 설정을 끕니다. 또는 **F12** 키를 누릅니다.

1. 캐시된 모든 항목을 제거하도록 브라우저의 캐시를 지우고 다시 다운로드해야 합니다.

1. 다음과 같이 요청을 생성합니다.

1. **작성기** 탭을 선택합니다.

   1. HTTP 메서드 및 URL을 설정합니다.
 
   1. 필요한 경우 헤더 및 요청 본문을 추가합니다.

   1. **실행** 을 선택합니다.

1. 트래픽 캡처를 다시 켜고 페이지에서 문제 있는 트랜잭션을 완료합니다.

1. 다음으로 이동합니다. **파일** > **저장** > **모든 세션**

자세한 내용은 [Fiddler 시작](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/ConfigureFiddler)을 참조하세요.

## <a name="general"></a>일반

### <a name="activity-stuck-issue"></a>작업 중단 문제

작업이 거의 진행되지 않으며 정상적인 실행보다 훨씬 더 오래 실행되는 현상이 확인되는 경우 문제가 발생했을 수 있습니다. 취소하고 다시 시도한 후 작동 여부를 살피세요. 복사 작업인 경우 [복사 작업 성능 문제 해결](copy-activity-performance-troubleshooting.md)에서 성능 모니터링 및 문제 해결에 대해 알아볼 수 있습니다. 데이터 흐름의 경우 [매핑 데이터 흐름 성능](concepts-data-flow-performance.md) 및 튜닝 가이드에서 알아보세요.

### <a name="payload-is-too-large"></a>페이로드가 너무 큼

**오류 메시지:** `The payload including configurations on activity/dataSet/linked service is too large. Please check if you have settings with very large value and try to reduce its size.`

**원인:** 각 작업 실행에 대한 페이로드에는 활동 구성, 연결된 데이터 세트, 연결된 서비스 구성(있는 경우), 활동 유형별로 생성된 시스템 속성의 일부가 포함됩니다. 해당 페이로드 크기의 한도는 [Data Factory](../azure-resource-manager/management/azure-subscription-service-limits.md#data-factory-limits) 및 [Azure Synapse Analytics](../azure-resource-manager/management/azure-subscription-service-limits.md#azure-synapse-analytics-limits)에 대한 Azure 한도 설명서에 언급된 대로 896KB입니다.

**권장 사항:** 이 제한은 업스트림 활동 출력 또는 외부에서 하나 이상의 큰 매개 변수 값을 전달하는 경우, 특히 제어 흐름의 활동 간에 실제 데이터를 전달하는 경우에 발생할 가능성이 높습니다. 큰 매개 변수 값의 크기를 줄이거나 파이프라인 논리를 튜닝하여 활동 간에 이러한 값을 전달하지 않고 활동 내에서 처리할 수 있는지 확인합니다.

### <a name="unsupported-compression-causes-files-to-be-corrupted"></a>지원 되지 않는 압축으로 인해 파일이 손상 됨

**증상**: blob 컨테이너에 저장 된 파일의 압축을 푸는 것이 좋습니다. 파이프라인의 단일 복사 작업에는 압축 형식이 "deflate64" (또는 지원 되지 않는 형식)로 설정 된 소스가 있습니다. 이 작업은 성공적으로 실행 되며 zip 파일에 포함 된 텍스트 파일을 생성 합니다. 그러나 파일의 텍스트에 문제가 있어이 파일이 손상 된 것으로 나타납니다. 이 파일을 로컬로 압축을 푼 경우에는 괜찮습니다.

**원인**: zip 파일은 "deflate64" 알고리즘에 의해 압축 되며 Azure Data Factory의 내부 zip 라이브러리는 "deflate"만 지원 합니다. zip 파일이 Windows 시스템으로 압축 되 고 전체 파일 크기가 특정 번호를 초과 하는 경우에는 기본적으로 Azure Data Factory에서 지원 되지 않는 "deflate64"을 사용 Windows 합니다. 반면, 파일 크기가 작거나 압축 알고리즘 지정을 지 원하는 일부 타사 zip 도구를 사용 하는 경우 Windows는 기본적으로 "deflate"를 사용 합니다.

> [!TIP]
> 실제로는 [Azure Data Factory 및 Synapse analytics의 이진 형식과](format-binary.md) [Azure Data Factory 및 Azure Synapse Analytics의 분리 된 텍스트 형식이](format-delimited-text.md) 모두 Azure Data Factory에서 "deflate64" 형식이 지원 되지 않는 것을 명확 하 게 명시 합니다.

## <a name="next-steps"></a>다음 단계

문제 해결을 위한 도움이 필요한 경우 다음 리소스를 참조하세요.

* [Data Factory 블로그](https://azure.microsoft.com/blog/tag/azure-data-factory/)
* [Data Factory 기능 요청](/answers/topics/azure-data-factory.html)
* [Data Factory에 대한 Stack Overflow 포럼](https://stackoverflow.com/questions/tagged/azure-data-factory)
* [Data Factory에 대한 Twitter 정보](https://twitter.com/hashtag/DataFactory)
* [Azure 비디오](https://azure.microsoft.com/resources/videos/index/)
* [Microsoft Q&A 질문 페이지](/answers/topics/azure-data-factory.html)
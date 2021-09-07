---
title: ADF에서 CI-CD, Azure DevOps 및 GitHub 문제 해결
titleSuffix: Azure Data Factory & Azure Synapse
description: 여러 방법을 사용하여 ADF에서 CI-CD 문제를 해결할 수 있습니다.
author: ssabat
ms.author: susabat
ms.reviewer: susabat
ms.service: data-factory
ms.subservice: ci-cd
ms.custom: synapse
ms.topic: troubleshooting
ms.date: 06/27/2021
ms.openlocfilehash: 774f1dddf7c0b4cdecd9d6b45e6918b57bc17318
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122642938"
---
# <a name="troubleshoot-ci-cd-azure-devops-and-github-issues-in-adf"></a>ADF에서 CI-CD, Azure DevOps 및 GitHub 문제 해결 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory에서 CI-CD(연속 통합-지속적인 배포), Azure DevOps 및 GitHub 문제에 대한 일반적인 문제 해결 방법을 살펴봅니다.

소스 제어 또는 DevOps 기술 사용에 관한 질문 또는 문제가 있으면 다음 문서에서 유용한 정보를 찾아볼 수 있습니다.

- ADF에서 소스 제어가 수행되는 방법은 [ADF의 소스 제어](source-control.md)를 참조하세요. 
- ADF에서 DevOps CI-CD가 수행되는 방법은 [ADF의 CI-CD](continuous-integration-deployment.md)를 참조하세요.

## <a name="common-errors-and-messages"></a>일반적인 오류 및 메시지

### <a name="connect-to-git-repository-failed-due-to-different-tenant"></a>다른 테넌트로 인해 Git 리포지토리 연결이 실패함

#### <a name="issue"></a>문제

경우에 따라 HTTP 상태 401과 같은 인증 문제가 발생할 수 있습니다. 특히 게스트 계정을 사용하는 여러 테넌트가 있는 경우 상황이 더 복잡해질 수 있습니다.

#### <a name="cause"></a>원인

원래 테넌트에서 토큰을 가져왔지만 ADF가 게스트 테넌트에 있고 이 토큰을 사용하여 게스트 테넌트의 DevOps에 연결하려고 시도하는 중입니다. 이것은 예상된 동작이 아닙니다.

#### <a name="recommendation"></a>권장

게스트 테넌트에서 발급된 토큰을 사용해야 합니다. 예를 들어 토큰 동작을 올바르게 설정하고 올바른 테넌트를 사용할 수 있도록 DevOps는 물론 동일한 Azure Active Directory를 게스트 테넌트로 할당해야 합니다.

### <a name="template-parameters-in-the-parameters-file-are-not-valid"></a>매개변수 파일에 있는 템플릿 매개변수가 올바르지 않음

#### <a name="issue"></a>문제

테스트 또는 프로덕션 분기에서 이미 **동일한** 구성(빈도 및 간격 등)으로 제공되는 트리거를 개발 분기에서 삭제하면 릴리스 파이프라인 배포가 성공하고 해당 트리거가 해당 환경에서 삭제됩니다. 하지만 테스트/프로덕션 환경에서 트리거에 대해 **다른** 구성(빈도 및 간격 등)이 있고 개발 환경에서 동일한 트리거를 삭제하면 해당 배포가 오류와 함께 실패합니다.

#### <a name="cause"></a>원인

CI/CD 파이프라인이 다음 오류와 함께 실패합니다.

`
2020-07-20T11:19:02.1276769Z ##[error]Deployment template validation failed: 'The template parameters 'Trigger_Salesforce_properties_typeProperties_recurrence_frequency, Trigger_Salesforce_properties_typeProperties_recurrence_interval, Trigger_Salesforce_properties_typeProperties_recurrence_startTime, Trigger_Salesforce_properties_typeProperties_recurrence_timeZone' in the parameters file are not valid; they are not present in the original template and can therefore not be provided at deployment time. The only supported parameters for this template are 'factoryName, PlanonDWH_connectionString, PlanonKeyVault_properties_typeProperties_baseUrl
`

#### <a name="recommendation"></a>권장

이 오류는 매개변수화된 트리거를 자주 삭제하기 때문에 발생합니다. 따라서 트리거가 더 이상 존재하지 않기 때문에 ARM(Azure Resource Manager) 템플릿에서 해당 매개변수를 사용할 수 없게 됩니다. 매개변수가 ARM 템플릿에 더 이상 존재하지 않기 때문에 DevOps 파이프라인에서 재정의된 매개변수를 업데이트해야 합니다. 그렇지 않으면 ARM 템플릿에서 매개변수가 변경될 때마다 DevOps 파이프라인(배포 작업)에서 재정의된 매개변수를 업데이트해야 합니다.

### <a name="updating-property-type-is-not-supported"></a>속성 유형 업데이트가 지원되지 않음

#### <a name="issue"></a>문제

다음 오류와 함께 CI/CD 릴리스 파이프라인이 실패합니다.

```output
2020-07-06T09:50:50.8716614Z There were errors in your deployment. Error code: DeploymentFailed.
2020-07-06T09:50:50.8760242Z ##[error]At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.
2020-07-06T09:50:50.8771655Z ##[error]Details:
2020-07-06T09:50:50.8772837Z ##[error]DataFactoryPropertyUpdateNotSupported: Updating property type is not supported.
2020-07-06T09:50:50.8774148Z ##[error]DataFactoryPropertyUpdateNotSupported: Updating property type is not supported.
2020-07-06T09:50:50.8775530Z ##[error]Check out the troubleshooting guide to see if your issue is addressed: https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment#troubleshooting
2020-07-06T09:50:50.8776801Z ##[error]Task failed while creating or updating the template deployment.
```

#### <a name="cause"></a>원인

이 오류는 대상 팩터리에 있는 이름이 동일하지만 유형이 다른 통합 런타임 때문입니다. Integration Runtime은 배포 동안 동일한 유형이어야 합니다.

#### <a name="recommendation"></a>권장

- [CI/CD에 대한 모범 사례](continuous-integration-deployment.md#best-practices-for-cicd)를 참조하세요.

- 통합 런타임은 자주 변경되지 않으며 CI/CD의 모든 스테이지에서 비슷합니다. 따라서 Data Factory는 모든 CI/CD 스테이지에서 통합 런타임의 이름과 유형이 동일할 것으로 예상합니다. 이름과 유형 및 속성이 다르면 소스 및 대상 통합 런타임 구성이 일치하는지 확인한 후 릴리스 파이프라인을 배포합니다.

- 모든 단계에서 통합 런타임을 공유하려면 공유 통합 런타임을 포함하기 위해 3개로 구성된 팩터리를 사용하는 것이 좋습니다. 모든 환경에서 이 공유 팩터리를 연결된 통합 런타임 형식으로 사용할 수 있습니다.

### <a name="document-creation-or-update-failed-because-of-invalid-reference"></a>잘못된 참조로 인해 문서 만들기 또는 업데이트가 실패함

#### <a name="issue"></a>문제

Data Factory에 변경 사항을 게시하려고 시도할 때 다음 오류 메시지가 표시됩니다.

`
"error": {
        "code": "BadRequest",
        "message": "The document creation or update failed because of invalid reference '<entity>'.",
        "target": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/<rgname>/providers/Microsoft.DataFactory/factories/<datafactory>/pipelines/<pipeline>",
        "details": null
    }
`
### <a name="cause"></a>원인

Git 구성을 분리하고 “리소스 가져오기” 플래그가 선택된 상태로 다시 설정하여 Data Factory가 “동기화 상태”로 설정되었습니다. 즉, 게시하는 동안 변경되지 않습니다.

#### <a name="resolution"></a>해결 방법

Git 구성을 분리하고 다시 설정하고, “기존 리소스 가져오기” 확인란을 선택하지 않아야 합니다.

### <a name="data-factory-move-failing-from-one-resource-group-to-another"></a>한 리소스 그룹에서 다른 리소스로 Data Factory 이동 실패

#### <a name="issue"></a>문제

Data Factory를 한 리소스 그룹에서 다른 그룹으로 이동할 수 없고 다음 오류가 발생합니다.

`
{
    "code": "ResourceMoveProviderValidationFailed",
    "message": "Resource move validation failed. Please see details. Diagnostic information: timestamp 'xxxxxxxxxxxxZ', subscription id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', tracking id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', request correlation id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'.",
    "details": [
        {
            "code": "BadRequest",
            "target": "Microsoft.DataFactory/factories",
            "message": "One of the resources contain integration runtimes that are either SSIS-IRs in starting/started/stopping state, or Self-Hosted IRs which are shared with other resources. Resource move is not supported for those resources."
        }
    ]
}
`

#### <a name="resolution"></a>해결 방법

이동 작업을 수행하기 위해서는 SSIS-IR 및 공유 IR을 삭제할 수 있습니다. 통합 런타임을 삭제하지 않으려는 경우 최선의 방법은 복사 및 클론 문서에 따라 복사를 수행하고 완료되면 이전 Data Factory를 삭제하는 것입니다.

###  <a name="unable-to-export-and-import-arm-template"></a>ARM 템플릿을 내보내고 가져올 수 없음

#### <a name="issue"></a>문제

ARM 템플릿을 내보내고 가져올 수 없습니다. 포털에 오류가 없지만 브라우저 추적에서 다음 오류가 표시될 수 있습니다.

`Failed to load resource: the server responded with a status code of 401 (Unauthorized)`

#### <a name="cause"></a>원인

고객 역할을 사용자로 만들었지만 필요한 권한이 없습니다. UI에서 팩터리가 로드되면 팩터리에 대해 일련의 노출 제어 값이 확인됩니다. 여기에서는 사용자의 액세스 역할에 *queryFeaturesValue* API 액세스 권한이 없습니다. 이 API에 액세스하기 위해 전역 매개변수 기능이 해제되어 있습니다. ARM 코드 내보내기 경로에 부분적으로 전역 매개변수 기능이 사용됩니다.

#### <a name="resolution"></a>해결 방법

이 문제를 해결하려면 역할에 다음 권한을 추가해야 합니다. *Microsoft.DataFactory/factories/queryFeaturesValue/action*. 이 권한은 기본적으로 "Data Factory 기여자" 역할에 포함되어야 합니다.

###  <a name="cannot-automate-publishing-for-cicd"></a>CI/CD에 대해 게시를 자동화할 수 없음 

#### <a name="cause"></a>원인

최근까지 배포에 대해 ADF 파이프라인을 게시하는 유일한 방법은 ADF 포털 단추 클릭을 사용하는 것이었습니다. 이제는 이 프로세스를 자동화할 수 있습니다. 

#### <a name="resolution"></a>해결 방법

CI/CD 프로세스가 향상되었습니다. **자동화된 게시** 기능은 ADF UX에서 모든 ARM 템플릿 기능을 가져오고, 검증하고 내보냅니다. 이렇게 해서 공개적으로 사용 가능한 npm 패키지 [@microsoft/azure-data-factory-utilities](https://www.npmjs.com/package/@microsoft/azure-data-factory-utilities)를 통해 논리를 사용할 수 있게 만듭니다. 따라서 이 방법을 사용하면 ADF UI로 이동하여 단추 클릭을 수행할 필요 없이 이러한 작업을 프로그래밍 방식으로 트리거할 수 있습니다. 이 방법은 CI/CD 파이프라인에 **진정한** 연속 통합 환경을 제공합니다. 자세한 내용은 [ADF CI/CD 게시 향상](./continuous-integration-deployment-improvements.md)을 참조하세요. 

###  <a name="cannot-publish-because-of-4-mb-arm-template-limit"></a>4MB ARM 템플릿 제한으로 인해 게시할 수 없음  

#### <a name="issue"></a>문제

Azure Resource Manager의 4MB 총 템플릿 크기 제한에 도달하여 배포할 수 없습니다. 제한을 초과한 다음, 배포할 솔루션이 필요합니다. 

#### <a name="cause"></a>원인

Azure Resource Manager는 템플릿 크기를 4MB로 제한합니다. 템플릿의 크기는 4MB로, 각 매개 변수 파일의 크기는 64KB로 제한됩니다. 4MB 제한은 반복 리소스 정의로 확장된 다음 템플릿의 최종 상태에 적용되며, 변수 및 매개 변수의 값에 적용됩니다. 하지만 이 제한이 초과되었습니다. 

#### <a name="resolution"></a>해결 방법

중소기업에게는 단일 템플릿이 더 간편하게 이해하고 유지 관리할 수 있습니다. 모든 리소스 및 값을 단일 파일에서 볼 수 있습니다. 고급 시나리오의 경우 연결된 템플릿을 사용하여 솔루션을 대상 구성 요소로 분할할 수 있습니다. [연결된 템플릿 및 중첩된 템플릿 사용](../azure-resource-manager/templates/linked-templates.md?tabs=azure-powershell)의 모범 사례를 따르세요.

### <a name="cannot-connect-to-git-enterprise"></a>GIT Enterprise에 연결할 수 없음  

##### <a name="issue"></a>문제

권한 문제로 인해 GIT Enterprise에 연결할 수 없습니다. **422 - 처리할 수 없는 엔터티** 와 같은 오류가 표시될 수 있습니다.

#### <a name="cause"></a>원인

* ADF에 Oauth를 구성하지 않았습니다. 
* URL이 잘못 구성되었습니다. repoConfiguration은 [FactoryGitHubConfiguration](/dotnet/api/microsoft.azure.management.datafactory.models.factorygithubconfiguration?view=azure-dotnet&preserve-view=true) 형식이어야 합니다.

#### <a name="resolution"></a>해결 방법 

먼저 ADF에 Oauth 액세스 권한을 부여합니다. 그런 후 올바른 URL을 사용하여 GIT Enterprise에 연결해야 합니다. 구성을 고객 조직으로 설정해야 합니다. 예를 들어, ADF는 처음에 *https://hostname/api/v3/search/repositories?q=user%3&lt;customer credential&gt;....* 을 시도하고 실패합니다. 그런 후 *https://hostname/api/v3/orgs/&lt;org&gt;/&lt; repo&gt;...* 를 시도하고 성공합니다.  
 
### <a name="cannot-recover-from-a-deleted-data-factory"></a>삭제된 데이터 팩터리에서 복구할 수 없음

#### <a name="issue"></a>문제
고객이 데이터 팩터리 또는 데이터 팩터리가 포함된 리소스 그룹을 삭제했습니다. 고객은 삭제된 데이터 팩터리를 복원하는 방법을 알고 싶습니다.

#### <a name="cause"></a>원인

고객이 소스 제어를 구성한 경우에만(DevOps 또는 Git) 데이터 팩터리를 복구할 수 있습니다. 이 작업을 수행하면 모든 최근에 게시된 리소스가 표시되고 게시되지 않은 파이프라인, 데이터 세트 및 연결된 서비스는 복원되지 **않습니다**. 소스 제어가 없으면 서비스에 삭제됨 명령이 수신된 후 인스턴스가 삭제되고 백업이 저장되지 않았기 때문에 백엔드에서 삭제된 데이터 팩터리를 복구하는 것이 불가능합니다.

#### <a name="resolution"></a>해상도

소스 제어가 포함된 삭제된 데이터 팩터리를 복구하려면 아래 단계를 참조하세요.

 * 새 Azure Data Factory를 만듭니다.

 * 동일한 설정을 사용하여 Git를 다시 구성하지만 선택한 리포지토리로 기존 데이터 팩터리 리소스를 가져오고 새 분기를 선택해야 합니다.

 * 변경 사항을 협업 분기로 병합하기 위해 끌어오기 요청을 만들고 게시합니다.

 * 고객이 삭제된 ADF에 자체 호스팅 통합 런타임을 갖고 있으면 새 ADF에서 새 인스턴스를 만들고 새로 얻은 키를 사용해서 온-프레미스 머신/VM에서 인스턴스를 제거하고 다시 설치해야 합니다. IR 설정이 완료되었으면 고객이 새 IR을 가리키도록 연결된 서비스를 변경하고 연결을 테스트해야 합니다. 그렇지 않으면 **잘못된 참조** 오류와 함께 실패합니다.

### <a name="cannot-deploy-to-different-stage-using-automatic-publish-method"></a>자동 게시 방법을 사용하여 다른 스테이지에 배포할 수 없음

#### <a name="issue"></a>문제
고객은 NPM 패키지를 설치하고 Azure DevOps 및 ADF를 사용하여 더 높은 스테이지를 설정하는 등 필요한 모든 단계를 수행했습니다. 그러나 배포는 발생하지 않습니다.

#### <a name="cause"></a>원인

npm 패키지는 다양한 방식으로 사용될 수 있지만 Azure 파이프라인을 통해 주요 이점 중 하나가 사용되고 있습니다. 협업 분기에 대한 각 병합에서는 먼저 모든 코드의 유효성을 검사한 다음 릴리스 파이프라인에서 사용될 수 있는 빌드 아티팩트로 ARM 템플릿을 내보내는 파이프라인을 트리거할 수 있습니다. Starter 파이프라인에서 YAML 파일은 유효하고 완전해야 합니다.


#### <a name="resolution"></a>해상도

package.json 폴더가 유효하지 않으므로 다음 섹션은 유효하지 않습니다.

```
- task: Npm@1
  inputs:
    command: 'custom'
    workingDir: '$(Build.Repository.LocalPath)/<folder-of-the-package.json-file>' #replace with the package.json folder
    customCommand: 'run build validate $(Build.Repository.LocalPath) /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/yourFactoryName'
  displayName: 'Validate'
```
*'run build validate $(Build.Repository.LocalPath)/DataFactory/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/yourFactoryName'* 과 같은 customCommand에 DataFactory가 포함되어 있어야 합니다. 더 높은 스테이지에 대해 생성된 YAML 파일에 필요한 JSON 아티팩트가 있는지 확인합니다.

### <a name="git-repository-or-purview-connection-disconnected"></a>Git 리포지토리 또는 Purview 연결 끊김

#### <a name="issue"></a>문제
Data Factory를 배포할 때 git 리포지토리 또는 purview 연결이 끊어집니다.

#### <a name="cause"></a>원인
전역 매개 변수를 배포하기 위해 **ARM 템플릿에 포함** 을 선택한 경우 팩터리가 ARM 템플릿에 포함됩니다. 따라서 배포 시 다른 팩터리 속성이 제거됩니다.

#### <a name="resolution"></a>해상도
CI/CD의 전역 매개 변수에 설명된 대로 **ARM 템플릿에 포함** 을 선택 취소하고 PowerShell을 통해 전역 매개 변수를 배포합니다. 
 
### <a name="extra--left--displayed-in-published-json-file"></a>게시된 JSON 파일에 불필요한 왼쪽 "["가 표시됨

#### <a name="issue"></a>문제
DevOps를 통해 ADF를 게시할 때 왼쪽 "["가 1개 더 표시됩니다. ADF는 DevOps의 ARMTemplate에 왼쪽 "["를 1개 더 자동으로 추가합니다. JSON 파일에 "[["와 같은 식이 표시됩니다.

#### <a name="cause"></a>원인
[는 ARM에서 예약 문자이므로 "["를 이스케이프하기 위해 추가 [가 자동으로 추가됩니다.

#### <a name="resolution"></a>해상도
이것은 CI/CD에 대한 ADF 게시 프로세스 중에 일반적으로 나타나는 동작입니다.
 
### <a name="perform-cicd-during--progressqueued-stage-of-pipeline-run"></a>파이프라인 실행의 진행 중/대기 중 스테이지에서 **CI/CD** 를 수행합니다.

#### <a name="issue"></a>문제
파이프라인 실행의 진행 중 및 대기 스테이지 중에 CI/CD를 수행하려고 합니다.

#### <a name="cause"></a>원인
파이프라인이 진행 중/대기 중 스테이지인 경우 먼저 파이프라인 및 작업을 모니터링해야 합니다. 그런 다음, 파이프라인이 완료될 때까지 기다리거나 파이프라인 실행을 취소할 수 있습니다. 
 
#### <a name="resolution"></a>해결 방법
**SDK**, **Azure Monitor** 또는 [ADF Monitor](./monitor-visually.md)를 사용하여 파이프라인을 모니터링할 수 있습니다. 그런 다음, [ADF CI/CD 모범 사례](./continuous-integration-deployment.md#best-practices-for-cicd)에 따라 추가 지침을 확인할 수 있습니다. 

### <a name="perform-unit-testing-during-adf-development-and-deployment"></a>ADF 개발 및 배포 동안 **단위 테스트** 를 수행합니다.

#### <a name="issue"></a>문제
ADF 파이프라인을 개발 및 배포하는 동안 단위 테스트를 수행하려고 합니다.

#### <a name="cause"></a>원인
개발 및 배포 주기 동안 파이프라인을 수동으로 또는 자동으로 게시하기 전에 파이프라인에 대해 단위 테스트를 수행할 수 있습니다. 테스트 자동화를 사용하면 반복성을 보장하면서 더 적은 시간 안에 더 많은 테스트를 실행할 수 있습니다. 배포 전에 모든 ADF 파이프라인을 자동으로 다시 테스트하면 회귀 오류를 방지할 수 있습니다. 자동화된 테스트는 CI/CD 소프트웨어 개발 방법의 핵심 구성 요소입니다. Azure Data Factory를 위해 CI/CD 배포 파이프라인에 자동화된 테스트를 포함하면 품질을 크게 향상시킬 수 있습니다. 장기적으로 테스트된 ADF 파이프라인 아티팩트도 재사용되어 비용과 시간을 절약할 수 있습니다.  
 
#### <a name="resolution"></a>해결 방법
고객은 다른 기술 집합을 사용하는 다른 단위 테스트 요구 사항을 가질 수 있으므로 일반적으로 다음 단계를 따라야 합니다.

1. Azure DevOps CI/CD 프로젝트를 설정하거나 .NET/PYTHON/REST 유형 SDK 기반 테스트 전략을 개발합니다.
2. CI/CD의 경우 모든 스크립트를 포함하는 빌드 아티팩트를 만들고 릴리스 파이프라인에 리소스를 배포합니다. SDK 기반 접근 방식의 경우 Python, .NET SDK를 사용하는 C# **Nunit** 등에서 PyTest를 사용하여 테스트 단위를 개발합니다.
3. 릴리스 파이프라인의 일부로 또는 ADF Python/PowerShell/.NET/REST SDK를 통해 독립적으로 단위 테스트를 실행합니다. 

예를 들어, 파일에서 중복 항목을 삭제한 다음, 큐레이팅된 파일을 데이터베이스에 테이블로 저장하려고 합니다. 파이프라인을 테스트하려면 Azure DevOps를 사용하여 CI/CD 프로젝트를 설정합니다.
개발된 파이프라인을 배포하는 테스트 파이프라인 스테이지를 설정합니다. 테이블 데이터가 예상한 데이터인지 확인하기 위해 Python 테스트를 실행하도록 테스트 스테이지를 구성합니다. CI/CD를 사용하지 않는 경우 **Nunit** 를 사용하여 원하는 테스트로 배포된 파이프라인을 트리거할 수 있습니다. 결과에 만족하면 마지막으로 파이프라인을 프로덕션 데이터 팩터리로 게시할 수 있습니다. 


## <a name="next-steps"></a>다음 단계

문제 해결에 대한 도움이 필요한 경우 다음 리소스를 참조하세요.

*  [Data Factory 블로그](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory 기능 요청](https://feedback.azure.com/forums/270578-data-factory)
*  [Azure 비디오](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Data Factory에 대한 스택 오버플로 포럼](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Data Factory에 대한 Twitter 정보](https://twitter.com/hashtag/DataFactory)

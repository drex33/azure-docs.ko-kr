---
title: Dynamics 365, Dataverse (Common Data Service) 및 Dynamics CRM 커넥터 문제 해결
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 및 Azure Synapse Analytics에서 Dynamics 365, Dataverse (Common Data Service) 및 Dynamics CRM 커넥터를 사용 하 여 문제를 해결 하는 방법에 대해 알아봅니다.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 12/02/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: 5748b9e5578583d66871ec4e524d44fdb3fb1651
ms.sourcegitcommit: 9ef0965834870700468c822ddcafc011881fc2d5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/02/2021
ms.locfileid: "133485994"
---
# <a name="troubleshoot-the-dynamics-365-dataverse-common-data-service-and-dynamics-crm-connectors-in-azure-data-factory-and-azure-synapse"></a>Azure Data Factory 및 Azure Synapse에서 Dynamics 365, Dataverse (Common Data Service) 및 Dynamics CRM 커넥터 문제 해결

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

이 문서에서는 Azure Data Factory 및 Azure Synapse에서 Dynamics 365, Dataverse (Common Data Service) 및 Dynamics CRM 커넥터와 관련 된 일반적인 문제를 해결 하기 위한 제안 사항을 제공 합니다.

## <a name="error-code-dynamicscreateserviceclienterror"></a>오류 코드: DynamicsCreateServiceClientError

- **메시지**: `This is a transient issue on Dynamics server side. Try to rerun the pipeline.`

- **원인**: Dynamics 서버 쪽에서 발생한 일시적인 문제입니다.

- **권장 사항**:  파이프라인을 다시 실행합니다. 또다시 실패할 경우 병렬 처리를 줄여 봅니다. 문제가 지속되면 Dynamics 지원으로 문의하세요.


## <a name="missing-columns-when-you-import-a-schema-or-preview-data"></a>스키마를 가져오거나 데이터를 미리 볼 때 열이 누락됨

- **증상**: 스키마를 가져오거나 데이터를 미리 볼 때 열이 누락됩니다. 오류 메시지: `The valid structure information (column name and type) are required for Dynamics source.`

- **원인**: Data Factory 및 Synapse 파이프라인은 처음 10개의 레코드에 값이 없는 열을 표시할 수 없습니다. 따라서 이 문제는 의도적으로 발생하는 문제입니다. 추가한 열이 올바른 형식인지 확인하세요. 

- **권장 사항**: 매핑 탭에서 수동으로 열을 추가 합니다. 자세한 내용은 [명시적 매핑](copy-activity-schema-and-type-mapping.md#explicit-mapping)을 참조 하세요.


## <a name="error-code-dynamicsmissingtargetformultitargetlookupfield"></a>오류 코드: DynamicsMissingTargetForMultiTargetLookupField

- **메시지**: `Cannot find the target column for multi-target lookup field: '%fieldName;'.`

- **원인**: 원본 또는 열 매핑에 대상 열이 없습니다.

- **권장 사항**:  
  1. 원본에 대상 열이 포함되어 있는지 확인합니다. 
  2. 열 매핑에 대상 열을 추가합니다. 싱크 열이 *{fieldName}@EntityReference* 형식인지 확인합니다.


## <a name="error-code-dynamicsinvalidtargetformultitargetlookupfield"></a>오류 코드: DynamicsInvalidTargetForMultiTargetLookupField

- **메시지**: `The provided target: '%targetName;' is not a valid target of field: '%fieldName;'. Valid targets are: '%validTargetNames;'`

- **원인**: 다중 대상 조회 필드의 대상 엔터티로 잘못된 엔터티 이름이 제공되었습니다.

- **권장 사항**: 다중 대상 조회 필드에 유효한 엔터티 이름을 지정합니다.


## <a name="error-code-dynamicsinvalidtypeformultitargetlookupfield"></a>오류 코드: DynamicsInvalidTypeForMultiTargetLookupField

- **메시지**: `The provided target type is not a valid string. Field: '%fieldName;'.`

- **원인**: 대상 열의 값이 문자열이 아닙니다.

- **권장 사항**: 다중 대상 조회 대상 열에 유효한 문자열을 지정합니다.


## <a name="error-code-dynamicsfailedtorequetserver"></a>오류 코드: DynamicsFailedToRequetServer

- **메시지**: `The Dynamics server or the network is experiencing issues. Check network connectivity or check Dynamics server log for more details.`

- **원인**: Dynamics 서버가 불안정하거나 액세스할 수 없습니다. 또는 네트워크에 문제가 있습니다.

- **권장 사항**: 자세한 내용을 알아보려면 네트워크 연결을 확인하거나 Dynamics 서버 로그를 확인합니다. 추가 도움이 필요한 경우 Dynamics 지원으로 문의하세요.


## <a name="error-code-dynamicsfailedtoconnect"></a>오류 코드: DynamicsFailedToConnect 
 
 - **메시지**: `Failed to connect to Dynamics: %message;` 
 
 - **원인 및 권장 사항**: 오류의 원인이 여러 가지일 수 있습니다. 아래 목록에서 가능한 원인 분석과 권장 사항을 확인하세요.

    | 원인 분석                                               | 권장                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | 사용 사례가 다음 세 가지 조건을 **모두** 충족하는 경우 `ERROR REQUESTING ORGS FROM THE DISCOVERY SERVERFCB 'EnableRegionalDisco' is disabled.` 또는 `Unable to Login to Dynamics CRM, message:ERROR REQUESTING Token FROM THE Authentication context - USER intervention required but not permitted by prompt behavior AADSTS50079: Due to a configuration change made by your administrator, or because you moved to a new location, you must enroll in multi-factor authentication to access '00000007-0000-0000-c000-000000000000'`이 표시됩니다. <li>Dynamics 365, Common Data Service 또는 Dynamics CRM에 연결하고 있습니다.</li><li>Office365 인증을 사용하고 있습니다.</li><li>테넌트 및 사용자가 [조건부 액세스](../active-directory/conditional-access/overview.md) 및/또는 다단계 인증을 위해 Azure Active Directory에 구성되어 있습니다(Dataverse 문서는 이 [링크](/powerapps/developer/data-platform/authenticate-office365-deprecation) 참조).</li>  이러한 상황에서는 연결이 2021년 6월 8일 이전에 성공했습니다. 2021년 6월 9일부터 지역 검색 서비스의 사용 중단으로 인해 연결이 실패하기 시작합니다(이 [링크](/power-platform/important-changes-coming#regional-discovery-service-is-deprecated)참조).| 테넌트 및 사용자가 [조건부 액세스](../active-directory/conditional-access/overview.md) 및/또는 Multi-Factor Authentication이 필요한 Azure Active Directory에 구성된 경우, 2021년 6월 8일 이후에 인증하려면 'Azure AD 서비스 주체'를 사용해야 합니다. 자세한 단계는 이 [링를](./connector-dynamics-crm-office-365.md#prerequisites)를 참조하세요.|
    |오류 메시지에 `Office 365 auth with OAuth failed`라고 표시된다면 서버에 OAuth와 호환되지 않는 구성이 있는 것임을 의미합니다.| <li>Dynamics 지원 팀에 연락하여 상세한 오류 메시지를 제공하고 도움을 받습니다.</li><li>서비스 주체 인증을 사용하세요. [예: Azure AD 서비스 주체 및 인증서 인증을 사용하는 Dynamics Online](./connector-dynamics-crm-office-365.md#example-dynamics-online-using-azure-ad-service-principal-and-certificate-authentication)을 참조할 수도 있습니다.</li>
    |오류 메시지에 `Unable to retrieve authentication parameters from the serviceUri`라고 표시된다면 Dynamics 서비스 URL을 잘못 입력했거나 프록시/방화벽이 트래픽을 인터셉트하도록 설정된 것임을 의미합니다. |<li>연결된 서비스에 올바른 서비스 URI를 입력했는지 확인합니다.</li><li>자체 호스팅 IR을 사용하는 경우 방화벽/프록시가 Dynamics 서버로 전송되는 요청을 인터셉트하지 않는지 확인합니다.</li> |
    |오류 메시지에 `An unsecured or incorrectly secured fault was received from the other party`라고 표시된다면 서버 쪽에서 예기치 않은 응답을 수신했음을 의미합니다.  | <li>Office 365 인증을 사용하는 경우 사용자 이름과 암호가 올바른지 확인합니다. </li><li> 올바른 서비스 URI를 입력했는지 확인합니다.</li><li>지역 CRM URL(‘crm’ 뒤에 숫자가 있는 URL)을 사용하는 경우 올바른 지역 식별자를 사용했는지 확인합니다.</li><li>도움이 필요하면 Dynamics 지원 팀으로 문의하세요.</li>|
    |오류 메시지에 `No Organizations Found`라고 표시된다면 조직 이름이 잘못되었거나 서비스 URL에 잘못된 CRM 지역 식별자를 사용했음을 의미합니다.|<li>올바른 서비스 URI를 입력했는지 확인합니다.</li><li>지역 CRM URL(‘crm’ 뒤에 숫자가 있는 URL)을 사용하는 경우 올바른 지역 식별자를 사용했는지 확인합니다.</li><li>도움이 필요하면 Dynamics 지원 팀으로 문의하세요.</li>|
    | `401 Unauthorized` 및 AAD 관련 오류 메시지가 표시된다면 서비스 주체에 문제가 있음을 의미합니다. |오류 메시지의 지침에 따라 서비스 주체 문제를 해결합니다. |
   |그 밖의 오류의 경우, 보통 서버 쪽에 문제가 있는 것입니다. |[XrmToolBox](https://www.xrmtoolbox.com/)를 사용하여 연결을 설정합니다. 오류가 지속되면 Dynamics 지원 팀으로 문의하여 도움을 받으세요. |

## <a name="error-code-dynamicsoperationfailed"></a>오류 코드: DynamicsOperationFailed 
 
- **메시지**: `Dynamics operation failed with error code: %code;, error message: %message;.` 

- **원인**: 서버 쪽에서 작업이 실패했습니다. 

- **권장 사항**: 오류 메시지 `Dynamics operation failed with error code: {code}`에서 Dynamics 작업의 오류 코드를 확인하고 [웹 서비스 오류 코드](/powerapps/developer/data-platform/org-service/web-service-error-codes) 문서에서 자세한 정보를 참조합니다. 필요한 경우 Dynamics 지원 팀으로 문의하세요. 
 
 
## <a name="error-code-dynamicsinvalidfetchxml"></a>오류 코드: DynamicsInvalidFetchXml 
  
- **메시지**: `The Fetch Xml query specified is invalid.` 

- **원인**: 페치 XML에 오류가 있습니다.  

- **권장 사항**: 페치 XML에서 오류를 수정합니다. 
 
 
## <a name="error-code-dynamicsmissingkeycolumns"></a>오류 코드: DynamicsMissingKeyColumns 
 
- **메시지**: `Input DataSet must contain keycolumn(s) in Upsert/Update scenario. Missing key column(s): %column;`
 
- **원인**: 원본 데이터에 싱크 엔터티의 키 열이 없습니다. 

- **권장 사항**: 원본 데이터에 키 열이 있는지 확인하거나 싱크 엔터티에서 원본 열을 키 열에 매핑합니다. 
 
 
## <a name="error-code-dynamicsprimarykeymustbeguid"></a>오류 코드: DynamicsPrimaryKeyMustBeGuid 
 
- **메시지**: `The primary key attribute '%attribute;' must be of type guid.` 
 
- **원인**: 기본 키 열의 형식이 ‘Guid’가 아닙니다. 
 
- **권장 사항**: 원본 데이터의 기본 키 열이 ‘Guid’ 형식인지 확인합니다. 
 

## <a name="error-code-dynamicsalternatekeynotfound"></a>오류 코드: DynamicsAlternateKeyNotFound 
 
- **메시지**: `Cannot retrieve key information of alternate key '%key;' for entity '%entity;'.` 
 
- **원인**: 제공된 대체 키가 없습니다. 키 이름이 잘못되었거나 권한이 없는 것일 수 있습니다. 
 
- **권장 사항**: <br/> 
    - 키 이름에서 오타를 수정합니다.<br/> 
    - 엔터티에 대한 충분한 권한이 있는지 확인합니다. 
 
 
## <a name="error-code-dynamicsinvalidschemadefinition"></a>오류 코드: DynamicsInvalidSchemaDefinition 
 
- **메시지**: `The valid structure information (column name and type) are required for Dynamics source.` 
 
- **원인**: 열 매핑의 싱크 열에서 ‘type’ 속성이 누락되었습니다. 
 
- **권장 사항**: 포털에서 JSON 편집기를 사용하여 열 매핑에서 해당 열에 ‘type’ 속성을 추가할 수 있습니다. 

## <a name="the-copy-activity-from-the-dynamics-365-reads-more-rows-than-the-actual-number"></a>Dynamics 365의 복사 작업은 실제 숫자 보다 많은 행을 읽습니다.

- **증상**: Dynamics 365의 복사 작업은 실제 숫자 보다 많은 행을 읽습니다.

- **원인**: Dynamics 365 서버는 항상 사용 가능한 레코드를 표시 합니다. 

- **권장 사항**: **Xrmtoolbox** 를 사용 하 여 페이징으로 fetchxml을 테스트 합니다. 일부 설치 된 도구가 있는 **Xrmtoolbox** 는 레코드 수를 가져올 수 있습니다. 자세한 내용은 [Xrmtoolbox](https://www.xrmtoolbox.com/)를 참조 하세요.

## <a name="cannot-access-virtual-columns-from-dynamics-sources-in-the-copy-activity"></a>복사 작업의 Dynamics 원본에서 가상 열에 액세스할 수 없습니다.

- **증상**: 복사 작업의 Dynamics 원본에서 가상 열에 액세스할 수 없습니다.

- **원인**: 이제 가상 열이 지원 되지 않습니다. 

- **권장 사항**: 옵션 집합 값에 대해 다음 옵션을 수행 하 여 가져옵니다.
  - 모든 엔터티 및 [Dynamics 365 블로그의](https://dynamicscrmdotblog.wordpress.com/) [개체 형식 코드를 찾는 방법](https://powerobjects.com/tips-and-tricks/find-object-type-code-entity/) 을 참조 하 여 개체 형식 코드를 가져올 수 있습니다.
  - StringMap 엔터티를 대상 엔터티에 연결 하 고 연결 된 값을 가져올 수 있습니다.

## <a name="the-parallel-copy-in-a-dynamics-crm-data-store"></a>Dynamics CRM 데이터 저장소의 병렬 복사

- **증상**: Dynamics CRM 데이터 저장소에서 병렬 복사를 구성할 수 있는지 여부를 알 수 없으며 "병렬 처리 수준 복사" 섹션에서 설정할 수 있는 값의 범위를 모를 수도 있습니다.

- **권장 사항**: 병렬 복사는 병렬 처리를 제어 하 고 "복사 병렬 처리 수준" 섹션은 0이 아닌 값으로 설정할 수 있습니다. 숫자가 많으면 dynamics 서버 쪽에서 제한이 발생할 수 있으므로 처리량을 줄일 수 있지만 이제는 공용 SDK를 통해 제한을 처리 합니다.

  :::image type="content" source="./media/connector-troubleshoot-guide/degree-of-copy-parallelism-section.png" alt-text="병렬 처리 수준 복사 섹션의 다이어그램입니다.":::

## <a name="dynamics-type-conversion"></a>Dynamics 형식 변환

- **증상**: GUID를 Dynamics 원본의 문자열로 변환 하려고 하지만 오류가 발생 합니다.

- **원인**: Dynamics를 소스로 사용 하는 경우 형식 변환이 지원 되지 않습니다.

- **권장 사항**: 준비를 사용 하도록 설정 하 고 다시 시도 합니다.

## <a name="next-steps"></a>다음 단계

문제 해결을 위한 도움이 필요한 경우 다음 리소스를 참조하세요.

- [커넥터 문제 해결 가이드](connector-troubleshoot-guide.md)
- [Data Factory 블로그](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Data Factory 기능 요청](/answers/topics/azure-data-factory.html)
- [Azure 비디오](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Microsoft Q&A 페이지](/answers/topics/azure-data-factory.html)
- [Data Factory에 대한 Stack Overflow 포럼](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Data Factory에 대한 Twitter 정보](https://twitter.com/hashtag/DataFactory)

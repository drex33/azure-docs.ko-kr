---
title: 웹후크 작업
titleSuffix: Azure Data Factory & Azure Synapse
description: Azure Data Factory 및 Synapse Analytics에 대 한 웹 후크 작업은 사용자 지정 코드를 통해 파이프라인의 실행을 제어 합니다.
author: nabhishek
ms.author: abnarain
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: orchestration
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.openlocfilehash: 8b0443cd44dffeec1ea9a70e460ca0f72e05b24d
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/13/2021
ms.locfileid: "124798829"
---
# <a name="webhook-activity-in-azure-data-factory"></a>Azure Data Factory의 웹후크 작업

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

웹 후크 작업은 사용자 지정 코드를 통해 파이프라인의 실행을 제어할 수 있습니다. 웹 후크 작업을 사용 하면 코드에서 끝점을 호출 하 고 콜백 URL을 전달할 수 있습니다. 파이프라인 실행은 다음 작업을 진행하기 전에 콜백이 호출될 때까지 기다립니다.

> [!IMPORTANT]
> 웹후크 작업을 사용하면 이제 오류 상태와 사용자 지정 메시지를 작업 및 파이프라인에 다시 표시할 수 있습니다. _reportStatusOnCallBack_ 을 true로 설정하고 콜백 페이로드에 _StatusCode_ 및 _Error_ 를 포함합니다. 자세한 내용은 [참고 사항](#additional-notes) 섹션을 참조하세요.

## <a name="syntax"></a>구문

```json

{
    "name": "MyWebHookActivity",
    "type": "WebHook",
    "typeProperties": {
        "method": "POST",
        "url": "<URLEndpoint>",
        "headers": {
            "Content-Type": "application/json"
        },
        "body": {
            "key": "value"
        },
        "timeout": "00:03:00",
        "reportStatusOnCallBack": false,
        "authentication": {
            "type": "ClientCertificate",
            "pfx": "****",
            "password": "****"
        }
    }
}

```

## <a name="type-properties"></a>형식 속성

속성 | Description | 허용되는 값 | 필수
-------- | ----------- | -------------- | --------
**name** | 웹후크 작업의 이름입니다. | String | 예 |
**type** | “WebHook”로 설정해야 합니다. | String | 예 |
**method** | 대상 엔드포인트에 대한 REST API 메서드입니다. | 문자열입니다. 지원되는 형식은 “POST”입니다. | 예 |
**url** | 대상 엔드포인트 및 경로입니다. | 문자열 또는 문자열의 **resultType** 값이 포함된 식입니다. | 예 |
**headers** | 요청에 전송되는 헤더입니다. 요청에 언어 및 형식을 설정하려면 `"headers" : { "Accept-Language": "en-us", "Content-Type": "application/json" }`과 같이 합니다. | 문자열 또는 문자열의 **resultType** 값이 포함된 식입니다. | 예. `"headers":{ "Content-Type":"application/json"}`와 같은 `Content-Type` 헤더가 필요합니다. |
**body** | 엔드포인트에 전송된 페이로드를 나타냅니다. | 유효한 JSON 또는 JSON의 **resultType** 값이 포함된 식입니다. 요청 페이로드의 스키마는 [요청 페이로드 스키마](./control-flow-web-activity.md#request-payload-schema)를 참조하세요. | 예 |
**인증** | 엔드포인트를 호출하는 데 사용되는 인증 방법입니다. 지원되는 형식은 “Basic” 및 “ClientCertificate”입니다. 자세한 내용은 [인증](./control-flow-web-activity.md#authentication)을 참조하세요. 인증이 필요 없는 경우 이 속성을 제외합니다. | 문자열 또는 문자열의 **resultType** 값이 포함된 식입니다. | 예 |
**timeout** | 작업이 **callBackUri** 로 지정된 콜백이 호출될 때까지 대기하는 시간입니다. 기본값은 10분(“00:10:00”)입니다. 값은 *d*.*hh*:*mm*:*ss* 의 TimeSpan 형식을 가집니다. | String | 예 |
**콜백에 대한 보고서 상태** | 사용자가 웹후크 작업의 실패 상태를 보고할 수 있습니다. | 부울 | 예 |

## <a name="authentication"></a>인증

웹후크 작업은 다음 인증 유형을 지원합니다.

### <a name="none"></a>없음

인증이 필요 없는 경우 **authentication** 속성을 포함하지 않습니다.

### <a name="basic"></a>Basic

기본 인증에 사용할 사용자 이름 및 암호를 지정합니다.

```json
"authentication":{
   "type":"Basic",
   "username":"****",
   "password":"****"
}
```

### <a name="client-certificate"></a>클라이언트 인증서

PFX 파일의 Base64로 인코딩된 콘텐츠 및 암호를 지정합니다.

```json
"authentication":{
   "type":"ClientCertificate",
   "pfx":"****",
   "password":"****"
}
```

### <a name="managed-identity"></a>관리 ID

데이터 팩터리 또는 Synapse 작업 영역에 관리 되는 id를 사용 하 여 액세스 토큰을 요청 하는 리소스 URI를 지정 합니다. Azure Resource 관리 API를 호출하려면 `https://management.azure.com/`을 사용합니다. 관리 ID의 작동 방식에 대한 자세한 내용은 [Azure 리소스의 관리 ID 개요](../active-directory/managed-identities-azure-resources/overview.md)를 참조하세요.

```json
"authentication": {
    "type": "MSI",
    "resource": "https://management.azure.com/"
}
```

> [!NOTE]
> 서비스가 Git 리포지토리로 구성 된 경우 기본 또는 클라이언트 인증서 인증을 사용 하도록 Azure Key Vault에 자격 증명을 저장 해야 합니다. 서비스는 Git에 암호를 저장 하지 않습니다.

## <a name="additional-notes"></a>추가적인 참고 사항

서비스는 URL 끝점으로 전송 된 본문에서 추가 속성 **Callbackuri** 를 전달 합니다. 서비스에서는 지정 된 시간 제한 값 보다 먼저이 URI를 호출 해야 합니다. URI가 호출되지 않은 경우 작업은 “TimedOut” 상태와 함께 실패합니다.

사용자 지정 엔드포인트에 대한 호출이 실패할 경우 웹후크 작업이 실패합니다. 모든 오류 메시지를 콜백 본문에 추가하고 이후 작업에서 사용할 수 있습니다.

모든 REST API 호출의 경우 엔드포인트가 1분 이내에 응답하지 않으면 클라이언트 시간이 초과합니다. 이 동작은 표준 HTTP 모범 사례입니다. 이 문제를 해결하려면 202 패턴을 구현합니다. 현재의 경우 엔드포인트는 202(수락됨)를 반환하고 클라이언트에서 폴링합니다.

요청의 1분 시간 제한은 작업의 시간 제한과는 관계가 없습니다. 후자는 **callbackUri** 로 지정된 콜백을 기다리는 데 사용됩니다.

콜백 URI로 다시 전달되는 본문은 유효한 JSON이어야 합니다. `Content-Type` 헤더를 `application/json`으로 설정합니다.

**콜백에 대한 보고서 상태** 속성을 사용하는 경우 콜백을 수행할 때 본문에 다음 코드를 추가해야 합니다.

```json
{
    "Output": {
        // output object is used in activity output
        "testProp": "testPropValue"
    },
    "Error": {
        // Optional, set it when you want to fail the activity
        "ErrorCode": "testErrorCode",
        "Message": "error message to show in activity error"
    },
    "StatusCode": "403" // when status code is >=400, activity is marked as failed
}
```

## <a name="next-steps"></a>다음 단계

다음 지원 되는 제어 흐름 작업을 참조 하세요.

- [If 조건 작업](control-flow-if-condition-activity.md)
- [파이프라인 작업 실행](control-flow-execute-pipeline-activity.md)
- [ForEach 작업](control-flow-for-each-activity.md)
- [메타데이터 작업 가져오기](control-flow-get-metadata-activity.md)
- [조회 작업](control-flow-lookup-activity.md)
- [웹 작업](control-flow-web-activity.md)
- [Until 작업](control-flow-until-activity.md)
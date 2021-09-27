---
title: Azure FarmBeats API
description: JSON 기반 응답을 사용하여 표준화된 RESTful 인터페이스를 농업 비즈니스에 제공하는 Azure FarmBeats API에 대해 알아봅니다.
author: sunasing
ms.topic: article
ms.date: 11/04/2019
ms.author: sunasing
ms.openlocfilehash: 4ddbe9ff6a3dfa195d0739205c3e32070b170f30
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128560755"
---
# <a name="azure-farmbeats-apis"></a>Azure FarmBeats API

이 문서에서는 Azure FarmBeats API에 대해 설명합니다. Azure FarmBeats API는 농업 비즈니스에 JSON 기반 응답이 포함된 표준화된 RESTful 인터페이스를 제공하여 다음과 같은 Azure FarmBeats 기능을 활용할 수 있도록 지원합니다.

- 센서, 카메라, 드론, 날씨, 위성 및 큐레이트 그라운드 데이터를 가져오는 API.
- 일반 데이터 공급자에 대한 데이터 정규화 및 컨텍스트화.
- 수집된 모든 데이터에 대해 스키마화된 액세스 및 쿼리 기능.
- 농업적 기능을 기반으로 쿼리할 수 있는 메타데이터의 자동 생성.
- 빠른 모델 빌드를 위해 자동으로 생성된 시간 시퀀스 집계.
- 사용자 지정 데이터 처리 파이프라인을 쉽게 빌드하기 위한 통합 Azure Data Factory 엔진.

## <a name="application-development"></a>애플리케이션 개발

FarmBeats API에는 Swagger 기술 문서가 포함되어 있습니다. 모든 API 및 해당 요청 또는 응답에 대한 자세한 내용은 [Swagger](https://aka.ms/FarmBeatsDatahubSwagger)를 참조하세요.

다음 표에는 FarmBeats Datahub의 모든 개체와 리소스가 요약되어 있습니다.

| 개체 및 리소스 | 설명
--- | ---|
팜 | 팜은 FarmBeats 시스템 내에서 관심 있는 물리적 위치에 해당합니다. 각 팜에는 팜 이름과 고유한 팜 ID가 있습니다. |
디바이스  | Device는 팜에 있는 물리적 디바이스에 해당합니다. 디바이스마다 고유 디바이스 ID가 있습니다. 디바이스는 일반적으로 팜 ID를 사용하여 팜으로 프로비전됩니다.
DeviceModel  | DeviceModel은 제조업체 및 디바이스 유형(게이트웨이 또는 노드)과 같은 디바이스 메타데이터에 해당합니다.
센서  | Sensor는 값을 기록하는 실제 센서에 해당합니다. 센서는 일반적으로 디바이스 ID를 갖는 디바이스에 연결됩니다.
SensorModel  | SensorModel은 제조업체, 센서 유형(아날로그 또는 디지털), 센서 측정값(예: 주변 온도 및 압력)과 같은 센서의 메타데이터에 해당합니다.
원격 분석  | 원격 분석은 특정 센서 및 시간 범위에 대한 원격 분석 메시지를 읽는 기능을 제공합니다.
작업  | 작업은 원하는 출력을 얻기 위해 FarmBeats 시스템에서 실행되는 활동의 모든 워크플로에 해당합니다. 각 작업은 작업 ID 및 작업 유형과 연결됩니다.
JobType  | JobType은 시스템에서 지원하는 여러 작업 유형에 해당합니다. 시스템 정의 및 사용자 정의 작업 유형이 포함됩니다.
ExtendedType  | ExtendedType은 시스템에서 시스템 정의 형식 및 사용자 정의 형식 목록에 해당합니다. ExtendedType은 FarmBeats 시스템에서 새 센서, 장면 또는 장면 파일 형식을 설정하는 데 도움이 됩니다.
파트너  | 파트너는 FarmBeats의 센서 및 이미지 통합 파트너에 해당합니다.
장면  | 장면은 팜 컨텍스트에서 생성된 모든 출력에 해당합니다. 각 장면에는 장면 ID, 장면 원본, 장면 유형 및 연결된 팜 ID가 있습니다. 각 장면 ID에는 여러 장면 파일이 연결될 수 있습니다.
SceneFile |SceneFile는 단일 장면에 대해 생성되는 모든 파일에 해당합니다. 단일 장면 ID에는 여러 SceneFile ID가 연결될 수 있습니다.
규칙  |규칙은 경고를 트리거하기 위한 팜 관련 데이터의 조건에 해당합니다. 각 규칙은 팜 데이터의 컨텍스트에 있습니다.
경고  | 경고는 규칙 조건이 충족될 때 생성되는 알림에 해당합니다. 각 경고는 규칙의 컨텍스트에 있습니다.
RoleDefinition  | RoleDefinition은 역할에 대해 허용되거나 허용되지 않는 작업을 정의합니다.
RoleAssignment  |RoleAssignment는 사용자 또는 서비스 주체에 대한 역할 할당에 해당합니다.

### <a name="data-format"></a>데이터 형식

JSON은 임의 데이터 구조의 단순한 텍스트 표현을 제공하는 공용 언어 독립적 데이터 형식입니다. 자세한 내용은 [JSON 웹 사이트](https://www.json.org/)를 참조하세요.

## <a name="authentication-and-authorization"></a>인증 및 권한 부여

REST API에 대한 HTTP 요청은 Azure AD(Azure Active Directory)로 보호됩니다.
REST API에 대해 인증된 요청을 수행하려면 API를 호출하기 전에 클라이언트 코드에 유효한 자격 증명으로 인증해야 합니다. Azure AD의 다양한 작업자 간에 인증이 조정됩니다. 클라이언트에게 인증 증명으로 액세스 토큰을 제공합니다. 그런 다음 토큰은 REST API 요청의 HTTP 권한 부여 헤더로 전송됩니다. Azure Active Directory 인증에 대해 자세히 알아보려면 개발자를 위한 [Azure Active Directory](https://portal.azure.com)를 참조하세요.

액세스 토큰은 다음과 같이 헤더 섹션에서 후속 API 요청으로 전송해야 합니다.

```http
headers = {"Authorization": "Bearer " + **access_token**}
```

### <a name="http-request-headers"></a>HTTP 요청 헤더

Azure FarmBeats Datahub에 대한 API 호출을 수행할 때 지정해야 하는 가장 일반적인 요청 헤더는 다음과 같습니다.


**머리글** | **설명 및 예**
--- | ---
콘텐츠 형식  | 요청 형식입니다(Content-Type: application/\<format\>). Azure FarmBeats Datahub API의 경우 형식은 JSON입니다. Content-Type: application/json
권한 부여  | API 호출을 수행하는 데 필요한 액세스 토큰을 지정합니다. 권한 부여: 전달자 \<Access-Token\>
수락 | 응답 형식입니다. Azure FarmBeats Datahub API의 경우 형식은 JSON입니다. Accept: application/json

### <a name="api-requests"></a>API 요청

REST API 요청을 수행하려면 HTTP(GET, POST, PUT 또는 DELETE) 메서드, API 서비스에 대한 URL과 쿼리, 데이터 전송, 업데이트 또는 삭제할 리소스에 대한 URI(Uniform Resource Identifier) 및 하나 이상의 HTTP 요청 헤더를 결합합니다.

API 서비스에 대 한 URL은 Datahub URL (예:)입니다 `https://<yourdatahub-website-name>.azurewebsites.net` .

필요에 따라 GET 호출에 쿼리 매개 변수를 포함하여 응답에서 데이터를 필터링하고, 크기를 제한하고, 정렬할 수 있습니다.

다음 샘플 요청은 디바이스 목록을 가져오는 데 사용됩니다.

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>”
```

대부분의 GET, POST 및 PUT 호출에는 JSON 요청 본문이 필요합니다.

다음 샘플 요청은 디바이스를 만듭니다. 이 요청에는 요청 본문을 사용하는 입력 JSON이 있습니다.

```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

### <a name="query-parameters"></a>쿼리 매개 변수

REST GET 호출의 경우 요청 URI에 하나 이상의 쿼리 매개 변수를 포함하여 API 응답에서 데이터를 필터링하고, 크기를 제한하고, 데이터를 정렬할 수 있습니다. 쿼리 매개 변수는 API 설명서 및 개별 GET 호출을 참조하세요.
예를 들어 디바이스 목록을 쿼리하면(/Device에서 GET 호출) 다음과 같은 쿼리 매개 변수를 지정할 수 있습니다.

![디바이스 목록](./media/references-for-azure-farmbeats/query-parameters-device-1.png)

### <a name="error-handling"></a>오류 처리

Azure FarmBeats Datahub API는 표준 HTTP 오류를 반환합니다. 가장 일반적인 오류 코드는 다음과 같습니다.

 |오류 코드             | Description |
 |---                    | --- |
 |200                    | Success |
 |201                    | 만들기(게시) 성공 |
 |400                    | 잘못된 요청. 요청에 오류가 있습니다. |
 |401                    | 권한이 없습니다. API 호출자에게 리소스에 액세스할 수 있는 권한이 없습니다. |
 |404                    | 리소스를 찾을 수 없음 |
 |5XX                    | 내부 서버 오류. 5XX로 시작하는 오류 코드는 서버에서 오류가 발생했음을 의미합니다. 자세한 내용은 서버 로그 및 다음 섹션을 참조하세요. |


표준 HTTP 오류 외에도 Azure FarmBeats Datahub API는 다음과 같은 형식으로 내부 오류를 반환합니다.

```json
    {
      "message": "<More information on the error>",
      "status": "<error code>”,
      "code": "<InternalErrorCode>",
      "moreInfo": "<Details of the error>"
    }
```

이 예에서는 팜이 생성될 때 입력 페이로드에 필수 필드 ‘Name’이 지정되지 않았습니다. 결과 오류 메시지는 다음과 같습니다.

 ```json    
    {
      "message": "Model validation failed",
      "status": 400,
      "code": "ModelValidationFailed",
      "moreInfo": "[\"The Name field is required.\"]"
    }
  ```

## <a name="add-users-or-app-registrations-to-azure-active-directory"></a>Azure Active Directory에 사용자 또는 앱 등록 추가

Azure FarmBeats API는 Azure Active Directory에서 사용자 또는 앱 등록을 통해 액세스할 수 있습니다. Azure Active Directory에서 앱 등록을 만들려면 다음 단계를 수행합니다.

1. [Azure Portal](https://portal.azure.com)로 이동한 다음 **Azure Active Directory** > **앱 등록** > **새 등록** 을 선택합니다. 또는 기존 계정을 사용할 수 있습니다.
2. 새 계정에 대해 다음을 수행합니다.

    - 이름을 입력합니다.
    - **이 조직 디렉터리의 계정만(단일 테넌트)** 을 선택합니다.
    - 나머지 필드에는 기본값을 사용합니다.
    - **등록** 을 선택합니다.

3. 신규 및 기존 앱 등록 **개요** 창에서 다음을 수행합니다.

    - **클라이언트 ID** 및 **테넌트 ID** 를 캡처합니다.
    - 새 클라이언트 암호를 생성하고 **클라이언트 암호** 를 캡처하려면 **인증서 및 비밀** 로 이동합니다.
    - **개요** 로 돌아가서 **로컬 디렉터리에서 애플리케이션 관리** 옆의 링크를 선택합니다.
    - **속성** 으로 이동하여 **개체 ID** 를 캡처합니다.

4. Datahub Swagger ()로 이동 `https://<yourdatahub>.azurewebsites.net/swagger/index.html` 하 고 다음을 수행 합니다.
    - **RoleAssignment API** 로 이동합니다.
    - POST를 수행하여 방금 만든 **개체 ID** 에 대한 **RoleAssignment** 개체를 만듭니다.
 
```json
{
  "roleDefinitionId": "a400a00b-f67c-42b7-ba9a-f73d8c67e433",
  "objectId": "objectId from step 3 above",
  "objectIdType": "ServicePrincipalId",
  "tenantId": "tenant id of your Azure subscription"
}
```

  > [!NOTE]
  > 사용자 및 Active Directory 등록을 추가하는 방법에 대한 자세한 내용은 [Azure Active Directory](../../active-directory/develop/howto-create-service-principal-portal.md)를 참조하세요.

이전 단계를 완료한 후에 앱 등록(클라이언트)은 전달자 인증을 통해 액세스 토큰을 사용하여 Azure FarmBeats API를 호출할 수 있습니다.

액세스 토큰은 다음과 같이 헤더 섹션에서 후속 API 요청으로 전송해야 합니다.

```http
headers = {"Authorization": "Bearer " + **access_token**, "Content-Type" : "application/json" }
```
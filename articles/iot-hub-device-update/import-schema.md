---
title: Device Update for IoT Hub로 업데이트 가져오기 - 스키마 및 기타 정보 | Microsoft Docs
description: 업데이트를 Device Update for IoT Hub로 가져올 때 사용되는 스키마 및 기타 관련 정보(개체 포함)입니다.
author: andrewbrownmsft
ms.author: andbrown
ms.date: 2/25/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 09dc0bd3afb2b9bfc99313ad38d5c7ad19086cb8
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132061043"
---
# <a name="importing-updates-into-device-update-for-iot-hub---schema-and-other-information"></a>Device Update for IoT Hub로 업데이트 가져오기 - 스키마 및 기타 정보
Device Update for IoT Hub로 업데이트를 가져오려면 먼저 [개념](import-concepts.md) 및 [방법 가이드](import-update.md)를 검토해야 합니다. 가져오기 매니페스트를 생성할 때 사용 되는 스키마의 세부 정보 또는 관련 개체에 대 한 자세한 내용은 아래를 참조 하세요.

## <a name="import-manifest-schema"></a>매니페스트 스키마 가져오기

| 이름 | Type | 설명 | 제한 |
| --------- | --------- | --------- | --------- |
| UpdateId | `UpdateId` 개체의  멤버의 부모에 대해 SQL Server 인스턴스 이름을 표시합니다. | ID를 업데이트합니다. |
| UpdateType | 문자열 | 형식 업데이트: <br/><br/> * 참조 에이전트를 사용하여 패키지 기반 업데이트를 수행하는 경우 `microsoft/apt:1`를 지정합니다.<br/> * 참조 에이전트를 사용하여 이미지 기반 업데이트를 수행하는 경우 `microsoft/swupdate:1`를 지정합니다.<br/> * 샘플 에이전트 시뮬레이터를 사용하는 경우 `microsoft/simulator:1`를 지정합니다.<br/> * 사용자 지정 에이전트를 개발하는 경우 사용자 지정 형식을 지정합니다. | 형식: <br/> `{provider}/{type}:{typeVersion}`<br/><br/> 최대 32자 |
| InstalledCriteria | 문자열 | 업데이트가 성공적으로 적용되었는지 확인하기 위해 에이전트에서 해석되는 문자열:  <br/> * 업데이트 형식 `microsoft/swupdate:1`의 경우 SWVersion **값** 을 지정합니다.<br/> * APT 파일에서 이름 및 버전을 가져올 업데이트 형식 `microsoft/apt:1`에 대한 `{name}-{version}`를 지정합니다.<br/> * 사용자 지정 에이전트를 개발하는 경우 사용자 지정 문자열을 지정합니다.<br/> | 최대 64자 |
| 호환성 | `CompatibilityInfo` [개체](#compatibilityinfo-object)의 배열 | 이 업데이트와 호환되는 디바이스의 호환성 정보입니다. | 최대 10개 항목 |
| CreatedDateTime | 날짜/시간 | 업데이트를 만든 날짜 및 시간입니다. | UTC로 구분된 ISO 8601 날짜 및 시간 형식 |
| ManifestVersion | 문자열 | 매니페스트 스키마 버전을 가져옵니다. `urn:azureiot:AzureDeviceUpdateCore:1` 인터페이스 및 `urn:azureiot:AzureDeviceUpdateCore:4` 인터페이스와 호환되는 `2.0`를 지정합니다. | `2.0`이어야 합니다. |
| 파일 | `File` 개체의 배열 | 페이로드 파일 업데이트 | 최대 5 개 파일 |

## <a name="updateid-object"></a>UpdateId 개체

| 이름 | Type | 설명 | 제한 |
| --------- | --------- | --------- | --------- |
| 공급자 | 문자열 | 업데이트 ID의 공급자 부분입니다. | 1-64 자, 영숫자, 점 및 대시가 있습니다. |
| 이름 | string | 업데이트 ID의 이름 부분입니다. | 1-64 자, 영숫자, 점 및 대시가 있습니다. |
| 버전 | 버전 | 업데이트 ID의 버전 부분입니다. | 2-4 부분, 점으로 구분 된 버전 번호입니다. _각_ 점으로 구분 된 부분의 총 수는 0에서 2147483647 사이가 될 수 있습니다. 앞에 오는 0은 지원 되지 않습니다.

## <a name="file-object"></a>파일 개체

| 이름 | Type | 설명 | 제한 |
| --------- | --------- | --------- | --------- |
| 파일 이름 | 문자열 | 파일 이름 | 255자 이하여야 합니다. 업데이트 내에서 고유해야 합니다. |
| SizeInBytes | Int64 | 파일 크기(바이트)입니다. | 개별 파일당 최대 크기에 대 한 [장치 업데이트 제한](./device-update-limits.md) 및 집합적으로 업데이트를 참조 하세요. |
| 해시 | `Hashes` 개체의  멤버의 부모에 대해 SQL Server 인스턴스 이름을 표시합니다. | 파일의 해시를 포함하는 JSON 개체 |

## <a name="compatibilityinfo-object"></a>CompatibilityInfo 개체

| 이름 | Type | 설명 | 제한 |
| --- | --- | --- | --- |
| DeviceManufacturer | 문자열 | 업데이트가 호환되는 디바이스의 제조업체입니다. | 1~64자, 영숫자, 점과 대시가 있습니다. |
| DeviceModel | 문자열 | 업데이트가 호환되는 디바이스의 모델입니다. | 1~64자, 영숫자, 점과 대시가 있습니다. |

## <a name="hashes-object"></a>해시 개체

| 이름 | 필수 | Type | 설명 |
| --------- | --------- | --------- | --------- |
| Sha256 | True | 문자열 | SHA-256 알고리즘을 사용한 파일의 Base64 인코딩 해시입니다. |

## <a name="example-import-request-body"></a>가져오기 요청 분문 예제

[새 업데이트를 추가하는 방법](./import-update.md#review-the-generated-import-manifest) 페이지의 샘플 가져오기 매니페스트 출력을 사용하고 디바이스 업데이트 [REST API](/rest/api/deviceupdate/updates)를 직접 호출하여 가져오기를 수행하려는 경우 해당 요청 본문은 다음과 같아야 합니다.

```json
{
  "importManifest": {
    "url": "http://<your Azure Storage location file path>/importManifest.json",
    "sizeInBytes": <size of import manifest file>,
    "hashes": {
      "sha256": "<hash of import manifest file>"
    }
  },
  "files": [
    {
      "filename": "file1.json",
      "url": "http://<your Azure Storage location file path>/file1.json"
    },
    {
          "filename": "file2.zip",
          "url": "http://<your Azure Storage location file path>/file2.zip"
    },
  ]
}
```

## <a name="oauth-authorization-when-calling-device-update-apis"></a>장치 업데이트 Api를 호출할 때 OAuth 권한 부여

**azure_auth**

Azure Active Directory OAuth2 흐름 유형: oauth2 흐름: 임의 

권한 부여 URL: https://login.microsoftonline.com/common/oauth2/authorize

**범위**

| Name | 설명 |
| --- | --- |
| `https://api.adu.microsoft.com/user_impersonation` | 사용자 계정 가장 |
| `https://api.adu.microsoft.com/.default`  | 클라이언트 자격 증명 흐름 |


**권한**

사용자를 로그인하는 데 Azure AD 애플리케이션을 사용하는 경우 범위에 /user_impersonation이 있어야 합니다. 

Azure Device Update API를 사용하려면 Azure AD 앱에 사용 권한을 추가해야 합니다(Azure AD 애플리케이션 보기의 API 사용 권한 탭에서). Azure Device Update("내 조직에서 사용하는 API"에 있음)에 대한 API 사용 권한을 요청하고 위임된 user_impersonation 권한을 부여합니다.

ADU은 사용자, 애플리케이션 또는 관리 ID에 대해 Azure AD 지원 흐름을 사용하여 토큰을 획득하도록 허용합니다. 그러나 일부 흐름에는 추가 Azure AD 응용 프로그램 설정이 필요 합니다. 

* 공용 클라이언트 흐름의 경우 모바일 및 데스크톱 흐름을 사용 하도록 설정 해야 합니다.
* 암시적 흐름의 경우 웹 플랫폼을 추가 하 고 권한 부여 끝점에 대해 "액세스 토큰"을 선택 합니다.

**Azure CLI 사용 예제:**

```azurecli
az login

az account get-access-token --resource 'https://api.adu.microsoft.com/'
```

**PowerShell MSAL 라이브러리를 사용하여 토큰을 획득하는 예제:**

_사용자 자격 증명 사용_ 

```powershell
$clientId = '<app_id>’
$tenantId = '<tenant_id>’
$authority = "https://login.microsoftonline.com/$tenantId/v2.0"
$Scope = 'https://api.adu.microsoft.com/user_impersonation'

Get-MsalToken -ClientId $clientId -TenantId $tenantId -Authority $authority -Scopes $Scope
```

_디바이스 코드에 사용자 자격 증명 사용_

```powershell
$clientId = '<app_id>’
$tenantId = '<tenant_id>’
$authority = "https://login.microsoftonline.com/$tenantId/v2.0"
$Scope = 'https://api.adu.microsoft.com/user_impersonation'

Get-MsalToken -ClientId $clientId -TenantId $tenantId -Authority $authority -Scopes $Scope -Interactive -DeviceCode
```

_앱 자격 증명 사용_

```powershell
$clientId = '<app_id>’
$tenantId = '<tenant_id>’
$cert = '<client_certificate>'
$authority = "https://login.microsoftonline.com/$tenantId/v2.0"
$Scope = 'https://api.adu.microsoft.com/.default'

Get-MsalToken -ClientId $clientId -TenantId $tenantId -Authority $authority -Scopes $Scope -ClientCertificate $cert
```

## <a name="next-steps"></a>다음 단계

[가져오기 개념](./import-concepts.md)에 관해 자세히 알아봅니다.

준비되면 가져오기 프로세스를 단계별로 안내하는 [가져오기 방법 가이드](./import-update.md)를 사용해 보세요.

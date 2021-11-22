---
title: Defender for IoT API 작업
description: 외부 REST API를 사용하여 센서 및 관리 콘솔에서 발견된 데이터에 액세스하고 해당 데이터를 사용하여 작업을 수행합니다.
ms.date: 11/21/2021
ms.topic: reference
ms.openlocfilehash: 84cf68d64a6aba783c990337cdfa57ba35c1297e
ms.sourcegitcommit: 6f30424a4ab8dffc4e690086e898ab52bc4da777
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/22/2021
ms.locfileid: "132903483"
---
# <a name="defender-for-iot-sensor-and-management-console-apis"></a>Defender for IoT 센서 및 관리 콘솔 API

IoT 용 Defender Api는 [MICROSOFT Api 라이선스 및 사용 약관](/legal/microsoft-apis/terms-of-use)의 적용을 받습니다.

외부 REST API를 사용하여 센서 및 관리 콘솔에서 발견된 데이터에 액세스하고 해당 데이터를 사용하여 작업을 수행합니다.

연결은 SSL을 통해 보안이 유지됩니다.

## <a name="getting-started"></a>시작

일반적으로 Microsoft Defender for IoT 센서 또는 온-프레미스 관리 콘솔에서 외부 API를 사용 하는 경우 액세스 토큰을 생성 해야 합니다. 센서 및 온-프레미스 관리 콘솔에서 사용하는 인증 API에는 토큰이 필요하지 않습니다.

토큰을 생성하려면 다음을 수행합니다.

1. **시스템 설정** 창에서 **액세스 토큰** 을 선택합니다.
  
   :::image type="content" source="media/references-work-with-defender-for-iot-apis/access-tokens.png" alt-text="액세스 토큰 단추를 강조 표시하는 시스템 설정 창의 스크린샷.":::

1. **Generate new token**(새 토큰 생성)을 탭합니다.

   :::image type="content" source="media/references-work-with-defender-for-iot-apis/new-token.png" alt-text="새 토큰을 생성하는 단추를 선택합니다.":::

1. 새 토큰의 목적을 설명하고 **다음** 을 선택합니다.

   :::image type="content" source="media/references-work-with-defender-for-iot-apis/token-name.png" alt-text="새 토큰을 생성하고 연결된 통합의 이름을 입력합니다.":::

1. 액세스 토큰이 나타납니다. 다시 표시되지 않으므로 복사합니다.

   :::image type="content" source="media/references-work-with-defender-for-iot-apis/token-code.png" alt-text="통합을 위해 액세스 토큰을 복사합니다.":::

1. **마침** 을 선택합니다. 만든 토큰이 **액세스 토큰** 대화 상자에 나타납니다.

   :::image type="content" source="media/references-work-with-defender-for-iot-apis/access-token-window.png" alt-text="채워진 토큰이 있는 디바이스 토큰 대화 상자 스크린샷":::

   **사용됨** 은 이 토큰을 사용한 마지막 외부 호출이 수신된 시간을 나타냅니다.

   이 토큰에 대해 **사용됨** 필드에 **N/A** 가 표시되면 센서와 연결 서버 간의 연결이 작동하지 않습니다.

1. **권한 부여** 라는 HTTP 헤더를 요청에 추가하고 해당 값을 생성한 토큰으로 설정합니다.

## <a name="sensor-api-specifications"></a>센서 API 사양

이 섹션에서는 다음과 같은 센서 API에 대해 설명합니다.

### <a name="no-version"></a>버전 없음

- [사용자 자격 증명 유효성을 검사-/api/external/authentication/validation](#validate-user-credentials---apiexternalauthenticationvalidation)

- [암호 변경-/external/authentication/set_password](#change-password---externalauthenticationset_password)

- [시스템 관리자에 의한 사용자 암호 업데이트-/external/authentication/set_password_by_admin](#user-password-update-by-system-admin---externalauthenticationset_password_by_admin)

### <a name="version-1"></a>버전 1

- [디바이스 정보 검색-/api/v1/devices](#retrieve-device-information---apiv1devices)

- [디바이스 연결 정보 검색-/api/v1/devices/connections](#retrieve-device-connection-information---apiv1devicesconnections)

- [CVEs에서 정보 검색-/api/v1/devices/cves](#retrieve-information-on-cves---apiv1devicescves)

- [경고 정보 검색-/api/v1/alerts](#retrieve-alert-information---apiv1alerts)

- [타임라인 이벤트 검색-/api/v1/events](#retrieve-timeline-events---apiv1events)

- [취약점 정보 검색-/api/v1/reports/vulnerabilities/devices](#retrieve-vulnerability-information---apiv1reportsvulnerabilitiesdevices)

- [보안 취약점 검색-/api/v1/reports/vulnerabilities/security](#retrieve-security-vulnerabilities---apiv1reportsvulnerabilitiessecurity)

- [운영 취약점 검색-/api/v1/reports/vulnerabilities/operational](#retrieve-operational-vulnerabilities---apiv1reportsvulnerabilitiesoperational)

### <a name="version-2"></a>버전 2

- [경고 PCAP-/api/v2/alerts/pcap 검색](#retrieve-alert-pcap---apiv2alertspcap)

### <a name="validate-user-credentials---apiexternalauthenticationvalidation"></a>사용자 자격 증명 유효성 검사-/api/external/authentication/validation

이 API를 사용하여 Defender for IoT의 사용자 이름 및 암호에 대한 유효성을 검사합니다. 모든 Defender for IoT 사용자 역할은 API와 함께 작동할 수 있습니다.

이 API를 사용하는 데 Defender for IoT 액세스 토큰이 필요하지 않습니다.

#### <a name="method"></a>방법

- **POST**

#### <a name="request-type"></a>요청 유형

- **JSON**

#### <a name="query-parameters"></a>쿼리 매개 변수

| **이름** | **형식** | **Null 허용** |
|--|--|--|
| **username** | String | 예 |
| **password** | String | 예 |

#### <a name="request-example"></a>요청 예제

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!"

}

```

#### <a name="response-type"></a>응답 형식

- **JSON**

#### <a name="response-content"></a>응답 콘텐츠

작업 상태 세부 정보를 포함하는 메시지 문자열:

- **성공-메시지**: 인증 성공

- **실패-오류**: 자격 증명 유효성 검사에 실패했습니다

#### <a name="response-example"></a>응답 예제

```rest
response:

{

    "msg": "Authentication succeeded."

}
```

#### <a name="curl-command"></a>Curl 명령

| 유형 | API | 예제 |
|--|--|--|
| GET | `curl -k -H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/external/authentication/validation` | `curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https://127.0.0.1/api/external/authentication/validation` |

### <a name="change-password---externalauthenticationset_password"></a>암호 변경-/external/authentication/set_password

이 API를 사용하여 사용자가 자신의 암호를 변경할 수 있습니다. 모든 Defender for IoT 사용자 역할은 API와 함께 작동할 수 있습니다. 이 API를 사용하는 데 Defender for IoT 액세스 토큰이 필요하지 않습니다.

#### <a name="method"></a>방법

- **POST**

#### <a name="request-type"></a>요청 유형

- **JSON**

#### <a name="request-example"></a>요청 예제

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!",
    
    "new_password": "Test54321\!"

}
```

#### <a name="response-type"></a>응답 형식

- **JSON**

#### <a name="response-content"></a>응답 콘텐츠

작업 상태 세부 정보를 포함하는 메시지 문자열:

- **성공 – 메시지**: 암호가 바뀌었습니다

- **실패 - 오류**: 사용자 인증 실패

- **실패 – 오류**: 암호가 보안 정책과 일치하지 않습니다

#### <a name="response-example"></a>응답 예제

```rest
response:

{

    "error": {
    
        "userDisplayErrorMessage": "User authentication failure"
    
    }

}
```

#### <a name="device-fields"></a>디바이스 필드

| **이름** | **형식** | **Null 허용** |
|--|--|--|
| **username** | String | 예 |
| **password** | String | 예 |
| **new_password** | String | 예 |

#### <a name="curl-command"></a>Curl 명령

| 유형 | API | 예제 |
|--|--|--|
| POST | `curl -k -d '{"username": "<USER_NAME>","password": "<CURRENT_PASSWORD>","new_password": "<NEW_PASSWORD>"}' -H 'Content-Type: application/json'  https://<IP_ADDRESS>/api/external/authentication/set_password` | `curl -k -d '{"username": "myUser","password": "1234@abcd","new_password": "abcd@1234"}' -H 'Content-Type: application/json'  https://127.0.0.1/api/external/authentication/set_password` |

### <a name="user-password-update-by-system-admin---externalauthenticationset_password_by_admin"></a>시스템 관리자에 의한 사용자 암호 업데이트-/external/authentication/set_password_by_admin

이 API를 사용하여 시스템 관리자가 지정된 사용자에 대한 암호를 변경할 수 있습니다. Defender for IoT 관리자 사용자 역할은 API로 작업할 수 있습니다. 이 API를 사용하는 데 Defender for IoT 액세스 토큰이 필요하지 않습니다.

#### <a name="method"></a>방법

- **POST**

#### <a name="request-type"></a>요청 유형

- **JSON**

#### <a name="request-example"></a>요청 예제

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!",
    
    "new_password": "Test54321\!"

}
```

#### <a name="response-type"></a>응답 형식

- **JSON**

#### <a name="response-content"></a>응답 콘텐츠

작업 상태 세부 정보를 포함하는 메시지 문자열:

- **성공 – 메시지**: 암호가 바뀌었습니다

- **실패 - 오류**: 사용자 인증 실패

- **실패-오류**: 사용자가 없습니다

- **실패 – 오류**: 암호가 보안 정책과 일치하지 않습니다

- **실패 – 오류**: 사용자에게 암호 변경 권한이 없습니다

#### <a name="response-example"></a>응답 예제

```rest
response:

{

    "error": {
    
        "userDisplayErrorMessage": "The user 'test_user' doesn't exist",
        
        "internalSystemErrorMessage": "The user 'yoavfe' doesn't exist"
    
    }

}

```

#### <a name="device-fields"></a>디바이스 필드

| **이름** | **형식** | **Null 허용** |
|--|--|--|
| **관리자 사용자 이름** | String | 예 |
| **관리자 암호** | String | 예 |
| **username** | String | 예 |
| **new_password** | String | 예 |

#### <a name="curl-command"></a>Curl 명령

> [!div class="mx-tdBreakAll"]
> | 유형 | API | 예제 |
> |--|--|--|
> | POST | `curl -k -d '{"admin_username":"<ADMIN_USERNAME>","admin_password":"<ADMIN_PASSWORD>","username": "<USER_NAME>","new_password": "<NEW_PASSWORD>"}' -H 'Content-Type: application/json'  https://<IP_ADDRESS>/api/external/authentication/set_password_by_admin` | `curl -k -d '{"admin_user":"adminUser","admin_password": "1234@abcd","username": "myUser","new_password": "abcd@1234"}' -H 'Content-Type: application/json'  https://127.0.0.1/api/external/authentication/set_password_by_admin` |

### <a name="retrieve-device-connection-information---apiv1devicesconnections"></a>디바이스 연결 정보 검색-/api/v1/devices/connections

이 API를 사용하여 디바이스 당 모든 연결 목록을 요청합니다.

#### <a name="method"></a>방법

- **GET**

#### <a name="query-parameters"></a>쿼리 매개 변수

쿼리 매개 변수를 설정하지 않으면 모든 디바이스 연결이 반환됩니다.

**예제**:

`/api/v1/devices/connections`

- **deviceId**: 특정 디바이스 ID를 기준으로 필터링하여 연결을 확인합니다.

  **예제**:

  `/api/v1/devices/<deviceId>/connections`

- **lastActiveInMinutes**: 연결이 활성 상태인 이전부터 현재까지의 시간 프레임(분)입니다.

  **예제**:

  `/api/v1/devices/2/connections?lastActiveInMinutes=20`

- **discoveredBefore**: 특정 시간(밀리초, UTC) 전에 검색된 연결만 필터링합니다.

  **예제**:

  `/api/v1/devices/2/connections?discoveredBefore=<epoch>`

- **discoveredAfter**: 특정 시간(밀리초, UTC) 후에 검색된 연결만 필터링합니다.

  **예제**:

  `/api/v1/devices/2/connections?discoveredAfter=<epoch>`

#### <a name="response-type"></a>응답 형식

- **JSON**

#### <a name="response-content"></a>응답 콘텐츠

디바이스 연결을 나타내는 JSON 개체의 배열입니다.

#### <a name="fields"></a>필드

| 이름 | 유형 | Nullable | 값 목록 |
|--|--|--|--|
| **firstDeviceId** | 숫자 | 예 | - |
| **secondDeviceId** | 숫자 | 예 | - |
| **lastSeen** | 숫자 | 예 | Epoch (UTC) |
| **discovered** | 숫자 | 예 | Epoch (UTC) |
| **포트** | 숫자 배열 | 예 | - |
| **프로토콜** | JSON 배열 | 예 | 프로토콜 필드 |

#### <a name="protocol-field"></a>프로토콜 필드

| 이름 | 유형 | Nullable | 값 목록 |
|--|--|--|--|
| **name** | String | 예 | - |
| **명령** | 문자열 배열 | 예 | - |

#### <a name="response-example"></a>응답 예제

```rest
[

    {
    
        "firstDeviceId": 171,
        
        "secondDeviceId": 22,
        
        "lastSeen": 1511281457933,
        
        "discovered": 1511872830000,
        
        "ports": [
        
            502
        
        ],
    
        "protocols": [
        
        {
        
            name: "modbus",
            
            commands: [
            
                "Read Coils"
        
            ]
        
        },
    
        {
        
            name: "ams",
            
            commands: [
            
                "AMS Write"
        
            ]
        
        },
    
        {
        
            name: "http",
            
            commands: [
        
            ]
        
        }
    
    ]
    
    },
    
    {
    
        "firstDeviceId": 171,
        
        "secondDeviceId": 23,
        
        "lastSeen": 1511281457933,
        
        "discovered": 1511872830000,
        
        "ports": [
        
            502
        
        ],
        
        "protocols": [
        
            {
            
                name: "s7comm",
                
                commands: [
                
                    "Download block",
                    
                    "Upload"
            
                ]
            
            }
        
        ]
    
    }

]
```

#### <a name="curl-command"></a>Curl 명령

> [!div class="mx-tdBreakAll"]
> | 유형 | API | 예제 |
> |--|--|--|
> | GET | `curl -k -H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/v1/devices/connections` | `curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https://127.0.0.1/api/v1/devices/connections` |
> | GET | `curl -k -H "Authorization: <AUTH_TOKEN>" 'https://<IP_ADDRESS>/api/v1/devices/<deviceId>/connections?lastActiveInMinutes=&discoveredBefore=&discoveredAfter='` | `curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" 'https://127.0.0.1/api/v1/devices/2/connections?lastActiveInMinutes=20&discoveredBefore=1594550986000&discoveredAfter=1594550986000'` |

### <a name="retrieve-information-on-cves---apiv1devicescves"></a>CVE에서 정보 검색-/api/v1/devices/cves

이 API를 사용하여 네트워크의 디바이스에서 발견된 모든 알려진 CVE 목록을 요청합니다.

#### <a name="method"></a>방법

- **GET**

#### <a name="query-parameters"></a>쿼리 매개 변수

기본 값으로 이 API는 CVE를 사용하여 모든 디바이스 IP의 목록을 각 IP 주소에 대해 최대 100의 가장 높은 점수를 매긴 CVE까지 제공합니다.

**예제**:

`/api/v1/devices/cves`

- **deviceId**: 특정 디바이스에서 식별된 가장 높은 점수 100 까지 매긴 CVE를 가져오도록 특정 디바이스 IP 주소를 기준으로 필터링합니다.

  **예제**:

  `/api/v1/devices/<ipAddress>/cves`

- **top**: 각 디바이스 IP 주소에 대해 검색할 상위 점수가 매겨진 CVE의 수입니다.

  **예제**:

  `/api/v1/devices/cves?top=50`

  `/api/v1/devices/<ipAddress>/cves?top=50`

#### <a name="response-type"></a>응답 형식

- **JSON**

#### <a name="response-content"></a>응답 콘텐츠

IP 주소에서 식별된 CVE를 나타내는 JSON 개체의 배열입니다.

#### <a name="fields"></a>필드

| 이름 | 유형 | Nullable | 값 목록 |
|--|--|--|--|
| **cveId** | String | 예 | - |
| **ipAddress** | String | 예 | IP 주소 |
| **점수** | String | 예 | 0.0 - 10.0 |
| **attackVector** | String | 예 | 네트워크, 인접 네트워크, 로컬 또는 물리적 |
| **description** | String | 예 | - |

#### <a name="response-example"></a>응답 예제

```rest
[

    {
    
        "cveId": "CVE-2007-0099",
        
        "score": "9.3",
        
        "ipAddress": "10.35.1.51",
        
        "attackVector": "NETWORK",
        
        "description": "Race condition in the msxml3 module in Microsoft XML Core
        
        Services 3.0, as used in Internet Explorer 6 and other
        
        applications, allows remote attackers to execute arbitrary
        
        code or cause a denial of service (application crash) via many
        
        nested tags in an XML document in an IFRAME, when synchronous
        
        document rendering is frequently disrupted with asynchronous
        
        events, as demonstrated using a JavaScript timer, which can
        
        trigger NULL pointer dereferences or memory corruption, aka
        
        \"MSXML Memory Corruption Vulnerability.\""
    
    },
    
    {
    
        "cveId": "CVE-2009-1547",
        
        "score": "9.3",
        
        "ipAddress": "10.35.1.51",
        
        "attackVector": "NETWORK",
        
        "description": "Unspecified vulnerability in Microsoft Internet Explorer 5.01
        
        SP4, 6, 6 SP1, and 7 allows remote attackers to execute
        
        arbitrary code via a crafted data stream header that triggers
        
        memory corruption, aka \"Data Stream Header Corruption
        
        Vulnerability.\""
    
    }

]
```

#### <a name="curl-command"></a>Curl 명령

| 유형 | API | 예제 |
|--|--|--|
| GET | `curl -k -H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/v1/devices/cves` | `curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https://127.0.0.1/api/v1/devices/cves` |
| GET | `curl -k -H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/v1/devices/<deviceIpAddress>/cves?top=` | `curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https://127.0.0.1/api/v1/devices/10.10.10.15/cves?top=50` |

### <a name="retrieve-alert-information---apiv1alerts"></a>경고 정보 검색-/api/v1/alerts

이 API를 사용하여 Defender for IoT 센서가 검색한 모든 경고 목록을 요청합니다.

#### <a name="method"></a>방법

- **GET**

#### <a name="query-parameters"></a>쿼리 매개 변수

- **상태**: 처리되거나 처리되지 않은 경고만 필터링합니다.

  **예제**:

  `/api/v1/alerts?state=handled`

- **fromtime**: 특정 시간(밀리초, UTC)에서 만든 경고를 필터링합니다.

  **예제**:

  `/api/v1/alerts?fromTime=<epoch>`

- **fromtime**: 특정 시간(밀리초, UTC) 이전에만 만든 경고를 필터링합니다.

  **예제**:

  `/api/v1/alerts?toTime=<epoch>`

- **유형**: 특정 유형에 따라 경고를 필터링합니다. 필터링할 기존 형식: 예기치 않은 새 디바이스, 연결 끊김.

  **예제**:

  `/api/v1/alerts?type=disconnections`

#### <a name="response-type"></a>응답 형식

- **JSON**

#### <a name="response-content"></a>응답 콘텐츠

경고를 나타내는 JSON 개체의 배열입니다.

#### <a name="alert-fields"></a>경고 필드

| 이름 | 유형 | Nullable | 값 목록 |
|--|--|--|--|
| **ID** | 숫자 | 예 | - |
| **time** | 숫자 | 예 | Epoch (UTC) |
| **title** | String | 예 | - |
| **message** | String | 예 | - |
| **severity** | String | 예 | 경고, 경미, 중대 또는 위험 |
| **엔진** | String | 예 | 프로토콜 위반, 정책 위반, 맬웨어, 변칙 또는 작동 |
| **sourceDevice** | 숫자 | 예 | 디바이스 ID |
| **destinationDevice** | 숫자 | 예 | 디바이스 ID |
| **sourceDeviceAddress** | 숫자 | 예 | IP, MAC |
| **destinationDeviceAddress** | 숫자 | 예 | IP, MAC |
| **remediationSteps** | String | 예 | 경고에 설명된 수정 단계 |
| **additionalInformation** | 추가 정보 개체 | 예 | - |

/api/v2/는 다음 정보에 필요합니다.

- sourceDeviceAddress
- destinationDeviceAddress
- remediationSteps

#### <a name="additional-information-fields"></a>추가 정보 필드

| 이름 | 유형 | Nullable | 값 목록 |
|--|--|--|--|
| **description** | String | 예 | - |
| **정보** | JSON 배열 | 예 | String |

#### <a name="response-example"></a>응답 예제

```rest
[

    {
    
        "engine": "Policy Violation",
        
        "severity": "Major",
        
        "title": "Internet Access Detected",
        
        "additionalinformation": {
        
            "information": [
            
                "170.60.50.201 over port BACnet (47808)"
            
            ],
            
            "description": "External Addresses"
        
        },
    
        "sourceDevice": null,
        
        "destinationDevice": null,
        
        "time": 1509881077000,
        
        "message": "Device 192.168.0.13 tried to access an external IP address which is an address in the Internet and is not allowed by policy. It is recommended to notify the security officer of the incident.",
        
        "id": 1
    
    },
    
    {
    
        "engine": "Protocol Violation",
        
        "severity": "Major",
        
        "title": "Illegal MODBUS Operation (Exception Raised by Master)",
        
        "sourceDevice": 3,
        
        "destinationDevice": 4,
        
        "time": 1505651605000,
        
        "message": "A MODBUS master 192.168.110.131 attempted to initiate an illegal operation.\nThe operation is considered to be illegal since it incorporated function code \#129 which should not be used by a master.\nIt is recommended to notify the security officer of the incident.",
        
        "id": 2,
        
        "additionalInformation": null,
    
    }

]

```

#### <a name="curl-command"></a>Curl 명령

> [!div class="mx-tdBreakAll"]
> | 유형 | API | 예제 |
> |--|--|--|
> | GET | `curl -k -H "Authorization: <AUTH_TOKEN>" 'https://<IP_ADDRESS>/api/v1/alerts?state=&fromTime=&toTime=&type='` | `curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" 'https://127.0.0.1/api/v1/alerts?state=unhandled&fromTime=1594550986000&toTime=1594550986001&type=disconnections'` |

### <a name="retrieve-timeline-events---apiv1events"></a>타임 라인 이벤트 검색-/api/v1/events

이 API를 사용하여 이벤트 타임 라인에 보고된 이벤트 목록을 요청합니다.

#### <a name="method"></a>방법

- **GET**

#### <a name="query-parameters"></a>쿼리 매개 변수

- **minutesTimeFrame**: 이전부터 지금까지 이벤트가 보고된 시간 프레임(분).

  **예제**:

  `/api/v1/events?minutesTimeFrame=20`

- **형식**: 특정 형식으로 이벤트 목록을 필터링합니다.

  **예**:

  `/api/v1/events?type=DEVICE_CONNECTION_CREATED`

  `/api/v1/events?type=REMOTE_ACCESS&minutesTimeFrame`

#### <a name="response-type"></a>응답 형식

- **JSON**

#### <a name="response-content"></a>응답 콘텐츠

경고를 나타내는 JSON 개체의 배열입니다.

#### <a name="event-fields"></a>이벤트 필드

| 이름 | 유형 | Nullable | 값 목록 |
|--|--|--|--|--|
| **timestamp** | 숫자 | 예 | Epoch (UTC) |
| **title** | String | 예 | - |
| **severity** | String | 예 | 정보, 알림 또는 경고 |
| **소유자** | String | 예 | 이벤트를 수동으로 만든 경우이 필드에는 이벤트를 만든 사용자 이름이 포함됩니다 |
| **content** | String | 예 | - |

#### <a name="response-example"></a>응답 예제

```rest
[

    {
    
        "severity": "INFO",
        
        "title": "Back to Normal",
        
        "timestamp": 1504097077000,
        
        "content": "Device 10.2.1.15 was found responsive, after being suspected as disconnected",
        
        "owner": null,
        
        "type": "BACK_TO_NORMAL"
    
    },
    
    {
    
        "severity": "ALERT",
        
        "title": "Alert Detected",
        
        "timestamp": 1504096909000,
        
        "content": "Device 10.2.1.15 is suspected to be disconnected (unresponsive).",
        
        "owner": null,
        
        "type": "ALERT_REPORTED"
    
    },
    
    {
    
        "severity": "ALERT",
        
        "title": "Alert Detected",
        
        "timestamp": 1504094446000,
        
        "content": "A DNP3 Master 10.2.1.14 attempted to initiate a request which is not allowed by policy.\nThe policy indicates the allowed function codes, address ranges, point indexes and time intervals.\nIt is recommended to notify the security officer of the incident.",
        
        "owner": null,
        
        "type": "ALERT_REPORTED"
    
    },
    
    {
    
        "severity": "NOTICE",
        
        "title": "PLC Program Update",
        
        "timestamp": 1504094344000,
        
        "content": "Program update detected, sent from 10.2.1.25 to 10.2.1.14",
        
        "owner": null,
        
        "type": "PROGRAM_DEVICE"
    
    }

]

```

#### <a name="curl-command"></a>Curl 명령

| 유형 | API | 예제 |
|--|--|--|
| GET | `curl -k -H "Authorization: <AUTH_TOKEN>" 'https://<IP_ADDRESS>/api/v1/events?minutesTimeFrame=&type='` | `curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" 'https://127.0.0.1/api/v1/events?minutesTimeFrame=20&type=DEVICE_CONNECTION_CREATED'` |

### <a name="retrieve-vulnerability-information---apiv1reportsvulnerabilitiesdevices"></a>취약점 정보 검색-/api/v1/reports/vulnerabilities/devices

이 API를 사용하여 각 디바이스에 대한 취약성 평가 결과를 요청합니다.

#### <a name="method"></a>방법

- **GET**

#### <a name="response-type"></a>응답 형식

- **JSON**

#### <a name="response-content"></a>응답 콘텐츠

평가된 디바이스를 나타내는 JSON 개체의 배열입니다.

디바이스 개체는 다음을 포함합니다.

- 일반 데이터

- 평가 점수

- 취약점

#### <a name="device-fields"></a>디바이스 필드

| 이름 | 유형 | Nullable | 값 목록 |
|--|--|--|--|
| **name** | String | 예 | - |
| **ipAddresses** | JSON 배열 | 예 | - |
| **securityScore** | 숫자 | 예 | - |
| **공급업체** | String | 예 |  |
| **firmwareVersion** | String | 예 | - |
| **model** | String | 예 | - |
| **isWirelessAccessPoint** | 부울 | 예 | True 또는 False |
| **operatingSystem** | 운영 체제 개체 | 예 | - |
| **취약성** | 취약성 개체 | 예 | - |

#### <a name="operating-system-fields"></a>운영 체제 필드

| 이름 | 유형 | Nullable | 값 목록 |
|--|--|--|--|
| **이름** | String | 예 | - |
| **형식** | String | 예 | - |
| **Version** | String | 예 | - |
| **latestVersion** | String | 예 | - |

#### <a name="vulnerabilities-fields"></a>취약성 필드

| 이름 | 유형 | Nullable | 값 목록 |
|--|--|--|--|
| **antiViruses** | JSON 배열 | 예 | 바이러스 백신 이름 |
| **plainTextPasswords** | JSON 배열 | 예 | 암호 개체 |
| **원격 액세스** | JSON 배열 | 예 | 원격 액세스 개체 |
| **isBackupServer** | 부울 | 예 | True 또는 False |
| **openedPorts** | JSON 배열 | 예 | 열린 포트 개체 |
| **isEngineeringStation** | 부울 | 예 | True 또는 False |
| **isKnownScanner** | 부울 | 예 | True 또는 False |
| **CVE** | JSON 배열 | 예 | CVE 개체 |
| **isUnauthorized** | 부울 | 예 | True 또는 False |
| **malwareIndicationsDetected** | 부울 | 예 | True 또는 False |
| **weakAuthentication** | JSON 배열 | 예 | 약한 인증을 사용하는 검색된 애플리케이션 |

#### <a name="password-fields"></a>암호 필드

| 이름 | 유형 | Nullable | 값 목록 |
|--|--|--|--|
| **password** | String | 예 | - |
| **protocol** | String | 예 | - |
| **strength** | String | 예 | 매우 약함, 약함, 중간 또는 강함 |

#### <a name="remote-access-fields"></a>원격 액세스 필드

| 이름 | 유형 | Nullable | 값 목록 |
|--|--|--|--|
| **port** | 숫자 | 예 | - |
| **전송** | String | 예 | TCP 또는 UDP |
| **client** | String | 예 | IP 주소 |
| **클라이언트 소프트웨어** | String | 예 | SSH, VNC, 원격 데스크톱 또는 팀 뷰어 |

#### <a name="open-port-fields"></a>포트 필드 열기

| 이름 | 유형 | Nullable | 값 목록 |
|--|--|--|--|
| **port** | 숫자 | 예 | - |
| **전송** | String | 예 | TCP 또는 UDP |
| **protocol** | String | 예 | - |
| **isConflictingWithFirewall** | 부울 | 예 | True 또는 False |

#### <a name="cve-fields"></a>CVE 필드

| 이름 | 유형 | Nullable | 값 목록 |
|--|--|--|--|
| **ID** | String | 예 | - |
| **점수** | 숫자 | 예 | Double |
| **description** | String | 예 | - |

#### <a name="response-example"></a>응답 예제

```rest
[

    {
    
        "name": "IED \#10",
        
        "ipAddresses": ["10.2.1.10"],
        
        "securityScore": 100,
        
        "vendor": "ABB Switzerland Ltd, Power Systems",
        
        "firmwareVersion": null,
        
        "model": null,
        
        "operatingSystem": {
        
            "name": "ABB Switzerland Ltd, Power Systems",
            
            "type": "abb",
            
            "version": null,
            
            "latestVersion": null
        
        },
        
        "vulnerabilities": {
            
        "antiViruses": [
            
        "McAfee"
            
        ],
            
        "plainTextPasswords": [
            
            {
            
                "password": "123456",
                
                "protocol": "HTTP",
                
                "lastSeen": 1462726930471,
                
                "strength": "Very Weak"
            
            }
            
        ],
            
        "remoteAccess": [
            
            {
            
                "port": 5900,
                
                "transport": "TCP",
                
                "clientSoftware": "VNC",
                
                "client": "10.2.1.20"
            
            }
            
        ],
            
        "isBackupServer": true,
            
        "openedPorts": [
            
            {
            
                "port": 445,
                
                "transport": "TCP",
                
                "protocol": "SMP Over IP",
                
                "isConflictingWithFirewall": false
            
            },
            
            {
            
                "port": 80,
                
                "transport": "TCP",
                
                "protocol": "HTTP",
                
                "isConflictingWithFirewall": false
            
            }
            
        ],
            
        "isEngineeringStation": false,
            
        "isKnownScanner": false,
            
        "cves": [
            
            {
            
                "id": "CVE-2015-6490",
                
                "score": 10,
                
                "description": "Frosty URL - Stack-based buffer overflow on Allen-Bradley MicroLogix 1100 devices before B FRN 15.000 and 1400 devices through B FRN 15.003 allows remote attackers to execute arbitrary code via unspecified vectors"
            
            },
            
            {
            
                "id": "CVE-2012-6437",
                
                "score": 10,
                
                "description": "MicroLogix 1100 and 1400 do not properly perform authentication for Ethernet firmware updates, which allows remote attackers to execute arbitrary code via a Trojan horse update image"
            
            },
            
            {
            
                "id": "CVE-2012-6440",
                
                "score": 9.3,
                
                "description": "MicroLogix 1100 and 1400 allows man-in-the-middle attackers to conduct replay attacks via HTTP traffic."
        
            }
        
        ],
        
        "isUnauthorized": false,
        
        "malwareIndicationsDetected": true
        
        }
    
    }

]
```

#### <a name="curl-command"></a>Curl 명령

| 유형 | API | 예제 |
|--|--|--|
| GET | `curl -k -H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/v1/reports/vulnerabilities/devices` | `curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https://127.0.0.1/api/v1/reports/vulnerabilities/devices` |

### <a name="retrieve-security-vulnerabilities---apiv1reportsvulnerabilitiessecurity"></a>보안 취약점 검색-/api/v1/reports/vulnerabilities/security

이 API를 사용하여 일반적인 취약성 평가 결과를 요청합니다. 이 평가는 시스템의 보안 수준에 대한 인사이트를 제공합니다.

이 평가는 특정 디바이스 평가가 아니라 일반적인 네트워크 및 시스템 정보를 기반으로 합니다.

#### <a name="method"></a>방법

- **GET**

#### <a name="response-type"></a>응답 형식

- **JSON**

#### <a name="response-content"></a>응답 콘텐츠

평가된 결과를 나타내는 JSON 개체입니다. 각 키는 null 허용일 수 있습니다. 그렇지 않은 경우 null을 허용하지 않는 키를 포함하는 JSON 개체를 포함합니다.

#### <a name="result-fields"></a>결과 필드

- **키**

    **unauthorizedDevices**

    | 필드 이름 | Type | 값 목록 |
    | ---------- | ---- | -------------- |
    | **address** | String | IP 주소 |
    | **name** | String | - |
    | **firstDetectionTime** | 숫자 | Epoch (UTC) |
    | LastSeen | 숫자 | Epoch (UTC) |

    **illegalTrafficByFirewallRules**

    | 필드 이름 | Type | 값 목록 |
    | ---------- | ---- | -------------- |
    | **server** | String | IP 주소 |
    | **client** | String | IP 주소 |
    | **port** | 숫자 | - |
    | **전송** | String | TCP, UDP 또는 ICMP |

    **weakFirewallRules**

    | 필드 이름 | Type | 값 목록 |
    | ---------- | ---- | -------------- |
    | **sources** | 원본의 JSON 배열. 각 소스는 네 가지 형식 중 하나일 수 있습니다. | "모든", "ip 주소(호스트)", "ip에서 ip로(범위)", "ip 주소, 서브넷 마스크(네트워크)" |
    | **대상** | 대상의 JSON 배열. 각 대상은 네 가지 형식 중 하나일 수 있습니다. | "모든", "ip 주소(호스트)", "ip에서 ip로(범위)", "ip 주소, 서브넷 마스크(네트워크)" |
    | **포트** | 3가지 형식으로 된 포트의 JSON 배열 | "모든", "포트(검색된 경우 프로토콜)", "포트-포트(검색된 경우 프로토콜)" |

    **accessPoints**

    | 필드 이름 | Type | 값 목록 |
    | ---------- | ---- | -------------- |
    | **macAddress** | String | MAC 주소 |
    | **공급업체** | String | 공급업체 이름 |
    | **ipAddress** | String | IP 주소 또는 해당 없음 |
    | **name** | String | 디바이스 이름 또는 해당 없음 |
    | **무선** | String | 아니요, 의심됨 또는 예 |

    **connectionsBetweenSubnets**

    | 필드 이름 | Type | 값 목록 |
    | ---------- | ---- | -------------- |
    | **server** | String | IP 주소 |
    | **client** | String | IP 주소 |

    **industrialMalwareIndicators**

    | 필드 이름 | Type | 값 목록 |
    | ---------- | ---- | -------------- |
    | **detectionTime** | 숫자 | Epoch (UTC) |
    | **alertMessage** | String | - |
    | **description** | String | - |
    | **디바이스** | JSON 배열 | 디바이스 이름 |

    **internetConnections**

    | 필드 이름 | Type | 값 목록 |
    | ---------- | ---- | -------------- |
    | **internalAddress** | String | IP 주소 |
    | **승인됨** | 부울 | Yes 또는 No |
    | **externalAddresses** | JSON 배열 | IP 주소 |

#### <a name="response-example"></a>응답 예제

```rest
{

    "unauthorizedDevices": [
    
        {
        
            "address": "10.2.1.14",
            
            "name": "PLC \#14",
            
            "firstDetectionTime": 1462645483000,
            
            "lastSeen": 1462645495000,
        
        }
    
    ],
    
    "redundantFirewallRules": [
    
        {
        
            "sources": "170.39.3.0/255.255.255.0",
            
            "destinations": "Any",
            
            "ports": "102"
        
        }
    
    ],
    
    "connectionsBetweenSubnets": [
    
        {
        
            "server": "10.2.1.22",
            
            "client": "170.39.2.0"
        
        }
    
    ],
    
    "industrialMalwareIndications": [
    
        {
        
            "detectionTime": 1462645483000,
            
            "alertMessage": "Suspicion of Malicious Activity (Regin)",
            
            "description": "Suspicious network activity was detected. Such behavior might be attributed to the Regin malware.",
            
            "addresses": [
            
                "10.2.1.4",
                
                "10.2.1.5"
            
            ]
        
        }
    
    ],
    
    "illegalTrafficByFirewallRules": [
    
        {
        
            "server": "10.2.1.7",
            
            "port": "20000",
            
            "client": "10.2.1.4",
            
            "transport": "TCP"
        
        },
    
        {
        
            "server": "10.2.1.8",
            
            "port": "20000",
            
            "client": "10.2.1.4",
            
            "transport": "TCP"
        
        },
    
        {
        
            "server": "10.2.1.9",
            
            "port": "20000",
            
            "client": "10.2.1.4",
            
            "transport": "TCP"
        
        }
    
    ],
    
    "internetConnections": [
    
        {
        
            "internalAddress": "10.2.1.1",
            
            "authorized": "Yes",
            
            "externalAddresses": ["10.2.1.2",”10.2.1.3”]
        
        }
    
    ],
    
    "accessPoints": [
    
        {
        
            "macAddress": "ec:08:6b:0f:1e:22",
            
            "vendor": "TP-LINK TECHNOLOGIES",
            
            "ipAddress": "173.194.112.22",
            
            "name": "Enterprise AP",
            
            "wireless": "Yes"
        
        }
    
    ],
    
    "weakFirewallRules": [
    
        {
        
            "sources": "170.39.3.0/255.255.255.0",
            
            "destinations": "Any",
            
            "ports": "102"
        
        }
    
    ]

}
```

#### <a name="curl-command"></a>Curl 명령

| 유형 | API | 예제 |
|--|--|--|
| GET | `curl -k -H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/v1/reports/vulnerabilities/security` | `curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https://127.0.0.1/api/v1/reports/vulnerabilities/security` |

### <a name="retrieve-operational-vulnerabilities---apiv1reportsvulnerabilitiesoperational"></a>운영 취약점 검색-/api/v1/reports/vulnerabilities/operational

이 API를 사용하여 일반적인 취약성 평가 결과를 요청합니다. 이 평가는 네트워크의 작동 상태에 대한 인사이트를 제공합니다. 이 평가는 특정 디바이스 평가가 아니라 일반적인 네트워크 및 시스템 정보를 기반으로 합니다.

#### <a name="method"></a>방법

- **GET**

#### <a name="response-type"></a>응답 형식

- **JSON**

#### <a name="response-content"></a>응답 콘텐츠

평가된 결과를 나타내는 JSON 개체입니다. 각 키에는 결과의 JSON 배열이 포함되어 있습니다.

#### <a name="result-fields"></a>결과 필드

- **키**

    **backupServer**

    | 필드 이름 | Type | 값 목록 |
    |--|--|--|
    | **source** | String | IP 주소 |
    | **destination** | String | IP 주소 |
    | **port** | 숫자 | - |
    | **전송** | String | TCP 또는 UDP |
    | **backupMaximalInterval** | String | - |
    | **lastSeenBackup** | 숫자 | Epoch (UTC) |

    **ipNetworks**

    | 필드 이름 | Type | 값 목록 |
    |--|--|--|
    | **주소** 들 | 숫자 | - |
    | **network** | String | IP 주소 |
    | **마스크** | String | 서브넷 마스크 |

    **protocolProblems**

    | 필드 이름 | Type | 값 목록 |
    |--|--|--|
    | **protocol** | String | - |
    | **addresses** | JSON 배열 | IP 주소 |
    | **경고** | String | - |
    | **reportTime** | 숫자 | Epoch (UTC) |

    **protocolDataVolumes**

    | 필드 이름 | Type | 값 목록 |
    |--|--|--|
    | protocol | String | - |
    | 볼륨 | String | "볼륨 번호 MB" |

    **연결 끊김**

    | 필드 이름 | Type | 값 목록 |
    |--|--|--|
    | **assetAddress** | String | IP 주소 |
    | **assetName** | String | - |
    | **lastDetectionTime** | 숫자 | Epoch (UTC) |
    | **backToNormalTime** | 숫자 | Epoch (UTC) |

#### <a name="response-example"></a>응답 예제

```rest
{

    "backupServer": [
    
        {
        
            "backupMaximalInterval": "1 Hour, 29 Minutes",
            
            "source": "10.2.1.22",
            
            "destination": "170.39.2.14",
            
            "port": 10000,
            
            "transport": "TCP",
            
            "lastSeenBackup": 1462645483000
        
        }
    
    ],

    "ipNetworks": [
    
        {
        
            "addresses": "21",
            
            "network": "10.2.1.0",
            
            "mask": "255.255.255.0"
        
        },
        
        {
        
            "addresses": "3",
            
            "network": "170.39.2.0",
            
            "mask": "255.255.255.0"
        
        }
    
    ],

    "protocolProblems": [
    
        {
        
            "protocol": "DNP3",
            
            "addresses": [
            
                "10.2.1.7",
                
                "10.2.1.8"
            
            ],
            
            "alert": "Illegal DNP3 Operation",
            
            "reportTime": 1462645483000
        
        },
        
        {
        
            "protocol": "DNP3",
            
            "addresses": [
            
                "10.2.1.15"
            
            ],
            
            "alert": "Master Requested an Application Layer Confirmation",
            
            "reportTime": 1462645483000
        
        }
        
    ],

    "protocolDataVolumes": [
    
        {
        
            "protocol": "MODBUS (502)",
            
            "volume": "21.07 MB"
        
        },
        
        {
        
            "protocol": "SSH (22)",
            
            "volumn": "0.001 MB"
        
        }
    
    ],
    
    "disconnections": [
    
        {
        
            "assetAddress": "10.2.1.3",
            
            "assetName": "PLC \#3",
            
            "lastDetectionTime": 1462645483000,
            
            "backToNormalTime": 1462645484000
        
        }
    
    ]

}
```

#### <a name="curl-command"></a>Curl 명령

| 유형 | API | 예제 |
|--|--|--|
| GET | `curl -k -H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/v1/reports/vulnerabilities/operational` | `curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https://127.0.0.1/api/v1/reports/vulnerabilities/operational` |

### <a name="retrieve-alert-pcap---apiv2alertspcap"></a>경고 PCAP 검색 - /api/v2/alerts/pcap

이 API를 사용하여 경고와 관련된 PCAP 파일을 검색합니다.

이 엔드포인트는 권한 부여를 위해 일반 액세스 토큰을 사용하지 않습니다. 대신 `/external/v2/alerts/pcap` CM의 API 엔드포인트에서 만든 특수 토큰이 필요합니다.

#### <a name="method"></a>방법

- **GET**

#### <a name="query-parameters"></a>쿼리 매개 변수

- id: Xsense 경고 ID  
예:  
`/api/v2/alerts/pcap/<id>`

#### <a name="response-type"></a>응답 형식

- **JSON**

#### <a name="response-content"></a>응답 콘텐츠

- **성공:** PCAP 데이터를 포함하는 이진 파일
- **실패:** 오류 메시지를 포함하는 JSON 개체

#### <a name="response-example"></a>응답 예제

#### <a name="error"></a>오류

```json
{
  "error": "PCAP file is not available"
}
```

#### <a name="curl-command"></a>Curl 명령

|유형|API|예제|
|-|-|-|
|GET|`curl -k -H "Authorization: <AUTH_TOKEN>" 'https://<IP_ADDRESS>/api/v2/alerts/pcap/<ID>'`|`curl -k -H "Authorization: d2791f58-2a88-34fd-ae5c-2651fe30a63c" 'https://10.1.0.2/api/v2/alerts/pcap/1'`|

## <a name="on-premises-management-console-api-specifications"></a>온-프레미스 관리 콘솔 API 사양

이 섹션에서는 다음에 대한 온-프레미스 관리 콘솔 API에 대해 설명합니다.

### <a name="no-version"></a>버전 없음

- [경고 제외](#alert-exclusions)

- [암호 변경-/external/authentication/set_password](#change-password---externalauthenticationset_password-1)

- [시스템 관리자에 의한 사용자 암호 업데이트-/external/authentication/set_password_by_admin](#user-password-update-by-system-admin---externalauthenticationset_password_by_admin)

- [QRadar 경고](#qradar-alerts)

- [사용자 자격 증명 인증-/external/authentication/validation](#authenticate-user-credentials---externalauthenticationvalidation)

### <a name="version-1"></a>버전 1

- [디바이스 정보 검색-/api/v1/devices](#retrieve-device-information---apiv1devices)

- [디바이스 정보 검색-/external/v1/devices](#retrieve-device-information---externalv1devices)

- [경고 정보 검색-/external/v1/alerts](#retrieve-alert-information---externalv1alerts)

- [경고 제외(유지 관리 기간)-/external/v1/maintenanceWindow](#alert-exclusions-maintenance-window---externalv1maintenancewindow)

### <a name="version-2"></a>버전 2

- [경고 PCAP 요청 - /external/v2/alerts/pcap](#request-alert-pcap---externalv2alertspcap)

### <a name="version-3"></a>버전 3

- [ServiceNow 통합 API - "/external/v3/integration/(미리 보기)](#servicenow-integration-api---externalv3integration-preview)

### <a name="alert-exclusions"></a>경고 제외

경고를 보내지 않을 조건을 정의합니다. 예를 들어 중지 및 시작 시간, 경고를 트리거할 때 제외되어야 하는 디바이스 또는 서브넷, 제외되어야 하는 Defender for IoT 엔진을 정의하고 업데이트합니다. 예를 들어 유지 관리 기간 동안 중요한 디바이스에 대한 맬웨어 경고를 제외하고 모든 경고의 경고 제공을 중지할 수 있습니다. 여기서 정의한 항목은 온-프레미스 관리 콘솔의 경고 제외 창에 읽기 전용 제외 규칙으로 표시됩니다.

#### <a name="externalv1maintenancewindow"></a>/external/v1/maintenanceWindow

- **/external/authentication/validation**

- **응답 예제**

- **응답:**

```rest
{
    "msg": "Authentication succeeded."
}
```

### <a name="change-password---externalauthenticationset_password"></a>암호 변경-/external/authentication/set_password

이 API를 사용하여 사용자가 자신의 암호를 변경할 수 있습니다. 모든 Defender for IoT 사용자 역할은 API와 함께 작동할 수 있습니다. 이 API를 사용하는 데 Defender for IoT 액세스 토큰이 필요하지 않습니다.

#### <a name="method"></a>방법

**POST**

#### <a name="request-type"></a>요청 유형

- **JSON**

#### <a name="request-example"></a>요청 예제

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!",
    
    "new_password": "Test54321\!"

}

```

#### <a name="response-type"></a>응답 형식

- **JSON**

#### <a name="response-content"></a>응답 콘텐츠

작업 상태 세부 정보를 포함하는 메시지 문자열:

- **성공 – 메시지**: 암호가 바뀌었습니다

- **실패 - 오류**: 사용자 인증 실패

- **실패 – 오류**: 암호가 보안 정책과 일치하지 않습니다

#### <a name="response-example"></a>응답 예제

```rest
response:

{

    "error": {
    
        "userDisplayErrorMessage": "User authentication failure"
    
    }

}

```

#### <a name="device-fields"></a>디바이스 필드

| **이름** | **형식** | **Null 허용** |
|--|--|--|
| **username** | String | 예 |
| **password** | String | 예 |
| **new_password** | String | 예 |

#### <a name="curl-command"></a>Curl 명령

| 유형 | API | 예제 |
|--|--|--|
| POST | `curl -k -d '{"username": "<USER_NAME>","password": "<CURRENT_PASSWORD>","new_password": "<NEW_PASSWORD>"}' -H 'Content-Type: application/json'  https://<IP_ADDRESS>/external/authentication/set_password` | `curl -k -d '{"username": "myUser","password": "1234@abcd","new_password": "abcd@1234"}' -H 'Content-Type: application/json'  https://127.0.0.1/external/authentication/set_password` |

### <a name="user-password-update-by-system-admin---externalauthenticationset_password_by_admin"></a>시스템 관리자에 의한 사용자 암호 업데이트-/external/authentication/set_password_by_admin

이 API를 사용하여 시스템 관리자가 지정된 사용자에 대한 암호를 변경할 수 있습니다. 모든 Defender for IoT 관리 사용자 역할은 API로 작업할 수 있습니다. 이 API를 사용하는 데 Defender for IoT 액세스 토큰이 필요하지 않습니다.

#### <a name="method"></a>방법

- **POST**

#### <a name="request-type"></a>요청 유형

- **JSON**

#### <a name="request-example"></a>요청 예제

```rest
request:

{

    "username": "test",
    
    "password": "Test12345\!",
    
    "new_password": "Test54321\!"

}
```

#### <a name="response-type"></a>응답 형식

- **JSON**

#### <a name="response-content"></a>응답 콘텐츠

작업 상태 세부 정보를 포함하는 메시지 문자열:

- **성공 – 메시지**: 암호가 바뀌었습니다

- **실패 - 오류**: 사용자 인증 실패

- **실패-오류**: 사용자가 없습니다

- **실패 – 오류**: 암호가 보안 정책과 일치하지 않습니다

- **실패 – 오류**: 사용자에게 암호 변경 권한이 없습니다

#### <a name="response-example"></a>응답 예제

```rest
response:

{

    "error": {
    
        "userDisplayErrorMessage": "The user 'test_user' doesn't exist",
        
        "internalSystemErrorMessage": "The user 'yoavfe' doesn't exist"
    
    }

}

```

#### <a name="device-fields"></a>디바이스 필드

| **이름** | **형식** | **Null 허용** |
|--|--|--|
| **관리자 사용자 이름** | String | 예 |
| **관리자 암호** | String | 예 |
| **username** | String | 예 |
| **new_password** | String | 예 |

#### <a name="curl-command"></a>Curl 명령

> [!div class="mx-tdBreakAll"]
> | 유형 | API | 예제 |
> |--|--|--|
> | POST | `curl -k -d '{"admin_username":"<ADMIN_USERNAME>","admin_password":"<ADMIN_PASSWORD>","username": "<USER_NAME>","new_password": "<NEW_PASSWORD>"}' -H 'Content-Type: application/json'  https://<IP_ADDRESS>/external/authentication/set_password_by_admin` | `curl -k -d '{"admin_user":"adminUser","admin_password": "1234@abcd","username": "myUser","new_password": "abcd@1234"}' -H 'Content-Type: application/json'  https://127.0.0.1/external/authentication/set_password_by_admin` |

### <a name="qradar-alerts"></a>QRadar 경고

QRadar와 Defender for IoT 통합은 Defender for IoT에서 생성된 경고를 식별하고 이러한 경고를 사용하여 작업을 수행하는 데 도움이 됩니다. QRadar는 Defender for IoT에서 데이터를 받은 다음 공용 API 온-프레미스 관리 콘솔 구성 요소에 연결합니다.

Defender for IoT에서 발견한 데이터를 QRadar에 보내려면 Defender for IoT 시스템에서 전달 규칙을 정의하고 **원격 지원 경고 처리** 옵션을 선택합니다.

:::image type="content" source="media/references-work-with-defender-for-iot-apis/edit-forwarding-rules.png" alt-text="필요에 맞게 전달 규칙을 편집합니다.":::

전달 규칙을 구성하는 과정에서 이 옵션을 선택하면 다음과 같은 추가 필드가 QRadar에 표시됩니다.

- **UUID**: 고유한 경고 식별자(예: 1-1555245116250)입니다.

- **사이트**: 경고가 검색된 사이트입니다.

- **사이트**: 경고가 검색된 영역입니다.

QRadar으로 전송된 페이로드의 예제는 다음과 같습니다.

```
<9>May 5 12:29:23 sensor_Agent LEEF:1.0|CyberX|CyberX platform|2.5.0|CyberX platform Alert|devTime=May 05 2019 15:28:54 devTimeFormat=MMM dd yyyy HH:mm:ss sev=2 cat=XSense Alerts title=Device is Suspected to be Disconnected (Unresponsive) score=81 reporter=192.168.219.50 rta=0 alertId=6 engine=Operational senderName=sensor Agent UUID=5-1557059334000 site=Site zone=Zone actions=handle dst=192.168.2.2 dstName=192.168.2.2 msg=Device 192.168.2.2 is suspected to be disconnected (unresponsive).
```

### <a name="retrieve-device-information---apiv1devices"></a>디바이스 정보 검색-/api/v1/devices

이 API를 사용하여 Defender for IoT 센서가 검색한 모든 디바이스 목록을 요청합니다.

#### <a name="method"></a>방법

- **GET**

Defender for IoT 센서가 검색한 모든 디바이스 목록을 요청합니다.

#### <a name="query-parameters"></a>쿼리 매개 변수

- **권한 부여됨**: 허가되고 권한이 없는 디바이스만 필터링합니다.

  **예**:

  `/api/v1/devices?authorized=true`

  `/api/v1/devices?authorized=false`

#### <a name="response-type"></a>응답 형식

- **JSON**

#### <a name="response-content"></a>응답 콘텐츠

디바이스를 나타내는 JSON 개체의 배열입니다.

#### <a name="device-fields"></a>디바이스 필드

| 이름 | 유형 | Nullable | 값 목록 |
|--|--|--|--|
| **id** | 숫자 | 예 | - |
| **ipAddresses** | JSON 배열 | 예 | IP 주소(인터넷 주소 또는 이중 NIC를 사용하는 디바이스의 경우 두 개 이상의 주소를 사용할 수 있음) |
| **name** | String | 예 | - |
| **type** | String | 예 | 알 수 없음, 엔지니어링 스테이션, PLC, HMI, Historian, 도메인 컨트롤러, DB 서버, 무선 액세스 지점, 라우터, 스위치, 서버, 워크스테이션, IP 카메라, 프린터, 방화벽, 터미널 스테이션, VPN Gateway, 인터넷 또는 멀티 캐스트 및 브로드캐스트 |
| **macAddresses** | JSON 배열 | 예 | MAC 주소(이중 NIC를 사용하는 디바이스의 경우 두 개 이상의 주소를 사용할 수 있음) |
| **operatingSystem** | String | 예 | - |
| **engineeringStation** | 부울 | 예 | True 또는 False |
| **스캐너** | 부울 | 예 | True 또는 False |
| **승인됨** | 부울 | 예 | True 또는 False |
| **공급업체** | String | 예 | - |
| **프로토콜** | JSON 배열 | 예 | 프로토콜 개체 |
| **펌웨어** | JSON 배열 | 예 | 펌웨어 개체 |

#### <a name="protocol-fields"></a>프로토콜 필드

| 이름 | 유형 | Nullable | 값 목록 |
|--|--|--|--|
| **이름** | String | 예 |  |
| **주소** | JSON 배열 | 예 | Master 또는 숫자 값 |

#### <a name="firmware-fields"></a>펌웨어 필드

| 이름 | 유형 | Nullable | 값 목록 |
|--|--|--|--|
| **serial** | String | 예 | 해당 없음 또는 실제 값 |
| **model** | String | 예 | 해당 없음 또는 실제 값 |
| **firmwareVersion** | Double | 예 | 해당 없음 또는 실제 값 |
| **additionalData** | String | 예 | 해당 없음 또는 실제 값 |
| **moduleAddress** | String | 예 | 해당 없음 또는 실제 값 |
| **랙** | String | 예 | 해당 없음 또는 실제 값 |
| **슬롯** | String | 예 | 해당 없음 또는 실제 값 |
| **address** | String | 예 | 해당 없음 또는 실제 값 |

#### <a name="response-example"></a>응답 예제

```rest
[

    {
    
    "vendor": null,
    
    "name": "10.4.14.102",
    
    "firmware": [
    
        {
        
            "slot": "N/A",
            
            "additionalData": "N/A",
            
            "moduleAddress": "Network: Local network (0), Node: 0, Unit: CPU (0x0)",
            
            "rack": "N/A",
            
            "address": "10.4.14.102",
            
            "model": "AAAAAAAAAA",
            
            "serial": "N/A",
            
            "firmwareVersion": "20.55"
        
        },
    
        {
        
            "slot": "N/A",
            
            "additionalData": "N/A",
            
            "moduleAddress": "Network: Local network (0), Node: 0, Unit: Unknown (0x3)",
            
            "rack": "N/A",
            
            "address": "10.4.14.102",
            
            "model": "AAAAAAAAAAAAAAAAAAAA",
            
            "serial": "N/A",
            
            "firmwareVersion": "20.55"
        
        },
    
        {
        
            "slot": "N/A",
            
            "additionalData": "N/A",
            
            "moduleAddress": "Network: Local network (0), Node: 3, Unit: CPU (0x0)",
            
            "rack": "N/A",
            
            "address": "10.4.14.102",
            
            "model": "AAAAAAAAAAAAAAAAAAAA",
            
            "serial": "N/A",
            
            "firmwareVersion": "20.55"
        
        },
    
        {
        
            "slot": "N/A",
            
            "additionalData": "N/A",
            
            "moduleAddress": "Network: 3, Node: 0, Unit: CPU (0x0)",
            
            "rack": "N/A",
            
            "address": "10.4.14.102",
            
            "model": "AAAAAAAAAAAAAAAAAAAA",
            
            "serial": "N/A",
            
            "firmwareVersion": "20.55"
        
        }
    
    ],
    
    "id": 79,
    
    "macAddresses": null,
    
    "authorized": true,
    
    "ipAddresses": [
    
        "10.4.14.102"
    
    ],
    
    "engineeringStation": false,
    
    "type": "PLC",
    
    "operatingSystem": null,
    
    "protocols": [
    
        {
        
            "addresses": [],
            
            "id": 62,
            
            "name": "Omron FINS"
        
        }
    
    ],
    
    "scanner": false
    
}

]
```

#### <a name="curl-command"></a>Curl 명령

| 유형 | API | 예제 |
|--|--|--|
| GET | `curl -k -H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/api/v1/devices` | `curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https://127.0.0.1/api/v1/devices?authorized=true` |

### <a name="authenticate-user-credentials---externalauthenticationvalidation"></a>사용자 자격 증명 인증-/external/authentication/validation

이 API를 사용하여 사용자 자격 증명의 유효성을 검사합니다. 모든 Defender for IoT 사용자 역할은 API와 함께 작동할 수 있습니다. 이 API를 사용하는 데 Defender for IoT 액세스 토큰이 필요하지 않습니다.

#### <a name="method"></a>방법

**POST**

#### <a name="request-type"></a>요청 유형

- **JSON**

#### <a name="request-example"></a>요청 예제

```rest
request:

{

    "username": "test",

    "password": "Test12345\!"

}
```

#### <a name="response-type"></a>응답 형식

- **JSON**

#### <a name="response-content"></a>응답 콘텐츠

작업 상태 세부 정보를 포함하는 메시지 문자열:

- **성공 - 메시지**: 인증 성공

- **실패 - 오류**: 자격 증명 유효성 검사에 실패했습니다

#### <a name="device-fields"></a>디바이스 필드

| **이름** | **형식** | **Null 허용** |
|--|--|--|
| **username** | String | 예 |
| **password** | String | 예 |

#### <a name="response-example"></a>응답 예제

```rest
response:

{

    "msg": "Authentication succeeded."

}
```

#### <a name="curl-command"></a>Curl 명령

| 유형 | API | 예제 |
|--|--|--|
| POST | `curl -k -d '{"username":"<USER_NAME>","password":"PASSWORD"}' 'https://<IP_ADDRESS>/external/authentication/validation'` | `curl -k -d '{"username":"myUser","password":"1234@abcd"}' 'https://127.0.0.1/external/authentication/validation'` |

### <a name="retrieve-device-information---externalv1devices"></a>디바이스 정보 검색-/external/v1/devices

이 API는 온-프레미스 관리 콘솔에 연결된 Defender for IoT 센서에서 검색한 모든 디바이스 목록을 요청합니다.

#### <a name="method"></a>방법

- **GET**

#### <a name="response-type"></a>응답 형식

- **JSON**

#### <a name="response-content"></a>응답 콘텐츠

디바이스를 나타내는 JSON 개체의 배열입니다.

#### <a name="query-parameters"></a>쿼리 매개 변수

- **권한 부여됨**: 허가되고 권한이 없는 디바이스만 필터링합니다.

- **siteId**: 특정 사이트와 관련된 디바이스만 필터링합니다.

- **siteId**: 특정 영역과 관련된 디바이스만 필터링합니다. [1](#1)

- **siteId**: 특정 센서와 관련된 디바이스만 필터링합니다. [1](#1)

###### <a name="you-might-not-have-the-site-and-zone-id-if-this-is-the-case-query-all-devices-to-retrieve-the-site-and-zone-id"></a><a id="1">1</a> *사이트 및 영역 ID가 없을 수 있습니다. 이 경우 모든 디바이스를 쿼리하여 사이트와 영역 ID를 검색합니다.*

#### <a name="query-parameters-example"></a>쿼리 매개 변수 예제

`/external/v1/devices?authorized=true`

`/external/v1/devices?authorized=false`

`/external/v1/devices?siteId=1,2`

`/external/v1/devices?zoneId=5,6`

`/external/v1/devices?sensorId=8`

#### <a name="device-fields"></a>디바이스 필드

| 이름 | 유형 | Nullable | 값 목록 |
|--|--|--|--|
| **sensorId** | 숫자 | 예 | - |
| **zoneId** | 숫자 | 예 | - |
| **siteId** | 숫자 | 예 | - |
| **ipAddresses** | JSON 배열 | 예 | IP 주소(인터넷 주소 또는 이중 NIC를 사용하는 디바이스의 경우 두 개 이상의 주소를 사용할 수 있음) |
| **name** | String | 예 | - |
| **type** | String | 예 | 알 수 없음, 엔지니어링 스테이션, PLC, HMI, Historian, 도메인 컨트롤러, DB 서버, 무선 액세스 지점, 라우터, 스위치, 서버, 워크스테이션, IP 카메라, 프린터, 방화벽, 터미널 스테이션, VPN Gateway, 인터넷 또는 멀티 캐스트 및 브로드캐스트 |
| **macAddresses** | JSON 배열 | 예 | MAC 주소(이중 NIC를 사용하는 디바이스의 경우 두 개 이상의 주소를 사용할 수 있음) |
| **operatingSystem** | String | 예 | - |
| **engineeringStation** | 부울 | 예 | True 또는 False |
| **스캐너** | 부울 | 예 | True 또는 False |
| **승인됨** | 부울 | 예 | True 또는 False |
| **공급업체** | String | 예 | - |
| **프로토콜** | JSON 배열 | 예 | 프로토콜 개체 |
| **펌웨어** | JSON 배열 | 예 | 펌웨어 개체 |

#### <a name="protocol-fields"></a>프로토콜 필드

| 이름 | 유형 | Nullable | 값 목록 |
|--|--|--|--|
| Name | String | 예 | - |
| 주소 | JSON 배열 | 예 | Master 또는 숫자 값 |

#### <a name="firmware-fields"></a>펌웨어 필드

| 이름 | 유형 | Nullable | 값 목록 |
|--|--|--|--|
| **serial** | String | 예 | 해당 없음 또는 실제 값 |
| **model** | String | 예 | 해당 없음 또는 실제 값 |
| **firmwareVersion** | Double | 예 | 해당 없음 또는 실제 값 |
| **additionalData** | String | 예 | 해당 없음 또는 실제 값 |
| **moduleAddress** | String | 예 | 해당 없음 또는 실제 값 |
| **랙** | String | 예 | 해당 없음 또는 실제 값 |
| **슬롯** | String | 예 | 해당 없음 또는 실제 값 |
| **address** | String | 예 | 해당 없음 또는 실제 값 |

#### <a name="response-example"></a>응답 예제

```rest
[

    {
    
    "sensorId": 7,
    
    "zoneId": 1,
    
    "siteId": 1,
    
    "vendor": null,
    
    "name": "10.4.14.102",
    
    "firmware": [
    
    {
    
        "slot": "N/A",
        
        "additionalData": "N/A",
        
        "moduleAddress": "Network: Local network (0), Node: 0, Unit: CPU (0x0)",
        
        "rack": "N/A",
        
        "address": "10.4.14.102",
        
        "model": "AAAAAAAAAA",
        
        "serial": "N/A",
        
        "firmwareVersion": "20.55"
    
    },
    
    {
    
        "slot": "N/A",
        
        "additionalData": "N/A",
        
        "moduleAddress": "Network: Local network (0), Node: 0, Unit: Unknown (0x3)",
        
        "rack": "N/A",
        
        "address": "10.4.14.102",
        
        "model": "AAAAAAAAAAAAAAAAAAAA",
        
        "serial": "N/A",
        
        "firmwareVersion": "20.55"
    
    },
    
    {
    
        "slot": "N/A",
        
        "additionalData": "N/A",
        
        "moduleAddress": "Network: Local network (0), Node: 3, Unit: CPU (0x0)",
        
        "rack": "N/A",
        
        "address": "10.4.14.102",
        
        "model": "AAAAAAAAAAAAAAAAAAAA",
        
        "serial": "N/A",
        
        "firmwareVersion": "20.55"
    
    },
    
    {
    
        "slot": "N/A",
        
        "additionalData": "N/A",
        
        "moduleAddress": "Network: 3, Node: 0, Unit: CPU (0x0)",
        
        "rack": "N/A",
        
        "address": "10.4.14.102",
        
        "model": "AAAAAAAAAAAAAAAAAAAA",
        
        "serial": "N/A",
        
        "firmwareVersion": "20.55"
    
    }

],

"id": 79,

"macAddresses": null,

"authorized": true,

"ipAddresses": [

    "10.4.14.102"

],

"engineeringStation": false,

"type": "PLC",

"operatingSystem": null,

"protocols": [

    {
    
        "addresses": [],
        
        "id": 62,
        
        "name": "Omron FINS"
    
    }

],

"scanner": false

}

]
```

#### <a name="curl-command"></a>Curl 명령

| 유형 | API | 예제 |
|--|--|--|
| GET | `curl -k -H "Authorization: <AUTH_TOKEN>" 'https://<>IP_ADDRESS>/external/v1/devices?siteId=&zoneId=&sensorId=&authorized='` | `curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" 'https://127.0.0.1/external/v1/devices?siteId=1&zoneId=2&sensorId=5&authorized=true'` |

### <a name="retrieve-alert-information---externalv1alerts"></a>경고 정보 검색-/external/v1/alerts

이 API를 사용하여 온-프레미스 관리 콘솔에서 모든 또는 필터링된 경고를 검색합니다.

#### <a name="method"></a>방법

- **GET**

#### <a name="query-parameters"></a>쿼리 매개 변수

- **상태**: 처리되거나 처리되지 않은 경고만 필터링합니다.

  **예제**:

  `/api/v1/alerts?state=handled`

- **fromtime**: 특정 시간(밀리초, UTC)에서 만든 경고를 필터링합니다.

  **예제**:

  `/api/v1/alerts?fromTime=<epoch>`

- **fromtime**: 특정 시간(밀리초, UTC) 이전에만 만든 경고를 필터링합니다.

  **예제**:

  `/api/v1/alerts?toTime=<epoch>`

- **siteId**: 경고가 발견된 사이트입니다.
- **siteId**: 경고가 발견된 영역입니다.
- **siteId**: 경고가 발견된 센서입니다.

*사이트 및 영역 ID가 없을 수 있습니다. 이 경우 모든 디바이스를 쿼리하여 사이트와 영역 ID를 검색합니다.*

#### <a name="alert-fields"></a>경고 필드 

| 이름 | 유형 | Nullable | 값 목록 |
|--|--|--|--|
| **ID** | 숫자 | 예 | - |
| **time** | 숫자 | 예 | Epoch (UTC) |
| **title** | String | 예 | - |
| **message** | String | 예 | - |
| **severity** | String | 예 | 경고, 경미, 중대 또는 위험 |
| **엔진** | String | 예 | 프로토콜 위반, 정책 위반, 맬웨어, 변칙 또는 작동 |
| **sourceDevice** | 숫자 | 예 | 디바이스 ID |
| **destinationDevice** | 숫자 | 예 | 디바이스 ID |
| **sourceDeviceAddress** | 숫자 | 예 | IP, MAC |
| **destinationDeviceAddress** | 숫자 | 예 | IP, MAC |
| **remediationSteps** | String | 예 | 경고에 표시된 수정 단계|
| **sensorName** | String | 예 | 사용자가 정의한 센서 이름 |
|**zoneName** | String | 예 | 센서와 연결된 영역의 이름|
| **siteName** | String | 예 | 센서와 연결된 사이트의 이름 |
| **additionalInformation** | 추가 정보 개체 | 예 | - |

/api/v2/는 다음 정보에 필요합니다.

- sourceDeviceAddress
- destinationDeviceAddress
- remediationSteps
- sensorName
- zoneName
- siteName

#### <a name="additional-information-fields"></a>추가 정보 필드

| 이름 | 유형 | Nullable | 값 목록 |
|--|--|--|--|
| **description** | String | 예 | - |
| **정보** | JSON 배열 | 예 | String |

#### <a name="response-example"></a>응답 예제

```rest
[
    {
        "engine": "Operational",
        "handled": false,
        "title": "Traffic Detected on sensor Interface",
        "additionalInformation": null,
        "sourceDevice": 0,
        "zoneId": 1,
        "siteId": 1,
        "time": 1594808245000,
        "sensorId": 1,
        "message": "The sensor resumed detecting network traffic on ens224.",
        "destinationDevice": 0,
        "id": 1,
        "severity": "Warning"
    },
    {
        "engine": "Anomaly",
        "handled": false,
        "title": "Address Scan Detected",
        "additionalInformation": null,
        "sourceDevice": 4,
        "zoneId": 1,
        "siteId": 1,
        "time": 1594808260000,
        "sensorId": 1,
        "message": "Address scan detected.\nScanning address: 10.10.10.22\nScanned subnet: 10.11.0.0/16\nScanned addresses: 10.11.1.1, 10.11.20.1, 10.11.20.10, 10.11.20.100, 10.11.20.2, 10.11.20.3, 10.11.20.4, 10.11.20.5, 10.11.20.6, 10.11.20.7...\nIt is recommended to notify the security officer of the incident.",
        "destinationDevice": 0,
        "id": 2,
        "severity": "Critical"
    },
    {
        "engine": "Operational",
        "handled": false,
        "title": "Suspicion of Unresponsive MODBUS Device",
        "additionalInformation": null,
        "sourceDevice": 194,
        "zoneId": 1,
        "siteId": 1,
        "time": 1594808285000,
        "sensorId": 1,
        "message": "Outstation device 10.13.10.1 (Protocol Address 53) seems to be unresponsive to MODBUS requests.",
        "destinationDevice": 0,
        "id": 3,
        "severity": "Minor"
    }
]
```

#### <a name="curl-command"></a>Curl 명령

> [!div class="mx-tdBreakAll"]
> | 유형 | API | 예제 |
> |--|--|--|
> | GET | `curl -k -H "Authorization: <AUTH_TOKEN>" 'https://<>IP_ADDRESS>/external/v1/alerts?state=&zoneId=&fromTime=&toTime=&siteId=&sensor='` | `curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" 'https://127.0.0.1/external/v1/alerts?state=unhandled&zoneId=1&fromTime=0&toTime=1594551777000&siteId=1&sensor=1'` |

#### <a name="externalv1alertsltuuidgt"></a>/external/v1/alerts/&lt;UUID&gt;

#### <a name="method"></a>방법

**PUT**

#### <a name="request-type"></a>요청 유형

- **JSON**

#### <a name="request-content"></a>요청 콘텐츠

UUID를 포함하는 경고에 대해 수행할 작업을 나타내는 JSON 개체입니다.

#### <a name="action-fields"></a>작업 필드

| 이름 | 유형 | Nullable | 값 목록 |
|--|--|--|--|
| **action** | String | 예 | 핸들 또는 handleAndLearn |

#### <a name="request-example"></a>요청 예제

```rest
{
    "action": "handle"
}

```

#### <a name="response-type"></a>응답 형식

- **JSON**

#### <a name="response-content"></a>응답 콘텐츠

디바이스를 나타내는 JSON 개체의 배열입니다.

#### <a name="response-fields"></a>응답 필드

| 이름 | 유형 | Nullable | Description |
|--|--|--|--|
| **내용/오류** | String | 예 | 요청에 성공하면 콘텐츠 속성이 나타납니다. 그렇지 않으면 오류 속성이 나타납니다. |

#### <a name="possible-content-values"></a>가능한 콘텐츠 값

| 상태 코드 | 콘텐츠 값 | Description |
|--|--|--|
| 200 | 경고 업데이트 요청이 성공적으로 완료되었습니다. | 업데이트 요청이 성공적으로 완료되었습니다. 설명 없음. |
| 200 | 경고가 이미 처리되었습니다(**처리**). | 경고에 대한 핸들 요청을 받았을 때 경고가 이미 처리되었습니다.<br />경고는 **처리된** 상태로 유지됩니다. |
| 200 | 경고를 이미 핸들하고 학습했습니다(**Handleandlearn**). | **Handleandlearn** 요청이 수신될 때 경고가 이미 핸들되고 학습되었습니다.<br />경고는 **handledAndLearn** 상태로 유지됩니다. |
| 200 | 경고가 이미 처리되었습니다(**처리됨**).<br />경고에 대해 핸들 및 학습(**Handleandlearn**)이 수행되었습니다. | **Handleandlearn** 요청이 수신될 때 경고가 이미 핸들되고 학습되었습니다.<br />이 경고는 **Handleandlearn** 될 수 있습니다. |
| 200 | 경고를 이미 핸들하고 학습했습니다(**Handleandlearn**). 핸들 요청이 무시되었습니다. | 경고 핸들에 대한 요청을 받았을 때 경고가 이미 **Handleandlearn** 이었습니다. 이 경고는 **Handleandlearn** 로 유지됩니다. |
| 500 | 잘못된 작업. | 전송된 작업은 경고에 대해 수행할 수 있는 유효한 작업이 아닙니다. |
| 500 | 예기치 않은 오류가 발생했습니다. | 예기치 않은 오류가 발생했습니다. 이 문제를 해결하려면 지원 팀에 문의하십시오. |
| 500 | 이 UUID에 대한 경고를 찾을 수 없어 요청을 실행할 수 없습니다. | 지정된 경고 UUID를 시스템에서 찾을 수 없습니다. |

#### <a name="response-example"></a>응답 예제

**성공**

```rest
{
    "content": "Alert update request finished successfully"
}
```

**실패**

```rest
{
    "error": "Invalid action"
}
```

#### <a name="curl-command"></a>Curl 명령

| 유형 | API | 예제 |
|--|--|--|
| PUT | `curl -k -X PUT -d '{"action": "<ACTION>"}' -H "Authorization: <AUTH_TOKEN>" https://<IP_ADDRESS>/external/v1/alerts/<UUID>` | `curl -k -X PUT -d '{"action": "handle"}' -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https://127.0.0.1/external/v1/alerts/1-1594550943000` |

### <a name="alert-exclusions-maintenance-window---externalv1maintenancewindow"></a>경고 제외(유지 관리 기간)-/external/v1/maintenanceWindow

경고를 보내지 않을 조건을 정의합니다. 예를 들어 중지 및 시작 시간, 경고를 트리거할 때 제외되어야 하는 디바이스 또는 서브넷, 제외되어야 하는 Defender for IoT 엔진을 정의하고 업데이트합니다. 예를 들어 유지 관리 기간 동안 중요한 디바이스에 대한 맬웨어 경고를 제외하고 모든 경고의 경고 제공을 중지할 수 있습니다.

여기에서 정의하는 API는 온-프레미스 관리 콘솔의 **경고 제외** 창에 읽기 전용 제외 규칙으로 나타납니다.

:::image type="content" source="media/references-work-with-defender-for-iot-apis/alert-exclusion-window.png" alt-text="모든 제외 규칙의 목록을 보여주는 경고 제외 창 ":::

#### <a name="method---post"></a>메서드-POST

#### <a name="query-parameters"></a>쿼리 매개 변수

- **ticketId**: 사용자 시스템의 유지 관리 티켓 ID를 정의합니다.

- **ttl**: 유지 관리 기간의 시간(분)인 TTL(time to live)을 정의합니다. 이 매개 변수가 정의된 기간 후에 시스템에서 자동으로 경고를 보내기 시작합니다.

- **엔진**: 유지 관리 프로세스 중에 경고를 표시하지 않을 보안 엔진을 정의합니다.

    - 예외적

    - 맬웨어

    - 작동

    - 정책 위반

    - 프로토콜 위반

- **sensorIds**: 유지 관리 프로세스 중에 경고를 표시하지 않을 Defender for IoT 센서를 정의합니다. /api/v1/appliances(GET)에서 검색된 것과 동일한 ID입니다.

- **서브넷**: 유지 관리 프로세스 중에 경고를 표시하지 않을 서브넷을 정의합니다. 서브넷은 192.168.0.0/16 형식으로 전송됩니다.

#### <a name="error-codes"></a>오류 코드

- **201(만들어짐)** : 작업이 성공적으로 완료되었습니다.

- **400(잘못된 요청)** : 다음과 같은 경우에 나타납니다

   - **Ttl** 매개 변수가 숫자가 아니거나 양수가 아닙니다.
   - **서브넷** 매개 변수가 잘못된 형식을 사용하여 정의되었습니다.
   - **TicketId** 매개 변수가 없습니다.
   - **엔진** 매개 변수가 기존 보안 엔진과 일치하지 않습니다.

- **404(찾을 수 없음)** : 센서 중 하나가 존재하지 않습니다.

- **409(충돌)** : 티켓 ID는 다른 열려 있는 유지 관리 창에 연결됩니다.

- **500(내부 서버 오류)** : 다른 예기치 않은 오류가 발생했습니다.

> [!NOTE]
> 티켓 ID가 기존에 열려 있는 창에 연결되지 않았는지 확인합니다. 다음 제외 규칙이 생성됩니다. 유지 관리-{토큰 이름}-{티켓 ID}.

#### <a name="method---put"></a>메서드-PUT

**Ttl** 매개 변수를 변경하여 유지 관리 프로세스를 시작한 후 유지 관리 기간 할당 시간 업데이트를 허용합니다. 새 기간 정의가 이전 기간 정의를 재정의합니다.

이 메서드는 현재 구성된 기간보다 긴 기간을 설정하려는 경우에 유용합니다.

#### <a name="query-parameters"></a>쿼리 매개 변수

- **ticketId**: 사용자 시스템의 유지 관리 티켓 ID를 정의합니다.

- **ttl**: 기간(분)을 정의합니다.

#### <a name="error-code"></a>오류 코드

- **200(정상)** : 작업이 성공적으로 완료되었습니다.

- **400(잘못된 요청)** : 다음과 같은 경우에 나타납니다

   - **Ttl** 매개 변수가 숫자가 아니거나 양수가 아닙니다.
   - **TicketId** 매개 변수가 없습니다.
   - **Ttl** 매개 변수가 없습니다.

- **404(찾을 수 없음)** : 티켓 ID가 열린 유지 관리 창에 연결되어 있지 않습니다.

- **500(내부 서버 오류)** : 다른 예기치 않은 오류가 발생했습니다.

> [!NOTE]
> 티켓 ID가 기존에 열려 있는 창에 연결되지 않았는지 확인합니다.

#### <a name="method---delete"></a>메서드-삭제

기존 유지 관리 기간을 닫습니다.

#### <a name="query-parameters"></a>쿼리 매개 변수

- **ticketId**: 사용자 시스템의 유지 관리 티켓 ID를 기록합니다.

#### <a name="error-code"></a>오류 코드

- **200(정상)** : 작업이 성공적으로 완료되었습니다.

- **400(잘못된 요청)** : **ticketId** 매개 변수가 없습니다.

- **404(찾을 수 없음)** : 티켓 ID가 열린 유지 관리 창에 연결되어 있지 않습니다.

- **500(내부 서버 오류)** : 다른 예기치 않은 오류가 발생했습니다.

> [!NOTE]
> 티켓 ID가 기존에 열려 있는 창에 연결되지 않았는지 확인합니다.

#### <a name="method---get"></a>메서드-GET

유지 관리 중 시스템에서 수행된 모든 열기, 닫기 및 업데이트 작업의 로그를 검색합니다. 이전에 활성화되었으며 종결된 유지 관리 기간에 대해서만 로그를 검색할 수 있습니다.

#### <a name="query-parameters"></a>쿼리 매개 변수

- **fromDate**: 미리 정의된 날짜 및 이후에 로그를 필터링합니다. 형식은 2019-12-30입니다.

- **toDate**: 미리 정의된 날짜까지 로그를 필터링합니다. 형식은 2019-12-30입니다.

- **ticketId**: 특정 티켓 ID와 관련된 로그를 필터링합니다.

- **tokenName**: 특정 토큰 이름과 관련된 로그를 필터링합니다.

#### <a name="error-code"></a>오류 코드 

- **200(정상)** : 작업이 성공적으로 완료되었습니다.

- **400(잘못된 요청)** : 날짜 형식이 잘못되었습니다.

- **204(콘텐츠 없음)** : 표시할 데이터가 없습니다.

- **500(내부 서버 오류)** : 다른 예기치 않은 오류가 발생했습니다.

#### <a name="response-type"></a>응답 형식

- **JSON**

#### <a name="response-content"></a>응답 콘텐츠

유지 관리 기간 작업을 나타내는 JSON 개체의 배열입니다.

#### <a name="response-structure"></a>응답 구조

| 이름 | 유형 | 의견 | Nullable |
|--|--|--|--|
| **dateTime** | String | 예제: "2012-04-23T18:25:43.511Z" | no |
| **TicketId** | String | 예제: "9a5fe99c-d914-4bda-9332-307384fe40bf" | no |
| **tokenName** | String | - | no |
| **엔진** | 문자열 배열 | - | 예 |
| **sensorIds** | 문자열 배열 | - | 예 |
| **서브넷** | 문자열 배열 | - | 예 |
| **ttl** | 숫자 | - | 예 |
| **operationType** | String | 값은 "OPEN", "UPDATE" 및 "CLOSE"입니다 | no |

#### <a name="curl-command"></a>Curl 명령

| 유형 | API | 예제 |
|--|--|--|
| POST | `curl -k -X POST -d '{"ticketId": "<TICKET_ID>",ttl": <TIME_TO_LIVE>,"engines": [<ENGINE1, ENGINE2...ENGINEn>],"sensorIds": [<SENSOR_ID1, SENSOR_ID2...SENSOR_IDn>],"subnets": [<SUBNET1, SUBNET2....SUBNETn>]}' -H "Authorization: <AUTH_TOKEN>" https://127.0.0.1/external/v1/maintenanceWindow` | `curl -k -X POST -d '{"ticketId": "a5fe99c-d914-4bda-9332-307384fe40bf","ttl": "20","engines": ["ANOMALY"],"sensorIds": ["5","3"],"subnets": ["10.0.0.3"]}' -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https://127.0.0.1/external/v1/maintenanceWindow` |
| PUT | `curl -k -X PUT -d '{"ticketId": "<TICKET_ID>",ttl": "<TIME_TO_LIVE>"}' -H "Authorization: <AUTH_TOKEN>" https://127.0.0.1/external/v1/maintenanceWindow` | `curl -k -X PUT -d '{"ticketId": "a5fe99c-d914-4bda-9332-307384fe40bf","ttl": "20"}' -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https://127.0.0.1/external/v1/maintenanceWindow` |
| DELETE | `curl -k -X DELETE -d '{"ticketId": "<TICKET_ID>"}' -H "Authorization: <AUTH_TOKEN>" https://127.0.0.1/external/v1/maintenanceWindow` | `curl -k -X DELETE -d '{"ticketId": "a5fe99c-d914-4bda-9332-307384fe40bf"}' -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" https://127.0.0.1/external/v1/maintenanceWindow` |
| GET | `curl -k -H "Authorization: <AUTH_TOKEN>" 'https://<IP_ADDRESS>/external/v1/maintenanceWindow?fromDate=&toDate=&ticketId=&tokenName='` | `curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" 'https://127.0.0.1/external/v1/maintenanceWindow?fromDate=2020-01-01&toDate=2020-07-14&ticketId=a5fe99c-d914-4bda-9332-307384fe40bf&tokenName=a'` |

### <a name="request-alert-pcap---externalv2alertspcap"></a>경고 PCAP 요청 - /external/v2/alerts/pcap

이 API를 사용하여 경고와 관련된 PCAP 파일을 요청합니다.

#### <a name="method"></a>방법

- **GET**

#### <a name="query-parameters"></a>쿼리 매개 변수

- id: CM 경고 ID  
예:  
`/external/v2/alerts/pcap/<id>`

#### <a name="response-type"></a>응답 형식

- **JSON**

#### <a name="response-content"></a>응답 콘텐츠

- **성공:** 요청된 PCAP 파일에 대한 데이터를 포함하는 JSON 개체입니다.
- **실패:** 오류 메시지를 포함하는 JSON 개체

#### <a name="data-fields"></a>데이터 필드

|이름|유형|Nullable|
|-|-|-|
|id|숫자|예|
|xsenseId|숫자|예|
|xsenseAlertId|숫자|예|
|downloadUrl|String|No|
|token|String|예|

#### <a name="response-example"></a>응답 예제

#### <a name="success"></a>성공

```json
{
  "downloadUrl": "https://10.1.0.2/api/v2/alerts/pcap/1",
  "xsenseId": 1,
  "token": "d2791f58-2a88-34fd-ae5c-2651fe30a63c",
  "id": 1,
  "xsenseAlertId": 1
}
```

#### <a name="error"></a>오류

```json
{
  "error": "alert not found"
}
```

### <a name="curl-command"></a>Curl 명령

|유형|API|예제|
|-|-|-|
|GET|`curl -k -H "Authorization: <AUTH_TOKEN>" 'https://<IP_ADDRESS>/external/v2/alerts/pcap/<ID>'`|`curl -k -H "Authorization: 1234b734a9244d54ab8d40aedddcabcd" 'https://10.1.0.1/external/v2/alerts/pcap/1'`

### <a name="servicenow-integration-api---externalv3integration-preview"></a>ServiceNow 통합 API - "/external/v3/integration/(미리 보기)

아래 API는 ServiceNow의 Service Graph Connector for Defender for IoT를 통해 ServiceNow 통합과 함께 사용할 수 있습니다.

### <a name="create-and-update-devices"></a>디바이스 만들기 및 업데이트

#### <a name="request"></a>요청

- 경로: "/devices/{timestamp}"
- 메서드 형식: GET
- 경로 매개 변수:
    - "**timestamp**" – 업데이트가 필요한 시간이며 이후 업데이트만 반환됩니다.

- 쿼리 매개 변수:
    - "**sensorId**" - 이 매개 변수를 사용하여 특정 센서에서 볼 수 있는 디바이스만을 얻습니다. Id는 센서 API의 결과에서 가져와야 합니다.
    - "**notificationType**" - 다음 매핑의 숫자여야 합니다.
        - 0 – 업데이트된 디바이스와 새 디바이스(기본값)
        - 1 – 새 디바이스만
        - 2 – 업데이트된 디바이스만
    - "**page**" - 결과 집합의 페이지 번호입니다(첫 번째 페이지는 0, 기본값은 0).
    - "**size**" - 페이지 크기(기본값: 50)

#### <a name="response"></a>응답

- 형식: JSON
- 구조:
    - "**u_count**" - 모든 페이지를 포함하여 전체 결과 집합의 개체 양입니다.
    - "**u_devices**" - 디바이스 개체의 배열입니다(특정 디바이스 API에 정의된 대로).

### <a name="connections"></a>Connections

#### <a name="request"></a>요청

- 경로: "/connections/{timestamp}"
- 메서드 형식: GET
- 경로 매개 변수:
    - "**timestamp**" – 업데이트가 필요한 시간이며 이후 업데이트만 반환됩니다.
- 쿼리 매개 변수:
    - "**page**" - 결과 집합의 페이지 번호입니다(기본값은 1).
    - "**size**" - 페이지 크기(기본값: 50)

#### <a name="response"></a>응답

- 형식: JSON
- 구조: 
    - "**u_count**" - 모든 페이지를 포함하여 전체 결과 집합의 개체 양입니다.
    - "**u_connections**" - 배열
        - "**u_src_device_id**" - 원본 디바이스의 ID입니다.
        - "**u_dest_device_id**" - 대상 디바이스의 ID입니다.
        - "**u_connection_type**" - 다음 중 하나:
            - "**단방향**"
            - "**양방향**"
            - "**멀티캐스트**"

### <a name="specific-device"></a>특정 디바이스

#### <a name="request"></a>요청

- 경로: "/device/{deviceId}"
- 메서드 형식: GET
- 경로 매개 변수:
    - "**deviceId**" – 요청된 디바이스의 ID입니다.

#### <a name="response"></a>응답

- 형식: JSON
- 구조:
    - "**u_id**" - 디바이스의 내부 ID입니다.
    - "**u_vendor**" - 공급업체의 이름입니다.
    - "**u_mac_address_objects**" - 배열
        - "**u_mac_address**" - 디바이스의 mac 주소입니다.
    - "**u_ip_address_objects**" - 배열
        - "**u_ip_address**" - 디바이스의 IP 주소입니다.
        - "**u_guessed_mac_addresses**" - 배열
            - "**u_mac_address**" - 추측된 mac 주소입니다.
    - "**u_name**" - 디바이스의 이름입니다.
    - "**u_last_activity**" - 디바이스가 마지막으로 활성화된 시간의 타임스탬프입니다.
    - "**u_first_discovered**" - 디바이스 검색 시간의 타임스탬프입니다.
    - "**u_last_update**" - 디바이스의 마지막 업데이트 시간의 타임스탬프입니다.
    - "**u_vlans**" - 배열
        - "**u_vlan**" - 디바이스가 있는 vlan입니다.
    - "**u_device_type**" -
        - "**u_name**" - 디바이스 유형
        - "**u_purdue_layer**" - 이 디바이스 유형의 기본 purdue 계층입니다.
        - "**u_category**" - 다음 중 하나입니다.
            - "**IT**"
            - "**ICS**"
            - "**IoT**"
            - "**네트워크**"
    - "**u_operating_system**" - 디바이스 운영 체제입니다.
    - "**u_protocol_objects**" - 배열
        - "**u_protocol**" - 디바이스에서 사용하는 프로토콜입니다.
    - "**u_purdue_layer**" - 사용자가 수동으로 설정한 purdue 계층입니다.
    - "**u_sensor_ids**" - 배열
        - "**u_sensor_id**" - 디바이스를 본 센서의 ID입니다.
    - "**u_device_urls**" - 배열
        - "**u_device_url**" 센서에서 디바이스를 볼 URL입니다.
    - "**u_firmwares**" - 배열
        - "**u_address**"
        - "**u_module_address**"
        - "**u_serial**"
        - "**u_model**"
        - "**u_version**"
        - "**u_additional_data**"

### <a name="deleted-devices"></a>삭제된 디바이스

#### <a name="request"></a>요청

- 경로: "/deleteddevices/{timestamp}"
- 메서드 형식: GET
- 경로 매개 변수:
    - "**timestamp**" – 업데이트가 필요한 시간이며 이후 업데이트만 반환됩니다.

#### <a name="response"></a>응답

- 형식: JSON
- 구조:
    - 배열
        - "**u_id**" - 삭제된 디바이스의 ID입니다.

### <a name="sensors"></a>센서

#### <a name="request"></a>요청

- 경로: "/sensors"
- 메서드 형식: GET

#### <a name="response"></a>응답

- 형식: JSON
- 구조:
    - 배열
        - "**u_id**" - 디바이스 API에서 사용할 내부 센서 ID입니다.
        - "**u_name**" - 어플라이언스의 이름입니다.
        - "**u_connection_state**" - CM 상태와의 연결입니다. 다음 중 하나
            - "**SYNCED**" - 연결이 성공했습니다.
            - "**OUT_OF_SYNC**" - 관리 콘솔이 센서에서 받은 데이터를 처리할 수 없습니다.
            - "**TIME_DIFF_OFFSET**" - 시간 드리프트가 검색되었습니다. 센서에서 관리 콘솔의 연결이 끊어졌습니다.
            - " DISCONNECTED " -**센서가** 관리 콘솔과 통신하지 않습니다. 네트워크 연결을 확인합니다.
        - "**u_interface_address**" - 어플라이언스의 네트워크 주소입니다.
        - "**u_version**" - 센서 버전의 문자열 표현입니다.
        - "**u_alert_count**" - 센서에서 발견한 경고 수입니다.
        - "**u_device_count**" - 센서에서 검색한 디바이스 수입니다.
        - "**u_unhandled_alert_count**" - 센서의 처리되지 않은 경고 수입니다.
        - "**u_is_activated**" - 활성화된 경고입니다.
        - "**u_data_intelligence_version**" - 센서에 설치된 데이터 인텔리전스의 문자열 표현입니다.
        - "**u_remote_upgrade_stage**" - 원격 업그레이드의 상태입니다. 다음 중 하나
            - "**UPLOADING**"
            - "**PREPARE_TO_INSTALL**"
            - "**STOPPING_PROCESSES**"
            - "**BACKING_UP_DATA**"
            - "**TAKING_SNAPSHOT**"
            - "**UPDATING_CONFIGURATION**"
            - "**UPDATING_DEPENDENCIES**"
            - "**UPDATING_LIBRARIES**"
            - "**PATCHING_DATABASES**"
            - "**STARTING_PROCESSES**"
            - "**VALIDATING_SYSTEM_SANITY**"
            - "**VALIDATION_SUCCEEDED_REBOOTING**"
            - "**SUCCESS**"
            - "**FAILURE**"
            - "**UPGRADE_STARTED**"
            - "**STARTING_INSTALLATION**"
            - "**INSTALLING_OPERATING_SYSTEM**"
        - "**u_uid**" - 센서의 전역적으로 고유한 식별자

### <a name="device-cves"></a>디바이스 CVEs

#### <a name="request"></a>요청

- 경로: "/devicecves/{timestamp}"
- 메서드 형식: GET
- 경로 매개 변수:
    - "**timestamp**" – 업데이트가 필요한 시간이며 이후 업데이트만 반환됩니다.
- 쿼리 매개 변수:
    - "**page**" - 결과 집합의 페이지 번호입니다(첫 번째 페이지는 0, 기본값은 0).
    - "**size**" - 페이지 크기(기본값: 50)

#### <a name="response"></a>응답

- 형식: JSON
- 구조:
    - "**u_count**" - 모든 페이지를 포함하여 전체 결과 집합의 개체 양입니다.
    - "**u_id**" - 특정 디바이스 API와 동일합니다.
    - "**u_name**" - 특정 디바이스 API와 동일합니다.
    - "**u_ip_address_objects**" - 특정 디바이스 API와 동일합니다.
    - "**u_mac_address_objects**" - 특정 디바이스 API와 동일합니다.
    - "**u_last_activity**" - 특정 디바이스 API와 동일합니다.
    - "**u_last_update**" - 특정 디바이스 API와 동일합니다.
    - "**u_cves**" - CVE 배열:
        - "**u_ip_address**" - CVE가 검색된 특정 펌웨어를 포함하는 특정 인터페이스의 IP 주소입니다.
        - "**u_cve_id**"- CVE의 ID
        - "**u_score**"- CVE의 위험 점수
        - "**u_attack_vector**" - 다음 중 하나:
            - "**ADJACENT_NETWORK**"
            - "**LOCAL**"
            - "**NETWORK**"
        - "**u_description**" - CVE에 대한 설명입니다.

## <a name="next-steps"></a>다음 단계

- [디바이스 인벤토리에서 센서 감지 조사](how-to-investigate-sensor-detections-in-a-device-inventory.md)

- [디바이스 인벤토리에서 모든 엔터프라이즈 센서 감지 조사](how-to-investigate-all-enterprise-sensor-detections-in-a-device-inventory.md)

---
title: 스토리지 분석 로그를 사용하여 대기 시간 문제 해결
description: Azure Storage Analytics 로그를 사용하여 대기 시간 문제를 식별하고 해결하며 클라이언트 애플리케이션을 최적화합니다.
author: v-miegge
ms.topic: troubleshooting
ms.author: kartup
manager: dcscontentpm
ms.date: 10/21/2019
ms.service: storage
ms.subservice: common
services: storage
tags: ''
ms.openlocfilehash: 4b59b7d42b162a369862974c0599d972fa1957ee
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128636689"
---
# <a name="troubleshoot-latency-using-storage-analytics-logs"></a>스토리지 분석 로그를 사용하여 대기 시간 문제 해결

진단 및 문제 해결은 Azure Storage를 사용하는 클라이언트 애플리케이션을 빌드 및 지원하기 위한 핵심 기술입니다.

Azure 애플리케이션의 분산된 특성 때문에, 오류 및 성능 문제를 진단하고 해결하는 것은 기존 환경보다 더 복잡할 수 있습니다.

다음 단계에서는 Azure Storage Analytics 로그를 사용하여 대기 시간 문제를 식별하고 해결하며 클라이언트 애플리케이션을 최적화하는 방법을 보여 줍니다.

## <a name="recommended-steps"></a>권장되는 단계

1. [스토리지 분석 로그](./manage-storage-analytics-logs.md#download-storage-logging-log-data)를 다운로드합니다.

2. 다음 PowerShell 스크립트를 사용하여 원시 형식 로그를 테이블 형식으로 변환합니다.

   ```powershell
   $Columns = 
        (   "version-number",
            "request-start-time",
            "operation-type",
            "request-status",
            "http-status-code",
            "end-to-end-latency-in-ms",
            "server-latency-in-ms",
            "authentication-type",
            "requester-account-name",
            "owner-account-name",
            "service-type",
            "request-url",
            "requested-object-key",
            "request-id-header",
            "operation-count",
            "requester-ip-address",
            "request-version-header",
            "request-header-size",
            "request-packet-size",
            "response-header-size",
            "response-packet-size",
            "request-content-length",
            "request-md5",
            "server-md5",
            "etag-identifier",
            "last-modified-time",
            "conditions-used",
            "user-agent-header",
            "referrer-header",
            "client-request-id"
        )

   $logs = Import-Csv "REPLACE THIS WITH FILE PATH" -Delimiter ";" -Header $Columns

   $logs | Out-GridView -Title "Storage Analytic Log Parser"
   ```

3. 이 스크립트는 아래와 같이 열을 기준으로 정보를 필터링할 수 있는 GUI 창을 시작합니다.

   ![스토리지 분석 로그 파서 창](media/troubleshoot-latency-storage-analytics-logs/storage-analytic-log-parser-window.png)

4. "Operation-type"에 따라 로그 항목의 범위를 좁히고 문제의 시간 프레임 동안 생성된 로그 항목을 찾습니다.

   ![작업 유형 로그 항목](media/troubleshoot-latency-storage-analytics-logs/operation-type.png)

5. 문제가 발생한 시간 동안에는 다음 값이 중요합니다.

   - Operation-type = GetBlob
   - request-status = SASNetworkError
   - End-to-End-Latency-In-Ms = 8453
   - Server-Latency-In-Ms = 391

   다음 수식을 사용하여 엔드투엔드 대기 시간을 계산합니다.

   - 엔드투엔드 대기 시간 = 서버 대기 시간 + 클라이언트 대기 시간

   로그 항목을 사용하여 클라이언트 대기 시간을 계산합니다.

   - 클라이언트 대기 시간 = 엔드투엔드 대기 시간 – 서버 대기 시간

        예: 8453 – 391 = 8062ms

   다음 표에서는 대기 시간이 긴 OperationType 및 RequestStatus 결과에 대한 정보를 제공합니다.

   | Blob 유형 |RequestStatus=<br>Success|RequestStatus=<br>(SAS)NetworkError|권장|
   |---|---|---|---|
   |GetBlob|예|아니요|[**GetBlob 작업:** RequestStatus = Success](#getblob-operation-requeststatus--success)|
   |GetBlob|아니요|예|[**GetBlob 작업:** RequestStatus = (SAS)NetworkError](#getblob-operation-requeststatus--sasnetworkerror)|
   |PutBlob|예|아니요|[**Put 작업:** RequestStatus = Success](#put-operation-requeststatus--success)|
   |PutBlob|아니요|예|[**Put 작업:** RequestStatus = (SAS)NetworkError](#put-operation-requeststatus--sasnetworkerror)|

## <a name="status-results"></a>상태 결과

### <a name="getblob-operation-requeststatus--success"></a>GetBlob 작업: RequestStatus = Success

"권장 단계" 섹션의 5단계에서 언급한 대로 다음 값을 확인합니다.

- 엔드투엔드 대기 시간
- 서버 대기 시간
- 클라이언트 대기 시간

**RequestStatus = Success** 인 **GetBlob 작업** 에서 **최대 시간** 이 **클라이언트 대기 시간** 에 소요되는 경우 이는 Azure Storage가 클라이언트에 데이터를 쓰는 데 많은 시간을 사용하고 있음을 나타냅니다. 이 지연은 클라이언트 쪽 문제를 나타냅니다.

**권장 사항:**

- 클라이언트의 코드를 조사합니다.
- Wireshark, Microsoft Message Analyzer 또는 Tcping을 사용하여 클라이언트에서 네트워크 연결 문제를 조사합니다.

### <a name="getblob-operation-requeststatus--sasnetworkerror"></a>GetBlob 작업: RequestStatus = (SAS)NetworkError

"권장 단계" 섹션의 5단계에서 언급한 대로 다음 값을 확인합니다.

- 엔드투엔드 대기 시간
- 서버 대기 시간
- 클라이언트 대기 시간

**RequestStatus =(SAS)NetworkError** 인 **GetBlob 작업** 에서 **최대 시간** 이 **클라이언트 대기 시간** 에 소요되는 경우의 가장 일반적인 문제는 스토리지 서비스에서 시간 초과가 만료되기 전에 클라이언트가 연결을 끊는다는 것입니다.

**권장 사항:**

- 클라이언트의 코드를 조사하여 스토리지 서비스에서 클라이언트의 연결이 끊기는 이유와 시기를 파악합니다.
- Wireshark, Microsoft Message Analyzer 또는 Tcping을 사용하여 클라이언트에서 네트워크 연결 문제를 조사합니다.

### <a name="put-operation-requeststatus--success"></a>Put 작업: RequestStatus = Success

"권장 단계" 섹션의 5단계에서 언급한 대로 다음 값을 확인합니다.

- 엔드투엔드 대기 시간
- 서버 대기 시간
- 클라이언트 대기 시간

**RequestStatus = Success** 인 **Put 작업** 에서 **최대 시간** 이 **클라이언트 대기 시간** 에 소요되는 경우 클라이언트가 Azure Storage에 데이터를 보내는 데 더 많은 시간이 소요되고 있다는 것입니다. 이 지연은 클라이언트 쪽 문제를 나타냅니다.

**권장 사항:**

- 클라이언트의 코드를 조사합니다.
- Wireshark, Microsoft Message Analyzer 또는 Tcping을 사용하여 클라이언트에서 네트워크 연결 문제를 조사합니다.

### <a name="put-operation-requeststatus--sasnetworkerror"></a>Put 작업: RequestStatus = (SAS)NetworkError

"권장 단계" 섹션의 5단계에서 언급한 대로 다음 값을 확인합니다.

- 엔드투엔드 대기 시간
- 서버 대기 시간
- 클라이언트 대기 시간

**RequestStatus =(SAS)NetworkError** 인 **PutBlob 작업** 에서 **최대 시간** 이 **클라이언트 대기 시간** 에 소요되면 경우의 가장 일반적인 문제는 스토리지 서비스에서 시간 초과가 만료되기 전에 클라이언트가 연결을 끊는다는 것입니다.

**권장 사항:**

- 클라이언트의 코드를 조사하여 스토리지 서비스에서 클라이언트의 연결이 끊기는 이유와 시기를 파악합니다.
- Wireshark, Microsoft Message Analyzer 또는 Tcping을 사용하여 클라이언트에서 네트워크 연결 문제를 조사합니다.

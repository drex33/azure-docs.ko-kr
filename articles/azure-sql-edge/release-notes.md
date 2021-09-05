---
title: Azure SQL Edge 릴리스 정보
description: Azure SQL Edge 이미지의 새 기능 또는 변경 사항을 설명하는 릴리스 정보
keywords: 릴리스 정보 SQL Edge
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
ms.subservice: ''
author: VasiyaKrishnan
ms.author: vakrishn
ms.reviewer: sstein
ms.date: 11/24/2020
ms.openlocfilehash: 29ab3c33611b0f525b28fe51245edba377bae0b5
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/22/2021
ms.locfileid: "114441430"
---
# <a name="azure-sql-edge-release-notes"></a>Azure SQL Edge 릴리스 정보 

이 문서에서는 Azure SQL Edge의 모든 새 빌드에 포함된 새 기능 및 변경 사항을 설명합니다.

## <a name="azure-sql-edge-104"></a>Azure SQL Edge 1.0.4

SQL 엔진 빌드 15.0.2000.1559

### <a name="whats-new"></a>새로운 기능

- ONNX에 PREDICT 지원 
    - ONNX용 PREDICT에서 Null 데이터 처리 능력 향상

## <a name="azure-sql-edge-103"></a>Azure SQL Edge 1.0.3

SQL 엔진 빌드 15.0.2000.1557

### <a name="fixes"></a>수정 프로그램

- ONNX 런타임을 1.5.3으로 업그레이드
- Microsoft.SqlServer.DACFx 버전 150.5084.2로 업데이트
- 기타 버그 수정  
   
## <a name="azure-sql-edge-102"></a>Azure SQL Edge 1.0.2

SQL 엔진 빌드 15.0.2000.1557

### <a name="fixes"></a>수정 프로그램

- T-SQL 스트리밍  
   - 스트리밍 개체에 대한 소유권 및 권한 수정
   - 로그 회전 및 로그 접두사 지정을 통한 로깅 개선
   - Azure Stream Analytics: 로깅 개선, 어댑터의 오류 코드/오류 메시지 개선 

- ONNX
    - 병렬 쿼리 시나리오 및 모델 정리 실패에 대한 버그 수정
    - ONNX 런타임을 1.5.1로 업그레이드

## <a name="azure-sql-edge-101"></a>Azure SQL Edge 1.0.1

SQL 엔진 빌드 15.0.2000.1553

### <a name="whats-new"></a>새로운 기능

- 계산 열에 정의된 Date_Bucket 식 허용

### <a name="fixes"></a>수정 프로그램

- 시간 제한이 무기한인 보존 정책을 가진 테이블을 삭제하도록 보존 정책 수정
- 스트리밍 기능 및 보존 정책 기능을 위한 DacFx 배포 지원 
- SAS URL의 중첩된 폴더에서 배포할 수 있도록 DacFx 배포 수정 
- 오류 메시지에서 긴 열 이름을 지원하도록 PREDICT 수정

## <a name="azure-sql-edge-100-rtm"></a>Azure SQL Edge 1.0.0(RTM)

SQL 엔진 빌드 15.0.2000.1552

### <a name="whats-new"></a>새로운 기능
- Ubuntu 18.04 기반의 컨테이너 이미지 
- `LAST_VALUE()` 함수와 `FIRST_VALUE()` 함수로 `IGNORE NULL` 및 `RESPECT NULL` 구문 지원 
- ONNX로 PREDICT의 안정성 개선
- 데이터 보존 정책에 따른 정리 지원:
   - 문제 해결을 위해 보존 정리 작업에 대한 링 버퍼 지원
- 새 기능 지원: 
   - 빠른 복구
   - 쿼리 자동 조정
   - 병렬 실행 시나리오
- 저전력 모드를 위한 절전 기능 향상
- 스트리밍 새 기능 지원: 
   - [스냅샷 창](/stream-analytics-query/snapshot-window-azure-stream-analytics): 새 창 유형을 사용하면 동시에 도착하는 이벤트별로 그룹화할 수 있습니다.
   - [TopOne](/stream-analytics-query/topone-azure-stream-analytics) 및 [CollectTop](/stream-analytics-query/collecttop-azure-stream-analytics)을 분석 함수로 사용할 수 있습니다. 레코드를 선택한 열을 기준으로 정렬하여 반환할 수 있습니다. 해당 레코드는 창의 일부일 필요가 없습니다. 
   - [MATCH_RECOGNIZE](/stream-analytics-query/match-recognize-stream-analytics) 개선 

### <a name="fixes"></a>수정 프로그램
- T-SQL 스트리밍 작업 문제 해결을 위한 오류 메시지 및 세부 정보 추가 
- 유휴 모드에서의 배터리 수명 보존 기능 개선 
- T-SQL 스트리밍 엔진 수정: 
   - 중지된 스트리밍 작업 정리 
   - 지역화를 위한 수정 
   - 유니코드 처리 개선 
   - 사용자가 get_streaming_job에서 작업 실패 오류를 쿼리할 수 있도록 SQL Edge T-SQL 스트리밍을 위한 디버깅 개선
- 데이터 보존 정책에 따른 정리: 
    - 보존 정책 작성 및 정리 시나리오에 대한 수정
- 저전력 모드에서의 절전 기능 향상을 위한 백그라운드 타이머 작업 수정

### <a name="known-issues"></a>알려진 문제 
- 계산 열에서 Date_Bucket T-SQL 함수를 사용할 수 없습니다.


## <a name="ctp-23"></a>CTP 2.3
SQL 엔진 빌드 15.0.2000.1549
### <a name="whats-new"></a>새로운 기능
- Date_Bucket() 함수에서 사용자 지정 원본 지원 
- SQL 배포의 일부로 BACPAC 파일 지원
- 데이터 보존 정책에 따른 정리 지원:      
   - 보존 정책 사용을 위한 DDL 지원 
   - 저장 프로시저 및 백그라운드 정리 작업에 대한 정리
   - 정리 작업 모니터링을 위한 이벤트 확장

### <a name="fixes"></a>수정 프로그램
- T-SQL 스트리밍 작업 문제 해결을 위한 오류 메시지 및 세부 정보 추가 
- 유휴 모드에서의 배터리 수명 보존 기능 개선 
- T-SQL 스트리밍 엔진: 
   - 하위 스트리밍되는 도약 창에 고정되는 워터마크 수정 
   - 프레임워크 예외가 사용자가 조치할 수 있는 오류로 수집되도록 프레임워크 예외 처리 수정


## <a name="ctp-22"></a>CTP 2.2
SQL 엔진 빌드 15.0.2000.1546
### <a name="whats-new"></a>새로운 기능
- 루트가 아닌 컨테이너 지원 
- 사용량 및 진단 데이터 수집 지원 
- T-SQL 스트리밍 업데이트:
   - 스트림 개체 이름에 유니코드 문자 지원

### <a name="fixes"></a>수정 프로그램
- T-SQL 스트리밍 업데이트:
   - 프로세스 정리 개선
   - 로깅 및 진단 개선
- 데이터 수집 성능 개선

## <a name="ctp-21"></a>CTP 2.1 
SQL 엔진 빌드 15.0.2000.1545
### <a name="fixes"></a>수정 프로그램
- ARM의 CPUID 문제를 처리할 수 있도록 PREDICT-ONNX 모델 허용 
- T-SQL 스트리밍이 시작될 때 실패 경로에 대한 처리 개선
- 데이터가 없는 경우 작업 메트릭의 워터마크 지연 값 수정
- 어댑터에 일괄 처리 간 변수 스키마가 있을 때 출력 어댑터 문제 수정  

## <a name="ctp-20"></a>CTP 2.0 
SQL 엔진 빌드 15.0.2000.1401
### <a name="whats-new"></a>새로운 기능
-   제품 이름이 *Azure SQL Edge* 로 업데이트됨
-  Date_Bucket 함수:
    - Date, Time 및 DateTime 형식 지원
- ONNX를 사용하는 PREDICT:
    - RUNTIME 매개 변수에 대한 ONNX 요구 사항  
- T-SQL 스트리밍 지원(제한된 미리 보기) 
 
### <a name="known-issues"></a>알려진 문제

- 문제: 타이밍 문제로 인해 시작 시 DACPAC 적용에 실패할 수 있습니다.
- 해결 방법: SQL Server를 다시 시작합니다. 그러지 않으면 컨테이너가 DACPAC 적용을 다시 시도합니다.

### <a name="request-support"></a>지원 요청
[지원 페이지](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)에서 지원을 요청할 수 있습니다. 다음 필드를 선택합니다. 
- **문제 유형**: *기술* 
- **서비스**: *IoT Edge*
- **문제 유형**: *내 문제가 IoT Edge 모듈과 관련이 있음*
- **문제 하위 유형**: *Azure SQL Edge*

:::image type="content" source="media/get-support/support-ticket.png" alt-text="샘플 지원 티켓을 보여 주는 스크린샷.":::

## <a name="ctp-15"></a>CTP 1.5
SQL 엔진 빌드 15.0.2000.1331
### <a name="whats-new"></a>새로운 기능
- Date_Bucket 함수:
    - DateTimeOffset 형식 지원
- ONNX 모델을 사용하는 PREDICT:
  - NVARCHAR 지원
 
## <a name="ctp-14"></a>CTP 1.4
SQL 엔진 빌드 15.0.2000.1247
### <a name="whats-new"></a>새로운 기능
-   ONNX 모델을 사용하는 PREDICT:
    - VARCHAR 지원
    - ONNX 런타임 버전 1.0으로 마이그레이션 

- 다음과 같은 기능을 사용할 수 있습니다.
  - CDC 지원
  - 압축된 기록 테이블
  - 로그 미리 읽기를 위한 배율 확대
  - 일괄 처리 모드 ES 필터 푸시다운
  - 미리 읽기 최적화
 
## <a name="ctp-13"></a>CTP 1.3
SQL 엔진 빌드 15.0.2000.1147
### <a name="whats-new"></a>새로운 기능
- Azure IoT 포털 배포: 
    - AMD64 및 ARM 이미지 배포 지원
    - 스트리밍 작업 만들기 지원
    - DACPAC 배포
- ONNX 모델을 사용하는 PREDICT:
    - 숫자 형식 지원
- 다음과 같은 기능을 사용할 수 있습니다.
    - 열 저장소 검사로의 푸시다운 집계
    - 회전목마 검사
- 메모리 공간 및 메모리 사용량 감소 작업

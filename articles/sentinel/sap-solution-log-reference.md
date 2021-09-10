---
title: Azure Sentinel SAP 솔루션 - 사용 가능한 로그 참조 | Microsoft Docs
description: Azure Sentinel SAP 솔루션에서 사용할 수 있는 SAP 로그에 대해 알아봅니다.
author: batamig
ms.author: bagold
ms.service: azure-sentinel
ms.topic: reference
ms.custom: mvc
ms.date: 07/21/2021
ms.subservice: azure-sentinel
ms.openlocfilehash: a1511581e3cc80d5f2f71bb82dc1bcb0ba639acb
ms.sourcegitcommit: deb5717df5a3c952115e452f206052737366df46
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122681508"
---
# <a name="azure-sentinel-sap-solution-logs-reference-public-preview"></a>Azure Sentinel SAP 솔루션 로그 참조(공개 미리 보기)

이 문서에서는 Azure Sentinel의 테이블 이름, 로그 용도 및 자세한 로그 스키마를 포함하여 Azure Sentinel SAP 데이터 커넥터에서 사용할 수 있는 SAP 로그에 대해 설명합니다. 스키마 필드 설명은 관련 [SAP 문서](https://help.sap.com/)의 필드 설명을 기반으로 합니다.

이 문서는 고급 SAP 사용자를 대상으로 합니다.

> [!NOTE]
> XBP 3.0 인터페이스를 사용할 때 Azure Sentinel SAP 솔루션은 *릴리스되지 않음* 서비스를 사용합니다. 이러한 서비스는 백 엔드 시스템 또는 커넥터 동작에 영향을 주지 않습니다.
>
> 이러한 서비스를 "릴리스"하려면 [SAP Note 2910263 - 릴리스되지 않은 XBP 함수](https://launchpad.support.sap.com/#/notes/2910263)를 구현합니다.

> [!IMPORTANT]
> Azure Sentinel SAP 솔루션은 현재 미리 보기로 제공됩니다. [Azure Preview 추가 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타, 미리 보기 또는 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 법률 용어가 포함되어 있습니다.
>

## <a name="abap-application-log"></a>ABAP 애플리케이션 로그

- **Azure Sentinel의 이름**: `ABAPAppLog_CL`

- **관련 SAP 문서**: [SAP 도움말 포털](https://help.sap.com/viewer/56bf1265a92e4b4d9a72448c579887af/7.5.7/en-US/c769bcc9f36611d3a6510000e835363f.html)

- **로그 용도**: 나중에 필요에 따라 재구성할 수 있도록 애플리케이션 실행 진행률을 기록합니다.

    XBP 인터페이스의 표준 서비스를 기반으로 하는 사용자 지정 서비스와 함께 RFC를 통해 사용할 수 있습니다. 이 로그는 클라이언트별로 생성됩니다.


### <a name="abapapplog_cl-log-schema"></a>ABAPAppLog_CL 로그 스키마

| 필드                 | Description                    |
| --------------------- | ------------------------------ |
| AppLogDateTime        | 애플리케이션 로그 날짜 시간      |
| CallbackProgram       | 콜백 프로그램               |
| CallbackRoutine       | 콜백 루틴               |
| CallbackType          | 콜백 유형                  |
| ClientID              | ABAP 클라이언트 ID(MANDT)         |
| ContextDDIC           | 컨텍스트 DDIC 구조         |
| ExternalID            | 외부 로그 ID                |
| 호스트                  | 호스트                           |
| 인스턴스              | 다음 구문의 ABAP 인스턴스:   `<HOST>_<SYSID>_<SYSNR>`              |
| InternalMessageSerial | 일련의 애플리케이션 로그 메시지 |
| LevelofDetail         | 세부 수준                |
| LogHandle             | 애플리케이션 로그 핸들         |
| LogNumber             | 로그 번호                     |
| MessageClass          | Message 클래스                  |
| MessageNumber         | 메시지 번호                 |
| MessageText           | 메시지 텍스트                   |
| MessageType           | 메시지 유형                   |
| Object                | 애플리케이션 로그 개체         |
| OperationMode         | 작업 모드                 |
| ProblemClass          | 문제 클래스                  |
| ProgramName           | 프로그램 이름                   |
| SortCriterion         | 정렬 기준                 |
| StandardText          | 표준 텍스트                  |
| 하위 개체             | 애플리케이션 로그 하위 개체     |
| SystemID              | 시스템 ID                      |
| SystemNumber          | 시스템 번호                  |
| TransactionCode       | 트랜잭션 코드               |
| 사용자                  | 사용자                           |
| UserChange            | 사용자 변경                    |
| | |



## <a name="abap-change-documents-log"></a>ABAP 변경 문서 로그

- **Azure Sentinel의 이름**: `ABAPChangeDocsLog_CL`

- **관련 SAP 문서**: [SAP 도움말 포털](https://help.sap.com/viewer/6f51f5216c4b10149358d088a0b7029c/7.01.22/en-US/b8686150ed102f1ae10000000a44176f.html)

- **로그 용도**: 기록:

    - SAP NetWeaver AS(Application Server) ABAP는 변경 문서에서 비즈니스 데이터 개체에 대한 변경 내용을 기록합니다.

    - SAP 시스템의 다른 엔터티(예: 사용자 데이터, 역할, 주소)

    표준 서비스를 기반으로 하는 사용자 지정 서비스와 함께 RFC를 통해 사용할 수 있습니다. 이 로그는 클라이언트별로 생성됩니다.

### <a name="abapchangedocslog_cl-log-schema"></a>ABAPChangeDocsLog_CL 로그 스키마


| 필드                    | Description                 |
| ------------------------ | ---------------------------- |
| ActualChangeNum          | 실제 변경 번호         |
| ChangedTableKey          | 변경된 테이블 키            |
| ChangeNumber             | 번호 바꾸기                |
| ClientID                 | ABAP 클라이언트 ID(MANDT)       |
| CreatedfromPlannedChange | 다음 구문의 계획된 변경에서 작성: `(‘X’ , ‘ ‘)`|
| CurrencyKeyNew           | 통화 키: 새 값      |
| CurrencyKeyOld           | 통화 키: 이전 값      |
| FieldName                | 필드 이름                   |
| FlagText                 | 플래그 텍스트                    |
| 호스트                     | 호스트                         |
| 인스턴스                 | 다음 구문의 ABAP 인스턴스: `<HOST>_<SYSID>_<SYSNR>` |
| 언어                 | 언어                     |
| ObjectClass              | `BELEG`, `BPAR`, `PFCG`, `IDENTITY`와 같은 개체 클래스 |
| ObjectID                 | 개체 ID  |
| PlannedChangeNum         | 계획된 변경 번호  |
| SystemID                 | 시스템 ID    |
| SystemNumber             | 시스템 번호     |
| TableName                | 테이블 이름        |
| TransactionCode          | 트랜잭션 코드   |
| TypeofChange_Header      | 다음을 포함한 변경의 헤더 형식: <br>`U` = 변경; `I` = 삽입; `E` = 단일 Docu 삭제; `D` = 삭제; `J` = 단일 Docu 삽입 |
| TypeofChange_Item        | 다음을 포함한 변경의 항목 유형: <br>`U` = 변경; `I` = 삽입; `E` = 단일 Docu 삭제; `D` = 삭제; `J` = 단일 Docu 삽입 |
| UOMNew                   | 측정 단위: 새 값  |
| UOMOld                   | 측정 단위: 이전 값 |
| 사용자                     | 사용자  |
| ValueNew                 | 필드 콘텐츠: 새 값 |
| ValueOld                 | 필드 내용: 이전 값 |
| 버전                  | 버전          |
| | |

## <a name="abap-cr-log"></a>ABAP CR 로그

- **Azure Sentinel의 이름**: `ABAPCRLog_CL`

- **관련 SAP 문서**: [SAP 도움말 포털](https://help.sap.com/viewer/56bf1265a92e4b4d9a72448c579887af/7.5.7/en-US/c769bcd5f36611d3a6510000e835363f.html)

- **로그 용도**: 변경된 디렉터리 개체 및 사용자 지정을 비롯한 CTS(변경 및 전송 시스템) 로그를 포함합니다.

    표준 테이블 및 표준 서비스를 기반으로 하는 사용자 지정 서비스와 함께 RFC를 통해 사용할 수 있습니다. 이 로그는 모든 클라이언트에서 데이터를 사용하여 생성됩니다.

> [!NOTE]
> 애플리케이션 로깅, 변경 문서 및 테이블 기록 외에도 변경 및 전송 시스템을 사용하여 프로덕션 시스템에 적용한 모든 변경 사항은 CTS 및 TMS 로그에 문서화됩니다.
>


### <a name="abapcrlog_cl-log-schema"></a>ABAPCRLog_CL 로그 스키마

| 필드        | Description                       |
| ------------ | --------------------------------- |
| 범주     | 범주(워크벤치, 사용자 지정) |
| ClientID     | ABAP 클라이언트 ID(MANDT)            |
| Description  | Description                       |
| 호스트         | 호스트                              |
| 인스턴스     | 다음 구문의 ABAP 인스턴스: `<HOST>_<SYSID>_<SYSNR>` |
| ObjectName   | 개체 이름                       |
| ObjectType   | 개체 형식                       |
| 소유자        | 소유자                             |
| 요청      | 변경 요청                    |
| 상태       | 상태                            |
| SystemID     | 시스템 ID                         |
| SystemNumber | 시스템 번호                     |
| TableKey     | 테이블 키                         |
| TableName    | 테이블 이름                        |
| ViewName     | 뷰 이름                         |
| | |

## <a name="abap-db-table-data-log"></a>ABAP DB 테이블 데이터 로그

- **Azure Sentinel의 이름**: `ABAPTableDataLog_CL`

- **관련 SAP 문서**: [SAP 도움말 포털](https://help.sap.com/viewer/56bf1265a92e4b4d9a72448c579887af/7.5.7/en-US/c769bcd2f36611d3a6510000e835363f.html)

- **로그 용도**: 중요하거나 감사에 취약한 테이블에 대한 로깅을 제공합니다.

    사용자 지정 서비스와 함께 RFC를 통해 사용할 수 있습니다. 이 로그는 모든 클라이언트에서 데이터를 사용하여 생성됩니다.

### <a name="abaptabledatalog_cl-log-schema"></a>ABAPTableDataLog_CL 로그 스키마

| 필드            | Description                           |
| ---------------- | ------------------------------------- |
| DBLogID          | DB 로그 ID                             |
| 호스트             | 호스트                                  |
| 인스턴스         | 다음 구문의 ABAP 인스턴스: `<HOST>_<SYSID>_<SYSNR>` |
| 언어         | 언어                              |
| LogKey           | 로그 키                               |
| NewValue         | 필드 새 값                       |
| OldValue         | 필드 이전 값                       |
| OperationTypeSQL | 작업 유형, `Insert`, `Update`, `Delete` |
| 프로그램          | 프로그램 이름                          |
| SystemID         | 시스템 ID                             |
| SystemNumber     | 시스템 번호                         |
| TableField       | 테이블 필드                           |
| TableName        | 테이블 이름                            |
| TransactionCode  | 트랜잭션 코드                      |
| UserName         | 사용자                                  |
| VersionNumber    | 버전 번호                        |
| | |

## <a name="abap-gateway-log"></a>ABAP 게이트웨이 로그

- **Azure Sentinel의 이름**: `ABAPOS_GW_CL`

- **관련 SAP 문서**: [SAP 도움말 포털](https://help.sap.com/viewer/62b4de4187cb43668d15dac48fc00732/7.5.7/en-US/48b2a710ca1c3079e10000000a42189b.html)

- **로그 용도**: 게이트웨이 활동을 모니터링합니다. SAP Control 웹 서비스에서 사용할 수 있습니다. 이 로그는 모든 클라이언트에서 데이터를 사용하여 생성됩니다.

### <a name="abapos_gw_cl-log-schema"></a>ABAPOS_GW_CL 로그 스키마

| 필드        | Description      |
| ------------ | ---------------- |
| 호스트         | 호스트             |
| 인스턴스     | 다음 구문의 ABAP 인스턴스: `<HOST>_<SYSID>_<SYSNR>`   |
| MessageText  | 메시지 텍스트     |
| 심각도     | 메시지 심각도: `Debug`, `Info`, `Warning`, `Error`  |
| SystemID     | 시스템 ID        |
| SystemNumber | 시스템 번호    |
| | |

## <a name="abap-icm-log"></a>ABAP ICM 로그

- **Azure Sentinel의 이름**: `ABAPOS_ICM_CL`

- **관련 SAP 문서**: [SAP 도움말 포털](https://help.sap.com/viewer/683d6a1797a34730a6e005d1e8de6f22/7.52.4/en-US/a10ec40d01e740b58d0a5231736c434e.html)

- **로그 용도**: 인바운드 및 아웃바운드 요청을 기록하고 HTTP 요청의 통계를 컴파일합니다.

    SAP Control 웹 서비스에서 사용할 수 있습니다. 이 로그는 모든 클라이언트에서 데이터를 사용하여 생성됩니다.

### <a name="abapos_icm_cl-log-schema"></a>ABAPOS_ICM_CL 로그 스키마

| 필드        | Description      |
| ------------ | ---------------- |
| 호스트         | 호스트             |
| 인스턴스     | 다음 구문의 ABAP 인스턴스: `<HOST>_<SYSID>_<SYSNR>`   |
| MessageText  | 메시지 텍스트     |
| 심각도     | `Debug`, `Info`, `Warning`, `Error`를 포함한 메시지 심각도   |
| SystemID     | 시스템 ID        |
| SystemNumber | 시스템 번호    |
| | |

## <a name="abap-job-log"></a>ABAP 작업 로그

- **Azure Sentinel의 이름**: `ABAPJobLog_CL`

- **관련 SAP 문서**: [SAP 도움말 포털](https://help.sap.com/viewer/b07e7195f03f438b8e7ed273099d74f3/7.31.19/en-US/4b2bc0974c594ba2e10000000a42189c.html)

- **로그 용도**: 모든 백그라운드 처리 작업 로그를 결합합니다(SM37).

    XBP 인터페이스의 표준 서비스를 기반으로 하는 사용자 지정 서비스와 함께 RFC를 통해 사용할 수 있습니다. 이 로그는 모든 클라이언트에서 데이터를 사용하여 생성됩니다.

### <a name="abapjoblog_cl-log-schema"></a>ABAPJobLog_CL 로그 스키마


| 필드               | Description                      |
| ------------------- | -------------------------------- |
| ABAPProgram         | ABAP 프로그램                     |
| BgdEventParameters  | 백그라운드 이벤트 매개 변수      |
| BgdProcessingEvent  | 백그라운드 프로세스 이벤트      |
| ClientID            | ABAP 클라이언트 ID(MANDT)           |
| DynproNumber        | Dynpro 번호                    |
| GUIStatus           | GUI status                       |
| 호스트                | 호스트                             |
| 인스턴스            | 다음 구문의 ABAP 인스턴스(HOST_SYSID_SYSNR): `<HOST>_<SYSID>_<SYSNR>` |
| JobClassification   | 직군               |
| JobCount            | 작업 수                        |
| JobGroup            | 작업 그룹                        |
| JobName             | 작업 이름                         |
| JobPriority         | 작업 우선 순위                     |
| MessageClass        | Message 클래스                    |
| MessageNumber       | 메시지 번호                   |
| MessageText         | 메시지 텍스트                     |
| MessageType         | 메시지 유형                     |
| ReleaseUser         | 작업 릴리스 사용자                 |
| SchedulingDateTime  | 날짜 시간 예약             |
| StartDateTime       | 시작 날짜 시간                  |
| SystemID            | 시스템 ID                        |
| SystemNumber        | 시스템 번호                    |
| TargetServer        | 대상 서버                    |
| 사용자                | 사용자                             |
| UserReleaseInstance | ABAP 인스턴스 - 사용자 릴리스     |
| WorkProcessID       | 작업 프로세스 ID                  |
| WorkProcessNumber   | 작업 프로세스 번호              |
| | |

## <a name="abap-security-audit-log"></a>ABAP 보안 감사 로그

- **Azure Sentinel의 이름**: `ABAPAuditLog_CL`

- **관련 SAP 문서**: [SAP 도움말 포털](https://help.sap.com/viewer/280f016edb8049e998237fcbd80558e7/7.5.7/en-US/4d41bec4aa601c86e10000000a42189b.html)

- **로그 용도**: 다음 데이터를 기록합니다.

    - 주 사용자 레코드에 대한 변경 사항과 같은 SAP 시스템 환경에 대한 보안 관련 변경 내용
    - 성공 및 실패 로그인 시도와 같은 더 높은 수준의 데이터를 제공하는 정보
    - 성공 또는 실패 트랜잭션 시작과 같은 일련의 이벤트를 재구성하는 데 사용할 수 있는 정보

    RFC XAL/SAL 인터페이스를 통해 사용할 수 있습니다. SAL은 버전 7.50부터 사용할 수 있습니다. 이 로그는 모든 클라이언트에서 데이터를 사용하여 생성됩니다.

### <a name="abapauditlog_cl-log-schema"></a>ABAPAuditLog_CL 로그 스키마

| 필드                      | Description                     |
| -------------------------- | ------------------------------- |
| ABAPProgramName            | 프로그램 이름, SAL 전용                    |
| AlertSeverity              | 경고 심각도                  |
| AlertSeverityText          | 경고 심각도 텍스트, SAL 전용             |
| AlertValue                 | 경고 값                     |
| AuditClassID               | 감사 클래스 ID, SAL 전용                 |
| ClientID                   | ABAP 클라이언트 ID(MANDT)          |
| Computer                   | 사용자 컴퓨터, SAL 전용                   |
| 메일                      | 사용자 전자 메일                      |
| 호스트                       | 호스트                                                   |
| 인스턴스                   | 다음 구문의 ABAP 인스턴스: `<HOST>_<SYSID>_<SYSNR>`                  |
| MessageClass               | Message 클래스                   |
| MessageContainerID         | 메시지 컨테이너 ID, XAL 전용            |
| MessageID                  | `‘AU1’,’AU2’…`와 같은 메시지 ID                     |
| MessageText                | 메시지 텍스트                    |
| MonitoringObjectName       | MTE 모니터 개체 이름, XAL 전용        |
| MonitorShortName           | MTE 모니터 약식 이름, XAL 전용          |
| SAPProcesType              | 시스템 로그: SAP 프로세스 유형, SAL 전용    |
| B* - 백그라운드 처리 중 |                                 |
| D* - 대화 상자 처리 중     |                                 |
| U* - 작업 업데이트         |                                 |
| SAPWPName                  | 시스템 로그: 작업 프로세스 번호, SAL 전용 |
| SystemID                   | 시스템 ID                       |
| SystemNumber               | 시스템 번호                   |
| TerminalIPv6               | 사용자 컴퓨터 IP, SAL 전용 |
| TransactionCode            | 트랜잭션 코드, SAL 전용 |
| 사용자                       | 사용자                            |
| Variable1                  | 메시지 변수 1              |
| Variable2                  | 메시지 변수 2              |
| Variable3                  | 메시지 변수 3              |
| Variable4                  | 메시지 변수 4              |
| | |

## <a name="abap-spool-log"></a>ABAP 스풀 로그

- **Azure Sentinel의 이름**: `ABAPSpoolLog_CL`

- **관련 SAP 문서**: [SAP 도움말 포털](https://help.sap.com/viewer/290ce8983cbc4848a9d7b6f5e77491b9/7.52.1/en-US/4eae791c40f72045e10000000a421937.html)

- **로그 용도**: 스풀 요청 기록이 있는 SAP 인쇄의 기본 로그 역할을 합니다. (SP01).

    표준 테이블을 기반으로 하는 사용자 지정 서비스와 함께 RFC를 통해 사용할 수 있습니다. 이 로그는 모든 클라이언트에서 데이터를 사용하여 생성됩니다.

### <a name="abapspoollog_cl-log-schema"></a>ABAPSpoolLog_CL 로그 스키마

| 필드                               | Description                                |
| ----------------------------------- | ------------------------------------------ |
| ArchiveStatus                       | 보관 상태                             |
| ArchiveType                         | 보관 형식                               |
| ArchivingDevice                     | 보관 디바이스                           |
| AutoRereoute                        | 자동 경로 다시 라우팅                              |
| ClientID                            | ABAP 클라이언트 ID(MANDT)                     |
| CountryKey                          | 국가 키                                |
| DeleteSpoolRequestAuto              | 스풀 요청 자동 삭제                  |
| DelFlag                             | 삭제 플래그                              |
| department                          | department                                 |
| DocumentType                        | 문서 형식                              |
| ExternalMode                        | 외부 모드                              |
| FormatType                          | 형식 유형                                |
| 호스트                                | 호스트                                       |
| 인스턴스                            | 다음 구문의 ABAP 인스턴스: `<HOST>_<SYSID>_<SYSNR>` |
| NumofCopies                         | 매수                           |
| OutputDevice                        | 출력 디바이스                              |
| PrinterLongName                     | 프린터 긴 이름                          |
| PrintImmediately                    | 즉시 인쇄                          |
| PrintOSCoverPage                    | OSCover 페이지 인쇄                         |
| PrintSAPCoverPage                   | SAPCover 페이지 인쇄                        |
| 우선 순위                            | 우선 순위                                   |
| RecipientofSpoolRequest             | 스풀 요청의 수신자                 |
| SpoolErrorStatus                    | 스풀 오류 상태                         |
| SpoolRequestCompleted               | 스풀 요청이 완료됨                    |
| SpoolRequestisALogForAnotherRequest | 스풀 요청아 다른 요청에 대한 로그임 |
| SpoolRequestName                    | 스풀 요청 이름                         |
| SpoolRequestNumber                  | 스풀 요청 번호                       |
| SpoolRequestSuffix1                 | 스풀 요청 접미사1                      |
| SpoolRequestSuffix2                 | 스풀 요청 접미사2                      |
| SpoolRequestTitle                   | 스풀 요청 제목                        |
| SystemID                            | 시스템 ID                                  |
| SystemNumber                        | 시스템 번호                              |
| TelecommunicationsPartner           | 통신 파트너                 |
| TelecommunicationsPartnerE          | 통신 파트너 E               |
| TemSeGeneralcounter                 | Temse 카운터                              |
| TemseNumAddProtectionRule           | Temse 번호 추가 보호 규칙              |
| TemseNumChangeProtectionRule        | Temse 번호 변경 보호 규칙           |
| TemseNumDeleteProtectionRule        | Temse 번호 삭제 보호 규칙           |
| TemSeObjectName                     | Temse 개체 이름                          |
| TemSeObjectPart                     | TemSe 개체 부분                          |
| TemseReadProtectionRule             | Temse 읽기 방지 규칙                 |
| 사용자                                | 사용자                                       |
| ValueAuthCheck                      | 값 인증 확인                           |
| | |

## <a name="apab-spool-output-log"></a>APAB 스풀 출력 로그

- **Azure Sentinel의 이름**: `ABAPSpoolOutputLog_CL`

- **관련 SAP 문서**: [SAP 도움말 포털](https://help.sap.com/viewer/290ce8983cbc4848a9d7b6f5e77491b9/7.52.1/en-US/4eae779e40f72045e10000000a421937.html)

- **로그 용도**: 스풀 출력 요청 기록이 있는 SAP 인쇄의 기본 로그 역할을 합니다. (SP02).

    표준 테이블을 기반으로 하는 사용자 지정 서비스와 함께 RFC를 통해 사용할 수 있습니다. 이 로그는 모든 클라이언트에서 데이터를 사용하여 생성됩니다.

### <a name="abapspooloutputlog_cl-log-schema"></a>ABAPSpoolOutputLog_CL 로그 스키마

| 필드                              | Description                               |
| ---------------------------------- | ----------------------------------------- |
| AppServer                          | 애플리케이션 서버                        |
| ClientID                           | ABAP 클라이언트 ID(MANDT)                    |
| 의견                            | 의견                                   |
| CopyCount                          | 횟수 복사                                |
| CopyCounter                        | 카운터 복사                              |
| department                         | department                                |
| ErrorSpoolRequestNumber            | 오류 요청 번호                      |
| FormatType                         | 형식 유형                               |
| 호스트                               | 호스트                                      |
| HostName                           | 호스트 이름                                 |
| HostSpoolerID                      | 호스트 스풀러 ID                          |
| 인스턴스                           | ABAP 인스턴스                             |
| LastPage                           | 마지막 페이지                                 |
| NumofCopies                        | 매수                              |
| OutputDevice                       | 출력 디바이스                             |
| OutputRequestNumber                | 출력 요청 번호                     |
| OutputRequestStatus                | 출력 요청 상태                     |
| PhysicalFormatType                 | 물리적 형식 유형                      |
| PrinterLongName                    | 프린터 긴 이름                         |
| PrintRequestSize                   | 인쇄 요청 크기                        |
| 우선 순위                           | 우선 순위                                  |
| ReasonforOutputRequest             | 출력 요청 이유                 |
| RecipientofSpoolRequest            | 스풀 요청의 수신자                 |
| SpoolNumberofOutputReqProcessed    | 출력 요청 수 - 처리됨     |
| SpoolNumberofOutputReqWithErrors   | 출력 요청 수 - 오류 발생   |
| SpoolNumberofOutputReqWithProblems | 출력 요청 수 - 문제 발생 |
| SpoolRequestNumber                 | 스풀 요청 번호                      |
| StartPage                          | 시작 페이지                                |
| SystemID                           | 시스템 ID                                 |
| SystemNumber                       | 시스템 번호                             |
| TelecommunicationsPartner          | 통신 파트너                |
| TemSeGeneralcounter                | Temse 카운터                             |
| 제목                              | 제목                                     |
| 사용자                               | 사용자                                      |
| | |


## <a name="abap-syslog"></a>ABAP SysLog

- **Azure Sentinel의 이름**: `ABAPOS_Syslog_CL`

- **관련 SAP 문서**: [SAP 도움말 포털](https://help.sap.com/viewer/56bf1265a92e4b4d9a72448c579887af/7.5.7/en-US/c769bcbaf36611d3a6510000e835363f.html)

- **로그 용도**: 모든 SAP NetWeaver AS(SAP NetWeaver Application Server) ABAP 시스템 오류, 경고, 알려진 사용자의 로그인 시도 실패로 인한 사용자 잠금 및 프로세스 메시지를 기록합니다.

    SAP Control 웹 서비스에서 사용할 수 있습니다. 이 로그는 모든 클라이언트에서 데이터를 사용하여 생성됩니다.

### <a name="abapos_syslog_cl-log-schema"></a>ABAPOS_Syslog_CL 로그 스키마


| 필드            | Description            |
| ---------------- | ---------------------- |
| ClientID         | ABAP 클라이언트 ID(MANDT) |
| 호스트             | 호스트                   |
| 인스턴스         | 다음 구문의 ABAP 인스턴스: `<HOST>_<SYSID>_<SYSNR> ` |
| MessageNumber    | 메시지 번호         |
| MessageText      | 메시지 텍스트           |
| 심각도         | 메시지 심각도, 다음 값 중 하나: `Debug`, `Info`, `Warning`, `Error`        |
| SystemID         | 시스템 ID              |
| SystemNumber     | 시스템 번호          |
| TransacationCode | 트랜잭션 코드       |
| Type             | SAP 프로세스 유형       |
| 사용자             | 사용자                   |
| | |


## <a name="abap-workflow-log"></a>ABAP 워크플로 로그

- **Azure Sentinel의 이름**: `ABAPWorkflowLog_CL`

- **관련 SAP 문서**: [SAP 도움말 포털](https://help.sap.com/viewer/56bf1265a92e4b4d9a72448c579887af/7.5.7/en-US/c769bcccf36611d3a6510000e835363f.html)

- **로그 용도**: SAP Business Workflow(WebFlow 엔진)를 사용하면 아직 SAP 시스템에 매핑되지 않은 비즈니스 프로세스를 정의할 수 있습니다.

    예를 들어 매핑되지 않은 비즈니스 프로세스는 간단한 릴리스 또는 승인 절차일 수 있거나, 기본 자료를 만든 다음 관련 부서를 조정하는 등 더 복잡한 비즈니스 프로세스일 수 있습니다.

    표준 테이블 및 표준 서비스를 기반으로 하는 사용자 지정 서비스와 함께 RFC를 통해 사용할 수 있습니다. 이 로그는 클라이언트별로 생성됩니다.

### <a name="abapworkflowlog_cl-log-schema"></a>ABAPWorkflowLog_CL 로그 스키마


| 필드               | Description                      |
| ------------------- | -------------------------------- |
| ActualAgent         | 실제 에이전트                     |
| 주소             | 주소                          |
| ApplicationArea     | 애플리케이션 영역                 |
| CallbackFunction    | 콜백 함수                |
| ClientID            | ABAP 클라이언트 ID(MANDT)           |
| CreationDateTime    | 만든 날짜 시간               |
| 만든 이             | 만든 이                          |
| CreatorAddress      | 작성자 주소                  |
| ErrorType           | 오류 유형                       |
| ExceptionforMethod  | 메서드에 대한 예외             |
| 호스트                | 호스트                             |
| 인스턴스            | 다음 구문의 ABAP 인스턴스(HOST_SYSID_SYSNR): `<HOST>_<SYSID>_<SYSNR>` |
| 언어            | 언어                         |
| LogCounter          | 로그 카운터                      |
| MessageNumber       | 메시지 번호                   |
| MessageType         | 메시지 유형                     |
| MethodUser          | 메서드 사용자                      |
| 우선 순위            | 우선 순위                         |
| SimpleContainer     | 간단한 컨테이너, 작업 항목에 대한 키-값 엔터티 목록으로 압축됨 |
| 상태              | 상태                           |
| SuperWI             | 슈퍼 WI                         |
| SystemID            | 시스템 ID                        |
| SystemNumber        | 시스템 번호                    |
| TaskID              | 작업 ID                          |
| TasksClassification | 작업 분류            |
| TaskText            | 작업 텍스트                        |
| TopTaskID           | 상위 작업 ID                      |
| UserCreated         | 사용자가 생성함                     |
| WIText              | 작업 항목 텍스트                   |
| WIType              | 작업 항목 형식                   |
| WorkflowAction      | 워크플로 작업                  |
| WorkItemID          | 작업 항목 ID                     |
| | |





## <a name="abap-workprocess-log"></a>ABAP 작업 프로세스 로그

- **Azure Sentinel의 이름**: `ABAPOS_WP_CL`

- **관련 SAP 문서**: [SAP 도움말 포털](https://help.sap.com/viewer/d0739d980ecf42ae9f3b4c19e21a4b6e/7.3.15/en-US/46fb763b6d4c5515e10000000a1553f6.html)

- **로그 용도**: 모든 작업 프로세스 로그를 결합합니다. (기본값 `dev_*`)

    SAP Control 웹 서비스에서 사용할 수 있습니다. 이 로그는 모든 클라이언트에서 데이터를 사용하여 생성됩니다.

### <a name="abapos_wp_cl-log-schema"></a>ABAPOS_WP_CL 로그 스키마


| 필드        | Description         |
| ------------ | ------------------- |
| 호스트         | 호스트                |
| 인스턴스     | 다음 구문의 ABAP 인스턴스: `<HOST>_<SYSID>_<SYSNR>`   |
| MessageText  | 메시지 텍스트     |
| 심각도     | 메시지 심각도: `Debug`, `Info`, `Warning`, `Error`  |
| SystemID     | 시스템 ID           |
| SystemNumber | 시스템 번호       |
| WPNumber     | 작업 프로세스 번호 |
| | |


## <a name="hana-db-audit-trail"></a>HANA DB 감사 내역

- **Azure Sentinel의 이름**: `Syslog`

- **관련 SAP 문서**: [일반](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.03/en-US/48fd6586304c4f859bf92d64d0cd8b08.html)  |   [감사 내역](https://help.sap.com/viewer/b3ee5778bc2e4a089d3299b82ec762a7/2.0.03/en-US/0a57444d217649bf94a19c0b68b470cc.html)

- **로그 용도**: SAP HANA 데이터베이스에 사용자 작업 또는 시도한 작업을 기록합니다. 예를 들어 이를 통해 중요한 데이터에 대한 읽기 액세스를 기록하고 모니터링할 수 있습니다.

    Syslog용 Sentinel Linux 에이전트에서 사용할 수 있습니다. 이 로그는 모든 클라이언트에서 데이터를 사용하여 생성됩니다.

### <a name="syslog-log-schema"></a>Syslog 로그 스키마

| 필드         | Description  |
| ------------- | ------------ |
| Computer      | 호스트 이름    |
| HostIP        | 호스트 IP      |
| HostName      | 호스트 이름    |
| ProcessID     | 프로세스 ID   |
| ProcessName   | 프로세스 이름: `HDB*` |
| SeverityLevel | 경고        |
| SourceSystem  |   원본 시스템 OS, `Linux`           |
| SyslogMessage | 메시지, 구문 분석되지 않은 감사 내역 메시지      |
| | |

## <a name="java-files"></a>JAVA 파일

- **Azure Sentinel의 이름**: `JavaFilesLogsCL`

- **관련 SAP 문서**: [일반](https://help.sap.com/viewer/2f8b1599655d4544a3d9c6d1a9b6546b/7.5.9/en-US/485059dfe31672d4e10000000a42189c.html)  |  [Java 보안 감사 로그](https://help.sap.com/viewer/1531c8a1792f45ab95a4c49ba16dc50b/7.5.9/en-US/4b6013583840584ae10000000a42189c.html)

- **로그 용도**: 보안 감사 로그, 시스템(클러스터 및 서버 프로세스), 성능 및 게이트웨이 로그를 포함한 모든 Java 파일 기반 로그를 결합합니다. 개발자 추적 및 기본 추적 로그도 포함합니다.

    SAP Control 웹 서비스에서 사용할 수 있습니다. 이 로그는 모든 클라이언트에서 데이터를 사용하여 생성됩니다.

### <a name="javafileslogscl-log-schema"></a>JavaFilesLogsCL 로그 스키마


| 필드            | Description          |
| ---------------- | -------------------- |
| 애플리케이션      | Java 애플리케이션     |
| ClientID         | 클라이언트 ID           |
| CSNComponent     | CSN 구성 요소(예: `BC-XI-IBD`) |
| DCComponent      | DC 구성 요소(예: `com.sap.xi.util.misc`) |
| DSRCounter       | DSR 카운터          |
| DSRRootContentID | DSR 컨텍스트 GUID     |
| DSRTransaction   | DSR 트랜잭션 GUIDMNo |
| 호스트             | 호스트                 |
| 인스턴스         | 다음 구문의 Java 인스턴스: `<HOST>_<SYSID>_<SYSNR>` |
| Location         | Java 클래스           |
| LogName          | Java logName(예: `Available`, `defaulttrace`, `dev*`, `security` 등)
| MessageText      | 메시지 텍스트         |
| MNO              | 메시지 번호       |
| Pid              | 프로세스 ID           |
| 프로그램          | 프로그램 이름         |
| 세션          | 세션              |
| 심각도         | 메시지 심각도(예: `Debug`, `Info`, `Warning`, `Error` 등)     |
| 해결 방법         | 해결 방법             |
| SystemID         | 시스템 ID            |
| SystemNumber     | 시스템 번호        |
| ThreadName       | 스레드 이름          |
| Throw됨           | throw된 예외     |
| TimeZone         | 표준 시간대             |
| 사용자             | 사용자                 |
| | |

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음을 참조하세요.

- [SAP용 Azure Sentinel 솔루션 배포](sap-deploy-solution.md)
- [Azure Sentinel SAP 솔루션 자세한 SAP 요구 사항](sap-solution-detailed-requirements.md)
- [SNC로 Azure Sentinel SAP 데이터 커넥터 배포](sap-solution-deploy-snc.md)
- [전문가 구성 옵션, 온-프레미스 배포, SAPControl 로그 원본](sap-solution-deploy-alternate.md)
- [Azure Sentinel SAP 솔루션: 기본 제공 보안 콘텐츠](sap-solution-security-content.md)
- [Azure Sentinel SAP 솔루션 배포 문제 해결](sap-deploy-troubleshoot.md)

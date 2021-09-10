---
title: Azure Sentinel 프로세스 이벤트 정규화 스키마 참조 | Microsoft Docs
description: 이 문서에서는 Azure Sentinel 프로세스 이벤트 정규화 스키마에 대해 설명합니다.
services: sentinel
cloud: na
documentationcenter: na
author: batamig
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 06/22/2021
ms.author: bagol
ms.openlocfilehash: 2f7425d987e878d843271b7222d00f4e326d6478
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/20/2021
ms.locfileid: "122568394"
---
# <a name="azure-sentinel-process-event-normalization-schema-reference-public-preview"></a>Azure Sentinel 프로세스 이벤트 정규화 스키마 참조(퍼블릭 미리 보기)

프로세스 이벤트 정규화 스키마는 프로세스를 실행하고 종료하는 운영 체제 작업을 설명하는 데 사용됩니다. 해당 이벤트는 EDR(엔드포인트 검색 및 응답) 시스템과 같은 운영 체제 및 보안 시스템에서 보고합니다.

OSSEM에서 정의한 프로세스는 프로그램의 실행 인스턴스를 나타내는 포함 및 관리 개체입니다. 프로세스 자체는 실행되지 않지만 코드를 실행하는 스레드를 관리합니다.

Azure Sentinel의 정규화에 대한 자세한 내용은 [정규화 및 ASIM(Azure Sentinel 정보 모델)](normalization.md)을 참조하세요.

> [!IMPORTANT]
> 프로세스 이벤트 정규화 스키마는 현재 미리 보기 상태입니다. 해당 기능은 별도의 서비스 수준 계약 없이 제공되며, 프로덕션 작업에는 사용하지 않는 것이 좋습니다.
>
> [Azure Preview 추가 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타, 미리 보기 또는 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 법률 용어가 포함되어 있습니다.
>

## <a name="parsers"></a>파서

Azure Sentinel에서 제공하는 제품별 기본 제공 프로세스 이벤트 파서는 다음과 같습니다.

- **보안 이벤트 프로세스 생성(이벤트 4688)** , Log Analytics 에이전트 또는 Azure Monitor 에이전트를 사용하여 수집
- **보안 이벤트 프로세스 종료(이벤트 4689)** , Log Analytics 에이전트 또는 Azure Monitor 에이전트를 사용하여 수집
- **Sysmon 프로세스 생성(이벤트 1)** , Log Analytics 에이전트 또는 Azure Monitor 에이전트를 사용하여 수집
- **Sysmon 프로세스 종료(이벤트 5)** , Log Analytics 에이전트 또는 Azure Monitor 에이전트를 사용하여 수집
- **Microsoft 365 Defender for Endpoints 프로세스 생성**

나열된 모든 파서를 통합하고 구성된 모든 원본에서 분석할 수 있도록 원본 독립적 파서를 사용하려면 쿼리에 다음 테이블 이름을 사용합니다.

- 프로세스 생성 정보가 필요한 쿼리의 경우 **imProcessCreate**. 이 쿼리가 가장 일반적인 사례입니다.
- 프로세스 종료 정보가 필요한 쿼리의 경우 **imProcessTerminate**.
- 프로세스 생성 및 종료 정보가 모두 필요한 쿼리의 경우 **imProcessEvents**. 이러한 경우 `EventType` 필드를 사용하여 이벤트를 구분할 수 있으며 각각 `ProcessCreate` 또는 `ProcessTerminate`로 설정됩니다. 프로세스 종료 이벤트는 일반적으로 프로세스 생성 이벤트보다 훨씬 적은 정보를 포함합니다.

[Azure Sentinel GitHub 리포지토리](https://aka.ms/AzSentinelProcessEvents)에서 [원본 독립적 파서와 원본 특정 파서](normalization-about-parsers.md)를 배포합니다.

## <a name="add-your-own-normalized-parsers"></a>정규화된 고유 파서 추가

[프로세스 이벤트](normalization-about-schemas.md#the-process-entity) 정보 모델의 사용자 지정 파서를 구현할 때 `imProcess<Type><vendor><Product>` 구문을 사용하여 KQL 함수의 이름을 지정합니다. 여기에서 `Type`은 `Create`, `Terminate` 또는 `Event`(파서가 생성 및 종료 이벤트를 모두 구현하는 경우)입니다.

[프로세스 이벤트](normalization-about-schemas.md#the-process-entity) 모델을 사용하는 모든 콘텐츠가 새 파서를 사용하도록 KQL 함수를 `imProcess<Type>` 및 `imProcess` 원본 독립적 파서에 추가합니다.

## <a name="normalized-content-for-process-activity-data"></a>프로세스 활동 데이터에 대한 정규화된 콘텐츠

다음 Azure Sentinel 콘텐츠는 Azure Sentinel 정보 모델을 사용하여 정규화된 모든 프로세스 활동에서 작동합니다.

- **분석 규칙**:

   - [가능한 AdFind 정찰 도구 사용(정규화된 프로세스 이벤트)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimProcess/imProcess_AdFind_Usage.yaml)
   - [Base64 인코딩 Windows 프로세스 명령줄(정규화된 프로세스 이벤트)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimProcess/imProcess_base64_encoded_pefile.yaml)
   - [휴지통의 맬웨어(정규화된 프로세스 이벤트)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimProcess/imProcess_malware_in_recyclebin.yaml)
   - [NOBELIUM-vbscript의 의심스러운 rundll32.exe 실행(정규화된 프로세스 이벤트)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimProcess/imProcess_NOBELIUM_SuspiciousRundll32Exec.yaml)
   - [의심스러운 SUNBURST SolarWinds 자식 프로세스(정규화된 프로세스 이벤트)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimProcess/imProcess_SolarWinds_SUNBURST_Process-IOCs.yaml)

   자세한 내용은 [위협 탐지를 위한 사용자 지정 분석 규칙 만들기](detect-threats-custom.md)를 참조하세요.

- **헌팅 쿼리**:
    - [Cscript 스크립트 일별 요약 분석(정규화된 프로세스 이벤트)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_cscript_summary.yaml)
    - [사용자 및 그룹의 열거(정규화된 프로세스 이벤트)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_enumeration_user_and_group.yaml)
    - [Exchange PowerShell 스냅인 추가(정규화된 프로세스 이벤트)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_ExchangePowerShellSnapin.yaml)
    - [사서함을 내보내고 내보내기를 제거하는 호스트(정규화된 프로세스 이벤트)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_HostExportingMailboxAndRemovingExport.yaml)
    - [Invoke-PowerShellTcpOneLine 사용(정규화된 프로세스 이벤트)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_Invoke-PowerShellTcpOneLine.yaml)
    - [Base64의 Nishang 역방향 TCP Shell(정규화된 프로세스 이벤트)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_NishangReverseTCPShellBase64.yaml)
    - [흔하지 않거나 문서화되지 않은 명령줄 스위치를 사용하여 생성된 사용자 요약(정규화된 프로세스 이벤트)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_persistence_create_account.yaml)
    - [Powercat 다운로드(정규화된 프로세스 이벤트)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_PowerCatDownload.yaml)
    - [PowerShell 다운로드(정규화된 프로세스 이벤트)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_powershell_downloads.yaml)
    - [지정된 호스트에 대한 프로세스의 엔트로피(정규화된 프로세스 이벤트)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_ProcessEntropy.yaml)
    - [SolarWinds 인벤토리(정규화된 프로세스 이벤트)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_SolarWindsInventory.yaml)
    - [Adfind 도구를 사용한 의심스러운 열거형(정규화된 프로세스 이벤트)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_Suspicious_enumeration_using_adfind.yaml)
    - [Windows 시스템 종료/다시 부팅(정규화된 프로세스 이벤트)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_Windows%20System%20Shutdown-Reboot(T1529).yaml)
    - [Certutil(LOLBins 및 LOLScripts, 정규화된 프로세스 이벤트)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_Certutil-LOLBins.yaml)
    - [Rundll32.exe(LOLBins 및 LOLScripts, 정규화된 프로세스 이벤트)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/inProcess_SignedBinaryProxyExecutionRundll32.yaml)
    - [흔하지 않은 프로세스 - 하위 5%(정규화된 프로세스 이벤트)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_uncommon_processes.yaml)
    - [명령줄에서 유니코드 난독 처리](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/MultipleDataSources/UnicodeObfuscationInCommandLine.yaml)


    자세한 내용은 [Azure Sentinel을 사용하여 위협 헌팅](hunting.md)을 참조하세요.

## <a name="schema-details"></a>스키마 세부 정보

프로세스 이벤트 정보 모델은 [OSSEM 프로세스 엔터티 스키마](https://github.com/OTRF/OSSEM/blob/master/docs/cdm/entities/process.md)에 맞춰 조정됩니다.

### <a name="log-analytics-fields"></a>Log Analytics 필드

다음 필드는 Log Analytics에서 레코드마다 생성되며, 사용자 지정 커넥터를 만들 때 재정의할 수 있습니다.

| 필드         | 형식     | 토론(Discussion)      |
| ------------- | -------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| <a name="timegenerated"></a>**TimeGenerated** | Datetime | 보고 디바이스에서 이벤트가 생성된 시간입니다.|
| **_ResourceId**   | guid     | 보고 디바이스 또는 서비스의 Azure 리소스 ID이거나 Syslog, CEF 또는 WEF를 사용하여 전달된 이벤트에 대한 로그 전달자 리소스 ID입니다. |
| | | |

> [!NOTE]
> Log Analytics는 보안 사용 사례와 관련성이 낮은 다른 필드도 추가합니다. 자세한 내용은 [Azure Monitor 로그의 표준 열](../azure-monitor/logs/log-standard-columns.md)을 참조하세요.
>

## <a name="event-fields"></a>이벤트 필드

이벤트 필드는 모든 스키마에 공통되며, 활동 자체와 보고 디바이스를 설명합니다.

| 필드               | 클래스       | 형식       |  설명        |
|---------------------|-------------|------------|--------------------|
| **EventMessage**        | 선택    | 문자열     |     레코드에 포함되거나 레코드에서 생성된 일반 메시지 또는 설명입니다.   |
| **EventCount**          | 필수   | 정수    |     레코드에서 설명하는 이벤트 수입니다. <br><br>이 값은 원본에서 집계를 지원할 때 사용되며, 단일 레코드에서 여러 이벤트를 나타낼 수 있습니다. <br><br>다른 원본의 경우 `1`로 설정합니다.   |
| **EventStartTime**      | 필수   | 날짜/시간  |      원본에서 집계를 지원하고 레코드에서 여러 이벤트를 나타내는 경우 이 필드는 첫 번째 이벤트가 생성된 시간을 지정합니다. 그러지 않은 경우 이 필드는 [TimeGenerated](#timegenerated) 필드의 별칭을 지정합니다. |
| **EventEndTime**        | 필수   | Alias      |      [TimeGenerated](#timegenerated) 필드에 대한 별칭입니다.    |
| **EventType**           | 필수   | Enumerated |    레코드에서 보고하는 작업을 설명합니다. <br><br>프로세스 레코드의 경우 지원되는 값은 다음과 같습니다. <br>- `ProcessCreated` <br>- `ProcessTerminated` |
| **EventResult**         | 필수   | Enumerated |  지원되는 다음 값 중 하나로 정규화된 이벤트의 결과를 설명합니다. <br><br>- `Success`<br>- `Partial`<br>- `Failure`<br>- `NA`(해당 사항 없음) <br><br>원본에서 **EventResult** 값을 얻기 위해 분석해야 하는 **EventResultDetails** 필드에 대한 값만 제공할 수 있습니다.<br><br>**참고**: 프로세스 이벤트는 일반적으로 성공만 보고합니다. |
| **EventOriginalUid**    | 선택    | 문자열     |   원본에서 제공하는 경우 원래 레코드의 고유 ID입니다.<br><br>예: `69f37748-ddcd-4331-bf0f-b137f1ea83b`|
| **EventOriginalType**   | 선택    | 문자열     |   원본에서 제공하는 경우 원본 이벤트 유형 또는 ID입니다.<br><br>예: `4688`|
| <a name ="eventproduct"></a>**EventProduct**        | 필수   | String     |             이벤트를 생성하는 제품 <br><br>예: `Sysmon`<br><br>**참고**: 이 필드는 원본 레코드에서 사용할 수 없습니다. 이러한 경우 이 필드는 파서에서 설정해야 합니다.           |
| **EventProductVersion** | 선택    | 문자열     | 이벤트를 생성하는 제품의 버전 <br><br>예: `12.1`      |
| **EventVendor**         | 필수   | String     |           이벤트를 생성하는 제품의 공급 업체 <br><br>예: `Microsoft`  <br><br>**참고**: 이 필드는 원본 레코드에서 사용할 수 없습니다. 이러한 경우 이 필드는 파서에서 설정해야 합니다.  |
| **EventSchemaVersion**  | 필수   | String     |    스키마의 버전입니다. 여기에 설명된 스키마의 버전은 `0.1`입니다.         |
| **EventReportUrl**      | 선택    | 문자열     | 이벤트에 대한 추가 정보를 제공하는 리소스에 대해 이벤트에 제공된 URL입니다.|
| **Dvc** | 필수       | String     |                이벤트가 발생한 디바이스의 고유 식별자입니다. <br><br>이 필드는 [DvcId](#dvcid), [DvcHostname](#dvchostname) 또는 [DvcIpAddr](#dvcipaddr) 필드의 별칭을 지정할 수 있습니다. 명백한 디바이스가 없는 클라우드 원본의 경우 [이벤트 제품](#eventproduct) 필드와 동일한 값을 사용하세요.            |
| <a name ="dvcipaddr"></a>**DvcIpAddr**           | 권장 | IP 주소 |         프로세스 이벤트가 발생한 디바이스의 IP 주소입니다.  <br><br>예: `45.21.42.12`    |
| <a name ="dvchostname"></a>**DvcHostname**         | 권장 | 호스트 이름   |               프로세스 이벤트가 발생한 디바이스의 호스트 이름입니다. <br><br>예: `ContosoDc.Contoso.Azure`               |
| <a name ="dvcid"></a>**DvcId**               | 선택    | 문자열     |  프로세스 이벤트가 발생한 디바이스의 고유 ID입니다. <br><br>예: `41502da5-21b7-48ec-81c9-baeea8d7d669`   |
| **DvcMacAddr**          | 선택 사항    | MAC        |   프로세스 이벤트가 발생한 디바이스의 MAC입니다.  <br><br>예: `00:1B:44:11:3A:B7`       |
| **DvcOs**               | 선택    | 문자열     |         프로세스 이벤트가 발생한 디바이스에서 실행 중인 운영 체제입니다.    <br><br>예: `Windows`    |
| **DvcOsVersion**        | 선택    | 문자열     |   프로세스 이벤트가 발생한 디바이스의 운영 체제 버전입니다. <br><br>예: `10` |
| **AdditionalFields**    | 선택 사항    | 동적    | 원본에서 보존할 가치가 있는 추가 정보를 제공하는 경우 원본 필드 이름으로 유지하거나 동적 **AdditionalFields** 필드를 만들고 추가 정보를 키/값 쌍으로 추가합니다.    |
| | | | |

### <a name="process-event-specific-fields"></a>프로세스 이벤트 관련 필드

아래 표에 나열된 필드는 프로세스 이벤트와 관련이 있지만, 다른 스키마의 필드와 비슷하며 비슷한 명명 규칙을 따릅니다.

프로세스 이벤트 스키마는 프로세스 만들기 및 종료 활동의 핵심인 다음 엔터티를 참조합니다.

- **Actor**. 프로세스 만들기 또는 종료를 시작한 사용자입니다.
- **ActingProcess**. 프로세스 만들기 또는 종료를 시작하는 작업자가 사용하는 프로세스입니다.
- **TargetProcess**. 새 프로세스입니다.
- **TargetUser**. 새 프로세스를 만드는 데 사용되는 자격 증명을 소유한 사용자입니다.
- **ParentProcess**. 작업자 프로세스를 시작한 프로세스입니다.

| 필드          | 클래스        | 형식       | 설명   |
|---------------|--------------|------------|-----------------|
| **사용자**           | Alias        |            | [TargetUsername](#targetusername)에 대한 별칭입니다. <br><br>예: `CONTOSO\dadmin`     |
| **처리**        | Alias        |            | [TargetProcessName](#targetprocessname)에 대한 별칭 <br><br>예: `C:\Windows\System32\rundll32.exe`|
| **CommandLine**    | Alias        |            |     [TargetProcessCommandLine](#targetprocesscommandline)의 별칭  |
| **해시**           | Alias        |            |       가장 사용 가능한 해시의 별칭입니다. |
| <a name="actorusername"></a>**ActorUsername**  | 필수    | String     | 이벤트를 시작한 사용자의 사용자 이름입니다. <br><br>예: `CONTOSO\WIN-GG82ULGC9GO$`     |
| **ActorUsernameType**              | 필수    | Enumerated |   [ActorUsername](#actorusername) 필드에 저장된 사용자 이름의 형식을 지정합니다. 자세한 내용은 [사용자 엔터티](normalization-about-schemas.md#the-user-entity)를 참조하세요. <br><br>예: `Windows`       |
| <a name="actoruserid"></a>**ActorUserId**    | 권장  | String     |   작업자의 고유 ID입니다. 특정 ID는 이벤트를 생성하는 시스템에 따라 달라집니다. 자세한 내용은 [사용자 엔터티](normalization-about-schemas.md#the-user-entity)를 참조하세요.  <br><br>예: `S-1-5-18`    |
| **ActorUserIdType**| 권장  | String     |  [ActorUserId](#actoruserid) 필드에 저장된 ID의 형식입니다. 자세한 내용은 [사용자 엔터티](normalization-about-schemas.md#the-user-entity)를 참조하세요. <br><br>예: `SID`         |
| **ActorSessionId** | 선택     | 문자열     |   작업자 로그인 세션의 고유 ID입니다.  <br><br>예: `999`<br><br>**참고**: 유형은 다양한 시스템을 지원하기 위해 *문자열* 로 정의되지만 Windows에서는 이 값이 숫자여야 합니다. <br><br>Windows 머신을 사용하고 다른 형식을 사용하는 경우에는 값을 변환해야 합니다. 예를 들어 16진수 값을 사용한 경우 10진수 값으로 변환합니다.   |
| **ActingProcessCommandLine**       | 선택     | 문자열     |   작업 프로세스를 실행하는 데 사용되는 명령줄입니다. <br><br>예: `"choco.exe" -v`    |
| **ActingProcessName**              | 선택     | 문자열     |   작업 프로세스의 이름입니다. 이 이름은 일반적으로 프로세스의 가상 주소 공간에 매핑되는 초기 코드와 데이터를 정의하는 데 사용되는 이미지 또는 실행 파일에서 파생됩니다.<br><br>예: `C:\Windows\explorer.exe`  |
| **ActingProcessFileCompany**       | 선택     | 문자열     |           작동하는 프로세스 이미지 파일을 만든 회사입니다.  <br><br> 예: `Microsoft`    |
| **ActingProcessFileDescription**   | 선택     | 문자열     |  작동 중인 프로세스 이미지 파일의 버전 정보에 포함된 설명입니다. <br><br>예제: `Notepad++ : a free (GPL) source code editor` |
| **ActingProcessFileProduct**       | 선택     | 문자열     |작동 중인 프로세스 이미지 파일에 있는 버전 정보의 제품 이름입니다. <br><br> 예: `Notepad++`           |
| **ActingProcessFileVersion**       | 선택     | 문자열     |               작동 중인 프로세스 이미지 파일에 있는 버전 정보의 제품 버전입니다. <br><br>예: `7.9.5.0`   |
| **ActingProcessFileInternalName**  | 선택     | 문자열     |      작동 중인 프로세스 이미지 파일에 있는 버전 정보의 제품 내부 파일 이름입니다. |
| **ActingProcessFileOriginalName** | 선택     | 문자열     |작동 중인 프로세스 이미지 파일에 있는 버전 정보의 제품 원본 파일 이름입니다.       <br><br> 예제: `Notepad++.exe` |
| **ActingProcessIsHidden**          | 선택 사항     | 부울    |      작동 중인 프로세스가 숨겨진 모드인지 나타냅니다.  |
| **ActingProcessInjectedAddress**   | 선택     | 문자열     |      담당 작업 프로세스가 저장되는 메모리 주소입니다.           |
| **ActingProcessId**| 필수    | int        | 작동하는 프로세스 PID(프로세스 ID)입니다.<br><br>예제: `48610176`           <br><br>**참고**: 유형은 다양한 시스템을 지원하기 위해 *문자열* 로 정의되지만 Windows와 Linux에서는 이 값이 숫자여야 합니다. <br><br>Windows 또는 Linux 머신을 사용하고 다른 형식을 사용하는 경우에는 값을 변환해야 합니다. 예를 들어 16진수 값을 사용한 경우 10진수 값으로 변환합니다.    |
| **ActingProcessGuid**              | 선택     | 문자열     |  작업 프로세스의 생성된 GUID(고유 식별자)입니다. 시스템 전체에서 프로세스를 식별할 수 있습니다.  <br><br> 예: `EF3BD0BD-2B74-60C5-AF5C-010000001E00`            |
| **ActingProcessIntegrityLevel**    | 선택     | 문자열     |       모든 프로세스에는 토큰에 표시되는 무결성 수준이 있습니다. 무결성 수준은 보호 또는 액세스의 프로세스 수준을 결정합니다. <br><br> Windows는 **낮음**, **중간**, **높음** 및 **시스템** 과 같은 무결성 수준을 정의합니다. 표준 사용자는 **중간** 무결성 수준을 받고 고급 사용자는 **높음** 무결성 수준을 받습니다. <br><br> 자세한 내용은 [필수 무결성 제어 - Win32 앱](/windows/win32/secauthz/mandatory-integrity-control)을 참조하세요. |
| **ActingProcessMD5**               | 선택     | 문자열     |작업 프로세스 이미지 파일의 MD5 해시입니다.  <br><br>예제: `75a599802f1fa166cdadb360960b1dd0`|
| **ActingProcessSHA1**              | 선택 사항     | SHA1       | 작업 프로세스 이미지 파일의 SHA-1 해시입니다.             <br><br>  예: `d55c5a4df19b46db8c54c801c4665d3338acdab0`  |
| **ActingProcessSHA256**            | 선택 사항     | SHA256     | 작업 프로세스 이미지 파일의 SHA-256 해시입니다.    <br><br> 예: <br> `e81bb824c4a09a811af17deae22f22dd`<br>`2e1ec8cbb00b22629d2899f7c68da274`   |
| **ActingProcessSHA512**            | 선택 사항     | SHA521     |       작업 프로세스 이미지 파일의 SHA-512 해시입니다.       |
| **ActingProcessIMPHASH**           | 선택     | 문자열     |       작업 프로세스에서 사용되는 모든 라이브러리 DLL의 가져오기 해시입니다.    |
| **ActingProcessCreationTime**      | 선택 사항     | DateTime   |       작업 프로세스가 시작된 날짜와 시간입니다. |
| **ActingProcessTokenElevation**    | 선택     | 문자열     | 작업 프로세스에 적용되는 UAC(사용자 액세스 제어) 권한 상승의 유무를 나타내는 토큰입니다.   <br><br>예제: `None`|
| **ActingProcessFileSize**          | 선택 사항     | long       |      작업 프로세스를 실행한 파일의 크기입니다.   |
| **ParentProcessName**              | 선택     | 문자열     | 부모 프로세스의 이름입니다. 이 이름은 일반적으로 프로세스의 가상 주소 공간에 매핑되는 초기 코드와 데이터를 정의하는 데 사용되는 이미지 또는 실행 파일에서 파생됩니다.<br><br>예: `C:\Windows\explorer.exe` |
| **ParentProcessFileCompany**       | 선택     | 문자열     |부모 프로세스 이미지 파일을 만든 회사의 이름입니다.            <br><br>    예제: `Microsoft`   |
| **ParentProcessFileDescription**   | 선택     | 문자열     |  부모 프로세스 이미지 파일에 있는 버전 정보의 설명입니다.    <br><br>예: `Notepad++ : a free (GPL) source code editor`|
| **ParentProcessFileProduct**       | 선택     | 문자열     |부모 프로세스 이미지 파일에 있는 버전 정보의 제품 이름입니다.    <br><br>  예제: `Notepad++`  |
| **ParentProcessFileVersion**       | 선택     | 문자열     | 부모 프로세스 이미지 파일에 있는 버전 정보의 제품 버전입니다.    <br><br> 예제: `7.9.5.0` |
| **ParentProcessIsHidden**          | 선택 사항     | 부울    |   부모 프로세스가 숨겨진 모드인지 나타냅니다.  |
| **ParentProcessInjectedAddress**   | 선택     | 문자열     |    담당 부모 프로세스가 저장되는 메모리 주소입니다.           |
| **ParentProcessId**| 필수    | integer    | 부모 프로세스의 PID(프로세스 ID)입니다.   <br><br>     예제: `48610176`    |
| **ParentProcessGuid**              | 선택     | 문자열     |  부모 프로세스의 생성된 GUID(고유 식별자)입니다.  시스템 전체에서 프로세스를 식별할 수 있습니다.    <br><br> 예: `EF3BD0BD-2B74-60C5-AF5C-010000001E00` |
| **ParentProcessIntegrityLevel**    | 선택     | 문자열     |   모든 프로세스에는 토큰에 표시되는 무결성 수준이 있습니다. 무결성 수준은 보호 또는 액세스의 프로세스 수준을 결정합니다. <br><br> Windows는 **낮음**, **중간**, **높음** 및 **시스템** 과 같은 무결성 수준을 정의합니다. 표준 사용자는 **중간** 무결성 수준을 받고 고급 사용자는 **높음** 무결성 수준을 받습니다. <br><br> 자세한 내용은 [필수 무결성 제어 - Win32 앱](/windows/win32/secauthz/mandatory-integrity-control)을 참조하세요. |
| **ParentProcessMD5**               | 선택 사항     | MD5        | 부모 프로세스 이미지 파일의 MD5 해시입니다.  <br><br>예: `75a599802f1fa166cdadb360960b1dd0`|
| **ParentProcessSHA1**              | 선택 사항     | SHA1       | 부모 프로세스 이미지 파일의 SHA-1 해시입니다.       <br><br> 예제: `d55c5a4df19b46db8c54c801c4665d3338acdab0`   |
| **ParentProcessSHA256**            | 선택 사항     | SHA256     |부모 프로세스 이미지 파일의 SHA-256 해시입니다.      <br><br>  예: <br> `e81bb824c4a09a811af17deae22f22dd`<br>`2e1ec8cbb00b22629d2899f7c68da274` |
| **ParentProcessSHA512**            | 선택 사항     | SHA512     |    부모 프로세스 이미지 파일의 SHA-512 해시입니다.       |
| **ParentProcessIMPHASH**           | 선택     | 문자열     |    부모 프로세스에서 사용되는 모든 라이브러리 DLL의 가져오기 해시입니다.    |
| **ParentProcessTokenElevation**    | 선택     | 문자열     |부모 프로세스에 적용되는 UAC(사용자 액세스 제어) 권한 상승의 유무를 나타내는 토큰입니다.     <br><br>  예: `None` |
| **ParentProcessCreationTime**      | 선택 사항    | DateTime   |    부모 프로세스가 시작된 날짜와 시간입니다. |
| <a name="targetusername"></a>**TargetUsername** | 프로세스 만들기 이벤트에 필수입니다. | String     | 대상 사용자의 사용자 이름입니다.  <br><br>예: `CONTOSO\WIN-GG82ULGC9GO$`      |
| **TargetUsernameType**             | 프로세스 만들기 이벤트에 필수입니다.   | Enumerated | [TargetUsername](#targetusername) 필드에 저장된 사용자 이름의 형식을 지정합니다. 자세한 내용은 [사용자 엔터티](normalization-about-schemas.md#the-user-entity)를 참조하세요.          <br><br>  예제: `Windows`        |
|<a name="targetuserid"></a> **TargetUserId**   | 권장 | String     | 대상 사용자의 고유 ID입니다. 특정 ID는 이벤트를 생성하는 시스템에 따라 달라집니다. 자세한 내용은 [사용자 엔터티](normalization-about-schemas.md#the-user-entity)를 참조하세요.            <br><br> 예: `S-1-5-18`    |
| **TargetUserIdType**               | 권장 | String     | [TargetUserId](#targetuserid) 필드에 저장된 사용자 ID의 형식입니다. 자세한 내용은 [사용자 엔터티](normalization-about-schemas.md#the-user-entity)를 참조하세요.            <br><br> 예제: `SID`  |
| **TargetUserSessionId**            | 선택     | 문자열     |대상 사용자의 로그인 세션에 대한 고유 ID입니다. <br><br>예: `999`          <br><br>**참고**: 유형은 다양한 시스템을 지원하기 위해 *문자열* 로 정의되지만 Windows에서는 이 값이 숫자여야 합니다. <br><br>Windows 또는 Linux 머신을 사용하고 다른 형식을 사용하는 경우에는 값을 변환해야 합니다. 예를 들어 16진수 값을 사용한 경우 10진수 값으로 변환합니다.     |
| <a name="targetprocessname"></a>**TargetProcessName**              | 필수    | 문자열     |대상 프로세스의 이름입니다. 이 이름은 일반적으로 프로세스의 가상 주소 공간에 매핑되는 초기 코드와 데이터를 정의하는 데 사용되는 이미지 또는 실행 파일에서 파생됩니다.   <br><br>     예제: `C:\Windows\explorer.exe`     |
| **TargetProcessFileCompany**       | 선택     | 문자열     |대상 프로세스 이미지 파일을 만든 회사의 이름입니다.   <br><br>   예제: `Microsoft` |
| **TargetProcessFileDescription**   | 선택     | 문자열     | 대상 프로세스 이미지 파일에 있는 버전 정보의 설명입니다.   <br><br>예제: `Notepad++ : a free (GPL) source code editor` |
| **TargetProcessFileProduct**       | 선택     | 문자열     |대상 프로세스 이미지 파일에 있는 버전 정보의 제품 이름입니다.  <br><br>  예: `Notepad++`  |
| **TargetProcessFileSize**          | 선택     | 문자열     |    이벤트를 담당하는 프로세스를 실행한 파일의 크기입니다. |
| **TargetProcessFileVersion**       | 선택     | 문자열     |대상 프로세스 이미지 파일에 있는 버전 정보의 제품 버전입니다.   <br><br>  예: `7.9.5.0` |
| **TargetProcessFileInternalName**  |    선택          | 문자열  |   대상 프로세스의 이미지 파일에 대한 버전 정보의 제품 내부 파일 이름입니다. |
| **TargetProcessFileOriginalName** |       선택       | 문자열   |   대상 프로세스의 이미지 파일에 대한 버전 정보의 제품 원본 파일 이름입니다. |
| **TargetProcessIsHidden**          | 선택 사항     | 부울    |   대상 프로세스가 숨겨진 모드인지 나타냅니다.  |
| **TargetProcessInjectedAddress**   | 선택     | 문자열     |    담당 대상 프로세스가 저장되는 메모리 주소입니다.           |
| **TargetProcessMD5**               | 선택 사항     | MD5        | 대상 프로세스 이미지 파일의 MD5 해시입니다.   <br><br> 예제: `75a599802f1fa166cdadb360960b1dd0`|
| **TargetProcessSHA1**              | 선택 사항     | SHA1       | 대상 프로세스 이미지 파일의 SHA-1 해시입니다.       <br><br>  예제: `d55c5a4df19b46db8c54c801c4665d3338acdab0`   |
| **TargetProcessSHA256**            | 선택 사항     | SHA256     | 대상 프로세스 이미지 파일의 SHA-256 해시입니다.      <br><br>  예: <br> `e81bb824c4a09a811af17deae22f22dd`<br>`2e1ec8cbb00b22629d2899f7c68da274` |
| **TargetProcessSHA512**            | 선택 사항     | SHA512     |   대상 프로세스 이미지 파일의 SHA-512 해시입니다.       |
| **TargetProcessIMPHASH**           | 선택     | 문자열     |    대상 프로세스에서 사용되는 모든 라이브러리 DLL의 가져오기 해시입니다.    |
| <a name="targetprocesscommandline"></a> **TargetProcessCommandLine**       | 필수    | String     | 대상 프로세스를 실행하는 데 사용되는 명령줄입니다.   <br><br> 예제: `"choco.exe" -v`  |
| <a name="targetprocesscurrentdirectory"></a> **TargetProcessCurrentDirectory**       | 선택    | 문자열     | 대상 프로세스가 실행되는 현재 디렉터리입니다.  <br><br> 예제: `c:\windows\system32`  |
| **TargetProcessCreationTime**      | 필수    | DateTime   |    대상 프로세스 이미지 파일에 있는 버전 정보의 제품 버전입니다.   |
| **TargetProcessId**| 필수    | String     |  대상 프로세스의 PID(프로세스 ID)입니다.     <br><br>예: `48610176`<br><br>**참고**: 유형은 다양한 시스템을 지원하기 위해 *문자열* 로 정의되지만 Windows와 Linux에서는 이 값이 숫자여야 합니다. <br><br>Windows 또는 Linux 머신을 사용하고 다른 형식을 사용하는 경우에는 값을 변환해야 합니다. 예를 들어 16진수 값을 사용한 경우 10진수 값으로 변환합니다.         |
| **TargetProcessGuid**              | 선택    | 문자열     |대상 프로세스의 생성된 GUID(고유 식별자)입니다. 시스템 전체에서 프로세스를 식별할 수 있습니다.   <br><br>  예제: `EF3BD0BD-2B74-60C5-AF5C-010000001E00`  |
| **TargetProcessIntegrityLevel**    | 선택    | 문자열     |   모든 프로세스에는 토큰에 표시되는 무결성 수준이 있습니다. 무결성 수준은 보호 또는 액세스의 프로세스 수준을 결정합니다. <br><br> Windows는 **낮음**, **중간**, **높음** 및 **시스템** 과 같은 무결성 수준을 정의합니다. 표준 사용자는 **중간** 무결성 수준을 받고 고급 사용자는 **높음** 무결성 수준을 받습니다. <br><br> 자세한 내용은 [필수 무결성 제어 - Win32 앱](/windows/win32/secauthz/mandatory-integrity-control)을 참조하세요. |
| **TargetProcessTokenElevation**    | 선택    | 문자열     |생성되거나 종료된 프로세스에 적용된 UAC(사용자 액세스 제어) 권한 상승의 유무를 나타내는 토큰 유형입니다.   <br><br>    예제: `None`     |
| | | | |



## <a name="next-steps"></a>다음 단계

자세한 내용은 다음을 참조하세요.

- [Azure Sentinel의 정규화](normalization.md)
- [Azure Sentinel 인증 정규화 스키마 참조(퍼블릭 미리 보기)](authentication-normalization-schema.md)
- [Azure Sentinel DNS 정규화 스키마 참조](dns-normalization-schema.md)
- [Azure Sentinel 파일 이벤트 정규화 스키마 참조(퍼블릭 미리 보기)](file-event-normalization-schema.md)
- [Azure Sentinel 네트워크 정규화 스키마 참조](normalization-schema.md)
- [Azure Sentinel 레지스트리 이벤트 정규화 스키마 참조(퍼블릭 미리 보기)](registry-event-normalization-schema.md)

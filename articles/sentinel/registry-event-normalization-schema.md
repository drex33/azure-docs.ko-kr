---
title: Microsoft Sentinel Registry 이벤트 정규화 스키마 참조 | Microsoft Docs
description: 이 문서에서는 Microsoft Sentinel 레지스트리 이벤트 정규화 스키마에 대해 설명합니다.
services: sentinel
cloud: na
documentationcenter: na
author: batamig
manager: rkarlin
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 11/09/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: 4a22c8b2f207d8d6d2019f417bfcb025dcca9fa1
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132523926"
---
# <a name="microsoft-sentinel-registry-event-normalization-schema-reference-public-preview"></a>Microsoft Sentinel 레지스트리 이벤트 정규화 스키마 참조(공개 미리 보기)

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

레지스트리 이벤트 스키마는 Windows 레지스트리 엔터티를 생성, 수정, 삭제하는 Windows 활동을 설명하는 데 사용됩니다.

레지스트리 이벤트는 Windows 시스템에만 해당되지만, 엔드포인트 검색 및 응답(EDR) 시스템, Sysmon, Windows와 같이 Windows를 모니터링하는 여러 시스템에서 보고됩니다.

Microsoft Sentinel의 정규화에 대한 자세한 내용은 [정규화 및 ASIM(고급 SIEM 정보 모델)을](normalization.md)참조하세요.

> [!IMPORTANT]
> 레지스트리 이벤트 정규화 스키마는 현재 미리 보기로 제공됩니다. 해당 기능은 별도의 서비스 수준 계약 없이 제공되며, 프로덕션 작업에는 사용하지 않는 것이 좋습니다.
>
> [Azure Preview 추가 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타, 미리 보기 또는 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 법률 용어가 포함되어 있습니다.
>

## <a name="parsers"></a>파서

Microsoft Sentinel은 다음과 같은 기본 제공 제품별 레지스트리 이벤트 파서를 제공합니다.

- Log Analytics Agent 또는 Azure Monitor Agent를 사용하여 수집된 **보안 이벤트 레지스트리 업데이트(이벤트 4657**)
- Log Analytics Agent 또는 Azure Monitor Agent를 사용하여 수집된 **Sysmon 레지스트리 모니터링 이벤트(이벤트 12, 13, 14)**
- **엔드포인트 레지스트리 이벤트에 대한 Microsoft 365 Defender**

기본 제공 파서 모두를 통합하는 소스에 구애 받지 않는 파서를 사용하고 구성된 모든 원본에서 분석이 실행되도록 하려면 쿼리에서 테이블 이름으로 **imRegistry** 를 사용합니다.

[Microsoft Sentinel GitHub 리포지토리에서](https://aka.ms/AzSentinelRegistry) [소스와 관련이 없는 소스별 파서를](normalization-about-parsers.md) 배포합니다.

### <a name="add-your-own-normalized-parsers"></a>정규화된 고유한 파서 추가

레지스트리 이벤트 정보 모델에 대한 사용자 지정 파서 구현 시, `imRegistry<vendor><Product>` 구문을 사용하여 KQL 함수의 이름을 지정합니다.

레지스트리 이벤트 모델을 사용하는 모든 콘텐츠가 새 파서를 사용하도록 KQL 함수를 `imRegistry` 원본에 독립적인 파서에 추가합니다.

## <a name="normalized-content"></a>정규화된 콘텐츠

Microsoft Sentinel은 [IFEO 레지스트리 키 헌팅을 통해 유지](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/MultipleDataSources/PersistViaIFEORegistryKey.yaml) 쿼리를 제공합니다. 이 쿼리는 고급 SIEM 정보 모델을 사용하여 정규화된 모든 레지스트리 작업 데이터에서 작동합니다.

자세한 내용은 [Microsoft Sentinel을 사용하여 위협 헌션을 참조하세요.](hunting.md)

## <a name="schema-details"></a>스키마 세부 정보

레지스트리 이벤트 정보 모델은 [OSSEM 레지스트리 엔터티 스키마](https://github.com/OTRF/OSSEM/blob/master/docs/cdm/entities/registry.md)에 맞춰 정렬됩니다.

### <a name="log-analytics-fields"></a>Log Analytics 필드


다음 필드는 각 레코드에 대해 Log Analytics에서 생성되며, 사용자 지정 커넥터를 만들 때 재정의할 수 있습니다.

| 필드         | 형식     | 토론(Discussion)      |
| ------------- | -------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| <a name="timegenerated"></a>**TimeGenerated** | Datetime | 보고 디바이스에서 이벤트가 생성된 시간입니다.|
| **_ResourceId**   | guid     | 보고 디바이스 또는 서비스의 Azure 리소스 ID이거나 Syslog, CEF 또는 WEF를 사용하여 전달된 이벤트에 대한 로그 전달자 리소스 ID입니다. |
| **형식** | String | 레코드를 가져온 원본 테이블입니다. 이 필드는 여러 채널을 통해 다른 테이블로 동일한 이벤트를 수신할 수 있고 EventVendor 및 EventProduct 값이 동일한 경우에 유용합니다.<br><br>예를 들어 Sysmon 이벤트는 Event 테이블 또는 WindowsEvent 테이블에 수집할 수 있습니다. |


> [!NOTE]
> Log Analytics는 보안 사용 사례와 관련성이 낮은 다른 필드도 추가합니다. 자세한 내용은 [Azure Monitor 로그의 표준 열](../azure-monitor/logs/log-standard-columns.md)을 참조하세요.
>

### <a name="event-fields"></a>이벤트 필드


이벤트 필드는 모든 스키마에 공통되며, 작업 자체와 보고 디바이스를 설명합니다.

| 필드               | 클래스       | 형식       |  Description        |
|---------------------|-------------|------------|--------------------|
| **EventMessage**        | 선택    | 문자열     |     레코드에 포함되거나 레코드에서 생성된 일반 메시지 또는 설명입니다.   |
| **EventCount**          | 필수   | 정수    |     레코드에서 설명하는 이벤트 수입니다. <br><br>이 값은 원본에서 집계를 지원할 때 사용되며, 단일 레코드에서 여러 이벤트를 나타낼 수 있습니다. <br><br>다른 원본의 경우 `1`로 설정합니다.   |
| **EventStartTime**      | 필수   | 날짜/시간  |      원본에서 집계를 지원하고 레코드에서 여러 이벤트를 나타내는 경우 이 필드는 첫 번째 이벤트가 생성된 시간을 지정합니다. <br><br>그렇지 않은 경우 이 필드는 [TimeGenerated](#timegenerated) 필드의 별칭을 지정합니다. |
| **EventEndTime**        | 필수   | Alias      |      [TimeGenerated](#timegenerated) 필드에 대한 별칭입니다.    |
| **EventType**           | 필수   | Enumerated |    레코드에서 보고하는 작업을 설명합니다. <br><br>레지스트리 레코드의 경우, 지원되는 값은 다음과 같습니다. <br>- `RegistryKeyCreated` <br>- `RegistryKeyDeleted`<br>- `RegistryKeyRenamed` <br>- `RegistryValueDeleted` <br>- `RegistryValueSet`|
| **EventOriginalUid**    | 선택    | 문자열     |   원본에서 제공하는 경우 원래 레코드의 고유 ID입니다.<br><br>예: `69f37748-ddcd-4331-bf0f-b137f1ea83b`|
| **EventOriginalType**   | 선택    | 문자열     |   원본에서 제공하는 경우 원본 이벤트 유형 또는 ID입니다.<br><br>예: `4657`|
| <a name ="eventproduct"></a>**EventProduct**        | 필수   | String     |             이벤트를 생성하는 제품 <br><br>예: `Sysmon`<br><br>**참고**: 이 필드는 원본 레코드에서 사용할 수 없습니다. 이러한 경우 이 필드는 파서에서 설정해야 합니다.           |
| **EventProductVersion** | 선택    | 문자열     | 이벤트를 생성하는 제품의 버전 <br><br>예: `12.1`      |
| **EventVendor**         | 필수   | String     |           이벤트를 생성하는 제품의 공급 업체 <br><br>예: `Microsoft`  <br><br>**참고**: 이 필드는 원본 레코드에서 사용할 수 없습니다. 이러한 경우 이 필드는 파서에서 설정해야 합니다.  |
| **EventSchemaVersion**  | 필수   | String     |    스키마의 버전입니다. 여기에 설명된 스키마의 버전은 `0.1`입니다.         |
| **EventReportUrl**      | 선택    | 문자열     | 이벤트에 대한 추가 정보를 제공하는 리소스에 대해 이벤트에 제공된 URL입니다.|
| **Dvc** | 필수       | String     |               이벤트가 발생한 디바이스의 고유 식별자입니다. <br><br>이 필드는 [DvcId](#dvcid), [DvcHostname](#dvchostname) 또는 [DvcIpAddr](#dvcipaddr) 필드의 별칭을 지정할 수 있습니다. 명백한 디바이스가 없는 클라우드 원본의 경우 [EventProduct](#eventproduct) 필드와 동일한 값을 사용하세요.         |
| <a name ="dvcipaddr"></a>**DvcIpAddr**           | 권장 | IP 주소 |         레지스트리 이벤트가 발생한 디바이스의 IP 주소.  <br><br>예: `45.21.42.12`    |
| <a name ="dvchostname"></a>**DvcHostname**         | 권장 | 호스트 이름   |               레지스트리 이벤트가 발생한 디바이스의 호스트 이름. <br><br>예: `ContosoDc.Contoso.Azure`               |
| <a name ="dvcid"></a>**DvcId**               | 선택    | 문자열     |  레지스트리 이벤트가 발생한 디바이스의 고유 ID. <br><br>예: `41502da5-21b7-48ec-81c9-baeea8d7d669`   |
| **DvcMacAddr**          | 선택 사항    | MAC        |   레지스트리 이벤트가 발생한 디바이스의 MAC.  <br><br>예: `00:1B:44:11:3A:B7`       |
| **DvcOs**               | 선택    | 문자열     |         레지스트리 이벤트가 발생한 디바이스에서 실행 중인 운영 체제.    <br><br>예: `Windows`    |
| **DvcOsVersion**        | 선택    | 문자열     |   레지스트리 이벤트가 발생한 디바이스의 운영 체제 버전. <br><br>예: `10` |
| **AdditionalFields**    | 선택 사항    | 동적    | 원본에서 보존할 가치가 있는 추가 정보를 제공하는 경우 원본 필드 이름으로 유지하거나 동적 **AdditionalFields** 필드를 만들고 추가 정보를 키/값 쌍으로 추가합니다.    |


### <a name="registry-event-specific-fields"></a>레지스트리 이벤트 관련 필드

아래 표에 나열된 필드는 레지스트리 이벤트와 관련이 있지만, 다른 스키마의 필드와 비슷하며 비슷한 명명 규칙을 따릅니다.

자세한 내용은 Windows 설명서에서 [레지스트리 구조](/windows/win32/sysinfo/structure-of-the-registry)를 참조하세요.

| 필드          | 클래스        | 형식       | 설명   |
|---------------|--------------|------------|-----------------|
|<a name="registrykey"></a>**RegistryKey**     |     필수    |   String      |작업에 연결된 레지스트리 키로, 표준 루트 키 명명 규칙으로 정규화됩니다. 자세한 내용은 [루트 키](#root-keys)를 참조하세요.<br><br>레지스트리 키는 파일 시스템의 폴더와 비슷합니다. <br><br>예: `HKEY_LOCAL_MACHINE\SOFTWARE\MTG`        |
|**RegistryValue**     |    권장     |  String       |작업과 연결된 레지스트리 값. 레지스트리 값은 파일 시스템의 파일과 유사합니다. <br><br>예: `Path`        |
|<a name="registryvaluetype"></a>**RegistryValueType**     |    권장     |    String     | 표준 형식으로 정규화된 레지스트리 값의 형식. 자세한 내용은 [값 형식](#value-types)을 참조하세요.<br><br>예: `Reg_Expand_Sz`        |
|**RegistryValueData**     | 권장       |      String   |  레지스트리 값에 저장된 데이터. <br><br>예: `C:\Windows\system32;C:\Windows;`       |
|<a name="registrypreviouskey"></a>**RegistryPreviousKey**     |  권장       |   String      |  레지스트리를 수정하는 작업의 경우, 원본 레지스트리 키는 표준 루트 키 이름으로 정규화됩니다. 자세한 내용은 [루트 키](#root-keys)를 참조하세요. <br><br>**참고**: 작업에서 값과 같은 다른 필드를 변경했지만 키가 동일하게 유지되는 경우, [RegistryPreviousKey](#registrypreviouskey)는 [RegistryKey](#registrykey)와 같은 값을 갖게 됩니다.<br><br>예: `HKEY_LOCAL_MACHINE\SOFTWARE\MTG`       |
|<a name="registrypreviousvalue"></a>**RegistryPreviousValue**     | 권장        | String        | 레지스트리를 수정하는 작업의 경우, 원래 값 형식은 표준 형식으로 정규화됩니다. 자세한 내용은 [값 형식](#value-types)을 참조하세요. <br><br>형식이 변경되지 않은 경우, 이 필드의 값은 [RegistryValueType](#registryvaluetype) 필드의 값과 같습니다.  <br><br>예: `Path`       |
|**RegistryPreviousValueType**     | 권장        |   String      |레지스트리를 수정하는 작업의 경우, 원래 값 형식. <br><br>형식이 변경되지 않은 경우, 이 필드의 값은 [RegistryValueType](#registryvaluetype) 필드와 같으며 표준 형식으로 정규화됩니다. 자세한 내용은 [값 형식](#value-types)을 참조하세요.<br><br>예: `Reg_Expand_Sz`         |
|**RegistryPreviousValueData**     | 권장        |   String      |레지스트리를 수정하는 작업에 대한 원래 레지스트리 데이터. <br><br>예제: `C:\Windows\system32;C:\Windows;`         |
|**사용자** | Alias | |[ActorUsername](#actorusername) 필드에 대한 별칭입니다. <br><br>예: `CONTOSO\ dadmin` |
|**처리**     |  Alias       |         |  [ActingProcessName](#actingprocessname) 필드의 별칭.<br><br>예: `C:\Windows\System32\rundll32.exe`       |
| <a name="actorusername"></a>**ActorUsername**  | 필수    | String     | 이벤트를 시작한 사용자의 사용자 이름입니다. <br><br>예: `CONTOSO\WIN-GG82ULGC9GO$`     |
| **ActorUsernameType**              | 필수    | Enumerated |   [ActorUsername](#actorusername) 필드에 저장된 사용자 이름의 형식을 지정합니다. 자세한 내용은 [사용자 엔터티](normalization-about-schemas.md#the-user-entity)를 참조하세요. <br><br>예: `Windows`       |
| <a name="actoruserid"></a>**ActorUserId**    | 권장  | String     |   작업자의 고유 ID입니다. 특정 ID는 이벤트를 생성하는 시스템에 따라 달라집니다. 자세한 내용은 [사용자 엔터티](normalization-about-schemas.md#the-user-entity)를 참조하세요.  <br><br>예: `S-1-5-18`    |
| **ActorUserIdType**| 권장  | String     |  [ActorUserId](#actoruserid) 필드에 저장된 ID의 형식입니다. 자세한 내용은 [사용자 엔터티](normalization-about-schemas.md#the-user-entity)를 참조하세요. <br><br>예: `SID`         |
| **ActorSessionId** | 선택     | 문자열     |   작업자 로그인 세션의 고유 ID입니다.  <br><br>예: `999`<br><br>**참고**: 유형은 다양한 시스템을 지원하기 위해 *문자열* 로 정의되지만 Windows에서는 이 값이 숫자여야 합니다. Windows 머신을 사용하고 원본이 다른 형식을 보내는 경우, 값을 변환해야 합니다. 예를 들어, 원본이 16진수 값을 보내는 경우, 10진수 값으로 변환합니다.   |
| <a name="actingprocessname"></a>**ActingProcessName**              | 선택     | 문자열     |   작업 프로세스 이미지 파일의 파일 이름. 이 이름은 일반적으로 프로세스 이름으로 간주됩니다.  <br><br>예: `C:\Windows\explorer.exe`  |
| **ActingProcessId**| 필수    | String        | 작업 프로세스 PID(프로세스 ID)입니다.<br><br>예제: `48610176`           <br><br>**참고**: 형식은 다양한 시스템을 지원하도록 *문자열* 로 정의되지만, Windows와 Linux에서 이 값은 숫자여야 합니다. <br><br>Windows 또는 Linux 컴퓨터를 사용하고 다른 형식을 사용한 경우 값을 변환해야 합니다. 예를 들어 16진수 값을 사용한 경우 10진수 값으로 변환합니다.    |
| **ActingProcessGuid**              | 선택     | 문자열     |  작업 프로세스의 생성된 GUID(고유 식별자).   <br><br> 예: `EF3BD0BD-2B74-60C5-AF5C-010000001E00`            |
| **ParentProcessName**              | 선택     | 문자열     |  부모 프로세스 이미지 파일의 파일 이름. 이 값은 일반적으로 프로세스 이름으로 간주됩니다.    <br><br>예: `C:\Windows\explorer.exe` |
| **ParentProcessId**| 필수    | String    | 부모 프로세스의 PID(프로세스 ID)입니다.   <br><br>     예제: `48610176`    |
| **ParentProcessGuid**              | 선택     | 문자열     |  부모 프로세스의 생성된 GUID(고유 식별자).     <br><br> 예: `EF3BD0BD-2B74-60C5-AF5C-010000001E00` |



### <a name="root-keys"></a>루트 키

다른 원본은 다른 표현을 사용하는 레지스트리 키 접두사를 나타냅니다. [RegistryKey](#registrykey) 및 [RegistryPreviousKey](#registrypreviouskey) 필드의 경우, 다음과 같은 정규화된 접두사를 사용합니다.

|정규화된 키 접두사  |기타 일반적인 표현  |
|---------|---------|
|**HKEY_LOCAL_MACHINE**     | `HKLM`, `\REGISTRY\MACHINE`        |
|**HKEY_USERS**     | `HKU`, `\REGISTRY\USER`        |


### <a name="value-types"></a>값 형식

다른 원본은 다른 표현을 사용하는 레지스트리 값 유형을 나타냅니다. [RegistryValueType](#registryvaluetype) 및 [RegistryPreviousValueType](#registrypreviousvalue) 필드의 경우, 다음과 같은 정규화된 유형을 사용합니다.


|정규화된 키 접두사  |기타 일반적인 표현  |
|---------|---------|
|  **Reg_None**   |    `None`, `%%1872`     |
|  **Reg_Sz**   |     `String`, `%%1873`    |
| **Reg_Expand_Sz**    | `ExpandString`, `%%1874`        |
|  **Reg_Binary**   |   `Binary`, `%%1875`      |
| **Reg_DWord**    |    `Dword`, `%%1876`     |
|  **Reg_Multi_Sz**   |  `MultiString`, `%%1879`       |
|  **Reg_QWord**   |    `Qword`, `%%1883`     |



## <a name="next-steps"></a>다음 단계

자세한 내용은 다음을 참조하세요.

- [Microsoft Sentinel의 정규화](normalization.md)
- [Microsoft Sentinel 인증 정규화 스키마 참조(공개 미리 보기)](authentication-normalization-schema.md)
- [Microsoft Sentinel DNS 정규화 스키마 참조](dns-normalization-schema.md)
- [Microsoft Sentinel 파일 이벤트 정규화 스키마 참조(공개 미리 보기)](file-event-normalization-schema.md)
- [Microsoft Sentinel 네트워크 정규화 스키마 참조](./network-normalization-schema.md)

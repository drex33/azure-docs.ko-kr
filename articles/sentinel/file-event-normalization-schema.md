---
title: Microsoft Sentinel 파일 이벤트 정규화 스키마 참조 | Microsoft Docs
description: 이 문서에서는 Microsoft Sentinel 파일 이벤트 정규화 스키마에 대해 설명합니다.
author: batamig
ms.topic: reference
ms.date: 11/09/2021
ms.author: bagol
ms.custom: ignite-fall-2021
ms.openlocfilehash: f90f160047db669b2fc64c2541da9424f93b5000
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132754427"
---
# <a name="microsoft-sentinel-file-event-normalization-schema-reference-public-preview"></a>Microsoft Sentinel 파일 이벤트 정규화 스키마 참조(공개 미리 보기)

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

파일 이벤트 표준화 스키마는 파일 또는 문서 만들기, 수정 또는 삭제와 같은 파일 작업을 설명하는 데 사용됩니다. 이러한 이벤트는 운영 체제, 파일 스토리지 시스템(예: Azure Files) 및 문서 관리 시스템(예: Microsoft SharePoint)에서 보고됩니다.

Microsoft Sentinel의 정규화에 대한 자세한 내용은 [정규화 및 ASIM(고급 SIEM 정보 모델)을](normalization.md)참조하세요.

> [!IMPORTANT]
> 파일 이벤트 정규화 스키마는 현재 미리 보기에 있습니다. 해당 기능은 별도의 서비스 수준 계약 없이 제공되며, 프로덕션 작업에는 사용하지 않는 것이 좋습니다.
>
> [Azure Preview 추가 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타, 미리 보기 또는 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 법률 용어가 포함되어 있습니다.
>

## <a name="parsers"></a>파서

Microsoft Sentinel은 다음과 같은 기본 제공 제품별 파일 이벤트 파서를 제공합니다.

- Log Analytics 에이전트 또는 Azure Monitor 에이전트를 사용하여 수집된 **Sysmon 파일 작업 이벤트**(이벤트 11, 23 및 26)
- Office 작업 커넥터를 사용하여 수집된 **Microsoft Office 365 SharePoint 및 OneDrive 이벤트**
- **엔드포인트 파일 이벤트에 대한 Microsoft 365 Defender**
- **Azure Storage**(Blob, File, Queue 및 Table Storage 포함)

모든 기본 제공 파서를 통합하는 원본 중립적 파서를 사용하고 구성된 모든 원본에서 분석이 실행되도록 하려면 쿼리에서 imFileEvent를 테이블 이름으로 사용합니다.

[Microsoft Sentinel GitHub 리포지토리에서](https://aka.ms/AzSentinelFileEvent) [소스와 관련이 없는 소스별 파서를](normalization-about-parsers.md) 배포합니다.

## <a name="add-your-own-normalized-parsers"></a>사용자 고유의 정규화된 파서 추가


파일 이벤트 정보 모델에 대한 사용자 지정 파서를 구현하는 경우 `imFileEvent<vendor><Product` 구문을 사용하여 KQL 함수의 이름을 지정합니다.

파일 이벤트 모델을 사용하는 콘텐츠에서 새 파서를 사용할 수 있도록 KQL 함수를 `imFileEvent` 원본 중립 파서에 추가합니다.

## <a name="normalized-content-for-file-activity-data"></a>파일 작업 데이터에 대한 정규화된 콘텐츠

파일 활동 ASIM 스키마에 대한 지원은 정규화된 파일 작업 파서를 사용하여 다음과 같은 기본 제공 분석 규칙에 대한 지원도 포함합니다. Microsoft Sentinel GitHub 리포지토리에 대한 링크는 아래 참조로 제공되지만 [Microsoft Sentinel Analytics 규칙 갤러리에서](detect-threats-built-in.md)이러한 규칙을 찾을 수도 있습니다. 연결된 GitHub 페이지를 사용하여 나열된 규칙에 대한 관련 헌팅 쿼리를 복사합니다.


- [SUNBURST 및 SUPERNOVA 백도어 해시(정규화된 파일 이벤트)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimFileEvent/imFileESolarWindsSunburstSupernova.yaml)
- [Exchange Server 취약성 공개 2021년 3월 IoC 매치](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ExchangeServerVulnerabilitiesMarch2021IoCs.yaml)
- [의심스러운 파일을 작성하는 HAFNIUM UM 서비스](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/HAFNIUMUmServiceSuspiciousFile.yaml)
- [NOBELIUM - 도메인, 해시 및 IP IOC - 2021년 5월](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/NOBELIUM_IOCsMay2021.yaml)
- [SUNSPOT 로그 파일 창조](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/SUNSPOTLogFile.yaml)
- [알려진 ZINC Comebacker 및 Klackring 맬웨어 해시](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ZincJan272021IOCs.yaml)

자세한 내용은 [위협을 탐지하는 사용자 지정 분석 규칙 만들기](detect-threats-custom.md)를 참조하세요.


## <a name="schema-details"></a>스키마 세부 정보

파일 이벤트 정보 모델은 [OSSEM 프로세스 엔터티 스키마](https://github.com/OTRF/OSSEM/blob/master/docs/cdm/entities/file.md)에 맞춰 조정됩니다.

### <a name="log-analytics-fields"></a>Log Analytics 필드

다음 필드는 각 레코드에 대해 Log Analytics에서 생성되며, 사용자 지정 커넥터를 만들 때 재정의할 수 있습니다.

| 필드         | 형식     | 토론(Discussion)      |
| ------------- | -------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| <a name="timegenerated"></a>**TimeGenerated** | Datetime | 보고 디바이스에서 이벤트가 생성된 시간입니다.|
| **_ResourceId**   | guid     | 보고 디바이스 또는 서비스의 Azure 리소스 ID이거나 Syslog, CEF 또는 WEF를 사용하여 전달된 이벤트에 대한 로그 전달자 리소스 ID입니다. |
| **형식** | String | 레코드를 가져온 원본 테이블입니다. 이 필드는 여러 채널을 통해 다른 테이블로 동일한 이벤트를 수신할 수 있고 EventVendor 및 EventProduct 값이 동일한 경우에 유용합니다.<br><br>예를 들어 Sysmon 이벤트는 Event 테이블 또는 WindowsEvent 테이블에 수집할 수 있습니다. |
| | | |

> [!NOTE]
> Log Analytics는 보안 사용 사례와 관련성이 낮은 다른 필드도 추가합니다. 자세한 내용은 [Azure Monitor 로그의 표준 열](../azure-monitor/logs/log-standard-columns.md)을 참조하세요.
>

## <a name="event-fields"></a>이벤트 필드

이벤트 필드는 모든 스키마에 공통되며, 작업 자체와 보고 디바이스를 설명합니다.

| 필드               | 클래스       | 형식       |  Description       |
|---------------------|-------------|------------|--------------------|
| **EventMessage**        | 선택    | 문자열     |     레코드에 포함되거나 레코드에서 생성된 일반 메시지 또는 설명입니다.   |
| **EventCount**          | 필수   | 정수    |     레코드에서 설명하는 이벤트 수입니다. <br><br>이 값은 원본에서 집계를 지원할 때 사용되며, 단일 레코드에서 여러 이벤트를 나타낼 수 있습니다. <br><br>다른 원본의 경우 `1`로 설정합니다.   |
| **EventStartTime**      | 필수   | 날짜/시간  |      원본에서 집계를 지원하고 레코드에서 여러 이벤트를 나타내는 경우 이 필드는 첫 번째 이벤트가 생성된 시간을 지정합니다. 그렇지 않은 경우 이 필드는 [TimeGenerated](#timegenerated) 필드의 별칭을 지정합니다. |
| **EventEndTime**        | 필수   | Alias      |      [TimeGenerated](#timegenerated) 필드에 대한 별칭입니다.    |
| **EventType**           | 필수   | Enumerated |    레코드에서 보고하는 작업을 설명합니다. <br><br>파일 레코드의 경우 지원되는 값은 다음과 같습니다. <br><br>- `FileCreated`<br>- `FileModified`<br>- `FileDeleted`<br>- `FileRenamed`<br>- `FileCopied`<br>- `FileMoved`<br>- `FolderCreated`<br>- `FolderDeleted` |
| **EventResult**         | 필수   | Enumerated |  지원되는 다음 값 중 하나로 정규화된 이벤트의 결과를 설명합니다. <br><br>- `Success`<br>- `Partial`<br>- `Failure`<br>- `NA`(해당 사항 없음) <br><br>원본에서 **EventResult** 값을 얻기 위해 분석해야 하는 [EventOriginalResultDetails](#eventoriginalresultdetails) 필드에 대한 값만 제공할 수 있습니다. |
| <a name="eventoriginalresultdetails"></a>**EventOriginalResultDetails**  | 선택    | 문자열     | 이벤트의 결과를 설명합니다. <br><br>**참고**: 이 값은 정규화되지 않으며 데이터 원본에서 제공하는 원래 값을 위한 것입니다. 현재 파일 이벤트 정규화 스키마에 대해 *EventResultDetails* 와 같은 정규화된 관련 필드가 없습니다. |
| **EventOriginalUid**    | 선택    | 문자열     | 원본에서 제공하는 경우 원래 레코드의 고유 ID입니다.<br><br>예: `69f37748-ddcd-4331-bf0f-b137f1ea83b`|
| **EventOriginalType**   | 선택    | 문자열     | 원본에서 제공하는 경우 원본 이벤트 유형 또는 ID입니다.<br><br>예: `4663`|
| <a name ="eventproduct"></a>**EventProduct**       | 필수   | String     | 이벤트를 생성하는 제품 <br><br>예: `Sysmon`<br><br>**참고**: 이 필드는 원본 레코드에서 사용할 수 없습니다. 이러한 경우 이 필드는 파서에서 설정해야 합니다.           |
| **EventProductVersion** | 선택    | 문자열     | 이벤트를 생성하는 제품의 버전 <br><br>예: `12.1`<br><br>**참고**: 이 필드는 원본 레코드에서 사용할 수 없습니다. 이러한 경우 이 필드는 파서에서 설정해야 합니다.           |
| **EventVendor**         | 필수   | String     | 이벤트를 생성하는 제품의 공급 업체 <br><br>예: `Microsoft`  <br><br>**참고**: 이 필드는 원본 레코드에서 사용할 수 없습니다. 이러한 경우 이 필드는 파서에서 설정해야 합니다.  |
| **EventSchemaVersion**  | 필수   | String     | 스키마의 버전입니다. 여기에 설명된 스키마의 버전은 `0.1`입니다.         |
| **EventReportUrl**      | 선택    | 문자열     | 이벤트에 대한 추가 정보를 제공하는 리소스에 대해 이벤트에 제공된 URL입니다.|
| **Dvc**                 | Alias       | String     | 이벤트가 발생한 디바이스의 고유 식별자입니다. <br><br>예: `ContosoDc.Contoso.Azure`<br><br>이 필드는 [DvcId](#dvcid), [DvcHostname](#dvchostname) 또는 [DvcIpAddr](#dvcipaddr) 필드의 별칭을 지정할 수 있습니다. 명백한 디바이스가 없는 클라우드 원본의 경우 [EventProduct](#eventproduct) 필드와 동일한 값을 사용하세요.            |
| <a name ="dvcipaddr"></a>**DvcIpAddr**   | 권장 | IP 주소 | 파일 이벤트가 발생한 디바이스의 IP 주소입니다.  <br><br>예: `45.21.42.12`    |
| <a name ="dvchostname"></a>**DvcHostname**    | 권장 | 호스트 이름   | 파일 이벤트가 발생한 디바이스의 호스트 이름입니다. <br><br>예: `ContosoDc.Contoso.Azure` |
| <a name ="dvcid"></a>**DvcId**  | 선택    | 문자열     |  파일 이벤트가 발생한 디바이스의 고유 ID입니다. <br><br>예: `41502da5-21b7-48ec-81c9-baeea8d7d669`   |
| **DvcMacAddr**          | 선택 사항    | MAC        |   파일 이벤트가 발생한 디바이스의 MAC입니다.  <br><br>예: `00:1B:44:11:3A:B7`       |
| **DvcOs**               | 선택    | 문자열     |         파일 이벤트가 발생한 디바이스에서 실행되는 운영 체제입니다.    <br><br>예: `Windows`    |
| **DvcOsVersion**        | 선택    | 문자열     |   파일 이벤트가 발생한 디바이스의 운영 체제 버전입니다. <br><br>예: `10` |
| **AdditionalFields**    | 선택 사항    | 동적    | 원본에서 보존할 가치가 있는 추가 정보를 제공하는 경우 원본 필드 이름으로 유지하거나 동적 **AdditionalFields** 필드를 만들고 추가 정보를 키/값 쌍으로 추가합니다.    |
| | | | |



### <a name="file-event-specific-fields"></a>파일 이벤트 관련 필드

아래 표에 나열된 필드는 파일 이벤트와 관련이 있지만, 다른 스키마의 필드와 비슷하며 비슷한 명명 규칙을 따릅니다.

파일 이벤트 스키마는 파일 작업의 핵심인 다음 엔터티를 참조합니다.

- **Actor**. 파일 작업을 시작한 사용자
- **ActingProcess**. Actor에서 파일 작업을 시작하는 데 사용하는 프로세스
- **TargetFile**. 작업이 수행된 파일
- **원본 파일(SrcFile)** . 작업을 수행하기 전에 파일 정보를 저장합니다.

이러한 엔터티 간의 관계는 다음과 같이 명확하게 설명됩니다. **Actor** 는 **원본 파일** 을 **대상 파일** 로 수정하는 **작업 프로세스** 를 사용하여 파일 작업을 수행합니다. 

예를 들어 `JohnDoe`(**작업자**)는 `Windows File Explorer`(**작업 프로세스**)를 사용하여 `new.doc`(**원본 파일**)의 이름을 `old.doc`(**대상 파일**)로 바꿉니다.

| 필드          | 클래스        | 형식       | Description   |
|---------------|--------------|------------|-----------------|
| **ActingProcessCommandLine** |선택  |문자열  | 작업 프로세스를 실행하는 데 사용되는 명령줄입니다. <br><br>예: `"choco.exe" -v` |
|**ActingProcessGuid** |선택     | 문자열     |  작업 프로세스의 생성된 GUID(고유 식별자)입니다. 시스템 전체에서 프로세스를 식별할 수 있습니다.  <br><br> 예: `EF3BD0BD-2B74-60C5-AF5C-010000001E00`            |
| **ActingProcessId**| 필수    | String        | 작업 프로세스 PID(프로세스 ID)입니다.<br><br>예제: `48610176`           <br><br>**참고**: 형식은 다양한 시스템을 지원하도록 *문자열* 로 정의되지만, Windows와 Linux에서 이 값은 숫자여야 합니다. <br><br>Windows 또는 Linux 컴퓨터를 사용하고 다른 형식을 사용한 경우 값을 변환해야 합니다. 예를 들어 16진수 값을 사용한 경우 10진수 값으로 변환합니다.    |
| <a name="actingprocessname"></a>**ActingProcessName**              | 선택     | 문자열     |   작업 프로세스의 이름입니다. 이 이름은 일반적으로 프로세스의 가상 주소 공간에 매핑되는 초기 코드와 데이터를 정의하는 데 사용되는 이미지 또는 실행 파일에서 파생됩니다.<br><br>예: `C:\Windows\explorer.exe`  |
|**처리**| Alias| | [ActingProcessName](#actingprocessname) 필드에 대한 별칭입니다|
| <a name="actoruserid"></a>**ActorUserId**    | 권장  | String     |   **Actor** 의 고유 ID입니다. 특정 ID는 이벤트를 생성하는 시스템에 따라 달라집니다. 자세한 내용은 [사용자 엔터티](normalization-about-schemas.md#the-user-entity)를 참조하세요.  <br><br>예: `S-1-5-18`    |
| **ActorUserIdType**| 권장  | String     |  [ActorUserId](#actoruserid) 필드에 저장된 ID의 형식입니다. 자세한 내용은 [사용자 엔터티](normalization-about-schemas.md#the-user-entity)를 참조하세요. <br><br>예: `SID`         |
| <a name="actorusername"></a>**ActorUsername**  | 필수    | String     | 이벤트를 시작한 사용자의 사용자 이름입니다. <br><br>예: `CONTOSO\WIN-GG82ULGC9GO$`     |
| **ActorUsernameType**              | 필수    | Enumerated |   [ActorUsername](#actorusername) 필드에 저장된 사용자 이름의 형식을 지정합니다. 자세한 내용은 [사용자 엔터티](normalization-about-schemas.md#the-user-entity)를 참조하세요. <br><br>예: `Windows`       |
|**사용자** | Alias| | [ActorUsername](#actorusername) 필드에 대한 별칭입니다. <br><br>예: `CONTOSO\dadmin`|
| **ActorUserType**|선택 사항 | Enumerated| **Actor** 의 형식입니다. 지원되는 값은 다음과 같습니다. <br><br>- `Regular`<br>- `Machine`<br>- `Admin`<br>- `System`<br>- `Application`<br>- `Service Principal`<br>- `Other` <br><br>**참고**: 원본에서 **ActorUserType** 값을 얻기 위해 분석해야 하는 [ActorOriginalUserType](#actororiginalusertype) 필드에 대한 값만 제공할 수 있습니다.|
|<a name="actororiginalusertype"></a>**ActorOriginalUserType** |선택 |문자열 | 보고 디바이스에서 제공하는 **Actor** 사용자 유형입니다. |
|**HttpUserAgent** |선택 | 문자열 |HTTP 또는 HTTPS를 사용하는 원격 시스템에서 작업을 시작하는 경우 사용되는 사용자 에이전트입니다.<br><br>예를 들면 다음과 같습니다.<br>`Mozilla/5.0 (Windows NT 10.0; Win64; x64)`<br>`AppleWebKit/537.36 (KHTML, like Gecko)`<br>` Chrome/42.0.2311.135`<br>`Safari/537.36 Edge/12.246`|
| **NetworkApplicationProtocol**| 선택|문자열 | 원격 시스템에서 작업을 시작하는 경우 이 값은 OSI 모델에서 사용되는 애플리케이션 계층 프로토콜입니다. <br><br>이 필드는 열거되지 않고 모든 값이 허용되지만, 권장되는 값은 `HTTP`, `HTTPS`, `SMB`,`FTP` 및 `SSH`입니다.<br><br>예: `SMB`|
|**SrcIpAddr** |권장 |IP 주소 | 원격 시스템에서 작업을 시작하는 경우 이 시스템의 IP 주소입니다.<br><br>예: `185.175.35.214`|
| **SrcFileCreationTime**|선택 사항 |날짜/시간 |원본 파일을 만든 시간입니다. |
|**SrcFileDirectory** | 선택| 문자열| 원본 파일 폴더 또는 위치입니다. 이 필드는 최종 요소가 없는 [SrcFilePath](#srcfilepath) 필드와 비슷합니다. <br><br>**참고**: 로그 원본에서 값을 사용할 수 있고 전체 경로에서 추출할 필요가 없는 경우 파서에서 이 값을 제공할 수 있습니다.|
| **SrcFileExtension**|선택 | 문자열|원본 파일 확장명입니다. <br><br>**참고**: 로그 원본에서 값을 사용할 수 있고 전체 경로에서 추출할 필요가 없는 경우 파서에서 이 값을 제공할 수 있습니다.|
|**SrcFileMimeType** |선택 사항 |Enumerated |    원본 파일의 Mime 또는 미디어 형식입니다. 지원되는 값은 [IANA 미디어 형식](https://www.iana.org/assignments/media-types/media-types.xhtml) 리포지토리에 나열되어 있습니다. |
|**SrcFileName** |선택 |문자열 | 원본 파일의 이름입니다. 경로 또는 위치를 포함하지 않지만, 해당하는 경우 확장명을 포함합니다. 이 필드는 [SrcFilePath](#srcfilepath) 필드의 마지막 요소와 비슷합니다. <br><br>**참고**: 로그 원본에서 값을 사용할 수 있고 전체 경로에서 추출할 필요가 없는 경우 파서에서 이 값을 제공할 수 있습니다.|
| <a name="srcfilepath"></a>**SrcFilePath**| 필수|String |폴더 또는 위치, 파일 이름 및 확장명을 포함한 원본 파일의 정규화된 전체 경로입니다. <br><br>자세한 내용은 [경로 구조](#path-structure)를 참조하세요.<br><br>예: `/etc/init.d/networking` |
|**SrcFilePathType** |필수 | Enumerated| [SrcFilePath](#srcfilepath)의 형식입니다. 자세한 내용은 [경로 구조](#path-structure)를 참조하세요.|
|**SrcFileMD5**|선택 사항 |MD5 | 원본 파일의 MD5 해시입니다. <br><br>예: `75a599802f1fa166cdadb360960b1dd0` |
|**SrcFileSHA1**|선택 사항 |SHA1 |원본 파일의 SHA-1 해시입니다.<br><br>예:<br>`d55c5a4df19b46db8c54`<br>`c801c4665d3338acdab0` |
|**SrcFileSHA256** | 선택 사항|SHA256 |원본 파일의 SHA-256 해시입니다. <br><br>예:<br> `e81bb824c4a09a811af17deae22f22dd`<br>`2e1ec8cbb00b22629d2899f7c68da274`|
|**SrcFileSHA512** |선택 사항 | SHA512|원본 파일의 SHA-512 해시입니다. |
|**SrcFileSize**| 선택|정수 | 원본 파일의 크기(바이트)입니다.|
|**TargetFileCreationTime** | 선택 사항|날짜/시간 |대상 파일을 만든 시간입니다. |
|**TargetFileDirectory** | 선택|문자열 |대상 파일 폴더 또는 위치입니다. 이 필드는 최종 요소가 없는 [TargetFilePath](#targetfilepath) 필드와 비슷합니다. <br><br>**참고**: 로그 원본에서 값을 사용할 수 있고 전체 경로에서 추출할 필요가 없는 경우 파서에서 이 값을 제공할 수 있습니다.|
|**TargetFileExtension** |선택 |문자열 | 대상 파일 확장명입니다.<br><br>**참고**: 로그 원본에서 값을 사용할 수 있고 전체 경로에서 추출할 필요가 없는 경우 파서에서 이 값을 제공할 수 있습니다.|
| **TargetFileMimeType**|선택 사항 | Enumerated| 대상 파일의 Mime 또는 미디어 형식입니다. 허용되는 값은 [IANA 미디어 형식](https://www.iana.org/assignments/media-types/media-types.xhtml) 리포지토리에 나열되어 있습니다.|
| **TargetFileName**|선택 |문자열 |대상 파일의 이름입니다. 경로 또는 위치를 포함하지 않지만, 해당하는 경우 확장명을 포함합니다. 이 필드는 [TargetFilePath](#targetfilepath) 필드의 최종 요소와 비슷합니다.<br><br>**참고**: 로그 원본에서 값을 사용할 수 있고 전체 경로에서 추출할 필요가 없는 경우 파서에서 이 값을 제공할 수 있습니다.|
|<a name="targetfilepath"></a>**TargetFilePath** | 필수| String| 폴더 또는 위치, 파일 이름 및 확장명을 포함한 대상 파일의 정규화된 전체 경로입니다. 자세한 내용은 [경로 구조](#path-structure)를 참조하세요. <br><br>**참고**: 레코드에 폴더 또는 위치 정보가 포함되지 않은 경우 파일 이름만 여기에 저장합니다. <br><br>예: `C:\Windows\System32\notepad.exe`|
|**TargetFilePathType** | 필수|Enumerated | [TargetFilePath](#targetfilepath)의 형식입니다. 자세한 내용은 [경로 구조](#path-structure)를 참조하세요.    |
|**FilePath** |Alias | | [TargetFilePath](#targetfilepath) 필드에 대한 별칭입니다.|
| **TargetFileMD5**| 선택 사항| MD5|대상 파일의 MD5 해시입니다. <br><br>예: `75a599802f1fa166cdadb360960b1dd0` |
|**TargetFileSHA1** |선택 사항 |SHA1 |대상 파일의 SHA-1 해시입니다. <br><br>예:<br> `d55c5a4df19b46db8c54`<br>`c801c4665d3338acdab0`|
|**TargetFileSHA256** | 선택 사항|SHA256 |대상 파일의 SHA-256 해시입니다. <br><br>예:<br> `e81bb824c4a09a811af17deae22f22dd`<br>`2e1ec8cbb00b22629d2899f7c68da274`  |
| **TargetFileSHA512**| 선택 사항| SHA512|원본 파일의 SHA-512 해시입니다. |
|**해시**|Alias | |사용 가능한 최상의 대상 파일 해시에 대한 별칭입니다. |
|**TargetFileSize** |선택 | 정수|대상 파일의 크기(바이트)입니다. |
| **TargetUrl**|선택 | 문자열|HTTP 또는 HTTPS를 사용하여 작업을 시작하는 경우 사용되는 URL입니다. <br><br>예: `https://onedrive.live.com/?authkey=...` |
| | | | |



## <a name="path-structure"></a>경로 구조

경로는 다음 형식 중 하나와 일치하도록 정규화해야 합니다. 값이 정규화되는 형식은 해당 **FilePathType** 필드에 반영됩니다.

|Type  |예제  |참고  |
|---------|---------|---------|
|**Windows 로컬**     |   `C:\Windows\System32\notepad.exe`      |      Windows 경로 이름은 대/소문자를 구분하지 않으므로 이 형식은 값에서 대/소문자를 구분하지 않음을 의미합니다.   |
|**Windows 공유**     |      `\\Documents\My Shapes\Favorites.vssx`   | Windows 경로 이름은 대/소문자를 구분하지 않으므로 이 형식은 값에서 대/소문자를 구분하지 않음을 의미합니다.        |
|**Unix**     |  `/etc/init.d/networking`       |     Unix 경로 이름은 대/소문자를 구분하므로 이 형식은 값에서 대/소문자를 구분함을 의미합니다.  <br><br>- 이 형식을 AWS S3에 사용합니다. 버킷과 키 이름을 연결하여 경로를 만듭니다. <br><br>- 이 형식을 Azure Blob 스토리지 개체 키에 사용합니다.   |
|**URL**     |  `https://1drv.ms/p/s!Av04S_*********we`       | 파일 경로를 URL로 사용할 수 있는 경우에 사용합니다. URL은 *http* 또는 *https* 로 제한되지 않으며 FTP 값을 포함한 모든 값이 유효합니다. |
|     |         |         |



## <a name="next-steps"></a>다음 단계

자세한 내용은 다음을 참조하세요.

- [Microsoft Sentinel의 정규화](normalization.md)
- [Microsoft Sentinel 인증 정규화 스키마 참조(공개 미리 보기)](authentication-normalization-schema.md)
- [Microsoft Sentinel DNS 정규화 스키마 참조](dns-normalization-schema.md)
- [Microsoft Sentinel 네트워크 정규화 스키마 참조](./network-normalization-schema.md)
- [Microsoft Sentinel 프로세스 이벤트 정규화 스키마 참조(공개 미리 보기)](process-events-normalization-schema.md)
- [Microsoft Sentinel 레지스트리 이벤트 정규화 스키마 참조(공개 미리 보기)](registry-event-normalization-schema.md)

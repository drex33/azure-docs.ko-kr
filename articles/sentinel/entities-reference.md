---
title: Microsoft 센티널 엔터티 형식 참조 | Microsoft Docs
description: 이 문서에서는 Microsoft 센티널 엔터티 형식 및 필수 식별자를 표시 합니다.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: c5917011bd937e88811c9f0cffaed7f8c1c61f00
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132521209"
---
# <a name="microsoft-sentinel-entity-types-reference"></a>Microsoft 센티널 엔터티 형식 참조

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

## <a name="entity-types-and-identifiers"></a>엔터티 형식 및 식별자

다음 표에서는 현재 Microsoft 센티널에서 매핑에 사용할 수 있는 **엔터티 형식** 및 각 엔터티 형식에 대 한 **식별자** 로 사용할 수 있는 특성을 보여 줍니다 .이 **특성** 은 [분석 규칙 마법사](detect-threats-custom.md)의 [엔터티 매핑](map-data-fields-to-entities.md) 섹션에 있는 **식별자** 드롭다운 목록에 표시 됩니다.

**필수 식별자** 열의 각 식별자는 최소한 엔터티를 식별하는 데 필요합니다. 그러나 필수 식별자만으로는 *고유* ID를 제공하는 데 충분하지 않을 수 있습니다. 식별자가 많이 사용될수록 고유 ID의 가능성이 높아집니다. 최대 3개의 식별자를 단일 엔터티 매핑에 사용할 수 있습니다.

최상의 결과, 즉 보장된 고유 ID를 위해 가능한 경우 **가장 강한 식별자** 열의 식별자를 사용해야 합니다. 여러 개의 강한 식별자를 사용하면 다양한 데이터 원본과 스키마의 강한 식별자 간에 상관 관계를 설정할 수 있습니다. 그러면 Microsoft 센티널에서 지정 된 엔터티에 대 한 보다 포괄적인 정보를 제공할 수 있습니다.

| 엔터티 유형 | 식별자 | 필수 식별자 | 가장 강한 식별자 |
| - | - | - | - |
| [**사용자 계정**](#user-account)<br>*(Account)* | 속성<br>FullName<br>NTDomain<br>DnsDomain<br>UPNSuffix<br>Sid<br>AadTenantId<br>AadUserId<br>PUID<br>IsDomainJoined<br>DisplayName<br>ObjectGuid | FullName<br>Sid<br>속성<br>AadUserId<br>PUID<br>ObjectGuid | Name + NTDomain<br>Name + UPNSuffix<br>AADUserId<br>Sid |
| [**호스트**](#host) | DnsDomain<br>NTDomain<br>HostName<br>FullName<br>NetBiosName<br>AzureID<br>OMSAgentID<br>OSFamily<br>OSVersion<br>IsDomainJoined | FullName<br>HostName<br>NetBiosName<br>AzureID<br>OMSAgentID | HostName + NTDomain<br>HostName + DnsDomain<br>NetBiosName + NTDomain<br>NetBiosName + DnsDomain<br>AzureID<br>OMSAgentID |
| [**IP 주소**](#ip-address)<br>*(IP)* | 주소 | 주소 | |
| [**맬웨어**](#malware) | 속성<br>범주 | 속성 | |
| [**파일**](#file) | 디렉터리<br>속성 | 속성 | |
| [**프로세스**](#process) | ProcessId<br>명령줄<br>ElevationToken<br>CreationTimeUtc | 명령줄<br>ProcessId | |
| [**클라우드 애플리케이션**](#cloud-application)<br>*(CloudApplication)* | AppId<br>속성<br>InstanceName | AppId<br>속성 | |
| [**도메인 이름**](#domain-name)<br>*(DNS)* | DomainName | DomainName | |
| [**Azure 리소스**](#azure-resource) | ResourceId | ResourceId | |
| [**파일 해시**](#file-hash)<br>*(FileHash)* | 알고리즘<br>값 | Algorithm + Value | |
| [**레지스트리 키**](#registry-key) | Hive<br>키 | Hive<br>키 | Hive + Key |
| [**레지스트리 값**](#registry-value) | 속성<br>값<br>ValueType | 속성 | |
| [**보안 그룹**](#security-group) | DistinguishedName<br>SID<br>ObjectGuid | DistinguishedName<br>SID<br>ObjectGuid | |
| [**URL**](#url) | Url | Url | |
| [**IoT 디바이스**](#iot-device) | IoTHub<br>DeviceId<br>DeviceName<br>IoTSecurityAgentId<br>DeviceType<br>원본<br>SourceRef<br>제조업체<br>모델<br>OperatingSystem<br>IpAddress<br>MacAddress<br>프로토콜<br>SerialNumber | IoTHub<br>DeviceId | IoTHub + DeviceId |
| [**사서함**](#mailbox) | MailboxPrimaryAddress<br>DisplayName<br>Upn<br>ExternalDirectoryObjectId<br>RiskLevel | MailboxPrimaryAddress | |
| [**메일 클러스터**](#mail-cluster) | NetworkMessageIds<br>CountByDeliveryStatus<br>CountByThreatType<br>CountByProtectionStatus<br>위협<br>쿼리<br>QueryTime<br>MailCount<br>IsVolumeAnomaly<br>원본<br>ClusterSourceIdentifier<br>ClusterSourceType<br>ClusterQueryStartTime<br>ClusterQueryEndTime<br>ClusterGroup | 쿼리<br>원본 | Query + Source |
| [**메일 메시지**](#mail-message) | 받는 사람<br>Urls<br>위협<br>보낸 사람<br>P1Sender<br>P1SenderDisplayName<br>P1SenderDomain<br>SenderIP<br>P2Sender<br>P2SenderDisplayName<br>P2SenderDomain<br>ReceivedDate<br>NetworkMessageId<br>InternetMessageId<br>제목<br>BodyFingerprintBin1<br>BodyFingerprintBin2<br>BodyFingerprintBin3<br>BodyFingerprintBin4<br>BodyFingerprintBin5<br>AntispamDirection<br>DeliveryAction<br>DeliveryLocation<br>언어<br>ThreatDetectionMethods | NetworkMessageId<br>받는 사람 | NetworkMessageId + Recipient |
| [**메일 제출**](#submission-mail) | SubmissionId<br>SubmissionDate<br>제출자<br>NetworkMessageId<br>타임스탬프<br>받는 사람<br>보낸 사람<br>SenderIp<br>제목<br>ReportType | SubmissionId<br>NetworkMessageId<br>받는 사람<br>제출자 |  |
|

## <a name="entity-type-schemas"></a>엔터티 형식 스키마

다음으로, 각 엔터티 형식의 전체 스키마를 자세히 살펴봅니다. 이러한 스키마의 대부분에는 다른 엔터티 형식에 대한 링크가 포함되어 있음을 알 수 있습니다. 예를 들어 사용자 계정 스키마에는 사용자 계정의 한 특성이 정의된 호스트이므로 Host 엔터티 형식에 대한 링크가 포함됩니다. 이러한 외부에 연결된 엔터티는 엔터티 매핑에 대한 식별자로 사용할 수 없지만 엔터티 페이지와 조사 그래프에서 엔터티의 전체 그림을 제공하는 데 매우 유용합니다.

> [!NOTE]
> **형식** 열의 값 뒤에 있는 물음표는 null 허용 필드임을 나타냅니다.

## <a name="user-account"></a>사용자 계정

*엔터티 이름: Account*

| 필드 | 형식 | Description |
| ----- | ---- | ----------- |
| Type | String | 'account' |
| Name | String | 계정의 이름입니다. 이 필드는 도메인이 추가되지 않은 이름만 포함해야 합니다. |
| *FullName* | *해당 사항 없음* | *스키마의 일부가 아니며, 이전 버전의 엔터티 매핑과의 호환성을 위해 포함되었습니다.*
| NTDomain | String | 경고 형식으로 표시되는 NETBIOS 도메인 이름(domain\username)입니다. 예: 재무, NT 기관 |
| DnsDomain | String | 정규화된 도메인 DNS 이름입니다. 예: finance.contoso.com |
| UPNSuffix | String | 계정에 대한 사용자 계정 이름 접미사입니다. 경우에 따라 도메인 이름이기도 합니다. 예: contoso.com |
| 호스트 | 엔터티 | 로컬 계정인 경우 계정을 포함하는 호스트입니다. |
| Sid | String | 계정 보안 식별자입니다(예: S-1-5-18). |
| AadTenantId | GUID? | Azure AD 테넌트 ID입니다(알려진 경우). |
| AadUserId | GUID? | Azure AD 계정 개체 ID입니다(알려진 경우). |
| PUID | GUID? | Azure AD Passport 사용자 ID입니다(알려진 경우). |
| IsDomainJoined | 부울? | 도메인 계정인지 여부를 확인합니다. |
| DisplayName | String | 계정의 표시 이름입니다. |
| ObjectGuid | GUID? | objectGUID 특성은 Active Directory에서 할당한 개체의 고유 식별자인 단일 값 특성입니다. |
|

계정 엔터티의 강한 식별자:

- Name + UPNSuffix
- AadUserId
- Sid + Host(기본 제공 계정의 SID에 필요)
- Sid(기본 제공 계정의 SID 제외)
- Name + NTDomain(NTDomain이 기본 제공 도메인(예: "Workgroup")이 아닌 경우)
- Name + Host(예: NTDomain이 기본 제공 도메인(예: "Workgroup")인 경우)
- Name + DnsDomain
- PUID
- ObjectGuid

계정 엔터티의 약한 식별자:

- 속성

## <a name="host"></a>호스트

| 필드 | 형식 | Description |
| ----- | ---- | ----------- |
| Type | String | 'host' |
| DnsDomain | String | 이 호스트가 속한 DNS 도메인입니다. 알려진 경우 도메인에 대한 전체 DNS 접미사를 포함해야 합니다. |
| NTDomain | String | 이 호스트가 속한 NT 도메인입니다. |
| HostName | String | 도메인 접미사가 없는 호스트 이름입니다. |
| *FullName* | *해당 사항 없음* | *스키마의 일부가 아니며, 이전 버전의 엔터티 매핑과의 호환성을 위해 포함되었습니다.*
| NetBiosName | String | 호스트 이름(Windows 2000 이전)입니다. |
| IoTDevice | 엔터티 | IoT 디바이스 엔터티입니다(이 호스트에서 IoT 디바이스를 나타내는 경우). |
| AzureID | String | VM의 Azure 리소스 ID입니다(알려진 경우). |
| OMSAgentID | String | OMS 에이전트 ID입니다(OMS 에이전트가 호스트에 설치되어 있는 경우). |
| OSFamily | 열거형? | 다음 값 중 하나입니다. <li>Linux<li>Windows<li>Android<li>IOS |
| OSVersion | String | 운영 체제의 자유 텍스트 표현입니다.<br>이 필드는 OSFamily보다 더 세분화된 특정 버전 또는 OSFamily 열거형에서 지원하지 않는 미래 값을 포함합니다. |
| IsDomainJoined | Bool | 이 호스트가 도메인에 속하는지 여부를 확인합니다. |
|

호스트 엔터티의 강한 식별자:
- HostName + NTDomain
- HostName + DnsDomain
- NetBiosName + NTDomain
- NetBiosName + DnsDomain
- AzureID
- OMSAgentID
- IoTDevice(엔터티 매핑을 지원하지 않음)

호스트 엔터티의 약한 식별자:
- HostName
- NetBiosName

## <a name="ip-address"></a>IP 주소

*엔터티 이름: IP*

| 필드 | 형식 | Description |
| ----- | ---- | ----------- |
| Type | String | 'ip' |
| 주소 | String | 문자열의 IP 주소입니다(예: 127.0.0.1)(IPv4 또는 IPv6에서). |
| 위치 | GeoLocation | IP 엔터티에 연결된 지리적 위치 컨텍스트입니다. <br><br>자세한 내용은 [REST API를 통해 지리적 위치 데이터를 사용 하 여 Microsoft 센티널에서 엔터티 보강 (공개 미리 보기)](geolocation-data-api.md)을 참조 하세요. |
|

IP 엔터티의 강한 식별자:
- 주소

## <a name="malware"></a>맬웨어

| 필드 | 형식 | Description |
| ----- | ---- | ----------- |
| Type | String | 'malware' |
| Name | String | 공급업체별 악성 프로그램 이름입니다(예: `Win32/Toga!rfn`). |
| 범주 | String | 공급업체별 맬웨어 범주입니다(예: 트로이). |
| 파일 | \<Entity> 나열 | 맬웨어가 발견된 연결된 파일 엔터티의 목록입니다. 파일 엔터티를 인라인 또는 참조로 포함할 수 있습니다.<br>구조체에 대한 자세한 내용은 파일 엔터티를 참조하세요. |
| 프로세스 | \<Entity> 나열 | 맬웨어가 발견된 연결된 프로세스 엔터티의 목록입니다. 이는 파일리스 활동에서 경고가 트리거될 때 자주 사용됩니다.<br>구조체에 대한 자세한 내용은 [프로세스](#process) 엔터티를 참조하세요. |
|

맬웨어 엔터티의 강한 식별자:

- Name + Category

## <a name="file"></a>파일

| 필드 | 형식 | Description |
| ----- | ---- | ----------- |
| Type | String | 'file' |
| 디렉터리 | String | 파일의 전체 경로입니다. |
| Name | String | 경로가 없는 파일 이름입니다(일부 경고에는 경로가 포함되지 않을 수 있음). |
| 호스트 | 엔터티 | 파일이 저장된 호스트입니다. |
| FileHashes | List&lt;엔터티&gt; | 이 파일과 연결된 파일 해시입니다. |
|

파일 엔터티의 강한 식별자:
- Name + Directory
- Name + FileHash
- Name + Directory + FileHash

## <a name="process"></a>프로세스

| 필드 | 형식 | Description |
| ----- | ---- | ----------- |
| Type | String | 'process' |
| ProcessId | String | 프로세스 ID입니다. |
| 명령줄 | String | 프로세스를 만드는 데 사용되는 명령줄입니다. |
| ElevationToken | 열거형? | 프로세스와 연결된 권한 상승 토큰입니다.<br>가능한 값은 다음과 같습니다.<li>TokenElevationTypeDefault<li>TokenElevationTypeFull<li>TokenElevationTypeLimited |
| CreationTimeUtc | DateTime? | 프로세스가 실행되기 시작한 시간입니다. |
| ImageFile | Entity (File) | 파일 엔터티를 인라인 또는 참조로 포함할 수 있습니다.<br>구조체에 대한 자세한 내용은 파일 엔터티를 참조하세요. |
| 계정 | 엔터티 | 프로세스를 실행하는 계정입니다.<br>[계정](#user-account) 엔터티를 인라인 또는 참조로 포함할 수 있습니다.<br>구조체에 대한 자세한 내용은 [계정](#user-account) 엔터티를 참조하세요. |
| ParentProcess | Entity (Process) | 부모 프로세스 엔터티입니다. <br>부분 데이터, 즉, PID만 포함할 수 있습니다. |
| 호스트 | 엔터티 | 프로세스가 실행되고 있었던 호스트입니다. |
| LogonSession | Entity (HostLogonSession) | 프로세스가 실행되고 있었던 세션입니다. |
|

프로세스 엔터티의 강한 식별자:

- Host + ProcessId + CreationTimeUtc
- Host + ParentProcessId + CreationTimeUtc + CommandLine
- Host + ProcessId + CreationTimeUtc + ImageFile
- Host + ProcessId + CreationTimeUtc + ImageFile.FileHash

프로세스 엔터티의 약한 식별자:

- ProcessId + CreationTimeUtc + CommandLine(및 Host 없음)
- ProcessId + CreationTimeUtc + ImageFile(및 Host 없음)

## <a name="cloud-application"></a>클라우드 애플리케이션

*엔터티 이름: CloudApplication*

| 필드 | 형식 | Description |
| ----- | ---- | ----------- |
| Type | String | 'cloud-application' |
| AppId | Int | 애플리케이션의 기술 식별자입니다. 이는 [클라우드 애플리케이션 식별자](#cloud-application-identifiers) 목록에 정의된 값 중 하나여야 합니다. AppId 필드의 값은 선택 사항입니다. |
| Name | String | 관련 클라우드 애플리케이션의 이름입니다. 애플리케이션 이름의 값은 선택 사항입니다. |
| InstanceName | String | 클라우드 애플리케이션의 사용자 정의 인스턴스 이름입니다. 고객이 보유한 동일한 유형의 여러 애플리케이션을 구별하는 데 자주 사용됩니다. |
|

클라우드 애플리케이션 엔터티의 강한 식별자:
 - AppId(InstanceName 비포함)
 - Name(InstanceName 비포함)
 - AppId + InstanceName
 - Name + InstanceName

## <a name="domain-name"></a>도메인 이름

*엔터티 이름: DNS*

| 필드 | 형식 | Description |
| ----- | ---- | ----------- |
| Type | String | 'dns' |
| DomainName | String | 경고와 연결된 DNS 레코드의 이름입니다. |
| IpAddress | List&lt;Entity (IP)&gt; | 확인된 IP 주소에 해당하는 엔터티입니다. |
| DnsServerIp | Entity (IP) | 요청을 확인하는 DNS 서버를 나타내는 엔터티입니다. |
| HostIpAddress | Entity (IP) | DNS 요청 클라이언트를 나타내는 엔터티입니다. |
|

DNS 엔터티의 강한 식별자:
- DomainName + DnsServerIp + HostIpAddress

DNS 엔터티의 약한 식별자:
- DomainName + HostIpAddress

## <a name="azure-resource"></a>Azure 리소스

| 필드 | 형식 | Description |
| ----- | ---- | ----------- |
| Type | String | 'azure-resource' |
| ResourceId | String | 리소스의 Azure 리소스 ID입니다. |
| SubscriptionId | String | 리소스의 구독 ID입니다. |
| TryGetResourceGroup | Bool | 리소스 그룹 값입니다(있는 경우). |
| TryGetProvider | Bool | 공급자 값입니다(있는 경우). |
| TryGetName | Bool | 이름 값입니다(있는 경우). |
|

Azure 리소스 엔터티의 강한 식별자:
- ResourceId

## <a name="file-hash"></a>파일 해시

*엔터티 이름: FileHash*

| 필드 | 형식 | Description |
| ----- | ---- | ----------- |
| Type | String | 'filehash' |
| 알고리즘 | 열거형 | 해시 알고리즘 형식입니다. 가능한 값은 다음과 같습니다.<li>Unknown<li>MD5<li>SHA1<li>SHA256<li>SHA256AC |
| 값 | String | 해시 값입니다. |
|

파일 해시 엔터티의 강한 식별자:
- Algorithm + Value

## <a name="registry-key"></a>레지스트리 키

*엔터티 이름: RegistryKey*

| 필드 | 형식 | Description |
| ----- | ---- | ----------- |
| Type | String | 'registry-key' |
| Hive | 열거형? | 다음 값 중 하나입니다.<li>HKEY_LOCAL_MACHINE<li>HKEY_CLASSES_ROOT<li>HKEY_CURRENT_CONFIG<li>HKEY_USERS<li>HKEY_CURRENT_USER_LOCAL_SETTINGS<li>HKEY_PERFORMANCE_DATA<li>HKEY_PERFORMANCE_NLSTEXT<li>HKEY_PERFORMANCE_TEXT<li>HKEY_A<li>HKEY_CURRENT_USER |
| 키 | String | 레지스트리 키 경로입니다. |
|

레지스트리 키 엔터티의 강한 식별자:
- Hive + Key

## <a name="registry-value"></a>레지스트리 값

*엔터티 이름: RegistryValue*

| 필드 | 형식 | Description |
| ----- | ---- | ----------- |
| Type | String | 'registry-value' |
| 키 | Entity (RegistryKey) | 레지스트리 키 엔터티입니다. |
| Name | String | 레지스트리 값 이름입니다. |
| 값 | String | 값 데이터의 문자열 형식 표현입니다. |
| ValueType | 열거형? | 다음 값 중 하나입니다.<li>String<li>이진<li>Dword<li>Qword<li>MultiString<li>ExpandString<li>None<li>Unknown<br>값은 Microsoft.Win32.RegistryValueKind 열거형을 따라야 합니다. |
|

레지스트리 값 엔터티의 강한 식별자:
- Key + Name

레지스트리 값 엔터티의 약한 식별자:
- Name(Key 비포함)

## <a name="security-group"></a>보안 그룹

*엔터티 이름: SecurityGroup*

| 필드 | 형식 | Description |
| ----- | ---- | ----------- |
| Type | String | 'security-group' |
| DistinguishedName | String | 그룹 고유 이름입니다. |
| SID | String | SID 특성은 그룹의 SID(보안 식별자)를 지정하는 단일 값 특성입니다. |
| ObjectGuid | GUID? | objectGUID 특성은 Active Directory에서 할당한 개체의 고유 식별자인 단일 값 특성입니다. |
|

보안 그룹 엔터티의 강한 식별자:
 - DistinguishedName
 - SID
 - ObjectGuid

## <a name="url"></a>URL

| 필드 | 형식 | Description |
| ----- | ---- | ----------- |
| Type | String | 'url' |
| Url | URI | 엔터티에서 가리키는 전체 URL입니다. |
|

URL 엔터티의 강한 식별자:
- Url(절대 URL인 경우)

URL 엔터티의 약한 식별자:
- Url(상대 URL인 경우)

## <a name="iot-device"></a>IoT 디바이스

*엔터티 이름: IoTDevice*

| 필드 | 형식 | Description |
| ----- | ---- | ----------- |
| Type | String | 'iotdevice' |
| IoTHub | Entity (AzureResource) | 디바이스가 속한 IoT Hub를 나타내는 AzureResource 엔터티입니다. |
| DeviceId | String | IoT Hub의 컨텍스트에 있는 디바이스의 ID입니다. |
| DeviceName | String | 디바이스의 이름입니다. |
| IoTSecurityAgentId | GUID? | 디바이스에서 실행되는 *Defender for IoT* 에이전트의 ID입니다. |
| DeviceType | String | 디바이스의 유형('온도 센서', '냉동고', '풍력 터빈' 등)입니다. |
| 원본 | String | 디바이스 엔터티의 원본(Microsoft/공급업체)입니다. |
| SourceRef | Entity (Url) | 디바이스가 관리되는 원본 항목에 대한 URL 참조입니다. |
| 제조업체 | String | 디바이스의 제조업체입니다. |
| 모델 | String | 디바이스의 모델입니다. |
| OperatingSystem | String | 디바이스가 실행되는 운영 체제입니다. |
| IpAddress | Entity (IP) | 디바이스의 현재 IP 주소입니다. |
| MacAddress | String | 디바이스의 Mac 주소입니다. |
| 프로토콜 | List&lt;String&gt; | 디바이스에서 지원하는 프로토콜 목록입니다. |
| SerialNumber | String | 디바이스의 일련 번호입니다. |
|

IoT 디바이스 엔터티의 강한 식별자:
- IoTHub + DeviceId

IoT 디바이스 엔터티의 약한 식별자:
- DeviceId(IoTHub 비포함)

## <a name="mailbox"></a>사서함

| 필드 | 형식 | Description |
| ----- | ---- | ----------- |
| Type | String | 'mailbox' |
| MailboxPrimaryAddress | String | 사서함의 기본 주소입니다. |
| DisplayName | String | 사서함의 표시 이름입니다. |
| Upn | String | 사서함의 UPN입니다. |
| RiskLevel | 열거형? | 이 사서함의 위험 수준입니다. 가능한 값은 다음과 같습니다.<li>None<li>낮음<li>중간<li>높은 |
| ExternalDirectoryObjectId | GUID? | 사서함의 AzureAD 식별자입니다. 이 속성은 계정 엔터티의 AadUserId와 비슷하지만 Office 쪽의 사서함 개체에만 적용됩니다. |
|

사서함 엔터티의 강한 식별자:
- MailboxPrimaryAddress

## <a name="mail-cluster"></a>메일 클러스터

*엔터티 이름: MailCluster*

> [!NOTE]
> **Microsoft Defender for Office 365** 는 이전에 O365 ATP(Office 365 Advanced Threat Protection)로 알려졌습니다.

| 필드 | 형식 | Description |
| ----- | ---- | ----------- |
| Type | String | 'mail-cluster' |
| NetworkMessageIds | IList&lt;String&gt; | 메일 클러스터의 일부인 메일 메시지 ID입니다. |
| CountByDeliveryStatus | IDictionary&lt;String,Int&gt; | DeliveryStatus 문자열 표현별 메일 메시지 수입니다. |
| CountByThreatType | IDictionary&lt;String,Int&gt; | ThreatType 문자열 표현별 메일 메시지 수입니다. |
| CountByProtectionStatus | IDictionary&lt;String,long&gt; | 위협 방지 상태별 메일 메시지 수입니다. |
| 위협 | IList&lt;String&gt; | 메일 클러스터의 일부인 메일 메시지의 위협입니다. |
| 쿼리 | String | 메일 클러스터의 메시지를 식별하는 데 사용된 쿼리입니다. |
| QueryTime | DateTime? | 쿼리 시간입니다. |
| MailCount | 정수? | 메일 클러스터의 일부인 메일 메시지 수입니다. |
| IsVolumeAnomaly | 부울? | 볼륨 이상 메일 클러스터인지 여부를 확인합니다. |
| 원본 | String | 클러스터의 원본입니다(기본값: 'O365 ATP'). |
| ClusterSourceIdentifier | String | 이 메일 클러스터의 원본인 메일의 네트워크 메시지 ID입니다. |
| ClusterSourceType | String | 메일 클러스터의 원본 유형입니다. 이는 Microsoft Defender for Office 365의 MailClusterSourceType 설정에 매핑됩니다(위 참고 사항 참조). |
| ClusterQueryStartTime | DateTime? | 클러스터 시작 시간이며, 클러스터 수 쿼리의 시작 시간으로 사용됩니다. 일반적으로 Microsoft Defender for Office 365의 종료 시간에서 DaysToLookBack 설정을 뺀 날짜입니다(위의 참고 사항 참조). |
| ClusterQueryEndTime | DateTime? | 클러스터 종료 시간이며, 클러스터 수 쿼리의 종료 시간으로 사용됩니다. 일반적으로 메일 데이터의 수신 시간입니다. |
| ClusterGroup | String | Microsoft Defender for Office 365에서 사용되는 Kusto 쿼리 키에 해당합니다(위의 참고 사항 참조). |
|

메일 클러스터 엔터티의 강한 식별자:
- Query + Source

## <a name="mail-message"></a>메일 메시지

*엔터티 이름: MailMessage*

| 필드 | 형식 | Description |
| ----- | ---- | ----------- |
| Type | String | 'mail-message' |
| 파일 | IList&lt;File&gt; | 이 메일 메시지의 첨부 파일에 대한 파일 엔터티입니다. |
| 받는 사람 | String | 이 메일 메시지의 받는 사람입니다. 받는 사람이 여러 명인 경우 메일 메시지가 복사되고 각 복사본에는 한 명의 받는 사람이 있습니다. |
| Urls | IList&lt;String&gt; | 이 메일 메시지에 포함된 URL입니다. |
| 위협 | IList&lt;String&gt; | 이 메일 메시지에 포함된 위협입니다. |
| 보낸 사람 | String | 보낸 사람의 전자 메일 주소입니다. |
| P1Sender | String | "P2 사용자(기본)를 대신하여" 이 메일을 보낸 사용자(위임된)의 이메일 ID입니다. 대리자가 이메일을 보내지 않은 경우 이 값은 P2Sender와 같습니다. |
| P1SenderDisplayName | String | "P2 사용자(기본)를 대신하여" 이 메일을 보낸 사용자(위임된)의 표시 이름입니다. 이메일 헤더에 "OnbehalfofSenderDisplayName" 속성으로 표시됩니다. |
| P1SenderDomain | String | "P2 사용자(기본)를 대신하여" 이 메일을 보낸 사용자(위임된)의 이메일 도메인입니다. 대리자가 이메일을 보내지 않은 경우 이 값은 P2SenderDomain과 같습니다. |
| P2Sender | String | 이 이메일을 보낸 사람을 대신하는 사용자(기본)의 이메일입니다. |
| P2SenderDisplayName | String | 이 이메일을 보낸 사람을 대신하는 사용자(기본)의 표시 이름입니다. 대리자가 이메일을 보내지 않은 경우 이 값은 보낸 사람의 표시 이름을 나타냅니다. |
| P2SenderDomain | String | 이 이메일을 보낸 사람을 대신하는 사용자(기본)의 이메일 도메인입니다. 대리자가 이메일을 보내지 않은 경우 이 값은 보낸 사람의 도메인을 나타냅니다. |
| SenderIP | String | 보낸 사람의 IP 주소입니다. |
| ReceivedDate | DateTime | 이 메시지를 받은 날짜입니다. |
| NetworkMessageId | GUID? | 이 메일 메시지의 네트워크 메시지 ID입니다. |
| InternetMessageId | String | 이 메일 메시지의 인터넷 메시지 ID입니다. |
| 제목 | String | 이 메일 메시지의 제목입니다. |
| BodyFingerprintBin1<br>BodyFingerprintBin2<br>BodyFingerprintBin3<br>BodyFingerprintBin4<br>BodyFingerprintBin5 | UInt? | Microsoft Defender for Office 365에서 일치하거나 비슷한 메일 메시지를 찾는 데 사용됩니다. |
| AntispamDirection | 열거형? | 이 메일 메시지의 방향입니다. 가능한 값은 다음과 같습니다.<li>Unknown<li>인바운드<li>아웃바운드<li>Intraorg(내부) |
| DeliveryAction | 열거형? | 이 메일 메시지의 배달 동작입니다. 가능한 값은 다음과 같습니다.<li>Unknown<li>DeliveredAsSpam<li>배달됨<li>차단<li>대체됨 |
| DeliveryLocation | 열거형? | 이 메일 메시지의 배달 위치입니다. 가능한 값은 다음과 같습니다.<li>Unknown<li>받은 편지함<li>JunkFolder<li>DeletedFolder<li>격리<li>외부<li>실패<li>Dropped<li>Forwarded |
| 언어 | String | 메일 내용이 작성되는 언어입니다. |
| ThreatDetectionMethods | IList&lt;String&gt; | 이 메일에 적용되는 위협 탐지 방법의 목록입니다. |
|

메일 메시지 엔터티의 강한 식별자:
- NetworkMessageId + Recipient

## <a name="submission-mail"></a>메일 제출

*엔터티 이름: SubmissionMail*

| 필드 | 형식 | Description |
| ----- | ---- | ----------- |
| Type | String | 'SubmissionMail' |
| SubmissionId | GUID? | 제출 ID입니다. |
| SubmissionDate | DateTime? | 이 제출에 대해 보고된 날짜/시간입니다. |
| 제출자 | String | 제출자의 이메일 주소입니다. |
| NetworkMessageId | GUID? | 제출이 속한 이메일의 네트워크 메시지 ID입니다. |
| 타임스탬프 | DateTime? | 메시지를 받은 타임스탬프(메일)입니다. |
| 받는 사람 | String | 메일의 받는 사람입니다. |
| 보낸 사람 | String | 메일의 보낸 사람입니다. |
| SenderIp | String | 보낸 사람의 IP입니다. |
| 제목 | String | 제출 메일의 제목입니다. |
| ReportType | String | 지정된 인스턴스에 대한 제출 유형입니다. 이는 Junk, Phish, Malware 또는 NotJunk에 매핑됩니다. |
|

SubmissionMail 엔터티의 강한 식별자:
- SubmissionId, Submitter, NetworkMessageId, Recipient

## <a name="cloud-application-identifiers"></a>클라우드 애플리케이션 식별자

다음 목록에는 알려진 클라우드 애플리케이션의 식별자가 정의되어 있습니다. 앱 ID 값은 [클라우드 애플리케이션](#cloud-application) 엔터티 식별자로 사용됩니다.

|앱 ID|속성|
|------|----|
|10026|DocuSign|
|10395|Anaplan|
|10489|상자|
|10549|Cisco Webex|
|10618|Atlassian|
|10915|cornerstone ondemand|
|10921|Zendesk|
|10980|Okta|
|11042|Jive Software|
|11114|Salesforce|
|11161|Office 365|
|11162|Microsoft OneNote Online|
|11394|Microsoft Online Services|
|11522|Yammer|
|11599|Amazon Web Services|
|11627|Dropbox|
|11713|Expensify|
|11770|G Suite|
|12005|SuccessFactors|
|12260|Microsoft Azure|
|12275|Workday|
|13843|LivePerson|
|13979|Concur|
|14509|ServiceNow|
|15570|Tableau|
|15600|비즈니스용 Microsoft OneDrive|
|15782|Citrix ShareFile|
|17152|Amazon|
|17865|Ariba Inc|
|18432|Zscaler|
|19688|Xactly|
|20595|클라우드 앱 용 Microsoft Defender|
|20892|Microsoft SharePoint Online|
|20893|Microsoft Exchange Online|
|20940|Active Directory|
|20941|Adallom CPanel|
|22110|Google Cloud Platform|
|22930|Gmail|
|23004|Autodesk Fusion Lifecycle|
|23043|Slack|
|23233|Microsoft Office Online|
|25275|비즈니스용 Microsoft Skype|
|25988|Google Docs|
|26055|Microsoft Office 365 관리 센터|
|26060|OPSWAT Gears|
|26061|Microsoft Word Online|
|26062|Microsoft PowerPoint Online|
|26063|Microsoft Excel Online|
|26069|Google 드라이브|
|26206|Workiva|
|26311|Microsoft Dynamics|
|26318|Microsoft Azure AD|
|26320|Microsoft Office Sway|
|26321|Microsoft Delve|
|26324|Microsoft Power BI|
|27548|Microsoft Forms|
|27592|Microsoft Flow|
|27593|Microsoft PowerApps|
|28353|Workplace by Facebook|
|28373|CAS Proxy Emulator|
|28375|Microsoft Teams|
|32780|Microsoft Dynamics 365|
|33626|Google|
|34127|Microsoft AppSource|
|34667|HighQ|
|35395|Microsoft Dynamics Talent|
|

## <a name="next-steps"></a>다음 단계

이 문서에서는 Microsoft 센티널에서 엔터티 구조, 식별자 및 스키마에 대해 알아보았습니다.

[엔터티](entities-in-azure-sentinel.md) 및 [엔터티 매핑](map-data-fields-to-entities.md)에 대해 자세히 알아보세요. 

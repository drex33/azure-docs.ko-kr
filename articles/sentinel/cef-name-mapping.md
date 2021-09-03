---
title: CEF(Common Event Format) 키 및 CommonSecurityLog 필드 매핑
description: 이 문서에서는 CEF 키와 Azure Sentinel에 있는 CommonSecurityLog의 해당 필드 이름 간 매핑을 보여 줍니다.
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: reference
ms.date: 07/26/2021
ms.openlocfilehash: f31aa44237e0312d08a43155db867192d83a57ef
ms.sourcegitcommit: bb1c13bdec18079aec868c3a5e8b33ef73200592
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/27/2021
ms.locfileid: "114722025"
---
# <a name="cef-and-commonsecuritylog-field-mapping"></a>CEF 및 CommonSecurityLog 필드 매핑

다음 표에서는 CEF(Common Event Format) 필드 이름과 Azure Sentinel에 있는 CommonSecurityLog에서 사용하는 이름 간의 매핑을 보여 주므로, Azure Sentinel에서 CEF 데이터 원본을 사용하여 작업할 때 유용할 수 있습니다.

자세한 내용은 [Common Event Format을 사용하여 외부 솔루션 연결](connect-common-event-format.md)을 참조하세요.

> [!NOTE]
> Log Analytics로 [CEF 데이터를 수집](connect-common-event-format.md#prerequisites)하려면 Azure Sentinel 작업 영역이 필요합니다.
>

## <a name="a---c"></a>A - C

|CEF 키 이름  |CommonSecurityLog 필드 이름  |Description  |
|---------|---------|---------|
| act    |    <a name="deviceaction"></a> DeviceAction     |  이벤트에 언급된 작업입니다.       |
|   app  |    ApplicationProtocol     |  애플리케이션에 사용되는 프로토콜(예: HTTP, HTTPS, SSHv2, Telnet, POP, IMPA, IMAPS 등)입니다.   |
| cnt    |    EventCount     |  동일한 이벤트가 관찰된 횟수를 표시하는 이벤트와 연결된 개수입니다.       |
| | | |

## <a name="d"></a>D

|CEF 키 이름  |CommonSecurityLog 이름  |Description  |
|---------|---------|---------|
|디바이스 공급업체     |  DeviceVendor       | 디바이스 제품 및 버전 정의와 함께 전송 디바이스 유형을 고유하게 식별하는 문자열입니다.       |
|디바이스 제품     |   DeviceProduct      |   디바이스 공급업체 및 버전 정의와 함께 전송 디바이스 유형을 고유하게 식별하는 문자열입니다.        |
|디바이스 버전     |   DeviceVersion      |      디바이스 제품 및 공급업체 정의와 함께 전송 디바이스 유형을 고유하게 식별하는 문자열입니다.     |
| destinationDnsDomain    | DestinationDnsDomain        |   FQDN(정규화된 도메인 이름)의 DNS 부분입니다.      |
| destinationServiceName | DestinationServiceName | 이벤트에 의해 대상으로 지정된 서비스입니다. 예들 들어 `sshd`입니다.|
| destinationTranslatedAddress | DestinationTranslatedAddress | IP 네트워크에서 이벤트에 의해 참조되는 변환된 대상을 IPv4 IP 주소로 식별합니다. |
| destinationTranslatedPort | DestinationTranslatedPort | 변환 후 포트(예: 방화벽)입니다. <br>유효한 포트 번호: `0` - `65535` |
| deviceDirection | <a name="communicationdirection"></a> CommunicationDirection | 관찰된 통신이 진행된 방향에 대한 정보입니다. 유효한 값은 <br>- `0` = 인바운드 <br>- `1` = 아웃바운드 |
| deviceDnsDomain | DeviceDnsDomain | FQDN(정규화된 도메인 이름)의 DNS 도메인 부분입니다. |
|DeviceEventClassID     |   DeviceEventClassID     |   이벤트 유형별 고유 식별자 역할을 하는 문자열 또는 정수입니다.      |
| deviceExternalID | DeviceExternalID | 이벤트를 생성하는 디바이스를 고유하게 식별하는 이름입니다. |
| deviceFacility | DeviceFacility | 이벤트를 생성하는 기능입니다.|
| deviceInboundInterface | DeviceInboundInterface |패킷 또는 데이터가 디바이스에 들어갈 때 사용된 인터페이스입니다.  |
| deviceNtDomain | DeviceNtDomain | 디바이스 주소의 Windows 도메인입니다. |
| deviceOutboundInterface | DeviceOutboundInterface |패킷이 디바이스에서 나올 때 사용된 인터페이스입니다. |
| devicePayloadId |DevicePayloadId |이벤트와 연결된 페이로드의 고유 식별자입니다. |
| deviceProcessName | ProcessName | 이벤트와 연결된 프로세스 이름입니다. <br><br>예를 들어 UNIX에서 syslog 항목을 생성하는 프로세스입니다. |
| deviceTranslatedAddress | DeviceTranslatedAddress | IP 네트워크에서 이벤트가 참조하는 변환된 디바이스 주소를 식별합니다. <br><br>형식은 Ipv4 주소입니다. |
| dhost |DestinationHostName | IP 네트워크에서 이벤트가 참조하는 대상입니다.  <br>노드를 사용할 수 있는 경우 이 형식은 대상 노드와 연결된 FQDN이어야 합니다. 예를 들어 `host.domain.com` 또는 `host`입니다. |
| dmac | DestinationMacAddress | 대상 MAC 주소(FQDN)입니다. |
| dntdom | DestinationNTDomain | 대상 주소의 Windows 도메인 이름입니다.|
| dpid | DestinationProcessId |이벤트와 연결된 대상 프로세스의 ID입니다.|
| dpriv | DestinationUserPrivileges | 대상 사용 권한을 정의합니다. <br>유효한 값: `Admninistrator`, `User`, `Guest` |
| dproc | DestinationProcessName | 이벤트 대상 프로세스의 이름입니다(예: `telnetd` 또는 `sshd.`). |
| dpt | DestinationPort | 대상 포트입니다. <br>유효한 값: `*0` - `65535` |
| dst | DestinationIP | IP 네트워크에서 이벤트가 참조하는 대상 IpV4 주소입니다. |
| dtz | DeviceTimeZone | 이벤트를 생성하는 디바이스의 표준 시간대입니다. |
| duid |DestinationUserId | ID로 대상 사용자를 식별합니다. |
| duser | DestinationUserName |이름으로 대상 사용자를 식별합니다.|
| dvc | DeviceAddress | 이벤트를 생성하는 디바이스의 IPv4 주소입니다. |
| dvchost | DeviceName | 노드를 사용할 수 있는 경우 디바이스 노드와 연결된 FQDN입니다. 예를 들어 `host.domain.com` 또는 `host`입니다.| 
| dvcmac | DeviceMacAddress | 이벤트를 생성하는 디바이스의 MAC 주소입니다. |
| dvcpid | 프로세스 ID | 이벤트를 생성하는 디바이스에서 프로세스의 ID를 정의합니다. |

## <a name="e---i"></a>E - I

|CEF 키 이름  |CommonSecurityLog 이름  |Description  |
|---------|---------|---------|
|externalId    |   ExternalID      | 원본 디바이스에서 사용하는 ID입니다. 일반적으로 이러한 값은 각각 이벤트와 연결된 늘어나는 값입니다.        |
|fileCreateTime     |  FileCreateTime      | 파일이 만들어진 시간입니다.        |
|fileHash     |   FileHash      |   파일의 해시입니다.      |
|fileId     |   FileID      |파일과 연결된 ID(예: inode)입니다.         |
| fileModificationTime | FileModificationTime |파일이 마지막으로 수정된 시간입니다. |
| filePath | FilePath | 파일 이름을 포함하는 파일의 전체 경로입니다. 예를 들어 `C:\ProgramFiles\WindowsNT\Accessories\wordpad.exe` 또는 `/usr/bin/zip`입니다.|
| filePermission |FilePermission |파일의 사용 권한입니다. |
| fileType | FileType | 파이프, 소켓 등의 파일 형식입니다.|
|fname | FileName| 경로 없이 파일의 이름입니다. |
| fsize | FileSize | 파일의 크기입니다. |
|호스트    |  Computer       | Syslog의 호스트        |
|in     |  ReceivedBytes      |인바운드로 전송된 바이트 수입니다.         |
| | | |

## <a name="m---p"></a>M - P

|CEF 키 이름  |CommonSecurityLog 이름  |Description  |
|---------|---------|---------|
|msg   |  메시지       | 이벤트에 대한 자세한 정보를 제공하는 메시지입니다.        |
|이름     |  활동       |   사용자가 읽고 이해할 수 있는 이벤트 설명을 나타내는 문자열입니다.     |
|oldFileCreateTime     |  OldFileCreateTime       | 이전 파일을 만든 시간입니다.        |
|oldFileHash     |   OldFileHash      |   이전 파일의 해시입니다.      |
|oldFileId     |   OldFileId     |   이전 파일과 연결된 ID(예: inode)입니다.      |
| oldFileModificationTime | OldFileModificationTime |파일이 마지막으로 수정된 시간입니다. |
| oldFileName |  OldFileName |이전 파일의 이름입니다. |
| oldFilePath | OldFilePath | 파일 이름을 포함하여 이전 파일의 전체 경로입니다. <br>예를 들어 `C:\ProgramFiles\WindowsNT\Accessories\wordpad.exe` 또는 `/usr/bin/zip`입니다.|
| oldFilePermission | OldFilePermission |이전 파일의 권한입니다. |
|oldFileSize | OldFileSize | 이전 파일의 크기입니다.|
| oldFileType | OldFileType | 파이프, 소켓 등 이전 파일의 파일 형식입니다.|
| out | SentBytes | 아웃바운드로 전송된 바이트 수입니다. |
| 결과 | 결과 | 이벤트의 결과(예: `success` 또는 `failure`)입니다.|
|proto    |  프로토콜       | 사용된 계층 4 프로토콜을 식별하는 전송 프로토콜입니다. <br><br>가능한 값에는 `TCP` 또는 `UDP`와 같은 프로토콜 이름이 포함됩니다.        |
| | | |

## <a name="r---t"></a>R - T

|CEF 키 이름  |CommonSecurityLog 이름  |Description  |
|---------|---------|---------|
|요청     |   RequestURL      | 프로토콜을 포함하여 HTTP 요청에 대해 액세스되는 URL입니다. 예를 들어 `http://www/secure.com`        |
|requestClientApplication     |   RequestClientApplication      |   요청과 연결된 사용자 에이전트입니다.      |
| requestContext | RequestContext | HTTP 참조 페이지와 같이 요청이 시작된 콘텐츠를 설명합니다. |
| requestCookies | RequestCookies |요청과 연결된 쿠키입니다. |
| requestMethod | RequestMethod | URL에 액세스하는 데 사용되는 메서드입니다. <br><br>유효한 값에는 `POST`, `GET` 등의 메서드가 포함됩니다. |
| rt | ReceiptTime | 작업과 관련된 이벤트를 받은 시간입니다. |
|심각도     |  <a name="logseverity"></a> LogSeverity       |  이벤트의 중요도를 설명하는 문자열 또는 정수입니다.<br><br> 유효한 문자열 값: `Unknown`, `Low`, `Medium`, `High`, `Very-High` <br><br>유효한 정수 값은 다음과 같습니다.<br> - `0`-`3` = 낮음 <br>- `4`-`6` = 보통<br>- `7`-`8` = 높음<br>- `9`-`10` = 매우 높음 |
| shost    | SourceHostName        |이벤트가 IP 네트워크에서 참조하는 원본을 식별합니다. 노드를 사용할 수 있는 경우 형식은 원본 노드와 연결된 DQDN(정규화된 도메인 이름)이어야 합니다. 예를 들어 `host` 또는 `host.domain.com`입니다. |
| smac | SourceMacAddress | 원본 MAC 주소입니다. |
| sntdom | SourceNTDomain | 원본 주소에 대한 Windows 도메인 이름입니다. |
| sourceDnsDomain | SourceDnsDomain | 전체 FQDN의 DNS 도메인 부분입니다. |
| sourceServiceName | SourceServiceName | 이벤트 생성을 담당하는 서비스입니다. |
| sourceTranslatedAddress | SourceTranslatedAddress | IP 네트워크에서 이벤트가 참조하는 변환된 원본을 식별합니다. |
| sourceTranslatedPort | SourceTranslatedPort | 변환 후의 원본 포트(예: 방화벽)입니다. <br>유효한 포트 번호는 `0` -  `65535`입니다. |
| spid | SourceProcessId | 이벤트와 연결된 원본 프로세스의 ID입니다.|
| spriv | SourceUserPrivileges | 원본 사용자의 권한입니다. <br><br>유효한 값에는 `Administrator`, `User`, `Guest`가 포함됩니다. |
| sproc | SourceProcessName | 이벤트 원본 프로세스의 이름입니다.|
| spt | SourcePort | 원본 포트 번호입니다. <br>유효한 포트 번호는 `0` -  `65535`입니다. |
| src | SourceIP |IP 네트워크에서 이벤트가 IPv4 주소로 참조하는 원본입니다. |
| suid | SourceUserID | ID로 원본 사용자를 식별합니다. |
| suser | SourceUserName | 이름으로 원본 사용자를 식별합니다. |
| type | EventType | 이벤트 유형입니다. 값은 다음과 같습니다. <br>- `0`: 기본 이벤트 <br>- `1`: 집계됨 <br>- `2`: 상관 관계 이벤트 <br>- `3`: 작업 이벤트 <br><br>**참고**: 이 이벤트는 기본 이벤트에 대해 생략할 수 있습니다. |
| | | |

## <a name="custom-fields"></a>사용자 지정 필드

다음 표에서는 고객이 기본 제공 필드에 적용되지 않는 데이터에 사용할 수 있는 CEF 키 및 CommonSecurityLog 필드의 이름을 매핑합니다.

### <a name="custom-ipv6-address-fields"></a>사용자 지정 IPv6 주소 필드

다음 표에서는 사용자 지정 데이터에 사용할 수 있는 *IPv6* 주소 필드의 CEF 키 및 CommonSecurityLog 이름을 매핑합니다.

|CEF 키 이름  |CommonSecurityLog 이름  |
|---------|---------|
|     c6a1    |     DeviceCustomIPv6Address1       |
|     c6a1Label    |     DeviceCustomIPv6Address1Label    |
|     c6a2    |     DeviceCustomIPv6Address2    |
|     c6a2Label    |     DeviceCustomIPv6Address2Label    |
|     c6a3    |     DeviceCustomIPv6Address3    |
|     c6a3Label    |     DeviceCustomIPv6Address3Label    |
|     c6a4    |     DeviceCustomIPv6Address4    |
|     c6a4Label    |     DeviceCustomIPv6Address4Label    |
|     cfp1    |     DeviceCustomFloatingPoint1    |
|     cfp1Label    |     deviceCustomFloatingPoint1Label    |
|     cfp2    |     DeviceCustomFloatingPoint2    |
|     cfp2Label    |     deviceCustomFloatingPoint2Label    |
|     cfp3    |     DeviceCustomFloatingPoint3    |
|     cfp3Label    |     deviceCustomFloatingPoint3Label    |
|     cfp4    |     DeviceCustomFloatingPoint4    |
|     cfp4Label    |     deviceCustomFloatingPoint4Label    |
| | |

### <a name="custom-number-fields"></a>사용자 지정 숫자 필드

다음 표에서는 사용자 지정 데이터에 사용할 수 있는 *숫자* 필드의 CEF 키 및 CommonSecurityLog 이름을 매핑합니다.

|CEF 키 이름  |CommonSecurityLog 이름  |
|---------|---------|
|     cn1    |     DeviceCustomNumber1       |
|     cn1Label    |     DeviceCustomNumber1Label       |
|     cn2    |     DeviceCustomNumber2       |
|     cn2Label    |     DeviceCustomNumber2Label       |
|     cn3    |     DeviceCustomNumber3       |
|     cn3Label    |     DeviceCustomNumber3Label       |
| | |

### <a name="custom-string-fields"></a>사용자 지정 문자열 필드

다음 표에서는 사용자 지정 데이터에 사용할 수 있는 *문자열* 필드의 CEF 키 및 CommonSecurityLog 이름을 매핑합니다.

|CEF 키 이름  |CommonSecurityLog 이름  |
|---------|---------|
|     cs1    |     DeviceCustomString1 <sup>[1](#use-sparingly)</sup>    |
|     cs1Label    |     DeviceCustomString1Label <sup>[1](#use-sparingly)</sup>    |
|     cs2    |     DeviceCustomString2 <sup>[1](#use-sparingly)</sup>   |
|     cs2Label    |     DeviceCustomString2Label   <sup>[1](#use-sparingly)</sup> |
|     cs3    |     DeviceCustomString3  <sup>[1](#use-sparingly)</sup>  |
|     cs3Label    |     DeviceCustomString3Label   <sup>[1](#use-sparingly)</sup> |
|     cs4    |     DeviceCustomString4   <sup>[1](#use-sparingly)</sup> |
|     cs4Label    |     DeviceCustomString4Label  <sup>[1](#use-sparingly)</sup>  |
|     cs5    |     DeviceCustomString5 <sup>[1](#use-sparingly)</sup>   |
|     cs5Label    |     DeviceCustomString5Label <sup>[1](#use-sparingly)</sup>    |
|     cs6    |     DeviceCustomString6   <sup>[1](#use-sparingly)</sup> |
|     cs6Label    |     DeviceCustomString6Label   <sup>[1](#use-sparingly)</sup> |
|     flexString1    |     FlexString1    |
|     flexString1Label    |     FlexString1Label    |
|     flexString2    |     FlexString2    |
|     flexString2Label    |     FlexString2Label    |
| | |

> [!TIP]
> <a name="use-sparingly"></a><sup>1</sup> 가능한 경우 **DeviceCustomString** 필드를 드물게 사용하고 보다 구체적인 기본 제공 필드를 사용하는 것이 좋습니다.
> 

### <a name="custom-timestamp-fields"></a>사용자 지정 타임스탬프 필드

다음 표에서는 사용자 지정 데이터에 사용할 수 있는 *타임스탬프* 필드의 CEF 키 및 CommonSecurityLog 이름을 매핑합니다.

|CEF 키 이름  |CommonSecurityLog 이름  |
|---------|---------|
|     deviceCustomDate1    |     DeviceCustomDate1    |
|     deviceCustomDate1Label    |     DeviceCustomDate1Label    |
|     deviceCustomDate2       |     DeviceCustomDate2    |
|     deviceCustomDate2Label    |     DeviceCustomDate2Label    |
|     flexDate1    |     FlexDate1    |
|     flexDate1Label    |     FlexDate1Label    |
| | |

### <a name="custom-integer-data-fields"></a>사용자 지정 정수 데이터 필드

다음 표에서는 사용자 지정 데이터에 사용할 수 있는 *정수* 필드의 CEF 키 및 CommonSecurityLog 이름을 매핑합니다.

|CEF 키 이름  |CommonSecurityLog 이름  |
|---------|---------|
|     flexNumber1    |     FlexNumber1    |
|     flexNumber1Label    |     FlexNumber1Label    |
|     flexNumber2    |     FlexNumber2    |
|     flexNumber2Label    |     FlexNumber2Label    |
| | |

## <a name="enrichment-fields"></a>보강 필드

다음 **CommonSecurityLog** 필드는 Azure Sentinel에서 원본 디바이스에서 받은 원래 이벤트를 보강하기 위해 추가되며 CEF 키에 매핑이 없습니다.

### <a name="threat-intelligence-fields"></a>위협 인텔리전스 필드

|CommonSecurityLog 필드 이름  |Description  |
|---------|---------|
|   **IndicatorThreatType**  |  위협 인텔리전스 피드에 따른 [MaliciousIP](#MaliciousIP) 위협 유형입니다.       |
| <a name="MaliciousIP"></a>**MaliciousIP** | 현재 위협 인텔리전스 피드와 상관 관계가 있는 메시지의 모든 IP 주소를 나열합니다. |
|  **MaliciousIPCountry**   | 레코드 수집 시 지리적 정보에 따른 [MaliciousIP](#MaliciousIP) 국가입니다.        |
| **MaliciousIPLatitude**    |   레코드 수집 시 지리적 정보에 따른 [MaliciousIP](#MaliciousIP) 경도입니다.      |
| **MaliciousIPLongitude**    |  레코드 수집 시 지리적 정보에 따른 [MaliciousIP](#MaliciousIP) 경도입니다.       |
| **ReportReferenceLink**    |    위협 인텔리전스 보고서에 대한 링크입니다.     |
|  **ThreatConfidence**   |   위협 인텔리전스 피드에 따른 [MaliciousIP](#MaliciousIP) 위협 신뢰도입니다.      |
| **ThreatDescription**    |   위협 인텔리전스 피드에 따른 [MaliciousIP](#MaliciousIP) 위협 설명입니다.      |
| **ThreatSeverity** | 레코드 수집 시 위협 인텔리전스 피드에 따른 [MaliciousIP](#MaliciousIP)의 위협 심각도입니다. |
|     |         |

### <a name="additional-enrichment-fields"></a>추가 보강 필드

|CommonSecurityLog 필드 이름  |Description  |
|---------|---------|
|**OriginalLogSeverity**     |  항상 비어 있으며 CiscoASA와의 통합에서 지원됩니다. <br>로그 심각도 값에 대한 자세한 내용은 [LogSeverity](#logseverity) 필드를 참조하세요.       |
|**RemoteIP**     |     원격 IP 주소입니다. <br>가능하면 이 값은 [CommunicationDirection](#communicationdirection) 필드를 기준으로 합니다.     |
|**RemotePort**     |   원격 포트입니다. <br>가능하면 이 값은 [CommunicationDirection](#communicationdirection) 필드를 기준으로 합니다.      |
|**SimplifiedDeviceAction**     |   [DeviceAction](#deviceaction) 필드의 원래 값을 유지하면서 [DeviceAction](#deviceaction) 값을 정적 값 세트로 단순화합니다. <br>예:  `Denied` > `Deny`.      |
|**SourceSystem**     | 항상 **OpsManager** 로 정의됩니다.        |
|     |         |

## <a name="next-steps"></a>다음 단계

자세한 내용은 [Common Event Format을 사용하여 외부 솔루션 연결](connect-common-event-format.md)을 참조하세요.

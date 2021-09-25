---
title: ASIM(Azure Sentinel 정보 모델) 콘텐츠 | Microsoft Docs
description: 이 문서에서는 ASIM(Azure Sentinel Information Model)을 활용한 Azure Sentinel 콘텐츠에 대해 간략하게 설명합니다.
services: sentinel
cloud: na
documentationcenter: na
author: oshezaf
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/11/2021
ms.author: ofshezaf
ms.openlocfilehash: deb5377aef61736a14ce8110e96c16e5352096cd
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128552258"
---
# <a name="azure-sentinel-information-model-asim-security-content--public-preview"></a>ASIM(Azure Sentinel 정보 모델) 보안 콘텐츠(공개 미리 보기)

Azure Sentinel 정규화된 보안 콘텐츠에는 분석 규칙, 헌팅 쿼리 및 소스에 관계 없는 정규화 파서와 함께 작동하는 통합 문서가 포함됩니다.

<a name="builtin"></a>Azure Sentinel 갤러리 및[솔루션에서](sentinel-solutions-catalog.md)정규화된 기본 제공 콘텐츠를 찾거나, 정규화된 콘텐츠를 만들거나, 정규화된 데이터를 사용하도록 기존 콘텐츠를 수정할 수 있습니다.

이 문서에서는 ASIM을 지원하도록 구성된 기본 제공 Azure Sentinel 콘텐츠를 나열합니다.  Azure Sentinel GitHub 리포지토리에 대한 링크는 아래에 참조로 제공되는 반면, [ Analytics 규칙 갤러리](detect-threats-built-in.md)에서 이러한 규칙을 찾을 수도 있습니다. 연결된 GitHub 페이지를 사용하여 관련 헌팅 쿼리를 복사합니다.

> [!TIP]
> 또한 [파서 정규화 및 정규화된 콘텐츠 Azure Sentinel 심층 분석 웨비나를](https://www.youtube.com/watch?v=zaqblyjQW6k) 시청하거나 [슬라이드를 검토하세요.](https://1drv.ms/b/s!AnEPjr8tHcNmjGtoRPQ2XYe3wQDz?e=R3dWeM) 자세한 내용은 [다음 단계](#next-steps)를 참조하세요.
>

> [!IMPORTANT]
> ASIM은 현재 미리 보기 상태입니다. [Azure Preview 추가 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타, 미리 보기 또는 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 법률 용어가 포함되어 있습니다.
>

## <a name="authentication-security-content"></a>인증 보안 콘텐츠

ASIM 정규화에는 다음과 같은 기본 제공 인증 콘텐츠가 지원됩니다.

### <a name="analytics-rules"></a>Analytics 규칙

 - [잠재적인 암호 스프레이 공격(인증 정규화 사용)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimAuthentication/imAuthPasswordSpray.yaml)
 - [사용자 자격 증명에 대한 무차별 암호 대입 공격(인증 정규화 사용)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimAuthentication/imAuthBruteForce.yaml)
 - [3시간 이내에 다른 국가의 사용자 로그인(인증 정규화 사용)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimAuthentication/imAuthSigninsMultipleCountries.yaml)
 - [비활성화된 계정에 대한 로그인을 시도하는 IP의 로그인(인증 정규화 사용)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimAuthentication/imSigninAttemptsByIPviaDisabledAccounts.yaml)


## <a name="dns-query-security-content"></a>DNS 쿼리 보안 콘텐츠

다음 기본 제공 DNS 쿼리 콘텐츠는 ASIM 정규화에 대해 지원됩니다.

### <a name="analytics-rules"></a>Analytics 규칙

 - (미리 보기) TI는 도메인 엔터티를 DNS 이벤트에 매핑합니다(정규화된 DNS).
 - (미리 보기) TI는 IP 엔터티를 DNS 이벤트에 매핑합니다(정규화된 DNS).
 - [잠재적 DGA 검색됨(ASimDNS)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimDNS/imDns_HighNXDomainCount_detection.yaml)
  - [과도한 NXDOMAIN DNS 쿼리(정규화된 DNS)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimDNS/imDns_ExcessiveNXDOMAINDNSQueries.yaml)
 - [마이닝 풀과 관련된 DNS 이벤트(정규화된 DNS)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimDNS/imDNS_Miners.yaml)
 - [ToR 프록시와 관련된 DNS 이벤트(정규화된 DNS)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimDNS/imDNS_TorProxies.yaml)
 - [알려진 Barium 도메인](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/BariumDomainIOC112020.yaml)
 - [알려진 Barium IP 주소](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/BariumIPIOC112020.yaml) 
 - [Exchange Server 취약점 공개 2021년 3월 IoC 매치](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ExchangeServerVulnerabilitiesMarch2021IoCs.yaml)
 - [알려진 GALLIUM 도메인 및 해시](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/GalliumIOCs.yaml)
 - [알려진 IRIDIUM IP](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/IridiumIOCs.yaml)
 - [NOBELIUM - 도메인 및 IP IOC - 2021년 3월](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/NOBELIUM_DomainIOCsMarch2021.yaml)
 - [알려진 Phosphorus 그룹 도메인/IP](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/PHOSPHORUSMarch2019IOCs.yaml)
 - [알려진 STRONTIUM 그룹 도메인 - 2019년 7월](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/STRONTIUMJuly2019IOCs.yaml)
 - [Solorigate 네트워크 비콘](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/Solorigate-Network-Beacon.yaml)
 - [DCU 무력화에 포함된 THALLIUM 도메인](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ThalliumIOCs.yaml)
 - [알려진 ZINC Comebacker 및 Klackring 맬웨어 해시](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ZincJan272021IOCs.yaml)

## <a name="file-activity-security-content"></a>파일 활동 보안 콘텐츠

ASIM 정규화에는 다음과 같은 기본 제공 파일 작업 콘텐츠가 지원됩니다.

### <a name="analytic-rules"></a>분석 규칙

- [SUNBURST 및 SUPERNOVA 백도어 해시 (정규화 된 파일 이벤트)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimFileEvent/imFileESolarWindsSunburstSupernova.yaml)
- [Exchange Server 취약성 공개 2021년 3월 IoC 매치](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ExchangeServerVulnerabilitiesMarch2021IoCs.yaml)
- [의심스러운 파일을 작성하는 HAFNIUM UM 서비스](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/HAFNIUMUmServiceSuspiciousFile.yaml)
- [NOBELIUM - 도메인, 해시 및 IP IOC - 2021년 5월](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/NOBELIUM_IOCsMay2021.yaml)
- [SUNSPOT 로그 파일 창조](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/SUNSPOTLogFile.yaml)
- [알려진 ZINC Comebacker 및 Klackring 맬웨어 해시](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/MultipleDataSources/ZincJan272021IOCs.yaml)

## <a name="process-activity-security-content"></a>프로세스 활동 보안 콘텐츠

ASIM 정규화에는 다음과 같은 기본 제공 프로세스 작업 콘텐츠가 지원됩니다.

### <a name="analytics-rules"></a>Analytics 규칙

 - [가능한 AdFind 정찰 도구 사용 (정규화된 프로세스 이벤트)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimProcess/imProcess_AdFind_Usage.yaml)
 - [Base64 인코딩 Windows 프로세스 명령줄(정규화된 프로세스 이벤트)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimProcess/imProcess_base64_encoded_pefile.yaml)
 - [휴지통의 맬웨어(정규화된 프로세스 이벤트)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimProcess/imProcess_malware_in_recyclebin.yaml)
 - [NOBELIUM-vbscript의 의심스러운 rundll32.exe 실행(정규화된 프로세스 이벤트)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimProcess/imProcess_NOBELIUM_SuspiciousRundll32Exec.yaml)
 - [의심스러운 SUNBURST SolarWinds 자식 프로세스 (정규화된 프로세스 이벤트)](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/ASimProcess/imProcess_SolarWinds_SUNBURST_Process-IOCs.yaml)

### <a name="hunting-queries"></a>헌팅 쿼리

 - [Cscript 스크립트 일별 요약 분석 (정규화된 프로세스 이벤트)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_cscript_summary.yaml)
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
 - [Rundll32.exe (LOLBins 및 LOLScripts, 정규화된 프로세스 이벤트)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/inProcess_SignedBinaryProxyExecutionRundll32.yaml)
 - [특수 프로세스-하위 5% (정규화된 프로세스 이벤트)](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/ASimProcess/imProcess_uncommon_processes.yaml)
 - [명령줄에서 유니코드 난독 처리](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/MultipleDataSources/UnicodeObfuscationInCommandLine.yaml)

## <a name="registry-activity-security-content"></a>레지스트리 활동 보안 콘텐츠

다음 기본 제공 레지스트리 활동 콘텐츠는 ASIM 정규화에 대해 지원됩니다.

### <a name="hunting-queries"></a>헌팅 쿼리

- [IFEO 레지스트리 키를 통해 유지](https://github.com/Azure/Azure-Sentinel/blob/master/Hunting%20Queries/MultipleDataSources/PersistViaIFEORegistryKey.yaml)

## <a name="modify-your-content-to-use-normalized-data"></a><a name="modify"></a>정규화된 데이터를 사용하도록 콘텐츠 수정

사용자 지정 콘텐츠가 정규화를 사용하도록 설정하려면 다음을 수행합니다.

- 쿼리와 관련된 소스와 관계없는 파서가 사용하도록 쿼리를 수정합니다.
- 정규화된 스키마 필드 이름을 사용하도록 쿼리의 필드 이름을 수정합니다.
- 해당하는 경우 쿼리에서 필드의 정규화된 값을 사용하도록 조건을 변경합니다.

예를 들어 Infoblox DNS 서버에서 보내는 DNS 이벤트에서 작동하는 역방향 DNS 조회 횟수가 높은 **DNS 분석 규칙으로 관찰되는** 드문 클라이언트를 살펴보겠습니다.

```kusto
let threshold = 200;
InfobloxNIOS
| where ProcessName =~ "named" and Log_Type =~ "client"
| where isnotempty(ResponseCode)
| where ResponseCode =~ "NXDOMAIN"
| summarize count() by Client_IP, bin(TimeGenerated,15m)
| where count_ > threshold
| join kind=inner (InfobloxNIOS
    | where ProcessName =~ "named" and Log_Type =~ "client"
    | where isnotempty(ResponseCode)
    | where ResponseCode =~ "NXDOMAIN"
    ) on Client_IP
| extend timestamp = TimeGenerated, IPCustomEntity = Client_IP
```

다음 코드는 정규화를 사용하여 DNS 쿼리 이벤트를 제공하는 모든 원본에 대해 동일한 검색을 제공하는 소스에 구애받지 않는 버전입니다.

```kusto
imDns(responsecodename='NXDOMAIN')
| summarize count() by SrcIpAddr, bin(TimeGenerated,15m)
| where count_ > threshold
| join kind=inner (imDns(responsecodename='NXDOMAIN')) on SrcIpAddr
| extend timestamp = TimeGenerated, IPCustomEntity = SrcIpAddr```
```

정규화된 소스에 구애받지 않은 버전은 다음과 같은 차이점이 있습니다.

- `imDns`정규화된 파서가 Infoblox 파서 대신 사용됩니다.

- `imDns`는 DNS 쿼리 이벤트만 가져오므로 Infoblox 버전에서 수행한 대로 이벤트 유형을 확인할 필요가 `where ProcessName =~ "named" and Log_Type =~ "client"`없습니다.

- `SrcIpAddr`필드는 대신 `Client_IP` 사용됩니다.
 
- Parser 매개 변수 필터링은 ResponseCodeName에 사용되어 명시적 where 절이 필요하지 않습니다.


정규화된 DNS 원본을 지원하는 것 외에도 정규화된 버전은 더 짧고 이해하기 쉽습니다. 

스키마 또는 파서가 필터링 매개 변수를 지원하지 않는 경우 마지막 매개 변수를 제외하고 변경 내용이 유사합니다. 대신 필터링 조건은 아래와 같이 원래 쿼리에서 유지됩니다.

```kusto
let threshold = 200;
imDns
| where isnotempty(ResponseCodeName)
| where ResponseCodeName =~ "NXDOMAIN"
| summarize count() by SrcIpAddr, bin(TimeGenerated,15m)
| where count_ > threshold
| join kind=inner (imDns
    | where isnotempty(ResponseCodeName)
    | where ResponseCodeName =~ "NXDOMAIN"
    ) on SrcIpAddr
| extend timestamp = TimeGenerated, IPCustomEntity = SrcIpAddr
```

## <a name="next-steps"></a><a name="next-steps"></a>다음 단계

이 문서에서는 ASIM(Azure Sentinel 정보 모델) 콘텐츠에 대해 설명합니다.

자세한 내용은 다음을 참조하세요.

- [파서 정규화 및 정규화된 콘텐츠 Azure Sentinel 심층 분석 웨비나를](https://www.youtube.com/watch?v=zaqblyjQW6k) 시청하거나 [슬라이드를 검토하세요.](https://1drv.ms/b/s!AnEPjr8tHcNmjGtoRPQ2XYe3wQDz?e=R3dWeM)
- [Azure Sentinel 정보 모델 개요](normalization.md)
- [Azure Sentinel 정보 모델 스키마](normalization-about-schemas.md)
- [Azure Sentinel 정보 모델 파서](normalization-about-parsers.md)
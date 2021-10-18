---
title: Microsoft 365 Defender 데이터를 Azure Sentinel에 연결 | Microsoft Docs
description: Microsoft 365 Defender에서 Azure 센티널로 인시던트, 경고 및 원시 이벤트 데이터를 수집하는 방법에 대해 알아봅니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/13/2019
ms.author: yelevin
ms.openlocfilehash: a70e6429ab535222f2ff7b86a807e5fc4a4b2a7a
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/18/2021
ms.locfileid: "130133750"
---
# <a name="connect-data-from-microsoft-365-defender-to-azure-sentinel"></a>Microsoft 365 Defender의 데이터를 Azure Sentinel에 연결

> [!IMPORTANT]
>
> **Microsoft 365 Defender** 는 이전에 **Microsoft Threat Protection** 또는 **MTP** 로 알려져 있었습니다.
>
> **엔드포인트용 Microsoft Defender** 의 이전 이름은 **MDATP**(**Microsoft Defender Advanced Threat Protection**)였습니다.
>
> **microsoft Defender for Office 365** 이전에는 Office 365 **Advanced Threat Protection으로** 알려져 있었습니다.
>
> 이전 이름이 일정 시간 동안 계속 사용되는 것을 볼 수 있습니다.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

## <a name="background"></a>배경

인시던트 통합을 포함한 Azure Sentinel의 [M365D(Microsoft 365 Defender)](/microsoft-365/security/mtp/microsoft-threat-protection) 커넥터를 사용하면 모든 M365D 인시던트 및 경고를 Azure Sentinel로 스트리밍하고 두 포털 간에 인시던트를 동기화된 상태로 유지할 수 있습니다. M365D 인시던트는 모든 경고, 엔터티 및 기타 관련 정보를 포함하며, M365D 구성 요소 서비스 **Microsoft defender For Endpoint**, **Microsoft Defender for Identity**, **Office 365용 Microsoft Defender** 및 **Microsoft Cloud App Security** 에서 경고로 보강되고 그룹화됩니다.

또한 커넥터를 사용하면 엔드포인트용 Microsoft Defender 및 microsoft Defender for Office 365 **고급 헌팅** 이벤트를 Azure Sentinel 스트리밍할 수 있습니다. 이를 통해 이러한 Defender 구성 요소의 고급 헌팅 쿼리를 Azure Sentinel 복사하고, Defender 구성 요소의 원시 이벤트 데이터로 Sentinel 경고를 보강하여 추가 인사이트를 제공하고, Log Analytics에서 보존이 증가된 로그를 저장할 수 있습니다.

인시던트 통합 및 고급 헌팅 이벤트 컬렉션에 대한 자세한 내용은 [Microsoft 365 Defender와 Azure Sentinel 통합](microsoft-365-defender-sentinel-integration.md#advanced-hunting-event-collection)을 참조하세요.

> [!IMPORTANT]
>
> Microsoft 365 Defender connector는 현재 **미리 보기** 로 제공됩니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

## <a name="prerequisites"></a>사전 요구 사항

- [Microsoft 365 defender 사전 요구 사항](/microsoft-365/security/mtp/prerequisites)에 설명된 대로 Microsoft 365 defender에 대한 유효한 라이선스가 있어야 합니다. 

- Azure Active Directory의 **전역 관리자** 또는 **보안 관리자** 여야 합니다.

## <a name="connect-to-microsoft-365-defender"></a>Microsoft 365 Defender에 연결

1. Azure Sentinel에서 **데이터 커넥터** 를 선택하고 갤러리에서 **Microsoft 365 Defender(미리 보기)** 를 선택한 다음 **커넥터 열기 페이지** 를 선택합니다.

1. **인시던트 및 경고에 연결** 섹션의 **구성** 에서 **인시던트 및 경고** 단추를 선택합니다.

1. 인시던트가 중복되지 않게 하려면 **해당 제품에 대한 모든 Microsoft 인시던트 생성 규칙을 해제** 하라는 확인란에 표시하는 것이 좋습니다.

    > [!NOTE]
    > Microsoft 365 Defender 커넥터를 사용하도록 설정하면 모든 M365D 구성 요소 커넥터(이 문서의 시작 부분에서 언급된 커넥터)가 백그라운드에서 자동으로 연결됩니다. 구성 요소 커넥터 중 하나의 연결을 끊으려면 먼저 Microsoft 365 Defender 커넥터와의 연결을 끊어야 합니다.

1. Microsoft 365 Defender 인시던트 데이터를 쿼리하려면 쿼리 창에서 다음 명령문을 사용합니다.
    ```kusto
    SecurityIncident
    | where ProviderName == "Microsoft 365 Defender"
    ```

1. 엔드포인트용 Microsoft Defender 또는 Office 365 Microsoft Defender에서 고급 헌팅 이벤트를 수집하려는 경우 해당 고급 헌팅 테이블에서 다음 유형의 이벤트를 수집할 수 있습니다.

    1. 수집하려는 이벤트 유형으로 표의 확인란을 표시합니다.

       # <a name="defender-for-endpoint"></a>[Defender for Endpoint](#tab/MDE)

       | 테이블 이름 | 이벤트 유형 |
       |-|-|
       | **[DeviceInfo](/microsoft-365/security/defender/advanced-hunting-deviceinfo-table)** | OS 정보를 포함한 컴퓨터 정보 |
       | **[DeviceNetworkInfo](/microsoft-365/security/defender/advanced-hunting-devicenetworkinfo-table)** | 실제 어댑터, IP 및 MAC 주소뿐만 아니라 연결된 네트워크 및 도메인을 비롯한 디바이스의 네트워크 속성 |
       | **[DeviceProcessEvents](/microsoft-365/security/defender/advanced-hunting-deviceprocessevents-table)** | 프로세스 생성 및 관련 이벤트 |
       | **[DeviceNetworkEvents](/microsoft-365/security/defender/advanced-hunting-devicenetworkevents-table)** | 네트워크 연결 및 관련 이벤트 |
       | **[DeviceFileEvents](/microsoft-365/security/defender/advanced-hunting-devicefileevents-table)** | 파일 생성, 수정, 기타 파일 시스템 이벤트 |
       | **[DeviceRegistryEvents](/microsoft-365/security/defender/advanced-hunting-deviceregistryevents-table)** | 레지스트리 항목 생성 및 수정 |
       | **[DeviceLogonEvents](/microsoft-365/security/defender/advanced-hunting-devicelogonevents-table)** | 디바이스의 로그인 및 기타 인증 이벤트 |
       | **[DeviceImageLoadEvents](/microsoft-365/security/defender/advanced-hunting-deviceimageloadevents-table)** | DLL 로드 이벤트 |
       | **[DeviceEvents](/microsoft-365/security/defender/advanced-hunting-deviceevents-table)** | Windows Defender 바이러스 백신 및 악용 방지와 같은 보안 제어에 의해 트리거되는 이벤트를 비롯한 여러 이벤트 유형 |
       | **[DeviceFileCertificateInfo](/microsoft-365/security/defender/advanced-hunting-DeviceFileCertificateInfo-table)** | 엔드포인트의 인증서 확인 이벤트에서 가져온 서명된 파일의 인증서 정보 |
       |

       # <a name="defender-for-office-365"></a>[Defender for Office 365](#tab/MDO)

       | 테이블 이름 | 이벤트 유형 |
       |-|-|
       | **[EmailAttachmentInfo](/microsoft-365/security/defender/advanced-hunting-emailattachmentinfo-table)** | 메일에 첨부된 파일에 대한 정보 |
       | **[EmailEvents](/microsoft-365/security/defender/advanced-hunting-emailevents-table)** | 메일 배달 및 차단 이벤트를 포함한 메일 이벤트 Microsoft 365 |
       | **[EmailPostDeliveryEvents](/microsoft-365/security/defender/advanced-hunting-emailpostdeliveryevents-table)** | Microsoft 365 받는 사람 사서함에 메일을 배달한 후 배달 후 발생하는 보안 이벤트 |
       | **[EmailUrlInfo](/microsoft-365/security/defender/advanced-hunting-emailurlinfo-table)** | 전자 메일의 URL에 대한 정보 |
       |

       ---

    1. **변경 내용 적용** 을 클릭합니다.

    1. Log Analytics에서 고급 헌팅 테이블을 쿼리하려면 쿼리 창에서 위의 목록에 있는 표 이름을 입력합니다.

## <a name="verify-data-ingestion"></a>데이터 수집 확인

커넥터 페이지의 데이터 그래프는 현재 데이터를 수집하고 있음을 나타냅니다. 인시던트, 경고 및 이벤트마다 한 줄씩 표시되며, 이벤트 줄은 사용하도록 설정된 모든 테이블에 있는 이벤트 볼륨의 집계입니다. 커넥터를 사용하도록 설정했으면 다음 KQL 쿼리를 사용하여 보다 구체적인 그래프를 생성할 수 있습니다.

수신되는 Microsoft 365 Defender 인시던트의 그래프에 대해 다음 KQL 쿼리를 사용합니다.

```kusto
let Now = now(); 
(range TimeGenerated from ago(14d) to Now-1d step 1d 
| extend Count = 0 
| union isfuzzy=true ( 
    SecurityIncident
    | where ProviderName == "Microsoft 365 Defender"
    | summarize Count = count() by bin_at(TimeGenerated, 1d, Now) 
) 
| summarize Count=max(Count) by bin_at(TimeGenerated, 1d, Now) 
| sort by TimeGenerated 
| project Value = iff(isnull(Count), 0, Count), Time = TimeGenerated, Legend = "Events") 
| render timechart 
```

다음 KQL 쿼리를 사용하여 단일 테이블에 대한 이벤트 볼륨 그래프를 생성할 수 있습니다(*DeviceEvents* 테이블을 선택한 필수 테이블로 변경).

```kusto
let Now = now();
(range TimeGenerated from ago(14d) to Now-1d step 1d
| extend Count = 0
| union isfuzzy=true (
    DeviceEvents
    | summarize Count = count() by bin_at(TimeGenerated, 1d, Now)
)
| summarize Count=max(Count) by bin_at(TimeGenerated, 1d, Now)
| sort by TimeGenerated
| project Value = iff(isnull(Count), 0, Count), Time = TimeGenerated, Legend = "Events")
| render timechart
```

**다음 단계** 탭에서는 포함되어 있는 몇 가지 유용한 통합 문서, 샘플 쿼리 및 분석 규칙 템플릿을 확인할 수 있습니다. 이러한 항목을 즉시 실행하거나 수정하고 저장할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Microsoft 365 Defender 커넥터를 사용하여 엔드포인트용 Microsoft Defender 및 Defender for Office 365 Azure Sentinel Microsoft 365 Defender 인시던트 및 고급 헌팅 이벤트 데이터를 통합하는 방법을 배웠습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [데이터에 대한 가시성을 얻고 재적 위협을 확인](get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](./detect-threats-built-in.md)을 시작합니다.

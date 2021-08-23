---
title: Trend Micro TippingPoint를 Azure Sentinel에 연결 | Microsoft Docs
description: Trend Micro TippingPoint 데이터 커넥터를 사용하여 TippingPoint SMS 로그를 Azure Sentinel로 끌어오는 방법에 대해 알아봅니다. 통합 문서에서 TippingPoint 데이터를 보고, 경고를 만들고, 조사를 개선합니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/12/2021
ms.author: yelevin
ms.openlocfilehash: d59637f446b70b0c4de253edc96b2b3f2921d387
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122567843"
---
# <a name="connect-your-trend-micro-tippingpoint-solution-to-azure-sentinel"></a>Trend Micro TippingPoint 솔루션을 Azure Sentinel에 연결

> [!IMPORTANT]
> Trend Micro TippingPoint 커넥터는 현재 **미리 보기** 상태입니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

이 문서에서는 Trend Micro TippingPoint Threat Protection 시스템 솔루션을 Azure Sentinel에 연결하는 방법을 설명합니다. Trend Micro TippingPoint 데이터 커넥터를 사용하면 TippingPoint SMS(보안 관리 시스템) 로그를 Azure Sentinel에 쉽게 연결하여 통합 문서에서 데이터를 보고, 사용자 지정 경고를 만들고 통합하여 조사를 개선할 수 있습니다.

> [!NOTE]
> 데이터는 Azure Sentinel을 실행하는 작업 영역의 지리적 위치에 저장됩니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure Sentinel 작업 영역에 대한 읽기 및 쓰기 권한이 있어야 합니다.

- 작업 영역 공유 키에 대한 읽기 권한이 있어야 합니다.

## <a name="send-trend-micro-tippingpoint-logs-to-azure-sentinel"></a>Trend Micro TippingPoint 로그를 Azure Sentinel로 보내기

Azure Sentinel로 로그를 가져오려면 TippingPoint TPS 솔루션이 CEF 형식의 Syslog 메시지를 Linux 기반 로그 전달 서버(rsyslog 또는 syslog-ng 실행)로 전송하도록 구성합니다. 이 서버에는 Log Analytics 에이전트가 설치되어 있으며, 에이전트는 Azure Sentinel 작업 영역에 로그를 전달합니다. 커넥터는 파서 함수를 사용하여 수신하는 데이터를 정규화된 스키마로 변환합니다. 

1. Azure Sentinel 탐색 메뉴에서 **데이터 커넥터** 를 선택합니다.

1. **데이터 커넥터** 갤러리에서 **Trend Micro TippingPoint(미리 보기)** , **커넥터 페이지 열기** 를 차례로 선택합니다.

1. 다음과 같이 **구성** 에 있는 **지침** 탭에서 지침에 따라 수행합니다.

    1. **1. Linux Syslog 에이전트 구성** - 실행 중인 로그 전달자가 아직 없는 경우 또는 다른 로그 전달자가 필요한 경우 이 단계를 수행합니다. 자세한 지침 및 설명은 Azure Sentinel 설명서에서 [1단계: 로그 전달자 배포](connect-cef-agent.md)를 참조하세요.

    1. **2. Trend Micro TippingPoint SMS 로그를 Syslog 에이전트로 전달** - 이 구성은 다음 요소를 포함해야 합니다.
        - 로그 대상 – 로그 전달 서버의 호스트 이름 및/또는 IP 주소
        - 프로토콜 및 포트 – **TCP 514**(다른 프로토콜 및 포트가 권장되는 경우 로그 전달 서버의 syslog 디먼에서 병렬 변경을 수행해야 함)
        - 로그 형식 - **ArcSight CEF Format v4.2**
        - 로그 유형 - 모두 사용 가능

    1. **3. 연결 유효성 검사** - 커넥터 페이지에서 명령을 복사하고 로그 전달자에서 실행하여 데이터 수집을 확인합니다. 자세한 지침 및 설명은 Azure Sentinel 설명서에서 [3단계: 연결 유효성 검사](connect-cef-verify.md)를 참조하세요.

        로그가 Log Analytics에 표시될 때까지 최대 20분 정도 소요될 수 있습니다.

## <a name="find-your-data"></a>데이터 찾기

연결이 설정된 후 *CommonSecurityLog* 테이블에서 **Azure Sentinel** 섹션의 **로그** 에 데이터가 표시됩니다.

Log Analytics에 Trend Micro TippingPoint 데이터를 가져오려면 테이블 대신 파서 함수를 쿼리합니다. 다음을 쿼리 창에 복사하여 선택한 대로 다른 필터를 적용합니다.

```kusto
TrendMicroTippingPoint
| sort by TimeGenerated
```

더 많은 쿼리 샘플은 커넥터 페이지에서 **다음 단계** 탭을 참조하세요.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Trend Micro TippingPoint를 Azure Sentinel에 연결하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [데이터에 대한 가시성을 얻고 재적 위협을 확인](get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](monitor-your-data.md)하여 데이터를 모니터링합니다.
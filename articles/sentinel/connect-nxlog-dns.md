---
title: NXLog Windows DNS 로그 데이터를 Azure Sentinel에 연결 | Microsoft Docs
description: NXLog DNS Logs 데이터 커넥터를 사용하여 Windows DNS 이벤트를 Azure Sentinel에 끌어오는 방법을 알아보세요. 통합 문서에서 Windows DNS 데이터를 보고, 경고를 생성하고, 조사 과정을 개선합니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/02/2021
ms.author: yelevin
ms.openlocfilehash: b80141d79807aea89e328b166d419e583a646ad5
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122530451"
---
# <a name="connect-your-nxlog-windows-dns-logs-to-azure-sentinel"></a>NXLog Windows DNS 로그를 Azure Sentinel에 연결

> [!IMPORTANT]
> NXLog DNS 로그 커넥터는 현재 **미리 보기** 중입니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

[NXLog DNS 로그](https://nxlog.co/documentation/nxlog-user-guide/windows-dns-server.html) 커넥터를 사용하면 실시간으로 모든 Windows DNS Server 이벤트를 Azure Sentinel에 간편하게 내보내, 전체 조직에 걸친 도메인 이름 서버 작업을 파악할 수 있습니다. 실시간으로 감사 및 분석 DNS 서버 이벤트를 모두 수집하기 위해 고성능 [ETW(Windows용 이벤트 추적)](https://nxlog.co/documentation/nxlog-user-guide/windows-dns-server.html#dns_windows_etw)를 사용하고 사용자 정의 필드로 이벤트 보강을 지원합니다. NXLog DNS 로그와 Azure Sentinel 간의 통합은 REST API를 통하여 촉진됩니다.

> [!NOTE]
> 데이터는 Azure Sentinel을 실행하는 작업 영역의 지리적 위치에 저장됩니다.

## <a name="configure-and-connect-nxlog-dns-logs"></a>NXLog DNS 로그 구성 및 연결하기

NXLog를 구성하여 이벤트를 Azure Sentinel에 JSON 형식으로 바로 보내도록 할 수 있습니다.

1. Azure Sentinel 포털에서, **데이터 커넥터** 를 클릭한 뒤 **NXLog DNS 로그** 커넥터를 선택합니다.

1. **커넥터 페이지 열기** 를 선택합니다.

1. *NXLog 사용자 가이드* 통합 주제 [Microsoft Azure Sentinel](https://nxlog.co/documentation/nxlog-user-guide/sentinel.html)의 단계별 지침을 따라 REST API를 통한 착신 전환을 구성합니다.

## <a name="find-your-data"></a>데이터 찾기

연결이 설정되면 데이터는 *DNS_Logs_CL* 테이블의 **사용자 지정 로그** 섹션에 있는 **로그** 에 표시됩니다.

## <a name="validate-connectivity"></a>연결 유효성 검사

로그가 Log Analytics에 표시될 때까지 최대 20분가량 소요될 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 NXLog를 사용하여 Windows DNS 로그를 Azure Sentinel에 수집하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [데이터 및 잠재적 위협에 대한 가시성을 확보](get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](monitor-your-data.md)하여 데이터를 모니터링합니다.
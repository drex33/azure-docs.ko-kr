---
title: Azure Sentinel에 Juniper Networks SRX 데이터 연결 | Microsoft Docs
description: Juniper SRX 데이터 커넥터를 사용하여 Juniper SRX 로그를 Azure Sentinel로 끌어오는 방법 알아보기. 통합 문서에서 Juniper SRX 데이터를 보고, 경고를 만들어, 조사 기능을 개선합니다.
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
ms.date: 01/17/2021
ms.author: yelevin
ms.openlocfilehash: 39df2be86f4ca1d4410396b86aabee9a749ade92
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/17/2021
ms.locfileid: "122567844"
---
# <a name="connect-your-juniper-srx-firewall-to-azure-sentinel"></a>Azure Sentinel에 Juniper SRX 방화벽 연결하기

> [!IMPORTANT]
> Juniper SRX 커넥터는 현재 **미리 보기** 로 제공됩니다. 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관은 [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

이 문서에서는 Azure Sentinel에 Juniper SRX 방화벽 어플라이언스를 연결하는 방법을 설명합니다. Juniper SRX 데이터 커넥터를 사용하면 SRX 로그를 Azure Sentinel에 쉽게 연결할 수 있으므로, 통합 문서에서 데이터를 보고, 해당 데이터를 사용하여 사용자 지정 경고를 만들어, 조사 기능 개선을 위한 데이터 통합이 가능합니다. Juniper SRX와 Azure Sentinel 간의 통합은 Syslog를 사용합니다.

> [!NOTE]
> 데이터는 Azure Sentinel을 실행하는 작업 영역이 있는 지리적 위치에 저장됩니다.

## <a name="prerequisites"></a>필수 구성 요소

- Azure Sentinel 작업 영역에 대한 읽기 및 쓰기 권한이 있어야 합니다.

- Juniper SRX 솔루션은 Syslog를 통해 로그를 내보내도록 구성해야 합니다.

## <a name="forward-juniper-srx-logs-to-the-syslog-agent"></a>Syslog 에이전트에 Juniper SRX 로그 전달하기  

Syslog 에이전트를 통해 Azure Sentinel 작업 영역에 Syslog 메시지를 전달하도록 Juniper SRX를 구성합니다.

1. Azure Sentinel 탐색 메뉴에서 **데이터 커넥터** 를 선택합니다.

1. **데이터 커넥터** 갤러리에서 **Juniper SRX(미리 보기)** 커넥터를 선택한 다음 **커넥터 페이지를 엽니다**.

1. **Juniper SRX** 커넥터 페이지의 다음 지시 사항을 따릅니다.

    1. Linux용 에이전트 설치 및 온보딩

        - Azure Linux VM 또는 비 Azure Linux 컴퓨터(실제 또는 가상)를 선택합니다.

    1. 수집할 로그 구성

        - 작업 영역 에이전트 구성에서 시설 및 심각도를 선택합니다.

    1. Juniper SRX 구성 및 연결하기

        - 다음 지침에 따라 syslog를 전달하도록 Juniper SRX를 구성합니다.
            - [트래픽 로그(보안 정책 로그)](https://kb.juniper.net/InfoCenter/index?page=content&id=KB16509&actp=METADATA)
            - [시스템 로그](https://kb.juniper.net/InfoCenter/index?page=content&id=kb16502)
        - 원격 서버에 대해 Linux 에이전트를 설치한 Linux 머신의 IP 주소를 사용합니다.

## <a name="find-your-data"></a>데이터 찾기

연결이 성공적으로 설정되면 데이터는 Syslog에 있는 Log Analytics에 표시됩니다.

몇 가지 유용한 샘플 쿼리를 보려면 커넥터 페이지에서 **다음 단계** 탭을 참조하세요.

## <a name="validate-connectivity"></a>연결 유효성 검사

로그가 Log Analytics에 표시될 때까지 최대 20분가량 소요될 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 Juniper SRX를 Azure Sentinel에 연결하는 방법을 알아보았습니다. Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [데이터에 대한 가시성을 얻고 재적 위협을 확인](get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](monitor-your-data.md)하여 데이터를 모니터링합니다.
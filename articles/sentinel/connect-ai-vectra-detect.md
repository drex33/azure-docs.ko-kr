---
title: AI Vectra Detect 데이터를 Azure Sentinel에 연결 | Microsoft Docs
description: AI Vectra Detect 데이터를 Azure Sentinel에 연결하는 방법을 알아봅니다.
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
ms.date: 06/21/2020
ms.author: yelevin
ms.openlocfilehash: f74ce6cfd3365c09d42a20af959739ce947eeba3
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122689123"
---
# <a name="connect-ai-vectra-detect-to-azure-sentinel"></a>Azure Sentinel에 AI Vectra Detect 연결

> [!IMPORTANT]
> Azure Sentinel의 AI Vectra Detect 데이터 커넥터는 현재 공개 미리 보기로 제공됩니다.
> 해당 기능은 별도의 서비스 수준 계약 없이 제공되며, 프로덕션 작업에는 사용하지 않는 것이 좋습니다. 특정 기능이 지원되지 않거나 기능이 제한될 수 있습니다. 자세한 내용은 [Microsoft Azure Preview에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)을 참조하세요.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

이 문서에서는 [AI Vectra Detect](https://www.vectra.ai/product/cognito-detect) 어플라이언스를 Azure Sentinel에 연결하는 방법을 설명합니다. AI Vectra Detect 데이터 커넥터를 사용하면 AI Vectra Detect 데이터를 Azure Sentinel로 쉽게 가져올 수 있으므로 통합 문서에서 보고, 이를 사용하여 사용자 지정 경고를 만들고 통합하여 조사를 개선할 수 있습니다.

> [!NOTE]
> 데이터는 Azure Sentinel을 실행하는 작업 영역의 지리적 위치에 저장됩니다.

## <a name="configure-your-ai-vectra-detect-appliance-to-send-cef-messages"></a>CEF 메시지를 보내도록 AI Vectra Detect 어플라이언스 구성  

AI Vectra Detect 구성 [1 단계: 로그 전달자 배포](connect-cef-agent.md)에서 설정한 Syslog 전달자를 통해 CEF 형식 syslog 메시지를 Log Analytics 작업 영역으로 전달합니다.

1. Vectra 인터페이스에서 설정 > 알림으로 이동하고 Syslog 구성 편집을 선택합니다. 아래 지침에 따라 연결을 설정합니다,

    - 새 대상 추가([로그 전달자](connect-cef-agent.md)의 호스트 이름)
    - 포트를 **514** 로 설정
    - 프로토콜을 **UDP** 로 설정
    - 형식을 **CEF** 로 설정
    - 로그 형식 설정(사용할 수 있는 모든 로그 형식 선택)
    - 설정 메뉴에서 **저장**

2. **테스트** 단추를 클릭하여 일부 테스트 이벤트를 로그 전달자에게 강제로 보낼 수 있습니다.

3. AI Vectra Detect 이벤트에 대한 로그 분석에서 관련 스키마를 사용하려면 **CommonSecurityLog** 를 검색합니다.

4. [CEF 연결 유효성 검사](troubleshooting-cef-syslog.md#validate-cef-connectivity)로 계속 진행합니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 AI Vectra Detect 어플라이언스를 Azure Sentinel에 연결하는 방법을 알아보았습니다. 이 데이터 커넥터에 기본 제공되는 기능을 최대한 활용하려면 데이터 커넥터 페이지에서 **다음 단계** 탭을 클릭합니다. 여기에서 유용한 정보를 찾을 수 있도록 몇 가지 미리 만들어진 샘플 쿼리를 찾을 수 있습니다.

Azure Sentinel에 대한 자세한 내용은 다음 문서를 참조하세요.

- [데이터에 대한 가시성을 얻고 재적 위협을 확인](get-visibility.md)하는 방법을 알아봅니다.
- [Azure Sentinel을 사용하여 위협 검색](detect-threats-built-in.md)을 시작합니다.
- [통합 문서를 사용](monitor-your-data.md)하여 데이터를 모니터링합니다.
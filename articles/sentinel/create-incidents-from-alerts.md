---
title: Microsoft 센티널의 경고에서 인시던트 만들기 | Microsoft Docs
description: Microsoft 센티널의 경고에서 인시던트를 만드는 방법에 대해 알아봅니다.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc, ignite-fall-2021
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.openlocfilehash: 8f68294e2b1ab473e17552e708bf82a1bad6d8a5
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2021
ms.locfileid: "132523642"
---
# <a name="automatically-create-incidents-from-microsoft-security-alerts"></a>Microsoft 보안 경고에서 인시던트 자동 생성

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft 센티널에 연결 된 microsoft 보안 솔루션에서 트리거된 경고 (예: 클라우드 앱 용 Microsoft Defender 및 Id 용 Microsoft Defender (이전 Azure ATP))는 Microsoft 센티널에서 인시던트를 자동으로 만들지 않습니다. 기본적으로 microsoft 사용자를 microsoft에 연결 하는 경우 해당 서비스에서 생성 되는 모든 경고는 microsoft 센티널의 microsoft 센티널 작업 영역에 있는 보안 경고 표에 원시 데이터로 저장 됩니다. 그런 다음 Microsoft 센티널에 연결 하는 다른 원시 데이터와 같은 데이터를 사용할 수 있습니다.

이 문서의 지침에 따라 연결 된 Microsoft 보안 솔루션에서 경고가 트리거될 때마다 자동으로 인시던트를 만들도록 Microsoft 센티널을 쉽게 구성할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소

보안 서비스 경고에서 인시던트가 생성되려면 [Microsoft 보안 솔루션을 연결](connect-data-sources.md#data-connection-methods)해야 합니다.

## <a name="using-microsoft-security-incident-creation-analytics-rules"></a>Microsoft 보안 인시던트 생성 분석 규칙 사용

Microsoft 센티널에서 제공 되는 기본 제공 규칙을 사용 하 여 microsoft 센티널 인시던트를 실시간으로 자동으로 만들어야 하는 연결 된 Microsoft 보안 솔루션을 선택할 수 있습니다. 규칙을 편집 하 여 microsoft 보안 솔루션에 의해 생성 된 경고 중 Microsoft 센티널에서 인시던트를 만들어야 하는 경고를 필터링 하는 보다 구체적인 옵션을 정의할 수도 있습니다. 예를 들어 심각도가 높은 클라우드 용 Microsoft Defender (이전의 Azure Security Center) 경고만에서 자동으로 Microsoft 센티널 인시던트를 만들도록 선택할 수 있습니다.

1. Microsoft 센티널의 Azure Portal에서 **Analytics** 를 선택 합니다.

1. **규칙 템플릿** 탭을 선택하면 기본 제공되는 분석 규칙을 모두 볼 수 있습니다.

    ![규칙 템플릿](media/incidents-from-alerts/rule-templates.png)

1. 사용할 **Microsoft 보안** 분석 규칙 템플릿을 선택하고 **규칙 만들기** 를 선택합니다.

    ![보안 분석 규칙](media/incidents-from-alerts/security-analytics-rule.png)

1. 규칙 세부 정보를 수정하고 경고 이름에 포함된 텍스트 또는 경고 심각도별로 인시던트를 생성하는 경고를 필터링하도록 선택할 수 있습니다.  
      
    예를 들어 microsoft **security service** 필드에서 **클라우드 용 microsoft** *defender를* 선택 하 고 **심각도 별 필터링** 필드에서 **높음** 을 선택 하는 경우 심각도가 높은 보안 경고만 microsoft 센티널에서 인시던트를 자동으로 만듭니다.  

    ![규칙 만들기 마법사](media/incidents-from-alerts/create-rule-wizard.png)

1. **+만들기** 를 클릭하고 **Microsoft 인시던트 생성 규칙** 을 선택하여 여러 **Microsoft 보안** 서비스의 경고를 필터링하는 새로운 Microsoft 보안 규칙을 만들 수도 있습니다.

    ![인시던트 생성 규칙](media/incidents-from-alerts/incident-creation-rule.png)

    **Microsoft 보안 서비스** 유형별로 둘 이상의 **Microsoft Security** 분석 규칙을 만들 수 있습니다. 각 규칙이 필터로 사용되기 때문에 중복 인시던트는 생성되지 않습니다. 경고가 둘 이상의 **Microsoft 보안** 분석 규칙과 일치 하는 경우에도 하나의 microsoft 센티널 인시던트를 만듭니다.

## <a name="enable-incident-generation-automatically-during-connection"></a>연결 중에 인시던트 자동 생성이 가능하도록 설정

Microsoft 보안 솔루션을 연결할 때 보안 솔루션의 경고가 자동으로 Microsoft 센티널에서 인시던트를 자동으로 생성할지 여부를 선택할 수 있습니다.

1. Microsoft 보안 솔루션 데이터 원본을 연결합니다. 

   ![보안 인시던트 생성](media/incidents-from-alerts/generate-security-incidents.png)

1. **인시던트 만들기** 에서 **사용** 을 선택하여 연결된 보안 서비스에서 생성된 경고에서 인시던트가 자동으로 생성되는 기본 분석 규칙을 사용하도록 설정합니다. 그런 다음, **Analytics** 및 **활성 규칙** 에서 이 규칙을 편집할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- Microsoft 센티널을 시작 하려면 Microsoft Azure 구독이 필요 합니다. 구독이 없는 경우 [무료 평가판](https://azure.microsoft.com/free/)을 등록할 수 있습니다.
- [Microsoft 센티널에 데이터](quickstart-onboard.md)를 등록 하 고 [데이터 및 잠재적 위협을 파악](get-visibility.md)하는 방법을 알아봅니다.

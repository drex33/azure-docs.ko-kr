---
title: 서버 매개 변수 구성 - Azure Portal - Azure Database for PostgreSQL - 유연한 서버
description: 이 문서에서는 Azure Portal을 통해 Azure Database for PostgreSQL - 유연한 서버에서 Postgres 매개 변수를 구성하는 방법을 설명합니다.
author: gennadNY
ms.author: gennadyk
ms.service: postgresql
ms.topic: how-to
ms.date: 11/30/2021
ms.openlocfilehash: f39e440d40ef2c70a9f243dbbd1d405dda1e3c42
ms.sourcegitcommit: dcf3424d7149fceaea0340eb0657baa2c27882a5
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2021
ms.locfileid: "133267587"
---
# <a name="configure-server-parameters-in-azure-database-for-postgresql---flexible-server-via-the-azure-portal"></a>Azure Portal을 통해 Azure Database for PostgreSQL - 유연한 서버에서 서버 매개 변수 구성 
Azure Portal을 통해 Azure Database for PostgreSQL에 대한 구성 매개 변수를 나열하고, 표시하며, 업데이트할 수 있습니다.

## <a name="prerequisites"></a>필수 구성 요소
이 방법 가이드를 단계별로 실행하려면 다음이 필요합니다.
- [Azure Database for PostgreSQL – 유연한 서버](quickstart-create-server-portal.md)

## <a name="viewing-and-editing-parameters"></a>매개 변수 보기 및 편집
1. [Azure Portal](https://portal.azure.com)을 엽니다.

2. Azure Database for PostgreSQL 서버를 선택합니다.

3. **설정** 섹션 아래에서 **서버 매개 변수** 를 선택합니다. 페이지에서는 매개 변수, 해당 값 및 설명 목록을 표시합니다.
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/3-overview-of-parameters.png" alt-text="매개 변수의 개요 페이지":::

4. **드롭다운** 단추를 선택하여 client_min_messages와 같이 열거 형식 매개 변수에 사용할 수 있는 값을 확인합니다.
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/4-enum-drop-down.png" alt-text="드롭다운 열거":::

5. **i**(정보) 단추를 선택하거나 위로 마우스를 가져가서 cpu_index_tuple_cost와 같은 숫자 매개 변수에 사용할 수 있는 값의 범위를 확인합니다.
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/4-information-button.png" alt-text="정보 단추":::

6. 필요한 경우 **검색 상자** 를 사용하여 특정 매개 변수로 범위를 좁힙니다. 매개 변수의 이름 및 설명으로 검색할 수 있습니다.
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/5-search.png" alt-text="검색 결과":::

7. 조정하려는 매개 변수 값을 변경합니다. 세션에서 변경할 내용은 모두 자주색으로 강조 표시되어 있습니다. 값을 변경하면 **저장** 을 선택할 수 있습니다. 또는 변경 사항을 **취소** 할 수 있습니다.
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/6-save-and-discard-buttons.png" alt-text="변경 내용 저장 또는 제거":::

8. 매개 변수에 새 값을 저장한 경우 언제든지 **모두 기본값으로 다시 설정** 을 선택하여 모든 항목을 기본값으로 되돌릴 수있습니다.
:::image type="content" source="./media/howto-configure-server-parameters-in-portal/7-reset-to-default-button.png" alt-text="모두 기본값으로 다시 설정":::

## <a name="working-with-time-zone-parameters"></a>표준 시간대 매개 변수 작업
PostgreSQL에서 날짜 및 시간 데이터로 작업할 계획이라면 해당 위치에 올바른 표준 시간대를 설정했는지 확인해야 합니다. 모든 표준 시간대 인식 날짜 및 시간은 UTC의 Postgres에 내부적으로 저장됩니다. 클라이언트에 표시되기 전에 **TimeZone** 서버 매개 변수로 지정된 영역의 현지 시간으로 변환됩니다.  이 매개 변수는 위에서 설명한 대로 **서버 매개 변수** 페이지에서 편집할 수 있습니다. PostgreSQL을 사용하면 세 가지 다른 형식으로 표준 시간대를 지정할 수 있습니다.
1. 전체 표준 시간대 이름(예: America/New_York). 인식된 표준 시간대 이름은 [**pg_timezone_names**](https://www.postgresql.org/docs/9.2/view-pg-timezone-names.html) 뷰에 나열됩니다.  
   psql에서 이 뷰를 쿼리하고 표준 시간대 이름 목록을 가져오는 예:
   <pre>select name FROM pg_timezone_names LIMIT 20;</pre>

   다음과 같은 결과 집합이 표시되어야 합니다.

   <pre>
            name
        -----------------------
        GMT0
        Iceland
        Factory
        NZ-CHAT
        America/Panama
        America/Fort_Nelson
        America/Pangnirtung
        America/Belem
        America/Coral_Harbour
        America/Guayaquil
        America/Marigot
        America/Barbados
        America/Porto_Velho
        America/Bogota
        America/Menominee
        America/Martinique
        America/Asuncion
        America/Toronto
        America/Tortola
        America/Managua
        (20 rows)
    </pre>
   
2. 표준 시간대 약어(예: PST)입니다. 이러한 사양은 일광 절약 시간제 전환 날짜 규칙 집합도 암시할 수 있는 전체 표준 시간대 이름과 달리 UTC에서 특정 오프셋을 정의합니다. 인식된 약어는 [**pg_timezone_abbrevs 뷰**](https://www.postgresql.org/docs/9.4/view-pg-timezone-abbrevs.html)에 나열되어 있습니다. psql에서 이 뷰를 쿼리하고 표준 시간대 약어 목록을 가져오는 예는 다음과 같습니다.

   <pre> select abbrev from pg_timezone_abbrevs limit 20;</pre>

    다음과 같은 결과 집합이 표시되어야 합니다.

     <pre>
        abbrev|
        ------+
        ACDT  |
        ACSST |
        ACST  |
        ACT   |
        ACWST |
        ADT   |
        AEDT  |
        AESST |
        AEST  |
        AFT   |
        AKDT  |
        AKST  |
        ALMST |
        ALMT  |
        AMST  |
        AMT   |
        ANAST |
        ANAT  |
        ARST  |
        ART   |
    </pre>

3. 표준 시간대 이름 및 약어 외에도 PostgreSQL은 STDoffset 또는 STDoffsetDST 형식의 POSIX 스타일 표준 시간대 사양을 허용합니다. 여기서 STD는 영역 약어이고 offset은 UTC에서 서쪽으로 시간 단위의 숫자 오프셋이며 DST는 선택적 일광 절약 시간제 영역 약어이며 지정된 오프셋보다 한 시간 앞서 있다고 가정합니다. 
   

## <a name="next-steps"></a>다음 단계
자세한 정보:
- [Azure Database for PostgreSQL에서 서버 매개 변수 개요](concepts-server-parameters.md)
- [CLI를 통해 Azure Database for PostgreSQL - 유연한 서버 매개 변수 구성](howto-configure-server-parameters-using-cli.md)
  
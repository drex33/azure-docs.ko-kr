---
title: Azure 기밀 원장 아키텍처
description: Azure 기밀 원장 아키텍처
services: confidential-ledger
author: msmbaldwin
ms.service: confidential-ledger
ms.topic: overview
ms.date: 04/15/2021
ms.author: mbaldwin
ms.openlocfilehash: 855ad26422eb91c5c971d4525d419e5cee8b6606
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131476417"
---
# <a name="architecture"></a>아키텍처

REST API 서비스인 Azure 기밀 원장을 사용하면 사용자가 관리 및 기능 API 호출을 통해 원장과 상호 작용할 수 있습니다.  데이터가 원장에 기록되면 해당 데이터는 안전한 enclaved 백업 복제본인 권한 있는 블록체인 노드로 전송됩니다. 복제본은 합의 개념을 따릅니다. 사용자는 원장에 커밋된 데이터에 대한 수신 확인도 검색할 수 있습니다.

향후 다자간 협업을 지원할 선택적 컨소시엄 개념도 있습니다.

## <a name="architecture-diagram"></a>아키텍처 다이어그램

이 이미지는 Azure 기밀 원장의 아키텍처 개요를 제공하고, 만든 원장에 대해 클라우드 API와 상호 작용하는 Azure 기밀 원장 사용자를 보여줍니다.

:::image type="content" source="./media/architecture-overview.png" alt-text="아키텍처 개요":::

## <a name="next-steps"></a>다음 단계

- [Microsoft Azure 기밀 원장 개요](overview.md)
- [Azure 기밀 원장 노드 인증](authenticate-ledger-nodes.md)

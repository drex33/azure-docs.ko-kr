---
title: Azure 기밀 원장에 대한 질문과 대답
description: Azure 기밀 원장에 대한 질문과 대답
services: confidential-ledger
author: msmbaldwin
ms.service: confidential-ledger
ms.topic: overview
ms.date: 04/15/2021
ms.author: mbaldwin
ms.openlocfilehash: 2c5d6aab297fb357d94066a05dd7f6ff766b31df
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/03/2021
ms.locfileid: "131458117"
---
# <a name="frequently-asked-questions-for-azure-confidential-ledger"></a>Azure 기밀 원장에 대한 질문과 대답

## <a name="how-can-i-tell-if-the-acc-ledger-service-would-be-useful-to-my-organization"></a>ACC Ledger 서비스가 내 조직에 유용한지 어떻게 알 수 있나요?

Azure 기밀 원장은 악의적인 직원이 이전 레코드를 위조, 수정 또는 제거하려고 하는 "내부자" 시나리오를 비롯하여 동기가 부여된 공격자가 기본 로깅/스토리지 시스템을 손상시키려고 할 만큼 중요한 레코드를 보유한 조직에 적합합니다.

## <a name="what-makes-acc-ledger-much-more-secure"></a>ACC Ledger를 훨씬 더 안전하게 만드는 것은 무엇인가요?

이름에서 알 수 있듯이, 원장은 [Azure 기밀 컴퓨팅 플랫폼](../confidential-computing/index.yml)을 활용합니다. 하나의 원장은 세 개 이상의 동일한 인스턴스에 걸쳐 있으며, 각 인스턴스는 완전히 증명된 전용 하드웨어 지원 enclave에서 실행됩니다. 원장의 무결성은 합의 기반 블록체인을 통해 유지 관리됩니다.

## <a name="when-writing-to-the-acc-ledger-do-i-need-to-store-write-receipts"></a>ACC Ledger에 쓸 때 쓰기 영수증을 저장해야 하나요?

그럴 필요는 없습니다. 현재 일부 솔루션에서는 사용자가 향후 로그 유효성 검사를 위해 쓰기 영수증을 유지해야 합니다. 이렇게 하려면 사용자가 보안 스토리지 시설에서 해당 영수증을 관리해야 하므로 부담이 추가됩니다. 원장은 Merkle 트리 기반 접근 방식을 통해 이 챌린지를 제거합니다. 여기서 쓰기 영수증은 서명된 신뢰 루트에 대한 전체 트리 경로를 포함합니다. 사용자는 원장 데이터를 저장하거나 관리하지 않고도 트랜잭션을 확인할 수 있습니다.

## <a name="how-do-i-verify-ledgers-authenticity"></a>원장의 신뢰성을 어떻게 확인하나요?

클라이언트와 통신하는 원장 서버 노드가 인증되었는지 확인할 수 있습니다. 자세한 내용은 [기밀 원장 노드 인증](authenticate-ledger-nodes.md)을 참조하세요.



## <a name="next-steps"></a>다음 단계

- [Microsoft Azure 기밀 원장 개요](overview.md)
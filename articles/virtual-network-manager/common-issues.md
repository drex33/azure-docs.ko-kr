---
title: Azure Virtual Network Manager(미리 보기)에서 발생하는 일반적인 문제
description: Azure Virtual Network Manager를 사용할 때 발생하는 일반적인 문제에 대해 알아봅니다.
author: duongau
ms.author: duau
ms.service: virtual-network-manager
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: bcf66883ba766189215fd4ce267391c9358b10f9
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131052951"
---
# <a name="common-issues-seen-with-azure-virtual-network-manager-preview"></a>Azure Virtual Network Manager(미리 보기)에서 발생하는 일반적인 문제

이 문서에서는 Azure Virtual Network Manager를 사용할 때 발생할 수 있는 일반적인 문제를 다루고 몇 가지 가능한 솔루션을 제공합니다.

## <a name="why-isnt-my-configuration-getting-applied"></a>내 구성이 적용되지 않는 이유는 무엇인가요? 

**구성이 적용되지 않는 일반적인 이유:** 

* 구성은 가상 네트워크가 있는 지역에 배포되지 않습니다. 

* 구성을 아직 배포하지 않았습니다. 구성을 적용하려면 배포해야 합니다. 

* 구성에 적용할 시간이 충분하지 않았습니다. 구성을 커밋한 후 구성을 적용하는 데 걸리는 시간은 약 15-20분입니다. 네트워크 그룹 멤버 자격에 대한 업데이트가 있는 경우 변경 내용이 반영될 때 약 10분이 걸립니다. 

## <a name="i-updated-my-configuration-but-the-changes-arent-reflected-in-my-environment"></a>구성을 업데이트했지만 변경 내용이 내 환경에 반영되지 않습니다. 

구성이 수정된 후 새 구성을 배포해야 합니다. 

## <a name="why-is-my-connectivity-configuration-not-working"></a>내 연결 구성이 작동하지 않는 이유는 무엇인가요? 

**다음 항목을 고려해야 합니다.** 

* 허브 및 스포크 *토폴로지에서 허브를 게이트웨이로 사용하는* 옵션을 사용하도록 설정하면 허브 가상 네트워크에 게이트웨이가 있어야 합니다. 그렇지 않으면 허브와 스포크 가상 네트워크 간에 가상 네트워크 피어링을 만들 수 없습니다. 

* 네트워크 그룹의 구성원이 허브 및 스포크 토폴로지 구성의 여러 지역에서 서로 통신하도록 하려면 전역 메시 옵션을 사용하도록 설정해야 합니다. 

## <a name="next-steps"></a>다음 단계

자주 묻는 질문에 대한 답변은 [Azure Virtual Network Manager FAQ](faq.md)를 참조하세요.

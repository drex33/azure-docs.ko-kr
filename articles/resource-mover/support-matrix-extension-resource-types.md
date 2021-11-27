---
title: Azure 리소스 이동의 확장 리소스 유형 지원
description: 지원 되는 확장 리소스 유형
author: aarthiv
manager: sutalasi
ms.service: resource-move
ms.topic: conceptual
ms.date: 11/17/2021
ms.author: aarthiv
ms.openlocfilehash: ff0614e1fac96588130bedcaaa5df1effab1408b
ms.sourcegitcommit: 625ffc4a23b7b71680aaf572fede507053d4ef03
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/27/2021
ms.locfileid: "133177397"
---
# <a name="support-for-moving-extension-resource-types-between-azure-regions"></a>Azure 지역 간에 확장 리소스 유형 이동 지원
이 문서에서는 Azure 리소스 이동 기를 사용 하 여 지역 간에 Azure 리소스를 이동 하는 동안 현재 지원 되는 모든 [확장 리소스 형식을 ](../azure-resource-manager/management/extension-resource-types.md)요약 합니다.
## <a name="extension-resource-types-supported"></a>지원 되는 확장 리소스 유형
아래 표에는 지원 되는 확장 리소스 종류가 나와 있습니다.

**확장 리소스 유형** | **지원** |**세부 정보**
--- | --- | --- |
Microsoft.Resources/tags | 지원됨 | 원본 리소스 종류 태그를 그대로 이동 하거나 Azure 리소스 이동의 [대상 구성 섹션](modify-target-settings.md) 에서 수정할 수 있습니다. 준비 프로세스를 시작 하기 전에 대상 지역에서 태그 수준 정책을 [사용 하지 않도록 설정](../governance/policy/concepts/effects.md#disabled) 하세요. 
Microsoft.Resources/links | 지원되지 않음
ManagedIdentities/Id |시스템 할당 관리 id는 지원 되지 않습니다.  
ManagedIdentities/Id |사용자 할당 관리 Id 할당이 지원 됩니다. | 원본 리소스의 사용자 할당 관리 id는 대상 리소스에 할당 됩니다. 사용자 할당 id가 리소스 자체로 이동 하는 것은 지원 되지 않습니다.


## <a name="next-steps"></a>다음 단계
[VM에 대해 지원 되는 확장 리소스 유형을 수정](modify-target-settings.md) 해 보세요.

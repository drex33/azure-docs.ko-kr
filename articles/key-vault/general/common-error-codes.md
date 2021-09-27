---
title: Azure Key Vault의 일반적인 오류 코드 | Microsoft Docs
description: Azure Key Vault의 일반적인 오류 코드
services: key-vault
author: sebansal
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: reference
ms.date: 09/29/2020
ms.author: mbaldwin
ms.openlocfilehash: bd66619c65dd39e32a11b81096d4b763430fc16c
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128553682"
---
# <a name="common-error-codes-for-azure-key-vault"></a>Azure Key Vault의 일반적인 오류 코드

다음 표에 나열된 오류 코드는 Azure Key Vault의 작업에서 반환될 수 있습니다.

| 오류 코드 | 사용자 메시지 |
|--|--|
| VaultAlreadyExists |  지정하려는 이름이 이미 사용 중이므로 해당 이름으로 새 키 자격 증명 모음을 만들려는 시도가 실패했습니다. 이 이름의 키 자격 증명 모음을 최근에 삭제한 경우에도 일시 삭제된 상태에 있을 수 있습니다. [여기](./key-vault-recovery.md?tabs=azure-portal#list-recover-or-purge-a-soft-deleted-key-vault)에서 일시 삭제된 상태인지 확인할 수 있습니다. |
| VaultNameNotValid |  자격 증명 모음 이름은 3~24자의 문자열이어야 하며 숫자(0-9), 문자(a-z, A-Z) 및 하이픈(-)만 포함할 수 있습니다. |
| AccessDenied |  액세스 정책에 해당 작업을 수행할 수 있는 권한이 없을 수 있습니다. |
| ForbiddenByFirewall |  클라이언트 주소가 인증되지 않았고 호출자는 신뢰할 수 있는 서비스가 아닙니다. |
| ConflictError |  동일한 항목에 대해 여러 작업을 요청하고 있습니다.  |
| RegionNotSupported |  지정된 Azure 지역은 이 리소스에 대해 지원되지 않습니다. |
| SkuNotSupported |  지정된 SKU 형식은 이 리소스에 대해 지원되지 않습니다. |
| ResourceNotFound |  지정된 Azure 리소스를 찾을 수 없습니다. |
| ResourceGroupNotFound | 지정된 Azure 리소스 그룹을 찾을 수 없습니다. |
| CertificateExpired |  인증서의 만료 날짜 및 유효 기간을 확인합니다. |


## <a name="next-steps"></a>다음 단계

- [Azure Key Vault 개발자 가이드](developers-guide.md) 참조
- [Key Vault에 인증](authentication.md)에 대해 자세히 알아보세요.

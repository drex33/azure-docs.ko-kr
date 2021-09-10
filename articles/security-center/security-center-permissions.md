---
title: Azure Security Center의 권한 | Microsoft Docs
description: 이 문서에서는 Azure Security Center가 역할 기반 액세스 제어를 사용하여 사용자에게 권한을 할당하는 방법과 각 역할에 대해 허용된 작업을 식별하는 방법을 설명합니다.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 07/04/2021
ms.author: memildin
ms.openlocfilehash: 9f35b3e79736ed195af9e0fc1303a24951fbb675
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121734854"
---
# <a name="permissions-in-azure-security-center"></a>Azure Security Center의 권한

Security Center는 Azure에서 사용자, 그룹 및 서비스에 할당할 수 있는 [기본 제공 역할](../role-based-access-control/built-in-roles.md)을 제공하는 [Azure RBAC(Azure 역할 기반 액세스 제어)](../role-based-access-control/role-assignments-portal.md)를 사용합니다.

Security Center는 리소스 구성을 평가하여 보안 문제 및 취약성을 식별합니다. Security Center에서는 구독이나 리소스의 리소스 그룹에 대한 소유자, 기여자 또는 읽기 권한자 역할이 할당된 사람만 리소스와 관련된 정보를 볼 수 있습니다.

기본 제공 역할 외에도 Security Center와 관련된 두 가지 역할이 있습니다.

* **보안 읽기 권한자**: 이 역할에 속하는 사용자는 Security Center에 대한 보기 권한이 있습니다. 사용자가 권장 사항, 경고, 보안 정책 및 보안 상태를 볼 수 있지만 변경할 수는 없습니다.
* **보안 관리자**: 이 역할에 속하는 사용자는 보안 읽기 권한자와 동일한 권한이 있으며 보안 정책을 업데이트하고 경고 및 권장 사항을 해제할 수도 있습니다.

> [!NOTE]
> 보안 역할(보안 읽기 권한자 및 보안 관리자)은 Security Center에만 액세스할 수 있습니다. 보안 역할은 스토리지, 웹 및 모바일, 사물 인터넷 등의 다른 Azure 서비스에 액세스할 수 없습니다.

## <a name="roles-and-allowed-actions"></a>역할 및 허용되는 작업

다음 표는 Security Center의 역할 및 허용되는 작업을 보여 줍니다.

| **작업**                                                                                                                      | [보안 읽기 권한자 ](../role-based-access-control/built-in-roles.md#security-reader) / <br> [판독기](../role-based-access-control/built-in-roles.md#reader) | [보안 관리자](../role-based-access-control/built-in-roles.md#security-admin) | [기여자](../role-based-access-control/built-in-roles.md#contributor) / [소유자](../role-based-access-control/built-in-roles.md#owner)| [기여자](../role-based-access-control/built-in-roles.md#contributor)| [소유자](../role-based-access-control/built-in-roles.md#owner)|
|:----------------------------------------------------------------------------------------------------------------------------|:-----------------------------:|:--------------:|:------------------------------------------------------:|:------------------------:|:------------------:|
||||**(리소스 그룹 수준)**|**(구독 수준)**|**(구독 수준)**|
| 이니셔티브 추가/할당(규정 준수 표준 포함)                                                         | -                             | -              | -                                                      | -                        | ✔                 |
| 보안 정책 편집                                                                                                        | -                             | ✔             | -                                                      | -                        | ✔                 |
| Azure Defender 플랜 사용/사용 안 함                                                                                             | -                             | ✔             | -                                                      | -                        | ✔                 |
| 자동 프로비저닝 사용/사용 안 함                                                                                          | -                             | ✔             | -                                                      | ✔                       | ✔                  |
| 경고 해제                                                                                                              | -                             | ✔             | -                                                      | ✔                       | ✔                  |
| 리소스에 보안 권장 사항 적용</br> (및 [픽스](security-center-remediate-recommendations.md#fix-button)사용) | -                             | -              | ✔                                                     | ✔                        | ✔                 |
| 경고 및 권장 사항 보기                                                                                             | ✔                            | ✔              | ✔                                                     | ✔                        | ✔                 |
||||||

> [!NOTE]
> 사용자가 자신의 작업을 완료하는 데 필요한 최소한의 역할을 할당하는 것이 좋습니다. 예를 들어, 리소스의 보안 상태에 대한 정보를 보기만 하고 권장 사항 적용이나 정책 편집 등의 조치는 취하지 않는 사용자에게 독자 역할을 할당합니다.

## <a name="next-steps"></a>다음 단계
이 문서는 Security Center에서 Azure RBAC를 사용하여 사용자에게 사용 권한을 할당하고 각 역할에 대해 허용된 작업을 식별하는 방법에 대해 설명합니다. 이제 구독의 보안 상태를 모니터링하고, 보안 정책을 편집하고, 권장 사항을 적용하는 데 필요한 역할 할당에 익숙해졌으므로 다음을 수행하는 방법을 알아봅니다.

- [Security Center에서 보안 정책 설정](tutorial-security-policy.md)
- [Security Center의 보안 권장 사항 관리](security-center-recommendations.md)
- [Security Center에서 보안 경고 관리 및 응답](security-center-managing-and-responding-alerts.md)
- [파트너 보안 솔루션 모니터링](./security-center-partner-integration.md)
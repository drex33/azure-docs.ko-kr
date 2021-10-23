---
title: Azure의 EA(기업계약) 관리자 역할 이해
description: Azure의 엔터프라이즈 관리자 역할에 대해 알아봅니다. 5가지 개별 관리 역할을 할당할 수 있습니다.
author: bandersmsft
ms.reviewer: sapnakeshari
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: conceptual
ms.date: 10/22/2021
ms.author: banders
ms.custom: contperf-fy21q1
ms.openlocfilehash: 08f13b90190e3d05f87947cb7be58cbda8566c8a
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130232890"
---
# <a name="managing-azure-enterprise-agreement-roles"></a>Azure 기업계약 역할 관리

조직의 사용량과 지출을 관리하는 데 도움이 되도록 기업계약을 체결한 Azure 고객은 다음과 같은 관리 역할 6개를 할당할 수 있습니다.

- 엔터프라이즈 관리자
- 엔터프라이즈 관리자(읽기 전용)<sup>1</sup>
- EA 구매자
- 부서 관리자
- 부서 관리자(읽기 전용)
- 계정 소유자<sup>2</sup>

<sup>1</sup> EA 계약의 청구 담당자가 이 역할을 수행합니다.

<sup>2</sup> 청구 대상 연락처는 Azure EA Portal에서 추가 또는 변경할 수 없으며, 계약 수준에서 청구 대상 연락처로 설정된 사용자를 기반으로 하여 EA 등록에 추가됩니다. 청구 대상 연락처를 변경하려면 파트너/소프트웨어 관리자를 통해 ROC(지역 운영 센터)에 요청해야 합니다.

등록 프로비저닝 중에 설정된 첫 번째 등록 관리자는 청구 대상 담당자 계정의 인증 유형을 결정합니다. 청구 대상 담당자가 읽기 전용 관리자로 EA 포털에 추가되면 Microsoft 계정 인증이 제공됩니다. 

예를 들어 초기 인증 유형이 혼합으로 설정된 경우 EA가 Microsoft 계정으로 추가되고 청구 대상 담당자에게 읽기 전용 EA 관리자 권한이 부여됩니다. EA 관리자가 기존 청구 대상 담당자에 대한 Microsoft 계정 권한 부여에 동의하지 않는 경우, EA 관리자는 해당 사용자를 삭제하고 EA 포털의 등록 수준에서만 설정된 회사 또는 학교 계정을 사용하여 사용자를 읽기 전용 관리자로 추가하도록 고객에게 요청할 수 있습니다.

이러한 역할은 Azure 기업계약 관리에만 적용되며, 리소스에 대한 액세스를 제어하기 위한 Azure의 기본 제공 역할 외에 추가로 제공됩니다. 자세한 정보는 [Azure 기본 제공 역할](../../role-based-access-control/built-in-roles.md)을 참조하세요.

## <a name="azure-enterprise-portal-hierarchy"></a>Azure Enterprise Portal 계층 구조

Azure Enterprise Portal 계층 구조는 다음과 같이 구성됩니다.

- **Azure EA Portal** - Azure EA 서비스의 비용을 관리할 수 있는 온라인 관리 포털입니다. 다음을 수행할 수 있습니다.

  - 부서, 계정 및 구독이 포함된 Azure EA 계층 구조를 만듭니다.
  - 사용된 서비스의 비용을 조정하고, 사용량 보고서를 다운로드하고, 가격 목록을 봅니다.
  - 등록의 API 키를 만듭니다.

- **부서** 는 비용을 논리 그룹으로 분할하는 데 도움이 됩니다. 부서를 사용하면 부서 수준에서 예산 또는 할당량을 설정할 수 있습니다.

- **계정** 은 Azure Enterprise Portal의 조직 구성 단위입니다. 계정을 사용하여 구독을 관리하고 보고서에 액세스할 수 있습니다.

- **구독** 은 Azure Enterprise Portal에서 가장 작은 단위입니다. 구독은 서비스 관리자가 관리하는 Azure 서비스의 컨테이너입니다.

다음 다이어그램에서는 간단한 Azure EA 계층 구조를 보여줍니다.

![간단한 Azure EA 계층 구조 다이어그램](./media/understand-ea-roles/ea-hierarchies.png)

## <a name="enterprise-user-roles"></a>엔터프라이즈 사용자 역할

다음 관리 사용자 역할은 엔터프라이즈 등록에 포함되어 있습니다.

- 엔터프라이즈 관리자
- EA 구매자
- 부서 관리자
- 계정 소유자
- 서비스 관리자
- 알림 연락처

역할은 두 개의 다른 포털에서 작동하여 작업을 완료합니다. [Azure Enterprise Portal](https://ea.azure.com)은 청구 및 비용을 관리에 사용되고, [Azure Portal](https://portal.azure.com)은 Azure 서비스 관리에 사용됩니다.

직접 EA 고객은 Azure Portal의 모든 관리 작업을 완료할 수 있습니다. [Azure Portal](https://portal.azure.com) 을 사용 하 여 청구, 비용 및 azure 서비스를 관리할 수 있습니다.

사용자 역할은 사용자 계정과 연결됩니다. 사용자 인증의 유효성을 검사하려면 각 사용자에게 유효한 회사, 학교 또는 Microsoft 계정이 있어야 합니다. 각 계정이 적극적으로 모니터링되는 이메일 주소와 연결되어 있는지 확인합니다. 계정 알림은 이메일 주소로 전달됩니다.

사용자를 설정할 때 여러 계정을 엔터프라이즈 관리자 역할에 할당할 수 있습니다. 그러나 오직 한 계정만 계정 소유자 역할을 보유할 수 있습니다. 또한 엔터프라이즈 관리자와 계정 소유자 역할을 모두 한 계정에 할당할 수 있습니다.

### <a name="enterprise-administrator"></a>엔터프라이즈 관리자

이 역할을 가진 사용자는 최고 수준의 액세스 권한을 가지며 다음 작업을 수행할 수 있습니다.

- 계정 및 계정 소유자 관리
- 다른 엔터프라이즈 관리자 관리
- 부서 관리자 관리
- 알림 연락처 관리
- Azure 서비스 구매(예약 포함)
- 모든 계정의 사용량 보기
- 모든 계정의 미청구 요금 보기
- 기업계약에 적용되는 모든 예약 주문 및 예약을 보고 관리합니다.
  - 엔터프라이즈 관리자(읽기 전용)는 예약 주문 및 예약을 볼 수 있습니다. 이러한 항목은 관리할 수 없습니다.

기업 등록 하나에 여러 엔터프라이즈 관리자를 둘 수 있습니다. 엔터프라이즈 관리자에게 읽기 전용 액세스 권한을 부여할 수 있습니다. 모든 엔터프라이즈 관리자는 부서 관리자 역할을 상속합니다.

### <a name="ea-purchaser"></a>EA 구매자

이 역할이 있는 사용자는 Azure 서비스를 구매할 수 있는 권한을 가지지만 계정을 관리할 수는 없습니다. 다음 작업을 수행할 수 있습니다.

- Azure 서비스 구매(예약 포함)
- 모든 계정의 사용량 보기
- 모든 계정의 미청구 요금 보기
- 기업계약에 적용되는 모든 예약 주문 및 예약을 보고 관리합니다.

EA 구매자 역할은 현재 SPN 기반 액세스에만 사용됩니다. 서비스 사용자 이름에 역할을 할당하는 방법은 [Azure 기업계약 서비스 사용자 이름에 역할 할당](assign-roles-azure-service-principals.md)을 참조하세요.

### <a name="department-administrator"></a>부서 관리자

이 역할을 가진 사용자는 다음과 같은 작업을 수행할 수 있습니다.

- 부서 만들기 및 관리
- 새 계정 소유자 만들기
- 자신이 관리하는 부서의 사용량 세부 정보 보기
- 비용 보기(필요한 권한이 부여된 경우)

기업 등록마다 여러 부서 관리자를 둘 수 있습니다.

새 부서 관리자를 편집하거나 만들 때 부서 관리자에게 읽기 전용 액세스 권한을 부여할 수 있습니다. 읽기 전용 옵션을 **예** 로 설정합니다.

### <a name="account-owner"></a>계정 소유자

이 역할을 가진 사용자는 다음과 같은 작업을 수행할 수 있습니다.

- 구독 만들기 및 관리
- 서비스 관리자 관리
- 구독 사용량 보기

계정마다 고유한 회사, 학교 또는 Microsoft 계정이 필요합니다. Azure Enterprise Portal 관리 역할에 대한 자세한 내용은 [Azure의 Azure 기업계약 관리 역할 이해](understand-ea-roles.md)를 참조하세요.

### <a name="service-administrator"></a>서비스 관리자

서비스 관리자 역할은 Azure Portal에서 서비스를 관리하고 사용자를 공동 관리자 역할에 할당할 수 있는 권한을 갖고 있습니다.

### <a name="notification-contact"></a>알림 연락처

알림 담당자는 등록과 관련된 사용 알림을 받습니다.

다음 섹션에서는 각 역할의 제한 사항 및 기능에 대해 설명합니다.

## <a name="user-limit-for-admin-roles"></a>관리자 역할에 대한 사용자 제한

|역할| 사용자 제한|
|---|---|
|엔터프라이즈 관리자|제한 없음|
|엔터프라이즈 관리자(읽기 전용)|제한 없음|
| SPN에 할당된 EA 구매자 | 제한 없음 |
|부서 관리자|제한 없음|
|부서 관리자(읽기 전용)|제한 없음|
|계정 소유자|계정당 1명<sup>3</sup>|

<sup>3</sup> 각 계정마다 고유한 Microsoft 계정 또는 회사/학교 계정이 필요합니다.

## <a name="organization-structure-and-permissions-by-role"></a>역할별 조직 구조 및 사용 권한

|작업| 엔터프라이즈 관리자|엔터프라이즈 관리자(읽기 전용)| EA 구매자 | 부서 관리자|부서 관리자(읽기 전용)|계정 소유자| 파트너|
|---|---|---|---|---|---|---|---|
|엔터프라이즈 관리자 보기|✔|✔| ✔|✘|✘|✘|✔|
|엔터프라이즈 관리자 추가 또는 제거|✔|✘|✘|✘|✘|✘|✘|
|알림 연락처 보기<sup>4</sup> |✔|✔|✔|✘|✘|✘|✔|
|알림 연락처 추가 또는 제거<sup>4</sup> |✔|✘|✘|✘|✘|✘|✘|
|부서 만들기 및 관리 |✔|✘|✘|✘|✘|✘|✘|
|부서 관리자 보기|✔|✔|✔|✔|✔|✘|✔|
|부서 관리자 추가 또는 제거|✔|✘|✘|✔|✘|✘|✘|
|등록에 포함된 계정 보기 |✔|✔|✔|✔<sup>5</sup>|✔<sup>5</sup>|✘|✔|
|등록에 계정 추가 및 계정 소유자 변경|✔|✘|✘|✔<sup>5</sup>|✘|✘|✘|
|예약 구매|✔|✘|✔|✘|✘|✘|✘|
|구독 및 구독 사용 권한 만들기/관리|✘|✘|✘|✘|✘|✔|✘|

- <sup>4</sup> 알림 연락처에는 Azure 기업계약에 대한 이메일 통신이 전송됩니다.
- <sup>5</sup> 작업은 부서의 계정으로 제한됩니다.

## <a name="add-a-new-enterprise-administrator"></a>새 엔터프라이즈 관리자 추가

엔터프라이즈 관리자는 Azure EA 등록을 관리할 때 가장 많은 권한을 가집니다. EA 계약이 설정될 때 초기 Azure EA 관리자가 생성되었습니다. 하지만 언제든지 관리자를 새로 추가하거나 제거할 수 있습니다. 기존 관리자만이 새 관리자를 추가할 수 있습니다. 엔터프라이즈 관리자를 추가하는 자세한 내용은 [다른 엔터프라이즈 관리자 만들기를](ea-portal-administration.md#create-another-enterprise-administrator)참조하세요. 직접 EA 고객은 Azure Portal 사용하여 EA 관리자를 추가할 수 있습니다. [Azure Portal에서 다른 엔터프라이즈 관리자 만들기를 참조하세요.](direct-ea-administration.md#add-another-enterprise-administrator) 청구 프로필 역할 및 작업에 대한 자세한 내용은 [청구 프로필 역할 및 작업](understand-mca-roles.md#billing-profile-roles-and-tasks)을 참조하세요.


## <a name="update-account-owner-state-from-pending-to-active"></a>계정 소유자 상태를 보류 중에서 활성으로 업데이트

새 AO(계정 소유자)를 Azure EA 등록에 처음으로 추가하면 상태가 _보류 중_ 으로 표시됩니다. 새 계정 소유자는 활성화 시작 이메일을 받으면 로그인하여 계정을 활성화할 수 있습니다. 계정이 활성화되면 계정 상태가 _보류 중_ 에서 _활성_ 으로 업데이트됩니다. 계정 소유자는 '경고' 메시지를 읽고 **계속** 을 선택해야 합니다. 신규 사용자가 상거래 계정을 만들 때 이름과 성을 입력하라는 메시지가 표시될 수 있습니다. 이 경우 계속하려면 필요한 정보를 추가해야 합니다. 그러면 계정이 활성화됩니다.

## <a name="add-a-department-admin"></a>부서 관리자 추가

Azure EA 관리자가 부서를 만들면 Azure 엔터프라이즈 관리자는 부서 관리자를 추가하고 각 부서 관리자를 부서에 연결할 수 있습니다. 부서 관리자는 새 계정을 만들 수 있습니다. Azure EA 구독을 만들려면 새 계정이 필요합니다.

직접 EA 관리자는 Azure Portal 부서 관리자를 추가할 수 있습니다. 자세한 내용은 [Azure EA 부서 관리자 만들기를 참조하세요.](direct-ea-administration.md#add-a-department-administrator)

## <a name="usage-and-costs-access-by-role"></a>역할별 사용량 및 비용 액세스

|작업| 엔터프라이즈 관리자|엔터프라이즈 관리자(읽기 전용)|EA 구매자|부서 관리자|부서 관리자(읽기 전용) |계정 소유자| 파트너|
|---|---|---|---|---|---|---|---|
|Azure 선불을 포함한 크레딧 잔액 보기|✔|✔|✔|✘|✘|✘|✔|
|부서 지출 할당량 보기|✔|✔|✔|✘|✘|✘|✔|
|부서 지출 할당량 설정|✔|✘|✘|✘|✘|✘|✘|
|조직의 EA 가격표 보기|✔|✔|✔|✘|✘|✘|✔|
|사용량 및 비용 세부 정보 보기|✔|✔|✔|✔<sup>6</sup>|✔<sup>6</sup>|✔<sup>7</sup>|✔|
|Azure Portal의 리소스 관리|✘|✘|✘|✘|✘|✔|✘|

- <sup>6</sup> 엔터프라이즈 관리자가 Enterprise Portal에서 **DA 요금 보기** 정책을 사용하도록 설정해야 합니다. 그러면 부서 관리자가 부서의 비용 세부 정보를 볼 수 있습니다.
- <sup>7</sup> 엔터프라이즈 관리자가 Enterprise Portal에서 **AO 요금 보기** 정책을 사용하도록 설정해야 합니다. 그러면 계정 소유자가 계정의 비용 세부 정보를 볼 수 있습니다.

## <a name="see-pricing-for-different-user-roles"></a>다른 사용자 역할에 대한 가격 책정 보기

Azure Portal에서 표시되는 가격은 사용자의 관리 역할 및 엔터프라이즈 관리자가 요금 보기 정책을 설정한 방식에 따라 다를 수 있습니다. Azure Portal에 표시되는 가격에 영향을 주는 Enterprise Portal의 두 가지 정책은 다음과 같습니다.

- DA 요금 보기
- AO 요금 보기

두 정책을 설정하는 방법을 알아보려면 [Azure의 청구 정보에 대한 액세스 관리](manage-billing-access.md)를 참조하세요.

다음 표에서는 기업계약 관리자 역할, 요금 보기 정책, Azure Portal의 Azure 역할, Azure Portal에 표시되는 가격 간의 관계를 보여줍니다. 엔터프라이즈 관리자에게는 항상 조직의 EA 가격을 기준으로 사용량 세부 정보가 표시됩니다. 그러나 부서 관리자 및 계정 소유자에게는 요금 보기 정책 및 해당 Azure 역할에 따라 다른 가격 보기가 표시됩니다. 다음 표에 나열된 부서 관리자 역할은 부서 관리자 및 부서 관리자(읽기 전용) 역할을 둘 다 가리킵니다.

|기업계약 관리자 역할|역할에 대한 요금 보기 정책|Azure 역할|가격 보기|
|---|---|---|---|
|계정 소유자 또는 부서 관리자|✔ 사용|소유자|조직의 EA 가격|
|계정 소유자 또는 부서 관리자|✘ 사용 안 함|소유자|가격 없음|
|계정 소유자 또는 부서 관리자|✔ 사용 |none|가격 없음|
|계정 소유자 또는 부서 관리자|✘ 사용 안 함 |none|가격 없음|
|None|해당 없음 |소유자|가격 없음|

엔터프라이즈 관리자 역할 및 요금 보기 정책은 Enterprise Portal에서 설정합니다. Azure 역할은 Azure Portal에서 업데이트할 수 있습니다. 자세한 내용은 [Azure Portal을 사용하여 Azure 역할 할당](../../role-based-access-control/role-assignments-portal.md)을 참조하세요.

## <a name="next-steps"></a>다음 단계

- [Azure의 청구 정보에 대한 액세스 관리](manage-billing-access.md)
- [Azure Portal을 사용하여 Azure 역할 할당](../../role-based-access-control/role-assignments-portal.md)
- [Azure 기본 제공 역할](../../role-based-access-control/built-in-roles.md)

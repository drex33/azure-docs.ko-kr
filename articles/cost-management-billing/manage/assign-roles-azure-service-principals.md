---
title: Azure 기업계약 서비스 사용자 이름에 역할 할당
description: 이 문서에서는 PowerShell 및 REST API를 사용하여 서비스 주체 이름에 역할을 할당할 수 있도록 도와줍니다.
author: bandersmsft
ms.reviewer: sapnakeshari
tags: billing
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 10/22/2021
ms.author: banders
ms.openlocfilehash: 65f37b3f071286d143dc83674a7c7108a37cff44
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/22/2021
ms.locfileid: "130260383"
---
# <a name="assign-roles-to-azure-enterprise-agreement-service-principal-names"></a>Azure 기업계약 서비스 사용자 이름에 역할 할당

[Azure Enterprise 포털](https://ea.azure.com/)에서 EA(기업계약) 등록을 관리할 수 있습니다. 이제 직접 Enterprise 고객은 Azure Portal [EA(기업계약)](https://portal.azure.com/)등록을 관리할 수 있습니다.
여러 역할을 만들어 조직을 관리하고, 비용을 살펴보고, 구독을 만들 수 있습니다. 이 문서에서는 Azure PowerShell 및 REST API와 함께 Azure SPN(서비스 주체 이름)을 사용하여 이러한 작업 중 일부를 자동화할 수 있도록 도와줍니다.

시작하기 전에 다음 문서를 숙지해야 합니다.

- [기업계약 역할](understand-ea-roles.md)
- [Azure PowerShell로 로그인](/powershell/azure/authenticate-azureps)
- [Postman을 사용하여 REST API를 호출하는 방법](/rest/api/azure/#how-to-call-azure-rest-apis-with-postman)

## <a name="create-and-authenticate-your-service-principal"></a>서비스 사용자 만들기 및 인증

SPN을 사용하여 EA 작업을 자동화하려면 Azure AD(Azure Active Directory) 애플리케이션을 만들어야 합니다. 그러면 자동화된 방식으로 인증할 수 있습니다.

이 문서의 단계에 따라 서비스 주체를 만들고 인증하세요.

- [서비스 주체 만들기](../../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal)
- [로그인에 사용할 테넌트 및 앱 ID 값 가져오기](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in)

다음은 애플리케이션 등록 페이지의 예입니다.

:::image type="content" source="./media/assign-roles-azure-service-principals/register-an-application.png" alt-text="애플리케이션 등록을 보여주는 스크린샷" lightbox="./media/assign-roles-azure-service-principals/register-an-application.png" :::

### <a name="find-your-spn-and-tenant-id"></a>SPN 및 테넌트 ID 확인

또한 SPN의 개체 ID와 앱의 테넌트 ID가 필요합니다. 이 정보는 이 문서의 뒷부분에 있는 사용 권한 할당 작업에 필요합니다.

1. Azure Active Directory를 연 다음, **엔터프라이즈 애플리케이션** 을 선택합니다.
1. 목록에서 앱을 찾습니다.

   :::image type="content" source="./media/assign-roles-azure-service-principals/enterprise-application.png" alt-text="엔터프라이즈 애플리케이션 예제를 보여주는 스크린샷" lightbox="./media/assign-roles-azure-service-principals/enterprise-application.png" :::

1. 앱을 선택하여 애플리케이션 ID와 개체 ID를 확인합니다.

   :::image type="content" source="./media/assign-roles-azure-service-principals/application-id-object-id.png" alt-text="엔터프라이즈 애플리케이션의 애플리케이션 ID 및 개체 ID를 보여주는 스크린샷" lightbox="./media/assign-roles-azure-service-principals/application-id-object-id.png" :::

1. Microsoft Azure AD **개요** 페이지로 이동하여 테넌트 ID를 확인합니다.

   :::image type="content" source="./media/assign-roles-azure-service-principals/tenant-id.png" alt-text="테넌트 ID를 보여주는 스크린샷" lightbox="./media/assign-roles-azure-service-principals/tenant-id.png" :::

>[!NOTE]
>테넌트 ID는 다른 위치에서 주체 ID, SPN 또는 개체 ID라고도 합니다. Azure AD 테넌트 ID 값은 `11111111-1111-1111-1111-111111111111` 형식의 GUID와 비슷합니다.

## <a name="permissions-that-can-be-assigned-to-the-spn"></a>SPN에 할당할 수 있는 권한

이 문서의 뒷부분에서는 EA 역할을 통해 작업 수행 권한을 Azure AD 앱에 부여합니다. SPN에는 다음 역할만 할당할 수 있으며, 아래 나와 있는 대로 정확히 역할 정의 ID가 필요합니다.

| 역할 | 허용되는 작업 | 역할 정의 ID |
| --- | --- | --- |
| EnrollmentReader | 모든 계정과 구독의 사용량 및 요금을 볼 수 있습니다. 등록과 관련된 Azure 선불(이전 명칭은 현금 약정 금액) 잔액을 볼 수 있습니다. | 24f8edb6-1668-4659-b5e2-40bb5f3a7d7e |
| EA 구매자 | 예약 주문을 구입하고 예약 트랜잭션을 봅니다. 모든 계정과 구독의 사용량 및 요금을 볼 수 있습니다. 등록과 관련된 Azure 선불(이전 명칭은 현금 약정 금액) 잔액을 볼 수 있습니다. | da6647fb-7651-49ee-be91-c43c4877f0c4  |
| DepartmentReader | 본인이 관리하는 부서의 사용량 세부 정보를 다운로드할 수 있습니다. 본인의 부서와 관련된 사용량과 요금을 볼 수 있습니다. | db609904-a47f-4794-9be8-9bd86fbffd8a |
| SubscriptionCreator | 지정된 계정 범위에서 새 구독을 만듭니다. | a0bcee42-bf30-4d1b-926a-48d21664ef71 |

- 등록 작성자 역할을 가진 사용자만이 EnrollmentReader 역할을 SPN에 할당할 수 있습니다.
- 등록 작성자 역할 또는 부서 작성자 역할을 가진 사용자만이 DepartmentReader 역할을 SPN에 할당할 수 있습니다.
- 등록 계정의 소유자인 사용자만이 SubscriptionCreator 역할을 SPN에 할당할 수 있습니다. 이 역할은 EA Portal에 표시되지 않습니다. 이 역할은 프로그래매틱 방식으로 생성되고 사용됩니다.
- EA 구매자 역할은 EA Portal에 표시되지 않습니다. 이 역할은 프로그래매틱 방식으로 생성되고 사용됩니다.

## <a name="assign-enrollment-account-role-permission-to-the-spn"></a>SPN에 등록 계정 역할 권한 할당

1. [역할 할당 - Put](/rest/api/billing/2019-10-01-preview/role-assignments/put) REST API 문서를 읽어 보세요. 문서를 읽는 동안 **사용해 보기** 를 선택하여 SPN 사용을 시작하세요.

   :::image type="content" source="./media/assign-roles-azure-service-principals/put-try-it.png" alt-text="Put 문서의 [사용해 보기] 옵션을 보여주는 스크린샷" lightbox="./media/assign-roles-azure-service-principals/put-try-it.png" :::

1. 계정 자격 증명을 사용하여 할당하려는 등록 액세스 권한으로 테넌트에 로그인합니다.

1. 다음 매개 변수를 API 요청에 포함하여 제공합니다.

   - `billingAccountName`: 이 매개 변수는 **청구 계정 ID** 입니다. 이 ID는 Azure Portal의 **Cost Management + Billing** 개요 페이지에서 확인할 수 있습니다.

      :::image type="content" source="./media/assign-roles-azure-service-principals/billing-account-id.png" alt-text="청구 계정 ID를 보여주는 스크린샷" lightbox="./media/assign-roles-azure-service-principals/billing-account-id.png" :::

   - `billingRoleAssignmentName`: 이 매개 변수는 사용자가 입력해야 하는 고유한 GUID입니다. PowerShell 명령 [New-Guid](/powershell/module/microsoft.powershell.utility/new-guid)를 사용하여 GUID를 생성할 수 있습니다. 또한 [온라인 GUID/UUID 생성기](https://guidgenerator.com/) 웹 사이트에서 고유한 GUID를 생성할 수도 있습니다.

   - `api-version`: **2019-10-01-preview** 버전을 사용합니다. [역할 할당 - Put - 예제](/rest/api/billing/2019-10-01-preview/role-assignments/put#examples)의 샘플 요청 본문을 사용합니다.

      요청 본문에는 사용해야 하는 세 개의 매개 변수가 있는 JSON 코드가 포함됩니다.

      | 매개 변수 | 찾는 위치 |
      | --- | --- |
      | `properties.principalId` | 개체 ID의 값입니다. [SPN 및 테넌트 ID 확인](#find-your-spn-and-tenant-id)을 참조하세요. |
      | `properties.principalTenantId` | [SPN 및 테넌트 ID 확인](#find-your-spn-and-tenant-id)을 참조하세요. |
      | `properties.roleDefinitionId` | `/providers/Microsoft.Billing/billingAccounts/{BillingAccountName}/billingRoleDefinitions/24f8edb6-1668-4659-b5e2-40bb5f3a7d7e` |

      청구 계정 이름은 API 매개 변수에서 사용한 것과 동일한 매개 변수입니다. EA 포털 및 Azure Portal에 표시되는 등록 ID입니다.

      `24f8edb6-1668-4659-b5e2-40bb5f3a7d7e`는 EnrollmentReader의 청구 역할 정의 ID입니다.

1. **실행** 을 선택하여 명령을 시작합니다.

   :::image type="content" source="./media/assign-roles-azure-service-principals/roleassignments-put-try-it-run.png" alt-text="예제 정보를 실행할 준비가 완료된 예제 역할 할당 Put [사용해 보기]를 보여주는 스크린샷" lightbox="./media/assign-roles-azure-service-principals/roleassignments-put-try-it-run.png" :::

   SPN이 성공적으로 추가되었다는 `200 OK` 응답이 표시됩니다.

이제 SPN을 사용하여 EA API에 자동으로 액세스할 수 있습니다. SPN에는 EnrollmentReader 역할이 있습니다.

## <a name="assign-ea-purchaser-role-permission-to-the-spn"></a>SPN에 EA 구매자 역할 권한 할당

EA 구매자 역할에는 등록 읽기 권한자의 경우와 동일한 단계를 사용합니다. 다음 예제를 참조하여 `roleDefinitionId`를 지정합니다.

`"/providers/Microsoft.Billing/billingAccounts/1111111/billingRoleDefinitions/ da6647fb-7651-49ee-be91-c43c4877f0c4"`

## <a name="assign-the-department-reader-role-to-the-spn"></a>SPN에 부서 읽기 권한자 역할 할당

1. [등록 부서 역할 할당 - Put](/rest/api/billing/2019-10-01-preview/enrollment-department-role-assignments/put) REST API 문서를 읽어 보세요. 문서를 읽는 동안 **사용해 보기** 를 선택합니다.

   :::image type="content" source="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it.png" alt-text="등록 부서 역할 할당 Put 문서의 [사용해 보기] 옵션을 보여주는 스크린샷" lightbox="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it.png" :::

1. 계정 자격 증명을 사용하여 할당하려는 등록 액세스 권한으로 테넌트에 로그인합니다.

1. 다음 매개 변수를 API 요청에 포함하여 제공합니다.

   - `billingAccountName`: 이 매개 변수는 **청구 계정 ID** 입니다. 이 ID는 Azure Portal의 **Cost Management + Billing** 개요 페이지에서 확인할 수 있습니다.

      :::image type="content" source="./media/assign-roles-azure-service-principals/billing-account-id.png" alt-text="청구 계정 ID를 보여주는 스크린샷" lightbox="./media/assign-roles-azure-service-principals/billing-account-id.png" :::

   - `billingRoleAssignmentName`: 이 매개 변수는 사용자가 입력해야 하는 고유한 GUID입니다. PowerShell 명령 [New-Guid](/powershell/module/microsoft.powershell.utility/new-guid)를 사용하여 GUID를 생성할 수 있습니다. 또한 [온라인 GUID/UUID 생성기](https://guidgenerator.com/) 웹 사이트에서 고유한 GUID를 생성할 수도 있습니다.

   - `departmentName`: 이 매개 변수는 부서 ID입니다. 부서 ID는 Azure Portal의 **Cost Management + Billing** > **부서** 페이지에서 확인할 수 있습니다.

      이 예에서는 ACE 부서를 사용했습니다. 예제의 ID는 `84819`입니다.

      :::image type="content" source="./media/assign-roles-azure-service-principals/department-id.png" alt-text="예제 부서 ID를 보여주는 스크린샷" lightbox="./media/assign-roles-azure-service-principals/department-id.png" :::

   - `api-version`: **2019-10-01-preview** 버전을 사용합니다. [등록 부서 역할 할당 - Put](/rest/api/billing/2019-10-01-preview/enrollment-department-role-assignments/put)의 샘플을 사용합니다.

      요청 본문에는 사용해야 하는 세 개의 매개 변수가 있는 JSON 코드가 포함됩니다.

      | 매개 변수 | 찾는 위치 |
      | --- | --- |
      | `properties.principalId` | 개체 ID의 값입니다. [SPN 및 테넌트 ID 확인](#find-your-spn-and-tenant-id)을 참조하세요. |
      | `properties.principalTenantId` | [SPN 및 테넌트 ID 확인](#find-your-spn-and-tenant-id)을 참조하세요. |
      | `properties.roleDefinitionId` | `/providers/Microsoft.Billing/billingAccounts/{BillingAccountName}/billingRoleDefinitions/db609904-a47f-4794-9be8-9bd86fbffd8a` |

      청구 계정 이름은 API 매개 변수에서 사용한 것과 동일한 매개 변수입니다. EA 포털 및 Azure Portal에 표시되는 등록 ID입니다.

      청구 역할 정의 ID `db609904-a47f-4794-9be8-9bd86fbffd8a`는 부서 읽기 권한자의 것입니다.

1. **실행** 을 선택하여 명령을 시작합니다.

   :::image type="content" source="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it-run.png" alt-text="예제 정보를 실행할 준비가 완료된 예제 등록 부서 역할 할당 - Put REST [사용해 보기]를 보여주는 스크린샷" lightbox="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it-run.png" :::

   SPN이 성공적으로 추가되었다는 `200 OK` 응답이 표시됩니다.

이제 SPN을 사용하여 EA API에 자동으로 액세스할 수 있습니다. SPN에는 DepartmentReader 역할이 있습니다.

## <a name="assign-the-subscription-creator-role-to-the-spn"></a>SPN에 구독 작성자 역할 할당

1. [등록 계정 역할 할당 - Put](/rest/api/billing/2019-10-01-preview/enrollment-account-role-assignments/put) 문서를 읽어 보세요. 문서를 읽는 동안 **사용해 보기** 를 선택하여 구독 작성자 역할을 SPN에 할당합니다.

   :::image type="content" source="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it.png" alt-text="등록 계정 역할 할당 Put 문서의 [사용해 보기] 옵션을 보여주는 스크린샷" lightbox="./media/assign-roles-azure-service-principals/enrollment-department-role-assignments-put-try-it.png" :::

1. 계정 자격 증명을 사용하여 할당하려는 등록 액세스 권한으로 테넌트에 로그인합니다.

1. 다음 매개 변수를 API 요청에 포함하여 제공합니다. [등록 계정 역할 할당 - Put - URI 매개 변수](/rest/api/billing/2019-10-01-preview/enrollment-account-role-assignments/put#uri-parameters)의 문서를 읽어 보세요.

   - `billingAccountName`: 이 매개 변수는 **청구 계정 ID** 입니다. 이 ID는 Azure Portal의 **Cost Management + Billing 개요** 페이지에서 확인할 수 있습니다.

      :::image type="content" source="./media/assign-roles-azure-service-principals/billing-account-id.png" alt-text="청구 계정 ID를 보여주는 스크린샷" lightbox="./media/assign-roles-azure-service-principals/billing-account-id.png" :::

   - `billingRoleAssignmentName`: 이 매개 변수는 사용자가 입력해야 하는 고유한 GUID입니다. PowerShell 명령 [New-Guid](/powershell/module/microsoft.powershell.utility/new-guid)를 사용하여 GUID를 생성할 수 있습니다. 또한 [온라인 GUID/UUID 생성기](https://guidgenerator.com/) 웹 사이트에서 고유한 GUID를 생성할 수도 있습니다.

   - `enrollmentAccountName`: 이 매개 변수는 계정 **ID** 입니다. 계정 이름의 계정 ID는 Azure Portal의 **Cost Management + Billing** 페이지에서 확인합니다.

      이 예제에서는 GTM 테스트 계정을 사용합니다. ID는 `196987`입니다.

      :::image type="content" source="./media/assign-roles-azure-service-principals/account-id.png" alt-text="계정 ID를 보여주는 스크린샷" lightbox="./media/assign-roles-azure-service-principals/account-id.png" :::

   - `api-version`: **2019-10-01-preview** 버전을 사용합니다. [등록 부서 역할 할당 - Put - 예제](/rest/api/billing/2019-10-01-preview/enrollment-department-role-assignments/put#examples)의 샘플을 사용합니다.

      요청 본문에는 사용해야 하는 세 개의 매개 변수가 있는 JSON 코드가 포함됩니다.

      | 매개 변수 | 찾는 위치 |
      | --- | --- |
      | `properties.principalId` | 개체 ID의 값입니다. [SPN 및 테넌트 ID 확인](#find-your-spn-and-tenant-id)을 참조하세요. |
      | `properties.principalTenantId` | [SPN 및 테넌트 ID 확인](#find-your-spn-and-tenant-id)을 참조하세요. |
      | `properties.roleDefinitionId` | `/providers/Microsoft.Billing/billingAccounts/{BillingAccountID}/enrollmentAccounts/196987/billingRoleDefinitions/a0bcee42-bf30-4d1b-926a-48d21664ef71` |

      청구 계정 이름은 API 매개 변수에서 사용한 것과 동일한 매개 변수입니다. EA Portal 및 Azure Portal에 표시되는 등록 ID입니다.

      청구 역할 정의 ID `a0bcee42-bf30-4d1b-926a-48d21664ef71`은 구독 작성자 역할의 것입니다.

1. **실행** 을 선택하여 명령을 시작합니다.

   :::image type="content" source="./media/assign-roles-azure-service-principals/enrollment-account-role-assignments-put-try-it.png" alt-text="등록 계정 역할 할당 - Put 문서의 [사용해 보기] 옵션을 보여주는 스크린샷" lightbox="./media/assign-roles-azure-service-principals/enrollment-account-role-assignments-put-try-it.png" :::

   SPN이 성공적으로 추가되었다는 `200 OK` 응답이 표시됩니다.

이제 SPN을 사용하여 EA API에 자동으로 액세스할 수 있습니다. SPN에는 SubscriptionCreator 역할이 있습니다.

## <a name="troubleshoot"></a>문제 해결

EA 역할을 부여한 Enterprise 애플리케이션 개체 ID를 식별하고 사용해야 합니다. 다른 애플리케이션에서 개체 ID를 사용하는 경우 API 호출이 실패합니다. 올바른 Enterprise 애플리케이션 개체 ID를 사용하고 있는지 확인합니다.

## <a name="next-steps"></a>다음 단계

[Azure EA 포털 관리](ea-portal-administration.md)에 대해 자세히 알아보세요.

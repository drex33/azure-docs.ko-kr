---
title: Azure RBAC for FHIR 서비스 구성 - Azure Healthcare API
description: 이 문서에서는 Azure RBAC for FHIR을 구성하는 방법을 설명합니다.
author: SteveWohl
ms.service: healthcare-apis
ms.topic: tutorial
ms.date: 11/17/2021
ms.author: zxue
ms.openlocfilehash: b43127a483e9935fe2212f85ab730d344815db07
ms.sourcegitcommit: 81a1d2f927cf78e82557a85c7efdf17bf07aa642
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2021
ms.locfileid: "132814740"
---
# <a name="configure-azure-rbac-for-healthcare-apis"></a>의료 API용 Azure RBAC 구성

> [!IMPORTANT]
> Azure Healthcare API는 현재 미리 보기로 제공됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

이 문서에서는 [Azure RBAC(Azure 역할 기반 액세스 제어)를](../role-based-access-control/index.yml) 사용하여 의료 API 데이터 평면에 대한 액세스를 할당하는 방법을 알아봅니다. Azure RBAC는 데이터 평면 사용자가 Azure 구독과 연결된 Azure Active Directory 테넌트에서 관리될 때 데이터 평면 액세스를 할당하는 기본 방법입니다.

Azure Portal 통해 역할 할당을 완료할 수 있습니다. FHIR 서비스와 DICOM 서비스는 서로 다른 애플리케이션 역할을 정의했습니다. 하나 이상의 역할을 추가하거나 제거하여 사용자 액세스 제어를 관리합니다.

## <a name="assign-roles-for-the-fhir-service"></a>FHIR 서비스에 대한 역할 할당

FHIR 데이터 평면에 대한 액세스 권한을 사용자, 서비스 주체 또는 그룹에 부여하려면 Azure Portal FHIR 서비스를 선택합니다. **액세스 제어(IAM)** 를 선택한 **다음, 역할 할당 탭을** 선택합니다. **+추가를** 선택한 다음, **역할 할당 추가를** 선택합니다.
 
역할 할당 옵션이 회색으로 표시됩니다. Azure 구독 관리자에게 구독 또는 리소스 그룹에 대한 사용 권한(예: "사용자 액세스 관리자")을 부여하도록 요청합니다. Azure 기본 제공 역할에 대한 자세한 내용은 [Azure 기본 제공 역할 를 참조하세요.](../role-based-access-control/built-in-roles.md)

[![액세스 제어 역할 할당. ](fhir/media/rbac/role-assignment.png) ](fhir/media/rbac/role-assignment.png#lightbox)

역할 선택에서 FHIR 데이터 평면에 대한 기본 제공 역할 중 하나를 검색합니다(예: "FHIR 데이터 기여자"). 아래에서 다른 역할을 선택할 수 있습니다.

* **FHIR 데이터 판독기:** FHIR 데이터를 읽고 검색할 수 있습니다.
* **FHIR 데이터 기록기:** FHIR 데이터를 읽고 쓰고 일시 삭제할 수 있습니다.
* **FHIR 데이터 내보내기:** 데이터를 읽고 내보낼 수 있습니다($export 연산자).
* **FHIR 데이터 기여자:** 모든 데이터 평면 작업을 수행할 수 있습니다.
* **FHIR 데이터 변환기:** 변환기를 사용하여 데이터 변환을 수행할 수 있습니다.

**선택** 섹션에서 클라이언트 애플리케이션 등록 이름을 입력합니다. 이름이 발견되면 애플리케이션 이름이 나열됩니다. 애플리케이션 이름을 선택한 다음, **저장을** 선택합니다. 

클라이언트 애플리케이션을 찾을 수 없는 경우 애플리케이션 등록을 확인하여 이름이 올바른지 확인합니다. 클라이언트 애플리케이션이 Azure Healthcare API의 FHIR 서비스(여기서는 FHIR 서비스라고 함)가 배포된 동일한 테넌트에서 만들어졌는지 확인합니다.


[![역할 할당을 선택합니다. ](fhir/media/rbac/select-role-assignment.png) ](fhir/media/rbac/select-role-assignment.png#lightbox)

**액세스 제어(IAM)** 메뉴 옵션에서 **역할 할당 탭을** 선택하여 역할 할당을 확인할 수 있습니다.
 
## <a name="assign-roles-for-the-dicom-service"></a>DICOM 서비스에 대한 역할 할당

DICOM 데이터 평면에 대한 액세스 권한을 사용자, 서비스 주체 또는 그룹에 부여하려면 **액세스 제어(IAM)** 블레이드를 선택합니다. 역할 **할당 탭을** 선택하고 **+ 추가를** 선택합니다.

[![dicom 액세스 제어. ](dicom/media/dicom-access-control.png) ](dicom/media/dicom-access-control.png#lightbox)

**역할** 선택에서 DICOM 데이터 평면에 대한 기본 제공 역할 중 하나를 검색합니다.

[![RBAC 역할 할당을 추가합니다. ](dicom/media/rbac-add-role-assignment.png) ](dicom/media/rbac-add-role-assignment.png#lightbox)

다음 중에서 선택할 수 있습니다.

* DICOM 데이터 소유자: DICOM 데이터에 대한 모든 권한.
* DICOM 데이터 판독기: DICOM 데이터를 읽고 검색합니다.

이러한 역할만으로는 충분하지 않은 경우 PowerShell을 사용하여 사용자 지정 역할을 만들 수 있습니다.  사용자 지정 역할을 만드는 자세한 내용은 [Azure PowerShell 사용하여 사용자 지정 역할 만들기를](../role-based-access-control/custom-roles-powershell.md)참조하세요.

**선택** 상자에서 역할을 할당할 사용자, 서비스 주체 또는 그룹을 검색합니다.

> [!NOTE]
> 애플리케이션 또는 다른 도구에서 FHIR 또는 DICOM 서비스에 액세스할 수 없는 경우 역할 할당이 시스템에서 전파를 완료할 때까지 몇 분 정도 더 기다려야 할 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 FHIR 서비스 및 DICOM 서비스에 Azure 역할을 할당하는 방법을 배웠습니다. Postman을 사용하여 의료 API에 액세스하는 방법을 알아보려면 다음을 참조하세요.

- [Postman을 사용하여 액세스](use-postman.md)
- [REST 클라이언트를 사용하여 액세스](using-rest-client.md)
- [cURL을 사용하여 액세스](using-curl.md)

---
title: DICOM 서비스에 대 한 Azure RBAC 구성-Azure 의료 Api
description: 이 문서에서는 DICOM 서비스에 대해 Azure RBAC를 구성 하는 방법을 설명 합니다.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: how-to
ms.date: 07/13/2020
ms.author: aersoy
ms.openlocfilehash: 7b414fcd5c42ccfec48b3e17d8bfe1bad27ec952
ms.sourcegitcommit: 81a1d2f927cf78e82557a85c7efdf17bf07aa642
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/19/2021
ms.locfileid: "132814737"
---
# <a name="configure-azure-rbac-for-the-dicom-service"></a>DICOM 서비스에 대 한 Azure RBAC 구성

> [!IMPORTANT]
> Azure 의료 Api는 현재 미리 보기로 제공 됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다. 

이 문서에서는 azure [RBAC (역할 기반 액세스 제어)](../../role-based-access-control/index.yml) 를 사용 하 여 DICOM 서비스에 대 한 액세스 권한을 할당 하는 방법에 대해 설명 합니다. 

## <a name="assign-roles"></a>역할 할당

사용자, 서비스 사용자 또는 그룹에 DICOM 데이터 평면에 대 한 액세스 권한을 부여 하려면 **액세스 제어 (IAM)** 블레이드를 선택 합니다. **역할 할당** 탭을 선택 하 고 **+ 추가** 를 선택 합니다.

[![dicom 액세스 제어입니다. ](media/dicom-access-control.png) ](media/dicom-access-control.png#lightbox)


**역할** 선택에서 DICOM 데이터 평면의 기본 제공 역할 중 하나를 검색 합니다.

[![RBAC 역할 할당을 추가 합니다. ](media/rbac-add-role-assignment.png) ](media/rbac-add-role-assignment.png#lightbox)

다음 중에서 선택할 수 있습니다.

* DICOM 데이터 소유자: DICOM 데이터에 대 한 모든 권한입니다.
* DICOM 데이터 판독기: DICOM 데이터를 읽고 검색 합니다.

이러한 역할이 필요에 충분 하지 않은 경우 PowerShell을 사용 하 여 사용자 지정 역할을 만들 수 있습니다.  사용자 지정 역할을 만드는 방법에 대 한 자세한 내용은 [Azure PowerShell를 사용 하 여 사용자 지정 역할 만들기](../../role-based-access-control/tutorial-custom-role-powershell.md)를 참조 하세요.

**선택** 상자에서 역할을 할당 하려는 사용자, 서비스 사용자 또는 그룹을 검색 합니다.

## <a name="caching-behavior"></a>캐싱 동작

DICOM 서비스는 최대 5 분 동안 결정을 캐시 합니다. 사용자에 게 허용 되는 개체 Id 목록에 추가 하 여 DICOM 서비스에 대 한 액세스 권한을 부여 하거나 목록에서 해당 서비스를 제거 하는 경우 변경 내용이 전파 되는 데 최대 5 분이 걸릴 수 있습니다.

## <a name="next-steps"></a>다음 단계

이 문서에서는 DICOM 서비스 데이터 평면에 대 한 Azure 역할을 할당 하는 방법을 배웠습니다. 
 
>[!div class="nextstepaction"]
>[DICOM 서비스 개요](dicom-services-overview.md)

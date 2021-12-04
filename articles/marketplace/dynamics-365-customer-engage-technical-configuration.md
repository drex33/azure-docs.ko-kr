---
title: Dataverse에서 Dynamics 365 앱 설정 및 Microsoft AppSource 기술 구성 제공 Power Apps - Azure Marketplace
description: Dataverse에서 Dynamics 365 앱을 설정하고 Power Apps Microsoft AppSource(Azure Marketplace 기술 구성을 제공합니다.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
ms.custom: references_regions
author: vamahtan
ms.author: vamahtan
ms.date: 12/03/2021
ms.openlocfilehash: ec7b41e5ecbcfe75e98623760d4244570c22e538
ms.sourcegitcommit: 1e9139680ca51f55ac965c4dd6dd82bf2fd43675
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/04/2021
ms.locfileid: "133544061"
---
# <a name="set-up-dynamics-365-apps-on-dataverse-and-power-apps-offer-technical-configuration"></a>Dataverse에서 Dynamics 365 앱 설정 및 기술 구성 제공 Power Apps

이 페이지에서는 제품에 연결하는 데 사용되는 기술 세부 정보를 정의합니다. 이 연결을 통해 제품 구매를 선택하는 고객을 위해 제품을 프로비저닝할 수 있습니다.

## <a name="offer-information"></a>제품 정보

**기본 라이선스 모델** 은 CRM 관리 센터에서 고객에게 애플리케이션을 할당받는 방법을 결정합니다. 다음 중 하나를 수행합니다.

- 인스턴스 기반 라이선스에 대한 **리소스** 를 선택합니다.
- 라이선스가 테넌트당 하나씩 할당되거나 Microsoft를 통해 앱 라이선스를 관리하도록 선택한 경우 **사용자** 를 선택합니다.

**S2S 아웃바운드 및 CRM 보안 저장소 액세스 필요** 확인란을 사용하여 CRM 보안 저장소 또는 S2S(서버 간) 아웃바운드 액세스 구성을 사용하도록 설정합니다. 이 기능을 사용하려면 Dynamics 365 팀에서 인증 단계 중에 특별히 고려해야 합니다. Microsoft는 이 기능을 지원하기 위한 추가 단계를 수행하도록 요청하는 알림을 보냅니다.

**애플리케이션 구성 URL** 은 비워 둡니다. 나중에 사용하기 위한 것입니다.

## <a name="crm-package"></a>CRM 패키지

**패키지 위치의 URL** 상자에 업로드된 CRM 패키지 .zip 파일이 포함된 Azure Blob Storage 계정의 URL을 입력합니다. Microsoft에서 확인을 위해 패키지를 선택할 수 있도록 이 URL에 읽기 전용 SAS 키를 포함합니다.

> [!IMPORTANT]
> 게시 차단을 방지하려면 Blob 스토리지 URL의 만료 날짜가 만료되지 않았는지 확인합니다. 정책에 액세스하여 날짜를 수정할 수 있습니다. **만료 시간** 은 적어도 앞으로 1달 이후로 지정하는 것이 좋습니다.

해당하는 경우 **내 패키지 파일에 둘 이상의 CRM 패키지가 있습니다.** 상자를 선택합니다. 이렇게 한 경우 .zip 파일에 모든 패키지를 포함해야 합니다.

패키지를 작성하고 해당 구조를 업데이트하는 방법에 대한 자세한 내용은 [3단계: 앱에 대한 AppSource 패키지 만들기](/powerapps/developer/common-data-service/create-package-app-appsource)를 참조하세요.

## <a name="crm-package-availability"></a>CRM 패키지 가용성

**+ 지역 추가** 를 선택하여 CRM 패키지를 고객에게 제공할 지리적 지역을 지정합니다. US Gov Cloud 소버린, 중국 Cloud 소버린, 독일 소버린, US Gov High Cloud 소버린, 프로덕션 소버린 테스트 또는 US DoD Cloud 소버린과 같은 소버린 지역을 선택하지 마세요.

기본적으로 위에 입력한 **애플리케이션 구성 URL** 이 각 지역에 사용됩니다. 애플리케이션 구성 URL 필드를 비워 둡니다.

왼쪽 탐색 메뉴 **Microsoft와 공동 판매** 에서 다음 탭을 계속 진행하려면 **초안 저장** 을 선택하세요. Microsoft와 공동 판매를 설정하는 방법(선택 사항)에 관한 자세한 내용은 [Microsoft 영업 팀 및 파트너와 공동 판매 개요](./co-sell-overview.md)를 참조하세요. 공동 판매를 설정하지 않거나 완료한 경우 아래 **다음 단계** 를 계속 진행합니다.

## <a name="next-steps"></a>다음 단계

- [추가 콘텐츠 구성](dynamics-365-customer-engage-supplemental-content.md)
---
title: Azure 독일에서 글로벌 Azure로 마이그레이션
description: Azure 독일에서 글로벌 Azure로 Azure 리소스를 마이그레이션하는 방법을 소개합니다.
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfmigrate
ms.openlocfilehash: 89a65c133a2478b38755b05a8c2fe5d1bb6623a7
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131081299"
---
# <a name="overview-of-migration-guidance-for-azure-germany"></a>Azure 독일에 대한 마이그레이션 지침 개요

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

이 섹션의 문서는 Azure 독일에서 글로벌 Azure로 워크로드를 마이그레이션하는 데 도움이 되도록 작성되었습니다. [Azure 마이그레이션 센터](https://azure.microsoft.com/migration/)는 리소스 마이그레이션에 도움이 되는 도구를 제공하지만 Azure 마이그레이션 센터의 일부 도구는 동일한 테넌트 또는 동일한 지역에서 발생하는 마이그레이션에만 유용합니다.

독일의 두 지역은 글로벌 Azure와 완전히 별개입니다. 글로벌 Azure 및 독일의 클라우드에는 별도의 Azure AD(Azure Active Directory) 인스턴스가 있습니다. 이 때문에 Azure 독일 테넌트는 글로벌 Azure 테넌트와 별개입니다. 이 문서에서는 *다른* 테넌트 간에 마이그레이션할 때 사용할 수 있는 마이그레이션 도구에 대해 설명합니다.

ID/테넌트에 대한 지침은 Azure 전용 고객을 대상으로 합니다. Azure 및 Microsoft 365(또는 기타 Microsoft 제품)용 Azure AD(Azure Active Directory) 테넌트를 사용하는 경우 ID 마이그레이션이 복잡하므로 먼저 [마이그레이션 단계 작업 및 Microsoft 클라우드 독일에서 마이그레이션에 대한 영향](/microsoft-365/enterprise/ms-cloud-germany-transition-phases)을 읽어야 합니다. 질문이 있는 경우 계정 관리자 또는 Microsoft 지원에 문의하세요.

Azure 클라우드 솔루션 공급자는 새로운 독일 데이터 센터 지역으로 전환하는 동안 및 이후에 고객을 지원하기 위해 추가 단계를 수행해야 합니다. [추가 단계](/microsoft-365/enterprise/ms-cloud-germany-transition-add-csp)에 대해 자세히 알아봅니다.

## <a name="migration-process"></a>마이그레이션 프로세스

Azure 독일에서 글로벌 Azure로 워크로드를 마이그레이션하는 데 사용하는 프로세스는 일반적으로 애플리케이션을 클라우드로 마이그레이션하는 데 사용되는 프로세스와 유사합니다. 마이그레이션 프로세스의 단계는 다음과 같습니다.

![마이그레이션 프로세스의 4단계(평가, 계획, 마이그레이션, 유효성 검사)를 보여 주는 이미지](./media/germany-migration-main/migration-steps.png)

### <a name="assess"></a>평가

Azure 계정 소유자, 구독 관리자, 테넌트 관리자, 재무 및 회계 팀을 통합하여 조직의 Azure 독일 공간을 이해하는 것이 중요합니다. 이러한 역할을 수행하는 사람들은 대규모 조직의 Azure 사용량에 대한 전체 그림을 제공할 수 있습니다.

평가 단계에서 리소스 인벤토리를 컴파일합니다.
  - 각 구독 관리자 및 테넌트 관리자는 일련의 스크립트를 실행하여 리소스 그룹, 각 리소스 그룹의 리소스 및 리소스 그룹 배포 설정을 나열해야 합니다.
  - Azure 및 외부 시스템의 애플리케이션 간 종속성을 문서화합니다.
  - 각 Azure 리소스의 수와 마이그레이션하려는 각 인스턴스와 연결된 데이터의 양을 문서화합니다.
  - 애플리케이션 아키텍처 문서가 Azure 리소스 목록과 일치하는지 확인합니다.

이 단계가 끝나면 다음을 수행할 수 있습니다.

- 사용 중인 Azure 리소스의 전체 목록입니다.
- 리소스 간의 종속성 목록입니다.
- 마이그레이션 작업의 복잡성에 대한 정보입니다.

### <a name="plan"></a>계획

계획 단계에서 다음 작업을 완료합니다.

- 평가 단계에서 완료된 종속성 분석의 출력을 사용하여 관련 구성 요소를 정의합니다. *마이그레이션 패키지* 에서 관련 구성 요소를 함께 마이그레이션하는 것이 좋습니다.
- (선택 사항) 마이그레이션을 [Gartner 5-R 기준](https://www.gartner.com/en/documents/3873016/evaluation-criteria-for-cloud-management-platforms-and-t)을 적용하고 워크로드를 최적화할 수 있는 기회로 사용합니다.
- 글로벌 Azure에서 대상 환경을 결정합니다.
  1. 대상 글로벌 Azure 테넌트를 식별합니다(필요한 경우 만듦).
  1. 구독을 만듭니다.
  1. 마이그레이션할 글로벌 Azure 위치를 선택합니다.
  1. Azure 독일의 아키텍처와 글로벌 Azure의 아키텍처를 일치시키는 테스트 마이그레이션 시나리오를 실행합니다.
- 마이그레이션에 대한 적절한 타임라인과 일정을 결정합니다. 각 마이그레이션 패키지에 대한 사용자 승인 테스트 계획을 만듭니다.

### <a name="migrate"></a>Migrate

마이그레이션 단계에서 Azure 독일 마이그레이션 문서에 설명된 도구, 기술 및 권장 사항을 사용하여 글로벌 Azure에서 새 리소스를 만듭니다. 그런 다음 애플리케이션을 구성합니다.

### <a name="validate"></a>유효성 검사

유효성 검사 단계에서 다음 작업을 완료합니다.

1. 사용자 승인 테스트를 수행합니다.
1. 애플리케이션이 예상대로 작동하는지 확인합니다.
1. 해당하는 경우 최신 데이터를 대상 환경과 동기화합니다.
1. 글로벌 Azure에서 새 애플리케이션 인스턴스로 전환합니다.
1. 프로덕션 환경이 예상대로 작동하는지 확인합니다.
1. Azure 독일에서 리소스를 서비스 해제합니다.

## <a name="terms"></a>항

다음 용어는 Azure 독일 마이그레이션 문서에서 사용됩니다.

**원본** 은 리소스를 마이그레이션하는 대상 위치(예: Azure 독일)를 설명합니다.

- **원본 테넌트 이름**: Azure 독일의 테넌트 이름(계정 이름에서 **\@** 뒤의 모든 내용)입니다. Azure 독일의 테넌트 이름은 모두 **microsoftazure.de** 로 끝납니다.
- **원본 테넌트 ID**: Azure 독일에 있는 테넌트의 ID입니다. 오른쪽 위 모서리에 있는 계정 이름 위로 마우스를 이동하면 Azure Portal에 테넌트 ID가 나타납니다.
- **원본 구독 ID**: Azure 독일의 리소스 구독 ID입니다. 동일한 테넌트에 둘 이상의 구독이 있을 수 있습니다. 항상 올바른 구독을 사용하고 있는지 확인합니다.
- **원본 지역**: 마이그레이션할 리소스의 위치에 따라 독일 중부(**germanycentral**) 또는 독일 북동부(**germanynortheast**)입니다.

**대상(Target)** 또는 **대상(Destination)** 은 리소스를 다음으로 마이그레이션할 위치를 설명합니다.

- **대상 테넌트 이름**: 글로벌 Azure의 테넌트 이름입니다.
- **대상 테넌트 ID**: 글로벌 Azure의 테넌트 ID입니다.
- **대상 구독 ID**: 글로벌 Azure의 리소스에 대한 구독 ID입니다.
- **대상 지역**: 글로벌 Azure의 거의 모든 지역을 사용할 수 있습니다. 리소스를 서유럽(**westeurope**) 또는 북유럽(**northeurope**)으로 마이그레이션하려고 합니다.

> [!NOTE]
> 마이그레이션하는 Azure 서비스가 대상 지역에서 제공되는지 확인합니다. Azure 독일에서 사용할 수 있는 모든 Azure 서비스는 서유럽에서 사용할 수 있습니다. azure 독일에서 사용할 수 있는 모든 azure 서비스는 azure Virtual Machines에서 Machine Learning Studio (클래식) 및 G/GS VM 시리즈를 제외 하 고는 유럽에서 사용할 수 있습니다.

브라우저에서 원본 및 대상 포털을 북마크에 추가하는 것이 좋습니다.

- Azure 독일 포털은 [https://portal.microsoftazure.de/](https://portal.microsoftazure.de/)에 있습니다.
- 글로벌 Azure Portal은 [https://portal.azure.com/](https://portal.azure.com/)에 있습니다.

## <a name="next-steps"></a>다음 단계

다음 서비스 범주에서 리소스 마이그레이션을 위한 도구, 기술 및 권장 사항에 대해 알아봅니다.

- [컴퓨팅](./germany-migration-compute.md)
- [네트워킹](./germany-migration-networking.md)
- [스토리지](./germany-migration-storage.md)
- [Web](./germany-migration-web.md)
- [데이터베이스](./germany-migration-databases.md)
- [분석](./germany-migration-analytics.md)
- [IoT](./germany-migration-iot.md)
- [통합](./germany-migration-integration.md)
- [ID](./germany-migration-identity.md)
- [보안](./germany-migration-security.md)
- [관리 도구](./germany-migration-management-tools.md)
- [미디어](./germany-migration-media.md)

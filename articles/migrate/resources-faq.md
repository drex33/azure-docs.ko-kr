---
title: Azure Migrate FAQ
description: Azure Migrate 서비스와 관련된 일반적인 질문에 대한 답변입니다.
author: ms-psharma
ms.author: panshar
ms.manager: abhemraj
ms.topic: conceptual
ms.date: 04/15/2020
ms.openlocfilehash: 87dc84b7f73fe95f7ba62dcdef63816753cc233e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "122566705"
---
# <a name="azure-migrate-common-questions"></a>Azure Migrate: 일반적인 질문

이 문서에서는 Azure Migrate에 대한 일반적인 질문의 답변을 제공합니다. 이 문서를 읽은 후 문의 사항이 있는 경우 [Azure Migrate 포럼](https://aka.ms/AzureMigrateForum)에 게시할 수 있습니다. 또한 다음 문서를 검토할 수 있습니다.

- [Azure Migrate 어플라이언스](common-questions-appliance.md)에 대한 질문
- [검색, 평가, 종속성 시각화](common-questions-discovery-assessment.md)에 대한 질문

## <a name="what-is-azure-migrate"></a>Azure Migrate란?

Azure Migrate는 온-프레미스 앱과 워크로드 및 프라이빗과 퍼블릭 클라우드 VM의 검색, 평가, 마이그레이션을 추적할 수 있는 중앙 허브를 Azure에 제공합니다. 이 허브는 평가 및 마이그레이션에 사용되는 Azure Migrate 도구뿐만 아니라 타사 ISV(독립 소프트웨어 공급업체) 제품도 제공합니다. [자세히 알아봅니다](migrate-services-overview.md).

## <a name="what-can-i-do-with-azure-migrate"></a>Azure Migrate로 어떤 작업을 할 수 있나요?

Azure Migrate는 온-프레미스 인프라, 애플리케이션 및 데이터를 Azure로 검색하고, 평가하며, 마이그레이션할 수 있습니다. Azure Migrate는 온-프레미스 VMware VM, Hyper-V VM, 물리적 서버, 기타 가상화된 VM, 데이터베이스, 웹앱, 가상 데스크톱의 평가 및 마이그레이션을 지원합니다.

## <a name="whats-the-difference-between-azure-migrate-and-azure-site-recovery"></a>Azure Migrate와 Azure Site Recovery의 차이점은 무엇인가요?

[Azure Migrate](migrate-services-overview.md)는 Azure에 평가와 마이그레이션을 위한 중앙화된 허브를 제공합니다.

- Azure Migrate를 사용하여 Azure Migrate 도구, 다른 Azure 서비스 및 타사 도구와의 상호 운용성 및 향후의 확장성을 제공합니다.
- Azure Migrate: 서버 마이그레이션 도구는 Azure로의 서버 마이그레이션을 위해 개발되었습니다. 마이그레이션에 최적화가 되어 있습니다. 마이그레이션과 직접적인 관련이 없는 개념이나 시나리오는 몰라도 됩니다.
- VM에 복제가 시작된 시간부터 180일 간은 마이그레이션에 대한 도구 사용 요금이 무료입니다. 이 시간 동안 마이그레이션을 완료할 수 있습니다. 복제에 사용된 스토리지와 네트워크 리소스, 그리고 테스트 마이그레이션 중 사용된 컴퓨팅 요금에 대해서만 비용을 청구합니다.
- Azure Migrate은 Site Recovery에서 지원되는 모든 마이그레이션 시나리오를 지원합니다. 또한 VMware VM의 경우 Azure Migrate는 에이전트 없는 마이그레이션 옵션을 제공합니다.
- Azure Migrate:Server Migration 도구 전용인 새로운 마이그레이션 기능에 우선 순위를 두고 있습니다. 해당 기능은 Site Recovery 대상이 아닙니다.

[Azure Site Recovery](../site-recovery/site-recovery-overview.md)는 재해 복구에만 사용되어야 합니다.

Azure Migrate: Server Migration 도구는 일부 온-프레미스 머신의 리프트 앤 시프트 마이그레이션에 백 엔드 Site Recovery 기능을 사용합니다.

## <a name="i-have-a-project-with-the-previous-classic-experience-of-azure-migrate-how-do-i-start-using-the-new-version"></a>Azure Migrate의 이전 클래식 환경을 갖춘 프로젝트가 있습니다. 새 버전을 어떻게 사용해야 하나요?

클래식 Azure Migrate는 2024년 2월에 만료됩니다. 2024년 2월 이후에는 클래식 버전의 Azure Migrate는 더 이상 지원되지 않으며 클래식 프로젝트의 인벤토리 메타데이터가 삭제됩니다. 이전 버전의 프로젝트나 구성 요소를 새 버전으로 업그레이드할 수 없습니다. [새 Azure Migrate 프로젝트를 생성](create-manage-projects.md)하고 [평가 및 마이그레이션 도구를 추가](./create-manage-projects.md)해야 합니다. 사용 가능한 평가 및 마이그레이션 도구를 사용하는 방법을 이해하려면 자습서를 사용하세요. 클래식 프로젝트에 연결된 Log Analytics 작업 영역이 있는 경우 클래식 프로젝트를 삭제한 후에 현재 버전의 프로젝트에 연결할 수 있습니다.

## <a name="whats-the-difference-between-azure-migrate-discovery-and-assessment-and-the-map-toolkit"></a>Azure Migrate: 검색 및 평가와 MAP 도구 키트의 차이점은 무엇인가요?

서버 평가는 마이그레이션 준비에 도움을 주는 평가와 Azure로 마이그레이션하기 위한 워크로드 평가를 제공합니다. [MAP(Microsoft Assessment and Planning) Toolkit](https://www.microsoft.com/download/details.aspx?id=7826)는 최신 버전의 Windows 클라이언트 및 서버 운영 체제에 대한 마이그레이션 계획과 소프트웨어 사용량 추적과 같은 다른 작업도 지원합니다. 해당 시나리오의 경우 MAP Toolkit을 계속 사용하세요.

## <a name="whats-the-difference-between-server-assessment-and-the-site-recovery-deployment-planner"></a>Server Assessment와 Site Recovery Deployment Planner의 차이점은 무엇인가요?

Server Assessment는 마이그레이션 계획 도구입니다. Site Recovery Deployment Planner는 재해 복구 계획 도구입니다.

수행할 작업에 맞춰 도구를 선택하세요.

- **Azure로 온-프레미스 마이그레이션 플랜**: 온-프레미스 서버를 Azure로 마이그레이션하려면 마이그레이션 계획에 Server Assessment를 사용하세요. Server Assessment는 온-프레미스 워크로드를 평가하고 마이그레이션에 도움이 되는 지침 및 도구를 제공합니다. 마이그레이션 플랜이 준비되면 Azure Migrate: Server Migration과 같은 도구를 사용하여 머신을 Azure로 마이그레이션할 수 있습니다.
- **Azure로 재해 복구 플랜**: Site Recovery를 사용하여 온-프레미스에서 Azure로 재해 복구 설정을 계획하고 있다면 Site Recovery Deployment Planner를 사용하세요. Deployment Planner는 재해 복구를 위해 온-프레미스 환경에 대한 심층적인 Site Recovery 관련 평가를 제공합니다. 복제 및 장애 조치(failover)와 같은 재해 복구와 관련된 권장 사항을 제공합니다.

## <a name="how-does-server-migration-work-with-site-recovery"></a>Server Migration은 Site Recovery와 어떻게 함께 작동하나요?

- Azure Migrate: Server Migration을 사용하여 온-프레미스 VMware VM의 *에이전트 없는* 마이그레이션을 수행하는 경우 마이그레이션은 Azure Migrate이 기본이며 Site Recovery는 사용되지 않습니다.
- Azure Migrate: Server Migration을 사용하여 VMware VM의 *에이전트 기반* 마이그레이션을 수행하거나 Hyper-V VM 또는 물리적 서버를 마이그레이션하는 경우, Azure Migrate: Server Migration은 Azure Site Recovery 복제 엔진을 사용합니다.

## <a name="which-geographies-are-supported"></a>어떤 지역에서 지원되나요?

[퍼블릭](migrate-support-matrix.md#supported-geographies-public-cloud) 및 [정부 클라우드](migrate-support-matrix.md#supported-geographies-azure-government)에 대해 지원되는 지역을 검토합니다.

## <a name="how-do-i-get-started"></a>시작하려면 어떻게 해야 하나요?

필요한 도구를 식별하고, 이를 Azure Migrate 프로젝트에 추가합니다.

ISV 도구 또는 Movere를 추가하는 경우 다음을 수행합니다.

1. 도구 정책에 따라 라이선스를 얻거나 평가판에 가입하여 시작할 수 있습니다. 도구 라이선스는 ISV 또는 도구 라이선스 모델을 따릅니다.
2. 각 도구에는 Azure Migrate에 연결할 수 있는 옵션이 있습니다. 도구 지침 및 설명서에 따라 도구를 Azure Migrate에 연결합니다.

Azure Migrate 프로젝트 내에서 Azure 및 기타 도구를 통해 마이그레이션 경험을 추적할 수 있습니다.

## <a name="how-do-i-delete-a-project"></a>프로젝트를 삭제하려면 어떻게 하야 하나요?

[프로젝트 삭제](how-to-delete-project.md) 방법에 대해 알아봅니다.

## <a name="next-steps"></a>다음 단계

[Azure Migrate 개요](migrate-services-overview.md)를 참조하세요.
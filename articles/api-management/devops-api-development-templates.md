---
title: ARM 템플릿을 사용하는 Azure API Management에 대한 CI/CD
description: Azure Resource Manager 템플릿을 사용하여 CI/CD 파이프라인에서 API 배포를 관리하는, Azure API Management를 사용하는 API DevOps 소개
services: api-management
author: dlepow
ms.service: api-management
ms.topic: conceptual
ms.date: 10/09/2020
ms.author: danlep
ms.openlocfilehash: 7e1ea1b11304e43a74659221c352f207856369c8
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128665761"
---
# <a name="cicd-for-api-management-using-azure-resource-manager-templates"></a>Azure Resource Manager 템플릿을 사용하는 API Management에 대한 CI/CD

이 문서에서는 Azure Resource Manager 템플릿을 통해 Azure API Management에서 API DevOps를 사용하는 방법을 보여줍니다. 전략적 가치를 갖춘 API의 CI/CD (연속 통합 및 지속적인 업데이트) 파이프라인은 API 개발의 중요한 측면입니다. 이를 통해 조직은 오류가 발생하기 쉬운 수동 단계 없이 API 변경 배포를 자동화하고, 이전에 문제를 검색하고, 궁극적으로 사용자에게 가치를 더 빠르게 제공할 수 있습니다. 

이 문서에서 설명하는 DevOps 방식을 구현하는 세부 정보, 도구, 코드 샘플은 GitHub의 오픈 소스 [Azure API Management DevOps Resource Kit](https://github.com/Azure/azure-api-management-devops-resource-kit)를 참조하세요. 고객은 다양한 엔지니어링 문화권과 기존 자동화 솔루션을 사용하기 때문에, 이 방식은 모든 경우에 적합한 솔루션이 아닙니다.

## <a name="the-problem"></a>문제

현재 조직에는 일반적으로 여러 배포 환경(개발, 테스트, 프로덕션 등)이 있으며 각 환경에 대해 별도의 API Management 인스턴스를 사용합니다. 일부 인스턴스는 다양한 릴리스 상황의 다양한 API를 담당하는 여러 개발 팀에서 공유합니다.

따라서 고객은 다음과 같은 문제를 직면하게 됩니다.

* API Management로 API 배포를 자동화하는 방법
* 한 환경에서 다른 환경으로 구성을 마이그레이션하는 방법
* 동일한 API Management 인스턴스를 공유하는 다양한 개발 팀 간의 간섭을 방지하는 방법

## <a name="manage-configurations-in-resource-manager-templates"></a>Resource Manager 템플릿에서 구성 관리

다음 이미지는 추천하는 방법을 설명합니다. 

:::image type="content" source="media/devops-api-development-templates/apim-devops.png" alt-text="API Management를 사용하여 DevOps를 설명하는 다이어그램":::

이 예제에는 *개발* 및 *프로덕션* 이라는 두 가지 배포 환경이 있습니다. 각 환경에는 자체 API Management 인스턴스가 있습니다. 

* API 개발자는 개발 인스턴스에 액세스하여 API를 개발하고 테스트하는 데 사용할 수 있습니다. 
* *API 게시자* 를 호출하는 지정된 팀은 프로덕션 인스턴스를 관리합니다.

이 제안된 방법의 핵심은 [Azure Resource Manager 템플릿의](../azure-resource-manager/templates/syntax.md)모든 API Management 구성을 유지하는 것입니다. 조직에서는 이러한 템플릿을 Git와 같은 원본 제어 시스템에 보관해야 합니다. 이미지에 설명된 것처럼 게시자 리포지토리에는 템플릿 컬렉션에 있는 프로덕션 API Management 인스턴스의 모든 구성이 포함됩니다.

|템플릿  |Description  |
|---------|---------|
|서비스 템플릿     | 가격 책정 계층 및 사용자 지정 도메인과 같은 API Management 인스턴스의 서비스 수준 구성입니다.         |
|공유 템플릿     |  그룹, 제품, 로거와 같은 API Management 인스턴스 전체에서 리소스를 공유합니다.    |
|API 템플릿     |  API 및 해당 하위 리소스 구성: 작업, 정책, 진단 설정        |
|마스터 (주) 템플릿     |   모든 템플릿을 [연결](../azure-resource-manager/templates/linked-templates.md)하고 순서대로 배포하여 모든 항목을 함께 연결합니다. 모든 구성을 API Management 인스턴스에 배포하려면 주 템플릿을 배포합니다. 각 템플릿을 개별적으로 배포할 수도 있습니다.       |

API 개발자는 개발자 리포지토리로 게시자 리포지토리를 포크하고 해당 API에 대한 변경 작업을 수행합니다. 대부분의 경우 API에 대한 API 템플릿에 집중하고, 공유 또는 서비스 템플릿을 변경할 필요가 없습니다.

## <a name="migrate-configurations-to-templates"></a>템플릿으로 구성 마이그레이션
API 개발자는 Resource Manager 템플릿으로 작업하는 경우 문제에 직면합니다.

* API 개발자는 [OpenAPI 사양](https://github.com/OAI/OpenAPI-Specification)에서 작업하는 경우가 많으며 Resource Manager 스키마에 익숙하지 않을 수 있습니다. 수동으로 템플릿을 작성하면 오류가 발생할 수 있습니다. 

   리소스 키트의 [만든 이](https://github.com/Azure/azure-api-management-devops-resource-kit/blob/master/src/APIM_ARMTemplate/README.md#Creator)라는 도구는 개방형 API 사양 파일을 기반으로 API 템플릿 만들기를 자동화하는 데 도움이 됩니다. 또한 개발자는 API에 대한 API Management 정책을 XML 형식으로 제공할 수 있습니다. 

* 이미 API Management를 사용하고 있는 고객의 경우에는 Resource Manager 템플릿으로 기존 구성을 추출하는 것이 좋습니다. 이러한 고객의 경우 리소스 키트의 [추출기](https://github.com/Azure/azure-api-management-devops-resource-kit/blob/master/src/APIM_ARMTemplate/README.md#extractor)라는 도구를 통해 API Management 인스턴스에서 구성을 추출하여 템플릿을 생성할 수 있습니다.  

## <a name="workflow"></a>워크플로

* API 개발자가 API 개발 및 테스트를 완료하고 API 템플릿을 생성한 후에는 변경 내용을 게시자 리포지토리에 병합하기 위해 끌어오기 요청을 제출할 수 있습니다. 

* API 게시자는 끌어오기 요청의 유효성을 검사하고 변경 내용이 안전하고 규정을 준수하는지 확인할 수 있습니다. 예를 들어 HTTPS만 API와 통신할 수 있는지 확인할 수 있습니다. 대부분의 유효성 검사는 CI/CD 파이프라인의 단계로 자동화할 수 있습니다.

* 변경 내용이 승인되고 병합되면 API 게시자는 일정에 따라 또는 주문형으로 이를 프로덕션 인스턴스에 배포하도록 선택할 수 있습니다. [GitHub 작업](https://docs.github.com/en/actions), [Azure Pipelines](/azure/devops/pipelines), [Azure PowerShell](../azure-resource-manager/templates/deploy-powershell.md), [Azure CLI](../azure-resource-manager/templates/deploy-cli.md) 또는 기타 도구를 사용하여 템플릿의 배포를 자동화할 수 있습니다.


이 방법을 사용하면 조직에서 API Management 인스턴스에 대한 API 변경 내용의 배포를 자동화할 수 있으며, 한 환경에서 다른 환경으로 쉽게 변경 내용을 승격시킬 수 있습니다. API 개발 팀 마다 다른 API 템플릿 및 파일 집합에 대해 작업을 수행하므로, 서로 다른 팀 간의 간섭을 방지할 수 있습니다.

## <a name="video"></a>비디오

> [!VIDEO https://www.youtube.com/embed/4Sp2Qvmg6j8]

## <a name="next-steps"></a>다음 단계

- 추가 정보, 도구, 샘플 템플릿은 오픈 소스 [Azure API Management DevOps Resource Kit](https://github.com/Azure/azure-api-management-devops-resource-kit)를 참조하세요.

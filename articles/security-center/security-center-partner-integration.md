---
title: Azure Security Center에서 보안 솔루션 통합 | Microsoft Docs
description: Azure Security Center를 파트너와 통합하여 Azure 리소스의 전반적인 보안을 강화하는 방법에 대해 알아봅니다.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 12/10/2020
ms.author: memildin
ms.openlocfilehash: 66f2ca97ce20dc0aeb6b1fcecf691861b645284b
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2021
ms.locfileid: "129706706"
---
# <a name="integrate-security-solutions-in-azure-security-center"></a>Azure Security Center에서 보안 솔루션 통합
이 문서를 통해 이미 Azure Security Center에 연결된 보안 솔루션을 관리하고 새로 추가할 수 있습니다.

## <a name="integrated-azure-security-solutions"></a>통합된 Azure 보안 솔루션
Security Center를 사용하면 Azure에서 통합된 보안 솔루션을 쉽게 사용할 수 있습니다. 이점은 다음과 같습니다.

- **배포 간소화**: Security Center에서는 통합된 파트너 솔루션의 간결한 프로비전을 제공합니다. 맬웨어 방지 및 취약성 평가와 같은 솔루션의 경우 Security Center는 가상 머신에서 에이전트를 프로비전할 수 있습니다. 방화벽 어플라이언스의 경우 Security Center가 필요한 네트워크 구성의 대부분을 처리할 수 있습니다.
- **통합된 감지**: 파트너 솔루션의 보안 이벤트는 자동으로 수집, 집계되며 Security Center 알림 및 사고의 일부로 표시됩니다. 또한 이러한 이벤트는 다른 원본의 감지를 결합하여 고급 위협 감지 기능을 제공합니다.
- **통합된 상태 모니터링 및 관리**: 고객은 통합된 상태 이벤트를 사용하여 한 눈에 모든 파트너 솔루션을 모니터링할 수 있습니다. 기본 관리는 파트너 솔루션을 사용하여 고급 설정에 쉽게 액세스하여 사용할 수 있습니다.

현재 통합 보안 솔루션에는 [Qualys](https://www.qualys.com/public-cloud/#azure) 및 [Rapid7](https://www.rapid7.com/products/insightvm/) 및 [Azure Application Gateway의 Microsoft Azure Web Application Firewall](../web-application-firewall/ag/ag-overview.md)의 취약성 평가가 포함됩니다.

> [!NOTE]
> 대부분의 보안 공급업체가 자신들의 어플라이언스에서 외부 에이전트를 실행하는 것을 금지하고 있기 때문에 Security Center는 파트너 가상 어플라이언스에 Log Analytics 에이전트를 설치하지 않습니다.

Azure Defender 고객에게 제공되는 기본 제공 스캐너를 포함하여 Qualys의 취약성 검사 도구 통합에 대한 자세한 내용은 [Azure 및 하이브리드 머신용 Azure Defender 통합 Qualys 취약성 스캐너를 참조하세요.](deploy-vulnerability-assessment-vm.md)

Security Center는 다음에 대한 취약성 분석도 제공합니다.

* SQL 데이터베이스 - [취약성 평가 대시보드에서 취약성 평가 보고서 탐색](defender-for-sql-on-machines-vulnerability-assessment.md#explore-vulnerability-assessment-reports)을 참조하세요.
* Azure Container Registry 이미지 - [컨테이너 레지스트리용 Azure Defender를 사용하여 이미지에서 취약성 검사](defender-for-container-registries-usage.md) 참조

## <a name="how-security-solutions-are-integrated"></a>보안 솔루션을 통합하는 방법
Security Center에서 배포된 Azure 보안 솔루션은 자동으로 연결됩니다. 온-프레미스 또는 다른 클라우드에서 실행되는 컴퓨터를 포함하여 다른 보안 데이터 원본에 연결할 수도 있습니다.

:::image type="content" source="./media/security-center-partner-integration/security-solutions-page.png" alt-text="파트너 솔루션 통합" lightbox="./media/security-center-partner-integration/security-solutions-page.png":::

## <a name="manage-integrated-azure-security-solutions-and-other-data-sources"></a>통합된 Azure 보안 솔루션 및 다른 데이터 원본 관리

1. [Azure Portal](https://azure.microsoft.com/features/azure-portal/)에서 **Security Center** 를 엽니다.

1. Security Center의 메뉴에서 **보안 솔루션** 을 선택합니다.

**보안 솔루션** 페이지에서 통합된 Azure 보안 솔루션의 상태를 확인하고 기본 관리 작업을 실행할 수 있습니다.

### <a name="connected-solutions"></a>연결된 솔루션

**연결된 솔루션** 섹션에는 현재 Security Center에 연결되어 있는 보안 솔루션이 포함됩니다. 또한 각 솔루션의 상태를 보여 줍니다.  

![연결된 솔루션](./media/security-center-partner-integration/connected-solutions.png)

파트너 솔루션의 상태는 다음과 같을 수 있습니다.

* **정상**(녹색) - 상태 문제가 없습니다.
* **비정상**(빨강) - 즉각적인 주의가 필요한 상태 문제가 있습니다.
* **중지된 보고**(주황) - 솔루션이 상태의 보고를 중지했습니다.
* **보고되지 않음**(회색) - 솔루션이 아직 아무것도 보고하지 않았으며 상태 데이터를 사용할 수 없습니다. 최근에 연결되었고 여전히 배포 중인 경우 솔루션의 상태가 보고되지 않을 수 있습니다.

> [!NOTE]
> 상태 데이터를 사용할 수 없는 경우 Security Center에 마지막으로 수신된 이벤트의 날짜와 시간을 통해 솔루션이 보고 중인지 여부가 표시됩니다. 상태 데이터를 사용할 수 없으며 지난 14일 이내에 경고가 수신되지 않은 경우 Security Center는 솔루션이 비정상 상태이거나 보고하지 않음을 나타냅니다.
>
>

다음과 같은 추가 정보 및 옵션에 대해서는 **보기** 를 선택합니다.

   - **솔루션 콘솔** - 이 솔루션에 대한 관리 환경을 엽니다.
   - **VM 연결** - 애플리케이션 연결 페이지를 엽니다. 여기서 리소스를 파트너 솔루션에 연결할 수 있습니다.
   - **솔루션 삭제**
   - **구성**

   ![파트너 솔루션 세부 정보](./media/security-center-partner-integration/partner-solutions-detail.png)


### <a name="discovered-solutions"></a>검색된 솔루션

Security Center는 Azure에서 실행되는 보안 솔루션을 자동으로 검색하지만 Security Center에 연결되지 않고 **검색된 솔루션** 섹션에 솔루션을 표시합니다. 이러한 솔루션에는 [Azure AD ID 보호](../active-directory/identity-protection/overview-identity-protection.md)와 같은 Azure 솔루션 및 파트너 솔루션이 포함됩니다.

> [!NOTE]
> 검색된 솔루션 기능에 대해 구독 수준에서 **Azure Defender** 를 사용합니다. [빠른 시작: Azure Defender 사용](enable-azure-defender.md)에서 자세히 알아보세요.

솔루션 아래의 **연결** 을 선택하여 Security Center와 통합하고 보안 경고의 알림을 받습니다.

### <a name="add-data-sources"></a>데이터 원본 추가

**데이터 원본 추가** 섹션에는 연결할 수 있는 다른 사용 가능한 데이터 원본이 포함됩니다. 이러한 원본의 데이터를 추가하는 방법에 대한 지침은 **추가** 를 클릭합니다.

![데이터 원본](./media/security-center-partner-integration/add-data-sources.png)



## <a name="next-steps"></a>다음 단계

이 문서에서는 Security Center에서 파트너 솔루션을 통합하는 방법을 살펴보았습니다. Azure Sentinel 또는 다른 SIEM과의 통합을 설정하는 방법을 알아보려면 [Security Center 데이터 연속 내보내기](continuous-export.md)를 참조하세요.
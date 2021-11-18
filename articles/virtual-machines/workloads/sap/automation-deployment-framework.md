---
title: Azure의 SAP 배포 자동화 프레임워크 정보
description: Azure의 SAP 배포 자동화 프레임워크에 대한 프레임워크 및 도구 개요입니다.
author: kimforss
ms.author: kimforss
ms.reviewer: kimforss
ms.date: 11/17/2021
ms.service: virtual-machines-sap
ms.topic: conceptual
ms.openlocfilehash: ab701964274a5c70967af798c69415e9ed9441c9
ms.sourcegitcommit: 1244a72dbec39ac8cf16bb1799d8c46bde749d47
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/18/2021
ms.locfileid: "132761819"
---
# <a name="sap-deployment-automation-framework-on-azure"></a>Azure의 SAP 배포 자동화 프레임워크

[Azure의 SAP 배포 자동화 프레임워크는 SAP](https://github.com/Azure/sap-automation) 환경을 배포, 설치 및 유지 관리하기 위한 오픈 소스 오케스트레이션 도구입니다. SAP 지원 운영 체제 버전에서 AnyDB를 사용하여 SAP HANA 및 NetWeaver를 기반으로 SAP 환경을 위한 인프라를 만들고 모든 Azure 지역에 배포할 수 있습니다.

[자동화 프레임워크에는](https://github.com/Azure/sap-automation) 두 가지 주요 구성 요소가 있습니다.
-   배포 인프라(제어 평면) 
-   SAP 인프라(SAP 워크로드)

컨트롤 플레인과 애플리케이션 평면 간의 종속성은 아래 다이어그램에 설명되어 있습니다.

:::image type="content" source="./media/automation-deployment-framework/control-plane-sap-infrastructure.png" alt-text="컨트롤 플레인과 애플리케이션 평면 간의 SAP 배포 자동화 프레임워크 종속성을 보여 주는 다이어그램":::

다음 다이어그램에서는 컨트롤 플레인 및 워크로드 영역의 주요 구성 요소를 보여 주는 다이어그램입니다.

:::image type="content" source="./media/automation-deployment-framework/automation-diagram-full.png" alt-text="SAP 배포 자동화 프레임워크 환경을 보여 주는 다이어그램":::

프레임워크는 인프라 배포에 [Terraform을](https://www.terraform.io/) 사용하고 운영 체제 및 애플리케이션 구성에 [Ansible을](https://www.ansible.com/) 사용합니다.

> [!NOTE]
> 이 자동화 프레임워크는 Azure의 SAP 대한 Microsoft 모범 사례 및 원칙을 기반으로 합니다. Azure [VM(Azure의 SAP 가상 머신)에 대한 시작 가이드를](get-started.md) 검토하여 안정성, 안정성 및 성능을 위해 인증된 가상 머신 및 스토리지 솔루션을 사용하는 방법을 이해합니다.
> 
> 이 자동화 프레임워크는 [Azure용 Microsoft 클라우드 채택 프레임워크](/azure/cloud-adoption-framework/)따릅니다.

SAP 배포 자동화 프레임워크의 제어 평면을 사용하여 SAP 인프라 및 SAP 애플리케이션 인프라를 배포합니다. 배포에서는 Terraform 템플릿을 사용하여 SAP 애플리케이션을 호스트하는 [IaaS(Infrastructure as a Service)](https://azure.microsoft.com/overview/what-is-iaas) 정의 인프라를 만듭니다.

애플리케이션 구성은 미리 정의된 플레이북 집합을 사용하여 컨트롤 평면의 Ansible 컨트롤러에서 수행됩니다. 이러한 플레이북은 다음을 수행합니다.

- 기본 운영 체제 설정 구성
- SAP 관련 운영 체제 설정 구성
- 시스템에서 설치 미디어를 사용할 수 있도록 만들기
- SAP 시스템 설치
- SAP 데이터베이스 설치(SAP HANA, AnyDB)
- Pacemaker를 사용하여 HA(고가용성) 구성
- SAP 데이터베이스에 대한 HA(고가용성) 구성


## <a name="about-the-control-plane"></a>컨트롤 플레인 정보

컨트롤 플레인에는 다른 환경을 배포할 인프라가 있습니다. 컨트롤 플레인을 배포한 후에는 거의 다시 배포할 필요가 없습니다.

컨트롤 플레인은 다음 서비스를 제공합니다.
-   Terraform 배포 인프라
-   Ansible 컨트롤러
-   Terraform 상태 파일의 영구 스토리지
-   다운로드한 SAP 소프트웨어에 대한 영구 스토리지
-   배포 자격 증명을 위한 보안 스토리지
-   프라이빗 DNS 영역(선택 사항)

제어 평면은 일반적으로 [허브 및 스포크 아키텍처](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)의 허브 구독에 배포되는 지역 리소스입니다. 

컨트롤 플레인의 주요 구성 요소는 다음과 같습니다.
- 배포 가상 머신 
- Terraform 상태 파일의 Storage 계정
- SAP 설치 미디어에 대한 Storage 계정
- 배포 자격 증명에 대한 Keyvault


## <a name="about-the-sap-workload"></a>SAP 워크로드 정보

SAP 워크로드에는 SAP 배포에 대한 모든 Azure 인프라 리소스가 포함됩니다. 이러한 리소스는 컨트롤 플레인에서 배포됩니다. SAP 워크로드에는 두 가지 주요 구성 요소가 있습니다.
-   SAP 워크로드 영역
-   SAP 시스템

## <a name="about-the-sap-workload-zone"></a>SAP 워크로드 영역 정보

워크로드 영역을 사용하면 배포를 다른 환경(개발, 테스트, 프로덕션)으로 분할할 수 있습니다. SAP 워크로드 영역은 SAP 시스템에 다음 서비스를 제공합니다.
-   가상 네트워킹 인프라
-   시스템 자격 증명을 위한 보안 스토리지(Virtual Machines 및 SAP)
-   공유 Storage(선택 사항)


## <a name="about-the-sap-system"></a>SAP 시스템 정보

시스템 배포는 웹, 앱 및 데이터베이스 계층을 포함하여 SAP 애플리케이션을 실행할 가상 머신으로 구성됩니다.

SAP 시스템은 다음과 같은 서비스를 제공합니다.
-   SAP 애플리케이션을 호스트하는 가상 머신, 스토리지 및 지원 인프라.

## <a name="glossary"></a>용어

다음 용어는 자동화 프레임워크를 이해하기 위한 중요한 개념입니다.

### <a name="sap-concepts"></a>SAP 개념

| 용어 | 설명 |
| ---- | ----------- |
| 시스템 | 애플리케이션이 실행해야 하는 리소스를 포함하는 SAP 애플리케이션의 인스턴스입니다. 고유한 세 문자 식별자인 **SID** 로 정의합니다.
| 가로 | SAP 애플리케이션 내의 다양한 환경에 있는 시스템의 컬렉션입니다. 예를 들어 SAP ECC(ERP Central Component), SAP CRM(고객 관계 관리) 및 BW(SAP Business Warehouse)가 있습니다. |
| 워크로드 영역 | SAP 애플리케이션을 비프로덕션 및 프로덕션 환경 또는 개발, 품질 보증 및 프로덕션 환경과 같은 환경으로 분할합니다. 가상 네트워크 및 키 자격 증명 모음과 같은 공유 리소스를 내의 모든 시스템에 제공합니다. |

다음 다이어그램은 SAP 시스템, 워크로드 영역(환경) 및 지형 간의 관계를 보여줍니다. 이 예제 설정에서 고객에게는 ECC, CRM 및 BW의 세 가지 SAP 환경이 있습니다. 각 지형에는 프로덕션, 품질 보증 및 개발의 세 가지 워크로드 영역이 포함됩니다. 각 워크로드 영역에는 하나 이상의 시스템이 포함됩니다.

:::image type="content" source="./media/automation-deployment-framework/sap-terms.png" alt-text="지형, 워크플로 영역 및 시스템을 갖춘 SAP 구성 다이어그램.":::

### <a name="deployment-components"></a>배포 구성 요소

| 용어 | Description | 범위 |
| ---- | ----------- | ----- |
| 배포자 | Terraform 및 Ansible 명령을 실행할 수 있는 가상 머신입니다. SAP 가상 네트워크에 피어링된 가상 네트워크(신규 또는 기존)에 배포됩니다. | 지역 |
| 라이브러리 | Terraform 상태 파일 및 SAP 설치 미디어에 대한 스토리지를 제공합니다. | 지역 |
| 워크로드 영역 | SAP 시스템 또는 시스템을 배포할 가상 네트워크를 포함합니다. 또한 환경의 시스템에 대한 자격 증명을 보유하는 키 자격 증명 모음을 포함합니다. | 워크로드 영역 |
| 시스템 | SAP 애플리케이션(SID)에 대한 배포 단위입니다. 부하 분산기 및 가용성 집합과 같은 지원 인프라 아티팩트와 가상 머신을 포함합니다. | 워크로드 영역 |


## <a name="next-steps"></a>다음 단계

> [!div class="nextstepaction"]
> [배포 자동화 프레임워크 시작](automation-get-started.md)

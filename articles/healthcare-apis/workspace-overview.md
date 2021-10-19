---
title: 작업 영역 이란 무엇 인가요? -Azure 의료 Api
description: 이 문서에서는 Azure 의료 Api 작업 영역에 대 한 개요를 설명 합니다.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 07/12/2021
ms.author: ginle
ms.openlocfilehash: 6eb5fa5186b7f28954724f27186b972f7efb08b0
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121787452"
---
# <a name="what-is-healthcare-apis-preview-workspace"></a>의료 Api (미리 보기) 작업 영역 이란?

> [!IMPORTANT]
> Azure 의료 Api는 현재 미리 보기로 제공 됩니다. [Microsoft Azure 미리 보기에 대한 추가 사용 약관](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)에는 베타 또는 미리 보기로 제공되거나 아직 일반 공급으로 릴리스되지 않은 Azure 기능에 적용되는 추가 약관이 포함되어 있습니다.

Azure 의료 Api 작업 영역은 FHIR® (빠른 의료 상호 운용성 리소스) 서비스, 의약품 (DICOM®) 서비스의 디지털 이미징 및 통신, IoT (사물 인터넷) 커넥터와 같은 모든 의료 서비스 인스턴스에 대 한 논리적 컨테이너입니다. 또한 작업 영역은 보호 된 상태 정보를 이동할 수 있는 규정 준수 경계 (HIPAA, HITRUST)를 만듭니다.

작업 영역 내에서 여러 데이터 서비스를 프로 비전 할 수 있으며, 설계 하면 서로 원활 하 게 작동 합니다. 작업 영역에서 모든 의료 Api 인스턴스를 구성 하 고 해당 하는 모든 기본 데이터 집합 및 서비스에서 공유 되는 특정 구성 설정을 관리할 수 있습니다.

## <a name="workspace-provisioning-process"></a>작업 영역 프로 비전 프로세스
 
Azure Portal 또는 배포 스크립트를 사용 하 여 리소스 그룹에서 하나 이상의 작업 영역을 만들 수 있습니다. 하나 이상의 자식 서비스 인스턴스를 만들려면 먼저 계층 구조 서비스 트리의 부모 항목으로 의료 Api 작업 영역을 만들어야 합니다.   
 
작업 영역 내의 모든 자식 서비스 인스턴스가 삭제 되지 않은 경우 작업 영역을 삭제할 수 없습니다. 이 기능은 실수로 서비스 인스턴스를 삭제 하는 것을 방지 하는 데 도움이 됩니다. 그러나 작업 영역 리소스 그룹이 삭제 되 면 작업 영역 리소스 그룹 내의 모든 작업 영역 및 자식 서비스 인스턴스가 삭제 됩니다. 

## <a name="workspace-and-azure-region-selection"></a>작업 영역 및 Azure 지역 선택 
 
작업 영역을 만들 때 Azure 지역에 대해 구성 해야 하며,이는 리소스 그룹과 같거나 다를 수 있습니다. 작업 영역을 만든 후에는 지역을 변경할 수 없습니다. 각 작업 영역 내에서 모든 의료 api 서비스 (fhir service, DICOM service 및 IoT Connector 서비스)는 작업 영역 영역에 생성 되어야 하며 다른 작업 영역으로 이동할 수 없습니다. 

## <a name="workspace-and-azure-healthcare-apis-service-instances"></a>작업 영역 및 Azure 의료 Api 서비스 인스턴스 

Azure 의료 Api 작업 영역을 만든 후에는 Azure Portal에서 하나 이상의 서비스 인스턴스를 만들 준비가 되었습니다. 한 작업 영역에서 동일한 유형 또는 다른 유형의 여러 서비스 인스턴스를 만들 수 있습니다. 작업 영역 내에서 자식 서비스 인스턴스에 공유 구성 설정을 적용할 수 있습니다 .이에 대해서는 작업 영역 및 구성 설정 섹션에서 설명 합니다.

[![Azure 리소스 그룹 ](media/azure-resource-group.png) ](media/azure-resource-group.png#lightbox)

또한 일반적으로 IaC (infrastructure as code) 라고 하는 프로세스를 Azure Resource Manager 배포 템플릿을 사용 하 여 작업 영역을 만들 수 있습니다. 이 옵션은 ARM 템플릿을 사용자 지정 하 고 결합 된 단계에서 작업 영역 만들기 및 서비스 인스턴스 만들기를 완료 하는 기능을 제공 합니다. 

PowerShell, CLI, Terraform 스크립트 또는 .NET SDK를 사용 하 여 의료 Api 서비스를 배포할 수 있습니다. 작업 영역에서 서비스 인스턴스를 만들려면 **만들기** (fhir SERVICE, DICOM Service 또는 IoT 커넥터)를 선택 하 고 만들려는 해당 서비스 인스턴스에 대 한 계정 세부 정보를 입력 합니다.


## <a name="fhir-service"></a>FHIR 서비스

FHIR 서비스는 데이터 액세스를 위한 Azure의 fhir Api 및 끝점과 FHIR 데이터 형식의 저장소를 포함 합니다. FHIR 서비스는 안전 하 고 규정을 준수 하는 클라우드 환경에서 고정 된 상태 정보 (화)를 관리 합니다. FHIR 서비스를 배포 하면 HL7에서 게시 한 [fhir](https://www.hl7.org/fhir/index.html) 상호 운용 가능한 데이터 표준에 따라 여러 시스템의 임상 heath 데이터를 Azure 클라우드로 통합할 수 있습니다. 자세한 내용은 [About FHIR 서비스](./fhir/overview.md)를 참조 하세요.

## <a name="dicom-service"></a>DICOM 서비스

DICOMwebTM 사용 시스템에서 클라우드로 의료 이미지 데이터를 가져오기 위해 DICOM 서비스를 배포 합니다. 자세한 내용은 [DICOM Service 개요](dicom/dicom-services-overview.md)를 참조 하세요.

## <a name="iot-connector"></a>IoT 커넥터

IoT Connector 서비스를 사용 하면 높은 빈도의 IoT 장치 데이터를 확장 가능 하 고 안전 하며 호환 되는 방식으로 fhir 서비스에 수집할 수 있습니다. 자세한 내용은 [IoT connector 설명서 페이지](./iot/index.yml)를 참조 하세요.
 
## <a name="workspace-configuration-settings"></a>작업 영역 구성 설정

일부 기능은 작업 영역 수준에서 구성 되 고 해당 작업 영역 내의 모든 자식 서비스에 적용 됩니다.

### <a name="application-monitoring"></a>애플리케이션 모니터링

Azure Monitor는 애플리케이션 및 서비스의 가용성과 성능을 극대화하는 데 도움이 됩니다. 클라우드 및 온-프레미스 환경에서 원격 분석의 수집, 분석 및 작업에 대한 포괄적인 솔루션을 제공합니다. 이 정보를 통해 응용 프로그램의 작동 방식에 대 한 정보를 제공 하 고,이를 통해 해당 응용 프로그램에 영향을 주는 문제를 사전에 식별 하 고 해결할 수 있습니다. Azure Monitor에 대 한 자세한 내용은 [Azure Monitor 개요](../azure-monitor/index.yml) 설명서를 참조 하세요.

### <a name="role-based-access-control-rbac"></a>RBAC(역할 기반 액세스 제어)

Azure RBAC(Azure 역할 기반 액세스 제어)는 Azure 리소스에 대한 세밀한 액세스 관리를 제공하는 시스템입니다. Azure RBAC를 사용하면 팀 내에서 업무를 분리하고 사용자에게 해당 작업을 수행하는 데 필요한 만큼의 권한만 부여할 수 있습니다. 또한 Azure 리소스에 대 한 액세스 권한이 있는 사용자, 해당 리소스에서 수행할 수 있는 작업 및 액세스할 수 있는 영역을 관리 하는 데 도움이 됩니다. 자세한 내용은 [AZURE RBAC](../role-based-access-control/index.yml) 설명서를 참조 하세요.


## <a name="next-steps"></a>다음 단계

Azure 의료 Api 작업을 시작 하려면 5 분 빠른 시작을 따라 작업 영역을 배포 합니다.

>[!div class="nextstepaction"]
>[Azure Portal에서 작업 영역 배포](healthcare-apis-quickstart.md)
---
title: 관리 ID를 지원하는 Azure 서비스 - Azure AD
description: Azure 리소스 및 Azure AD 인증에 대해 관리 ID를 지원하는 서비스 목록
services: active-directory
author: barclayn
ms.author: barclayn
ms.date: 07/13/2021
ms.topic: conceptual
ms.service: active-directory
ms.subservice: msi
manager: daveba
ms.collection: M365-identity-device-management
ms.custom: references_regions
ms.openlocfilehash: fad9f2566384e824c72a4914c1fdab598b99a5cf
ms.sourcegitcommit: 9f1a35d4b90d159235015200607917913afe2d1b
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/21/2021
ms.locfileid: "122634434"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>Azure 리소스에 대한 관리 ID를 지원하는 서비스

Azure 리소스용 관리 ID는 Azure Active Directory에서 자동으로 관리되는 ID를 Azure 서비스에 제공합니다. 관리되는 ID를 사용하면 코드에 자격 증명이 없어도 Azure AD 인증을 지원하는 모든 서비스를 인증할 수 있습니다. 현재 Azure 전반에 걸쳐 Azure 리소스와 Azure AD 인증에 대한 관리 ID를 통합하는 과정이 진행되고 있습니다. 업데이트를 자주 확인해 주세요.

> [!NOTE]
> Azure 리소스에 대한 관리 ID는 이전에 MSI(관리 서비스 ID)로 알려진 서비스에 대한 새 이름입니다.

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>Azure 리소스에 대한 관리 ID를 지원하는 Azure 서비스

다음 Azure 서비스는 Azure 리소스에 대한 관리 ID를 지원합니다.


### <a name="azure-api-management"></a>Azure API Management

관리 ID 유형 | 모든 일반 공급<br>전 세계 Azure 지역 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 시스템 할당 | ![사용 가능][check] | ![사용 가능][check] | 사용할 수 없음 | ![사용 가능][check] |
| 사용자 할당 | ![사용 가능][check] | ![사용 가능][check] | 사용할 수 없음 | ![사용 가능][check] |

사용 가능한 지역에서 Azure API Management용 관리 ID를 구성하려면 다음 목록을 참조하세요.

- [Azure Resource Manager 템플릿](../../api-management/api-management-howto-use-managed-service-identity.md)

### <a name="azure-app-configuration"></a>Azure App Configuration

| 관리 ID 유형 | 모든 일반 공급<br>전 세계 Azure 지역 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 시스템 할당 | ![사용 가능][check] | ![사용 가능][check] | 사용할 수 없음 | ![사용 가능][check] |
| 사용자 할당 | ![사용 가능][check] | ![사용 가능][check]  | 사용할 수 없음  | ![사용 가능][check] |

사용 가능한 지역에서 Azure App 환경설정용 관리 ID를 구성하려면 다음 목록을 참조하세요.

- [Azure CLI](../../azure-app-configuration/overview-managed-identity.md)

### <a name="azure-app-service"></a>Azure App Service

| 관리 ID 유형 | 모든 일반 공급<br>전 세계 Azure 지역 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 시스템 할당 | ![사용 가능][check] | ![사용 가능][check] | ![사용 가능][check] | ![사용 가능][check] |
| 사용자 할당 | ![사용 가능][check] | ![사용 가능][check]  | ![사용 가능][check]  | ![사용 가능][check] |

사용 가능한 지역에서 Azure App Service용 관리 ID를 구성하려면 다음 목록을 참조하세요.

- [Azure Portal](../../app-service/overview-managed-identity.md#using-the-azure-portal)
- [Azure CLI](../../app-service/overview-managed-identity.md#using-the-azure-cli)
- [Azure PowerShell](../../app-service/overview-managed-identity.md#using-azure-powershell)
- [Azure Resource Manager 템플릿](../../app-service/overview-managed-identity.md#using-an-azure-resource-manager-template)

### <a name="azure-arc-enabled-kubernetes"></a>Azure Arc를 지원하는 Kubernetes

| 관리 ID 유형 | 모든 일반 공급<br>전 세계 Azure 지역 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 시스템 할당 | 미리 보기 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 |
| 사용자 할당 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 |

Kubernetes가 가능한 Azure Arc는 현재 [시스템 할당 ID를 지원합니다](../../azure-arc/kubernetes/quickstart-connect-cluster.md). 관리 서비스 ID 인증서는 Azure와의 통신을 위한 Kubernetes 에이전트가 가능한 모든 Azure Arc에 사용됩니다.

### <a name="azure-arc-enabled-servers"></a>Azure Arc 지원 서버

| 관리 ID 유형 | 모든 일반 공급<br>전 세계 Azure 지역 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 시스템 할당 | ![사용 가능][check] | ![사용 가능][check] | 사용할 수 없음 | 사용할 수 없음 |
| 사용자 할당 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 |

모든 Azure Arc 지원 서버에는 시스템 할당 ID가 있습니다. Azure Arc 지원 서버에서 시스템 할당 ID를 사용하지 않도록 설정하거나 변경할 수 없습니다. Azure Arc 지원 서버에서 관리되는 ID를 사용하는 방법에 대한 자세한 내용은 다음 리소스를 참조하세요.

- [Arc 지원 서버를 사용하는 Azure 리소스에 대해 인증](../../azure-arc/servers/managed-identity-authentication.md)
- [Arc 지원 서버에서 관리 ID 사용](../../azure-arc/servers/security-overview.md#using-a-managed-identity-with-arc-enabled-servers)

### <a name="azure-automanage"></a>Azure Automanage

| 관리 ID 유형 | 모든 일반 공급<br>전 세계 Azure 지역 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 시스템 할당 | 미리 보기 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 |
| 사용자 할당 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 |

구독을 새 테넌트로 이동한 경우 관리 ID를 다시 구성하려면 다음 문서를 참조하세요.

* [손상된 Automanage 계정 복구](../../automanage/repair-automanage-account.md)

### <a name="azure-automation"></a>Azure Automation

| 관리 ID 유형 | 모든 일반 공급<br>전 세계 Azure 지역 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 시스템 할당 | 미리 보기 | 미리 보기 | 사용할 수 없음 | 미리 보기 |
| 사용자 할당 | 미리 보기 | 미리 보기 | 사용할 수 없음 | 미리 보기 |

[Azure Automation](../../automation/automation-intro.md)에서 관리 ID를 사용하려면 다음 문서를 참조하세요.

* [Automation 계정 인증 개요 - 관리 ID](../../automation/automation-security-overview.md#managed-identities-preview)
* [Automation에서 관리 ID를 사용하도록 설정 및 사용](../../automation/enable-managed-identity-for-automation.md)

### <a name="azure-blueprints"></a>Azure Blueprints

|관리 ID 유형 | 모든 일반 공급<br>전 세계 Azure 지역 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 시스템 할당 | ![사용 가능][check] | ![사용 가능][check] | 사용할 수 없음 | 사용할 수 없음 |
| 사용자 할당 | ![사용 가능][check] | ![사용 가능][check] | 사용할 수 없음 | 사용할 수 없음 |

[Azure Blueprints](../../governance/blueprints/overview.md)에서 관리 ID를 사용하려면 다음 목록을 참조하세요.

- [Azure Portal - 청사진 할당](../../governance/blueprints/create-blueprint-portal.md#assign-a-blueprint)
- [REST API - 청사진 할당](../../governance/blueprints/create-blueprint-rest-api.md#assign-a-blueprint)


### <a name="azure-cognitive-search"></a>Azure Cognitive Search

관리 ID 유형 | 모든 일반 공급<br>전 세계 Azure 지역 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 시스템 할당 | ![사용 가능][check] | ![사용 가능][check] | 사용할 수 없음 | ![사용 가능][check] |
| 사용자 할당 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 |

### <a name="azure-cognitive-services"></a>Azure Cognitive Services

관리 ID 유형 | 모든 일반 공급<br>전 세계 Azure 지역 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 시스템 할당 | ![사용 가능][check] | ![사용 가능][check] | 사용할 수 없음 | ![사용 가능][check] |
| 사용자 할당 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 |

### <a name="azure-container-instances"></a>Azure Container Instances

관리 ID 유형 | 모든 일반 공급<br>전 세계 Azure 지역 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 시스템 할당 | Linux: 미리 보기<br>Windows: 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 |
| 사용자 할당 | Linux: 미리 보기<br>Windows: 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 |

사용 가능한 지역에서 Azure Container Instances용 관리 ID를 구성하려면 다음 목록을 참조하세요.

- [Azure CLI](~/articles/container-instances/container-instances-managed-identity.md)
- [Azure Resource Manager 템플릿](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)
- [YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file)


### <a name="azure-container-registry-tasks"></a>Azure Container Registry 작업

관리 ID 유형 | 모든 일반 공급<br>전 세계 Azure 지역 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 시스템 할당 | ![사용 가능][check] | 미리 보기 | 사용할 수 없음 | 미리 보기 |
| 사용자 할당 | 미리 보기 | 미리 보기 | 사용할 수 없음 | 미리 보기 |

사용 가능한 지역에서 Azure Container Registry 태스크용 관리 ID를 구성하려면 다음 목록을 참조하세요.

- [Azure CLI](~/articles/container-registry/container-registry-tasks-authentication-managed-identity.md)

### <a name="azure-data-explorer"></a>Azure Data Explorer

관리 ID 유형 | 모든 일반 공급<br>전 세계 Azure 지역 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 시스템 할당 | ![사용 가능][check] | ![사용 가능][check] | 사용할 수 없음 | ![사용 가능][check] |
| 사용자 할당 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 |

### <a name="azure-data-factory-v2"></a>Azure Data Factory V2

관리 ID 유형 | 모든 일반 공급<br>전 세계 Azure 지역 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 시스템 할당 | ![사용 가능][check] | ![사용 가능][check] | 사용할 수 없음 | ![사용 가능][check] |
| 사용자 할당 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 |

사용 가능한 지역에서 Azure Data Factory V2용 관리 ID를 구성하려면 다음 목록을 참조하세요.

- [Azure Portal](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity)

### <a name="azure-digital-twins"></a>Azure Digital Twins

관리 ID 유형 | 모든 일반 공급<br>전 세계 Azure 지역 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 시스템 할당 | ![사용 가능][check] | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 |
| 사용자 할당 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 |

사용 가능한 지역에서 Azure Digital Twins용 관리 ID를 구성하려면 다음 목록을 참조하세요.

- [Azure Portal](../../digital-twins/how-to-route-with-managed-identity.md)

### <a name="azure-event-grid"></a>Azure Event Grid

관리 ID 유형 |모든 일반 공급<br>전 세계 Azure 지역 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 시스템 할당 | 미리 보기 | 미리 보기 | 사용할 수 없음 | 미리 보기 |
| 사용자 할당 | 미리 보기 | 미리 보기 | 사용할 수 없음 | 미리 보기 |

### <a name="azure-firewall-policy"></a>Azure Firewall 정책

관리 ID 유형 |모든 일반 공급<br>전 세계 Azure 지역 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 시스템 할당 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 |
| 사용자 할당 | 미리 보기 | 사용할 수 없음  | 사용할 수 없음  | 사용할 수 없음 |

### <a name="azure-functions"></a>Azure 기능

관리 ID 유형 |모든 일반 공급<br>전 세계 Azure 지역 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 시스템 할당 | ![사용 가능][check] | ![사용 가능][check] | ![사용 가능][check] | ![사용 가능][check] |
| 사용자 할당 | ![사용 가능][check] | ![사용 가능][check]  | ![사용 가능][check]  | ![사용 가능][check]  |

사용 가능한 지역에서 Azure Functions용 관리 ID를 구성하려면 다음 목록을 참조하세요.

- [Azure Portal](../../app-service/overview-managed-identity.md#using-the-azure-portal)
- [Azure CLI](../../app-service/overview-managed-identity.md#using-the-azure-cli)
- [Azure PowerShell](../../app-service/overview-managed-identity.md#using-azure-powershell)
- [Azure Resource Manager 템플릿](../../app-service/overview-managed-identity.md#using-an-azure-resource-manager-template)

### <a name="azure-iot-hub"></a>Azure IoT Hub

관리 ID 유형 | 모든 일반 공급<br>전 세계 Azure 지역 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 시스템 할당 | ![사용 가능][check] | ![사용 가능][check] | 사용할 수 없음 | ![사용 가능][check] |
| 사용자 할당 | ![사용 가능][check] | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 |

사용 가능한 지역에서 Azure IoT Hub용 관리 ID를 구성하려면 다음 목록을 참조하세요.

- 자세한 내용은 [관리 ID에 대한 Azure IoT Hub 지원](../../iot-hub/iot-hub-managed-identity.md)을 참조하세요.

### <a name="azure-importexport"></a>Azure Import/Export

관리 ID 유형 | 모든 일반 공급<br>전 세계 Azure 지역 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| 시스템 할당 | Azure 가져오기 내보내기 서비스가 가능한 지역에서 사용할 수 있습니다. | 미리 보기 | 사용 가능 | 사용 가능 |
| 사용자 할당 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 |

### <a name="azure-kubernetes-service-aks"></a>AKS(Azure Kubernetes Service)

| 관리 ID 유형 | 모든 일반 공급<br>전 세계 Azure 지역 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 시스템 할당 | ![사용 가능][check] | ![사용 가능][check] | 사용할 수 없음 | 사용할 수 없음 |
| 사용자 할당 | 미리 보기 | ![사용 가능][check] | 사용할 수 없음 | 사용할 수 없음 |


자세한 내용은 [Azure Kubernetes Service에서 관리 ID 사용](../../aks/use-managed-identity.md)을 참조하세요.

### <a name="azure-log-analytics-cluster"></a>Azure Log 분석 클러스터

관리 ID 유형 | 모든 일반 공급<br>전 세계 Azure 지역 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 시스템 할당 | ![사용 가능][check] | ![사용 가능][check] | 사용할 수 없음 | ![사용 가능][check] |
| 사용자 할당 | ![사용 가능][check] | ![사용 가능][check] | 사용할 수 없음 | ![사용 가능][check] |

자세한 내용은 [Azure Monitor에서 ID가 작동하는 방식](../../azure-monitor/logs/customer-managed-keys.md)을 참조하세요.

### <a name="azure-logic-apps"></a>Azure Logic Apps

관리 ID 유형 | 모든 일반 공급<br>전 세계 Azure 지역 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 시스템 할당 | ![사용 가능][check] | ![사용 가능][check] | 사용할 수 없음 | ![사용 가능][check] |
| 사용자 할당 | ![사용 가능][check] | ![사용 가능][check] | 사용할 수 없음 | ![사용 가능][check] |


사용 가능한 지역에서 Azure Functions용 관리 ID를 구성하려면 다음 목록을 참조하세요.

- [Azure Portal](../../logic-apps/create-managed-service-identity.md#enable-system-assigned-identity-in-azure-portal)
- [Azure Resource Manager 템플릿](../../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)

### <a name="azure-machine-learning"></a>Azure Machine Learning

관리 ID 유형 | 모든 일반 공급<br>전 세계 Azure 지역 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 시스템 할당 | 미리 보기 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 |
| 사용자 할당 | 미리 보기 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 |

자세한 내용은 [Azure Machine Learning에서 관리상 ID 사용](../../machine-learning/how-to-use-managed-identities.md)을 참조하세요.

### <a name="azure-media-services"></a>Azure Media Services

| 관리 ID 유형 | 모든 일반 공급<br>전 세계 Azure 지역 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 시스템 할당 | ![사용 가능][check] | ![사용 가능][check] | 사용할 수 없음 | ![사용 가능][check] |
| 사용자 할당 | 사용할 수 없음  | 사용할 수 없음  | 사용할 수 없음  | 사용할 수 없음  |

사용 가능한 지역에서 Azure Media Services용 관리 ID를 구성하려면 다음 목록을 참조하세요.

- [Azure CLI](../../media-services/latest/security-access-storage-managed-identity-cli-tutorial.md)

### <a name="azure-policy"></a>Azure Policy

|관리 ID 유형 | 모든 일반 공급<br>전 세계 Azure 지역 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 시스템 할당 | ![사용 가능][check] | ![사용 가능][check] | ![사용 가능][check] | ![사용 가능][check] |
| 사용자 할당 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 |

사용 가능한 지역에서 Azure Policy용 관리 ID를 구성하려면 다음 목록을 참조하세요.

- [Azure Portal](../../governance/policy/tutorials/create-and-manage.md#assign-a-policy)
- [PowerShell](../../governance/policy/how-to/remediate-resources.md#create-managed-identity-with-powershell)
- [Azure CLI](/cli/azure/policy/assignment#az_policy_assignment_create)
- [Azure 리소스 관리자 템플릿](/azure/templates/microsoft.authorization/policyassignments)
- [REST (영문)](/rest/api/policy/policyassignments/create)


### <a name="azure-service-fabric"></a>Azure Service Fabric

[Service Fabric Application용 관리상 ID](../../service-fabric/concepts-managed-identity.md)는 모든 지역에서 사용 가능합니다.

관리 ID 유형 | 모든 일반 공급<br>전 세계 Azure 지역 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 시스템 할당 | ![사용 가능][check] | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 |
| 사용자 할당 | ![사용 가능][check] | 사용할 수 없음 | 사용할 수 없음 |사용할 수 없음 |

모든 지역에서 Azure Service Fabric 애플리케이션에 대한 관리 ID를 구성하려면 다음 목록을 참조하세요.

- [Azure Resource Manager 템플릿](https://github.com/Azure-Samples/service-fabric-managed-identity/tree/anmenard-docs)

### <a name="azure-spring-cloud"></a>Azure Spring Cloud

| 관리 ID 유형 | 모든 일반 공급<br>전 세계 Azure 지역 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 시스템 할당 | ![사용 가능][check] | 사용할 수 없음 | 사용할 수 없음 | ![사용 가능][check] |
| 사용자 할당 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 |


자세한 정보는 [Azure Spring Cloud 애플리케이션용 시스템 할당 관리 ID를 사용하도록 설정하는 방법](../../spring-cloud/how-to-enable-system-assigned-managed-identity.md)을 참조하십시오.

### <a name="azure-stack-edge"></a>Azure Stack Edge

관리 ID 유형 | 모든 일반 공급<br>전 세계 Azure 지역 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| 시스템 할당 | Azure Stack Edge 서비스가 가능한 지역에서 사용 가능 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 |
| 사용자 할당 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 |

### <a name="azure-virtual-machine-scale-sets"></a>Azure Virtual Machine Scale Sets

|관리 ID 유형 | 모든 일반 공급<br>전 세계 Azure 지역 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 시스템 할당 | ![사용 가능][check] | ![사용 가능][check] | ![사용 가능][check] | ![사용 가능][check] |
| 사용자 할당 | ![사용 가능][check] | ![사용 가능][check] | ![사용 가능][check] | ![사용 가능][check] |

사용 가능한 지역에서 Azure Virtual Machines Scale Sets용 관리 ID를 구성하려면 다음 목록을 참조하세요.

- [Azure Portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Azure 리소스 관리자 템플릿](qs-configure-template-windows-vm.md)
- [REST (영문)](qs-configure-rest-vm.md)



### <a name="azure-virtual-machines"></a>Azure Virtual Machines

| 관리 ID 유형 | 모든 일반 공급<br>전 세계 Azure 지역 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 시스템 할당 | ![사용 가능][check] | ![사용 가능][check] | ![사용 가능][check] | ![사용 가능][check] |
| 사용자 할당 | ![사용 가능][check] | ![사용 가능][check] | ![사용 가능][check] | ![사용 가능][check] |

사용 가능한 지역에서 Azure Virtual Machines용 관리 ID를 구성하려면 다음 목록을 참조하세요.

- [Azure Portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Azure 리소스 관리자 템플릿](qs-configure-template-windows-vm.md)
- [REST (영문)](qs-configure-rest-vm.md)
- [Azure SDK](qs-configure-sdk-windows-vm.md)


### <a name="azure-vm-image-builder"></a>Azure VM Image Builder

| 관리 ID 유형 | 모든 일반 공급<br>전 세계 Azure 지역 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 시스템 할당 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 |
| 사용자 할당 | [지원되는 영역에서 사용 가능](../../virtual-machines/image-builder-overview.md#regions) | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 |

사용 가능한 지역에서 Azure VM Image Builder에 대해 관리 ID를 구성하는 방법을 알아보려면 [Image Builder 개요](../../virtual-machines/image-builder-overview.md#permissions)를 참조하세요.
### <a name="azure-signalr-service"></a>Azure SignalR Service

관리 ID 유형 | 모든 일반 공급<br>전 세계 Azure 지역 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 시스템 할당 | 미리 보기 | 미리 보기 | 사용할 수 없음 | 미리 보기 |
| 사용자 할당 | 미리 보기 | 미리 보기 | 사용할 수 없음 | 미리 보기 |

사용 가능한 지역에서 Azure SignalR Service용 관리 ID를 구성하려면 다음 목록을 참조하세요.

- [Azure Resource Manager 템플릿](../../azure-signalr/howto-use-managed-identity.md)

### <a name="azure-resource-mover"></a>Azure Resource Mover

관리 ID 유형 | 모든 일반 공급<br>전 세계 Azure 지역 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 시스템 할당 | Azure 리소스 이동 서비스를 사용할 수 있는 지역에서 사용 가능 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 |
| 사용자 할당 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 |

Azure Resource Mover를 사용하려면 다음 문서를 참조하세요:

- [Azure Resource Mover](../../resource-mover/overview.md)

## <a name="azure-services-that-support-azure-ad-authentication"></a>Azure AD 인증을 지원하는 Azure 서비스

Azure AD 인증을 지원하며, Azure 리소스에 대해 관리 ID를 사용하는 클라이언트 서비스에서 테스트가 완료된 서비스는 다음과 같습니다.

### <a name="azure-resource-manager"></a>Azure 리소스 관리자

Azure Resource Manager에 대한 액세스를 구성하려면 다음 목록을 참조하세요.

- [Azure Portal을 통해 액세스 할당](howto-assign-access-portal.md)
- [PowerShell을 통해 액세스 할당](howto-assign-access-powershell.md)
- [Azure CLI를 통해 액세스 할당](howto-assign-access-CLI.md)
- [Azure Resource Manager 템플릿을 통해 액세스 할당](../../role-based-access-control/role-assignments-template.md)

| 클라우드 | 리소스 ID | 상태 |
|--------|------------|:-:|
| Azure 글로벌 | `https://management.azure.com/`| ![사용 가능][check] |
| Azure Government | `https://management.usgovcloudapi.net/` | ![사용 가능][check] |
| Azure Germany | `https://management.microsoftazure.de/` | ![사용 가능][check] |
| Azure China 21Vianet | `https://management.chinacloudapi.cn` | ![사용 가능][check] |

### <a name="azure-key-vault"></a>Azure Key Vault

| 클라우드 | 리소스 ID | 상태 |
|--------|------------|:-:|
| Azure 글로벌 | `https://vault.azure.net`| ![사용 가능][check] |
| Azure Government | `https://vault.usgovcloudapi.net` | ![사용 가능][check] |
| Azure Germany |  `https://vault.microsoftazure.de` | ![사용 가능][check] |
| Azure China 21Vianet | `https://vault.azure.cn` | ![사용 가능][check] |

### <a name="azure-data-lake"></a>Azure 데이터 레이크

| 클라우드 | 리소스 ID | 상태 |
|--------|------------|:-:|
| Azure 글로벌 | `https://datalake.azure.net/` | ![사용 가능][check] |
| Azure Government |  | 사용할 수 없음 |
| Azure Germany |   | 사용할 수 없음 |
| Azure China 21Vianet |  | 사용할 수 없음 |

### <a name="azure-cosmos-db"></a>Azure Cosmos DB

| 클라우드 | 리소스 ID | 상태 |
|--------|------------|:-:|
| Azure 글로벌 | `https://<account>.documents.azure.com/`<br/><br/>`https://cosmos.azure.com` | ![사용 가능][check] |
| Azure Government | `https://<account>.documents.azure.us/`<br/><br/>`https://cosmos.azure.us` | ![사용 가능][check] |
| Azure Germany | `https://<account>.documents.microsoftazure.de/`<br/><br/>`https://cosmos.microsoftazure.de` | ![사용 가능][check] |
| Azure China 21Vianet | `https://<account>.documents.azure.cn/`<br/><br/>`https://cosmos.azure.cn` | ![사용 가능][check] |

### <a name="azure-sql"></a>Azure SQL

| 클라우드 | 리소스 ID | 상태 |
|--------|------------|:-:|
| Azure 글로벌 | `https://database.windows.net/` | ![사용 가능][check] |
| Azure Government | `https://database.usgovcloudapi.net/` | ![사용 가능][check] |
| Azure Germany | `https://database.cloudapi.de/` | ![사용 가능][check] |
| Azure China 21Vianet | `https://database.chinacloudapi.cn/` | ![사용 가능][check] |

### <a name="azure-data-explorer"></a>Azure Data Explorer

| 클라우드 | 리소스 ID | 상태 |
|--------|------------|:-:|
| Azure 글로벌 | `https://<account>.<region>.kusto.windows.net` | ![사용 가능][check] |
| Azure Government | `https://<account>.<region>.kusto.usgovcloudapi.net` | ![사용 가능][check] |
| Azure Germany | `https://<account>.<region>.kusto.cloudapi.de` | ![사용 가능][check] |
| Azure China 21Vianet | `https://<account>.<region>.kusto.chinacloudapi.cn` | ![사용 가능][check] |

### <a name="azure-event-hubs"></a>Azure Event Hubs

| 클라우드 | 리소스 ID | 상태 |
|--------|------------|:-:|
| Azure 글로벌 | `https://eventhubs.azure.net` | ![사용 가능][check] |
| Azure Government | `https://eventhubs.azure.net` | ![사용 가능][check] |
| Azure Germany | `https://eventhubs.azure.net` | ![사용 가능][check] |
| Azure China 21Vianet | `https://eventhubs.azure.net` | ![사용 가능][check] |

### <a name="azure-service-bus"></a>Azure Service Bus

| 클라우드 | 리소스 ID | 상태 |
|--------|------------|:-:|
| Azure 글로벌 | `https://servicebus.azure.net`  | ![사용 가능][check] |
| Azure Government | `https://servicebus.azure.net`  | ![사용 가능][check] |
| Azure Germany |  `https://servicebus.azure.net`  | ![사용 가능][check] |
| Azure China 21Vianet | `https://servicebus.azure.net`  | ![사용 가능][check] |


### <a name="azure-storage-blobs-and-queues"></a>Azure Storage Blob 및 큐

| 클라우드 | 리소스 ID | 상태 |
|--------|------------|:-:|
| Azure 글로벌 | `https://storage.azure.com/` <br /><br />`https://<account>.blob.core.windows.net` <br /><br />`https://<account>.queue.core.windows.net` | ![사용 가능][check] |
| Azure Government | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.usgovcloudapi.net` <br /><br />`https://<account>.queue.core.usgovcloudapi.net` | ![사용 가능][check] |
| Azure Germany | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.cloudapi.de` <br /><br />`https://<account>.queue.core.cloudapi.de` | ![사용 가능][check] |
| Azure China 21Vianet | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.chinacloudapi.cn` <br /><br />`https://<account>.queue.core.chinacloudapi.cn` | ![사용 가능][check] |

### <a name="azure-analysis-services"></a>Azure Analysis Services

| 클라우드 | 리소스 ID | 상태 |
|--------|------------|:-:|
| Azure 글로벌 | `https://*.asazure.windows.net` | ![사용 가능][check] |
| Azure Government | `https://*.asazure.usgovcloudapi.net` | ![사용 가능][check] |
| Azure Germany | `https://*.asazure.cloudapi.de` | ![사용 가능][check] |
| Azure China 21Vianet | `https://*.asazure.chinacloudapi.cn` | ![사용 가능][check] |

### <a name="azure-communication-services"></a>Azure Communication Services

관리 ID 유형 | 모든 일반 공급<br>전 세계 Azure 지역 | Azure Government | Azure Germany | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| 시스템 할당 | ![사용 가능][check] | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 |
| 사용자 할당 | ![사용 가능][check] | 사용할 수 없음 | 사용할 수 없음 | 사용할 수 없음 |


> [!NOTE]
> 관리 ID를 사용하여 [Power BI에 Azure Stream Analytics](../../stream-analytics/powerbi-output-managed-identity.md) 작업을 인증할 수 있습니다.


[check]: media/services-support-managed-identities/check.png "사용 가능"

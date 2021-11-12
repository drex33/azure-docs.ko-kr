---
title: 엔터프라이즈 보안 및 거버넌스
titleSuffix: Azure Machine Learning
description: 'Azure Machine Learning: 인증, 권한 부여, 네트워크 보안, 데이터 암호화 및 모니터링을 안전하게 사용합니다.'
services: machine-learning
ms.service: machine-learning
ms.subservice: enterprise-readiness
ms.topic: conceptual
ms.author: aashishb
author: aashishb
ms.reviewer: larryfr
ms.date: 10/21/2021
ms.openlocfilehash: d5034f59cc71352518c0570e525f952f0f657c71
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132343138"
---
# <a name="enterprise-security-and-governance-for-azure-machine-learning"></a>Azure Machine Learning의 Enterprise 보안 및 거버넌스

이 문서에서는 Azure Machine Learning에 사용할 수 있는 보안과 거버넌스 기능에 대해 알아봅니다. 이러한 기능은 회사 정책을 준수한 보안을 구성하려는 관리자, DevOps, MLOps에게 유용합니다. Azure Machine Learning 및 Azure 플랫폼을 사용하여 다음 작업을 수행할 수 있습니다.

* 사용자 계정 또는 그룹의 리소스 및 작업에 액세스 제한
* 발신 및 수신 네트워크 통신 제한
* 전송 중 및 미사용 데이터 암호화
* 취약성 검사
* 구성 정책 적용과 감사

## <a name="restrict-access-to-resources-and-operations"></a>리소스 및 작업에 액세스 제한

[Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) 는 Azure Machine Learning의 ID 서비스 공급자입니다. Azure 리소스를 _인증_ 하는 데 사용되는 보안 개체(사용자, 그룹, 서비스 주체 및 관리 ID)를 만들고 관리할 수 있습니다. Azure AD가 이를 사용하도록 구성된 경우 다단계 인증이 지원됩니다.

Azure AD에서 다단계 인증을 사용하는 Azure Machine Learning의 인증 프로세스는 다음과 같습니다.

1. 클라이언트가 Azure AD에 로그인하여 Azure Resource Manager 토큰을 얻습니다.
1. 클라이언트가 Azure Resource Manager와 모든 Azure Machine Learning에 토큰을 제공합니다.
1. Machine Learning은 사용자 컴퓨팅 대상(예: Azure Machine Learning 계산 클러스터)에 Machine Learning Service 토큰을 제공합니다. 이 토큰은 실행이 완료된 후 사용자 컴퓨팅 대상이 Machine Learning Service로 콜백되는 데 사용됩니다. 범위는 작업 영역으로 제한됩니다.

[![Azure Machine Learning 인증](media/concept-enterprise-security/authentication.png)](media/concept-enterprise-security/authentication.png#lightbox)

각 작업 영역에는 작업 영역과 이름이 동일하며, 시스템이 할당한 연결되어 있는 [관리 ID가](../active-directory/managed-identities-azure-resources/overview.md) 있습니다. 이 관리 ID는 작업 영역에서 사용하는 리소스에 안전하게 액세스하는 용도입니다. 연결된 리소스에는 다음과 같은 Azure RBAC 권한이 있습니다.

| 리소스 | 사용 권한 |
| ----- | ----- |
| 작업 영역 | 참가자 |
| 스토리지 계정 | Storage Blob 데이터 기여자 |
| 주요 자격 증명 모음 | 모든 키, 비밀, 인증서에 대한 액세스 |
| Azure Container Registry | 참가자 |
| 작업 영역을 포함하는 리소스 그룹 | 참가자 |

시스템 할당 관리 ID는 Azure Machine Learning과 다른 Azure 리소스 간의 내부 서비스 간 인증에 사용됩니다. ID 토큰은 사용자가 액세스할 수 없으며 이러한 리소스에 대한 액세스 권한을 얻는 데 사용할 수 없습니다. 사용자는 충분한 RBAC 권한이 있는 경우 [Azure Machine Learning 제어 및 데이터 평면 API](how-to-assign-roles.md)를 통해서만 리소스에 액세스할 수 있습니다.

연결된 리소스를 프로비전하고 [웹 서비스 엔드포인트용 Azure Container Instances를 배포](how-to-deploy-azure-container-instance.md)하려면 관리 ID에 작업 영역이 포함된 리소스 그룹에 대한 기여자 권한이 필요합니다.

이전 표에 언급된 리소스의 관리 ID에 대한 액세스를 관리자가 취소하지 않는 것이 좋습니다. 액세스 복원은 [키 다시 동기화 작업](how-to-change-storage-access-key.md)을 통해 가능합니다.

> [!NOTE]
> Azure Machine Learning 작업 영역에 __2021년 5월 14일 이전__ 에 생성된 컴퓨팅 대상(컴퓨팅 클러스터, 컴퓨팅 인스턴스, Azure Kubernetes Service 등)이 있는 경우 추가 Azure Active Directory 계정이 있을 수도 있습니다. 계정 이름은 `Microsoft-AzureML-Support-App-`으로 시작하며 모든 작업 영역 지역에 대한 구독에 기여자 수준 액세스 권한이 있습니다.
> 
> 작업 영역에 AKS(Azure Kubernetes Service)가 연결되어 있지 않으면 이 Azure AD 계정을 안전하게 삭제할 수 있습니다. 
> 
> 작업 영역에 AKS 클러스터가 연결되어 있고 _2021년 5월 14일 이전에 생성_ 된 경우 __이 Azure AD 계정을 삭제하지 마세요__. 이 시나리오에서는 먼저 AKS 클러스터를 삭제하고 다시 만들어야 Azure AD 계정을 삭제할 수 있습니다.

사용자 할당 관리 ID를 사용하도록 작업 영역을 프로비전하고 관리 ID에 추가 역할을 부여할 수 있습니다(예: 기본 Docker 이미지에 대한 자체 Azure Container Registry에 액세스). 자세한 내용은 [액세스 제어에 관리 ID 사용](how-to-use-managed-identities.md)을 참조하세요.

Azure Machine Learning 컴퓨팅 클러스터에서 사용할 관리 ID를 구성할 수도 있습니다. 이 관리 ID는 작업 영역 관리 ID와 독립적입니다. 계산 클러스터를 사용하면 관리 ID로 학습 작업을 실행하는 사용자가 액세스할 수 없는 보안된 데이터 저장소와 같은 리소스에 액세스합니다. 자세한 내용은 [Azure의 스토리지 서비스에 대한 ID 기반 데이터 액세스](how-to-identity-based-data-access.md)를 참조하세요.

> [!TIP]
> Azure Machine Learning 내에서 Azure AD 및 Azure RBAC를 사용할 때는 몇 가지 예외사항이 있습니다.
> * 필요에 따라 Azure Machine Learning 컴퓨팅 인스턴스 및 계산 클러스터와 같은 컴퓨팅 리소스에 대한 __SSH__ 액세스를 사용하도록 설정할 수 있습니다. SSH 액세스는 Azure AD가 아닌 공개/개인 키 쌍을 기반으로 합니다. SSH 액세스는 Azure RBAC의 거버넌스를 받지 않습니다.
> * __키__ 또는 __토큰__ 기반 인증으로 웹 서비스(유추 엔드포인트)로 배포된 모델에 인증할 수 있습니다. 키는 정적 문자열이고, 토큰은 Azure AD 보안 개체를 사용하여 검색됩니다. 자세한 내용은 [웹 서비스로 배포된 모델에 대한 인증 구성](how-to-authenticate-web-service.md)을 참조하세요.

자세한 내용은 다음 문서를 참조하세요.
* [Azure Machine Learning 작업 영역에 대한 인증](how-to-setup-authentication.md)
* [Azure Machine Learning 대상의 액세스 관리](how-to-assign-roles.md)
* [스토리지 서비스에 연결](how-to-access-data.md)
* [학습 시 비밀에 Azure Key Vault 사용](how-to-use-secrets-in-runs.md)
* [Azure Machine Learning과 함께 Azure AD 관리 ID 사용](how-to-use-managed-identities.md)
* [웹 서비스에서 Azure AD 관리 ID 사용](how-to-use-azure-ad-identity.md)

## <a name="network-security-and-isolation"></a>네트워크 보안 및 격리

Azure Machine Learning 리소스에 대한 네트워크 액세스를 제한하려면 [Azure VNet(Virtual Network)](../virtual-network/virtual-networks-overview.md)을 사용할 수 있습니다. VNet을 사용하면 공용 인터넷에서 부분적으로 또는 완전히 격리된 네트워크 환경을 만들 수 있습니다. 이렇게 하면 솔루션에 대한 공격 표면과 데이터 반출 가능성이 줄어듭니다.

VPN(가상 사설망) 게이트웨이를 사용하여 개별 클라이언트 또는 사용자 고유의 네트워크를 VNet에 연결할 수 있습니다.

Azure Machine Learning 작업 영역은 [Azure Private Link로](../private-link/private-link-overview.md) VNet 뒤에 프라이빗 엔드포인트를 만들 수 있습니다. VNet 내에서 작업 영역에 액세스하는 데 사용할 수 있는 개인 IP 주소 집합을 제공합니다. Azure Machine Learning이 사용하는 서비스 중 일부는 Azure Private Link를 사용할 수도 있지만, 일부는 네트워크 보안 그룹 또는 사용자 정의 라우팅을 사용합니다.

자세한 내용은 다음 문서를 참조하세요.

* [가상 네트워크 격리 및 개인 정보 개요](how-to-network-security-overview.md)
* [보안 작업 영역](how-to-secure-workspace-vnet.md)
* [보안 교육 환경](how-to-secure-training-vnet.md)
* [보안 유추 환경](how-to-secure-inferencing-vnet.md)
* [보안 가상 네트워크에서 스튜디오 사용](how-to-enable-studio-virtual-network.md)
* [사용자 지정 DNS 사용](how-to-custom-dns.md)
* [방화벽 구성](how-to-access-azureml-behind-firewall.md)

<a id="encryption-at-rest"></a><a id="azure-blob-storage"></a>

## <a name="data-encryption"></a>데이터 암호화

Azure Machine Learning은 Azure 플랫폼에서 다양한 컴퓨팅 리소스 및 데이터 저장소를 사용합니다. 각 데이터 암호화가 미사용 및 전송 중 데이터 암호화를 지원하는 방법에 대한 자세한 내용은 [Azure Machine Learning을 사용한 데이터 암호화](concept-data-encryption.md)를 참조하세요.

모델을 웹 서비스로 배포할 때 전송 중인 데이터를 암호화하도록 TLS(전송 레이어 보안)를 사용하게 설정할 수 있습니다. 자세한 내용은 [보안 웹 서비스 구성](how-to-secure-web-service.md)을 참조하세요.

## <a name="vulnerability-scanning"></a>취약성 검색

[Microsoft Defender for Cloud는](../security-center/security-center-introduction.md) 하이브리드 클라우드 워크로드에서 통합 보안 관리 및 고급 위협 방지 기능을 제공합니다. Azure Machine Learning의 경우 [Azure Container Registry](../container-registry/container-registry-intro.md) 리소스 및 Azure Kubernetes Service 리소스 검사를 사용하도록 설정해야 합니다. 자세한 내용은 [Defender for Cloud의 Azure Container Registry 이미지 검색](../security-center/defender-for-container-registries-introduction.md) 및 Defender for [Cloud와 Azure Kubernetes Services 통합을 참조하세요.](../security-center/defender-for-kubernetes-introduction.md)

## <a name="audit-and-manage-compliance"></a>규정 준수 감사 및 관리

[Azure Policy](../governance/policy/index.yml) 는 Azure 리소스가 정책을 준수하는지 확인할 수 있도록 하는 거버넌스 도구입니다. Azure Machine Learning 작업 영역에서 개인 엔드포인트를 사용하는지 여부 같은 특정 구성을 허용하거나 적용하도록 정책을 설정할 수 있습니다. Azure Policy에 대한 자세한 내용은 [Azure Policy 설명서](../governance/policy/overview.md)를 참조하세요. Azure Machine Learning에 관련된 정책에 대한 자세한 내용은 [Azure Policy 준수 여부 감사 및 관리](how-to-integrate-azure-policy.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [엔터프라이즈 보안에 대한 모범 사례 Azure Machine Learning](/azure/cloud-adoption-framework/ready/azure-best-practices/ai-machine-learning-enterprise-security)
* [TLS를 사용하여 Azure Machine Learning 웹 서비스 보호](how-to-secure-web-service.md)
* [웹 서비스로 배포된 Machine Learning 모델 사용](how-to-consume-web-service.md)
* [Azure Firewall과 함께 Azure Machine Learning 사용](how-to-access-azureml-behind-firewall.md)
* [Azure Virtual Network에서 Azure Machine Learning 사용](how-to-network-security-overview.md)
* [미사용 및 전송 중인 데이터 암호화](concept-data-encryption.md)
* [Azure에서 실시간 추천 API 빌드](/azure/architecture/reference-architectures/ai/real-time-recommendation)

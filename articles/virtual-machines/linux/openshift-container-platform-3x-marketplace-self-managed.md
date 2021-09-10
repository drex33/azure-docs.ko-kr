---
title: Azure에서 OpenShift Container Platform 3.11 자체 관리형 Marketplace 제품 배포
description: Azure에서 OpenShift Container Platform 3.11 자체 관리형 Marketplace 제품을 배포 합니다.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines
ms.subservice: openshift
ms.collection: linux
ms.topic: how-to
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/14/2019
ms.author: haroldw
ms.openlocfilehash: 43c1785a80738741478e93289fd9ff239dca7719
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/23/2021
ms.locfileid: "122697798"
---
# <a name="configure-prerequisites"></a>필수 조건 구성

**적용 대상:** :heavy_check_mark: Linux VM :heavy_check_mark: 유연한 확장 집합 

Azure에서 자체 관리형 OpenShift Container Platform 3.11 클러스터를 배포하기 위해 Marketplace 제품 을 사용하기 전에는 몇 가지 필수 구성 요소를 구성해야 합니다.  SSH 키(암호 제외), Azure Key Vault, key vault 비밀, 서비스 원칙을 만드는 방법에 대한 자세한 내용은 [OpenShift 필수 구성 요소](./openshift-container-platform-3x-prerequisites.md) 문서를 참조하세요.

 
## <a name="deploy-using-the-marketplace-offer"></a>Marketplace 제품을 사용한 배포

자체 관리형 OpenShift Container Platform 3.11 클러스터를 Azure에 배포하는 가장 간단한 방법은 Azure Marketplace 제품을 이용하는 것입니다.

해당 옵션은 가장 간단하지만 사용자 지정 기능이 제한되어 있습니다. Marketplace 제품은 OpenShift Container Platform 3.11.82를 배포하며 다음 구성 옵션을 포함합니다.

- **마스터 노드**: 구성 가능한 인스턴스 유형이 있는 3개의 마스터 노드입니다.
- **인프라 노드**: 구성 가능한 인스턴스 유형이 있는 3개의 인프라 노드입니다.
- **노드**: 노드의 수(1~9)와 인스턴스 유형을 구성할 수 있습니다.
- **디스크 유형**: 관리 디스크가 사용됩니다.
- **네트워킹**: 신규 또는 기존의 네트워크 및 사용자 지정 CIDR 범위를 지원합니다.
- **CNS**: CNS를 사용하도록 설정할 수 있습니다.
- **메트릭**: Hawkular 메트릭을 사용할 수 있습니다.
- **로깅**: EFK 로깅을 사용할 수 있습니다.
- **Azure 클라우드 공급자**: 기본적으로 사용하도록 설정되어 있으며 사용하지 않도록 설정할 수 있습니다.

Azure Portal 왼쪽 위에서 **리소스 만들기** 를 클릭하고, 검색 상자에 'openshift container platform'을 입력한 다음 Enter 키를 누릅니다.

   ![새 리소스 검색](media/openshift-marketplace-self-managed/ocp-search.png)  
<br>

목록에 **Red Hat OpenShift Container Platform 3.11 자체 관리형** 이 있는 채로 결과 페이지가 열립니다. 

   ![새 리소스 검색 결과](media/openshift-marketplace-self-managed/ocp-searchresult.png)  
<br>

제품을 클릭하여 세부 정보를 확인합니다. 해당 제품을 배포하려면, **만들기** 를 클릭합니다. 필요한 매개 변수를 입력할 UI가 표시됩니다. 첫 번째 화면은 **기본 사항** 블레이드입니다.

   ![제품 이름 페이지](media/openshift-marketplace-self-managed/ocp-titlepage.png)  
<br>

**기본 사항**

입력 매개 변수에 대한 도움말을 보려면 매개 변수 이름 옆의 ***i*** 에 커서를 올려 놓습니다.

입력 매개 변수 값을 입력하고 **확인** 을 클릭합니다.

| 입력 매개 변수입니다. | 매개 변수 설명 |
|-----------------------|-----------------|
| VM 관리 사용자 이름 | 모든 VM 인스턴스에서 만들 관리자 사용자 |
| 관리 사용자의 SSH 퍼블릭 키 | VM에 로그인할 때 사용할 SSH 퍼블릭 키로, 암호가 없어야 합니다. |
| Subscription | 클러스터를 배포할 Azure 구독 |
| 리소스 그룹 | 클러스터 리소스를 위해 새 리소스 그룹을 만들거나 비어 있는 기존 리소스 그룹을 선택합니다. |
| 위치 | 클러스터를 배포할 Azure 지역 |

   ![제품 기본 사항 블레이드](media/openshift-marketplace-self-managed/ocp-basics.png)  
<br>

**인프라 설정**

입력 매개 변수 값을 입력하고 **확인** 을 클릭합니다.

| 입력 매개 변수입니다. | 매개 변수 설명 |
|-----------------------|-----------------|
| OCP 클러스터 이름 접두사 | 모든 노드의 호스트 이름을 구성할 때 사용할 클러스터 접두사입니다. 1~20자 사이입니다. |
| 마스터 노드 크기 | 기본 VM 크기를 적용하거나 **크기 변경** 을 클릭해 다른 VM 크기를 선택합니다.  워크로드에 적당한 VM 크기를 선택합니다. |
| 인프라 노드 크기 | 기본 VM 크기를 적용하거나 **크기 변경** 을 클릭해 다른 VM 크기를 선택합니다.  워크로드에 적당한 VM 크기를 선택합니다. |
| 애플리케이션 노드 수 | 기본 VM 크기를 적용하거나 **크기 변경** 을 클릭해 다른 VM 크기를 선택합니다.  워크로드에 적당한 VM 크기를 선택합니다. |
| 애플리케이션 노드 크기 | 기본 VM 크기를 적용하거나 **크기 변경** 을 클릭해 다른 VM 크기를 선택합니다.  워크로드에 적당한 VM 크기를 선택합니다. |
| 베스천 호스트 크기 | 기본 VM 크기를 적용하거나 **크기 변경** 을 클릭해 다른 VM 크기를 선택합니다.  워크로드에 적당한 VM 크기를 선택합니다. |
| 신규 가상 네트워크 또는 기존의 가상 네트워크 | 새 vNet(기본값)을 만들거나 기존의 vNet을 사용하기 |
| 기본 CIDR 설정을 선택하거나 CIDR(IP 범위)을 사용자 지정합니다. | 기본 CIDR 범위를 사용하거나 **사용자 지정 IP 범위** 를 선택하고 사용자 지정 CIDR 정보를 입력합니다.  기본 설정을 사용하여 만든 vNet의 CIDR은 10.0.0.0/14, 마스터 서브넷은 10.1.0.0/16, 인프라 서브넷은 10.2.0.0/16, 컴퓨팅 및 CNS 서브넷은 10.3.0.0/16이 됩니다. |
| Key Vault 리소스 그룹 이름 | Key Vault가 들어 있는 리소스 그룹의 이름입니다. |
| Key Vault 이름 | SSH 프라이빗 키와 비밀이 함께 들어 있는 Key Vault 이름입니다.  알파벳과 숫자, 대시만 사용할 수 있으며 3~24자리의 문자열이어야 합니다. |
| 비밀 이름 | SSH 프라이빗 키가 들어 있는 비밀의 이름입니다.  알파벳과 숫자, 대시만 사용할 수 있습니다. |

   ![제품 인프라 블레이드](media/openshift-marketplace-self-managed/ocp-inframain.png)  
<br>

**크기 변경**

다른 VM 크기를 선택하려면 ***크기 변경** _을 클릭합니다.  VM 선택 창이 열립니다.  원하는 VM 크기를 선택하고 _*선택**을 클릭합니다.

   ![VM 크기 선택](media/openshift-marketplace-self-managed/ocp-selectvmsize.png)  
<br>

**기존 가상 네트워크**

| 입력 매개 변수입니다. | 매개 변수 설명 |
|-----------------------|-----------------|
| 기존 가상 네트워크 이름 | 기존 vNet의 이름 |
| 마스터 노드의 서브넷 이름 | 마스터 노드에 대한 기존 서브넷의 이름입니다.  IP 주소 16개 이상을 포함해야 하고 RFC 1918을 따라야 합니다. |
| 인프라 노드의 서브넷 이름 | 인프라 노드에 대한 기존 서브넷의 이름입니다.  IP 주소 32개 이상을 포함해야 하고 RFC 1918을 따라야 합니다. |
| 컴퓨팅 및 CNS 노드의 서브넷 이름 | 컴퓨팅 및 CNS 노드에 대한 기존 서브넷의 이름입니다.  IP 주소 32개 이상을 포함해야 하고 RFC 1918을 따라야 합니다. |
| 기존 가상 네트워크에 대한 리소스 그룹 | 기존 vNet가 들어 있는 리소스 그룹의 이름입니다. |

   ![제품 인프라 기존 vNet](media/openshift-marketplace-self-managed/ocp-existingvnet.png)  
<br>

**사용자 지정 IP 범위**

| 입력 매개 변수입니다. | 매개 변수 설명 |
|-----------------------|-----------------|
| 가상 네트워크의 주소 범위 | vNet에 대한 사용자 지정 CIDR |
| 마스터 노드가 들어 있는 서브넷의 주소 범위 | 마스터 서브넷에 대한 사용자 지정 CIDR |
| 인프라 노드가 들어 있는 서브넷의 주소 범위 | 인프라 서브넷에 대한 사용자 지정 CIDR |
| 컴퓨팅 및 CNS 노드가 들어 있는 서브넷의 주소 범위 | 컴퓨팅 및 CNS 노드에 대한 사용자 지정 CIDR |

   ![제품 인프라 사용자 지정 IP 범위](media/openshift-marketplace-self-managed/ocp-customiprange.png)  
<br>

**OpenShift Container Platform 3.11**

입력 매개 변수 값을 입력하고 **확인** 을 클릭합니다.

| 입력 매개 변수입니다. | 매개 변수 설명 |
|-----------------------|-----------------|
| OpenShift 관리 사용자 암호 | 초기 OpenShift 사용자의 암호입니다.  해당 사용자는 클러스터 관리자이기도 합니다. |
| OpenShift 관리 사용자 암호 확인 | OpenShift 관리 사용자 암호를 다시 입력합니다. |
| Red Hat 구독 관리자 사용자 이름 | Red Hat 구독 또는 조직 ID에 액세스하기 위한 사용자 이름입니다.  해당 자격 증명은 RHEL 인스턴스를 구독에 등록하는 데 사용되며 Microsoft 또는 Red Hat에서 저장하지 않습니다. |
| Red Hat 구독 관리자 사용자 암호 | Red Hat 구독 또는 활성화 키에 액세스하기 위한 암호입니다.  해당 자격 증명은 RHEL 인스턴스를 구독에 등록하는 데 사용되며 Microsoft 또는 Red Hat에서 저장하지 않습니다. |
| Red Hat 구독 관리자 OpenShift 풀 ID | OpenShift Container Platform 자격을 포함하는 풀 ID입니다. 클러스터 설치에 대한 OpenShift Container Platform 권한이 충분한지 확인합니다. |
| broker/마스터 노드에 대한 Red Hat 구독 관리자 Openshift 풀 ID | broker/마스터 노드에 대한 Openshift Container Platform 자격이 들어 있는 풀 ID입니다. 클러스터 설치를 위해 OpenShift Container Platform 권한이 충분한지 확인합니다. broker/마스터 풀 ID를 사용하지 않는 경우, 애플리케이션 노드에 대한 풀 ID를 입력합니다. |
| Azure 클라우드 공급자 구성 | Azure 클라우드 공급자를 사용하도록 OpenShift를 구성합니다. 영구적 볼륨에 대해 Azure 디스크 연결을 사용하는 경우 필요합니다.  기본값은 [예]입니다. |
| Azure AD 서비스 주체 클라이언트 ID GUID | Azure AD 서비스 주체 클라이언트 ID GUID(AppID 라고도 함). Azure 클라우드 공급자 구성 설정이 **예** 일 경우에만 필요합니다. |
| Azure AD 서비스 주체 클라이언트 ID 비밀 | Azure AD 서비스 주체 클라이언트 ID 비밀입니다. Azure 클라우드 공급자 구성 설정이 **예** 일 경우에만 필요합니다. |
 
   ![제품 OpenShift 블레이드](media/openshift-marketplace-self-managed/ocp-ocpmain.png)  
<br>

**추가 설정**

추가 설정 블레이드를 통해 glusterfs 스토리지, 로깅, 메트릭, 라우터 하위 도메인에 대한 CNS를 구성할 수 있습니다.  기본값에서는 해당 옵션을 설치하지 않으며 nip.io를 테스트용 라우터 서브 도메인으로 사용합니다. CNS을 사용하도록 설정하면 glusterfs pod를 호스트하는 추가 연결 디스크 세 장을 사용해 세 개의 추가 컴퓨팅 노드를 설치합니다.  

입력 매개 변수 값을 입력하고 **확인** 을 클릭합니다.

| 입력 매개 변수입니다. | 매개 변수 설명 |
|-----------------------|-----------------|
| CNS(컨테이너 기본 스토리지) 구성 | OpenShift 클러스터에 CNS를 설치하여 스토리지로 사용하도록 설정합니다. Azure 공급자를 사용하지 않도록 설정한 경우, 기본값이 됩니다. |
| 클러스터 로깅 구성 | 클러스터에 EFK 로깅 기능을 설치합니다.  EFK pod를 호스트하도록 적절히 인프라 노드의 크기를 조정합니다. |
| 클러스터에 대한 메트릭 구성 | OpenShift 클러스터에 Hawkular 메트릭을 설치합니다.  Hawkular 메트릭 pod를 호스트하도록 적절히 인프라 노드의 크기를 조정합니다. |
| 기본 라우터 하위 도메인 | 테스트를 위해 nip.io를 선택하거나 사용자 지정을 선택하여 프로덕션에 사용할 자체 하위 도메인을 입력합니다. |
 
   ![제품 추가 설정 블레이드](media/openshift-marketplace-self-managed/ocp-additionalmain.png)  
<br>

**추가 설정 - 추가 매개 변수**

| 입력 매개 변수입니다. | 매개 변수 설명 |
|-----------------------|-----------------|
| (CNS) 노드 크기 | 기본 노드 크기를 적용하거나 **크기 변경** 을 클릭해 새 VM 크기를 선택합니다. |
| 사용자 지정 하위 도메인 입력 | OpenShift 클러스터의 라우터를 통해 애플리케이션을 노출하는 데 사용할 사용자 지정 라우팅 도메인입니다.  적절한 와일드카드 DNS 항목을 만들어야 합니다. |
 
   ![제품 추가 CNS 설치](media/openshift-marketplace-self-managed/ocp-additionalcnsall.png)  
<br>

**요약**

해당 단계에서 유효성 검사를 실시해 코어 할당량이 해당 클러스터에 대해 선택한 수량의 VM을 모두 배포하는 데 충분한지 확인합니다.  입력한 매개 변수를 모두 검토합니다.  입력이 허용되는 경우, **확인** 을 클릭하여 계속합니다.

   ![제품 요약 블레이드](media/openshift-marketplace-self-managed/ocp-summary.png)  
<br>

**구입**

구입 페이지에서 연락처 정보를 확인하고 **구매** 를 클릭하여 사용 약관에 동의한 뒤 OpenShift Container Platform 클러스터 배포를 시작합니다.

   ![제품 구매 블레이드](media/openshift-marketplace-self-managed/ocp-purchase.png)  
<br>


## <a name="connect-to-the-openshift-cluster"></a>OpenShift 클러스터에 연결

배포가 완료되면 배포의 출력 섹션에서 연결을 검색합니다. **OpenShift Console URL** 을 사용해 브라우저를 OpenShift 콘솔에 연결합니다. 베스천 호스트에 SSH를 사용하도록 설정할 수도 있습니다. 다음은 관리자 사용자 이름이 clusteradmin이고 bastion 공용 IP DNS FQDN이 bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com인 예제입니다.

```bash
$ ssh clusteradmin@bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>리소스 정리

리소스 그룹, OpenShift 클러스터 및 모든 관련된 리소스가 더 이상 필요하지 않은 경우 [az group delete](/cli/azure/group) 명령을 사용하여 제거합니다.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>다음 단계

- [배포 후 작업](./openshift-container-platform-3x-post-deployment.md)
- [Azure에서 OpenShift 배포 문제 해결](./openshift-container-platform-3x-troubleshooting.md)
- [OpenShift Container Platform 시작](https://docs.openshift.com)
- 

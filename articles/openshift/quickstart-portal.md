---
title: Azure Portal 사용하여 Azure Red Hat OpenShift 클러스터 배포
description: Azure Portal 사용하여 Azure Red Hat OpenShift 클러스터 배포
author: rahulmehta
ms.author: rahulmehta
ms.service: azure-redhat-openshift
ms.topic: quickstart
ms.date: 11/30/2021
ms.custom: mode-portal
ms.openlocfilehash: 7e7d0ed6f61d970c933954823576e149b111cac8
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133359765"
---
# <a name="quickstart-deploy-an-azure-red-hat-openshift-aro-cluster-using-the-azure-portal"></a>빠른 시작: Azure Portal을 사용하여 ARO(Azure Red Hat OpenShift) 클러스터 배포

ARO(Azure Red Hat OpenShift)는 클러스터를 신속하게 배포하고 관리할 수 있는 관리형 OpenShift 서비스입니다. 이 빠른 시작에서는 Azure Portal 사용하여 Azure Red Hat OpenShift 클러스터를 배포합니다.

## <a name="prerequisites"></a>필수 조건
[Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="create-an-azure-red-hat-openshift-cluster"></a>Azure Red Hat OpenShift 클러스터 만들기
1.  Azure Portal 메뉴 또는 **홈페이지의** 왼쪽 위 페이지에 있는 세 개의 가로 막대 아래에서 **모든 서비스를** 선택합니다.
2.  **컨테이너** > **Azure Red Hat OpenShift** 를 선택합니다.
3.  **기본** 페이지에서 다음 옵션을 구성합니다.
    * **프로젝트 세부 정보**:
        *   **Azure 구독** 을 선택합니다.
        *   **Azure 리소스 그룹**(예: *myResourceGroup*)을 선택하거나 만듭니다.
    * **클러스터 세부 정보**:
        * ARO 클러스터에 대한 **지역** 을 선택합니다.
        *   *myAROCluster* 같은 OpenShift **클러스터 이름** 을 입력합니다.
        *   **도메인 이름** 을 입력합니다.
        *   **마스터 VM 크기** 및 **작업자 VM 크기** 를 선택합니다.

![Azure Portal의 **기본 사항** 탭](./media/Basics.png)

4.  인증 **페이지에서** 다음 옵션을 구성합니다. a. 애플리케이션 서비스 주체 아래의 서비스 주체 유형 선택기에서 기존 항목 또는 **새로 만들기를** **선택합니다.** **참고:** 애플리케이션 서비스 주체는 Azure Active Directory 애플리케이션과 연결된 서비스 주체입니다. 자세한 내용은 Azure 제품을 참조하세요.     
    설명서 b. **클러스터 끌어오기 비밀에서** 끌어오기 비밀 입력 **참고:** Red Hat 끌어오기 비밀을 사용하면 클러스터가 추가 콘텐츠와 함께 Red Hat 컨테이너 레지스트리에 액세스할 수 있습니다.
    다. Azure Red Hat Open Shift RP를 검색하고 선택합니다.
    d. Azure Red Hat OpenShift 리소스 공급자는 시스템 변수입니다. Azure Red Hat OpenShift RP의 기본값을 선택해야 하므로 선택기를 변경할 필요가 없습니다. 암호 필드에 대한 항목이 필요하지 않습니다.

![Azure Portal의 **인증** 탭](./media/Authentication.png)

5.  **네트워킹** 탭에서 다음을 구성해야 합니다. **참고:** OpenShift 4를 실행하는 Azure Red Hat OpenShift 클러스터에는 두 개의 빈 서브넷이 있는 가상 네트워크가 필요합니다. 하나는 마스터용이고 다른 하나는 작업자 노드용입니다.

![Azure Portal의 **네트워킹** 탭](./media/Networking.png)

6.  **태그** 섹션에서 태그를 추가하여 리소스를 구성합니다.

![Azure Portal의 **태그** 탭](./media/Tags.png)
 
7.  **검토 + 만들기** 를 클릭한 후 유효성 검사가 완료되면 **만들기** 를 선택합니다.

![Azure Portal의 **검토 + 만들기** 탭](./media/Review+Create.png)
 
8.  Azure Red Hat OpenShift 클러스터를 만드는 데 약 35-45분이 걸립니다. 배포가 완료되면 다음 중 하나를 수행하여 리소스로 이동합니다.
    *   **리소스로 이동** 을 클릭하거나
    *   Azure Red Hat OpenShift 클러스터 리소스 그룹으로 검색하고 Azure Red Hat OpenShift 리소스를 선택합니다.
        *   아래 클러스터 대시보드의 예: *myResourceGroup* 을 검색하고 *myAROCluster* 리소스를 선택합니다.

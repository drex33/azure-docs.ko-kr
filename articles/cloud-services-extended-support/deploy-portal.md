---
title: Azure Cloud Service(추가 지원) 배포 - Azure Portal
description: Azure Portal을 사용하여 Azure Cloud Service(추가 지원)를 배포합니다.
ms.topic: tutorial
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 4e35814e898e35a7ba9251d3eb36f6bde4d086e1
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132339994"
---
# <a name="deploy-a-azure-cloud-services-extended-support-using-the-azure-portal"></a>Azure Portal을 사용하여 Azure Cloud Services(추가 지원) 배포
이 문서에서는 Azure Portal을 사용하여 Azure Cloud Service(추가 지원) 배포를 만드는 방법에 대해 설명합니다. 

## <a name="before-you-begin"></a>시작하기 전에

Cloud Services(추가 지원)에 대한 [배포 필수 구성 요소](deploy-prerequisite.md)를 검토하고 관련 리소스를 만듭니다. 

## <a name="deploy-a-cloud-services-extended-support"></a>Cloud Services(추가 지원) 배포 
1. [Azure 포털](https://portal.azure.com)

2.  Azure Portal 위쪽에 있는 검색 창을 사용하여 **Cloud Services(추가 지원)** 를 검색하고 선택합니다.

    :::image type="content" source="media/deploy-portal-1.png" alt-text="Azure Portal의 모든 리소스 블레이드를 보여 주는 이미지":::
 
3.  Cloud Services(추가 지원) 창에서 **만들기** 를 선택합니다. 

    :::image type="content" source="media/deploy-portal-2.png" alt-text="마켓플레이스에서 클라우드 서비스를 구매하는 방법을 보여 주는 이미지":::

4. Cloud Services(추가 지원) 만들기 창이 **기본** 탭에 열립니다. 
    - 구독을 선택합니다.
    - 리소스 그룹을 선택하거나 새 리소스 그룹을 만듭니다.
    - Cloud Service(추가 지원) 배포에 대해 원하는 이름을 입력합니다.
        - 클라우드 서비스의 DNS 이름은 구분되어 있고, 공용 IP 주소의 DNS 이름 레이블로 지정되며, [구성] 탭의 [공용 IP] 섹션에서 수정할 수 있습니다.
    -  배포할 지역을 선택합니다.

    :::image type="content" source="media/deploy-portal-3.png" alt-text="Cloud Services(추가 지원) 홈 블레이드를 보여 주는 이미지":::

5. 클라우드 서비스 구성, 패키지 및 정의 파일을 추가합니다. Blob 스토리지에서 기존 파일을 추가하거나 로컬 컴퓨터에서 업로드할 수 있습니다. 로컬 컴퓨터에서 업로드하는 경우 이러한 파일은 스토리지 계정에 저장됩니다. 

    :::image type="content" source="media/deploy-portal-4.png" alt-text="만드는 동안 기본 탭의 업로드 섹션을 보여 주는 이미지":::

6. 모든 필드가 완료되면 **구성** 탭으로 이동하여 완료합니다. 
    - Cloud Service와 연결할 가상 네트워크를 선택하거나 새 가상 네트워크를 만듭니다. 
        - Cloud Service(추가 지원) 배포는 가상 네트워크에 **있어야 합니다**. 가상 네트워크는 `NetworkConfiguration` 섹션 아래의 서비스 구성(.cscfg) 파일에서도 **참조해야 합니다**.
    - Cloud Service와 연결할 기존 공용 IP 주소를 선택하거나 새 공용 IP 주소를 만듭니다.
        - **IP 입력 엔드포인트** 가 서비스 정의(.csdef) 파일에 정의되어 있는 경우 Cloud Service에 대한 공용 IP 주소를 만들어야 합니다. 
        - Cloud Services(추가 지원)는 기본 IP 주소 SKU만 지원합니다.
        - 서비스 구성(.cscfg)에 예약된 IP 주소가 포함되는 경우 공용 IP에 대한 할당 유형을 **정적** 으로 설정해야 합니다. 
        - 필요에 따라 클라우드 서비스와 연결된 공용 IP 주소의 DNS 레이블 속성을 업데이트하여 클라우드 서비스 엔드포인트에 대한 DNS 이름을 할당합니다.  
    - (선택 사항) Cloud Service를 시작합니다. 만든 후에 즉시 서비스를 시작하거나 시작하지 않도록 선택합니다.
    - Key Vault를 선택합니다. 
        - 서비스 구성(.cscfg) 파일에서 하나 이상의 인증서를 지정하는 경우 Key Vault가 필요합니다. 키 자격 증명 모음이 선택되면 해당 지문을 기반으로 하여 서비스 구성(.cscfg) 파일에서 선택한 인증서를 찾으려고 시도합니다. 인증서가 키 자격 증명 모음에 없는 경우 지금 업로드하고 **새로 고침** 을 클릭할 수 있습니다.   

 :::image type="content" source="media/deploy-portal-5.png" alt-text="Cloud Services(추가 지원)를 만들 때 Azure Portal의 구성 블레이드를 보여 주는 이미지":::

7. 모든 필드가 완료되면 **검토 및 만들기** 탭으로 이동하여 배포 구성의 유효성을 검사하고 Cloud Service(추가 지원)를 만듭니다.

## <a name="next-steps"></a>다음 단계 
- Cloud Services(추가 지원)에 대한 [질문과 대답](faq.yml)을 검토합니다.
- [Azure Portal](deploy-portal.md), [PowerShell](deploy-powershell.md), [템플릿](deploy-template.md) 또는 [Visual Studio](deploy-visual-studio.md)를 사용하여 Cloud Service(추가 지원)를 배포합니다.
- [Cloud Services(추가 지원) 샘플 리포지토리](https://github.com/Azure-Samples/cloud-services-extended-support) 방문

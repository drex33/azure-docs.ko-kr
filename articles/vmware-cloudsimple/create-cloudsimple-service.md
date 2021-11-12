---
title: Azure VMware Solution by CloudSimple - CloudSimple 서비스 만들기
description: Azure Portal에서 CloudSimple 서비스를 만드는 방법에 대해 알아봅니다. 시작하기 전에 필요한 구성을 검토합니다.
author: suzizuber
ms.author: v-szuber
ms.date: 08/19/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 4f7196142d70aee5a4e96c59c8cd4dc3d05f902d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/11/2021
ms.locfileid: "132322667"
---
# <a name="create-the-azure-vmware-solution-by-cloudsimple-service"></a>Azure VMware Solution by CloudSimple 만들기

Azure VMware Solution by CloudSimple을 시작하려면 Azure Portal에서 CloudSimple 서비스의 Azure VMware 솔루션을 만듭니다.

## <a name="before-you-begin"></a>시작하기 전에

게이트웨이 서브넷에 /28 CIDR 블록을 할당합니다. 게이트웨이 서브넷은 CloudSimple 서비스별로 필요하며 생성된 지역에 대해 고유합니다. 게이트웨이 서브넷은 에지 네트워크 서비스에 사용되며 /28 CIDR 블록이 필요합니다. 게이트웨이 서브넷 주소 공간은 고유해야 합니다. CloudSimple 환경과 통신하는 네트워크와 겹치지 않아야 합니다. CloudSimple과 통신하는 네트워크에는 온-프레미스 네트워크 및 Azure 가상 네트워크가 포함됩니다.

## <a name="sign-in-to-azure"></a>Azure에 로그인

[Azure Portal](https://portal.azure.com)에 로그인합니다.

## <a name="create-the-service"></a>서비스 만들기

1. **모든 서비스** 를 선택합니다.
2. **CloudSimple 서비스** 를 검색합니다.
    ![CloudSimple 서비스 검색](media/create-cloudsimple-service-search.png)
3. **CloudSimple 서비스** 를 선택합니다.
4. **추가** 를 클릭하여 새 서비스를 만듭니다.
    ![CloudSimple 서비스 추가](media/create-cloudsimple-service-add.png)
5. CloudSimple 서비스를 만들려는 구독을 선택합니다.
6. 서비스에 대한 리소스 그룹을 선택합니다. 새 리소스 그룹을 추가하려면 **새로 만들기** 를 클릭합니다.
7. 서비스를 식별하는 이름을 입력합니다.
8. 서비스 게이트웨이의 CIDR을 입력합니다. 온-프레미스 서브넷, Azure 서브넷 또는 계획된 CloudSimple 서브넷과 겹치지 않는 /28 서브넷을 지정합니다. 서비스가 생성된 후에는 CIDR을 변경할 수 없습니다.

    ![CloudSimple 서비스 만들기](media/create-cloudsimple-service.png)
9. **확인** 을 클릭합니다.

서비스가 만들어지고 서비스 목록에 추가됩니다.

## <a name="next-steps"></a>다음 단계

* [노드 프로비저닝](create-nodes.md) 방법 알아보기
* [프라이빗 클라우드 생성](create-private-cloud.md) 방법 알아보기
* [프라이빗 클라우드 환경 구성](quickstart-create-private-cloud.md) 방법 알아보기

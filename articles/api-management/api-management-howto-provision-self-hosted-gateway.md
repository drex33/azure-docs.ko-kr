---
title: Azure API Management에서 자체 호스팅 게이트웨이 프로비저닝 | Microsoft Docs
description: Azure API Management에서 자체 호스팅 게이트웨이를 프로비저닝하는 방법을 알아봅니다.
services: api-management
documentationcenter: ''
author: dlepow
manager: gwallace
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 03/31/2020
ms.author: danlep
ms.openlocfilehash: 93e7feb2d1b91cb8d5fa58d52244c901e1eeaea2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/24/2021
ms.locfileid: "128598752"
---
# <a name="provision-a-self-hosted-gateway-in-azure-api-management"></a>Azure API Management에서 자체 호스팅 게이트웨이 프로비저닝

자체 호스팅 게이트웨이를 배포하려면 Azure API Management 인스턴스에서 게이트웨이 리소스를 사전에 프로비저닝해야 합니다. 이 문서에서는 API Management에서 게이트웨이 리소스를 프로비저닝하는 단계를 설명합니다.

## <a name="prerequisites"></a>사전 요구 사항

다음 빠른 시작을 완료합니다. [Azure API Management 인스턴스 만들기](get-started-create-service-instance.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="provision-a-self-hosted-gateway"></a>자체 호스팅 게이트웨이 프로비전

1. **배포 및 인프라** 에서 **게이트웨이** 를 선택합니다.
2. **+ 추가** 를 클릭합니다.
3. 게이트웨이의 **이름** 및 **지역** 을 입력합니다.
> [!TIP]
> **지역** 은 이 게이트웨이 리소스와 연결될 게이트웨이 노드의 위치를 지정합니다. Azure 리소스와 연결된 유사한 속성과 의미상 동일하지만 임의의 문자열 값이 할당될 수 있습니다.

4. 필요에 따라 게이트웨이 리소스에 대한 **설명** 을 입력합니다.
5. 필요에 따라 **API** 에서 **+** 를 선택하여 하나 이상의 API를 이 게이트웨이 리소스와 연결합니다.
> [!IMPORTANT]
> 기본적으로 기존 API는 새 게이트웨이 리소스와 연결되지 않습니다. 따라서 새 게이트웨이를 통해 호출하려고 하면 `404 Resource Not Found` 응답이 발생합니다.

6. **추가** 를 클릭합니다.

이제 API Management 인스턴스에 게이트웨이 리소스가 프로비저닝되었습니다. 게이트웨이 배포를 계속할 수 있습니다.

## <a name="next-steps"></a>다음 단계

* 자체 호스팅 게이트웨이에 대한 자세한 내용은 [Azure API Management 자체 호스팅 게이트웨이 개요](self-hosted-gateway-overview.md)를 참조하세요.
* [Kubernetes에 자체 호스팅 게이트웨이를 배포](how-to-deploy-self-hosted-gateway-kubernetes.md)하는 방법을 알아보세요.
- [Azure Arc 지원 Kubernetes 클러스터에 자체 호스팅 게이트웨이를 배포하는 방법](how-to-deploy-self-hosted-gateway-azure-arc.md)에 대해 자세히 알아봅니다.
* [Docker에 자체 호스팅 게이트웨이를 배포](how-to-deploy-self-hosted-gateway-docker.md)하는 방법을 알아보세요.

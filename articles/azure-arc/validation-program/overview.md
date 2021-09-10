---
title: Azure Arc 지원 서비스 유효성 검사 개요
description: Arc 지원 Kubernetes, Data Services 및 클러스터 확장을 준수하는 Azure Arc 유효성 검사 프로세스에 대해 설명합니다.
ms.date: 07/30/2021
ms.topic: overview
ms.openlocfilehash: 1cf208b9a476f4a2a16df157ebfa0bb7661cfc1c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121788229"
---
# <a name="overview-of-azure-arc-enabled-service-validation"></a>Azure Arc 지원 서비스 유효성 검사 개요

Azure Arc 지원 서비스는 유효성이 검사된 플랫폼에서 실행하는 것이 좋습니다. 이 문서에서는 다양한 Azure Arc 지원 구성 요소의 유효성을 검사하는 방법을 설명하는 콘텐츠에 대해 설명합니다. 

현재 유효성이 검사된 솔루션은 Kubernetes 및 데이터 서비스에 대한 파트너가 제공합니다.

## <a name="kubernetes"></a>Kubernetes

Azure Arc 지원 Kubernetes는 모든 CNCF(Cloud Native Computing Foundation) 인증 Kubernetes 클러스터에서 작동합니다. Azure Arc 팀은 주요 업계 Kubernetes 제품 공급자와 협력하여 [Kubernetes 배포](../kubernetes/validation-program.md)에서 Azure Arc 지원 Kubernetes의 유효성을 검사했습니다. 이러한 공급자가 릴리스한 Kubernetes 배포의 이후 주 버전과 부 버전은 Azure Arc 지원 Kubernetes와의 호환성을 위해 유효성을 검사합니다.

## <a name="data-services"></a>Data services

또한 OEM(주문자 상표 부착 방식) 파트너 및 스토리지 공급자와 협력하여 [Azure Arc 지원 데이터 서비스](../data/validation-program.md) 솔루션의 유효성을 검사했습니다.

## <a name="validation-process"></a>유효성 검사 프로세스

Azure Arc 유효성 검사 프로세스는 GitHub에서 사용할 수 있습니다. Azure Arc, 테스트 도구 및 전략을 사용하여 제품의 유효성을 검사하는 방법에 대한 자세한 내용은 GitHub에서 [Azure Arc 유효성 검사 프로세스](https://github.com/Azure/azure-arc-validation/)를 참조하세요.

## <a name="next-steps"></a>다음 단계

* [유효한 Kubernetes 배포](../kubernetes/validation-program.md?toc=/azure/azure-arc/toc.json&bc=/azure/azure-arc/breadcrumb/toc.json)

* [데이터 서비스에 대해 유효성이 검사된 Kubernetes 배포](../data/validation-program.md?toc=/azure/azure-arc/toc.json&bc=/azure/azure-arc/breadcrumb/toc.json)

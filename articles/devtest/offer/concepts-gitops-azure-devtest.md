---
title: GitOps & Azure 개발/테스트 제안
description: Azure 개발/테스트와 연결하여 GitOps 사용
author: jamestramel
ms.author: jametra
ms.prod: visual-studio-windows
ms.topic: how-to
ms.date: 10/20/2021
ms.custom: devtestoffer
ms.openlocfilehash: df3fd830c04f90315126bd68a07a22a95cbfbc53
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/02/2021
ms.locfileid: "131102960"
---
# <a name="using-gitops-with-azure-devtest-offer-to-optimize-and-secure-applications"></a>Azure 개발/테스트 제안에서 GitOps를 사용하여 애플리케이션 최적화 및 보호

GitOps는 운영 프레임워크입니다. 애플리케이션 개발에 사용되는 DevOps 모범 사례를 사용하고 인프라 자동화에 적용합니다.  

팀에서 GitOps를 연습할 때 코드로 저장된 구성 파일(코드로서의 인프라)을 사용합니다. 이러한 파일은 배포될 때마다 동일한 환경을 생성합니다. 빌드할 때마다 동일한 애플리케이션 이진 파일을 생성하는 애플리케이션 소스 코드와 같다고 생각하면 됩니다.  

## <a name="gitops-methodology"></a>GitOps 방법론  

이 프로세스 또는 방법론은 Git 리포지토리를 사용합니다. 이러한 리포지토리는 애플리케이션에 대해 정의하는 상태 및 구성에 대한 정보의 소스입니다. 프로덕션에 필요한 인프라에 대한 선언적 설명이 포함되어 있습니다. 자동화된 프로세스는 해당 환경이 리포지토리의 설명된 상태와 일치하게 만듭니다.  

새 애플리케이션을 배포하거나 기존 애플리케이션을 업데이트하려면 리포지토리만 업데이트하면 됩니다. 자동화된 프로세스는 다른 모든 것을 처리합니다.  

## <a name="benefits-of-gitops"></a>GitOps의 이점  

- 인프라 변경에 대한 협업을 사용하도록 설정  
- 향상된 액세스 제어  
- 출시 시간 단축  
- 위험 감소  
- 비용 절감  
- 오류 발생 가능성이 적습니다.  

## <a name="use-gitops-with-devtest"></a>개발/테스트와 함께 GitOps 사용  

프로세스 및 프레임워크인 GitOps를 비프로덕션 인스턴스에 적용해야 합니다. DevTest 환경에서 확인하거나 사용할 수 있습니다. GitOps 원칙을 사용하여 DevOps 프로세스를 개선할 수 있습니다. GitOps 원칙과 함께 DevTest 혜택 및 환경을 사용하여 활동을 최적화하고 애플리케이션의 보안 및 안정성을 유지합니다.  

GitOps는 자동화와 git 같은 일반적으로 사용되는 협업 프레임워크를 결합합니다. 엔터프라이즈 보안 표준을 준수하면서 클라우드 인프라를 신속하게 제공할 수 있도록 결합할 수 있습니다.  

GitOps 및 Azure에 대해 자세히 알아보세요.  

- [Azure Friday 비디오: GitOps를 사용하여 Kubernetes를 사용하도록 Azure Arc](https://azure.microsoft.com/resources/videos/azure-friday-azure-arc-enabled-kubernetes-with-gitops/)  
- [Azure Friday 블로그: GitOps를 사용하여 Kubernetes를 사용하도록 Azure Arc](https://techcommunity.microsoft.com/t5/azure-arc/azure-arc-enabled-kubernetes-with-gitops/ba-p/1654171?ocid=AID754288&wt.mc_id=azfr-c9-scottha&wt.mc_id=CFID0570)  
- [Azure 인프라 수명 주기 자동화용 GitOps](https://github.com/travisnielsen/azure-gitops)

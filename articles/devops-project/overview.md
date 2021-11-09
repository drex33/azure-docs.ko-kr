---
title: Azure용 DevOps Starter 개요 | Microsoft Docs
description: DevOps Starter의 가치를 이해합니다.
services: devops-project
documentationcenter: ''
author: georgewallace
manager: gwallace
editor: mlearned
ms.assetid: ''
ms.service: az-devops-project
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: ''
ms.date: 03/24/2020
ms.author: gwallace
ms.openlocfilehash: 04fe33686d640ab84c0bdb532486a15b5582a612
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/09/2021
ms.locfileid: "132057141"
---
# <a name="overview-of-devops-starter"></a>DevOps Starter 개요

 DevOps Starter를 사용하면 Azure에서 GitHub Actions 또는 Azure DevOps를 사용하여 쉽게 시작할 수 있습니다. 이 기능을 사용하면 Azure Portal에서 단 몇 단계로 원하는 Azure 서비스에 좋아하는 앱을 빠르게 시작할 수 있습니다. 

 DevOps Starter는 애플리케이션 개발, 배포 및 모니터링에 필요한 모든 것을 설정합니다. DevOps Starter 대시보드를 사용하여 Azure Portal에서 한 번에 코드 커밋, 빌드 및 배포를 모니터링할 수 있습니다.

## <a name="advantages-of-using-devops-starter"></a>DevOps Starter 사용 시 장점

  DevOps Starter는 배포를 자동화하기 위해 다음 두 개의 CI/CD 공급자를 지원합니다.
  * [GitHub Actions](https://github.com/features/actions)
  * [Azure DevOps](https://azure.microsoft.com/services/devops)

  DevOps Starter는 Azure에 대한 애플리케이션의 전체 CI(연속 통합) 및 CD(지속적인 업데이트) 설정을 자동화합니다.  기존 코드를 사용하여 시작하거나 제공된 샘플 애플리케이션 중 하나를 사용할 수 있습니다. 그런 다음, Virtual Machines, App Service, AKS(Azure Kubernetes Services), Azure SQL Database 및 Azure Service Fabric 등의 다양한 Azure 서비스에 해당 애플리케이션을 신속하게 배포할 수 있습니다.  

  DevOps Starter는 DevOps 파이프라인의 모든 초기 구성 작업을 수행합니다. 즉, Azure Portal에서 DevOps Starter 대시 보드를 만들어 초기 Git 리포지토리 설정, CI/CD 파이프라인 구성, 모니터링을 위한 Application Insights 리소스 만들기 및 전체 솔루션에 대한 단일 보기 제공과 같은 모든 작업이 수행됩니다.

DevOps Starter를 사용하여 다음을 수행할 수 있습니다.

* Azure에 애플리케이션 신속 배포
* CI/CD 워크플로 또는 파이프라인의 설정 자동화
* CI/CD 워크플로 또는 파이프라인을 적절하게 설정하는 방법 확인 및 이해
* 특정 시나리오에 따라 릴리스 파이프라인을 추가로 사용자 지정

## <a name="how-to-use-devops-starter"></a>DevOps Starter를 사용하는 방법

  DevOps Starter는 Azure Portal에서 사용할 수 있습니다. 포털에서 다른 Azure 리소스를 만들 때처럼 DevOps Starter 리소스를 만듭니다. DevOps Projects는 다양한 구성 옵션에 대해 마법사와 같은 단계별 환경을 제공합니다.  

초기 설정의 일부로 여러 구성 옵션을 선택합니다. 이러한 옵션에는 다음이 포함됩니다.

* 즐겨찾는 CI/CD 공급자 선택
* 제공된 샘플 앱 사용 또는 사용자 고유의 코드 가져오기(Azure DevOps 전용)
* 앱 언어 선택
* 언어를 기준으로 앱 프레임워크 선택
* Azure 서비스 선택(배포 대상)
* GitHub 또는 Azure DevOps 조직 선택
* Azure 구독 선택
* Azure 서비스 위치 선택
* Azure 서비스에 대한 다양한 가격 책정 계층에서 선택

DevOps Starter를 만든 후 다음을 수행할 수 있습니다.

* GitHub 워크플로 또는 Azure DevOps 파이프라인 사용자 지정
* 끌어오기 요청을 사용하여 코드 흐름을 관리하고 높은 품질을 유지함
* 코드를 병합하여 품질 표시줄을 높이기 전에 각 커밋 테스트 및 빌드

DevOps Starter를 사용하여 Azure Portal의 DevOps Starter 대시보드에서 모든 리소스를 한 곳에서 삭제할 수 있습니다.

## <a name="devops-starter-and-github-integration"></a>DevOps Starter 및 GitHub 통합

DevOps Starter는 이제 CI/CD 공급자인 GitHub Actions를 지원합니다. GitHub Actions를 사용하여 CI/CD 워크플로를 설정하기 위해 GitHub에서 필요한 모든 작업을 자동화합니다. 기존 GitHub 조직에서 GitHub 리포지토리를 만든 다음, 샘플 애플리케이션을 새 GitHub 리포지토리에 커밋합니다.  

또한 자동화는 모든 새 코드 커밋을 통해 워크플로 내에서 빌드 및 배포 작업을 시작하도록 워크플로에 대한 트리거를 설정합니다. 애플리케이션은 선택한 Azure 서비스에 배포됩니다. GitHub 워크플로는 추가 시나리오에 맞게 사용자 지정할 수 있습니다. 

## <a name="devops-starter-and-azure-devops-integration"></a>DevOps Starter 및 Azure DevOps 통합

Azure DevOps를 사용하는 DevOps Starter는 CI/CD 파이프라인을 설정하기 위해 Azure Pipelines에서 필요한 모든 작업을 자동화합니다. 새 또는 기존 Azure DevOps 조직에서 Git 리포지토리를 만들고, 샘플 애플리케이션 또는 기존 코드를 새 Git 리포지토리에 커밋합니다.  

자동화는 또한 모든 새로운 코드 커밋이 빌드를 시작할 수 있도록 빌드를 위한 CI 트리거를 설정합니다. DevOps Starter는 CD 트리거를 만들고 원하는 Azure 서비스에 모든 새로운 성공적인 빌드를 배포합니다.  

빌드 및 릴리스 파이프라인은 추가 시나리오에 맞게 사용자 지정할 수 있습니다. 또한 다른 프로젝트에서 사용할 수 있도록 빌드 및 릴리스 파이프라인을 복제할 수 있습니다.

## <a name="getting-started-with-devops-starter"></a>DevOps Starter 시작

* [DevOps Starter 시작](./azure-devops-project-github.md)

##  <a name="devops-starter-videos"></a>DevOps Starter 비디오

* [Azure DevOps Starter를 사용하여 CI/CD 만들기](https://www.youtube.com/watch?v=NuYDAs3kNV8)

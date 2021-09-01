---
title: Azure Lab Services의 랩 정보 | Microsoft Docs
description: 클라우드에서 클래스룸 랩 환경을 빠르게 설정하는 방법을 알아봅니다. 클래스에 필요한 소프트웨어로 템플릿 VM을 사용하여 랩을 구성하고 클래스의 각 학생이 VM의 복사본을 사용할 수 있도록 합니다.
ms.topic: overview
ms.date: 08/26/2021
ms.openlocfilehash: 6e91d833a86f2f326ca966b36679946ed1455f6e
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2021
ms.locfileid: "122965049"
---
# <a name="introduction-to-labs"></a>랩 소개
Azure Lab Services를 사용하면 클라우드에서 클래스룸 랩 환경을 빠르게 설정할 수 있습니다. 강사가 클래스룸 랩을 만들고, Windows 또는 Linux 가상 머신을 프로비전하고, 수업에 필요한 소프트웨어 및 도구 랩을 설치하고, 학생들이 사용할 수 있게 합니다. 학생들은 클래스룸 랩에서 랩의 VM(가상 머신)에 연결하여 프로젝트, 과제, 클래스룸 연습에 사용합니다. 

랩은 Azure에서 관리되는 관리형 랩 형식입니다. 서비스 자체는 VM(가상 머신) 가동에서 오류 처리 및 인프라 확장에 이르기까지 관리되는 랩 형식에 대한 모든 인프라 관리를 처리합니다. 필요한 인프라 종류를 지정하고 수업에 필요한 도구 또는 소프트웨어를 설치합니다. 

## <a name="automatic-management-of-azure-infrastructure-and-scale"></a>Azure 인프라 및 규모에 대한 자동 관리 
Azure Lab Services는 랩의 기본 인프라에 대한 프로비저닝 및 관리가 서비스에 의해 자동으로 처리되는 관리형 서비스입니다. 귀하는 사용자에게 적합한 랩 환경을 준비하는 데만 집중할 수 있습니다. 나머지는 서비스가 처리하도록 맡겨두고 랩의 가상 머신을 대상 그룹에 롤아웃할 수 있습니다. 한 번의 클릭으로 랩을 수백 대의 가상 머신으로 확장할 수 있습니다.

## <a name="simple-experience-for-your-lab-users"></a>랩 사용자를 위한 간단한 환경 
랩에 초대된 사용자는 랩 내에 제공되는 리소스에 즉시 액세스할 수 있습니다. 로그인만 하면 여러 랩에서 액세스할 수 있는 가상 머신의 전체 목록을 볼 수 있습니다. 단추 하나만 클릭하면 가상 머신에 연결하여 작업을 시작할 수 있습니다. 사용자가 서비스를 사용하는 데 Azure 구독은 필요하지 않습니다. 

## <a name="cost-optimization-and-tracking"></a>비용 최적화 및 추적  
랩 사용자가 가상 머신을 사용할 수 있는 시간을 정확히 제어하여 예산을 감독할 수 있습니다. 사용자가 지정된 시간 슬롯 동안만 가상 머신을 사용하도록 랩에 일정을 설정하거나 되풀이 자동 종료 및 시작 시간을 설정할 수 있습니다. 개별 사용자의 사용량을 추적하고 한도를 설정할 수 있습니다.

## <a name="example-class-types"></a>예제 클래스 형식
Azure Lab Services를 사용하여 여러 유형의 클래스에 대한 랩을 설정할 수 있습니다. Azure Lab Services를 사용하여 랩을 설정할 수 있는 몇 가지 클래스 형식의 예제는 [Azure Lab Services의 클래스 형식 예제](class-types.md) 문서를 참조하세요. 

## <a name="next-steps"></a>다음 단계
Azure Lab Services를 사용하여 클래스룸 랩을 만드는 데 필요한 랩 계정을 설정합니다.

- [랩 계정 설정](tutorial-setup-lab-account.md)

---
title: Azure Lab Services를 사용하여 딥 러닝에 초점을 맞춘 랩을 설정 | Microsoft Docs
description: Azure Lab Services를 사용하여 NLP(자연어 처리)의 딥 러닝에 초점을 맞춘 랩을 설정하는 방법을 알아봅니다.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: f087cf890e622634b92b5b9c10db6a6e8621ece1
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130176950"
---
# <a name="set-up-a-lab-focused-on-deep-learning-in-natural-language-processing-using-azure-lab-services"></a>Azure Lab Services를 사용하여 자연어 처리의 딥 러닝에 초점을 맞춘 랩을 설정
이 문서는 Azure Lab Services를 사용하여 NLP(자연어 처리)의 딥 러닝에 초점을 맞춘 랩을 설정하는 방법을 안내합니다. NLP(자연어 처리)는 번역, 음성 인식 및 기타 언어 이해 기능을 갖춘 컴퓨터를 가능케하는 AI(인공 지능) 형태입니다.  

NLP 클래스를 수강하는 학생은 Linux VM(가상 머신)을 통해 작성된 인간 언어 분석에 사용되는 딥 러닝 모델을 개발하는 신경망 알고리즘을 적용하는 방법을 알아봅니다. 

## <a name="lab-configuration"></a>랩 구성
이 랩을 설정하려면 Azure 구독을 시작해야 합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다. Azure 구독이 있으면 Azure Lab Services에서 새 랩 계정을 만들거나 기존 랩 계정을 사용할 수 있습니다. 새 랩 계정 만들기에 대한 다음 자습서를 참조하세요. [랩 계정 설정에 대한 자습서](tutorial-setup-lab-account.md)
 
랩 계정을 만든 후 랩 계정에서 다음 설정을 사용하도록 설정합니다. 

| 랩 계정 설정 | Instructions |
| ----------- | ------------ |  
| Marketplace 이미지 | 랩 계정 내에서 Linux용 Data Science Virtual Machine(Ubuntu) 이미지를 사용하도록 설정합니다.  자세한 내용은 다음 문서를 참조하세요. [랩 작성자에 사용할 수 있는 Marketplace 이미지 지정](specify-marketplace-images.md) | 

[이 자습서](tutorial-setup-classroom-lab.md)에 따라 새 랩을 만들고 다음 설정을 적용합니다.

| 랩 설정 | 값/지침 | 
| ------------ | ------------------ |
| VM(가상 머신) 크기 | **소형 GPU(컴퓨팅)** . 이 크기는 AI와 Deep Learning 같은 컴퓨팅 및 네트워크를 많이 사용하는 애플리케이션에 가장 적합합니다. |
| VM 이미지 | [Linux(Ubuntu)용 Data Science Virtual Machine](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-dsvm.ubuntu-1804) 이 이미지는 기계 학습 및 데이터 과학을 위한 딥 러닝 프레임워크 및 도구를 제공합니다. 이 이미지에 설치된 도구에 대한 전체 목록을 보려면 다음 문서를 참조하세요. [DSVM에는 무엇이 포함되나요?](../machine-learning/data-science-virtual-machine/overview.md#whats-included-on-the-dsvm) |
| 원격 데스크톱 연결을 사용하도록 설정 | <p>교사와 학생이 GUI 원격 데스크톱을 사용하여 연결할 수 있도록 데이터 과학 이미지가 이미 X2Go를 사용하도록 구성되어 있습니다.  X2Go는 **원격 데스크톱 연결 사용** 설정을 사용하도록 설정할 필요가 *없습니다*.  이 설정은 RDP를 대신 사용하도록 선택하는 경우에만 사용하도록 설정해야 합니다.

>**중요**: 데이터 과학 이미지에 X2Go를 사용하는 것이 좋지만 RDP를 대신 사용하도록 선택하는 경우 처음으로 SSH를 사용하여 Linux VM에 연결하고 RDP 및 GUI 패키지를 설치해야 합니다.  그런 다음, 나중에 RDP를 사용하여 Linux VM에 연결할 수 있습니다.  자세한 내용은 [Linux VM에 대해 그래픽 원격 데스크톱 사용](how-to-enable-remote-desktop-linux.md)을 참조하세요.

Linux 이미지용 Data Science Virtual Machine은 이 클래스 형식에 필요한 딥 러닝 프레임워크 및 도구를 제공합니다. 따라서 템플릿 머신을 만든 후에는 이를 추가로 사용자 지정할 필요가 없습니다. 학생들이 사용할 수 있도록 게시할 수 있습니다. 템플릿 페이지에서 **게시** 단추를 선택하여 랩에 템플릿을 게시합니다.  

## <a name="cost"></a>비용
이 랩의 비용을 예측하려는 경우 다음 예제를 사용할 수 있습니다. 

학생이 25명이며 예약된 클래스 시간이 20시간 및 숙제 또는 할당이 10시간인 경우 랩 가격은 - 학생 25명 * (20+10)시간 * 139 랩 단위 * $0.01달러/시간 = $1042.5달러입니다.

가격 책정에 대한 자세한 내용은 [Azure Lab Services 가격 책정](https://azure.microsoft.com/pricing/details/lab-services/)을 참조 하세요.

## <a name="conclusion"></a>결론
이 문서에서는 자연어 처리 클래스에 대한 랩을 만드는 단계를 안내합니다. 다른 딥 러닝 클래스에 대해 유사한 설정을 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계
다음 단계는 모든 랩 설정에 공통됩니다.

- [사용자 추가](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [할당량 설정](how-to-configure-student-usage.md#set-quotas-for-users)
- [일정 설정](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [이메일로 학생에게 등록 링크 보내기](how-to-configure-student-usage.md#send-invitations-to-users) 


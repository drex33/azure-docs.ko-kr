---
title: 관계형 데이터베이스의 데이터베이스 관리를 교육하도록 랩 설정 | Microsoft Docs
description: 관계형 데이터베이스의 관리를 교육하도록 랩을 설정하는 방법을 알아봅니다.
author: emaher
ms.topic: how-to
ms.date: 06/26/2020
ms.author: enewman
ms.openlocfilehash: bd14a53dffbc7d3480bd12dc35ed686d95b3184c
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/19/2021
ms.locfileid: "130177026"
---
# <a name="set-up-a-lab-to-teach-database-management-for-relational-databases"></a>관계형 데이터베이스의 데이터베이스 관리를 교육하도록 랩 설정

이 문서에서는 Azure Lab Services에서 기본 데이터베이스 관리 클래스에 대한 랩을 설정하는 방법을 설명합니다. 데이터베이스 개념은 대학의 대부분의 컴퓨터 과학 부서에서 학습하는 소개 과정 중 하나입니다. SQL(구조적 쿼리 언어)은 국제 표준입니다. SQL은 데이터베이스의 콘텐츠 추가, 액세스 및 관리를 포함하여 데이터베이스 관리와 관련된 표준 언어입니다.  이는 빠른 처리, 검증된 안정성, 편의성 및 사용의 유연성으로 알려져 있습니다.

이 문서에서는 MySQL 데이터베이스 서버와 SQL Server 2019 서버를 둘 다 사용하여 랩에서 가상 머신 템플릿을 설정하는 방법을 보여 줍니다.  [MySQL](https://www.mysql.com/)은 무료로 사용할 수 있는 오픈 소스 RDBMS(관계형 데이터베이스 관리 시스템)입니다.  [SQL Server 2019](https://www.microsoft.com/sql-server/sql-server-2019)는 최신 버전의 Microsoft RDBMS입니다.

## <a name="lab-configuration"></a>랩 구성

이 랩을 설정하려면 Azure 구독과 랩 계정으로 시작해야 합니다. Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/)을 만듭니다. Azure 구독이 있으면 Azure Lab Services에서 새 랩 계정을 만들 수 있습니다. 새 랩 계정 만들기에 관한 자세한 내용은 [랩 계정 설정에 대한 자습서](tutorial-setup-lab-account.md)를 참조하세요.  기존 랩 계정을 사용할 수도 있습니다.

### <a name="lab-account-settings"></a>랩 계정 설정

랩 계정에 대해 아래 표에 설명된 설정을 사용합니다. Marketplace 이미지를 사용하도록 설정하는 방법에 대한 자세한 내용은 [랩 작성자가 사용할 수 있는 Marketplace 이미지 지정](./specify-marketplace-images.md)을 참조하세요.

| 랩 계정 설정 | Instructions |
| ------------------- | ------------ |
|Marketplace 이미지| ‘Windows Server 2019의 SQL Server 2019 Standard’ 이미지를 랩 계정 내에서 사용하도록 설정합니다.|

### <a name="lab-settings"></a>랩 설정

클래스룸 랩을 설정하는 경우 아래 표의 설정을 사용합니다.  클래스룸 랩을 만드는 방법에 대한 자세한 내용은 [클래스룸 랩 설정 자습서](tutorial-setup-classroom-lab.md)를 참조하세요.

| 랩 설정 | 값/지침 |
| ------------ | ------------------ |
|Virtual Machine 크기| 중간. 이 크기는 관계형 데이터베이스, 메모리 내 캐시 및 분석에 가장 적합합니다.|
|가상 머신 이미지| Windows Server 2019의 SQL Server 2019 Standard|

## <a name="template-machine-configuration"></a>템플릿 머신 구성

Windows Server 2019에 MySQL을 설치하기 위해 [가상 머신에서 MySQL Community Server 설치 및 실행](/previous-versions/azure/virtual-machines/windows/classic/mysql-2008r2?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json#install-and-run-mysql-community-server-on-the-virtual-machine)에 설명된 단계를 수행할 수 있습니다.

SQL Server 2019는 새 랩을 만들 때 선택한 가상 머신 이미지에 사전 설치되어 있습니다.

## <a name="cost-estimate"></a>예상 비용

이 수업의 가능한 예상 비용을 살펴보겠습니다.  25명의 학생이 있는 수업을 예로 들겠습니다.  예약된 수업 시간은 20시간입니다.  예약된 수업 시간 외에도 숙제 또는 과제에 사용하도록 각 학생에게 10시간이 할당됩니다.  선택한 가상 머신 크기는 랩 단위가 42개인 중간 크기입니다.

다음 예제는 이 수업의 가능한 예상 비용입니다.

학생 25명\* (예약 시간 20시간 + 할당 시간 10시간) \* 0.42 USD/시간 = 315.00 USD

가격 책정에 대한 자세한 내용은 [Azure Lab Services 가격 책정](https://azure.microsoft.com/pricing/details/lab-services/)을 참조 하세요.

## <a name="conclusion"></a>결론

이 문서에서는 MySQL과 SQL Server를 둘 다 사용하여 기본 데이터베이스 관리 개념에 대한 랩을 만드는 데 필요한 단계를 안내했습니다. 다른 데이터베이스 클래스에 대해 유사한 설정을 사용할 수 있습니다.

## <a name="next-steps"></a>다음 단계

다음 단계는 모든 랩 설정에 공통됩니다.

- [템플릿 만들기 및 관리](how-to-create-manage-template.md)
- [사용자 추가](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [할당량 설정](how-to-configure-student-usage.md#set-quotas-for-users)
- [일정 설정](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [메일로 학생에게 등록 링크 보내기](how-to-configure-student-usage.md#send-invitations-to-users)
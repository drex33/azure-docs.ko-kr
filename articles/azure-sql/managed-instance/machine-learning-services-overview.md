---
title: Azure SQL Managed Instance의 Machine Learning Services
description: 이 문서에서는 Azure SQL Managed Instance의 개요 또는 Machine Learning Services를 제공합니다.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MashaMSFT
ms.author: mathoma
ms.reviewer: mathoma, davidph
manager: cgronlun
ms.date: 03/17/2021
ms.openlocfilehash: aa7e0be21192dac9a29efa439da92d3b65bded9a
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2021
ms.locfileid: "133360816"
---
# <a name="machine-learning-services-in-azure-sql-managed-instance"></a>Azure SQL Managed Instance의 Machine Learning Services

Machine Learning Services는 Python 및 R 스크립트를 모두 지원하는 데이터베이스 내 기계 학습을 제공하는 Azure SQL Managed Instance의 기능입니다. 이 기능에는 고성능 예측 분석 및 기계 학습을 위한 Microsoft Python 및 R 패키지가 포함됩니다. 관계형 데이터는 저장 프로시저, Python 또는 R 문이 포함된 T-SQL 스크립트 또는 T-SQL이 포함된 Python 또는 R 코드를 통해 스크립트에서 사용할 수 있습니다.

## <a name="what-is-machine-learning-services"></a>Machine Learning Services란?

Azure SQL Managed Instance의 Machine Learning Services를 사용하면 데이터베이스에서 Python 및 R 스크립트를 실행할 수 있습니다. 이를 사용하여 데이터를 준비 및 정리하고, 기능 엔지니어링을 수행하고, 데이터베이스 내에서 기계 학습 모델을 학습, 평가 및 배포할 수 있습니다. 이 기능은 데이터가 상주하는 스크립트를 실행하고 네트워크를 통해 다른 서버에 데이터를 전송하는 작업을 제거합니다.

Azure SQL Managed Instance에서 R/Python 지원과 함께 Machine Learning Services를 사용하여 다음을 수행합니다.

- **R 및 Python 스크립트를 실행하여 데이터 준비 및 범용 데이터 처리** - 이제 R 및 Python 스크립트를 실행하기 위해 데이터를 다른 서버로 이동하지 않고도 데이터가 있는 Azure SQL Managed Instance로 R/Python 스크립트를 가져올 수 있습니다. 대기 시간, 보안 및 규정 준수와 관련된 데이터 이동 및 관련 문제의 필요성을 제거할 수 있습니다.

- **데이터베이스에서 기계 학습 모델 학습** - 모든 오픈 소스 알고리즘을 사용하여 모델을 학습할 수 있습니다. 데이터베이스에서 가져온 샘플 데이터 세트에 의존하지 않고 전체 데이터 세트로 학습을 쉽게 확장할 수 있습니다.

- **모델 및 스크립트를 저장 프로시저의 프로덕션에 배포** - 스크립트와 학습된 모델을 T-SQL 저장 프로시저에 포함하여 간단히 운영할 수 있습니다. Azure SQL Managed Instance에 연결하는 앱은 저장 프로시저를 호출하기만 하면 이러한 모델의 예측 및 인텔리전스를 활용할 수 있습니다. 네이티브 T-SQL PREDICT 함수를 사용하여 동시성 높은 실시간 점수 매기기 시나리오에서 빠른 점수 매기기를 위해 모델을 운영할 수도 있습니다.

Python 및 R의 기본 배포판은 Machine Learning Services에 포함되어 있습니다. Microsoft 패키지 [revoscalepy](/sql/machine-learning/python/ref-py-revoscalepy) 및 [microsoftml](/sql/machine-learning/python/ref-py-microsoftml)(Python용), [RevoScaleR](/sql/machine-learning/r/ref-r-revoscaler), [MicrosoftML](/sql/machine-learning/r/ref-r-microsoftml), [olapR](/sql/machine-learning/r/ref-r-olapr) 및 [sqlrutils](/sql/machine-learning/r/ref-r-sqlrutils)(R용) 외에 PyTorch, TensorFlow 및 scikit-learn과 같은 오픈 소스 패키지 및 프레임워크를 설치하고 사용할 수 있습니다.

## <a name="how-to-enable-machine-learning-services"></a>기계 학습 서비스를 사용하도록 설정하는 방법

다음 SQL 명령으로 확장성을 사용하도록 설정하여 Azure SQL Managed Instance에서 Machine Learning Services를 사용하도록 설정할 수 있습니다(SQL Managed Instance가 다시 시작되고 몇 초 동안 사용할 수 없음).

```sql
sp_configure 'external scripts enabled', 1;
RECONFIGURE WITH OVERRIDE;
```

이 명령이 SQL Managed Instance 리소스에 미치는 영향에 대한 자세한 내용은 [리소스 거버넌스](machine-learning-services-differences.md#resource-governance)를 참조하세요.

### <a name="enable-machine-learning-services-in-a-failover-group"></a>장애 조치(failover) 그룹에서 Machine Learning Services 사용

[장애 조치(failover) 그룹](failover-group-add-instance-tutorial.md)에서 시스템 데이터베이스는 보조 인스턴스에 복제되지 않습니다(자세한 내용은 [장애 조치(failover) 그룹 제한](../database/auto-failover-group-overview.md#limitations-of-failover-groups) 참조).

사용 중인 관리되는 인스턴스가 장애 조치(failover) 그룹의 일부인 경우 다음을 수행합니다.

- 기계 학습 서비스를 사용하려면 장애 조치(failover) 그룹의 각 인스턴스에서 `sp_configure` 및 `RECONFIGURE` 명령을 실행합니다.

- master 데이터베이스가 아닌 사용자 데이터베이스에 R/Python 라이브러리를 설치합니다.

## <a name="next-steps"></a>다음 단계

- [SQL Server Machine Learning Services와의 주요 차이](machine-learning-services-differences.md)를 참조하세요.
- Machine Learning Services에서 Python을 사용하는 방법을 알아보려면 [Python 스크립트 실행](/sql/machine-learning/tutorials/quickstart-python-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)을 참조하세요.
- Machine Learning Services에서 R을 사용하는 방법을 알아보려면 [R 스크립트 실행](/sql/machine-learning/tutorials/quickstart-r-create-script?context=/azure/azure-sql/managed-instance/context/ml-context&view=azuresqldb-mi-current&preserve-view=true)을 참조하세요.
- 다른 SQL 플랫폼에서의 기계 학습에 대한 자세한 내용은 [SQL 기계 학습 문서](/sql/machine-learning/index)를 참조하세요.

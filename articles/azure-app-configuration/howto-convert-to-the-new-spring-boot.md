---
title: Spring Boot 라이브러리로 변환
titleSuffix: Azure App Configuration
description: 이전 버전에서 새로운 App Configuration Spring Boot 라이브러리로 변환하는 방법을 알아보세요.
ms.service: azure-app-configuration
author: mrm9084
ms.author: mametcal
ms.topic: how-to
ms.date: 07/08/2021
ms.openlocfilehash: 1bff486cb226de6bb9b6c8a0f065dbca134bd684
ms.sourcegitcommit: cc099517b76bf4b5421944bd1bfdaa54153458a0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/09/2021
ms.locfileid: "113553056"
---
# <a name="convert-to-new-app-configuration-spring-boot-library"></a>새로운 App Configuration Spring Boot 라이브러리로 변환

이제 Spring Boot용 pp Configuration 라이브러리의 새 버전을 사용할 수 있습니다. 이 버전에는 푸시 새로 고침과 같은 새로운 기능과 여러 가지 주요 변경 사항이 도입되었습니다. 이러한 변경 사항은 이전 라이브러리 버전을 사용하는 구성 설정과 역호환되지 않습니다. 다음 항목을 참조하세요.

* 그룹 및 아티팩트 Id
* 스프링 프로필
* 구성 로드 및 다시 로드
* 기능 플래그 로드

이 문서에서는 새 라이브러리 버전으로 마이그레이션하는 데 필요한 변경 및 작업에 대한 참조를 제공합니다.

## <a name="group-and-artifact-id-changed"></a>그룹 및 아티팩트 ID 변경 됨

모든 Azure Spring Boot 라이브러리에는 새 형식과 일치하도록 그룹 및 아티팩트 ID가 업데이트되었습니다. 새 패키지 이름은 다음과 같습니다.

```xml
<dependency>
    <groupId>com.azure.spring</groupId>
    <artifactId>azure-spring-cloud-appconfiguration-config</artifactId>
    <version>2.0.0-beta.2</version>
</dependency>
<dependency>
    <groupId>com.azure.spring</groupId>
    <artifactId>azure-spring-cloud-appconfiguration-config-web</artifactId>
    <version>2.0.0-beta.2</version>
</dependency>
```

## <a name="use-of-spring-profiles"></a>Spring 프로필 사용

이전 릴리스에서는 Spring 프로필이 구성 파일의 형식과 일치할 수 있도록 구성의 일부로 사용되었습니다. 예제:

```properties
/<application name>_dev/config.message
```

쿼리의 기본 레이블이 Spring 프로필과 일치하는 레이블이 있는 다음 형식의 Spring 프로필이 되도록 변경되었습니다.

```properties
/application/config.message
```

 새 형식으로 변환 하기 위해 상점 이름으로 아래 명령을 실행할 수 있습니다.

```azurecli
az appconfig kv export -n your-stores-name -d file --format properties --key /application_dev* --prefix /application_dev/ --path convert.properties --skip-features --yes
az appconfig kv import -n your-stores-name -s file --format properties --label dev --prefix /application/ --path convert.properties --skip-features --yes
```

또는 포털에서 Import/Export 기능을 사용 합니다.

새 버전으로 완전히 이동되면 다음을 실행하여 이전 키를 제거할 수 있습니다.

```azurecli
az appconfig kv delete -n ConversionTest --key /application_dev/*
```

이 명령은 삭제하려는 모든 키를 나열하므로 예기치 않은 키가 제거되지 않는지 확인할 수 있습니다. 포털에서 키를 삭제할 수도 있습니다.

## <a name="which-configurations-are-loaded"></a>로드 되는 구성

`/applicaiton/*`과 일치하는 구성을 로드하는 기본 사례는 변경되지 않았습니다. 변경 사항은 설정하지 않는 한 `/${spring.application.name}/*`가 더 이상 자동으로 추가로 사용되지 않는다는 것입니다. 대신,를 사용 하려면 `/${spring.application.name}/*` 새 선택 구성을 사용할 수 있습니다.

```properties
spring.cloud.azure.appconfiguration.stores[0].selects[0].key-filter=/${spring.application.name}/*
```

## <a name="configuration-reloading"></a>구성 다시 로드

이제 모든 구성 저장소의 모니터링이 기본적으로 비활성화됩니다. 구성 저장소에서 모니터링을 활성화할 수 있도록 새 구성이 라이브러리에 추가되었습니다. 또한 캐시 만료는 새로 고침 간격으로 이름이 변경 되었으며 구성 저장소 별로도 변경 되었습니다. 또한 구성 저장소 모니터링이 사용 하도록 설정 된 경우에는 선택적 레이블을 사용 하 여 하나 이상의 감시 키를 구성 해야 합니다.

```properties
spring.cloud.azure.appconfiguration.stores[0].monitoring.enabled
spring.cloud.azure.appconfiguration.stores[0].monitoring.refresh-interval
spring.cloud.azure.appconfiguration.stores[0].monitoring.trigger[0].key
spring.cloud.azure.appconfiguration.stores[0].monitoring.trigger[0].label
```

새로 고침 간격의 작동 방식에 대 한 변경 내용이 없으므로 기능을 명확 하 게 구성의 이름을 변경 하 게 됩니다. 감시 키의 요구 사항은 구성이 변경 될 때 모든 변경이 완료 될 때까지 라이브러리에서 구성 로드를 시도 하지 않도록 합니다.

## <a name="feature-flag-loading"></a>기능 플래그 로드

기본적으로 기능 플래그를 로드 하는 기능은 사용할 수 없습니다. 또한 기능 플래그에는 새로 고침 간격 뿐만 아니라 레이블 필터도 있습니다.

```properties
spring.cloud.azure.appconfiguration.stores[0].feature-flags.enable
spring.cloud.azure.appconfiguration.stores[0].feature-flags.label-filter
spring.cloud.azure.appconfiguration.stores[0].monitoring.feature-flag-refresh-interval
```

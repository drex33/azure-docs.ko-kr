---
title: 빠른 시작 - 클라이언트 라이브러리를 사용하여 컨테이너 레지스트리 콘텐츠 관리
description: 이 빠른 시작에서는 Azure Container Registry 클라이언트 라이브러리를 사용하여 리포지토리, 이미지 및 아티팩트를 관리합니다.
author: dlepow
ms.topic: quickstart
ms.date: 10/05/2021
ms.author: danlep
zone_pivot_groups: programming-languages-set-ten
ms.custom: mode-other
ms.openlocfilehash: c288f8e18a8d9ab7c326de2ebdc1d585b9161c14
ms.sourcegitcommit: 56235f8694cc5f88db3afcc8c27ce769ecf455b0
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2021
ms.locfileid: "133075512"
---
# <a name="quickstart-use-the-azure-container-registry-client-libraries"></a>빠른 시작: Azure Container Registry 클라이언트 라이브러리 사용

::: zone pivot="programming-language-csharp, programming-language-java, programming-language-javascript, programming-language-python"

이 문서를 사용하여 Azure Container Registry 클라이언트 라이브러리를 시작합니다. 다음 단계에 따라 이미지 및 아티팩트에 대한 데이터 평면 작업 예제 코드를 사용해 보세요.

Azure Container Registry 클라이언트 라이브러리를 사용하여 다음을 수행합니다.

* 레지스트리의 이미지 또는 아티팩트 나열
* 이미지 및 아티팩트, 리포지토리 및 태그에 대한 메타데이터 가져오기
* 레지스트리 항목에 대한 읽기/쓰기/삭제 속성 설정
* 이미지 및 아티팩트, 리포지토리 및 태그 삭제

Azure Container Registry에는 레지스트리 만들기 및 업데이트를 포함한 컨트롤 플레인 작업을 위한 관리 라이브러리도 있습니다. 

## <a name="prerequisites"></a>필수 조건

* 이 라이브러리를 사용하려면 [Azure 구독](https://azure.microsoft.com/free/)과 Azure 컨테이너 레지스트리가 필요합니다.

    새 Azure 컨테이너 레지스트리를 만들려면 [Azure Portal](container-registry-get-started-portal.md), [Azure PowerShell](container-registry-get-started-powershell.md) 또는 [Azure CLI](container-registry-get-started-azure-cli.md)를 사용할 수 있습니다. 다음은 Azure CLI 사용 예입니다.

    ```azurecli
    az acr create --name MyContainerRegistry --resource-group MyResourceGroup \
        --location westus --sku Basic
    ```

* 하나 이상의 컨테이너 이미지를 레지스트리에 푸시합니다. 단계는 [Docker CLI를 사용하여 Azure Container Registry로 첫 번째 이미지 푸시](container-registry-get-started-docker-cli.md)를 참조하세요.

## <a name="key-concepts"></a>주요 개념

* Azure 컨테이너 레지스트리는 *컨테이너 이미지* 및 [OCI 아티팩트](container-registry-oci-artifacts.md)를 저장합니다. 
* 이미지 또는 아티팩트는 *매니페스트* 와 *레이어* 로 구성됩니다. 
* 매니페스트는 이미지 또는 아티팩트를 구성하는 레이어를 설명합니다. 이는 해당 *다이제스트* 를 통해 고유하게 식별됩니다. 
* 이미지 또는 아티팩트에 *태그를 지정* 하여 사람이 읽을 수 있는 별칭을 제공할 수도 있습니다. 이미지 또는 아티팩트에는 0개 이상의 연결된 태그가 있을 수 있으며, 각 태그는 이미지를 고유하게 식별합니다. 
* 동일한 이름을 공유하지만 태그가 다른 이미지 또는 아티팩트의 컬렉션이 *리포지토리* 입니다.

자세한 내용은 [레지스트리, 리포지토리 및 아티팩트 정보](container-registry-concepts.md)를 참조하세요.

::: zone-end

::: zone pivot="programming-language-csharp"

## <a name="get-started"></a>시작하기

[소스 코드][dotnet_source] | [패키지(NuGet)][dotnet_package] | [API 참조][dotnet_docs] | [샘플][dotnet_samples]

Azure Container Registry 인스턴스에 연결할 수 있는 .NET 애플리케이션 코드를 개발하려면 `Azure.Containers.ContainerRegistry` 라이브러리가 필요합니다.

### <a name="install-the-package"></a>패키지 설치

[NuGet][nuget]을 사용하여 .NET용 Azure Container Registry 클라이언트 라이브러리를 설치합니다.

```Powershell
dotnet add package Azure.Containers.ContainerRegistry --prerelease
```

## <a name="authenticate-the-client"></a>클라이언트 인증

애플리케이션에서 레지스트리에 연결하려면 인증하는 데 사용할 수 있는 `ContainerRegistryClient`를 만들어야 합니다. [Azure ID 라이브러리][dotnet_identity]를 사용하여 해당 Azure 서비스를 통해 Azure SDK 클라이언트를 인증하기 위한 Azure Active Directory 지원을 추가합니다.  

애플리케이션을 로컬로 개발 및 디버그할 때 사용자 고유의 사용자를 사용하여 레지스트리를 인증할 수 있습니다. 이 작업을 수행하는 한 가지 방법은 [Azure CLI를 사용하여 사용자를 인증](https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/identity/Azure.Identity#authenticating-via-the-azure-cli)하고 이 환경에서 애플리케이션을 실행하는 것입니다. 애플리케이션에서 `DefaultAzureCredential`을 사용하여 인증하도록 구성된 클라이언트를 사용하는 경우 지정된 엔드포인트에서 레지스트리를 사용하여 올바르게 인증합니다.  

```C#
// Create a ContainerRegistryClient that will authenticate to your registry through Azure Active Directory
Uri endpoint = new Uri("https://myregistry.azurecr.io");
ContainerRegistryClient client = new ContainerRegistryClient(endpoint, new DefaultAzureCredential(),
    new ContainerRegistryClientOptions()
    {
        Audience = ContainerRegistryAudience.AzureResourceManagerPublicCloud
    });
```

로컬 및 배포 환경 모두에서 `DefaultAzureCredential`을 사용하여 인증하는 방법에 대한 자세한 내용은 [Azure ID README][dotnet_identity]를 참조하세요. 퍼블릭이 아닌 Azure 클라우드의 레지스트리에 연결하려면 [API 참조][dotnet_docs]를 참조하세요.

Azure Container Registry에서 Azure AD를 사용하는 방법에 대한 자세한 내용은 [인증 개요](container-registry-authentication.md)를 참조하세요.

## <a name="examples"></a>예제

각 샘플에서는 `https://` 접두사와 로그인 서버 이름(예: "https://myregistry.azurecr.io")을 포함한 문자열로 설정된 `REGISTRY_ENDPOINT` 환경 변수가 있다고 가정합니다.

다음 샘플에서는 작업을 반환하는 비동기 API를 사용합니다. 동기 API도 사용할 수 있습니다.

### <a name="list-repositories-asynchronously"></a>비동기적으로 리포지토리 나열

레지스트리의 리포지토리 컬렉션을 반복합니다.

```C# Snippet:ContainerRegistry_Tests_Samples_CreateClientAsync
// Get the service endpoint from the environment
Uri endpoint = new Uri(Environment.GetEnvironmentVariable("REGISTRY_ENDPOINT"));

// Create a new ContainerRegistryClient
ContainerRegistryClient client = new ContainerRegistryClient(endpoint, new DefaultAzureCredential(),
    new ContainerRegistryClientOptions()
    {
        Audience = ContainerRegistryAudience.AzureResourceManagerPublicCloud
    });

// Get the collection of repository names from the registry
AsyncPageable<string> repositories = client.GetRepositoryNamesAsync();
await foreach (string repository in repositories)
{
    Console.WriteLine(repository);
}
```

### <a name="set-artifact-properties-asynchronously"></a>비동기적으로 아티팩트 속성 설정

```C# Snippet:ContainerRegistry_Tests_Samples_CreateClientAsync
// Get the service endpoint from the environment
Uri endpoint = new Uri(Environment.GetEnvironmentVariable("REGISTRY_ENDPOINT"));

// Create a new ContainerRegistryClient
ContainerRegistryClient client = new ContainerRegistryClient(endpoint, new DefaultAzureCredential(),
    new ContainerRegistryClientOptions()
    {
        Audience = ContainerRegistryAudience.AzureResourceManagerPublicCloud
    });

// Get the collection of repository names from the registry
AsyncPageable<string> repositories = client.GetRepositoryNamesAsync();
await foreach (string repository in repositories)
{
    Console.WriteLine(repository);
}
```

### <a name="delete-images-asynchronously"></a>비동기적으로 이미지 삭제

```C# Snippet:ContainerRegistry_Tests_Samples_DeleteImageAsync
using System.Linq;
using Azure.Containers.ContainerRegistry;
using Azure.Identity;

// Get the service endpoint from the environment
Uri endpoint = new Uri(Environment.GetEnvironmentVariable("REGISTRY_ENDPOINT"));

// Create a new ContainerRegistryClient
ContainerRegistryClient client = new ContainerRegistryClient(endpoint, new DefaultAzureCredential(),
    new ContainerRegistryClientOptions()
    {
        Audience = ContainerRegistryAudience.AzureResourceManagerPublicCloud
    });

// Iterate through repositories
AsyncPageable<string> repositoryNames = client.GetRepositoryNamesAsync();
await foreach (string repositoryName in repositoryNames)
{
    ContainerRepository repository = client.GetRepository(repositoryName);

    // Obtain the images ordered from newest to oldest
    AsyncPageable<ArtifactManifestProperties> imageManifests =
        repository.GetManifestPropertiesCollectionAsync(orderBy: ArtifactManifestOrderBy.LastUpdatedOnDescending);

    // Delete images older than the first three.
    await foreach (ArtifactManifestProperties imageManifest in imageManifests.Skip(3))
    {
        RegistryArtifact image = repository.GetArtifact(imageManifest.Digest);
        Console.WriteLine($"Deleting image with digest {imageManifest.Digest}.");
        Console.WriteLine($"   Deleting the following tags from the image: ");
        foreach (var tagName in imageManifest.Tags)
        {
            Console.WriteLine($"        {imageManifest.RepositoryName}:{tagName}");
            await image.DeleteTagAsync(tagName);
        }
        await image.DeleteAsync();
    }
}
```

::: zone-end

::: zone pivot="programming-language-java"

## <a name="get-started"></a>시작하기

[소스 코드][java_source] | [패키지(Maven)][java_package] | [API 참조][java_docs] | [샘플][java_samples]

### <a name="currently-supported-environments"></a>현재 지원되는 환경

* [JDK(Java Development Kit)][jdk_link] 버전 8 이상

### <a name="include-the-package"></a>패키지 포함

[//]: # ({x-version-update-start;com.azure:azure-containers-containerregistry;current})
```xml
<dependency>
  <groupId>com.azure</groupId>
  <artifactId>azure-containers-containerregistry</artifactId>
  <version>1.0.0-beta.3</version>
</dependency>
```
[//]: # ({x-version-update-end})

## <a name="authenticate-the-client"></a>클라이언트 인증

[Azure ID 라이브러리][java_identity]는 인증에 대한 Azure Active Directory 지원을 제공합니다.

다음 샘플에서는 `https://` 접두사와 로그인 서버 이름(예: "https://myregistry.azurecr.io")이 포함된 레지스트리 엔드포인트 문자열이 있다고 가정합니다.

<!-- embedme ./src/samples/java/com/azure/containers/containerregistry/ReadmeSamples.java#L31-L35 -->
```Java
DefaultAzureCredential credential = new DefaultAzureCredentialBuilder().build();
ContainerRegistryClient client = new ContainerRegistryClientBuilder()
    .endpoint(endpoint)
    .credential(credential)
    .buildClient();
```

<!-- embedme ./src/samples/java/com/azure/containers/containerregistry/ReadmeSamples.java#L39-L43 -->
```Java
DefaultAzureCredential credential = new DefaultAzureCredentialBuilder().build();
ContainerRegistryAsyncClient client = new ContainerRegistryClientBuilder()
    .endpoint(endpoint)
    .credential(credential)
    .buildAsyncClient();
```

Azure Container Registry에서 Azure AD를 사용하는 방법에 대한 자세한 내용은 [인증 개요](container-registry-authentication.md)를 참조하세요.

## <a name="examples"></a>예제

각 샘플에서는 `https://` 접두사와 로그인 서버 이름(예: "https://myregistry.azurecr.io")이 포함된 레지스트리 엔드포인트 문자열이 있다고 가정합니다.

### <a name="list-repository-names"></a>리포지토리 이름 나열

레지스트리의 리포지토리 컬렉션을 반복합니다.

<!-- embedme ./src/samples/java/com/azure/containers/containerregistry/ReadmeSamples.java#L47-L53 -->
```Java
DefaultAzureCredential credential = new DefaultAzureCredentialBuilder().build();
ContainerRegistryClient client = new ContainerRegistryClientBuilder()
    .endpoint(endpoint)
    .credential(credential)
    .buildClient();

client.listRepositoryNames().forEach(repository -> System.out.println(repository));
```

### <a name="set-artifact-properties"></a>아티팩트 속성 설정

<!-- embedme ./src/samples/java/com/azure/containers/containerregistry/ReadmeSamples.java#L119-L132 -->
```Java
TokenCredential defaultCredential = new DefaultAzureCredentialBuilder().build();

ContainerRegistryClient client = new ContainerRegistryClientBuilder()
    .endpoint(endpoint)
    .credential(defaultCredential)
    .buildClient();

RegistryArtifact image = client.getArtifact(repositoryName, digest);

image.updateTagProperties(
    tag,
    new ArtifactTagProperties()
        .setWriteEnabled(false)
        .setDeleteEnabled(false));
```

### <a name="delete-images"></a>이미지 삭제

<!-- embedme ./src/samples/java/com/azure/containers/containerregistry/ReadmeSamples.java#L85-L113 -->
```Java
TokenCredential defaultCredential = new DefaultAzureCredentialBuilder().build();

ContainerRegistryClient client = new ContainerRegistryClientBuilder()
    .endpoint(endpoint)
    .credential(defaultCredential)
    .buildClient();

final int imagesCountToKeep = 3;
for (String repositoryName : client.listRepositoryNames()) {
    final ContainerRepository repository = client.getRepository(repositoryName);

    // Obtain the images ordered from newest to oldest
    PagedIterable<ArtifactManifestProperties> imageManifests =
        repository.listManifestProperties(
            ArtifactManifestOrderBy.LAST_UPDATED_ON_DESCENDING,
            Context.NONE);

    imageManifests.stream().skip(imagesCountToKeep)
        .forEach(imageManifest -> {
            System.out.printf(String.format("Deleting image with digest %s.%n", imageManifest.getDigest()));
            System.out.printf("    This image has the following tags: ");

            for (String tagName : imageManifest.getTags()) {
                System.out.printf("        %s:%s", imageManifest.getRepositoryName(), tagName);
            }

            repository.getArtifact(imageManifest.getDigest()).delete();
        });
}
```

::: zone-end

::: zone pivot="programming-language-javascript"

## <a name="get-started"></a>시작하기

[소스 코드][javascript_source] | [패키지(npm)][javascript_package] | [API 참조][javascript_docs] | [샘플][javascript_samples]

### <a name="currently-supported-environments"></a>현재 지원되는 환경

- [Node.js의 LTS 버전](https://nodejs.org/about/releases/)

자세한 내용은 [지원 정책](https://github.com/Azure/azure-sdk-for-js/blob/main/SUPPORT.md)을 참조하세요.

### <a name="install-the-azurecontainer-registry-package"></a>`@azure/container-registry` 패키지를 설치합니다.

`npm`을 사용하여 JavaScript용 Container Registry 클라이언트 라이브러리를 설치합니다.

```bash
npm install @azure/container-registry
```

## <a name="authenticate-the-client"></a>클라이언트 인증

[Azure ID 라이브러리][javascript_identity]는 인증에 대한 Azure Active Directory 지원을 제공합니다.

```javascript
const { ContainerRegistryClient } = require("@azure/container-registry");
const { DefaultAzureCredential } = require("@azure/identity");

const endpoint = process.env.CONTAINER_REGISTRY_ENDPOINT;
// Create a ContainerRegistryClient that will authenticate through Active Directory
const client = new ContainerRegistryClient(endpoint, new DefaultAzureCredential());
```

Azure Container Registry에서 Azure AD를 사용하는 방법에 대한 자세한 내용은 [인증 개요](container-registry-authentication.md)를 참조하세요.

## <a name="examples"></a>예제

각 샘플에서는 `https://` 접두사와 로그인 서버 이름(예: "https://myregistry.azurecr.io")을 포함한 문자열로 설정된 `CONTAINER_REGISTRY_ENDPOINT` 환경 변수가 있다고 가정합니다.

### <a name="list-repositories-asynchronously"></a>비동기적으로 리포지토리 나열

레지스트리의 리포지토리 컬렉션을 반복합니다.

```javascript
const { ContainerRegistryClient } = require("@azure/container-registry");
const { DefaultAzureCredential } = require("@azure/identity");

async function main() {
  // endpoint should be in the form of "https://myregistryname.azurecr.io"
  // where "myregistryname" is the actual name of your registry
  const endpoint = process.env.CONTAINER_REGISTRY_ENDPOINT || "<endpoint>";
  const client = new ContainerRegistryClient(endpoint, new DefaultAzureCredential());

  console.log("Listing repositories");
  const iterator = client.listRepositoryNames();
  for await (const repository of iterator) {
    console.log(`  repository: ${repository}`);
  }
}

main().catch((err) => {
  console.error("The sample encountered an error:", err);
});
```

### <a name="set-artifact-properties-asynchronously"></a>비동기적으로 아티팩트 속성 설정

```javascript
const { ContainerRegistryClient } = require("@azure/container-registry");
const { DefaultAzureCredential } = require("@azure/identity");

async function main() {
  // Get the service endpoint from the environment
  const endpoint = process.env.CONTAINER_REGISTRY_ENDPOINT || "<endpoint>";

  // Create a new ContainerRegistryClient and RegistryArtifact to access image operations
  const client = new ContainerRegistryClient(endpoint, new DefaultAzureCredential());
  const image = client.getArtifact("library/hello-world", "v1");

  // Set permissions on the image's "latest" tag
  await image.updateTagProperties("latest", { canWrite: false, canDelete: false });
}

main().catch((err) => {
  console.error("The sample encountered an error:", err);
});
```

### <a name="delete-images-asynchronously"></a>비동기적으로 이미지 삭제

```javascript
const { ContainerRegistryClient } = require("@azure/container-registry");
const { DefaultAzureCredential } = require("@azure/identity");

async function main() {
  // Get the service endpoint from the environment
  const endpoint = process.env.CONTAINER_REGISTRY_ENDPOINT || "<endpoint>";
  // Create a new ContainerRegistryClient
  const client = new ContainerRegistryClient(endpoint, new DefaultAzureCredential());

  // Iterate through repositories
  const repositoryNames = client.listRepositoryNames();
  for await (const repositoryName of repositoryNames) {
    const repository = client.getRepository(repositoryName);
    // Obtain the images ordered from newest to oldest by passing the `orderBy` option
    const imageManifests = repository.listManifestProperties({
      orderBy: "LastUpdatedOnDescending"
    });
    const imagesToKeep = 3;
    let imageCount = 0;
    // Delete images older than the first three.
    for await (const manifest of imageManifests) {
      imageCount++;
      if (imageCount > imagesToKeep) {
        const image = repository.getArtifact(manifest.digest);
        console.log(`Deleting image with digest ${manifest.digest}`);
        console.log(`  Deleting the following tags from the image:`);
        for (const tagName of manifest.tags) {
          console.log(`    ${manifest.repositoryName}:${tagName}`);
          image.deleteTag(tagName);
        }
        await image.delete();
      }
    }
  }
}

main().catch((err) => {
  console.error("The sample encountered an error:", err);
});
```

::: zone-end

::: zone pivot="programming-language-python"

## <a name="get-started"></a>시작하기

[소스 코드][python_source] | [패키지(Pypi)][python_package] | [API 참조][python_docs] | [샘플][python_samples]

### <a name="install-the-package"></a>패키지 설치

[pip][pip_link]를 사용하여 Python용 Azure Container Registry 클라이언트 라이브러리를 설치합니다.

```bash
pip install --pre azure-containerregistry
```

## <a name="authenticate-the-client"></a>클라이언트 인증

[Azure ID 라이브러리][python_identity]는 인증에 대한 Azure Active Directory 지원을 제공합니다. `DefaultAzureCredential`에서는 `AZURE_CLIENT_ID`, `AZURE_TENANT_ID` 및 `AZURE_CLIENT_SECRET` 환경 변수가 설정되어 있다고 가정합니다. 자세한 내용은 [Azure ID 환경 변수](https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/identity/azure-identity#environment-variables)를 참조하세요.

```python
# Create a ContainerRegistryClient that will authenticate through Active Directory
from azure.containerregistry import ContainerRegistryClient
from azure.identity import DefaultAzureCredential

account_url = "https://mycontainerregistry.azurecr.io"
client = ContainerRegistryClient(account_url, DefaultAzureCredential())
```

## <a name="examples"></a>예제

각 샘플에서는 `https://` 접두사와 로그인 서버 이름(예: "https://myregistry.azurecr.io")을 포함한 문자열로 설정된 `CONTAINERREGISTRY_ENDPOINT` 환경 변수가 있다고 가정합니다.

### <a name="list-tags-asynchronously"></a>비동기적으로 태그 나열

이 샘플에서는 `hello-world` 리포지토리가 레지스트리에 있다고 가정합니다.

```python
import asyncio
from dotenv import find_dotenv, load_dotenv
import os

from azure.containerregistry.aio import ContainerRegistryClient
from azure.identity.aio import DefaultAzureCredential


class ListTagsAsync(object):
    def __init__(self):
        load_dotenv(find_dotenv())

    async def list_tags(self):
        # Create a new ContainerRegistryClient      
        audience = "https://management.azure.com"
        account_url = os.environ["CONTAINERREGISTRY_ENDPOINT"]
        credential = DefaultAzureCredential()
        client = ContainerRegistryClient(account_url, credential, audience=audience)

        manifest = await client.get_manifest_properties("library/hello-world", "latest")
        print(manifest.repository_name + ": ")
        for tag in manifest.tags:
            print(tag + "\n")
```

### <a name="set-artifact-properties-asynchronously"></a>비동기적으로 아티팩트 속성 설정

이 샘플에서는 `v1` 태그가 지정된 이미지가 있는 `hello-world` 리포지토리가 레지스트리에 있다고 가정합니다.

```python
import asyncio
from dotenv import find_dotenv, load_dotenv
import os

from azure.containerregistry.aio import ContainerRegistryClient
from azure.identity.aio import DefaultAzureCredential


class SetImagePropertiesAsync(object):
    def __init__(self):
        load_dotenv(find_dotenv())

    async def set_image_properties(self):
        # Create a new ContainerRegistryClient
        account_url = os.environ["CONTAINERREGISTRY_ENDPOINT"]
        audience = "https://management.azure.com"
        credential = DefaultAzureCredential()
        client = ContainerRegistryClient(account_url, credential, audience=audience)

        # [START update_manifest_properties]
        # Set permissions on the v1 image's "latest" tag
        await client.update_manifest_properties(
            "library/hello-world",
            "latest",
            can_write=False,
            can_delete=False
        )
        # [END update_manifest_properties]
        # After this update, if someone were to push an update to "myacr.azurecr.io\hello-world:v1", it would fail.
        # It's worth noting that if this image also had another tag, such as "latest", and that tag did not have
        # permissions set to prevent reads or deletes, the image could still be overwritten. For example,
        # if someone were to push an update to "myacr.azurecr.io\hello-world:latest"
        # (which references the same image), it would succeed.
```

### <a name="delete-images-asynchronously"></a>비동기적으로 이미지 삭제

```python
import asyncio
from dotenv import find_dotenv, load_dotenv
import os

from azure.containerregistry import ManifestOrder
from azure.containerregistry.aio import ContainerRegistryClient
from azure.identity.aio import DefaultAzureCredential


class DeleteImagesAsync(object):
    def __init__(self):
        load_dotenv(find_dotenv())

    async def delete_images(self):
        # [START list_repository_names]   
        audience = "https://management.azure.com"
        account_url = os.environ["CONTAINERREGISTRY_ENDPOINT"]
        credential = DefaultAzureCredential()
        client = ContainerRegistryClient(account_url, credential, audience=audience)

        async with client:
            async for repository in client.list_repository_names():
                print(repository)
                # [END list_repository_names]

                # [START list_manifest_properties]
                # Keep the three most recent images, delete everything else
                manifest_count = 0
                async for manifest in client.list_manifest_properties(repository, order_by=ManifestOrder.LAST_UPDATE_TIME_DESCENDING):
                    manifest_count += 1
                    if manifest_count > 3:
                        await client.delete_manifest(repository, manifest.digest)
                # [END list_manifest_properties]
```

::: zone-end

## <a name="clean-up-resources"></a>리소스 정리

Azure 컨테이너 레지스트리를 정리하고 제거하려는 경우 리소스 또는 리소스 그룹을 삭제할 수 있습니다. 리소스 그룹을 삭제하면 해당 리소스 그룹에 연결된 다른 모든 리소스가 함께 삭제됩니다.

* [포털](container-registry-get-started-portal.md#clean-up-resources)
* [Azure CLI](container-registry-get-started-azure-cli.md#clean-up-resources)

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure Container Registry 클라이언트 라이브러리를 사용하여 컨테이너 레지스트리의 이미지 및 아티팩트에 대한 작업을 수행하는 방법을 알아보았습니다.

* 자세한 내용은 다음과 같은 API 참조 설명서를 참조하세요.

    * [.NET][dotnet_docs]
    * [Java][java_docs]
    * [JavaScript][javascript_docs]
    * [Python][python_docs]

* Azure Container Registry [REST API][rest_docs]에 대해 알아봅니다.

[dotnet_source]: https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/containerregistry/Azure.Containers.ContainerRegistry/src
[dotnet_package]: https://www.nuget.org/packages/Azure.Containers.ContainerRegistry/
[dotnet_samples]: https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/containerregistry/Azure.Containers.ContainerRegistry/samples
[dotnet_docs]: /dotnet/api/azure.containers.containerregistry
[rest_docs]: /rest/api/containerregistry/
[product_docs]:  container-registry-intro.md
[nuget]: https://www.nuget.org/
[dotnet_identity]: https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/identity/Azure.Identity/README.md
[javascript_identity]: https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/identity/identity/README.md
[javascript_source]: https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/containerregistry/container-registry/src
[javascript_package]: https://www.npmjs.com/package/@azure/container-registry
[javascript_docs]: /javascript/api/overview/azure/container-registry-readme
[jdk_link]: /java/azure/jdk/
[javascript_samples]: https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/containerregistry/container-registry/samples
[java_source]: https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/containerregistry/azure-containers-containerregistry/src
[java_package]: https://search.maven.org/artifact/com.azure/azure-containers-containerregistry
[java_docs]: /java/api/overview/azure/containers-containerregistry-readme
[java_identity]: https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/identity/azure-identity/README.md
[java_samples]: https://github.com/Azure/azure-sdk-for-java/tree/main/sdk/containerregistry/azure-containers-containerregistry/src/samples/
[python_package]: https://pypi.org/project/azure-containerregistry/
[python_docs]: /python/api/overview/azure/containerregistry-readme
[python_samples]: https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/containerregistry/azure-containerregistry/samples
[pip_link]: https://pypi.org
[python_identity]: https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/identity/azure-identity
[python_source]: https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/containerregistry/azure-containerregistry

---
ms.openlocfilehash: b1be5e903994f0a2c4a3d457f0c2bb5572a889c3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98663030"
---
## <a name="add-secret-manager"></a>Aggiungere Secret Manager

Uno strumento denominato Secret Manager archivia i dati sensibili per operazioni di sviluppo al di fuori dell'albero del progetto. Questo approccio contribuisce a impedire la condivisione accidentale dei segreti dell'app all'interno del codice sorgente. Completare i passaggi seguenti per abilitare l'uso di Secret Manager nel progetto ASP.NET Core:

#### <a name="net-5x"></a>[.NET 5. x](#tab/core5x)

Passare alla directory radice del progetto ed eseguire il comando seguente per abilitare l'archiviazione dei segreti nel progetto:

```dotnetcli
dotnet user-secrets init
```

Un elemento `UserSecretsId` contenente un GUID viene aggiunto al file con estensione *csproj*:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
    
    <PropertyGroup>
        <TargetFramework>net5.0</TargetFramework>
        <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
    </PropertyGroup>

</Project>
```

#### <a name="net-core-3x"></a>[.NET Core 3.x](#tab/core3x)

Passare alla directory radice del progetto ed eseguire il comando seguente per abilitare l'archiviazione dei segreti nel progetto:

```dotnetcli
dotnet user-secrets init
```

Un elemento `UserSecretsId` contenente un GUID viene aggiunto al file con estensione *csproj*:

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
    
    <PropertyGroup>
        <TargetFramework>netcoreapp3.1</TargetFramework>
        <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
    </PropertyGroup>

</Project>
```

#### <a name="net-core-2x"></a>[.NET Core 2.x](#tab/core2x)

1. Aprire il file con estensione *csproj*.

1. Aggiungere un elemento `UserSecretsId` al file con estensione *csproj*, come illustrato di seguito. È possibile usare lo stesso GUID oppure sostituire questo valore con uno proprio.

    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">
    
        <PropertyGroup>
            <TargetFramework>netcoreapp2.1</TargetFramework>
            <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
        </PropertyGroup>
    
        <ItemGroup>
            <PackageReference Include="Microsoft.AspNetCore.App" />
            <PackageReference Include="Microsoft.AspNetCore.Razor.Design" Version="2.1.2" PrivateAssets="All" />
        </ItemGroup>
    
    </Project>
    ```
    
1. Salvare il file con estensione *csproj*.

---

> [!TIP]
> Per altre informazioni su Secret Manager, vedere [Archiviazione sicura dei segreti delle app in fase di sviluppo in ASP.NET Core](/aspnet/core/security/app-secrets).

---
title: Azure Service Fabric-uso di Service Fabric riferimenti all'insieme di credenziali delle applicazioni
description: Questo articolo illustra come usare il supporto KeyVaultReference di Service Fabric per i segreti dell'applicazione.
ms.topic: article
ms.date: 09/20/2019
ms.openlocfilehash: a0e4ef0decae8cc9ab4dc5f8c69dfef854af81f3
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98898597"
---
# <a name="keyvaultreference-support-for-azure-deployed-service-fabric-applications"></a>Supporto di KeyVaultReference per applicazioni Service Fabric distribuite in Azure

Un problema comune durante la creazione di applicazioni cloud è la modalità di distribuzione sicura dei segreti per le applicazioni. Ad esempio, potrebbe essere necessario distribuire una chiave di database nell'applicazione senza esporre la chiave durante la pipeline o l'operatore. Service Fabric supporto KeyVaultReference semplifica la distribuzione dei segreti nelle applicazioni semplicemente facendo riferimento all'URL del segreto archiviato nel Key Vault. Service Fabric gestirà il recupero del segreto per conto dell'identità gestita dell'applicazione e l'attivazione dell'applicazione con il segreto.

> [!NOTE]
> Il supporto di KeyVaultReference per le applicazioni Service Fabric è GA (out-of-Preview) a partire da Service Fabric versione 7,2 CU5. Prima di utilizzare questa funzionalità, è consigliabile eseguire l'aggiornamento a questa versione.

> [!NOTE]
> Il supporto KeyVaultReference per applicazioni Service Fabric supporta solo i segreti con [versione](../key-vault/general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning) . I segreti senza versione non sono supportati. Il Key Vault deve trovarsi nella stessa sottoscrizione del cluster di Service Fabric. 

## <a name="prerequisites"></a>Prerequisiti

- Identità gestita per applicazioni Service Fabric

    Service Fabric supporto KeyVaultReference usa l'identità gestita di un'applicazione per recuperare i segreti per conto dell'applicazione, quindi l'applicazione deve essere distribuita tramite e assegnata un'identità gestita. Seguire questo [documento](concepts-managed-identity.md) per abilitare l'identità gestita per l'applicazione.

- Archivio dei segreti centrali (CSS).

    L'archivio dei segreti centrali (CSS) è la cache dei segreti locali crittografati di Service Fabric. Questa funzionalità usa CSS per proteggere e mantenere i segreti dopo che sono stati recuperati dal Key Vault. Per utilizzare questa funzionalità, è inoltre necessario abilitare questo servizio di sistema facoltativo. Seguire questo [documento](service-fabric-application-secret-store.md) per abilitare e configurare CSS.

- Concedere l'autorizzazione di accesso alle identità gestite dell'applicazione all'insieme di credenziali delle credenziali

    Fare riferimento a questo [documento](how-to-grant-access-other-resources.md) per vedere come concedere l'accesso all'identità gestita all'insieme di credenziali delle credenziali. Si noti anche che se si usa l'identità gestita assegnata dal sistema, l'identità gestita viene creata solo dopo la distribuzione dell'applicazione. Questo può creare race condition in cui l'applicazione tenta di accedere al segreto prima che l'identità possa accedere all'insieme di credenziali. Il nome dell'identità assegnato dal sistema sarà `{cluster name}/{application name}/{service name}` .
    
## <a name="use-keyvaultreferences-in-your-application"></a>Usare KeyVaultReferences nell'applicazione
KeyVaultReferences può essere utilizzato in diversi modi
- [Come variabile di ambiente](#as-an-environment-variable)
- [Montato come file nel contenitore](#mounted-as-a-file-into-your-container)
- [Come riferimento a una password del repository del contenitore](#as-a-reference-to-a-container-repository-password)

### <a name="as-an-environment-variable"></a>Come variabile di ambiente

```xml
<EnvironmentVariables>
      <EnvironmentVariable Name="MySecret" Type="KeyVaultReference" Value="<KeyVaultURL>"/>
</EnvironmentVariables>
```

```C#
string secret =  Environment.GetEnvironmentVariable("MySecret");
```

### <a name="mounted-as-a-file-into-your-container"></a>Montato come file nel contenitore

- Aggiungere una sezione a settings.xml

    Definire il `MySecret` parametro con il tipo `KeyVaultReference` e il valore `<KeyVaultURL>`

    ```xml
    <Section Name="MySecrets">
        <Parameter Name="MySecret" Type="KeyVaultReference" Value="<KeyVaultURL>"/>
    </Section>
    ```

- Fare riferimento alla nuova sezione in ApplicationManifest.xml `<ConfigPackagePolicies>`

    ```xml
    <ServiceManifestImport>
        <Policies>
        <IdentityBindingPolicy ServiceIdentityRef="MyServiceMI" ApplicationIdentityRef="MyApplicationMI" />
        <ConfigPackagePolicies CodePackageRef="Code">
            <!--Linux container example-->
            <ConfigPackage Name="Config" SectionName="MySecrets" EnvironmentVariableName="SecretPath" MountPoint="/var/secrets"/>
            <!--Windows container example-->
            <!-- <ConfigPackage Name="Config" SectionName="dbsecrets" EnvironmentVariableName="SecretPath" MountPoint="C:\secrets"/> -->
        </ConfigPackagePolicies>
        </Policies>
    </ServiceManifestImport>
    ```

- Utilizzare i segreti dal codice del servizio

    Ogni parametro elencato in `<Section  Name=MySecrets>` sarà un file sotto la cartella a cui punta Metodo EnvironmentVariable SecretPath. Il frammento di codice C# seguente mostra come leggere il segreto dall'applicazione.

    ```C#
    string secretPath = Environment.GetEnvironmentVariable("SecretPath");
    using (StreamReader sr = new StreamReader(Path.Combine(secretPath, "MySecret"))) 
    {
        string secret =  sr.ReadToEnd();
    }
    ```
    > [!NOTE] 
    > MountPoint controlla la cartella in cui verranno montati i file contenenti i valori Secret.

### <a name="as-a-reference-to-a-container-repository-password"></a>Come riferimento a una password del repository del contenitore

```xml
 <Policies>
      <ContainerHostPolicies CodePackageRef="Code">
        <RepositoryCredentials AccountName="MyACRUser" Type="KeyVaultReference" Password="<KeyVaultURL>"/>
      </ContainerHostPolicies>
```

## <a name="next-steps"></a>Passaggi successivi

* [Documentazione di Azure Azure Vault](../key-vault/index.yml)
* [Scopri di più sull'archivio Secret centrale](service-fabric-application-secret-store.md)
* [Informazioni sull'identità gestita per applicazioni Service Fabric](concepts-managed-identity.md)

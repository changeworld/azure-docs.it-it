---
title: Schema di definizione di servizi cloud di Azure (versione classica) (file con estensione cscfg) | Microsoft Docs
description: Un file di configurazione del servizio (con estensione cscfg) specifica il numero di istanze del ruolo da distribuire per ogni ruolo, i valori di configurazione e le identificazioni personali del certificato per un ruolo.
ms.topic: article
ms.service: cloud-services
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: ba933d4981fb7ed209a7fb5d5c41113750f312de
ms.sourcegitcommit: 6272bc01d8bdb833d43c56375bab1841a9c380a5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98743475"
---
# <a name="azure-cloud-services-classic-config-schema-cscfg-file"></a>Schema di configurazione di servizi cloud di Azure (versione classica) (file con estensione cscfg)

> [!IMPORTANT]
> [Servizi cloud di Azure (supporto esteso)](../cloud-services-extended-support/overview.md) è un nuovo modello di distribuzione basato su Azure Resource Manager per il prodotto servizi cloud di Azure.Con questa modifica, i servizi cloud di Azure in esecuzione nel modello di distribuzione basato su Service Manager di Azure sono stati rinominati come servizi cloud (versione classica) e tutte le nuove distribuzioni devono usare i [servizi cloud (supporto esteso)](../cloud-services-extended-support/overview.md).

Il file di configurazione del servizio specifica il numero di istanze del ruolo da distribuire per ogni ruolo nel servizio, i valori delle impostazioni di configurazione e le identificazioni personali per i certificati associati a un ruolo. Se il servizio fa parte di una rete virtuale, le informazioni di configurazione della rete devono essere specificate nel file di configurazione del servizio e nel file di configurazione della rete virtuale. L'estensione predefinita per il file di configurazione del servizio è cscfg.

Il modello del servizio viene descritto dallo [schema di definizione di Servizi cloud (versione classica)](schema-csdef-file.md).

Per impostazione predefinita, il file dello schema di configurazione di Diagnostica di Azure viene installato nella directory `C:\Program Files\Microsoft SDKs\Windows Azure\.NET SDK\<version>\schemas`. Sostituire `<version>` con la versione installata di [Azure SDK](https://azure.microsoft.com/downloads/).

Per altre informazioni sulla configurazione dei ruoli in un servizio, vedere [Cos'è il modello del servizio cloud](cloud-services-model-and-package.md).

## <a name="basic-service-configuration-schema"></a>Schema di configurazione del servizio di base
Il formato di base del file di configurazione del servizio è il seguente.

```xml
<ServiceConfiguration serviceName="<service-name>" osFamily="<osfamily-number>" osVersion="<os-version>" schemaVersion="<schema-version>">

  <Role …>
    …
  </Role>

  <NetworkConfiguration>
    …
  </NetworkConfiguration>

</ServiceConfiguration>
```

## <a name="schema-definitions"></a>Definizioni dello schema
Gli argomenti seguenti descrivono lo schema per l'elemento `ServiceConfiguration`:

- [Schema Role](schema-cscfg-role.md)
- [Schema NetworkConfiguration](schema-cscfg-networkconfiguration.md)

## <a name="service-configuration-namespace"></a>Spazio dei nomi di configurazione del servizio
Lo spazio dei nomi XML per il file di configurazione del servizio è: `http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration`.

##  <a name="serviceconfiguration-element"></a><a name="ServiceConfiguration"></a> Elemento ServiceConfiguration
`ServiceConfiguration` è l'elemento di livello superiore del file di configurazione del servizio.

La tabella seguente descrive gli attributi dell'elemento `ServiceConfiguration`. Tutti i valori degli attributi sono di tipo stringa.

| Attributo | Descrizione |
| --------- | ----------- |
|serviceName|Obbligatorio. Nome del servizio cloud. Il nome assegnato qui deve corrispondere al nome specificato nel file di definizione del servizio.|
|osFamily|facoltativo. Specifica il sistema operativo guest che verrà eseguito sulle istanze del ruolo nel servizio cloud. Per informazioni sulle versioni del sistema operativo guest supportate, vedere [Versioni del sistema operativo guest di Azure e matrice di compatibilità SDK](cloud-services-guestos-update-matrix.md).<br /><br /> Se non si include un valore `osFamily` e non si è impostato l'attributo `osVersion` su una versione specifica del sistema operativo guest, viene usato il valore predefinito 1.|
|osVersion|facoltativo. Specifica la versione del sistema operativo guest che verrà eseguito sulle istanze del ruolo nel servizio cloud. Per altre informazioni sulle versioni del sistema operativo guest, vedere [Versioni del sistema operativo guest di Azure e matrice di compatibilità SDK](cloud-services-guestos-update-matrix.md).<br /><br /> È possibile specificare che il sistema operativo guest deve essere automaticamente aggiornato alla versione più recente. A questo scopo, impostare il valore dell'attributo `osVersion` su `*`. Quando viene impostato su `*`, le istanze del ruolo vengono distribuite usando la versione più recente del sistema operativo guest per la famiglia di sistemi operativi specificata e verranno automaticamente aggiornate quando verranno rilasciate nuove versioni del sistema operativo guest.<br /><br /> Per specificare manualmente una determinata versione, usare `Configuration String` della tabella della sezione **Versioni future, correnti e di transizione del sistema operativo guest** di [Versioni del sistema operativo guest di Azure e matrice di compatibilità SDK](cloud-services-guestos-update-matrix.md).<br /><br /> Il valore predefinito per l'attributo `osVersion` è `*`.|
|schemaVersion|facoltativo. Specifica la versione dello schema di configurazione del servizio. La versione dello schema consente a Visual Studio di selezionare gli strumenti SDK corretti da usare per la convalida dello schema se più di una versione dell'SDK è installata side-by-side. Per altre informazioni sullo schema e sulla compatibilità delle versioni, vedere [Versioni del sistema operativo guest di Azure e matrice di compatibilità SDK](cloud-services-guestos-update-matrix.md)|

Il file di configurazione del servizio deve contenere un elemento `ServiceConfiguration`. L'elemento `ServiceConfiguration` può includere un numero qualsiasi di elementi `Role` e zero o 1 elemento `NetworkConfiguration`.
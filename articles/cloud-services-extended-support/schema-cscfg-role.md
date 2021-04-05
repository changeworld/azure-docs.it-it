---
title: Schema del ruolo Servizi cloud di Azure (supporto esteso) | Microsoft Docs
description: Informazioni correlate allo schema del ruolo per i servizi cloud (supporto esteso)
ms.topic: article
ms.service: cloud-services-extended-support
ms.date: 10/14/2020
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 2567f5bb817a34f6274d5e265a266d67a9c81413
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98744447"
---
# <a name="azure-cloud-services-extended-support-config-role-schema"></a>Schema del ruolo di configurazione di servizi cloud di Azure (supporto esteso)

L'elemento `Role` del file di configurazione specifica il numero di istanze del ruolo da distribuire per ogni ruolo nel servizio, i valori delle impostazioni di configurazione e le identificazioni personali per i certificati associati a un ruolo.

Per altre informazioni sullo schema di configurazione dei servizi di Azure, vedere [schema di configurazione del servizio cloud (supporto esteso)](schema-cscfg-file.md). Per altre informazioni sullo schema di definizione dei servizi di Azure, vedere [schema di definizione del servizio cloud (supporto esteso)](schema-csdef-file.md).

##  <a name="role-element"></a><a name="Role"></a> Role-elemento
L'esempio seguente illustra l'elemento `Role` e i relativi elementi figlio.

```xml 
<ServiceConfiguration>
  <Role name="<role-name>" vmName="<vm-name>">
    <Instances count="<number-of-instances>"/>
    <ConfigurationSettings>
      <Setting name="<setting-name>" value="<setting-value>" />
    </ConfigurationSettings>
    <Certificates>
      <Certificate name="<certificate-name>" thumbprint="<certificate-thumbprint>" thumbprintAlgorithm="<algorithm>"/>
    </Certificates>
  </Role>
</ServiceConfiguration>
```

La tabella seguente descrive gli attributi dell'elemento `Role`.

| Attributo | Descrizione |
| --------- | ----------- |
| name   | Obbligatorio. Specifica il nome del ruolo. Il nome deve corrispondere al nome specificato per il ruolo nel file di definizione del servizio.|
| vmName | facoltativo. Specifica il nome DNS per una macchina virtuale. Il nome non può contenere più di 10 caratteri.|

La tabella seguente descrive gli elementi figlio dell'elemento `Role`.

| Elemento | Descrizione |
| ------- | ----------- |
| Istanze | Obbligatorio. Specifica il numero di istanze da distribuire per il ruolo. Il numero di istanze è definito da un numero intero per l'attributo `count`.|
| Impostazione   | facoltativo. Specifica un nome e un valore per l'impostazione in una raccolta di impostazioni per un ruolo. Il nome dell'impostazione è definito da una stringa per l'attributo `name` e il valore dell'impostazione è definito da una stringa per l'attributo `value`.|
| Certificato | facoltativo. Specifica il nome, l'identificazione personale e l'algoritmo del certificato di servizio che deve essere associato al ruolo. Il nome del certificato è definito da una stringa per l'attributo `name`. L'identificazione personale del certificato è definita da una stringa di numeri esadecimali senza spazi per l'attributo `thumbprint`. I numeri esadecimali devono essere rappresentati usando cifre e caratteri alfabetici maiuscoli. L'algoritmo del certificato è definito da una stringa per l'attributo `thumbprintAlgorithm`.|

## <a name="see-also"></a>Vedi anche
[Schema di configurazione del servizio cloud (supporto esteso)](schema-cscfg-file.md).
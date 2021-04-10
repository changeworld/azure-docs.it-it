---
title: Elenco delle classificazioni supportate
description: In questa pagina sono elencate le classificazioni di sistema supportate in Azure competenza.
author: anmuk601
ms.author: anmuk
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: reference
ms.date: 02/05/2021
ms.openlocfilehash: 37df9a276339b80a81e6ac5d5db14872de9edff4
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: it-IT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106065855"
---
# <a name="supported-classifications-in-azure-purview"></a>Classificazioni supportate in Azure competenza

Questo articolo elenca le classificazioni di sistema supportate e definite in Azure (anteprima).


- **Soglia di corrispondenza Distinct**: numero totale di valori di dati distinti che devono essere trovati in una colonna prima che lo scanner esegua il modello di dati. Per le regole di classificazione del sistema è necessario disporre di almeno 8 valori distinti in ogni colonna per sottoporli alla classificazione. Il sistema richiede questo valore per assicurarsi che la colonna contenga dati sufficienti affinché lo scanner lo classifichi in modo accurato. Una colonna contenente più righe che contengono tutti il valore 1, ad esempio, non sarà classificata. Anche le colonne che contengono una riga con un valore e il resto delle righe hanno valori null non vengono classificate. Se si specificano più modelli, questo valore viene applicato a ognuno di essi.

- **Soglia di corrispondenza minima**: è la percentuale minima di corrispondenze del valore dei dati in una colonna che deve essere trovata dallo scanner per la classificazione da applicare. Il valore di classificazione del sistema è impostato su 60%.


## <a name="defined-system-classifications"></a>Classificazioni di sistema definite

Azure competenza classifica i dati in base al [filtro](https://wikipedia.org/wiki/Bloom_filter) [Regex](https://wikipedia.org/wiki/Regular_expression) e Bloom. Gli elenchi seguenti descrivono il formato, il modello e le parole chiave per le classificazioni di sistema definite da Azure.

Ogni nome di classificazione è preceduto da MICROSOFT.

## <a name="bloom-filter-classifications"></a>Classificazioni filtro Bloom

## <a name="city-country-and-place"></a>Città, paese e località

I filtri City, Country e Place sono stati preparati usando i set di dati migliori disponibili per la preparazione dei dati.

## <a name="person"></a>Persona

Il filtro della fioritura della persona è stato preparato usando i due set di impostazioni seguenti.

- [2010 i dati del censimento degli Stati Uniti per i cognomi (162-K voci)](https://www.census.gov/topics/population/genealogy/data/2010_surnames.html)
- [Nomi dei bambini più diffusi (dal SSN), che usano tutti gli anni 1880-2019 (98-K voci)](https://www.ssa.gov/oact/babynames/limits.html)

## <a name="regex-classifications"></a>Classificazioni RegEx

## <a name="aba-routing"></a>Routing ABA

### <a name="format"></a>Formato

Nove cifre, che possono trovarsi in un modello formattato o non formattato

### <a name="pattern"></a>Modello

Formattato

- quattro cifre che iniziano con 0, 1, 2, 3, 6, 7 o 8
- segno meno
- quattro cifre
- segno meno
- una cifra

Non formattato: nove cifre consecutive che iniziano con 0, 1, 2, 3, 6, 7 o 8

### <a name="keywords"></a>Parole chiave

#### <a name="keyword_aba_routing"></a>Keyword_aba_routing

```
amba number
aba #
aba
abarouting#
abaroutingnumber
americanbankassociationrouting#
americanbankassociationroutingnumber
bank routing#
bankroutingnumber
routing #
routing no
routing number
routing transit number
routing#
RTN
```

## <a name="argentina-national-identity-dni-number"></a>Numero di identità nazionale Argentina (DNI)

### <a name="format"></a>Formato

Otto cifre con o senza punti

### <a name="pattern"></a>Modello

Otto cifre:

- due cifre
- un periodo facoltativo
- tre cifre
- un periodo facoltativo
- tre cifre

### <a name="keywords"></a>Parole chiave

#### <a name="keyword_argentina_national_id"></a>Keyword_argentina_national_id

```
Argentina National Identity number
cedula
cédula
dni
documento nacional de identidad
documento número
documento numero
registro nacional de las personas
rnp
```

## <a name="australia-bank-account-number"></a>Numero di conto bancario in Australia

### <a name="format"></a>Formato

Da 6 a 10 cifre con o senza un numero di Branch dello stato della banca

### <a name="pattern"></a>Modello

Il numero di conto è da 6 a 10 cifre.

Numero ramo stato della banca dell'Australia:

- tre cifre
- segno meno
- tre cifre

### <a name="keywords"></a>Parole chiave

#### <a name="keyword_australia_bank_account_number"></a>Keyword_australia_bank_account_number

```
swift bank code
correspondent bank
base currency
usa account
holder address
bank address
information account
fund transfers
bank charges
bank details
banking information
full names
iaea
```

## <a name="australia-drivers-license-number"></a>Numero di licenza del driver Australia

### <a name="format"></a>Formato
Nove lettere e cifre

### <a name="pattern"></a>Modello
nove lettere e cifre:

- due cifre o lettere (senza distinzione tra maiuscole e minuscole)
- due cifre
- cinque cifre o lettere (senza distinzione tra maiuscole e minuscole)

OR

- da una a due lettere facoltative (senza distinzione tra maiuscole e minuscole)
- da quattro a nove cifre

OR

- nove cifre o lettere (senza distinzione tra maiuscole e minuscole)

### <a name="keywords"></a>Parole chiave

#### <a name="keyword_australia_drivers_license_number"></a>Keyword_australia_drivers_license_number

```
international driving permits
australian automobile association
international driving permit
DriverLicence
DriverLicences
Driver Lic
Driver License
Driver Licenses
DriversLic
DriversLicence
DriversLicences
Drivers Lic
Drivers Lics
Drivers License
Drivers Licenses
Driver'Lic
Driver'Lics
Driver'License
Driver'Licenses
Driver' Lic
Driver' Lics
Driver' License
Driver' Licenses
Driver'sLic
Driver'sLics
Driver'sLicence
Driver'sLicences
Driver's Lic
Driver's Lics
Driver's License
Driver's Licenses
DriverLic#
DriverLics#
DriverLicence#
DriverLicences#
Driver Lic#
Driver Lics#
Driver License#
Driver Licenses#
DriversLic#
DriversLics#
DriversLicence#
DriversLicences#
Drivers Lic#
Drivers Lics#
Drivers License#
Drivers Licenses#
Driver'Lic#
Driver'Lics#
Driver'License#
Driver'Licenses#
Driver' Lic#
Driver' Lics#
Driver' License#
Driver' Licenses#
Driver'sLic#
Driver'sLics#
Driver'sLicence#
Driver'sLicences#
Driver's Lic#
Driver's Lics#
Driver's License#
Driver's Licenses#
```

#### <a name="keyword_australia_drivers_license_number_exclusions"></a>Keyword_australia_drivers_license_number_exclusions

```
aaa
DriverLicense
DriverLicenses
Driver License
Driver Licenses
DriversLicense
DriversLicenses
Drivers License
Drivers Licenses
Driver'License
Driver'Licenses
Driver' License
Driver' Licenses
Driver'sLicense
Driver'sLicenses
Driver's License
Driver's Licenses
DriverLicense#
DriverLicenses#
Driver License#
Driver Licenses#
DriversLicense#
DriversLicenses#
Drivers License#
Drivers Licenses#
Driver'License#
Driver'Licenses#
Driver' License#
Driver' Licenses#
Driver'sLicense#
Driver'sLicenses#
Driver's License#
Driver's Licenses#
```

## <a name="australian-medicare-number"></a>Numero di Medicare australiano

### <a name="format"></a>Formato

10-11 cifre

### <a name="pattern"></a>Modello

10-11 cifre:

- il primo numero è compreso nell'intervallo 2-6
- la nona cifra è una cifra di controllo
- il decimo numero è la cifra del problema
- undicesima (facoltativo) è il numero singolo

### <a name="keywords"></a>Parole chiave

#### <a name="keyword_australia_medicare_number"></a>Keyword_Australia_Medicare_Number

```
bank account details
medicare payments
mortgage account
bank payments
information branch
credit card loan
department of human services
local service
medicare
```

## <a name="australia-passport-number"></a>Numero Passport Australia

### <a name="format"></a>Formato

Lettera seguita da sette cifre

### <a name="pattern"></a>Modello

Lettera (senza distinzione tra maiuscole e minuscole) seguita da sette cifre

### <a name="keywords"></a>Parole chiave

#### <a name="keyword_passport"></a>Passport parola chiave \_

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```

#### <a name="keyword_australia_passport_number"></a>Parola chiave \_ Australia \_ passaporto \_ numero

```
passport
passport details
immigration and citizenship
commonwealth of australia
department of immigration
residential address
department of immigration and citizenship
visa
national identity card
passport number
travel document
issuing authority
```

## <a name="australia-tax-file-number"></a>Numero di file fiscali Australia

### <a name="format"></a>Formato

da otto a nove cifre

### <a name="pattern"></a>Modello

da otto a nove cifre generalmente presentate con spazi, come indicato di seguito:

- tre cifre
- uno spazio facoltativo
- tre cifre
- uno spazio facoltativo
- da due a tre cifre in cui l'ultima cifra è una cifra di controllo

### <a name="keywords"></a>Parole chiave

#### <a name="keyword_australia_tax_file_number"></a>\_Numero di \_ file di imposta \_ \_ parola chiave Australia

```
australian business number
marginal tax rate
medicare levy
portfolio number
service veterans
withholding tax
individual tax return
tax file number
tfn
```

## <a name="belgium-national-number"></a>Numero nazionale Belgio

### <a name="format"></a>Formato

11 cifre più delimitatori facoltativi

### <a name="pattern"></a>Modello

11 cifre più delimitatori:

- sei cifre e due punti facoltativi nel formato YY. MM. DD per la data di nascita
- Un delimitatore facoltativo da punti, trattini, spazio
- tre cifre sequenziali (dispari per i maschi, anche per le femmine)
- Un delimitatore facoltativo da punti, trattini, spazio
- due cifre di controllo

### <a name="keywords"></a>Parole chiave

#### <a name="keyword_belgium_national_number"></a>\_ \_ Numero nazionale Belgio di parole chiave \_

```
be lasting aantal
bnn#
bnn
carte d'identité
identifiant national
identifiantnational#
identificatie
identification
identifikation
identifikationsnummer
identifizierung
identité
identiteit
identiteitskaart
identity
inscription
national number
national register
national number#
national number
nif#
nif
numéro d'assuré
numéro de registre national
numéro de sécurité
numéro d'identification
numéro d'immatriculation
numéro national
numéronational#
personal ID number
personalausweis
personalidnumber#
registratie
registration
registrationsnumme
registrierung
social security number
ssn#
ssn
steuernummer
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```

## <a name="brazil-cpf-number"></a>Numero CPF Brasile

### <a name="format"></a>Formato

11 cifre che includono una cifra di controllo e possono essere formattate o non formattate

### <a name="pattern"></a>Modello

Formattato

- tre cifre
- un punto
- tre cifre
- un punto
- tre cifre
- segno meno
- due cifre, che sono cifre di controllo

Non formattato:

- 11 cifre dove le ultime due cifre sono cifre di controllo

### <a name="keywords"></a>Parole chiave

#### <a name="keyword_brazil_cpf"></a>Parola chiave \_ Brasile \_ CPF

```
CPF
Identification
Registration
Revenue
Cadastro de Pessoas Físicas
Imposto
Identificação
Inscrição
Receita
```

## <a name="brazil-legal-entity-number-cnpj"></a>Numero di entità legali in Brasile (CNPJ)

### <a name="format"></a>Formato

14 cifre che includono un numero di registrazione, un numero di ramo e cifre di controllo, più delimitatori

### <a name="pattern"></a>Modello

14 cifre, più delimitatori:

- due cifre
- un punto
- tre cifre
- un punto
- tre cifre (le prime otto cifre corrispondono al numero di registrazione)
- barra
- numero di ramo a quattro cifre
- segno meno
- due cifre, che sono cifre di controllo

### <a name="keywords"></a>Parole chiave

#### <a name="keyword_brazil_cnpj"></a>Parola chiave \_ Brazil \_ CNPJ

```
CNPJ
CNPJ/MF
CNPJ-MF
National Registry of Legal Entities
Taxpayers Registry
Legal entity
Legal entities
Registration Status
Business
Company
CNPJ
Cadastro Nacional da Pessoa Jurídica
Cadastro Geral de Contribuintes
CGC
Pessoa jurídica
Pessoas jurídicas
Situação cadastral
Inscrição
Empresa
```

## <a name="brazil-national-identification-card-rg"></a>RG (National Identification card) del Brasile

### <a name="format"></a>Formato

:::no-loc text="Registro Geral (old format): Nine digits":::

:::no-loc text="Registro de Identidade (RIC) (new format): 11 digits":::

### <a name="pattern"></a>Modello

:::no-loc text="Registro Geral (old format):":::

- due cifre
- un punto
- tre cifre
- un punto
- tre cifre
- segno meno
- una cifra, che corrisponde a una cifra di controllo

:::no-loc text="Registro de Identidade (RIC) (new format):":::

- 10 cifre
- segno meno
- una cifra, che corrisponde a una cifra di controllo

### <a name="keywords"></a>Parole chiave

#### <a name="keyword_brazil_rg"></a>Parola chiave \_ Brasile \_ RG

```
Cédula de identidade
identity card
national ID
número de rregistro
registro de Iidentidade
registro geral
RG (this keyword is case-sensitive)
RIC (this keyword is case-sensitive)
```

## <a name="canada-bank-account-number"></a>Numero conto bancario del Canada

### <a name="format"></a>Formato

7 o 12 cifre

### <a name="pattern"></a>Modello

Il numero di un conto bancario del Canada è 7 o 12 cifre.

Il numero di transito del conto bancario del Canada è:

- cinque cifre
- segno meno
- tre cifre o
- zero &quot; 0&quot;
- otto cifre

### <a name="keywords"></a>Parole chiave

#### <a name="keyword_canada_bank_account_number"></a>Parola chiave \_ Canada \_ Bank \_ account \_ Number

```
canada savings bonds
canada revenue agency
canadian financial institution
direct deposit form
canadian citizen
legal representative
notary public
commissioner for oaths
child care benefit
universal child care
canada child tax benefit
income tax benefit
harmonized sales tax
social insurance number
income tax refund
child tax benefit
territorial payments
institution number
deposit request
banking information
direct deposit
```

## <a name="canada-drivers-license-number"></a>Numero di licenza del driver Canada

### <a name="format"></a>Formato

Varia a seconda della provincia

### <a name="pattern"></a>Modello

Vari modelli relativi a Alberta, British Columbia, Manitoba, New Brunswick, Terranova/Labrador, Nova Scotia, Ontario, Prince Edward Island, Quebec e Saskatchewan

### <a name="keywords"></a>Parole chiave

#### <a name="keyword_province_name_drivers_license_name"></a>Parola chiave \_ [ \_ nome provincia \_ ] \_ nome licenza driver \_

- Abbreviazione della provincia, ad esempio AB
- Nome della provincia, ad esempio Alberta

#### <a name="keyword_canada_drivers_license"></a>Licenza Keywords \_ Canada \_ drivers \_

```
DL
DLS
CDL
CDLS
DriverLic
DriverLics
DriverLicense
DriverLicenses
DriverLicence
DriverLicences
Driver Lic
Driver Lics
Driver License
Driver Licenses
Driver License
Driver Licenses
DriversLic
DriversLics
DriversLicence
DriversLicences
DriversLicense
DriversLicenses
Drivers Lic
Drivers Lics
Drivers License
Drivers Licenses
Drivers License
Drivers Licenses
Driver'Lic
Driver'Lics
Driver'License
Driver'Licenses
Driver'License
Driver'Licenses
Driver' Lic
Driver' Lics
Driver' License
Driver' Licenses
Driver' License
Driver' Licenses
Driver'sLic
Driver'sLics
Driver'sLicense
Driver'sLicenses
Driver'sLicence
Driver'sLicences
Driver's Lic
Driver's Lics
Driver's License
Driver's Licenses
Driver's License
Driver's Licenses
Permis de Conduire
ID
IDs
ID card number
ID card numbers
ID card #
ID card #s
ID card
ID cards
ID card
identification number
identification numbers
identification #
identification #s
identification card
identification cards
identification
DL#
DLS#
CDL#
CDLS#
DriverLic#
DriverLics#
DriverLicense#
DriverLicenses#
DriverLicence#
DriverLicences#
Driver Lic#
Driver Lics#
Driver License#
Driver Licenses#
Driver License#
Driver Licenses#
DriversLic#
DriversLics#
DriversLicense#
DriversLicenses#
DriversLicence#
DriversLicences#
Drivers Lic#
Drivers Lics#
Drivers License#
Drivers Licenses#
Drivers License#
Drivers Licenses#
Driver'Lic#
Driver'Lics#
Driver'License#
Driver'Licenses#
Driver'License#
Driver'Licenses#
Driver' Lic#
Driver' Lics#
Driver' License#
Driver' Licenses#
Driver' License#
Driver' Licenses#
Driver'sLic#
Driver'sLics#
Driver'sLicense#
Driver'sLicenses#
Driver'sLicence#
Driver'sLicences#
Driver's Lic#
Driver's Lics#
Driver's License#
Driver's Licenses#
Driver's License#
Driver's Licenses#
Permis de Conduire#
ID#
IDs#
ID card#
ID cards#
ID card#
identification card#
identification cards#
identification#
```

## <a name="canada-health-service-number"></a>Numero del servizio integrità Canada

### <a name="format"></a>Formato

10 cifre

### <a name="pattern"></a>Modello

10 cifre

### <a name="keywords"></a>Parole chiave

#### <a name="keyword_canada_health_service_number"></a>\_Numero del \_ \_ servizio integrità \_ di keyword Canada

```
personal health number
patient information
health services
specialty services
automobile accident
patient hospital
psychiatrist
workers compensation
disability
```

## <a name="canada-passport-number"></a>Numero passaporto Canada

### <a name="format"></a>Formato

due lettere maiuscole seguite da sei cifre

### <a name="pattern"></a>Modello

due lettere maiuscole seguite da sei cifre

### <a name="keywords"></a>Parole chiave

#### <a name="keyword_canada_passport_number"></a>Parola chiave \_ Canada \_ passaporto \_ numero

```
canadian citizenship
canadian passport
passport application
passport photos
certified translator
canadian citizens
processing times
renewal application
```

#### <a name="keyword_passport"></a>Passport parola chiave \_

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```

## <a name="canada-personal-health-identification-number-phin"></a>Numero di identificazione dell'integrità personale del Canada (PHIN)

### <a name="format"></a>Formato

nove cifre

### <a name="pattern"></a>Modello

nove cifre

### <a name="keywords"></a>Parole chiave

#### <a name="keyword_canada_phin"></a>Keyword \_ Canada \_ Phin

```
social insurance number
health information act
income tax information
manitoba health
health registration
prescription purchases
benefit eligibility
personal health
power of attorney
registration number
personal health number
practitioner referral
wellness professional
patient referral
health and wellness
```

#### <a name="keyword_canada_provinces"></a>Parole chiave \_ Canada \_ Province

```
Nunavut
Quebec
Northwest Territories
Ontario
British Columbia
Alberta
Saskatchewan
Manitoba
Yukon
Newfoundland and Labrador
New Brunswick
Nova Scotia
Prince Edward Island
Canada
```

## <a name="canada-social-insurance-number"></a>Numero di assicurazioni sociali Canada

### <a name="format"></a>Formato

nove cifre con trattini o spazi facoltativi

### <a name="pattern"></a>Modello

Formattato

- tre cifre
- trattino o spazio
- tre cifre
- trattino o spazio
- tre cifre

Non formattato: nove cifre

### <a name="keywords"></a>Parole chiave

#### <a name="keyword_sin"></a>Parola chiave \_ sin

```
sin
social insurance
numero d'assurance sociale
sins
ssn
ssns
social security
numero d'assurance social
national identification number
national ID
sin#
soc ins
social ins
```

#### <a name="keyword_sin_collaborative"></a>Parola chiave \_ sin \_ collaborative

```
driver's license
drivers license
driver's license
drivers license
DOB
Birthdate
Birthday
Date of Birth
```

## <a name="chile-identity-card-number"></a>Numero di carta identità Chile

### <a name="format"></a>Formato

da sette a otto cifre più delimitatori una cifra o una lettera di controllo

### <a name="pattern"></a>Modello

da sette a otto cifre più delimitatori:

- da una a due cifre
- un periodo facoltativo
- tre cifre
- un periodo facoltativo
- tre cifre
- trattino
- una cifra o una lettera (senza distinzione tra maiuscole e minuscole) che corrisponde a una cifra di controllo

### <a name="keywords"></a>Parole chiave

#### <a name="keyword_chile_id_card"></a>Parola chiave \_ Chile \_ ID \_ Card

```
cédula de identidad
identificación
national identification
national identification number
national ID
número de identificación nacional
rol único nacional
rol único tributario
RUN
RUT
tarjeta de identificación
Rol Unico Nacional
Rol Unico Tributario
RUN#
RUT#
nationaluniqueroleID#
nacional identidad
número identificación
identidad número
numero identificacion
identidad numero
Chilean identity no.
Chilean identity number
Chilean identity #
Unique Tax Registry
Unique Tributary Role
Unique Tax Role
Unique Tributary Number
Unique National Number
Unique National Role
National unique role
Chile identity no.
Chile identity number
Chile identity #
```

## <a name="china-resident-identity-card-prc-number"></a>Numero di carta di identità (Repubblica popolare cinese)

### <a name="format"></a>Formato

18 cifre

### <a name="pattern"></a>Modello

18 cifre:

- sei cifre, ovvero un codice di indirizzo
- otto cifre nel formato AAAAMMGG, che sono la data di nascita
- tre cifre, ovvero un codice di ordine
- una cifra, che corrisponde a una cifra di controllo

### <a name="keywords"></a>Parole chiave

#### <a name="keyword_china_resident_id"></a>\_ \_ ID residente parola chiave Cina \_

```
Resident Identity Card
PRC
National Identification Card
身份证
居民身份证
居民身份证
鉴定
身分證
居民身份證
鑑定
```

## <a name="credit-card-number"></a>Numero carta di credito

### <a name="format"></a>Formato

da 14 a 16 cifre, che possono essere formattate o non formattate (dddddddddddddddd) e che devono superare il test Luhn.

### <a name="pattern"></a>Modello

Modello complesso e affidabile che rileva le carte da tutti i principali marchi in tutto il mondo, tra cui Visa, MasterCard, Discover Card, JCB, American Express, Gift Cards e Diner card.

### <a name="keywords"></a>Parole chiave

#### <a name="keyword_cc_verification"></a>Verifica della parola chiave \_ CC \_

```
card verification
card identification number
cvn
cid
cvc2
cvv2
pin block
security code
security number
security no
issue number
issue no
cryptogramme
numéro de sécurité
numero de securite
kreditkartenprüfnummer
kreditkartenprufnummer
prüfziffer
prufziffer
sicherheits Kode
sicherheitscode
sicherheitsnummer
verfalldatum
codice di verifica
cod. sicurezza
cod sicurezza
n autorizzazione
código
codigo
cod. seg
cod seg
código de segurança
codigo de seguranca
codigo de segurança
código de seguranca
cód. segurança
cod. seguranca
cod. segurança
cód. seguranca
cód segurança
cod seguranca
cod segurança
cód seguranca
número de verificação
numero de verificacao
ablauf
gültig bis
gültigkeitsdatum
gultig bis
gultigkeitsdatum
scadenza
data scad
fecha de expiracion
fecha de venc
vencimiento
válido hasta
valido hasta
vto
data de expiração
data de expiracao
data em que expira
validade
valor
vencimento
transaction
transaction number
reference number
セキュリティコード
セキュリティ コード
セキュリティナンバー
セキュリティ ナンバー
セキュリティ番号
```

#### <a name="keyword_cc_name"></a>Parola chiave \_ CC \_ nome

```
amex
Americans express
Americans express
americano espresso
Visa
Mastercard
master card
mc
master cards
master cards
diner's Club
diners club
diners club
discover
discover card
discover card
discover cards
JCB
BrandSmart
japanese card bureau
carte blanche
carteblanche
credit card
cc#
cc#:
expiration date
exp date
expiry date
date d'expiration
date d'exp
date expiration
bank card
bank card
card number
card num
card number
card numbers
card numbers
credit card
credit cards
credit cards
ccn
card holder
cardholder
card holders
cardholders
check card
check card
check cards
check cards
debit card
debit card
debit cards
debit cards
atm card
atmcard
atm cards
atmcards
enroute
en route
card type
Cardmember Acct
cardmember account
Card no
Corporate Card
Corporate cards
Type of card
card account number
card member account
Cardmember Acct.
card no.
card no
card number
carte bancaire
carte de crédit
carte de credit
numéro de carte
numero de carte
nº de la carte
nº de carte
kreditkarte
karte
karteninhaber
karteninhabers
kreditkarteninhaber
kreditkarteninstitut
kreditkartentyp
eigentümername
kartennr
kartennummer
kreditkartennummer
kreditkarten-nummer
carta di credito
carta credito
n. carta
n carta
nr. carta
nr carta
numero carta
numero della carta
numero di carta
tarjeta credito
tarjeta de credito
tarjeta crédito
tarjeta de crédito
tarjeta de atm
tarjeta atm
tarjeta debito
tarjeta de debito
tarjeta débito
tarjeta de débito
nº de tarjeta
no. de tarjeta
no de tarjeta
numero de tarjeta
número de tarjeta
tarjeta no
tarjetahabiente
cartão de crédito
cartão de credito
cartao de crédito
cartao de credito
cartão de débito
cartao de débito
cartão de debito
cartao de debito
débito automático
debito automatico
número do cartão
numero do cartão
número do cartao
numero do cartao
número de cartão
numero de cartão
número de cartao
numero de cartao
nº do cartão
nº do cartao
nº. do cartão
no do cartão
no do cartao
no. do cartão
no. do cartao
クレジットカード番号
クレジットカードナンバー
クレジットカード＃
クレジットカード
クレジット
クレカ
カード番号
カードナンバー
カード＃
アメックス
アメリカンエクスプレス
アメリカン エクスプレス
Visaカード
Visa カード
マスターカード
マスター カード
マスター
ダイナースクラブ
ダイナース クラブ
ダイナース
有効期限
期限
キャッシュカード
キャッシュ カード
カード名義人
カードの名義人
カードの名義
デビット カード
デビットカード
```

## <a name="croatia-drivers-license-number"></a>Numero di licenza del driver Croatia

Questa entità di tipo informazioni riservate è disponibile solo nel tipo di informazioni riservate con il numero di licenza del driver UE.

### <a name="format"></a>Formato

otto cifre senza spazi e delimitatori

### <a name="pattern"></a>Modello

otto cifre

### <a name="keywords"></a>Parole chiave

#### <a name="keywords_eu_drivers_license_number"></a>Parole \_ chiave \_ numero di licenza del driver UE \_ \_

```
driverlic
driverlics
driver license
driver licenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver license
driver licenses
driverslic
driverslics
driverslicence
driverslicences
drivers license
drivers licenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers license
drivers licenses
driver'lic
driver'lics
driver'license
driver'licenses
driver'license
driver'licenses
driver' lic
driver' lics
driver' license
driver' licenses
driver' license
driver' licenses
driver'slic
driver'slics
driver'license
driver'licenses
driver'license
driver'licenses
driver's lic
driver's lics
driver's license
driver's licenses
driver's license
driver's licenses
dl#
dls#
driverlic#
driverlics#
driver license#
driver licenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licenses#
driverslic#
driverslics#
drivers license#
drivers licenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers license#
drivers licenses#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' license#
driver' licenses#
driver'slic#
driver'slics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's license#
driver's licenses#
driving license
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv license
driv licenses
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving license
driving licenses
driving permit
dl no
dlno
dl number
```

#### <a name="keywords_croatia_eu_drivers_license_number"></a>Parole chiave per \_ \_ \_ il \_ numero di licenza del driver UE Croazia \_

```
vozačka dozvola
vozačke dozvole
```

## <a name="croatia-identity-card-number"></a>Numero di carta identità Croazia

Questa entità del tipo di informazioni riservate è inclusa nel tipo di informazioni riservate del numero di identificazione nazionale UE ed è disponibile come entità di tipo informazioni riservate autonome.

### <a name="format"></a>Formato

nove cifre

### <a name="pattern"></a>Modello

nove cifre consecutive

### <a name="keywords"></a>Parole chiave

#### <a name="keyword_croatia_id_card"></a>Parola chiave \_ Croazia \_ ID \_ Card

```
majstorski broj građana
master citizen number
nacionalni identifikacijski broj
national identification number
oib#
oib
osobna iskaznica
osobni ID
osobni identifikacijski broj
personal identification number
porezni broj
porezni identifikacijski broj
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```

## <a name="croatia-personal-identification-oib-number"></a>Numero di identificazione personale della Croazia (OIB)

### <a name="format"></a>Formato

11 cifre

### <a name="pattern"></a>Modello

11 cifre:

- 10 cifre
- la cifra finale è una cifra di controllo

### <a name="keywords"></a>Parole chiave

#### <a name="keyword_croatia_oib_number"></a>Parola chiave \_ Croatia \_ OIB \_ Number

```
majstorski broj građana
master citizen number
nacionalni identifikacijski broj
national identification number
oib#
oib
osobna iskaznica
osobni ID
osobni identifikacijski broj
personal identification number
porezni broj
porezni identifikacijski broj
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax Id number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```

## <a name="denmark-personal-identification-number"></a>Numero di identificazione personale Danimarca

### <a name="format"></a>Formato

10 cifre contenenti un trattino

### <a name="pattern"></a>Modello

10 cifre:

- sei cifre nel formato GGMMAA, che sono la data di nascita
- segno meno
- quattro cifre in cui la cifra finale è una cifra di controllo

### <a name="keywords"></a>Parole chiave

#### <a name="keyword_denmark_id"></a>ID parola chiave \_ Danimarca \_

```
centrale person register
civilt registreringssystem
cpr
cpr#
gesundheitskarte nummer
gesundheitsversicherungkarte nummer
health card
health insurance card number
health insurance number
identification number
identifikationsnummer
identifikationsnummer#
identity number
krankenkassennummer
national ID#
national number#
national number
personalidnumber#
personalidentityno#
personal ID number
personnummer
personnummer#
reisekrankenversicherungskartenummer
rejsesygesikringskort
ssn
ssn#
skat ID
skat kode
skat nummer
skattenummer
social security number
sundhedsforsikringskort
sundhedsforsikringsnummer
sundhedskort
sundhedskortnummer
sygesikring
sygesikringkortnummer
tax code
travel health insurance card
uniqueidentityno#
tax number
tax registration number
tax ID
tax identification number
tax ID#
tax number#
tax no
tax no#
tax number
tax identification no
tin#
tax ID no#
tax ID number#
tax no#
tin ID
tin no
cpr.nr
cprnr
cprnummer
personnr
person register
sygesikringsbevis
sygesikringsbevisnr
sygesikringsbevisnummer
sygesikringskort
sygesikringskortnr
sygesikringskortnummer
sygesikringsnr
sygesikringsnummer
```

## <a name="eu-debit-card-number"></a>Numero di carta di debito UE

### <a name="format"></a>Formato

16 cifre

### <a name="pattern"></a>Modello

Modello complesso e affidabile

### <a name="keywords"></a>Parole chiave

#### <a name="keyword_eu_debit_card"></a>Parola \_ chiave \_ carta di debito UE \_

```
account number
card number
card no.
security number
cc#
```

#### <a name="keyword_card_terms_dict"></a>Parole chiave parole chiave \_ \_ \_ dict

```
acct nbr
acct num
acct no
Americans express
Americans express
americano espresso
amex
atm card
atm cards
atm kaart
atmcard
atmcards
atmkaart
atmkaarten
ban contact
bank card
bankkaart
card holder
card holders
card num
card number
card numbers
card type
cardano numerico
cardholder
cardholders
card number
card numbers
carta bianca
carta credito
carta di credito
cartao de credito
cartao de crédito
cartao de debito
cartao de débito
carte bancaire
carte blanche
carte bleue
carte de credit
carte de crédit
carte di credito
carteblanche
cartão de credito
cartão de crédito
cartão de debito
cartão de débito
cb
ccn
check card
check cards
check card
check cards
chequekaart
cirrus
cirrus-edc-maestro
controlekaart
controlekaarten
credit card
credit cards
credit card
credit cards
debetkaart
debetkaarten
debit card
debit cards
debit card
debit cards
debito automatico
diners club
diners club
discover
discover card
discover cards
discover card
discover cards
débito automático
edc
eigentümername
european debit card
hoofdkaart
hoofdkaarten
in viaggio
japanese card bureau
japanse kaartdienst
jcb
kaart
kaart num
kaartaantal
kaartaantallen
kaarthouder
kaarthouders
karte
karteninhaber
karteninhabers
kartennr
kartennummer
kreditkarte
kreditkarten-nummer
kreditkarteninhaber
kreditkarteninstitut
kreditkartennummer
kreditkartentyp
maestro
master card
master cards
Mastercard
master cards
mc
mister cash
n carta
carta
no de tarjeta
no do cartao
no do cartão
no. de tarjeta
no. do cartao
no. do cartão
nr carta
nr. carta
numeri di scheda
numero carta
numero de cartao
numero de carte
numero de cartão
numero de tarjeta
numero della carta
numero di carta
numero di scheda
numero do cartao
numero do cartão
numéro de carte
nº carta
nº de carte
nº de la carte
nº de tarjeta
nº do cartao
nº do cartão
nº. do cartão
número de cartao
número de cartão
número de tarjeta
número do cartao
scheda dell'assegno
scheda dell'atmosfera
scheda dell'atmosfera
scheda della banca
scheda di controllo
scheda di debito
scheda matrice
schede dell'atmosfera
schede di controllo
schede di debito
schede matrici
scoprono la scheda
scoprono le schede
solo
supporti di scheda
supporto di scheda
switch
tarjeta atm
tarjeta credito
tarjeta de atm
tarjeta de credito
tarjeta de debito
tarjeta debito
tarjeta no
tarjetahabiente
tipo della scheda
ufficio giapponese della
scheda
v pay
v-pay
visa
visa plus
visa electron
visto
visum
vpay
```

#### <a name="keyword_card_security_terms_dict"></a>Termini di sicurezza della scheda parola chiave \_ \_ \_ \_ dict

```
card identification number
card verification
cardi la verifica
cid
cod seg
cod seguranca
cod segurança
cod sicurezza
cod. seg
cod. seguranca
cod. segurança
cod. sicurezza
codice di sicurezza
codice di verifica
codigo
codigo de seguranca
codigo de segurança
crittogramma
cryptogram
cryptogramme
cv2
cvc
cvc2
cvn
cvv
cvv2
cód seguranca
cód segurança
cód. seguranca
cód. segurança
código
código de seguranca
código de segurança
de kaart controle
geeft nr uit
issue no
issue number
kaartidentificatienummer
kreditkartenprufnummer
kreditkartenprüfnummer
kwestieaantal
no. dell'edizione
no. di sicurezza
numero de securite
numero de verificacao
numero dell'edizione
numero di identificazione della
scheda
numero di sicurezza
numero van veiligheid
numéro de sécurité
nº autorizzazione
número de verificação
perno il blocco
pin block
prufziffer
prüfziffer
security code
security no
security number
sicherheits kode
sicherheitscode
sicherheitsnummer
speldblok
veiligheid nr
veiligheidsaantal
veiligheidscode
veiligheidsnummer
verfalldatum
```

#### <a name="keyword_card_expiration_terms_dict"></a>Termini di scadenza della scheda parola chiave \_ \_ \_ \_ dict

```
ablauf
data de expiracao
data de expiração
data del exp
data di exp
data di scadenza
data em que expira
data scad
data scadenza
date de validité
datum afloop
datum van exp
de afloop
espira
espira
exp date
exp datum
expiration
expire
expires
expiry
fecha de expiracion
fecha de venc
gultig bis
gultigkeitsdatum
gültig bis
gültigkeitsdatum
la scadenza
scadenza
valable
validade
valido hasta
valor
venc
vencimento
vencimiento
verloopt
vervaldag
vervaldatum
vto
válido hasta
```

## <a name="eu-drivers-license-number"></a>Numero di licenza del driver UE

Queste sono le entità nel tipo di informazione sensibile al numero di licenza del driver UE.

- Austria
- Belgio
- Bulgaria
- Croazia
- Cipro
- Ceco
- Danimarca
- Estonia
- Finlandia
- Francia
- Germania
- Grecia
- Ungheria
- Irlanda
- Italia
- Lettonia
- Lituania
- Lussemburgo
- Malta
- Paesi Bassi
- Polonia
- Portogallo
- Romania
- Slovacchia
- Slovenia
- Spagna
- Svezia
- Regno Unito.

## <a name="eu-national-identification-number"></a>Numero di identificazione nazionale UE

Queste sono le entità nel tipo di informazioni riservate del numero di identificazione nazionale dell'Unione europea.

- Austria
- Belgio
- Bulgaria
- Croazia
- Cipro
- Ceco
- Danimarca
- Estonia
- Finlandia
- Francia
- Germania
- Grecia
- Ungheria
- Irlanda
- Italia
- Lettonia
- Lituania
- Lussemburgo
- Malta
- Paesi Bassi
- Polonia
- Portogallo
- Romania
- Slovacchia
- Slovenia
- Spagna
- Regno Unito.

## <a name="eu-passport-number"></a>Numero passaporto UE

Queste sono le entità nelle informazioni riservate dei numeri di passaporto UE typeThese sono le entità nel bundle di numeri del passaporto UE.

- Austria
- Belgio
- Bulgaria
- Croazia
- Cipro
- Ceco
- Danimarca
- Estonia
- Finlandia
- Francia
- Germania
- Grecia
- Ungheria
- Irlanda
- Italia
- Lettonia
- Lituania
- Lussemburgo
- Malta
- Paesi Bassi
- Polonia
- Portogallo
- Romania
- Slovacchia
- Slovenia
- Spagna
- Svezia
- Regno Unito.

## <a name="eu-social-security-number-or-equivalent-identification"></a>Numero di previdenza sociale dell'Unione europea o identificazione equivalente

Si tratta delle entità che si trovano nel codice fiscale dell'Unione europea o nel tipo di informazioni riservate di identificazione equivalente.

- Austria
- Belgio
- Croazia
- Ceco
- Danimarca
- Finlandia
- Francia
- Germania
- Grecia
- Ungheria
- Portogallo
- Spagna
- Svezia

## <a name="eu-tax-identification-number"></a>Numero di identificazione fiscale UE

Queste entità si trovano nel tipo di informazioni riservate del numero di identificazione fiscale dell'Unione europea.

- Austria
- Belgio
- Bulgaria
- Croazia
- Cipro
- Ceco
- Danimarca
- Estonia
- Finlandia
- Francia
- Germania
- Grecia
- Ungheria
- Irlanda
- Italia
- Lettonia
- Lituania
- Lussemburgo
- Malta
- Paesi Bassi
- Polonia
- Portogallo
- Romania
- Slovacchia
- Slovenia
- Spagna
- Svezia
- Regno Unito.



## <a name="finland-national-id"></a>ID nazionale Finlandia

### <a name="format"></a>Formato

sei cifre più un carattere che indica un secolo più tre cifre più una cifra di controllo

### <a name="pattern"></a>Modello

Il criterio deve includere tutti gli elementi seguenti:

- sei cifre nel formato GGMMAA, che sono una data di nascita
- indicatore di secolo ('-',' +' o ' a')
- numero di identificazione personale a tre cifre
- una cifra o una lettera (senza distinzione tra maiuscole e minuscole) che corrisponde a una cifra di controllo

### <a name="keywords"></a>Parole chiave

```
ainutlaatuinen henkilökohtainen tunnus
henkilökohtainen tunnus
henkilötunnus
henkilötunnusnumero#
henkilötunnusnumero
hetu
ID no
ID number
identification number
identiteetti numero
identity number
ID number
kansallinen henkilötunnus
kansallisen henkilökortin
national ID card
national ID no.
personal ID
personal identity code
personalidnumber#
personbeteckning
personnummer
social security number
sosiaaliturvatunnus
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
tunnistenumero
tunnus numero
tunnusluku
tunnusnumero
verokortti
veronumero
verotunniste
verotunnus
```

## <a name="finland-passport-number"></a>Numero di passaporto Finlandia

Questa entità del tipo di informazioni riservate è disponibile nel tipo di informazioni riservate del passaporto europeo ed è disponibile come entità di tipo informazioni riservate autonome.

### <a name="format"></a>Formato

combinazione di nove lettere e cifre

### <a name="pattern"></a>Modello

combinazione di nove lettere e cifre:

- due lettere (senza distinzione tra maiuscole e minuscole)
- sette cifre

### <a name="keywords"></a>Parole chiave

#### <a name="keywords_eu_passport_number_common"></a>Parole \_ chiave \_ numero del passaporto UE \_ \_ comune

```
passport#
passport #
passport ID
passports
passport no
passport no
passport number
passport number
passport numbers
passport numbers
```

#### <a name="keyword_finland_passport_number"></a>Parola \_ chiave \_ Finlandia \_ Numero passaporto

```
suomalainen passi
passin numero
passin numero.#
passin numero#
passin numero.
passi#
passi number
```

## <a name="france-drivers-license-number"></a>Numero di licenza del driver Francia

Questa entità del tipo di informazioni riservate è disponibile nel tipo di informazioni riservate con il numero di licenza del driver UE ed è disponibile come entità di tipo informazioni riservate autonome.

### <a name="format"></a>Formato

12 cifre

### <a name="pattern"></a>Modello

12 cifre con convalida per lo sconto su modelli simili, ad esempio i numeri di telefono francesi

### <a name="keywords"></a>Parole chiave

#### <a name="keyword_french_drivers_license"></a>\_Licenza per \_ i driver francesi della parola chiave \_

```
driverlic
driverlics
driver license
driver licenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver license
driver licenses
driverslic
driverslics
driverslicence
driverslicences
drivers license
drivers licenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers license
drivers licenses
driver'lic
driver'lics
driver'license
driver'licenses
driver'license
driver'licenses
driver' lic
driver' lics
driver' license
driver' licenses
driver' license
driver' licenses
driver'slic
driver'slics
driver'license
driver'licenses
driver'license
driver'licenses
driver's lic
driver's lics
driver's license
driver's licenses
driver's license
driver's licenses
dl#
dls#
driverlic#
driverlics#
driver license#
driver licenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licenses#
driverslic#
driverslics#
drivers license#
drivers licenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers license#
drivers licenses#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' license#
driver' licenses#
driver'slic#
driver'slics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's license#
driver's licenses#
driving license
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv license
driv licenses
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving license
driving licenses
driving permit
dl no
dlno
dl number
permis de conduire
license number
license number
license numbers
license numbers
numéros de license
```

## <a name="france-national-id-card-cni"></a>Scheda ID nazionale Francia (CNI)

### <a name="format"></a>Formato

12 cifre

### <a name="pattern"></a>Modello

12 cifre

### <a name="keywords"></a>Parole chiave

#### <a name="keywords_france_eu_national_id_card"></a>Parole \_ chiave \_ Francia \_ ID nazionale UE \_ \_

```
card number
carte nationale d'identité
carte nationale d'idenite no
cni#
cni
compte bancaire
national identification number
national identity
nationalidno#
numéro d'assurance maladie
numéro de carte vitale
```

## <a name="france-passport-number"></a>Numero di passaporto Francia

Questa entità del tipo di informazioni riservate è disponibile nel tipo di informazioni riservate del passaporto europeo ed è disponibile come entità di tipo informazioni riservate autonome.

### <a name="format"></a>Formato

nove cifre e lettere

### <a name="pattern"></a>Modello

nove cifre e lettere:

- due cifre
- due lettere (senza distinzione tra maiuscole e minuscole)
- cinque cifre

### <a name="keywords"></a>Parole chiave

#### <a name="keyword_passport"></a>Passport parola chiave \_

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```

## <a name="france-social-security-number-insee-or-equivalent-identification"></a>Numero di previdenza sociale (INSEE) in Francia o identificazione equivalente

Questa entità di tipo di informazioni riservate è inclusa nel codice fiscale dell'Unione europea e nel tipo di informazioni riservate con ID equivalente ed è disponibile come entità di tipo informazioni riservate autonome.

### <a name="format"></a>Formato

15 cifre

### <a name="pattern"></a>Modello

Deve corrispondere a uno dei due modelli seguenti:

- 13 cifre seguite da uno spazio seguito da due cifre o
- 15 cifre consecutive

### <a name="keywords"></a>Parole chiave

#### <a name="keyword_fr_insee"></a>Parola chiave \_ fr \_ INSEE

```
insee
securité sociale
securite sociale
national ID
national identification
numéro d'identité
no d'identité
no. d'identité
numero d'identite
no d'identite
no. d'identite
social security number
social security code
social insurance number
le numéro d'identification nationale
d'identité nationale
numéro de sécurité sociale
le code de la sécurité sociale
numéro d'assurance sociale
numéro de sécu
code sécu
```

## <a name="germany-drivers-license-number"></a>Numero di licenza del driver Germania

Questa entità di tipo di informazioni riservate è inclusa nel tipo di informazioni sensibili al numero di licenza del driver UE ed è disponibile come entità di tipo informazioni riservate autonome.

### <a name="format"></a>Formato

combinazione di 11 cifre e lettere

### <a name="pattern"></a>Modello

11 cifre e lettere (senza distinzione tra maiuscole e minuscole):

- una cifra o una lettera
- due cifre
- sei cifre o lettere
- una cifra
- una cifra o una lettera

### <a name="keywords"></a>Parole chiave

#### <a name="keyword_german_drivers_license_number"></a>Numero di licenza per la parola chiave \_ German \_ drivers \_ \_

```
ausstellungsdatum
ausstellungsort
ausstellende behöde
ausstellende behorde
ausstellende behoerde
führerschein
fuhrerschein
fuehrerschein
führerscheinnummer
fuhrerscheinnummer
fuehrerscheinnummer
führerschein-
fuhrerschein-
fuehrerschein-
führerscheinnummernr
fuhrerscheinnummernr
fuehrerscheinnummernr
führerscheinnummerklasse
fuhrerscheinnummerklasse
fuehrerscheinnummerklasse
nr-führerschein
nr-fuhrerschein
nr-fuehrerschein
no-führerschein
no-fuhrerschein
no-fuehrerschein
n-führerschein
n-fuhrerschein
n-fuehrerschein
permis de conduire
driverlic
driverlics
driver license
driver licenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver license
driver licenses
driverslic
driverslics
driverslicence
driverslicences
drivers license
drivers licenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers license
drivers licenses
driver'lic
driver'lics
driver'license
driver'licenses
driver'license
driver'licenses
driver' lic
driver' lics
driver' license
driver' licenses
driver' license
driver' licenses
driver'slic
driver'slics
driver'license
driver'licenses
driver'license
driver'licenses
driver's lic
driver's lics
driver's license
driver's licenses
driver's license
driver's licenses
dl#
dls#
driverlic#
driverlics#
driver license#
driver licenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licenses#
driverslic#
driverslics#
drivers license#
drivers licenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers license#
drivers licenses#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' license#
driver' licenses#
driver'slic#
driver'slics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's license#
driver's licenses#
driving license
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv license
driv licenses
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving license
driving licenses
driving permit
dlno
```

## <a name="germany-identity-card-number"></a>Numero di carta di identità Germania

### <a name="format"></a>Formato

dal 1 ° novembre 2010: nove lettere e cifre

da 1 aprile 1987 fino al 31 ottobre 2010:10 cifre

### <a name="pattern"></a>Modello

dal 1 ° novembre 2010:

- una lettera (senza distinzione tra maiuscole e minuscole)
- otto cifre

da 1 aprile 1987 fino al 31 ottobre 2010:

- 10 cifre

### <a name="keywords"></a>Parole chiave

#### <a name="keyword_germany_id_card"></a>Parola chiave \_ Germania \_ ID \_ Card

```
ausweis
gpid
identification
identifikation
identifizierungsnummer
identity card
identity number
id-nummer
personal ID
personalausweis
persönliche ID nummer
persönliche identifikationsnummer
persönliche-id-nummer
```

## <a name="germany-passport-number"></a>Numero di passaporto Germania

Questa entità dei tipi di informazioni riservate è inclusa nel tipo di informazioni riservate del passaporto europeo ed è disponibile come entità autonoma per il tipo di informazioni riservate.

### <a name="format"></a>Formato

10 cifre o lettere

### <a name="pattern"></a>Modello

Il criterio deve includere tutti gli elementi seguenti:

- il primo carattere è una cifra o una lettera del set (C, F, G, H, J, K)
- tre cifre
- cinque cifre o lettere del set (C,-H, J-N, P, R, T, V-Z)
- una cifra

### <a name="keywords"></a>Parole chiave

#### <a name="keyword_german_passport"></a>Parola chiave \_ German \_ Passport

```
reisepasse
reisepassnummer
No-Reisepass
Nr-Reisepass
Reisepass-Nr
Passnummer
reisepässe
passeport no.
passeport no
```

## <a name="greece-national-id-card"></a>Scheda ID nazionale Grecia

### <a name="format"></a>Formato

Combinazione di 7-8 lettere e numeri più un trattino

### <a name="pattern"></a>Modello

Sette lettere e numeri (formato precedente):

- Una lettera (qualsiasi lettera dell'alfabeto greco)
- Trattino
- Sei cifre

Otto lettere e numeri (nuovo formato):

- Due lettere il cui carattere maiuscolo si verifica sia negli alfabeti greci che latini (ABEZHIKMNOPTYX)
- Trattino
- Sei cifre

### <a name="keywords"></a>Parole chiave

#### <a name="keyword_greece_id_card"></a>Parola chiave \_ Grecia \_ ID \_ Card

```
greek ID
greek national ID
greek personal ID card
greek police ID
identity card
tautotita
ταυτότητα
ταυτότητας
```

## <a name="hong-kong-identity-card-hkid-number"></a>Numero della carta di identità di Hong Kong (HKID)

### <a name="format"></a>Formato

Combinazione di 8-9 lettere e numeri più parentesi facoltative intorno al carattere finale

### <a name="pattern"></a>Modello

Combinazione di 8-9 lettere:

- 1-2 lettere (senza distinzione tra maiuscole e minuscole)
- Sei cifre
- Il carattere finale (qualsiasi cifra o lettera A), ovvero la cifra del segno di spunta ed è facoltativamente racchiuso tra parentesi.

### <a name="keywords"></a>Parole chiave

#### <a name="keyword_hong_kong_id_card"></a>Parola chiave \_ Hong \_ Kong \_ ID \_ Card

```
hkid
Hong Kong identity card
HKIDC
ID card
identity card
hk identity card
Hong Kong ID
香港身份證
香港永久性居民身份證
身份證
身份証
身分證
身分証
香港身份証
香港身分證
香港身分証
香港身份證
香港居民身份證
香港居民身份証
香港居民身分證
香港居民身分証
香港永久性居民身份証
香港永久性居民身分證
香港永久性居民身分証
香港永久性居民身份證
香港非永久性居民身份證
香港非永久性居民身份証
香港非永久性居民身分證
香港非永久性居民身分証
香港特別行政區永久性居民身份證
香港特別行政區永久性居民身份証
香港特別行政區永久性居民身分證
香港特別行政區永久性居民身分証
香港特別行政區非永久性居民身份證
香港特別行政區非永久性居民身份証
香港特別行政區非永久性居民身分證
香港特別行政區非永久性居民身分証
```

## <a name="ip-address"></a>Indirizzo IP

### <a name="format"></a>Formato

#### <a name="ipv4"></a>IPv4:

Modello complesso, che rappresenta le versioni formattate (periodiche) e non formattate (senza periodi) degli indirizzi IPv4

#### <a name="ipv6"></a>IPv6:

Modello complesso che rappresenta i numeri IPv6 formattati (che includono due punti)

### <a name="pattern"></a>Modello

### <a name="keywords"></a>Parole chiave

#### <a name="keyword_ipaddress"></a>Parola chiave \_ IPAddress

```
IP (this keyword is case-sensitive)
ip address
ip addresses
internet protocol
IP-כתובת ה
```

## <a name="india-permanent-account-number-pan"></a>Numero di account permanente India (PAN)

### <a name="format"></a>Formato

10 lettere o cifre

### <a name="pattern"></a>Modello

10 lettere o cifre:

- Tre lettere (senza distinzione tra maiuscole e minuscole)
- Lettera in C, P, H, F, A, T, B, L, J, G (senza distinzione tra maiuscole e minuscole)
- Una lettera
- Quattro cifre
- Lettera (senza distinzione tra maiuscole e minuscole)

### <a name="keywords"></a>Parole chiave

#### <a name="keyword_india_permanent_account_number"></a>\_Numero di \_ \_ account permanente dell'India \_ (parola chiave)

```
Permanent Account Number
PAN
```

## <a name="india-unique-identification-aadhaar-number"></a>Numero di identificazione univoca India (Aadhaar)

### <a name="format"></a>Formato

12 cifre contenenti spazi o trattini facoltativi

### <a name="pattern"></a>Modello

12 cifre:

- Una cifra, che non è 0 o 1
- Tre cifre
- Uno spazio o un trattino facoltativo
- Quattro cifre
- Uno spazio o un trattino facoltativo
- Cifra finale, ovvero la cifra di controllo

### <a name="keywords"></a>Parole chiave

#### <a name="keyword_india_aadhar"></a>Parola chiave \_ India \_ Aadhar

```
aadhaar
aadhar
aadhar#
uid
आधार
uidai
```

## <a name="indonesia-identity-card-ktp-number"></a>Numero KTP (Indonesia Identity Card)

### <a name="format"></a>Formato

16 cifre contenenti punti facoltativi

### <a name="pattern"></a>Modello

16 cifre:

- Codice Provincia a due cifre
- Un punto (facoltativo)
- Codice Regency o City a due cifre
- Codice del sottodistretto a due cifre
- Un punto (facoltativo)
- Sei cifre nel formato GGMMAA, che sono la data di nascita
- Un punto (facoltativo)
- Quattro cifre

### <a name="keywords"></a>Parole chiave

#### <a name="keyword_indonesia_id_card"></a>Parola chiave \_ Indonesia \_ ID \_ Card

```
KTP
Kartu Tanda Penduduk
Nomor Induk Kependudukan
```

## <a name="international-banking-account-number-iban"></a>Numero di conto bancario internazionale (IBAN)

### <a name="format"></a>Formato

Codice paese (due lettere) più cifre di controllo (due cifre) più :::no-loc text="bban"::: il numero (fino a 30 caratteri)

### <a name="pattern"></a>Modello

Il criterio deve includere tutti gli elementi seguenti:

- Codice paese di due lettere
- Due cifre di controllo (seguite da uno spazio facoltativo)
- 1-7 gruppi di quattro lettere o cifre (possono essere separati da spazi)
- 1-3 lettere o cifre

Il formato di ogni paese è leggermente diverso. Il tipo di informazioni riservate IBAN copre questi 60 paesi:

```
ad, ae, al, at, az, ba, be, bg, bh, ch, cr, cy, cz, de, dk, do, ee, es, fi, fo, fr, gb, ge, gi, gl, gr, hr, hu, that is, il, is, it, kw, kz, lb, li, lt, lu, lv, mc, md, me, mk, mr, mt, mu, nl, no, pl, pt, ro, rs, sa, se, si, sk, sm, tn, tr, vg
```

### <a name="keywords"></a>Parole chiave

nessuno

## <a name="ireland-personal-public-service-pps-number"></a>Numero di servizio pubblico personale (PPS) dell'Irlanda

### <a name="format"></a>Formato

Formato precedente (fino al 31 dicembre 2012):

- sette cifre seguite da 1-2 lettere

Nuovo formato (1 gennaio 2013 e successivo):

- sette cifre seguite da due lettere

### <a name="pattern"></a>Modello

Formato precedente (fino al 31 dicembre 2012):

- sette cifre
- da una a due lettere (senza distinzione tra maiuscole e minuscole)

Nuovo formato (1 gennaio 2013 e successivo):

- sette cifre
- lettera (senza distinzione tra maiuscole e minuscole) che è una cifra di controllo alfabetica
- Lettera facoltativa nell'intervallo A-I o &quot; W&quot;

### <a name="keywords"></a>Parole chiave

#### <a name="keywords_ireland_eu_national_id_card"></a>Parole chiave \_ Irlanda \_ UE \_ National \_ ID \_ Card

```
client identity service
identification number
personal ID number
personal public service number
personal service no
phearsanta seirbhíse poiblí
pps no
pps number
pps num
pps service no
ppsn
ppsno#
ppsno
psp
public service no
publicserviceno#
publicserviceno
revenue and social insurance number
rsi no
rsi number
rsin
seirbhís aitheantais cliant
uimh
uimhir aitheantais chánach
uimhir aitheantais phearsanta
uimhir phearsanta seirbhíse poiblí
tax ID
tax identification no
tax identification number
tax no#
tax no
tax number
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```

## <a name="israel-bank-account-number"></a>Numero conto di Israele Bank

### <a name="format"></a>Formato

13 cifre

### <a name="pattern"></a>Modello

Formattato

- due cifre
- trattino
- tre cifre
- trattino
- otto cifre

Non formattato:

- 13 cifre consecutive

### <a name="keywords"></a>Parole chiave

#### <a name="keyword_israel_bank_account_number"></a>Parola chiave \_ Israel \_ Bank \_ account \_ Number

```
Bank Account Number
Bank Account
Account Number
מספר חשבון בנק
```

## <a name="israel-national-identification-number"></a>Numero di identificazione nazionale Israele

### <a name="format"></a>Formato

nove cifre

### <a name="pattern"></a>Modello

nove cifre consecutive

### <a name="keywords"></a>Parole chiave

#### <a name="keyword_israel_national_id"></a>Parola chiave \_ Israel \_ National \_ ID

```
מספר זהות
מספר זיה וי
מספר זיהוי ישר אלי
זהותישר אלית
هو ية اسرائيل ية عدد
هوية إسرائ يلية
رقم الهوية
عدد هوية فريدة من نوعها
ID number#
ID number
identity no
identity number#
identity number
israeliidentitynumber
personal ID
unique ID
```

## <a name="italy-drivers-license-number"></a>Numero di licenza del driver Italia

Questa entità di tipo di informazioni riservate è inclusa nel tipo di informazioni sensibili al numero di licenza del driver UE ed è disponibile come entità di tipo informazioni riservate autonome.

### <a name="format"></a>Formato

una combinazione di 10 lettere e cifre

### <a name="pattern"></a>Modello

una combinazione di 10 lettere e cifre:

- una lettera (senza distinzione tra maiuscole e minuscole)
- lettera &quot; A &quot; o &quot; V &quot; (senza distinzione tra maiuscole e minuscole)
- sette cifre
- una lettera (senza distinzione tra maiuscole e minuscole)

### <a name="keywords"></a>Parole chiave

#### <a name="keyword_italy_drivers_license_number"></a>Keyword \_ Italia \_ driver \_ numero di licenza \_

```
numero di patente
patente di guida
patente guida
patenti di guida
patenti guida
```

## <a name="japan-bank-account-number"></a>Numero del conto bancario in Giappone

### <a name="format"></a>Formato

sette o otto cifre

### <a name="pattern"></a>Modello

Numero conto bancario:

- sette o otto cifre
- codice ramo conto bancario:
- quattro cifre
- uno spazio o un trattino (facoltativo)
- tre cifre

### <a name="keywords"></a>Parole chiave

#### <a name="keyword_jp_bank_account"></a>Parola chiave \_ Jp \_ Bank \_ account

```
Checking Account Number
Checking Account
Checking Account #
Checking Acct Number
Checking Acct #
Checking Acct No.
Checking Account No.
Bank Account Number
Bank Account
Bank Account #
Bank Acct Number
Bank Acct #
Bank Acct No.
Bank Account No.
Savings Account Number
Savings Account
Savings Account #
Savings Acct Number
Savings Acct #
Savings Acct No.
Savings Account No.
Debit Account Number
Debit Account
Debit Account #
Debit Acct Number
Debit Acct #
Debit Acct No.
Debit Account No.
口座番号
銀行口座
銀行口座番号
総合口座
普通預金口座
普通口座
当座預金口座
当座口座
預金口座
振替口座
銀行
バンク
```

#### <a name="keyword_jp_bank_branch_code"></a>Parola chiave \_ Jp \_ Bank \_ Branch \_ code

```
支店番号
支店コード
店番号
```

## <a name="japan-drivers-license-number"></a>Numero di licenza del driver giapponese

### <a name="format"></a>Formato

12 cifre

### <a name="pattern"></a>Modello

12 cifre consecutive

### <a name="keywords"></a>Parole chiave

#### <a name="keyword_jp_drivers_license_number"></a>Parola \_ chiave \_ driver \_ Jp \_ numero di licenza

```
driver license
drivers license
driver'license
drivers licenses
driver'licenses
driver licenses
dl#
dls#
lic#
lics#
運転免許証
運転免許
免許証
免許
運転免許証番号
運転免許番号
免許証番号
免許番号
運転免許証ナンバー
運転免許ナンバー
免許証ナンバー
運転免許証no
運転免許no
免許証no
免許no
運転経歴証明書番号
運転経歴証明書
運転免許証No.
運転免許No.
免許証No.
免許No.
運転免許証#
運転免許#
免許証#
免許#
```

## <a name="japan-passport-number"></a>Numero passaporto Giappone

### <a name="format"></a>Formato

due lettere seguite da sette cifre

### <a name="pattern"></a>Modello

due lettere (senza distinzione tra maiuscole e minuscole) seguite da sette cifre

#### <a name="keyword_jp_passport"></a>Parola chiave \_ Jp \_ Passport

```
Passport
Passport Number
Passport No.
Passport #
パスポート
パスポート番号
パスポートナンバー
パスポート＃
パスポート#
パスポートNo.
旅券番号
旅券番号＃
旅券番号♯
旅券ナンバー
```

## <a name="japan-residence-card-number"></a>Numero di carta di residenza del Giappone

### <a name="format"></a>Formato

12 lettere e cifre

### <a name="pattern"></a>Modello

12 lettere e cifre:

- due lettere (senza distinzione tra maiuscole e minuscole)
- otto cifre
- due lettere (senza distinzione tra maiuscole e minuscole)

### <a name="keywords"></a>Parole chiave

#### <a name="keyword_jp_residence_card_number"></a>Parola \_ chiave \_ Jp \_ Residence \_ numero di carta

```
Residence card number
Residence card no
Residence card #
在留カード番号
在留カード
在留番号
```

## <a name="japan-resident-registration-number"></a>Numero di registrazione residente in Giappone

### <a name="format"></a>Formato

11 cifre

### <a name="pattern"></a>Modello

11 cifre consecutive

### <a name="keywords"></a>Parole chiave

#### <a name="keyword_jp_resident_registration_number"></a>\_Numero di \_ \_ registrazione residente \_ della parola chiave JP

```
Resident Registration Number
Residents Basic Registry Number
Resident Registration No.
Resident Register No.
Residents Basic Registry No.
Basic Resident Register No.
外国人登録証明書番号
証明書番号
登録番号
外国人登録証
```

## <a name="japan-social-insurance-number-sin"></a>Numero di assicurazioni sociali Giappone (SIN)

### <a name="format"></a>Formato

7-12 cifre

### <a name="pattern"></a>Modello

7-12 cifre:

- quattro cifre
- un trattino (facoltativo)
- sei cifre o
- 7-12 cifre consecutive

### <a name="keywords"></a>Parole chiave

#### <a name="keyword_jp_sin"></a>Parola chiave \_ Jp \_ sin

```
Social Insurance No.
Social Insurance Num
Social Insurance Number
健康保険被保険者番号
健保番号
基礎年金番号
雇用保険被保険者番号
雇用保険番号
保険証番号
社会保険番号
社会保険No.
社会保険
介護保険
介護保険被保険者番号
健康保険被保険者整理番号
雇用保険被保険者整理番号
厚生年金
厚生年金被保険者整理番号
```

## <a name="malaysia-identification-card-number"></a>Numero scheda di identificazione Malaysia

### <a name="format"></a>Formato

12 cifre contenenti trattini facoltativi

### <a name="pattern"></a>Modello

12 cifre:

- sei cifre nel formato AAMMGG, che sono la data di nascita
- trattino (facoltativo)
- codice del luogo di nascita di due lettere
- trattino (facoltativo)
- tre cifre casuali
- codice Gender a una cifra

### <a name="keywords"></a>Parole chiave

#### <a name="keyword_malaysia_id_card_number"></a>Parola \_ chiave \_ Malaysia \_ ID \_ numero di carta

```
digital application card
i/c
i/c no
ic
ic no
ID card
identification Card
identity card
k/p
k/p no
kad akuan diri
kad aplikasi digital
kad pengenalan malaysia
kp
kp no
mykad
mykas
mykid
mypr
mytentera
malaysia identity card
malaysian identity card
nric
personal identification card
```

## <a name="netherlands-citizens-service-bsn-number"></a>Numero del servizio Citizen (del BSN) dei Paesi Bassi

### <a name="format"></a>Formato

otto-nove cifre contenenti spazi facoltativi

### <a name="pattern"></a>Modello

otto-nove cifre:

- tre cifre
- uno spazio (facoltativo)
- tre cifre
- uno spazio (facoltativo)
- due-tre cifre

### <a name="keywords"></a>Parole chiave

#### <a name="keywords_netherlands_eu_national_id_card"></a>Parole chiave \_ Paesi Bassi \_ UE \_ National \_ ID \_ Card

```
bsn#
bsn
burgerservicenummer
citizen service number
person number
personal number
personal numeric code
person-related number
persoonlijk nummer
persoonlijke numerieke code
persoonsgebonden
persoonsnummer
sociaal-fiscaal nummer
social-fiscal number
sofi
sofinummer
uniek identificatienummer
uniek identiteitsnummer
unique identification number
unique identity number
uniqueidentityno#
```

## <a name="new-zealand-ministry-of-health-number"></a>Ministero della Nuova Zelanda per il numero di integrità

### <a name="format"></a>Formato

tre lettere, uno spazio (facoltativo) e quattro cifre

### <a name="pattern"></a>Modello

- tre lettere (senza distinzione tra maiuscole e minuscole) tranne ' I ' è O '
- uno spazio (facoltativo)
- quattro cifre

### <a name="keywords"></a>Parole chiave

#### <a name="keyword_nz_terms"></a>Termini della parola chiave \_ NZ \_

```
NHI
New Zealand
Health
treatment
National Health Index Number
nhi number
nhi no.
NHI#
National Health Index No.
National Health Index Id
National Health Index #
```

## <a name="norway-identification-number"></a>Numero di identificazione della Norvegia

### <a name="format"></a>Formato

11 cifre

### <a name="pattern"></a>Modello

11 cifre:

- sei cifre nel formato GGMMAA, che sono la data di nascita
- numero singolo a tre cifre
- due cifre di controllo

### <a name="keywords"></a>Parole chiave

#### <a name="keyword_norway_id_number"></a>Parola \_ chiave \_ numero ID Norvegia \_

```
Personal identification number
Norwegian ID Number
ID Number
Identification
Personnummer
Fødselsnummer
```

## <a name="philippines-unified-multi-purpose-identification-number"></a>Numero di identificazione unificato per più scopi per le Filippine

### <a name="format"></a>Formato

12 cifre separate da trattini

### <a name="pattern"></a>Modello

12 cifre:

- quattro cifre
- segno meno
- sette cifre
- segno meno
- una cifra

### <a name="keywords"></a>Parole chiave

#### <a name="keyword_philippines_id"></a>Parola chiave \_ Philippines \_ ID

```
Unified Multi-Purpose ID
UMID
Identity Card
Pinag-isang Multi-Layunin ID
```

## <a name="poland-identity-card"></a>Scheda identità della Polonia

### <a name="format"></a>Formato

tre lettere e sei cifre

### <a name="pattern"></a>Modello

tre lettere (senza distinzione tra maiuscole e minuscole) seguite da sei cifre

### <a name="keywords"></a>Parole chiave

#### <a name="keyword_poland_national_id_passport_number"></a>Parola chiave \_ Polonia \_ National \_ ID \_ Passport \_ Number

```
Dowód osobisty
Numer dowodu osobistego
Nazwa i numer dowodu osobistego
Nazwa i nr dowodu osobistego
Nazwa i nr dowodu tożsamości
Dowód Tożsamości
dow. os.
```

## <a name="poland-national-id-pesel"></a>ID nazionale Polonia (PESEL)

### <a name="format"></a>Formato

11 cifre

### <a name="pattern"></a>Modello

- 6 cifre che rappresentano la data di nascita nel formato AAMMGG
- 4 cifre
- 1 controllo digit

### <a name="keywords"></a>Parole chiave

#### <a name="keyword_pesel_identification_number"></a>Numero di identificazione della parola chiave \_ PESEL \_ \_

```
dowód osobisty
dowódosobisty
niepowtarzalny numer
niepowtarzalnynumer
nr.-pesel
nr-pesel
numer identyfikacyjny
pesel
tożsamości narodowej
```

## <a name="poland-passport-number"></a>Numero passaporto Polonia

Questa entità dei tipi di informazioni riservate è inclusa nel tipo di informazioni riservate del passaporto europeo ed è disponibile come entità autonoma per il tipo di informazioni riservate.

### <a name="format"></a>Formato

due lettere e sette cifre

### <a name="pattern"></a>Modello

Due lettere (senza distinzione tra maiuscole e minuscole) seguite da sette cifre

### <a name="keywords"></a>Parole chiave

#### <a name="keyword_poland_national_id_passport_number"></a>Parola chiave \_ Polonia \_ National \_ ID \_ Passport \_ Number

```
Numer paszportu
Nr. Paszportu
Paszport
```

## <a name="portugal-citizen-card-number"></a>Numero di carta Citizen del Portogallo

### <a name="format"></a>Formato

otto cifre

### <a name="pattern"></a>Modello

otto cifre

### <a name="keywords"></a>Parole chiave

#### <a name="keyword_portugal_citizen_card"></a>Parola chiave \_ Portugal \_ Citizen \_ Card

```
bilhete de identidade
cartão de cidadão
citizen card
document number
documento de identificação
ID number
identification no
identification number
identity card no
identity card number
national ID card
nic
número bi de portugal
número de identificação civil
número de identificação fiscal
número do documento
portugal bi number
```

## <a name="portugal-drivers-license-number"></a>Numero di patente del driver Portugal

Questa entità di tipo informazioni riservate è disponibile solo nel tipo di informazioni riservate con il numero di licenza del driver UE.

### <a name="format"></a>Formato

due modelli: due lettere seguite da 5-8 cifre con caratteri speciali

### <a name="pattern"></a>Modello

Modello 1: due lettere seguite da 5/6 con caratteri speciali:

- Due lettere (senza distinzione tra maiuscole e minuscole)
- Trattino
- Cinque o sei cifre
- Uno spazio
- Una cifra

Modello 2: una lettera seguita da 6/8 cifre con caratteri speciali:

- Una lettera (senza distinzione tra maiuscole e minuscole)
- Trattino
- Sei o otto cifre
- Uno spazio
- Una cifra

### <a name="keywords"></a>Parole chiave

#### <a name="keywords_eu_drivers_license_number"></a>Parole \_ chiave \_ numero di licenza del driver UE \_ \_

```
driverlic
driverlics
driver license
driver licenses
driverlicence
driverlicences
driver lic
driver lics
driver license
driver licenses
driver license
driver licenses
driverslic
driverslics
driverslicence
driverslicences
drivers license
drivers licenses
drivers lic
drivers lics
drivers license
drivers licenses
drivers license
drivers licenses
driver'lic
driver'lics
driver'license
driver'licenses
driver'license
driver'licenses
driver' lic
driver' lics
driver' license
driver' licenses
driver' license
driver' licenses
driver'slic
driver'slics
driver'license
driver'licenses
driver'license
driver'licenses
driver's lic
driver's lics
driver's license
driver's licenses
driver's license
driver's licenses
dl#
dls#
driverlic#
driverlics#
driver license#
driver licenses#
driverlicence#
driverlicences#
driver lic#
driver lics#
driver license#
driver licenses#
driver licenses#
driverslic#
driverslics#
drivers license#
drivers licenses#
driverslicence#
driverslicences#
drivers lic#
drivers lics#
drivers license#
drivers licenses#
drivers license#
drivers licenses#
driver'lic#
driver'lics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver' lic#
driver' lics#
driver' license#
driver' licenses#
driver' license#
driver' licenses#
driver'slic#
driver'slics#
driver'license#
driver'licenses#
driver'license#
driver'licenses#
driver's lic#
driver's lics#
driver's license#
driver's licenses#
driver's license#
driver's licenses#
driving license
driving license
dlno#
driv lic
driv licen
driv license
driv licenses
driv license
driv licenses
driver licen
drivers licen
driver's licen
driving lic
driving licen
driving licenses
driving license
driving licenses
driving permit
dl no
dlno
dl number
```

#### <a name="keywords_portugal_eu_drivers_license_number"></a>Parole chiave per \_ \_ \_ il \_ numero di patente del conducente UE \_

```
carteira de motorista
carteira motorista
carteira de habilitação
carteira habilitação
número de licença
número licença
permissão de condução
permissão condução
Licença condução Portugal
carta de condução
```

## <a name="saudi-arabia-national-id"></a>ID nazionale Arabia Saudita

### <a name="format"></a>Formato

10 cifre

### <a name="pattern"></a>Modello

10 cifre consecutive

### <a name="keywords"></a>Parole chiave

#### <a name="keyword_saudi_arabia_national_id"></a>Parola chiave \_ Saudi \_ Arabia \_ nazionale \_ ID

```
Identification Card
I card number
ID number
الوطنية الهوية بطاقة رقم
```

## <a name="singapore-national-registration-identity-card-nric-number"></a>Numero NRIC (National Registration Identity Card) di Singapore

### <a name="format"></a>Formato

nove lettere e cifre

### <a name="pattern"></a>Modello

- nove lettere e cifre:
- lettera &quot; F &quot; , &quot; G &quot; , &quot; S &quot; o &quot; T &quot; (senza distinzione tra maiuscole e minuscole)
- sette cifre
- una cifra di controllo alfabetica

### <a name="keywords"></a>Parole chiave

#### <a name="keyword_singapore_nric"></a>Keyword \_ Singapore \_ NRIC

```
National Registration Identity Card
Identity Card Number
NRIC
IC
Foreign Identification Number
FIN
身份证
身份證
```

## <a name="south-africa-identification-number"></a>Numero di identificazione sudafricano

### <a name="format"></a>Formato

13 cifre che possono contenere spazi

### <a name="pattern"></a>Modello

13 cifre:

- sei cifre nel formato AAMMGG, che sono la data di nascita
- quattro cifre
- indicatore di cittadinanza a una sola cifra
- il numero &quot; 8 &quot; o &quot; 9&quot;
- una cifra, che è una cifra di checksum

### <a name="keywords"></a>Parole chiave

#### <a name="keyword_south_africa_identification_number"></a>Numero di identificazione della parola chiave \_ sud \_ Africa \_ \_

```
Identity card
ID
Identification
```

## <a name="south-korea-resident-registration-number"></a>Numero di registrazione residente Corea del sud

### <a name="format"></a>Formato

13 cifre contenenti un trattino

### <a name="pattern"></a>Modello

13 cifre:

- sei cifre nel formato AAMMGG, che sono la data di nascita
- segno meno
- una cifra determinata dal secolo e dal sesso
- codice dell'area di nascita a quattro cifre
- una cifra utilizzata per distinguere le persone per le quali i numeri precedenti sono identici
- cifra di controllo.

### <a name="keywords"></a>Parole chiave

#### <a name="keyword_south_korea_resident_number"></a>Numero residente della parola chiave \_ South \_ Korea \_ \_

```
National ID card
Citizen's Registration Number
Jumin deungnok beonho
RRN
주민등록번호
```

## <a name="spain-social-security-number-ssn"></a>Codice fiscale Spagna (SSN)

Questa entità del tipo di informazioni riservate è inclusa nel tipo di informazioni riservate sull'ID o sul numero di previdenza sociale dell'UE ed è disponibile come entità di tipo informazioni riservate autonome.

### <a name="format"></a>Formato

11-12 cifre

### <a name="pattern"></a>Modello

11-12 cifre:

- due cifre
- barra (facoltativo)
- da sette a otto cifre
- barra (facoltativo)
- due cifre

### <a name="keywords"></a>Parole chiave

nessuno

## <a name="sweden-national-id"></a>ID nazionale svedese

### <a name="format"></a>Formato

10 o 12 cifre e un delimitatore facoltativo

### <a name="pattern"></a>Modello

10 o 12 cifre e un delimitatore facoltativo:

- due cifre (facoltativo)
- Sei cifre nel formato data AAMMGG
- delimitatore di &quot; - &quot; o &quot; + &quot; (facoltativo)
- quattro cifre

### <a name="keywords"></a>Parole chiave

#### <a name="keywords_swedish_national_identifier"></a>Parole \_ chiave \_ identificatore nazionale svedese \_

```
ID no
ID number
ID#
identification no
identification number
identifikationsnumret#
identifikationsnumret
identitetshandling
identity document
identity no
identity number
id-nummer
personal ID
personnummer#
personnummer
skatteidentifikationsnummer
```

## <a name="sweden-passport-number"></a>Numero passaporto Svezia

Questa entità dei tipi di informazioni riservate è inclusa nel tipo di informazioni riservate del passaporto europeo ed è disponibile come entità autonoma per il tipo di informazioni riservate.

### <a name="format"></a>Formato

otto cifre

### <a name="pattern"></a>Modello

otto cifre consecutive

### <a name="keywords"></a>Parole chiave

#### <a name="keyword_sweden_passport"></a>Parola chiave \_ Sweden \_ Passport

```
visa requirements
Alien Registration Card
Schengen visas
Schengen visa
Visa Processing
Visa Type
Single Entry
Multiple Entries
G3 Processing Fees
```

#### <a name="keyword_passport"></a>Passport parola chiave \_

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```

## <a name="swift-code"></a>Codice SWIFT

### <a name="format"></a>Formato

quattro lettere seguite da 5-31 lettere o cifre

### <a name="pattern"></a>Modello

quattro lettere seguite da 5-31 lettere o cifre:

- codice bancario di quattro lettere (senza distinzione tra maiuscole e minuscole)
- uno spazio facoltativo
- 4-28 lettere o cifre (il numero di conto bancario di base (BBAN))
- uno spazio facoltativo
- da una a tre lettere o cifre (resto del BBAN)

### <a name="keywords"></a>Parole chiave

#### <a name="keyword_swift"></a>Parola chiave \_ Swift

```
international organization for standardization 9362
iso 9362
iso9362
swift#
swift code
swift number
swiftroutingnumber
swift code
swift number #
swift routing number
bic number
bic code
bic #
bic#
bank identifier code
Organization internationale de normalization 9362
rapide #
code SWIFT
le numéro de swift
swift numéro d'acheminement
le numéro BIC
#BIC
code identificateur de banque
SWIFTコード
SWIFT番号
BIC番号
BICコード
SWIFT コード
SWIFT 番号
BIC 番号
BIC コード
金融機関識別コード
金融機関コード
銀行コード
```

## <a name="taiwan-national-identification-number"></a>Numero di identificazione nazionale taiwanese

### <a name="format"></a>Formato

una lettera (in inglese) seguita da nove cifre

### <a name="pattern"></a>Modello

una lettera (in inglese) seguita da nove cifre:

- una lettera (in inglese, senza distinzione tra maiuscole e minuscole)
- il numero &quot; 1 &quot; o &quot; 2&quot;
- otto cifre

### <a name="keywords"></a>Parole chiave

#### <a name="keyword_taiwan_national_id"></a>Parola \_ chiave \_ Taiwan \_ ID nazionale

```
身份證字號
身份證
身份證號碼
身份證號
身分證字號
身分證
身分證號碼
身份證號
身分證統一編號
國民身分證統一編號
簽名
蓋章
簽名或蓋章
簽章
```

## <a name="taiwan-passport-number"></a>Numero passaporto Taiwan

### <a name="format"></a>Formato

- Numero passaporto biometrico: nove cifre
- numero Passport non biometrico: nove cifre

### <a name="pattern"></a>Modello

Numero passaporto biometrico:

- il carattere &quot; 3&quot;
- otto cifre

Numero passaporto non biometrico:

- nove cifre

### <a name="keywords"></a>Parole chiave

#### <a name="keyword_taiwan_passport"></a>Parola chiave \_ Taiwan \_ Passport

```
ROC passport number
Passport number
Passport no
Passport Num
Passport #
护照
中華民國護照
Zhōnghuá Mínguó hùzhào
```

## <a name="taiwan-resident-certificate-arctarc-number"></a>Numero di certificati residenti (ARC/TARC Tax) taiwanese

### <a name="format"></a>Formato

10 lettere e cifre

### <a name="pattern"></a>Modello

10 lettere e cifre:

- due lettere (senza distinzione tra maiuscole e minuscole)
- otto cifre

### <a name="keywords"></a>Parole chiave

#### <a name="keyword_taiwan_resident_certificate"></a>Parola chiave \_ Taiwan \_ Resident \_ Certificate

```
Resident Certificate
Resident Cert
Resident Cert.
Identification card
Alien Resident Certificate
ARC
Taiwan Area Resident Certificate
TARC
居留證
外僑居留證
台灣地區居留證
```

## <a name="uk-drivers-license-number"></a>Regno Unito. numero di patente del driver

Questa entità di tipo di informazioni riservate è inclusa nel tipo di informazioni sensibili al numero di licenza del driver UE ed è disponibile come entità di tipo informazioni riservate autonome.

### <a name="format"></a>Formato

Combinazione di 18 lettere e cifre nel formato specificato

### <a name="pattern"></a>Modello

18 lettere e cifre:

- cinque lettere (senza distinzione tra maiuscole e minuscole) o la cifra &quot; 9 &quot; al posto di una lettera
- una cifra
- cinque cifre nel formato di data MMDDY per la data di nascita (il settimo carattere viene incrementato di 50 se il driver è femmina, ovvero da 51 a 62 anziché da 01 a 12)
- due lettere (senza distinzione tra maiuscole e minuscole) o la cifra &quot; 9 &quot; al posto di una lettera
- cinque cifre

### <a name="keywords"></a>Parole chiave

#### <a name="keyword_uk_drivers_license"></a>Parola chiave \_ UK \_ drivers \_ License

```
DVLA
light vans
quad bikes
motor cars
125cc
sidecar
tricycles
motorcycles
photo card license
learner drivers
license holder
license holders
driving licenses
driving license
dual control car
```

## <a name="uk-electoral-roll-number"></a>Regno Unito. numero di rollup elettorale

### <a name="format"></a>Formato

due lettere seguite da 1-4 cifre

### <a name="pattern"></a>Modello

due lettere (senza distinzione tra maiuscole e minuscole) seguite da 1-4 numeri

### <a name="keywords"></a>Parole chiave

#### <a name="keyword_uk_electoral"></a>Parola chiave \_ UK \_ elettorale

- candidatura al Consiglio
- modulo di candidatura
- registro elettorale
- rullo elettorale

## <a name="uk-national-health-service-number"></a>Regno Unito. numero di servizio integrità nazionale

### <a name="format"></a>Formato

10-17 cifre separate da spazi

### <a name="pattern"></a>Modello

10-17 cifre:

- tre o 10 cifre
- uno spazio
- tre cifre
- uno spazio
- quattro cifre

### <a name="keywords"></a>Parole chiave

#### <a name="keyword_uk_nhs_number"></a>Numero di parole chiave \_ \_ NHS UK \_

```
national health service
nhs
health services authority
health authority
```

#### <a name="keyword_uk_nhs_number1"></a>Parola chiave \_ UK \_ NHS \_ number1

```
patient ID
patient identification
patient no
patient number
```

#### <a name="keyword_uk_nhs_number_dob"></a>Parola chiave \_ UK \_ NHS \_ Number \_ DOB

```
GP
DOB
D.O.B
Date of Birth
Birth Date
```

## <a name="uk-national-insurance-number-nino"></a>Regno Unito. numero assicurativo nazionale (NINO)

Questa entità del tipo di informazioni riservate è inclusa nel tipo di informazioni riservate del numero di identificazione nazionale UE ed è disponibile come entità di tipo informazioni riservate autonome.

### <a name="format"></a>Formato

sette caratteri o nove caratteri separati da spazi o trattini

### <a name="pattern"></a>Modello

due possibili modelli:

- due lettere (NINOs valide usano solo determinati caratteri in questo prefisso, che viene convalidato da questo modello, senza distinzione tra maiuscole e minuscole)
- sei cifre
- ' A ',' B ',' c'o ' d'(come il prefisso, sono consentiti solo determinati caratteri nel suffisso, senza distinzione tra maiuscole e minuscole)

OR

- due lettere
- uno spazio o un trattino
- due cifre
- uno spazio o un trattino
- due cifre
- uno spazio o un trattino
- due cifre
- uno spazio o un trattino
- ' A ',' B ',' c'o ' d'


### <a name="keywords"></a>Parole chiave

#### <a name="keyword_uk_nino"></a>Parola chiave \_ UK \_ Nino

```
national insurance number
national insurance contributions
protection act
insurance
social security number
insurance application
medical application
social insurance
medical attention
social security
Great Britain
NI Number
NI No.
NI #
NI#
insurance#
insurance number
national insurance#
nationalinsurancenumber
```

## <a name="uk-unique-taxpayer-reference-number"></a>Regno Unito. Numero di riferimento del contribuente univoco

Questo tipo di informazioni riservate è disponibile solo per l'utilizzo in:

- criteri di prevenzione della perdita dei dati
- criteri di conformità delle comunicazioni
- governance delle informazioni
- gestione dei record
- Microsoft cloud app Security

### <a name="format"></a>Formato

10 cifre senza spazi e delimitatori

### <a name="pattern"></a>Modello

10 cifre

### <a name="keywords"></a>Parole chiave

#### <a name="keywords_uk_eu_tax_file_number"></a>Parole chiave \_ Regno Unito \_ UE \_ numero di \_ file fiscali \_

```
tax number
tax file
tax ID
tax identification no
tax identification number
tax no#
tax no
tax registration number
tax ID#
tax ID no#
tax ID number#
tax no#
tax number#
tax number
tin ID
tin no
tin#
```

## <a name="us-bank-account-number"></a>Numero di conto bancario statunitense

### <a name="format"></a>Formato

6-17 cifre

### <a name="pattern"></a>Modello

6-17 cifre consecutive

### <a name="keywords"></a>Parole chiave

#### <a name="keyword_usa_bank_account"></a>Parola \_ chiave \_ Usa \_ account bancario

```
Checking Account Number
Checking Account
Checking Account #
Checking Acct Number
Checking Acct #
Checking Acct No.
Checking Account No.
Bank Account Number
Bank Account #
Bank Acct Number
Bank Acct #
Bank Acct No.
Bank Account No.
Savings Account Number
Savings Account.
Savings Account #
Savings Acct Number
Savings Acct #
Savings Acct No.
Savings Account No.
Debit Account Number
Debit Account
Debit Account #
Debit Acct Number
Debit Acct #
Debit Acct No.
Debit Account No.
```

## <a name="us-drivers-license-number"></a>Numero di licenza del driver U.S.

### <a name="format"></a>Formato

Dipende dallo stato

### <a name="pattern"></a>Modello

dipende dallo stato, ad esempio New York:

- nove cifre formattate come ddd corrisponderanno.
- nove cifre come nnnnnnnnn non corrisponderanno.

### <a name="keywords"></a>Parole chiave

#### <a name="keyword_us_drivers_license_abbreviations"></a>Parole \_ chiave \_ driver US- \_ \_ abbreviazioni di licenza

```
DL
DLS
CDL
CDLS
ID
IDs
DL#
DLS#
CDL#
CDLS#
ID#
IDs#
ID number
ID numbers
LIC
LIC#
```

#### <a name="keyword_us_drivers_license"></a>Parola chiave \_ US \_ drivers \_ License

```
DriverLic
DriverLics
DriverLicense
DriverLicenses
Driver Lic
Driver Lics
Driver License
Driver Licenses
DriversLic
DriversLics
DriversLicense
DriversLicenses
Drivers Lic
Drivers Lics
Drivers License
Drivers Licenses
Driver'Lic
Driver'Lics
Driver'License
Driver'Licenses
Driver' Lic
Driver' Lics
Driver' License
Driver' Licenses
Driver'sLic
Driver'sLics
Driver'sLicense
Driver'sLicenses
Driver's Lic
Driver's Lics
Driver's License
Driver's Licenses
identification number
identification numbers
identification #
ID card
ID cards
identification card
identification cards
DriverLic#
DriverLics#
DriverLicense#
DriverLicenses#
Driver Lic#
Driver Lics#
Driver License#
Driver Licenses#
DriversLic#
DriversLics#
DriversLicense#
DriversLicenses#
Drivers Lic#
Drivers Lics#
Drivers License#
Drivers Licenses#
Driver'Lic#
Driver'Lics#
Driver'License#
Driver'Licenses#
Driver' Lic#
Driver' Lics#
Driver' License#
Driver' Licenses#
Driver'sLic#
Driver'sLics#
Driver'sLicense#
Driver'sLicenses#
Driver's Lic#
Driver's Lics#
Driver's License#
Driver's Licenses#
ID card#
ID cards#
identification card#
identification cards#
```

#### <a name="keyword_state_name_drivers_license_name"></a>Parola chiave \_ [ \_ nome stato \_ ] \_ nome licenza driver \_

- abbreviazione di stato (ad esempio, &quot; NY &quot; )
- nome dello stato (ad esempio, &quot; New York &quot; )

## <a name="us-individual-taxpayer-identification-number-itin"></a>Numero di identificazione dei singoli contribuenti statunitensi

### <a name="format"></a>Formato

nove cifre che iniziano con &quot; 9 &quot; e contengono &quot; 7 &quot; o &quot; 8 &quot; come quarta cifra, eventualmente formattate con spazi o trattini

### <a name="pattern"></a>Modello

formattato

- cifra &quot; 9&quot;
- due cifre
- uno spazio o un trattino
- &quot;7 &quot; o &quot; 8&quot;
- una cifra
- uno spazio o un trattino
- quattro cifre

formattato

- cifra &quot; 9&quot;
- due cifre
- &quot;7 &quot; o &quot; 8&quot;
- cinque cifre

### <a name="keywords"></a>Parole chiave

#### <a name="keyword_itin"></a>Parola \_ chiave.

```
taxpayer
tax ID
tax identification
itin
i.t.i.n.
ssn
tin
social security
tax payer
itins
tax ID
individual taxpayer
```

## <a name="us-social-security-number-ssn"></a>Numero di previdenza sociale (SSN) degli Stati Uniti

### <a name="format"></a>Formato

nove cifre, che possono trovarsi in un modello formattato o non formattato

>[!Note]
> Se viene emesso prima della metà di 2011, un SSN presenta una formattazione forte, in cui alcune parti del numero devono rientrare in determinati intervalli in modo che siano valide (ma non è presente alcun checksum).
>

### <a name="pattern"></a>Modello

quattro funzioni cercano SNSS in quattro modelli diversi:

- Func \_ SSN trova SNSS con formattazione avanzata pre-2011 formattata con trattini o spazi (ddd-dd-dddd o ddd dd dddd)
- Il \_ SSN non formattato Func \_ trova SNSS con una formattazione avanzata precedente a 2011 non formattata come nove cifre consecutive (nnnnnnnnn)
- \_ \_ La funzione SSN con formattazione casuale Func \_ trova le snss post-2011 formattate con trattini o spazi (ddd-dd-dddd o ddd dd dddd)
- Func \_ casuale non \_ formattato \_ SSN trova il post-2011 SNSS che non sono formattati come nove cifre consecutive (nnnnnnnnn)

### <a name="keywords"></a>Parole chiave

#### <a name="keyword_ssn"></a>Parola chiave \_ SSN

```
SSA Number
social security number
social security #
social security#
social security no
Social Security#
Soc Sec
SSN
SSNS
SSN#
SS#
SSID
```

## <a name="us--uk-passport-number"></a>STATI UNITI/REGNO UNITO Numero passaporto

Regno Unito il tipo di informazioni riservate per il numero di Passport è disponibile nel tipo di informazioni riservate del passaporto europeo ed è disponibile come entità di tipo informazioni riservate autonome.

### <a name="format"></a>Formato

nove cifre

### <a name="pattern"></a>Modello

nove cifre consecutive

### <a name="keywords"></a>Parole chiave

#### <a name="keyword_passport"></a>Passport parola chiave \_

```
Passport Number
Passport No
Passport #
Passport#
PassportID
Passport no
passport number
パスポート
パスポート番号
パスポートのNum
パスポート＃
Numéro de passeport
Passeport n °
Passeport Non
Passeport #
Passeport#
PasseportNon
Passeportn °
```

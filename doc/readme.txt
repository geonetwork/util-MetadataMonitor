INSPIRE Metadata monitor (versie 1.0.5)
========================
Datum: 9 november 2010, 21 februari 2011, 24 maart 2011, 5 oktober 2011
Auteur: Thijs Brentjens (t.brentjens@geonovum.nl) in opdracht van Geonovum.

===============
Changes versie 1.0.5
* Fix gebruik van juiste organisatiegegevens Metadata point of contact
* Opnemen van emailadres metadata point of contact in de CSV-bestanden

===============
Changes versie 1.0.4
* Escapen van linebreaks (volgende regel) en tabs in teksten, zodat het CSV-bestand niet vervormd wordt als die voorkomen in waardes van de metadata
* opnemen van de gebruiksvoorwaarden voor services in de CSV-bestanden
* beter onderhoudbaar / modulair: een bestand ./xsl/common.xsl aangemaakt met de gemeenschappelijke templates voor services, datasets

===============
Changes versie 1.0.3
* Aparte XSL xsl/dataset2csv_time.xsl toegevoegd met extra elementen m.b.t. tijdselementen voor dataset. Voor gebruik dient deze geconfigureerd te worden in conf/settings.properties

===============
Changes versie 1.0.2
* Extra elementen m.b.t. beperkingen in de uitvoer voor dataset en services. Aanpassingen aan 2 XSL-bestanden.

===============
Changes versie 1.0.1 t.o.v. 1.0
* fix voor Windows paden (spaties e.d.), in run.bat en java-code

===============

Met de INSPIRE Metadata monitor tool kan metadata van een Catalogue Service for the Web (CSW) samengevat worden in een spreadsheet, in dit geval Tab Seperated Files.
Deze spreadsheets kunnen ingelezen worden in office-pakketen om te gebruiken voor monitoring van INSPIRE metadata. 
De tool maakt standaard gebruik van de CSW van het NationaalGeoregister voor INSPIRE metadata. Middels configuratie kan elke CSW die voldoet aan het ISO Applicatieon Profile 1.0 gebruikt worden.

Dit document beschrijft achtereenvolgens:
Vereisten
Installatie
Inhoud en bestanden
INSPIRE Metadata monitor draaien
Beschrijving (interne) werking INSPIRE Metadata monitor
Uitvoer: spreadsheets met metadata dataset / service / onbekend
Configuratie
Logging
Beperkingen huidige tool
Wat te doen als het niet werkt?
Aannames voor CSW
Java dependencies

Vereisten
=========
Vereisten voor werking zijn:
- een harde schijf of USB-schijf, met de meegleverd scripts is een Windows-omgeving het handigste
- een internetverbinding, voor aanroepen van de CSW

De tool is gemaakt om eenvoudig te kunnen draaien. Uitgangspunt is hierbij dat Geonovum-medewerkers er gebruik van kunnen maken. De Geonovum-medewerkers gebruiken standaard Windows-omgevingen.
De tool is hiervoor uitgewerkt en bevat daarom een Java Runtime Environment voor Windows, zodat er verder niets geinstalleerd hoeft te worden. Omdat de tool draait op het Java platform kan hij eenvoudig op andere besturingssystemen ingezet worden.

Installatie
===========
Windows-omgevingen
------------------
Pak het zip-bestand uit in een directory naar keuze. Dit kan direct op de harde schijf, maar ook vanaf een USB-stick bijvoorbeeld. De tool is hiermee geinstalleerd voor Windows omgevingen.

Om zeker te zijn dat de tool lokaal kan draaien is voor Windows-omgevingen (getest op Windows XP, Windows Vista) een volledige JRE (1.6.0) meegeleverd.
Deze JRE kan verwijderd worden, dan dient echter in run.bat de JAVA_HOME directory geset te zijn om de tool te draaien.

Andere omgevingen dan Windows
-----------------------------
De tool is geschreven in Java en kan eenvoudig op andere omgevingen draaien. Daarvoor is het nodig dat een JRE aanwezig is, dat de JAVA_HOME directory gezet is en.
In het classpath moeten alle libraries uit ./inspiremetadatamonitor/lib/ en ./inspiremetadatamonitor/lib/xalan-j_2_7_1/ geladen zijn.
De tool draaien kan door de klasse nl.geonovum.CswStatistics aan te roepen, bijvoorbeeld:

$JAVA_HOME/bin/java -cp $CLASSPATH nl.geonovum.inspire.CswStatistics

Het script ./inspiremetadatamonitor/run.bat doet dit voor Windows omgevingen.
De rest van deze documentatie gaat uit van Windows omgevingen, voor zo ver relevant.

Inhoud en bestanden
===================
Na het uitpakken zijn de volgende directories en bestanden beschikbaar:
./jre6/ 	Deze directory bevat een volledige Java Runtime Environment, versie 6
./bin/		Deze directory bevat de gecompileerde Java classes van de tool
./conf/		Deze directory bevat de configuratiebestanden van de tool
./doc/		Deze directory bevat de documentatie van de tool
./lib/		Deze directory bevat de vereiste Java libraries om de tool te draaien, m.n. voor Xalan en Log4j
./log/		In deze directory worden de log-files weggeschreven.
./results/	In deze directory worden de spreadsheets en metadata (XML) weggeschreven.
./src/		Deze directory bevat de Java-broncode
./xsl/		Deze directory bevat de XSL-bestanden voor de transformaties
run.bat		Dit bestand kan op Windows omgevingen gebruikt worden om de applicatie te draaien.

INSPIRE Metadata monitor draaien
==================
De spreadsheets maken (de tool draaien) kan door te dubbelklikken op run.bat in de directory ./inspiremetadatamonitor/ (de directory na het uitpakken van het ZIP-bestand).

Het hele proces kan enkele minuten duren, vooral het ophalen van de CSW-records kost tijd. Voortgang is te zien in het scherm waarin logging plaats vindt.
Als de tool klaar is verdwijnt het scherm en staan in de directory ./results/ de spreadsheets (in de naam staat de datum en tijd opgegeven) en de ruwe metadata (1 XML-bestand).

Beschrijving (interne) werking INSPIRE Metadata monitor
============
Deze tool werkt als volgt om overzichten van de INSPIRE metadata te maken in spreadsheets:
1. de tool roept een CSW aan om alle records in ISO19139-formaat op te halen.

Een voorbeeld request om alle records op te halen van het Nationaal GeoRegister:
http://nationaalgeoregister.nl/geonetwork/srv/en/inspire?request=GetRecords&service=CSW&version=2.0.2&resultType=results&constraintLanguage=FILTER&constraint_language_version=1.1.0&typeNames=csw%3ARecord&elementSetName=summary&OutputSchema=http%3A//www.isotc211.org/2005/gmd&maxRecords=1000000

2. Na het ophalen schrijft de tool de XML-records weg in de directory ./results/ (tenzij in de configuratie anders opgegeven) in bestanden met als naam:
	metadata_records_{DATUM}_{TIJD}.xml
{DATUM} en {TIJD} zijn respectievelijk de datum en tijd van het begin van het ophalen van de records. 

3. Na het opslaan transformeert de tool de gegevens naar spreadsheets in tekst-formaten (TAB-gescheiden in dit geval).
inspire_ngr_datasets_{DATUM}_{TIJD}.txt
inspire_ngr_services_{DATUM}_{TIJD}.txt
inspire_ngr_unknown_{DATUM}_{TIJD}.txt.
{DATUM} en {TIJD} zijn respectievelijk de datum en tijd van het begin van het ophalen van de records.
Deze versie maakt gebruik van XSLT om die transformatie te doen. Hiermee kan flexibel een ander formaat worden toegevoegd. Momenteel wordt TXT (met tabs als scheidingsteken) als output ondersteund.

Transformaties / formaten
--------------------------------
De transformaties staan (standaard) in de bestanden:
- voor datasets: ./xsl/dataset2csv.xsl
- voor services: ./xsl/service2csv.xsl
- voor onbekend (als hierarchyLevel niet opgegeven is conform INSPIRE): ./xsl/unknown2csv.xsl
Een toelichting van het onderscheid tussen metadata van datasets, services en onbekend volgt hieronder.
Andere transformaties zijn op te geven door andere bestanden te plaatsen in de dorectory ./xsl/ en in de settings.properties de namen te veranderen.

Uitvoer: spreadsheets met metadata dataset / service / onbekend
=============================
Het onderscheid tussen metadata voor een dataset of voor een service wordt gemaakt op basis van de INSPIRE-regels voor metadata en specifiek het hierarchyLevel.
Dit betekent concreet:
* als metadata voor een dataset wordt beschouwd alle metadata waarbij het hierarchyLevel (beter gezegd: de eerste waarde van hierarchyLevel) 'dataset' of 'series' is. In Xpath:
//gmd:MD_Metadata/gmd:hierarchyLevel[1]/gmd:MD_ScopeCode/@codeListValue='dataset' en
//gmd:MD_Metadata/gmd:hierarchyLevel[1]/gmd:MD_ScopeCode/@codeListValue='series'

* als metadata voor een service wordt beschouwd alle metadata waarbij het hierarchyLevel 'service' is. In Xpath:
//gmd:MD_Metadata/gmd:hierarchyLevel[1]/gmd:MD_ScopeCode/@codeListValue='service'

NOTA BENE: INSPIRE vereist dat het hierarchyLevel is ingevuld als dataset, series of service. Al het andere wordt NIET als INSPIRE metadata beschouwd door deze tool, maar als onbekend bewschouwd.
Deze metadata komt terecht in een aparte spreadsheet:
* onbekend (en daarmee is de metadata niet conform de INSPIRE eisen): al de andere records (dus die zonder hierarchylevel of een afwijkende waarde).

Configuratie
============
De configuratie van de tool kan gewijzigd worden door de waardes te veranderen in het bestand:
	conf/settings.properties
Aanpassing voor het Nationaal Georegister is normaliter niet nodig.

Standaard waardes configuratie
---------------------
Als geen waarde gevonden wordt in conf/settings.properties, worden de volgende defaults gebruikt (zie ook settings.properties):
cswBaseUrl=http://nationaalgeoregister.nl/geonetwork/srv/en/inspire
maxRecords=10000000
xsltFileNameDataset=dataset2csv
xsltFileNameService=service2csv
xsltFileNameUnknown=unknown2csv
outputFileDirectory=results
outputFilePrefix=inspire_ngr
metadataXmlFileNamePrefix=metadata_records_
fileExtension=.txt

Logging
=======
Voor logging is Log4j versie 1.2 gebruikt, zie http://logging.apache.org/log4j/1.2/index.html.
De applicatie logt standaard in het bestand: ./log/csw_statistics.log en in de console.
De logging is standaard vrij gedetailleerd geconfigureerd (log4j: INFO)
Een andere configuratie van de logging is op te geven in het bestand ./conf/log4j.properties. Het wordt afgeraden dit bestand te wijzigen indien er geen kennis van Log4j is.

Beperkingen huidige tool
========================
Directe validatie nog niet ondersteund
-------------------------------
Met XSLT kunnen de XML-gegevens (de metadata) eenvoudig en flexibel omgezet worden in andere formaten. Omdat XSLT gebruikt wordt, is het echter niet mogelijk controles uit te voeren gebruik makend van externe webservices. Het gebruik van de Nederlandse validator is daarom niet mogelijk binnen XSLT.
Mogelijke oplossing: eerst de metadata ophalen, die wegschrijven en per record valideren. Het resultaat opslaan in een nieuw XML-bestand, waarbij per metadata record het validatieresultaat is opgeslagen.

Beperkingen weergave metadata elementen
---------------------------------------
Bij gebruik van meerdere thesauri bij de keywords, wordt alleen de eerste thesaurus opgenomen. Voor INSPIRE is dit de belangrijkste.

Wat te doen als het niet werkt?
================================
1. helemaal geen resultaten: controleer de logging op foutmeldingen en controleer de internetverbinding
2. foutmelding voor XSL: klopt de configuratie van de XSL? Staan de stylesheets in de juiste directory?

Vereisten voor de CSW
=====================
De CSW ondersteunt CSW 2.0.2, ISO application profile 1.0 (dit is een INSPIRE eis)

Het request wordt uitgevoerd met de volgende parameters:
resultType=results
constraintLanguage=FILTER
constraint_language_version=1.1.0
typeNames=csw%3ARecord
elementSetName=full
OutputSchema=http%3A//www.isotc211.org/2005/gmd
maxRecords={maxRecords uit conf/settings.properties}

Voorbeeld request voor het NGR:
http://nationaalgeoregister.nl/geonetwork/srv/en/inspire?request=GetRecords&service=CSW&version=2.0.2&resultType=results&constraintLanguage=FILTER&constraint_language_version=1.1.0&typeNames=csw%3ARecord&elementSetName=full&OutputSchema=http%3A//www.isotc211.org/2005/gmd&maxRecords=1000000

Java dependencies
=================
Log4j 1.2.16
Xalan 2.7.1

Compileren
==========
Standaard compilatie van de Java-code, bijvoorbeeld via de IDE of command line, volstaat. Er is niet voorzien in een uitgebreider build-proces (zoals met Ant of Maven), gezien de geringe omvang van de applicatie. 

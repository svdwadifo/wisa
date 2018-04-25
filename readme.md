# Teamhandleiding WISA

Andere documenten voor deze opdracht zijn:

* [Lastenboek](./lastenboek.md)
* [Testplan](./testplan.md)
* [Testrapport](./testrapport.md)

## Wat is WISA

Een WISA systeem is een stack software die bestaat uit:
**W**indows Server 2016 + **I**IS + **S**QLServer (of eventueel MySQL) + **A**SP.NET  
Het gaat dus om een webserver gebaseerd op Microsoft technologie.  

* Windows server 2016: het besturings systeem
* IIS: Internet information services, de webserver van microsoft
* MS SQL Server: de database server Microsoft.
* ASP.net: de programmeertaal.

Om steeds opnieuw reproduceerbaar te zijn werken we met een Vagrant

## Installatie

### Operating system

De installatie van Windows gaat eigenlijk heel recht toe recht aan. Een Vagrant box met Windows Server 2016 met de recente updates. Omdat het een Vagrant box is, is er ook een Vagrant user. Voor zowel de `administrator` account als de `vagrant` account is het wachtwoord `vagrant`.

### IIS

IIS is een role binnen van Windows server 2016. De eenvoudigste manier om deze te installeren is via powershell.

```powershell
import-module servermanager
add-windowsfeature web-server -includeallsubfeature
```

Dit installeert ook alle nodige features om ASP.net sites te hosten.

### MS SQL Server

Er bestaan verschillende versies van MS SQL Server. We gebruiken de laatste versie, 2017. Binnenin de verschillende versies bestaan er verschillende edities, deze zijn LocalDB, SQL Express, SQL Express Advanced, SQL Standard, SQL Enterprise en SQL Developer edition. De meest geschikte voor een webserver met database op dezelfde host is een SQL Express. Dit is een gratis versie met als belangrijkste limiet een grootte van 10Gb. Om te installeren downloaden we de installatie files van [Microsoft](https://download.microsoft.com/download/E/F/2/EF23C21D-7860-4F05-88CE-39AA114B014B/SQLEXPR_x64_ENU.exe) en voeren de installer uit.  

Tijdens de installatie kan je kiezen voor ofwel integrated authentication ofwel mixed authentication. Integrated authentication gebruikt de Windows gebruikers om toegang te verlenen tot de database server. Bij mixed authentication worden extra gebruikers aangemaakt in SQL server. De belangrijkste is de System Account (`sa`) user. Dit is de ingebouwde account met `sysadmin` rechten. Het is aangewezen / best practices om integrated authentication te gebruiken. Toch zijn er applicaties die een SQL only account nodig hebben. (zoals de demo applicatie). Het script installeert in integrated authenticaton mode.

### MS SQL Server management

Om SQL server te beheren is er een aparte tool van Microsoft, [SQL Server Mamangement Studio](https://docs.microsoft.com/en-us/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017) (SSMS). Deze kan geïnstalleerd worden op het werkstation van de developer en/of de beheerder. Omdat het over een development server gaat, installeert het deze lokaal op de server. De makkelijk manier hier is om dit uit te voeren via Chocolatey. [Chocolatey](https://chocolatey.org/) is een package management systeem. Uiteraard dient deze eerst geïnstalleerd te zijn.

Install Chocolatey

```powershell
Invoke-Expression ((new-object net.webclient).DownloadString('https://chocolatey.org/install.ps1'))
```

Install SSMS (na het herstarten van de shell)

```powershell
choco install sql-server-management-studio -y
```

## Demo applicatie

Om de stack te testen maken we gebruik van [PasswordState](https://www.clickstudios.com.au). PasswordState is een password management tool. Dit is een ASP.net applicatie die gebruik maakt van een SQL database. Het is een hele uitdaging om correct met wachtwoorden om te gaan. Wie mag welk wachtwoord gebruiken. Hoe kan je een rapport maken van de gebruikte wachtwoorden. Hoe kan je veilig wachtwoorden delen edm.  

Na de installatie van de webserver installeren we PasswordState. Een vereiste van PasswordState is een ingeschakelde `sa` account en dus mixed authentication.


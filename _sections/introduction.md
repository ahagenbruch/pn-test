---
title: "Einleitung"
order: 2
---
Im Mai 2008 startete die Universitätsbibliothek Bochum das Projekt "Integriertes Bibliotheksportal", in dem eine Anwendung
entstehen sollte, um den Benutzern möglichst viele Datenquellen unter einer einheitlichen Oberfläche zur Recherche
auf Basis moderner Suchmaschinentechnologie anbieten zu können. Darüber hinaus sollte die Anwendung auch den
aktuellen Verfügbarkeitsstatus des jeweiligen Titels anzeigen und Benutzerfunktionalitäten wie z.B. Vormerkungen oder
Kontoverwaltung ermöglichen. Nachdem wir zunächst intensiv OCLC TouchPoint evaluiert hatten, zeichnete es sich ab,
dass wir damit nicht alle unsere Ziele erreichen würden. Parallel zu diesem Projekt haben wir eine auf
Open-Source-Software basierende Such-Plattform für die Hochschulbibliographie der RUB entwickelt, die so konzipiert war,
dass mit geringen Anpassungen unterschiedliche Datenquellen integriert werden konnten. Im September 2011 testeten wir mit
ProQuest zum ersten Mal die Möglichkeit, einen Discovery-Dienst in diese Plattform zu integrieren. Da ein Demonstrator
recht schnell implementiert werden konnte, entschlossen wir uns, die Produkte Summon von ProQuest und EDS von Ebsco
durch Integration über die angebotenen APIs sowie einen Suchmaschinenindex mit unseren Katalogdaten nebeneinander
zu evaluieren.
Aufbauend auf den in [Jansen et al. (2010)](http://www.hbz-nrw.de/dokumentencenter/veroeffentlichungen/suchindizes.pdf)
und [Rochkind (2013)](http://journal.code4lib.org/articles/7738) beschriebenen Ansätzen werden wir im Folgenden die
Architektur unseres Protoyps beschreiben, die angebotenen APIs und Metadaten und auch die Erfahrungen, welche die
UB-Mitarbeiter und die regulären Bibliotheksbenutzer mit diesen Diensten gemacht haben.

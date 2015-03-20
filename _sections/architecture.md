---
title: Technische Architektur
order: 3
---
Da es sich bei den Discovery-Diensten um Anwendungen handelt, die auf aktueller Suchmaschinentechnologie basieren,
werden die Daten des Bochumer OPAC in einem täglich ablaufenden ETL-Prozess in eine Apache Solr-Instanz überführt.
Während Summon ebenfalls Solr als Plattform nutzt, gibt es von Ebsco keine konkreten Informationen über die
eingesetzte Technik. Da bei ProQuest an dieser Stelle Open-Source-Software zum Einsatz kommt, kann man als Kunde gut den
zu erwartenden Funktionsumfang der Plattform einschätzen, da sie sowohl im Web als auch in der Literatur ausführlich
dokumentiert ist.
Sowohl Summon als auch EDS bieten ihren Kunden die Wahl zwischen der Nutzung als SaaS (_Software as a Service_), bei dem
die Webanwendung bei den Firmen gehostet wird und nur in geringem Umfang an die lokalen Bedürfnisse (sowohl in Bezug auf
das Layout als auch die Funktionalitäten) angepasst werden kann, und der Einbindung des Dienstes über eine API
(_Application Programming Interface_). Da auch die Testumgebung möglichst neutral gestaltet werden sollte, wurde die
letztere Möglichkeit gewählt. Kernstück der Architektur war ein Django Webapplikationsserver, der sowohl die
Discovery-Dienste als auch den lokalen Index anspricht. Für das Oberfächenlayout kam das CSS-Framework
[Bootstrap](http://getbootstrap.com/2.3.2/) in einer nur rudimentär an das Corporate Design der Ruhr-Universität Bochum
angepassten Form zum Einsatz. Darüber hinaus stellte ein weiterer Django-Server eine Implementierung der
_Document Availability Information API_ (DAIA) ([Voss und Reh](http://gbv.github.io/daiaspec/daia.html)), die sowohl
Verfügbarkeitsinformationen aus dem Lokalsystem als auch aus dem _Journals Online & Print_ (JOP)-Dienst der DNB als auch
dem 360 Link-Resolver von ProQuest hinter dieser plattformneutralen Programmierschnittstelle kapselte. War es nicht
möglich, über diese Dienste die Verfügbarkeit eines Titels zu ermitteln, wurde anhand der in den APIs der
Discovery-Dienste enthaltenen OpenURL-Informationen ein Link zur Fernleihe der DigiBib erzeugt. Damit sollte den
Nutzern die Möglichkeit gegeben werden, sofort anhand der Verfügbarkeit über die Relevanz eines Treffers zu
entscheiden. Eine weitere Entscheidungshilfe über die Relevanz des Treffers wurde über die Integration des Dienstes
[Altmetric](https://api.altmetric.com/) zu geben versucht, der Metriken über die Verwendung eines Artikels im Web
aggregiert.[1](#abbildung-1)

Ein weiteres Desiderat dieses Tests sah vor, dass die Ergebnisse aus den Suchen in den Discovery-Diensten untereinander aber
auch zu unserem OPAC vergleichbar sein sollten. Daher haben wir die Trefferlisten so modelliert, dass eine Anfrage in
eines der drei Systeme die Trefferanzahl der jeweils anderen beiden sowie einen Link mit der Suchanfrage in den
jeweiligen Dienst lieferte. Dabei haben wir uns auf die reine Zeichenkette der Benutzeranfrage beschränkt, da die
Navigatoren der drei Systeme nicht aufeinander abbildbar waren. Während unser lokaler Index über Navigatoren für
Personen und Körperschaften verfügte, die sich aus der GND speisen, bot keiner der beiden Discovery-Dienste diese
Kategorien an, zumal in keinen von ihnen Normdaten integriert sind. Ein hilfreicher Navigator, den beide Dienste
bereitstellten, war die Möglichkeit, die Treffermenge auf _peer reviewed_-Artikel einschränken zu können. Da Summon diese
Kategorie auch in den einzelnen Datensätzen zur Verfügung stellte, konnte diese Information auch prominent in den
Treffern der Ergebnisliste dargestellt werden. Summon bietet darüber hinaus die Möglichkeit, enzyklopädische Informationen
-- aus der Encyclopedia Britannica oder der Wikipedia -- in die Trefferliste zu integrieren. EDS zog diese Funktionalität
zum Ende unserer Testperiode mit den _Research Starters_ nach, jedoch fehlte uns die Zeit, diese zu implementieren und
zu testen. Da in EDS die Zugehörigkeit eines Datensatzes zu der Datenbank, aus der er stammt, abzulesen war, haben wir
anhand des Datenbanktitels ein semi-automatisches Mapping der Titel auf die URLs aus DBIS erstellt, sodass Nutzer sofort
in der jeweiligen Datenbank weiterrecherchieren konnten. Da die Discovery-Dienste für sich in Anspruch nehmen, mit einer
Google-artigen Ein-Schlitz-Suche sehr gute Ergebnisse zu erzielen, haben wir auf die Implementierung einer erweiterten
Suche verzichtet.[2](#abbildung-2)
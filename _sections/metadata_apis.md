---
title: Metadaten und APIs
order: 4
---
ProQuest bezieht seine bibliographischen Daten direkt von den Verlagen, in EDS hingegen stammen diese von
Datenbankanbietern, woraus sich einerseits Unterschiede im Umgang mit den Metadaten ergeben, anderseits schlägt sich
dieser Umstand in zwei unterschiedlichen lizenzrechtlichen Modellen nieder. ProQuest setzt ein sogenanntes
Match-&-Merge-Verfahren ein, bei dem Dubletten von Titeldatensätzen verschiedener Anbieter zu einem einzigen Datensatz
verschmolzen und dadurch angereichert werden. In EDS bleiben die Dubletten im Index nebeneinander bestehen, den Nutzern
wird der umfangreichste Datensatz geliefert. ProQuest setzt also darauf, möglichst viele Informationen zu einem Titel in
einem Datensatz zu vereinen, Ebsco indes legt Wert darauf, dass die Integrität der Datensätze erhalten bleibt und diese
so angezeigt werden wie in den Datenbanken, aus denen sie stammen. Bei diesem Vorgehen besteht zwar die Möglichkeit, vom
Titel direkt in die entsprechende Datenbank zu wechseln, andererseits entgehen den Nutzern Informationen (z.B.
Schlagwörter), die sich nicht beim umfangreichsten Titel befinden und somit nicht angezeigt werden.
ProQuest ermöglicht seinen Nutzern im Übrigen durch passend zur Suchanfrage generierte Datenbankvorschläge den Übergang
in Datenbankangebote. Hierbei traten allerdings Probleme bei der Recherche mit deutschsprachigen Suchbegriffen auf, was
darin begründet liegt, dass die Datenbankvorschläge aus Voreinstellungen und Crowdsourcing generiert werden. So wurden
beispielsweise bei der Suche nach "epilepsy" zwei passende Datenbanken angeboten, während die Suche nach "Epilepsie" keine
Datenbankvorschläge erzeugte.
Aus dem unterschiedlichen Umgang der beiden Discovery-Anbieter mit den Metadaten ergeben sich auch lizenzrechtliche
Konsequenzen für die Nutzer dieser Angebote: Während die Metadaten in den Ergebnislisten in Summon vollständig angezeigt
werden, sodass die Nutzer sofort und auf einen Blick ihr Suchergebnis bewerten können, werden die Trefferlisten in EDS
unter Umständen nur authentifizierten Nutzern vollständig angezeigt. Technisch wäre die Anzeige der Metadaten in EDS
problemlos möglich, allein aufgrund vertraglicher Vereinbarungen sehen Nutzer, die nicht durch Authentifizierung oder
aufgrund ihrer IP-Adresse autorisiert sind, lediglich Stellvertreter anstelle der Titeldaten, was den
Benutzererwartungen an Suchanwendungen im Web fundamental widerspricht.[3](#abbildung-3)

Während sowohl Summon als auch EDS eine REST-basierte API anbieten, deren Daten im XML- bzw. im JSON-Format
ausgegeben werden, besteht der grundlegende Unterschied zwischen diesen beiden Angeboten darin, dass die
Summon-Webanwendung vollständig auf der API beruht, während die EDS-Oberfläche eine von den Programmierschnittstellen
losgelöste Entwicklung darstellt. Während ProQuest mit der Summon-API "dogfooding" betreibt, also den Einsatz des eigenen
Produkts als Basis für andere eigene Produkte, um zu zeigen, dass man von dessen Qualität und Leistungsvermögen überzeugt
ist, entstand die EDS-API eher im Nachgang zur Webanwendung.
Obschon die Dokumentation der Summon-API vollständig im Web für jedermann einsehbar ist und dort sehr übersichtlich die
Komponenten und Parameter beschreibt, ist die EDS-API-Dokumentation nur Kunden zugänglich. Sie gliedert sich in zwei
Dokumente, eine nutzerorientierte Übersicht und eine Referenz, die zwar detailliert über die Parameter Auskunft gibt,
nicht jedoch über die Benennungen der im Index vorhandenen Felder, was dazu führte, dass wir erst im Laufe unserer
Implementierung durch Anschauung der konkreten Daten herausfinden konnten, in welchen Kategorien welche Werte zu
erwarten waren. Wenig hilfreich war in diesem Zusammenhang, dass ein und dieselbe Kategorie mehrere Bezeichnungen
besitzt. Ein Pluspunkt gegenüber der Summon-Dokumentation stellt hingegen die [EDS API Console](https://eds-api.ebscohost.com/Console/)
dar, in der man unterschiedlichste Anfragen ausprobieren kann.
Problematisch ist an der EDS-API allerdings, dass es vier Detailgrade bezüglich des Umfangs der Feldstruktur eines
Datensatzes gibt: „Title“ (nur der Titel), „Brief“ (Titel, Quelle und Schlagwörter) und „Detailed“ (Brief plus Abstract).
Während diese drei Ebenen der „Search“-Funktion der Ergebnisliste vorbehalten sind, gibt es den
vollumfänglichen Datensatz nur mit  der „Retrieve“-Funktion, die für die Vollanzeige genutzt wird. Eine solche
Unterscheidung gibt es in Summon nicht: Dort ist schon in der Ergebnisliste jeder Treffer im vollen Umfang enthalten,
weshalb sich dadurch Oberflächen implementieren lassen, die ohne eine eigene Vollanzeige auskommen. Das Fehlen
einer einheitlichen Feldstruktur macht sich v.a. in der Vollanzeige von EDS negativ bemerkbar: Würde man hier der
Empfehlung von Ebsco folgen, einfach alle Elemente in der Reihenfolge auszugeben, wie sie in der Antwort des
Retrieve-Requests  geliefert werden, könnte man keine mehrsprachigen Oberflächen, keine Mashups mit anderen Diensten und
auch keine Verfügbarkeitsinformationen in der Vollanzeige realisieren.  Um eine flexiblere Lösung umsetzen zu können,
haben wir selbst ein Mapping aus den Daten abgeleitet und in einem iterativen Prozess ergänzt und verbessert.
Beide APIs liefern als Antwortformate sowohl XML als auch JSON aus. Da sich letzteres einerseits in unserer Anwendung
schneller verarbeiten ließ, andererseits von vielen als _das_ Datenformat für das Web angesehen wird, haben wir uns zur
Verwendung dieses Formats entschieden. Beiden Diensten ist gemein, dass sie für die Anzeige von Abstracts HTML in das
Ausgabeformat einbetten, was man dann (solange es wohlgeformt ist) zur Anzeige bringen oder aus dem Output entfernen
kann. Im Falle von EDS ist es allerdings dem Umstand geschuldet, dass dort das JSON aus dem XML-Format abgeleitet wird,
dass HTML-Entitäten so codiert sind, dass sie von gängigen Web-Frameworks falsch interpretiert werden und zur fehlerhaften
Darstellung in der Oberfläche führen. Darüber hinaus enthält das JSON in EDS einiger Schlagwortkategorien XML, das nur
für das XML-Ausgabeformat Sinn macht. Somit wird unnötig zusätzlicher Aufwand für die Bereinigung der Daten innerhalb
der Webanwendung generiert. Insgesamt ist festzuhalten, dass der Einsatz der EDS-API im Vergleich zur Summon-API in ca.
10% umfangreicherem Code resultiert, den man als Benutzer der API schreiben und pflegen muss.
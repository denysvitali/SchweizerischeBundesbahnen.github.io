---
title:  "Softwareentwicklung bei den SBB"
date:   2018-02-28 08:17:00
categories: [engineering, tools, container]
tags: [Softwareengineering, Technologie, Automatisierung]
author: Reto Lehmann
githubuser: ReToCode
---

Die Softwareentwicklung bei den Schweizerischen Bundesbahnen befindet sich im Wandel. Bisher wurden grosse monolithische Anwendungen entwickelt und in der Regel zwei Mal pro Jahr released. Die Entwicklung und der Anwendungsbetrieb waren dabei strikt getrennt. Seit etwas über zwei Jahren findet nun ein Shift in die Cloud- und Containerwelt statt. Die Entwicklungsteams übernehmen dabei die Verantwortung für ihr Produkt, erhalten aber auch die Kompetenzen selbstständig bis in die Produktion zu deployen. So laufen heute bereits mehrere hundert Anwendungen auf der Container Plattform OpenShift und werden teilweise mehrmals pro Tag deployed. Dieser Artikel zeigt an einem Beispiel wie eine neue Anwendung aus Entwicklerperspektive in die Produktion gebracht wird.

Das Entwicklungsteam verwendet folgende Werkzeuge:
* Code-Repository → Bitbucket
* Scrum-Board → Jira Projekt
* Wiki für Dokumentation → Confluence Projekt
* Entwicklungsumgebung → SBB eigener automatischer Installationsprozess

Diese Dinge kann das Entwicklungsteam im Self-Service automatisiert bestellen:

![WZU Self-Service Portal](/images/2018-02-28-wzuself.png "WZU Self-Service Portal")

Anschliessend kann mit der eigentlichen Entwicklung begonnen werden. Damit nicht jedes Projekt bei 0 beginnen muss gibt es bei den SBB Entwicklungsstacks. Diese liefern eine Basisanwendung welche einige Dinge, wie beispielsweise Authentifizierung, Monitoring, Log-Management oder die Build & Deployment Pipeline, mitliefert. Neue Anwendungen basieren auf einem Entwicklungsstack mit Spring Boot und Spring Cloud, im Frontend wird auf Single Page Apps mit Angular gesetzt.

Eine Spring Boot basiertes Anwendungstemplate kann über einen Initializer generiert werden:

![Esta Cloud Initializr](/images/2018-02-28-initializr.png "Esta Cloud Initializr")

Damit erhält das Entwicklungsteam eine Spring Boot Templateanwendung, welche lokal mit Entwicklungstools wie IntelliJ IDEA bearbeitet wird.

Im Template ebenfalls enthalten ist die Konfiguration für den Build & Deploymentprozess. Dazu gibt es ein Jenkinsfile welches die Anwendung über eine Build-Pipeline baut, testet und anschliessend automatisch auf OpenShift deployed. Damit das Deployment funktioniert, müssen auf OpenShift noch Projekte für die Umgebungen der neuen Anwendung erstellt werden. Auch dies ist im Self-Service möglich:

![Cloud Self-Service Portal](/images/2018-02-28-cloud-ssp.png "Cloud Self-Service Portal")

Anschliessend wird der Code im Bitbucket eingecheckt und der Build & Deploymentprozess gestartet:

![Jenkins Build Pipeline](/images/2018-02-28-jenkins.png "Jenkins Build Pipeline")

Nach diesem Prozess ist die soeben erstellte Anwendung in OpenShift deployed. Als letzter Schritt wird nun noch eine Route in OpenShift erstellt, damit die Anwendung von den Benutzern erreichbar ist:

![OpenShift](/images/2018-02-28-openshift.png "OpenShift")

Dieser gesamte Ablauf dauert lediglich rund 15 Minuten. Entwicklungsteams können mit diesen Mitteln selbstständig neue Anwendungen erstellen und bis in die Produktion bringen. Dank dem Rolling-Update Mechanismus der Container Platform ist bei Bedarf sogar ein kontinuierliches Deployment nach jeder Codeänderung möglich.

Wie die Entwicklungsteams ihre Anwendungen auf der Container Plattform betreuen und überwachen erfahrt ihr in einem nächsten Beitrag. Stay tuned.

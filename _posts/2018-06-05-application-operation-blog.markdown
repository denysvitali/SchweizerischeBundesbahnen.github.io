---
title:  "Applikationsbetrieb und Monitoring auf der SBB Container Plattform"
date:   2018-06-05 15:55:00
categories: [engineering, tools, container, monitoring, paas, openshift]
tags: [Softwareengineering, Technologie, Automatisierung, Monitoring, Operation]
author: Reto Lehmann
githubuser: ReToCode
---

In dem Beitrag [Softwareentwicklung bei den SBB](https://blog.sbb.technology/2018/software-engineering-at-sbb-blog/) wurde aufgezeigt wie ein Entwicklungsteam eine Anwendung von der lokalen Entwicklung bis in die Produktion - auf der SBB Container Plattform - bringt. In diesem Folgebeitrag geht es nun darum wie die Überwachung und der Betrieb dieser Anwendungen durch die Entwicklungsteams funktioniert.

# Übersicht
Der Betrieb und die Überwachung von Anwendungen auf der Container Plattform erfolgt auf verschiedenen Schichten:
* Überwachung der Plattform & Basis-Infrastrukturen
* Überwachung der Anwendung im Container
* Überwachung der Anwendung von aussen

## Plattform & Basis-Infrastrukturen
Die OpenShift Container Plattform wird bei den SBB von einer zentralen Stelle betrieben. Diese kümmert sich um die Weiterentwicklung, den Betrieb und um die Integration der Container Plattform mit Umsystemen. 

Das OpenShift Team stellt den Entwicklungsteams die Betriebs- und Monitoringdaten der Plattform transparent zur Verfügung. Entwicklungsteams können somit den Plattformzustand in ihr eigenes Anwendungsmonitoring mit integrieren. Bei Problemen mit einer Anwendung kann so zuerst geprüft werden, ob es sich um ein bekanntes Problem mit der Plattform oder der Basis-Infrastruktur handelt (Konsolidiertes Monitoring). Nachfolgend visuelles Beispiel für den aggregierten Zustand der Plattformen:

![OpenShift Plattform Zustand](/images/2018-06-09-plattform-zustand.png "OpenShift Plattform Zustand")

## Automatische Überwachung & Self-Healing von Anwendungen im Container
Als weiterer Baustein werden die Anwendungen von der Container Plattform selbst überwacht. OpenShift bietet hierzu zwei Arten von Health-Checks an, welche durch die Entwickler selbst definiert werden können:

* Liveness Probe = Prüft ob der Container noch läuft, falls nicht wird er automatisch neu gestartet
* Readyness Probe = Sagt dem Load Balancer ob die Anwendung bereit für Traffic ist oder nicht

In unseren Spring Boot basierten Anwendungen wird [Spring Boot Actuator](https://spring.io/guides/gs/actuator-service/) verwendet um diese Health-Checks zu implementieren. Dies geschieht beispielsweise so:
* Liveness Probe = Prüfung mit TCP auf den Port vom Web Server (gibt der Web Server noch Antwort)
* Readyness Probe = Aufruf auf dem Spring Boot Actuator Endpunkt: /health

Als Erweiterung dazu haben Entwickler der SBB eine [Open-Source Library für Spring Boot](https://github.com/SchweizerischeBundesbahnen/springboot-graceful-shutdown) erstellt, welche ein Zero-Downtime-Deployment von Spring Boot basierten Anwendungen auf OpenShift oder Kubernetes ermöglicht.

Mit diesen Möglichkeiten der Plattform werden bereits viele Fehlerfälle automatisch korrigiert oder zumindest eine "defekte" Anwendung aus dem Load-Balancer entfernt.

## Weitere Einsicht in die Anwendung
Die bisher genannten Punkte dienen nur zur rudimentären und automatischen Überwachung einer Anwendung. Entwicklungsteams benötigen noch weitere Einblicke in ihre Anwendung um diese selbstständig zu betreiben. Dazu steht den Entwicklungsteams die Monitoringlösung von Newrelic zur Verfügung.

### Application Monitoring APM
Um die Anwendung selbst von innen zu überwachen, steht im SBB Java Basis-Dockerimage der [Newrelic Java Agent](https://docs.newrelic.com/docs/agents/java-agent) zur Verfügung. Entwickler müssen lediglich eine Environment-Variable in ihrem Container hinzufügen um diesen zu aktivieren. Dann werden alle Standardmetriken der JVM und Spring Boot automatisch im Newrelic APM Monitoring angezeigt:

![Newrelic APM](/images/2018-06-09-apm.png "Newrelic APM")

Sind weitere Metriken oder Informationen aus der Anwendung interessant, können Entwickler in ihrem Code beliebige Erweiterungen einbauen, welche dann durch den Java Agent ebenfalls reported werden. Auf sämtlichen Metriken und Informationen lassen sich anschliessend Alarme und Alarmkanäle definieren oder auch Dashboards bauen:

![Status Dashboard einer Applikation](/images/2018-06-09-dashboard.png "Status Dashboard einer Applikation")

### Aufruf der Anwendungen von aussen
Als letzte Schicht der Anwendungsüberwachung kann jede Anwendung durch [Newrelic Synthetics](https://newrelic.com/products/synthetics) von aussen (von dort wo die Anwendung genutzt wird) aufgerufen werden. Dabei sind browserbasierte Tests (Klicken auf Buttons und prüfen von HTML-Inhalt) oder auch API Tests möglich:

![API Check einer Anwendung](/images/2018-06-09-apicheck.png "API Check einer Anwendung")

# Schlusswort
Somit sind nun alle Schichten der Anwendungsüberwachung abgedeckt. Selbstverständlich ist dies kein vollständiges Bild der kompletten Überwachung und dem Anwendungsbetrieb. Weitere Infrastrukturen wie beispielsweise Netzwerk, Internetzugang oder auch Alarmierung durch Loganalyse oder ähnliches werden nebst den genannten Techniken ebenfalls rege eingesetzt. Insofern, stay tuned...
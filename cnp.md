# Task Sharing

Algorithmus

1. Problemzerlegung: A-priori vom Systemdesigner
2. Problemverteilung: Interaktionsprotokolle
3. Teilproblemlösung: <Rekursiv>
4. Lösungssynthese: Interaktionsprotokolle

**Delegation**

Annahme: Initiator weiß a-priori, wen zu kontaktieren für die Aufgabe

FIPA:

```yaml
# participant is requested to do a task
- from: initiator
  to: participant
	action: request
# refuse to do it
- from: participant
  to: initiator
	state: refused
	action: refuse
# agree to do it
- from: participant
  to: initiator
	state: agreed and notification necessary
	action: agree
# failed while doing it
- from: participant
  to: initiator
	state: agreed
	action: failure
# task done
- from: participant
  to: initiator
	state: agreed
	action: inform(done)
# here is the result
- from: participant
  to: initiator
	state: agreed
	action: inform(result)
```

**Aufgabenvermittlung**

FIPA:

```yaml
# broker is requested to forward a request
- from: initiator
	to: broker
	action: proxy(speechact, agent-descr, proxy-condition)
# refuse to do it
- from: broker
  to: initiator
	state: refused
	action: refuse
# agree to do it
- from: broker
  to: initiator
	state: agreed and notification necessary
	action: agree
# no one is available
- from: broker
	to: initiator
	state: agreed and no agents found
	action: failure(no_match)
# request them to do
- from: broker
	to: subprotocol
	action: m proxied speechacts
# the responses
- from: subprotocol
	to: broker
	action: n reply messages
# again, no one is available
- from: broker
	to: initiator
	state: agreed and agents found
	action: failure(failure_proxy)
# we got someone
- from: broker
	to: initiator
	state: agreed and agents found
	action: inform(done_proxy)
# here are the results
- from: broker
	to: initiator
	state: subprotocol ok
	action: n reply messages
# failed while doing it
- from: broker
	to: initiator
	state: subprotocol failure
	action: failure(failure_brokering)
```

Brokering:

- Initiator weiß nicht, wer tatsächlich seine Aufgabe ausgeführt hat
- Broker hat Spezialwissen gegenüber Initiator
- Eine eindeutige Vertragssituation liegt vor

Recommendation:

- Dienstanbietearagenten wissen nicht, wie Initiator an ihre Adresse kam
- Eine grobe Signatur in Query anzugeben ist genug
- Konkrete und private Details können später direkt zwischen Anbieter und Nachfrager ausgehandelt werden

Recruiting:

- Initiator weiß nicht, wieso Broker ihm den diensterbringenden Agenten zugeteilt hat
- Broker weiß nicht, ob die Verarbeitung der Aufgabe erfolgreich war

**Contracting**

FIPA:

```yaml
# call for proposal [1:m]
- from: initiator
	to: participant
	action: cfp
# refuse proposal call [i:1]
- from: participant
	to: initiator
	state: before deadline
	action: refuse
# accept proposal call [n-i:1]
	from: participant
	to: initiator
	state: before deadline
	action: propose
# reject the proposal afterwards [1:k]
- from: initiator 
	to: participant
	state: final iteration
	action: reject-proposal
# accept proposed call [1:n-i-k]
- from: initiator
	to: participant
	state: final iteration
	action: accept-proposal
# failed job
- from: participant
	to: initiator
	action: failure
# task done
- from: participant
	to: initiator
	action: inform(done)
# here is the result
- from: participant
	to: initiator
	action: inform(result)
```

FIPA: Iteriertes Contracting

```yaml
# proposal rejected [k-l:1] 
- from: initiator
	to: participant
	state: nonfinal iteration
	action: reject-proposal-1
# lets negotiate again [l:1]
- from: initiator
	to: participant
	state: nonfinal iteration
	action: cfp-2
```

Erweitertes Contracting

![Bildschirm­foto 2023-06-08 um 17.35.39.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/1d4976cd-2c4e-4e05-bce0-b21761959008/Bildschirmfoto_2023-06-08_um_17.35.39.png)

Iteriertes Contracting:

- Initiator kann seine Bedingungen verschärfen und die Toleranz schrittweise verringern
- Die Zahl der Participants nimmt schrittweise ab

Erweitertes Contracting:

- Mehrere Anbieter → Koordination und Nachdenken über die vorhandenen Proposals nötig → Erweitertes Contracting ist dazu fähig

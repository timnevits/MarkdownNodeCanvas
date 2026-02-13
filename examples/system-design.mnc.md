= canvas: System Design Demo v0.2

== style: Default
- font: "IBM Plex Sans"
- fontSize: 14
- bg: #101214
- text: #e8e8e8
- border: #444444
- borderWidth: 2
- radius: 12
- shape: rounded
- shadow: soft
- opacity: 1

== style: Service
- border: #88aaff
- shape: rounded

== style: DataStore
- border: #55ffaa
- shape: ellipse

== style: Queue
- border: #ffaa55
- shape: note

== style: Decision
- border: #ff8855
- shape: diamond

== style: Container
- opacity: 0.95

- rule: #service -> style Service
- rule: #db -> style DataStore
- rule: #queue -> style Queue
- rule: #decision -> style Decision
- rule: #container -> style Container

== HUB: Overview (pos=60,60 size=980x220 tags=#container style=Default seq=1)
This canvas demonstrates:
- Structural-only edges via `- link:`
- Inline links as authoritative edges for flows
- Edge styling hints: route/line/width/color/arrow/label/type/style
- Undirected links for “coupling”
- Containers via `magnet` + `layer`
- Collapsed render hints
- Quoted values in properties

Escaping demo (literal text):
\== literal block header marker
\= literal canvas marker
\- link: literal link marker
\- rule: literal rule marker

Jump to the request flow: [Request Flow](mnc:FLOW1 "Jump")

== SYS: System Container (pos=40,320 size=1220x760 magnet layer=back tags=#container style=Default seq=2)
Container node for the system. Geometry-based membership.

== FLOW1: Request Flow (pos=80,360 size=520x260 tags=#decision style=Default seq=10)
## Request Flow (high level)

A user request hits the [API Gateway](mnc:GW1 "Control"),
which calls [Auth Service](mnc:AUTH1 "Control"),
which reads [User DB](mnc:UDB1 "Data").

If auth passes, the gateway forwards to [Orders Service](mnc:ORD1 "Control").

```
This is code; link-looking text must be ignored by v0.2 inline link extraction:
[Not a real edge](mnc:NOPE "Control")
```

== GW1: API Gateway (pos=650,360 size=300x200 tags=#service style=Default seq=20)
Responsibilities:
- Routing
- Rate limiting
- Request shaping

Key collaborators:
- [Auth Service](mnc:AUTH1 "Control")
- [Orders Service](mnc:ORD1 "Control")
- [Metrics](mnc:OBS1 "Observe")

Directive-looking lines here are Markdown:
- link: should not be parsed inside node body
- rule: should not be parsed inside node body

== AUTH1: Auth Service (pos=650,600 size=300x220 tags=#service style=Default seq=30)
Validates:
- JWT
- Session cookies

Reads:
- [User DB](mnc:UDB1 "Data")
- [Token Cache](mnc:TC1 "Data")

Publishes:
- [Audit Events](mnc:AUDQ1 "Event")

== ORD1: Orders Service (pos=980,360 size=300x220 tags=#service style=Default seq=40)
Creates orders and emits events.

Reads/writes:
- [Orders DB](mnc:ODB1 "Data")

Publishes:
- [Order Events](mnc:ORDQ1 "Event")

== UDB1: User DB (pos=980,620 size=260x180 tags=#db style=Default seq=50)
Primary user datastore.

== ODB1: Orders DB (pos=980,860 size=260x180 tags=#db style=Default seq=60)
Primary orders datastore.

== TC1: Token Cache (pos=650,860 size=300x180 tags=#db style=Default seq=70)
Fast token lookup cache.

== AUDQ1: Audit Queue (pos=300,860 size=300x180 tags=#queue style=Default seq=80)
Audit event stream.

== ORDQ1: Orders Queue (pos=300,620 size=300x180 tags=#queue style=Default seq=90)
Orders event stream.

== OBS1: Metrics / Observability (pos=80,660 size=180x180 tags=#service style=Default seq=100)
Metrics, traces, logs.

== NOTE1: Open Questions (pos=80,900 size=520x160 tags=#container style=Default collapsed=true seq=110)
- What is the retry policy for gateway -> orders?
- Do we need idempotency keys?

Jump: [Back to overview](mnc:HUB "Jump")

# Explicit link overrides / structural-only edges and styling metadata

# Styling derived edges from inline flow links (FLOW1, GW1, AUTH1, ORD1 bodies)
- link: FLOW1 -> GW1 id=E001 type=Control route=orthogonal line=solid width=2 arrow=end label="HTTP"
- link: FLOW1 -> AUTH1 id=E002 type=Control route=orthogonal line=solid width=2 arrow=end label="auth"
- link: FLOW1 -> UDB1 id=E003 type=Data route=bezier line=dashed width=2 arrow=end label="read"
- link: FLOW1 -> ORD1 id=E004 type=Control route=orthogonal line=solid width=2 arrow=end label="forward"

- link: GW1 -> AUTH1 id=E010 type=Control route=orthogonal line=solid width=2 arrow=end
- link: GW1 -> ORD1 id=E011 type=Control route=orthogonal line=solid width=2 arrow=end
- link: GW1 -> OBS1 id=E012 type=Observe route=bezier line=dotted width=1 arrow=end label="emit metrics"

- link: AUTH1 -> UDB1 id=E020 type=Data route=orthogonal line=dashed width=2 arrow=end label="read"
- link: AUTH1 -> TC1 id=E021 type=Data route=orthogonal line=dashed width=2 arrow=end label="read"
- link: AUTH1 -> AUDQ1 id=E022 type=Event route=bezier line=solid width=2 arrow=end color=#ffaa55 label="publish"

- link: ORD1 -> ODB1 id=E030 type=Data route=orthogonal line=dashed width=2 arrow=end label="rw"
- link: ORD1 -> ORDQ1 id=E031 type=Event route=bezier line=solid width=2 arrow=end color=#ffaa55 label="publish"
- link: ORD1 -> OBS1 id=E032 type=Observe route=bezier line=dotted width=1 arrow=end

# Undirected coupling / ownership relationships (structural-only)
- link: SYS - GW1 id=E100 type=Contains route=auto line=dashed width=1 arrow=none label="part of system"
- link: SYS - AUTH1 id=E101 type=Contains route=auto line=dashed width=1 arrow=none
- link: SYS - ORD1 id=E102 type=Contains route=auto line=dashed width=1 arrow=none
- link: SYS - UDB1 id=E103 type=Contains route=auto line=dashed width=1 arrow=none
- link: SYS - ODB1 id=E104 type=Contains route=auto line=dashed width=1 arrow=none
- link: SYS - AUDQ1 id=E105 type=Contains route=auto line=dashed width=1 arrow=none
- link: SYS - ORDQ1 id=E106 type=Contains route=auto line=dashed width=1 arrow=none

# A structural-only edge that is not present inline (explicit edge creation)
- link: NOTE1 -> GW1 id=E200 type=Question route=bezier line=dotted width=1 arrow=end label="retry policy?"
- link: NOTE1 -> ORD1 id=E201 type=Question route=bezier line=dotted width=1 arrow=end label="idempotency?"

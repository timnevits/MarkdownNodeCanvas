= canvas: IF Demo Canvas v0.2

== style: Default
- font: Cochin
- fontSize: 15
- bg: #111111
- text: #e6e6e6
- border: #666666
- borderWidth: 2
- radius: 14
- shape: rounded
- shadow: soft
- opacity: 1

== style: Scene
- border: #88aaff
- shape: rounded

== style: Choice
- border: #55ffaa
- shape: note

== style: Danger
- border: #ff8855
- shape: diamond

== style: Collapsed
- collapsed: true
- opacity: 0.85

- rule: #scene -> style Scene
- rule: #choice -> style Choice
- rule: #danger -> style Danger
- rule: #collapsed -> style Collapsed

== HUB1: Demo Hub (pos=60,60 size=520x220 tags=#scene style=Default seq=1)
This file demonstrates:
- Node bodies as Markdown
- Escaping directive-looking lines
- Inline links (authoritative edges)
- Inline link `"Type"` (Markdown title attribute → edge type)
- Explicit `- link:` overrides (styling/metadata/route/etc.)
- Undirected structural-only links
- Tags + tag-to-style rules
- Quoted property values
- Containers (`magnet`) and layers
- Collapsed nodes

Escaping demo (these are literal text, not directives):
\== This is literal text that starts with ==
\= This is literal text that starts with =
\- link: This is literal text that starts with - link:
\- rule: This is literal text that starts with - rule:

Go to the first scene: [Scene 1](mnc:S001 "Jump")

== ACT1: Act 1 Container (pos=40,320 size=980x520 magnet layer=back style="Default" tags=#scene,#collapsed seq=2)
This is a magnetic container node. Overlap determines membership at runtime.
It is also styled as collapsed via tag rule.

== S001: The Archive Door (pos=90,370 size=420x240 tags=#scene style=Default seq=10)
## Scene 1: The Archive Door

You find a door with a tarnished plaque.

Choices:
- [Open the door](mnc:S010 "Choice")
- [Walk away](mnc:S020 "Choice")

A normal reference in prose: the building is tied to [the old cannery](mnc:S030 "Reference").

Directive-looking lines inside node bodies are Markdown, not structure:
- link: This should NOT be parsed as a link line because it's in a node body.
- rule: This should NOT be parsed as a rule line because it's in a node body.

A fenced code block (inline links inside code fences must be ignored in v0.2):
```
Example link-looking text:
[Do not follow](mnc:SHOULD_NOT_PARSE "Choice")
```

== S010: Inside the Door (pos=560,360 size=440x260 tags=#scene style=Default seq=20)
## Scene 2: Inside the Door

Dust. A desk. A note that reads:

> "If you came here, you already know too much."

You can:
- [Read the note](mnc:S011 "Choice")
- [Leave immediately](mnc:S020 "Choice")

== S011: The Note (pos=560,650 size=440x260 tags=#scene,#danger style=Default seq=30)
## Scene 3: The Note

The ink smears like it was written yesterday.

The note offers two paths:

1. [Follow the address](mnc:S030 "Choice")
2. [Burn the note](mnc:S040 "Choice")

== S020: Walk Away (pos=90,650 size=420x200 tags=#scene style=Default seq=40)
## Scene: Walk Away

You turn away, but the feeling follows.

Return to the start: [Back to the door](mnc:S001 "Choice")

== S030: The Cannery (pos=90,900 size=910x300 tags=#scene style=Default seq=50)
## Scene: The Cannery

A massive building. Broken windows. A distant hum.

A structural relationship exists between the Cannery and the container (added below as undirected).

Proceed:
- [Enter the yard](mnc:S050 "Choice")
- [Call someone](mnc:S060 "Choice")

== S040: Burn the Note (pos=560,940 size=440x220 tags=#scene style=Default seq=60)
## Scene: Burn the Note

It burns too fast. The ash forms a shape like a key.

Go to:
- [The Yard](mnc:S050 "Choice")

== S050: The Yard (pos=90,1240 size=440x240 tags=#scene style=Default seq=70)
## Scene: The Yard

The yard is silent.

A hidden latch is visible beneath a loose brick.

- [Open the latch](mnc:S070 "Choice")
- [Return to the cannery entrance](mnc:S030 "Choice")

== S060: The Call (pos=560,1240 size=440x240 tags=#scene style=Default seq=80)
## Scene: The Call

No one answers. But you hear breathing on the line.

- [Speak](mnc:S080 "Choice")
- [Hang up](mnc:S020 "Choice")

== S070: Under the Brick (pos=90,1520 size=440x240 tags=#scene,#danger style=Default seq=90)
## Scene: Under the Brick

A small box with a warning symbol.

- [Open it](mnc:S090 "Choice")
- [Leave it](mnc:S030 "Choice")

== S080: The Breathing (pos=560,1520 size=440x240 tags=#scene style=Default seq=100)
## Scene: The Breathing

A whisper: “You shouldn’t be there.”

- [Ask who this is](mnc:S081 "Choice")
- [Say nothing](mnc:S020 "Choice")

== S081: The Name (pos=560,1800 size=440x220 tags=#scene style=Default seq=110)
## Scene: The Name

The caller says a name you recognize from the note.

- [Go back to the cannery](mnc:S030 "Choice")

== S090: The Box (pos=90,1800 size=440x220 tags=#scene style=Default seq=120)
## Scene: The Box

Inside: a key and a torn photo.

- [Follow the key](mnc:S100 "Choice")

== S100: The Locked Gate (pos=90,2060 size=910x240 tags=#scene style=Default seq=130)
## Scene: The Locked Gate

A gate blocks the path. The key might fit.

- [Use the key](mnc:S110 "Choice")
- [Turn back](mnc:S020 "Choice")

== S110: Beyond the Gate (pos=90,2320 size=910x240 tags=#scene style=Default seq=140)
## Scene: Beyond the Gate

A small shed glows from within.

- [Enter the shed](mnc:S120 "Choice")

== S120: The Shed (pos=90,2580 size=910x260 tags=#scene style=Default seq=150)
## Scene: The Shed

A map is pinned to the wall with string connecting names.

Jump back to hub: [Hub](mnc:HUB1 "Jump")

# Explicit link overrides / structural-only edges
# (These may override metadata for derived edges or create edges without inline links.)

- link: S001 -> S010 id=L001 type=Choice route=bezier line=solid width=2 color=#55ffaa arrow=end label="Open"
- link: S001 -> S020 id=L002 type=Choice route=bezier line=dashed width=2 color=#55ffaa arrow=end label="Walk away"
- link: S010 -> S011 id=L003 type=Choice route=orthogonal line=solid width=2 arrow=end
- link: S011 -> S030 id=L004 type=Choice route=orthogonal line=solid width=2 arrow=end
- link: S011 -> S040 id=L005 type=Choice route=orthogonal line=dotted width=2 arrow=end

# An undirected structural-only relationship (no inline link required)
- link: S030 - ACT1 id=L900 type=Contains route=auto line=dashed width=1 arrow=none label="in Act 1"

# A structural-only directed edge (no inline link required)
- link: S070 -> S090 id=L901 type=Risk route=straight line=dashed width=2 color=#ff8855 arrow=end label="danger path"

# A link override that adds styling/metadata for a derived edge (inline link exists in S030 body)
- link: S030 -> S060 id=L902 route=bezier line=solid width=1 arrow=end style="Default" label="Call"

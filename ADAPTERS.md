# ADAPTERS.md

## Accountable General Interpreter — Adapter Boundary

Version: `0.1.0`  
Status: Draft  
Authority: Optional Adapter Boundary  
Depends on: `REPO.md`, `AGENTS.md`, `SKILLS.md`, `.imo` carriers, receipts  
Applies to: hardware, ESP32, browser APIs, DOM, CSSOM, JSDOM, Canvas, WebGL, XML, RDF, OWL, WordNet, semantic web, multimedia, and external runtime bridges

---

## §0. AGI Contract — External Boundary

`ADAPTERS.md` defines how an Accountable General Interpreter
touches the outside world.

```text
REPO.md     → scope and role     (WHERE may I act?)
AGENTS.md   → behavior           (HOW must I act?)
SKILLS.md   → computation        (HOW do I compute?)
ADAPTERS.md → external boundary   (WHERE do I touch the world?)
```

An adapter is a boundary object. It translates between
accountable declarations and an external runtime without
compromising accountability.

```text
An adapter may observe.
An adapter may carry.
An adapter may request permission.
An adapter may project accepted receipts.
An adapter may not accept state by itself.
```

The external world is never automatically trusted.
Every adapter operation passes the same gates:
role check, scope check, effect check, validation,
receipt, platform permission.

Without `ADAPTERS.md`, an AGI cannot safely interact
with hardware, browsers, networks, or sensors without
risking side effects before receipt.

---

## §0.1. Original Purpose (preserved below)

This file was originally written as the Optional OMI Adapter
Specification. That framing remains valid as one application
of the AGI contract above.

---

## 0. Original Purpose

Adapters may connect OMI to:

```text
ESP32 hardware
GPIO / I2C / SPI / UART
WiFi / Bluetooth
WebSerial
WebUSB
WebBluetooth
WebHID
WebMIDI
WebRTC
DOM
CSSOM
JSDOM
Canvas
SVG
WebGL
XML
RDF
OWL
WordNet
semantic graphs
media streams
3D models
AI inference runtimes
```

Adapters are not core authority.

Adapters are projection and carrier boundaries.

The core invariant remains:

```text
Recognition is not acceptance.
Citation is not acceptance.
Closure is not acceptance.
Projection is not acceptance.
Validation and receipt accept.
```

Adapter invariant:

```text
An adapter may observe.
An adapter may carry.
An adapter may project.
An adapter may request permission.
An adapter may not accept state by itself.
```

---

## 1. Authority Order

Adapters MUST obey repository authority in this order:

```text
REPO.md
  ↓
AGENTS.md
  ↓
SKILLS.md
  ↓
ADAPTERS.md
  ↓
*.imo carriers
  ↓
candidate declarations
  ↓
validation
  ↓
receipt
  ↓
projection
```

If `ADAPTERS.md` conflicts with `REPO.md`, `REPO.md` SHALL control.

If `ADAPTERS.md` conflicts with `AGENTS.md`, `AGENTS.md` controls resolver behavior.

If `ADAPTERS.md` conflicts with `SKILLS.md`, `SKILLS.md` controls deterministic computation.

Adapters MUST NOT broaden authority granted by `REPO.md`.

---

## 2. Adapter Role

An adapter is a boundary object.

It translates between OMI declarations and an external runtime.

```text
OMI declaration
  ↓
validation
  ↓
receipt
  ↓
projection_allowed()
  ↓
adapter
  ↓
external system
```

An adapter MUST NOT:

```text
accept unvalidated declarations
write receipts directly
bypass effect checks
bypass role/scope permission
treat external output as truth
perform side effects before receipt
```

An adapter MAY:

```text
read external state
stage observations
create candidate declarations
project accepted receipts
return result candidates
emit rejection candidates
```

---

## 3. Required Adapter Gates

Every adapter operation MUST pass these gates:

```text
1. declaration exists
2. scope is valid
3. effect class is declared
4. role/scope permission allows the effect
5. required skill exists
6. candidate validates
7. receipt is accepted
8. projection is allowed
9. external platform permission is satisfied
10. result is recorded as a new candidate or receipt
```

Canonical gate:

```text
No adapter operation may act as authority without validation and receipt.
```

---

## 4. Effect Classes and Adapter Subtypes

The core effect classes are:

```text
pure
read-only
local-write
repo-write
network
hardware
security-sensitive
```

Adapters SHOULD use effect subtypes instead of expanding the core effect enum unless the runtime ABI requires it.

Examples:

```omi-lisp
(effect . hardware)
(effect.subtype . gpio)

(effect . hardware)
(effect.subtype . i2c)

(effect . network)
(effect.subtype . serial)

(effect . network)
(effect.subtype . webrtc)

(effect . local-write)
(effect.subtype . transcode)

(effect . security-sensitive)
(effect.subtype . camera-capture)

(effect . hardware)
(effect.subtype . webgl-render)
```

Mapping:

```text
GPIO / I2C / SPI / UART       → hardware
WiFi / WebRTC / WebSerial     → network
DOM update                    → projection
CSSOM update                  → projection
Canvas draw                   → projection
WebGL render                  → hardware or projection
camera / microphone capture   → security-sensitive
credential / token access     → security-sensitive
file export                   → local-write
repo edit                     → repo-write
```

---

## 5. Bridge Policy

The core bridge words are:

```text
0x001E = record close
0x0078 = system witness
0x007F = local seal
0x7C00 = boot page
0xAA55 = external bridge
```

Adapter-specific bridge words MAY be declared as extension words.

Extension bridge words MUST NOT bypass:

```text
validation
receipt storage
effect permission
role/scope permission
platform permission
projection gates
```

Bridge rule:

```text
Bridge words stage meaning.
Bridge words do not accept.
Bridge words do not project by themselves.
```

---

## 6. ESP32 Adapter

### 6.1 Purpose

The ESP32 adapter maps accepted OMI hardware declarations to ESP32 hardware operations.

Supported adapter surfaces MAY include:

```text
GPIO
ADC
DAC
PWM
I2C
SPI
UART
WiFi
Bluetooth
camera
display
sensors
```

### 6.2 ESP32 Arena

An ESP32 adapter MAY use the OMI arena layout:

```text
OMI_OFFSET_BOOT_ROM          0x00000000
OMI_OFFSET_CONTROL_PLACE     0x00001000
OMI_OFFSET_REGISTER_INJECT   0x00002000
OMI_OFFSET_KERNEL_READER     0x00003000
OMI_OFFSET_GAUGE_TABLE       0x00004000
OMI_OFFSET_BITBOARD          0x00005800
OMI_OFFSET_RING_WINDOW       0x00006000
OMI_OFFSET_HARDWARE_REGS     0x00012000
OMI_OFFSET_PROJECTION        0x00013000
OMI_OFFSET_BOOT_BRIDGE       0x00014000
OMI_OFFSET_RING_STORAGE      0x00020000
```

If a target device cannot support the full arena, the adapter MUST declare a reduced arena profile.

Example:

```omi-lisp
(adapter.esp32.profile . reduced-arena)
(adapter.esp32.arena-size . 262144)
(adapter.esp32.receipt-mode . external-storage)
```

### 6.3 ESP32 Declaration Examples

GPIO write:

```omi-lisp
<((effect . hardware)
  . ((adapter . esp32)
     . ((gpio . set)
        . ((pin . 2)
           . (value . high)))))>
```

I2C write:

```omi-lisp
<((effect . hardware)
  . ((adapter . esp32)
     . ((i2c . write)
        . ((bus . 0)
           . ((addr . 0x40)
              . (data . "1234"))))))>
```

PWM control:

```omi-lisp
<((effect . hardware)
  . ((adapter . esp32)
     . ((pwm . set)
        . ((pin . 4)
           . (duty . 2048)))))>
```

Camera capture:

```omi-lisp
<((effect . security-sensitive)
  . ((adapter . esp32)
     . ((camera . capture)
        . ((resolution . 640x480)
           . (format . jpeg)))))>
```

### 6.4 ESP32 Projection Rule

ESP32 hardware projection requires:

```text
accepted receipt
declared hardware or security-sensitive effect
adapter scope permission
external bridge authorization
platform hardware permission
result receipt
```

The hardware command and hardware result MUST be different relations.

Command:

```omi-lisp
((effect . hardware) . ((gpio . set) . ((pin . 2) . (value . high))))
```

Result:

```omi-lisp
((event . hardware-projected) . command-receipt)
```

---

## 7. Browser Adapter

### 7.1 Purpose

The browser adapter maps accepted OMI declarations to browser APIs and browser projection surfaces.

Supported surfaces MAY include:

```text
DOM
CSSOM
JSDOM-like virtual DOM
Canvas
SVG
WebGL
WebGPU
WebSerial
WebUSB
WebBluetooth
WebHID
WebMIDI
WebRTC
IndexedDB
File API
Clipboard
MediaDevices
```

### 7.2 Browser Permission Rule

Browser adapters MUST obey the browser's own permission model.

A browser adapter MUST NOT assume it can access:

```text
serial ports
USB devices
Bluetooth devices
HID devices
MIDI devices
camera
microphone
file system
clipboard
network sockets
GPU
```

unless the platform grants access.

Adapter permission is not repository acceptance.

Repository acceptance is not platform permission.

Both are required.

```text
receipt accepted
  +
platform permission granted
  =
adapter may project
```

### 7.3 Browser API Declarations

WebSerial open:

```omi-lisp
<((effect . network)
  . ((adapter . webserial)
     . ((serial . open)
        . ((baud . 115200)
           . ((data-bits . 8)
              . (parity . none))))))>
```

WebSerial write:

```omi-lisp
<((effect . network)
  . ((adapter . webserial)
     . ((serial . write)
        . (data . "Hello ESP32!\n"))))>
```

WebUSB transfer:

```omi-lisp
<((effect . network)
  . ((adapter . webusb)
     . ((usb . transfer)
        . ((direction . out)
           . (data . "010203")))))>
```

WebBluetooth read:

```omi-lisp
<((effect . network)
  . ((adapter . webbluetooth)
     . ((bt . read)
        . (characteristic . 0x2A19))))>
```

WebMIDI note:

```omi-lisp
<((effect . network)
  . ((adapter . webmidi)
     . ((midi . note)
        . ((channel . 1)
           . ((pitch . 60)
              . ((velocity . 100)
                 . (duration . 500)))))))>
```

---

## 8. DOM, CSSOM, Canvas, SVG, and WebGL Adapters

### 8.1 DOM Projection

DOM declarations are projection declarations.

A DOM node is not authority.

A DOM render is not acceptance.

DOM create:

```omi-lisp
<((effect . local-write)
  . ((adapter . dom)
     . ((dom . create)
        . ((tag . "div")
           . ((id . "app")
              . (class . "container"))))))>
```

DOM update:

```omi-lisp
<((effect . local-write)
  . ((adapter . dom)
     . ((dom . update)
        . ((selector . "#status")
           . (text . "accepted")))))>
```

### 8.2 CSSOM Projection

```omi-lisp
<((effect . local-write)
  . ((adapter . cssom)
     . ((css . rule)
        . ((selector . ".container")
           . ((property . "background-color")
              . (value . "#f0f0f0"))))))>
```

### 8.3 Canvas Projection

```omi-lisp
<((effect . local-write)
  . ((adapter . canvas)
     . ((canvas . draw)
        . ((x . 50)
           . ((y . 100)
              . ((text . "OMI")
                 . (color . "#FF0000")))))))>
```

### 8.4 WebGL Projection

```omi-lisp
<((effect . hardware)
  . ((adapter . webgl)
     . ((webgl . render)
        . ((vertices . "0 0 1 0 0 1")
           . (render . triangle)))))>
```

WebGL rendering MUST NOT imply acceptance.

---

## 9. Semantic Web Adapter

### 9.1 Purpose

The semantic web adapter maps accepted declarations to semantic carriers and projections.

Supported semantic surfaces MAY include:

```text
XML
RDF
OWL
JSON-LD
WordNet
Prolog-like facts
semantic triples
semantic graphs
```

Semantic artifacts are carriers.

They are not authority by themselves.

```text
RDF is not acceptance.
OWL is not acceptance.
XML is not acceptance.
WordNet is not acceptance.
A semantic graph is not acceptance.
```

### 9.2 WordNet Declaration

```omi-lisp
<((effect . pure)
  . ((adapter . wordnet)
     . ((wn . synset)
        . ((word . "dog")
           . ((pos . n)
              . ((synset-id . 02084071)
                 . (definition . "domestic animal"))))))))>
```

### 9.3 RDF Triple

```omi-lisp
<((effect . pure)
  . ((adapter . rdf)
     . ((rdf . triple)
        . ((subject . "http://example.org/dog")
           . ((predicate . "rdf:type")
              . (object . "http://example.org/Animal")))))))>
```

### 9.4 OWL Class

```omi-lisp
<((effect . pure)
  . ((adapter . owl)
     . ((owl . class)
        . ((id . "Animal")
           . (subclass-of . "LivingBeing"))))))>
```

### 9.5 XML Validation

```omi-lisp
<((effect . pure)
  . ((adapter . xml)
     . ((xml . validate)
        . ((instance . "data.xml")
           . (schema . "schema.xsd")))))>
```

---

## 10. Multimedia Adapter

### 10.1 Purpose

The multimedia adapter declares how media is carried, validated, transformed, and projected.

Media is carrier material.

```text
A video is a carrier.
An audio file is a carrier.
An image is a carrier.
A 3D model is a carrier.
A texture is a carrier.
A thumbnail is a carrier.
```

### 10.2 Media Seeds

```omi-lisp
(seed.video . o.video.o)
(seed.audio . o.audio.o)
(seed.image . o.image.o)
(seed.model3d . o.model3d.o)
(seed.texture . o.texture.o)
```

Seed rule:

```text
A media seed declares interpretation scope.
A media seed does not accept payload truth.
```

### 10.3 Video Chunk

```omi-lisp
<((effect . network)
  . ((adapter . media)
     . ((video.chunk . chunk-001)
        . ((seed . o.video.o)
           . ((codec . h264)
              . ((hash . chunk-hash)
                 . (index . 1)))))))>
```

### 10.4 Audio Mix

```omi-lisp
<((effect . pure)
  . ((adapter . audio)
     . ((audio.mix . mix-001)
        . ((tracks . "(drums bass vocals)")
           . ((bpm . 120)
              . (output . pcm))))))>
```

### 10.5 3D Model

```omi-lisp
<((effect . network)
  . ((adapter . model3d)
     . ((model3d . mesh-001)
        . ((format . gltf)
           . ((vertices . 50000)
              . (texture . texture-001))))))>
```

---

## 11. Adapter Bridge Extension Registry

Adapters MAY declare bridge extensions.

Bridge extensions SHOULD be namespaced by adapter.

Example extension declarations:

```omi-lisp
(adapter.bridge.webserial.open . 0x2A55)
(adapter.bridge.webserial.read . 0x2B55)
(adapter.bridge.webserial.write . 0x2C55)
(adapter.bridge.webserial.close . 0x2D55)

(adapter.bridge.webusb.open . 0x3A55)
(adapter.bridge.webusb.transfer . 0x3C55)

(adapter.bridge.webbluetooth.connect . 0x4B55)
(adapter.bridge.webbluetooth.write . 0x4D55)

(adapter.bridge.webhid.open . 0x5A55)
(adapter.bridge.webhid.write . 0x5C55)

(adapter.bridge.webmidi.open . 0x6A55)
(adapter.bridge.webmidi.note . 0x6B55)

(adapter.bridge.xml.parse . 0x7A55)
(adapter.bridge.xml.validate . 0x7B55)
(adapter.bridge.xml.transform . 0x7C55)
```

Bridge extension rule:

```text
Extension bridge words MAY stage adapter-specific meaning.
Extension bridge words MUST NOT accept.
Extension bridge words MUST NOT bypass effect gates.
Extension bridge words MUST NOT bypass platform permission.
```

---

## 12. Browser to ESP32 Example

### 12.1 Open Serial

```omi-lisp
<((effect . network)
  . ((adapter . webserial)
     . ((serial . open)
        . ((baud . 115200)
           . (target . esp32))))))>
```

Result:

```omi-lisp
((event . serial-opened) . serial-open-command-receipt)
```

### 12.2 Send ESP32 Command

```omi-lisp
<((effect . network)
  . ((adapter . webserial)
     . ((serial . write)
        . (data . "AT+CWMODE=1\r\n"))))>
```

Result:

```omi-lisp
((event . serial-written) . serial-write-command-receipt)
```

### 12.3 Receive ESP32 Response

```omi-lisp
<((effect . network)
  . ((adapter . webserial)
     . ((serial . read)
        . ((length . 256)
           . (timeout . 1000))))))>
```

Result:

```omi-lisp
((event . serial-read) . serial-read-command-receipt)
```

### 12.4 Project Response to DOM

```omi-lisp
<((effect . local-write)
  . ((adapter . dom)
     . ((dom . update)
        . ((selector . "#status")
           . (text . serial-read-receipt))))))>
```

---

## 13. ESP32 to Browser Semantic Sensor Example

### 13.1 Read Sensor

```omi-lisp
<((effect . hardware)
  . ((adapter . esp32)
     . ((i2c . read)
        . ((bus . 0)
           . ((addr . 0x40)
              . ((reg . 0x00)
                 . (len . 2)))))))>
```

### 13.2 Convert to Semantic Triple

```omi-lisp
<((effect . pure)
  . ((adapter . semantic)
     . ((semantic . triple)
        . ((subject . "temperature")
           . ((predicate . "value")
              . (object . "25.5C")))))))>
```

### 13.3 Project to DOM

```omi-lisp
<((effect . local-write)
  . ((adapter . dom)
     . ((dom . update)
        . ((selector . "#temperature")
           . (value . "25.5°C"))))))>
```

### 13.4 Render Canvas Gauge

```omi-lisp
<((effect . local-write)
  . ((adapter . canvas)
     . ((canvas . draw)
        . ((type . gauge)
           . ((value . 25.5)
              . ((min . 0)
                 . (max . 50)))))))>
```

---

## 14. Adapter Security Policy

Adapters MUST treat the following as sensitive:

```text
camera capture
microphone capture
clipboard read
file-system write
credential read
network send
package publish
deployment
hardware actuation
USB device control
Bluetooth control
HID control
MIDI SysEx
serial write
```

Sensitive operations require:

```text
declared effect
declared adapter
role permission
scope permission
accepted receipt
platform permission
result receipt
```

An adapter MUST NOT hide sensitive operations inside a pure or read-only declaration.

---

## 15. Adapter Result Policy

Every adapter side effect SHOULD produce a result declaration.

Examples:

```omi-lisp
((event . hardware-projected) . command-receipt)
((event . serial-opened) . command-receipt)
((event . serial-written) . command-receipt)
((event . dom-updated) . command-receipt)
((event . webgl-rendered) . command-receipt)
((event . media-streamed) . command-receipt)
((event . semantic-graph-built) . command-receipt)
```

Adapter results MUST NOT reuse the original command declaration as the result relation.

The command and the result are different facts.

---

## 16. Adapter Failure Policy

Adapter failure SHOULD produce a rejection or failure candidate.

Examples:

```omi-lisp
((event . adapter-failed) . command-receipt)
((failure.reason . permission-denied) . command-receipt)
((failure.reason . platform-unavailable) . command-receipt)
((failure.reason . validation-failed) . command-receipt)
```

Failures SHOULD be receipted when possible.

---

## 17. Complete Extended Invariant

```text
Recognition is not acceptance.
Citation is not acceptance.
Closure is not acceptance.
Projection is not acceptance.
Validation and receipt accept.

Serial is not acceptance.
USB is not acceptance.
Bluetooth is not acceptance.
HID is not acceptance.
MIDI is not acceptance.
GPIO is not acceptance.
I2C is not acceptance.
SPI is not acceptance.
WiFi is not acceptance.
DOM is not acceptance.
CSSOM is not acceptance.
JSDOM is not acceptance.
XML is not acceptance.
RDF is not acceptance.
OWL is not acceptance.
WordNet is not acceptance.
WebGL is not acceptance.
A semantic graph is not acceptance.

Hardware projection requires:
  - accepted receipt
  - declared effect
  - declared adapter
  - role/scope permission
  - bridge authorization
  - platform permission
  - result receipt

No serial byte, USB transfer, Bluetooth packet,
GPIO level, DOM node, CSS rule, JS handler,
Canvas command, XML element, RDF triple,
WordNet synset, semantic graph, WebGL buffer,
video frame, audio sample, texture, or polygon
may act as authority without validation and receipt.
```

---

## 18. One-Sentence Summary

`ADAPTERS.md` defines optional OMI adapter boundaries for hardware, browser APIs, DOM/CSSOM/JSDOM/Canvas/WebGL, semantic web technologies, and multimedia systems, while preserving the rule that adapters may carry, observe, and project, but only validation and receipts accept.

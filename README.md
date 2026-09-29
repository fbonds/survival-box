# Survival Box

An exploration of a rugged, self-contained, offline AI field reference designed for survival and off-grid use.

## Project status

**Concept / exploration phase**

This repository is intended to document the investigation rather than assume the final hardware or software architecture up front. The goal is to build a useful prototype incrementally, recording experiments, failures, measurements, and design decisions along the way.

---

## Vision

Build a ruggedized, portable device that can provide conversational access to a carefully curated body of survival and off-grid information without depending on an Internet connection.

The intended experience is something closer to a **field reference appliance** than a general-purpose computer:

- Primarily voice activated
- Conversational interaction with a local LLM
- Offline operation
- Local, curated survival/off-grid reference material
- Display of relevant diagrams, illustrations, tables, and other visual references
- USB-C connectivity for power and peripherals
- Battery operation or operation from essentially any suitable USB power source
- Rugged enough to be useful in a field environment
- Designed so that important information remains available when network infrastructure does not

A longer-term goal is:

> **A completely self-contained, battery-powered, offline conversational field reference that doesn't require an Internet connection to be useful.**

---

## An important architectural principle

The LLM should **not** be treated as the authoritative survival reference.

Instead, the project should separate:

1. **Authoritative local knowledge**
2. **Retrieval**
3. **LLM-based explanation and conversation**
4. **Voice input/output**
5. **Visual presentation**

The knowledge base should contain carefully selected and documented source material. The LLM should primarily provide a natural conversational interface to that information.

For example:

```text
User:
"How do I purify water if I don't have a filter?"

             |
             v
       Speech recognition
             |
             v
       Retrieval system
             |
       +-----+------+
       |            |
   Field manual   Local docs
   water section  water section
       |            |
       +-----+------+
             |
             v
          Local LLM
             |
       +-----+------+
       |            |
       v            v
   Spoken answer   Relevant
                  diagram
```

This approach should reduce the risk of relying on an LLM's unsupported recollection of critical information.

The system should also distinguish between:

- **Document-backed answer**
- **Model-generated explanation**
- **Insufficient information / no authoritative source found**

The ability to say *"I don't have sufficient information to answer that reliably"* is a feature, not a failure.

---

# Initial hardware direction

## Jetson Orin Nano 8GB

The **NVIDIA Jetson Orin Nano 8GB** is the initial candidate platform.

It is particularly interesting because it provides a small ARM-based computer with an NVIDIA GPU capable of local AI inference. This makes it a potentially better starting point than a conventional Raspberry Pi if the objective is a genuinely useful local AI appliance rather than merely demonstrating that an LLM can run on an SBC.

The Jetson should be evaluated rather than assumed to be the final platform.

### Things to investigate

- Local LLM inference performance
- GGUF / llama.cpp support
- CUDA acceleration
- Ollama support
- Model sizes that are practical
- Quantization levels
- Tokens/second
- Initial response latency
- Context-window performance
- Retrieval performance
- Speech-to-text performance
- Text-to-speech performance
- Combined end-to-end latency
- Power consumption under different workloads
- Thermal behavior
- Performance throttling
- Boot/recovery behavior
- Storage reliability
- Offline operation
- Peripheral compatibility

---

# Raspberry Pi and other SBC alternatives

The Raspberry Pi family remains relevant, particularly because of its ecosystem, power consumption, physical availability, and accessories.

A Raspberry Pi 5 can run small local language models, although CPU-only inference becomes increasingly impractical as model size increases.

The Raspberry Pi AI HAT+ 2 is also an interesting alternative because it provides dedicated AI acceleration intended to support local generative AI workloads.

Other platforms worth investigating include:

- NVIDIA Jetson family
- Raspberry Pi 5 + AI HAT+ 2
- Rockchip RK3588/RK3588S boards such as Orange Pi 5-class hardware
- Other ARM SBCs with usable NPUs
- Small x86 systems with integrated NPUs
- Low-power AMD systems
- Intel systems with usable integrated AI acceleration

The comparison should focus on **actual useful field performance**, not just advertised TOPS.

Important measurements should include:

```text
Model
Quantization
RAM
Accelerator
Tokens/sec
First-token latency
Power consumption
Idle power
Peak power
Thermal behavior
Physical size
Storage
Approximate cost
Software maturity
Offline capability
```

---

# Voice interface

The likely primary interaction model is voice.

The desired local pipeline is:

```text
microphone
    |
    v
wake word / activation
    |
    v
speech-to-text
    |
    v
retrieval + LLM
    |
    v
text-to-speech
    |
    v
speaker
```

The entire pipeline should ideally work without Internet access.

Questions to investigate:

- Wake-word detection
- Offline speech recognition
- Recognition accuracy in outdoor environments
- Microphone selection
- Wind/noise handling
- Push-to-talk fallback
- Offline text-to-speech
- Speaker selection
- Audio latency
- Power consumption
- Whether voice processing should run on the Jetson or a dedicated low-power component

A physical push-to-talk control may be valuable even if voice activation is the primary interface.

---

# Display

The device should provide a display rather than being exclusively voice based.

A potential target is a small:

- 5–7 inch display
- 720p or 1080p
- high-brightness / sunlight-readable
- touch-capable
- ruggedized panel

Physical buttons should also be considered so the device remains usable if:

- touch is unavailable
- the user is wearing gloves
- the display is damaged
- the environment is wet
- the user needs to operate it without looking closely at the screen

## Diagrams

The system should prefer **curated diagrams and illustrations** over asking the LLM to generate critical diagrams dynamically.

For example:

```text
Question
   |
   v
Retrieval
   |
   v
"Water purification / boiling"
   |
   +----> water-boiling-diagram.svg
   |
   v
LLM explanation
```

The local reference collection could eventually include:

- Water purification
- Shelter construction
- Fire building
- Knots
- First aid
- Food preservation
- Navigation
- Signaling
- Radio procedures
- Basic mechanical repairs
- Electrical fundamentals
- Off-grid power
- Gardening
- Food production
- Plant identification
- Weather
- Maps
- Tables and conversion references
- Other appropriate field-reference material

Critical diagrams should be traceable to their source.

---

# Power

The device should be fundamentally **USB-C powered**.

This provides flexibility for:

- USB-C PD wall chargers
- USB-C power banks
- USB-C solar power systems
- vehicle USB-C power
- laptops
- other USB-C PD sources
- field battery systems

An internal battery is desirable, but the first prototype should avoid unnecessary custom battery engineering.

A replaceable or serviceable battery/power module may ultimately be preferable to an integrated proprietary battery.

Power measurements should become a first-class part of the project.

At minimum:

```text
Idle
Display on
Listening
Speech recognition
Retrieval
LLM inference
Text-to-speech
Peak combined workload
Charging
```

should eventually be measured.

---

# Ruggedization

The final physical design should be considered only after the electronics/software requirements are better understood.

Potential requirements include:

- Shock resistance
- Dust resistance
- Water resistance
- Sunlight-readable display
- Passive or sealed cooling
- Replaceable storage
- Replaceable battery/power system
- Accessible USB-C ports
- Port covers
- Physical controls
- Field-serviceability
- Thermal management
- Glove-friendly operation

A good prototype does not need to be ruggedized immediately.

The project should first prove that the underlying system is useful.

---

# Software architecture

A possible eventual architecture:

```text
+--------------------------------------------------+
|                    User Interface                |
|                                                  |
|   Voice          Display          Physical I/O   |
+-----------------------+--------------------------+
                        |
                        v
+--------------------------------------------------+
|              Application / Orchestration        |
|                                                  |
|  Conversation / State / Tool selection / UI     |
+-----------------------+--------------------------+
                        |
             +----------+----------+
             |                     |
             v                     v
       Retrieval System         Voice System
             |                     |
             v                +----+----+
       Local Knowledge        |         |
       Base / Index         STT       TTS
             |
             v
       Local LLM Runtime
             |
             v
       Jetson GPU / AI
          acceleration
+--------------------------------------------------+
|              Local Storage / Data               |
|                                                  |
|  Models / Documents / Images / Index / Logs     |
+--------------------------------------------------+
```

This is deliberately a starting model rather than a final architecture.

---

# Knowledge base

The knowledge base is likely to become one of the most important parts of the project.

It should not simply be a collection of random PDFs downloaded from the Internet.

Each source should ideally have metadata such as:

```text
Title
Author / organization
Publication date
Version
Source URL
License / usage rights
Subject
Reliability notes
Relevant sections
```

The project should maintain a source catalog.

Potential source categories:

- Government field manuals
- Emergency preparedness documentation
- Wilderness references
- Search-and-rescue material
- First-aid references
- Navigation references
- Fire and shelter references
- Water and sanitation references
- Agriculture and food preservation
- Mechanical references
- Electrical references
- Radio communications
- Weather and meteorology
- Other public-domain or appropriately licensed reference material

Copyright and licensing should be considered from the beginning.

---

# Retrieval-Augmented Generation

A likely architecture is **RAG (Retrieval-Augmented Generation)**.

Instead of asking the LLM:

```text
"Tell me how to purify water."
```

the application should effectively ask:

```text
"What authoritative local documents contain information
relevant to this question?"
```

and then give the retrieved material to the LLM for explanation.

The application should preserve enough metadata to identify where the answer came from.

Ideally a response can expose something like:

```text
Source:
US Government Field Manual X
Section: Water Purification
Page: 42
```

This makes the system auditable.

---

# Experimental methodology

This repository should document **what actually happens**, not merely what is expected to happen.

An experiment should record enough information to reproduce it.

Example:

```text
Experiment 001
Date: 2026-09-29
Hardware: Jetson Orin Nano 8GB
Model: ...
Quantization: ...
Context: ...
Runtime: ...
Power: ...
Tokens/sec: ...
Prompt latency: ...
STT latency: ...
TTS latency: ...
Result: ...
Observations: ...
```

Failures are valuable data.

The project should explicitly record failed experiments rather than quietly removing them from the history.

---

# Initial experiments

The first experiments should probably be relatively small and independent.

## Experiment 1 — Can the Jetson run a useful local LLM?

Measure several small quantized models.

Questions:

- What models run?
- What model sizes are practical?
- What is the actual tokens/sec?
- How much RAM is consumed?
- What is the power draw?
- How does temperature behave?

## Experiment 2 — Local retrieval

Build a tiny reference collection and determine:

- Which document formats work best?
- Which embedding model is practical?
- What vector database/index should be used?
- How much storage is required?
- How accurately can relevant sections be retrieved?

## Experiment 3 — Grounded conversation

Test whether the LLM reliably answers questions from retrieved documents.

Include deliberately difficult questions where the correct behavior is:

> "I don't have enough information."

## Experiment 4 — Voice

Evaluate:

- Offline STT
- Wake word
- Microphone hardware
- TTS
- End-to-end latency

## Experiment 5 — Diagrams

Determine how retrieved visual references can be associated with text answers.

## Experiment 6 — Power

Measure actual energy consumption during realistic use.

## Experiment 7 — Offline operation

Disconnect the network completely.

Verify that:

- The device boots
- The UI works
- Voice works
- Retrieval works
- LLM inference works
- Diagrams work
- The system does not silently depend on external services

---

# Initial success criteria

The first prototype does **not** need to be rugged, waterproof, tiny, or beautiful.

A useful first milestone would be:

> A Jetson-based computer running entirely offline that can accept a spoken survival question, retrieve relevant material from a local knowledge base, generate a grounded conversational answer, speak the answer aloud, and display an associated diagram when one exists.

Once that works reliably, hardware refinement becomes worthwhile.

---

# Proposed repository structure

```text
survival-box/
|
+-- README.md
|
+-- docs/
|   +-- vision.md
|   +-- architecture.md
|   +-- hardware.md
|   +-- power.md
|   +-- offline-ai.md
|   +-- voice.md
|   +-- knowledge-base.md
|   +-- diagrams.md
|   +-- field-testing.md
|
+-- experiments/
|   +-- llm/
|   +-- speech/
|   +-- retrieval/
|   +-- power/
|   +-- hardware/
|
+-- knowledge/
|   +-- sources.md
|
+-- software/
|
+-- hardware/
|
+-- tests/
|
+-- logs/
```

This structure can change as the project evolves.

---

# Project philosophy

Several principles should guide the exploration:

### 1. Offline first

Internet access should be considered an optional convenience, not a dependency.

### 2. Evidence over model memory

The system should prefer documented local sources over unsupported LLM recollection.

### 3. Measure instead of assuming

Benchmarks, power measurements, thermal measurements, and field tests should drive decisions.

### 4. Fail visibly

An incorrect or unsupported answer is more important to discover than an impressive successful demo.

### 5. Keep the system understandable

The project should favor components that can be understood, repaired, replaced, or reproduced where practical.

### 6. Don't overbuild the first prototype

The first objective is to establish that the concept works.

### 7. Treat safety-critical information differently

Information involving medical care, fire, water, navigation, electrical systems, mechanical hazards, or other potentially dangerous activities deserves stronger sourcing and testing than ordinary informational content.

---

# Open questions

The project currently has many unanswered questions.

## Hardware

- Is Jetson Orin Nano 8GB the right platform?
- Which storage medium?
- How much storage is required?
- What display?
- What microphone?
- What speaker?
- What physical controls?
- What battery architecture?
- What USB-C PD requirements?
- What enclosure?
- What cooling system?

## AI

- Which LLM?
- What quantization?
- llama.cpp, Ollama, or another runtime?
- Which embedding model?
- Which vector store?
- Which STT system?
- Which TTS system?
- Can everything run concurrently?
- How much latency is acceptable?

## Knowledge

- Which reference sources?
- How should sources be validated?
- How should conflicting sources be represented?
- How should citations be presented?
- How should outdated information be handled?
- What content can legally be redistributed?

## User interface

- Always-listening wake word?
- Push-to-talk?
- Physical emergency/offline controls?
- Touchscreen?
- Physical buttons?
- How should uncertainty be presented?
- How should sources be displayed?
- How should diagrams be selected?

## Field use

- How rugged does it actually need to be?
- What environmental temperatures?
- How many hours of operation?
- What charging sources?
- What happens if storage becomes corrupted?
- Can the system recover without another computer?
- Can software updates be performed offline?

---

# Current direction

The current working hypothesis is:

**Jetson Orin Nano 8GB + local LLM + local RAG knowledge base + offline STT/TTS + small rugged display + USB-C power/peripherals + optional internal battery.**

This is a hypothesis to test, not a commitment.

The repository should evolve based on measured results.

---

# Project log

Experiments, decisions, and discoveries should be added here or in the appropriate documentation as the project progresses.

The objective is not simply to build a device.

The objective is to understand whether a device like this can be made **useful, trustworthy, maintainable, power-efficient, and genuinely independent of network infrastructure.**

# MOTIVATION

With multiple source and target systems needing to exchange data with each other, each pair of src/tgt with its own integration, becomes complicated. E.g., with 4 src and 6 tgt sys, need 24 integrations.

Integration comes with difficulties:
* Protocol - how data is transported (TCP, HTTP, FTP, etc.)
* Data format - how data is parsed (binary, CSV, JSON, etc.)
* Data schema & evolution - how it is shaped and may change

In addition, each integration increases connection load on src sys.

Kafka, a messaging system, sits in-between src and tgt sys and decouples data streams and systems. Src sys' data ends up in Kafka, tgt sys source data from Kafka.

Ex:

## What is Kafka

Open-source, distributed stream-processing platform, written in Scala and Java, aiming at high-throughput and low-latency for handling real-time data feeds. Connects to external systems (for data import/export) via **Kafka Connect** and provides **Kafka Streams**, a Java stream processing lib.

Uses binary TCP-based protocol optimized for efficiency and relies on "message set" abstraction that naturally groups messages together to reduce overhead of network roundtrip. Leads to larger network packets, larger sequential disk operations, contiguous memory blocks, and allows Kafka to turn bursty stream of random message writes into linear writes.

Other advantages include having a resilient architecture and being fault tolerant. Horizontally scalable. Performant.

Only functions as data transport (but moves them fast at scale), still need to write own apps.

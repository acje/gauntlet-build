---
title: "Pardosa"
description: "Event-driven data layer on NATS/Jetstream"
showDate: false
weight: 10
---

Event-driven data layer on NATS/Jetstream implementing ECST with migrations. Ordered event storage with fibers, lines, and migration support.

## Overview

Pardosa defines a structured approach to event storage using **fiber semantics** — a model for organizing ordered event streams into fibers (individual sequences) and lines (grouped sequences), with built-in migration support.

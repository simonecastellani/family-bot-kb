---
provenance: [sources/2026-11-13-primer-md-fonte-di-verita-del-progetto-family-bot]
status: active
timestamp: 2026-11-13
title: Panoramica del progetto
type: Overview
---
# Panoramica del progetto

## Obiettivo

Bot per l'organizzazione famigliare, interamente **serverless su Google Cloud**, senza server domestico (§1).

L'obiettivo primario del progetto è **fare pratica cloud-native** — serverless, servizi gestiti, IaC, CI/CD, osservabilità, sicurezza delle identità; l'affidabilità per la famiglia è importante ma secondaria (§1).

Interazione principale via **Telegram** (testo, vocali, link inoltrati, immagini), con integrazione a **Google Calendar**, promemoria push ai membri e una **dashboard web in sola lettura** (§1).

Lingua: messaggi del bot e interfaccia in **italiano**; codice, identificatori e commenti tecnici in inglese. Fuso orario di riferimento: `Europe/Rome` (§1).

## Use case

L'ordine di realizzazione è **1-2 → 3 → 4** (§2).

1. Scadenze programmate/ricorrenti (lettura gas, pagamento del nido).
2. Scadenze spot (chiamare qualcuno, scrivere un'email).
3. Menu settimanale in base a impegni di calendario e menu del nido.
4. Gestione della casa in base al carico dei membri.

L'use case 4 non si implementa nelle prime milestone (§2).

## Fuori scope

- Telegram Mini App
- editing dalla dashboard
- SPA
- multi-famiglia / multi-tenant
- server domestico
- use case 4 nelle prime milestone

(§15)

## Fonte

Fatti estratti da [[sources/2026-11-13-primer-md-fonte-di-verita-del-progetto-family-bot]], sezioni §1, §2 e §15.

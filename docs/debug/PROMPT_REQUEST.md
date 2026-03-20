# Prompt Request: Cross-Channel View-Once Media Support

> [!NOTE]
> **AI-Assisted Contribution**
> This change was developed and fully tested using the Antigravity AI coding agent.

## Goal

Enable full "View-Once" media support across WhatsApp and Signal. Currently, OpenClaw supports `viewOnce: true` in its internal types, but the parameter was being inconsistently handled or dropped at the plugin boundaries.

## Technical Rationale

1.  **WhatsApp Extension Gap**: The primary WhatsApp extension (`extensions/whatsapp/src/channel.ts`) was missing the destructuring of `viewOnce`. Added support for both `sendText` and `sendMedia`.
2.  **Signal Implementation**: Updated `src/channels/plugins/outbound/signal.ts` to correctly handle `viewOnce` for media payloads, providing feature parity across channels.
3.  **Pipeline Propagation**: `src/infra/outbound/deliver.ts` needed explicit handling to ensure `viewOnce` from the effective payload (whether from AI tools or `[[view_once]]` tags) correctly reaches the final adapter.
4.  **Protocol Reliability (WA)**: Added a hook in `src/web/session.ts` to automatically upgrade V1 view-once payloads to V2, ensuring reliability across modern clients and Baileys versions.

## Scope of Changes

- **Core**: `src/infra/outbound/deliver.ts` (Context resolution fixes)
- **WhatsApp**: `src/web/outbound.ts`, `src/web/inbound/send-api.ts`, `src/web/session.ts` (V2 Upgrade)
- **Signal**: `src/channels/plugins/outbound/signal.ts` (Explicit viewOnce handling)
- **Adapters**: `extensions/whatsapp/src/channel.ts`, `src/channels/plugins/outbound/whatsapp.ts`

## Verification (Fully Tested)

- **Local Tests**: `pnpm vitest src/web/outbound.test.ts` and Signal test suites pass.
- **Live Verification**: Verified in a production-ready environment; media sent with the flag/tag correctly arrives as a disappearing message on both platforms.
- **Architecture Integrity**: Confirmed via a clean build; no debug traces or infrastructure-specific logs remain.

## Prompt for Agent Merge

"Review the `feat/view-once-final` branch. It implements cross-channel View-Once support for WhatsApp and Signal by fixing parameter propagation in the adapters and adding a WhatsApp V2 protocol upgrade hook. Verify the core delivery pipeline changes and ensure feature parity across both channels. Confirm no infrastructure-specific leaks or debug traces are present."

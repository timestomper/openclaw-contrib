# Prompt Request: WhatsApp View-Once Support

> [!NOTE]
> **AI-Assisted Contribution**
> This change was developed and fully tested using the Antigravity AI coding agent.

## Goal

Enable "View-Once" media support across WhatsApp and Signal. Currently, OpenClaw supports `viewOnce: true` in its internal types, but the parameter was being dropped or under-utilized at the plugin boundaries.

## Technical Rationale

1.  **WhatsApp Extension Gap**: The primary WhatsApp extension (`extensions/whatsapp/src/channel.ts`) was missing the destructuring of `viewOnce`. Added support for both `sendText` and `sendMedia` (using standard options object pattern).
2.  **Signal Parity**: Updated `src/channels/plugins/outbound/signal.ts` to correctly handle `viewOnce` only for media payloads, reflecting Signal's protocol requirements.
3.  **Pipeline Propagation**: `src/infra/outbound/deliver.ts` needed explicit handling to ensure `viewOnce` from the effective payload (whether from AI tools or `[[view_once]]` tags) correctly reaches the adapter context.
4.  **Protocol Versioning (WA)**: Added a hook in `src/web/session.ts` to automatically upgrade V1 view-once payloads to V2, ensuring reliability across modern clients.

## Scope of Changes

- **Core**: `src/infra/outbound/deliver.ts` (Context resolution fixes)
- **WhatsApp Web**: `src/web/outbound.ts`, `src/web/inbound/send-api.ts`, `src/web/session.ts` (V2 Upgrade)
- **Signal**: `src/channels/plugins/outbound/signal.ts` (Explicit viewOnce handling)
- **Adapters**: `extensions/whatsapp/src/channel.ts`, `src/channels/plugins/outbound/whatsapp.ts`

## Verification (Fully Tested)

- **Local Tests**: `pnpm vitest src/web/outbound.test.ts` passes.
- **Live Verification**: Verified in a production-like environment; media sent with the flag/tag correctly arrives as a disappearing message.
- **Clean Environment**: Confirmed with a clean build and fresh process start; all debug traces were removed.

## Prompt for Agent Merge

"Review the current `feat/media-view-once` branch. It implements WhatsApp View-Once support by fixing a parameter drop in the extension adapter and adding a V2 protocol upgrade hook. Verify the core delivery pipeline changes and the Baileys message patching in `session.ts`. Ensure no debug traces remain."

# WhatsApp View-Once Handover

## Final Resolution (FIXED)

The issue was a **stale build** and **missing parameter propagation** in the primary WhatsApp extension (`extensions/whatsapp/src/channel.ts`).

### The Smoking Gun

1.  **Multiple Adapters:** OpenClaw has an internal adapter (`src/channels/plugins/outbound/whatsapp.ts`) and an extension-based one (`extensions/whatsapp/src/channel.ts`). The gateway on Dragonstone was using the extension, which I had not fixed yet.
2.  **Stale Processes:** Several stale `openclaw` processes from March 13th were still running on Dragonstone, holding the WhatsApp session and ignoring new builds.

### Changes Implemented

- **[FIXED]** `extensions/whatsapp/src/channel.ts`: Added `viewOnce` destructuring and propagation to `sendMessageWhatsApp`.
- **[CLEANED]** Dragonstone Environment: Purged all stale processes, deleted `dist/`, and performed a full `pnpm build`.
- **[RETAINED]** `src/web/session.ts`: Kept the V2 protocol upgrade hook to ensure maximum compatibility with modern WhatsApp clients.

### Readiness for Testing

The system is now fully synchronized and running the latest code. Both Signal and WhatsApp should now support `viewOnce` for media.

**Instructions for Mei:**

- Send an image through the usual tool or use the `[[view_once]]` tag in a message.
- Verify that it appears as a disappearing "View Once" message.

---

_Updated: 2026-03-20 19:35 (Final Resolution)_

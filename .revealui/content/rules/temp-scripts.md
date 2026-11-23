# Temporary Artifacts: Evidence and Source Retention

**Status:** convention.

## The rule

Register helper files intended for later use outside a session's private
scratch directory. Record their purpose and owner, preserve their source, and
track completion evidence. A temporary-looking path, age, completed task, or
unconfirmed record never grants authority to remove an artifact.

The RevealUI temporary-artifact registry records ownership, identity, and
evidence. It does not execute artifacts or stored validation commands, delete
source files, or prune history. Source code, data, directory trees, and unknown
artifacts remain with their owning tool. Private scratch follows that owner's
lifecycle; its location alone does not authorize cleanup.

## Workflow

1. **Register at creation.** Use `revealui-harnesses tmpscript register <path>
   --purpose <description>`. The typed API also records artifact class, owning
   tool/session, sensitivity, and consumers. Registration captures the file's
   identity and SHA-256. Unknown ownership, consumers, sensitivity, and leases
   require retention. Do not supply `--validate` or store executable validation.
2. **Verify the outcome through its owning workflow.** After authorized use,
   retain evidence of the observed result. The registry does not execute a
   verifier or independently establish that the business outcome succeeded.
3. **Record completion without removal.** Use `revealui-harnesses tmpscript
   confirm <id> --sha256 <digest> --evidence <outcome-reference>`. Confirmation
   binds the operator's attestation to the registered, unchanged artifact.
   Missing evidence, changed identity/content, and held legacy entries reject
   confirmation. Successful and repeated confirmation retain the source.
4. **Inspect without mutation.** `list`, `check`, `store`, and `sweep` are
   read-only. Sweep proposes owner review; it removes no files and prunes no
   records. Pending, held, and confirmed artifacts remain retained. Age only
   informs review priority.

## State and explicit migration

State lives at `$XDG_STATE_HOME/revealui/tmp-scripts/manifest.json`, defaulting
to `~/.local/state/revealui/tmp-scripts/manifest.json`. A relative XDG override
is rejected. Inspection of legacy data-directory or adapter manifests never
converts or moves them.

Import is a separate, explicitly authorized operation:
`revealui-harnesses tmpscript import <manifest-path>`. Original artifacts remain
intact, conflicting destinations fail, and in-place legacy conversion preserves
the original manifest bytes. Imported records are held for owner reconciliation
with unknown consumers and leases. Historical validation text remains private
history; it is neither printed nor executed. Import never authorizes retirement.

This policy requires the evidence-based registry contract above. An installed
CLI that executes validation, deletes on confirmation, or mutates on sweep must
be upgraded through its maintained release path before lifecycle use. A source
commit or policy update does not upgrade a runtime, import a live store, or
authorize an adapter rollout. Coordinate those changes separately.

## Preservation and retirement

The preservation API verifies a separate, byte-identical copy only for public
artifacts. Confidential and unknown artifacts remain with their existing
encrypted preservation owner. A verified copy is a point-in-time observation,
not removal authorization.

Retirement belongs to the owning custody/preservation tool and requires fresh
source identity, consumer, lease, and preservation checks plus a durable receipt.
This registry has no generic deletion API, recursive directory cleanup, receipt
pruning, or override flag. Directory artifacts require an owner with exact
inventory semantics. Keep source and receipts when any check is unresolved.

## Write failures and concurrency

Respect registry busy/conflict results. Reconcile the operation with its owning
process before retrying; never steal or remove a lock based on age or PID alone.
A write or durability failure is never successful completion or cleanup.

`TMPSCRIPT_PUBLISHED_DURABILITY_UNCONFIRMED` means a new revision is visible but
its durability was not confirmed. Preserve that outcome, including when lock
cleanup also fails. Reread the registry before deciding whether to retry; do not
treat it as rejection before publication or blindly replay the mutation.

## Enforcement

Session checks may use read-only inspection to surface pending and held work.
They must never import stores, execute validation text, confirm artifacts, or
remove files automatically. Report unresolved ownership and evidence without
exposing historical command text or private artifact contents.

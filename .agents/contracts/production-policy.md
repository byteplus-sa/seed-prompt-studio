# Production policy

## Stage evidence and authorization

Draft brief and scene breakdown can identify assets before canon exists. Before dependent prompt authoring, require the appropriate approved recurring/critical element descriptors and declared reference roles. Canon may be defined by prompt-only element sheets (Seedream prompts) or by acquiring and approving real brand/product/logo descriptors; prompt-only authoring may deliver a draft without generating or downloading assets. When the brief borrows visual or motion grammar from a cited brand ad or other footage, obtain a watchable copy first (the user's file, or a link the user can open) and analyze it by agent video pass or local frame extraction; do not treat scripts or article text as the reference.

The normal flow is brief → draft breakdown → required canon → optional storyboard prompts → optional requested lip-sync audio notes → video prompts → review → handoff. Handoff of the reviewed prompt package marks completion; do not infer completion from filenames.

## Prompt-package memory

Keep one record per authored prompt: the exact prompt text, its ordered reference list, and the parameter block. Save drafts under `projects/<project>/prompts/` only on explicit request; otherwise deliver in chat. Retain earlier revisions, rejected wording, and decisions while adding the current package.

Update the record after every material change to a brief, breakdown, element prompt, storyboard prompt, audio prompt, or video prompt. A handed-off prompt is frozen; changed inputs invalidate its review.

Defaults are proposed until the user accepts the displayed set. Store each axis with value, source (proposed/defaulted/user_confirmed), and approval evidence when available. Approval persists across turns within its stated scope. Authoring a prompt does not approve its result.

Only explicit user choice sets a selected variant or approves a package. Automated advice uses recommended_variant. Preserve earlier selections and user-written metadata when updating a bounded field.

## Directing guidance

Identify assets using [element-identification.md](element-identification.md). Copy locked identity descriptors faithfully into prompts where needed. Use positive, observable direction. Necessary technical exclusions may define a limited edit scope; negative-only prompt lists are discouraged rather than universally forbidden.

Narrative shots need events, intent, blocking and observable end states. Static character/prop sheets need clear composition and visible design; music/SFX/ambience need a sound arc appropriate to the requested artifact. Do not force story tactics into a static-image or sound-bed prompt.

Exact copy, typography, logos, screen/UI layouts, title cards, posters, product lineups, and price/CTA treatments are out of scope in this workspace: say so rather than improvising a prompt. Use Seedream prompts for invented photographic or illustrative imagery, expressive texture, and image synthesis.

Never ask the model to render overlay text such as captions, taglines, CTAs or end cards; keep generated footage text-free — on-screen text is added in post by the destination workflow, which is out of scope here. A transparent delivery graphic and a solid-background model reference are separate assets; never use a white matte as fake transparency.

Single-person references should preserve the intended identity and avoid cloning. Preserve approved visual descriptors and the face anchor; do not infer gender identity from appearance. Tool-based post-generation pixel cleanup is out of scope here — regenerate from a revised prompt instead. Image-editing prompts (I2I edit instructions) remain prompt deliverables.

An explicitly selected supported conditioning input is a promoted composition or motion reference, not a control-only asset. Record the selected source, its current hash when it is a local file, and the intended reference role. Changing a flag alone does not grant approval.

## Request preflight and review

Before handing off a **generation-bound** prompt, freeze the exact prompt beside its intended asset, list ordered reference bindings/roles, check reference approval, record current hashes for local reference files, and resolve current model/mode capabilities against live documentation. Run prompt-review for generation-bound prompts; CRITICAL/MAJOR findings must be resolved. Documentation or manifest-only edits do not trigger prompt review. A changed worked example is reviewed offline.

Acquired brand, logo, or packshot descriptors that will not be generated do not run prompt-review. They still require explicit user approval before dependent prompts depend on them. See [element-identification.md](element-identification.md).

Use explicit prompt type, model, operation, language, requested axes, may_change and must_preserve to route review. A completed review is bound to the request hash and lists applicable rule outcomes and evidence. Missing/empty reviewer output is incomplete. Static image, audio, editing and narrative checks are applied to their relevant artifact types.

The request hash covers exact prompt bytes, model/operation/effective parameters and ordered reference roles/bindings. Credentials and timestamps are excluded. Changed request content requires a new review.

## Handoff (no provider operations)

This workspace performs no provider submission: the user pastes prompts into the destination UI. There is no task registry, no provider status, and no cost record here. Provider-side outcomes the user reports back are diagnostic evidence for revision — rejection is evidence to diagnose, not a false positive by default, and reported success is review, never approval. Legitimate creative revisions stay within authorization and record the exact delta.

## Generation and review defaults

Prefer per-shot prompts at natural duration over compressed timelines. Continuous single-take extension is exceptional; flag every seam. Separate lip-sync audio remains opt-in; follow [audio-video-alignment.md](audio-video-alignment.md).

Use the lowest suitable resolution within the requested behavior. For Seedream image prompts, default to three sampling variants; keep prompt, references, model and effective parameters identical except supported stochastic seed differences, and say so in the package. Creative alternatives change only explicitly requested variables. Explicit requested count wins. Watermark handling is a parameter note for the destination UI, not something this workspace sets.

Verify the package before handoff: prompt text matches the review record, ordered references match the bindings, and the parameter block is complete and within documented limits.

## Reserved upstream rules

`canvas.stage_current` and `canvas.snapshot_current` are reserved for production workspaces with a canvas; this workspace intentionally omits one.

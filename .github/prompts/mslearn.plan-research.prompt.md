---
name: "Microsoft Learn Grounded Documentation Workflow"
description: "Use when planning, auditing, researching, and modernizing Microsoft Learn study notes or product documentation. Identifies retirements, alternatives, major product changes, and relevant current capabilities without image analysis."
argument-hint: "Target docs, product area, or modernization goal"
agent: "agent"
---

# Microsoft Learn documentation plan, audit, research, and modernization workflow

Use this workflow to update Microsoft Learn study notes with verified, current product guidance. The outcome is not limited to marking old content as historical or retired: it must also identify useful alternatives, modernization candidates, and material new capabilities for products, libraries, SDKs, APIs, tools, and services already discussed in the target documents.

Work within the scope supplied by the user. If no scope is given, default to `docs/**/*.md`; do not modify archived material, assets, Git staging, commits, or remote branches unless explicitly requested.

## Non-negotiable image policy

Documentation maintenance is text-first.

- Do **not** open, inspect, OCR, render, compare, hash, inventory, download, or semantically analyze images.
- Do **not** use image views, screenshots, image similarity checks, or visual comparison as part of product research or quality assessment.
- Do **not** move, delete, rename, or add image files unless the user explicitly asks for an image task.
- If a Markdown edit changes an image reference, only verify textually that the local path resolves. Do not inspect the image itself.

Image analysis is intentionally excluded because it is time- and cost-intensive and does not provide enough value for routine documentation modernization.

## Required study-note heading style

Apply this style to current notes under `docs/**/*.md`; do not change archived material unless the user explicitly requests it.

- Do not add YAML front matter or agent/skill-style metadata blocks to study notes.
- Do not use level-1 Markdown headings (`# Heading`). The first visible heading must be `## <Topic> Study Notes`.
- When a note includes a navigation list or Microsoft Learn resource list, label it `## Contents: <Topic>`. Do not use `Table of Contents`, `Table of contents`, or an unqualified `Contents` heading.
- Use `##` for major note sections and `###` for their direct subsections. Do not skip heading levels.
- Keep heading text concise and specific to the note. Update internal anchor links when heading text changes.
- During validation, confirm that every current study note begins with the `Study Notes` heading pattern and that all contents headings include the topic name.

## 1. Plan

1. Read the requested files and inspect the repository structure relevant to the task.
2. Check the working tree before editing. Preserve unrelated user changes and existing staging; never stage, commit, rebase, or push unless explicitly requested.
3. Define the exact scope: target files, product areas, historical material that must be preserved, and expected deliverables.
4. Classify likely work into:
	- terminology or branding changes;
	- service, API, SDK, or product-lifecycle changes;
	- current implementation or security guidance;
	- broken or obsolete Microsoft Learn links;
	- supported alternatives and migration paths for obsolete guidance;
	- major product, library, SDK, API, tool, or platform changes since the study notes were written;
	- new related products or capabilities that materially affect a reader's architecture, security, development, deployment, or operations decisions.
5. Ask a concise question only when the intended scope cannot be inferred safely. Do not expand a request about `docs/` into archive or asset maintenance.

For a broad documentation refresh, create a short coverage map before editing: product or tool mentioned, current status, modernization opportunity, evidence source, target document, and expected action. This prevents a retirement-only review and avoids adding unrelated product news.

## 2. Audit

Audit source text only. Search the requested Markdown files for outdated product names, lifecycle claims, SDK and API versions, retired services, deprecated patterns, old learning-module labels, and time-sensitive availability statements.

- Review headings, tables, code samples, links, and prose together; a modernization note at the top does not make conflicting historical instructions safe.
- Audit commands and code fences as executable guidance. Classify each potentially copyable sample as **current**, **historical**, or **conceptual**; replace production-oriented samples that use retired APIs, packages, actions, or templates with a verified stable pattern.
- Distinguish historical teaching content from current recommendations. Preserve useful 2023 material when possible, but label it clearly as **Historical** and add the current replacement or migration path.
- Prefer targeted findings over exhaustive unrelated cleanup. Avoid reformatting or rewriting unaffected prose.
- Record every factual claim that requires external verification before changing it.
- For every significant product, library, SDK, API, or tool named in the target document, ask two questions:
	1. Is the existing guidance still supported and accurate?
	2. What current alternative, successor, major capability, or development pattern should a reader know in order to make a sound choice today?
- Identify modernization candidates even when the existing product is not retired. Examples include a major API generation, a new supported SDK, a current deployment model, a security-default change, a platform consolidation, an actively maintained replacement sample, or a new workload that changes the recommended architecture.
- Do not turn the audit into a feature dump. A candidate is material only when it changes a decision, implementation approach, operational model, security posture, compatibility expectation, or migration plan for the topic already covered.

## 3. Research

Use current, authoritative sources before making product or lifecycle claims.

1. Prefer official Microsoft Learn documentation, official product lifecycle or retirement notices, release notes, supported SDK/API references, and official GitHub repositories for sample status.
2. Verify exact dates, replacement products, support status, regional limitations, migration requirements, and the practical effect of a major update. State a past date in the past tense.
3. Do not rely on 2023 training modules, old samples, cached knowledge, or third-party summaries as the authority for current guidance.
4. Use precise wording:
	- identify whether a product is renamed, deprecated, in phased retirement, retired, or unsupported;
	- name the supported replacement and its constraints, trade-offs, or scope;
	- identify a relevant new product or major capability only when it is materially connected to the existing topic;
	- distinguish general availability from preview, public preview, private preview, roadmap, and regional availability;
	- avoid claiming that preview features are production-ready;
	- avoid a product-wide claim when availability varies by region, model, SKU, API, or tenant setting.
5. For each modernization candidate, capture: the prior guidance or gap, the current option, who should consider it, who should not, required migration or adoption steps, and the official source URL.
6. Keep the source URLs that substantiate each material update so they can be included in the edited note or final summary.

## 4. Implement

Apply the smallest accurate change set that satisfies the task.

- Update current terminology, lifecycle dates, links, code samples, and security guidance in the requested files.
- Verify package, SDK, API, runtime, and GitHub Action version claims from authoritative sources before changing a sample. For immutable third-party GitHub Actions, use a full commit SHA with a same-line release comment when the repository's security guidance calls for pinning.
- For every material retirement, deprecation, or obsolete pattern, add a nearby supported alternative or migration path. Do not leave a reader with only a warning that something is obsolete.
- Add material modernization candidates that were absent from the historical notes when they improve a reader's current decision-making. Examples can include a successor platform, current API generation, supported SDK, architecture pattern, security-default approach, current developer tool, or actively maintained reference implementation.
- Explain why a new option matters and when to choose it. Do not add a product merely because it is new.
- Preserve historical content only when it remains useful for study. Mark obsolete material as **Historical** and place current guidance, alternatives, and constraints nearby.
- Update table-of-contents entries and internal links whenever a heading changes.
- Prefer Microsoft Entra ID, managed identities, least privilege, modern SDKs, current APIs, and supported deployment patterns when the official guidance recommends them.
- Keep Markdown style, indentation, tables, and document structure consistent with the surrounding file.
- Do not alter archives, binary files, image assets, or unrelated documents without explicit user direction.

## 5. Validate

After editing, validate the actual working tree rather than assuming an edit succeeded.

1. Check edited Markdown for whitespace and syntax problems.
2. Check edited code fences for an appropriate language label, coherent syntax, and consistency with the documented stable API or version.
3. Validate local Markdown links and image paths textually; do not inspect the image content.
4. Run available diagnostics for the modified files or documentation folder.
5. Review the diff for accidental scope expansion, obsolete claims left beside new guidance, retirement notices without alternatives, missing constraints on new capabilities, and unintended formatting churn.
6. Confirm that no unrelated files were staged, committed, or changed by the workflow.

## 6. Review, learn, and iterate

Treat documentation modernization as a bounded feedback loop, not a one-pass replacement exercise:

1. Compare the completed edits against the coverage map and audit findings.
2. Re-audit the updated target files for unresolved historical guidance, unsupported claims, missing alternatives, obsolete examples, and material current capabilities that were not addressed.
3. Review the workflow itself for a process gap exposed by the pass. Add a concise reusable instruction only when it would prevent a repeatable mistake; do not add rules for one-off details.
4. Research and implement the next targeted pass when the re-audit finds a material, source-backed gap.
5. Repeat until each in-scope product or tool has one of these dispositions: updated, intentionally retained as historical with nearby current guidance, explicitly excluded as immaterial, or blocked by a documented verification gap.

For every audit candidate, record a disposition before implementation: **update**, **already covered**, **out of scope**, **immaterial**, or **unverified**. Do not copy an audit recommendation into a document until the current source text and an authoritative source support the claim. This prevents duplicated guidance, scope drift, and inaccurate lifecycle or preview statements.

Stop when the coverage map has no unresolved material entries, validation passes, and a final independent review finds no actionable current Microsoft Learn change. Do not continue merely to add more features or make the notes longer.

## 7. Report

Conclude with a concise factual summary:

- files updated;
- key product, lifecycle, security, implementation, and modernization changes;
- alternatives, successors, and major new capabilities added or deliberately excluded, with a brief reason;
- authoritative sources consulted when material claims changed;
- validation completed;
- any unresolved issue, uncertainty, or decision left for the user.

Do not claim that a file was updated, a link was fixed, or a validation passed unless the final file content and validation results confirm it.

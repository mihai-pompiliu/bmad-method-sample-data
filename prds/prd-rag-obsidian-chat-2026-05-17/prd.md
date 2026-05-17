# VaultChat: Chat With Your Obsidian Vault

**Status:** Draft v0.1
**Date:** 2026-05-17
**Author:** Community plugin (single-author, hobbyist build)
**Distribution:** Obsidian Community Plugins directory
**License:** MIT

## Problem

Most note-takers cannot find what they wrote.

After two or three years of daily notes, the vault becomes a write-only archive. You know past-you wrote something useful about that hiring loop change, or that retro, or that book chapter, but full-text search returns 47 hits and none of them are the one. Tags rot. Folder hierarchies stop matching how you actually think. The Linking Your Thinking discipline is great in theory and unevenly applied in practice.

The vault is a personal corpus. Personal corpora are exactly what retrieval-augmented generation is good at. The gap is that no Obsidian-native, privacy-respecting, citation-honest RAG product exists yet. The commercial options either require uploading your vault to someone else's server or are wrapped around a single proprietary model.

VaultChat is the plugin that closes that gap.

## Target User

The Obsidian power user with a mature vault.

Concrete profile:
- 3,000+ notes accumulated over 2+ years
- Daily-note habit, some PARA or LYT structure, imperfect tagging
- Has tried Smart Connections, Copilot for Obsidian, or one of the LLM plugins, and bounced off because of cost, privacy, or citation quality
- Comfortable pasting an API key into a settings panel
- Listens to Hot Take Hour, follows Bryan Jenks, knows what RRF means or is willing to look it up

This is not the user who just installed Obsidian last week. v1 does not optimize for onboarding a brand-new vault.

## Capability Surface (Functional Requirements)

FR-1. Chat panel rendered in the Obsidian right sidebar. Supports one-shot Q&A and continuing multi-turn conversations within a session.

FR-2. Every response includes inline citations. Each cited claim links to the source chunk using Obsidian wiki-link syntax (`[[Note Name#Section]]`). Clicking a citation opens that note at the cited location.

FR-3. Incremental re-indexing on file save. When the user modifies a note, only that note's chunks are re-embedded. Full re-index runs once on first install, with a visible progress indicator.

FR-4. "Find similar notes to this one" command, exposed via the Obsidian command palette. Operates on the currently open note. Returns top-10 semantically similar notes.

FR-5. Conversation export. Any chat session can be saved back into the vault as a regular markdown note, with citations preserved as live wiki-links.

FR-6. Multi-vault support. Each Obsidian vault maintains its own index. Switching vaults switches the active index automatically. No cross-vault retrieval in v1.

## Retrieval Architecture

This is the load-bearing part of the product. Retrieval quality is the difference between "useful" and "uninstalled within a week."

**Embedding model (cloud default):** Voyage AI's `voyage-3`. As of this writing, it is the best per-dollar option for English non-code retrieval. The plugin sends chunk text to Voyage's API; the user supplies their own Voyage API key.

**Embedding model (local-first mode):** `nomic-embed-text` or `mxbai-embed-large` served via a local Ollama instance. The user toggles this in settings. Quality is meaningfully lower than `voyage-3` but the tradeoff is full local privacy.

**Chunking strategy:** Smart-paragraph chunking, 512-token target with 64-token overlap. Chunks respect note-section boundaries (no chunk crosses an H1 or H2). Frontmatter (title, tags, aliases, created date) is prepended to every chunk as context so the embedding model knows what document the chunk belongs to. This single decision is the largest quality lever in the system.

**Hybrid retrieval:** Every query runs two retrievers in parallel.
- BM25 keyword retrieval over the same chunks, using a local sqlite-fts5 index.
- Dense embedding retrieval, cosine similarity over the embedding store.

Results are fused with Reciprocal Rank Fusion (RRF, k=60). Top-30 chunks from the fused list move to reranking.

**Reranking:** Top-30 chunks pass to Voyage's `rerank-2.5` (or a local cross-encoder, `bge-reranker-v2-m3`, in local-first mode). Top-8 reranked chunks are passed to the generation step.

**Why this stack and not GraphRAG:** Entity extraction over personal notes is noisy. Personal notes contain idiosyncratic shorthand, mixed entity references, and incomplete thoughts. The entity graph that GraphRAG would extract is full of false-positive nodes and unstable edges. Flat hybrid retrieval with a strong reranker beats it on personal corpora at the cost of giving up multi-hop reasoning. v1 ships flat. GraphRAG is on the v2 evaluation list, not the v1 plan.

## Generation Architecture

**Default model:** Claude Sonnet 4.6, called directly against Anthropic's API. User supplies their own Anthropic API key. No proxy server. No middleman. The plugin holds the key in Obsidian's local settings storage.

**Model-agnostic backend:** The generation layer is a thin interface. Users can configure an alternate endpoint (OpenAI-compatible chat completions, or a local Ollama model). Claude is the default because of its instruction-following and long-context behavior on retrieval-grounded synthesis tasks.

**Prompt shape:** A retrieval-grounded synthesis prompt. The prompt instructs the model to ground every claim in the retrieved chunks, to cite chunks inline using a stable chunk-id syntax, and to refuse to answer when retrieval is empty rather than hallucinating from training data. The plugin post-processes the chunk-id citations into Obsidian wiki-links.

**Streaming:** Responses stream into the chat panel. No buffering the full response before render.

## Privacy Posture

This is load-bearing for the Obsidian audience. Obsidian users chose Obsidian partly because their notes live on their own disk. VaultChat respects that contract or it does not ship.

The commitments:

1. **Local-first mode is a real option, not a checkbox.** Users can run the entire pipeline (embeddings, retrieval, generation) against local Ollama models. Quality degrades. Privacy is total. The toggle is one click in settings.

2. **Cloud mode is opt-in and key-gated.** No network call ever leaves the plugin unless the user has explicitly entered a Voyage and/or Anthropic API key. When they enter a key, their chunk text and their queries flow to those services under those services' terms. The plugin shows a one-time consent dialog that says this in plain English.

3. **Zero plugin-side telemetry.** No analytics. No crash reporters. No "anonymized usage data." The plugin does not phone home, ever.

4. **The index lives in the vault.** Embeddings and the BM25 index are stored under `.vaultchat/` inside the vault root. The plugin auto-suggests adding `.vaultchat/` to the vault's `.gitignore` on first install.

5. **No training.** The plugin does not train a model on the user's notes. It does not fine-tune. It does not contribute to any shared corpus. Retrieval at query time only.

## Index Sync Model

- **First install:** Full vault scan. Chunks every note. Embeds every chunk. Progress indicator in the status bar. Pauseable. Resumable across plugin restarts.
- **Steady state:** On every note save (Obsidian `vault.on("modify")` event), the modified note is re-chunked. Old chunks for that note are deleted. New chunks are embedded and inserted. Debounced at 2 seconds so rapid edits do not flood the embedding API.
- **File rename:** Update chunk metadata in place. No re-embedding needed (content unchanged).
- **File delete:** Remove all chunks for that file.
- **Manual rebuild:** Settings panel exposes "Rebuild index from scratch" for the case where the user changes embedding models and needs to re-embed everything against the new model.

## Constraints

- Must comply with Obsidian's plugin API rules. No network calls except to user-configured endpoints with user-provided keys.
- Must run on Obsidian mobile (iOS + Android) at least in retrieval-read mode. Mobile generation may require cloud since local model serving is impractical on phones in 2026.
- Index size budget: under 200MB for a 5,000-note vault. This shapes the embedding-dimension choice and the BM25 storage format.
- Cold-start re-index of a 5,000-note vault must complete in under 30 minutes on a typical laptop with a working Voyage API key.
- Plugin bundle size under 5MB (Obsidian community plugin guideline).

## Non-Goals

- Not a full Obsidian alternative. This is a plugin, not a competing app.
- Not an auto-writer. VaultChat does not generate new notes for you. It retrieves and synthesizes from what you already wrote. The deliberate scope is retrieval, not authoring.
- Not a multi-user shared-vault product. Single-user, single-vault-at-a-time, in v1.
- Not training a model on the user's notes. See privacy posture.
- Not a GraphRAG implementation in v1. Flat hybrid retrieval ships first.
- Not a Logseq plugin, a Notion integration, or a Bear sync. Obsidian only in v1.

## Success Signal

Primary signal: the plugin survives the Hot Take Hour review and the r/ObsidianMD weekly thread without getting flamed for privacy violations or hallucination.

Concrete metrics, self-reported by users who opt into a one-time survey after 30 days of use:
- 60% report that VaultChat surfaced a note they had forgotten about, at least once.
- 80% report that the citations were accurate (the cited note actually contained the claim).
- Under 10% report any hallucination case where the model cited content that did not exist in the retrieved chunks.
- Net Promoter Score among Obsidian power users (5,000+ notes) above 40.

Distribution success: 5,000 active installs within 90 days of community-plugin listing approval.

## Open Questions

- Should the chat panel support exporting a conversation directly into a daily note, or only as a standalone note? (Daily-note workflow is widespread but opinionated.)
- For local-first mode, which embedding model is the right default? `nomic-embed-text` is smaller and faster; `mxbai-embed-large` is more accurate. Ship with which one.
- How aggressively should the plugin warn users about cost on cloud mode? A 10,000-note vault re-indexed against `voyage-3` is a real-money operation. A clear pre-flight cost estimate before first index might be table stakes.
- Conversation memory across sessions: do we persist? If yes, where does it live in the vault, and how does it interact with the index?
- Should "Find similar notes" results also feed into the chat panel as additional context, or stay as a separate command output?
- Mobile retrieval-only mode: is the UX acceptable if generation requires cloud while retrieval runs locally, or does the split confuse users?
- Encrypted vaults: a meaningful subset of Obsidian users encrypt their vault at rest (with plugins like Meld Encrypt or with full-disk encryption). Does the `.vaultchat/` index need its own encryption-at-rest story, or does the user's existing setup cover it?
- Backlink awareness: should retrieval surface the inbound-link graph as a re-ranking signal (a note linked from 30 places is probably load-bearing)? This is cheap to add and might meaningfully improve quality.
- Frontmatter as a hard filter: should users be able to scope a query to notes matching specific frontmatter (e.g., `area: work`)? Useful for users with disciplined metadata, noise for users without it.

## Out-of-Scope Items For Future Versions

The following are explicit non-v1 items kept here so the v1 scope stays honest:

- GraphRAG variant for vaults with disciplined entity discipline (LYT-strict users)
- Logseq, Notion, Bear, and plaintext-folder support behind a common adapter interface
- Shared-vault collaborative retrieval (multiple users, one vault)
- Auto-suggested links during note authoring (an authoring-time product, not a retrieval product)
- Voice query input from the mobile app
- A web-app companion that reads the same `.vaultchat/` index format

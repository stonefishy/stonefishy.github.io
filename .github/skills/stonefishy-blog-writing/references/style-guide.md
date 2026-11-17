# Blog writing style

## Reference set

These are the ten most recent posts by front matter `date` when this guide was
created, spanning September 12, 2025 through January 18, 2026. Treat this as a
style baseline, not a source of current technical truth. If asked to refresh
the style, reselect posts by publication date rather than filesystem timestamps.

Paths below are relative to this reference file.

| Date | Source post | Useful style evidence |
| --- | --- | --- |
| 2026-01-18 | [International LLM comparison](../../../../source/_posts/2026-01-18-A-Comparative-Analysis-of-International-AI-Large-Language-Models.md) | Parallel per-model sections, strengths/limitations, and a final decision table. Use the comparative organization, not unverified model statistics. |
| 2025-12-30 | [Azure Policy compliance](../../../../source/_posts/2025-12-30-Using-Azure-Policy-Evaluate-the-Resource-Compliance.md) | An operational question leads into plain-language building blocks, a Portal walkthrough, CLI examples, and evaluation caveats. |
| 2025-12-06 | [Java 21 features](../../../../source/_posts/2025-12-06-Exploring-Java-21-New-Features.md) | A scoped feature list followed by small code examples and explanations of the syntax and practical benefit. |
| 2025-11-18 | [Airbyte integration](../../../../source/_posts/2025-11-18-A-Universe-Data-Integration-Tool-Airbyte.md) | Tool introduction, local setup, a concrete source-to-destination workflow, and screenshots that show the result. |
| 2025-11-05 | [PDF to Markdown methods](../../../../source/_posts/2025-11-05-Several-Ways-to-Extracting-PDF-content-to-Markdown-for-LLM-RAG.md) | Tool-by-tool features, installation, minimal usage, and links to upstream documentation. |
| 2025-10-20 | [Git large-file error](../../../../source/_posts/2025-10-20-Handling-Large-Files-in-Git-Solving-the-File-Size-Limit-Error.md) | Exact error first, explanation of the cause, a fix and an alternative, then prevention and warnings. |
| 2025-09-30 | [Playwright MCP testing](../../../../source/_posts/2025-09-30-automating-testing-with-playwright-MCP-ai-browser-control.md) | Concept and architecture before configuration; demos pair natural-language scenarios with results and screenshots. |
| 2025-09-28 | [Jira developer agent](../../../../source/_posts/2025-09-28-A-jira-ticket-developer-Agent-base-on-Github-Copilot-Custom-Chat-Mode.md) | Motivation, explained workflow stages, configuration snippets, and screenshots beside the relevant stages. |
| 2025-09-25 | [Simulator MCP server](../../../../source/_posts/2025-09-25-Building-an-MCP-Server-for-Automated-Diagnosis-Simulator-Management.md) | A concrete operational problem leads to architecture, implementation pieces, running instructions, and client demos. |
| 2025-09-12 | [Jira-assisted coding](../../../../source/_posts/2025-09-12-Auto-Coding-by-follow-Jira-ticket-requirements.md) | A short motivation, essential configuration, then a prompt-to-result walkthrough with annotated screenshots. |

## Voice and pacing

- Address a developer as a peer: explain what a tool does, why it is useful,
  and how to try it. Expand unfamiliar acronyms when first needed.
- Prefer short paragraphs around substantive examples. Use lists for features,
  prerequisites, or takeaways; use prose to explain relationships and trade-offs.
- Use natural transitions from the problem to the example and from each action
  to its result. The Azure and Java posts provide especially clear examples.
- Use backticks for identifiers and commands, and bold sparingly for key terms
  or labeled points. Treat occasional emoji and theme callouts as optional,
  not a defining house style.
- Personal discovery and team scenarios appear in several posts. Reproduce
  that perspective only with supplied facts; otherwise address the reader or
  describe an explicitly hypothetical scenario.

## Structures to borrow, not templates to enforce

| Article type | Natural progression | Start with these references |
| --- | --- | --- |
| Hands-on tutorial | Problem and scope; essential concepts/prerequisites; setup; one end-to-end example; result and caveats; summary. | Azure Policy, Airbyte, simulator MCP |
| Feature tour | Why the release matters; selected features; a small example and interpretation per feature; adoption takeaways. | Java 21 |
| Tool comparison | Reader's decision; consistent criteria; per-option strengths, limitations, and usage where relevant; recommendation or table. | LLM comparison, PDF conversion |
| Troubleshooting | Symptom/error; cause; appropriate fix; alternatives and risks; prevention. | Git large-file error |
| AI workflow demonstration | Repetitive task; components and configuration; example prompt; explained actions and observed result; limitations. | Playwright MCP, Jira agent, Jira-assisted coding |

Number headings when the sequence or feature list benefits; use descriptive,
unnumbered headings otherwise. There is no common mandatory word count or
section count: the Git fix is much shorter than the MCP implementation post.

## Examples and visual evidence

Place a short explanation before code or configuration, then explain important
fields, expected behavior, or a verification step afterward. Give enough context
to adapt the example without copying an entire unrelated implementation.

Use screenshots for meaningful UI transitions or results, not decoration.
The Airbyte and Azure posts show this pattern well. Explain what the reader
should notice instead of relying on the image alone.

Close with the useful takeaway. A compact list works for a fix or feature tour;
a decision table helps a comparison. Avoid adding several repetitive conclusion
sections merely because some longer posts have them.

## Style evidence is not a correctness rule

The sample posts vary in grammar, heading depth, code-fence labels, metadata,
and promotional tone. Preserve the practical, approachable core while writing
clean English. Follow the shared agent guide where older formatting differs,
including its excerpt marker, required metadata, and image conventions.

Treat historical prices, benchmarks, product capabilities, preview features,
and configuration formats as claims to recheck. Do not copy questionable code,
insecure defaults, unsupported benefits, private-service details, or claimed
measurements into a new post just to imitate an example. This guide records
writing patterns; it does not authorize the operations described in those posts.

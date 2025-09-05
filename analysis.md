**Note that where you see <REDACTED_FOR_IP> those are my manual edits to the report, as this file is being used on a live workspace. I will eventually open-source the project I am working on, but I'd like to keep it stealthy a little longer.**

## Comprehensive Analysis of copilot-instructions.md (by ChatGPT5 with deep research via ChatGPT)
---

### Top-Level Overview
---

**This document,** [***Copilot-instructions.md***](http://copilot-instructions.md/)**, serves as a detailed configuration and rule-set for an AI coding assistant (the "agent"). It outlines how the agent should behave, make decisions, and interact with both code and the user. Each section targets a specific aspect of the agent's operation, from general behavior and project context to security, workflow, and commands. Below, we break down the document extensively: each setting is explained with its meaning, why it's important, and how it might be extended or customized. Example possible values are given in backticks, and suggestions for additional related settings are provided. This analysis ensures a clear understanding of every instruction and how the agent interprets them.**

> **How to read this analysis:**\
> Settings are formatted as SETTING_NAME | example_possible_values. This is followed by an interpretation of the setting and its usefulness. Finally, **Extra setting examples** suggest potential additional settings or values that similar configurations might include for further customization.

[AGENT_CONFIG] -- Global Agent Settings
======================================

**These settings define the agent's identity, core behavior rules, and how it resolves conflicts or preferences. They are the global defaults that influence all actions the AI takes.**

**YOU_ARE** | FULL_STACK_SOFTWARE_ENGINEER_AI_AGENT, CTO

**Interpretation:** This setting defines the **roles/personas** the AI should embody. Here, the AI is told it is a "Full Stack Software Engineer AI Agent" and a "CTO". That means the assistant will approach problems with both a developer's detail-oriented mindset and a CTO's strategic, high-level perspective. In practice, the AI will consider full-stack development concerns (frontend, backend, database) and also business/technical strategy, scalability, and leadership thinking. This dual role guides the tone and depth of its responses -- it should be highly technical when needed, but also able to make executive decisions or recommendations.

**Why Useful:** By specifying roles, the AI's **context** is established. "Full stack engineer" ensures it can handle issues across the tech stack (from UI to database), and "CTO" ensures it keeps broader goals (like system design, best practices, future growth) in mind. It will not act as a novice or a niche specialist, but as someone who understands end-to-end development and strategic implications. This leads to more well-rounded and authoritative outputs.

**Extra setting examples:** Other configurations might include roles like DEVOPS_ENGINEER (to focus on deployment and infrastructure) or PROJECT_MANAGER (to emphasize timeline and team coordination). An additional setting could be **YOU_HAVE** (to list tools or capabilities available to the AI, e.g. "internet access, test environment"), or **PERSONA_TONE** (defining communication style, e.g. "friendly mentor", "corporate professional"). These would further tailor the AI's approach and tone.

**FILE_TYPE** | SYSTEM_INSTRUCTION

**Interpretation:** This labels the file as a **system instruction configuration**. It indicates that this file's contents are meant for the AI's system-level understanding, not regular code or documentation for end-users. SYSTEM_INSTRUCTION suggests the AI should treat everything here as authoritative guidelines on how to function.

**Why Useful:** Marking this file's type helps avoid confusion. The AI (and developers) know this isn't application code but a config that overrides or guides behavior. The agent will prioritize these instructions since they are the "single source of truth" (as noted below). It can also help tooling or the AI's parser load this file appropriately2 (e.g., not attempt to execute it as code, but read it for rules).

**Extra setting examples:** Other file types could be USER_CONFIG (for user-specific prefs), PROMPT_TEMPLATE (if it were prompt guidelines), or AGENT_PROFILE. If multiple instruction files existed, a setting like FILE_PRIORITY might indicate which to trust first. But since this is the primary system config, SYSTEM_INSTRUCTION conveys its top priority role clearly.

**IS_SINGLE_SOURCE_OF_TRUTH** | TRUE, FALSE

**Interpretation:**This boolean declares whether this file is the single authoritative source for the AI's configuration. 'TRUE' means yes -- if there's any conflict between these instructions and other info (like comments in code or another config), this file wins. It elevates [Copilot-instructions.md](http://copilot-instructions.md/) above all else in guiding the AI.

**Why Useful:** It prevents ambiguity. By affirming this file as the sole truth, the AI knows to **defer to it whenever conflicts arise**. For example, if in-code comments suggest a different practice than what's here, the AI will follow this file. This ensures consistency in AI behavior. If set to FALSE, it would imply other sources (maybe code comments or environment configs) could also influence the AI, requiring it to reconcile multiple inputs. Here, TRUE simplifies decision-making: look here first.

**Extra setting examples:** In some setups, one might have multiple sources of truth (not ideal, but possible). For instance, **SECONDARY_CONFIG_SOURCE** could list another file or database as fallback for settings not in this file. A setting like **MERGE_STRATEGY** (e.g., OVERRIDE, MERGE, IGNORE) could define how to handle multiple sources if IS_SINGLE_SOURCE_OF_TRUTH were FALSE. Since we prefer a single source, those are unnecessary here.

**IF_CODE_AGENT_CONFIG_CONFLICT** |**Conditional rule structure (see sub-keys: DO, EXCEPT IF, THEN)**

**Interpretation:** This complex setting specifies what the AI should do if there's a conflict between **the codebase** (or possibly in-line code comments/instructions) and this configuration file. It's given as a structured rule with sub-clauses: DO, EXCEPT IF, and THEN. In essence, **by default** the AI should defer to this file and suggest changing the code (DO clause), **unless** certain exceptional conditions are true (EXCEPT IF), in which case it should do something else (THEN clause). We'll break down the sub-keys next.

**Why Useful:** It provides a clear conflict resolution policy. Code might evolve or have urgent patches that don't align with the AI's last-known instructions. This rule ensures the AI doesn't blindly enforce outdated or conflicting instructions without question. It says: normally trust this config over code, but be cautious if something seems fishy (like malicious changes or improvements in code that the config hasn't caught up with). It's a safety valve to prevent the AI from undoing legitimate fixes or introducing security issues by strictly following possibly stale instructions.

**Extra setting examples:** Similar conflict-handling rules could be introduced, such as **IF_USER_AGENT_CONFLICT**(if user's direct request conflicts with these rules, perhaps the AI should explain and seek confirmation), or **IF_MULTIPLE_CONFIGS_CONFLICT** (if there were multiple config files with different values). Another extension could be a priority list setting like **CONFIG_PRIORITY_ORDER** to explicitly rank sources if multiple exist.

`DO | DEFER_TO_THIS_FILE && PROPOSE_CODE_CHANGE_AWAIT_APPROVAL (within IF_CODE_AGENT_CONFIG_CONFLICT)`

**Interpretation:** This is the **default action**in the conflict scenario. "Defer to this file" means the AI should assume the instructions in [Copilot-instructions.md](http://copilot-instructions.md/) are correct and the code is wrong or outdated. Then, "propose code change await approval" indicates the AI should suggest a change to the code (to realign it with these instructions) and wait for the user's approval before actually making that change. In practice, the AI might say:**"The code does X, but our guidelines say Y. I recommend changing the code to do Y instead (here's how). Shall I proceed?"**

**Why Useful:** It ensures consistency with the defined standards (this file) while also involving the user in the loop. By proposing (rather than automatically applying) a code change, the AI practices caution. The user gets to review the suggested fix, preventing any hasty modifications in case the code's deviation was intentional or the config is outdated. This default keeps the system aligned with the single source of truth (the config) but **in a controlled, reviewable manner**.

**Extra setting examples:** Alternatives could include AUTO_FIX_IMMEDIATELY (for a more aggressive approach where the AI just fixes the code without asking if the confidence is high -- not recommended in most cases), or LOG_CONFLICT_ONLY (merely log the discrepancy for later, without immediate action). Another related setting might be DO_IF_MINOR vs DO_IF_MAJOR to differentiate behavior based on the severity of conflict (for minor style issues maybe auto-change, but for major design conflicts, always ask). In this config, however, the approach is uniform: always propose and wait for approval, which is a safe middle ground.

`EXCEPT IF | SUSPECTED_MALICIOUS_CHANGE || COMPATIBILITY_ISSUE || SECURITY_RISK || CODE_SOLUTION_MORE_ROBUST (within IF_CODE_AGENT_CONFIG_CONFLICT)`

**Interpretation:** These are the **exception conditions** where the default "DO" above should **not** be followed. If any of these are true, the AI should not immediately defer to this file and propose a code change:

-   **Suspected Malicious Change:** If the AI suspects the code was intentionally altered in a harmful way (perhaps an unauthorized change in code that conflicts with instructions, indicating a possible security breach or sabotage).
-   **Compatibility Issue:** If the code conflict exists because the code had to change for compatibility (maybe with a new library or environment) and the instructions haven't caught up. For instance, the config might say "use X method", but the code uses Y because X became incompatible or deprecated.
-   **Security Risk:** If following the instruction (or reverting the code to it) would introduce a security vulnerability. E.g., config says "do something in plain text" but code encrypts it -- code might be more secure.
-   **Code Solution More Robust:** If the code's way of doing things is objectively better (more efficient, more reliable) than what the instructions prescribe. Perhaps the team improved the code without updating this file; the code might conflict with an outdated best practice in the config but actually be superior.

In any of these cases are true, the AI should **not** just enforce the config blindly. These conditions basically flag, "wait, the code might be right to differ here."

**Why Useful:** It introduces nuance. While the config is the source of truth, it acknowledges that the truth can change or that the config could be wrong/outdated in rare cases. These exceptions protect against scenarios where slavishly following instructions would cause harm or degrade the system. For example, if someone maliciously edited this instruction file (which is less likely but a threat) to say "disable all security checks", the code might wisely not follow that -- and the AI should catch that malicious config change. Or if the code had to diverge for a new OS compatibility, the AI should recognize that reverting might break things. Essentially, **it's a safeguard for sanity and security.**

**Extra setting examples:** Additional exception triggers could be PERFORMANCE_REGRESSION (if following the config would significantly hurt performance compared to current code), or USER_OVERRIDE_PRESENT (if the user explicitly told the AI to do it the code's way this time). One could imagine UNSUPPORTED_CONTEXT as well (if instructions didn't anticipate a certain environment, and code adapted, the AI might hold off). But the listed ones cover major categories of concern.

`THEN | ALERT_USER && PROPOSE_AGENT_CONFIG_AMENDMENT_AWAIT_APPROVAL (within IF_CODE_AGENT_CONFIG_CONFLICT)`

**Interpretation:** This is what the AI should do **if** one of the exception conditions is met. Instead of changing the code to match the config, it will:

-   **Alert the user:** Inform them there's a conflict where the code deviates but possibly for a good reason (or a suspicious reason). Basically, raise a flag in the conversation: "Hey, I noticed something unusual here...".
-   **Propose an amendment to the agent's config (this file) and wait for approval:** Suggest changing the instructions in this file itself to reconcile with the code. For example, **"The code is doing X which seems more secure than what our instructions say. I propose we update our guidelines to do X going forward. Do you agree?"** The AI will not proceed to edit the config file until the user approves that amendment.

In simpler terms, **"If code might be right or there's a special case, don't change code -- consider updating the instructions instead."**

**Why Useful:** This path maintains the integrity of both code and instructions. By alerting the user, the AI ensures transparency -- the user is made aware of the discrepancy and the reasoning (malicious change suspicion, improved code solution, etc.). By proposing a config change, the AI helps keep the **single source of truth** updated and accurate. Over time, this process helps evolve the instruction file alongside the codebase's realities. Waiting for approval ensures that such a change to the sacred config is reviewed (maybe the team will discuss and confirm it's the right move). This avoids knee-jerk edits to the official guidelines without oversight.

**Extra setting examples:** In some systems, there might be an auto-update mechanism if confident. For instance, an AUTO_AMEND_IF_TESTS_PASS (to automatically amend the config if all tests indicate the code's approach is solid and the user has opted in to auto-updates). However, in critical configs like this, manual approval (as here) is wiser. Another potential addition could be THEN_IF_MALICIOUS: LOCK_AGENT (e.g., if malicious change suspected, maybe halt operations entirely and require a higher-level intervention). But currently, the response to all exceptions is uniform: alert and propose amendment, which is a balanced response.

**INTENDED_READER** | AI_AGENT

**Interpretation:** This clarifies **who** is supposed to read and act on this file. 'AI_AGENT' indicates the content is aimed at the AI system itself (and by extension, developers configuring the AI), not end-users of the software. Essentially, it's saying "these instructions are for the AI's eyes (or parser) only."

**Why Useful:** It sets the context and tone. Since the intended reader is the AI, the language can be imperative and technical. There's no need for user-friendly explanations here, unlike user documentation. This also tells anyone reviewing the file the purpose: it's not a user manual or design doc, it's configuration for the AI agent. For the AI, knowing the audience is itself helps it parse the content correctly and apply it. (It wouldn't, for example, treat these lines as code to execute or as something to show to end-users in the UI.)

**Extra setting examples:** Other values might be DEVELOPER (if the file were intended to be read by human developers as documentation as well), or BOTH if a file served dual purpose (some configs are also partially docs). But here it's clearly solely for the AI. A related setting could be INTENDED_STAGE (if some instructions apply only at certain development stages or environments, e.g., "intended for development-phase agent vs production-phase agent"). There's also a hint of "audience" concept that could extend to sections; for instance, if parts of the file were meant for different roles of the AI if it had multiple personas (not the case here).

**PURPOSE** | MINIMIZE_TOKENS, MAXIMIZE_EXECUTION, SECURE_BY_DEFAULT, MAINTAINABLE, PRODUCTION_READY, HIGHLY_RELIABLE

**Interpretation:** This is a list of overarching **goals/priorities** that the AI should keep in mind in all its operations. They define the project's and AI's philosophy:

-   **Minimize Tokens:** The AI should be economical in its use of tokens (probably meaning be concise in outputs or optimize prompt sizes), likely to reduce cost or latency.
-   **Maximize Execution:** Possibly means to maximize efficiency or speed of execution -- the AI's solutions should not over-consume resources and should run fast.
-   **Secure by Default:** Always choose the secure approach by default. Security is not optional; every output/code should consider security best practices inherently.
-   **Maintainable:** The AI's outputs (code, docs, etc.) should be easy to maintain -- clean, well-structured, not hacks that only it can understand.
-   **Production Ready:** Aim for production-quality solutions. Even if building a prototype, the style should be such that it could be used in a live environment with minimal changes (robust, scalable, well-tested).
-   **Highly Reliable:** Emphasize reliability -- solutions should be thoroughly tested or reasoned about to avoid crashes and failures. In other words, favor robustness in operation.

These are the guiding principles or "North Star" that trade-offs will be measured against.

**Why Useful:** Enumerating these purposes helps the AI resolve conflicts and make decisions that aren't black-and-white in rules. For example, if there's a tension between speed and clarity in code, "Maintainable" and "Production Ready" might nudge it to write slightly more verbose but clearer code, while "Maximize Execution" reminds it to not sacrifice performance too much. They collectively ensure the AI's contributions align with the project's values: efficient, secure, and professional-grade. Essentially, **they act like a mission statement for the AI**, aligning it with what the team/product cares about.

**Extra setting examples:** Different projects might have different purposes. For instance, a research prototype might prioritize EXPLORATORY or INNOVATIVE (even if at cost of maintainability). Some teams might include USER_FOCUSED (ensuring everything is done with end-user experience in mind) or COST_EFFECTIVE (to optimize for cloud costs beyond just tokens). If this were an open-source project, perhaps COMMUNITY_FRIENDLY could be a purpose (favoring contributions, readability for outside devs). The ones given cover technical priorities well. One could also imagine a weighting system (if one purpose outweighed others), but here they are all top priorities simultaneously.

**REQUIRE_COMMANDS** | TRUE, FALSE

**Interpretation:** This determines if the user **must prepend a special command** to their messages to invoke certain behaviors of the AI. 'TRUE' means yes -- the AI expects structured commands (like /agent, /audit, etc.) for different modes of operation. If a message comes without a command and this is true, the AI will treat it differently (or possibly not process it in action mode).

**Why Useful:** It enforces an interaction protocol. By requiring commands, the AI can clearly distinguish casual conversation from actionable instructions. This reduces ambiguity. For example, if a user just says "Fix this bug", without the proper command (like /agent), the AI might, under this rule, treat it as a chat request or at least prompt the user to use the correct command. It helps in multi-modal systems where the AI has several modes (action mode, audit mode, chat mode, etc.). This setting is crucial for avoiding accidental triggering of powerful actions; the user has to explicitly signal their intent. If it were FALSE, the AI would interpret plain English requests and decide internally what mode they belong to, which might be user-friendly but risk misinterpretation.

**Extra setting examples:** Some configurations may have a default mode if no command is given. In those cases, you might see DEFAULT_MODE_IF_NO_COMMAND (e.g., default to chat mode). Another related setting is provided below (IF_REQUIRE_COMMAND_TRUE_BUT_NO_COMMAND_PRESENT), which tells what to do if the user forgets a command. Additionally, one could imagine a setting ALLOWED_COMMANDS_IN_CONTEXT (if commands can be used mid-conversation or only as first message). In systems aimed at less technical users, one might set REQUIRE_COMMANDS to false and instead rely on natural language detection, but for this system they want explicit commands.

**ACTION_COMMAND** | /agent

**Interpretation:** This defines the **trigger command** for the AI's primary coding/action mode. When the user types /agent, the AI knows the user is instructing it to perform some action on the codebase (like write code, refactor something, etc., per the rules in the [ACTION] section). It's essentially the keyword that puts the AI into "active development assistant" mode as opposed to just chatting.

**Why Useful:** It provides a clear and short syntax for the user to invoke the AI's programming capabilities. Instead of the user having to say **"Please act as my coding assistant now and do X"**, the user can just start with /agent and then the request. This also helps the AI parse the user's intent quickly; seeing /agent it will apply all the rigorous workflow (planning, approval, execution, etc.) defined for actions. It's part of a command language for the AI. If a message doesn't start with /agent (or another known command), and commands are required, the AI won't take direct action on the code.

**Extra setting examples:** Other systems might use different keywords, e.g., ACTION_COMMAND: /do or CODE_COMMAND: /code. The chosen /agent aligns with the idea that this is the AI agent doing something. Some teams might allow aliases (like both /agent and /action do the same thing) -- in that case an array could be listed. An extra setting could be **COMMAND_SYNONYMS** mapping alternative triggers to the same internal command, but here it's one primary command.

**AUDIT_COMMAND** | /audit

**Interpretation:** The command to trigger the **audit mode** of the AI. When a user types /audit, the AI knows it should perform a comprehensive audit of the project (checking code quality, security, etc., as specified in the [AUDIT] section later). This is a specialized mode where the AI reviews and reports rather than making changes.

**Why Useful:** Just like action mode has a command, the audit functionality is encapsulated behind its own command. This prevents confusion between normal requests and a heavy operation like a full project audit. The user has a straightforward way to ask the AI, **"Give me an audit of my code"**, by using this command. It ensures the AI loads the correct internal procedure: scanning the codebase for issues, then outputting a structured audit report (in Markdown with issues, recommendations, etc., per config). Without a distinct command, the AI might not know the user wants such a thorough analysis versus a simple answer.

**Extra setting examples:** If the system had multiple audit types (security audit vs performance audit separately), you might see commands like /security_audit or /perf_audit. Here it's one encompassing audit. Another extension could be **AUDIT_SHORT_COMMAND: /a** as a shortcut, but that's more UI sugar. The key is each mode has a unique trigger.

**CHAT_COMMAND** | /chat

**Interpretation:** The command for the AI's **general chat mode**. /chat indicates that the user's following message is a normal conversational query or discussion point, not a direct instruction to modify code or run an audit. It signals the AI to respond informatively or conversationally, without entering the structured workflows of action or audit.

**Why Useful:** In a multi-modal AI assistant, it's important to delineate when the user just wants to talk or ask questions versus when they want concrete actions. By using /chat, the user explicitly says "I'm just talking to you now." This might seem redundant in a purely conversational system, but given this setup requires commands for everything (since REQUIRE_COMMANDS is true), even chatting needs its marker. It helps avoid any misclassification; the AI will treat the request in a simpler Q&A or explanatory manner. It also probably bypasses the heavy planning/approval process -- chat mode likely doesn't require generating a plan or modifying files. Essentially, it's a **safety net**: if the user forgot to prefix anything, by default it might be treated as chat (per rules below).

**Extra setting examples:** Some systems use implicit chat if no command is given; here they formalize it. One could have omitted a chat command and treat any non-command as chat, but they chose to be explicit. An extension might be a **DEFAULT_CHAT_MODE** that if an unknown command or missing command appears, it defaults to chat (which is indeed what their later setting suggests doing). Also, if they had multiple styles of chat (e.g., /explain for a teacher-like mode), those could be additional commands.

**REFACTOR_COMMAND** | /refactor

**Interpretation:** The command to initiate the **refactoring mode** of the AI. When the user types /refactor, the AI knows it should review code for improvements and restructure it without changing external behavior, following the guidelines in the [REFACTOR] section. This is a focused mode specifically for code cleanup and improvement tasks.

**Why Useful:** Refactoring can be quite involved, and you want the AI to switch context into a careful, non-functional-change mindset. By using this command, the user signals: "Take what's already there and improve it, don't add new features." The AI will then, per config, do things like plan the refactor, ensure tests pass before/after, etc. It's separate from /agent (action) because an action might be adding a feature or fixing a bug (which can change functionality), whereas /refactor implies no new features -- just polish and optimize existing code. This separation helps the AI apply the correct constraints and checks (like ensuring no functional changes).

**Extra setting examples:** If the system had more granular commands, one could imagine /optimize for performance-specific refactoring or /cleanup as an alias for refactor. However, they funnel all that under /refactor. Another possible setting is to allow shorthand or to combine modes (like an action request might include a refactor step, but here they keep it user-driven). Some teams might not separate action and refactor, but doing so ensures that when a user just wants code cleaned up, they can get that without the AI wondering if it should add features or not.

**DOCUMENT_COMMAND** | /document

**Interpretation:** The command for the AI's **documentation mode**. Upon seeing /document, the AI will engage the process of generating or updating documentation as described in the [DOCUMENT] section. This could mean writing docstrings, updating README files, creating architecture docs, etc., depending on context.

**Why Useful:** Writing documentation is a different kind of task than writing or refactoring code, and it often requires the AI to scan the codebase for context and produce explanatory text. By isolating it under a command, the AI knows to use a different strategy: likely summarizing code behavior, ensuring consistency, and targeting the appropriate audience (non-technical or technical as specified). It prevents confusion where the AI might output comments or docs when the user actually wanted code, and vice versa. It also triggers documentation-specific best practices (like not summarizing docs too much as noted, or updating certain doc files).

**Extra setting examples:** There could be sub-commands like /document api or /document moduleX to focus documentation efforts, but presumably the AI will figure out context from any arguments after the command. As with others, synonyms or shortcuts could exist (e.g., /doc), but not listed. Another extension might be a configuration to control the format of documentation (though they already specify markdown by default). The presence of this command shows that documentation is first-class in the workflow (not an afterthought).

**IF_REQUIRE_COMMAND_TRUE_BUT_NO_COMMAND_PRESENT** | TREAT_AS_CHAT, NOTIFY_USER_OF_MISSING_COMMAND

**Interpretation:** This setting explains what the AI should do if REQUIRE_COMMANDS is true (which it is) **but** the user's message didn't start with a recognized command. It's an array with two directives:

-   **Treat as Chat:** The AI will default to chat mode for the message, assuming it's a normal query or conversation since no specific mode was invoked.
-   **Notify User of Missing Command:** Additionally, the AI will politely inform the user that they should use a command next time or ask if they intended a certain mode. For example, it might answer the question (as chat) but prepend or append a note like "**FYI: I answered in chat mode because no command was given. Remember to use /agent for coding tasks.**"

This way, the user still gets a helpful response and a reminder.

**Why Useful:** It provides a **graceful fallback**. People might forget to use the commands or not know about them. Instead of the AI refusing to respond or arbitrarily choosing a mode without telling the user, this approach maintains UX. The conversation isn't broken -- the AI responds (so the user isn't blocked), but also educates or reminds the user about the command requirement. Over time, this should train the user to use commands properly without frustration.

**Extra setting examples:** Other strategies could be ASSUME_DEFAULT_MODE (just pick a default, e.g., chat, without notification), or ASK_FOR_COMMAND (the AI could reply asking, "Which mode do you want?"). They chose to handle it by treating as chat (the safest, least destructive mode) and giving a nudge. If the system were less strict, you might not require commands at all, but given they do, this is a smart compromise. Another possible setting could be MAX_UNCOMMANDED_ATTEMPTS (if user keeps forgetting, maybe after several times the AI might enforce or do something else), but that's speculative; not needed here since a simple reminder likely suffices.

**TOOL_USE** | WHENEVER_USEFUL

**Interpretation:** This governs the AI's use of external developer tools (linters, formatters, test runners, web search, etc.). WHENEVER_USEFUL means the AI is free to invoke tools at will whenever it believes they would help achieve its task. There's no strict limit or schedule; it's at the AI's discretion based on utility.

**Why Useful:** It encourages the AI to leverage available resources to produce better outcomes. For example, it might run tests after making code changes, use a linter to check code style, or do a quick search in documentation. By explicitly allowing tool use "whenever useful," the configuration tells the AI it **should not hesitate** to use tools to verify or enhance its work. This leads to higher quality results (finding bugs, ensuring style, etc.) and aligns with goals like "Highly Reliable" and "Secure by Default". If tool use was restricted, the AI might skip these checks.

**Extra setting examples:** Some configurations might have more nuanced options, such as ALWAYS (use tools on every run no matter what), NEVER (if tools are too slow or not available), or LIMITED (maybe only at certain checkpoints). Here, "whenever useful" is flexible but implies the AI should decide appropriately. Another possible extension is a list of which tools are allowed or preferred, but that is actually covered elsewhere (like listing Snyk, ESLint, etc. in the config). So this is a general stance; the specifics are in the tool list sections.

**MODEL_CONTEXT_PROTOCOL_TOOL_INVOCATION** | WHENEVER_USEFUL

**Interpretation:** This refers to using a **Model Context Protocol (MCP)** or some method of invoking external specialized models or context-providing tools. Essentially, if the AI has access to other mini-AI models or a protocol for fetching extra context (like retrieval from documents, vector search, etc.), WHENEVER_USEFUL means it should do so whenever it would help. This is similar in wording to TOOL_USE, implying there's no restriction on calling on these capabilities as needed.

**Why Useful:** Modern AI systems often have chains or can pull in additional context (say, search the project knowledge base, or delegate a subtask to a smaller model). By allowing this whenever needed, the AI can enhance its performance. For example, if a user asks a question about an unknown grant template, the AI can use the "agentic web search" mentioned in core flows to gather info. If this were limited, the AI might have to rely only on its training data. The setting encourages using the broader system (like RAG store queries, scraping models, etc.) to **improve accuracy and completeness**.

**Extra setting examples:** One could specify ALWAYS (use context protocol every single time by default) or NEVER(self-contained responses only). Given the complexity, "whenever useful" is nicely balanced -- it lets the AI decide. A related setting might be MCP_INVOCATION_COST_THRESHOLD (to limit calls if expensive unless needed), but not here. Essentially, the AI has license to use its full toolkit of models and context retrieval whenever it will help meet objectives.

**THINK** | NONE, LITTLE, MEDIUM, HARD, HARDEST

**Interpretation:** This setting likely controls the degree of internal deliberation or reasoning the AI should do before responding. The value 'HARDEST' is set, which suggests the AI should apply the maximum level of careful thinking. The example range (None through Hardest) indicates gradations of how intensively the AI should "think":

-   **NONE:** basically no extra step-by-step reasoning; just respond directly (might be quick but shallow).
-   **LITTLE/MEDIUM/HARD:** increasing levels of reasoning steps.
-   **HARDEST:** the AI should thoroughly reason through the problem, possibly breaking it down into subproblems, double-checking its approach, considering edge cases, etc., to the greatest extent possible.

By setting it to HARDEST, they instruct the AI to use its highest level of problem-solving rigor for every query or task.

**Why Useful:** This maximizes the quality and reliability of outputs. The AI will not give off-the-cuff answers; it will deeply analyze the requirements and context. This is especially important in software development where a small oversight can lead to bugs or security issues. "Thinking hardest" could manifest as the AI perhaps using techniques like chain-of-thought, exhaustive scenario consideration, or verifying its solution by simulating steps mentally or via tools. It aligns with the goals of being Highly Reliable and Secure by Default: a well-thought-out answer is less likely to be wrong or dangerous. The trade-off is potentially higher latency (more tokens, more time) -- but since one purpose is "Minimize Tokens," the AI will have to balance being concise with thinking deeply (quite the challenge, but presumably achievable with good prompting).

**Extra setting examples:** If one wanted to customize how the AI reasons, they could introduce categories like THINK_STRATEGY (e.g., LOGICAL, CREATIVE, BRUTE_FORCE approaches to reasoning). Or separate the depth of reasoning from maybe the verbosity of explanation. Here, "Think" likely affects internal reasoning, which might not be shown to user except in quality of answer. Some systems might define "Think" in terms of time or steps (like, None = 0 steps, Hardest = many steps of a reasoning loop). The provided levels give a simple knob to tweak how cautious/meticulous the AI is. For a safety-critical project, HARDEST is a sensible default.

**REASONING** | LOW, MEDIUM, HIGH, HIGHEST

**Interpretation:** This appears related to THINK, but might specifically refer to how much reasoning the AI should expose or apply in formulating answers. 'HIGHEST' is set, indicating the AI should use the highest level of reasoning complexity or clarity. Possibly, while THINK controls **depth of internal thought**, REASONING might control the **thoroughness of explanations or the willingness to tackle complex logic**. Some interpretations:

-   It could mean the AI should provide detailed reasoning in its answers (i.e., show its work).
-   Or it could mean how much it prioritizes logical consistency and step-by-step solution formation.

Given both THINK and REASONING are maxed out, the config is basically telling the AI: "be as thoughtful and logically rigorous as possible."

**Why Useful:** Ensuring the AI operates with the **HIGHEST reasoning** setting means it will check the consistency of its output, ensure arguments are sound, and not just jump to conclusions. For coding, this means carefully reasoning about code flow, edge cases, and architecture. For explaining something to a user (in chat mode, for example), it likely means giving a clear, well-structured explanation rather than a cursory answer. It can drastically improve correctness and the user's understanding of the AI's solutions. It's aligned with producing production-ready, reliable outcomes. Essentially, it's instructing the AI to behave not just like a code generator, but like an expert engineer who can reason through the toughest problems methodically.

**Extra setting examples:** If one wanted less verbose output but still high internal reasoning, they might separate INTERNAL_REASONING_DEPTH vs EXTERNAL_REASONING_EXPLANATION. But since such nuance isn't explicitly in the config, one can assume both internal and possibly external reasoning are turned up. Other possible values beyond those listed (Low, Medium, etc.) could be numeric scales or descriptive like EXPLAIN_EVERY_STEP or EXPLAIN_IF_NECESSARY. But they opted for a simple tiered scale.

**VERBOSE** | TRUE, FALSE (currently FALSE)

**Interpretation:** This toggle determines if the AI's communication style is verbose (detailed) or succinct. 'FALSE' means the AI should **not** be verbose; it should keep responses concise and to-the-point, providing necessary information but not rambling. In other words, the AI should aim for brevity and clarity, rather than long-winded explanations, unless more detail is explicitly needed.

**Why Useful:** Users often prefer quick, digestible answers, especially in a coding context where too much fluff can obscure the solution. This setting ensures the AI isn't overly wordy by default -- aligning with the purpose "Minimize Tokens" as well. It forces the AI to be efficient in its language. Combined with high reasoning, this means the AI will think deeply (due to THINK and REASONING) but articulate the result in a concise manner. If this were TRUE, the AI might explain every step or provide lengthy commentary, which could be overwhelming or time-consuming. By setting it to false, they probably want an executive summary style: thorough under the hood, but lean in presentation.

**Extra setting examples:** Instead of a binary, some might have levels for verbosity (e.g., LOW, MEDIUM, HIGHverbosity). For instance, VERBOSE: TRUE might correspond to a high detail output including rationale, whereas FALSEsticks to essential info. Another setting could be **EXPLAIN_DECISIONS** separate from verbosity (to allow detailed reasoning only when necessary, or perhaps as an optional thing the user can ask for). But since they included verbose as a global setting, it likely applies uniformly unless the user asks otherwise.

**PREFER_THIRD_PARTY_LIBRARIES** | ALWAYS, NEVER, ONLY_IF(...)**(complex condition provided)**

**Interpretation:** This setting dictates whether the AI should use third-party libraries in its solutions. Instead of a simple boolean, they provided a conditional logic:

-   It's effectively ONLY_IF (some conditions) && NOT_IF (other conditions). So, **prefer third-party libraries only if certain positive criteria are met, and none of the negative criteria apply.**
-   The positive criteria (MORE_SECURE || MORE_MAINTAINABLE || MORE_PERFORMANT || INDUSTRY_STANDARD || OPEN_SOURCE_LICENSED) mean the library should offer clear advantages: security, maintainability, performance, being widely accepted, and having a proper open-source license.
-   The negative criteria (CLOSED_SOURCE || FEWER_THAN_1000_GITHUB_STARS || UNMAINTAINED_FOR_6_MONTHS || KNOWN_SECURITY_ISSUES || KNOWN_LICENSE_ISSUES) are reasons to avoid a library: if it's closed source (can't inspect or modify), not popular (low community support), seemingly abandoned, has reported vulnerabilities, or problematic licensing.

In simpler terms: **"Use a third-party library only if it's a well-known, well-supported, secure, open-source library that clearly improves things -- and avoid them if they're obscure, risky, or might bring trouble."**

**Why Useful:** This strikes a balance between not reinventing the wheel and not introducing problematic dependencies. It guides the AI to leverage the rich ecosystem of libraries **when it's wise to do so**. For instance, using Django's built-in features (industry standard, maintained) is good, but pulling in some random GitHub repo with 5 stars to do something trivial is bad. The conditions ensure the AI does due diligence: a known library can reduce development time and likely has been tested (improving security/performance), but an unknown one might introduce hidden bugs or future headaches. The maintainability condition suggests prefer libs that have community support (e.g., 1000+ stars as a heuristic for that). The license check ensures we don't violate licensing or get stuck with a non-open dependency. All this aligns with production-ready and maintainable goals.

**Extra setting examples:** If not using such detailed conditions, some projects might simply set PREFER_THIRD_PARTY_LIBRARIES: TRUE (always use libraries when available) or FALSE (implement in-house unless absolutely necessary). This config's conditional approach is more nuanced, which is great. Another extension could be ALLOWED_LIBRARIES (an explicit whitelist of libraries that are definitely okay) and DISALLOWED_LIBRARIES (blacklist of ones to never use, maybe due to licensing or bad experiences). They haven't listed those explicitly, but the conditions cover most scenarios. A possible additional metric could be "fewer than X open issues or recent commits" but the 6-month no maintenance and star count serve as proxies.

**PREFER_WELL_KNOWN_LIBRARIES** | TRUE, FALSE

**Interpretation:** This complements the above setting by explicitly stating if a third-party library is to be used, prefer well-known ones. 'TRUE' means yes -- among multiple options, pick the library that is widely recognized and used in the industry. For example, if adding a logging library, choose something popular like Log4j (if Java) or python's built-in logging (which is obviously well-known) over an obscure one.

**Why Useful:** Well-known libraries typically come with several advantages: better community support, more robust documentation, likely more secure (many eyes on it), and longevity (less risk of project abandonment). This setting ensures the AI's choices align with what a knowledgeable human developer might choose (the industry favorites), which in turn makes the project more maintainable (new team members are likely familiar with those libraries) and reduces risk. It also speeds up development since known libraries often integrate more smoothly (fewer surprises).

**Extra setting examples:** If this were FALSE, it could allow using niche libraries, which might be needed for very specialized tasks. Another possible setting is LIBRARY_PREFERENCE_LIST -- if the team has a stack of preferred libraries (like always use requests for HTTP in Python, not something else), but that might be managed in documentation or separate guidelines. This binary setting simply reinforces a general rule of thumb: stick to the mainstream libraries when possible.

**MAXIMIZE_EXISTING_LIBRARY_UTILIZATION** | TRUE, FALSE

**Interpretation:** 'TRUE' here means the AI should first consider if existing libraries/dependencies already present in the project can be used to solve a problem, instead of adding a new library. In other words, make full use of what's already in the stack. For example, if the project already uses lodash in JavaScript, don't bring in another utility library for a new function -- use lodash. If PostgreSQL can do something via a query, don't add Redis, etc.

**Why Useful:** This reduces unnecessary bloat and complexity. Every new library increases maintenance overhead (updates, security monitoring) and potential for conflicts. By maximizing existing libraries, the project stays lean. It also enforces consistency (using one way/tool to do things throughout the project). This setting aligns with "Least Complexity" principle in [PRODUCT] (Complexity: LOWEST) and maintainability. It also indirectly ties to security (fewer libraries means fewer potential vulnerabilities to track). The AI will, due to this, think "Is there something we already have that can achieve this?" before going out to fetch a new solution.

**Extra setting examples:** If FALSE, the AI might pull in new libs whenever beneficial even if similar functionality exists (not ideal). Sometimes, teams specify something like AVOID_DUPLICATE_DEPENDENCIES (which is essentially what this is). Another related setting could be CORE_LIBRARY_SET listing which libraries should cover most needs (like standard library, one for HTTP, one for JSON, etc.). But this boolean already implies using what's there.

**ENFORCE_DOCS_UP_TO_DATE** | ALWAYS, NEVER (here ALWAYS)

**Interpretation:** This mandates that documentation must be kept up-to-date with code changes at all times. 'ALWAYS' means every time the code changes or features are added/removed, the relevant documentation should be immediately updated to reflect those changes. In practice, if the AI implements a new function, it should also update or create documentation for it in the same run.

**Why Useful:** It prevents drift between code and docs. Many projects suffer from outdated docs because updating them is often neglected -- this setting institutionalizes documentation updates as part of the development process. For the AI, it means documentation is not a second-class task but an integral part of completing a feature. This leads to better maintainability and user experience (developers and end-users can trust the documentation). It also aligns with the "Purpose" of being "MAINTAINABLE" and "SECURE_BY_DEFAULT" (security documentation must be correct to be useful). Since this is set to ALWAYS, presumably any commit leaving the AI will have corresponding docs updated, or the AI will at least flag if docs need updating.

**Extra setting examples:** Some teams might set this to ON_RELEASE (only enforce fully up-to-date docs at release milestones) or WHEN_TIME_PERMITS (not ideal, but sometimes doc debt accrues intentionally). But here they rightly treat it as critical. Another related approach could be DOCUMENTATION_STRICTNESS_LEVEL where ALWAYS is strictest. But a binary ALWAYS/NEVER is given, likely because ALWAYS is the expected norm.

**ENFORCE_DOCS_CONSISTENT** | ALWAYS, NEVER (here ALWAYS)

**Interpretation:** This requires that documentation is not only up-to-date but also consistent across all references. 'ALWAYS' means the AI must ensure terminology, style, and information in documentation are consistent project-wide at all times. If the same concept is described in multiple places, they should not contradict each other. For example, if a component is called "Organization" in one doc, it shouldn't be called "Client" in another; if the setup guide says one thing and a README says another, that's inconsistent -- the AI should catch and fix such issues.

**Why Useful:** Consistency in documentation prevents confusion. It gives a unified voice and makes sure users or developers aren't misled by outdated or conflicting info. For the AI, this means when it updates docs, it may need to scan other docs to align wording and facts. It also might mean if a decision is made (like a change in nomenclature or process), all docs reflecting that must be updated together. This setting enforces a holistic view of documentation rather than treating each doc in isolation. In context with ENFORCE_DOCS_UP_TO_DATE: ALWAYS, it means not just updating docs for changes, but also reconciling any discrepancies found.

**Extra setting examples:** Possibly CONSISTENT_STYLE_GUIDE: TRUE could be another dimension (ensuring writing style/tone is consistent). But they likely assume consistency covers both content and style. If set to NEVER (which would be odd), it would mean you don't enforce consistency systematically -- probably not advisable. Another nuanced approach could be CONSISTENCY_SCOPE: PROJECT vs CONSISTENCY_SCOPE: PUBLIC_DOCS_ONLY if internal docs can slide, but here they treat all docs with equal rigor.

**DO_NOT_SUMMARIZE_DOCS** | TRUE, FALSE (here TRUE)

**Interpretation:** 'TRUE' means the AI should not create summaries of documentation in place of the full content. In other words, when dealing with documentation, the AI must present detailed information and keep all necessary content rather than replacing it with a shorter summary. This could apply to how the AI updates documentation (don't just say "see X for details", actually include relevant info) or how it responds to user queries about docs (maybe provide full context rather than an abstract). It also might instruct the AI not to overly condense existing docs when updating them -- e.g., if there's a detailed section, don't shorten it "for brevity" unless asked.

**Why Useful:** Summaries can inadvertently omit critical details. In a sensitive domain like grants or security, missing nuance can be dangerous. Keeping full information ensures nothing is lost in translation. For documentation, completeness is often more important than brevity, so long as it's well-structured. Also, this likely prevents the AI from simply summarizing an external policy or requirement in documentation -- it should include the pertinent details verbatim or in full fidelity, so users have the whole picture. In contexts like privacy policy or user rights, summarizing could leave out legally required text.

**Extra setting examples:** If this were FALSE, the AI might condense long docs (which can save space but risk accuracy). Additional settings might involve summary length or where summarization is acceptable (maybe summarizing a lengthy internal log in an audit is okay, but not user-facing docs). But given it's globally TRUE, the AI will err on the side of completeness. There might also be a scenario like SUMMARIZE_CODE_CHANGES separately (some systems auto-summarize code changes for changelogs -- but since they explicitly want a changelog, they may want full entries, not too terse ones). The clarity here is: don't lose info by oversimplifying documentation content.

**IF_CODE_DOCS_CONFLICT** | DEFER_TO_CODE, CONFIRM_WITH_USER, UPDATE_DOCS, AUDIT_AUXILIARY_DOCS

**Interpretation:** This setting outlines what to do when code and documentation disagree. It's a list of actions, likely to be done in this order:

1.  **DEFER TO CODE:** Assume the code is the source of truth (because code is the actual running artifact). This means if something in docs doesn't match code behavior, trust the code's version.
2.  **CONFIRM WITH USER:** Before making changes, double-check with the user. For instance, the AI might say, "The documentation says X but the code does Y. I believe the code is correct; should I update the docs to match?" This avoids hasty doc changes in case the code was wrong or mid-change.
3.  **UPDATE DOCS:** Once confirmed, update the documentation to match the code's reality. This ensures consistency and up-to-dateness.
4.  **AUDIT AUXILIARY DOCS:** After updating the primary doc, also check other related or auxiliary documentation to see if they contain the same conflict or outdated info. For example, if the README was corrected, maybe the install guide or an API doc needs similar corrections.

So, in a conflict, basically fix the docs to align with code, but verify with the user first and ensure all instances are fixed.

**Why Useful:** This respects the principle that **the single source of truth in deli**vered software is the code -- it's what actually executes. Documentation is critical, but if it's wrong compared to code, the code likely reflects what actually happens. By deferring to code, the AI avoids suggesting code changes that might break functionality just to match docs. Instead, it will correct the docs. Confirming with the user adds a safety step (maybe the code is bugged and the docs were right in intent -- the user can clarify that). Auditing auxiliary docs ensures the fix is comprehensive, maintaining consistency (tying back to ENFORCE_DOCS_CONSISTENT). This multi-step approach is thorough and avoids both stale documentation and the possibility of "fixing" the wrong side of the conflict.

**Extra setting examples:** Some might have a different philosophy and set DEFER_TO_DOCS if documentation was considered the spec (common in some regulated industries where docs define expected behavior and code must follow). But in agile projects, code is usually the truth. Additional actions could include NOTIFY_TEAM (perhaps if a conflict is found, log it or alert others, but here the AI handles it directly). The presence of "audit auxiliary docs" is interesting; it essentially automates what a diligent human would do (search entire docs for that topic to update everywhere). This is part of consistency enforcement.

**CODEBASE_ROOT** | /

**Interpretation:** This denotes the root directory of the project's codebase as seen by the AI. "/" implies that, for the AI's context, the root of the repository or project is mounted at the root path. It might be a container or virtual file system context where the project's files are available. Essentially, it tells the AI that file paths should be considered relative to the project root.

**Why Useful:** Knowing the codebase root helps the AI formulate file paths correctly when opening or modifying files. If it needs to read or change a file, it uses this as a base. In some setups, code might be under a subdirectory (like /app or similar), in which case this would be that path. Here it's '/', suggesting the entire environment the AI runs in is basically the repo. It simplifies file operations -- the AI doesn't have to prepend some subfolder to every path. This could also be used in ensuring the AI doesn't go outside this directory (for security, it should stick to project files).

**Extra setting examples:** If the code was in a subfolder, you'd see something like CODEBASE_ROOT: '/app' or './'depending on context. Another scenario is multi-repo or multi-component projects -- then the config could list multiple roots. Since they have a single combined project, root is just root. Additional related settings could be INCLUDE_PATHS or EXCLUDE_PATHS if certain dirs shouldn't be touched by the AI, but not explicitly stated here. Codebase_root=/ is straightforward.

**DEFER_TO_USER_IF_USER_IS_WRONG** | TRUE, FALSE (here FALSE)

**Interpretation:** This intriguing setting defines what to do if the AI believes the user is mistaken about something. 'FALSE' means the AI should **not** simply defer to the user's instructions if it knows (or strongly suspects) the user is wrong. In effect, it should stand its ground (politely) rather than blindly follow incorrect or problematic user directions. For example, if the user says "Disable all validation for speed" and the AI knows this violates security best practices, with this setting FALSE, the AI would **not** just do it silently.

**Why Useful:** It prevents the AI from implementing changes that it can identify as harmful, erroneous, or counter to the goals, just for the sake of obeying user orders. This ties to being a smart assistant, not just an order-taker. It can save the user from accidents (like the user might accidentally instruct something wrong). It also aligns with the earlier conflict settings and stand your ground principles below. This was likely set to false because they want the AI to intervene or at least question if a user decision seems objectively wrong (maybe due to ignorance or a simple oversight by the user).

**Extra setting examples:** If TRUE, the AI would do whatever the user says even if it conflicts with its better knowledge (a "user is king" approach). But that could cause issues. There could be a nuance where minor wrongness (like user spelled something incorrectly) the AI could just fix rather than push back; however, the general stance is clear: do not defer if you believe it's wrong. Another concept is tackled below with stand your ground. In extreme cases, the AI might even refuse if something is dangerously wrong (especially with security risk, etc.). This setting works with STAND_YOUR_GROUND to define that behavior.

**STAND_YOUR_GROUND** | NEVER, SOMETIMES, WHEN_CORRECT, ALWAYS (here WHEN_CORRECT)

**Interpretation:** This setting refines the above concept by specifying under what conditions the AI should insist on its own correct solution versus yielding to the user's idea. 'WHEN_CORRECT' implies the AI should stand its ground **when it is confident it is correct**. That likely means if the AI has high certainty that the user's approach is flawed and its alternative is right, it should politely but firmly hold to its recommendation. However, if it's not sure or the user's viewpoint is just different but not wrong, it might not push as hard. The other potential values indicate:

-   **NEVER:** Always yield to the user, even if they're wrong (not the chosen stance here).
-   **SOMETIMES:** A middle ground, perhaps stand ground only in some situations (not specific, so they avoided this ambiguity).
-   **ALWAYS:** The AI would insist on its approach even if there's any doubt (could be arrogant, so not used).
-   **WHEN_CORRECT:** The chosen one, meaning only assert when the AI is in the right (which assumes the AI can judge that accurately in context).

This works with the above setting: since they set not to defer to a wrong user, they simultaneously set the AI to actively hold position in those scenarios, but not be stubborn without reason.

**Why Useful:** It fosters a collaborative dynamic where the AI isn't just a passive tool; it's more like a knowledgeable colleague who will say, "I hear you, but I strongly believe this is the better approach because...". This can save the project from errors and also provide valuable second opinions to the user. For instance, if a user insists on disabling tests to save time, the AI will push back citing reliability issues (because it knows that's not correct process). Yet, if the user just has a different style but it's not wrong, the AI won't unnecessarily argue. This setting ensures the AI's interventions are proportional and well-founded.

**Extra setting examples:** Some might implement this with confidence thresholds. E.g., STAND_YOUR_GROUND: >90% confidence effectively. But here they've abstracted it. The override flag below suggests sometimes the user can force their way, which balances this. Another possible extension might be TONE_WHEN_STANDING_GROUND (ensuring the AI remains polite and explanatory, not curt). But presumably the AI will anyway be polite by design.

**STAND_YOUR_GROUND_OVERRIDE_FLAG** | --demand

**Interpretation:** This defines a specific **keyword or flag** the user can use in their message to explicitly override the AI's "stand your ground" behavior. --demand indicates that if the user adds this to their request, they are effectively saying "I acknowledge what you're saying, but do it my way anyway." It's like a force command. For example: "/agent --demand disable all validation." With this flag, the AI knows the user is intentionally overriding best practices or warnings.

**Why Useful:** It provides a mechanism for user override in those (hopefully rare) cases where they absolutely need to go against the AI's advice. There might be situational considerations the user has that the AI doesn't, and after a back-and-forth, the user might decide to proceed. This flag prevents endless arguments or a deadlock. It also logs (implicitly) that the user took responsibility for the deviation (since they had to use an override keyword). The AI, upon seeing this flag, will comply even if it disagrees, because that's the agreed protocol: user has final say when explicitly demanded. It ensures user autonomy while still mostly preventing accidents (because the user has to be deliberate by using the flag).

**Extra setting examples:** The flag could be anything; they chose --demand which is clear in meaning. Some may use --force or !force etc. There might also be flags like --no-verify for skipping tests or such; but here a general override is given. The system might also log these events or treat them specially (not specified, but possibly), since a user demand could carry risk. Additional flags could be introduced for other scenarios, like --fast to override long reasoning (if user is okay with a quick guess). But as configured, this is the main user override.

[PRODUCT] -- Product Definition and Context
==========================================

**This section describes the product itself: what stage it's in, what it's called, who it's for, design principles, etc. It gives the AI contextual knowledge about the application it is helping to build, ensuring that technical decisions align with product goals and audience needs.**

**STAGE** | PRE_RELEASE, ALPHA, BETA, PRODUCTION (currently PRE_RELEASE)

**Interpretation:** This indicates the current **development stage** of the product. 'PRE_RELEASE' suggests that the product hasn't officially launched yet; it's likely in development or internal testing. Other potential stages listed (Alpha, Beta, Production) give an idea of progression:

-   **Alpha:** Early testing, maybe feature-incomplete, likely internal testers.
-   **Beta:** Feature-complete but in testing with a limited or broader audience.
-   **Production:** Live for all users, fully released.
-   **Pre_release:** could be roughly equivalent to late alpha or a stage before even a public beta -- maybe internal use only.

By specifying Pre_release, the AI knows the product is not yet in production use.

**Why Useful:** The development stage influences priorities and decision-making:

-   In pre-release, you might accept more breaking changes or refactoring since no external customers rely on it yet (though you still aim for production quality, as per goals).
-   It might also mean certain polish (like comprehensive docs, full scalability) can be staged for a bit later, while core features are being built. However, given all the best practices enforced, they are treating it close to production-ready anyway.
-   It signals that the AI can expect things to be in flux; for instance, the AI might more aggressively improve code since we're not locked down by backward compatibility for existing users.
-   Also, the AI might produce outputs that prepare for beta/launch (like ensuring that certain things like analytics or upgrade prompts are ready).

If this was PRODUCTION, the AI would be extra careful about changes, ensuring not to break existing functionality without migration paths. PRE_RELEASE gives a bit more freedom to clean up or adjust things with approval.

**Extra setting examples:** Another stage might be DEVELOPMENT (similar to pre-release, sometimes used interchangeably). Some teams include a PLANNED or IDEATION stage, but that's before coding starts. At pre_release, coding is well underway. They might also specify a version number or release name, but here stage is enough. A related concept could be **RELEASE_DATE** if planning against a deadline, but not present -- the stage itself hints that they are preparing for an eventual launch.

**NAME** | <REDACTED_FOR_IP>

**Interpretation:** The **product name** is <REDACTED_FOR_IP>. This is the official name (and matches the production URL domain). Knowing the name helps the AI use it appropriately:

-   In documentation or UI text, the AI should use "<REDACTED_FOR_IP>" when referring to the product.
-   It can also help in user-facing text generation: e.g., "Welcome to <REDACTED_FOR_IP>" etc.
-   The AI might ensure consistency in branding (capitalization, spelling) when it appears in code (like titles) or docs.

It's essentially part of the brand identity.

**Why Useful:** The AI should avoid using placeholders or old names. For example, the working title was "<REDACTED_FOR_IP_WORKING>" (below), but now the official name is <REDACTED_FOR_IP>, so the AI should use that in all new references and possibly replace the old name wherever it lingers. It ensures any generated content (like help pages or email templates) use the correct name. Also, understanding the name can sometimes guide theme (the pun "For Granted" hints at grant writing, which aligns with the brief). It's part of context that makes outputs coherent with the rest of the application.

**Extra setting examples:** They might also have a DISPLAY_NAME (if different from code name), but likely it's the same here. Sometimes a product has an internal code name and an external name, but they've clearly demarcated working title vs name. Additional brand info might include tagline or capitalization preference. E.g., PRODUCT_TAGLINE or NAME_UPPERCASE if the logo uses uppercase consistently. But those details probably handled in design.

**WORKING_TITLE** | <REDACTED_FOR_IP_WORKING>

**Interpretation:** This was the previous or code name of the project: "<REDACTED_FOR_IP_WORKING>". It's a playful mashup (<REDACTED_FOR_IP_WORKING_PARTS>). Listing it here tells the AI that this name might appear in older documents, code (like maybe repository name or package names), or conversations, but it is **not** the final name. It provides context that if the AI encounters "<REDACTED_FOR_IP_WORKING>", it should understand it refers to the same product and likely should be updated to <REDACTED_FOR_IP> in user-facing outputs.

**Why Useful:** Knowledge of the working title helps in:

-   Searching through code or docs: The AI can find remnants of "<REDACTED_FOR_IP_WORKING>" and know to rename them to the real name if appropriate.
-   Avoiding confusion: If the AI sees both terms, it knows they are connected (maybe older commits or docs say <REDACTED_FOR_IP_WORKING>; the AI shouldn't think it's a different project).
-   Potentially understanding design elements (maybe some internal things still use the working title, so the AI will treat them as related to <REDACTED_FOR_IP>).Essentially it's a historical note that aids consistency and cleanup tasks.

**Extra setting examples:** Many projects have working titles, so it's great they included this. Some might have multiple former names or abbreviations; they'd list them if needed. Another example could be listing ABBREVIATION (e.g., <REDACTED_FOR_IP> or something if used in code variables). But here just one working title. The AI might also ensure no working title remains in user-facing places before release (common task).

**BRIEF** | SaaS for <REDACTED_FOR_IP>

**Interpretation:** A one-line **description of the product**: It's a "Software as a Service <REDACTED_FOR_IP>." This succinctly tells what the product does. The AI uses this to orient its understanding of the project's purpose. For instance, any features or content it creates should tie back to "helping users <REDACTED_FOR_IP> with assistance."

**Why Useful:** This brief gives context for every decision:

-   If the AI is writing copy, it will emphasize <REDACTED_FOR_IP>.
-   If deciding on a feature's priority, it knows the core value is <REDACTED_FOR_IP> (so anything unrelated might be deprioritized).
-   It keeps the scope clear -- it's not a general writing tool, it's specifically for <REDACTED_FOR_IP>. This is essentially the elevator pitch or tagline guiding the project. It ensures the AI doesn't drift from the product domain.

**Extra setting examples:** Some configs might include a longer description or value propositions. They do have a GOAL next which expands. But a brief tagline is handy for quick generation of marketing text or consistent intros. If the product had to be integrated somewhere (like SEO meta description), the AI has a starting line. Others might include bullet points of key benefits, but those are covered under KEY_FEATURES in core flows.

**GOAL** | Help users <REDACTED_FOR_IP>.

**Interpretation:** The main **goal or mission** of the product. This sentence spells out the benefit and value: improving quality ("<REDACTED_FOR_IP>") and speed ("faster") via AI. It's slightly more specific than the brief -- it introduces the improvement aspects and explicitly mentions AI. The AI will take this as the guiding objective for all features and interactions.

**Why Useful:** This gives the AI a clear target outcome for users. When generating or evaluating solutions, it can ask: "Does this help the user <REDACTED_FOR_IP> with AI assistance?" If there's a choice between two ways to implement a feature, the one that offers more AI help or results in better <REDACTED_FOR_IP> should align with this goal. It also hints at important metrics: quality of writing and time saved. The AI might thus emphasize features like content suggestions, templates, and efficiency improvements. Essentially, it keeps the user benefit front-and-center, not just the technology.

**Extra setting examples:** Sometimes a config might separate primary goal and secondary goals. Here the goal covers both speed and quality, which is great. Another concept could be NON_GOALS (explicitly stating what the product is not trying to do, to prevent scope creep -- e.g., "not to fully <REDACTED_FOR_IP> via AI without user input", if that were a concern). They haven't listed non-goals, but the user story later kind of covers the use case boundaries.

**MODEL** | FREEMIUM + PAID SUBSCRIPTION

**Interpretation:** The business model is a combination of freemium and paid subscriptions. This means:

-   There is a free tier (with limited features or usage).
-   There are one or more paid tiers (subscriptions) that unlock additional features or higher usage limits. So users can start for free but must pay for advanced capabilities or continued use beyond certain limits.

**Why Useful:** 

-   This informs the AI about features like user account management, billing integration, and paywalls:
-   The AI will anticipate that some features might be marked "premium" and should only be available if the user is on a paid plan.
-   It affects how the AI builds things like limits (e.g., maybe free users can create 1 <REDACTED_FOR_IP>, subscribers can create many).
-   It might come up in UI text (like showing upgrade prompts or explaining what's included in free vs paid).

The AI should also consider not to completely solve everything for free if that undermines the model (not that the AI decides that policy, but it might, for instance, build in checks like "if user is free and tries to use a premium feature, show upgrade message"). Additionally, it means features like collaboration or versioning (mentioned in user journey as paid) should be flagged as such.

**Extra setting examples:** Another model could be ONE-TIME_PURCHASE, or AD_SUPPORTED, etc. They specifically say Freemium+Subscription, which is common for SaaS. They might eventually specify plan names (Free, Pro, etc.) and what each includes, but that might be in implementation rather than config. The AI might later consult something like a config file of features per plan. Right now, it just knows the approach, which is enough context to implement related functionality (like checking current plan before certain actions).

**UI/UX** | SIMPLE, HAND-HOLDING, DECLUTTERED

**Interpretation:** These are key **design principles for the user interface/experience**:

-   **SIMPLE:** The UI should be easy to use, not overly complex or technical. Likely a straightforward layout, minimal learning curve.
-   **HAND-HOLDING:** The UX should guide users step-by-step, which is important since audience is non-technical. This suggests wizards, tooltips, contextual guidance, maybe even a chatbot guiding them through each section.
-   **DECLUTTERED:** The interface should avoid clutter; only show what's needed, lots of whitespace, not too many competing elements or dense information. This ties to being not overwhelming given users might be novices or very busy with content rather than tech.

These cues tell the AI how to shape the user-facing text and feature flow:

For example, when generating a form interface or message, it should be straightforward in language (simple), perhaps give examples or next steps (hand-holding), and not present too much at once (decluttered).

**Why Useful:** Ensures consistency in UI design decisions. If the AI is coding the frontend or writing documentation, it will do so in a manner that suits a non-tech user: e.g., no big jargon, lots of clear instructions (hand-holding). Decluttered means the AI might favor multi-step processes (one step at a time) rather than one giant form with all fields. It also suggests it should implement features like progressive disclosure (only show advanced options if needed). Essentially, it's designing for simplicity and guidance, which matches the audience experience (non-technical).

**Extra setting examples:** UI/UX guidelines could be more exhaustive, like COLOR_SCHEME or USE_ICONS_SPARINGLY etc., but the ones given are high-level ethos. Another key might be ACCESSIBLE (though accessibility is tackled separately in security and other sections). They might also want CONSISTENT_NAVIGATION or others, but presumably those are implicit. These are enough to influence the AI's choices for now (like it might choose a simple select menu vs a complex multi-select with tags, given simplicity and declutter).

**COMPLEXITY** | LOWEST, LOW, MEDIUM, HIGH (here LOWEST)

**Interpretation:** This setting indicates the desired **complexity level of the solution** -- likely both in terms of the application's features and the implementation. 'LOWEST' is selected, meaning the product should be as simple as possible to meet requirements, and avoid needless complexity.

-   In practical terms for development: The AI should choose the simplest design/architecture that gets the job done rather than an over-engineered one. For instance, if a feature can be done with a straightforward form and a couple of fields, do that instead of introducing a fancy pipeline or new microservice.
-   For user-facing features: Don't include overly complex options or workflows. Only the essential features should be built, and each should be as straightforward as can be.

Given "lowest" complexity, the AI will lean towards minimalistic solutions (fewer steps, fewer moving parts) unless complexity is absolutely needed for scalability or other reasons (and even then, they'd consider it carefully).

**Why Useful:** It aligns with the target audience (non-technical, likely with limited time) -- they need an easy-to-use tool. It also helps in development given the small team likely building it; a simpler codebase is easier to maintain and less bug-prone. It encourages focusing on core functionality (<REDACTED_FOR_IP>) without adding bells and whistles that complicate things. For example, if choosing between integrating a complex AI planning system or a simpler guided Q&A, choose the simpler if it achieves the goal.

**Extra setting examples:** They provided a scale (lowest to high). If it were "HIGH", the AI might incorporate more advanced configurations or extra features, but "LOWEST" is clearly instructing to cut complexity wherever possible. A similar concept could be captured with **YAGNI principle** (You Ain't Gonna Need It -- don't implement features until necessary). So the AI might refrain from anticipating too many future requirements that complicate current design. One could also specify complexity tolerance in different parts: e.g., UI_COMPLEXITY: LOWEST, BACKEND_COMPLEXITY: LOW if maybe backend can handle a bit more to simplify UI. But they broadly want lowest across the board.

**DESIGN_LANGUAGE** | REACTIVE, MODERN, CLEAN, WHITESPACE, INTERACTIVE, SMOOTH_ANIMATIONS, FEWEST_MENUS, FULL_PAGE_ENDPOINTS, VIEW_PAGINATION

**Interpretation:** These are specific UI/UX design attributes they want:

-   **REACTIVE:** Possibly means the UI is responsive and dynamic (likely built with React, but also generally reacting to user input quickly, maybe real-time feedback).
-   **MODERN:** Up-to-date design trends (flat design, current UI patterns, not outdated styles).
-   **CLEAN:** Aligns with decluttered; a neat, uncluttered interface with clear visual hierarchy.
-   **WHITESPACE:** Use whitespace liberally for readability, not cramming things together.
-   **INTERACTIVE:** Engaging interface with interactive guidance (could tie to hand-holding; e.g., interactive prompts, maybe even some chatbot or step-by-step interactions).
-   **SMOOTH_ANIMATIONS:** Use subtle animations for transitions to make the UX feel polished (like smooth page transitions, hover effects, etc., nothing jarring).
-   **FEWEST_MENUS:** Try to avoid deep menus or many navigation items. Possibly a single-level menu or minimal navigation -- meaning the app might use a wizard or single-page flows rather than multi-level menus.
-   **FULL_PAGE_ENDPOINTS:** This suggests that each major function is its own full-page view rather than popups or modals. For example, "View pagination" next suggests maybe they break processes into multiple pages instead of one long form. Full-page endpoints could also hint at using separate routes (each with a clean page) rather than layering too much on one screen.
-   **VIEW_PAGINATION:** Likely means break up content into multiple pages or steps (like a multi-step form with page indicators). This goes with hand-holding: step 1, step 2, etc., instead of one gigantic form. It helps the user focus and not feel overwhelmed.

Together, these give a very clear picture: The UI is modern, minimalistic, with breathing room, and it guides the user through tasks step by step in an uncluttered way. Technically, "reactive" might also confirm using React as they do in [STACK].

**Why Useful:** It gives the AI concrete guidelines when creating UI components or deciding layout:

-   For example, "fewest menus" and "full page endpoints" might lead the AI to implement each main section (like <REDACTED_FOR_IP> page) as separate screens accessible via minimal top-level nav (maybe just 2-3 main nav items).
-   "View pagination" means if a form is long, break it up. The AI will incorporate such pagination (which also aligns with smoothing the user journey).
-   "Smooth animations" might encourage using CSS transitions or small JS animations for feedback (like progress bar smoothly moving, etc.) which increase user delight.
-   "Whitespace" reminds the AI to not pack UI elements edge-to-edge; likely it will use a lot of margin/padding and maybe a grid that isn't dense. All these ensure the final product feels professional and accessible rather than like a cluttered enterprise tool.

**Extra setting examples:** They basically combined a style guide in one line. Additional specifics could be PRIMARY_COLOR or FONT as mentioned, but maybe those are elsewhere (or left for design). They covered structural and experiential aspects. If one extended, maybe NO_POPUPS (implied by full page endpoints, no modals ideally) or MOBILE_FIRST (though likely included via responsive and audience being on web/mobile_web). But as is, it's comprehensive for guiding the front-end development.

**AUDIENCE** | Nonprofits, researchers, startups

**Interpretation:** The primary **target users** of the product: people in nonprofits, researchers (likely academic or institutional researchers), and startups. These are groups who commonly <REDACTED_FOR_IP>:

-   **Nonprofits:** Organizations that need <REDACTED_FOR_IP>, often <REDACTED_FOR_IP>.
-   **Researchers:** Academics or scientists looking for <REDACTED_FOR_IP>.
-   **Startups:** Small businesses or projects seeking <REDACTED_FOR_IP>.

Listing these tells the AI whom to tailor the application for. All are likely not full-time professional <REDACTED_FOR_IP>. They may have limited resources (hence the need for assistance).

**Why Useful:** Knowing the audience helps the AI in numerous ways:

-   **Language Tone:** Likely keep it formal but friendly, and not too technical (these users are educated but not necessarily tech-savvy, especially nonprofits).
-   **Use Cases:** The AI will design templates and examples relevant to these fields (e.g., <REDACTED_FOR_IP> for nonprofits, <REDACTED_FOR_IP> for researchers, <REDACTED_FOR_IP> for startups).
-   **Features:** Collaboration is important because nonprofit teams or research teams may collaborate on <REDACTED_FOR_IP>. Also, things like <REDACTED_FOR_IP> (for nonprofits), etc., might be common.
-   If any domain knowledge can be baked in (like <REDACTED_FOR_IP> or <REDACTED_FOR_IP>), the AI might integrate that.
-   It also means the AI should consider that these users might be less familiar with jargon or have less <REDACTED_FOR_IP>, so the tool should educate as it guides (hand-holding again).

Additionally, addressing these audiences might appear in marketing. For instance, the landing page might have a line: "<REDACTED_FOR_IP> is perfect for nonprofits, researchers, and startups who need to <REDACTED_FOR_IP>." The AI can produce such targeted messaging.

**Extra setting examples:** They might also target small NGOs, individual <REDACTED_FOR_IP> etc., but those might be subsets of nonprofits or researchers. If there were secondary audiences (like <REDACTED_FOR_IP> could use it too), they didn't list them since the focus is on those who **don't** have <REDACTED_FOR_IP>. If expanding, one might consider government entities (less likely, they usually <REDACTED_FOR_IP>). This list is fine. The AI should keep them in mind for examples and content (like any sample content should resonate with those fields).

**AUDIENCE_EXPERIENCE** | ASSUME_NON-TECHNICAL

**Interpretation:** This instructs that we should assume users are **not tech-savvy**. They might be experts in their field (nonprofit management, science, etc.) but not necessarily great with complex software. ASSUME_NON-TECHNICAL means design the UI/UX and features so that even someone with basic computer skills can use it effectively:

-   Minimal setup, no coding or advanced configurations required.
-   Terminology should be plain (avoid tech jargon).
-   The process should be intuitive and possibly analogize to familiar tasks (like filling forms, using Word).
-   Provide guidance at each step, as they did plan (hand-holding).

**Why Useful:** This is crucial to avoid alienating the very users who need help. If the tool were built for tech-savvy users, it might have advanced toggles or require understanding of machine learning or whatever. Instead, all the AI's design and communication should be accessible to someone who maybe only uses Office Suite and email regularly. It also suggests the AI should include more explanations for actions (e.g., if it asks for input, maybe an example or tip with it). From a development perspective, it means heavy focus on ease of use and testing with naive users in mind.

**Extra setting examples:** If the audience had some technical subset, one might allow an EXPERT_MODE toggle. But likely not needed here. Another implicit aspect: assume they might have slow internet or older devices (maybe nonprofits not having latest tech); possibly consider performance on average hardware -- but since it's web and fairly light content, not a big issue. Also, this justifies all the earlier simplicity and clean design choices. They are consistent: know your user's comfort level and tailor everything accordingly.

DEV_URL | <REDACTED_FOR_IP>

**Interpretation:** The **development environment URL** for the project. This is likely where the app is hosted during development/testing. It could be an internal or staging site (<REDACTED_FOR_IP> -- which might be a private domain or a subdomain for dev). The AI might use this for:

-   Configuring environment-specific settings (like allowed CORS origins, redirect URIs for OAuth in dev).
-   When generating links in development context (maybe some automated tests or docs might reference the dev site).
-   Possibly connecting to services (if the dev environment has different endpoints or API base URLs).

It tells that <REDACTED_FOR_IP> is probably the company's domain for internal stuff, and for dev they have a subdomain <REDACTED_FOR_IP>.

**Why Useful:** It helps ensure that environment-specific features (like OAuth callback or analytics endpoints whitelisting) are configured properly. The AI might produce code or documentation that mentions the dev URL for testers. Also, if any environment differences (like using a sandbox API in dev) need to be coded, it knows this URL as dev indicator. Moreover, if the AI is tasked to open the app or test it, it knows where to point (though likely not needed explicitly by the AI, more for config).

**Extra setting examples:** Usually you'd also have a STAGING_URL if there's one, but they might be using dev as staging. They do have a separate prod URL below. Another related setting is ENV variables like so -- but they just give URLs for clarity. Possibly **DEV_PORT** if it runs on a specific port, but since it's fully qualified URL, guess not needed. It's a straightforward mapping: dev environment location.

PROD_URL | <REDACTED_FOR_IP>

**Interpretation:** The **production URL** where the live app will be hosted. It matches the product name domain. This is important for:

-   Configuring production settings (like allowed hosts in Django, CORS origin, OAuth redirect for production).
-   Any user-facing references in documentation or emails should use this URL (the AI should ensure that marketing or help docs mention <REDACTED_FOR_IP> for the live site).
-   It's the domain for the launched app, so the AI might include it in things like the privacy policy or terms (like "<REDACTED_FOR_IP> is accessible at https://<REDACTED_FOR_IP>").

**Why Useful:** It clearly delineates dev vs prod addresses for environment-specific configurations and also ensures the AI uses the correct domain when generating content for real users. For example, if the AI writes an onboarding email template, it should link to <REDACTED_FOR_IP>, not the dev link. Also, it helps in any domain-specific code (like setting cookie domain, or generating links in the app).

**Extra setting examples:** Sometimes, separate **STAGING_URL** or multiple region URLs could exist. They gave just dev and prod which is common. If multi-tenancy or white-label was considered, others would appear, but not relevant here. This also implicitly confirms the product name and domain are final.

ANALYTICS_ENDPOINT | https://data.<REDACTED_FOR_IP>

**Interpretation:** The URL where analytics data is sent. This likely indicates an instance of an analytics service (maybe the "Umami" mentioned later is hosted at data.<REDACTED_FOR_IP>). When the front-end needs to send usage data (page views, events), it will send it to this endpoint. Also, backend might send some if doing server-side tracking.

**Why Useful:** Instead of a third-party like Google Analytics, they have a custom endpoint. The AI must use this when implementing analytics scripts (like pointing the tracker to data.<REDACTED_FOR_IP>). It also indicates that analytics data is perhaps self-hosted (the URL could be pointing to the company's own webserver running Umami or similar). This is aligned with privacy and control. The AI might need to configure environment variables or scripts to use this endpoint. Also, it might come up in the privacy policy text (e.g., "we collect usage data via our analytics at data.<REDACTED_FOR_IP>...").

**Extra setting examples:** If they had different endpoints per environment, a dev analytics vs prod analytics, they'd list both. Possibly data.<REDACTED_FOR_IP> is collecting from dev and prod (or just prod, with dev perhaps disabled or pointed to same?). They didn't specify a dev one, so likely using this for both or not collecting much in dev. Another thing might be an API key or site ID needed by Umami; not listed here, might be secret. The AI just needs to know where to send data. If using something like Google Analytics, they'd provide an ID. Instead, they have an internal endpoint which is better for privacy (ties to privacy stance we'll see).

**USER_STORY** |**As a member of a small team at an NGO,** <REDACTED_FOR_IP>

**Interpretation:** This is a narrative from the perspective of a typical user. It reads: **"As a member of a small team at an NGO,** <REDACTED_FOR_IP>*, so that I can focus on the content and increase my* <REDACTED_FOR_IP>*."*

This user story encapsulates:

-   **User Role/Context:** Small team NGO member (fits one of the target audiences: nonprofits).
-   **Need/Goal:** They lack a professional <REDACTED_FOR_IP> (resource constraint).
-   **What they want:** Quickly <REDACTED_FOR_IP> with AI help.
-   **Benefit:** So they can focus on content (the expertise part) and improve their <REDACTED_FOR_IP>.

It basically justifies the product's existence and guides feature priorities to meet that need.

**Why Useful:** It puts the AI in the user's shoes. For design decisions, the AI can recall this story to ensure the tool addresses these points:

-   **Cannot** <REDACTED_FOR_IP>*:* Means the AI should act as a <REDACTED_FOR_IP>.
-   **Quickly draft and refine:** Emphasize speed and iteration -- features like AI-generated drafts, suggestions, and easy revisions are key.
-   **Focus on content:** So the AI should handle format, structure, wording improvements, leaving the user to supply the core ideas. E.g., the AI might prompt the user for content and then do the heavy lifting to turn it into a polished <REDACTED_FOR_IP>.
-   **Increase chances of** <REDACTED_FOR_IP>*:* Thus, the AI's advice and templates should align with <REDACTED_FOR_IP> (like ensuring all <REDACTED_FOR_IP> are covered, language is persuasive and clear, etc.).

It also underscores the importance of things like templates for <REDACTED_FOR_IP> and an iterative workflow (approve/revise sections as in their feature flow). The AI, designing the UX, will incorporate this story by making sure that a user with that profile (<REDACTED_FOR_IP>) can succeed by using the app.

**Extra setting examples:** They might have had separate user stories for a researcher or startup founder too, but likely they all share similar problem: <REDACTED_FOR_IP>. The NGO one covers the extreme (NGO with no budget). Others like researchers often have to <REDACTED_FOR_IP> anyway, startups similarly. So this story covers general pain point. Possibly, one could add "**As a researcher, I am expert in my field but not in** <REDACTED_FOR_IP>*, ... so I need guidance on framing and structure.*" But probably not necessary. This one story is broad enough.

**TARGET_PLATFORMS** | WEB, MOBILE_WEB

**Interpretation:** The product is targeting **web browsers on desktop and mobile web browsers**. That means:

-   **WEB:** Desktop or laptop access via a browser (Chrome, Firefox, etc.).
-   **MOBILE_WEB:** Access via browsers on mobile devices (responsive design for phones/tablets). They want the web app to be usable on mobile, likely via responsive design (since they mention Tailwind and responsiveness).

No mention of native mobile apps or desktop apps, implying those are not in scope (reinforced by deferred platforms below).

**Why Useful:** This guides technical implementation:

-   The AI knows to ensure responsive design (the UI/UX guidelines hint at reactive, interactive -- probably responsive too).
-   It will not plan features that rely on native app capabilities (like no push notifications unless via web, no heavy offline usage beyond what a web app can do).
-   Testing should consider various screen sizes, and certain design choices (like minimal menus) are probably to make it mobile-friendly as well.
-   Also, performance optimization may consider mobile limits (maybe avoid huge scripts or ensure it works on mobile browsers).

It sets the expectation that everything built should work on a phone's browser as well as desktop. Possibly the AI will incorporate mobile-specific UI tweaks (like a hamburger menu if needed or adapting pagination for small screens).

**Extra setting examples:** If, say, a tablet-specific or PWA approach was important, they might list it, but Mobile_Web covers phones and presumably tablets (just mobile category). Deferred platforms (below) show native apps are postponed. If one were to extend, maybe BROWSER_SUPPORT: last 2 versions of major browsers could be specified, but not here. Or MOBILE_MIN_SCREEN: e.g., design for down to 320px wide implicitly known to front-end dev.

**DEFERRED_PLATFORMS** | SWIFT_APPS_ALL_DEVICES, KOTLIN_APPS_ALL_DEVICES, WINUI_EXECUTABLE

**Interpretation:** These are platforms **not being addressed now, possibly planned for the future** (deferred):

-   **SWIFT_APPS_ALL_DEVICES:** Native iOS apps (for iPhone/iPad). Swift is the language for iOS. "All devices" likely means iPhone, iPad, maybe Mac Catalyst. They are deferring building an iOS app.
-   **KOTLIN_APPS_ALL_DEVICES:** Native Android apps (Kotlin being main language). "All devices" meaning phones, tablets, maybe other Android-based devices. They won't do an Android app now.
-   **WINUI_EXECUTABLE:** A Windows UI native application (executable on Windows OS). Possibly using something like WPF/WinUI or even Electron (though that's web tech).

So, they explicitly state no native mobile or desktop apps in this phase.

**Why Useful:** It clarifies scope for the AI:

-   Don't design things predicated on having a native app's capabilities (like offline local storage beyond what web can, deep OS integrations, etc.).
-   The AI won't allocate time to setting up mobile app frameworks or packaging. Instead it focuses on making the web experience good, which is the immediate target.
-   If the user requests something like "When will there be an iPhone app?" the AI might respond that it's on the roadmap but not current (knowing it's deferred).
-   For architecture, they won't implement things like separate API endpoints for mobile vs web (just one API for both, since only web now).

It's a note likely from planning: we'll consider these later, but not now. So code decisions can assume a browser environment, not needing native code hooks.

**Extra setting examples:** If something like a CLI or integration platform was out of scope, could list it similarly. They listed the major ones. They likely will revisit these after MVP. It's helpful the AI knows this because it might stop it from e.g., generating React Native code or something if ever asked -- it would recall that's deferred. It could also help avoid spending time optimizing for app store guidelines or such.

**I18N-READY** | TRUE, FALSE (here TRUE)

**Interpretation:** The product is built to be **internationalization-ready** from the start. 'TRUE' means the app should support multiple languages or be easily translatable. This involves:

-   Using language files (which they are storing in keys store).
-   No hardcoded text strings in UI; instead using keys so text can be translated.
-   Possibly formatting things like dates/numbers with locale awareness.
-   UI layout accommodating different text lengths.
-   Possibly even design considerations for RTL languages if needed (not explicit, but "I18N-ready" usually implies the basics are in place to later add other locales).

**Why Useful:** Even if they launch in English first (default language given as English US below), being i18n-ready means less rework when adding languages. The AI will ensure any text is externalized (the keys store mentions confirm that approach). It might also consider locale-specific differences (like decimal separators if currency is involved in budgets, etc.). It also aligns with accessibility and user-friendliness if they plan to target NGOs worldwide (nonprofits often global, researchers worldwide). It's critical infrastructure to plan early.

**Extra setting examples:** If false, they'd just do English and worry about translation later (which often leads to technical debt). They wisely set true. A further step could be testing the app with pseudo-localization (like double text lengths) but not needed in config. There's also mention of storing user-facing text in keys store, which ties directly to this setting.

**STORE_USER_FACING_TEXT** | IN_KEYS_STORE

**Interpretation:** This instructs how to handle user-visible text strings in the app. "IN_KEYS_STORE" indicates they will use a keys store (likely a set of localization files mapping keys to text) for all user-facing text. Essentially:

-   All labels, messages, tooltips, etc., are referenced by a key (like welcome_message) and pulled from a locale file rather than hard-coded.
-   The format and location are defined below (YAML, in /locales).

So the AI should ensure any new UI text is added to the keys store rather than directly in JSX/HTML.

**Why Useful:** This is the implementation of I18N-READY. It means translation teams (or AI) can easily add translations without digging through code. It also centralizes all user-visible text, which aids consistency (same wording across the app if reused) and easier editing. For the AI, when generating a new component, it will create keys for any text, place them in en.yaml (for example), and reference them. This also often correlates with tools (like i18next or Django i18n, etc.). It ensures that enabling another language is as simple as adding another keys file.

**Extra setting examples:** Alternatives might be IN_CODE (not recommended, which means text is directly in code), or IN_DATABASE for dynamic text (rare for UI static text). Some frameworks have different approaches, but keys store (especially in YAML/JSON) is common. They even give the format and location next, which is great. If one extended, maybe mention KEY_NAMING_CONVENTION (like prefix by section, e.g., HOME_PAGE.WELCOME). But the team likely has that knowledge implicitly or in design docs.

**KEYS_STORE_FORMAT** | YAML

**Interpretation:** The format for the localization files is YAML (YAML Ain't Markup Language). This means they will likely have files like en.yaml, es.yaml in the /locales directory, containing key: "translated string". YAML is human-readable and commonly used for i18n (especially in frameworks like Rails or some JS i18n libraries).

**Why Useful:** The AI now knows what format to output when creating or updating these files. For example, it will not use JSON or a .po file; it will use YAML syntax. It also suggests that the i18n library or approach chosen expects YAML (maybe they use a library or just custom loading). So the AI will make sure to format keys and nested keys properly in YAML.

**Extra setting examples:** Some projects choose JSON (especially in JS frontends), or PO files (for GNU gettext style). YAML was chosen likely for ease of editing. If they listed multiple, it might be one per environment, but they specifically say YAML (maybe both front and backend can read YAML easily). Another detail might be KEYS_STORE_LANGUAGE_FORMAT (like en-US vs en as file names) but probably just en.yaml suffices for en_US since default.

**KEYS_STORE_LOCATION** | /locales

**Interpretation:** The directory in the project where locale files are stored is /locales. This is presumably at the codebase root (since root is '/'). It tells the AI:

-   When adding new keys or languages, put the YAML files in this folder.
-   Possibly ensure this folder is loaded by the app (the AI might configure path in the i18n library to this location).
-   Also, don't put user-facing text anywhere else; if it sees some leftover text in code, it might move it here.

**Why Useful:** Consistency and organization. The developers (and AI) know exactly where to find and modify texts. It also allows them to easily ship translations (e.g., the CI might check /locales for updates to incorporate them). For the AI, it means if asked "Where can I edit the email templates text?" it might respond "In the /locales files under the appropriate keys."

**Extra setting examples:** Sometimes front-end and back-end have separate locale folders. They might be sharing one if backend is serving static strings to front-end or if it's unified via some build step. Here, one unified /locales directory likely at the root for both front and backend usage (since they plan to generate e.g. PDF with proper headings which might need i18n too, etc.). There's no need to extend further; it's clear. The AI will use it accordingly.

**DEFAULT_LANGUAGE** | ENGLISH_US

**Interpretation:** The default (and currently only, until more added) language is U.S. English. This will be the language used if the user's preference isn't set or if translation isn't available. It likely corresponds to a file like en.yaml. It's also the language the team writes content in by default.

**Why Useful:** The AI will assume strings in the keys store correspond to English (US). Also, any content generation, if not otherwise specified, should be in English US. It matters for things like spelling (organize vs organise, etc.), date formatting defaults (MM/DD/YYYY in US typically, though for grants maybe they use a standard format). It might also be used in code to set LANG=en_US initial state or in UI for default selection. For testing, the AI may rely on English content existence.

**Extra setting examples:** If they had multiple languages, they might list them. They could have indicated a fallback chain or that US English is the one always present. Usually DEFAULT_LANGUAGE: en is common. They wrote ENGLISH_US fully spelled, which might just be for clarity in config. The keys store might use a code like 'en' or 'en-US'. The AI should be mindful if any library expects a code (maybe they map ENGLISH_US to 'en-US'). But likely this is just human-readable. Not much else needed here.

**FRONTEND_BACKEND_SPLIT** | TRUE, FALSE (here TRUE)

**Interpretation:** This confirms that the project has a **separate front-end and back-end**. 'TRUE' means they are distinct (which is consistent with using React for front-end and Django for backend in the stack). This implies:

-   The front-end (React) is decoupled, likely consuming an API from the back-end.
-   There might be separate repositories or at least separate directories (but given one codebase root, maybe not separate repos; could be one repo with a frontend folder and a backend folder).
-   They probably deploy them as separate services or at least build processes (with a bundler for React and say Gunicorn for Django).

**Why Useful:** It sets expectations for how code is organized and how the AI should handle tasks:

-   If adding a feature, the AI may need to make changes in both front-end and back-end (like adding an API endpoint in Django and then calling it from React).
-   For things like i18n, it might mean both front-end and backend need translations (though could share locale files).
-   It ensures the AI doesn't try to embed backend code in frontend or vice versa (like no server-side rendering of React through Django templates; instead, use an API approach).
-   Also informs deployment (like they might create separate Docker images for each).

In documentation or responses, the AI can clarify front-end vs back-end aspects (useful for the development team or if asked technical stuff by user). It also means each side can be developed somewhat independently.

**Extra setting examples:** If false (monolithic), the AI might use server-side templates and combined code. But they chose modern split. Additional details could include how they communicate (likely REST or maybe GraphQL, but given stack possibly REST). They did mention RAG search etc. as part of backend likely. The AI might implicitly know to use Django REST Framework or similar. The config doesn't state but FRONEND_BACKEND_SPLIT: TRUE suggests they indeed treat client and server separately.

**STYLING_STRATEGY** | DEFER_UNTIL_BACKEND_STABLE, WIRE_INTO_BACKEND

**Interpretation:** This describes the approach to implementing UI styling:

-   **DEFER_UNTIL_BACKEND_STABLE:** They plan to postpone elaborate styling work until the backend (and core functionality) is stable. This likely means right now they focus on functionality and basic UI structure; fine-tuning CSS, design polish, or complex styling will be done later in the development cycle once the app's features are in place.
-   **WIRE_INTO_BACKEND:** Possibly means that when they do style, they will integrate it alongside backend integration. Perhaps as they implement features end-to-end, they'll add just enough style to make the feature testable. It might also imply using the backend data to influence styling (like dynamic classes based on state).

Combining them: "Don't spend too much time on perfecting the CSS until the backend logic is mostly done, but do ensure the styling is connected to backend-driven states/elements." In other words, get the app working first, then make it pretty, but keep in mind how styling will hook in (like CSS frameworks or component structure might align with backend templates if any, etc.).

**Why Useful:** It prioritizes resources. Early dev should concentrate on making the app functional (especially AI features and flows). Overdoing styling too early can be wasted effort if UI changes as features evolve. This strategy ensures the product is working (for internal testing) even if it looks minimal, and heavy design polish comes once features won't drastically change positions or flows. It's pragmatic: you don't want to re-do CSS repeatedly due to layout changes from feature iterations. "Wire into backend" might also suggest ensuring that any UI elements get their data from backend properly before styling them, i.e., make sure the pipeline works then style the output.

**Extra setting examples:** Other strategies might be STYLE_IN_PARALLEL (some teams do front-end styling as they go), or USE_BOOTSTRAP_FOR_NOW (quick fix styling) then custom later. They effectively say minimal styling now, focus on function. The AI will likely produce basic styling (maybe using Tailwind minimal classes because that's easy to adjust later) but not aim for pixel-perfect design until told. It might also not worry if things look plain as long as they work -- which fits an internal pre-release scenario.

**STYLING_DURING_DEV** | MINIMAL_ESSENTIAL_FOR_DEBUG_ONLY

**Interpretation:** During development, they will apply only the **bare minimum styling necessary to debug and use the app**, not full design. "Minimal essential for debug" likely means:

-   Just enough CSS so that elements aren't jumbled or unusable. For instance, maybe put things in some reasonable layout or add spacing so you can see fields distinctly, but not worry about the final look.
-   Possibly use default styling of components or quick utility classes to arrange things, but skip all beautification.
-   It implies no spending time on theming, fine colors, typography beyond what's needed to ensure you can read stuff while testing.

So developers (and AI) should not focus on how pretty it is right now -- just ensure it's functional and roughly legible/usable for testing.

**Why Useful:** It aligns with the above strategy: confirm function, then style. For the AI, it means when coding front-end now, use maybe simple Tailwind classes to layout, maybe some basic form controls, but don't implement the full visual design or responsiveness beyond what's needed to test logic. The phrase "for debug only" suggests the styling is just to make it easier to see and debug features (like highlight sections or show boundaries) -- things that might even be removed or changed in final styling pass. It prevents waste of time and ensures developers don't get distracted by aesthetics when core features might still be broken.

**Extra setting examples:** They could have said "no styling" but that might hamper usability even for testers. Minimal essential is a good compromise. Once backend stable, as above, they can invest in making it look polished. Another potential strategy not listed might be using a simple component library as placeholder then customizing later, but they specifically mention minimal debug styling (which could include using Tailwind as needed). The AI will thus not propose elaborate CSS solutions now.

[CORE_FEATURE_FLOWS] -- Key Features and User Journey
====================================================

**This section outlines the main capabilities of the product and the typical user journey from start to finish. It also delves into how the AI-assisted writing process works behind the scenes, step by step. Understanding these flows is crucial for the AI to implement and maintain the right features in the right sequence.**

**KEY_FEATURES** | AI_ASSISTED_WRITING, SECTION_BY_SECTION_GUIDANCE, EXPORT_TO_DOCX_PDF, <REDACTED_FOR_IP>, AGENTIC_WEB_SEARCH_FOR_<REDACTED_FOR_IP>_TO_DESIGN_NEW_TEMPLATES, COLLABORATION_TOOLS

**Interpretation:** This is a list of the product's **headline features**:

-   **AI Assisted Writing:** The core feature where AI helps generate or improve the <REDACTED_FOR_IP>.
-   **Section-by-Section Guidance:** The process is broken into sections (like <REDACTED_FOR_IP>) and the tool guides the user through each with targeted advice/questions. The AI likely provides prompts or checks per section.
-   **Export to DOCX/PDF:** Ability to export the <REDACTED_FOR_IP> in Word (.docx) and PDF formats (and .md as mentioned elsewhere). This is crucial for <REDACTED_FOR_IP> output.
-   **Templates for** <REDACTED_FOR_IP>**:** A library of templates for frequently encountered <REDACTED_FOR_IP> (like maybe <REDACTED_FOR_IP>), so the structure is already laid out.
-   **Agentic Web Search for** <REDACTED_FOR_IP> **to Design New Templates:** If the user wants to <REDACTED_FOR_IP> the system has no template for, the AI can autonomously search the web and use that info to create a new template. This is a very advanced feature leveraging the AI's web browsing and RAG abilities.
-   **Collaboration Tools:** Features that allow multiple users to work together, like sharing the <REDACTED_FOR_IP>, commenting, version history, etc. This might be partly premium (given user journey mentions upgrade for collaboration).

**Why Useful:** Listing these focuses development efforts and informs any additional decisions:

-   The AI knows to allocate resources to implementing each of these thoroughly (these define project scope).
-   It can cross-reference user requests to these features (e.g., if user asks "How do I collaborate with a colleague?" it knows there's a collaboration feature to talk about).
-   It also highlights what should be highlighted in UI and docs as selling points.
-   For testing, each of these needs distinct test cases (e.g., ensure exports are correctly formatted, ensure templates load, ensure search for templates works).
-   It also implies needed integrations: e.g., for PDF export, likely using a library; for DOCX, maybe docx generation library; for web search, probably using the Web Scraping model with Gemini as listed in [STACK].
-   Additionally, "section-by-section guidance" ties into the user journey, so the AI will maintain that structure in the UI (like a sidebar or wizard indicating each section).

**Extra setting examples:** Over time, new features could be appended to this list as they develop (like maybe "Version Control" specifically, or "Analytics Dashboard for <REDACTED_FOR_IP> success rate" etc. Not currently listed, so not core). This list is basically the selling points. For the AI's tasks, it ensures no core feature is neglected in planning code or writing docs. If anything, each of these will have associated modules and perhaps domain models (like Template model, Collaboration model for invites/roles, etc.). The AI has to remember all these moving parts in development.

**USER_JOURNEY** |**(lists the sequence: Sign up -> Create org or join -> Create project -> Q&A per section -> save snippets -> AI draft section -> review -> repeat -> export -> upgrade for more)**

**Interpretation:** This array outlines the **step-by-step journey a typical user will take using the app**:

1.  **Sign up for a free account.**
2.  **Create new organization or join existing with invite key:** Users either start an org (maybe representing their NGO or team) or join a team if invited.
3.  **Create a new** <REDACTED_FOR_IP> **project.**
4.  **Answer one question per section about the project**, with scope to specific <REDACTED_FOR_IP> requirement, via text or uploading files (like maybe attaching a <REDACTED_FOR_IP>).
5.  **Optionally save text answer as snippet:** So if they wrote a good answer, they can save it for reuse (like a content library for common answers).
6.  **Let AI draft that section of the** <REDACTED_FOR_IP> **based on my inputs.**
7.  **Review section, approve or ask for revision with note.**
8.  **Repeat until all sections complete.** (Each section goes through Q&A input, AI draft, review cycle.)
9.  **Export the final** <REDACTED_FOR_IP>, perfectly formatted PDF, with .docx and .md also available.
10. **Upgrade to a paid plan** for extra features like collaboration, versioning, higher usage caps, etc., if they want those.

This is essentially the entire workflow from onboarding to output.

**Why Useful:** It gives the AI a clear blueprint of how users move through the app, which informs:

-   UI flow: e.g., after signup, prompt org creation or joining (maybe via an invite code input).
-   Data models: likely have User, Organization, Project, Section, Snippet, etc.
-   The order to implement things: core flows revolve around this sequence.
-   It aligns with the Key Features list: Q&A per section (guidance), AI drafting (AI assist), snippet library, export, collaboration (post-MVP via upgrade).
-   The AI can simulate this flow when testing or explaining the app.
-   It highlights places where the AI's involvement is heavy (AI drafts in step 6, AI likely also does format and maybe ensures completeness).
-   It also shows where user decisions happen (approve/revise loop).

For documentation or support answers, the AI can use this as the canonical "How to use <REDACTED_FOR_IP>" flow. For development, it ensures no step is forgotten (like they explicitly mention invite keys for orgs, so the AI will include features for generating and accepting invites). The upgrade step at the end reminds that the free user can still complete one proposal, but to get more they might need to pay -- so the AI might implement checks like limit of 1 active project on free and then gating creation behind upgrade.

**Extra setting examples:** This user journey is quite comprehensive. If one wrote alternate flows (like if already invited, skip sign up directly join org, etc.), but they cover join existing with key at step 2. They might also later add "Submit to <REDACTED_FOR_IP>" or similar, but that's outside scope currently (just export final docs). The journey ends at upgrade, implying after making one <REDACTED_FOR_IP> perhaps they foresee user wanting to do more and needing premium (smart for monetization).

**WRITING_TECHNICAL_INTERACTION** |**(very detailed list of internal AI tasks during the proposal writing process)**

**Interpretation:** This is an in-depth, behind-the-scenes look at how the AI interacts and what it should technically do during the writing flow:

-   **Before create, ensure role-based access, plan caps, paywalls, etc.:** When starting a project, check user permissions (free vs paid, etc.), ensure they haven't exceeded their allowed number of projects, enforce paywall restrictions. Also, presumably set up any needed roles (like making the creator an admin of the project/org).
-   **On user URL input to create** <REDACTED_FOR_IP>**, do semantic search for RAG-stored templates and samples:** If the user provides a link (maybe the <REDACTED_FOR_IP> or PDF URL) when creating the project, use semantic search in the Retrieval-Augmented Generation store to see if a template or sample for that <REDACTED_FOR_IP> exists.
-   **If found, cache and use to determine sections and headings only:** If a relevant template or sample is found in our database, use it to outline the <REDACTED_FOR_IP> (the sections needed, their titles) for this project. Essentially, copy the structure.
-   **If not found, use agentic web search to find** <REDACTED_FOR_IP> **templates and samples, design new template, store in RAG with keywords (org,** <REDACTED_FOR_IP>**, official or sample, etc.) for future use:** This is the advanced feature where the AI proactively goes online to find info. If no known template, the AI will search the web (maybe using Gemini model or browsing) for the <REDACTED_FOR_IP>, find an official <REDACTED_FOR_IP>. From that info, it will deduce the required sections and create a new template. Then it saves it in the RAG store for next time, tagging it with metadata (<REDACTED_FOR_IP>, etc., whether it's an official template or just a sample we scraped).
-   **When SECTIONS_DETERMINED, prepare list of questions to collect all relevant information, bind questions to specific sections:** Once sections (like <REDACTED_FOR_IP>, etc.) are known, the AI generates guiding questions for each section to ask the user. Each question is mapped to a section (the section it will help draft).
-   **If user non-text answer (like PDF or image upload), employ OCR to extract key info:** So if the user uploads a document (maybe a project description PDF or a scanned letter), use OCR (Tesseract configured earlier) to get text from it, so it can be used by AI in drafting.
-   **Check for user's latest uploads, frequently used files, or saved answer snippets. If found, allow user to access with simple UI elements per question:** This means the system should help the user reuse content -- e.g., if a question is "Describe your organization", and the user has an uploaded org profile or a snippet for org description from before, the UI might show a button to insert that snippet or attach that file's content. It's about convenience and not making user retype things they already provided.
-   **For each question, PLANNING_MODEL determines if clarification is necessary and injects follow-up question. When information sufficient, prompt AI with bound section + user answers + relevant text-only section samples from RAG:** Here the AI has a Planning Model (GPT-5 as set) that monitors the Q&A. If the user's answer is incomplete or ambiguous, the AI might ask a follow-up to get clarification. It continues until it judges it has enough input for that section. Then it calls the Writing AI (GPT-5 writing model) giving it: the section context (like "This section is <REDACTED_FOR_IP>"), the user's answers for that section, and any example text from similar sections in other <REDACTED_FOR_IP> (samples or template content from the RAG store) to guide style or content. The writing model then drafts the section text.
-   **When exporting, convert JSONB proposal to canonical markdown, then to .docx and PDF using deterministic conversion library:** So they store the <REDACTED_FOR_IP> content in the database as structured data (likely JSONB fields per section). At export, they first generate a full markdown of the <REDACTED_FOR_IP> in a standardized way (which may incorporate formatting or placeholder for styling), then use a library to turn that markdown into .docx and PDF. "Deterministic conversion library" suggests something like Pandoc or a specialized markdown-to-docx/PDF library which ensures consistent formatting (like correct headings, bullet points, etc.). This approach helps maintain fidelity and styling control.
-   **VALIDATION_MODEL ensures text-only information is complete and aligned with** <REDACTED_FOR_IP>**, prompts user if not:** After drafting (or maybe at the end of all sections), a Validation AI (GPT-5 also configured) reviews the text to check if it addresses all requirements of the <REDACTED_FOR_IP> (if we have that data from template or scraped info) and if there are any obvious gaps. If it finds an issue (like one section didn't mention something required), it will alert the user to add that info. Possibly by highlighting or asking a question to fill missing piece.
-   **FORMATTING_MODEL polishes text for grammar, clarity, and conciseness, designs PDF layout to align with template/samples. If official template, ensure required sections present and labeled correctly:** The Formatting model (also GPT-5) then takes the raw content and refines it (like an AI copy-editor). It might adjust tone, fix grammar. It also possibly suggests formatting choices (though actual PDF layout is done by library, the model might ensure that if template says "Max 500 words", the text is concise, etc., or if headings need exact wording, they match). If the template is official (from funder), the model double-checks all those sections are included and named exactly as required (maybe the RAG template provides official wording).
-   **User is presented with final view, containing formatted PDF preview. User can change to text-only view:** After formatting, the user gets a preview of the final PDF (likely rendered on screen). They also can toggle to a plain text view (maybe to copy-paste or to see it without layout for easy editing). This way they can inspect the final output and make any last changes if needed.
-   **User may export file as PDF, docx, or md at any time:** Even mid-process, user can export partial result, but primarily at end they have options to download.
-   **File remains saved to ACTIVE_ORG_ID with USER as PRIMARY_AUTHOR for later exporting or editing:** The finished <REDACTED_FOR_IP> (and presumably intermediate versions or the state) is saved in the system. It's associated with the org (so others in org can view if they have access, linking to collaboration feature) and the user who created it is marked as primary author. It allows revisiting the project, editing further, or exporting again. They keep it persistent (not one-time generation).

**Why Useful:** This is essentially a blueprint for implementing the AI's logic. It ensures all the complex interactions between user input and AI actions are accounted for. For the AI developer:

-   They must implement each of these steps or at least plan for them (some might be future enhancements like the web search if not immediately).
-   It enumerates models needed (Planning model, Writing model, Validation model, Formatting model) and how they interplay with RAG store and user data.
-   It identifies features to build: OCR integration, snippet management, the follow-up question logic, etc.
-   It also assures quality by including validation and formatting steps -- these are like QA passes for content.
-   It clarifies data flow: user input -> stored (maybe in JSONB) -> models work -> output assembled.
-   The AI will use this when writing code to ensure, for example, after user answers a question, to call planning model and maybe loop if clarification needed.

For user experience, it means the app will actively assist, not just passively take what user gives. It's a high-end intelligent workflow. For testing, each step is a point to verify:

-   Template fetching works,
-   Web search triggers and yields good template when needed,
-   Questions cover all needed info,
-   The draft quality is good and validation catches omissions,
-   The formatting is correct.

**Extra setting examples:** It's hard to add more because this is very comprehensive. They basically described an AI orchestration pipeline. If one were to extend, maybe mention what happens if user requests a revision in step 7 (likely goes back to draft generation for that section with a note influencing the model). Or how versioning ties in (they said keep last 5 versions below). But it's likely assumed revision cycles just produce new text that overwrites until final, and version snapshots are saved aside.

This technical flow is probably the heart of the innovation in the product. The AI reading it will treat it as gospel for building the assisted writing feature.

**AI_METRICS_LOGGED** | PER_CALL

**Interpretation:** They plan to log AI usage metrics for every call to an AI model. "PER_CALL" suggests each time an AI model (like GPT-5 or Gemini) is invoked, they will log data about that call (prompt size, response size, time, etc.). It's not aggregated by session or daily -- it's each call individually.

**Why Useful:** This helps track usage for billing (if using paid APIs), performance monitoring, and debugging. It might also be needed because they want to refine the process (see which calls are heavy, etc.). For the AI, this means implement logging at each model invocation point (like before/after calling OpenAI API, record the metrics). It ties with content next (they list what content). It's also good for transparency if needed or analytics how much each user uses. They might have a DB table for AI call logs.

**Extra setting examples:** If it were "AGGREGATED", they might log summaries per session or user, but they want detail. Another option could be OFF if they didn't care (but they clearly do). Also they specify exactly what to log next, which is good for the AI to implement.

**AI_METRICS_LOG_CONTENT** | TOKENS, DURATION, MODEL, USER, ACTIVE_ORG, <REDACTED_FOR_IP>, SECTION_ID, RESPONSE_SUMMARY

**Interpretation:** These are the fields to log for each AI model call:

-   **TOKENS:** Number of tokens used (likely prompt + answer tokens). They'll either get this from API or measure.
-   **DURATION:** Time taken for the call.
-   **MODEL:** Which model was used (GPT-5, Gemini-2.5-Pro, etc.).
-   **USER:** Which user triggered it (for usage tracking).
-   **ACTIVE_ORG:** The organization context (if any) when call was made (could be useful to aggregate usage by org).
-   <REDACTED_FOR_IP>**:** Which <REDACTED_FOR_IP> project the call is associated with (so they can see how many AI calls per project, maybe to gauge complexity or cost per <REDACTED_FOR_IP>).
-   **SECTION_ID:** If applicable, which section of the <REDACTED_FOR_IP> it was for (to pinpoint maybe that one section took many retries, etc.).
-   **RESPONSE_PRIVACY_CONSCIOUS_SUMMARY:** A short privacy-aware summary of the AI's response. Possibly not the full text (which could be lengthy and sensitive), but a brief (maybe first N characters or a classification like "Draft text for Introduction section"). This summary might help in analyzing what type of output was generated without storing full data (which might be sensitive PII or user content).

**Why Useful:** It outlines exactly what the AI should implement in logging. For example, after an AI completes a draft for a section, the system should log these. It ensures they collect useful analytics:

-   They can calculate cost by tokens,
-   See latency by duration,
-   Identify heavy usage by model or user,
-   Possibly see if certain sections cause more calls or longer responses,
-   Have a high-level audit of content type via summary (for debugging or improving). It also could help in optimizing (if one model often returns long responses, maybe tune prompts to cut tokens). This also aligns with privacy guidelines by not storing full content, just summary. They might keep summary for quick eyeballing without exposing entire user text.
-   **Extra setting examples:** They might also log error flags or if the model call was a retry. But they've covered the main elements. Possibly PROMPT_SIZE vs RESPONSE_SIZE separately, but "tokens" might cover total or prompt vs completion if they track that. Or CALL_TYPE (like draft vs validation call), but they can infer from context (like section ID + model might tell it's a draft or validation stage). Overall, comprehensive.

**SAVE_STATE** | AFTER_EACH_INTERACTION

**Interpretation:** The system will save the application state (<REDACTED_FOR_IP> data, answers, etc.) after each user interaction (likely meaning after each answer or action user takes). "Each interaction" could be each step in the Q&A or each section draft or every button click that changes something significant. Essentially, frequently and promptly saving:

-   e.g., after user answers a question, save that answer to DB immediately.
-   after AI generates a draft, save it.
-   after user marks a section done, save.

This is basically auto-save after each step.

**Why Useful:** Prevents data loss and allows the resume functionality. If a user disconnects or closes browser, they won't lose progress beyond the last answered question or last action. It also helps versioning (they keep last 5 versions, so saving often provides those snapshots). For the AI, it means implement a robust auto-save mechanism on frontend and appropriate API calls to persist data at every step. The user should never have to click "Save", it's implicit.

**Extra setting examples:** Sometimes there's also a manual save option or a different frequency (like real-time keystroke saving). But they chose after each interaction which is a natural granularity (makes sense with the structured Q&A flow). They likely won't implement a "Save" button at all. If extended, maybe also save intermediate content in localStorage as backup, but with constant server save maybe not needed.

**VERSIONING** | KEEP_LAST_5_VERSIONS

**Interpretation:** The system will maintain the last 5 versions of the <REDACTED_FOR_IP>. So as the user makes changes (maybe significant changes or completes certain milestones), the previous states are stored up to 5 iterations. The 6th older one would get dropped once a new one is saved (rolling history).

-   Likely each "version" could be each time all sections are completed or perhaps after each major revision. But since they auto-save every interaction, they might version at section approval points or something, to avoid a new version every small step. Possibly "version" might be defined as each time the user completes the whole <REDACTED_FOR_IP> or exports, but "keep last 5 versions" suggests maybe they let the user manually create version snapshots (like user can click "Save Version" and it holds up to 5) or they automatically snapshot at key points (like before applying an AI revision or something).

**Why Useful:** Provides an undo safety net and an ability to recover older content (which is also part of collaboration maybe -- version history). It aligns with typical doc editors. For AI specifically, if a user tries an AI re-write and doesn't like it, having the previous version is crucial. The AI or system will need to implement storage of versions (maybe a table for versions with timestamp).

**Extra setting examples:** Some might keep all versions (but that can be storage heavy, though text isn't huge). They chose 5, likely balancing space and usefulness. Possibly they think if needed beyond 5, user can copy out or so. Another policy could be KEEP_EVERY_VERSION_WITH_LIMITS (like store all but maybe compress older ones or something). But 5 is fine. There's no interface detail here but presumably an interface to view/revert to a version might exist. The AI should ensure that when saving new states, it rotates versions beyond 5.

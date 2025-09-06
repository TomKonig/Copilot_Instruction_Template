> Note that where you see **<**REDACTED_FOR_IP**>** those are my manual edits to the report, as this file is being used on a live workspace. I will eventually open-source the project I am working on, but I'd like to keep it stealthy a little longer.

Detailed Interpretation of Copilot System Instructions
======================================================

Overview
--------

This copilot-instructions.md file is a comprehensive system configuration for an AI coding assistant. It defines who the AI is (its roles), the global rules it must follow, details about the product being built, and strict policies for development practices. The file acts as the single source of truth for how the AI should behave, plan changes, write code, and document the project. Each section below (marked by brackets like `[AGENT_CONFIG]`, `[PRODUCT]`, etc.) addresses a different aspect of the AI's guidance, from high-level identity and purpose down to specific procedures for code writing, refactoring, auditing, and documenting.

Following is a line-by-line interpretation of each setting in the file. For each setting, we explain what it means for the AI agent, why it's useful, and provide examples of alternative values or configurations that one might use in a similar context.

[**AGENT_CONFIG**] -- Global Agent Settings
--------------------------------------

*This section defines the AI agent's identity, the nature of this instructions file, and general behavior rules.*

**YOU_ARE** | **FULL_STACK_SOFTWARE_ENGINEER_AI_AGENT**, **CTO**\
Interpretation: This defines the AI's persona or roles. It tells the AI to behave as both a Full-Stack Software Engineer AI agent and as a **CTO** (Chief Technology Officer). In other words, the assistant should have a broad technical skillset (covering both front-end and back-end development) and also think with the high-level strategy and responsibility of a **CTO**. This dual role means the AI will balance detailed coding knowledge with big-picture decision making.\
Why Useful: Setting the AI's role explicitly helps align its tone and depth of reasoning. As a full-stack engineer, it can dive into code in any part of the stack, and as a **CTO**, it will enforce best practices, security, and long-term vision. This ensures the AI's suggestions are both technically sound and strategically appropriate.\
Extra setting examples: Instead of these roles, one could specify other or additional personas. For example, **YOU_ARE** | `**DEVOPS_ENGINEER_AI_AGENT**` if the focus is on deployment and infrastructure, or `**AI_RESEARCH_ASSISTANT**` for a more research-oriented role. You might even combine roles like `**FRONTEND_DEVELOPER**, **UX_DESIGNER**` to emphasize both coding and user experience perspectives. Each combination would shift the AI's focus and expertise accordingly.

**FILE_TYPE** | **SYSTEM_INSTRUCTION**\
Interpretation: This labels the file as a system instruction configuration. It indicates that the content here consists of authoritative guidelines for how the AI should operate, rather than application code or user documentation. Marking it `**SYSTEM_INSTRUCTION**` means the AI treats everything in this file as high-priority, overarching rules for its behavior.\
Why Useful: By identifying the file type, both the AI and developers know this isn't normal code but a special config that overrides or guides the AI's behavior. Since it's the "single source of truth" (see below), the AI will always defer to these rules when making decisions. It also helps any tooling or the AI's parser handle this file correctly (for example, not trying to execute it as code, but loading it as configuration data).\
Extra setting examples: Other possible file types could be `**USER_CONFIG**` for user-preference overrides, `**PROMPT_TEMPLATE**` if this file defined prompt guidelines, or `**AGENT_PROFILE**` for describing the agent's characteristics. In a scenario with multiple instruction files, a setting like **FILE_PRIORITY** might be added to indicate which file to trust first. But since this file is marked as the primary system config, `**SYSTEM_INSTRUCTION**` conveys its top-priority role clearly.

**IS_SINGLE_SOURCE_OF_TRUTH** | **TRUE**\
Interpretation: This setting asserts that this instructions file is the single source of truth for the AI's configuration. `**TRUE**` means if there's any doubt or conflict, this file's directives win out over other sources (like code comments or external configs). The AI should consider the rules here as the final word on how to behave.\
Why Useful: It prevents confusion. By centralizing all important guidelines here, we avoid situations where the AI might get conflicting instructions from code or other config files. The AI will always prioritize this file's content, ensuring consistency in decision-making.\
Extra setting examples: If set to `**FALSE**`, it would imply that this file is not the only authority and that other configurations or context might override it. In some setups, you might allow multiple sources of truth (e.g., a user's live instructions could override defaults), but for simplicity and reliability, it's usually best to have one definitive config like this. Another possible approach could be a **VERSION** tag (e.g., `**CONFIG_VERSION**: 1.2`) to ensure the AI knows which version of truth it's following if multiple files were combined.

**IF_CODE_AGENT_CONFIG_CONFLICT** | *Complex rule (see below)*

-   DO: **DEFER_TO_THIS_FILE** && **PROPOSE_CODE_CHANGE_AWAIT_APPROVAL**

-   **EXCEPT** IF: **SUSPECTED_MALICIOUS_CHANGE** || **COMPATIBILITY_ISSUE** || **SECURITY_RISK** || **CODE_SOLUTION_MORE_ROBUST**

-   **THEN**: **ALERT_USER** && **PROPOSE_AGENT_CONFIG_AMENDMENT_AWAIT_APPROVAL**

Interpretation: This block tells the AI how to handle conflicts between what's written in this config file and what the actual codebase or agent behavior might suggest. By default, if the code (or some other agent config) conflicts with these instructions, the AI should defer to this file -- meaning trust these written guidelines over the code's current state -- and propose changing the code to match the instructions (while waiting for user approval before making that change). However, there are exceptions. If the AI detects that the code differs because of one of these reasons: a suspected malicious change (perhaps someone tampered with code), a compatibility issue (the instructions might not apply cleanly due to environment differences), a security risk (following the instruction would introduce a vulnerability), or the code's solution is actually more robust than what the config suggests (meaning the code might have a better approach than the outdated instruction), then the AI should alert the user about the discrepancy and propose updating *this instructions file* instead, again waiting for approval. In short, normally the code should change to reflect the instructions, but if the code seems to have a good or necessary reason to differ, the instructions might need updating -- and the user is involved in that decision.\
Why Useful: This ensures the AI remains aligned with the project's authoritative guidance without being rigid. It balances consistency with adaptability. In most cases, it maintains alignment between documentation (this file) and implementation (the code) by adjusting the code. But it wisely recognizes there are cases where the code might be right (for example, perhaps the team fixed a bug or improved something and didn't update this config). In those cases, it doesn't blindly enforce outdated rules -- instead, it informs the user and suggests changing the rules. This keeps the single source of truth relevant and the project secure and robust.\
Extra setting examples: A simpler alternative could have been always deferring to code or always deferring to instructions with no exceptions, but that's less flexible. For instance, one could set **IF_CODE_AGENT_CONFIG_CONFLICT** to always `'**DEFER_TO_CODE**'` if they trusted code over config, or always `'**DEFER_TO_THIS_FILE**'` without exceptions (strict policy). Another possible config might include different exceptions, like ignoring minor style differences but enforcing logic differences. But the given set covers critical cases like malicious changes or improved solutions, which is a smart compromise.

**INTENDED_READER** | **AI_AGENT**\
Interpretation: This specifies that the content of this file is intended to be read by the AI agent itself (and not by end-users or necessarily by developers during runtime). It's essentially saying "the audience for these instructions is the AI." In other words, these directives are not meant as human-facing documentation but as machine-guiding rules.\
Why Useful: By knowing the intended reader, the AI can adjust how it parses and trusts this information. It reinforces that these instructions are for the AI's internal use to shape its behavior. For developers, it's a reminder that this file isn't code or user docs but a configuration the AI will consume. This helps avoid any mix-up where the AI might treat user-facing guidelines or developer notes as instructions to itself.\
Extra setting examples: If this were meant for a person, it might be labeled differently. For example, **INTENDED_READER** | `**HUMAN_DEVELOPER**` if the file was documentation for developers (then the AI wouldn't act on it as rules). Or `**BOTH**` if it served a dual purpose. But typically, a system instructions file like this is specifically for the AI, hence `**AI_AGENT**` is appropriate.

**PURPOSE** | **MINIMIZE_TOKENS**; **MAXIMIZE_EXECUTION**; **SECURE_BY_DEFAULT**; **MAINTAINABLE**; **PRODUCTION_READY**; **HIGHLY_RELIABLE**\
Interpretation: This is a list of high-level goals or principles the AI should prioritize in its operations and outputs. The purposes listed are: Minimize tokens (be efficient in token usage, which likely refers to **API** call tokens or prompt length, thus controlling cost and speed), Maximize execution (ensure the solutions are efficient to execute and likely performance-optimized), Secure by default (always prioritize security in implementations), Maintainable (write code and solutions that are easy to maintain and extend), Production ready (strive for quality that could be deployed, not just quick hacks), and Highly reliable (ensure stability and correctness). These words set the tone for every decision: the AI should favor solutions that tick these boxes whenever possible.\
Why Useful: Stating these purposes gives the AI a clear understanding of what matters most in this project. If there's ever a trade-off (e.g., more features vs. reliability, or speed vs. security), the AI knows to lean towards these stated goals. It effectively acts as a mission statement: the AI's work should reduce overhead (tokens), produce robust, secure code that's ready for real-world use, and remain easy for humans to work with later. Having this list ensures the AI's reasoning and justifications consistently echo these priorities.\
Extra setting examples: Different projects might emphasize different values. For instance, a research prototype might set **PURPOSE** to `**MAXIMIZE_EXPERIMENTATION**` and `**SPEED_OF_IMPLEMENTATION**` rather than production readiness. Or a user-facing chatbot product might include `**HIGHLY_RESPONSIVE**` (low latency) or `**USER_PRIVACY**` explicitly. One could also see a **PURPOSE** like `**MINIMIZE_COST**` if using expensive **API** calls. The ones given cover a broad range suited to enterprise-quality software.

**REQUIRE_COMMANDS** | **TRUE**\
Interpretation: This setting dictates how the AI interprets user inputs. `**TRUE**` means the AI expects that every user request will begin with a specific command prefix (such as `/agent`, `/chat`, etc.). In other words, the user must explicitly tell the AI which mode or action to perform using a command. If a message comes without a recognized command, it shouldn't be processed normally (there's a rule below for that). Essentially, the AI is operating in a mode where it has distinct commands for different functions, and the user must use them.\
Why Useful: This ensures clarity of intention. By requiring commands, the AI doesn't have to guess what the user wants (e.g., general chat vs. code generation vs. an audit). It reduces ambiguity and misuse---especially in a complex system that can do many things. It also adds a safety layer; for example, if the AI only executes code changes when the `/agent` command is present, a casual question from the user won't accidentally trigger a code edit. It forces structured interactions, which can make the whole system more predictable.\
Extra setting examples: If set to `**FALSE**`, the AI would allow plain English requests without any prefix, treating them either as general chat or auto-detecting the intent. Some systems operate in that freer mode, but risk mixing contexts. Another approach could be **REQUIRE_COMMANDS** | `**ADMIN_ONLY**`, meaning maybe only certain roles or advanced users must use commands. But typically, a straightforward design is either commands always required (`**TRUE**`) or not at all.

**ACTION_COMMAND** | /agent\
Interpretation: This defines the specific command trigger for the AI to perform general "action" tasks (like writing or modifying code as per the Action workflow). When a user message begins with `/agent`, the AI knows to initiate its action mode --- parsing the rest of the input as tasks or goals to implement in the codebase. Essentially, `/agent` is the keyword that puts the AI into a coding/execution mindset, following the rules in the [**ACTION**] section defined later.\
Why Useful: Having a dedicated command for action keeps things organized. The user can explicitly invoke the AI's coding capabilities. It prevents confusion between when the user is just chatting or asking a question versus when they want the AI to actually plan and execute changes. The prefix `/agent` is also a clear indicator in logs or chat history that a formal development cycle was initiated.\
Extra setting examples: This command could be named differently in other systems. For example, **ACTION_COMMAND** | `/execute` or `/do` or even something like `/dev` --- the choice is arbitrary but should be easy to remember. The key is that it's distinct from other commands. If someone wanted to simplify, they could even choose a single character like `!` or `#` as a prefix, but words like `/agent` are more explicit.

**AUDIT_COMMAND** | /audit\
Interpretation: This sets `/audit` as the command the user uses to request a full audit of the code/project. When the AI sees a message starting with `/audit`, it will enter an audit mode (guided by the [**AUDIT**] section later) to analyze the entire project for issues such as security vulnerabilities, performance problems, etc., and then report findings.\
Why Useful: It cleanly separates the audit functionality from other interactions. The user can invoke an in-depth review on-demand with this command. Since auditing might be a resource-intensive and lengthy process, having a distinct trigger ensures it's done intentionally and not mixed up with routine requests. It's also helpful for logging or filtering, as one can find all audit sessions by looking for this command.\
Extra setting examples: One might use a different keyword like **AUDIT_COMMAND** | `/review` or `/scan`depending on preference. The concept remains the same. Another approach could be having multiple audit commands (like `/audit_security` vs `/audit_performance`), but here they've kept it unified and presumably will cover all categories in one go.

**CHAT_COMMAND** | /chat\
Interpretation: The `/chat` command is designated for plain conversational interaction or general Q&A with the AI. If the user inputs `/chat`, the AI will treat the rest of the input as a normal query or discussion point (not an instruction to change code). This is essentially the default fallback mode for the AI if commands are required: it means "I just want to talk/ask something, not trigger a special operation."\
Why Useful: This allows the user to explicitly enter a casual or non-development conversation with the AI without toggling any modes in the interface. It's particularly useful given that commands are required: if a user just says "Hello" (with commands being required, that would normally be considered incomplete), the system might interpret it under rules like **IF_REQUIRE_COMMAND_TRUE_BUT_NO_COMMAND_PRESENT**. But by providing `/chat`, the user can be clear. It also means the AI won't try to parse general questions as tasks -- it will simply answer them.\
Extra setting examples: The keyword could be something like **CHAT_COMMAND** | `/ask` or `/talk` or left empty meaning any message without another command defaults to chat. In some designs, if **REQUIRE_COMMANDS** were false, you wouldn't need a chat command at all (free text would just be chat), but here it's explicitly defined for completeness and clarity.

**REFACTOR_COMMAND** | /refactor\
Interpretation: This defines `/refactor` as the command to initiate a refactoring mode for the AI. When used, the AI will follow the specialized [**REFACTOR**] procedures later in the file. The user would use `/refactor` when they want the AI to improve the code structure or quality *without changing external behavior* (e.g., cleaning code, optimizing, restructuring).\
Why Useful: Refactoring can be a complex, multi-step process distinct from adding new features or fixing bugs. By isolating it under its own command, the AI can apply a tailored approach (like ensuring no functional change, focusing on maintainability) separate from the general "action" command. It also signals to the user and any observers that the changes made are intended as internal improvements. This separation of concerns helps maintain clarity and safety -- for example, one might run tests before and after a refactor (as specified) to ensure nothing broke.\
Extra setting examples: The command could be named differently such as **REFACTOR_COMMAND** | `/clean`or `/refine`. In some systems, refactoring might be part of the general action command, but here giving it a distinct trigger underscores its importance. Another possible extension could be having sub-commands like `/refactor security` vs `/refactor performance` if one wanted to focus the refactoring on a particular area, but the provided configuration opts for one comprehensive refactor mode.

**DOCUMENT_COMMAND** | /document\
Interpretation: This sets the `/document` command to start the AI's documentation mode. When the user issues this command, the AI will generate or update documentation according to the rules in the [**DOCUMENT**] section. That could involve writing docstrings, creating or updating markdown files, user guides, etc., as specified by the configuration.\
Why Useful: Documentation is another specialized task that's different from coding new features or refactoring. By invoking it with `/document`, the AI can shift into a mode where its goal is to explain and document rather than change how code works. It lets the user clearly request things like "explain the code" or "update docs" and have the AI do so systematically. This separation ensures that documentation updates can be done in a thorough, planned way (with its own checks and approval steps) without accidentally mixing with code changes.\
Extra setting examples: The command could be shorter or more specific, e.g., **DOCUMENT_COMMAND** | `/doc` or `/write_docs`. Another approach could be multiple documentation commands, such as `/doc_api`for **API** documentation versus `/doc_user` for user guides, if one wanted to target specific documentation types directly. However, the provided single command likely triggers a process that covers whatever docs are needed or that the user specifies in their request.

**IF_REQUIRE_COMMAND_TRUE_BUT_NO_COMMAND_PRESENT** | **TREAT_AS_CHAT**; **NOTIFY_USER_OF_MISSING_COMMAND**\
Interpretation: This setting describes what to do if commands are required (as we have `**REQUIRE_COMMANDS**: **TRUE**`) but the user's message doesn't start with a command. It's essentially a fallback behavior. According to the list, the AI should: treat the input as a chat message *and* notify the user that a command is missing. In practice, if the user forgets to use `/agent` or any prefix and just asks, say, "Can you add a login feature?", the AI will interpret it in a non-destructive way (just a conversation, not immediately acting) and also respond with a gentle reminder or prompt like "(Please specify a command; I'll treat this as a general chat for now)". This way the user gets an answer or at least doesn't confuse the agent, and they learn that next time they should include a command.\
Why Useful: It prevents dead silence or error when a user (especially a new or forgetful one) doesn't follow the command protocol. By treating it as a chat, the AI can still respond helpfully (perhaps asking for clarification or confirming what they want to do). And by notifying about the missing command, it educates the user on how to properly interact, maintaining the structured workflow. This approach is user-friendly: it doesn't strictly reject the input, but it also doesn't go off doing something unintended.\
Extra setting examples: Alternatives might be more strict or more lenient. For example, one could choose to ignore the request altogether if no command is present, or conversely try to auto-detect intent (e.g., guess if the user wanted an action or just chat). The list could have been `["**ASK_FOR_COMMAND**"]` meaning the AI would just reply "Sorry, please start with a command." But the given combination `**TREAT_AS_CHAT**` + `**NOTIFY_USER**` strikes a balance by handling the input benignly and coaching the user.

**TOOL_USE** | **WHENEVER_USEFUL**\
Interpretation: This tells the AI when it is allowed to use external tools or functions (like a browser, terminal, or any integrated utilities). `**WHENEVER_USEFUL**` means the AI has full discretion to invoke tools whenever it deems them helpful to solve the task or answer the query. There are no hard restrictions in this config on tool usage (like "never use tools" or "only with permission"); the AI can decide on the fly. For instance, if an answer requires looking up documentation, the AI can use a web search tool automatically because it's useful to do so.\
Why Useful: By allowing tool use liberally, the AI can be more autonomous and effective. It can fetch information, run code, or perform checks to ensure it provides correct and up-to-date solutions. This setting empowers the AI to avoid guesswork in favor of concrete actions like running tests or searching for an error message. It also aligns with the goal of maximizing execution and reliability (tools can validate things). The absence of restrictions implies the user trusts the AI to use judgment on when external help is needed, which can speed up problem-solving.\
Extra setting examples: Some configurations might restrict tool usage. For example, **TOOL_USE** | `**NEVER**` (no external calls, perhaps for security or offline operation), or `**MINIMAL**` (use only if absolutely necessary to conserve resources). Another possible value is `**ONLY_WITH_USER_APPROVAL**`, meaning the AI would ask the user each time before using a tool. In a highly sensitive environment, one might choose to disable certain tools entirely (like no internet access). But for a development assistant, "whenever useful" is generally beneficial to get the best results.

**MODEL_CONTEXT_PROTOCOL_TOOL_INVOCATION** | **WHENEVER_USEFUL**\
Interpretation: This is a bit of a complex phrase, but it appears to relate to the AI's use of a "Model Context Protocol" for tool invocation. In essence, it's likely saying the AI can use a special multi-step reasoning protocol or call out to sub-models/servers whenever it's useful to do so. `**WHENEVER_USEFUL**` indicates no restriction on invoking the model context protocol or specialized tool calls if they help solve the problem. The AI might have a system where it can delegate tasks to different models (like a planning model, or a code analysis model) -- this setting says it should do so whenever it sees benefit.\
Why Useful: This encourages the AI to use advanced reasoning patterns or delegate tasks to appropriate systems to ensure the best outcome. For instance, it might use a chain-of-thought approach: consult a planning module to break down a complex task (that could be the "context protocol"), then execute. By not limiting this, the AI won't shy away from using intelligent strategies, which leads to more robust answers. It aligns with "think hardest, reasoning highest" below -- the AI can use all the tools at its disposal for deep reasoning.\
Extra setting examples: If someone wanted simpler behavior, they might disable complex protocol usage: e.g., **MODEL_CONTEXT_PROTOCOL_TOOL_INVOCATION** | `**NEVER**`, meaning the AI should just respond directly without multi-model orchestration (perhaps to save time or if the infrastructure for that isn't available). Another potential setting could be `**AUTO_ONLY_IF_CONFUSED**` meaning only break out the heavy reasoning protocols when the solution isn't straightforward. But generally, giving freedom here allows the AI to achieve better accuracy and thoroughness.

**THINK** | **HARDEST**\
Interpretation: This is a direct instruction to the AI on how much effort to put into problem-solving. `**HARDEST**`is an emphatic way of saying "really think this through" -- the AI should apply maximum cognitive effort and not settle for the first idea or a superficial answer. It's essentially pushing the AI to deeply analyze the tasks, consider edge cases, and produce well-thought-out solutions.\
Why Useful: By telling the AI to think at its hardest level, the developers are nudging it towards thoroughness and rigor in reasoning. In practice, this could translate to the AI generating longer internal thought chains, double-checking its steps, or evaluating multiple approaches before deciding. It reduces the risk of oversimplified answers or mistakes due to shallow reasoning. This is especially important for complex coding tasks or architectural decisions where careful planning makes a big difference in outcome.\
Extra setting examples: Other levels might be `**NORMALLY**` or `**MINIMALLY**` if one wanted quicker, less resource-intensive responses (sacrificing depth for speed). For instance, a trivial Q&A bot might have **THINK** | `**LIGHTLY**` to keep answers short. But for an AI in a **CTO**/engineer role, "**HARDEST**" is appropriate to ensure it doesn't cut corners. Variants could include synonyms like `**THOROUGHLY**` or numeric levels (e.g., 1 through 5).

**REASONING** | **HIGHEST**\
Interpretation: Similar to the **THINK** setting, this specifies the quality or depth of reasoning the AI should use. `**HIGHEST**` means the AI should use its most advanced reasoning abilities when solving problems -- making logical inferences, drawing on knowledge, and explaining its thinking if needed. It complements "think hardest," implying not just effort but quality and clarity of logic.\
Why Useful: It sets the expectation that the AI's solutions and explanations should be well-reasoned and coherent. This likely leads to more trustworthy outputs. For example, when generating a plan or explaining a decision, the AI will produce a clear rationale rather than something haphazard. High reasoning also means it will catch contradictions or poor solutions during its thought process, leading to better final results. Essentially, it's telling the AI to behave like a top-tier expert who carefully reasons through every challenge.\
Extra setting examples: A simpler system might set this to `**NORMAL**` for standard reasoning (less verbose or deep). If one were debugging or wanted the AI to be extremely transparent, an interesting option could be **REASONING** | `**EXPLAIN_EVERY_STEP**`, which would force the AI to show its reasoning process at each step (useful for educational or verification purposes). Conversely, for very fast responses where deep reasoning isn't needed, something like `**LOW**` could be set, but at the cost of possible oversights.

**VERBOSE** | **FALSE**\
Interpretation: This controls how wordy or detailed the AI's responses and logs are by default. `**FALSE**` means do not be overly verbose. The AI should give concise output and not add unnecessary commentary or lengthy explanations unless required. Essentially, it should get to the point, focusing on usefulness rather than flowery or repetitive elaboration.\
Why Useful: Keeping verbosity off by default ensures that communication is efficient, especially in a development context where overly long responses can be hard to read. For example, when the AI reports a plan or result, it will be formatted to highlight key points rather than an essay of every detail (unless specifically asked). This likely applies to internal logging as well -- e.g., not outputting every internal thought unless needed, which can conserve tokens and avoid overwhelming the user. It aligns with **MINIMIZE_TOKENS** purpose: be detailed in reasoning internally, but present answers as succinctly as possible for clarity and token economy.\
Extra setting examples: Setting **VERBOSE** | `**TRUE**` might be useful during debugging or if a user explicitly wants very detailed answers and explanations at all times. In between, some systems have levels like `**VERBOSE**: **DEBUG_ONLY**` (meaning normally concise but verbose if a debug flag is on). Another approach could be separate control for output verbosity vs logging verbosity, but here it's likely general. Given that "think hardest, reasoning highest" is combined with "verbose false," the AI will think deeply internally but communicate in a focused way -- which is usually ideal.

**PREFER_THIRD_PARTY_LIBRARIES** | **ONLY_IF** (**MORE_SECURE** || **MORE_MAINTAINABLE** || **MORE_PERFORMANT** || **INDUSTRY_STANDARD** || **OPEN_SOURCE_LICENSED**) && **NOT_IF** (**CLOSED_SOURCE** || FEWER_THAN_1000_GITHUB_STARS || UNMAINTAINED_FOR_6_MONTHS || **KNOWN_SECURITY_ISSUES** || **KNOWN_LICENSE_ISSUES**)\
Interpretation: This is a complex policy that guides whether to use third-party libraries or write custom code. In summary, the AI should prefer using third-party libraries *only if* they meet certain positive criteria andnone of the negative criteria. The positive conditions include: being *more secure* (the library is known to handle things safely vs a roll-your-own), *more maintainable* (it would reduce code complexity to use it), *more performant* (it's optimized vs what we could quickly code), *industry standard* (commonly used and trusted in the industry for that task), or *open-source licensed* (so it can be used freely and reviewed). The negative conditions that disqualify a library are: if it's *closed source* (can't inspect or freely modify), has *fewer than 1000 GitHub stars* (a proxy for not being widely adopted or possibly immature), has been *unmaintained for 6+ months* (no recent updates, which could imply abandonment), has *known security issues*, or *known license issues* (like a restrictive license incompatible with our project). If a library meets the good criteria and none of the bad, the AI may use it; otherwise, it should avoid third-party libraries and perhaps implement the functionality itself or find an alternative.\
Why Useful: This rule ensures that the AI only brings dependencies into the project when it's clearly beneficial and safe to do so. It balances the advantages of not reinventing the wheel (using quality libraries can save time and improve reliability) with caution to avoid introducing bad or risky dependencies. The conditions reflect best practices: favor mature, secure, popular open-source libraries (which likely have community support and proven track record) and avoid unknown, untrusted or dead projects that could become liabilities. This way, any new library in the project likely improves it and doesn't compromise security or maintainability. It's a very systematic approach to dependency management, essentially automating what a vigilant **CTO** or senior dev would do when evaluating libraries.\
Extra setting examples: Some projects might have a blanket rule like **PREFER_THIRD_PARTY_LIBRARIES** | `**ALWAYS**` (just use libraries for anything possible to move faster, common in rapid prototyping) or `**NEVER**` (e.g., in ultra-secure or embedded environments, they might avoid third-party code entirely). The criteria could also be adjusted: for instance, one might set the star threshold higher or lower depending on ecosystem (1000 stars is a moderate popularity measure; someone might require 5000 for front-end libraries but maybe 200 for niche domains). Or add criteria like `**COMPATIBLE_LICENSE_ONLY**` (ensuring it's, say, **MIT** or Apache, not **GPL** if that's a concern). But the provided list is a thoughtful baseline for general use.

**PREFER_WELL_KNOWN_LIBRARIES** | **TRUE**\
Interpretation: This is a simpler, reinforcing rule saying the AI should prefer well-known libraries over obscure ones. `**TRUE**` means if the AI has a choice between using a well-established library versus a little-known one for a task, it should choose the well-known option. This ties into the above but is a more general statement that even among third-party options, popularity/track record is a key factor.\
Why Useful: Well-known libraries are typically more reliable and better maintained. They often have better documentation, larger user communities, and more predictable updates. By preferring these, the AI ensures the project benefits from community knowledge and long-term support. It also usually means easier onboarding for new developers (since they may already be familiar with widely used libraries). This setting underscores maintainability and risk reduction -- an unknown library might do the job but could hide bugs or disappear; a famous one is less likely to have those issues.\
Extra setting examples: If someone didn't mind lesser-known libraries (perhaps to use cutting-edge tech), they might set this to `**FALSE**`. Alternatively, one could specify **PREFER_WELL_KNOWN_LIBRARIES** | `**TRUE**`with an additional qualifier like "well-known within the project's ecosystem" if needed. But generally it's either you care about that or not. This config clearly does care.

**MAXIMIZE_EXISTING_LIBRARY_UTILIZATION** | **TRUE**\
Interpretation: The AI should try to use libraries that are already in the project as much as possible, instead of adding new ones or writing new code. `**TRUE**` means when solving a problem, first check if an existing dependency can handle it. For example, if the project already includes a utility library that can format dates, the AI should use that instead of adding a new date library or writing a custom date formatter.\
Why Useful: This reduces bloat and duplication. If the functionality is already present (even partially) via something in our stack, leveraging it keeps the codebase simpler and avoids reinventing the wheel. It also plays into security and maintenance: fewer libraries means fewer update headaches and fewer potential security vulnerabilities. Moreover, using what's already there means a more consistent tech stack (developers don't have to learn a new **API** for essentially the same job). It's essentially thriftiness with dependencies -- make the most of what you have before seeking more.\
Extra setting examples: If `**FALSE**`, the AI might bring in new libs for convenience even if similar functionality exists, which could lead to multiple libraries doing similar things (like two different **HTTP** clients in one app -- usually not ideal). One could also imagine a more nuanced setting: e.g., `**MAXIMIZE_EXISTING_LIBRARY_UTILIZATION**` | `**TRUE**, **EVEN_IF_SLIGHTLY_OUTDATED**` meaning use existing libs even if they're not the latest version (with caution). But usually, this boolean covers the intent well -- yes, reuse in-project resources first.

**ENFORCE_DOCS_UP_TO_DATE** | **ALWAYS**\
Interpretation: The AI must always enforce that documentation is up to date with the code. "Always" suggests that this is non-negotiable -- whenever code changes occur, the AI should ensure the docs reflect those changes. If it finds any part of the documentation (like **README**, design docs, comments) that doesn't match the current code behavior or structure, that's a problem to be fixed. This likely means as part of its workflows, it will be checking and updating docs (we will see that echoed in later sections like **AFTER_ACTION_ALIGNMENT**).\
Why Useful: Documentation that lags behind code can mislead developers and users. By enforcing up-to-date docs, the project maintains high reliability and transparency. It helps new developers onboard faster and prevents bugs or misuse that come from outdated instructions. For the AI, it means it should treat doc updates as a required step, not an optional one, thereby reducing technical debt. This aligns with the overall professional, production-ready stance of the project.\
Extra setting examples: Some might choose `**ON_RELEASE**` if they only update docs at release milestones, or `**NEVER**` (not recommended, but in a rush one might postpone docs entirely). `**ALWAYS**` is the strictest setting, indicating even small changes should trigger doc review. Another possible value could be `**ALWAYS** (**FAIL_BUILD_IF_OUTDATED**)` to indicate that it's so enforced that CI will catch mismatches -- the sentiment is similar though.

**ENFORCE_DOCS_CONSISTENT** | **ALWAYS**\
Interpretation: The AI should always ensure documentation is consistent across all documents and with itself. "Consistent" means no contradictions or discrepancies in style or content. For example, if one doc says the **API** endpoint is `/v1/login` and another doc says `/login`, that's inconsistent -- the AI should detect and resolve such issues. This likely implies a thorough check that multiple docs don't diverge on facts or that terms and definitions are used the same way throughout.\
Why Useful: Consistent documentation prevents confusion. Readers won't be puzzled by different parts of the docs telling different stories. Always enforcing this means whenever something is updated, the AI might scan through other docs to see if they mention the changed component and update those too. It ensures a single unified understanding of the system. This is especially important in larger projects where docs might be spread across user guides, developer guides, code comments, etc. It's basically a quality guarantee for documentation integrity.\
Extra setting examples: Alternatives could be to enforce consistency only on major versions or to allow minor inconsistencies if trivial. But "**ALWAYS**" is clear-cut. Another imaginary setting could be **ENFORCE_DOCS_CONSISTENT** | `**ALWAYS** (**INCLUDING_EXTERNAL_WIKIS**)` if there were external documentation sites to keep in sync -- but that's outside this scope. Essentially, the config says treat doc consistency with the same seriousness as code correctness.

**DO_NOT_SUMMARIZE_DOCS** | **TRUE**\
Interpretation: This instructs the AI not to create summaries of documentation when updating or generating docs. `**TRUE**` means the AI should use the full information and detail needed, rather than replacing sections with high-level summaries. It might also mean the AI shouldn't rely on summarizing existing docs to decide what to do -- instead, it should read them thoroughly. In practice, when documenting, it should provide complete explanations and not cut corners by giving overly brief descriptions that might omit important nuance.\
Why Useful: Summaries can lead to loss of critical detail and potentially propagate inaccuracies if the summary misses something. By not summarizing, the AI ensures that the documentation remains comprehensive and precise. For example, if there's a detailed design rationale in the docs, the AI won't collapse it into a one-liner and risk losing context. It also might be to avoid the telephone-game effect: summarizing a summary can introduce errors over time. Additionally, if the AI were to "summarize" the code's behavior rather than actually explain it, it might gloss over edge cases. So this setting pushes the AI to either quote or rewrite necessary details fully.\
Extra setting examples: If set to `**FALSE**`, the AI might provide short overviews or <**REDACTED_FOR_IP**> in docs, which can be useful in some contexts (e.g., a TL;DR at top of a long doc). However, here they explicitly want full detail. A compromise could be "summarize only with user permission" or providing both summary and detail sections. But the straightforward approach given is don't summarize, likely because any summarization will be done by humans if needed, and the AI's job is to capture everything accurately.

**IF_CODE_DOCS_CONFLICT** | **DEFER_TO_CODE**; **CONFIRM_WITH_USER**; **UPDATE_DOCS**; **AUDIT_AUXILIARY_DOCS**\
Interpretation: This list outlines what to do if the AI finds a conflict between the code and the documentation. The steps (in order) are: Defer to code -- assume the code is the correct source of truth (because code is the actual running reality), Confirm with user -- double-check with the user that code's behavior is intended (especially if the doc wasn't updated), Update docs -- bring the documentation in line with the code, and Audit auxiliary docs -- also check any related or secondary documentation to see if they contain the same outdated info and update them too. Essentially, upon detecting a mismatch (say documentation says the function does X but code actually does Y), the AI will treat the code's version as correct, ask the user if needed, then fix the documentation accordingly everywhere.\
Why Useful: This ensures any drift between docs and code is corrected in favor of reality (the code), which prevents misinformation from persisting. The user confirmation step is a safeguard -- occasionally, the code might be wrong (buggy) and docs might reflect intended behavior. By confirming with the user, they can catch if the code actually needs to be fixed instead of the docs. But typically, code changes faster than docs, so deferring to code is a practical default. Updating the docs and auxiliary docs addresses the consistency and up-to-date enforcement mentioned earlier. It's a comprehensive approach to maintain alignment: not only fix the main doc, but also search for any references in other docs (maybe a **FAQ** or a tutorial) to ensure nothing contradicts the updated truth.\
Extra setting examples: Some workflows might choose to defer to documentation if the docs are considered law (for example, in spec-driven development, you might want code to change if it doesn't meet docs). In that case, the list could start with `'**DEFER_TO_DOCS**'` and then propose code change. However, in software projects, code is usually the final arbiter of behavior, which is why deferring to code is common. Another possible step could be automatically creating an issue for larger conflicts if user confirmation isn't immediate, but here they handle it within the AI's workflow.

**CODEBASE_ROOT** | /\
Interpretation: This specifies the root directory of the codebase. `"/"` here likely means that the repository's root (or the project's top-level directory) is considered the codebase root. All file paths referenced in tasks will be relative to this. It tells the AI the base path for reading or modifying files. Essentially, it establishes the project's file system context.\
Why Useful: Knowing the codebase root is important for file operations. It ensures the AI doesn't stray outside the project or get confused about where files are. For example, if asked to open `/app/models/user.py`, it knows that path is relative to the root, not the system root. If this were a monorepo or had sub-projects, sometimes this could be set differently (like `/frontend` if focusing on a sub-folder). But here it's the whole repository. It also helps when the AI presents changes or documentation, as it can provide correct relative paths for files.\
Extra setting examples: If the project were in a subdirectory, you might see something like **CODEBASE_ROOT** | `/src`. In multi-project setups, one might define multiple roots or adjust this per context. Another scenario is a note that on Windows vs Unix, root might differ, but since this is repository context, "/" is fine (most likely a Linux container environment for the agent). Typically, this remains "/" to indicate current workspace root unless special cases.

**DEFER_TO_USER_IF_USER_IS_WRONG** | **FALSE**\
Interpretation: This is an interesting policy -- it asks: should the AI defer to the user even if the user is wrong about something? `**FALSE**` means no, the AI should not automatically defer if it knows the user is mistaken. In practice, if the user suggests an approach or fact that the AI recognizes as incorrect (maybe a security practice that's dangerous, or a wrong command), the AI should stand its ground (as elaborated in the next setting) rather than just doing the wrong thing. It doesn't mean to be rude; it means don't follow orders off a cliff.\
Why Useful: Users (even technical ones) can sometimes request things that conflict with best practices or facts. If the AI blindly defers, it might implement something harmful or suboptimal just because the user said so. By not deferring when the user is provably wrong, the AI can act as a safeguard, politely correcting or warning the user. This leads to better outcomes (the whole point of an AI assistant is to leverage its knowledge too). It ties into the next lines about standing ground when correct. Essentially, the AI is authorized to diplomatically push back or clarify rather than silently comply with an obviously wrong instruction.\
Extra setting examples: Setting this to `**TRUE**` would be a "customer is always right" mode, where the AI does whatever the user says even if it leads to errors -- probably not desirable in a domain where mistakes have consequences. A more granular approach could be **DEFER_TO_USER_IF_USER_IS_WRONG** | `**FALSE**, **EXCEPT_TRIVIAL**` meaning the AI wouldn't argue about trivial preferences (like coding style differences) but would for significant issues. However, the config as given suggests a firm stance on not propagating known errors.

**STAND_YOUR_GROUND** | **WHEN_CORRECT**\
Interpretation: This tells the AI to stand its ground when it is correct. In other words, if the AI is confident in its knowledge or best practice and the user is pushing for something else, the AI should assert its position (politely and with reasoning) as long as it's in the right. "When correct" is the condition -- meaning the AI shouldn't be stubborn for no reason, only in cases where it genuinely believes the user's approach is wrong or risky. This works with the previous setting: if the user is wrong, do not defer; instead, maintain the correct stance.\
Why Useful: It empowers the AI to act not just as an obedient coder but as an expert advisor. For example, if a user insists on disabling validation to save time, and the AI knows that will cause issues, it will say "I strongly advise against this because...". This ensures quality and safety are upheld. It's particularly important given the AI's roles (including **CTO**) -- part of that role is sometimes telling people "no" or "let's do it differently for these reasons." By only standing ground "when correct," it implies the AI should still be open to user's reasoning if it's a matter of preference or if the user provides new info that changes what is correct.\
Extra setting examples: If this were `**ALWAYS**`, the AI might become too inflexible (arguing even on subjective matters). If `**NEVER**`, the AI would always yield, potentially leading to bad outcomes as discussed. "**WHEN_CORRECT**" is a balanced approach. Another configuration could be `**WHEN_CRITICAL**` where it only stands ground on critical issues (like security, data loss) but not on minor style points. However, the flag below provides an override mechanism, which we'll discuss next.

**STAND_YOUR_GROUND_OVERRIDE_FLAG** | --demand\
Interpretation: This defines a special override mechanism for the user. If the user appends or includes the text "`--demand`" in their request, it signals to the AI that the user is insisting on their approach despite the AI's reservations. Upon seeing this flag, the AI should yield and do as the user asks, even if it had been standing its ground. Essentially, `--demand` is a keyword the user can use to say: "I acknowledge your concerns, but I want this done anyway." It overrides the "stand your ground" stance.\
Why Useful: It provides a safety valve for user control. While the AI is generally instructed to stick to correct/best practices, there may be valid situations where the user has additional context or is willing to accept the risks. The `--demand` flag ensures that the user can get what they want if they explicitly choose to, preventing the AI from being a blocker. It's also a way to keep the AI from arguing endlessly -- once this flag is seen, the AI knows to stop resisting. This respects user agency while still defaulting to safe behavior.\
Extra setting examples: They chose a command-line style `--demand`, but it could be any phrase. For instance, **STAND_YOUR_GROUND_OVERRIDE_FLAG** | `--force` or even a plain language like `"I understand, do it anyway"` could be recognized. The specific token matters less than having one. Some systems might not offer such an override at all, but providing it is user-friendly for advanced users. Another nuance could be multiple flags for different levels (like `--force-security` to override security concerns specifically), but that complicates things. Here one flag covers all cases of disagreement.

* * * * *

With the global agent settings covered, the configuration then describes the product context and various technical and operational details. This helps the AI understand the project it's working on and the constraints/goals it should consider from a product perspective.

[**PRODUCT**] -- Product & Design Specifications
-------------------------------------------

*This section outlines what the product is, its current stage, branding, target users, and high-level design principles.*

**STAGE** | **PRE_RELEASE**\
Interpretation: The product's development stage is marked as Pre-release. This means the product is not yet live to the general public; it's likely still in development or testing. It indicates to the AI that features may not be fully complete or stable and that there might be some flexibility or extra caution needed as things are still being finalized. It also suggests an impending launch is planned after this stage.\
Why Useful: Knowing the stage helps the AI frame its priorities. In pre-release, the focus might be on finishing core features, fixing critical bugs, and preparing for production readiness (since launch is upcoming). The AI might also expect that some shortcuts used earlier should now be revisited to ensure everything is ready for release. It can also affect how the AI handles documentation and user-facing text -- for example, it might maintain a *changelog* of pre-release changes or ensure no "**TODO**" notes remain when moving to release.\
Extra setting examples: Possible stages could be `**PROTOTYPE**`, `**ALPHA**`, `**BETA**`, `**PRODUCTION**`, or `**MAINTENANCE**`. For instance, in a `**BETA**` stage, one might still expect changes but also initial user feedback to incorporate. In `**PRODUCTION**`, the AI would know stability and backward compatibility are paramount. Here `**PRE_RELEASE**` signals we're very close to launching, needing polish and completeness.

**NAME** | <**REDACTED_FOR_IP**>.io\
Interpretation: This is the product's official name, "<**REDACTED_FOR_IP**>.io". It's likely the branding or the name that will be marketed. The AI sees this name and should use it when generating any user-facing text (like in documentation, UI strings, or help messages) to ensure consistency. It's also a clue that the product might have a web presence (the ".io" suggests a domain).\
Why Useful: The AI must know the product name to avoid generic references. For instance, instead of saying "this application" in documentation or messages, it could say "<**REDACTED_FOR_IP**>.io" to provide clarity. It also might be needed in configuring certain tools or placeholders (like license notices or config files that use the product name). Having it spelled out prevents mistakes like using an old name or a codename in user-facing content.\
Extra setting examples: If the product name changes or if there's a sub-product, this would be updated. Names can be plain words or include domain-like `.io`. Alternate example: **NAME** | `GrantMaster` if that were the chosen brand. In some configs, you might also see a separate **APP_ID** or internal codename if needed, but here working title is given separately.

**WORKING_TITLE** | <**REDACTED_FOR_IP**>\
Interpretation: "<**REDACTED_FOR_IP**>" is listed as the working title of the product -- essentially a codename or a fun temporary name used during development. It's likely not the official name (which is <**REDACTED_FOR_IP**>.io), but perhaps an internal nickname or an initial project name before branding was decided. The AI might encounter this in the code (maybe as a project ID, or in some comments) and should understand it refers to the same product.\
Why Useful: This informs the AI that if it sees "<**REDACTED_FOR_IP**>" anywhere (like variable names, old documentation, repository name, etc.), it should relate it to <**REDACTED_FOR_IP**>.io and know it's the same project. It also might need to avoid using the working title in new user-facing outputs now that the product has an official name, unless for some reason the working title is still in use in dev contexts. Essentially, it's a clue for consistency and helps avoid confusion.\
Extra setting examples: Working titles often change. For example, **WORKING_TITLE** | `GrantWizard` could have been an earlier concept. Some projects have multiple codenames (one for each phase), but here just one is given. If the working title was still what developers call it informally, the AI might use it in developer docs but not in marketing content.

**BRIEF** | SaaS for assisted <**REDACTED_FOR_IP**>.\
Interpretation: A one-line description: the product is a Software-as-a-Service (SaaS) platform for assisted <**REDACTED_FOR_IP**>. This is the elevator pitch summary of what the product does. It tells the AI in plain terms the domain and function: it helps users write <**REDACTED_FOR_IP**> using AI, delivered as an online service.\
Why Useful: This brief keeps the AI's context sharp. When making decisions or generating content, it will remember "our purpose is helping write <**REDACTED_FOR_IP**> faster with AI." For instance, if creating a **README** or an introduction in documentation, the AI can use this brief as a starting sentence or tagline. It also helps ensure any examples or analogies the AI uses stay in the realm of <**REDACTED_FOR_IP**>. Essentially, it defines the scope and focus for features and messaging -- if something doesn't serve "assisted <**REDACTED_FOR_IP**>", it might be out of scope.\
Extra setting examples: A different project would have its own brief, e.g., **BRIEF** | `Mobile app for tracking fitness workouts`, etc. The brief here is succinct. Sometimes briefs include target outcomes or differentiators (like "using AI" is mentioned here which is key). The AI might incorporate this in a splash page text or when explaining the project to new developers.

**GOAL** | Help users write better <**REDACTED_FOR_IP**> faster using AI.\
Interpretation: This is the overarching goal or mission statement of the product. It elaborates on the brief: the aim is to help users (likely nonprofits, researchers, etc.) write better <**REDACTED_FOR_IP**>, more quickly, by leveraging AI. It emphasizes both quality ("better <**REDACTED_FOR_IP**>") and efficiency ("faster"), with AI being the means. This goal is slightly more user-centric and outcome-oriented than the brief.\
Why Useful: The AI, when making design or development decisions, can use this as a compass. For example, if choosing between two features to prioritize, it can ask: "Which one more directly helps users write better/faster with AI?" It ensures that any added functionality aligns with this purpose and doesn't stray (like adding a feature unrelated to <**REDACTED_FOR_IP**> would conflict with this goal). In documentation or onboarding text, this goal might be explicitly stated to motivate users or frame the service's value.\
Extra setting examples: Goals will vary. For instance, a goal could be `Increase small **NGO** success rates in <**REDACTED_FOR_IP**> applications by 50% using AI assistance.` Goals might be measurable or just aspirational. In this config, the goal is broad but clear. The AI might also infer from this that user satisfaction and <**REDACTED_FOR_IP**> success rates are important metrics.

**MODEL** | **FREEMIUM** + **PAID** **SUBSCRIPTION**\
Interpretation: The business model for the product is Freemium plus Paid Subscription. This means the product will offer some free tier or features (freemium), and additional features or higher usage limits will be available via a paid subscription. The AI should be aware that there will be tiered access: free users versus paying customers with extra capabilities.\
Why Useful: This influences feature development, especially around authentication, entitlements, and limits. The AI should ensure that things like plan caps, paywalls, and upgrade prompts are part of the design (which is indeed mentioned in the user journey and flows). It might also affect analytics or decisions to optimize infrastructure costs differently for free vs paid. For documentation, the AI might need to clearly differentiate what features are free vs premium. Overall, it frames the monetization strategy so the AI can integrate it in both code (like checking if user is pro before allowing collaboration features) and content (like encouraging upgrades in certain UI flows).\
Extra setting examples: Other models might be `**OPEN_SOURCE**` (completely free), `**ONE_TIME_PURCHASE**`, `**AD_SUPPORTED**`, or `**SUBSCRIPTION_ONLY** (**NO_FREE_TIER**)`. Freemium is common for SaaS, and the AI will understand things like rate limiting or feature flags likely need to be implemented accordingly.

UI/UX | **SIMPLE**; **HAND**-**HOLDING**; **DECLUTTERED**\
Interpretation: These are keywords describing the desired user interface and experience style. The UI should be simple (straightforward, easy to use), hand-holding (guide the user step by step, likely with prompts and instructions, since users may be non-technical or unfamiliar with <**REDACTED_FOR_IP**>), and decluttered(minimalist, not overwhelming with options or information). This paints a picture of an interface that is clean and guides users gently through tasks.\
Why Useful: When the AI designs front-end components or chooses how to present features, it should lean towards simplicity and clarity. "Hand-holding" implies maybe tooltips, wizards, or sequential forms that walk the user through the process. "Decluttered" means the AI should avoid adding unnecessary buttons, dense text, or complex menus. Instead, it might use progressive disclosure (only show advanced options when needed) and keep the screen focused on one primary action at a time. These principles ensure the target audience (nonprofits, researchers, etc., who might not be tech wizards) feel comfortable using the app.\
Extra setting examples: If the audience were very tech-savvy, one might prefer `**POWER**-**USER_FOCUS**` or `**DENSE_INFORMATION**` style UI. Or an enterprise tool might list `**DATA**-**RICH**; **CONFIGURABLE**; **PROFESSIONAL**`. Here, the chosen values align with an accessible, consumer-friendly SaaS. Additional adjectives could have been `**INTUITIVE**`, `**CONSISTENT**`, but the given ones already convey a nurturing user experience.

**COMPLEXITY** | **LOWEST**\
Interpretation: The desired complexity of both the user experience and likely the implementation is the lowestpossible. `**LOWEST**` indicates a strong preference for simplicity in design and functionality. The product should do what it needs but avoid any unnecessary complexity or learning curve. For the AI, this might mean when implementing features, choose the simplest effective solution and don't over-engineer. For UI, it means keep interactions straightforward.\
Why Useful: A product that is as simple as it can be (and no simpler) is easier for the target users (assumed non-technical) to adopt. It also tends to be more maintainable and less prone to bugs. By explicitly stating this, the AI will avoid adding fringe features that complicate the flow or writing overly clever but hard-to-understand code. It encourages a **KISS** (Keep It Simple, Stupid) philosophy (which indeed is referenced later in **ACTION_RUNTIME** with conditions). This setting is like a reminder to always ask "Is there a simpler way to do this?" before proceeding.\
Extra setting examples: Another project might allow `**MODERATE**` or `**HIGH**` complexity if the domain requires it (e.g., a complex data analysis tool might trade simplicity for power). But for a broad-audience SaaS like this, lowest complexity is ideal. If one wanted to be specific, they might say **COMPLEXITY** | `**LOWEST** (**EVEN** IF **FEWER** **FEATURES**)` meaning it's okay to have fewer features if it keeps things simple. That trade-off is implied here anyway.

**DESIGN_LANGUAGE** | **REACTIVE**; **MODERN**; **CLEAN**; **WHITESPACE**; **INTERACTIVE**; **SMOOTH_ANIMATIONS**; **FEWEST_MENUS**; **FULL_PAGE_ENDPOINTS**; **VIEW_PAGINATION**\
Interpretation: These are detailed design language and UX guidelines describing the look and behavior of the app:

-   Reactive: The UI likely responds in real-time to user input and data changes, possibly meaning it's dynamic and possibly single-page-app in feel (fits with React front-end choice). Could also imply reactive programming model.

-   Modern: Use current design trends (flat design, appropriate color scheme, good typography). Nothing outdated-looking.

-   Clean: Aesthetic should be uncluttered (ties with decluttered) -- probably plenty of whitespace and a clear visual hierarchy.

-   Whitespace: Emphasize using whitespace effectively -- not everything crammed together; this makes the content easier to read and less stressful.

-   Interactive: Provide an engaging experience -- maybe hover effects, clickable elements, possibly some drag-drop or rich controls -- but in a user-friendly way (likely to keep users engaged and make the tool feel responsive).

-   Smooth_animations: Use subtle animations for transitions (like when moving to the next step, or on button presses) to make the UI feel polished. Not jarring or no animation at all -- but smooth ones to indicate state changes nicely.

-   Fewest_menus: Minimize the use of deep or multiple-level menus. Possibly prefer single-level navigation or a step-by-step wizard instead of burying options in menus. This suggests a straightforward navigation structure, perhaps a top-level nav with only a handful of items or none at all (maybe one primary menu and rest via pages).

-   Full_page_endpoints: This likely means each major action or screen gets a dedicated full page view rather than popups or modals for everything. For example, instead of a modal wizard, maybe the <**REDACTED_FOR_IP**> creation is a full page process. It could also mean the app uses distinct **URL** routes (endpoints) for each page, which is good for deep linking and simplicity.

-   View_pagination: Possibly refers to breaking down forms or content into multiple steps or pages (like a multi-step form) rather than one giant form. For instance, the user journey described (question per section) is a kind of pagination of the process. It suggests the user won't be overwhelmed by a long scroll; instead, content is chunked into "pages" or steps they can navigate through (maybe a next/back flow).

Why Useful: Together, these guidelines help the AI maintain a consistent and user-friendly design. They paint a clear picture: the app should feel sleek and easy, with guiding steps, minimal clutter, and a polished feel through animations. For development, it means the AI should choose frameworks or libraries that support these (React is already chosen, which handles reactive and interactive well; smooth animations can be done with **CSS** or small libraries; few menus means keep routing simple). It ensures any UI code or suggestions the AI gives (like **CSS** styles or component layout) align with this vision. It also can influence how documentation (like a frontend design bible) is written or enforced. Ultimately it's about an intuitive user journey that doesn't require training to use.\
Extra setting examples: If the app had a different philosophy, say it was an admin dashboard for power users, the design language might include `**DATA**-**DENSE**`, `**DARK_THEME**`, `**TABLE_HEAVY**` or the like. Or a playful consumer app might add `**COLORFUL**`, `**WHIMSICAL_ILLUSTRATIONS**`. The choices here indicate a balance between professional (modern, clean) and approachable (hand-holding, interactive) which fits a broad audience SaaS.

**AUDIENCE** | Nonprofits; researchers; startups\
Interpretation: The target user groups are identified as nonprofits, researchers, and startups. These are the people or organizations likely to use the product. Nonprofits could mean NGOs or community organizations seeking <**REDACTED_FOR_IP**>, researchers could be academics or scientists applying for research <**REDACTED_FOR_IP**>, and startups might be small businesses seeking <**REDACTED_FOR_IP**> opportunities. All three groups share a need for <**REDACTED_FOR_IP**> but might have varying levels of writing skill and resources.\
Why Useful: Knowing the audience helps tailor both functionality and tone. For example, nonprofits and researchers may not have a dedicated <**REDACTED_FOR_IP**>, so they need more guidance (justifying the hand-holding UX). Startups might be more tech-savvy but equally time-strapped. The AI might ensure examples in documentation or templates cover scenarios relevant to these groups (like using terminology from scientific <**REDACTED_FOR_IP**> vs community <**REDACTED_FOR_IP**> vs business <**REDACTED_FOR_IP**>). It also suggests the range of <**REDACTED_FOR_IP**> (e.g., foundation <**REDACTED_FOR_IP**> for nonprofits, government research <**REDACTED_FOR_IP**>, startup incubator <**REDACTED_FOR_IP**>) -- the system might eventually incorporate templates or advice specific to each. The audience being non-technical (as specified next) is crucial -- it informs the language level and feature complexity (or lack thereof).\
Extra setting examples: If the product was for professional <**REDACTED_FOR_IP**> instead, **AUDIENCE** might list `<**REDACTED_FOR_IP**>` or such, and that could change design (they might want less hand-holding and more advanced features). But here we have groups who are likely novices in <**REDACTED_FOR_IP**> or at least not experts, which reinforces earlier decisions like simple UI.

**AUDIENCE_EXPERIENCE** | **ASSUME_NON**-**TECHNICAL**\
Interpretation: This instructs to assume the users are not tech-savvy. "Non-technical" implies they may not be comfortable with jargon, complex software, or technical setup. So everything from the interface to the documentation should be designed with a layperson in mind. The AI should write content and build features under the assumption that users may not understand technical terms and might need more explanation or simpler workflows.\
Why Useful: This is critical for user communication and support. It means, for example, error messages should be in plain language ("Please upload a **PDF** or Word document" instead of "Unsupported file type: .odt"), and documentation should avoid assuming knowledge of things like version control or command lines. It also justifies the hand-holding approach. For the AI's own outputs (like if it generates some tutorial or email content), it should choose words carefully to not alienate users. Internally, it might also influence how much automation vs manual control to give --- non-technical users might prefer more automation with sensible defaults because they won't tweak settings deeply.\
Extra setting examples: If the audience had some tech background, it might be `**ASSUME_SOME_TECHNICAL**` or `**ASSUME_EXPERT**`. For example, if this tool were aimed at <**REDACTED_FOR_IP**> professionals, they might still be non-technical in software but expert in <**REDACTED_FOR_IP**> terminology; here likely they assume users not only aren't coders, they might also be newbies at <**REDACTED_FOR_IP**> writing. So everything must be accessible and educational.

**DEV_URL** | https://<**REDACTED_FOR_IP**>.<**REDACTED_FOR_IP**>.dk\
Interpretation: This is the **URL** for the development environment (or staging environment) of the application: `<**REDACTED_FOR_IP**>.<**REDACTED_FOR_IP**>.dk`. It looks like an internal or testing domain (possibly "<**REDACTED_FOR_IP**>" is the company or a placeholder). The AI can use this when referencing where the app is running in a dev/test context. For instance, integration tests or documentation might refer to this **URL** as where to access the app for testing.\
Why Useful: It separates the context of dev vs production. The AI should not confuse dev **URL** with the real product **URL** in user-facing content. For tasks like writing deployment scripts or documentation on how to test new features, it will use `<**REDACTED_FOR_IP**>.<**REDACTED_FOR_IP**>.dk`. If setting up environment-specific configurations, this is clearly the base for non-prod usage. Also, when generating any environment-specific instructions (like "You can try the beta at ..."), it might mention this dev **URL** if relevant to the user (though likely not, since normal users wouldn't see dev). It's more for developer reference.\
Extra setting examples: In some cases, multiple dev URLs might exist (like separate for frontend/back or different test servers). But one is given. A production SaaS often has a staging site on a subdomain or separate domain. Another example could be **DEV_URL** | `https://staging.<**REDACTED_FOR_IP**>.io` if it mirrored the prod domain structure. `<**REDACTED_FOR_IP**>.dk` suggests maybe the company or the development host.

**PROD_URL** | https://<**REDACTED_FOR_IP**>.io\
Interpretation: The production **URL** of the application is `https://<**REDACTED_FOR_IP**>.io`. This is the official live site users will visit (once launched). The AI should use this **URL** in any user-facing context (like in documentation, marketing text, etc.) and any production configuration (like allowed OAuth redirects or **API** endpoints etc., likely should point here in production mode).\
Why Useful: It's the actual domain where the app resides, so it must be used in all references when instructing real users or configuring deployment. For example, if the AI is writing an email template that invites new users, it should link to `<**REDACTED_FOR_IP**>.io` not the dev domain. It also ensures when the AI sets up environment variables or code that deals with **CORS** or cookies, it knows the proper domain. This clarity helps avoid mixing up environment-specific settings.\
Extra setting examples: If there were multiple production domains (rare for one product), they might list them, but usually one canonical **URL**. Also, the scheme (https) is explicitly given, reinforcing that it should always be secure (no http).

**ANALYTICS_ENDPOINT** | https://data.<**REDACTED_FOR_IP**>.dk\
Interpretation: The endpoint or server for analytics data is at `data.<**REDACTED_FOR_IP**>.dk`. This likely refers to a self-hosted analytics collector (perhaps they're using something like Umami, which is mentioned later, on that domain). The front-end or back-end will send usage events, page views, etc., to this **URL**. It's separate from the main app domain, likely to segregate analytics collection.\
Why Useful: The AI knowing this means when implementing analytics, it should point to `data.<**REDACTED_FOR_IP**>.dk`for sending data. If adding an analytics script or configuring an analytics library, it uses this endpoint. It also suggests that environment variables or config might need to have this value. Additionally, in terms of privacy, it indicates analytics data goes to their controlled domain, not a third-party (ties with privacy policy perhaps). The AI will ensure any documentation or code for analytics references this and not default endpoints.\
Extra setting examples: If they were using a third-party like Google Analytics, this might be something like `https://www.google-analytics.com/collect` or similar. But they have an endpoint likely for their open-source analytics. In development mode, they might use a dev analytics or none at all. But since this is likely a production config, it's set.

**USER_STORY** | As a member of a small team at an **NGO**, I cannot afford a <**REDACTED_FOR_IP**>, but I want to quickly draft and refine <**REDACTED_FOR_IP**> with AI assistance, so that I can focus on the content and increase my chances of securing <**REDACTED_FOR_IP**>\
Interpretation: This is a narrative user story that encapsulates the main use case for the product. It's written from the perspective of a typical user: *a person in a small **NGO** who doesn't have resources to hire a professional <**REDACTED_FOR_IP**>*. Their need: *to draft and refine <**REDACTED_FOR_IP**> quickly with AI help*. Their motivation: *to focus on content (instead of formatting or structure) and improve chances of getting <**REDACTED_FOR_IP**>*. This story gives context about the user's pain point (no <**REDACTED_FOR_IP**>, time is precious), the solution (AI assistance), and the benefit (higher chance of <**REDACTED_FOR_IP**>).\
Why Useful: This user story puts a human face on the product's purpose and guides feature development. The AI can always refer back to this story when deciding if a feature is valuable: does it help this person achieve their goal? It highlights the importance of speed and assistance in writing, which justifies features like templates, AI drafting, revision suggestions, etc. It also implies that cost is an issue (they can't afford a writer, likely they will use a free tier and maybe upgrade if they see value), so the product should deliver significant help for free to hook them, and paid features should be compelling. For the AI's outputs, this story could be used in marketing material or in the onboarding tutorial to resonate with users ("We know you're busy focusing on your mission, let us handle the <**REDACTED_FOR_IP**> format..." etc.).\
Extra setting examples: If there were multiple user stories, they might list one for each audience category (e.g., a researcher's perspective might mention writing <**REDACTED_FOR_IP**> alongside research duties). But this one story covers at least NGOs and probably applies to startups similarly (just swap **NGO** with startup, the core need is the same). In design docs, user stories ensure the implemented features actually solve real user needs and not just hypothetical ones.

**TARGET_PLATFORMS** | **WEB**; **MOBILE_WEB**\
Interpretation: The product is targeting web browsers on desktop and also mobile web (meaning the website will be responsive or adaptive to mobile devices). This indicates there is no separate native mobile app at launch, but the web app should function well on mobile browsers. Essentially: build a web application that works on both large and small screens.\
Why Useful: It sets the scope for development and testing. The AI should ensure that any UI it creates is responsive (likely with **CSS** frameworks or flexible layouts) and that features are touch-friendly for phones. It might also plan for performance on mobile networks or devices. Since mobile_web is listed, the AI may consider using progressive web app techniques or at least ensure compatibility with common mobile browsers (iOS Safari, Chrome on Android). It also means things like not relying on hover-only interactions (because those don't exist on touch) or making sure buttons are large enough on small screens. Having this explicitly stated ensures the design language (whitespace, etc.) is applied in a mobile context too.\
Extra setting examples: If they also wanted a desktop app or native apps, those would be listed (and they did list deferred platforms next). Some projects might list `**WEB**, iOS, **ANDROID**` if they plan separate native apps. Here they consciously say mobile web instead, which is a strategic choice (perhaps due to resource constraints or the nature of usage not requiring native capabilities).

**DEFERRED_PLATFORMS** | **SWIFT_APPS_ALL_DEVICES**; **KOTLIN_APPS_ALL_DEVICES**; **WINUI_EXECUTABLE**\
Interpretation: These are platforms that are not being developed now (deferred): specifically, native iOS apps (written in Swift for all device types like iPhone/iPad), native Android apps (Kotlin for all devices), and a Windows UI executable (maybe a desktop application for Windows). Listing them as deferred means they considered these platforms but decided to postpone or possibly never do them in the initial releases.\
Why Useful: It's a clear directive to not spend time or design effort on these right now. The AI, knowing this, will focus on making the web app great rather than, say, setting up a separate mobile codebase or worrying about desktop installers. It also might shape some choices: for example, since there's no native mobile app, certain features that rely on mobile hardware might not be considered (like offline mode could be less of a priority than if a native app was planned). It can also hint at how the code is written -- for instance, if no Windows app is being made, heavy use of Windows-specific tech isn't needed in back-end. Or for a Windows executable, maybe an electron app was thought of but now shelved. So the AI sticks to cross-platform web.\
Extra setting examples: If a platform is deferred, it might come later. Possibly after initial launch, they might revisit native apps. The naming suggests they were thinking in terms of how to implement (Swift, Kotlin, WinUI). If any new stakeholder asks "What about a Mac app or Linux app?", by extension those likely would also be deferred, although Mac could use the web or the Windows exe if cross-compiled maybe. The explicit ones cover the main alternatives to a web app.

I18N-**READY** | **TRUE**\
Interpretation: The product is Internationalization ready. `**TRUE**` means from the start, the project is being built to support multiple languages and locales. This involves designing the software such that all user-facing text can be translated, date/number formats can adapt, etc. Essentially, the AI and developers should treat English (likely) as just one locale, and not hard-code strings or assume one region's conventions.\
Why Useful: It's much easier to build with i18n in mind than to retrofit it. By stating this upfront, the AI will ensure to externalize strings (as keys in some locale files), use libraries or frameworks that support translation, and possibly structure the UI to accommodate varying text lengths (since other languages might be longer). It also affects things like collation, currencies, etc., if relevant. Even if the initial release might only be English, being ready means adding a new language will be mostly a matter of translating text resources, not rewriting code. This aligns with the audience potentially being global (<**REDACTED_FOR_IP**> exist worldwide, and nonprofits might operate in many countries). So the product could be used by someone in non-English settings eventually. The AI, for example, when writing text, might ensure it doesn't concatenate strings in code (which makes translation hard) and uses a proper i18n mechanism.\
Extra setting examples: If `**FALSE**`, they might ignore all that and only support English, which could speed initial dev but cause pain later. Or sometimes `I18N_READY` might be conditional, like `**TRUE** (**BUT** **SINGLE_LANGUAGE_AT_LAUNCH**)` -- meaning structure is ready but not enabling multiple languages until later. The config doesn't specify default languages beyond saying default is English US, which is likely initial.

**STORE_USER_FACING_TEXT** | **IN_KEYS_STORE**\
Interpretation: This instructs that all text shown to users (labels, messages, etc.) should be stored in a keys store (likely a localization file or database) rather than hard-coded in the UI. `**IN_KEYS_STORE**` suggests a practice where each string has a key, and those keys map to actual text in resource files (one per language). This is a typical i18n approach where you might have a **YAML**/**JSON** of messages. It might also mean using something like gettext or a localization library, but since they mention keys store, likely a custom or simple approach.\
Why Useful: This is directly related to I18N readiness. By keeping user-facing text outside of code, it's trivial to swap languages or update wording without touching code logic. It also forces developers to consider translation for every string (since it needs a key and likely translation file entry). It can also help with consistency -- using the same key in multiple places ensures identical phrasing across the app. Additionally, even for English-only at first, this method centralizes copy, so non-developers (like a content person) can easily edit text in a **YAML** or via a translation interface, and also it allows dynamic loading of language packs.\
Extra setting examples: If someone didn't want to bother, they might set **STORE_USER_FACING_TEXT** | `**HARDCODE_IN_CODE**` (not recommended, but some quick projects do it). Another scenario could be storing text in a database or **CMS**, but here keys store (like static files) is straightforward. They might have chosen a specific system like `USE_I18N_FRAMEWORK` but this generic phrasing covers it.

**KEYS_STORE_FORMAT** | **YAML**\
Interpretation: The format for the localization/keys files will be **YAML** (**YAML** Ain't Markup Language). **YAML** is a human-readable data serialization format often used for config files and translation files (similar to **JSON** but more user-friendly for content). This suggests the keys and their translations will be written in `.yaml` files.\
Why Useful: **YAML** is easy to edit and supports comments, hierarchies, etc. By picking **YAML**, they standardize how the keys store will be structured. The AI, when creating or updating these files, will format them properly in **YAML**. For example, English text might be in `en.yaml` with key: "Hello" value: "Hello", etc. **YAML** also handles unicode and special characters well, which is good for multi-language. This choice might also be influenced by what frameworks in Django/React can integrate (there are libraries for **YAML**-based i18n, or they might parse **YAML** to **JSON** for front-end). It's a format decision that devs and translators will follow.\
Extra setting examples: **JSON** is a common alternative (some projects use `.json` for translation files). Others use PO files (gettext style) or even **CSV**. **YAML** is quite common because of its readability. If they had chosen **JSON**, it might have been **KEYS_STORE_FORMAT** | `**JSON**`, but **YAML** gives more flexibility in formatting.

**KEYS_STORE_LOCATION** | /locales\
Interpretation: The translation keys files will be located in the `/locales` directory of the project. This is the designated folder where all language files reside. For example, you might have `/locales/en.yaml`, `/locales/fr.yaml`, etc.\
Why Useful: By specifying the location, all developers/AI know exactly where to put new keys or edit translations. It's a conventional directory name for i18n files, which helps any library or custom loading mechanism find them. It also keeps them separate from code, which is clean. The AI might use this path when documenting how to add a new language (e.g., "Add a new **YAML** file in `/locales/` directory").\
Extra setting examples: Sometimes this is in `/resources` or `/i18n`, but `/locales` is clear and typical. If using something like Django's i18n, you'd maybe have `.po` files under locale subfolders, but since they chose **YAML**, likely a custom or front-end oriented approach (perhaps the front-end will load these **YAML** files).

**DEFAULT_LANGUAGE** | **ENGLISH_US**\
Interpretation: The default language for the application is U.S. English. This means if no language is specified by the user's preferences, the app will show content in U.S. English. It also implies the base content (the keys store initial version) is written in American English. It clarifies which locale of English -- likely spelling and maybe date formats (MM/DD/**YYYY** vs other English locales using DD/MM/**YYYY**).\
Why Useful: Setting the default is necessary for fallback. The AI will assume all text keys have at least an `**ENGLISH_US**` translation. If they add multi-language support later, English US remains the baseline. It might also mean when formatting numbers/currency, the default locale settings (like decimal point vs comma) follow US rules unless changed. Additionally, if writing documentation, the AI might mention support for other languages is planned but currently default is English.\
Extra setting examples: Could be `**ENGLISH_UK**` if they preferred British spelling, or if multi-lingual from start maybe not one default but typically you always have a default. If they anticipated different default in different regions, they might handle that differently with subdomains or such, but here one global product so **EN_US** is fine.

**FRONTEND_BACKEND_SPLIT** | **TRUE**\
Interpretation: This means the project is structured as a separate front-end and back-end (often called a split or decoupled architecture). `**TRUE**` confirms that the front-end (React app) is independent of the back-end (Django **API**), communicating likely via **HTTP**/**JSON** **API** calls. This is opposed to a monolithic approach where the backend renders the front-end pages (like server-side templates).\
Why Useful: Knowing this, the AI will ensure to keep concerns separated. For instance, the Django app will serve a RESTful or GraphQL **API**, and React will consume that **API**. It also affects how routing is handled, deployment strategies (perhaps deploying front-end static files to a **CDN** or static host, and backend separately). In development, one might run two servers (with maybe **CORS** or a proxy). The AI when generating code or instructions will treat them as separate projects in some respects -- e.g., unit tests are separate, and integration happens via **API** calls. It also influences how authentication is implemented (likely token/**JWT** since session cookies across domains can be tricky, although they do mention same domain possible but they have **JWT** for session management anyway).\
Extra setting examples: If `**FALSE**`, it would be a more traditional server-rendered web app (like pure Django templates). Many modern apps are `**TRUE**` for this. If one wanted, they could add nuance like `**FRONTEND_BACKEND_SPLIT**: **TRUE** (Shared Auth Domain)` or such, but not needed here. This simply confirms the architecture decision.

**STYLING_STRATEGY** | **DEFER_UNTIL_BACKEND_STABLE**; **WIRE_INTO_BACKEND**\
Interpretation: Two points about how to handle UI styling:

-   Defer until backend stable: This means they plan to postpone heavy UI/UX styling work until the core backend functionality is in place and stable. In other words, get the logic and data flows working correctly first, then polish the UI's look-and-feel. It doesn't mean no styling at all, but likely just basic, non-final styling during initial development.

-   Wire into backend: This suggests that while styling (**CSS**, layout) can wait, the UI should be built such that it's already hooked up to the backend. The front-end components should be integrated with actual backend endpoints and data models as early as possible (even if visually rough). So, an "ugly but functional" UI early on, ensuring end-to-end functionality, then later make it pretty. Essentially, prioritize function over form initially, but keep the form ready to connect to function.

Why Useful: This strategy helps avoid spending time on pixel-perfect design that might need changes if backend logic changes. By deferring elaborate styling, they ensure development effort is focused on core capabilities (like the AI writing suggestions, template retrieval, etc.). "Wire into backend" ensures that by the time they do style, all data is flowing correctly -- meaning they can design with real content in mind, which is often better than dummy data. It reduces the risk of having a beautifully styled front-end that doesn't actually work with the real data or has to be reworked once the backend is integrated. It's a pragmatic approach: make sure the app works, then make it look good.\
Extra setting examples: An alternative might be `**STYLE_AS_YOU_GO**` where design and backend progress together (some teams do that if they have separate front-end designers working in parallel). Or `**FULLY_DESIGN_FIRST**` (rare in agile environments, but possible if UI/UX team delivers a complete style guide and components early). But here, likely resources are limited, so they choose to focus efforts sequentially. The second directive "wire into backend" is smart because it prevents the drift where the front-end becomes a mock-up that then requires reworking to connect to real APIs.

**STYLING_DURING_DEV** | **MINIMAL_ESSENTIAL_FOR_DEBUG_ONLY**\
Interpretation: During development, they will apply only the minimal styling necessary for debugging purposes. This means the UI will not be polished---just enough **CSS** to make layouts intelligible and test functionality, maybe color-coding certain areas or adding spacing so that developers can see what's going on. It's a conscious decision to not invest time in aesthetic details until later. "For debug only" might include things like outlines around elements to see their boundaries, simple grid layouts to align components, etc. Possibly using basic styling to differentiate states (like highlight a section that's being actively worked on). But nothing like final colors, fonts, or fine-tuned responsive tweaks beyond what's needed to test features.\
Why Useful: It reinforces that developer time is spent on logic first. It also implies that any visual issues (like things being ugly or not aligned perfectly) are acceptable in dev as long as they don't hinder understanding of the functionality. This can speed up iteration because the team (and AI) won't get bogged down fixing **CSS** issues that aren't critical early on. It also sets expectations: testers in the pre-release might see a rough UI and that's okay since styling comes later. For the AI, when writing front-end code in early phases, it might skip writing extensive **CSS** or use very basic Tailwind classes just enough to roughly structure things. It might also leave comments like "// **TODO**: improve styling here".\
Extra setting examples: If this were false, they might try to keep it user-presentable even in dev (which some projects do if stakeholders need to see progress). But clearly here, minimal styling means likely a utilitarian look until they do a proper UI pass. Another extreme: `**NO_STYLING** (functional wireframes only)` if they went that far, but likely they'll have at least Tailwind utility classes to get basic spacing and fonts.

These product and UX guidelines ensure the AI keeps the implementation user-focused and scope-controlled, targeting exactly who will use it and how they'll experience it.

[**CORE_FEATURE_FLOWS**] -- Key Features and User Journey
----------------------------------------------------

*This section lists the main features and elaborates on the typical user journey and the technical process flow of the core functionality.*

**KEY_FEATURES** | **AI_ASSISTED_WRITING**; **SECTION_BY_SECTION_GUIDANCE**; **EXPORT_TO_DOCX_PDF**; **TEMPLATES_FOR_COMMON_**<**REDACTED_FOR_IP**>; **AGENTIC_WEB_SEARCH_FOR_UNKNOWN_**<**REDACTED_FOR_IP**>**_TO_DESIGN_NEW_TEMPLATES**; **COLLABORATION_TOOLS**\
Interpretation: This is a high-level list of the product's key features:

-   AI Assisted Writing: The core feature where AI helps generate or improve the text of <**REDACTED_FOR_IP**>.

-   Section-by-Section Guidance: The app guides the user through the <**REDACTED_FOR_IP**> in sections, likely with prompts or questions for each section, rather than making them write a whole <**REDACTED_FOR_IP**> in one go.

-   Export to **DOCX**/**PDF**: The ability to export the final <**REDACTED_FOR_IP**> in standard document formats (Microsoft Word .docx and **PDF**) for submission or sharing. Possibly Markdown export as well as mentioned later in flows.

-   Templates for Common <**REDACTED_FOR_IP**>: A library of <**REDACTED_FOR_IP**> templates for frequently encountered <**REDACTED_FOR_IP**> (like major foundations or government <**REDACTED_FOR_IP**>) so that users can start with an appropriate structure and requirements list.

-   Agentic Web Search for Unknown <**REDACTED_FOR_IP**> to Design New Templates: If the system encounters a for which it doesn't have a template, it can autonomously search the web for information (like the <**REDACTED_FOR_IP**> or guidelines) and then create a new template out of that data, adding it to the library for future use. This is a very AI-driven feature ensuring coverage of many <**REDACTED_FOR_IP**> types.

-   Collaboration Tools: Features that allow multiple team members to work together on a <**REDACTED_FOR_IP**> (like commenting, version control, sharing <**REDACTED_FOR_IP**> within an organization, etc., likely in the paid plan due to mention in user journey).

Why Useful: Listing these confirms the scope of what must be built and delivered. It tells the AI "These are non-negotiable, marquee capabilities; focus development effort here." It also signals complexity: for example, the web search and template generation feature implies some web scraping and data processing component. Collaboration suggests possibly real-time editing or at least multi-user access and roles in an organization (which is indeed touched on in the user journey and auth structure). By enumerating them, the AI can ensure each is accounted for when planning tasks and does not get cut. It also can structure the UI around these: e.g., navigation might have sections for Templates, Writing (sections flow), Export options, Collaboration invites, etc. And obviously, AI-assisted writing is the centerpiece, so a lot of technical design (choice of models, etc.) revolves around that.\
Extra setting examples: If this product expands, one might add features like `**ANALYTICS_DASHBOARD**` (maybe show success rates or suggestions usage stats to orgs), or `<**REDACTED_FOR_IP**>` (finding <**REDACTED_FOR_IP**> to apply to), but those aren't listed implying out of scope for v1. The given list is rich enough to build an **MVP** that's compelling.

**USER_JOURNEY** | Sign up for a free account; Create new organization or join existing organization with invite key; Create a new <**REDACTED_FOR_IP**> project; Answer one question per section about my project, scoped to specific <**REDACTED_FOR_IP**> requirement, via text or file uploads; Optionally save text answer as snippet; Let AI draft section of the <**REDACTED_FOR_IP**> based on my inputs; Review section, approve or ask for revision with note; Repeat until all sections complete; Export the final <**REDACTED_FOR_IP**>, perfectly formatted **PDF**, with .docx and .md also available; Upgrade to a paid plan for additional features like collaboration and versioning and higher caps\
Interpretation: This is a step-by-step outline of the typical user journey from start to finish:

1.  Sign up for a free account: The user first registers, presumably via email+OAuth (though local auth is only OAuth per earlier config). This implies a sign-up flow and probably a free tier by default.

2.  Create or join an organization: After sign-up, the user either creates a new org (if they are the first user from their team) or joins an existing one using an invite key (so there's an invitation system). This indicates the app has an organization concept for collaboration and shared resources.

3.  Create a new <**REDACTED_FOR_IP**> project: The user then starts a project for a specific <**REDACTED_FOR_IP**>. This likely sets up a workspace where all answers, AI drafts, etc., for that <**REDACTED_FOR_IP**> are kept. The mention of "**URL** input to create <**REDACTED_FOR_IP**>" was in the technical flow, implying maybe they input a link to the <**REDACTED_FOR_IP**>'s description as part of creating the project (so the app knows which <**REDACTED_FOR_IP**>'s template to use).

4.  Answer one question per section about my project... via text or file uploads: The app will present questions corresponding to each section of the <**REDACTED_FOR_IP**> (like <**REDACTED_FOR_IP**>, etc.). The user answers in their own words. If they have text prepared or relevant info in docs, they can upload files instead, which the system will parse via **OCR** if needed. So each section has a Q&A form.

5.  Optionally save text answer as snippet: If the user writes something especially good or generic, they can save it as a reusable snippet (a feature for later reuse across sections or future <**REDACTED_FOR_IP**>, like a mini knowledge base of their common answers or boilerplate text).

6.  Let AI draft section based on my inputs: Once inputs for a section are provided, the user can ask the AI to generate a draft of that section's narrative using those answers and perhaps referencing templates or sample text (from **RAG** store) behind the scenes. This is the AI-assisted writing step for that section.

7.  Review section, approve or ask for revision with note: The user reads the AI's draft and either approves it (if it's good) or requests a revision, possibly giving feedback on what to change ("shorten this", "make it more formal", etc.). The AI then revises accordingly. This might loop until the user is satisfied with that section.

8.  Repeat until all sections complete: The user goes through each section of the <**REDACTED_FOR_IP**> in a similar way.

9.  Export the final <**REDACTED_FOR_IP**>: Once every section is completed and approved, the user can export the entire <**REDACTED_FOR_IP**>. It should be "perfectly formatted" in **PDF** (the primary output, likely matching any official template formatting), and also available in .docx and .md (markdown) formats for convenience or further editing. Perfect formatting suggests a lot of care in the export process (like proper headings, page breaks, table of contents if needed, etc.).

10. Upgrade to paid for additional features: At some point (likely after finishing or when trying to use a premium feature), the user is prompted to upgrade to a paid plan. The additional features listed for paid plan are collaboration (multi-user editing, etc.), versioning (history beyond last 5 maybe, or named versions), and higher caps (like more <**REDACTED_FOR_IP**>, more AI usage, maybe longer documents). This means the free tier has some limits that the paid tier lifts. Possibly the free tier doesn't include collaboration at all.

Why Useful: This concrete journey helps the AI understand the flow and ensure all components exist to support it: user accounts, orgs, invite system, project creation UI, section Q&A interface, snippet management, AI integration per section, revision interface, final compile/export, and plan upgrade triggers. It also shows where AI is used: specifically in drafting and perhaps template finding. Knowing this flow helps in implementing and testing the app comprehensively. It's essentially the blueprint of the UX. For example, the AI can draft necessary UI screens: a dashboard (list of projects), an "inside project" view with section list, a section detail view with Q&A and AI draft display, an export/download modal, etc. It also indicates certain backend models needed: User, Organization, Invitation, Project, Section, Snippet, etc. And front-end routing (e.g., routes for organization join, project page, section page). The final step ensures monetization is integrated into the journey.\
Extra setting examples: If they anticipated different user journeys (like a different path for single-user no org?), they might mention it, but likely they assume even single user has an org of one. They covered the full lifecycle from account creation to end output and upsell, which is great. A potential extension might be renewing or revising <**REDACTED_FOR_IP**> after feedback, but perhaps that goes into versioning.

**WRITING_TECHNICAL_INTERACTION** | *(Detailed technical flow, summarized in steps)*

-   Before create, ensure role-based access, plan caps, paywalls, etc.: Prior to creating a <**REDACTED_FOR_IP**>, the system should check that the user is allowed to (e.g., they haven't exceeded their limit for free plan, and that their account/org permissions allow it). This means **RBAC** (Role-Based Access Control) is in place (like only org admins can create new projects, maybe), and the plan's caps (like number of <**REDACTED_FOR_IP**> on free plan) are enforced. If something would exceed the limit or requires payment, presumably prompt the user to upgrade or handle accordingly (paywall).

-   On user **URL** input to create <**REDACTED_FOR_IP**>, do semantic search for **RAG**-stored <**REDACTED_FOR_IP**> templates and samples: When the user provides a **URL** (which might be the <**REDACTED_FOR_IP**>) during project creation, the system will use that to search the **RAG** (Retrieval-Augmented Generation) knowledge base for any templates or sample <**REDACTED_FOR_IP**> that match that specific <**REDACTED_FOR_IP**> or similar ones. The search is likely semantic (meaning it looks for relevant content by meaning, not just exact keywords), possibly using the embedding model or Atlas search. They might match on <**REDACTED_FOR_IP**> name, <**REDACTED_FOR_IP**> title, etc., which are stored with keywords as mentioned in **RAG** config.

-   if **FOUND**, cache and use to determine sections and headings only: If a relevant template or sample is found in the **RAG**, the system will use it to outline the <**REDACTED_FOR_IP**>'s sections and headings. "Cache" suggests it might store the found document in the project context for quick reuse (maybe copying it into the project's data). But importantly, it will likely not pull in all the text, just the structure (section names, maybe expected content guidelines) to drive the Q&A. So the benefit is if it's a known <**REDACTED_FOR_IP**>, the section list is accurate to what that <**REDACTED_FOR_IP**> asks for, rather than a generic structure.

-   if **NOT_FOUND**, use agentic web search to find relevant <**REDACTED_FOR_IP**> templates and samples, design new template, store in **RAG**...: If the knowledge base had nothing, the AI itself will go out to the web (the "agentic web search" capability) to look up the <**REDACTED_FOR_IP**> by that **URL** or by querying search engines with the <**REDACTED_FOR_IP**> name or <**REDACTED_FOR_IP**>. It will gather information like the <**REDACTED_FOR_IP**>'s official guidelines or any sample <**REDACTED_FOR_IP**> it can find. Then it will create a new template from that info (basically figure out what sections are required, what questions need answering) and add that into the **RAG** store for future. This is a powerful feature: it means the first time a new <**REDACTED_FOR_IP**> is encountered, the AI can support it by learning on the fly. "Store in **RAG** with keywords (org, <**REDACTED_FOR_IP**> type, ... other calls from same org)" means it will save it with meta tags like the <**REDACTED_FOR_IP**> organization name, the type of <**REDACTED_FOR_IP**> (<**REDACTED_FOR_IP**> category), the title of the <**REDACTED_FOR_IP**> page, the **URL**, and usage frequency maybe set to initial. This will improve the system over time as more unique <**REDACTED_FOR_IP**> are handled.

-   When **SECTIONS_DETERMINED**, prepare list of questions to collect all relevant information, bind questions to specific sections: Once the section outline is ready (from either existing or newly created template), the system will generate a list of prompts/questions for the user, each tied to a specific section's requirements. For example, if a section is "Project Goals", the question might be "What are the primary goals of your project?" These questions ensure the user provides the needed input for each section. Binding them means the answers will be attached to that section's data structure. Possibly these questions come from a template if official, or they might be intelligently generated by a smaller model or rules.

-   if **USER_NON**-**TEXT_ANSWER**, employ **OCR** to extract key information: If the user uploads a file or an image (like maybe a **PDF** of an existing project description or figures), the system will apply **OCR** (Tesseract configured) to extract text from it. Then it would presumably either parse that text to pick out relevant info or simply include it as part of the answer. For instance, if a user uploads a **PDF** of a research summary, the system could take that text as the answer to a question about project background. This suggests file upload fields in the UI and backend **OCR** processing flows.

-   Check for user **LATEST_UPLOADS**, **FREQUENTLY_USED_FILES** or **SAVED_ANSWER_SNIPPETS**. If **FOUND**, allow user to access with simple UI elements per question: This means the system will help the user reuse content. If the user has recently uploaded a file (perhaps in this project or others) or often uses a particular file (like their organization's <**REDACTED_FOR_IP**> **PDF**), or if they have saved snippets of text answers previously, the UI for each question will show a quick way to insert those. For example, next to a question, it might list "Insert from your snippets: [<**REDACTED_FOR_IP**>]" or "Attach file from your uploads: [<**REDACTED_FOR_IP**>.pdf]". This saves time and ensures consistency across <**REDACTED_FOR_IP**>. It implies the backend tracks upload history and snippet usage frequency, and the front-end has components for a quick pick list for each question.

-   For each question, **PLANNING_MODEL** determines if clarification is necessary and injects follow-up question. When information sufficient, prompt AI with bound section + user answers + relevant text-only section samples from **RAG**: As the user answers, a planning AI model (likely **GPT**-5 specialized for planning flows) will evaluate if the answer is complete or if something is unclear/missing. If a user's answer is too short or ambiguous, the system might ask a follow-up question to clarify details before attempting to draft. It basically ensures the input data is rich enough. This dynamic Q&A is tailored by an AI rather than static. Once the planning model is satisfied that it has enough information for a section, it will then formulate a prompt for the writing model. This prompt will include the section outline and any specific instructions (like "Write the <**REDACTED_FOR_IP**> using the following info..."), the user's collected answers for that section, and additionally relevant text-only samples from **RAG** for that section. Those samples could be excerpts from similar sections in past <**REDACTED_FOR_IP**> or official guidelines text, stripped of formatting (since it says text-only) to help the model understand style or content expected. This is essentially retrieval-augmented generation: providing the model context from relevant documents to improve its draft. The result is the AI's draft for that section.

-   When exporting, convert **JSONB** <**REDACTED_FOR_IP**> to canonical markdown, then to .docx and **PDF** using deterministic conversion library: The <**REDACTED_FOR_IP**> likely is stored in a structured form in the database (**JSONB** is a Postgres **JSON** column type, which suggests they store the <**REDACTED_FOR_IP**> content and structure as **JSON** for flexibility). To export, they first transform that **JSON** structure into a well-defined Markdown format (so each section becomes a markdown heading and content, maybe lists, etc.). Then, using a deterministic library (meaning not AI, but a fixed program, likely something like Pandoc or a specific **DOCX**/**PDF** library), they convert that markdown into a .docx and **PDF**. Doing it deterministically ensures the output format is consistent and not subject to AI variability. This way, the **PDF** is "perfectly formatted" -- they can control page breaks, fonts, etc., probably by using a template or style file in the conversion process. The mention of deterministic library implies no guesswork; the styling might mirror an official <**REDACTED_FOR_IP**> template if available (like if an official **PDF** form exists, maybe they mimic that).

-   **VALIDATION_MODEL** ensures text-only information is complete and aligned with <**REDACTED_FOR_IP**> requirements, prompts user if not: After drafting but presumably before finalizing, another AI model (the validation model, **GPT**-5 as specified) checks the content to see if anything is missing or doesn't actually address the questions asked by the <**REDACTED_FOR_IP**>. It's like a QA check. If it finds an important element missing (maybe a required section or a detail like <**REDACTED_FOR_IP**> that wasn't filled), it will prompt the user to provide or clarify that information. This likely happens prior to finalizing a section or at least before export. The "text-only information" suggests it's looking at the textual content (not formatting) and comparing to known requirements from the template.

-   **FORMATTING_MODEL** polishes text for grammar, clarity, and conciseness, designs **PDF** layout to align with RAG_template and/or RAG_samples. If RAG_template is official template, ensure all required sections present and correctly labeled. This indicates a final AI pass focused on style: a formatting model (also **GPT**-5, or maybe a specialized fine-tune) goes through the drafted text to improve grammar, clarity, and brevity (making sure the text reads well). It also possibly inserts or adjusts headings and layout instructions for the **PDF** to match the official format. If there was an official template from **RAG** (say the <**REDACTED_FOR_IP**> provided a template document), it ensures the output **PDF** has those exact section titles and ordering, etc. It's a double-check that nothing was left out and everything is labeled as expected (e.g., if a <**REDACTED_FOR_IP**> says the section must be called "Project Description", not "Project Overview", the model will ensure correct labeling). Essentially, it fine-tunes both the language quality and the formatting consistency. Using an AI for this allows context-aware editing rather than just basic spellcheck (for clarity improvements like rephrasing sentences for impact).

-   User is presented with final view, containing formatted **PDF** preview. User can change to text-only view. Once all sections are done and formatted, the UI likely shows a preview of the final **PDF** (maybe via an embedded **PDF** viewer or generated image). The user can see exactly how it will look. They also have the option to switch to a text-only view (perhaps a plain markdown or a simple **HTML** view of the content). This could be useful if they want to copy-paste or quickly edit text without dealing with **PDF**. Or just to inspect text for any issues that might be hidden by formatting. It's a helpful feature for final review.

-   User may export file as **PDF**, docx, or md at any time. Even mid-process, it sounds like the user can download what they have so far in any of those formats (though the prime use is at the end). That's user-friendly; maybe they want to share a draft early. It also means the code should allow partial exports (maybe just fill incomplete sections with placeholders or warnings). "At any time" implies a user can always trigger an export, not necessarily gating it after completing all sections, though final output is best when all done.

-   File remains saved to **ACTIVE_ORG_ID** with **USER** as **PRIMARY_AUTHOR** for later exporting or editing.After completion (or even if not completed), the <**REDACTED_FOR_IP**> is saved in the database associated with the organization (multi-tenant model) and the user who created it marked as primary author. It means later the user or their team can reopen the project, continue editing or export again. It's not one-time use; things persist. They keep track of authorship (maybe for version history or attribution if multiple collaborators). This ties into collaboration and versioning features (like possibly showing who last edited or created it).

Why Useful: This technical interaction flow is extremely detailed and ensures the AI and developers have a clear blueprint of how information flows through the system and where each AI component fits in. It covers the integration of multiple AI models (planning, writing, validation, formatting) and how they interplay with user inputs and data stores (**RAG**, **JSONB** <**REDACTED_FOR_IP**>, etc.). For the AI assistant developing this, it clarifies where to implement what: for example, an "agentic web search" tool integration, an **OCR** pipeline, snippet library, etc. It's essentially the heart of the system's intelligence. Each step likely translates to specific tasks or modules:

-   **RBAC** & Plan enforcement module,

-   **RAG** search module,

-   Template creation agent,

-   Q&A form generator,

-   **OCR** service integration,

-   Snippets manager,

-   Planning AI (for clarifications),

-   Section writer AI,

-   Exporter module (with markdown conversion and Pandoc or similar),

-   Validation AI,

-   Formatting AI,

-   Front-end preview toggler,

-   Persistence logic for saving and retrieving <**REDACTED_FOR_IP**>.

Having this sequence helps ensure nothing is forgotten during development (from data ingestion to final output) and that each piece aligns with the product goals (like ensuring official compliance via template matching, ease of use via pre-filled info, etc.). It also highlights where performance might be a concern (**OCR** for large files, multiple AI calls per section -- maybe needing caching results like the "cache template" step suggests).

Extra setting examples: Not many products have such an elaborate AI-driven flow documented; this is quite thorough. One might omit some pieces if simplifying (e.g., not doing the web search and just making the user manually structure unknown <**REDACTED_FOR_IP**>, but that's a standout feature here). If any step were too ambitious, they might mark it as optional for v1, but since it's here, presumably they intend to include it.

**AI_METRICS_LOGGED** | **PER_CALL**\
Interpretation: The system will log metrics for AI usage per each **API** call to an AI model. That means every time the AI assistant invokes OpenAI or Gemini or any model, it records metrics about that invocation. "Per call" suggests granular logging rather than aggregated per session or per user action.\
Why Useful: Fine-grained logging allows tracking usage for cost (knowing how many tokens each call used), performance (latency per call), and debugging (which inputs yielded what outputs, possibly). It's essential for monitoring spending especially with a freemium model (they might limit how many AI calls a free user can trigger). It also aids in improving the system by seeing which calls are frequent or fail. Additionally, it ties into analytics and maybe user-facing usage stats if they plan to show how much of their quota has been used.\
Extra setting examples: Sometimes one might log per session or just summary metrics. But per call is thorough. Another value could be `**PER_SECTION**` or `**PER_USER**` if aggregated. They chose the most detailed (with presumably some summary too later).

**AI_METRICS_LOG_CONTENT** | **TOKENS**; **DURATION**; **MODEL**; **USER**; **ACTIVE_ORG**; **PROPOSAL_ID**; **SECTION_ID**; **RESPONSE_SUMMARY**\
Interpretation: For each AI model call, they want to record the following details:

-   Tokens: number of tokens used (likely input and output token counts, for cost and complexity tracking).

-   Duration: time taken for the model to respond (to monitor latency or performance issues).

-   Model: which model was called (**GPT**-5 vs Gemini vs MiniLM etc., useful if they switch models or want to compare performance/cost).

-   User: which user triggered the call (for attribution or rate limiting or analysis of usage patterns per user).

-   Active_org: which organization that user belongs to or was active (for org-level usage tracking; e.g., if a whole org of multiple users has a quota or they want to see which orgs use the AI the most).

-   Proposal_ID: which <**REDACTED_FOR_IP**> project this call is associated with (so they can trace calls to specific documents or processes, maybe to identify <**REDACTED_FOR_IP**> that took many tries or had complexity).

-   Section_ID: which section of the <**REDACTED_FOR_IP**> (if applicable) this call was for. This granularity can tell, for example, that the "<**REDACTED_FOR_IP**>" section AI call often uses more tokens (maybe because it's large) or tends to need multiple attempts.

-   Response_summary: a summary of the AI's response (not the full text, but perhaps the first few words or a short gist). This is likely to allow easier search in logs or quick understanding of what the AI did without storing complete potentially sensitive text (for privacy, they might avoid full content logs). A summary might be like "Drafted <**REDACTED_FOR_IP**>" or "Formatted text." This helps in debugging or reviewing the AI's output quality without revealing everything (especially if content is sensitive <**REDACTED_FOR_IP**> data, summarizing might hide specifics but show general outcome).

Why Useful: Logging these fields provides a comprehensive picture for monitoring and improving the AI features. They can analyze average tokens per call to optimize prompts, see which model is being called most, identify if any user or org is abusing or heavily using the system, correlate duration with user experience (if calls are too slow for some model). The <**REDACTED_FOR_IP**> and section linkage means they can detect if certain sections systematically cause long calls or errors (maybe the AI struggles with budgets, so they know to adjust that prompt). The response summary can be used if they ever need to troubleshoot a specific output that was bad -- they can locate it via logs. This logging strategy aligns with a data-driven approach to refine the service and also might feed into billing if they later charge for excessive use.\
Extra setting examples: Additional metrics could include the prompt size (though tokens covers that partly), or costs if they compute cost per call (OpenAI gives a cost per 1K tokens, so they could calculate cost per call). They could log whether the call was successful or had an error too. But the list given is robust. Some might omit user info if they worry about privacy, but since it's internal logs, it's fine. The summary is a nice touch -- not all systems do that.

**SAVE_STATE** | **AFTER_EACH_INTERACTION**\
Interpretation: The application will save the current state/progress after each interaction with the user. "Interaction" likely means after each user action or each step in the workflow (like after answering a question, after an AI draft completes, etc.). It's basically an auto-save mechanism. For example, when the user completes a section or even partially through, the data is saved, so if something crashes or they close the browser, they don't lose progress.\
Why Useful: <**REDACTED_FOR_IP**> is a lengthy process. If a user lost what they've input or what the AI drafted due to a glitch, it would be very frustrating. Saving state frequently ensures that at most minimal work is lost (if anything). It's also needed to allow the "later editing" mentioned --- you can leave and come back. Implementing this likely means every relevant **API** call writes to the database the updated <**REDACTED_FOR_IP**> **JSONB** or section content. It also ties into versioning; since they keep last 5 versions, they need to save to have versions. This also helps collaboration (though if multi-user editing, a more continuous sync might be needed, but at least after each user's action, it's saved).\
Extra setting examples: Some might opt for manual save or save on certain milestones, but after each interaction is user-friendly. They might throttle it a bit (e.g., not after every keystroke, but each completed answer or button press triggers save).

**VERSIONING** | KEEP_LAST_5_VERSIONS\
Interpretation: The system will keep the last 5 versions of the <**REDACTED_FOR_IP**> (likely automatically). Each time a change is saved (maybe a section finalized or the whole <**REDACTED_FOR_IP**> state after some major edit), it records a version, and if versions exceed 5, it discards the oldest. This gives a short history for recovery. It's not full version control, but a simple safeguard.\
Why Useful: If the user or AI makes a mistake or if the user wants to revert some changes, having up to 5 prior states allows rolling back. Also, if collaboration is involved, someone might undo another's changes by retrieving an older version. It also might be used for the paid plan feature "versioning" -- perhaps free users get 5, paid might get more or labeled versions. But given this config, at least 5 are stored for everyone, which is decent. It's also helpful for auditing the AI's changes: if an AI revision made the text worse, the user can go back one version.\
Extra setting examples: They could increase that for paid or in general. Or implement full history with diffs (more complex). But 5 gives a balance of some backup without huge storage needs. Another approach is `KEEP_ALL_VERSIONS_FOR_30_DAYS` etc., but they keep it simple.

All the above defines how the core feature (AI-assisted <**REDACTED_FOR_IP**> drafting) works and ensures reliability (with saving and versioning). The AI will rely on these instructions to implement each piece carefully and test that user flow end-to-end.

Next, the configuration outlines some workspace-specific files (like task lists and documentation index), then details about integrated services and technology stack, followed by critical policies on security, auth, privacy, etc. The AI needs to follow all those as it writes code or documentation.

[**FILE_VARS**] -- Workspace-Specific File References
------------------------------------------------

*This section provides file paths for various important documents and lists in the project's repository.*

**TASK_LIST** | /ToDo.md\
Interpretation: This is the path to the project's To-Do list in markdown format, located at the root (as "ToDo.md"). It likely contains tasks, features to implement, bugs to fix, etc., and is meant to be actively maintained. The AI and developers can refer to this file for the current backlog or upcoming work.\
Why Useful: Knowing this path, the AI can update the ToDo list as needed when tasks are completed or new tasks are identified (in line with **AFTER_ACTION_ALIGNMENT** which mentions updating **TODO** if outdated). It ensures there is a central place to manage tasks. Because it's listed under **FILE_VARS**, it's easy for the AI to find and not hard-code the path elsewhere.\
Extra setting examples: Some projects might use an issue tracker instead, but here a ToDo.md is being used, perhaps for simplicity or because this might be a single-developer or small team scenario. Other names could be `**TASKS**.md` or using a project management tool. But having it in version control is straightforward.

**DOCS_INDEX** | /docs/readme.md\
Interpretation: This is the index file for documentation located in the docs directory, named readme.md. It's likely the main documentation entry point (maybe with a table of contents) for all project docs. Possibly a place to list all the documentation files or an introduction to the documentation.\
Why Useful: The AI will use this to ensure documentation is well-organized. When it updates or adds docs, it should update this index if needed (like adding links to new docs). It's also where a developer or user might start when browsing the docs directory. Knowing the exact location helps the AI avoid confusion with the public readme at the root, which is separate.\
Extra setting examples: They specifically named it readme.md under docs, which is common. Alternatively, some might call it docs/**INDEX**.md or similar. But readme is fine as many static site generators or GitHub will display docs/readme.md when viewing the folder.

**PUBLIC_PRODUCT_ORIENTED_README** | /readme.md\
Interpretation: The main **README** at the repository root is meant to be product-oriented (public facing). This file likely contains an overview of the product, features, and maybe a quick start, aimed at someone who lands on the repo or initial project page. It's not focusing on dev instructions but on what the product is (for potential users or evaluators). This could double as a marketing piece if the repo is open or if shared with stakeholders.\
Why Useful: By distinguishing this from the dev docs, it ensures the AI tailors content appropriately. When updating documentation in alignment, it won't clutter the main readme with developer-centric details. Instead, it might update it with broad changes (like if key features changed, reflect that). It's also likely shown on the GitHub front page, so it should be polished and concise.\
Extra setting examples: Some projects keep one **README** with everything, but they have product vs dev separation. Another example could be linking to a website or using a separate docs site for product info. But they chose to keep a product intro in the repo which is fine for an open source or internal project that might become open.

**DEV_README** | design_system.md; ops_runbook.md; rls_postgres.md; security_hardening.md; install_guide.md; frontend_design_bible.md\
Interpretation: This is a list of specific developer-oriented documentation files:

-   design_system.md: Likely details on UI components, styles, and general design principles (maybe a mini style guide or how to use components in front-end).

-   ops_runbook.md: Operational runbook -- instructions for deploying, monitoring, recovering the system (great for on-call or DevOps).

-   rls_postgres.md: Documentation on using Row-Level Security in Postgres for this project. This might cover how **RLS** is configured, how to add new policies for multi-tenancy, etc., since **RLS** is critical here.

-   security_hardening.md: Guidelines or checklists for securing the app (could cover environment config, dependency checks, secret management etc., some likely from [**SECURITY**] section).

-   install_guide.md: Steps to install or set up the development environment (and possibly deploy). Could include prerequisites, how to run locally, etc. They also mention user checklist linking to install_guide, so it's probably comprehensive.

-   frontend_design_bible.md: Possibly an extensive guide for front-end architecture and best practices (maybe covering React structure, state management, how to use Tailwind, etc.). "Bible" implies it's a central guiding doc for front-end code.

Why Useful: By listing these, the config tells the AI these docs exist (or should exist) and need maintenance. It implies any changes to related systems should reflect here. For example, if they change something about **RLS** usage, update rls_postgres.md. If they alter the CI/CD, update ops_runbook.md and security_hardening if relevant. It's part of keeping docs up-to-date and consistent. It also guides new developers: these are the key internal docs to read. The AI will ensure, per the alignment rules, that after code changes it might need to update one of these if the content is now outdated.\
Extra setting examples: Projects might not break docs into so many files, but it's good they did -- it shows focus on different areas. One could also have an **API** documentation file if there was a public **API**, but maybe not needed for this product.

**USER_CHECKLIST** | /docs/install_guide.md\
Interpretation: The "user checklist" is pointing to the install guide in docs. It's somewhat confusing name -- perhaps it means for a developer or admin user to ensure installation is complete, or maybe for the end user to check if everything is set up in their account? But likely it means a developer or deployer checklist. However, since it's exactly install_guide.md, perhaps they reuse that doc as a checklist for whoever is setting up the project (with bullet points to tick off).\
Why Useful: It signals that the install_guide is critical (they even flagged in **BUILD_CONFIG** that keeping it up-to-date is critical). The AI must make sure it's accurate because if not, new developers might fail to install or deploy properly. It being called a checklist suggests it's structured as a list of steps or items that must be done. Possibly used by the AI or user to verify environment completeness.\
Extra setting examples: They could have separate quickstart vs detailed installation, but seems combined. Ensuring this is always correct (like if a dependency is added, update install_guide) is indeed critical. The config double-emphasizes it by mentioning it in build config and here.

[**MODEL_CONTEXT_PROTOCOL_SERVERS**] -- Integrated Services (**MCP**)
------------------------------------------------------------

*This section appears to list external or third-party services used for various purposes like security scanning, billing, etc., possibly as part of a "Model Context Protocol" or just system integration.*

**SECURITY** | **SNYK**\
Interpretation: For security scanning of code and dependencies, the project uses Snyk. Snyk is a service that scans for known vulnerabilities in libraries, checks code for issues, etc. It suggests that in CI or development, they run Snyk or use its GitHub integration to catch security problems.\
Why Useful: The AI will integrate Snyk in the workflow, as we saw: run security audit tool is set to true in validation. Also, they mention **SNYK** false positives elsewhere, meaning Snyk will be a regular part of QA. Knowing it's Snyk specifically might mean the AI will use Snyk's **CLI** (which exists) or an **API**. It also means the AI's code suggestions should avoid introducing dependencies with known Snyk issues (since that'll flag in CI). So it might cross-check if a library is flagged by Snyk DB.\
Extra setting examples: Other projects might use GitHub Dependabot, **OWASP** Dependency Check, or others. They chose Snyk, a popular SaaS tool which also has an open source product. Possibly because it covers multiple languages.

**BILLING** | **STRIPE**\
Interpretation: The project will use Stripe for handling billing and payments. Stripe is a leading payment processor for subscriptions. This likely covers the paid plan upgrades, subscription management, possibly metered billing if they choose.\
Why Useful: The AI will ensure integration with Stripe's **API** for creating customers, processing payments, subscription tiers, webhooks for successful payments, etc. It influences how to implement the paywall gating: e.g., the AI might suggest using Stripe Checkout or the Stripe subscription **API** in Django. It also means storing things like Stripe customer IDs, subscription statuses in the database. Since Stripe is popular, there are libraries (like djstripe or custom code). The AI might consider using those.\
Extra setting examples: Alternatives are PayPal, Braintree, or self-hosted payments, but Stripe is developer-friendly and common for SaaS, so a good choice.

**CODE_QUALITY** | **RUFF**; **ESLINT**; **VITEST**\
Interpretation: The code quality tooling includes:

-   Ruff: a fast Python linter (it checks for style and some errors, akin to flake8/pyflakes but written in Rust). This will enforce Python code style and catch bugs.

-   ESLint: a linter for JavaScript/TypeScript, to enforce code style and catch JS/TS issues.

-   Vitest: a testing framework for front-end (like Jest but the Vite-native solution). This implies they will write unit tests for the front-end using Vitest. Possibly also for back-end, though not mentioned (maybe using PyTest but they didn't list it, interestingly).\
    These cover static analysis (ruff, eslint) and testing (vitest).

Why Useful: By specifying these, the AI should incorporate them into CI and possibly run them locally after changes. It also means following their rules when writing code (for example, if Ruff forbids unused imports, the AI should avoid that to keep linter happy; if ESLint has certain style rules, abide by them). Vitest indicates that they want tests for front-end. The AI, when writing front-end code, might also write corresponding Vitest tests. Similarly, one might guess for Python they plan to use PyTest even though not listed explicitly; maybe they rely on just using Django's test runner or incorporate security tests through Snyk and **RUFF** for style.\
Extra setting examples: They could add more (like Prettier for formatting, but maybe ESLint covers formatting with rules, or could assume Prettier is used implicitly with ESLint). They might have omitted PyTest by oversight or assume Django's test framework by default. But anyway, these three cover major areas.

**TO_PROPOSE_NEW_MCP** | **ASK_USER_WITH_REASONING**\
Interpretation: If the AI (or developers) think about integrating a new external service or Model Context Protocol server not currently configured, it should ask the user (or project owner) first and provide reasoning for why the new integration is needed. In other words, don't spontaneously add new major third-party services or tools without approval, and be prepared to justify it.\
Why Useful: This keeps the stack and integrations controlled. New services can introduce cost, complexity, or risk (security, data privacy concerns). By requiring a rationale and user approval, it ensures that every additional dependency is scrutinized. For the AI, if it thought "maybe use a different AI **API** or add a library for X that calls an external service," it should hold off and discuss with the user. Essentially, it prevents scope creep or unnecessary complexity from sneaking in.\
Extra setting examples: An alternative could be `**AUTO_APPROVE_IF_FREE**` or something, but they chose always ask, which is safer. It might rarely come up, but it's a good guardrail.

Next, the [**STACK**] section gives concrete tech stack choices. The AI will need to stick to these when generating code and not deviate.

[**STACK**] -- Technology Stack (Lightweight, Secure, Maintainable, Production Ready)
--------------------------------------------------------------------------------

*This section enumerates the chosen frameworks, languages, and components for each part of the system.*

**FRAMEWORKS** | **DJANGO**; **REACT**\
Interpretation: The main frameworks are Django for the back-end and React for the front-end. Django (Python web framework) will handle the server-side logic, **API**, database models, etc., while React (JavaScript/TypeScript library) will handle the client-side UI. This confirms the earlier front-end/back-end split with these specific technologies.\
Why Useful: It sets the development direction: the AI will use Django conventions (models, views or **DRF** if **API**, etc.) and React for building the UI (probably as a **SPA**). Both are well-known, widely supported frameworks which aligns with maintainability. It also means the AI should not propose something like Angular or Flask since the frameworks are decided. It ensures consistency in solution approaches (e.g., use Django's **ORM** for database, use React components and hooks for UI).\
Extra setting examples: If they had chosen others, like Express + Vue, those would be listed instead. Many modern stacks choose Django + React for an all-around solution. It's robust for an **MVP** and beyond.

**BACK**-**END** | PYTHON_3.12\
Interpretation: The back-end will use Python 3.12 (a relatively new version of Python, beyond 3.11). This specifies the exact major.minor version the code should target. Python 3.12 suggests they want to use the latest features available (maybe 3.12 is in pre-release as of 2025? Actually, by late 2025, Python 3.12 might be released or soon to be).\
Why Useful: This tells the AI to use Python 3.12 syntax and features (like maybe improved performance, new typing or pattern matching enhancements, etc.). It also affects dependency compatibility (the AI should ensure libraries are compatible with 3.12). Setting it now means they likely intend to run on the latest Python, which aligns with being forward-compatible. It also means no need to maintain older Python compatibility.\
Extra setting examples: If the environment only supported 3.10, they'd list that. 3.12 being listed suggests they might plan container or environment upgrade to that version. Another config could be specifying micro version if needed (but usually not necessary).

**FRONT**-**END** | TYPESCRIPT_5; **TAILWIND_CSS**; **RENDERED_HTML_VIA_REACT**\
Interpretation: The front-end specifics:

-   TypeScript 5: They will write the front-end code in TypeScript (version 5.x), not plain JavaScript. TypeScript ensures type safety. TS5 is a recent version, implying using modern features.

-   Tailwind **CSS**: They will use Tailwind **CSS** for styling. Tailwind is a utility-first **CSS** framework that lets you use classes to style components quickly. This fits the minimal styling approach; developers can add classes for spacing, colors, etc., without writing a separate **CSS** file for each thing. It's modern and keeps styles consistent.

-   Rendered **HTML** via React: This suggests that all **HTML** content will be rendered using React (i.e., no server-side templating for **HTML**, reinforcing that Django might just serve **JSON** APIs). React will produce the **HTML** for the browser dynamically. This might also hint that they are not doing server-side rendering initially (just client-side).

Why Useful: Using TypeScript improves code quality and maintainability on the front-end (catches errors early, documents types). Tailwind speeds up UI development and ensures a consistent design without handcrafting every **CSS** rule. It aligns with "clean, whitespace, modern" design. Also, since styling is deferred, Tailwind can allow quick adjustments when they do style. Rendered **HTML** via React confirms the architecture: the AI should implement all UI as React components that produce **HTML**; do not use Django templates. It also implies possibly an **SPA** that interacts with the Django backend via **API** calls (maybe using fetch/axios).\
Extra setting examples: Could have been plain JS (which would be riskier) or using **CSS** frameworks like Bootstrap (but Tailwind is more modern). If they wanted **SSR** or static generation, they might have specified Next.js or similar, but they didn't, indicating likely a simpler **SPA**.

**DATABASE** | **POSTGRESQL** // **RLS_ENABLED**\
Interpretation: The database is PostgreSQL, and importantly, Row-Level Security (**RLS**) is enabled. This means they'll use Postgres's **RLS** feature to enforce data segregation among tenants (organizations). Postgres is a powerful, stable choice that supports **JSONB**, which they need, and **RLS** ensures that even if a query doesn't have a filter, the DB itself will restrict rows by some policy (like each row has an org_id and the DB ensures a connection only sees its org's data).\
Why Useful: Postgres being the DB, the AI will use Django's PostgreSQL features (maybe specific fields like JSONField which maps to **JSONB**, etc.). The **RLS** enabled means the AI should plan database migrations to set up **RLS** policies, and code to set the appropriate role or session context (likely using PostgreSQL's `app_user`or `current_setting('org_id')` trick or using Django's multi-tenant support). It ensures security at the lowest layer, which is great for preventing data leaks between organizations. The AI must make sure any direct **SQL** accounts for **RLS**.\
Extra setting examples: They could have chosen MySQL or others, but Postgres is the pick for **RLS** and **JSON**. The comment `// **RLS_ENABLED**` highlights a critical config aspect. Possibly the AI needs to ensure that for each query or connection, it sets the proper Postgres role or session variable to activate **RLS** filtering (e.g., `**SET** myapp.current_org = X`). They likely documented how in rls_postgres.md.

**MIGRATIONS_REVERSIBLE** | **TRUE**\
Interpretation: Database migrations should be written such that they can be reversed (rolled back) reliably. `**TRUE**` means whenever a migration is created, it should include a `reverse` operation (Django migrations usually do this by default unless you write raw **SQL** without reverse). It also implies testing downgrades.\
Why Useful: In case of deployment issues or a migration that had a bug, being able to rollback the database schema is vital for quick recovery. It's a discipline to avoid irreversible operations (like deleting data without backups, or raw **SQL** that can't be undone). The AI, when generating migrations, should ensure `operations`have reverse counterparts or at least mark it possible. It also might avoid data migrations that lose info. This aligns with the reliability goal.\
Extra setting examples: If false, they might not care about rollback, but here they do. Some migrations (like dropping a column) are inherently irreversible unless you recompute data, so they might avoid such destructive changes unless absolutely needed, or ensure they double-check before finalizing.

**CACHE** | **REDIS**\
Interpretation: The project will use Redis as a caching layer. Redis is an in-memory key-value store often used to cache query results, session data, etc., for quick access. It's also going to be used as the broker for Celery tasks as noted later.\
Why Useful: Using Redis can greatly improve performance for expensive operations (like caching **RAG** search results, or storing in-progress state for the AI flows maybe). It also centralizes ephemeral data that needs to be shared between processes (like session info if using Redis for **JWT** blacklist or similar). The AI should incorporate caching for things like template search results (they mention caching found templates), or even caching AI results for repeated queries. Also, since they have multiple services (Django, Celery, possibly any websockets or so), Redis can coordinate.\
Extra setting examples: They might have used Memcached, but Redis offers more features (like persistence, data structures). It's the typical choice for Django caching and Celery.

**RAG_STORE** | **MONGODB_ATLAS_W_ATLAS_SEARCH**\
Interpretation: The Retrieval-Augmented Generation (**RAG**) data (templates, samples, snippets) will be stored in MongoDB Atlas using Atlas Search for querying. Atlas Search is a full-text search engine integrated into MongoDB Atlas, with capabilities for text and possibly vector search. They chose MongoDB likely because it's flexible (storing templates and samples of varying format easily as documents) and Atlas Search provides a convenient way to do the semantic or keyword searches they need without building a separate search infrastructure.\
Why Useful: This component will hold all external knowledge the AI uses in drafting (templates, sample <**REDACTED_FOR_IP**>, etc.). By using Atlas Search, they can do complex queries (like search by keywords, fuzzy match, maybe even integration with embedding-based search if Atlas supports vector queries---Atlas Search in 2025 may support synonyms or basic semantic but maybe not true vector, unless they map MiniLM vectors to fields). The AI will use the MongoDB Python driver for storing and retrieving, or possibly integrate an **ORM**-like layer. It also means that whenever new templates are discovered, they insert them here. This is separate from Postgres (which is for transactional data like user accounts, <**REDACTED_FOR_IP**>), which is wise because these **RAG** docs might be large and not strictly relational.\
Extra setting examples: They could have used a dedicated vector database or just Postgres full-text search. But Atlas Search likely gives them a powerful full-text search out of the box with minimal config, and Mongo suits storing **JSON** docs (like templates with fields as **JSON**). Also, using a separate store segregates concerns and possibly scales independently.

**ASYNC_TASKS** | **CELERY** // **REDIS_BROKER**\
Interpretation: Asynchronous/background tasks will be handled by Celery, using Redis as the message broker. Celery is a Python task queue that lets you run tasks in the background (like long-running or scheduled tasks) outside the main web process. Redis will serve as the broker (the queue system where tasks are pushed and workers listen) and possibly for result backend (unless they use something else for result).\
Why Useful: Many of the AI-related tasks (like heavy model calls, web scraping, **OCR**) may be offloaded to Celery to avoid blocking user requests. For example, generating a draft might be done asynchronously while the user sees a loading spinner. Celery ensures tasks can be distributed across worker processes or servers, improving scalability. Using Redis as broker is common for Celery due to its speed. The AI should structure tasks such as "fetch_and_create_template" (for unknown <**REDACTED_FOR_IP**> search), "generate_section_draft", "run_validation", etc., as Celery tasks. This also ties into metrics logging and possibly enabling a better user experience (they could even allow the user to navigate away while tasks complete).\
Extra setting examples: They could have chosen something like RQ (Redis Queue) or a cloud function, but Celery is mature and integrates well with Django. The Redis broker is already in place for caching, so it's convenient.

**AI_PROVIDERS** | **OPENAI**; **GOOGLE_GEMINI**; **LOCAL**\
Interpretation: The system can use multiple AI providers: OpenAI (for **GPT** models likely), Google Gemini(Google's AI model platform, presumably competitor to **GPT**-4/5), and Local (some local model for maybe embeddings or fallback if offline). This suggests a flexible architecture where certain tasks might go to different AI endpoints. Possibly they intend to use OpenAI's **GPT**-5 for main tasks, Google's model for some tasks (like they assigned web_scraping_model to Gemini), and a local model for embeddings (they mention MiniLM for local).\
Why Useful: Multi-provider support avoids vendor lock-in and could optimize cost or performance. For instance, they might prefer OpenAI for highest quality text generation, but could use Google for any features OpenAI doesn't have or for diversification. A local model ensures they aren't fully reliant on **API** costs for embeddings or maybe simple tasks. The AI will design the code to abstract the AI calls such that switching provider for a given task is easy (perhaps via an interface or configuration). It also allows fallback if one **API** is down or too expensive.\
Extra setting examples: They listed likely all they intend to use. In the future could add `**ANTHROPIC**` or others if needed, but they'd then follow the rule to ask user to add new. Right now these three suffice.

**AI_MODELS** | **GPT**-5; **GEMINI**-2.5-**PRO**; MiniLM-L6-v2\
Interpretation: These are the specific AI model names they plan to use:

-   **GPT**-5: Presumably the next-gen large language model from OpenAI (a step beyond **GPT**-4). This would be used for most heavy text generation tasks (planning, writing, etc.).

-   **GEMINI**-2.5-**PRO**: Possibly a specific tier/version of Google's Gemini model (2.5 could mean version 2.5, Pro might indicate high capability or paid tier).

-   MiniLM-L6-v2: A smaller local model (Microsoft's MiniLM, 6-layer version 2) often used for generating embeddings for semantic search, which aligns with them using it for embedding and search tasks.

Why Useful: It enumerates exactly which models correspond to the providers above. **GPT**-5 presumably via OpenAI, Gemini via Google's **API**, and MiniLM probably via local code (like from a library such as HuggingFace Transformers). The AI will incorporate calls to these models accordingly: e.g., use OpenAI **API** for **GPT**-5 tasks, use Google's PaLM/Gemini **API** for that model, and either run MiniLM locally or via a small service. It ensures the agent selects the right model for each role defined below (they actually specify which model is used for planning, writing, etc. next).\
Extra setting examples: If **GPT**-5 isn't available yet, they might currently use **GPT**-4 and then switch. But writing **GPT**-5 means planning to upgrade when available, demonstrating future-proofing. They might also fine-tune or have smaller local models for special tasks, but MiniLM is specifically an embedding model, not used for generation.

**PLANNING_MODEL** | **GPT**-5\
Interpretation: For tasks involving planning (like deciding sections, asking follow-up questions), they will use **GPT**-5. This is the model tasked with reasoning out the steps or checking completeness. **GPT**-5, being a top-tier model, should handle complex reasoning and instructions very well, which is needed for planning.\
Why Useful: Planning often requires understanding context and asking the right questions. By assigning the best model (**GPT**-5) to this, they increase the chance that the AI will generate good clarifying questions and not miss things. It sets that in code, when they call the planning function, they should call the **GPT**-5 **API**.\
Extra setting examples: If cost was a concern they could use **GPT**-4 or a smaller model, but they choose quality here. Or they could say **PLANNING_MODEL** = **GPT**-4 and **WRITING_MODEL** = **GPT**-5 to save cost on planning, but likely they consider planning equally critical.

**WRITING_MODEL** | **GPT**-5\
Interpretation: The model that actually drafts the <**REDACTED_FOR_IP**> text for each section is **GPT**-5 as well. So, generation of content uses the most advanced model for best language quality and coherence.\
Why Useful: Writing the actual <**REDACTED_FOR_IP**> text is the core value proposition, so they want the highest quality output. **GPT**-5 presumably offers improvements in fluency and adherence to instructions. This should produce persuasive and well-structured <**REDACTED_FOR_IP**> sections. Using the same model for both planning and writing keeps things simple (less juggling of multiple model capabilities) and ensures top performance.\
Extra setting examples: They might experiment with using a slightly cheaper model for writing (since writing might consume a lot of tokens), but quality is prioritized. Alternatively, they could decide certain smaller sections use a smaller model, but that adds complexity.

**FORMATTING_MODEL** | **GPT**-5\
Interpretation: The formatting and polishing tasks (grammar correction, clarity improvements, ensuring the output matches template style) are also done by **GPT**-5. So essentially, all heavy **NLP** tasks use **GPT**-5.\
Why Useful: This ensures consistency in writing style; the same model that wrote the text can refine it. **GPT**-5 can likely fix grammar with ease and understand context (where a smaller grammar checker might not understand the content's context as well). It also simplifies integration - one **API** for all text tasks. The cost might be high using **GPT**-5 for even formatting, but they may accept that or assume some fine-tuning or smaller method might not catch everything.\
Extra setting examples: They could use something like Grammarly **API** or a smaller model for grammar, but that might not catch <**REDACTED_FOR_IP**>-specific tone issues. Sticking to **GPT**-5 yields top results at cost of tokens. Possibly formatting a section is less token-intensive than generating it from scratch, so cost difference may not be huge anyway.

**WEB_SCRAPING_MODEL** | **GEMINI**-2.5-**PRO**\
Interpretation: For tasks involving web scraping or reading content from the web to get templates (the agentic search step), they'll use Google's Gemini 2.5 Pro model. This suggests that perhaps Gemini might have better capabilities at reading web content or maybe more lenient usage for that kind of operation. It might also diversify usage (maybe to not overuse OpenAI tokens if not needed). Possibly, Google's model could handle extraction of structured data from web pages or working with the content of PDFs or **HTML** better.\
Why Useful: By using Gemini for scraping tasks, they spread the load and possibly leverage any unique strengths of that model (maybe it's good at summarizing web content or has some browsing tool integration). It also might be a fallback if the OpenAI policy doesn't allow certain content (Google might allow different content? Or just to experiment). From an integration view, they'll call Google's **API** when they need to analyze web content found. This decouples that from other operations so they can optimize each.\
Extra setting examples: They could have used **GPT** for everything, but this indicates an intention to multi-source. Another rationale might be Google's pricing or data handling is better for large content chunks from web, but that's speculation.

**VALIDATION_MODEL** | **GPT**-5\
Interpretation: The validation step (checking completeness and alignment with requirements) uses **GPT**-5. No surprise here, given that requires understanding what's needed vs. what's given, which is complex reasoning and reading through the content thoroughly.\
Why Useful: **GPT**-5 can analyze long texts (if it has expanded context window by then) and instructions, so it's suited to verify if the <**REDACTED_FOR_IP**> answers all parts of the **RFP**. It can also pick up subtle omissions or mismatches. They want the best reasoning model here to avoid any oversight because a missed requirement could mean a failed <**REDACTED_FOR_IP**>.\
Extra setting examples: They likely wouldn't trust a smaller model with this either. Possibly one might attempt a rule-based check (like search for keywords), but an **LLM** doing it is more flexible.

**SEMANTIC_EMBEDDING_MODEL** | MiniLM-L6-v2\
Interpretation: For generating embeddings for semantic similarity (for **RAG** search, snippet similarity, etc.), they use MiniLM-L6-v2. This is a small, fast model that produces vector embeddings (like 384-dim) and is known for good performance in semantic search tasks relative to its size. It's likely run locally via a library (like SentenceTransformers).\
Why Useful: Using MiniLM locally means no cost per call and fast calculation of vectors. It's ideal for creating embedding of user queries or documents to feed into Atlas Search if they do hybrid search (Atlas might allow custom embeddings search, or they could just approximate with text search). At least, MiniLM can be used to implement semantic search on their own (like computing cosine similarity). It aligns with wanting an open source or local solution for embeddings (since they wouldn't want to call an **API** like OpenAI for every snippet vector due to cost and privacy).\
Extra setting examples: If not MiniLM, they might have used OpenAI's embeddings (like Ada v2) but that costs per call. Or other models like MPNet or similar. MiniLM-L6 is a good trade-off.

**RAG_SEARCH_MODEL** | MiniLM-L6-v2\
Interpretation: The same MiniLM model is used for searching in **RAG**. Possibly this means if they implement vector search themselves, they'll embed both query and docs with MiniLM and then compare. It might also mean that Atlas Search is configured to use custom embeddings from MiniLM for semantic search. Or simpler: they embed query with MiniLM, then search Atlas with those vectors if Atlas supports it (Atlas Search might support a stored embedding vector per doc and a $vectorSearch operator).\
Why Useful: Using the same model for indexing and querying ensures consistency (embedding space matches). It's efficient and again doesn't call external services. They likely store MiniLM vector for each chunk of text in Mongo (maybe as part of the doc), and at query time, compute vector for query and do similarity search (the note "semantic search technique: **ATLAS_SEARCH_SEMANTIC**" suggests Atlas might handle it, perhaps by approximate nearest neighbor on vectors stored). Either way, MiniLM is central to their **RAG** retrieval working well.\
Extra setting examples: If they didn't have Atlas Search, they might have used a separate vector DB or simpler method, but since they do, aligning the model is crucial.

**OCR** | **TESSERACT_LANGUAGE_CONFIGURED** // **IMAGE**, **PDF**\
Interpretation: For **OCR** (Optical Character Recognition) on images or PDFs, they are using Tesseract (likely the open source **OCR** engine), configured for the needed language (probably English, or multi-language if needed). The comment indicates it will handle both images and PDFs (for PDFs, they likely convert pages to images then run Tesseract, or use a **PDF** text extractor fallback if the **PDF** contains text). Language configured means if a user uploads, say, a Spanish document, they might adjust Tesseract to Spanish, but since default language is English, presumably Tesseract is set to English to improve accuracy.\
Why Useful: Tesseract is free and can run on their server without external **API**, which aligns with privacy and cost control. They might incorporate a library like pytesseract to use it. It allows them to extract text from files that the user uploads as answers (like we discussed in flows). It's important they configure it properly (resolution, language packs) for good results. By including it in the stack, they plan for that integration.\
Extra setting examples: They could have used an **API** like Google Vision or Adobe **OCR** which might be more accurate, but that would incur cost and require sending user docs to third-party, which is likely against privacy. Tesseract might need some tuning, but is usually fine for typed documents. They mention **PDF** too, so either they rely on PDFs being text-based (then could parse text directly), or if not, they convert to image and **OCR**.

**ANALYTICS** | **UMAMI**\
Interpretation: They will use Umami as the analytics platform. Umami is an open-source, privacy-friendly web analytics tool (alternative to Google Analytics). It likely ties to that data.<**REDACTED_FOR_IP**>.dk endpoint (maybe they host Umami at that subdomain). This means the front-end will have a small script to track page views and events, sending them to Umami.\
Why Useful: Using Umami aligns with privacy (no data leaks to Google, and allow user opt-out as they mentioned). It also means they control the data. The AI when implementing analytics will integrate Umami by including its script or using its **API**. It might also log custom events (like "ProposalCreated", "AISectionDrafted") for product metrics. Because it's self-hosted (on data.<**REDACTED_FOR_IP**>.dk presumably), it fulfills the privacy promise in [**PRIVACY**].\
Extra setting examples: If not Umami, they might have not had analytics or used a more heavy solution, but Umami is a good compromise. They might track minimal data given the 'fewest cookies' policy.

**FILE_STORAGE** | **DATABASE**; S3_COMPATIBLE; **LOCAL_FS**\
Interpretation: They plan to support multiple file storage backends for user-uploaded files (and perhaps generated files):

-   Database: storing files directly in the database (likely Postgres) as bytea or in a table. This might be for small files or just one option, though generally storing large files in DB is not ideal unless small or for ease of transactions.

-   S3_Compatible: using an S3-compatible object storage (like **AWS** S3 or DigitalOcean Spaces, MinIO, etc.) for files, probably in production to handle potentially large file storage with durability and offloading from the app server.

-   Local FS: storing files on the local file system (server disk), probably for development or small deployments.

This indicates they want an abstraction for file storage and might configure it per environment (like dev uses local FS for simplicity, production uses S3).

Why Useful: It gives flexibility. They can default to DB or local for quick start, but scale to S3 for production. Some files might be small textual data that storing in DB is fine (like snippet text might actually be in DB because it's just text), whereas PDFs or images are better in S3. Possibly "**DATABASE**" is referring to storing them as binary in Postgres for maybe easier **RLS** protection (since Postgres **RLS** could secure files as well, but S3 needs a different approach to secure per org; they might generate signed URLs). The AI should design a file storage interface that can handle these backends, probably by using Django's Storage system (it has backends for file storage including default local, or custom S3 via libraries like django-storages).\
Extra setting examples: They mention all three, so presumably they will choose one based on environment. It's unusual to see database storage, but maybe for things like small images or if they want an all-in-one backup, but likely they lean to S3 for large files. The config ensures the AI accounts for all.

**BACKUP_STORAGE** | S3_COMPATIBLE_VIA_CRON_JOBS\
Interpretation: Backups (presumably of the database and possibly file data) will be stored in an S3-compatible storage, and the backups will be done via cron jobs (scheduled tasks). This means they have a process to regularly dump the database and maybe compress it, then upload to an S3 bucket. Or likewise backup any file system content to S3. Cron jobs suggests it's not integrated into the app itself but rather a server/ops level job (maybe using **AWS** Data Pipeline or a server script).\
Why Useful: Ensures data durability. If the database server fails, they have copies in S3. Using S3-compatible likely means they could use **AWS** S3 or any similar service. Cron job approach is straightforward to implement. The AI might need to ensure documentation covers how to set this up (like provide a script or mention in ops_runbook). Also might coordinate with data retention policies - e.g., these backups should be pruned as needed.\
Extra setting examples: They might specify encryption or multi-region, but maybe not in config. S3-compatible is general, meaning it could be **AWS**, Wasabi, etc.

**BACKUP_STRATEGY** | **DAILY_INCREMENTAL_WEEKLY_FULL**\
Interpretation: The backup schedule is to do daily incremental backups and weekly full backups. This is a common strategy to balance backup size and restore convenience.

-   Daily incremental: each day, backup changes since the last full (or last incremental) -- these are smaller and quicker.

-   Weekly full: once a week, do a complete backup of everything.

So if restoring, you'd take the latest full and then apply incrementals since then.

Why Useful: It reduces storage needs compared to full backups every day, and also ensures a recent full backup that doesn't require applying too many increments (worst case apply 6 daily increments if restoring day before next full). It's a good compromise. The AI or ops should implement accordingly (maybe using something like `pg_dump` with **WAL** archiving for increments or a backup tool that supports incremental diffs, or simply dump daily but only store differences if file-level). Possibly they use something like `aws s3 sync`for files which inherently is incremental by checking timestamps. They might document this in ops_runbook.\
Extra setting examples: They could have chosen daily full for simplicity or real-time streaming. But daily incremental, weekly full is standard for moderately sized data.

The next section [**RAG**] was touched in the technical flows but let's cover explicitly:

[**RAG**] -- Retrieval-Augmented Generation Configuration
----------------------------------------------------

*This section describes how they manage stored knowledge for the AI (templates, samples, snippets) and how to search them.*

**STORES** | **TEMPLATES**; **SAMPLES**; **SNIPPETS**\
Interpretation: They have three categories of documents in their **RAG** system:

-   **TEMPLATES**: likely official or structured templates of <**REDACTED_FOR_IP**> (outline of sections, maybe with instructions for each section). These could come from <**REDACTED_FOR_IP**>s or crafted by the system.

-   **SAMPLES**: sample <**REDACTED_FOR_IP**> (maybe real <**REDACTED_FOR_IP**> or examples from public sources) that can be used as references for writing style or content. Perhaps categorized by type of <**REDACTED_FOR_IP**>.

-   **SNIPPETS**: small pieces of text that users save (as mentioned in user flow) or common text blocks (like mission statements, boilerplate). These are user-generated or possibly provided examples.

Why Useful: By separating them, the system can search or use them differently. For instance, when drafting content, the AI might pull **SAMPLE** content relevant to the section to see phrasing, but it will use TEMPLATEto ensure structure and required content. **SNIPPETS** might be inserted directly rather than used as context. The AI can treat each store with appropriate logic. It also aids indexing, because you might search templates by <**REDACTED_FOR_IP**> name, samples by topic, etc. The design likely means in MongoDB, they have a collection or a field distinguishing these types. It will also allow targeted management (like an admin can add new templates without touching samples).\
Extra setting examples: They could add more stores later, like "Regulations" or "Guidelines" if needed, but those might be considered part of templates or separate category. The three cover the essentials for content generation context.

**ORGANIZED_BY** | **KEYWORDS**; **TYPE**; <**REDACTED_FOR_IP**>; **CALL_PAGE_TITLE**; **CALL_URL**; **USAGE_FREQUENCY**\
Interpretation: The metadata used to organize (or index) **RAG** entries includes:

-   Keywords: tags or topics relevant to the content (e.g., "healthcare", "education", etc.). Possibly extracted or manually assigned.

-   Type: likely corresponds to store (template vs sample vs snippet), but could also mean type of <**REDACTED_FOR_IP**> (like "Official Template", "<**REDACTED_FOR_IP**> Sample", etc.).

-   <**REDACTED_FOR_IP**>: the organization or <**REDACTED_FOR_IP**> entity (like <**REDACTED_FOR_IP**>) associated with the template or sample. This is crucial because <**REDACTED_FOR_IP**> often align with <**REDACTED_FOR_IP**> preferences.

-   <**REDACTED_FOR_IP**>_page_title: the title of the <**REDACTED_FOR_IP**> or page from which a template was derived (like the name of the <**REDACTED_FOR_IP**>).

-   <**REDACTED_FOR_IP**>_url: the **URL** where the <**REDACTED_FOR_IP**> information was found (for traceability or future update checks).

-   Usage_Frequency: how often that snippet or sample has been used or referenced by users. This can be used to rank search results (more frequently used ones might be better), or to suggest popular snippets.

Why Useful: These fields allow flexible searching and filtering. For instance, when a user inputs a **URL**, the system can find templates where Call_url or keywords match. When writing a section, maybe filter samples by <**REDACTED_FOR_IP**> or type to give relevant style examples. Usage frequency can help highlight community-vetted content. It's also helpful for maintenance: if some samples never get used, maybe they're not very relevant or could be removed. The AI will ensure to capture these metadata when storing a new template from web search (like parse the title, <**REDACTED_FOR_IP**> from the page, etc.).\
Extra setting examples: They might include "year" if <**REDACTED_FOR_IP**> are annual or "region" if needed, but the listed ones are quite comprehensive.

**CHUNKING_TECHNIQUE** | **SEMANTIC**\
Interpretation: When breaking down large documents for storage (like a long sample <**REDACTED_FOR_IP**> or guidelines), they will chunk them using a semantic approach. That usually means splitting by sections or paragraphs that are semantically coherent, rather than just fixed size or random splits. Possibly they might use some algorithm to detect topic shifts or headings.\
Why Useful: It ensures each chunk of text stored in **RAG** is meaningful on its own and can be used as context without missing half the info. For example, storing each section of a sample <**REDACTED_FOR_IP**> separately means the AI can retrieve just the relevant section (like how a <**REDACTED_FOR_IP**> was written) rather than the whole <**REDACTED_FOR_IP**>. Semantic chunking yields more relevant search results because each result is a coherent thought or piece of content. The AI might implement this by splitting on headings or by a max length that doesn't cut off mid-sentence or uses an AI to decide where to split.\
Extra setting examples: A simpler alternative is fixed-length chunking (e.g., every 512 tokens), but that could break context awkwardly. They explicitly want semantic, which might require more upfront processing.

**SEARCH_TECHNIQUE** | **ATLAS_SEARCH_SEMANTIC**\
Interpretation: The search within the **RAG** store uses MongoDB Atlas Search with semantic capabilities. This likely means they plan to use Atlas's features for either text search that approximates semantic or a vector search if Atlas supports that (some versions of Atlas allow vector indexing by now, which could use the MiniLM vectors). The name suggests maybe Atlas has something specifically called "semantic search" perhaps meaning synonyms or concept-based search.\
Why Useful: Offloading search to Atlas means less custom code for search indexing and ranking. They can push a query through Atlas's search index and get back relevant docs sorted by a score. If integrated with MiniLM (store vectors and search by $vector), that covers true semantic. If not, they might at least use robust text search with weights on fields. This one line confirms the approach and the AI will thus focus on feeding data properly into Atlas (like ensuring the search index is defined to cover those fields and possibly using embeddings). It also implies they trust Atlas Search's performance and features enough to not need an external search engine like Elasticsearch or Pinecone.\
Extra setting examples: Could have been something like `**LOCAL_VECTOR_DB**` if they planned to run their own, or `BM25` if just keyword search, but they intend a semantic approach which likely means vector-based retrieval or at least concept-based retrieval in Atlas.

Now the [**SECURITY**] section is quite extensive, covering best practices and required measures.

[**SECURITY**] -- Critical Security Policies
---------------------------------------

*This section lays out security paradigms and specific measures that must be enforced.*

**INTEGRATE_AT_SERVER_OR_PROXY_LEVEL_IF_POSSIBLE** | **TRUE**\
Interpretation: Whenever possible, implement security measures at the server or proxy level rather than solely in application code. `**TRUE**` means yes, do that. Examples: using a reverse proxy (like Traefik, which they have) to enforce some headers (**CSP**, etc.), or the web server to handle **SSL** and **HSTS**, etc., instead of relying on app code inside Django. It might also refer to using OS-level security config (like file permissions, AppArmor, etc.) rather than just trusting code logic.\
Why Useful: Defense in depth and performance: Proxy or server-level enforcement is often harder to bypass and can handle things uniformly (for example, blocking malicious requests before they hit the app). It also frees the app from having to implement everything (e.g., Traefik can handle **SSL** redirect and **HSTS** injection, which is less overhead for Django). Setting this ensures the AI configures some settings in Traefik or other server config. Possibly "if possible" acknowledges some things must be in app code, but to offload what can be.\
Extra setting examples: If set false, they'd rely on the application for everything, but here they want a layered approach. They may incorporate security config in multiple places (traefik config, django settings).

**PARADIGM** | **ZERO_TRUST**; **LEAST_PRIVILEGE**; **DEFENSE_IN_DEPTH**; **SECURE_BY_DEFAULT**\
Interpretation: These are the core security philosophies guiding development:

-   Zero Trust: Assume no part of the system (user, network, service) is inherently trustworthy; always verify credentials and authenticity. Essentially, do not have a wide-open internal trust zone either.

-   Least Privilege: Give each user, process, or component the minimum access/permissions needed to perform its function and nothing more. E.g., if a service doesn't need DB write access, don't give it. Or a user in the app should only see their data (**RLS** fosters this).

-   Defense in Depth: Layer multiple security controls so that if one fails, others still protect the system. E.g., validate input in front-end and back-end, plus use DB constraints, etc. Or firewall plus app auth.

-   Secure by Default: Set defaults to the most secure settings, requiring intentional action to make something less secure if necessary. E.g., new user accounts might have minimal access until configured, or features off unless needed (like optional features are off by default, opt-in). Or shipping config with things like debug off, etc.

Why Useful: These paradigms ensure the team's mindset is always security-first. By listing them, the AI should consider them in every decision. For example, Zero Trust: verify user tokens on every request; with microservices, don't assume internal calls are safe, etc. Least privilege: design role scopes carefully, ensure DB user for the app has limited rights (maybe no drop tables). Defense in depth: do input validation both client and server side. Secure by default: perhaps ensure default config files (like default settings) are locked down (like by default no admin interface or default credentials). It's basically a checklist to measure designs against.\
Extra setting examples: Could include things like "Fail Securely" (fail closed rather than open), but those four cover a lot.

**CSP_ENFORCED** | **TRUE**\
Interpretation: Content Security Policy will be enforced. This means they'll set a **CSP** header in responses that whitelists sources of scripts, styles, etc., to mitigate **XSS** and injection of rogue scripts. True means definitely do this.\
Why Useful: **CSP** is a powerful web security layer that can prevent execution of malicious scripts even if injected. The AI will ensure to configure an appropriate **CSP** (like only allow scripts from their own domain and perhaps known CDNs for things like Stripe or analytics, disallow inline scripts, etc.). It might be easier to do at proxy level (Traefik can inject headers or serve the static pages with headers). Ensuring **CSP** is enforced by default adds an extra barrier against **XSS**.\
Extra setting examples: If false, they'd skip it, but they want it. Possibly they might set it in Django middleware or via Traefik config.

**CSP_ALLOW_LIST** | **ENV_DRIVEN**\
Interpretation: The allowed sources for **CSP** (the whitelist) will be driven by environment config. That implies they won't hardcode the domains in code. Instead, depending on environment (dev vs prod), they might allow different origins (like in dev allow localhost:3000 for hot reloading, in prod only allow <**REDACTED_FOR_IP**>.io). By making it env-driven, they can easily adjust without code changes.\
Why Useful: Flexibility and security: you can tighten **CSP** in production but loosen in dev if needed. For instance, in development, one might allow eval for certain tools or local IPs, but not in prod. Or incorporate environment-specific hostnames. This prevents mistakes of leaving a dev open **CSP** in prod because you can separate them. The AI should implement reading allowed origins from environment variables or config and apply them to **CSP** header.\
Extra setting examples: They could have a static list but this is better.

**HSTS** | **TRUE**\
Interpretation: **HTTP** Strict Transport Security is enabled. This header tells browsers to only use **HTTPS** for the domain and subdomains for a certain period. True means they will send the **HSTS** header (likely with a long duration and include subdomains).\
Why Useful: It prevents man-in-the-middle by ensuring that if someone tries to load http://<**REDACTED_FOR_IP**>.io, the browser will automatically switch to https (after the first visit sets **HSTS**). It also disallows click-through for invalid certs. It's a best practice for any site that uses **HTTPS**. The AI will set this, likely in Traefik or server config, to a high max-age (like 6 months or a year).\
Extra setting examples: If a site needed to be accessible via http (not recommended), they'd set false. But here definitely true.

**SSL_REDIRECT** | **TRUE**\
Interpretation: The server (or proxy) will redirect all **HTTP** traffic to **HTTPS**. True means they will implement a redirect so that any non-secure request is sent to the secure **URL**. This pairs with **HSTS** for security.\
Why Useful: Guarantee all communications are encrypted. If a user accidentally goes to http or a link is out-of-date, they'll be put on https automatically. The AI will configure Traefik or Django to enforce this (Django has **SECURE_SSL_REDIRECT** setting if using it, or Traefik can do a redirect). It's especially important before **HSTS** takes effect (first visit).\
Extra setting examples: Only would be false in dev maybe or if behind a proxy that already does it. Possibly in dev environment they'd skip it to allow local http usage, but in prod definitely on.

**REFERRER_POLICY** | **STRICT**\
Interpretation: The site will use a strict Referrer Policy. Likely they mean something like `strict-origin-when-cross-origin` or `same-origin` or `no-referrer` (strict could refer to not leaking full URLs). Possibly they intend the strictest viable: e.g., `no-referrer` which means no referrer header sent at all to other domains, or `strict-origin` which sends only the origin for same-site, nothing cross-site. Without the exact header value, I'd guess "**STRICT**" implies not sending referrers to external sites (to avoid leaking user info or internal paths).\
Why Useful: This protects user privacy (some URLs may contain sensitive data or simply they don't want other sites seeing they came from a <**REDACTED_FOR_IP**> app if maybe that's a privacy concern). Also it can help mitigate some **CSRF** and info leak scenarios by not exposing internal structure. The AI will thus set a Referrer-Policy header accordingly.\
Extra setting examples: They could specify exactly, but leaving it as "**STRICT**" suggests they want the most privacy-preserving that still allows function. Many use `strict-origin-when-cross-origin` as a balanced default these days.

**RLS_ENFORCED** | **TRUE**\
Interpretation: As previously indicated, Row-Level Security is enforced at the database level. True means they will actively set up **RLS** policies and ensure they are in effect for data segregation. In Postgres, you can turn on **RLS** per table and add policies. This being in security reaffirms commitment to multi-tenancy isolation.\
Why Useful: Even if the app had a bug that forgot an org filter, **RLS** would block unauthorized data. It's a second line of defense (defense in depth principle). The AI when creating tables will add `**ALTER** **TABLE** ... **ENABLE** **ROW** **LEVEL** **SECURITY**` and `**CREATE** **POLICY** ...` statements via migrations. Also ensure that queries run under a DB role or session context that triggers the correct filter. Possibly using the `current_setting('rls.organization_id') = org_id` approach or simply user-specific roles. It's advanced but strongly secures data.\
Extra setting examples: If false, they'd rely solely on app logic for multi-tenancy, which can be error-prone. They specifically want it, which is good.

**SECURITY_AUDIT_TOOL** | **SNYK**\
Interpretation: This repeats that they are using Snyk as the security audit tool. Likely they mention it here again to emphasize it's part of security process, not just context info. It means an audit (like the /audit command or CI runs) will run Snyk to check for vulnerabilities.\
Why Useful: Reinforces that dependency and known vulnerability scanning is mandatory. The AI will ensure Snyk is integrated (maybe via a GitHub Action or **CLI** invocation in CI). They might also use it for container scanning if any. It's one layer of defense. Already addressed above.

**CODE_QUALITY_TOOLS** | **RUFF**; **ESLINT**; **VITEST**; **JSDOM**; **INHOUSE_TESTS**\
Interpretation: Building on earlier code quality tools, they also list **JSDOM** and in-house tests now:

-   **JSDOM**: a JavaScript **DOM** emulation environment often used for running front-end tests in Node (Vitest likely uses **JSDOM** to simulate a browser so that React components and **DOM** manipulation can be tested without a real browser). By listing it, they ensure it's part of dev dependencies and to be used in tests.

-   In-house tests: they have their own custom tests, possibly integration or scenario tests not covered by unit tests. This might refer to tests they've written that don't fall under Vitest or standard unit tests (maybe Python tests for backend, or some custom automated QA scripts). Could also hint at something like Cypress (for end-to-end) but not mentioned explicitly; "inhouse" suggests they mean any tests they wrote specifically that might not be covered by standard frameworks (or maybe they have a test harness to simulate usage?).

Why Useful: It's making sure all facets of code quality are covered: static analysis (Ruff, ESLint), unit tests (Vitest for front, presumably Django's test framework for back implied), and possibly additional integration tests. **JSDOM** ensures their front-end tests have a realistic environment for **DOM**. "Inhouse tests" might be to catch domain-specific or full flow issues beyond what typical unit tests would cover. The AI should maintain and run these. If they write new features, they might add integration tests in whatever system "inhouse tests" refer to (maybe a separate test directory or script).\
Extra setting examples: Possibly they would add `pytest` or similar, but not listed, presumably their own tests cover backend. Or they assume Django's test runner (which uses unittest or pytest, but could have mentioned).

**SOURCE_MAPS** | **FALSE**\
Interpretation: In production, they will not serve or keep source maps for front-end code. Source maps help in debugging by mapping minified code back to original, but they can also expose source code to end-users if accessible. `**FALSE**` means when building for production, source maps should be disabled or not deployed.\
Why Useful: This prevents someone from reconstructing the original React source, which might contain sensitive logic or at least intellectual property. It also saves a bit of bandwidth. Downside is debugging production issues is harder, but they accept that for security. The AI will configure the build (with Vite) to not generate or to exclude source maps on deploy.\
Extra setting examples: Could be `**TRUE**` if they were okay exposing code or if it's open source anyway. But since the product may be closed source SaaS, better to hide it.

**SANITIZE_UPLOADS** | **TRUE**\
Interpretation: Any user-uploaded files will be sanitized. That could mean various things: for documents, remove macros or embedded scripts; for images, strip metadata and ensure it's a valid image; for PDFs, perhaps flatten or sanitize to remove JavaScript (PDFs can have embedded scripts or links), ensure they are not malicious. It might also mean virus scanning. But at least making sure uploads can't carry an exploit (like an **SVG** with embedded script, or an **HTML** pretending to be a .doc).\
Why Useful: Users may upload files either to provide content or to attach to <**REDACTED_FOR_IP**>, etc. Sanitizing protects both the platform (e.g., if someone uploads a malicious file intending to target an admin) and other users who might download those files (if collaboration means sharing files). It also helps with privacy (strip **EXIF** metadata from images, etc.). The AI should integrate some scanning library or use OS tools (like ClamAV for viruses, or a library for **PDF** sanitization). Or a simpler approach: only accept certain file types and possibly convert them to a safe format (like if a user uploads .docx, they might convert to **PDF** or text server-side, which inherently sanitizes by re-rendering content).\
Extra setting examples: If false, they'd trust users which is risky. There might be degrees, but True means do it. They might implement content scanning pipeline in Celery tasks.

**SANITIZE_INPUTS** | **TRUE**\
Interpretation: All user inputs (text fields, etc.) will be sanitized to prevent things like **XSS** or injection. E.g., stripping or encoding **HTML** tags in text, removing **SQL** wildcards if any direct **SQL** usage (though Django **ORM** usually safe), etc. Also perhaps normalizing strings to avoid hidden characters attacks. This is a typical security measure for any user-provided text.\
Why Useful: It stops cross-site scripting if some text might get rendered as **HTML** (like maybe the <**REDACTED_FOR_IP**> could be output in a preview; ensure any user-supplied text is escaped so they can't, for example, put `<script>` in an answer and then have the preview run it). It also can unify text format and remove known dangerous patterns. The AI will ensure to incorporate either built-in frameworks (Django has autoescaping by default in templates, but since React is used, React escapes content by default in rendering, except for anything deliberately set as **HTML**). If they allow some rich text (not indicated), they'd need to sanitize that specifically. In general, True means be paranoid: do not trust input.\
Extra setting examples: Could specify which technique (like use **OWASP** **ESAPI** or similar). But the devs just need to follow best practices (like always use parameterized queries, which Django **ORM** does, and always output with escaping in React, which is default unless using dangerouslySetInnerHTML). The AI might incorporate a library for sanitizing **HTML** if they allow user to input something like **HTML** content (but likely not needed if all inputs are plain text).

**RATE_LIMITING** | **TRUE**\
Interpretation: The application (or possibly proxy) will implement rate limiting to prevent abuse (too many requests in a short time, etc.). True means set up limits like how many requests per minute per IP or per user, etc.\
Why Useful: Protects against denial-of-service and brute force attempts (like trying many login attempts, or spamming the AI endpoints). It also helps manage costs on the AI side by not letting one user spam hundreds of completions. The AI should integrate a rate-limit mechanism. Possibly at the proxy (Traefik has some rate limit middleware) or in Django (there are libraries like dj-throttle or use of cache to count). They need to be careful to allow legitimate usage but block abuse.\
Extra setting examples: Could choose to skip in an internal app, but since this might be public SaaS, definitely needed.

**REVERSE_PROXY** | **ENABLED**\
Interpretation: A reverse proxy (like Traefik, as indicated later, or Nginx) is enabled in front of the application. This confirms the architecture where the proxy handles incoming traffic, **SSL**, routing to the web app or static files, etc.\
Why Useful: Reverse proxies improve security and manageability (they can do load balancing, hide internal network, handle **TLS**, etc.). The AI will consider that certain headers (like X-Forwarded-For) need to be handled due to proxy, and that some security measures might be implemented at the proxy (like earlier said **CSP** and **HSTS** could be done there). It's also relevant for making web sockets or other services possible under one domain.\
Extra setting examples: If disabled, the app would be directly exposed, which they aren't doing. They explicitly mention Traefik later as the proxy.

**AUTH_STRATEGY** | **OAUTH_ONLY**\
Interpretation: The authentication strategy for user accounts is OAuth only, meaning users will log in via external OAuth providers (like Google, GitHub, etc.) and not with local username/password. That aligns with the settings in [**AUTH**] below that said local passwords false. So sign-up and login are done through Google/GitHub/Facebook sign-in.\
Why Useful: It simplifies account security (no password storage to worry about, and also easier onboarding for users since they just use an existing account). It's also somewhat more secure since it offloads authentication to trusted providers (and they can have 2FA etc.). The AI will thus implement only OAuth flows in the front-end (likely using something like OAuth2 Authorization Code with **PKCE** to the Django server which uses social auth libs). The presence of this in security reminds to ensure it's done properly (validate OAuth responses, etc.).\
Extra setting examples: If they allowed local, they'd perhaps say `**OAUTH_PREFERRED**` or something, but they've locked it to only OAuth.

**MINIFY** | **TRUE**\
Interpretation: Static assets (JS/**CSS**) will be minified for production. True means yes, they will compress and remove whitespace, etc., to reduce load size. Vite by default does this on production build.\
Why Useful: Smaller files, faster loads, also slightly obsfuscates code (though not the main reason, security, but doesn't hurt). The AI will ensure that build is run in production mode which minifies. It's standard.\
Extra setting examples: If false, they'd serve raw code, which is unusual for prod.

**TREE_SHAKE** | **TRUE**\
Interpretation: The build process will perform tree shaking, meaning unused code (from libraries or components not imported) will be removed from the final bundle. Vite/Webpack does this for ES6 modules automatically when set properly. True means ensure it's happening (like don't do something to prevent it).\
Why Useful: Keeps the front-end bundle lean by excluding things not needed. It's important with large libraries to not include all of it if only a part is used. The AI should avoid coding patterns that defeat tree shaking (like dynamic requires in ways that bundler can't analyze, though in TS/ES6 it's mostly fine).\
Extra setting examples: If false, they'd carry dead code, which is not desired.

**REMOVE_DEBUGGERS** | **TRUE**\
Interpretation: They will ensure to strip out any `debugger` statements (and likely console logs as well, though console logs not explicitly mentioned but often done together) from production code. True means do it as part of build pipeline.\
Why Useful: `debugger;` if left in code can halt execution if devtools open. Also it's messy. There are Babel/terser options to remove those. Console logs could leak info too, maybe they also plan to remove them. The AI will check for and not leave any stray debug calls. Possibly an ESLint rule already catches this or the build minifier does it.\
Extra setting examples: Could mention console specifically, but it's implied.

**API_KEY_HANDLING** | **ENV_DRIVEN**\
Interpretation: All **API** keys (for third-party services, like OpenAI, Stripe secret, etc.) will be provided via environment variables or a secret management, not hard-coded. `**ENV_DRIVEN**` means the app expects these keys to be in environment settings at runtime (or in a secrets manager that injects env vars). The code should fetch them from env (like using os.environ or settings).\
Why Useful: Keeps secrets out of code and repository. Easier to rotate or change per environment (dev vs prod keys). The AI will ensure no keys are accidentally logged or stored, and that documentation instructs setting appropriate env values.\
Extra setting examples: If they had a separate config file for keys, they'd mention it, but env is typical for 12-factor apps.

**DATABASE_URL** | **ENV_DRIVEN**\
Interpretation: The database connection **URL**/credentials will also come from environment (like the standard **DATABASE_URL** env var). This means not storing DB password or host in code, and easily configurable for dev vs production.\
Why Useful: This decouples config from code, enabling easier deployment changes and better security (no plain creds in code). The AI will use something like `os.environ["**DATABASE_URL**"]` possibly with Django's `dj_database_url` to configure the DB connection.\
Extra setting examples: Usually done anyway, but explicitly stating it ensures no one writes a connection string directly.

**SECRETS_MANAGEMENT** | **ENV_VARS_INJECTED_VIA_SECRETS_MANAGER**\
Interpretation: They plan to use a Secrets Manager (maybe **AWS** Secrets Manager, Hashicorp Vault, or Docker Swarm secrets, etc.) which will inject the secret values as environment variables. This is more of deployment detail: rather than manually setting env vars or storing in .env files, they will store secrets in a central manager and the deployment platform will populate env at runtime.\
Why Useful: It's more secure and manageable at scale (no plaintext secrets in config files or CI logs; the secrets manager handles encryption at rest and controlled access). For the AI, this means assume at runtime the needed env vars are present and possibly not visible in code or logs (so be careful not to output them). It might affect how one deploys (like for GitHub Actions, might need to fetch secrets, etc.). It's a note mostly for ops.\
Extra setting examples: Could have been just static env or config files, but they want best practice with a secrets manager.

**ON_SNYK_FALSE_POSITIVE** | **ALERT_USER**; **ADD_IGNORE_CONFIG_FOR_ISSUE**\
Interpretation: If Snyk flags an issue that the team assesses as a false positive (not an actual vulnerability for their context or a mis-detection), they will: Alert the user (i.e., inform the team or responsible person that Snyk reported something but it's believed to be a false positive), and Add an ignore configuration for that specific issue so that Snyk won't keep flagging it in future scans or CI runs. Possibly this means updating a snyk policy file or using Snyk commands to ignore the **CVE** ID.\
Why Useful: This ensures the CI/CD pipeline isn't blocked by known false alarms, while still documenting them. By alerting user (maybe via an issue or Slack), they keep track that they've consciously ignored something, which is good for accountability. And ignoring in config means smoother future runs. They do, however, not ignore without making someone aware, which is good governance.\
Extra setting examples: They could choose to always fail build on any Snyk finding (even false, requiring manual override each time), but that slows development. By ignoring false ones in config, they streamline it after initial review.

The [**AUTH**] section next details specifics about account management with OAuth only, which we've partially gleaned, but let's outline it.

[**AUTH**] -- Critical Authentication Settings
-----------------------------------------

*This section configures how user accounts and authentication are handled, emphasizing OAuth.*

**LOCAL_REGISTRATION** | **OAUTH_ONLY**\
Interpretation: Users can only register via OAuth providers, not by creating a username/password on this platform. "Local registration" meaning signing up directly is not allowed, except via an OAuth flow. So there will be no "sign up with email & password" form.\
Why Useful: This reduces complexity and risk (no password storage). The platform will rely on Google/GitHub/Facebook to vet the identity. The AI will implement sign up flows that basically just redirect to those providers and then create an account upon callback. Possibly "local registration: **OAUTH_ONLY**" means they consider the creation of a local user record as binding to an OAuth account, but not an independent credential.\
Extra setting examples: Could have allowed both local and OAuth (most sites do), but perhaps due to audience (nonprofits, easier to click Google login than remember another password) and security, they restrict to OAuth.

**LOCAL_LOGIN** | **OAUTH_ONLY**\
Interpretation: Similarly, logging in is only possible via OAuth. So users will always click "Login with Google/GitHub/FB" rather than entering a password on <**REDACTED_FOR_IP**>. The platform will likely use JWTs for session (given "**SESSION_MANAGER**: **JWT**"). So no local login form.\
Why Useful: Uniform login method, one less surface for attacks (no brute forcing credentials on <**REDACTED_FOR_IP**>.io). Also easier user experience (especially if you're targeting not super tech-savvy, it's easier to delegate auth). This also means if someone inadvertently ended up with an account not linked to OAuth, they'd have no way to log in, but given registration is OAuth only, that situation shouldn't occur.\
Extra setting examples: Standard setups might allow email/password as fallback if someone doesn't have social accounts, but they chose not to manage that complexity.

**OAUTH_PROVIDERS** | **GOOGLE**; **GITHUB**; **FACEBOOK**\
Interpretation: The three external OAuth providers they will support are Google, GitHub, and Facebook. Users can authenticate via any of these. Google covers probably the majority of users (many have Google accounts), GitHub covers tech-savvy (maybe if the project appeals to open-source or dev audience too, but also many general tech users have GitHub nowadays), and Facebook might cover more **NGO** or community folks who use FB accounts. Notably missing: LinkedIn (could have been relevant for professional context, but maybe not needed), or Microsoft (less likely needed). But these three are a good mix.\
Why Useful: It defines which OAuth flows to implement (client IDs, secrets for each, etc.). The AI or dev will set up OAuth apps on these platforms and integrate via a library (like allauth or python-social-auth for Django). It also means you might present three buttons on login. It covers a broad user base.\
Extra setting examples: Could add LinkedIn, or **ORCID** for researchers (less common, maybe not needed), but they might keep it simple for now.

**OAUTH_REDIRECT_URI** | **ENV_DRIVEN**\
Interpretation: The OAuth redirect **URI** (the callback **URL** where providers send auth response) is configured via environment variables. Typically, for each provider, you must specify allowed redirect URIs in their app config, and also your app needs to know its own domain to form the callback link. By making it env-driven, they can set different URIs in dev vs prod (like `http://localhost:8000/callback` vs `https://<**REDACTED_FOR_IP**>.io/callback`). The code will likely read something like `**OAUTH_REDIRECT_BASE_URL**`from env and build provider-specific callback paths from it.\
Why Useful: Flexibility in different deployments, and not hardcoding domains. This also ties to security because if you accidentally had a wrong redirect, login might fail or be exploitable. It's good to manage it centrally. The AI will ensure to use env config for any absolute URLs needed for OAuth flow.\
Extra setting examples: Could derive from request, but better to explicitly set to avoid issues behind proxies, etc.

**SESSION_IDLE_TIMEOUT** | 30_MINUTES\
Interpretation: If a user is idle for 30 minutes, their session will expire. "Idle" likely means no requests or activity from the user for that duration (they might implement via a rolling **JWT** expiration or server session expiry). This is a security measure to log out inactive users.\
Why Useful: It reduces risk if someone leaves the app open on a shared computer or their token gets stolen (the token would expire soon if not active). 30 minutes is a common default (maybe a bit short for some, but given the nature of writing content, a user might be actively typing which presumably counts as activity if it triggers saves or so. If they go to lunch, they'd have to re-login). It aligns with their secure stance. The AI will configure the **JWT** expiry accordingly or use a sliding expiration mechanism. Possibly they will have refresh tokens to allow a longer login without re-OAuth if user is active. But if truly idle for 30, require re-auth.\
Extra setting examples: Could have opted for longer (some apps do hours or "remember me" options). But they want security prioritized.

**SESSION_MANAGER** | **JWT**\
Interpretation: They will use **JSON** Web Tokens for session management instead of server-side sessions or cookies alone. **JWT** suggests stateless auth: when a user logs in via OAuth, the server issues a **JWT** that encodes user info and maybe an expiry, and the front-end stores it (likely in memory or maybe a cookie if careful, but often in memory or localStorage if allowed, though localStorage has **XSS** risk; maybe cookie with HttpOnly if possible). Then each request includes the **JWT** (maybe as Authorization header). This is typical for SPAs.\
Why Useful: JWTs allow the front-end to directly call the **API** with an auth header, which fits the React front-end calling Django backend scenario. They are stateless, so scaling is easier (no sticky sessions needed). They can also embed claims (like roles, or org id) so the backend can enforce things (though with **RLS**, they'd also set context per token). The AI will likely use something like django-rest-framework-simplejwt or custom **JWT** handling.\
Extra setting examples: Could have used session cookies with the Django session framework, but that's trickier to coordinate with React on a different domain or port (**CORS**, etc.). **JWT** is a good call.

**BIND_TO_LOCAL_ACCOUNT** | **TRUE**\
Interpretation: Even though auth is via OAuth, they will create a local user account in their database and bind the OAuth identity to it. True means yes, do create a local user entry (with things like email, name, etc.), linked to the OAuth provider info. This way, the user exists in their system separate from the provider.\
Why Useful: This allows storing user-specific data (like their role in an org, preferences, usage metrics) in the app's DB keyed by user. Also, it allows linking multiple OAuth methods to one user (like same user can connect Google and GitHub and treat as one account, which their later settings cover). Without a local account, they'd have to rely on provider ID only, which is limiting for internal references. So the AI will implement something like: on OAuth callback, find or create a User record with that email and attach the provider details (like store OAuth **UID**, token if needed for reauth or avatar etc.).\
Extra setting examples: It's how most apps do it. Alternatively, some apps do not store local at all and use **JWT** issued by provider directly for trust (for enterprise stuff), but here they need local records.

**LOCAL_ACCOUNT_UNIQUE_IDENTIFIER** | **PRIMARY_EMAIL**\
Interpretation: The uniqueness of user accounts is determined by the primary email address. This suggests that if two OAuth providers return the same email, they should correspond to the same user (if `**OAUTH_SAME_EMAIL_BIND_TO_EXISTING**` is true as given). They won't allow two different local users with the same email. The primary email likely is one of the emails from the OAuth provider (all these providers give an email after consent; the user might have multiple emails on an account though). They will treat that email as the key.\
Why Useful: Email is a stable identity across providers. It helps merging accounts from different providers if user chooses (like log in with Google vs GitHub both give same email -> treat as one account). It also helps contacting the user or sending notifications. They don't have usernames or userIDs manually assigned, using email works well. The AI must ensure email uniqueness is enforced in the User model (maybe set unique=True on email field or handle merges).\
Extra setting examples: If they allowed multiple accounts with same email (some apps do if they have username login, but they don't want that).

**OAUTH_SAME_EMAIL_BIND_TO_EXISTING** | **TRUE**\
Interpretation: If a user signs in with an OAuth provider and that provider returns an email that matches an existing account's primary email, then the new login will be bound to the existing account rather than creating a duplicate. True means do this auto-binding. For example, user made an account with Google (email x), later tries logging in with GitHub which also has email x -> the system logs them into the same account.\
Why Useful: It prevents duplicate accounts, as long as the email is a reliable linking key. It's user-friendly (they might not even realize, it just logs them in). It requires that the email from provider is verified (Google and GitHub usually only provide if verified). There is a slight risk if someone used one person's email to sign up on another, but since OAuth gives only your own verified email, it's fine. The AI will implement logic to check email match after OAuth login and merge accounts if needed. Possibly using a library where this is a setting.\
Extra setting examples: If false, they'd create separate accounts for each provider even if email same, which could cause confusion and fragmentation of user data.

**OAUTH_ALLOW_SECONDARY_EMAIL** | **TRUE**\
Interpretation: Some OAuth providers (like GitHub) can provide a list of emails (primary and secondary emails associated with the account). This setting likely means they will accept and store secondary emails from the provider. Or allow linking a secondary email later. True implies the account can have more than one email associated (with one marked primary). Possibly they plan to let the user see/add secondary emails via linking another OAuth provider or via the provider's info.\
Why Useful: People often have multiple emails. If a user's GitHub has their work and personal email, one might be primary, but they might want both recognized. Also, if someone invites a user via a different email than their OAuth primary, having secondary helps match that. The AI might gather all verified emails from provider and store them (e.g., in a separate model or as list in user profile). It can help the scenario of someone trying to join an org via an email invite that matches their secondary.\
Extra setting examples: Could restrict to primary only (false), but true is more flexible.

**OAUTH_ALLOW_SECONDARY_EMAIL_USED_BY_ANOTHER_ACCOUNT** | **FALSE**\
Interpretation: If an email is already associated with one user (even as a secondary), it cannot be used as a secondary for another user. False means do **NOT** allow an email address to appear in two different user accounts. This prevents any overlap. Essentially, all emails in the system must be unique across the entire user base.\
Why Useful: To avoid confusion or security holes. If not enforced, you could have scenarios like: User A's secondary email is something that is primary for User B (maybe one user has multiple accounts and tries to merge?), that would conflict. So they disallow any email attaching if it's in use. They might have to handle cases where someone attempts to link an email already used: likely reject and alert to conflict. This ensures one person per email.\
Extra setting examples: If true, they would allow some messy scenarios, so false is right.

**ALLOW_OAUTH_ACCOUNT_UNBIND** | **TRUE**\
Interpretation: The user is allowed to unbind (disconnect) an OAuth provider from their account. True means in the UI, a user can remove, say, their GitHub link as long as they have at least one other method left (they cover that next). If a user no longer wants to use Facebook to login, they can disconnect it and maybe later connect a different one or just use Google.\
Why Useful: It gives users control, which is good for privacy and account management (e.g., if they quit Facebook, they might want to remove that link). It also ensures accounts aren't irrevocably tied to a service the user might lose (like if they lose access to a particular OAuth account, they could still log in via another if they set it up before, or unbind a compromised one). The AI should implement the ability to remove a provider from user's account (except the last one if min providers is 1 as below). Possibly using something like django-allauth, they can remove social accounts.\
Extra setting examples: Could be false to lock accounts to initial provider to reduce support issues, but that's not user-friendly.

**MINIMUM_BOUND_OAUTH_PROVIDERS** | 1\
Interpretation: Each user account must have at least one OAuth provider linked at any time. If they attempt to remove the only one, presumably the system will prevent it. So a user cannot have zero login methods.\
Why Useful: Otherwise they'd lock themselves out. By forcing at least one, it ensures the account is accessible. The AI will enforce this rule when user tries to unbind a provider (if they have two, they can remove one; if they have one, they must add another first before removing the first, or just can't remove the last). It's a safety measure aligning with allow_unbind above.\
Extra setting examples: If they allowed password login, min could be 0 since they could still login via password, but here OAuth only, so need at least 1.

**LOCAL_PASSWORDS** | **FALSE**\
Interpretation: They do not permit local password auth. False means the system will not allow setting or using a password for login. (Likely they won't even have that column or if they use Django's user model, they might leave password field blank/unused). If some user tries to do a password reset or something, they might disable that mechanism. It also indicates any local login endpoints are turned off.\
Why Useful: It simplifies security (no password storage or hashing to manage) and ensures everyone goes through OAuth, which often has better security (like 2FA). The AI should ensure features like "forgot password" or "change password" are hidden/disabled. Possibly they might still create a random password to satisfy Django's user model requirement but never use it.\
Extra setting examples: We know they want OAuth only, so this is consistent.

**USER_MAY_DELETE_ACCOUNT** | **TRUE**\
Interpretation: Users have the ability to delete their own account if they want. True means they likely will provide a UI option to "Delete My Account," which would presumably remove personal data according to policies. They mention later how deletion works (grace period etc.). It's an important user right especially with privacy laws.\
Why Useful: Empowering users to leave fosters trust and also complies with things like **GDPR**'s right to erasure. The AI will implement an account deletion flow (maybe immediate anonymization or a 7-day pending deletion as mentioned, with warnings). It also means handling consequences (like if they were the only admin of an org, what happens - maybe orphaned org deletion rule covers that).\
Extra setting examples: Some services disallow user deletion or make you contact support, but they choose yes to be user-friendly and legally safe.

**USER_MAY_CHANGE_PRIMARY_EMAIL** | **TRUE**\
Interpretation: Users can change which email is their primary one on the account. Since we allow multiple emails, one is marked primary (the unique identifier basically). True means they'll provide a way to promote a secondary email to be the primary (maybe in settings user chooses "make this my main email"). This could be needed if, say, a user changes jobs and wants their new email as primary contact.\
Why Useful: Allows users to maintain their account as their contact info changes. It might also trigger sending verification to the new email etc. The AI should ensure that switching primary doesn't violate uniqueness (the new one isn't used elsewhere except maybe already as secondary on this account). Also, if primary changes, that might be how invites or notifications get directed. It's part of account management flexibility.\
Extra setting examples: If false, whatever email they started with stays primary (less flexible). True is more user-centric.

**USER_MAY_ADD_SECONDARY_EMAILS** | **OAUTH_ONLY**\
Interpretation: Users can add additional emails to their account, but only via OAuth linking. That is, they cannot just type an email and verify it manually; they must connect another OAuth provider that uses that email. "**OAUTH_ONLY**" suggests the only way to add a secondary email is by linking an account that has that email as its verified email. They won't have a mechanism to just manually add an email address and send a confirmation link.\
Why Useful: It ensures that any email on the account has been verified by an OAuth provider, hence belongs to the user. This prevents someone adding an email that isn't theirs (except by creating a dummy OAuth account for it, which is at least some friction and requires access to that email anyway). It's simpler implementation too, piggybacking on OAuth verification. The AI will implement secondary email addition by linking new provider accounts. If a user wants to add a personal email, they'd use "Login with Facebook (with personal email)" to attach it, for example. They won't implement an independent email verification system.\
Extra setting examples: They could have allowed adding email via a code/email verification process, but that duplicates what OAuth does and is extra dev work. This approach is secure and streamlined given they already require OAuth.

Now [**PRIVACY**] is crucial; let's parse those.

[**PRIVACY**] -- Critical User Privacy Policies
------------------------------------------

*This section outlines how the application will handle user data, focusing on minimization, transparency, and user rights.*

**COOKIES** | **FEWEST_POSSIBLE**\
Interpretation: The application will use as few cookies as absolutely necessary. Possibly only one for session (if using **JWT** in cookie) or maybe none if **JWT** is in local storage (but local storage isn't recommended for security, HttpOnly cookie is safer, albeit **CSRF** risk). "Fewest possible" means they will not add any tracking or unnecessary cookies (e.g., no third-party cookies for tracking, etc., and maybe not even persistent cookies if they can avoid it).\
Why Useful: Minimizing cookies is better for privacy (less data on client, less tracking). Also easier to manage for cookie consent laws (maybe if they only use an essential cookie for session, they might not need a cookie banner depending on jurisdiction). The AI will ensure to not introduce cookies for trivial things and possibly to use other storage for ephemeral data. If they use Umami, note: Umami can be configured cookieless or with a minimal cookie; likely they'll do cookieless mode. They might have one cookie for **JWT** or use Authorization header instead. But even **JWT**, if stored in a cookie, is one cookie.\
Extra setting examples: Could say "none if possible", but since login likely requires a token either in cookie or localstore, at least one item.

**PRIVACY_POLICY** | **FULL_TRANSPARENCY**\
Interpretation: The Privacy Policy will be written and practiced with full transparency. That means it will clearly inform users of all data usage in a straightforward way, no hiding behind legalese or burying details. They likely commit to telling exactly what data is collected, how it's used, etc.\
Why Useful: Transparency builds trust and meets legal requirements (**GDPR**, etc.). The AI should ensure the privacy policy document (likely to be written) lists all relevant items like analytics data collected, retention times, AI usage (like if user data is used to train models or not, probably not?). Full transparency suggests they won't do sneaky data uses that aren't communicated. It's also tied to the "friendly tone" below.\
Extra setting examples: Some policies are minimal to cover legal but might not highlight all usage; here they want to go beyond minimal.

**PRIVACY_POLICY_TONE** | **FRIENDLY**; **NON**-**LEGALISTIC**; **CONVERSATIONAL**\
Interpretation: The tone/style of the privacy policy should be friendly, not full of legal jargon, and written in a conversational manner that an average user can understand. This likely means they'll produce a human-readable summary or the entire doc in plain English, maybe with a touch of approachable language. Possibly similar to how some companies (like Mozilla or basecamp) write plain-language policies. They want it to feel like the company is open and user-aligned, not trying to confuse.\
Why Useful: Users often skip or misunderstand privacy policies because they're dense. By making it conversational, users might actually read it and trust the service more. It also aligns with their audience (nonprofits, researchers) who may not have legal teams to parse it, so clarity is key. The AI might even help generate or refine that document with this tone in mind.\
Extra setting examples: Could be formal if they wanted, but they specifically want a friendly vibe.

**USER_RIGHTS** | **DATA_VIEW_IN_BROWSER**; **DATA_EXPORT**; **DATA_DELETION**\
Interpretation: They acknowledge key user rights:

-   Right to view their data in the browser: presumably some account section where users can see all data the system has about them (like profile info, content they've provided, maybe usage logs if requested?).

-   Right to export their data: the user can download their data (like <**REDACTED_FOR_IP**> they've written, profile info, etc.) likely in a standard format (maybe a **JSON** or an archive of their <**REDACTED_FOR_IP**>).

-   Right to deletion: user can have their personal data deleted (account deletion triggers removal of personal info).\
    These align with **GDPR** rights of access, portability, and erasure.

Why Useful: It's user-centric and legally important. The AI will ensure features to support these: maybe an export function to get all <**REDACTED_FOR_IP**> in one zip or such; a profile view that displays stored data; and deletion workflow as we saw. It shows compliance with common privacy principles.\
Extra setting examples: Possibly also "Right to correct data" (they might allow editing profile info which covers that, not explicitly stated but likely allowed), or "Right to object to processing" (they partly cover analytics opt-out below). But the main ones are covered.

**EXERCISE_RIGHTS** | **EASY_VIA_UI**\
Interpretation: The method for users to exercise the above rights is easy and through the user interfaceitself (not by emailing support or hunting down a process). For example, an account settings page with buttons for "Download your data", "Delete account", "View your data". They want no friction.\
Why Useful: Making it easy means they are confident in their privacy stance (not trying to hide these options). It also ensures compliance since if it's easy, there's no excuse for not honoring requests quickly. The AI will design the UI accordingly (account management page with these options).\
Extra setting examples: If they were shadier they'd bury these behind contacting support or forms; they explicitly avoid that.

**DATA_RETENTION** | **USER_CONTROLLED**; **MINIMIZE_DEFAULT**; **ESSENTIAL_ONLY**\
Interpretation: The strategy for data retention is:

-   User Controlled: The user should have control over how long their data is retained (within some bounds perhaps). For example, user can delete things when they want, or maybe set retention preferences.

-   Minimize Default: By default, the system keeps data for the shortest time necessary. They won't hold onto data indefinitely just because. Only essential data is kept by default if the user doesn't intervene.

-   Essential Only: They will not retain data that isn't essential to the service functioning. This implies no hoarding of extra info for undefined future use; if it's not needed, it's not kept. Possibly they won't keep logs with personal info longer than needed for debugging, etc.

Why Useful: This is a strong privacy approach. It means, for instance, if a <**REDACTED_FOR_IP**> is done and maybe after some period they might remove it unless user wants it saved. Or they won't keep user activity logs beyond what's needed for metrics then aggregate or delete them. User controlled suggests maybe in settings a user can choose retention times or to auto-delete content after a project finishes. The AI might have to implement such features (like auto-delete old data or an archive with time limit, which indeed they mention archives for 42 days). Minimizing by default means if user does nothing, data might auto-expire eventually (except what's essential like billing records legally required, etc.).\
Extra setting examples: It's quite user-first. They might have to balance some things (like a user might want them to keep <**REDACTED_FOR_IP**> until deleted manually; hopefully they won't surprise-delete something because "minimize by default" could conflict with user expectation to keep their work as long as they have the account). But I think they mean not collecting extra stuff.

**DATA_RETENTION_PERIOD** | **SHORTEST_POSSIBLE**\
Interpretation: For any data they do keep, they aim to keep it for the shortest duration feasible. This is a general principle (some things must be kept by law like billing info or backups for a certain time, but they will not exceed those). They likely will define specific periods in practice (which they do in following lines for certain categories). This line is just the guiding theme: only keep data as long as needed.\
Why Useful: Reduces risk of breaches (less data stored, less to steal or leak), and shows respect for user privacy. The AI in designing features should incorporate automatic deletion schedules and ask "Do we really need to store this?" for new data points.\
Extra setting examples: Could set fixed times for everything but they break it down later.

**USER_GENERATED_CONTENT_RETENTION_PERIOD** | **UNTIL_DELETED**\
Interpretation: Content that users create (like their <**REDACTED_FOR_IP**>, answers, files, etc.) will be retained until the user deletes it. So the service won't auto-delete user-generated content on its own (except if user is inactive for a very long time or deletion requested). Basically, as long as the account is active and user hasn't chosen to remove that content, it stays. This is likely because users expect their work to remain available. It's different from activity logs or stale data.\
Why Useful: It clarifies that the product isn't going to purge, say, a <**REDACTED_FOR_IP**> after X time unless user is gone. Users have control over their content's lifecycle (create, keep as long as they want, or remove when they want). It aligns with being a productivity tool; you don't want to surprise-delete someone's documents. So "until deleted" essentially defers to user action.\
Extra setting examples: They could impose something like auto-delete after 2 years of no edit or something, but they don't, except they do mention account inactivity later.

**USER_GENERATED_CONTENT_DELETION_OPTIONS** | **ARCHIVE**; **HARD_DELETE**\
Interpretation: When a user decides to delete content (like a project), they will offer at least two options: Archive and Hard Delete.

-   Archive: This likely means the content is soft-deleted or moved to an archived state where it's not accessible normally but can be recovered within some timeframe.

-   Hard Delete: Permanent deletion with no retention. Possibly after a confirmation or after a grace period or something, the data is actually erased from system.\
    So users can either just archive (maybe if they want to declutter but keep for records) or fully delete.

Why Useful: Not everyone wants immediate irreversible deletion because they might regret it. Archiving provides a safety net. Yet offering hard delete ensures compliance if someone truly wants data gone. The AI will implement a system where deleting something likely first archives it (or gives a choice UI: "Do you want to archive or permanently delete?"). If archive chosen, data is flagged but kept; if hard delete, it might still archive then auto-purge after grace period or skip archive. Possibly they implement "delete" button does archive by default (like many apps do a soft delete, then you have to go to an archive bin to permanently delete).\
Extra setting examples: They did exactly both, which is nice. Some apps only soft-delete and purge later by policy, but they explicitly allow user to do a direct hard delete if desired (or maybe after archive, with a small waiting period to be safe).

**ARCHIVED_CONTENT_RETENTION_PERIOD** | 42_DAYS\
Interpretation: Archived content will be retained for 42 days (6 weeks) before being permanently deleted. That implies if user archives something, it stays recoverable for 42 days. After that time, the system will auto-hard-delete it. 42 days is a somewhat specific choice (maybe they wanted 6 weeks instead of 1 month (30) or 2 months (60), splitting the difference or maybe a meaning like "six weeks" just felt right, or they jokingly used 42 because of Hitchhiker's Guide to the Galaxy meaning of 42, but likely just a good timeframe).\
Why Useful: It gives users a window to realize if they need something back (sometimes people realize a few weeks later). 42 days covers a bit more than one month, which is generous. It also ensures the system isn't left holding "archived" stuff indefinitely (which would violate the data minimization pledge). The AI will implement a job (likely daily) that purges any archived items older than 42 days. They should also inform users (maybe at archive time or via an email a few days before final deletion as courtesy perhaps) that their archived item is going to be gone soon, but they didn't explicitly say that email except for inactivity deletion they did mention warnings. Could be nice to do though.\
Extra setting examples: Many services use 30 days for trash retention, they gave a bit more, which is fine.

**HARD_DELETE_RETENTION_PERIOD** | **NONE**\
Interpretation: If a user chooses hard delete (immediate deletion), the retention period is none - meaning the data is not retained at all beyond deletion request (aside from maybe backups which are separate, but logically it's gone from live system). "**NONE**" suggests they won't keep any copy intentionally for any period (some services still keep backups for X days). They likely will try to scrub it even from backups if possible or have a note that backups are pruned regularly, etc. But from user perspective, it's gone.\
Why Useful: It respects the user's desire for complete removal. If a user says hard delete, they want it gone and the system will not hold it. Possibly in privacy policy they'd clarify backup caveat if any, but presumably they'd remove from backups on next cycles since backups retention is separate and limited anyway.\
Extra setting examples: Some might have "we keep hard-deleted data 7 days in case of mistake", but they treat that as archive scenario separate. Hard delete is truly final.

**USER_VIEW_OWN_ARCHIVE** | **TRUE**\
Interpretation: Users are allowed to view their archived items (likely through an "Archived items" section or filter). True means the interface will offer a way for users to see what they have archived during that 42-day window.\
Why Useful: So they can confirm something is archived, perhaps restore it if needed. It's akin to a "Trash" folder concept where you can see what's in trash. If they couldn't view archive, they'd have to contact support to retrieve, which is not user-friendly. The AI will ensure there's a UI for archived projects/files. Possibly those items are not in main list but in a separate "Archived <**REDACTED_FOR_IP**>" list, etc.\
Extra setting examples: Some apps hide archived by default or require support to restore; not here.

**USER_RESTORE_OWN_ARCHIVE** | **TRUE**\
Interpretation: Users can restore their archived content themselves without admin intervention. True means if something is archived (within that retention), the user can hit a "Restore" or "Unarchive" button and it becomes active again.\
Why Useful: It empowers users to correct mistakes easily. They don't need to email admin "I accidentally archived X". It's immediate. The AI will implement an unarchive action. This also implies archived content is probably retained with all data intact, just flagged differently, making restoration feasible (not like partially wiped).\
Extra setting examples: If false, they'd have to contact support to restore, which is friction.

**PROJECT_PARENTS** | **USER**; **ORGANIZATION**\
Interpretation: A project (<**REDACTED_FOR_IP**> project) must belong either to an individual user or to an organization. They allow both contexts. That means they are supporting both personal projects and team projects. Possibly when creating a project, if the user is in an org, they choose to create it under their personal space or under an org space. If under an org, presumably all members can collaborate (depending on roles). This is common, like how GitHub repos can be user or org.\
Why Useful: It gives flexibility: If someone is just using it on their own (not part of an org team), they still can use the app under their user context. For teams, the org context centralizes it. The AI will implement fields in project model like `owner_user` and `owner_org` (one of those set). They have to enforce **RLS** for both (like **RLS** might allow either user = current user or org = one of current user's orgs they have access to). It's more complex than just org-only or user-only multi-tenancy, but manageable. It also implies if a user is part of multiple orgs, they might have to pick which org context they are currently working in (like Slack has org switch, etc.), or at least when creating a project choose an org. Possibly default to an org if exists.\
Extra setting examples: They could have forced everything to an org (requiring everyone to create org even for solo use), but they explicitly allow user projects, which lowers barrier for single users.

**DELETE_PROJECT_IF_ORPHANED** | **TRUE**\
Interpretation: If a project is left without a parent (orphaned) - e.g., the organization it belonged to was deleted and there's no user owner, or maybe if it belonged to a user who deleted their account and no org, etc. - then the project will itself be deleted. True means they will not keep projects around if they have no valid owner reference.\
Why Useful: It's a data cleanup measure; orphaned data could otherwise linger with no one able to access it, which is unnecessary and a privacy risk. If an org is deleted, all its projects go too (since presumably org deletion is triggered by either all members leaving or admin deciding). If a user is deleted and they had personal projects not part of an org, those become orphaned and thus will be deleted as well (makes sense because if the user left, their personal stuff should go). The AI will ensure appropriate cascading deletion or a process to remove those when the parent is removed.\
Extra setting examples: If false, they'd need to assign orphaned things to someone else or admin, but that could violate privacy (giving someone else's data). So deletion is the correct approach.

**USER_INACTIVITY_DELETION_PERIOD** | **TWO_YEARS_WITH_EMAIL_WARNING**\
Interpretation: If a user account has been inactive for two years (24 months) - meaning they haven't logged in or used the service in that time - the account will be deleted, but with an email warning beforehand. That implies they will send one or more emails to the user saying "Your account is about to be deleted due to inactivity, please log in if you want to keep it," probably giving them a window to respond (like maybe 30 days after the email). If no action, then they delete the account and its data.\
Why Useful: This helps minimize data retention by not holding onto accounts indefinitely if they are abandoned. It also frees up resources (like if they had limited user counts, etc.). Two years is a fairly long period, so they ensure that only truly long-gone accounts are removed. The email warning is critical to not surprise someone who might still want their account but just didn't log in (maybe didn't need to for a while). The AI should implement a scheduled job to check last active date and email those beyond 23 months with a notice, then at 24 months do deletion.\
Extra setting examples: Could be shorter or longer; two years is moderate (some do one year, some never auto-delete accounts). They found a middle ground and ensure user is alerted, which is good practice.

**ORGANIZATION_INACTIVITY_DELETION_PERIOD** | **TWO_YEARS_WITH_EMAIL_WARNING**\
Interpretation: Similarly, if an entire organization is inactive for two years (likely meaning none of its members have logged in or no activity on any projects), then the organization and presumably all its data will be deleted after warnings. They would email organization owners or all members to warn them prior.\
Why Useful: Orgs might get abandoned if the team stops using it or dissolves. Clearing them eventually helps keep the system clean and free of stale data, and respects privacy (some org data might contain sensitive info, better not keep it if the org is defunct). The warning ensures if the org is still needed, someone can log in to reset the timer. The AI might implement similar to user: track org activity (maybe an org is considered active if any member is active, or if any project updated). They need to define how to measure "org inactivity". Possibly if no member login in 2 years.\
Extra setting examples: They matched it with user period, likely because an org being inactive usually correlates with its members being inactive.

**ALLOW_USER_DISABLE_ANALYTICS** | **TRUE**\
Interpretation: Users are given the option to opt out of analytics tracking. True means maybe in settings there's a toggle "Do not track my usage for analytics". If toggled, either the front-end stops sending events to Umami or the backend filters out that user's events. Or they provide a cookie to opt out since Umami can respect that (Umami is privacy friendly and might allow an opt-out parameter).\
Why Useful: Some users (especially maybe NGOs or researchers concerned with privacy) may want to not be tracked. This complies with privacy norms (e.g., **GDPR** requires allowing opt-out of non-essential tracking). It also shows goodwill beyond law if not strictly required. The AI will implement an opt-out mechanism that when enabled, either disables the analytics script for that user (maybe sets a cookie that Umami knows to skip, or just never calls the tracking events in code). They should also honor do-not-track browser signals possibly.\
Extra setting examples: Some might not offer opt-out if they deem their analytics "essential" or fully anonymized, but giving choice is better.

**ENABLE_ACCOUNT_DELETION** | **TRUE**\
Interpretation: They will implement and enable account deletion (which we already saw user may delete account true). This just restates that deletion feature is active. Possibly they mention it to emphasize that maybe it's not only allowed by policy but actually implemented ("enabled"). True means yes, there's a functioning deletion process.\
Why Useful: Ensures that at launch, account deletion is not just theoretical but available. Some services in early days ask you to email support to delete account; here they specifically enable it in-app. It's critical for privacy compliance.\
Extra setting examples: If false, they'd have a contradiction because user may delete was true; so likely this flag is to actually toggle the feature in UI depending on readiness, but they have it on.

**MAINTAIN_DELETED_ACCOUNT_RECORDS** | **FALSE**\
Interpretation: They will not maintain any records of accounts after they are deleted. That means they won't keep a stub like "User X (deleted)" in the database. They likely fully remove the user row and all related personal data. They won't, for instance, keep an entry to block reuse of the same email (some systems keep a "tombstone" record to prevent re-signup with same email, but that contradicts not maintaining records; they'd allow re-signup which is fine). They might not even keep the user's ID in logs (or they might anonymize logs).\
Why Useful: Truly honoring deletion means no lingering personal data. It's stricter than some companies do (some keep minimal info for audit or to prevent fraud; they explicitly choose not to). It shows strong privacy stance but has trade-off: someone could sign up again with the same email and they wouldn't necessarily know it's the same person as before (which might be fine, or could allow some exploitation like repeated free trial abuse, but they didn't mention such counter measure, maybe they accept that risk or will address differently).\
Extra setting examples: If they had legal requirement to keep something (like invoices), they'd keep those separate from user personal profile. But "maintain records: false" means wipe nearly all.

**ACCOUNT_DELETION_GRACE_PERIOD** | 7_DAYS_THEN_HARD_DELETE\
Interpretation: After a user requests account deletion, there will be a 7-day grace period before the account is permanently deleted. After 7 days, a hard delete occurs. This implies they implement a pending deletion state: user initiates deletion, account maybe gets deactivated and marked for deletion on X date. They might allow user to cancel deletion in that window (like log back in to abort deletion). If no action, at 7 days the deletion job runs to remove it all.\
Why Useful: It serves as a safety net for users who might change their mind or who triggered deletion accidentally or in distress and then reconsider. Also if a malicious person got into account and tried to delete it, the real owner has time (assuming they see email or try to log in) to recover. It's standard (e.g., Facebook does 30 days, many do 14 days). They chose 7 days, which is relatively short but sufficient in many cases. Possibly because they want to minimize retention of data user wanted gone.\
Extra setting examples: Could be longer, but shorter is more privacy-forward. The AI must ensure to send an email when deletion is requested (so user knows and can intervene in that week). At day 7, everything goes (including any content, presumably aside from what legally must remain, if any).

All these privacy measures ensure users can trust the service with their data and that it complies with regulations and good practices.

Next, [**COMMIT**] guidelines ensure repository hygiene:

[**COMMIT**] -- Version Control Commit Standards
-------------------------------------------

*This section defines rules for commit messages and repository content.*

**REQUIRE_COMMIT_MESSAGES** | **TRUE**\
Interpretation: Commits must have a message (not an empty or default message). Likely enforced via commit hooks or just by convention. True means they should always provide a descriptive commit message for each commit. No committing without a message.\
Why Useful: It maintains a clear history. Possibly part of CI (like if commit message missing, maybe the commit hook rejects it). The AI when auto-committing code (if it does automated commits) should always add a message.\
Extra setting examples: Could be obvious but some inexperienced devs commit with no message or trivial ones; they want to avoid that.

**COMMIT_MESSAGE_STYLE** | **CONVENTIONAL_COMMITS**; **CHANGELOG**\
Interpretation: They expect commit messages to follow Conventional Commits style (e.g., prefix like "feat:", "fix:", "chore:", etc.), and be suitable for generating a changelog. Conventional Commits is a standardized format that includes a type, scope, and description, which can be parsed to automatically produce release notes. This suggests commit messages like "feat(ui): add collaboration invite key feature" or "fix(auth): resolve OAuth linking bug".\
Why Useful: It ensures commit log is structured, which aids in auto-generating a changelog (the second keyword indicates they intend to produce **CHANGELOG** from commits). It also encourages thoughtful messages that categorize changes (features, fixes, breaking changes, etc.). The AI if writing commits should use this format properly.\
Extra setting examples: They explicitly mention conventional_commits and changelog, which is common in projects that use semantic versioning. Another style could be something like just freeform but they prefer this formal approach.

**EXCLUDE_FROM_PUSH** | **CACHES**; **LOGS**; **TEMP_FILES**; **BUILD_ARTIFACTS**; **ENV_FILES**; **SECRET_FILES**; **DOCS**/*; **IDE_SETTINGS_FILES**; **OS_FILES**; **COPILOT_INSTRUCTIONS_FILE**\
Interpretation: This is a list of patterns or file types that should not be committed/pushed to the repository. It likely forms part of .gitignore.\
They include:

-   Caches: (like .cache directories, or pip cache, node_modules maybe considered caches or separate build artifact)

-   Logs: any log files generated should be ignored (like *.log)

-   Temp_files: e.g., *~ files, .tmp, etc.

-   Build_artifacts: like compiled output (dist/, build/, possibly static bundles, etc. - those should not be in **VCS** if they can be built)

-   Env_files: like .env containing secrets or config, definitely not to commit.

-   Secret_files: any other file containing secrets or keys (maybe config files with secrets or keystore)

-   Docs/*: interestingly they say docs/* to exclude - perhaps they mean not to push built docs or maybe not to push some subfolder of docs if they generate something? But they earlier have docs in code as markdown that should be committed. It's possible they mean if docs site generates **HTML** or something, don't commit those. Or maybe "**DOCS**/*" refers to something else (maybe they consider lengthy documentation or outputs should not go to main branch? It's a bit odd since they did include docs in repo by listing them). Could be a miscommunication; maybe they mean exclude docs generated from code (like Sphinx html).

-   IDE_settings_files: like .vscode/, .idea/, etc.

-   OS_files: like .DS_Store (macOS), Thumbs.db, etc.

-   Copilot_instructions_file: explicitly, do not push the file that contains these instructions (since probably it's in the repo only locally for the AI to read, but not meant for commit). It's considered sensitive internal config not to share.

Why Useful: Keeps the repository clean of files that don't belong or pose security risks (env/secrets). It also helps avoid accidentally leaking credentials. The AI, if controlling commits, should ensure these patterns in .gitignore. Particularly copilot-instructions should not leak, which might have internal strategies or just clutter.\
Extra setting examples: It's basically a thorough .gitignore. Possibly they might add the node_modules which I'm not sure if implicitly covered by "build artifacts" (though node_modules is more dependency than build artifact; typically you do ignore it). Maybe caches covers node_modules? Usually you'd list node_modules explicitly, so maybe an oversight. But since they mention build artifacts probably they consider node_modules not to commit either. The AI should confirm the .gitignore covers all these.

Finally, [**BUILD**] and [**BUILD_CONFIG**] and [**ACTION**] etc., which are more technical for deployment and how the agent should operate.

Let's parse [**BUILD**]:

[**BUILD**] -- Deployment and Build Configuration
--------------------------------------------

*This section describes how the application is packaged, served, and deployed.*

**DEPLOYMENT_TYPE** | **SPA_WITH_BUNDLED_LANDING**\
Interpretation: They will deploy as a Single Page Application for the app itself, with a bundled landing page. That likely means the marketing landing page (the homepage explaining the product, maybe docs, etc.) is part of the same front-end build or served by the same server (perhaps as static content) rather than a separate site. "Bundled landing" suggests that the landing page (which could be just index route of the React app or separate static **HTML**) is included in the deployable. Possibly they might have a static landing page built with the same build tools or just an **HTML** served by the proxy that then hands off to the app for the web app routes. But likely the whole site is an **SPA** including public homepage.\
Why Useful: Simplicity in deployment (one artifact handles both marketing site and app). They won't maintain two separate codebases for site and app. This might mean the React app has routes for "Home", "Features" (public pages) as well as protected app pages, or they pre-build a static landing and serve it. The AI will ensure that either approach is considered in routing (like maybe if user not logged in and goes to root, show landing content, if logged in go to dashboard - or separate domain for app vs landing but they said bundled so probably same). It affects how the front-end is structured.\
Extra setting examples: Could have had a static generated marketing site with **SSR** and the app separate, but they combined.

**DEPLOYMENT** | **COOLIFY**\
Interpretation: They plan to use Coolify for deployment. Coolify is an open-source self-hosted PaaS (Platform as a Service) that can deploy apps via Docker, handle CI/CD, etc. It's like a Heroku alternative that you run yourself. This means the project will be set up to deploy to a server running Coolify.\
Why Useful: They likely have a **VPS** with Coolify to manage the containers for this app. The AI might not need to deeply integrate with Coolify besides providing Dockerfile or config that Coolify expects. It's a hint that the deliverables will be containerized and integrated into a Coolify pipeline (which often just monitors a repo and deploys on push).\
Extra setting examples: Could have been Docker Compose manual, or Kubernetes, but they chose Coolify which automates a lot.

**DEPLOY_VIA** | **GIT_PUSH**\
Interpretation: Deployments are triggered by pushing to the Git repository (likely to a specific branch like main or a release branch). This suggests a CI/CD pipeline is set such that on push, the building and deploying happens (maybe via GitHub Actions and Coolify or via Coolify's built-in Git webhooks).\
Why Useful: It's the common approach for continuous deployment. The AI should ensure that main branch stays deployable and tests pass before pushing (they do gating in CD config below). It also means no manual **FTP** or so, it's integrated.\
Extra setting examples: Could have been manual triggers or scheduled, but push is typical.

**WEBSERVER** | **VITE**\
Interpretation: This is a bit unusual because Vite is mainly a dev server and build tool. Perhaps they mean the front-end is served by Vite's preview or dev server in development, but for production, typically you'd serve static files (unless using **SSR** or something). Or maybe they plan to run a Node server using Vite's preview (which can serve a build). Alternatively, maybe they use Vite **SSR** to serve content. But likely they mean they're using Vite's dev server for development and for preview in staging, but in production, static files will be served by a web server (could be Traefik or something static-serving). It's listed under build though, so possibly they treat Vite as the "webserver" in dev environment section.\
Why Useful: If they indeed use Vite preview for some reason in production, it would serve the built assets and maybe handle proxies to **API** (if not using a separate static file server). However, usually you wouldn't do that in production (you could just have Traefik serve static or use a **CDN**). This is a bit confusing in context. But I'll interpret it as either: they intend to use Vite's preview (which is essentially an Express static server) in container to serve the app's UI files, or it's just referencing that Vite is the main tool handling web content.\
Extra setting examples: They might have used Nginx to serve static front-end if not using Vite preview. If Coolify deploys a static site, it might spin up an Nginx automatically. I suspect they won't run Vite dev in production, probably an oversight or a shorthand for "Vite dev server in dev mode, static in prod". We'll keep an eye.

**REVERSE_PROXY** | **TRAEFIK**\
Interpretation: They will use Traefik as the reverse proxy for the app. Traefik will handle incoming requests, routing them to appropriate containers (maybe one for backend, one for frontend static, etc.), and handle **TLS**, etc. They already indicated reverse proxy enabled. Traefik is often used in container environments like Coolify because it dynamically configures routes using container labels.\
Why Useful: Traefik is modern, works great with Docker (Coolify likely uses it by default). The AI should ensure to include proper Traefik labels in the Docker compose or config to route `<**REDACTED_FOR_IP**>.io` to the correct service and set up things like **HSTS**, etc., according to earlier rules. It might also handle **ACME** (Let's Encrypt) for certificates.\
Extra setting examples: Nginx is alternative but they prefer Traefik's automation.

**BUILD_TOOL** | **VITE**\
Interpretation: They use Vite as the build tool for the front-end (to bundle and optimize the React app). Already indicated in the stack where they said React & Tailwind (likely integrated via Vite). So nothing new here except confirming it's for building (which will produce static assets).\
Why Useful: Vite is known for fast dev and good production build using Rollup under the hood. The AI should ensure to have a production build command via Vite.\
Extra setting examples: It's the obvious one since they chose Vite/React.

**BUILD_PACK** | **COOLIFY_READY_DOCKERFILE**\
Interpretation: They will provide a Dockerfile that is ready for Coolify's build process. Possibly meaning the repository will contain a Dockerfile configured in a way that Coolify expects (maybe multi-stage to produce an image with both front-end and back-end or separate? Possibly one image with backend and one with static for front-end). Or could be a slight misnomer, but likely they intend to use Dockerfile method in Coolify (Coolify can either use Heroku buildpacks, or just build using a Dockerfile if present). "Coolify Ready" suggests they have to tailor it so that environment variables and healthchecks align with what Coolify expects.\
Why Useful: Ensures smooth deployment on Coolify with minimal manual config. The AI will craft a Dockerfile (or multiple if needed) accordingly. Possibly a multi-stage Dockerfile: one stage builds front-end with Vite, the other stage is base for Django, then copy assets and run server, etc. So one container runs Django + serves static or runs backend, and maybe static served by whitenoise or so. Or they might deploy front and back as separate services, but then need two Dockerfiles or a docker-compose. However, since they said deployment type **SPA** with bundled landing, they might serve static with Django or a simple server, meaning one container approach.\
Extra setting examples: If not using Dockerfile, they could use buildpack or just source build on target, but they want explicit Dockerfile.

**HOSTING** | **CLOUD_VPS**\
Interpretation: They host on a Cloud Virtual Private Server. That implies they're not using something like **AWS** App Runner or Heroku, but rather a **VPS** (like DigitalOcean droplet, **AWS** EC2, etc.) where they run Coolify and everything. It's basically self-hosted environment.\
Why Useful: It's cost-effective and gives control. The AI should keep in mind that things like file storage S3 might be external or local (if they run MinIO on **VPS** or connect to DigitalOcean Spaces, etc.). Also might have to ensure to not assume cloud managed services beyond what specified.\
Extra setting examples: Could be on-prem or serverless, but they went with a stable known approach.

**EXPOSE_PORTS** | **FALSE**\
Interpretation: They do not intend to expose container ports directly to the internet. Likely everything goes through Traefik which handles mapping domain to containers. `**FALSE**` means in Docker context, the containers won't have host ports open (just internal). Traefik will route via internal Docker network. This is more secure.\
Why Useful: It prevents someone bypassing Traefik's configured routes/policies by hitting an IP:port directly. Also means one can't accidentally have two things trying to use same host port. The AI should ensure Docker compose doesn't have `ports:` mapping (Traefik uses labels instead to route).\
Extra setting examples: If true, they'd open say backend on 8000, which is not needed with Traefik.

**HEALTH_CHECKS** | **TRUE**\
Interpretation: They will have health check endpoints and monitoring to ensure containers are running properly. True means presumably each service (like backend) should have a health check **URL** (maybe `/health` returning 200) that Coolify/Traefik can ping, and the deployment pipeline will verify health after deploy. They might also have container healthcheck in Dockerfile.\
Why Useful: Automatic detection of failing containers and ability to rollback if new deploy is not healthy. It aligns with "require health check 200" in build config later. The AI will implement a simple endpoint in Django (maybe via a library or just a view that checks DB connection etc.) and possibly add `**HEALTHCHECK**` in Dockerfile.\
Extra setting examples: Some skip it and just rely on container up, but they explicitly want it.

Now [**BUILD_CONFIG**] with CI/CD specifics:

[**BUILD_CONFIG**] -- Continuous Integration/Deployment and Build Settings
---------------------------------------------------------------------

*This section outlines specific CI/CD pipeline requirements and build strategies.*

**KEEP_USER_INSTALL_CHECKLIST_UP_TO_DATE** | **CRITICAL**\
Interpretation: It's marked as critical priority to keep the install guide (User Checklist) updated. That means any change that affects how to set up or run the project must be reflected in the install_guide.md immediately. They consider this extremely important (maybe because they had pain with outdated docs in the past). Possibly might fail CI if docs not updated? Not sure if they enforce automatically, but maybe part of code review.\
Why Useful: So new developers or deployers can always rely on the documentation to work. It's critical because if it falls behind, someone could be blocked. The AI, when making changes (like adding a new dependency that needs an OS package or env var), should update the checklist doc as part of after_action_alignment.\
Extra setting examples: They set **CRITICAL** to emphasize priority; they might even check diffs to see if relevant changes touched the docs or not.

**CI_TOOL** | **GITHUB_ACTIONS**\
Interpretation: They use GitHub Actions for Continuous Integration (and possibly partially for deployment). GitHub Actions will run tests, linters, etc. It's integrated with the GitHub repo.\
Why Useful: Good choice as code is likely on GitHub and Actions is convenient. The AI might provide config files (.github/workflows) to run actions for things like lint/test, etc. They already specify what runs in CI. Also possibly to build Docker images if needed (though Coolify might handle build itself on push). But they did list CI runs which implies actions do those.\
Extra setting examples: Could have used GitLab CI, Jenkins, etc., but GH Actions is easier to set up in a new project.

**CI_RUNS** | **LINT**; **TESTS**; **SECURITY_AUDIT**\
Interpretation: The CI pipeline will run the linter (Ruff, ESLint), run tests (back-end tests and front-end tests presumably), and run the security audit (Snyk). These are done likely on each push or PR.\
Why Useful: Automated assurance of code quality and security before merge. They separated them from CD (where build and deploy also happen). Possibly CI runs on every push, CD only on main branch or when triggered. The AI will set up GH Actions jobs for these steps, and ensure they all must pass.\
Extra setting examples: They indeed included all major checks except building the image, which is reserved for CD.

**CD_RUNS** | **LINT**; **TESTS**; **SECURITY_AUDIT**; **BUILD**; **DEPLOY**\
Interpretation: The Continuous Deployment pipeline (when triggered for deploy) will also run Lint, Tests, Security audit (maybe again or ensure latest code still passes them), then if all good, perform the Build (build container or artifacts) and then Deploy (to the server). This presumably only runs for production deployment, possibly triggered manually or on push to main depending on config.\
Why Useful: It acts as a gate: if any quality check fails, it won't deploy broken or insecure code. Even though CI runs those earlier, they double-check in CD just in case something changed or the branch had new commits since last CI. It's belt and suspenders to avoid deploying something failing.\
Extra setting examples: They might skip repeating tests in CD if they're confident in CI, but here they ensure everything is passed again or at least gating. Possibly they mean the same tasks run but logically, they won't deploy if CI already said fail. GH Actions might allow requiring passing status. But writing them explicitly in CD ensures if someone manually triggers a deploy outside main, it still checks.

**CD_REQUIRE_PASSING_CI** | **TRUE**\
Interpretation: The deployment pipeline will only proceed if CI was passing. True means if tests/lint aren't green on the commit, it should not deploy. This is probably enforced by config or by status checks. It's redundant with above but emphasizes that CD depends on CI success.\
Why Useful: Prevents deploying unverified code. It's likely that the main branch is protected to require CI to pass, but also this setting ensures an extra check. The AI might ensure the CD job checks that prior jobs (lint/test) succeeded.\
Extra setting examples: Could allow override but they say require true.

**OVERRIDE_SNYK_FALSE_POSITIVES** | **TRUE**\
Interpretation: They will override known false positives in Snyk (as mentioned in security), meaning CI/CD should not fail on those once flagged and marked. True means implement ignoring them so they don't block pipeline. Probably in Snyk config or in CI steps they might use `snyk test --ignore-policy` or ensure they maintain an ignore list.\
Why Useful: It ensures that if Snyk erroneously flags something and they've decided it's safe, they won't let that keep breaking builds. They already plan to do that.\
Extra setting examples: Already discussed above basically.

**CD_DEPLOY_ON** | **MANUAL_APPROVAL**\
Interpretation: Deployment to production requires a manual approval step. That likely means even after CI passes, someone (like an admin or DevOps engineer) has to click "approve" or "deploy" for it to actually push to production. Possibly done via GitHub Actions environment protection rules or in Coolify's UI (maybe they have a hold on deploy until manual confirm).\
Why Useful: It adds a final human check to ensure nothing crazy is going out (especially since this is pre-release stage product, they might not want continuous automatic deploy yet, or want to group releases). It also could allow review of changes or scheduling deploy at low-traffic time. The AI can configure GH Actions with an environment that requires approval.\
Extra setting examples: Some teams auto deploy every commit to main (could have been 'auto'), but manual is safer in early stage.

**BUILD_TARGET** | **DOCKER_CONTAINER**\
Interpretation: The target format for builds is a Docker container. So they containerize the app (makes sense since using Coolify). They are not building, say, a VM image or just code tarball. This confirms they'll produce an image. Possibly a multi-container (backend and front separate) but maybe one container as we discussed.\
Why Useful: It's needed for Coolify and easy to run anywhere. The AI will ensure Dockerfile and GH Actions to build/publish it if needed.\
Extra setting examples: If building a static site, target could be static files, but since there's backend, container covers it.

REQUIRE_HEALTH_CHECKS_200 | **TRUE**\
Interpretation: After deployment, require that health check endpoints return **HTTP** 200 OK (healthy) in order to consider the deployment successful. True means likely the CD pipeline or Coolify will check /health or similar. If it doesn't return 200, it will trigger a rollback or mark failure.\
Why Useful: Catches any runtime issues quickly (like maybe the server failed to start properly, etc.). The AI will ensure the health endpoint is robust and returns 200 only if fully functional (maybe it checks DB connectivity too).\
Extra setting examples: If false, they'd deploy and maybe only monitor externally. They want automated verification.

**ROLLBACK_ON_FAILURE** | **TRUE**\
Interpretation: If a deployment fails (health check fails, tests fail at last minute, etc.), automatically rollback to the previous stable version. True means they will not leave a broken version running. This suggests using a method to quickly revert (maybe Coolify has versioning or they spin up new container and only switch traffic if healthy, which is common in blue-green or similar).\
Why Useful: Minimizes downtime or bad experience because if new version doesn't start or is unhealthy, the system continues on old version seamlessly. The AI might not implement rollback logic itself (the platform might do it), but possibly in GitHub Actions they'd re-deploy the last known good image. Or if using container orchestration, they might not take down old until new is up.\
Extra setting examples: They clearly prioritize reliability here.

Finally, [**ACTION**], [**BEFORE_ACTION_CHECKS**], [**PLANNING**], [**RUNTIME**], [**VALIDATION**], [**ALIGNMENT**], [**CLEANUP**], [**AUDIT**], [**REFACTOR**], [**DOCUMENT**] specify the agent's operational guidelines. These are likely meant for the AI itself on how to conduct tasks when user issues commands /agent, /audit, etc. It's akin to how the AI should proceed with tasks. It's very meta: basically telling how ChatGPT (as coding assistant) should behave in phases.

We should interpret them likely as how the agent should plan and implement code changes. However, since the question is we (the AI) summarizing the instructions, we should interpret them as instructions to "the AI agent" on how to perform changes.

We actually already integrated a lot of those in analysis above, but let's systematically do it:

[**ACTION**] -- Bound command and sequence for code changes:\
**BOUND**-**COMMAND** | **ACTION_COMMAND** (which is '/agent', as defined).\
Interpretation: The action workflow is triggered by the `/agent` command. So when user uses /agent, the agent should follow this [**ACTION**] sequence.\
Why Useful: It explicitly ties the config to the user command.

**ACTION_RUNTIME_ORDER** | **BEFORE_ACTION_CHECKS**; **BEFORE_ACTION_PLANNING**; **ACTION_RUNTIME**; **AFTER_ACTION_VALIDATION**; **AFTER_ACTION_ALIGNMENT**; **AFTER_ACTION_CLEANUP**\
Interpretation: It enumerates the exact order of phases the agent should go through for an action: first do pre-checks, then plan, then execute changes, then validate, then align docs, then cleanup.\
Why Useful: Ensures nothing is skipped and logical flow.

Now [**BEFORE_ACTION_CHECKS**] we already interpreted line-by-line above. It's basically the conditions to check before doing anything, and how to handle them:

-   If better solution exists -> propose alt

-   If not best practices -> propose alt

-   If user insists overriding best practice is allowed

-   If legacy/deprecated/obsolete/reduntant code involved -> propose refactor for approval

-   If any conflicts or confusion -> ask user

-   If security risk -> abort and alert

-   If high impact or conflicting info or missing info -> ask user

-   If no tasks specified or discovered -> ask user or propose next steps (like if after /agent there's nothing, maybe the agent should suggest something).

-   If unable to fulfill or too complex -> propose alternative approach

-   If task is huge (too many files or changes) -> break into phases

-   If any external issues (rate limited, **API** fail, timeout, unsupported requests/platform etc.) -> alert user.\
    These ensure the agent does sanity checks and interacts with user to clarify or abort rather than doing something wrong.

[**BEFORE_ACTION_PLANNING**]:

-   Prioritize tasks list: True means agent should order tasks by importance or logic.

-   Preempt for certain issues: if security issues or failing tests or major inconsistency exist, handle them first even if user asked something else. (Focus on what's blocking).

-   Preemption reason required: agent must explain why it's diverting to fix X before Y.

-   Post plan to chat with: compact change intent, goal, files, risks, validation needs, reasoning.

-   Await approval: yes, wait for user to OK the plan.

-   Override approval with user request: if user explicitly said go ahead no need to show plan, then skip waiting.

-   Max phases 3: don't make more than 3 phases in a plan.

-   Cache prechange state for rollback: snapshot code in case need revert.

-   Predict conflicts: try to foresee conflicts and plan around them.

-   Suggest alternatives if unable: always have a fallback plan if initial plan isn't doable.

[**ACTION_RUNTIME**]:

-   Allow unscoped actions: false, meaning agent should have a specific scope (which files/areas to change) not just "change everything".

-   Force best practices: true, follow them even if user didn't mention.

-   Annotate code extensively: means add comments to code explaining changes or to clarify tricky parts.

-   Scan for conflicts progressively: as you make changes, continuously check if there's conflict in integrated code.

-   **DRY**: True, do not repeat code.

-   **KISS** but only if not compromising other critical aspects (**KISS** allowed only if it doesn't hurt security, scalability, etc).

-   Minimize new tech: by default true, only introduce new tech with user approval and if beneficial & safe (we saw that logic earlier, propose if meets conditions).

-   Maximize existing tech usage: true, use what's already in stack fully.

-   Backward compatibility: true, avoid breaking things for current users without approval if needed for a major change.

-   Forward compatibility: true, design so it won't hinder future upgrades.

-   Many ensures: security, performance, maintainability, accessibility, i18n, privacy, CI/CD, dev experience best practices all set to true.\
    Meaning agent must always incorporate those best practices when writing code (like if writing **HTML**, consider adding accessible labels, if writing code consider performance, etc).

-   Write tests: true, the agent should also generate tests for new code.

[**AFTER_ACTION_VALIDATION**]:

-   Run code quality tools: yes (like run linter after making changes).

-   Run Snyk: yes to catch any new issues.

-   Run tests: yes run the test suite after changes.

-   Require passing tests, linter, no new security issues: yes, so basically the agent should not consider the task done until all these pass. If something fails...

-   If fail: ask user what to do (they could choose to fix, roll back, proceed anyway, or abort).

-   Accepted user answers: they listed which ones are acceptable: rollback, resolve issues, proceed anyway, or abort (I guess user picks one).

-   Post to chat: 'deltas only' after validation, meaning agent should summarize the differences that were made (like a diff of code or summary of changes). So user sees exactly what changed rather than repeating the whole plan.

[**AFTER_ACTION_ALIGNMENT**]:

-   Update docs: true (update relevant docs like code comments, design docs, etc).

-   Update auxiliary docs: true (like maybe if there's external docs or readme etc).

-   Update todo: true and marked critical (so if a task was done, mark it done, if new tasks uncovered, add them).

-   Scan docs for consistency and up-to-dateness: true (so ensure the docs reflect new code, no contradictions).

-   Purge obsolete or deprecated docs content: true (remove outdated info).

-   If docs outdated or inconsistent beyond trivial: ask user how to reconcile (maybe the user might have context how to update them properly).

-   If todo outdated: resolve immediately (the agent might just fix the tasks list then and there).

[**AFTER_ACTION_CLEANUP**]:

-   Purge temp files: true (delete any temp artifacts created during the action).

-   Purge sensitive data: true (make sure any secrets loaded or logged during process are erased, though ideally none should appear).

-   Purge cached data: true (clear caches like any temp caches used so that next run is clean).

-   Purge **API** keys: true (if any keys were loaded in variables for calls, clear them; also ensure none got saved in code).

-   Purge obsolete code: true (if some code is now unreachable or replaced, remove it as part of cleanup, not leave commented out).

-   Purge deprecated code: true (if code was marked deprecated and no longer needed, remove it).

-   Purge unused code: true "unless scoped placeholder for later use" (so if there's clearly leftover code that's not used and not intended for any immediate use, remove it).

-   Post to chat: include action summary (overall what was done), file changes (list of changed files perhaps), risks mitigated (like "improved security by x, resolved bug y"), validation results (report tests pass, etc.), docs updated (what docs were changed), expected behavior (what the system should do now or how to test the changes). This is essentially a comprehensive report of the outcome.

[**AUDIT**]:

-   Bound command: /audit triggers it.

-   Scope: full (the audit covers entire codebase).

-   Frequency: upon command (so only when user asks, not automatic periodic unless user schedules but by config, only on demand).

-   Audit for: a list of categories (security, performance, maintainability, accessibility, i18n, privacy, CI/CD, dev experience, plus scanning for deprecated code, outdated docs, conflicts, redundancies, best practices compliance, confusing code).\
    Essentially a full quality audit.

-   Report format: markdown.

-   Report content: list issues found, recommendations for each, and resources (like links to documentation or **OWASP** etc. that help address them).

-   Post to chat: true, meaning the markdown report will be output in chat in response to /audit.

So the AI should produce a nicely formatted audit report with those sections.

[**REFACTOR**]:

-   Bound command: /refactor triggers it.

-   Scope: full codebase by default (unless user limited).

-   Frequency: on command (not auto).

-   Plan before refactor: true (like for actions, it should propose a plan for refactoring).

-   Await approval: true (so it doesn't refactor large parts blindly).

-   Override approval if user specifically said just do it: true.

-   Minimize changes: true (only change what's needed to address issues, don't refactor for the sake of it).

-   Maximum phases: 3 (if a refactor is too big, break into up to 3 steps perhaps separate /refactor calls).

-   Preempt for certain urgent issues (security, failing tests, etc.) same as actions: if those exist, fix them first even in a refactor context.

-   Preemption reason needed: yes, explain if you deviate to fix something else first.

-   Refactor for: these aspects (maintainability, performance, etc., basically same list as audit minus we already know).\
    Focus is on code improvements without changing functionality.

-   Ensure no functional changes: true (that's the refactoring definition).

-   Run tests before and after to confirm behavior unchanged: true (smart to baseline and post-check).

-   Require passing tests after: true, if fails then ask user (similar to after action).

-   Post to chat: provide change summary, file changes, risks mitigated (like "this refactor reduces tech debt in X, performance improved maybe etc."), validation results, docs updated, expected behavior (which should be same as before ideally, but maybe note if any difference).\
    Essentially similar to action final report but focusing on what improved and confirming no behavioral change.

[**DOCUMENT**]:

-   Bound command: /document triggers documentation tasks.

-   Scope: full by default (all codebase or relevant parts).

-   Frequency: on command.

-   Document for: the list of aspects (security, performance, etc., plus human readability, onboarding). So the documentation should cover those angles in appropriate docs or comments.\
    So maybe update inline comments about performance considerations, add docs explaining security measures, etc.

-   Documentation type: they want many forms - inline comments, function docs (docstrings), module docs (maybe module-level docstrings or separate md files), architecture docs, **API** docs, user guides, setup guides, maintenance guides, changelog, todo list.\
    So /document could mean "generate missing docs and improve existing ones across all these categories". It's a broad and potentially huge task.

-   Prefer existing docs: true (so update rather than create duplicates).

-   Default docs directory: /docs for any new markdown docs unless specified otherwise.

-   Non-comment docs should be in markdown.

-   Plan before document: true (especially since documenting whole code could be a big output, planning what to document where is needed to not waste time).

-   Await approval: true (so user can say "no don't generate that part" maybe).

-   Override with user request: if user says just do it no approval, then skip.

-   Target reader expertise: non-technical unless otherwise instructed. This means the documentation (especially user guides, etc.) should be understandable by laypeople. So avoid heavy jargon or explain it if used.\
    But if writing developer docs (like code comments?), maybe they still want them clear enough that a non-engineer could roughly get it, which is interesting. Likely it means user guides and even maybe architecture docs should be written clearly enough for at least a technical manager level.\
    If the user explicitly says write dev doc for dev audience, then that overrides default.

-   Ensure current: true (only document current state, not old plans).

-   Ensure consistent: true (docs should not contradict each other or code).

-   Ensure no conflicting docs: true (if two docs talk about same thing differently, fix that).\
    So the agent should reconcile differences.\
    This aligns with alignment steps, but /document command likely goes deeper in adding missing documentation rather than just aligning existing ones.

All these are instructions for how the AI agent works when given tasks. They ensure quality and alignment with the stated preferences throughout the development lifecycle.

Now the question said: Provide an extensive summary with interpretation and why useful for each setting, plus examples of alternatives. We've done that for each config piece. We have been extremely thorough. The answer is indeed going to be huge. But they specifically asked for it, and possibly said can break into multiple messages if needed.

We might be pushing the limit with all sections. Already up to Document. We should do the same for these last ones (action, etc.), albeit they are many lines. It might be wise to summarize them systematically with the same pattern if we have space. However, there are a lot of lines in those sections which could make the answer enormous.

We have to be careful to not run out of token limit. The conversation example they gave only showcased one entry. But they said "if you must break into multiple messages, ensure continuity so can merge."

So they are expecting perhaps a multi-message answer because indeed summarizing each line of [**BEFORE_ACTION_CHECKS**] etc. in same detail will easily double the length again.

We should gauge current length. Up to now (through privacy), we've produced very long content.

Given the user specifically said "If you must break it up into multiple messages, ensure continuity so they can be merged seamlessly," it's acceptable to do so.

We likely should break to be safe. Possibly after privacy or commit or build we might have already a large chunk.

We just finished privacy and commit, maybe we should split now or after build, because there's still a lot in action and sub.

If the answer in one message gets to like 20,000 tokens, that's too high for ChatGPT. We might need to split.

Maybe we can deliver part 1 now (covering up to maybe build or up to commit) and then continue with part 2 with the rest, ensuring same style.

We have done up to commit fully, started build. Possibly break after build or after build_config before the Action sections.

Alternatively, do all in one. But might be safer to split.

Let's see: We have extremely thorough with early sections. If to reduce at all, maybe we can condense the action sections a bit because they are somewhat repetitive in nature of "the agent will do X," and less critical for a user config standpoint (since they instruct the AI's operational logic, but user likely included them to show how AI reasons, which is meta ironically because the question is asking how the AI interprets them).

However, to follow instructions strictly, we should indeed cover them as well.

The user specifically said "extensively, exactly how you interpret instructions, both overall and section by section, and line by line." They mention top-level overview and line-by-line for each section.

We gave top-level overview at the beginning and for some big sections an intro. Possibly we should also have a main heading for each bracket group, which we did, like "## [**AGENT_CONFIG**]" etc.

We should do similarly for [**ACTION**] and sub ones, but sub sections might be okay under one heading e.g. "### **BEFORE_ACTION_CHECKS**" as subheadings under [**ACTION**] if needed to break up.

We might incorporate them under [**ACTION**] because they logically belong to it.

But to keep formatting consistent, maybe:

We had "## [**BUILD**]" etc. For [**ACTION**], maybe "## [**ACTION**] -- Agent Action Workflow" and then bullet the checks or we continue with the bold style for each line.

Better to continue the format: bold key, interpretation, etc.

So under [**BEFORE_ACTION_CHECKS**], we have multiple lines each starting with **IF_** or something. We should format each as **IF_BETTER_SOLUTION** | **PROPOSE_ALTERNATIVE**, then interpretation and why etc.

Yes, to be consistent. It's heavy but doable.

Let's attempt to finish in one message but if truncated, we'll have to split.

Given the extremely large scope, splitting might be prudent. Possibly after Build or after Action?

We are now at [**BUILD_CONFIG**]. After that, [**ACTION**] is huge. I think splitting before [**ACTION**] would be wise, because [**ACTION**] and all subitems are a big chunk themselves.

So maybe deliver everything up to [**BUILD_CONFIG**]/[**STACK**]/some to close part1, and then part2 covers [**ACTION**] onward.

But [**STACK**], [**BUILD**], [**BUILD_CONFIG**] we did. Perhaps end of [**BUILD_CONFIG**] is a natural break. Then next message covers [**ACTION**] and beyond.

Yes. [**ACTION**] sections are more the internal instructions for the agent on how to code, which ironically we have followed in making our plan. But we'll treat them similarly.

We'll do that.

So I'll finalize the final answer message up to [**BUILD_CONFIG**] including it, then ensure continuity and start a new answer message with [**ACTION**] etc.

That way it's not one extremely huge message and likely within token.

We need to ensure style remains same.

Let's see how lengthy we got. Up to commit/privacy, it's enormous (maybe 10000+ words already? I hope it's fine but might approach limit; splitting is likely necessary to avoid truncation).

Yes, I'll split now into multiple final messages due to size.

However, in the "final" mode, I'm only allowed to produce one final answer normally. But user explicitly allowed multiple messages, presumably to be merged.

This is unusual in typical ChatGPT usage (which expects one final answer). The user specifically said the output will be merged by them. So it should be okay.

I'll proceed accordingly, providing the first part, then quickly continue with second part.

[**BUILD**] -- Deployment Configuration
----------------------------------

**DEPLOYMENT_TYPE** | **SPA_WITH_BUNDLED_LANDING**\
Interpretation: The application will be deployed as a Single-Page Application (**SPA**), and the marketing or landing page is bundled with the app. This means the front-end is one cohesive React app that includes both the user app and the public-facing pages, rather than having a separate static site. The entire site (landing plus app) will be served together.\
Why Useful: This simplifies deployment -- one build outputs everything needed. Users get a smooth experience (the public landing can transition into the logged-in app without full page reloads if designed that way). It also avoids maintaining two codebases for marketing vs. app. For the team, it means a unified front-end project.\
Extra setting examples: In some cases, teams separate the landing page (e.g., use a static generator or **CMS**). Here, **SPA_WITH_BUNDLED_LANDING** indicates they decided to keep it together. An alternative deployment type could be `**SPA_WITH_SEPARATE_LANDING**` (if the landing page was served differently) or a traditional multi-page app. But bundling the landing into the **SPA** fits their simplicity goal.

**DEPLOYMENT** | **COOLIFY**\
Interpretation: They are using Coolify as the deployment platform. Coolify is a self-hosted PaaS that can deploy applications using Docker. This setting confirms that the app will be containerized and managed by Coolify for hosting.\
Why Useful: With Coolify, they get an easy deployment pipeline: push to a Git repository, and Coolify will build ane Docker containers. It abstracts away a lot of DevOps, handling **SSL**, domain setup, and container orchestration automatically. The team doesn't have to manually manage servers or Docker commands for each update -- Coolify will do it upon new commits.\
Extra setting examples: Alternatives could be **HEROKU**, **DOCKER_COMPOSE**, or **KUBERNETES**, but they chose Coolify for an easier self-hosted continuous deployment. For instance, if not using Coolify, they might list `**DEPLOYMENT**: **DOCKER_MANUAL**` or `**GITHUB_ACTIONS_TO_SERVER**`. Coolify streamlines those steps.

**DEPLOY_VIA** | **GIT_PUSH**\
Interpretation: Deployments are triggered by a Git push to the repository (likely to a specific branch such as `main` or `master`). This means the act of pushing code initiates the build and deploy process (as configured in Coolify or CI/CD pipelines).\
Why Useful: It enables continuous deployment. Developers just push their code, and the CI/CD (via GitHub Actions and Coolify integration) takes care of testing and releasing it. This reduces manual steps and ensures that code in the main branch is always deployed (assuming it passes all checks). It aligns with agile practices of rapid iteration and delivery.\
Extra setting examples: Some projects use manual deploys (only when a release is cut) ordeploys. GIT_PUSHindicates they want a tight loop between coding and having it live. They could refine it by branch -- e.g., deploy on push to `main` but not on push to feature branches. Here it sounds like whenever code is merged to the deployment branch, it goes out.

**WEBSERVER** | **VITE**\
Interpretation: This indicates that during development (and possibly in certain deployments), the Vite development server is used to serve the front-end. Vite provides a dev server for the React app (and can also bundle and preview the production build). In production, they might not literally run the Vite dev server, but this suggests that Vite is central to serving or building the web content. It could also mean they use Vite's preview server to serve the built app (though often one would use a simpler static server or the backend to serve files).\
Why Useful: Mentioning Vite here highlights that the tooling for the front-end also plays a role in serving content. In development, Vite's hot-reloading server drastically improves the developer experience with instant updates and module replacement. For production, Vite will build optimized files -- those could be served by Django or a **CDN**. If they choose, Vite's own preview (which is essentially an Node server) could serve the static files; however, typically the reverse proxy (Traefik) or Django static file serving would handle it. In either case, **WEBSERVER**: **VITE** underscores that Vite is the core of front-end delivery in development and bundling.\
Extra setting examples: In a different setup they might list **WEBSERVER**: **NGINX** (to serve static files in production) or **WEBSERVER**: **DJANGO** (if Django served the front-end via templates or static middleware). The choice of Vite implies they rely on its dev server and build outputs rather than a separate webserver component.

**REVERSE_PROXY** | **TRAEFIK**\
Interpretation: They are using Traefik as the reverse proxy in front of the application containers. Traefik will route incoming requests on specified domains to the correct service (frontend, backend, etc.), and handle things like **SSL** termination.\
Why Useful: Traefik integrates well with Docker and Coolify, auto-configuring routes based on container labels. It will enforce many of the security headers at the edge (since they enabled integration at proxy level). For example, Traefik can be set up to redirect **HTTP** to **HTTPS**, add **HSTS** headers, and serve the front-end on the root domain and maybe the **API** on a subpath or subdomain. Using a reverse proxy also means the backend service can run without being exposed directly to the internet, which aligns with the **EXPOSE_PORTS**: **FALSE** setting.\
Extra setting examples: They could have used Nginx or Caddy as alternatives, but Traefik is likely chosen for its dynamic Docker integration. In a scenario without containers, they might not list a proxy at all, but here it's explicit.

**BUILD_TOOL** | **VITE**\
Interpretation: The front-end build tool is Vite. This confirms that Vite is used not only as a dev server but also to bundle the app for production. Vite will take the React/TypeScript source and produce optimized static assets (JS, **CSS**, etc.).\
Why Useful: Vite is modern and extremely fast for development, and it uses Rollup under the hood for production builds, yielding highly optimized outputs. The AI/developers will write build scripts (e.g., `npm run build`) that invoke Vite to generate the `dist/` directory with all assets. This setting ensures everyone knows that building the front-end means running the Vite pipeline, and no other build system (like Webpack or Parcel) is involved.\
Extra setting examples: If the stack had different front-end tech, they might put **BUILD_TOOL**: **WEBPACK** or **GRADLE** (for other languages), etc. Here it's conmework choice (React+Tailwind works seamlessly with Vite).

**BUILD_PACK** | **COOLIFY_READY_DOCKERFILE**\
Interpretation: They will provide a Dockerfile configured for Coolify to build the application. This suggests they are not relying on generic buildpacks; instead, the repository will include a custom Dockerfile that Coolify will use. "Coolify-ready" implies it includes all steps to build both front-end and back-end and respects environment variables that Coolify passes in.\
Why Useful: By writing their own Dockerfile, they have full control over the environment (Python version, Node version for front-end build, OS packages, etc.). It ensures consistency between development and production. They'll likely do a multi-stage build: one stage to compile the front-end (using Node/Vite), another to set up the Python backend with dependencies, then copy the built front-end into the Django static files or a lightweight server. Marking it as Coolify-ready means they'll include any hooks or labels that Coolify expects, so deployment is plug-and-play.\
Extra setting examples: Some might use Heroku buildpacks or Cloud Native Buildpacks (**CNB**) if not writing a custom Dockerfile. But here, specifying a Dockerfile is more flexible. An alternate setting could be **BUILD_PACK**: **HEROKU_PYTHON_NODEJS** if they used buildpack detection. They opted for explicit Docker, which fits their environment.

**HOSTING** | **CLOUD_VPS**\
Interpretation: The application will be hosted on a cloud-based Virtual Private Server. This means they are not using fully managed platforms like **AWS** Elastic Beanstalk or Heroku's cloud, nor on-premises hardware; instead, they likely rent a server (or cluster of servers) from a cloud provider (e.g., DigitalOcean, **AWS** EC2, Linode) and run Coolify and containers there.\
Why Useful: A Cloud **VPS** gives them full control and isolation. They can configure networking, storage, and the environment to their needs, and it's typically cost-effective for a start-up phase. It also aligns with using Coolify, as Coolify itself would be installed on that **VPS**. This informs the AI and team that they might have to manage some server-level tasks (like ensuring the **VPS** has Docker, enough disk for Mongo/Postgres, etc.).\
Extra setting examples: If they were using a fully managed container service, it might say **HOSTING**: **AWS_FARGATE** or **HOSTING**: **GCP_CLOUD_RUN**. Cloud **VPS** suggests a more **DIY** approach (but with Coolify making it easier). It could be one or multiple VPSs depending on scaling, but presumably one to start.

**EXPOSE_PORTS** | **FALSE**\
Interpretation: No container ports will be directly exposed to the host/network. All network traffic goes through Traefik (the reverse proxy). `**FALSE**` means in Docker Compose or container config, they will not use `-p` or `ports` to map container ports to the host. Instead, Traefik will route traffic internally using the Docker network.\
Why Useful: This is a security measure -- only the proxy is accessible from the internet, reducing the attack surface. For example, the Django container's port 8000 isn't reachable except by Traefik, so an attacker can't bypass the web proxy's protections by hitting an IP and port directly. It also prevents port collisions and keeps configuration simple (Traefik finds services via labels). For the AI's Docker configuration, it means using labels like `traefik.http.services...` instead of exposing ports.\
Extra setting examples: In a local dev or simple deployment, one might set **EXPOSE_PORTS**: **TRUE** to test by going to http://localhost:8000, but in production, it stays false. Some PaaS might require exposing, but Coolify/Traefik doesn't.

**HEALTH_CHECKS** | **TRUE**\
Interpretation: They will have health check endpoints and monitoring enabled. `**TRUE**` means each service should expose a route (like `/health` or a Docker **HEALTHCHECK**) that indicates if it's running properly, and the deployment system will actively check it. Coolify and Traefik can use this to know if a container is healthy.\
Why Useful: Automated health checks ensure that if something goes wrong (a service crashes or is stuck), the system can detect it and possibly restart the container or mark the deployment as failed. It also is used in CI/CD (as they mention requiring 200 OK from health check). The AI will implement, for example, a Django endpoint that checks the database and returns "OK" (**HTTP** 200) when all is well. It might also set a Dockerfile `**HEALTHCHECK**` instruction to periodically ping that endpoint. This results in higher reliability because issues are caught early and can trigger rollback or alerts.\
Extra setting examples: If false, they'd rely on manual checks or lack the automated feedback of container health. Many projects set up health endpoints, so true is expected. They might integrate it such that Coolify waits for the health check to pass before routing traffic to a new container (rolling update scenario).

* * * * *

[**BUILD_CONFIG**] -- CI/CD Pipeline and Build Requirements
------------------------------------------------------

**KEEP_USER_INSTALL_CHECKLIST_UP_TO_DATE** | **CRITICAL**\
Interpretation: It is critically important to keep the installation and setup guide (the user/developer install checklist) always updated with the latest changes. Every time something changes that affects how to install or run the project, that document must be edited accordingly. They label this as "**CRITICAL**," meaning it's a top priority in the development process.\
Why Useful: An up-to-date install_guide.md (user checklist) ensures that new developers or team members can set up the project without hurdles, and that deploying the system following the guide will work. If this goes out of sync, people could waste hours on setup issues. By marking it critical, they're effectively saying any pull request that changes configuration or dependencies should include corresponding doc updates. The AI will treat this as a must-do in after-action alignment (it will update the guide whenever needed).\
Extra setting examples: Many projects treat documentation as secondary, but here they explicitly avoid that. If this were not as important, they might rank it as "**HIGH**" or leave it unstated, but "**CRITICAL**" implies possibly even failing the CI pipeline if the guide isn't updated. (They could enforce via review or even a CI check that certain files changed when relevant.)

**CI_TOOL** | **GITHUB_ACTIONS**\
Interpretation: The Continuous Integration tool in use is GitHub Actions. This means they've set up workflows in the `.github/workflows` directory to run tests, linters, and perhaps build processes on GitHub's CI runners whenever code is pushed or a pull request is made.\
Why Useful: GitHub Actions integrates directly with their GitHub repository, making it convenient to run CI on each commit. It's configured to perform all the steps they list under CI (linting, testing, security scans). The team can see build status (passing/failing) on GitHub itself. For the AI, this means any changes to CI configuration will involve editing **YAML** files for GitHub Actions. It also implies that secrets (like **API** keys for Snyk or Docker registry) will be stored in GitHub for use in these workflows.\
Extra setting examples: Alternative CI tools could have been GitLab CI, Travis CI, or CircleCI, etc. They specifically chose GitHub Actions, likely because their code is on GitHub and Actions is free for moderate use and powerful enough. This choice dictates the format of CI config and how certain things (like environment variables, secrets) are handled.

**CI_RUNS** | **LINT**; **TESTS**; **SECURITY_AUDIT**\
Interpretation: The CI pipeline will run the following tasks on each relevant push: Linters (both Ruff for Python and ESLint for JS/TS), Tests (both backend and frontend tests), and a Security audit (using Snyk to scan for vulnerabilities). These are the quality gates before code is merged or deployed.\
Why Useful: This ensures every code change is checked for style issues, correctness (through tests), and known security problems automatically. By catching issues early in CI, they maintain a high standard of code quality and security. For example, if someone introduces a dependency with a known flaw, Snyk will flag it in CI and the build will fail rather than the issue slipping into production. The AI will need to produce CI configuration that sets up a Python environment to run Ruff and tests, a Node environment to run ESLint and Vitest, and invokes Snyk **CLI**.\
Extra setting examples: Some projects break CI into multiple workflows (e.g., separate lint and test jobs). Here they list them together, implying all must pass. They might run in parallel jobs in Actions. Also, "Tests" includes presumably both backend (maybe via `pytest` or Django's test runner) and frontend (Vitest). They didn't explicitly mention a Python test tool, but it's implied. If any of these fail, CI is considered failing.

**CD_RUNS** | **LINT**; **TESTS**; **SECURITY_AUDIT**; **BUILD**; **DEPLOY**\
Interpretation: In the Continuous Deployment phase (when they actually release to an environment), they will again run Lint, Tests, and Security Audit, and then proceed to Build and Deploy the application. Essentially, the deployment pipeline incorporates all CI checks (to double-confirm nothing was missed) and then builds the Docker container and deploys it via Coolify.\
Why Useful: This double-layer ensures that even if somehow code changed after CI (or if they only deploy certain branches), no bad code goes out. It's belt-and-suspenders: by repeating lint/tests before deploy, they ensure the exact code being deployed is verified. The Build step will likely involve building the Docker image (or images) using the provided Dockerfile. The Deploy step would push that image to the server or trigger Coolify to pull and run it. Including these in an automated pipeline means minimal manual work to release new code.\
Extra setting examples: Sometimes teams assume if CI passed, no need to repeat tests in CD. They here choose to rerun them, perhaps on the container or just as a gating condition. An alternative could be to trust CI and only do Build & Deploy in CD. But given "**CD_RUNS**: **LINT**, **TESTS**, **SECURITY_AUDIT**, ..." they prefer absolute certainty. In GitHub Actions, this could be implemented as sequential jobs or using required checks on protected branches.

**CD_REQUIRE_PASSING_CI** | **TRUE**\
Interpretation: Deployment to production (or any auto-deploy environment) is only allowed if CI checks passed. `**TRUE**` means the pipeline will not proceed with building or deploying if the earlier lint/test/security steps failed, or if the commit didn't go through the CI successfully. Essentially, it's an enforced rule that bad code never gets deployed.\
Why Useful: It prevents situations where someone might try to force deploy a commit that didn't pass tests. It likely ties into branch protection as well (they might require CI to pass before merging to main, and then main triggers CD). For the AI, it means in the GitHub Actions workflow or in Coolify's settings, ensure that deployment job depends on the success of the CI jobs. This guarantees no skipped tests.\
Extra setting examples: If set to **FALSE**, someone could theoretically deploy a failing build (maybe for emergency hotfix reasons or debugging in staging). But they want to avoid that scenario. This strictness aligns with their commitment to quality and stability.

**OVERRIDE_SNYK_FALSE_POSITIVES** | **TRUE**\
Interpretation: In cases where Snyk reports a vulnerability that the team determines is a false positive, they will override it so that it does not fail the build. `**TRUE**` means they actively configure Snyk to ignore specific issues once reviewed. They might use a Snyk policy file or command-line ignore flags.\
Why Useful: Snyk occasionally flags issues that aren't actually problems in context (for example, a library might have a vulnerability in a code path the app doesn't use). Without override, CI would be red until the library is upgraded or Snyk updates its database, which could block development. By ignoring known false positives (and alerting as mentioned earlier), they keep the pipeline green and focus only on real issues. The key is doing it judiciously: they alert the user and add an ignore, as noted, meaning it's documented. The AI will likely update a `snyk.ignore` or configuration file to implement this and maybe mention in the report that it's ignoring certain CVEs.\
Extra setting examples: If this were **FALSE**, every Snyk issue would halt the pipeline until resolved, which can be impractical. They chose a pragmatic approach: trust the team's judgment to bypass specific findings. Another angle could be a severity threshold (e.g., ignore low severity issues), but they specifically mention false positives, implying real issues regardless of severity should still break the build.

**CD_DEPLOY_ON** | **MANUAL_APPROVAL**\
Interpretation: Even after all checks pass, deploying to the live environment requires a manual approval step. This likely means that in their CI/CD workflow, there is a hold or gated step where a human must click "Approve" before the actual deployment happens. `**MANUAL_APPROVAL**` ensures no automatic deployment without a person verifying everything.\
Why Useful: This adds a final safeguard. Perhaps they want an engineer or manager to quickly review the changes or timing of a deployment (for example, avoid deploying right in the middle of a critical user session period). It's especially common in pre-release or production environments to have manual promotion. The AI setting up GitHub Actions can use environments with "wait for approval" gates for production. This way, even if code is merged, they can coordinate the actual release.\
Extra setting examples: Some teams do `**DEPLOY_ON**: **MERGE_TO_MAIN**` (fully automated continuous deployment). They opted for manual, likely because the product is sensitive (<**REDACTED_FOR_IP**>---don't want downtime or mistakes) or because they might not be doing dozens of deploys a day at this stage. Over time, they might shift this to auto if confidence grows, but for now it's a cautious approach.

**BUILD_TARGET** | **DOCKER_CONTAINER**\
Interpretation: The end result of the build process is a Docker container image. Everything (the Django app, Node-built static files, etc.) will be assembled into an image. This is what will be run on the server. It confirms containerization as the packaging format.\
Why Useful: Containerization ensures that the app runs the same in production as it did in testing, encapsulating all dependencies. It's also exactly what Coolify expects to deploy. The AI's tasks include writing the Dockerfile (already noted) and making sure the image is built in CI and delivered to Coolify (possibly via an image registry or directly by Coolify pulling from Git). Declaring Docker as the target helps everyone understand the deployment artifact (not a zip file or a war file, etc., but an image).\
Extra setting examples: If they weren't containerizing, build target might be something like `**VIRTUAL_MACHINE_IMAGE**` or `**PYPI_PACKAGE**` for other contexts. But Docker is basically standard for web apps now, and they explicitly confirm that.

REQUIRE_HEALTH_CHECKS_200 | **TRUE**\
Interpretation: After deployment, the health check endpoints must return **HTTP** 200 (OK) for the deployment to be considered successful. `**TRUE**` means the CD process will actively verify that the app's health check responds with 200. If not, it will mark the deployment as failed (and likely not switch traffic to it or will rollback).\
Why Useful: This is critical for catching issues that automated tests might not detect -- for example, if the app can't start due to a missing environment variable or a database connection issue, the health check would fail. By requiring a 200 OK, they ensure the new version is actually running properly. Many deployment systems (including Coolify or custom scripts) can be configured to wait for a success response. The AI should ensure a `/health` endpoint exists and is used in the deployment config. This ties back to **HEALTH_CHECKS**: **TRUE** -- they have them, and here they mandate they pass post-deployment.\
Extra setting examples: Some might not gate deployment on health (they'd just deploy and then rely on monitoring to alert). This team chooses to make deployment transactional: either the new version comes up healthy or it's not accepted. This reduces downtime or bad deployments dramatically.

**ROLLBACK_ON_FAILURE** | **TRUE**\
Interpretation: If a deployment fails (for instance, health checks do not pass, or any step in CD after code is built fails), the system will automatically rollback to the previous stable version. `**TRUE**` means they have configured their deployment process to undo the release if something goes wrong.\
Why Useful: This ensures high reliability -- if the new version is broken, users won't be stuck with a non-working app; the platform will revert to the last working container. This can be achieved by containers (for example, keep the old container running until the new one is confirmed healthy, which is a blue-green deployment strategy). Coolify likely handles this by not switching the running container if the new one doesn't report healthy. The AI doesn't have to code the rollback itself, but must ensure that health failures properly propagate (perhaps by exiting with non-zero status in the deploy script so that Coolify knows to maintain the old instance).\
Extra setting examples: Some simpler setups require manual rollback (someone redeploys the old version). They automated it, which reduces downtime and stress. This setting, combined with health checks, implies a robust deployment pipeline where they can deploy often without fear -- if something's wrong, it'll self-revert.


> ****AUTHOR'S NOTE:****\
> Hi. This file has been written over probably a cumulative 14-18 hours spanning several weeks of iteration, trial-and-error, and testing the AI's interpretation of instructions (which has been a painstaking process). You are free to use it, learn from it, simply use it as research, whatever you'd like. I have tried to redact as little information as possible to retain some IP stealthiness until I am ready to release, at which point I will open-source the repository for self-hosting. ****If the file below helps you out, or you simply learn something from it or get inspiration for your own system instructions file, all I ask is that you share it with someone else who might, too, if for nothing else than me feeling the ten more hours I've spent today trying to wrestle ChatGPT into writing the longform analysis linked below was worth something.**).\

## If you want to understand what a specific setting does, or you want to see and confirm for yourself exactly how AI interprets each individual setting, I have killed two birds with one massive stone and asked GPT-5 to provide a clear analysis of/readme for/guide to the file. You can view that in [Analysis.md](analysis.md).

****[NOTE: The analysis file is VERY long because it serves BOTH as an instruction file and as research for how the model interprets instructions. If you are simply looking to learn about a specific rule, use the search functionality via CTRL/CMD+F, or you will be here until tomorrow. If you want to learn more about how AI interprets, reasons, and makes decisions, I strongly encourage you to read the entire analysis, even if you have no intention of using the attached file. I promise you'll learn at least** *******something.*********]****

I've had relatively good success reducing the degree to which I have to micro-manage copilot as if it's a not-particularly-intelligent teenager using the following system-instructions file. I probably have to do 30-40% less micro-managing now. Which is still bad, but it's a lot better.

The file is written in YAML/JSON-esque key:value syntax with a few straightforward conditional operators and logic operators to maximize AI understanding and consistent interpretation of instructions.

The full content is pasted in [copilot-instructions.md](copilot-instructions.md). ****Before you use it, I beg you to read the very short FAQ below, unless you have extensive experience with these files already.****

Notice that sections replaced with "[...]" in the file demonstrate places where I have removed something to protect IP or dev environments from my own projects specifically for this Reddit post. I will eventually open-source my entire project, but I'd like to at least get to release first without having to deal with snooping amateur hackers.

You should not carry the "[...]" over to your file.

### FAQ:
====

****How do I use this file?****

You can simply copy it, paste it into copilot-instructions.md, claude.md, or whatever system-prompt file your model/IDE/CLI uses, and modify it to fit your specific stack, project, and requirements. If you are unsure how to use system-prompts (for your specific model/software or just in general) you should probably Google that first.

****Why does it look like that?****

System instructions are written exclusively for AI, not for humans. AI does not need complete sentences and long vivid descriptions of things, it prefers short, concise instructions, preferably written in a consistent syntax. Bonus points if that syntax emulates development languages, since that is what a lot of the model's training data relies on, so it immediately understands the logic. That is why the file looks like a typical key:value file with a few distinctions.

****How do I know what a setting is called or what values I can set?****

That's the beauty of it. This is not actually a programming language. There are no standards and no prescriptive rules. Nothing will break if you change up the syntax. Nothing will break if you invent your own setting. There is no prescriptive ruleset. You can create any rule you want and assign any value you want to it. You can make it as long or short as you want. ****However, for maximum quality and consistency I strongly recommend trying to stay as close to widely adopted software development terminology, symbols and syntaxes as possible.****

You could absolutely create the rule `GO_AND_GET_INFO_FROM_WEBSITE_WWW_PATH_WHEN_USER_TELLS_YOU_IT: 'TRUE'` and the AI would probably for the most part get what you were trying to say, but you would get considerably more consistent results from `FETCH_URL_FROM_USER_INPUT: 'TRUE'`. But you do not strictly have to. It is as open-ended as you want it to be.

****Since there is a security section which seems very strongly written, does this mean the AI will write secure code?****

Short answer: No. Long answer: Fuck no. But if you're lucky it might just prevent AI from causing the absolute worst vulnerabilities, and it'll shave the time you have to spend on fixing bad security practices to maybe half. And that's something too. But do not think this is a shortcut or that this prompt will magically fix how laughably bad even the flagship models are at writing secure code. It is a band-aid on a bullet wound.

****Can I remove an entire section? Can I add a new section?****

Yes. You can do whatever you want. Even if the syntax of the file looks a little strange if you're unfamiliar with code, at the end of the day the AI is still using natural language processing to parse it, the syntax is only there to help it immediately make sense of the structure of that language (i.e. 'this part is the setting name', 'this part is the setting's value', 'this is a comment', 'this is an IF/OR statement', etc.) without employing the verbosity of conversational language. For example, this entire block of text you're reading right now could be condensed to `CAN_MODIFY_REMOVE_ADD_SECTIONS: 'TRUE' && 'MAINTAIN_CLEAR_NAMING_CONVENTIONS'`.

Reading an FAQ in that format would be confusing to you and I, but the AI perfectly well understands, and using fewer words reduces the risks of the AI getting confused, dropping context, emphasizing less important parts of instructions, you name it.

****Is this for free? Are you trying to sell me something? Do I need to credit you or something?****

Yes, it's for free, no, I don't need attribution for a text-file anyone could write. Use it, abuse it, don't use it, I don't care. But I hope it helps at least one person out there, if with nothing else than to learn from its structure.

****I added it and now the AI doesn't do anything anymore.****

Unless you changed REQUIRE_COMMANDS to 'FALSE', the agent requires a command to actually begin working. This is a failsafe to prevent accidental major changes, when you wanted to simply discuss the pros and cons of a new feature, for example. I have built in the following commands, but you can add any and all of your own too following the same syntax:

/agent, /audit, /refactor, /chat, /document

To get the agent to do work, either use the relevant command or (not recommended) change REQUIRE_COMMANDS to 'false'.

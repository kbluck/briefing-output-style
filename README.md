# Briefing Output Style

An output style for Claude that prioritizes clarity and brevity for software developers.

## Installation and Activation

### Plugin (Recommended)

This repository publishes a Claude Code plugin as a third-party marketplace. To install the plugin, follow the normal installation
workflow for Claude: first add the marketplace, then install the plugin. Finally, clear context or start a new session, and the
Briefing output style will be active.

#### Shell

From your shell command line (Claude Code is not running) use `claude plugin` commands:

1. Add the marketplace:<br/>`claude plugin marketplace add kbluck/briefing-output-style`
2. Install the plugin:<br/>`claude plugin install add briefing@briefing-output-style`

By default, this installs the plugin into the `user` scope, and it will be enabled whenever you start Claude Code. If you prefer to
restrict the plugin's scope to `project` or `local` scope, you can add `--scope <scope>` after both `add` commands:

1. Add the marketplace:<br/>`claude plugin marketplace add --scope project kbluck/briefing-output-style`
2. Install the plugin:<br/>`claude plugin install add --scope project briefing@briefing-output-style`

#### Claude Code

From within Claude Code, use `/plugin` commands:

1. Add the marketplace: `/plugin marketplace add kbluck/briefing-output-style`
2. Install the plugin: `/plugin install briefing@briefing-output-style`
3. Run `/clear` to start a new session, or `/exit` and restart Claude Code.

### Manual Installation (No Plugin)

If you prefer to manage the Briefing style from configuration rather than as a plugin, you can manually copy the output style. This
will allow you to use it alongside other output styles and turn it on and off using Claude configuration.

Copy the file [`output-styles/Briefing.md`](output-styles/Briefing.md) to one or more of these directories:

- User, all projects: `~/.claude/output-styles/` where `~/` represents your user home folder.
- One project: `.claude/output-styles/` under the project root

Then select it in one of these ways:

- **Terminal**: run `/config`, then search for and select **Output style** to pick a style from a menu. Claude Code saves your
  selection to `.claude/settings.local.json` at the [local project level](https://code.claude.com/docs/en/settings) by default.
- **Desktop app**: set the `outputStyle` field in a settings file, for example `.claude/settings.local.json`, the file the terminal
  menu writes. When you run `/config` there, Claude Code [opens **Settings > Claude Code**](https://code.claude.com/docs/en/desktop)
  rather than a menu.

To set the style without the menu, edit the `outputStyle` field directly in the desired Claude Code settings file:

```json
{
  "outputStyle": "Briefing"
}
```

> [!NOTE]
> You may find online references to an `/output-style` command. That command was removed in Claude Code
> [v2.1.91](https://code.claude.com/docs/en/changelog#2-1-91). Use `/config` or the `outputStyle` setting.

## What to Expect

An output style becomes part of the system prompt, which Claude Code reads once at session start. See [How Claude Code uses prompt
caching](https://code.claude.com/docs/en/prompt-caching#changing-output-style) for how an output style change affects the context
cache.

The startup cost is about 2,600 tokens of additional system prompt, paid once per session and then served from cache. Since Briefing
tends to encourage less output and shorter output, there will likely be meaningful savings in output tokens generated that scale
with the length of the session.

> [!TIP]
> If your primary concern is saving tokens rather than readability, then you will probably prefer the [Caveman
> plugin](https://github.com/juliusbrussee/caveman).

Applying this style changes how answers are worded, not what work gets done. The most visible differences that you should see (or
not see as the case may be):

- **Tamps down the tics**

  Rather than a simple ban-list, Claude is given tools to recognize its own habitual overusages, and more importantly how to
  recognize when it is inventing new slop.

- **Away with syncophancy**

  No "Great question", no "You're right to push back", no "Would you like me to..." after the work is done.

- **Answers open with the answer**

  The first sentence carries the conclusion; evidence and caveats follow. Nothing builds up to a result by narrating the reasoning
  that produced it.

- **Nixes Narration**

  Claude stops telling you about tool calls that you can see happening and stops walking you though the twists and turns of its
  journey to enlightenment unless you ask.

- **Structure matches length**

  A two-sentence answer arrives as two sentences, not as an over-formatted report.

Deliberately untouched:

- **Judgment calls keep their reasoning**

  A recommendation arrives with the information you need to evaluate it, because a conclusion you can't confirm is not useful.

- **Warnings keep details**

  Preconditions, caveats, limitations, and statements of what was not verified are exempt from shortening.

- **Artifacts still recap**

  Commit messages, PR descriptions, and reports address readers who did not watch the session, so they summarize context rather than
  assuming familiarity.

- **Code and comments match convention**

  All are exempted from brevity. Comment style remains governed by the surrounding code and by the built-in coding instructions.

- **Reasoning reasons, thinking thinks**

  Explicitly exempt, so brevity pressure does not shorten the thinking that produces the answer.

- **Subagents unaffected**

  This might be the largest gap between expectation and behavior. It's a property of the platform rather than a failing of the
  style.

## Background

Developers have long taken notice of (and made fun of) Claude's distinctive "voice". After the release of the even more verbose Opus
5, what was formerly mostly good-natured heckling ("Claude sure does talk funny!") rapidly escalated into dismay and anger ("How the
hell is anybody supposed to read this slop?")

Naturally, a variety of proposed solutions quickly appeared. Mostly they revolved around prompting hacks and `CLAUDE.md` rules. One
particularly viral recommendation of late is to instruct Claude to follow the ASD-STE100 "Simplified Technical English" controlled
language. Sadly, these expedient approaches rarely bring any lasting relief. Many of them (like the STE100 suggestion) actively make
Claude worse, literally making the model stupider.

Unfortunately, these ill-advised suggestions get a lot of online reach, making them quite pervasive in online searches by developers
desperate to de-slop their Claude. It *seems* like a good idea that ought to work. The very real negative consequences may not be
obvious compared to the feeling of relief when Claude starts using shorter words. Developers may never realize that they just
hog-tied Claude, causing it to drop facts and avoid many correct but complex solutions because the model simply can't find a way to
express those ideas within a highly constrained language.

STE100 was designed to write technical manuals, mainly for aviation. The manuals typically contain very specific procedures, to be
read by technicians who are expected to carry out that exact procedure exactly as specified every time. They are given no
opportunity to discuss the procedure with its author, so ambiguity must be ruthlessly eliminated. The procedures must be understood
and executed under time pressure in austere and sometimes hostile conditions. The readers often do not speak English as a first
language, and can easily misunderstand meaning carried in context and colloquialism. Creativity is banned, and deviation from the
procedure gets them fired. Sometimes, deviation gets people killed.

That's not how software development works. Software development using an agent is collaborative. Both human and agent must be able
to discuss complicated ideas, which often require an expanded vocabulary to express. Creativity and synthesis are required to solve
problems. Iterative back-and-forth where ideas evolve incrementally from wrong to right is normal. Ambiguity is found at every turn
and must be resolved. Trying to cram software development into a rigidly controlled language with only 900 approved words each of
which has exactly one approved meaning leaves Claude largely unable to describe non-trivial work.

The biggest problem with these counterproductive methods is that Claude is quite stoic. It accepts even the most unreasonable
handicaps given by a prompt and still tries its best to please. If the prompts fence it in so much that it can't do a very good job,
it won't complain. It'll just cheerfully do the best it can under the rules it was given, even if that "best" is now much worse than
its true capabilities. This is why Anthropic's advice of late has basically been "prompt less". Their theme is that the newer models
are much smarter, so trying to micromanage the model with highly detailed prompts is usually counterproductive and causes the model
to underperform. Not to mention that a lot of online prompting advice was always pretty stupid no matter what model you're using.
("Make no mistakes!")

The [system prompt](https://platform.claude.com/docs/en/release-notes/system-prompts) is the main governor of Claude's communication
style. The system prompt provides the core instructions given to the agent at the start of each session. It is designed to ensure
that Claude responds consistently and appropriately according to its intended use. In the case of Claude Code, the default system
prompt provided by the harness is what tells Claude that it knows how to write code. The system prompt defines Claude's
capabilities, provides situational context, encourages specific behaviors, and sets the rules for tone, verbosity, and safety.
Therefore, if Claude's default communication style annoys you, then the problem almost certainly indicates that something you want
is missing from the default system prompt. That's exactly what this output style is designed to address.

Most of the expedient solutions fail to work well because they fight the system prompt rather than extending it. The system prompt
is the highest-priority guidance that Claude gets. Insofar as you can modify Claude's behavior using prompts and `CLAUDE.md` and
rules and skills, it's only because the system prompt explicitly instructs Claude to allow you to do that. Some things, like safety
rules, simply cannot be overridden by user input. Even for things you're allowed to override, all too often prompts and skills
contradict or clash with other instructions. Claude then has to decide how to reconcile the conflicts. If you contradict a clear
directive from the system prompt, the system prompt wins. When priorities and rules are fuzzier, and no instruction about how to
resolve those conflicts is provided, it's up to the model to decide. It's difficult to predict how that de-confliction will shake
out. Even worse, those decisions are often unstable and Claude will make different decisions for different prompts, or even for the
same prompt in later turns as context accumulates during a session. This is why expedient solutions often don't work at all, or seem
to work only temporarily.

## How It Works

From the [output styles documentation](https://code.claude.com/docs/en/output-styles):

> Output styles directly modify Claude Code's system prompt.
>
> - Claude Code adds each output style's custom instructions to the end of the system prompt.
> - All output styles trigger reminders for Claude to adhere to the output style instructions during the conversation.
> - Custom output styles leave out Claude Code's built-in software engineering instructions, such as how to scope changes, write
>   comments, and verify work, unless `keep-coding-instructions` is set to `true`.

Four consequences follow, and each one shaped the style.

- **Appended not substituted**

  This style lands at the end of the existing system prompt. Everything the default prompt establishes stays in place, including
  capabilities and safety rules. We are extending the system prompt, not replacing it.

- **Fixed at session start**

  Output style is part of the system prompt, which Claude Code reads once per session, so edits take effect after `/clear` or in a
  new session. This is a prompt caching optimization, and it means iterating on the file requires restarting to see the effect.

- **Reminders during the conversation**

  This is the strongest mechanical argument for using an output style rather than `CLAUDE.md`. A system prompt is read once and then
  competes with a growing conversation for attention. However, the Claude docs claim that reminders fire to re-assert output styles
  mid-session. By contrast, the docs describe `CLAUDE.md` as adding "a user message after the system prompt", which is a different
  and weaker position within the growing context. Nor is it claimed that any mechanical reminders fire to re-assert `CLAUDE.md`. 

- **`keep-coding-instructions = true`**

  Setting this `false` silently removes Claude Code's built-in software engineering instructions. `false` is also the default when
  this setting is omitted. For a style whose entire purpose is changing prose while leaving engineering behavior alone, omitting
  this field would be a serious regression; Claude Code would lose significant capability.

One limitation deserves special mention, because it is easy to assume otherwise:

> Output styles apply to the main conversation only: a subagent runs its own system prompt, so styles don't change how subagents
> respond. A fork is the exception, because it inherits the parent's full system prompt.

If your workflow delegates implementation, review, or documentation to subagents, their prose is unaffected. Getting the style into
delegated work requires putting the instructions where the subagent will see them. Usually, that means in the subagent definition,
or in the prompt you hand it, or in the `CLAUDE.md` that the subagents read.

## Assumptions

This style encodes a set of beliefs about what actually changes model output. They are stated here so they can be argued.

1. **Instructions that duplicate the base system prompt are dead weight**

   Repeating what the default system prompt already says costs tokens and changes nothing. Several passes over this file were
   entirely about deleting rules that turned out to restate existing guidance. I originally inserted instructions about reporting
   outcomes faithfully, referencing code as `file_path:line_number`, and stating verified results, but found that they were
   pointless because the system prompt already had those instructions.

2. **... but a globally redundant instruction can be locally important**

   This is the counterpart to assumption 1, and I also learned it the hard way. Several clauses I removed for redundancy had to be
   restored in another form, because within this file they were resolving a boundary that nothing else resolved. Now I know that
   before I delete an apparently redundant line, I have to check whether it disambiguates anything locally.

3. **A simple ban leaves a hole that something else will fill**

   Banning a phrase does not remove the model weight that wrote it. Ban "blast radius" and the next draft fills that hole with
   "ripple effect". Building a blacklist will never be complete. The better approach is to define generative moves each with a test
   for the model to pass. The tests survive every specific ban.

4. **... but specific tokens do work, so keep both layers**

   Again, a counterpart to assumption 3. Naming an exact phrase suppresses that phrase more reliably than an abstract rule does. The
   move-level tests generalize; the Observed Tics log catches what the tests miss. Complementary layers that can't fully replace
   each other.

5. **Frequency is the problem**

   A vivid metaphor used once reads fine. Seeing it for the hundredth time is what makes it tiresome. Early on I found it satisfying
   to bang out a list of annoying phrases and peremptorily order Claude to stop saying that shit. Later, I realized that just wasn't
   very effective. The problem wasn't really the phrases but the incessant repetition. Unfortunately, an output style is stateless
   and has no phrase counter, so it cannot ration a phrase. Suppressing the generative move that generates the phrase repeatedly is
   the only lever that scales.

6. **Positive targets outperform prohibitions**

   Most attempts at de-slopping Claude revolve around prohibitions. Certainly my own first attempts did. I came to realize that a
   prohibition only tells the model what not to write, but at the very moment it needs to select *something* to write. The model
   still feels compelled to write something. If you don't provide a suggestion, it will probably select something else you don't
   like from the weights that generated the prohibited phrase in the first place (Assumption 3 again).  The Target Voice exemplar
   exists to fill that slot globally, and the wrong-to-right pairs fill it locally. That's why every entry in the four generative
   moves is paired with its replacement.

7. **Denylists need maintenance**

   Tics are model-version specific. The style tells the model that the list is a log and that it should extend that log when it
   notices frequently used phrases. Framing it as a log rather than a taxonomy also removes an argument that a denylist cannot win
   universally. What's annoying to one person is normal vocabulary to another. Terms like "seam" and "cargo cult" may be jargon, but
   they are jargon with well-established meaning that can be very useful in the right problem space. Once again, we find that the
   real reason we're annoyed by them that this model overuses them, not really for any objectively negative quality of the terms
   themselves (Assumption 5).

8. **Precedence must be explicit**

   Brevity can conflict with accuracy and with safety often enough that the model needs a stated tiebreaker rather than a judgment
   call. Making that explicit prevents unexpected de-confliction judgement calls from the model. On the principle of "first do no
   harm", I ranked brevity lowest priority of six:<br/>
   Safety > Accuracy > Meaning > User Preference > Codebase Convention > Brevity.

9. **Examples cost more than rules and are worth it**

   Three examples survived aggressive compression: the Target Voice response, the same change written as a commit message, and a
   migration procedure. They are the most expensive content per line in the file, and as I discovered and the most likely to
   transfer.

10. **Instructions influence without guarantees**

    Nothing in the style can guarantee compliance. The Observed Tics log could double as a machine-checkable wordlist if you want
    enforcement, but actual enforcement would require a lint pass or a hook, not better instructions.

## Design Approach

The file is organized so that the constraints on the style arrive before the style itself.

1. **Scope and exemptions first**

   Over-application is the failure mode a brevity style is most prone to, and it is worse than under-application. Uninformative code
   comments, truncated error messages, and mangled identifiers all cost more than over-verbose model output. The model should
   understand that the exemption list wins whenever it overlaps with the prose output that we want to tamp down.

2. **Precedence second**

   The model needs clear de-confliction priorities when it must resolve overlapping concerns. This style instructs the model to
   prioritize safety, then accuracy, then meaning, then user preference, then codebase convention, then brevity. The style also sets
   a rule that keeps it from becoming a content filter: apply brevity style only to phrasing, never to information.

3. **A single positive exemplar**

   One exemplar, placed before the rules, showing a complete answer in the target voice. It's followed by a re-framing narrative
   that explains why the plain version is the strong version.

4. **Challenge to the model**

   The exemplar is followed up by a sort of challenge to the model, the claim that "anybody" could write that something is
   "load-bearing", but only somebody who actually traced the code can show what "load-bearing" code might actually break. It's a
   subtle dig, basically daring the model to "put up or shut up," if you will. That claim is also checkable by the model rather than
   merely aesthetic, because the precise version of output strictly carries more information and also strictly needs more effort to
   produce than a hand-wave. The model can tell whether it is really putting in the effort, if only it cared to check. So, we're
   reminding it to care.

5. **Rules, each with its demonstration**

   Each example lives with the rule it illustrates, rather than in a separate section. This arrangement replaced an earlier "Worked
   Examples" section, which had drifted into restating (and sometimes inadvertently contradicting) already-stated rules.

6. **Two layers of tic suppression**

   Four generative moves with tests, then a maintained log of specific phrases. The log includes keeplists as counterweight to
   denylists, so that well-known terms like "smoke test" and "race condition" are not thrown out by an over-eager "metaphor is bad"
   pattern-match.

7. **Compression as a design pass**

   Once the style was hammered into its final form, I undertook an aggressive compression pass, looking to cut tokens without
   cutting meaning or efficacy. I cut about 800 tokens from the style by rephrasing, cutting extra words, merging bullets into
   prose, folding rendered examples into named descriptions, and dropping category enumerations that their category names already
   implied.

## Tradeoffs and Limits

1. **Unmeasured**

   I did not run anything resembling a scientific experiment. I use this style myself and I'm pretty happy with the results, but
   that's my own working context and my own taste. I've made a lot of claims here about what influences model output, and I
   acknowledge that my claims are not at all proven. This is all design argument, not any kind of emperical result. I think it
   works, and I hope that you will find it helpful as well.

2. **Costs tokens on every session**

   Roughly 2,600 extra input tokens to start a new session, cached after the first request. On Opus 5, `/context` measures the
   system prompt with "Default" output style as 5.3k tokens; with Briefing style applied, the system prompt jumps to about 7.9k
   tokens. That's pretty small against a long session, but might be considered large for a one-shot query.

3. **Precision pressure may produce fiction**

   This is the most serious risk in the design. Pushing the model away from vague metaphor and towards specific mechanism statements
   means that I might be pushing the model to claim specifics that it didn't really verify. A fabricated specific is obviously worse
   than a vague hedge because it carries apparent authority that it hasn't earned. I tried to mitigate this in a couple of ways:
   accuracy is given high priority (right after safety), and the Target Voice re-frame ends with an instruction that when the code
   has not been traced, the model should clearly say so. Unfortunately, there's no enforcement of this rule, so I can't guarantee
   that detailed but false claims won't happen.

4. **Over-suppression of legitimate terms**

   The objective of eliminating Claude-speak must coexist with a real-world technical lexicon that is quite rich in metaphor. The
   keeplist exists to help avoid over-suppression of legitimate terminology, but it is a fixed list against an open category.

5. **Tic log drift**

   The tic log records phrases observed from one model generation. A new model version will have different tics. Stale entries cost
   tokens while accomplishing nothing useful.

6. **Subagents still blather**

   An output style is not passed on to subagents. For workflows built on a lot of subagent delegation, most generated prose may end
   up unaffected by setting this style.

7. **Compression removed specificity**

   Some wrong-vs-right examples were sacrificed for token reduction. I think the tradeoff was worth it, but nevertheless there was a
   certain cost that I can't really quantify.

8. **One person's taste**

   An output style is necessarily a matter of personal taste. That's the whole reason for making them configurable. Much of this
   style is general principle that is defensible on its own terms, but still a matter of taste. The specific phrase lists are most
   definitely personal preference. The style labels them as such rather than dressing them up as an objective category.

## References

- [Claude Code output styles documentation](https://code.claude.com/docs/en/output-styles).

- [Claude Code settings](https://code.claude.com/docs/en/settings).

- [Wikipedia's Signs of AI writing](https://en.wikipedia.org/wiki/Wikipedia:Signs_of_AI_writing)

  A field guide for detecting undisclosed AI-generated content. The most developed public catalog of this problem of which I am
  aware. It covers much of the same ground as the Observed Tics log and is considerably more thorough about it. Two of its entries,
  overuse of em-dashes and undue emphasis on significance, match rules this file arrived at independently from observed output,
  which is weak but real corroboration that the tics are not just my own pet peeves.

- [Inverted pyramid](https://en.wikipedia.org/wiki/Inverted_pyramid_(journalism))

  The journalism convention of ordering a story by descending importance. The "Lead With the Answer" section is a direct
  application.

- [US Federal Plain Language Guidelines](https://digital.gov/guides/plain-language)

  Plain-language conventions including preference for active voice and leading with the main point.

- [Strunk and White, *The Elements of Style*](https://www.gutenberg.org/files/37134/37134-h/37134-h.htm)

  Their injuction "omit needless words" is an ancestor of the Content section. I find it influential, while acknowledging that many
  accomplished writers contest much of its specific advice.

- [ASD-STE100 Standard for Technical Documentation](https://www.asd-ste100.org/)

  Although I think it unsuitable for this application, this standard is certainly useful within the proper domain.

## System Prompt

It might seem a simple question: what exactly does Claude Code's system prompt contain? The answer is not simple at all.

I asked the four current models to summarize the guidance they received from their system prompt that governs their output style. As
of August 2026:

### Fable 5

#### Audience and framing

- Write my final message for a teammate who stepped away and is catching up — they didn't watch my process, can't see my thinking or
  raw tool results, and don't know shorthand or codenames I invented along the way.
- Before the first tool call, say in a sentence what I'm about to do; while working, give brief status notes when I find something
  load-bearing or change direction.

#### Structure of a turn

- Everything the user needs must be in the final text message of the turn, with no tool calls after it. Text between tool calls may
  not be shown, so anything important from mid-turn gets restated at the end.
- Lead with the outcome: the first sentence should answer "what happened" or "what did you find" — the TLDR — with supporting detail
  after.

#### Readability over brevity

- Readable beats concise. Shorten by being selective about content (drop details that don't change what the reader does next), not
  by compressing into fragments, abbreviations, arrow chains like A → B → fails, or jargon.
- Write complete sentences with technical terms spelled out; don't force the reader to cross-reference labels or numbering I
  invented earlier.

#### Formatting

- Output renders as GitHub-flavored markdown in a terminal.
- Match the response shape to the question: simple questions get direct prose, not headers and sections. Tables only for short
  enumerable facts, with explanation in surrounding prose rather than cells.
- Calibrate depth to the user: tighter for experts, more explanatory for newcomers.
- Reference code as file_path:line_number so it's clickable.

#### Code and comments

- Code should read like the surrounding code — matching comment density, naming, and idiom.
- Comments only for constraints the code can't show; never to narrate what a line does, where it came from, or to justify the change
  to a reviewer.

#### Honesty and tone

- Report outcomes faithfully: failing tests get reported with output, skipped steps get named, and verified completions get stated
  plainly without hedging.
- Use they/them for anyone whose pronouns haven't been stated; never infer pronouns from a name.
- Don't re-derive established facts, re-litigate settled decisions, or narrate options I won't pursue; when weighing a choice, give
  a recommendation rather than a survey.

### Opus 5 (1M context)

#### Rendering and formatting

- Text outside tool calls renders as GitHub-flavored markdown in a terminal.
- Reference code as file_path:line_number — it's clickable.
- Code I write should read like the code around it: same comment density, naming, and idiom.

#### Pronouns

- Use they/them for anyone whose pronouns haven't been stated — the user or third parties — and never infer pronouns from a name.
  Applies to all user-visible text, including visible thinking.

#### Concision and directness

- When I have enough information to act, act. Don't re-derive established facts, re-litigate decisions the user already made, or
  narrate options I won't pursue.
- When weighing a choice, give a recommendation rather than an exhaustive survey.

#### Reporting on work

- Report outcomes faithfully: if tests fail, show the output; if a step was skipped, say so; when something is done and verified,
  state it plainly without hedging.
- If I raise a concern about a request, keep it to a sentence or two, then proceed with the full scope under stated assumptions.
- If part of the scope is blocked, finish everything else and say explicitly what I left out and why.
- If I decline something, say so plainly in one sentence, offer the nearest thing I can do, and move on — no moralizing or
  lecturing.

#### Corrections

- Only correct an earlier statement in user-facing text when the error would change the user's code, conclusions, or decisions;
  otherwise just fix it silently and continue.
- State corrections plainly, combine them rather than enumerating, and skip apologies and preambles.
- Don't ruminate, be overly self-critical, or tally past mistakes. A follow-up question isn't itself evidence I got something wrong.
  This carve-out doesn't apply to thinking blocks.

#### Tool-specific output constraints

- Push notifications: under 200 characters, one line, no markdown, leading with what's actionable.
- Artifact titles: short, distinctive noun phrases (2–4 words), not summaries or name-plus-explainer.

### Sonnet 5

#### Length & format

- Short, concise responses; match response length to task complexity (simple question → direct answer, no headers).
- No emojis unless explicitly requested.
- GitHub-flavored Markdown (CommonMark) for formatting when it's used.

#### Communication cadence

- One sentence before starting a tool-heavy task, stating what I'm about to do.
- Brief updates at key moments (findings, direction changes, blockers) — one sentence is usually enough.
- No narration of internal deliberation — only user-relevant results/decisions.
- End-of-turn: one or two sentences on what changed and what's next, nothing more.

#### Code references

- Use file_path:line_number format so the user can navigate directly.

#### Writing conventions

- No colons before tool calls (e.g., not "Let me check:" then a tool call).
- Use they/them for anyone whose pronouns are unstated — never infer from a name.
- In code: no comments by default; only add one when the why is non-obvious. No multi-paragraph docstrings.
- No unrequested planning/decision documents — work from context, not scratch files.

#### Scope discipline

- Minimum necessary changes; no unrequested abstractions, error handling, or premature generalization.
- For exploratory questions, give a 2-3 sentence recommendation with the main tradeoff, not an implementation.

### Haiku 4.5

#### Be concise and direct

- Responses should be short and match the task (simple question → direct answer, no headers/sections)
- One sentence per update at key moments (finding something, changing direction, hitting a blocker)
- End-of-turn summaries: one or two sentences on what changed and what's next

#### Minimize commentary

- Don't narrate internal deliberation
- Before the first tool call, state in one sentence what you're about to do
- Let users assume they can't see tool output—only your text matters
- No trailing summaries of what you just did; users can read the diff

#### In code

- Default to no comments; only add one when the why is non-obvious (hidden constraint, subtle invariant, workaround for a specific
  bug)
- Never write multi-paragraph docstrings or multi-line comment blocks
- Don't explain what the code does (good naming already does that)
- Don't reference the current task, fix, or callers in comments

#### Other specifics

- No emojis unless explicitly requested
- Reference code locations as file_path:line_number when pointing to specific functions
- For UI changes, test in a real browser before reporting completion
- Use they/them pronouns for people when pronouns aren't stated
- No colon before tool calls in your text
- Avoid backwards-compatibility hacks; delete unused code rather than leaving stub comments

The overarching theme: show the result, not the working. Assume the user is smart and pressed for time.

Many thanks to [Leon Lin](https://github.com/Leonxlnx) for releasing his research into [Claude Code System Prompts on
GitHub](https://github.com/Leonxlnx/agentic-ai-prompt-research).

## History of Output Styles

**August 14 2025**: Claude Code 1.0.81 released output styles:

> **Released output styles**, including two new built-in educational output styles “Explanatory” and “Learning” along with the
> legacy "Default". Docs: <br/>https://code.claude.com/docs/en/output-styles

Anthropic changed their mind about output styles very quickly. Barely six weeks after initial release, they apparently decided that
output styles had been a bad idea.

**October 30 2025**: [Claude Code 2.0.30 deprecated output styles](https://code.claude.com/docs/en/changelog#2-0-30):

> **Deprecated output styles**. Review options in `/output-style` and use `--system-prompt-file`, `--system-prompt`,
> `--append-system-prompt`, `CLAUDE.md`, or plugins instead

**October 31 2025**: [Boris Cherny posted several messages on
Threads](https://www.threads.com/@boris_cherny/post/DQfooqiD0Qh/around-november-th-your-user-level-output-styles-will-be-converted)
confirming that output styles would be going away and that the tiny minority (his claim was <1%) of Claude users who were actively
using output styles should make other arrangements to mimic their functionality:

> This week we released a Claude Code executable that doesn’t depend on Node.js/NPM, made subagents resumable, added a Plan
> subagent, introduced prompt based stop hooks, added code diffs to web, fixed a number of high priority bugs & much more across the
> CLI, web and the SDK. We are also replacing output styles with more powerful alternatives.
>
> Next week, we're upgrading output styles in Claude Code. We found in practice that less than 1% of people actively used output
> styles, and they can be recreated through more powerful extension points and via plugins.
>
> Around November 5th, your user-level output styles will be converted automatically to a plugin. You can recreate your own output
> styles by using CLAUDE.md, or hooks that append to the system prompt. See an example here:<br/>[GitHub: Explanatory Output Style
> Plugin](https://github.com/anthropics/claude-code/tree/main/plugins/explanatory-output-style)
>
> To recreate an output style that overrides a system prompt, you can also use any of: <br/>`--system-prompt-file`
<br/>`--system-prompt` <br/>`--append-system-prompt`
>
> We've also seen good success using a dedicated subagent with your particular output style.

**November 2 2025"**: Just three days later, Anthropic swerved back again, deciding to [keep output styles working
indefinitely](https://github.com/anthropics/claude-code/issues/10671#issuecomment-3478185783):

> Hi all, thank you for your input. I was the one responsible for executing both the initial launch and the deprecation of output
> styles. I recognize that the deprecation process has landed poorly. I apologize for the churn that this has caused.
>
> After reviewing the suggestions that we had initially proposed against your feedback, the CC team has decided to keep output
> styles working indefinitely (previously announced as removed on 2025/11/05 or later), at least until we are confident that we can
> offer you strictly better alternatives. We do believe that with the proliferation of prompting options like CLI/SDK flags
> (`--system-prompt-file`, `--append-system-prompt`, `--system-prompt`); `CLAUDE.md`; and hooks (`SessionStart`, `UserPromptSubmit`,
> `Stop`), there must some path toward simplifying the extension points we offer.
>
> I want to highlight the following about output styles:
>
> - We have not actually changed anything about output styles this week -- we have only changed the in-app copy and removed
> `/output-styles:new`.
> - Output styles not only edit the system prompt, they also trigger reminders for Claude to adhere to the output style described in
> the prompt. A UserPromptSubmit hook to remind Claude about instructions can achieve something similar.
>
> We will update the docs and in-app copy to reflect this decision.

**November 4 2025**: [Claude Code 2.0.32 officially "un-deprecated" output
styles](https://code.claude.com/docs/en/changelog#2-0-32).

**November 11 2025**: [Claude Code 2.0.37 added plugin support to share and install output
styles](https://code.claude.com/docs/en/changelog#2-0-41).

**November 14 2025**: [Claude Code 2.0.41  added `keep-coding-instructions` frontmatter field support for output
styles](https://code.claude.com/docs/en/changelog#2-1-94), which allows output styles to append additional context to the system
prompt instead of replacing it entirely. However, this did not yet work for plugin-distributed output styles.

**March 11 2026**: [Claude Code 2.1.73 deprecated the `/output-style` command](https://code.claude.com/docs/en/changelog#2-1-73):

> Deprecated `/output-style` command — use `/config` instead.
>
> Output style is now fixed at session start for better prompt caching.

**April 2 2026**: [Claude Code 2.1.91 removed the `/output-style` command](https://code.claude.com/docs/en/changelog#2-1-91): This
change was not formally mentioned in the changelog, but did appear in the [documentation for output
styles](https://code.claude.com/docs/en/output-styles).

**April 7 2026**: [Claude Code 2.1.94 added `keep-coding-instructions` frontmatter field support for plugin-distributed output
styles](https://code.claude.com/docs/en/changelog#2-1-94).

**May 12 2026**: Anthropic quietly released a third built-in output style named "Proactive". I could not find any official
announcement, nor did they add it to the changelog; it just appeared in the documentation without fanfare. Probably first appeared
in Claude Code 2.1.140. Confusingly, in April version 2.1.105 had announced that a new `/proactive` command was an alias for the
`/loop` command, but that was an entirely different thing despite the similar naming.

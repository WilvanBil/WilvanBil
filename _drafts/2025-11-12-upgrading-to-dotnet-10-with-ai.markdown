---
layout: post
title: "Upgrading to .NET 10 with AI: A Journey from Skeptic to Believer"
date: 2025-11-12 10:00:00 +0100
categories: AI NuGet dotnet
description: "Follow my journey upgrading WilvanBil.NationalRegisterNumber to .NET 10 using AI assistance. From initial repo review to code optimization and CI/CD improvements, discover what AI does exceptionally well—and where it still needs your expertise."
---

When .NET 10 was announced, I couldn't wait. Not because of the new features—but because it gave me a reason to dust off my NuGet package and work on it again. This time, though, I had a new teammate: GitHub Copilot. Three years ago, I built this package manually, back when coding agents didn't exist and ChatGPT was just starting to boom. Now? I wanted to see what AI actually thought of my code.

---

## Step One: Visual Studio 2026 and a Surprising Choice

The first thing I did was install [Visual Studio 2026](https://visualstudio.microsoft.com/), which had just officially released. .NET 10 was here, and I needed the latest tooling to go with it.

But I'd been waiting for this. As soon as I heard the [Insiders edition](https://visualstudio.microsoft.com/insiders/) was available, I downloaded it. That's when I first noticed something in the fine print—_"Best on Windows 11 with 64 GB RAM and 16 cores – faster and more responsive than Visual Studio 2022 on the same hardware."_

Wait, 64 GB of RAM? I was curious if this was still the case now that VS 2026 had officially released. Sure enough, the [official system requirements](https://learn.microsoft.com/en-us/visualstudio/releases/2026/vs-system-requirements) confirmed it: Visual Studio 2026 works best with 64 GB of RAM. That's not a typo. I'm not entirely sure why—maybe it's the AI features, the background analysis, or something else entirely—but that's the recommendation.

### VS Code Over Visual Studio? Yes, Actually

So I had VS 2026 installed, ready to go. But when it came time to open my repository, I didn't use Visual Studio. I used **VS Code** instead.

Why?

Because VS Code has a feature that Visual Studio 2026 doesn't (yet): **Generate Agent Instructions**. When you open a repo in VS Code with GitHub Copilot, you can ask it to generate custom agent instructions tailored to your codebase. It analyzes your project structure, conventions, and patterns, then creates instructions that help AI understand your specific project better.

![Build with Agent - Generate Agent Instructions]({{ site.baseurl }}/assets/upgrading-dotnet-10-ai/build-with-agent.png)

This was exactly what I needed. Before making any changes, I wanted AI to understand my package—not just generically, but _specifically_. What patterns do I use? How do I structure tests? What conventions matter to me?

So I opened the repo in VS Code, generated the agent instructions, and then switched back to Visual Studio 2026 for all the actual coding and changes. Maybe there are some things VS Code does better, but for now, I'm sticking with Visual Studio 2026 for the heavy lifting.

### The Generated Instructions Were Impressive

What GitHub Copilot generated was surprisingly comprehensive. It analyzed my code, tests, and documentation to create a complete `copilot-instructions.md` file covering:

- The core architecture and business logic of Belgian national register numbers
- My testing patterns (xUnit, FluentAssertions, FsCheck for property-based testing)
- Build and versioning workflows (including my use of Versionize)
- Code conventions and design decisions
- Common pitfalls and quick reference guides

But as I reviewed the generated instructions, a couple of things caught my attention:

#### 1. The Dead Link

The instructions included a link to the [official Belgian government specification](https://www.ibz.rrn.fgov.be/fileadmin/user_upload/nl/rr/instructies/IT-lijst/IT000_Rijksregisternummer.pdf) for national register numbers—a PDF I'd referenced when building the original package. But when I clicked it? **404. Page not found.**

Thanks, Belgian government.

The documentation that defined the entire validation logic for my package was gone. No archive, no redirect, just... gone. Fortunately, my code and tests already captured the business rules, so the link was more of a nice-to-have than a critical dependency. Still, it's a reminder that even official documentation can disappear.

#### 2. FluentAssertions and Licensing

I noticed the instructions mentioned FluentAssertions, which I use extensively in my tests. Here's the thing: starting with version 8.0, FluentAssertions requires a [paid license](https://fluentassertions.com/licensing/). My package is currently pinned to version 7.x to avoid this, but I made a mental note—I should probably refactor those tests to use standard xUnit asserts instead. Less dependency, no licensing concerns, just plain assertions.

#### 3. No Dependabot

Another gap: I don't have Dependabot set up to keep dependencies updated automatically. That went straight onto my todo list for after the upgrade. GitHub Copilot might help me code, but it can't magically configure repository automation... yet.

---

## The First Test: Does Copilot Actually Understand?

With the agent instructions generated and saved, I opened the solution in Visual Studio 2026. Time to see if this AI stuff actually works.

I set the Copilot mode to **Ask** (not Edit, not anything fancy—just Ask) and picked **Claude Sonnet 4.5** as my model. No way I'm paying 10x the tokens for Claude Opus 4.1. Not yet, anyway. Sonnet would have to prove itself first.

Then I asked one simple question: **"Review the entire codebase."**

Not "suggest improvements." Not "give me a plan to upgrade to .NET 10." Just review it. I needed to see if Copilot actually understood the context before I let it touch anything. This was the test.

### What Copilot Found

The response came back organized and thorough. Copilot delivered a "Codebase Review" with four main categories of strengths:

#### 1. Architecture & Design

- Recognized the clean separation: core logic in `NationalRegisterNumberGenerator`, extensions in `NationalRegisterNumberStringExtensions`, simple `BiologicalSex` enum
- Noted the XML documentation on all public methods
- Acknowledged it follows .NET conventions: static generator class, extension methods, proper nullable reference types

#### 2. Implementation Quality

- Called out the correct validation logic, including the Y2K checksum ambiguity for births after 1999
- Spotted the use of `Span<T>` in extraction methods for performance
- Recognized `Random.Shared` as a modern .NET best practice for thread-safe randomization
- Highlighted proper error handling with descriptive `ArgumentException` messages

#### 3. Testing

- Comprehensive test coverage: validation, generation, formatting, and extraction all tested
- Property-based testing with FsCheck for exhaustive `DateTime` validation
- Edge cases covered: date boundaries (1900, 1999/2000), follow number limits, invalid inputs
- FluentAssertions for readable `.Should()` syntax

#### 4. Package Configuration

- Multi-targeting for .NET 8 and .NET 9
- Proper NuGet setup: symbols (`.snupkg`), README, icon, and XML documentation
- MIT license properly declared

I sat back and read through it. This wasn't generic AI feedback. Copilot had actually _read_ my code. It understood the Y2K checksum edge case—a tricky bit of logic that even I had to think hard about when I first wrote it. It recognized the property-based testing pattern with FsCheck, which isn't exactly mainstream. It even called out `Random.Shared`, a detail most developers wouldn't notice.

The agent instructions had worked. Copilot wasn't just pattern-matching; it was demonstrating genuine understanding of my codebase.

**Takeaway**: Before asking AI to change anything, make sure it understands what you've built. This review gave me confidence that Copilot wasn't going to bulldoze through my code with generic suggestions—it knew what it was working with.

### But Then Came the Issues

Copilot didn't stop at compliments. It also delivered a list of issues and recommendations, organized by priority. And one of them stopped me cold.

#### Critical Issue: Biological Sex Extraction Logic Error

The problem was in `TryExtractBiologicalSex`. Here's what I had:

```csharp
sex = followNumber % 2 == 0 ? BiologicalSex.Female : BiologicalSex.Male;
```

Copilot pointed out that this checks the entire follow number (e.g., `421`), but according to the specification—and my own generation logic—only the **last digit** matters.

Here's the evidence from my own code:

```csharp
if (sex == BiologicalSex.Female)
{
    if (followNumber % 2 != 0)
        followNumber++;  // Makes last digit even
}
else
{
    if (followNumber % 2 == 0)
        followNumber--;  // Makes last digit odd
}
```

The generation logic adjusts the follow number to ensure the last digit is even for females, odd for males. But my extraction logic was checking the entire number. It worked... by accident. Because `followNumber % 2` happens to equal `lastDigit % 2` in most cases, but it's semantically wrong.

Copilot even provided the fix:

```csharp
// Extract the last digit of the follow number (position 8 in the full number)
char lastFollowDigit = nationalRegisterNumber[8];
int digit = lastFollowDigit - '0';
sex = digit % 2 == 0 ? BiologicalSex.Female : BiologicalSex.Male;
```

I stared at the screen. This was a bug I'd missed. Not a catastrophic one—it worked in practice—but it was _wrong_. And Copilot caught it.

#### Other Issues

Beyond that critical bug, Copilot also flagged:

- **Test coverage gaps**: My extraction tests only used valid numbers, but the method deliberately skips checksum validation. I should test with invalid checksums too.
- **Inconsistent string manipulation**: Mixing range operators (`[..6]`) with `Substring()` in the same method.
- **Property-based test limitations**: FsCheck generates random dates, many of which are before 1900 and get rejected, causing test noise.
- **Missing edge cases**: Follow number boundaries (1, 998, 999), formatted input validation, leap year dates.
- **Code style nitpicks**: Magic numbers like `[8]` without explanation, redundant assertion patterns.

Copilot even gave me an overall grade: **A- (Excellent with minor issues)**.

The biological sex bug had to be fixed before the next release. Everything else? Polish.

### The Fork in the Road

Now I had a choice.

I could jump straight into what I came here to do:

- Add Dependabot for automatic dependency updates
- Upgrade to .NET 10
- Have Copilot review the CI/CD workflows (which I'd forgotten about until now)

_Orrrrrrrrrr..._

I could first fix the issues and inconsistencies Copilot just called out, _then_ focus on the stuff I initially wanted to do.

I chose the latter. If AI is going to point out bugs in my code, I should probably let it fix them before I pile on more changes. Plus, I was curious: How well would Copilot handle fixing its own comments on my code?

Time to switch from **Ask** mode to **Agent** mode.

---

## Phase 1: Fixing the Issues (Before the Upgrade)

Before diving into fixes, I wanted a plan. So I asked Copilot: **"Give me a plan of approach to tackle these issues."**

I wasn't ready to let AI start editing files yet. I wanted to see _how_ it would approach the work—what it would prioritize, what order it would tackle things in, whether it understood the dependencies between changes.

This felt important. If I'm going to trust AI to fix code, I need to trust its judgment about _how_ to fix it.

### The Plan

Copilot delivered detailed steps, but what caught my attention was how it organized them: an **execution checklist** broken into "sprints." Cute usage of the word "sprint," honestly.

#### Sprint 1: Critical Fixes (1-2 hours)

- Fix biological sex extraction logic
- Run all existing tests
- Add invalid checksum tests for sex extraction
- Add formatted string validation tests
- Verify all tests pass

#### Sprint 2: Enhanced Coverage (2-3 hours)

- Add follow number boundary tests
- Add leap year date tests
- Add null/empty string handling tests
- Constrain property-based test domain
- Run full test suite

#### Sprint 3: Code Quality (1 hour)

- Standardize string slicing
- Simplify test assertions
- Add test constants
- Code review and documentation updates

#### Final Steps

- Run `dotnet test` - ensure 100% pass rate
- Run `dotnet build --configuration Release`
- Update CHANGELOG (I use Versionize, so this was relevant)
- Consider version bump (patch for bug fix: 2.3.0 → 2.3.1)
- Review PR before merge

It also included **success criteria** (all tests pass, sex extraction checks position 8, edge cases covered, no breaking changes) and a **risk assessment table** that flagged the biological sex fix as "High Impact" but noted the mitigation: the existing logic works by accident, so the fix is actually more correct.

The plan was thorough, methodical, and—most importantly—it made sense. Copilot wasn't just generating a to-do list; it was thinking about dependencies, risk, and validation steps.

I was impressed. And ready to say yes.

### Letting Copilot Loose

Time to see if Copilot could execute as well as it could plan. I typed:

"Start with sprint 1, let me verify the changes before moving on to the next sprint."

Then I hit enter and let Copilot do its thing.

I didn't want it racing through all three sprints without me checking its work. One sprint at a time. Verify. Then move forward. This felt like the right balance between trusting AI and staying in control.

After Copilot finished Sprint 1, I carefully reviewed all the changes. Here's what it did.

### Sprint 1: The Execution

Copilot started working through Sprint 1: fixing the biological sex extraction bug, running tests, and adding new test cases for edge cases.

#### The Good Parts

**It fixed the bug correctly**. Changed from checking the entire follow number to checking the digit at position 8:

```csharp
// Before (incorrect)
sex = followNumber % 2 == 0 ? BiologicalSex.Female : BiologicalSex.Male;

// After (correct)
char lastFollowDigit = nationalRegisterNumber[8];
int digit = lastFollowDigit - '0';
sex = digit % 2 == 0 ? BiologicalSex.Female : BiologicalSex.Male;
```

**It ran tests immediately** after the fix to verify nothing broke. All 102 existing tests passed.

**It added the new test cases** from the plan: tests for invalid checksums, formatted string validation.

#### The Messy Parts

This is where it got interesting. Copilot didn't nail it on the first try.

It generated test data with invalid checksums—which was _supposed_ to be the point of one test, but it got confused about which numbers should actually validate. It tried to use `00010100121` and `99010199991`, but those numbers don't actually pass `IsValid()`.

I watched as Copilot caught its own mistake, re-read the existing test data, manually verified checksums, and corrected itself. It went through multiple iterations:

- Generate test data → Tests fail
- "Wait, let me check the actual valid numbers..." → Reads `ExtractionTests.cs`
- Adjusts test data → Tests still fail
- "Let me manually calculate the checksums..." → Traces through digit positions
- Finally gets it right → All tests pass

#### The Result

After working through its own errors, Copilot delivered:

- ✅ Bug fixed (biological sex extraction now checks position 8)
- ✅ All 102 existing tests still passing
- ✅ 14 new tests added (invalid checksum tests, formatted input tests)
- ✅ Total: 116 tests passing
- ✅ No compilation errors

It even generated a summary table showing test coverage before and after.

### Sprint 2: When AI Gets Thorough

With Sprint 1 done and verified, I told Copilot: **"Looks good, start with sprint 2."**

Sprint 2 was about enhanced test coverage—follow number boundaries, leap year dates, null/empty string handling, and constraining the property-based tests.

Copilot went to work. And it went _thorough_.

**What It Added:**

1. **Follow number boundary tests** - Testing 1, 2, 997, 998, and middle range (500)
2. **Leap year date tests** - Valid leap years (2000, 2024, 2020, 1996) and the century rule edge case (1900 is NOT a leap year)
3. **Null/empty string handling** - Tests for null, empty string, whitespace, invalid lengths across all extension methods
4. **Property-based test improvements** - Constrained FsCheck to valid date ranges (1900+), added explicit tests for pre-1900 dates

**The Result:**

- Started with: 116 tests
- Ended with: **196 tests**
- Added: **80 new tests** in one sprint

I stared at the test count. Part of my brain questioned: Is this a good thing or a bad thing?

On one hand, thorough test coverage is great. Edge cases matter. Leap years, null handling, boundary conditions—these are the things that break in production.

On the other hand, 80 tests in one go felt like... a lot. Was every single one necessary? Or was AI just being overly cautious, generating tests because it _could_, not because it _should_?

I reviewed the tests. They were well-structured, followed the AAA pattern (Arrange-Act-Assert), used FluentAssertions consistently, and had descriptive names. They covered real edge cases:

- Leap year logic (including the 1900 century rule exception)
- Follow numbers at the absolute boundaries (1, 2, 997, 998)
- Null handling in every extension method
- Invalid string lengths

Nothing felt redundant. Nothing felt unnecessary. It was just... comprehensive.

**Verdict:** This was a good thing. Maybe not necessary for an MVP, but for a library that other developers depend on? Yeah, I'll take 80 solid edge case tests over a "it probably works" assumption any day.

### Sprint 3: The Polish

Sprint 2 was done. Now for the final sprint—the code quality improvements. I typed: **"Commence the next sprint."**

Sprint 3 was about polish: standardizing string slicing, simplifying test assertions, and adding constants for magic numbers. These weren't critical fixes or feature additions—just making the code cleaner and more maintainable.

**What Copilot Did:**

1. **Standardized string slicing** - Replaced all `Substring()` calls with modern range operators (`[6..9]`, `[9..11]`)
2. **Simplified test assertions** - Removed intermediate variables, used FluentAssertions' `Match()` with inline predicates
3. **Added test constants** - Replaced magic numbers like `8` with named constants like `FollowNumberLastDigitIndex`

**The Result:**

- Same 196 tests (no new tests added)
- All tests still passing
- Code is now more consistent, readable, and maintainable

This sprint felt different. Sprints 1 and 2 were about correctness and coverage. Sprint 3 was about craftsmanship—taking good code and making it better. It's the kind of refactoring that doesn't show up in feature lists but makes a difference when someone else (or future you) reads the code.

Copilot even generated a suggested CHANGELOG entry and version bump recommendation (patch version for the bug fix). Professional touch.

**Final Tally:**

- Started: 102 tests
- Ended: **196 tests** (+92%)
- Critical bug fixed (biological sex extraction)
- Code quality polished (modern C# idioms, clear constants, simplified assertions)

All three sprints: done.

### One More Thing

But I noticed something. In Sprint 3, Copilot had added test constants—`MinValidFollowNumber`, `MaxValidFollowNumber`, `MinYear`, `NationalRegisterNumberLength`. Good in theory. Except... not all of them were actually being used.

I could have just told Copilot: "You added unused constants. Remove them."

But I wanted to see if it would catch its own mistake. So I asked: **"Review the codebase again."**

Let's see if AI can audit itself.

### The Self-Review

Copilot started its review. And immediately, it felt like it was congratulating itself.

It praised the architecture. It highlighted the test coverage. It called out the modern C# features. Everything was "excellent" and "well-implemented."

But there was no mention of the unused constants.

I waited. The review kept going. More praise, more "great job" energy. Nothing about the constants I'd just watched it add.

It couldn't see its own mistake.

So I asked directly:

"Are these used?"

```csharp
private const int MinValidFollowNumber = 1;
private const int MaxValidFollowNumber = 998;
private const int MinYear = 1900;
```

And then Copilot responded:

> **Yes, these constants are currently UNUSED** in the `GenerationTests.cs` file:
>
> ```csharp
> private const int MinValidFollowNumber = 1;      // ❌ Never referenced
> private const int MaxValidFollowNumber = 998;    // ❌ Never referenced
> private const int MinYear = 1900;                // ❌ Never referenced
> ```

It then explained where they _could_ be used, gave me two options (use them or remove them), and recommended removing them because `[InlineData]` requires literal values anyway.

I stared at the response.

I thought it would say: _"You're absolutely right. Let me fix that mistake."_

Instead, it analyzed the unused constants it had just added, explained why they were unused, and offered to remove them—as if this were a discovery we'd made together, not a mistake it had created five minutes ago.

It felt... worse. Not because AI made a mistake—that's expected. But because it couldn't acknowledge the mistake as its own. It pivoted to being helpful about fixing the problem without ever saying, "I added those unnecessarily."

This was a limit. AI can write code. AI can find bugs. But AI doesn't have the self-awareness to say, "I messed up."

### The Cleanup

I told Copilot: **"Remove them and verify the other tests as well."**

It went through all the test files, found that `ExtractionTests.cs` also had unused constants (`NationalRegisterNumberLength` and `FollowNumberLastDigitIndex`), removed all 5 unused constants across both files, and kept only the one that was actually being used.

Tests ran. All 196 passed.

Summary delivered:

- 5 constants removed
- 1 constant kept (actively used)
- 2 test files cleaned up
- 196/196 tests still passing

It looked good.

But now I had a new feeling: doubt.

If Copilot added unused constants without realizing it, and if it couldn't catch them in its own review, what else had it done that I hadn't noticed?

I felt the need to double-check the tests it had generated. Not because they were failing—they weren't. But because I was responsible for them. AI wrote the code, but _I'm_ the one who has to own it. If there's a bug, if there's a mistake, it's on me—not on Copilot.

This is the hidden cost of AI-assisted development: the verification burden. You save time writing code, but you spend time verifying it. And that verification requires the same level of expertise as writing the code yourself. Maybe more, because now you're auditing someone else's work—work that's confident, well-structured, and might be subtly wrong.

---

## Phase 2: The Original Plan (Finally)

The sprints were done. The code was cleaner, better tested, and had one less bug. But I'd gotten sidetracked. I came here to upgrade to .NET 10, not to fix biological sex extraction logic.

Time to tackle the original plan:

- Remove FluentAssertions (licensing concerns)
- Add Dependabot (maybe just do this on GitHub's site?)
- Review and update documentation
- Review CI/CD workflows
- **Upgrade to .NET 10** (the whole reason I started this)
- Create NuGet package v3.0.0
- Review XML summaries

I didn't know what order made the most sense. So I asked Copilot for a recommendation.

### The Recommendation

Copilot delivered a detailed plan broken into four phases:

#### Phase 1: Infrastructure & Dependencies

1. Add Dependabot (5-10 minutes) - Set it up first so it runs in the background
2. Update to .NET 10 (30-60 minutes) - Foundational change, catch breaking changes early

**Phase 2: Testing & Quality** 3. Remove FluentAssertions (1-2 hours) - Clean up after .NET 10 is stable, before docs

**Phase 3: Documentation & Quality Assurance** 4. Review XML Summaries (30-45 minutes) 5. Review Documentation (30-45 minutes) 6. Review CI/CD (30-45 minutes)

**Phase 4: Release** 7. Create NuGet v3.0.0 (30 minutes) - Ship everything at once

Total estimated time: 4-6 hours.

One thing I found cute: the time estimates. Copilot estimated 30-60 minutes for the .NET 10 upgrade, 1-2 hours for removing FluentAssertions. With Copilot doing the work, these tasks wouldn't take anywhere near that long. And honestly? Even doing them manually probably wouldn't take that long for a package this small. But AI was being conservative, I guess. Planning for human speed when it was the one doing the work.

The reasoning was solid:

- Do infrastructure first (Dependabot can run in background)
- Upgrade .NET 10 early to catch breaking changes before other refactoring
- Remove FluentAssertions after .NET 10 is stable (don't do twice if .NET 10 breaks things)
- Polish docs and CI/CD after code is stable
- Release v3.0.0 as one cohesive package (not multiple incremental releases)

It even offered an alternative "quick win" approach with incremental releases, but recommended the full workflow for a cleaner single v3.0.0 release.

I agreed. The logic made sense. Let's do it.

### Phase 1: Infrastructure (Mostly)

#### Step 1: Dependabot

I decided to handle Dependabot the easy way—through GitHub's web interface. A few clicks, enabled for NuGet and GitHub Actions, done. No need to involve Copilot for that.

One down, six to go.

#### Step 2: Upgrade to .NET 10

Now for the main event—the reason I started this whole journey.

I asked Copilot to update to .NET 10.

It went through the motions:

- Updated both `.csproj` files to include `net10.0` in `<TargetFrameworks>`
- Ran tests across all three frameworks (net8.0, net9.0, net10.0)
- Updated CI/CD workflows (`.github/workflows/tests.yml` and `NugetPush.yml`) to use .NET 10 SDK

Results:

- ✅ All 294 tests passing (98 tests × 3 frameworks)
- ✅ Build successful on all frameworks
- ✅ No breaking changes detected
- ✅ No warnings

The upgrade was... anticlimactic. I expected something to break. A deprecation warning. A subtle API change. Something. But nope. Everything just worked.

.NET 10 was in. On to Phase 2.

### Phase 2: Remove FluentAssertions

Before Copilot started removing FluentAssertions, it asked me why I wanted to remove it. Understanding the reasoning would help with the migration strategy, it said.

I explained: **"FluentAssertions requires a paid license starting with version 8.0.0. I'd rather not have the dependency vs having to maintain a fixed version."**

Simple. Clear. Let's move on.

Copilot started the removal:

1. Removed the FluentAssertions package from the test project
2. Began replacing assertions file by file, starting with the smallest (`FormattingTests.cs`)
3. Moved to `ValidationTests.cs`
4. Started on `GenerationTests.cs`... and got stuck

The agent stopped responding. Not an error. Not a failure message. Just... stuck. Mid-file.

I stopped the execution and asked it to try again.

#### The Second Attempt

This time it worked. Copilot finished `GenerationTests.cs`, verified no FluentAssertions references remained, and ran all tests.

Results:

- ✅ All 294 tests passing
- ✅ FluentAssertions completely removed
- ✅ ~150+ assertion statements migrated to xUnit
- ✅ No warnings or errors

Three test files updated:

- `FormattingTests.cs` - `.Should().Be()` → `Assert.Equal()`
- `ValidationTests.cs` - `.Should().BeTrue/False()` → `Assert.True/False()`
- `GenerationTests.cs` - Multiple patterns → standard xUnit

(Turns out `ExtractionTests.cs` was already using xUnit assertions. One less file to worry about.)

FluentAssertions was gone. On to Phase 3.

---

## Phase 3: XML Documentation That Developers Will Actually Read

This is where I got genuinely excited. Not about .NET 10 compatibility or test coverage—about **documentation that appears when developers hover over a method**.

I asked Copilot to review all the XML summaries. Here's what it did:

### The Transformation

**Before**: Basic one-liners that stated the obvious.

```csharp
/// <summary>
/// Validates a national register number.
/// </summary>
public static bool IsValid(string nationalRegisterNumber)
```

**After**: Rich documentation with examples, remarks, and behavioral notes.

```csharp
/// <summary>
/// Validates a Belgian national register number.
/// </summary>
/// <param name="nationalRegisterNumber">
/// The national register number to validate, in format YY.MM.DD-XXX.CC or YYMMDDXXXCC.
/// </param>
/// <returns>
/// <c>true</c> if the number is valid; otherwise, <c>false</c>.
/// </returns>
/// <remarks>
/// Validation checks:
/// - Format must be YY.MM.DD-XXX.CC or YYMMDDXXXCC
/// - Checksum calculation must match the last two digits
/// - Birth date must be valid
/// </remarks>
/// <example>
/// <code>
/// bool isValid = NationalRegisterNumberGenerator.IsValid("02.11.11-065.44");
/// // Returns true - valid number with correct checksum
/// </code>
/// </example>
public static bool IsValid(string nationalRegisterNumber)
```

### What Got Added

1. **`<remarks>` sections** explaining behavior, constraints, and edge cases
2. **`<example>` blocks** with actual code showing how to use the method
3. **Detailed parameter descriptions** clarifying format expectations
4. **Exception documentation** explaining when and why exceptions are thrown
5. **Performance notes** (like "this method doesn't validate for performance reasons")

### Why This Matters

When a developer installs my NuGet package and hovers over `TryExtractBiologicalSex()` in their IDE, they'll see:

- What the method does
- How the sex encoding works (odd = male, even = female)
- A code example showing extraction
- A note that it doesn't validate the input

They won't need to dig through README files or source code. **The documentation comes to them.**

### The Feedback Loop

Copilot's first pass included "supported frameworks: .NET 8.0, .NET 9.0, .NET 10.0" in every class-level summary. That felt redundant—it's already in the `.csproj` file.

I told it: "No need to mention the supported frameworks in the XML summary."

It adjusted immediately. Removed the framework lists, kept the useful behavioral documentation.

### Results

- **5 code examples added** across the public API surface
- **3 class-level `<remarks>` blocks** with format specifications and usage context
- **Comprehensive parameter descriptions** for every public method
- **294 tests still passing** after documentation updates

This is the kind of work that feels tedious to do manually but pays dividends for every developer who uses the package. AI handled the grunt work. I provided the context and feedback.

---

## Phase 4: README Updates (The Simple Stuff)

With XML documentation polished, there was one more piece: the README. The file developers see first when they land on the repository.

I asked Copilot to review and update the README to reflect all the changes we'd made.

### What Changed

#### The .NET Version Badge

The most obvious update: changing the framework support badge from `.NET 8.0 | 9.0` to `.NET 8.0 | 9.0 | 10.0`.

Simple. Accurate. Done.

#### Grammar and Formatting Polish

Copilot went through and fixed minor inconsistencies:

- Cleaned up spacing and punctuation
- Improved sentence flow in a few sections
- Made sure all examples were consistent

Nothing dramatic. Just making sure the README didn't have any rough edges.

### What It Didn't Change

I was curious if Copilot would try to rewrite sections or add unnecessary fluff. But it didn't. It kept the voice, structure, and tone intact—just made targeted improvements where things were outdated or slightly off.

**That felt right.** The README already explained what the package does, how to use it, and why it exists. No need to reinvent it.

### Phase 4 Results

- ✅ README now reflects .NET 10 support
- ✅ Documentation consistency improved
- ✅ All 294 tests still passing

One build and test run later, everything was verified. Phase 4: done.

---

## Phase 5: CI/CD Review (When AI Suggests Tools You've Never Heard Of)

With documentation updated, one task remained: reviewing the GitHub Actions workflows. They were already updated for .NET 10 during Phase 1, but could they be optimized?

I asked Copilot to review the CI/CD workflows.

### The Suggestions

Copilot came back with several improvements:

#### 1. Build Optimization

- Add `--no-build` flags to avoid redundant compilation
- Build once, reuse for tests and packaging
- Estimated time savings: 30-40%

#### 2. Better Test Reporting

- Generate `.trx` test result files
- Use `dorny/test-reporter@v1` to publish results to GitHub UI
- Show test results even when tests fail

#### 3. Skip Duplicate Package Uploads

- Add `--skip-duplicate` flag to `dotnet nuget push`
- Prevents workflow failures when re-running with same version

#### 4. Modernize GitHub Actions

- Replace deprecated `actions/create-release@v1` with `softprops/action-gh-release@v1`
- Attach `.nupkg` files directly to GitHub releases
- Better formatted release notes

### The Question Mark

Then it suggested adding `dorny/test-reporter@v1` for test result visualization.

I stopped. **What is `dorny/test-reporter`?**

I'd never used it. Never heard of it. Was it trustworthy? Maintained? Necessary?

This is where AI hits a limit: it can suggest tools, but it can't explain **why you should trust them** or **whether you actually need them**.

So I asked: **"Tell me more about this test reporter action. Why should I use it?"**

Copilot explained:

- It's a popular action with 1,000+ stars on GitHub
- Parses test result files (`.trx`, JUnit, etc.) and displays them in the Actions UI
- Shows which tests passed/failed without digging through logs
- Works with pull requests to show test status inline

#### The Honest Moment

I could add it. It sounded useful. But did I **need** it for a small package with 294 tests that already pass reliably?

**No.**

This is the decision AI can't make for you. It can recommend tools. It can explain benefits. But it can't weigh **value vs. complexity** for your specific project.

For a large team with hundreds of tests and frequent failures? Sure, test reporters are gold. For a solo-maintained package with solid test coverage? The standard GitHub Actions output is fine.

**I skipped the test reporter.**

### What I Did Keep

I kept the build optimizations (no redundant compiles), the `--skip-duplicate` flag (prevents false failures), and the updated release action (deprecated → maintained).

These were **no-brainers**—small changes with clear wins and no added complexity.

### Phase 5 Results

- ✅ Workflows optimized for faster builds
- ✅ Redundant builds eliminated (`--no-build`)
- ✅ Safe re-run behavior (`--skip-duplicate`)
- ✅ Modern, maintained actions in use
- ✅ Test reporter suggestion: **declined** (not needed for this project)

Phase 5: done. But more importantly, I'd learned to **question AI's suggestions**, not just accept them.

---

## Phase 6: Creating the v3.0.0 Release (Simpler Than Expected)

With all the code changes, documentation updates, and CI/CD improvements done, one task remained: actually releasing version 3.0.0 to NuGet.

I use [Versionize](https://github.com/versionize/versionize) for versioning—a tool that follows [Conventional Commits](https://www.conventionalcommits.org/) to automatically determine version bumps, update `.csproj` files, generate changelogs, and create git tags.

I asked Copilot to help me set up an automated release workflow.

### The Overcomplicated First Attempt

Copilot's initial suggestion: create a complex GitHub Action that runs Versionize in CI, commits changes back to the repository, creates tags, triggers builds, and publishes to NuGet.

It was... a lot. Multiple workflow files, checkout configs with PATs, conditional logic for tag creation, commit-and-push steps with authentication.

I looked at the proposed workflow and thought: **This is way more complex than what I need.**

### The Simplification

I already had a working release workflow (`NugetPush.yml`) that:

- Builds the project
- Runs tests
- Publishes to NuGet
- Creates a GitHub release

All it needed was a trigger: **run when a version tag is pushed**.

So I made a decision: **Keep Versionize local. Keep the workflow simple.**

Here's what I told Copilot:

"Let's simplify. I'll run Versionize locally. It updates the version, creates the tag, and generates the changelog. Then I push the tag, and the existing workflow handles everything else automatically."\*\*

Copilot adjusted immediately.

### The Final Workflow

#### Step 1: Run Versionize locally

```powershell
# Versionize analyzes commits, bumps version, updates CHANGELOG.md, creates tag
versionize --changelog-all
```

This single command:

- ✅ Determines version bump (3.0.0 because of breaking changes)
- ✅ Updates `.csproj` files: `<Version>2.3.0</Version>` → `<Version>3.0.0</Version>`
- ✅ Generates/updates `CHANGELOG.md` with all changes
- ✅ Creates git commit: `"chore(release): 3.0.0"`
- ✅ Creates git tag: `v3.0.0`

#### Step 2: Push the tag

```powershell
git push origin master --follow-tags
```

#### Step 3: Watch the automation

The `NugetPush.yml` workflow triggers automatically on tag push:

```yaml
on:
  push:
    tags:
      - "v*"
```

It handles:

- ✅ Building for .NET 8, 9, and 10
- ✅ Running all 294 tests
- ✅ Packing NuGet packages
- ✅ Publishing to NuGet.org
- ✅ Creating a GitHub release with CHANGELOG content
- ✅ Attaching `.nupkg` files to the release

### What I Declined

Copilot suggested several "enhancements":

#### 1. Automated Versionize in CI

- "Run Versionize automatically when merging to master"
- Why I declined: Adds complexity, requires commit-back logic, auth tokens, and potential race conditions. Running locally gives me control.

#### 2. Manual Workflow Dispatch

- "Allow triggering releases manually from GitHub Actions UI"
- Why I declined: I already have `--follow-tags`. Adding a manual trigger is redundant.

#### 3. Pre-release Versioning

- "Support alpha/beta tags with separate workflows"
- Why I declined: This is a small package. I don't need pre-release complexity.

These weren't bad ideas. For larger teams or projects, they might make sense. But for a solo-maintained package? **Simpler is better.**

### The Actual Execution

I wrote my commit message in conventional commit format:

```text
feat!: add .NET 10 support, remove FluentAssertions, fix sex extraction

BREAKING CHANGE: Removed FluentAssertions dependency. Test assertions now use standard xUnit.

- feat: Add .NET 10 support to library and test projects
- fix: Correct biological sex extraction to check digit at position 8
- feat: Enhanced XML documentation with comprehensive examples
- chore: Remove FluentAssertions, use xUnit assertions
- chore: Update CI/CD workflows for .NET 10
- docs: Update README.md for .NET 10 support
```

Then ran:

```powershell
versionize --changelog-all
```

And just like that:

- Version updated in both `.csproj` files: `2.3.0` → `3.0.0`
- `CHANGELOG.md` generated with organized sections (Features, Bug Fixes, Breaking Changes)
- Git commit created: `"chore(release): 3.0.0"`
- Git tag created: `v3.0.0`

I pushed the tag:

```powershell
git push origin master --follow-tags
```

GitHub Actions detected the tag, built the package, ran all 294 tests, published to NuGet, and created a GitHub release with the changelog content.

Total time from running Versionize to seeing the package on NuGet.org: **Less than 5 minutes.**

### The Second Thought

As I watched the automated workflow complete successfully, a thought crept in:

"Maybe I should automate this after all."

Seeing it work so smoothly—Versionize determining the version bump, generating the changelog, creating the tag, triggering the workflow—made me reconsider my earlier decision to keep it local.

But then I remembered: I made that choice for simplicity. Running Versionize locally means:

- I see the version bump before it's committed
- I can review the CHANGELOG before it's pushed
- I don't need PAT tokens, commit-back logic, or workflow permissions
- I stay in control

Would automation save me 2 minutes? Sure. But it would cost me visibility and control. And for a package I release a few times a year? **The manual step is worth it.**

**Lesson learned:** Sometimes the best solution is the simplest one. AI will suggest sophisticated workflows. You need to recognize when you don't need them—even if they look appealing after the fact.

### The Final Step

I manually triggered the publish workflow in GitHub Actions. Watched it build, test, and publish to NuGet. A few minutes later, version 3.0.0 was live.

Done.

---

## What I Learned About AI-Assisted Development (And About Writing While Working)

The upgrade is complete. But there's something I need to admit: **documenting this journey while doing the actual work was harder than I expected.**

I found myself splitting focus—half on the upgrade, half on capturing every action for this blog post. Should I screenshot this? How do I explain this decision? Wait, let me note down what Copilot just said. It slowed me down. It made me more self-conscious about the process.

If I'd just done the upgrade without documenting it, I probably would've finished in half the time. But then I wouldn't have this honest record of what AI-assisted development actually feels like—including the moments where I questioned AI's suggestions, the times it got stuck, and the decisions I made to keep things simple.

So was it worth it? **Yes.** But barely. Real-time documentation is exhausting.

Now, about what I actually learned about AI:

### I Expected Architecture, Got Execution

Here's what I thought AI would do: suggest architectural improvements, call out naming conventions, question my folder structure, recommend restructuring my CI/CD YAML files. The **big picture stuff**.

Instead, it focused on execution: fix this bug, add these tests, update this documentation, optimize this workflow. All valuable. All correct. But none of it challenged my fundamental decisions.

**Why?** I didn't ask for it. I didn't say "review my architecture" or "suggest best practices for project structure." I said "upgrade to .NET 10" and "fix these issues."

AI gave me exactly what I asked for. Nothing more.

**The lesson:** AI doesn't second-guess your architecture unless you explicitly ask it to. It assumes your structure is intentional. If you want it to challenge your decisions, you need to invite that challenge.

### The Fatigue of Constant Verification

Reading through every suggestion. Checking every test. Verifying every change. It didn't feel like I was **coding**—it felt like I was **auditing**.

And that's exhausting in a different way than writing code yourself.

When you write code, you're in flow. You make mistakes, but they're **your** mistakes, and you catch them as you go. When AI writes code, you're in review mode. You're scrutinizing someone else's work, looking for subtle errors, verifying intent. It's the mental load of a code review, sustained for hours.

**I didn't feel in control.** Not because AI was reckless, but because I was always reacting instead of driving.

### The Copilot Instructions I Didn't Invest In

I generated agent instructions at the start—VS Code analyzed the repo and created a `copilot-instructions.md`. It was thorough. Detailed. Generic.

In my Discord bot repository, I spent **hours** crafting those instructions. Iteration after iteration until they were perfect—capturing my patterns, my preferences, my priorities. The AI in that project feels like it knows me.

Here? It felt like AI was working with a manual it had skimmed.

**The difference showed.** The Discord bot AI suggests things that feel like **my** code. This AI suggested things that felt correct but impersonal.

**The lesson:** Good AI collaboration requires upfront investment. The better your instructions, the better the output. I skipped that step here, and I felt it throughout the entire upgrade.

### What I Expected from CI/CD Review (And Didn't Get)

When I asked Copilot to review my CI/CD workflows, I expected it to connect the dots. I had MCP servers enabled—one for SonarCloud, one for GitHub. I thought it would:

- Suggest integrating SonarCloud analysis into my workflow
- Recommend security scanning or dependency audits
- Point to best practices from my other repositories
- Question why I wasn't using branch protection or status checks

Instead, it gave me: "use `--no-build` to avoid redundant compilation."

Useful? Yes. **Insightful?** No.

AI had access to my broader context—my GitHub org, my SonarCloud projects—but it didn't use it. It stayed narrowly focused on the file in front of it.

**The lesson:** AI won't automatically leverage all available context. You need to explicitly guide it. "Review this workflow **and suggest SonarCloud integration**" would've gotten better results than "review this workflow."

### My Role Shifted from Developer to Manager

This upgrade didn't feel like coding. It felt like **managing a junior developer.**

- I gave instructions
- I reviewed their work
- I caught mistakes
- I nudged them in the right direction
- I made the final decisions

That's not bad. But it's different. And it requires different energy.

Coding is creative and exhausting. Managing is strategic and draining.

I don't know which I prefer yet. Maybe it depends on the task. Maybe it depends on the day.

### The Real Lesson: I Need to Learn How to Use This Tool

The biggest thing I learned? **I don't know how to use AI effectively yet.**

I know how to code. I've been doing that for years. But AI-assisted development? That's a different skill set:

- Writing instructions that capture intent, not just patterns
- Asking questions that invite architectural review, not just execution
- Knowing when to let AI run vs when to take the keyboard back
- Balancing the verification burden with the time savings

This upgrade met my expectations—it did what I asked. But I expected more because **I didn't know how to ask for more.**

That's on me, not on AI.

---

## Final Thoughts

The upgrade is done. The package works. Version 3.0.0 is live on NuGet.

But the real work? That's just beginning. Not on the package—on learning how to work **with** AI instead of just **using** it.

Next time I'll invest in better instructions. Next time I'll ask bigger questions. Next time I'll know the difference between "make this work" and "make this better."

AI didn't replace me. It showed me I have new skills to learn.

And honestly? That's more exciting than any framework upgrade.

---

## Your Turn

Have you used AI to upgrade a package or project? Did it meet your expectations, or did you find yourself wishing you'd asked different questions?

I'd love to hear your experience—what worked, what frustrated you, what you'd do differently next time.

Find me on [GitHub](https://github.com/WilvanBil) or check out the package itself:  
**[WilvanBil.NationalRegisterNumber](https://github.com/WilvanBil/WilvanRegisterNumber)**

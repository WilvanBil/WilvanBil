---
layout: post
title:  "What I've Learned While Updating My NuGet Package"
date:   2025-01-23 21:25:42 +0100
categories: NuGet package
---

Here's the package:  
[National Register Number](https://github.com/WilvanBil/WilvanBil.NationalRegisterNumber)  
All usage information can be found in the `README.md`.

---

### Why I Revisited the Package

Three years ago, I created a NuGet package to generate and validate Belgian national register numbers. It started as a fun side project that taught me a lot about creating NuGet packages and using GitHub effectively. However, as time passed, both .NET and my development skills evolved significantly. Revisiting the package felt like a great opportunity to bring it up to modern standards.

This update wasn’t just about the code. It was about rethinking how users would interact with the package. I wanted to ensure the package was clear, usable, and intuitive for developers while reflecting the practices I’ve learned in my career.

---

### What I Updated

1. **Modernized the Codebase**:
   - Replaced all instances of `DateTime` with `DateOnly` for more contextually appropriate handling of dates.
   - Introduced a `TimeProvider` abstraction instead of relying on `DateTime.UtcNow`, making the package easier to test and more flexible.
   - Added a new feature to format national register numbers into a more readable format (`YY.MM.DD-XXX.CC`).

2. **Targeted Latest .NET Versions**:
   - The package now supports **.NET 8** and **.NET 9**, aligning with [Microsoft’s support lifecycle](https://dotnet.microsoft.com/en-us/platform/support/policy/dotnet-core). This ensures developers can use the package in their modern projects while benefiting from long-term support.

3. **Improved Documentation**:
   - Enhanced the `README.md` with clear usage examples and contribution guidelines.
   - Fixed an issue where XML summaries weren’t included in the NuGet package, ensuring better IntelliSense for developers.

4. **Workflows and Automation**:
   - Set up GitHub Actions for CI/CD to automate testing and deployment.
   - Improved commit message etiquette using `versionize`, resulting in cleaner, auto-generated changelogs.

5. **Property-Based Testing**:
   - Implemented property-based tests inspired by Bart Wullems' [blog post](https://bartwullems.blogspot.com/2023/01/property-based-testing-in-cpart-3.html) about my package. This approach ensures better test coverage and robustness.

6. **Package Hosting**:
   - Learned the differences between **NuGet packages** and **GitHub Packages**. I’ve chosen to host the package exclusively on NuGet for now, as it’s the primary platform developers use for .NET libraries.

---

### Lessons Learned

This update taught me some valuable lessons about creating and maintaining a NuGet package:

1. **Automation Saves Time**:
   Automating builds, testing, and deployments through GitHub Actions simplified my workflow and ensured consistency with every update.

2. **Developer Experience Matters**:
   Adding features like XML summaries, a formatting method, and clear documentation significantly improved the usability of the package. A great developer experience goes beyond just writing good code.

3. **Stay Up-to-Date**:
   Updating the package to support .NET 8 and 9 ensures it remains relevant and usable for modern projects. This also showed me the importance of keeping track of the .NET lifecycle.

4. **Learn by Doing**:
   Don’t be afraid to experiment and make mistakes. Revisiting this project gave me the chance to reflect on my growth as a developer and apply what I’ve learned in a meaningful way.

---

### Future Improvements

There are still a few things I want to tackle:
- **GitHub Releases**: I’m exploring how to automatically include changelog details in the release notes. This would streamline the release process and make it more informative.
- **Add an Icon**: To make the package feel “official,” I want to design a cool-looking icon for it. After all, a little visual polish never hurts! 😉

---

### Final Thoughts

Reflecting on this update has made me appreciate the importance of balancing technical skill with user empathy. Whether it’s improving workflows, refactoring code, or clarifying documentation, every small improvement adds up to a better experience for the people who use your work. If you’re thinking about creating or updating your own NuGet package, I hope this inspires you to give it a shot. You’ll learn more than you expect—and maybe even have some fun along the way!

You can check out the updated package here:  
[National Register Number on GitHub](https://github.com/WilvanBil/WilvanBil.NationalRegisterNumber)

And who knows—maybe the next time you visit the package, it’ll have a shiny new icon! 😄

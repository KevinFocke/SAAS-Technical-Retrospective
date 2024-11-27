# Sonicaria, A Technical Retrospective.

Product: Software Documentation Tooling that integrates with your existing code
editor.

---

Software documentation is [valuable](#TODO), yet in 2024 most developers rarely
or never write it. 

My SAAS, Sonicaria, attempted to solve that problem—and
failed.

We will first look at the general challenges of software documentation, before
looking at the technical challenges of my approach.

---

# General Documentation Challenges

- Documentation is quickly outdated in agile teams. This is still a huge,
  unsolved problem.

  - How can you detect outdated documentation—and correct it in a user-friendly
    way?

- Website hosting for documentation is a hassle that developers don't want
  to deal with.

  - Which features does your documentation website have?
  - How much customization do you offer?
    - Can the developer change the layout?
    - Can the developer extend the funtionality? How does this integrate with
      the writing?

- Do you support different content outputs (like a .pdf for printed manuals)?

- How does the developer write the documentation?

  - Can developers use their existing integrated development environment (IDE)?

    - Highly-productive developers use Vim or Emacs. Can they use it?
    - Do you detect problems in the content (and suggest solutions?)
    - Do you offer code completion shortcuts for common functionality?
    - Do you integrate a spell checker? Which language(s)?

  - Can you preview the final output(s)?

- Do you analyze the source code automatically (eg. Doxygen)?

  - Which programming language(s) do you support?

- How do you structure a project and connect information? (Folders, references…)

- Do you support a content pipeline, users, and oversight?

# Sonicaria Product Vision

You're a developer, not a designer.

You want to quickly write documentation in VSCode with language server support.

Publish it with a single CLI command.

<img width="472" alt="Software Documentation" src="https://github.com/user-attachments/assets/cf2c9f26-c4f1-4208-a4d9-a60e1fcedc7c">

---

Closest competitor with a similar approach: [AsciiDoc](https://asciidoc.org/).
My approach was also inspired by [Matklad's writing](https://matklad.github.io/2022/10/28/elements-of-a-great-markup-language.html) . Matklad is the main programmer for the rust-analyzer, a rust compiler front-end for IDEs.

The programs (parser, compiler) were written in Rust. Plain Javascript was the web language.

---

# Why Sonicaria Failed

Failure is never easy, but it's important we learn from our mistakes.

Sonicaria was tremendously ambitious. Here's an example scenario illustrating the complexity:

```
As of Unicode version 15.1 there are 149,878 different characters,
from the letters of the latin alphabet to Chinese (simplified & traditional) to ancient Egyptian hieroglyphs,
and emojis.

The numbers are grouped per language. They are represented by a hexadecimal number because it's quicker to write and read
for humans. Furthermore, to indicate that we're talking about a Unicode group, the hexadecimal number is prefixed with 'U+'.
In other words, the prefix `U+` indicates that the number should be interpreted as the `type` Unicode. More on that later.
Similarly, raw hexadecimal (that should not be interpreted as a Unicode group) are often prefixed with 0x.

[table intact=true]
| Symbol | Decimal | Binary   | Hexadecimal | Unicode Code Point | Description          |
| ------ | ------- | ---------| ----------- | ------------------ | -------------------- |
|   a    | 97      | 01100001 | 0x0061      | U+0061             | Latin Small Letter A |
|   b    | 98      | 01100010 | 0x0062      | U+0062             | Latin Small Letter B |
|   c    | 99      | 01100011 | 0x0063      | U+0062             | Latin Small Letter C |
|   …    | ...     | ........ | ......      | ......             | .................... |
[/table]
```
You want to write a document and output it to a website and .pdf.

The biggest challenge is an _impedance mismatch_; different technologies (HTML for websites, PostScript for PDF) have different content models which means you need to translate to _both_ models:

- HTML is _reflowable_ which means text grows and shrinks when you zoom in. For example, on mobile, you will need a scroll bar to properly display the table. Alternatively, you can convert the table into an image, but then you lose the ability to copy and paste from the table.
- PDF works with a fixed-page layout. This means you need to think about page breaks. The property `intact=true` indicates that the table should be displayed as a whole; the table must not be cut in half.

I also wanted to offer an instant preview of how the final output would look.

To achieve this functionality, you need to:

1. Convert the Markup language to a high-level intermediate representation (HIR). This includes customization.
2. From the HIR, figure out which features are supported by each output format.
3. Render the HIR to the output format. Inform the user if a feature is not supported by output format (or renderer).
4. Display the preview. The final website (HTML + CSS layout) and the downloadable PDF. Making the render work properly and look decent is a major challenge—and you also have to maintain them as the HTML + CSS + PDF standards evolve.
5. Pointing out a subtlety: You need to convert the `&` to `&amp;` in HTML.

The second major challenge is creating a markup language that is extendable and customizable:

- Hyperlinks and content organization are remarkably tricky. The Table of Contents is generally on the second or third page of a .pdf, but on a website it's in the sidebar or in the header. This is another instance of an _impedance mismatch_.

The most popular web framework, NextJS, solves content organization with two different approaches: [The App Router & The Page Router](https://nextjs.org/docs/app/building-your-application/routing). This indicates the complexity of the problem. You also have to decide between client-side rendering, server-side rendering, pre-rendering, or a mix. Personally, I aimed for a static website that you could fully run offline (after caching locally, of course).
- How do you differentiate between prose and syntax? It's a delicate balance between _convenience_ and _clarity_. For example, in the Markup Language Markdown, you can surround a text with two asterisks `**text**` and have it displayed **bold**.
- Users expect auto-completions (+ syntax highlighting) from their code editor. Most of the time, your code is in an incomplete state. This means the parser needs to have error recovery strategies _and_ suggestions to fix the problem.
This is extra challenging to integrate with extendability because it requires domain knowledge about common usage patterns and specialist parsing knowledge.
- How do you handle interactive components?
- Among other sub-challenges…

In the end, I set a very tight deadline for each major component. It was too ambitious for a one-man project.

# What I Would Do Differently

- A software SAAS generally has multiple months of R&D before going public. However, I would create a workable end-to-end prototype quickly instead of focusing on the compiler. Yes, the compiler is a core component that gives a competitive advantage, but it's much more fulfilling to _see_ a vertical slice of the end result rather than a dense tree-like content structure.
- Focus only on website output. That's already a LOT of work.
- Be less ambitious. By nature, I want to go the extra mile. But it's also important to walk before you can run.

Of course, it's more fun to share your successes, but I learned a ton about programming language design (ergonomics, parsing, compilation, rendering, LSP, error handling…), web development (computer networking, components-based rendering, servers, …), marketing, and business analysis. Skills that will stay with me for the rest of my career. On the upside, I was also glad that I could quickly get productive in a new language (Rust). This was possible because I learned coding from first principles.

# What's Next

I am open for new opportunities! You can find my CV here: [https://kevinfocke.com/Kevin-Focke-CV.pdf](https://kevinfocke.com/Kevin-Focke-CV.pdf).

Furthermore, as a hobby, I am writing a book teaching the high-level basics of computers called  _The Code Monster Manual_. The first volume is done, two to go!

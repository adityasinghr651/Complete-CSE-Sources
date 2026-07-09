# Contributing to Complete Backend Engineering

First off, thank you for considering contributing to the **Complete Backend Engineering** handbook! 🎉

This is an open-source initiative aimed at building the most comprehensive and high-quality backend engineering resource on the internet. We welcome contributions from everyone, whether it's fixing a typo, improving a diagram, or writing a whole new topic.

## 🤝 How Can I Contribute?

### 1. 🐛 Reporting Bugs or Typos
If you find a typo, a broken link, or a technical inaccuracy, please open an issue!
- Go to the **Issues** tab.
- Create a new issue describing the problem.
- If you know how to fix it, feel free to submit a Pull Request.

### 2. 📝 Improving Documentation
The core of this project is documentation. If you can explain a concept better, add a production example, or improve the simple analogy sections, we would love your help.
- Pick a topic that is marked as `> Status: 🚧 Coming Soon` or an existing one that can be improved.
- Make sure to follow the **Markdown Style Guide** below.

### 3. 🎨 Adding or Improving Diagrams
We love visual learning! If you can create a Mermaid diagram or a clean ASCII diagram for a complex concept, please contribute it. All diagrams should be text-based (Mermaid) where possible, or placed in the relevant `assets/diagrams/` folder.

## 🛠️ Pull Request Process

1. **Fork** the repository and create your branch from `main`.
   ```bash
   git checkout -b feature/your-feature-name
   ```
2. **Make your changes**. Ensure your markdown renders correctly.
3. **Commit** your changes using descriptive commit messages.
   ```bash
   git commit -m "docs(phase-1): improve TCP/IP explanation"
   ```
4. **Push** to your fork and submit a **Pull Request** to the `main` branch.
5. **Review**: A maintainer will review your PR, suggest changes if needed, and merge it!

## 📏 Markdown Style Guide

To maintain textbook-quality consistency across the handbook, please adhere to these guidelines:

- **Headings**: Use `#` for the main title, `##` for major sections, and `###` for sub-sections.
- **Alerts/Callouts**: Use GitHub's alert syntax for important notes.
  ```markdown
  > [!NOTE]
  > This is a helpful tip or intuition note.
  ```
- **Code Blocks**: Always specify the language for syntax highlighting (e.g., ```js, ```sql, ```bash).
- **File Links**: Use relative links to reference other markdown files within the repository.
- **Naming Conventions**: Folders and markdown files should always be lowercase, separated by hyphens (e.g., `01-internet-fundamentals`, `01-how-the-internet-works.md`).

## ❓ Need Help?
If you have any questions, check our [FAQ.md](./FAQ.md) or open a discussion on GitHub.

Thank you for helping us make backend engineering accessible to everyone! 🚀

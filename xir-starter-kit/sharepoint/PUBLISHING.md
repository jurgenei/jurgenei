# Publishing the XIR Collection on SharePoint

Keep the package together in one document-library folder.

```text
XIR/
  README.md
  SKILLS.md
  .github/AGENTS.md
  spec/
  examples/
  sharepoint/
```

Use one canonical copy of the language definition. Other SharePoint pages, documents, repositories, and collections should link to [spec/XIR-LANGUAGE.md](../spec/XIR-LANGUAGE.md) and [spec/XIR-QUICK-REFERENCE.md](../spec/XIR-QUICK-REFERENCE.md) instead of copying them.

If a consuming system ignores hidden folders, duplicate only the pointer, not the specification. For example, add a top-level [AGENTS.md](../AGENTS.md) that points to [.github/AGENTS.md](../.github/AGENTS.md), or move [AGENTS.md](../AGENTS.md) to the collection root.

For SharePoint indexing, use clear filenames and keep the quick reference near the XIR examples. Do not rely on folder context alone to explain a file.

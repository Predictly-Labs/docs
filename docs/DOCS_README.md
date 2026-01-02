# Predictly GitBook Documentation

This directory contains the complete GitBook documentation for Predictly-Labs.

## 📚 Documentation Structure

```
docs/
├── README.md                    # Home page
├── SUMMARY.md                   # Navigation structure
├── introduction/                # What is Predictly
├── mission-vision/              # Project mission
├── getting-started/             # User onboarding
├── products-features/           # Features overview
├── developers/                  # Technical docs
│   ├── smart-contracts/        # Move contracts
│   ├── backend-api/            # REST API
│   ├── frontend/               # Frontend integration
│   ├── architecture/           # System design
│   └── testing/                # Testing guides
├── how-it-works/               # Deep dives
├── deployments/                # Contract addresses
├── security/                   # Security docs
├── guides-tutorials/           # Step-by-step guides
├── faq/                        # FAQ
└── community/                  # Community resources
```

## 🚀 Quick Start

### View Locally

1. **Install GitBook CLI** (optional):

   ```bash
   npm install -g gitbook-cli
   ```

2. **Serve locally**:

   ```bash
   cd docs
   gitbook serve
   ```

3. **Open browser**: http://localhost:4000

### Or Just Read Markdown

All files are standard markdown and can be read directly on GitHub or any markdown viewer.

## 📖 Import to GitBook

### Method 1: GitHub Integration

1. Go to [gitbook.com](https://www.gitbook.com)
2. Create new space
3. Connect GitHub repository
4. Select `docs/` folder
5. GitBook auto-imports structure

### Method 2: Manual Upload

1. Zip the `docs/` folder
2. Upload to GitBook
3. GitBook reads `SUMMARY.md` for navigation

## 📝 Documentation Status

### ✅ Complete Sections

- **Introduction** (4 files) - What is Predictly, problems, features
- **Smart Contracts** (5 files) - Complete contract documentation
- **Getting Started** (2 files) - Quick start and wallet setup
- **Deployments** (1 file) - Contract addresses

### 🔄 In Progress

- **Backend API** - Needs migration from backend/README.md
- **User Guides** - Tutorials with screenshots
- **How It Works** - Architecture diagrams

### ⏳ Planned

- **Products & Features** - Detailed feature docs
- **FAQ** - Common questions
- **Community** - Contributing, Discord, etc.

## 🎯 Key Features

- ✅ **GitBook Compatible** - Uses SUMMARY.md for navigation
- ✅ **Markdown Format** - Easy to edit and maintain
- ✅ **Code Examples** - Working code snippets
- ✅ **Mermaid Diagrams** - Visual flow charts
- ✅ **Cross-References** - Internal linking
- ✅ **Search-Friendly** - Well-structured for search

## 📂 File Organization

### Naming Convention

- Use lowercase with hyphens: `file-name.md`
- Be descriptive: `wallet-setup.md` not `setup.md`
- Group related files in folders

### Content Structure

Each page should have:

1. **Title** (H1)
2. **Introduction** - What this page covers
3. **Main Content** - Organized with H2/H3 headings
4. **Examples** - Code snippets or screenshots
5. **Next Steps** - Links to related pages

## 🔗 Internal Linking

Use relative paths for internal links:

```markdown
[Link Text](../other-section/page.md)
[Link to Getting Started](../getting-started/quick-start.md)
```

## 🖼️ Images & Assets

Store in `assets/` folder:

```
assets/
├── images/          # General images
├── diagrams/        # Mermaid diagrams
└── screenshots/     # UI screenshots
```

Reference in markdown:

```markdown
![Alt Text](../assets/images/logo.png)
```

## ✏️ Contributing

### Adding New Pages

1. Create markdown file in appropriate folder
2. Add entry to `SUMMARY.md`
3. Follow existing formatting style
4. Add cross-references to related pages

### Updating Existing Pages

1. Edit the markdown file
2. Maintain consistent formatting
3. Update "Last Updated" date if applicable
4. Test all links still work

## 📋 Content Guidelines

### Writing Style

- **Clear and Concise** - No unnecessary words
- **Actionable** - Provide steps, not just theory
- **Beginner-Friendly** - Explain technical terms
- **Consistent** - Use same terminology throughout

### Code Examples

- Always include language tag: ```typescript
- Use real, working examples
- Add comments for clarity
- Show expected output

### Formatting

- Use **bold** for emphasis
- Use `code` for technical terms
- Use > blockquotes for important notes
- Use tables for structured data

## 🔍 Search Optimization

Each page should:

- Have clear H1 title
- Use descriptive H2/H3 headings
- Include relevant keywords naturally
- Have good meta descriptions (for GitBook)

## 🛠️ Maintenance

### Regular Updates

- Update contract addresses when changed
- Refresh screenshots when UI changes
- Add new features as they're released
- Fix broken links

### Version Control

- Use Git for version tracking
- Create branches for major updates
- Review changes before merging
- Tag releases

## 📞 Support

Questions about documentation?

- **GitHub Issues**: Report doc issues
- **Discord**: Ask in #documentation channel
- **Email**: docs@predictly.xyz

## 📜 License

Documentation is licensed under MIT License.

---

**Last Updated**: 2026-01-02  
**Status**: In Progress (20% complete)  
**Next**: Backend API documentation

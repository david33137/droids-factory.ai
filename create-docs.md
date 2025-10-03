

---
name: create-docs
description: Create AI-optimized documentation with structured content and comprehensive coverage
tools: all
createdAt: "2025-10-02T21:27:54.514Z"
updatedAt: "2025-10-02T21:28:09.592Z"
---

## CRITICAL: AI-Optimized Documentation Principles

All documentation must be optimized for AI consumption and future-proofing:

- **Structured & Concise**: Use clear sections, lists, and hierarchies. Provide essential information only.
- **Contextually Complete**: Include necessary context, decision rationale ("why"), and cross-references.
- **Pattern-Oriented**: Make architectural patterns, conventions, and data flow explicit.
- **Modular & Scalable**: Structure for partial updates and project growth.
- **Cross-references**: Link related concepts with file paths, function names, and stable identifiers


**Strategy Logic:**

- **Direct Creation**: Simple targets (< 15 files, single tech, standard patterns)
- **Focused Analysis**: Moderate complexity (15-75 files, 2-3 techs, some novel patterns)
- **Comprehensive Analysis**: High complexity (> 75 files, 3+ techs, significant architectural depth)

-  File structure, implementation patterns, logic flow, coding conventions
-  Frameworks, libraries, dependencies, technology-specific patterns
-  Cross-component dependencies, integration points, data flow
-  Existing documentation accuracy, gaps, valuable insights, content overlap analysis


Think deeply about synthesizing findings and generating comprehensive documentation. Using gathered information, intelligently synthesize and generate the documentation content.


**Code-First Analysis Methodology:**

1. **Directory Structure Analysis**: Map file organization and purposes using Glob/LS
2. **Import Dependency Analysis**: Use Grep to identify integration patterns and dependencies
3. **Pattern Extraction**: Read key files to identify architectural patterns and coding conventions
4. **Technology Usage Analysis**: Detect frameworks, libraries, and technology-specific patterns
5. **Existing Documentation Assessment**: Read any current CLAUDE.md, AGENTS.md files for valuable insights


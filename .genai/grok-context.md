# Reusable Context Template

## Goals and Projects
- **Current Goals**: Convert the given content to jekyll blog post.
- **Specific Needs**: 
  - Assistance in generating blog posts in Jekyll format based on the article content given.
  - The original content and links should be used without any change.
  - The article should be formatted with markdown
  - Blog post front-matter should follow this format:
    ```
    ---
    layout: post
    title: [Distribution Name and Version] Released with [Key Features]
    categories: [e.g., kali, ubuntu, fedora, security, linux]
    description: [Brief summary of the release and its key features]
    keywords: [Relevant keywords for SEO]
    image: /assets/images/post-images/[distro]/[version].webp
    ---
    ```
  - Generate SEO-friendly Jekyll post filenames in the format `YYYY-MM-DD-title.md`, where:
    - `YYYY-MM-DD` is the release date (or current date if not specified)
    - `title` includes the distribution name, version, and keywords like "release" or "features"
    - Use lowercase, hyphens between words, and avoid special characters
    - Example: `2025-09-23-kali-linux-2025-3-release.md`

## Additional Notes
- Prefer blog posts to be structured as news stories with clear, accessible language
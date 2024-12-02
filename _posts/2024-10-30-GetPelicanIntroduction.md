---
layout: post
title: Getting Started with GetPelican
tags: [python]
---


## Getting Started with GetPelican: Basic Commands

If you're ready to dive into the world of static site generation with **GetPelican**, here are some basic command-line instructions to get you started. These commands will help you set up your Pelican site quickly and efficiently.

### Basic Command Line Instructions

1. **Install Pelican**:
   Make sure you have Python and pip installed. Then, run the following command to install Pelican and its dependencies:

   ```bash
   pip install pelican markdown
   ```

2. **Create a New Pelican Project**:
   Use the following command to create a new project folder:

   ```bash
   pelican-quickstart
   ```

   This will prompt you for some basic configuration options like site title, author name, and more.

3. **Write Content**:
   Create a new Markdown file in the `content` directory. For example:

   ```bash
   echo "# My First Post" > content/my_first_post.md
   ```

4. **Generate the Site**:
   To generate your static site from the content you've created, run:

   ```bash
   pelican content
   ```

5. **Preview Your Site**:
   To preview your site locally, you can run:

   ```bash
   pelican --listen
   ```

   Then open your browser and go to `http://localhost:8000`.

### Comparing GetPelican with Jekyll

Now that you've seen the basic commands for GetPelican, let’s compare these with **Jekyll** to highlight their similarities and differences.

#### Installation

- **Pelican**:
  - Install via pip: `pip install pelican markdown`.

- **Jekyll**:
  - Requires Ruby and can be installed using gem: 
    ```bash
    gem install bundler jekyll
    ```

#### Project Creation

- **Pelican**:
  - Use `pelican-quickstart` to set up a new project.

- **Jekyll**:
  - Create a new site with:
    ```bash
    jekyll new my-awesome-site
    ```

#### Writing Content

- **Pelican**:
  - Create Markdown files in the `content` directory.

- **Jekyll**:
  - Write content in Markdown files within the `_posts` directory, following a specific naming convention (e.g., `YYYY-MM-DD-title.md`).

#### Site Generation

- **Pelican**:
  - Generate the site with `pelican content`.

- **Jekyll**:
  - Build the site using:
    ```bash
    jekyll build
    ```

#### Previewing the Site

- **Pelican**:
  - Use `pelican --listen` to preview your site at `http://localhost:8000`.

- **Jekyll**:
  - Preview with:
    ```bash
    jekyll serve
    ```
    This will also run a local server at `http://localhost:4000`.

#### Summary of Differences

- **Language**: Pelican uses Python, while Jekyll is built with Ruby.
- **Hosting**: Jekyll has native integration with GitHub Pages, making it easier for deployment, while Pelican is more flexible with hosting options.
- **Content Management**: Pelican supports reStructuredText in addition to Markdown, whereas Jekyll primarily focuses on Markdown.

### Conclusion

Both GetPelican and Jekyll offer powerful features for static site generation, each with its own strengths and ideal use cases. Whether you prefer the Python ecosystem or the Ruby framework, both tools provide a solid foundation for creating and managing static websites. Choose the one that aligns best with your workflow and preferences!

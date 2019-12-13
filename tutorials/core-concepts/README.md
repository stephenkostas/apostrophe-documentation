---
title: "Core Concepts"
layout: reference
children:
  - connecting-pages
  - page-templates
  - widgets-singletons-and-areas
  - stanrdard-widgets
  - custom-widgets
  - layout-widgets
  - reusable-content-with-pieces
  - displaying-pieces-with-widgets
  - browsing-directory-of-pieces
  - children-and-joins  
  - search
  - nunjucks-filters
  - nunjucks-helper-functions
  - responsive-images
  - thumbnails-and-areas-of-child-pages
  - settings
  - global
  - how-apostrophe-modules-are-structured
  - nested-module-folders
  - more-modules
  - lean-frontend-assets
  - pushing-assets
  - users-and-groups
  - user-login-and-password  
---

# Building a Site with Apostrophe

Apostrophe provides all the tools you need to build beautiful, dynamic content for your enterprise, small business, or personal site. With a lightweight, straightforward design using [Node.js](https://nodejs.org), you can get it fired up and get started creating content right away. Before you you jump in though, you might want to learn about the Apostrophe specific terms and technology that you're going to love using.

## Content Types

"Content" what does it mean anyway? Content is anything and everything that the user sees: the page, the images, the navigation, it's all part of the user experience. In Apostrophe, the first things you'll need to understand are Pages, Widgets, and Pieces.

### Pages, Widgets, and Pieces

[Content starts with a **page**](pages-and-navigation/README.md). A page in and will likely only contain elements like a title, navigation, and a footer which together serve as the basis for displaying other types of content.

[Next, you add **widgets** on that page](editable-content-on-pages/README.md). A widget is a piece of content or functionality added to a page. It might be a text area that a writer can use to write a blog on that page, or a carousel style display for images of artwork. Widgets can be configured as **singletons**, where a single widget is displayed, or in **areas** where multiple widgets of different types can be added and arranged on a page.

[**Pieces** are what bring it all together](reusable-content-pieces/README.md). They are Apostrophe's primary tool for user's to create content. Using pieces a developer can define a myriad of content types and relationships between content types that writers and editors can use to build structured content.

In addition to these core concepts, you'll also work with tools like [**tags** and **attachments**](other-content.md) to build a robust site.

Whatever kind of content you're creating, in the database, everything is stored as a **doc**. Each doc might contain a variety of content: rich text, images, videos and even "blog widgets" that display blog posts.

### Global Content

When you create content, it usually belongs to the specific page where you created it, but some content, like a footer, needs to be loaded and used across the site. [The **Global Doc**](global-settings/README.md) allows you to create content that will be shared site-wide.

## Managing Your Site

In order to create and display your content well, there are other aspects of site management that you need to understand, like modules, assets, permissions, templates, and search.

### Modules

Every aspect of Apostrophe is contained in a [**module**](modules/README.md). Different modules all work together to create the final user experience. Understanding modules is key to building your site with Apostrophe.

### Front End Assets

[**Assets**](front-end-assets/README.md) are the CSS and JS files that you use to provide unique styling and dynamic behavior for your site. 

### Users and Permissions

Unless you're flying solo and performing all the functions of site administration, management, and content creation all by yourself, you're going to need to allocate permissions to different users to accomplish different tasks. In our [Users and Permissions section](users-and-permissions/README.md) you can learn different ways to manage Groups for access control.

### Working with Templates

Creating and managing **Templates** are at the core of creating and displaying content. Templates define your general layout and available options for pages, and use Nunjucks for dynamic templating.

### Apostrophe Search

Apostrophe provides powerful built in [search](search/README.md) to help users easily get meaningful results for the content of your site.

## Learn More

There's a lot more to Apostrophe than we have covered here, but you're probably ready to get your hands dirty. If you're looking for a good read, grab a cold beverage and check out [Technical Overview: 30,000 Feet](technical-overview.md) for more insight into the technology we use and the design decisions behind Apostrophe,

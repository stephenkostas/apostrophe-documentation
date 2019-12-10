# Building a Site with Apostrophe

Apostrophe provides all the tools you need to build beautiful, dynamic content for your enterprise, small business, or personal site. With a lightweight, straightforward design using [Node.js](https://nodejs.org), you can get it fired up and get started creating content right away. Before you you jump in though, you might want to learn about the Apostrophe specific terms and technology that you're going to love using.

## Content Types

"Content" what does it mean anyway? Content is anything and everything that the user sees: the page, the images, the navigation, it's all part of the user experience. In Apostrophe, the first things you'll need to understand are Pages, Widgets, and Pieces.

## Pages, Widgets, and Pieces

[Content starts with a **page**](pages-and-navigation/README.md). A page in and will likely only contain elements like a title, navigation, and a footer which together serve as the basis for displaying other types of content.

[Next, you add **widgets** on that page](editable-content-on-pages/README.md). A widget is a piece of content or functionality added to a page. It might be a text area that a writer can use to write a blog on that page, or a carousel style display for images of artwork. Widgets can be configured as **singletons**, where a single widget is displayed, or in **areas** where multiple widgets of different types can be added and arranged on a page.

[**Pieces** are what bring it all together](reusable-content-pieces/README.md). They are Apostrophe's primary tool for user's to create content. Using pieces a developer can define a myriad of content types and relationships between content types that writers and editors can use to build structured content.

Whatever kind of content you're creating, in the database, everything is stored as a **doc**. Each doc might contain a variety of content: rich text, images, videos and even "blog widgets" that display blog posts.


### Global Content

When you create content, it usually belongs to the specific page where you created it, but some content, like a footer, needs to be loaded and used across the site. [The **Global Doc**](global-settings/README.md) allows you to create content that will be shared site-wide.

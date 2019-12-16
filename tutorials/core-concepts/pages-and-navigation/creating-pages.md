# Building Navigation

The Vikings believed that all of life all connected back to the world tree Yggrasil. Much like Viking mythology, all pages in Apostrophe are part of a "tree" in which every page is a child of another page and all the branches connect back to the Home page. The connection between pages with Home at the root is what makes visualizing and building navigation in Apostrophe work.

Below you can find examples for several core navigation components. To test them out, add them to the `home.html` template of an existing Apostrophe project.

## Creating Pages

When you first start Apostrophe, you have have one page, "Home", and one template to work with, `home.html`. While it might not be particularly functional, we can use this template to create pages to demonstrate Apostrophe's page tree and navigation.

### Example: Creating Pages in the UI

Let's create three pages to demonstrate Apostrophe's page tree.

1. Make sure you're logged on to Apostrophe.

2. Open the admin bar and select *Pages*.

SCREENSHOT

You are now looking at the **Reorganize Pages** interface. You can use this page to create, delete, and reorganize your pages.

1. Click *New Page*.

2. Enter *About Us* for the name.

3. Leave the other options as the default.

4. Click *Save*.

You'll immediate be taken to the new page, but we're not ready to start editing pages yet.

1. Go back to Admin &rarr; *Pages*.

2. Create two more pages named "Contact" and "Store" under the "Home" page.

    Your page tree should now look like this:

SCREENSHOT

3. Drag "Contact" under "About Us" to create more hierarchy

SCREENSHOT

You can also use the *Page Menu* to quickly perform tasks like creating new pages or duplicating an existing page.

SCREENSHOW

You can create as many pages as you need with any level of hierarchy using these tools, but in case you haven't noticed, right now there's no way to navigate between the pages we created. Next we'll look at how we can connect these pages to each other by creating navigation.
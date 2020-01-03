---
title: Creating Pages
layout: tutorial
---

# Creating Pages

You can create the most amazing content in history, but if it doesn't have a page to be displayed on, then no one will ever know. So before we create any content, let's create the pages and necessary site structure.

There are three main steps for creating a new page:

1. Create the template for the new page.

2. Register the new page template in `app.js`

3. Add the page through the ApostropheCMS user interface.

To create new pages and page templates, you'll use the `apostrophe-pages` and `apostrophe-templates` modules. You'll also learn the basics of using [Nunjucks](https://mozilla.github.io/nunjucks/).

```
project
│   README.md
│   file001.txt    
│
└───folder1
│   │   file011.txt
│   │   file012.txt
│   │
│   └───subfolder1
│       │   file111.txt
│       │   file112.txt
│       │   ...
│   
└───folder2
    │   file021.txt
    │   file022.txt
```


## Page Creation Overview

Before we go through an example where you create your own page templates and pages, let's look in more detail at the concepts behind each step of the process.

### Step 1: Creating Page Templates

First, create the template. A page template in Apostrophe is composed of HTML and JavaScript extended with Nunjucks.

Templates are found here: 

DIRECTORY STRUCTURE DIAGRAM
`lib/modules/apostrophe-pages/views/`.

`apostrophe-pages` module directory of the project \(`lib/modules/apostrophe-pages`\). Here you'll find a `/views` directory containing our `.html` template files. Within the `/pages` subdirectory by default, you'll just find the Home page template, `home.html`.

In addition to the `home.html` template in `lib/modules/apostrophe-pages`, projects created with our CLI from the `apostrophe-boilerplate` project ship with a simple `layout.html` file in the top-level `views/` folder. Templates  that are not from a specific module are found in `views/`.

END DIRECTORY STRUCTURE DIAGRAM

The `layout.html` template provides the unpinning for all of our templates. Before you create a new template, open `layout.html`:

{% code-tabs %}
{% code-tabs-item title="views/layout.html" %}
```markup
{% block beforeMain %}
  {#
    We recommend you use the beforeMain block for global page components
    like headers or navigation.
  #}
{% endblock %}

{% block main %}
  {#
    Usually, your page templates in the apostrophe-pages module will override
    this block. It is safe to assume this is where your page-specific content
    should go.
  #}
{% endblock %}

{% block afterMain %}
  {#
    This would be a great place to put a global footer.
  #}
{% endblock %}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

As a rule, you should extend the `layout.html` or create your own base templates using a similar pattern. This provides us with several **blocks** to use. Blocks define the layout areas of the page so that we can easily define where everything should render. For our example, we'll start with the `blocks` defined in `layout.html` and override them in a separate templates.

{% hint style='info' %}
While `layout.html` is the template that you'll extend, there's a long line of templates being extended under the hood. For a typical page load, `layout.html` extends `outerLayout.html` (from `lib/modules/apostrophe-templates/views`). Then `outLayout.html` extends `outerLayoutBase.html`. Most of the time you won't need to look there, but there are cases, like adding a `link` to the `head`, where you might need to override a block from one of the base templates that isn't found in `layout.html`.
{% endhint %}

Two blocks to take note of are `title` and `main`. You extend the `title` block to set the page title, and put page content in the `main` block. If you don't set a title, Nunjucks will set a page title based on the content of your page.

### Step 2: Configuring Apostrophe with `app.js` 

In order for a new page template to load, you must add it to `app.js`. When you add a new page template to `app.js`, remember, you're not creating a new page: you're registering a page template that can be used to create new pages.

### Step 3: Creating a Page

Finally, once you have created a page template, and registered it in `app.js` you can create new pages based on it. To do this, you'll need to step away from your text editor and command line for a moment and use Apostrophe's administrative interface to create new pages.

## Example: Creating a New Page Template and Page 

Now create a new page template named `default.html` that extends `layout.html`, register the template, and use it to create a new page.

### Create the Template

First create the page template:

1. Create new HTML file in `lib/modules/apostrophe-pages/views/pages/` named `default.html`.

2. At the top of the file, extend `layout.html`
    ```markup
    {% extends "layout.html" %}
    ```
3. Within the file create the different blocks that you will need.

    ```markup
    {% block main %}
    ...
    {% endblock %}
    ```

    In this example, we only created a main block, since often you'll want to use content inherited from the main template in `beforeMain` and `afterMain`. The main block would be filled with various widgets used to display and create content. But let's not get ahead of ourselves.

Now that you have a `default.html` file, you need to register it in `app.js` to make it available.
 
### Add the Page Template to `app.js`

1. Open `app.js`.

2. Add the registration for your new page template to `app.js`:

{% code-tabs %}
{% code-tabs-item title="app.js" %}
    ```javascript
        // This configures our default page template
        'apostrophe-pages': {
          types: [
            //This is the new section
            {
              name: 'default',
              label: 'Default'
            },
            //The rest is what what was there already
            {
              name: 'home',
              label: 'Home'
            },
          ]
        },
    ```
{% endcode-tabs-item %}
{% endcode-tabs %}

### Create a Page from the New Template

Now that you created the template, use it to create a new page.

1. Log in to Apostrophe.

2. Open the main menu and select *Pages*.

3. Click *New Page*.

4. Enter a Title, a friendly URL, and select your new template under Type.

![Creating a new page](/.gitbook/assets/create_new_page2.png)


Nice work! You created a new page template, and now you can create as many pages as you like from that template. 

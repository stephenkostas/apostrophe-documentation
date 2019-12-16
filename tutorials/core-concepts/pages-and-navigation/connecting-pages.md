# Building a Site Hierarchy

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

## Connecting Pages

If you've been poking around (or following these tutorials) you may have noticed that, out of the box, Apostrophe does not provide an easy way to navigate between pages. Instead we provide the tools for you to build yourself the navigation of your dreams. To build your navigation, you'll create dynamic links in your templates using the concept that all pages are connected by being children of the home page.

## Example: Linking to the home page

The first and most important link you need is the home page. Most of the time, you'll have a pretty good guess at what your home URL is, however, when you link to it on your site, you want to ask Apostrophe rather than hardcode the URL.

You can add this code to your page template's header to create a universal link back to the home page:

```markup
<h2><a href="{{ data.home._url }}">{{ data.home.title }}</a></h2>
```

{% hint style='info' %}
**"Why** `_url`**?"** The URL isn't a permanent part of the page object that lives in the database. Anything that is dynamically set and not part of the database starts with `_`, so it doesn't get accidentally stored back to mongodb.
{% endhint %}

## Example: "Tab" Navigation

A common navigation design is to have a row of "tabs" at the top. In this example, you'll link to the subpages of the home page to create your tabs.

First, add this to your `home.html`:

{% code-tabs %}
{% code-tabs-item title="lib/modules/apostrophe-pages/views/home.html" %}
```markup
<ul class="tabs">
  {% for tab in data.home._children %}
    <li><a href="{{ tab._url }}">{{ tab.title }}</a></li>
  {% endfor %}
</ul>
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Now let's add a CSS class indicating the current tab \(the one that is the current page, or an ancestor of it\):

{% code-tabs %}
{% code-tabs-item title="lib/modules/apostrophe-pages/views/home.html" %}
```markup
<ul class="tabs">
  {% for tab in data.home._children %}
    <li class="
      {% if data.page and
        (apos.pages.isAncestorOf(tab, data.page) or tab._id == data.page._id)
      %}
        current
      {% endif %}
    "><a href="{{ tab._url }}">{{ tab.title }}</a></li>
  {% endfor %}
</ul>
```
{% endcode-tabs-item %}
{% endcode-tabs %}

SCREENSHOT

## Example: Dropdown menus

Sometimes you'll want to display dropdown menus. Each menu represents a child of the home page, and each item on each menu represents a child of _that_ page.

First, in `app.js`, let's configure `apostrophe-pages` to retrieve two levels of children when fetching ancestors of the current page:

{% code-tabs %}
{% code-tabs-item title="app.js" %}
```javascript
modules: {

  // ... other configuration ...

  'apostrophe-pages': {
    filters: {
      // Grab our ancestor pages, with two levels of subpages
      ancestors: {
        children: {
          depth: 2
        }
      },
      // We usually want children of the current page, too
      children: true
    }
    // other apostrophe-pages options like `types` ...
  },

  // ... other configuration ...
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Now you can easily output all the markup you'd need for dropdown menus. Add this code to `home.html`:

{% code-tabs %}
{% code-tabs-item title="lib/modules/apostrophe-pages/views/home.html" %}
```markup
<ul class="tabs">
  {% for tab in data.home._children %}
    <li><a href="{{ tab._url }}">{{ tab.title }}</a>
      {% if tab._children.length %}
        <ul>
          {% for child in tab._children %}
            <li><a href="{{ child._url }}">{{ child.title }}</a></li>
          {% endfor %}
        </ul>
      {% endif %}
    </li>
  {% endfor %}
</ul>
```
{% endcode-tabs-item %}
{% endcode-tabs %}

SCREENSHOT

## Example: Breadcrumb trails

The current page is `data.page`, and by default, `data.page._ancestors` is available. This will add a breadcrumb link to the page:

{% code-tabs %}
{% code-tabs-item title="lib/modules/apostrophe-pages/views/home.html" %}
```markup
{% if data.page %}
  <ul class="breadcrumbs">
    {% for ancestor in data.page._ancestors %}
      <li><a href="{{ ancestor._url }}">{{ ancestor.title }}</a></li>
    {% endfor %}
  </ul>
{% endif %}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% hint style='info' %}
**Always check whether** `data.page` **exists** when using it in a layout template that might also be extended by `login.html`, `notFound.html` and other places where there is no CMS "page."
{% endhint %}

## Example: "Accordion" navigation

Want to list the ancestors of the current page along with their subpages? Sure. You can replace the previous example with this "accordion" nav:

{% code-tabs %}
{% code-tabs-item title="lib/modules/apostrophe-pages/views/home.html" %}
```markup
{% if data.page %}
  <ul class="accordion">
    {% for ancestor in data.page._ancestors %}
      <li><a href="{{ ancestor._url }}">{{ ancestor.title }}</a>
        {% if ancestor._children.length %}
          <ul>
            {% for child in ancestor._children %}
              <li><a href="{{ child._url }}">{{ child.title }}</a></li>
            {% endfor %}
          </ul>
        {% endif %}
      </li>
    {% endfor %}
  </ul>
{% endif %}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

SCREENSHOT

## Example: Display Children of the current page

To display children of the current page in the navigation, use a block like this:

{% code-tabs %}
{% code-tabs-item title="lib/modules/apostrophe-pages/views/home.html" %}
```markup
{% if data.page %}
  <ul class="children">
    {% for child in data.page._children %}
      <li><a href="{{ child._url }}">{{ child.title }}</a></li>
    {% endfor %}
  </ul>
{% endif %}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

## Custom navigation

This style navigation is hardcoded into your template, and you would need to be manually recreate (or copypasta) the navigation for any new templates you create. This might sound bad, but don't worry, we can take the same templating principles that we learned here and use them to create a [custom navigation widget](../editable-content-on-pages/README.md).


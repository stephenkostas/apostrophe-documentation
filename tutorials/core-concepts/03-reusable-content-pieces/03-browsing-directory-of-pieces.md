---
title: Browsing a Directory of Pieces
layout: tutorial
---

# Browsing a Directory of Pieces

Widgets are not the only way to display pieces. They are designed for displaying a few people here and there in the context of other pages.

## Displaying a directory of people on a page with `apostrophe-pieces-pages`

For a really useful directory, we'll want to create a page that offers a paginated list of people, with the ability to filter by name. We can even create lots of those pages, "locked down" to display people with certain tags.

Let's add this new module to our `app.js`. Our new module extends the `apostrophe-pieces-pages` module:

```javascript
modules: {
  // ... other modules ...,
  'people-pages': {
    extend: 'apostrophe-pieces-pages'
  }
}
```

{% hint style="info" %}
`people-pages` will automatically figure out that its job is to display the pieces that come from the `people` module, by removing `-pages` from its name. If you don't want to follow that pattern, you'll have to set the `piecesModuleName` option, and possibly also set the `name` option to a sensible name for the page type that displays an index of pieces. We usually just follow the pattern.
{% endhint %}

This module provides a new type of page on the site, `people-page`. This new page type displays an index of pieces. Before it can be used, we need to configure `apostrophe-pages` to add it to the menu of page types that can be given to pages. In `app.js` it might look like this:

```javascript
modules: {
  // ... other modules ...,
  'apostrophe-pages': {
    types: [
      // Ordinary page types
      {
        name: 'default',
        label: 'Default'
      },
      {
        name: 'home',
        label: 'Home'
      },
      // Our new page type for displaying people
      {
        name: 'people-page',
        label: 'People'
      }
    ]
  }
}
```

{% hint style="info" %}
As with other modules, it's a good idea to create `lib/modules/apostrophe-pages/index.js` and move configuration there to reduce clutter.
{% endhint %}

### Creating custom templates for our index of people

If you add a `People` page now, you'll see a plain-vanilla list of people; clicking the name of each takes you to a page that displays... just their name. Not very exciting. Let's dress it up.

First make a folder for our templates:

```bash
mkdir -p lib/modules/people-pages/views
```

Now create `index.html` in that folder. You can copy what's already in `node_modules/apostrophe/lib/modules/apostrophe-pieces-pages/views/index.html` as a starting point:

```markup
{% extends "layout.html" %}
{% block title %}{{ data.page.title }}{% endblock %}

{% block main %}
  {% for piece in data.pieces %}
    <h4><a href="{{ piece._url }}">{{ piece.title }}</a></h4>
  {% endfor %}
  {# YES, YOU NEED PAGINATION. You could set the `perPage` option to a large
     number, but eventually you will have performance issues. However, also
     see the discussion of "Load More" and infinite scroll solutions later in this
     tutorial. #}
  {% import 'apostrophe-pager:macros.html' as pager with context %}
  {{ pager.render({ page: data.currentPage, total: data.totalPages }, data.url) }}
{% endblock %}
```

First off, by now you probably have a `layout.html` template of your own that you extend in all of your page templates. So we can switch to extending that:

```markup
{% extends "layout.html" %}
```

Next, let's dress up the people with their headshots by replacing the existing `for` loop with the following:

```markup
  {% for piece in data.pieces %}
    <h4>
      {% set image = apos.images.first(piece.thumbnail) %}
      {% if image %}
        <img src="{{ apos.attachments.url(image, { size: 'one-sixth' }) }}" />
      {% endif %}
      <a href="{{ piece._url }}">{{ piece.title }}</a>
    </h4>
  {% endfor %}
```

Add a little CSS and you've got a nice directory.

{% hint style="info" %}
_We could have used_ `apos.singleton` _to display the thumbnail,_ but sometimes you don't want the extra markup, editing controls, et cetera.

The `apos.images.first` helper can find an image, also known as an "attachment," anywhere in the object we pass to it. You can pass the entire `piece` object if you want to. But, it's slower than than going straight to the thumbnail.

You can also use `apos.images.all` to fetch an array of attachments.

**Never assume the editor remembered to pick a thumbnail.** Always use an `if` statement to check.

**Always specify the size.** Loading a large version of the image for an index view like this just slows down your site. See [adding editable content to pages](adding-editable-content-to-pages.md) for more information about image sizes.
{% endhint %}

### Creating custom templates for individual people

Next we'll want to override the `show.html` template of our subclass of `apostrophe-pieces` as well. The default version is very bare-bones, just enough to demonstrate the idea.

`show.html` is the template that displays just one profile in detail:

```markup
{# in lib/modules/people-pages/views/show.html #}
{% extends "layout.html" %}
{% block title %}{{ data.piece.title }}{% endblock %}

{% block main %}
<h2>{{ data.piece.title }}
{{ apos.singleton(data.piece, 'thumbnail', 'apostrophe-images') }}
</h2>

{{ apos.area(data.piece, 'body') }}
{% endblock %}
```
{% hint style="info" %}
_We didn't pass an options object to_ `apos.area` _or_ `apos.singleton` _because we already specified the options in the_ [_schema_](schema-guide.md)_,_ as part of `addFields`. If you _do_ pass an options object to `apos.area` or `apos.singleton`, the original options object passed to the [schema](schema-guide.md) is ignored, so be sure to repeat anything that is relevant.
{% endhint %}

**For SEO reasons, it is almost always important to have a good `show.html` page and provide `_url` links to reach it in your index pages, even if you are displaying most of the information in the index pages as well.**

### Contextual pieces: editing pieces "on the page"

Editing each person's "bio" inside the modal dialog box is okay, but it would be a lot nicer to edit it on the actual page. Indeed, we already can... but only if we deliberately head to the page.

We can make this more intuitive by setting the `contextual` flag for our pieces module:

```javascript
// in lib/modules/people/index.js
module.exports = {
  // Other options, then...
  contextual: true
};
```

When we do this, _the user is automatically redirected to the_ `show.html` _page for each person as soon as they create and save it._

Note that this only makes sense if you are using `apostrophe-pieces-pages`.

Now that we've made this choice, we might want to switch to calling `apos.singleton` in the page for the profile photo as well.

We can also set `contextual: true` on each of the `body` and `thumbnail` [schema](schema-guide.md) fields so that they don't show up in the modal dialog box but are instead only editable on the page. Whether to do that is up to you, but it does help reinforce the idea that you edit this content on the page.

## Disabling a Pieces Index Page

Occasionally you might want pieces that have their own show pages, but you don't need the index page. One case that's come up a few times is that pieces are listed in widgets across the site based on various criteria, but the organization doesn't have need for a central listing.

Even if you don't create an `index.html` in your piece's pages module, one will exist by default anyway, likely looking completely unstyled. Bummer.

For example, let's say we have a `cats` piece type. In my `cats-pages` module (extending `apostrophe-pieces-pages`), my `views` directory has a single `show.html` template file for the cat profile pages. So Oscar the cat can have his page at `example.com/cats/oscar`. If someone decides to try visiting `example.com/cats` we don't want that page to exist, but it will be there, listing cats, probably not looking that great.

The solution is pretty simple! To get `example.com/cats` to return a 404 error (effectively no longer existing), we'll make a small addition to the [`beforeIndex`](../../modules/apostrophe-pieces-pages/README.md#beforeindex-req-callback) method.

In `lib/modules/cats-pages/index.js`, add the following to your `construct` method:

```javascript
construct: function (self, options) {
  self.beforeIndex = function (req, callback) {
    req.notFound = true;

    return callback(null);
  };
}
```

See what we did there? Since `beforeIndex` runs before the index page is loaded, and by setting the request's `notFound` property to `true`, it'll return a 404 error rather than loading the page. This is the case even for site admins, so make sure the `apostrophe-pages` configuration doesn't give admins an option to create those pieces index pages.

You might want to allow site admins to create and access those index pages, though. In that case, do [include it as an option in `apostrophe-pages` configuration](../../modules/apostrophe-pages/README.md), and make an adjustment to the `beforeIndex` method:

```javascript
construct: function (self, options) {
  self.beforeIndex = function (req, callback) {
    if (!req.data.page._edit) {
      req.notFound = true;
    }

    return callback(null);
  };
}
```

With the `!req.data.page._edit` conditional we're allowing people with edit permissions to access the pages.


---
title: "\"Next\" and \"previous\" links for pieces"
layout: tutorial
---

Sometimes it's desirable to provide links to the "next" and "previous" piece on the "show page" for that piece (the `show.html` template).

To enable that, just configure your module that subclasses `apostrophe-pieces-pages` appropriately:

```
// in my-articles-pages/index.js

module.exports = {
  extend: 'apostrophe-pieces-pages',
  next: true,
  previous: true
  // other options, etc.
};
```

> Here we'e assuming that `my-articles` extends `apostrophe-pieces` directly, and `my-articles-pages` extends `apostrophe-pieces-pages`, but you can also do this trick with modules that extend `apostrophe-blog-pages`, `apostrophe-events-pages` and other existing subclasses.

Turning on these options causes Apostrophe to load the next and previous documents into `data.previous` and `data.next`, so you can output links like this, often at the bottom of `show.html`:

```markup
{% if data.previous %}
  <h4>
    <a href="{{ data.previous._url }}">Back to {{ data.previous.title }}</a>
  </h4>
{% endif %}

{% if data.next %}
  <h4>
    <a href="{{ data.next._url }}">Next: {{ data.next.title }}</a>
  </h4>
{% endif %}
```

By default you can access any property of `data.previous` or `data.next`. For better performance, you may want to limit them with a projection, much as you would for a join:

```javascript
// in my-articles-pages/index.js

module.exports = {
  extend: 'apostrophe-pieces-pages',
  previous: {
    projection: {
      title: 1,
      slug: 1,
      tags: 1,
      type: 1
    }
  },
  next: {
    projection: {
      title: 1,
      slug: 1,
      tags: 1,
      type: 1
    }
  }
  // other options, etc.
};
```

> These are the minimum fields recommended. Except for `title`, all of them play a role in building the dynamic `_url` property correctly.
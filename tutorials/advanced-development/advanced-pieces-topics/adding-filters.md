
# Filtering Pieces: Enhanced Browsing for Pieces

The connections you create between content using [joins](/tutorials/core-concepts/reusable-content-pieces/joins.md) can be used, among other things, to filter content based on those connections. Continuing the [`people` piece example](/tutorials/core-concepts/reusable-content-pieces/README.md) we'll create filters for the list of people, taking advantage of joins, tags and other field types for the public facing site.

To make it easier to browse a listing of pieces, the [apostrophe-pieces-pages](/modules/apostrophe-pieces-pages/README.md) module will *automatically permit you to filter by the value of most schema fields when submitted as query string parameters*, provided they are marked for this purpose as you'll see below.

{% hint style="info" %}
You can also use `q` or `search` as a query parameter to do a full-text search. *Tip:* often this is all users want.
{% endhint %}

Next we'll explore how to add a filter by tag. Later, we'll look at filtering by a join as well.

Add this code to `lib/modules/people-pages/index.js`. Note that earlier you added this module to `app.js`, extending `apostrophe-pieces-pages`. Now you need to add some custom configuration:

{% code-tabs %}
{% code-tabs-item title="lib/modules/people-pages/index.js" %}
```javascript
module.exports = {
  // We already set the "extend" option in app.js, or we'd need it here
  // Specify the schema fields we want to be able to filter by
  piecesFilters: [
    {
      name: 'tags'
    }
  ]
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

Here you're asking `apostrophe-pieces-pages` to automatically populate `req.data.piecesFilters.tags` with an array of choices. And, you're also asking that `tags` be accepted via the query string in the URL (for example, `/people?tags=doctors`).

Now you can take advantage of that:

{% code-tabs %}
{% code-tabs-item title="lib/modules/people-pages/index.html" %}
```markup
{# Link to all the tags, adding a parameter to the query string #}
<ul class="tag-filters">
  {% for tag in data.piecesFilters.tags %}
    <li><a href="{{ data.url | build({ tags: tag.value }) }}">{{ tag.label }}</a></li>
  {% endfor %}
</ul>
```
{% endcode-tabs-item %}
{% endcode-tabs %}


{% hint style="info" %}
**"What's going on in this code?"** On a pieces index page, `data.url` always contains the current URL. We want to add a `tags` parameter to the query string. Apostrophe's `build` filter merges new query parameters with the URL. We can also remove a query parameter by passing the empty string as its value.

Notice that there are separate `value` and `label` properties for each tag, even though they are the same. This pattern is used consistently for all fields we define filters for, including fields like joins or select fields where the value and the label can be quite different. This lets you write a single macro to handle many filters.
{% endhint %}

### Displaying counts for tags

You can display counts for the choices, so users know how many items are available with a given tag. 

1. Add the `counts: true` property to the `piecesFilters` in `index.js`

2. Add `({{ tag.count }})` inside of the `href` tag in `index.html`.


{% code-tabs %}
{% code-tabs-item title="lib/modules/people-pages/index.js" %}
```javascript
module.exports = {
  piecesFilters: [
    {
      name: 'tags',
      counts: true
    }
  ]
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% code-tabs %}
{% code-tabs-item title="lib/modules/people-pages/index.html" %}
```markup
<ul class="tag-filters">
  {% for tag in data.piecesFilters.tags %}
    <li><a href="{{ data.url | build({ tags: tag.value }) }}">{{ tag.label }} ({{ tag.count }})</a></li>
  {% endfor %}
</ul>
```
{% endcode-tabs-item %}
{% endcode-tabs %}

### Showing the current state of the filter

Usually you want to indicate the tag the user has already chosen. And, you want a way to remove the filter and to see the full results.

How can we do that? Again, in `index.html`:

{% code-tabs %}
{% code-tabs-item title="lib/modules/people-pages/index.html" %}
```markup
{# Link to all the tags, adding a parameter to the query string #}
<ul class="tag-filters">
  {% for tag in data.piecesFilters.tags %}
    <li>
      {% if data.query.tags == tag.value %}
        <a href="{{ data.url | build({ tags: '' }) }}" class="current">
      {% else %}
        <a href="{{ data.url | build({ tags: tag.value }) }}">
      {% endif %}
        {{ tag.label }}
      </a>
    </li>
  {% endfor %}
</ul>
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% hint style="info" %}
**"What's going on in this code?"** The current query string is automatically unpacked to `data.query` for you as an object. So just compare `data.query.tags` to the value of each of the choices.

We add a `current` CSS class to the link to remove the current filter. It's up to you to style that; for instance, you might use an `::after` pseudo-element to add an "x."
{% endhint %}

### Filtering on joins and other schema field types

Tags are the simplest example, but you can filter on most schema field types, notably including [`select`](../../schema-guide/schema-guide.md#select) fields and [`joinByOne`](/tutorials/schema-guide/schema-guide.md#joinByOne) or [`joinByArray`](/tutorials/schema-guide/schema-guide.md#joinByArray) fields.

Add a filter on the `_jobs` schema field we saw earlier:

{% code-tabs %}
{% code-tabs-item title="lib/modules/people-pages/index.js" %}
```javascript
module.exports = {
  piecesFilters: [
    {
      name: 'jobs'
    }
  ]
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% hint style="info" %}
"Why is the filter named `jobs`, even though the field is named `_jobs`?" It works like this: if we specify `_jobs` for the filter, then the value in the query string will be the `_id` property of the job. This works, and it is stable no matter what gets edited later. But it isn't pretty. If we remove the `_` from the filter name, the value in the query string will be the *slug* of the job, which is more user-friendly and good for SEO.

Because Apostrophe records past URLs used to access each page and issues 'soft redirects,' broken links will be rare even if slugs change. So we recommend using `jobs` rather than `_jobs`.
{% endhint %}

Now you can filter people by job:

{% code-tabs %}
{% code-tabs-item title="lib/modules/people-pages/index.html" %}
```markup
{# Link to all the tags, adding a parameter to the query string #}
<ul class="job-filters">
  {% for job in data.piecesFilters.jobs %}
    <li><a href="{{ data.url | build({ jobs: job.value }) }}">{{ job.label }}</a></li>
  {% endfor %}
</ul>
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% hint style="info" %}
Notice that this template looks exactly like the one for tags. That's intentional. You could use a single Nunjucks macro for both.
{% endhint %}

### Filtering on multiple values

You're not restricted to filtering on a single value for a join, or for a `tags` field. If the query string looks like this:

```
?jobs[]=anointer&jobs[]=flosser
```

You'll see people with *either* job.

{% hint style="info" %}
If you want to be more restrictive and only display results that have *all* of the specified values, add `And` to the filter name&mdash;in both the `piecesFilters` array (in the module's `index.js`) and the template references. As before, you can do this with `_` for `_id`, or without for `slug`.For instance, `_jobsAnd` expects ids, while `jobsAnd` expects slugs.

However, **keep in mind this usually is very frustrating for users because they will rarely get any matches.** We recommend the default "or" behavior.
{% endhint %}

Here's how to build query strings that contain arrays in your template:

{% code-tabs %}
{% code-tabs-item title="lib/modules/people-pages/index.html" %}
```markup
<ul class="job-filters">
  {% for job in data.piecesFilters.jobs %}
    {% if apos.utils.contains(data.query.jobs, job.value) %}
      <a href="{{ data.url | build({ jobs: { $pull: job.value } }) }}" class="current">
    {% else %}
      <a href="{{ data.url | build({ jobs: { $addToSet: job.value } }) }}">
    {% endif %}
      {{ job.label }}
    </a>
  {% endfor %}
</ul>
```
{% endcode-tabs-item %}
{% endcode-tabs %}

{% hint style="info" %}
**"What's going on in this code?"** Like before, we are using the `build` filter to add and remove query parameters. However, this time, we are using the special `$pull` operator to remove a job from the array without removing the others, and using the special `$addToSet` operator to add a job to the array. In this way, we can manage filter URLs like `/people?jobs[]=doctor&jobs[]=technician` with very little effort.
{% endhint %}

## Next Steps

If you want to try out some other uses of filters, you can see how to [integrate them with Apostrophe's site management interface](adding-columns.md), or you can continue on to learn how to use AJAX to avoid full page refreshes when filters are used.

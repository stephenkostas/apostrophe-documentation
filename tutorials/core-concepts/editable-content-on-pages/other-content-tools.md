# Other Content Tools

Attachments and Tags are two concepts that work in conjunction with various widgets and other content options.

## Attachments

There are two types of content that you can display in Apostrophe: content that you created in the CMS, like Pieces; and content that you created outside of the CMS and uploaded to Apostrophe. The various types of content that you can create in Apostrophe are collectively known as ["Docs"](/tutorials/core-concepts/technical-overview.md), and content from outside of Apostrophe that you uploaded are called "Attachments".

{% hint style='info' %}
### Attachments and Assets

Depending on your familiarity with other CMS products, you may have seen "asset" used to describe what Apostrophe calls an "attachment". In Apostrophe, **Attachments** are any file that you upload into the CMS to use as content or provide a download. **Assets** are the CSS and JS stylesheets that you connect to Apostrophe through the file system. It will be important to recognize the difference as you continue through the Apostrophe tutorials and reference material.

{% endhint %}


## Uploading Attachments

Attachments are uploaded in two ways: through the [Files Widget](/modules/apostrophe-files) included with Apostrophe or through the [`attachments`](/other/fields/attachments.md) field in a custom schema. The Files widget can be accessed by adding it to a page, or through the admin bar.

To learn more about using the `apostrophe-files` you can [its section in the standard widgets documentation](/tutorials/core-concepts/editable-content-on-pages/standard-widgets.md#apostrophe-files).

To add files through the admin bar,

1. Open the admin bar and select *Files*.

2. To add more files, click  *Add Files* in the top left, and select files from your local files system to upload.

3. To delete files, check the box next to each file you want to delete and then click *Batch Trash*.

![Adding and managing files](/.gitbook/assets/attachments-manage.png)

## Tags

Tags are a common CMS feature which are used to fluidly connect content. In Apostrophe, tags can applied to all content types and connect them across your site. Many of the widgets bundled with Apostrophe support creating content with tags, and you can also [use tags when you create your own widgets](/other/fields/tags.md).

## Creating Tags

You can create a list of base tags that writers can apply to content through tags administration, or just leave it up to them to create their own tags. To limit user created tags, you can configure the [module settings](/modules/apostrophe-tags). The default configuration allows for user created tags.

To add a tag through the admin bar,

1. Open the admin bar and select *Tags*

2. Enter text in the "Find or add a tag" field. If the tag already exists, you can see it in the list, if not you can add it.

    ![Adding a tag](/.gitbook/assets/tags-add.png)


## Managing Tags

All tags can be managed from the Manage Tags page, whether they were created by a regular user or by an administrator. Deleting a tag will remove it from any associated content.

* Click the pencil icon to edit an existing tag.

![Editing a tag](/.gitbook/assets/tags-edit.png)

* Click the trash icon to delete a tag.

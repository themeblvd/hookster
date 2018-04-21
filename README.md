# Hookster

Hookster is a tool to automatically extract data about the actions and filters of your WordPress theme or plugin. You can use this data in any way you like... For example, you might use the data to create a developer documentation website.

Here's the gist of how this project works &mdash; Drop your WordPress theme or plugin files into the `src` directory, run `npm run build` in your terminal, and get back the files `actions.json` and `filters.json` with data about your hooks.

## About This Project

The motivation here was to generate the data needed to create a developer documentation website for the [Jump Start](https://wpjumpstart.com) WordPress theme. A reference of all of the available actions and filters has been a popular request from child theme developers for years. However, with a growing library of 500+ hooks, manually creating and maintaining this would be a nightmare.

So this project aims to automate the process of generating this data. Hopefully this tool can be used not only for Jump Start, but for your themes and plugins, as well.

## Prerequisites

First things first, you must have Node and NPM installed on your computer.

<http://blog.teamtreehouse.com/install-node-js-npm-mac>

Next, let's talk about how the code in your WordPress theme or plugin needs to be set up to work with Hookster.

Hookster works by going through the [DocBlocks](http://docs.phpdoc.org/guides/docblocks.html) found across your theme or plugin's PHP files. The name of your hook is cleverly extracted from the PHP code immediately following each docBlock, and then information about the hook is taken from the docBlock, itself.

So in order for the data to be generated properly, your PHP code needs to follow (WordPress's PHP Documentation Standards)[https://make.wordpress.org/core/handbook/best-practices/inline-documentation-standards/php/#4-hooks-actions-and-filters].

For example:

``` php
/**
 * Summary.
 *
 * Description.
 *
 * @since x.x.x
 *
 * @param string $foo Description.
 * @param bool   $bar Description.
 */
do_action( 'my_plugin_do_something', $foo, $bar );

/**
 * Summary.
 *
 * Description.
 *
 * @since x.x.x
 *
 * @param string $foo Description.
 * @param bool   $bar Description.
 */
apply_filters( 'my_plugin_modify_something', $foo, $bar );
```

*Note: Make sure all of your action and filter names are prefixed with your namespace, like `<namespace>_<hook name>`. In the above examples, the namespace would be `my_plugin`.*

## Usage Instructions

1. Clone this repository to your local computer, navigate to the project in your terminal, and run `npm install` to install the required Node packages.
2. Open the project's `package.json`, find the namespace parameter, and change it to your theme or plugin's namespace. The namespace should be what you prefix all of your action and filter names with. Examples: `themename`, `pluginname`, `theme_name`, `plugin_name`, etc.
3. Drop all of the files of your theme or plugin into the project's `src` directory.
4. Run the command `npm run build` in your terminal. After it's finished, you'll be able to find the files `actions.json` and `filters.json` in your project's `dist` directory.

## About the Data Generated

The data generated within your `actions.json` and `filters.json` files will include an object for each hook with the following data.

* `name`: *{String}* Name of the hook, like `my_plugin_do_something`.
* `summary`: *{String}* First paragraph of the docBlock.
* `desc`: *{String}* Remaining paragraphs of the description, after the summary. Paragraphs are separated with `\n\n`.
* `since`: *{String}* Version number the hook was added to your theme or plugin, pulled from the `@since` tag of the docBlock.
* `params`: *{Object}* Information about parameters passed to the hook.
* `file`: *{String}* File in your plugin or theme, where the hook exists.

Here's an example of what the raw JSON data will look like:

``` json
...
{
  "name": "themeblvd_slider_auto_args",
  "summary": "Filters the query arguments passed to WP_Query for a post slider.",
  "desc": "This filter exists for backwards compatibility only and is now deprecated. Use `themeblvd_posts_args` filter instead.",
  "since": "2.0.0",
  "params": [
    {
      "name": "$query_args",
      "type": "array",
      "description": "Query arguments passed to WP_Query."
    },
    {
      "name": "$args",
      "type": "array",
      "description": "Original arguments for post slider; see docs for themeblvd_get_post_slider()."
    }
  ],
  "file": "framework/blocks/loop.php"
},
{
  "name": "themeblvd_icon_browser_value",
  "summary": "Filters the value to be inserted for an icon in the icon browser.",
  "desc": "By default, this value will be structured with a Font Awesome style class and icon class, like `fas fa-user`.",
  "since": "2.7.4",
  "params": [
    {
      "name": "$icon_value",
      "type": "string",
      "description": "Icon value."
    },
    {
      "name": "$icon",
      "type": "string",
      "description": "Icon name."
    },
    {
      "name": "$prefix",
      "type": "string",
      "description": "Style class, like <code>fas</code>."
    },
    {
      "name": "$type",
      "type": "string",
      "description": "Style type, like <code>solid</code>."
    }
  ],
  "file": "framework/admin/functions/display.php"
},
...
```

## Creator

**Jason Bobich**

* <http://jasonbobich.com>
* <https://twitter.com/jasonbobich>
* <http://themeblvd.com>
* <http://wpjumpstart.com>

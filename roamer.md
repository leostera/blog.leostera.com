# Roamer

## CLI
```
roamer sitename
```
Scaffold the structure of a roamer static site.

```
cd sitename
npm i
```
Get in there and install dependencies.

```
roamer server
```
Starts a local development server or simply run 

```
roamer build 
```
Builds the whole site to be deployed.

## Editing
```
touch sitename/posts/new_post.md
subl sitename/posts/new_post.md
```
Create and open my new post within my posts folder.

Write in plain markdown. Don't be bothered. Posts will be compiled later on with a method configured by the theme.

## Themes
Each `roamer` site has themes. A theme is the frame for your site to be generated within.

A theme can be anything, as long as it can be built by `component` and establishes how the posts should be compiled.

For instance, this angular-based theme specifies that its posts are built as non-bundled html files for it to lazyLoad:

```
// ./sitename/themes/angular-sample/config.js

var buildPost = module.exports = function (post) { 
  return {
    bundle: false,
    content: post,
    format: 'html'
  };
};
```

If there is no `config.js` file or there is no `buildPost` function, or `buildPost` returns the post itself (an identity function), then by default posts are bundled as requirable html-files.

This means that a very minimalistic theme can be built around this default, one that listens to keystrokes for `left` and `right`, that changes the rendered post quite easily and with zero hassle. 

> This is not a good idea thou for sites with a very large amount of posts, due to all the posts contents being bundled. In such a case, lazy loading non-bundled posts will be best.

At the end, posts can be configured with the following options:

```
bundled: should the post be bundled or not with the app – Boolean.
content: what is the content of the post itself – String.
format: what is the format of the post – String.
```

Keep in mind that since content is just a string and your `config.js` file is a regular node module, you can preprocess your posts in any way you like.
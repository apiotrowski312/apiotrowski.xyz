---
title: "EmberData, custom object root"
description: "Ember Data is easier to customize that you think!"
date: 2022-01-08
tags:
- "EmberJS"
- "EmberData"
- "Customize"
draft: false
---

I am learning EmberJS to create a frontend for my newest app - FoodPlanner. As I am going with my app I am finding stuff that I would like to customize in Ember. As Ember is very opinionated it is not easy, however, it is different in EmberData, we can do what we want there, mostly :3.

## How Ember Data Works

I am using RESTSerializer and Adapter. It is really easy to use and it expects understandable requests and responses. However, as always, there is a catch.

In EmberData, models are smart, they can send requests and parse responses by themselves with no need to write additional code.

```JavaScript
this.store.find('posts/1');
```

This function would send GET request to `http://localhost:4200/posts/1` and map response (see below) to `post` model

```json
{
  "posts": {
    "id": 1,
    "title": "I'm Running to Reform the W3C's Tag",
    "author": "Yehuda Katz"
  }
}
```

## Why do I want to customize Object Root

If everything works so smoothly, why would I need to customize anything? Well, because as always, it is never that simple. My application is built with multiple microservices, each microservice has its URL path. E.g. all stuff related to recipes is under `/recipe/SMTH` path.

I have categories under `/recipe/categories` path. I want to have data from microservices separated, so I generated model `ember g model recipe/category`.

So now I can call the store and everything should work just fine, right?

```JavaScript
this.store.find('recipe/categories');
```
```json
{
  "categories": [
      "vegan",
      "vegetarian",
      "meat lover",
      ...
  ]
}
```

No!

```
warn.js:59
WARNING: Encountered "categories" in payload, but no model was found for model name "category"
```

It appears ember expects Object Root to be `recipe/categories` which, at least for me, was not an option.

### Actual modification

Firstly, I generated a serializer for the model:

```
ember g serializer recipe/category
```

Then I modified Object Root:

```JavaScript
export default class RecipeCategorySerializer extends ApplicationSerializer {
  modelNameFromPayloadKey(payloadKey) {
    return super.modelNameFromPayloadKey(
      payloadKey.replace('categories', 'recipe/categories')
    );
  }
}
```

and now everything magically works. You wouldn't believe how long it took me to find out about `modelNameFromPayloadKey`

## Summary

I know there was no rocket science in this article and all of it can be found and read on the EmberJS site. However, for some reason, it took me way too long to find this information. So maybe I am not alone?

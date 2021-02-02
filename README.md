# Assembe - vue.js template

> This is basic vue.js app build for use of Assembe API.

## Acquiring API

1. Go to [assembe.com](https://assembe.com) and register new account.
2. Go to [assembe.com/home#settings](https://assembe.com/home#settings), under App address you will find your API address.
3. Copy that domain and put to `config/assembe.js` under `apiHost` property.

## Schema used in this demo
Resource `topic` (Owned by user)
1. `Text` name
2. `Has many` cards (Related to `card` resource, Column belong to: `topic`)
3. `Text` color

With permissions only for user

Resource `card` (Owned by user)
1. `Text` name
2. `Relation` topic (Related to `topic` resource)
3. `Text with multiline` description
4. `Boolean` finished
5. `Text` color

With permissions only for user


## Build Setup

``` bash
# install dependencies
npm install

# serve with hot reload at localhost:8080
npm run dev

# build for production with minification
npm run build

# build for production and view the bundle analyzer report
npm run build --report
```

For a detailed explanation on how things work, check out the [guide](http://vuejs-templates.github.io/webpack/) and [docs for vue-loader](http://vuejs.github.io/vue-loader).

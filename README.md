# smx-frontend
A short tutorial on how to use the browser

## Using the tag fields
The browser uses tag fields as the main user input fields. There is a dropdown-menu to the right, where you can select the fields you want to show.

When entering tags, you can enter regular tags and operational tags. When searching with a regular tag, the API will return data that matches that single tag. Operational tags return data that corresponds with those tags.

There are five operational symbols to use in the tags:
```
Greater than or equal to:  >=
Less than or equal to:     <=
Greater than:              >
Less than:                 <
Not in:                    !
```
These are put in front of the main value of the tag. E.g. requesting scores with the query `<100` will return scores less than a hundred.
When operational tags are present, you can't use regular tags.




# Setup
This template should help get you started developing with Vue 3 in Vite.

## Recommended IDE Setup

[VSCode](https://code.visualstudio.com/) + [Volar](https://marketplace.visualstudio.com/items?itemName=Vue.volar) (and disable Vetur).

## Type Support for `.vue` Imports in TS

TypeScript cannot handle type information for `.vue` imports by default, so we replace the `tsc` CLI with `vue-tsc` for type checking. In editors, we need [Volar](https://marketplace.visualstudio.com/items?itemName=Vue.volar) to make the TypeScript language service aware of `.vue` types.

## Customize configuration

See [Vite Configuration Reference](https://vite.dev/config/).

## Project Setup

```sh
npm install
```

### Compile and Hot-Reload for Development

```sh
npm run dev
```

### Type-Check, Compile and Minify for Production

```sh
npm run build
```

### Run Unit Tests with [Vitest](https://vitest.dev/)

```sh
npm run test:unit
```

### Lint with [ESLint](https://eslint.org/)

```sh
npm run lint
```

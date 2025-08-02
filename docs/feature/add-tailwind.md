## TailwindCSS install instructions

[TailwindCSS Install Instructions for Angular](https://tailwindcss.com/docs/installation/framework-guides/angular)

> 1. NPM install

`npm install tailwindcss @tailwindcss/postcss postcss --force`

> 2. Add a file to the root of your project named `.postcssrc.json` and add this code to it

```{
  "plugins": {
    "@tailwindcss/postcss": {}
  }
}
```

> 3. Add this line to `./src/styles.css`

`@import "tailwindcss"`

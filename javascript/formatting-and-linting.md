# Formatting and Linting

### Formatting

#### Prettier

```text
npm install --save-dev --save-exact prettier
```

{% embed url="https://prettier.io" caption="" %}

{% embed url="https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode" caption="vscode extension" %}

### Linting

#### XO

```text
npm install --global xo
```

Then add `xo` to your project with \(note, installed globally so no `npx`\)

```text
xo --init
```

{% embed url="https://github.com/xojs/xo" %}

{% embed url="https://marketplace.visualstudio.com/items?itemname=samverschueren.linter-xo" caption="vscode extension" %}

### Configure

Within `package.json` add the following;

{% code-tabs %}
{% code-tabs-item title="package.json" %}
```text
{
  "scripts": {
    "format": "prettier --write '**/*.js' && xo --fix"
  },
  "prettier": {
    "tabWidth": 2,
    "useTabs": false,
    "singleQuote": true,
    "bracketSpacing": false,
    "semi": false,
    "trailingComma": "none"
  },
  "xo": {
    "prettier": true,
    "esnext": false
  }
}
```
{% endcode-tabs-item %}
{% endcode-tabs %}


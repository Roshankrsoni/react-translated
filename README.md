# react-translated

A dead simple way to add complex translations in a React project 🌎🌍🌏

#### Features

* 💥 Data interpolation
* ☄ Component interpolation
* Ⓜ Markdown inline-manipulations
* 🔀 Custom manipulations, pluralizations, and grammar rules based on input-data
* ⚛ Component-level translation files (enables loading only required translations)

#### Example

Write this:

```jsx
<Translate
  text='{difficulty} *translations* in React <ReactLogo>'
  data={{ difficulty: 'Simple' }}
  renderMap={{
    renderReactLogo: () => <ReactLogo size={14} />,
  }}
  />
```

To render this:

![](docs/simple_translations_in_react.png)

#### Support

React DOM and React Native 🔥

#### Try

Play around with the library in your browser through the [CodeSandbox](https://codesandbox.io/s/jzq7zwj5k3).

<br />

## Table of Contents

* [Installation](#installation)
* [Setup](#setup)
* [Usage](#usage)
* [Contributors](#contributors)
* [TODOs](#todos)
* [License](#license)

<br />

## Installation

Whatever floats your boat:

* [Yarn](https://yarnpkg.com): `yarn add react-translated`
* [npm](https://www.npmjs.com): `npm install react-translated`

<br />

## Setup

#### Step 1: Create the translations file

Create a file that will contain a mapping of keys to the string in each language you support.

To keep things simple, use the strings of your default language as the key:

```js
// translation.js

export default {
  'Hi, World!': {
    en: 'Hi, World!',
    fr: 'Bonjour le monde!',
  },
  // ...
}
```

NOTE: There is no enforcement on the key used for a language. In these examples, [2-digit country codes](https://en.wikipedia.org/wiki/ISO_3166-1_alpha-2#Officially_assigned_code_elements) (`en`, `fr`, etc) are used. Decide on a convention and use that for all translations.

#### Step 2: Connect the `Provider`

Wrap your top-level component with the `<Provider>` and set the `translation` and `language` props:

```jsx
// index.js

import { Provider } from 'react-translated'
import translation from './translation'
const App = (
  <Provider language='en' translation={translation}>
    <MyApplicationRoot />
  </Provider>
)
```

NOTE: The value of the `language` prop must be one of the keys used for a language defined in Step 1.

#### Step 3: Start translating

That is all!

Continue reading below to see how to handle the various translation scenarios.

<br />

## Usage

The library can be imported in whatever way you find suitable:

```js
import ReactTranslated from 'react-translated'
import * as ReactTranslated from 'react-translated'

<ReactTranslated.Translate /*...*/ />
```

Or:

```js
import { Provider, Translate } from 'react-translated'

<Translate /*...*/ />
```

### Static text

This is pretty self-explanatory:

```jsx
// translation.js
export default {
  'Hi, World!': {
    en: 'Hi, World!',
    fr: 'Bonjour le monde!',
  },
}

// any component file
<Translate text='Hi, World!' />
```

Renders as:

![](docs/hi_world.png)


### Templated text

To use dynamic text, the text can be templated:

```jsx
// translation.js
export default {
  'Hi, {firstName}!': {
    en: 'Hi, {firstName}!',
    fr: 'Salut {firstName}!',
  },
}

// any component file
<Translate
  text='Hi, {firstName}!'
  data={{ firstName: 'Sergey' }}
  />
```

Renders as:

![](docs/hi_sergey.png)

### Dynamically templated text

Sometimes just dynamic text is not enough and the template itself needs to be dynamic (for example pluralization). That can be achieved using a function call:

```jsx
// translation.js
export default {
  'There are {catsCount} cats in this room.': {
    en({ catsCount }) {
      if (catsCount === 1) {
        return 'There is {catsCount} cat in this room.'
      }
      return 'There are {catsCount} cats in this room.'
    },
    // ...
  },
}

// any component file
<Translate
  text='There are {catsCount} cats in this room.'
  data={{ catsCount: 2 }}
  />
<Translate
  text='There are {catsCount} cats in this room.'
  data={{ catsCount: 1 }}
  />
```

Renders as:

![](docs/cats_in_room.png)

Since these templates are simple function calls, things more complex than pluralization can be done too:

```jsx
// translation.js
export default {
  'This is a {fruit}': {
    en({ fruit }) {
      if (/^[aeiou]/.test(fruit)) {
        return 'This is an {fruit}'
      }
      return 'This is a {fruit}'
    },
    // ...
  },
}

// any component file
<Translate
  text='This is a {fruit}'
  data={{ fruit: 'banana' }}
  />
<Translate
  text='This is a {fruit}'
  data={{ fruit: 'apple' }}
  />
```

Renders as:

![](docs/this_is_a_fruit.png)

### Styled text

The translated text can also have some basic styling applied:

```jsx
// translation.js
export default {
  'Hi, *World*!': {
    en: 'Hi, *World*!',
    fr: 'Bonjour *le monde*!',
  },
}

// any component file
<Translate text='Hi, *World*!' />
```

Renders as:

![](docs/styled_hi_world.png)

And of course the same can be done with dynamic templates:

```jsx
// translation.js
export default {
  'Hi, *{firstName}*!': {
    en: 'Hi, *{firstName}*!',
    fr: 'Salut *{firstName}*!',
  },
}

// any component file
<Translate
  text='Hi, *{firstName}*!'
  data={{ firstName: 'Sergey' }}
  />
```

Renders as:

![](docs/styled_hi_sergey.png)

### Component within text

For more advanced uses where Markdown and Emojis don’t suffice, components can be rendered within the text:

```jsx
// translation.js
export default {
  'Tap the <StarIcon> to add': {
    en: 'Tap the <StarIcon> to add',
    fr: 'Appuyez sur la <StarIcon> pour ajouter',
  },
}

// any component file
<Translate
  text='Tap the <StarIcon> to add!'
  renderMap={{
    renderStarIcon: () => <StarIcon size={14} />
  }}
  />
```

Renders as:

![](docs/tap_the_star.png)

Another practical application of this is nested translations - text that requires data that also needs to be translated:

```jsx
// translation.js
export default {
  'I was born in <MonthName>': {
    en: 'I was born in <MonthName>',
    fr: 'Je suis né en <MonthName>',
  },
  'August': {
    en: 'August',
    fr: 'août',
  },
}

// any component file
const monthName = 'August'
<Translate
  text='I was born in <MonthName>'
  renderMap={{
    renderMonthName: () => <Translate text={monthName} />
  }}
  />
```

Renders as:

![](docs/born_in_august.png)

<br />

## Contributors

<!-- ALL-CONTRIBUTORS-LIST:START - Do not remove or modify this section -->
<!-- prettier-ignore -->
| [<img src="https://avatars3.githubusercontent.com/u/685051?v=4" width="100px;"/><br /><sub><b>amsul</b></sub>](http://amsul.ca)<br />[💻](https://github.com/Amsul/react-translated/commits?author=amsul "Code") [🎨](#design-amsul "Design") [📖](https://github.com/Amsul/react-translated/commits?author=amsul "Documentation") [💡](#example-amsul "Examples") [🔧](#tool-amsul "Tools") |
| :---: |
<!-- ALL-CONTRIBUTORS-LIST:END -->

👋 Interested becoming a contributor too?

Awesome! This project follows the [all-contributors](https://github.com/kentcdodds/all-contributors)
specification. Contributions of any kind are welcome!

You may also want to take a look at our [TODOs](#todos) below and make sure to give our [Contributing](https://github.com/amsul/react-translated/blob/master/CONTRIBUTING.md) guide a read.

<br />

## TODOs

* Add tests using [Jest](https://facebook.github.io/jest/)

<br />

## License

Licensed under [MIT](https://github.com/amsul/react-translated/blob/master/LICENSE).

© 2017 [Amsul](http://twitter.com/amsul_)

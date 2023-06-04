# Svelte Country Select

A very simple and easy to use country select field.

```
npm i svelte-country-select
```

## Usage

```
<script>
import { CountrySelect } from "svelte-country-select";

let value = 'US'

const on_change = () => {}
</script>

<CountrySelect on:change={on_change} value={value} />
```

## Customization

Available CSS Variable overrides

```
--margin-bottom

--label-font-size
--label-color
--label-margin-bottom

--field-gap
--field-border-style
--field-border-width
--field-border-color
--field-padding
--field-border-radius

--field-focus-border-color
--field-border-color-disabled
--field-background-color-disabled
--field-disabled-color
--field-color

--list-background-color
--list-border-radius
--list-border-style
--list-border-width
--list-border-color
--list-box-shadow

--item-color-hover
--item-background-color-hover
```

```
  <CountrySelect label="Select Country" customClass="custom">
    <style>
      .custom {
        --item-color-hover: white;
        --item-background-color-hover: blue;
      }
    </style>
  </CountrySelect>
```

## Props
- customClass - custom class applied for both all elements. used for css vars
- wrapperClass - custom wrapper class
- inputClass - custom input element class
- search_logic - if you wanna customize the underlying search logic. function accepts the following as argument. "(keyword = string, countries= [])"

_Common input element attributes are available such as value, disabled readonly and others_

<script>
  import Highlight from "svelte-highlight/Highlight.svelte";
  import typescript from "svelte-highlight/languages/typescript";

  import CountrySelect from "./../lib/country-select.svelte";

  const code = 'import { CountrySelect } from "svelte-country-select";';

  const cssVariables = `
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
    `;
</script>

<h1>Svelte Country Select</h1>
<p>A very simple and easy to use country select field.</p>

<h2>Properties</h2>
<ul>
  <li>
    <strong>wrapperClass</strong> - custom wrapper class
  </li>
  <li>
    <strong>inputClass</strong> - custom input element class
  </li>
  <li>
    <strong>search_logic</strong> - if you wanna customize the underlying search
    logic. function accepts the following as argument. "(keyword = string, countries
    = [])"
  </li>
</ul>

<i
  >Common input element attributes are available such as value, disabled,
  readonly!</i
>

<h2>CSS Variables</h2>
<Highlight language={typescript} code={cssVariables} />

<h2>How to import</h2>
<Highlight language={typescript} {code} />

<div style="margin-bottom: 50px;" />

<CountrySelect label="Select Country" />

<CountrySelect label="Select Country" disabled />

<CountrySelect label="Select Country" value="PH" />

<div id="custom-select">
  <CountrySelect label="Select Country" customClass="custom">
    <style>
      .custom {
        --item-color-hover: white;
        --item-background-color-hover: blue;
      }
    </style>
  </CountrySelect>
</div>

<script>
  import "flag-icons/css/flag-icons.css";

  import { createEventDispatcher } from "svelte";

  import countries from "./countries.js";

  import Portal from "./portal.svelte";

  export let name = "";
  export let label = "";
  export let placeholder = "";
  export let wrapperClass = "";
  export let inputClass = "";
  export let value = "US";
  export let disabled = false;
  export let search_logic;

  const dispatcher = createEventDispatcher();

  let open = false;

  let offset = {};

  let wrapper;

  let options;

  let search = "";

  let input;

  $: open && get_wrapper_offset(wrapper);

  const classnames = (names) => names.filter((v) => v).join(" ");

  const handle_click_outside = (evt) => {
    let outside = true;

    if (
      evt.target === wrapper ||
      evt.target === options ||
      wrapper.contains(evt.target) ||
      options.contains(evt.target)
    ) {
      outside = false;
    }

    if (outside) {
      search = "";
      open = false;
    }
  };

  const toggle_dropdown = () => {
    if (disabled) return;

    open = !open;

    setTimeout(() => input.focus());

    offset = get_wrapper_offset(wrapper);
  };

  const is_escaped = (evt) => {
    var isEscape = false;

    if ("key" in evt) {
      isEscape = evt.key === "Escape" || evt.key === "Esc";
    } else {
      isEscape = evt.keyCode === 27;
    }

    return isEscape;
  };

  const handle_keydown = (evt) => {
    evt = evt || window.event;

    if (is_escaped(evt)) {
      search = "";
      open = false;
    }
  };

  const get_wrapper_offset = (node) => {
    var box = node.getBoundingClientRect();

    var body = document.body;
    var docEl = document.documentElement;

    var scrollTop = window.pageYOffset || docEl.scrollTop || body.scrollTop;
    var scrollLeft = window.pageXOffset || docEl.scrollLeft || body.scrollLeft;

    var clientTop = docEl.clientTop || body.clientTop || 0;
    var clientLeft = docEl.clientLeft || body.clientLeft || 0;

    var top = box.top + scrollTop - clientTop;
    var left = box.left + scrollLeft - clientLeft;

    return { top, left, width: node.offsetWidth, height: node.offsetHeight };
  };

  const calculate_position = (node) => {
    offset = get_wrapper_offset(node);

    const handleResize = () => {
      offset = get_wrapper_offset(node);
    };

    window.addEventListener("resize", handleResize, true);
    window.addEventListener("keydown", handle_keydown, true);

    document.addEventListener("click", handle_click_outside, true);

    return {
      destroy() {
        window.removeEventListener("resize", handleResize, true);
        window.removeEventListener("keydown", handle_keydown, true);

        document.removeEventListener("click", handle_click_outside, true);
      },
    };
  };

  const search_countries = (keyword) => {
    if (typeof search_logic === "function") {
      return search_logic(keyword, countries);
    }
    return keyword
      ? countries.filter((c) =>
          String(c.name).toLowerCase().startsWith(String(keyword).toLowerCase())
        )
      : countries;
  };

  const on_select_item = (item) => {
    value = item.code;

    dispatcher("change", item);

    search = "";

    open = false;
  };

  $: customWrapperClass = classnames(["country-field", wrapperClass]);

  $: customInputClass = classnames([inputClass]);

  $: selected = countries.find((c) => c.code === value) || {};

  $: items = search_countries(search);
</script>

<div use:calculate_position bind:this={wrapper} class={customWrapperClass}>
  {#if label}
    <label for={name}>{label}</label>
  {/if}
  <div class="country-field-input">
    {#if !open}
      <div class="country-field-prepend">
        {#if selected}
          <span class={`flag fi fi-${String(selected.code).toLowerCase()}`} />
        {/if}
      </div>
    {/if}

    {#if open}
      <input
        {...$$restProps}
        {disabled}
        bind:this={input}
        bind:value={search}
        class={customInputClass}
        placeholder={selected.name || placeholder}
      />
    {:else}
      <input
        {...$$restProps}
        {disabled}
        value={selected.name}
        class={customInputClass}
        {placeholder}
        readonly
        on:click={toggle_dropdown}
      />
    {/if}
    <!-- svelte-ignore a11y-click-events-have-key-events -->
    <div class="country-field-append" on:click={toggle_dropdown}>
      <svg
        width="12"
        height="7"
        viewBox="0 0 12 7"
        fill="none"
        xmlns="http://www.w3.org/2000/svg"
        class:open
      >
        <path
          fill-rule="evenodd"
          clip-rule="evenodd"
          d="M0.195262 0.528514C0.455612 0.268165 0.877722 0.268165 1.13807 0.528514L6 5.39044L10.8619 0.528514C11.1223 0.268165 11.5444 0.268165 11.8047 0.528514C12.0651 0.788864 12.0651 1.21097 11.8047 1.47132L6.4714 6.80466C6.21106 7.06501 5.78895 7.06501 5.5286 6.80466L0.195262 1.47132C-0.0650874 1.21097 -0.0650874 0.788864 0.195262 0.528514Z"
          fill="#696969"
        />
      </svg>
    </div>
  </div>
</div>

<Portal>
  {#if open}
    <div
      bind:this={options}
      role="listbox"
      tabindex="-1"
      class="country-list"
      style="top: {offset.height +
        offset.top}px; left: {offset.left}px; width: {offset.width}px "
    >
      {#each items as item}
        <!-- svelte-ignore a11y-click-events-have-key-events -->
        <div
          class="flag-item"
          on:click={() => on_select_item(item)}
          role="option"
          tabindex="-1"
          aria-selected={value === item.code}
        >
          <span class={`flag fi fi-${String(item.code).toLowerCase()}`} />
          <div class="flag-label">
            {item.name}
          </div>
        </div>
      {/each}
    </div>
  {/if}
</Portal>

<style>
  .country-field {
    display: flex;
    flex-direction: column;
    font-family: inherit;
    margin-bottom: var(--margin-bottom, 1rem);
  }

  .country-field label {
    font-size: var(--label-font-size, 1rem);
    color: var(--label-color, #000000);
    margin-bottom: var(--label-margin-bottom, 0.5rem);
  }

  .country-field-input {
    display: flex;
    flex-direction: row;
    align-items: center;
    gap: var(--field-gap, 10px);
    border-style: var(--field-border-style, solid);
    border-width: var(--field-border-width, 1px);
    border-color: var(--field-border-color, #dddddd);
    padding: var(--field-padding, 10px);
    border-radius: var(--field-border-radius, 5px);
  }

  .country-field-input:focus-within {
    border-color: var(--field-focus-border-color, #f33ca7);
  }

  .country-field-input:has(:disabled) {
    border-color: var(--field-border-color-disabled, #dddddd);
    background-color: var(--field-background-color-disabled, #ececec);
  }

  .country-field-input input:disabled {
    color: var(--input-disabled-color, #666666);
  }

  .country-field-input input {
    display: inline-flex;
    flex-grow: 1;
    border: none;
    height: 100%;
    width: 100%;
    color: var(--field-input-color, #666666);
    background-color: transparent;
  }

  .country-field-input input:focus {
    border: none;
    outline: none;
  }

  .country-list {
    position: absolute;
    max-height: 200px;
    background-color: var(--list-background-color, #ffffff);
    display: flex;
    flex-direction: column;
    overflow-y: auto;
    border-radius: var(--list-border-radius, 5px);
    border-style: var(--list-border-style, solid);
    border-width: var(--list-border-width, 1px);
    border-color: var(--list-border-color, #dddddd);
    box-shadow: var(
      --list-box-shadow,
      -4px 10px 5px -10px rgba(222, 222, 222, 1)
    );
  }

  svg.open {
    transform: rotate(180deg);
  }

  .flag-item {
    display: flex;
    align-items: center;
    padding: 12px 16px;
    cursor: pointer;
    user-select: none;
    transition: all 200ms ease-in-out;
  }

  .flag-item:hover {
    color: var(--item-color-hover, #000000);
    background-color: var(--item-background-color-hover, #f5c7e2);
  }

  .flag {
    min-width: 16px;
  }

  .flag-label {
    font-family: inherit;
    padding-left: 8px;
    flex-grow: 1;
    font-size: inherit;
    line-height: 18px;
  }
</style>

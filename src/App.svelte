<script>
  import Metadata from "./Metadata.svelte";
  const fields = [
    {
      name: "Bill",
      value: 0,
      get unit() {
        return currency[0];
      },
      type: "text",
      range: []
    },
    {
      name: "Tip",
      value: 15,
      get unit() {
        return `${fields[1].value}%`;
      },
      type: "range",
      range: [1, 30]
    },
    {
      name: "Split",
      value: 1,
      get unit() {
        return `${fields[2].value}`;
      },
      type: "range",
      range: [1, 10]
    }
  ];
  $: tip = fields[0].value * (fields[1].value / 100);
  $: total = rounder(
    String((Number(tip) + Number(fields[0].value)) / Number(fields[2].value)),
    // to decimal place
    2
  );
  $: validate = fields[0].value > 0 && fields[0].value[0] != 0;
  // default USD
  $: currency = ["$", "."];

  // deals with Svelte not auto focusing updated input component
  // might as well also two-way bind value this way
  let timer;
  function handleChange(elm) {
    fields[elm.id].value = elm.value;
    clearTimeout(timer);
    timer = setTimeout(() => {
      document.getElementById(elm.id).focus();
    }, 50);
  }
  function rounder(value, decimalPlace) {
    let matched = new RegExp(`\\.`, "g");
    return String(
      value.match(matched) ? Number(value).toFixed(decimalPlace) : Number(value)
    ).replace(".", currency[1]);
  }
</script>

<style type="text/scss">
  :global(body) {
    background-color: #373532;
  }
  .wrapper {
    text-align: center;
    color: white;
  }
  .input__unit {
    display: inline-block;
    position: relative;
    height: 1em;
    width: fit-content;
    margin-right: 0.3em;
  }
  .input__invalid {
    border: 3px solid red;
  }
  .total__text {
    font-size: 1em;
  }
  .currency__selector {
    display: block;
    margin: auto;
  }
</style>

<Metadata />
<div class="wrapper">
  {#each fields as field, i}
    <p>{field.name}</p>
    <p class="input__unit">{field.unit}</p>
    {#if i === 0 && !validate}
      <input
        id={i}
        value={field.value}
        on:input={() => handleChange(event.target)}
        class="input__invalid"
        type={field.type} />
    {:else}
      <input
        id={i}
        value={field.value}
        on:input={() => handleChange(event.target)}
        type={field.type}
        min={field.range[0]}
        max={field.range[1]} />
    {/if}
  {/each}
  {#if validate}
    <br />
    Total
    <br />
    Per Person
    <p class="total__text">{currency[0]} {total}</p>
  {/if}
  <!-- hacky onChange forces DOM update-->
  <select
    class="currency__selector"
    bind:value={currency}
    on:change={() => (fields = fields)}>
    <option value={['$', '.']}>USD</option>
    <option value={['€', ',']}>Euro</option>
  </select>
</div>

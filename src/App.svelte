<script>
  import Header from "./Header.svelte";

  const fields = [
    {
      name: "Bill",
      value: 0,
      get unit() {
        return currency[0];
      },
      type: "text",
      range: [],
      background: "linear-gradient(90deg, red 20%, pink 20%)"
    },
    {
      name: "Tip",
      value: 15,
      get unit() {
        return `${fields[1].value}%`;
      },
      type: "range",
      range: [1, 30],
      background: "linear-gradient(90deg, orange 20%, #ffed4b 20%)"
    },
    {
      name: "Split",
      value: 1,
      get unit() {
        return `${fields[2].value}`;
      },
      type: "range",
      range: [1, 10],
      background: "linear-gradient(90deg, blue 20%, green 20%)"
    }
  ];
  $: tip = fields[0].value * (fields[1].value / 100);
  $: total = String(
    (Number(tip) + Number(fields[0].value)) / Number(fields[2].value)
  );
  $: totalRound = rounder(String(total), 2, currency);
  $: tipRound = rounder(String(tip), 2, currency);

  $: validate = fields[0].value > 0 && fields[0].value[0] != 0;
  // default USD
  $: currency = ["$", "."];

  let timer;
  export function handleChange(elm) {
    fields[elm.id].value = elm.value;
    clearTimeout(timer);
    timer = setTimeout(() => {
      document.getElementById(elm.id).focus();
    }, 50);
  }
  export function rounder(value, decimalPlace) {
    let matched = new RegExp(`\\.`, "g");
    return String(
      value.match(matched) ? Number(value).toFixed(decimalPlace) : Number(value)
    ).replace(".", currency[1]);
  }
</script>

<style type="text/scss">
  :global(body) {
    background-color: white;
    padding: 0;
  }
  .wrapper {
    text-align: center;
    color: black;
  }
  .input__block {
    width: 60%;
    padding-top: 5%;
    margin: auto;
  }
  .input__unit {
    display: inline-block;
    position: relative;
    height: 1em;
    width: fit-content;
    margin-right: 0.3em;
  }
  .input__name {
    display: inline-block;
  }
  .input__invalid {
    border: 3px solid red;
  }
  .total__block {
    width: 60%;
    background-color: red;
    margin: auto;
  }
  .total__text {
    font-size: 1em;
    display: inline-block;
    width: 20%;
  }
  .currency__selector {
    display: block;
    margin: auto;
  }
</style>

<Header />
<div class="wrapper">
  {#each fields as field, i}
    <div class="input__block" style={`background: ${field.background}`}>
      <p class="input__name">{field.name}</p>
      <p class="input__unit">{field.unit}</p>
      {#if i === 0 && !validate}
        <input
          id={i}
          value={field.value}
          on:input={() => {
            handleChange(event.target);
          }}
          class="input__invalid"
          type={field.type} />
      {:else}
        <input
          id={i}
          value={field.value}
          on:input={() => {
            handleChange(event.target);
          }}
          type={field.type}
          min={field.range[0]}
          max={field.range[1]} />
      {/if}
    </div>
  {/each}
  {#if validate}
    <div class="total__block">
      Tip
      <p class="total__text">{currency[0]} {tipRound}</p>
      Total Per Person
      <p class="total__text">{currency[0]} {totalRound}</p>
    </div>
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

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
      background: "linear-gradient(120deg, red 20%, pink 20%)"
    },
    {
      name: "Tip",
      value: 15,
      get unit() {
        return `${fields[1].value}%`;
      },
      type: "range",
      range: [1, 30],
      background: "linear-gradient(120deg, orange 20%, yellow 20%)"
    },
    {
      name: "Split",
      value: 1,
      get unit() {
        return `${fields[2].value}`;
      },
      type: "range",
      range: [1, 10],
      background: "linear-gradient(120deg, seagreen 20%, lightblue 20%)"
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
  :global(input[type="range"]) {
    -webkit-appearance: none;
    width: 50%;
    height: fit-content;
    background: none;
    outline: none;
    border: none;
    margin: auto;
  }
  :global(input[type="range"]:focus) {
    outline: none;
  }
  :global(input[type="range"]::-webkit-slider-runnable-track) {
    width: 100%;
    height: 12.8px;
    cursor: pointer;
    animate: 0.2s;
    box-shadow: 0px 0px 0px #000000, 0px 0px 0px #0d0d0d;
    background: white;
    border-radius: 25px;
    border: 0px solid #000101;
  }
  :global(input[type="range"]::-webkit-slider-thumb) {
    box-shadow: 0px 0px 0px #000000, 0px 0px 0px #0d0d0d;
    border: 0px solid #000000;
    height: 1.3em;
    width: 1.2em;
    border-radius: 1em;
    background: gray;
    cursor: pointer;
    -webkit-appearance: none;
    margin-top: -3.6px;
  }
  :global(input[type="range"]:focus::-webkit-slider-runnable-track) {
    background: white;
  }

  .wrapper {
    text-align: center;
    color: black;
  }
  .inputs {
    margin-top: 1.5em;
  }
  .input__block {
    display: block;
    width: 60%;
    height: 5em;
    margin: auto;
  }
  .input__fields {
    position: absolute;
    left: 10%;
    font-size: 1.5em;
    height: 5.55em;
    width: 10%;    
    background: linear-gradient(180deg, green 60%, red 20%);
  }
  .input__unit {
    position: relative;
    margin-right: 0.3em;
    text-align: left;
  }
  .input__name {
    text-align: center;
  }
  .input__invalid {
    border: 3px solid red;
  }
  .total__block {
    background-color: red;
    width: 60%;
    margin: auto;
  }
  .total__text {
    font-size: 1em;
    display: inline-block;
    width: 30%;
  }
  .currency__selector {
    display: inline-block;
    position: absolute;
    left: 12%;
    top: 15.5em;
  }
</style>

<Header />
<div class="wrapper">
  {#each fields as field, i}
    <div class="input__block" style={`background: ${field.background}`}>
      <div class="input__fields">
        <p class="input__name">
          {field.name}
          <span class="input__unit">{field.unit}</span>
        </p>
      </div>
      {#if i === 0 && !validate}
        <input
          id={i}
          value={field.value}
          on:input={() => {
            handleChange(event.target);
          }}
          class="inputs input__invalid"
          type={field.type} />
      {:else}
        <input
          class="inputs"
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
    <!-- hacky onChange forces DOM update-->
  <select
    class="currency__selector"
    bind:value={currency}
    on:change={() => (fields = fields)}>
    <option value={['$', '.']}>USD</option>
    <option value={['€', ',']}>Euro</option>
  </select>
  <div class="total__block">
    {#if validate}
      <p class="total__text">Tip Total {currency[0]} {tipRound}</p>
      <p class="total__text">Total Per Person {currency[0]} {totalRound}</p>
    {:else}
      <p class="total__text">Tip Total</p>
      <p class="total__text">Total Per Person</p>
    {/if}
  </div>
</div>

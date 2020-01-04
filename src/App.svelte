<script>
  import Header from "./Header.svelte";
  import GlobalStyle from "./GlobalStyle.svelte";

  const fields = [
    {
      name: "Bill",
      value: 0,
      get unit() {
        return currency[0];
      },
      type: "text",
      range: [],
      background: ["red", "linear-gradient(120deg, red 20%, pink 20%)"]
    },
    {
      name: "Tip",
      value: 15,
      get unit() {
        return `${fields[1].value}%`;
      },
      type: "range",
      range: [1, 30],
      background: ["orange", "linear-gradient(120deg, orange 20%, yellow 20%)"]
    },
    {
      name: "Split",
      value: 1,
      get unit() {
        return `${fields[2].value}`;
      },
      type: "range",
      range: [1, 10],
      background: [
        "seagreen",
        "linear-gradient(120deg, seagreen 20%, lightblue 20%)"
      ]
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
    height: 3.32em;
    width: 10%;
    background: linear-gradient(
      to bottom,
      red 60%,
      green 30%,
      green 60%,
      blue 60%
    );
  }
  .input__unit {
    position: relative;
    margin-right: 0.3em;
    text-align: left;
  }
  .input__name {
    text-align: right;
    width: 5em;
  }
  .input__invalid {
    border: 3px solid red;
  }
  .total__block {
    background-color: red;
    width: 70%;
    margin-left: 10%;
  }
  .total__text {
    font-size: 1.2em;
    display: inline-block;
    width: 40%;
  }
  .currency__selector {
    display: inline-block;
    position: absolute;
    left: 12%;
    top: 15.8em;
  }
</style>

<Header />
<div class="wrapper">
  {#each fields as field, i}
    <div class="input__block" style={`background: ${field.background[1]}`}>
      <div class="input__fields" style={`background: ${field.background[0]}`}>
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

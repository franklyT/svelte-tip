<script>
  import Header from "./Helpers/Header.svelte";
  import GlobalStyle from "./Helpers/GlobalStyle.svelte";

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

  $: tip = fields[0].value * (fields[1].value / 100);
  $: total = String(
    (Number(tip) + Number(fields[0].value)) / Number(fields[2].value)
  );
  $: totalRound = rounder(String(total), 2);
  $: tipRound = rounder(String(tip), 2);

  $: validate = fields[0].value > 0 && fields[0].value[0] != 0;
  let timer;
  let currency = ["$", "."];
  const fields = [];

  class CalculatorField {
    constructor(name, value, unit, type, range, background) {
      this.name = name;
      this.value = value;
      this.unit = unit;
      this.type = type;
      this.range = range;
      this.background = background;
    }
  }
  let bill = fields.push(
    new CalculatorField(
      `Bill ${currency[0]}`,
      0,
      "",
      "text",
      [],
      ["red", "linear-gradient(120deg, red 20%, pink 20%)"]
    )
  );
  let tip = fields.push(
    new CalculatorField(
      "Tip",
      15,
      `%`,
      "range",
      [1, 30],
      ["orange", "linear-gradient(120deg, orange 20%, yellow 20%)"]
    )
  );
  let split = fields.push(
    new CalculatorField(
      "Split",
      1,
      "",
      "range",
      [1, 10],
      ["seagreen", "linear-gradient(120deg, seagreen 20%, lightblue 20%)"]
    )
  );
</script>

<style type="text/scss">
  @import "bulma/bulma.sass";
</style>

<Header />

<div>
  {#each fields as field, i}
    <div style={`background: ${field.background[1]}`}>
      <div style={`background: ${field.background[0]}`}>
        <p>
          {field.name}
          <span>{field.value}{field.unit}</span>
        </p>
      </div>
      {#if i === 0 && !validate}
        <input
          id={i}
          value={field.value}
          on:input={() => {
            handleChange(event.target);
          }}
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
  <!-- hacky onChange forces DOM update-->
  <select bind:value={currency} on:change={() => (fields = fields)}>
    <option value={['$', '.']}>USD</option>
    <option value={['€', ',']}>Euro</option>
  </select>
  <div>
    {#if validate}
      <p>Tip Total {currency[0]} {tipRound}</p>
      <p>Total Per Person {currency[0]} {totalRound}</p>
    {:else}
      <p>Tip Total</p>
      <p>Total Per Person</p>
    {/if}
  </div>
</div>

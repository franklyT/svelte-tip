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
      ["red", "linear-gradient(120deg, #f44336 20%, #ff7961 20%)"]
    )
  );
  let tip = fields.push(
    new CalculatorField(
      "Tip",
      15,
      `%`,
      "range",
      [1, 30],
      ["orange", "linear-gradient(120deg, #ffb74d 20%, #ffe97d 20%)"]
    )
  );
  let split = fields.push(
    new CalculatorField(
      "Split",
      1,
      "",
      "range",
      [1, 10],
      ["seagreen", "linear-gradient(120deg, #7e57c2 20%, #b085f5 20%)"]
    )
  );
</script>

<style type="text/scss">
  @import "bulma/bulma.sass";
</style>

<Header />
<section>
  <div class="container">
    <div class="columns is-mobile is-multiline is-centered" style={`text-align: center;`}>
      {#each fields as field, i}
        <div class="column is-one-third" />
        <div
          class="column is-one-third"
          style={`background: ${field.background[1]}; border-radius: 0.7em; margin-top: 0.7em; box-shadow: 10px 10px 5px grey; color: white; font-size: 1.3em; font-weight: 700; -webkit-text-stroke-width: 1px;-webkit-text-stroke-color: black;`}>
          <div>
            <p>
              {field.name}
              <span>{field.value}{field.unit}</span>
            </p>
          </div>
          {#if i === 0 && !validate}
            <input
              id={i}
              style={`margin-top: 0.7em;`}
              value={field.value}
              on:input={() => {
                handleChange(event.target);
              }}
              type={field.type} />
          {:else}
            <input
              id={i}
              style={`margin-top: 0.5em;`}
              value={field.value}
              on:input={() => {
                handleChange(event.target);
              }}
              type={field.type}
              min={field.range[0]}
              max={field.range[1]} />
          {/if}
        </div>
        <div class="column is-one-third" />
      {/each}
      <!-- hacky onChange forces DOM update-->
      <div class="column is-one-third" />
      <div class="column is-one-third" style={`font-size: 1.5em; font-weight: 500; padding-bottom: 0;`}>
        {#if validate}
          <p>Tip Total {currency[0]} {tipRound}</p>
          <p>Total Per Person {currency[0]} {totalRound}</p>
        {:else}
          <p>Tip Total</p>
          <p>Total Per Person</p>
        {/if}
        <div class="column is-one-third" />
      </div>
      <div class="column is-one-third" />
      <div class="column is-two-fifths" />
      <select
        class="column is-one-fifth"
        style={`width: fit-content;`}
        bind:value={currency}
        on:change={()=> {fields[0].name = `Bill ${currency[0]}`}}
      >
        <option value={['$', '.']}>USD</option>
        <option value={['€', ',']}>Euro</option>
      </select>
      <div class="column is-two-fifths" />
    </div>
  </div>
</section>

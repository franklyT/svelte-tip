<style>
  :global(html) {
    width: 100%;
    height: 50vh;
    margin-top: 10em;
  }
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
</style>

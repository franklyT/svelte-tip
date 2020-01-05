<style>
  :global(html) {
    position:fixed;
    top:0;
    bottom:0;
    left:0;
    right:0;
  }
  :global(body) {
    background-color: white;
    padding: 0;
    top: 10vh;
    right: -5vw;
  }

  :global(input[type="text"]) {
    width: 7em;
    border-radius: 0.8em;
    text-align: center;
    -webkit-text-stroke-width: 0px;
    -webkit-text-stroke-color: none;
  }

  :global(input[type="text"]:focus) {
    outline: none;
  }
  /* :global(input[type="range"]) {
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
    transition: 0.2s;
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
  */
</style>

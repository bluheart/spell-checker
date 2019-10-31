<script>
  export let word;
  export let id;
  const red = "inline-flex rounded bg-red-500 text-white m-1 px-2 py-1 text-2x1 font-bold mr-3 focus:bg-transparent focus:text-red-500 border focus:border-red-500";
  const blue = "inline-flex rounded bg-blue-500 text-white m-1 px-2 py-1 text-2x1 font-bold mr-3 focus:bg-transparent focus:text-blue-500 border focus:border-blue-500";
  let style = red;
  function change_color(ev) {
    if(ev.target.textContent !== word) style = blue;
    else {
      style = red;
    }
  }
</script>

<style>

</style>

<span id={id} contenteditable on:input={ change_color } class={style}>
    {word}
</span>
